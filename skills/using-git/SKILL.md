---
name: using-git
description: First-encounter skill for git. Activate on first appearance of any git concept in a conversation — `git`, "commit", "branch", "merge", "rebase", "diff", "stash", "tag", "remote", "checkout", "HEAD", "version control" — with a soft-teach (one-line gloss + glossary pointer + first-undo path). Deepen into the full mental model (snapshot model, working dir / staging / repo, branches as parallel timelines) if the user also shows a confusion signal: asks what a term means, runs a git command and asks "did that work?", describes a problem in non-git terms, contradicts the model. Defer / stay quiet if the user demonstrates fluency from the start.
---

# using-git

The user is meeting git for the first time, or has used it but doesn't have a model for it. Your job: install the model in their head before piling on commands. Most beginner git frustration comes from running commands without a model — they cargo-cult `git pull && git push` and then panic when something diverges.

This skill is **about the model**. Specific git operations during the session also benefit from the model, but the moment you teach commands without saying *what they mean*, you've slipped.

---

## Triggers

Two-stage activation. The skill always responds to first appearance, but how much it teaches depends on what else it sees.

**Soft activate on first appearance** (one-line gloss + glossary pointer + first-undo path):
- `git`, `commit`, `branch`, `merge`, `rebase`, `diff`, `stash`, `tag`, `remote`, `checkout`, `HEAD`, "version control", "I want to track changes".
- Repo is fresh (no `.git` yet), or repo exists but conversation has never touched git.

**Deepen into the full model** (photo-album metaphor, three places a file can live, branches as parallel chains) when, in addition to first appearance, you see a confusion signal:
- User asks what a term means.
- User runs a git command and asks "did that work?".
- User says "I'm new to git".
- User describes a problem in non-git terms ("I want to save my place").
- User makes a statement that contradicts the model ("does git delete the file?").

**Defer / stay quiet** if the user demonstrates fluency from the start ("rebase the feature branch onto main, force-with-lease"). Drop directly to the requested move with no model overhead.

---

## The mental model (teach this first)

Don't just define commands. Teach the model. Repeat: **don't just define commands. Teach the model.**

### Git is a chain of snapshots, not a list of changes

> "Imagine you're working on a project, and every now and then you take a complete photograph of the whole folder. Each photograph has a tiny note attached saying what's different about it. That's all git is — a chain of photographs. We call each photograph a **commit**. (See glossary: commit.)"

Most beginners think git tracks "changes." It doesn't — it tracks snapshots, and changes are computed *between* snapshots. This distinction makes everything else click later.

### Three places a file can live

```
Working directory  →  Staging area  →  Repo (committed)
   (you edit)         (git add)         (git commit)
```

> "Your project has three layers. The **working directory** is the files you edit. The **staging area** is files you've marked for the next photograph but haven't taken it yet. The **repo** is all the photographs you've ever taken. (See glossary: working directory, staging area.)"

`git status` shows all three layers at once. Run it; show the user what each section means.

### Branches are parallel chains of snapshots

> "Now imagine you can clone the photo album. You keep snapping photos in your copy, your friend snaps photos in theirs. Each copy is a **branch** — a parallel chain of snapshots from a common starting point. Most projects have one main chain (called `main` or `master`) and you create extra branches when you want to try something without affecting main. (See glossary: branch.)"

The "trying something" framing matters: branches are cheap, throwaway, parallel timelines. Beginners tend to fear branches; they shouldn't.

### Merging combines two chains

> "When you've taken some photos in your branch and want them in `main`, you **merge**. Git looks at both chains and combines them. If the same line was edited two different ways, git asks you to pick — that's a **merge conflict**. Conflicts look scary; they're just git asking 'which version?' (See glossary: merge, conflict.)"

### Remotes are copies that live elsewhere

> "Right now your photo album is just on your machine. A **remote** is the same album, copied somewhere else — usually GitHub. `git push` sends your photos to the remote; `git pull` brings down photos others have added. (See glossary: remote, push, pull.)"

We're not deep-diving GitHub here — that's `using-github`. We're naming the concept of "elsewhere copy."

---

## The core moves (after the model)

Only after the model lands. For each, name + glossary link + concrete demo with their actual files.

| Move | What it does | Undo |
|---|---|---|
| `git status` | Show what's changed and where it lives. | (read-only) |
| `git diff` | Show line-level changes in working dir. | (read-only) |
| `git add <file>` | Stage that file for the next snapshot. | `git restore --staged <file>` |
| `git commit -m "msg"` | Take the snapshot with that message. | `git reset HEAD~1` (un-commits, keeps changes) |
| `git log --oneline` | List recent snapshots. | (read-only) |
| `git branch <name>` | Make a new branch at HEAD. | `git branch -d <name>` (only if unmerged check passes) |
| `git checkout <branch>` (or `git switch <branch>`) | Move to that branch. | `git switch -` (back to previous) |
| `git merge <branch>` | Combine that branch into the current one. | `git merge --abort` mid-merge; `git reset --hard <prev>` after |
| `git push` | Send to remote. | (depends — `using-github` covers safe undoes) |
| `git pull` | Get from remote. | (depends — usually a fresh clone is the simplest restore) |

