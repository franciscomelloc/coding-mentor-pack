---
name: explain-this-project
description: Reactive orientation skill. Activate when the user asks "what is this project?", "what does this code do?", "I just cloned this and don't know where to start", "what is this repo?", "explain this codebase", or shows behavioral signs of being lost in an existing repo (asking generic questions in a folder Claude hasn't toured yet, vague language about "this", referring to files Claude hasn't read). Walks the repo, identifies entry points and dependencies, and outputs a plain-English tour: project purpose, how to run it, the 5–8 most important files and their roles, anything missing or broken. Then offers to dig deeper into one area.
---

# explain-this-project

The user is in a codebase they don't yet understand. Maybe they cloned it from GitHub, maybe they inherited it, maybe they wrote it 6 months ago and forgot. Your job: give them a plain-English tour they can hold in their head, then offer one path to go deeper.

---

## Triggers

Fire on any of:

- **Phrases:** "what is this project", "what does this code do", "explain this repo", "I just cloned this", "where do I start", "I'm lost", "tour this codebase", "what is this".
- **Behavioral signals:** user asks a generic question about "this" or "the project" before Claude has read anything. User refers to files by guess ("the main file?"). User asks to run something but doesn't know what.
- **Context clues:** new working directory, no prior conversation about the structure, multiple files visible but no clear entry point asked about.

If `using-git` or `running-locally` would also fire (e.g., user just cloned and asks "what is this and how do I run it"), you go first — the tour orients them, then those skills handle the specifics.

---

## What to do

### 1. Walk the repo

Read the root listing first. Look for:

- **README** (`README.md`, `README.txt`, `README.rst`) — read it fully.
- **Package manifest:** `package.json`, `pyproject.toml`, `requirements.txt`, `Cargo.toml`, `go.mod`, `Gemfile`. Read it.
- **Entry points:** `index.js`, `main.py`, `app.py`, `cmd/`, `src/main.*`, the `main`/`scripts` field in the manifest.
- **Config files** at the root: `tsconfig.json`, `vite.config.*`, `.env.example`, `Makefile`, `Dockerfile`, `docker-compose.yml`.
- **Source folders:** `src/`, `app/`, `lib/`, `pages/`, `components/`. Get a one-level listing.
- **Tests:** `tests/`, `test/`, `__tests__/`, `*.test.*`, `*.spec.*`. Note presence/absence.
- **CI config:** `.github/workflows/`, `.gitlab-ci.yml`, `.circleci/`. Note presence.

Read selectively — don't dump every file's contents. Open the README, manifest, and 1–2 entry-point files. Glance at structure, deep-read where it matters.

### 2. Identify what it actually does

In one or two sentences. What is this software for, who would run it, what does it produce?

If the README answers this, quote it. If not, infer from the code and say you're inferring.

### 3. Output the tour

Use this structure. Hit every section, keep each tight.

```
**What this is:** [1–2 sentences. Project purpose.]

**Stack:** [language, framework, key libs. One line.]

**How to run it:** [exact commands, copy-pasteable. Include install + run.]

**Key files (5–8):**
- `path/to/file` — [one-line role]
- ...

**Health check:**
- ✅ [things that look fine: README present, tests present, lockfile present, CI green]
- ⚠️ [things that look off: missing README, no tests, unpinned deps, TODO scattered, last commit very old, .env.example missing]
```

The tour is **5–8 files, not more**. Pick by importance: entry point, main config, one or two source files that show what the project actually does, anything weird worth flagging.

### 4. Offer one deeper path

End with a recommendation:

> "I suggest we [look at X / try running it / fix Y first]. Alternatives: [Z, W]. Want X?"

The X should be the highest-leverage next step given what you saw. If the code looks broken or unrunnable, X is "make it run." If it runs, X is "make a small change end-to-end so you understand the loop."

---

## End-of-turn footer

Always include:

> **What we did:** Toured the project — purpose, stack, how to run, key files.
> **What it means:** You now have a map. You can refer to files by name and know what each does.
> **What's next:** [the one deeper path you picked, as a yes/no].

---

## Composition

- **`coding-mentor`** sets the tone (always-on).
- **`using-git`/`using-github`** — if the user's first interaction is a clone and they don't know git, that skill teaches the model after the tour.
- **`installing-dependencies`** — your "How to run it" section will mention `npm install` etc.; if it's the first time, that skill expands on what happens.
- **`running-locally`** — same; first dev-server run is its territory.
- **`translate-the-error`** — if running it produces an error, hand off.
- **`next-step`** — once the tour is done, the user might ask "what now"; this skill already proposed one in the footer, but `next-step` can broaden if asked.

---

## Graduation signal

The user starts naming files and concepts directly without the tour:

- "Let's open `src/server.ts` and add the route."
- "I'll edit the README to add a usage example."
- "This is using Express — I'll add an `app.use` for that."

When that's happening:

- Skip the full table; just answer their direct question.
- Drop the "What this is" / "Stack" if obvious from context.
- The footer can shorten to one line.

---

## Examples

### Example: fresh clone, beginner

User: "I just cloned this. What is it?"

> Let me look around.
>
> [reads README, package.json, src/index.ts]
>
> **What this is:** A small command-line tool that converts Markdown files to HTML. You give it a folder of `.md` files, you get a folder of `.html` files.
>
> **Stack:** Node.js + TypeScript. Uses `marked` for parsing.
>
> **How to run it:**
> ```
> npm install
> npm run build
> npm start -- ./input ./output
> ```
>
> **Key files:**
> - `src/index.ts` — entry point; reads CLI args, calls the converter.
> - `src/convert.ts` — the actual `.md → .html` function. This is the heart.
> - `src/io.ts` — file reading and writing.
> - `package.json` — declares deps and the `start`/`build` scripts.
> - `tsconfig.json` — TypeScript config; targets Node 20+.
> - `README.md` — usage examples and a TODO list at the bottom.
>
> **Health check:**
> - ✅ README, lockfile (`package-lock.json`), TypeScript config all present.
> - ⚠️ No tests. The TODO list mentions "add tests" — they didn't.
> - ⚠️ `.env.example` missing but `src/index.ts` reads `process.env.DEBUG` — minor.
>
> **What we did:** Toured the project — it's a Markdown→HTML CLI in Node + TypeScript.
> **What it means:** You can now point to specific files. The actual conversion lives in `src/convert.ts`.
> **What's next:** I suggest we just *run it* on a tiny input first, so you see what it does. Alternatives: read `src/convert.ts` together, or skip ahead to adding a test. Want me to make a sample input and run it?

### Example: graduated user, terse question

User: "What's in here?" *(third repo this session, user is moving fast)*

> Astro 6 site, two pages (`index.astro`, `about.astro`), one component (`Nav.astro`). Tailwind v4. No tests.
>
> **What's next:** Open something specific?

---

## What this skill is NOT

- **Not a code review.** You're orienting, not critiquing.
- **Not a documentation generator.** You don't write to disk.
- **Not exhaustive.** 5–8 files, not 50. Beginners drown in 50.
- **Not the same as `what-changed`.** That skill recaps changes you just made; this skill orients in code that already existed.
