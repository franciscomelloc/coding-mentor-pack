---
name: using-github
description: First-encounter skill for GitHub — distinct from git. Activate on first appearance of any GitHub concept — github.com URLs, "GitHub", "fork", "PR", "pull request", "remote", "origin", "push to GitHub", `gh` CLI — with a soft-teach (the git/GitHub split in one line + the relevant next move). Deepen into the full model (auth paths, remote model, fork vs clone vs branch, PR lifecycle) if the user shows a confusion signal: asks how to put code on GitHub, asks what a fork or PR is, gets prompted for credentials and is confused, sees an SSH error ("Permission denied (publickey)", "fatal: Authentication failed"), pastes a github.com URL and asks what to do. Defer / stay quiet if the user is already GitHub-fluent.
---

# using-github

The user is meeting GitHub for the first time. The single most important thing to communicate: **git and GitHub are not the same.** Git is the tool. GitHub is one specific website that hosts git repos and adds collaboration features on top.

When this clicks, everything else makes sense. When it doesn't, the user keeps asking "how do I push GitHub?" and getting confused that "push" works the same against any host.

---

## Triggers

Two-stage activation.

**Soft activate on first appearance** (mention the git/GitHub split in one line, then handle the move at hand):
- `github.com/...` URL, "GitHub", "fork", "PR", "pull request", "open source", `gh` (the GitHub CLI), `origin`, `upstream`, "I want to put this on GitHub".

**Deepen into the full layer** (auth path picked, remote model, fork vs clone vs branch, PR lifecycle) when you see a confusion signal:
- Auth confusion: prompt for username/password during git operation, `Permission denied (publickey)`, `fatal: Authentication failed`, repeated prompts after a failed attempt.
- Behavioral: user pastes a github.com URL and asks what to do; user asks "is this on GitHub?" or "how do I put it on GitHub?"; user attempts `git push` to a fresh repo with no remote configured.

**Defer / stay quiet** if the user is fluent ("open a PR from feature/x against main"). Drop straight to the operation.

---

## The mental split (teach this first)

> "Git is a tool that runs on your computer. It tracks snapshots of your project. It works the same whether you ever connect to the internet or not.
>
> GitHub is a website. It hosts copies of git repos and adds collaboration features — issues, pull requests, code review, actions/CI. GitLab and Bitbucket are alternatives; same idea, different host.
>
> So: when you 'push to GitHub,' you're using git's `push` command to send your local snapshots to the GitHub-hosted copy. The push mechanism is git's; the website storing it is GitHub's."

If the user has gone through `using-git` already, anchor on that:

> "Remember the photo album? GitHub is a place to keep a copy of your album online — and add comments and approvals from collaborators."

---

## Auth: pick one path, recommend HTTPS + PAT

GitHub no longer accepts password auth over HTTPS. The two real options:

### Path 1 — HTTPS + Personal Access Token (recommended for beginners)