**Always show the undo path with the move.** That's the difference between "I did the thing" and "I understand the thing."

---

## Two operations to be careful about (defer to `guard-rails`)

- `git push --force` and `git push -f` — block tier. `guard-rails` handles the prompt; you fill in the model: "force push overwrites the remote's chain with yours, including any photos others added that aren't in your chain. That's why we don't do it on shared branches."
- `git reset --hard` — block tier when there's uncommitted work. Same flow.

You don't replicate guard-rails' prompts — you teach the *why* once the user encounters them.

---

## Composition

- **`coding-mentor`** — tone.
- **`guard-rails`** — destructive git operations route through guard-rails; you provide the conceptual context.
- **`using-github`** — separate skill; remote/PR/auth lives there. You teach git locally; that skill teaches the GitHub layer on top.
- **`safe-mode`** — every git command that writes is gated; you teach the move, the gate handles confirmation.
- **`translate-the-error`** — git errors are some of the most decodable; pair tightly. "Updates were rejected" → "the remote has photos you don't; pull first."
- **`what-changed`** — perfect after a multi-commit session; uses git's own info as the source.

---

## Tone

- Photo-album metaphor or another concrete one — don't lecture in the abstract.
- Run `git status` early and often. It's the cheapest way to anchor the model in their actual files.
- Don't apologize for git's UI. It's gnarly; the model is sound. Learn the model and the UI is decodable.

---

## Graduation signal

Look for these unprompted:

- User runs `git status` before asking what's going on.
- User reads `git diff` output and identifies what they changed.
- User says "this should be in its own branch" before being told.
- User uses terms correctly: "I haven't staged that yet", "let me commit first", "this branch has diverged from main".
- User catches a destructive move themselves: "I should rebase first, or we'll get a merge conflict."

When this happens:

- Drop the photo-album metaphor.
- Skip the staging-area definition.
- Just help with the specific move.
- Footer can shorten or drop.

---

## Examples

### Example: first git contact

User: "I made changes — how do I save them?"

> You've already saved them on disk (the file save you're used to). To track them in git — the tool that takes timestamped snapshots of your project — we'll do two things: stage the changes (mark them for the next snapshot), then commit (take the snapshot).
>
> Quick model: imagine git is a photo album. Right now your files have unsaved-to-album changes. We `git add` them — that's putting them in the staging area, our "ready for the next photo" zone — and then `git commit` — that's clicking the camera. (See glossary: commit, staging area.)
>
> Let me show you the current state first:
>
> [runs `git status`]
>
> ```
> Modified: src/index.js
> Modified: README.md
> ```
>
> Two files changed, none staged yet. I'll stage and commit them as one snapshot.
>
> 🔒 *(if safe-mode active, gates here; otherwise:)*
>
> [runs `git add src/index.js README.md && git commit -m "wire up the new route and document it"`]
>
> Done. Your first git snapshot is saved. Run `git log --oneline` to see it.
>
> **Undo path** (in case you ever want it): `git reset HEAD~1` un-commits but keeps your file edits.
>
> **What we did:** Took your first git snapshot — staged 2 files and committed them.
> **What it means:** Git now has a record of "this is what the project looked like at this moment." You can rewind here later.
> **What's next:** I suggest we make a *branch* before any more changes — that way risky experiments live separately from the working version. Want me to explain branches and create one?

### Example: user asks about merge conflict mid-session

User: "what's a merge conflict?"

> When you merge two branches that both edited the same line, git can't decide which version to keep — so it puts both versions in the file with `<<<<<<<` markers and asks you to pick.
>
> Looks like:
>
> ```
> <<<<<<< HEAD
> const port = 3000;
> =======
> const port = 4000;
> >>>>>>> feature-branch
> ```
>
> You edit the file, keep what you want, remove the markers, save. Then `git add <file>` and `git commit`. That's it.
>
> Conflicts feel scary because the markers look weird. They're not — git is just asking which version. (See glossary: conflict.)

### Example: graduated user

User: "stage and commit the readme"

> [runs `git add README.md && git commit -m "update readme"`]
>
> Done. `README.md` committed.

---

## What this skill is NOT

- **Not a `git` cookbook.** You teach the model and the moves it leads to. For exotic recipes (reflog rescue, interactive rebase), explain on demand, don't preempt.
- **Not GitHub.** Remotes, PRs, forks, auth — that's `using-github`.
- **Not always-on.** Once the model lands, you go quiet for that user.
