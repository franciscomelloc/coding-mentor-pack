# Example: beginner makes their first commit and pushes to GitHub

A user has just built a tiny static site and wants to "save it somewhere safe so I don't lose it." They've never used git or GitHub.

---

## Without the pack

> **User:** I want to save this somewhere safe so I don't lose it.
>
> **Claude:** You can use git for version control and push it to GitHub. First initialize git: `git init`. Stage your changes: `git add .`. Commit them: `git commit -m "initial commit"`. Then create a repo on GitHub at https://github.com/new, copy the URL, and connect it: `git remote add origin <URL>`. Finally `git push -u origin main`. You'll need a Personal Access Token for authentication. Want me to run the commands?

Two new tools, eight commands, two website actions, and an authentication concept — all in one paragraph. The user has no idea what most of those words mean. They say yes because they're embarrassed to admit confusion. Halfway through, the auth prompt fails and they don't know what to do.

---

## With the pack

(`using-git` fires for the local model; `using-github` follows for the remote and auth; `coding-mentor` sets the tone; `guard-rails` quietly watches.)

> **User:** I want to save this somewhere safe so I don't lose it.
>
> **Claude:** Two parts to that. Locally, we'll use **git** — that's a tool that takes timestamped snapshots of your project. Then we'll send a copy to **GitHub**, which is one specific website that hosts git repos online. Different things, often confused.
>
> Let's do them in two stages. Stage 1 is git only — your machine, no internet, fully reversible. Stage 2 connects to GitHub.
>
> **Stage 1 — git on your machine.**
>
> Quick model: imagine git is a photo album. You take a snapshot of your whole project, give it a one-line description ("set up the homepage"), and the album keeps it forever. Each snapshot is called a **commit**. *(See glossary: commit.)*
>
> Three steps:
>
> 1. Tell git to start tracking this folder: `git init`. Creates a hidden `.git/` folder. Reversible (just delete `.git/` if you change your mind).
> 2. Stage all your files for the first snapshot: `git add .`. *(See glossary: staging area — git's "ready for the next photo" zone.)*
> 3. Take the snapshot: `git commit -m "initial commit"`.
>
> Want me to do all three?
>
> ---
>
> **User:** yes
>
> **Claude:** *(runs `git init && git add . && git commit -m "initial commit"`)*
>
> Done. Your project's first git snapshot is saved.
>
> Quick check:
>
> *(runs `git log --oneline`)*
>
> ```
> a3f2c1d (HEAD -> main) initial commit
> ```
>
> One snapshot, one branch named `main`. Standard.
>
> **What we did:** Set up git tracking and took the first snapshot of your whole project.
>
> **What it means:** Even if you delete files now, the snapshot can restore them. Your project has a memory.
>
> **What's next:** Stage 2 — put a copy on GitHub so it's safe even if your machine dies. Ready?
>
> ---
>
> **User:** ready
>
> **Claude:** **Stage 2 — GitHub.**
>
> Reminder of the split: git is what we just used (local). GitHub is one website where we'll keep an online copy. Same album, different shelf.
>
> Two halves to this stage: create the GitHub repo (you do this in a browser), then connect your local repo to it (I do this in the terminal).
>
> **Half 1: in your browser.**
>
> 1. Go to https://github.com/new
> 2. Name it (e.g., your project's folder name).
> 3. **Don't** check "Initialize with README" — your local project already has files; we'll push them.
> 4. Click "Create repository."
> 5. The next page shows a URL like `https://github.com/<you>/<repo>.git` — copy it.
>
> **Half 2: when you've got the URL, paste it here.** Then I'll do the local connect + push commands.
>
> One thing first: the first push will ask for a username and password. Use a Personal Access Token as the password. Quick setup: https://github.com/settings/tokens → "Fine-grained tokens" → Generate. Scope: "Contents — Read and write" on the repo we just made. **Generate, copy, keep handy.** You'll only see it once.
>
> Tell me when you have the GitHub URL ready.
>
> ---
>
> **User:** https://github.com/janedoe/my-site.git here's the url
>
> **Claude:** Got it.
>
> Three short commands to wire this up:
>
> 1. `git remote add origin https://github.com/janedoe/my-site.git` — tells your local repo "there's a copy of you living at that URL, and we'll call it `origin`."
> 2. `git branch -M main` — makes sure your local branch is called `main` (matches GitHub's default).
> 3. `git push -u origin main` — sends your snapshots to GitHub. The `-u` part means "and make this the default for future pushes."
>
> The third one will prompt for username and password. Username is your GitHub username; password is the Personal Access Token you generated.
>
> Want me to run them?
>
> ---
>
> **User:** yes
>
> **Claude:** *(runs the three commands; the push prompts for credentials, user pastes username + token)*
>
> ```
> Enumerating objects: 12, done.
> ...
> To https://github.com/janedoe/my-site.git
>  * [new branch]      main -> main
> branch 'main' set up to track 'origin/main' from 'origin'.
> ```
>
> Worked. Your code is now on GitHub. Open https://github.com/janedoe/my-site in a browser — you'll see your files and the commit message.
>
> **What we did:** Connected your local repo to a GitHub copy and pushed your one snapshot up.
>
> **What it means:** Your code is now in two places — your machine and GitHub. If your machine dies, the GitHub copy survives. Future `git push` (no flags needed) sends new snapshots up to the same place.
>
> **What's next:** I suggest we make a second commit so you see the workflow end-to-end — change one tiny thing (a typo in the README, anything), commit it, push it, and watch GitHub update. Want to?

---

## What's different

- **The split is taught explicitly.** "Git is the tool, GitHub is one website" — the single most important distinction, said early.
- **Two stages, with a stop between.** Stage 1 (local-only) is fully reversible and lets the user feel the model land before anything touches the internet.
- **Glossary references.** "See glossary: commit" — the term gets a one-line gloss in the moment, with a longer definition available.
- **Auth path picked, not enumerated.** HTTPS + PAT recommended directly. SSH not mentioned unless asked. Beginners don't need to learn SSH on day one.
- **Browser action and terminal action separated.** "You do this in a browser, then paste me the URL." Avoids the user trying to run a URL-creation step in their terminal.
- **Each step lists its undo path.** Reversibility is communicated, not just guaranteed.
- **The follow-up is concrete.** Not "let me know if you have questions" but "let's make a second commit so you see the workflow end-to-end."
- **`guard-rails` is silent here.** Nothing the user is doing is risky. The skill stays out of the way until something actually warrants a pause.
