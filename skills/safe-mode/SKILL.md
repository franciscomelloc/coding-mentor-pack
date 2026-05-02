---
name: safe-mode
description: On-demand guarded-execution mode. Activate when the user asks for "safe mode", "be careful", "I'm nervous about this", "don't break anything", "I'm scared", "let me approve everything", "step by step", or explicitly toggles it on. Persists for the rest of the session unless the user says "normal mode" / "ok turn safe mode off". While active, every write, delete, install, or script-run is proposed in plain language with action + consequence + undo path, then waits for explicit "yes" before executing. Reads stay unrestricted. Small reversible actions can be batched in one prompt; never batch a destructive action with anything else.
---

# safe-mode

The user has asked for an explicit gate on every action that changes anything. While active, you do not write, delete, install, or run scripts without saying *exactly* what you're about to do and getting a "yes."

This is a stronger gate than [`guard-rails`](../guard-rails/SKILL.md). `guard-rails` fires on anti-patterns; safe-mode fires on **all** writes.

---

## Triggers (turn ON)

- "safe mode"
- "go safe mode" / "turn on safe mode"
- "be careful"
- "I'm nervous about this"
- "don't break anything"
- "I'm scared"
- "let me approve everything"
- "step by step"
- "ask before each thing"

When the user says any of these, confirm:

> ✅ Safe mode on. From here, I'll propose every change in plain language and wait for your "yes" before doing it. Reads (looking at files, listing folders) stay unrestricted. Say "normal mode" to turn it off.

---

## Triggers (turn OFF)

- "normal mode"
- "turn off safe mode"
- "ok safe mode off"
- "you can just do it"

Confirm:

> ✅ Safe mode off. I'll still pause for the dangerous stuff (force push, rm -rf, secrets — those are guard-rails' job), but routine changes I'll just make.

If the user toggles off mid-task, finish the current confirmed action, then revert to normal flow.

---

## What gets gated

| Action | Gated? |
|---|---|
| Reading a file | No |
| Listing a directory | No |
| Searching code | No |
| Running a read-only command (`git status`, `ls`, `cat`) | No |
| Creating a file | **Yes** |
| Editing a file | **Yes** |
| Deleting a file | **Yes** (and check `guard-rails` block tier) |
| Installing a package | **Yes** |
| Running a build / dev server / test | **Yes** |
| Running any script (`npm run …`, `python script.py`) | **Yes** |
| `git add` / `commit` / `push` / `pull` / branch ops | **Yes** |
| Network calls in a tool (curl, fetch from a script) | **Yes** |
| Hot-edit through a deferred tool that mutates state | **Yes** |

When in doubt — gate.

---

## Prompt format

For every gated action:

```
🔒 **Proposing:** [plain-English action]
**Consequence:** [what will be true after]
**Undo path:** [exact recovery steps, or "no undo — only re-do from memory"]
**Run it? (yes / no / explain more)**
```

The user replies "yes" → run.
The user replies "no" → don't run; ask what they want instead.
The user replies "explain more" → expand on consequence/undo, then re-prompt.

If the user says "yes to everything in this batch" but you proposed something destructive in the batch, you do **not** auto-confirm the destructive item — re-prompt for that one specifically.

---

## Batching small reversible actions

If you have 3+ tiny, fully reversible actions (e.g., create three new files in a fresh folder), you can batch:

```
🔒 **Proposing batch:**
1. Create `src/Nav.astro` (component, ~20 lines)
2. Create `src/Footer.astro` (component, ~10 lines)
3. Edit `src/pages/index.astro` to import both

**Consequence:** Three new/edited files. None overwrite existing content (Nav and Footer don't exist yet; the index edit adds 2 lines).
**Undo path:** `git status` to see all three; `git restore <file>` for the edit, `rm` for the new ones.
**Run all 3? (yes / no / one at a time)**
```

**Never batch a delete, an install, or a script-run** with anything else. Each of those is its own prompt.

---

## Composition

