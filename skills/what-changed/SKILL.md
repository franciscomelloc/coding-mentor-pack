---
name: what-changed
description: Reactive recap skill. Activate when the user asks "what did you just do", "what's different now", "summarize what changed", "what just happened", "I'm lost — recap", "I lost track", "wait, what happened", "what was that", or after a dense turn that touched multiple files. Designed as a manual hook the user runs after big actions. Lists every file created / modified / deleted with a one-line description per change, why each change was done, what's now possible that wasn't before, and what to keep an eye on. Reads the working state — does not re-do anything.
---

# what-changed

The user has lost the thread of recent work. Maybe a turn touched 5 files, maybe Claude did a big refactor, maybe they walked away and came back. Your job: hand them a clean summary they can use to decide what to do next.

This is a **read-only** recap. You don't modify anything.

---

## Triggers

- **Phrases:** "what did you just do", "what's different now", "summarize what changed", "what just happened", "wait what", "I'm lost", "recap", "what was that", "I lost track".
- **Behavioral signals:** the previous turn touched 3+ files; the user's reply is a question instead of an instruction; they ask about a file you modified as if they don't remember.
- **Context clues:** post-compaction, post-long-turn, post-error-recovery sequence.

---

## What to do

### 1. Identify the scope

Decide what window of work to recap. Default: the most recent dense turn or set of related turns. If the user is more vague ("today's work"), recap the session.

If unclear, ask one short question:

> "Recap the last turn (the file rename), or everything since we started this morning?"

### 2. Inventory the changes

List every file:

- **Created** — `path/to/file` (new file)
- **Modified** — `path/to/file` (what was changed in plain English, not raw diff)
- **Deleted** — `path/to/file` (gone)
- **Renamed/moved** — `old/path → new/path`

Use `git status` and `git diff --stat` if available. If not, recall from the conversation.

For each, **one line** describing what changed conceptually, not line-by-line.

❌ "Modified `src/server.js` lines 42–58."
✅ "Modified `src/server.js` to add a `/healthz` route that returns `{ ok: true }`."

### 3. Group by intent

If the changes serve one goal, group them:

```
**To add a health-check endpoint:**
- Created `src/routes/healthz.ts` — the new route handler.
- Modified `src/server.ts` — registered the new route.
- Modified `tests/server.test.ts` — added a test for the route.
```

If multiple unrelated goals, list them separately.

### 4. State what's now possible

For each goal, one line on what the user can do now that they couldn't before:

```
**Now possible:** Health check at `GET /healthz` returns `{ ok: true }`. Use it from a load balancer or uptime monitor.
```

### 5. Flag risks and follow-ups

What could break? What's incomplete? What should the user verify?

```
**Keep an eye on:**
- The new test isn't run by default — your CI script runs `npm test` which includes it, but locally `npm test` is the way.
- No auth on `/healthz` — that's fine for a public health check, but don't reuse this pattern for sensitive routes.
```

If nothing's risky, say "Nothing to watch — all reversible with a single git command."

### 6. End-of-turn footer

```
**What we did:** Recapped [N] file changes for [the goal].
**What it means:** [the new capability in one phrase].
**What's next:** I suggest [verify it works / move on to the next thing / commit it]. (yes / no)
```

---

## Composition

- **`coding-mentor`** — tone.
- **`safe-mode`** — recap is a read; doesn't trigger safe-mode.
- **`next-step`** — after recap, the user often wants to know what's next; the footer's "What's next" satisfies that. If they want broader options, `next-step` runs explicitly on their ask.
- **`translate-the-error`** — if the recap surfaces an error from the changes (e.g., a type-check failure), hand off cleanly: "One thing I noticed while recapping — there's a TS error from the new route. Want me to translate it?"

---

## Tone

- Crisp. The user wants the summary to fit on one screen.
- Concrete. File names and verbs, not adjectives.
- Don't recap things you didn't actually do. If you don't remember, say so and offer to read `git status`.

---

## Graduation signal

The user reads diffs themselves before asking for a recap:

- "I see you changed `server.ts` — what's the `app.use` line for?"
- "I'll commit these — looks like 3 files."

When that's happening:

- Skip the inventory; answer their direct question.
- Recap can shrink to "one line per goal" instead of file-by-file.

---

## Examples

### Example: routine recap after a feature

User: "what did you just do?"

> Recapping the last turn — adding a health-check endpoint.
>
> **Files:**
> - **Created** `src/routes/healthz.ts` — handler that returns `{ ok: true }` with a 200.
> - **Modified** `src/server.ts` — registered the route at `app.use('/healthz', healthzRouter)`.
> - **Modified** `tests/server.test.ts` — added a test that hits the route and asserts the response.
>
> **Now possible:** `GET /healthz` returns `{ ok: true }` with status 200. Useful for load balancers and uptime monitors.
>
> **Keep an eye on:** No auth — fine for a public health check, don't reuse the pattern for sensitive routes.
>
> **What we did:** Recapped 3 file changes for the health-check feature.
> **What it means:** Your server now answers a known-good URL that says "I'm alive."
> **What's next:** I suggest we run the test once to confirm it passes. Want me to?

### Example: post-compaction, user is fully lost

User: "I'm lost — what was happening?"

> Reasonable — let me look at the working state.
>
> [runs `git status`, glances at recent commits]
>
> We're partway through migrating the auth middleware from session cookies to JWT. So far this session:
>
> **Done:**
> - Wrote the JWT signing/verifying helpers in `src/auth/jwt.ts` (committed: `a3f2c1d`).
> - Replaced the cookie middleware with the JWT middleware in `src/server.ts` (committed: `b1c8e7a`).
>
> **In progress (uncommitted):**
> - Modified `src/routes/login.ts` to issue a JWT instead of setting a cookie. The change is partial — the JWT issues correctly, but the response shape is still the old one.
>
> **Now possible:** New requests with a valid JWT in `Authorization: Bearer …` are authenticated. Old session cookies no longer work.
>
> **Keep an eye on:** The frontend still expects a cookie on login response. Until we update it, login will succeed but the frontend won't know.
>
> **What we did:** Recapped the JWT migration so far.
> **What it means:** Backend is half-migrated; frontend is unchanged and will break on next login until updated.
> **What's next:** I suggest we finish `login.ts` first (return the JWT in the response body), then update the frontend to read it. Want to keep going?

### Example: graduated user, terse

User: "what changed?"

> Three files for the health check: new `healthz.ts`, registered in `server.ts`, test in `server.test.ts`. All reversible with `git restore`.

---

## What this skill is NOT

- **Not a diff dumper.** Diffs are for tools; you summarize.
- **Not a code review.** No critique unless asked.
- **Not the same as `explain-this-project`.** That tours code that already existed; you recap recent changes.
- **Not action.** You read; you don't write.