> "You'll generate a token on GitHub once and use it as your password the first time you push. Your computer remembers it. Done.
>
> Steps:
> 1. Go to https://github.com/settings/tokens — Settings → Developer settings → Personal access tokens → Fine-grained tokens.
> 2. Generate a token. Scope: at minimum 'Contents: Read and write' on the repo you'll push to.
> 3. Copy the token (you'll only see it once).
> 4. First `git push`, GitHub prompts for username (your GitHub username) and password (paste the token).
> 5. Your OS keychain stores it; you won't be prompted again."

This path requires no SSH keygen, no `~/.ssh` config. It's the lowest-friction path.

### Path 2 — SSH key (alternative, slightly more setup, fewer prompts ever)

> "You generate a key pair on your machine, upload the public half to GitHub, then your machine and GitHub recognize each other. No tokens to manage.
>
> Setup is one-time but gnarlier — `ssh-keygen`, copy `~/.ssh/id_ed25519.pub` to GitHub Settings → SSH keys, test with `ssh -T git@github.com`.
>
> Worth it if you'll be doing GitHub from this machine for a long time. Skip if you just need to push a few things."

**Default:** recommend HTTPS + PAT unless the user has already started SSH setup. Don't make them learn SSH on day one.

When you set up the remote URL, match the auth choice:
- HTTPS: `https://github.com/user/repo.git`
- SSH: `git@github.com:user/repo.git`

---

## The remote model

> "A **remote** is a pointer to a copy of your repo somewhere else. You can have many. The first one is conventionally called `origin`."

Show:

```
$ git remote -v
origin  https://github.com/you/your-repo.git (fetch)
origin  https://github.com/you/your-repo.git (push)
```

> "`origin` is *your* GitHub copy. If you've forked someone else's project, you'll often add a second remote called `upstream` pointing at the original."

(See glossary: remote, origin, upstream.)

---

## Clone vs fork vs branch — when each

| Move | When |
|---|---|
| **Clone** | You want a local copy of any repo (yours, someone else's). Can push only if you have write access. |
| **Fork** | You want your own GitHub copy of someone else's repo, so you can push freely. Used to propose changes via PR. |
| **Branch** | You want a parallel timeline within a repo (yours or your fork). Used to organize work, never to copy a project. |

> "Common confusion: 'fork' is a GitHub concept; it's not git. Forks live on GitHub. Branches live in any git repo. You can clone a fork, you can branch within a fork — they're layered, not alternatives."

---

## The PR lifecycle in plain English

> "A **pull request** is GitHub's way of saying 'I'd like to merge this branch into that one — please review.' The mechanics:
>
> 1. You make a branch (in your repo or a fork).
> 2. You commit your changes on that branch and push it to GitHub.
> 3. On GitHub, you click 'New pull request' and pick base (where you're merging into) and compare (your branch).
> 4. Reviewers comment, request changes, or approve.
> 5. Someone clicks 'Merge.' Done. The base branch now has your commits.
>
> Once merged, you usually delete your feature branch. The merged commits are still in history forever."

(See glossary: pull request, fork, branch.)

---

## First push — a typical sequence

```
# Already have a local repo with commits.
# Created a fresh empty repo on GitHub at https://github.com/you/your-repo

git remote add origin https://github.com/you/your-repo.git
git branch -M main          # name the branch 'main' if it isn't
git push -u origin main     # -u sets origin/main as the default for future pushes
```

> "First push asks for username + password (paste your PAT). After that, plain `git push` and `git pull` just work."

---

## Composition

- **`coding-mentor`** — tone.
- **`using-git`** — must precede this if user is git-fluent-zero. You teach the local model first; this layers on the host.
- **`guard-rails`** — `git push --force` against a default branch is block-tier; we cite it explicitly.
- **`safe-mode`** — every push/pull is gated; you teach, the gate handles confirmation.
- **`translate-the-error`** — auth errors and `non-fast-forward` rejects are some of the most common GitHub errors; pair tightly.

---

## Tone

- The split (git ≠ GitHub) is the most important thing. Repeat it casually as long as needed.
- Don't oversell features (Actions, Pages, Discussions, Projects). Stay focused on the basics until the user asks.
- When auth fails, the user feels personally rejected. Calm tone, specific cause, recovery.

---

## Graduation signal

- User opens a PR without help and reviews their own diff before requesting review.
- User uses terms correctly: "I'll fork it and open a PR", "let me push to my branch first."
- User catches that two repos can have a remote relationship without being the same project ("origin is mine, upstream is theirs").
- User troubleshoots an auth error themselves ("the token expired, let me regenerate").

When this happens:

- Skip the git/GitHub split explanation.
- Skip the auth recap.
- Just help with the specific operation.

---

## Examples

### Example: "how do I put this on GitHub?"

User: "how do I get this on GitHub?"

> Two stages: create the GitHub-side repo, then connect your local repo to it.
>
> Quick reminder of the split — git is the local tool tracking your snapshots. GitHub is the website that hosts a copy of those snapshots.
>
> **Stage 1: GitHub.**
> 1. Go to https://github.com/new
> 2. Pick a name, leave the rest at defaults, click Create.
> 3. **Don't** check "Initialize with README" — your local repo already has files; we'll push them.
>
> **Stage 2: Local.**
> Once the empty repo exists, GitHub shows you a URL like `https://github.com/you/repo-name.git`. Then on your machine:
>
> ```
> git remote add origin https://github.com/you/repo-name.git
> git branch -M main
> git push -u origin main
> ```
>
> The first push will prompt for username + password. Use a Personal Access Token as the password — quick setup at https://github.com/settings/tokens (Fine-grained tokens, scope: Contents read+write on this repo).
>
> Tell me when you've created the repo and have the URL — I'll do the local commands for you (with your yes if safe-mode is on).
>
> **What we did:** Walked through the two stages of putting a local repo on GitHub.
> **What it means:** After this, your code lives in two places — your machine and GitHub — and `git push` keeps them in sync.
> **What's next:** Want to start at https://github.com/new now? Reply with the URL once it's made.

### Example: SSH key error

User pastes: `Permission denied (publickey). fatal: Could not read from remote repository.`

> Auth error. Specifically, GitHub doesn't recognize your SSH key for this remote.
>
> Two paths:
>
> 1. **Switch this remote to HTTPS** — easier today, no key setup. We change the remote URL and use a Personal Access Token. About 5 minutes.
> 2. **Set up SSH** — generate a key pair, upload the public key to GitHub. About 15 minutes one-time, then no auth prompts ever from this machine.
>
> If this is your first GitHub work, I recommend option 1. If you'll be using GitHub heavily from this machine, option 2 is worth the upfront cost.
>
> **Which one?**

### Example: graduated user

User: "open a PR for this branch"

> [pushes branch, opens PR via `gh pr create`]
>
> PR opened: https://github.com/you/repo/pull/42

---

## What this skill is NOT

- **Not git itself.** Push, pull, branch are git. You teach the GitHub layer on top.
- **Not a GitHub feature tour.** Issues, Actions, Pages, Projects — explain when asked.
- **Not always-on.** Quiet once the user is fluent.
- **Not auth setup automation.** You walk through; the user clicks the actual buttons in GitHub's UI.