- **`guard-rails` block-tier action under safe-mode:** the safe-mode prompt absorbs the guard-rails warning (no double-prompt). You include the risk and the safer alternative in the safe-mode prompt itself, so the user gets one decision point with all the context.

- **First-encounter skills under safe-mode:** the teaching content (mental model, definitions) comes through normally; the *action* still goes through the safe-mode gate. So a first git commit becomes:

  > Here's what a commit is: [definition]. Here's the staging model: [diagram]. Now —
  >
  > 🔒 **Proposing:** stage all changes (`git add .`) and commit with the message "initial commit."
  > **Consequence:** Snapshot of your project saved to git history. Working files unchanged.
  > **Undo path:** `git reset HEAD~1` (un-commits but keeps your changes).
  > **Run it? (yes / no / explain more)**

- **`coding-mentor` footer:** still applies. After a confirmed batch, write the footer.

---

## Tone

- Calm. Confirmation prompts should feel like a copilot reading you the menu, not a security guard.
- No re-explaining safe-mode every prompt. The user opted in; they remember.
- Honor "yes" without flourish. "Doing it." then run. No "Great choice!"

---

## Graduation signal

The user disables safe-mode mid-session, often after they realize they trust your proposals. Don't push back. Don't talk them into it. Don't talk them out of it.

If they re-enable later (e.g., for a deploy), don't comment on the toggling.

---

## Examples

### Example: turn on, then a small edit

User: "ok let's go safe mode for this part, I'm working on prod data"

> ✅ Safe mode on. From here, I'll propose every change and wait for your "yes." Reads stay unrestricted. Say "normal mode" to turn it off.

User: "fix the typo in the heading on /about"

> [reads the file]
>
> Found it — `<h1>Aboot Us</h1>` in `src/pages/about.astro` line 8.
>
> 🔒 **Proposing:** edit `src/pages/about.astro` line 8, change "Aboot" to "About".
> **Consequence:** the page title displays correctly. One-character change.
> **Undo path:** `git restore src/pages/about.astro` (one file).
> **Run it? (yes / no / explain more)**

### Example: batched small adds

User: "scaffold a Nav and Footer and wire them up"

> 🔒 **Proposing batch:**
> 1. Create `src/components/Nav.astro` with three placeholder links.
> 2. Create `src/components/Footer.astro` with one placeholder line.
> 3. Edit `src/pages/index.astro` to import and render both.
>
> **Consequence:** Three new/edited files. No existing content overwritten.
> **Undo path:** `rm` the two new files; `git restore src/pages/index.astro` for the edit.
> **Run all 3? (yes / no / one at a time)**

User: "yes"

> [creates and edits all 3]
>
> Done. Both components render on the home page now.
>
> **What we did:** Added Nav and Footer components, wired them into the home page.
> **What it means:** You can now reuse the same Nav and Footer on any page just by importing them.
> **What's next:** I suggest we test by previewing — `bun run dev` and open `localhost:4321`. Want me to start the dev server? (Still in safe mode, so I'll prompt before running it.)

### Example: destructive in middle of batch — re-prompt

User: "delete the old Nav, create a new one, and update the page"

> Two of those are reversible (create + edit). One is a delete — I won't batch that.
>
> First:
>
> 🔒 **Proposing:** delete `src/components/OldNav.astro`.
> **Consequence:** the file is gone from disk. If it's not committed yet, no recovery.
> **Undo path:** if committed, `git restore src/components/OldNav.astro`. If not committed, retype from memory.
> **Run it? (yes / no / explain more)**

(Wait for yes, then propose the create+edit batch separately.)

---

## What this skill is NOT

- **Not always-on.** It's opt-in. `guard-rails` is the always-on safety; safe-mode is the elective gate.
- **Not capability reduction.** Everything Claude Code can normally do, it can still do — just one extra confirmation per action.
- **Not a permission denial.** You don't refuse to do anything legal; you wait.
- **Not silent.** Every action gets a one-paragraph prompt, even tiny edits. That's the contract.
