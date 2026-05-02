---
name: next-step
description: Reactive unstuck helper. Activate when the user is stalled or wandering — "what now", "what should I do", "I don't know what to ask", "where do I go from here", "I'm stuck", "any ideas", "what's next", "I'm out of ideas". Also fires on behavioral signals: 2+ off-topic or wandering messages in a row, the user asking generic questions after a successful action, long pauses followed by a vague reply. Surveys current project state, lists exactly three concrete next-step suggestions each with a one-line rationale, and recommends one. Always actionable, never abstract — "build feature X" is not actionable; "add a route at /api/x that returns the user count" is.
---

# next-step

The user has lost momentum. Maybe they finished a thing and don't know what's worth doing next. Maybe they're stuck on a hard problem. Maybe they're tired and want a small win. Your job: read the project state, give them three concrete options, recommend one, and let them say "yes."

---

## Triggers

- **Phrases:** "what now", "what next", "what should I do", "I don't know what to ask", "where do I go from here", "I'm stuck", "any ideas", "I'm out of ideas".
- **Behavioral signals:**
  - Two or more wandering / off-topic messages in a row.
  - User asks a generic question after a successful action ("ok cool, ... what?").
  - Long silence followed by a vague reply ("ok").
  - User explicitly says they're tired or losing focus.

---

## What to do

### 1. Quick state survey

Before suggesting, look:

- `git status` — what's uncommitted?
- The README or current task list, if present.
- The most recent files touched — what was the last goal?
- Any visible TODOs or FIXMEs in the working files.
- Any tests, lint, or build that's failing.

Build a one-paragraph mental picture. You'll fold a couple of details into the suggestions.

### 2. Generate exactly three suggestions

Three is the rule. Not two, not four.

Cover the spectrum:

- **One small win** — finishable in 10–20 minutes, low risk. Builds momentum.
- **One forward-progress move** — actually advances the project's main goal.
- **One investment** — improves something for later (a test, a doc, a refactor, a deploy step).

If the project state strongly suggests fewer dimensions (e.g., everything is broken — small win, fix one thing, fix another thing), name three flavors of the same direction.

### 3. Each suggestion is concrete

❌ "Add a feature for users."
✅ "Add a `/users` page that lists all users from the existing API endpoint — uses the `getUsers()` helper that's already there."

❌ "Improve testing."
✅ "Write one test for `parseDate()` in `src/util.ts` — it has no tests and the function is simple enough to test in 5 lines."

Each suggestion includes:

- The action (one phrase).
- Where it lives (file or area).
- The rationale (one short line).

### 4. Recommend one

State your pick and why:

> I'd start with the small win — your brain is fresh enough for it and it'll take 10 minutes.

If state strongly favors a different one (e.g., a build is failing — fix the build first), recommend that instead and say so.

### 5. Make it a yes/no

> **Want to do [X]?**

If the user picks a different one of the three, say "yes" and go. If they ask for a fourth, generate one — but flag that you ranked three for a reason.

### 6. End-of-turn footer

```
**What we did:** Surveyed where we are and laid out three options.
**What it means:** [the project's current state in one line].
**What's next:** [the recommended option, as yes/no].
```

---

## Composition

- **`coding-mentor`** — tone.
- **`what-changed`** — if the user seems lost about *what just happened* rather than what's *next*, that skill runs first. Sometimes you can sense the right one from the question; if unclear, do a one-line recap and flow into next-step.
- **`explain-this-project`** — if the user hasn't oriented yet (new repo, no tour), `explain-this-project` precedes you. You can't suggest a next step before they know what *this* is.
- **`safe-mode`** — orthogonal; suggestions still go through the gate when accepted.

---

## Tone

- Energetic but not pushy. "Here are three things, here's my pick" — not a sales pitch.
- Honest about size. "20 minutes" suggestions stay 20 minutes; don't lowball.
- No filler ("That's a great question!"). Get to the three.
- If the right answer is "stop for today," say so:

  > "Honest answer — you've done a lot today. The three options below are real, but the *best* next step is probably to commit what you have and pick this up tomorrow with fresh eyes. If you want to keep going, here are three:"

---

## Graduation signal

The user proposes their own next step. When that's happening:

- Validate the choice in one line ("good — that's the right priority because [reason]").
- Skip generating three options unless they ask.
- Just go.

The skill is essentially silent for that user, only firing when they actually ask "what now."

---

## Examples

### Example: post-feature, user wandering

User: "ok the route works. what now?"

> Quick look — main feature is done, no failing tests, one TODO in `src/auth.ts` you noted earlier, no commit since 2 hours ago.
>
> **Three options:**
>
> 1. **Commit and push.** *(small win, 5 min)* — Two hours of work isn't saved to git yet. Risk-low, momentum-positive.
> 2. **Wire up the new route in the nav.** *(forward, 15 min)* — Users can't actually get to the page yet. Edit `src/components/Nav.astro` to add a link.
> 3. **Write one test for the route.** *(investment, 20 min)* — No test for it; the harness already exists in `tests/`. Adds confidence for later changes.
>
> **My pick:** option 1, then 2. Committing first is the move — two hours of unsaved work makes me nervous, and the rest is easier with a clean baseline.
>
> **Want to commit?**
>
> **What we did:** Surveyed state and laid out three options.
> **What it means:** You're at a natural pause point with one safety task and two forward tasks ahead.
> **What's next:** Commit first? (yes / pick option 2 instead / something else)

### Example: explicitly stuck

User: "I'm stuck, this isn't working"

> Let me look. *(reads recent files, error state)*
>
> Where we are: the JWT middleware throws an "invalid signature" on every request, even with a token that was just issued. Build and lint are green; only the runtime fails.
>
> **Three things to try, in order of likelihood:**
>
> 1. **Confirm the secret matches issue and verify.** *(15 min)* — `JWT_SECRET` might be different between where you sign and where you verify. We log them both and compare.
> 2. **Try a known-good library example** end-to-end in a tiny scratch script. *(30 min)* — Sometimes the issue is in our wrapper, not jwt.io. A clean baseline tells us.
> 3. **Switch from `jsonwebtoken` to `jose`.** *(45 min, riskier)* — `jose` is stricter and gives better errors. Useful if (1) and (2) don't reveal the bug.
>
> **My pick:** option 1. 80% of "invalid signature" cases turn out to be secret mismatch; takes 15 min to rule out.
>
> **Want me to add the comparison logging?**

### Example: graduated user

User: "what now?"

> One unsaved hour and one TODO. Probably commit, then knock out the TODO. Sound right?

(User says yes; just goes.)

---

## What this skill is NOT

- **Not a roadmap generator.** Three concrete options, not a multi-week plan.
- **Not a brainstorm.** You converge to a recommendation.
- **Not a stand-in for a real plan.** If the user asks "should we use Postgres or SQLite," that's a design decision; route to a real architectural conversation, not a 3-option list.
- **Not a guesser.** If the project state is unclear, run `git status` and look — don't propose blindly.
