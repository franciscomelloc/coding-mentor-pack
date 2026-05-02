# Glossary

Plain-English definitions for every technical term any skill in this pack defines. **One source of truth.** Skills reference entries by linking ("see glossary: branch") rather than redefining.

If a skill needs a term that isn't here, add it here first, then reference it.

Format: term, one-line definition, optional "see also."

---

### anti-pattern
A common mistake — a thing many beginners do that looks fine but causes trouble later. Each one in this pack is tagged `block` (stop and ask) or `warn` (advise and proceed). See also: block tier, warn tier.

### API key
A password-like string that proves to a service it's really you calling. Treat it like a password: never commit, never paste in chat, never share. See also: secret, env var.

### block tier
The stricter of guard-rails' two safety levels. Action is irreversible or expensive to recover from, so guard-rails stops and requires an explicit "yes" before running it. See also: warn tier, undo path.

### branch
A parallel line of development in git. Like a save-game slot: you can experiment on a branch and switch back without losing your main work. See also: commit, merge.

### CLI
Command-line interface. The text-based way to drive a program, by typing commands instead of clicking. `git`, `npm`, `gh`, and Claude Code itself are CLIs.

### clone
Make your own local copy of a repo from somewhere else (usually GitHub). After cloning, you have the full history, not just the latest files. See also: fork, remote.

### commit
A saved snapshot of your project at one point in time, with a short message describing what changed. Commits are git's atom — every undo, branch, and merge is built on them. See also: branch, diff, working directory.

### conflict (merge conflict)
When two branches change the same lines and git can't decide which version wins, so it asks you. Looks scary, isn't — you read both versions, pick or combine, save. See also: merge, branch.

### dependency
A package your project needs to run. Listed in a manifest (like `package.json`) and installed by a package manager into a folder like `node_modules`. See also: devDependency, lockfile, manifest.

### devDependency
A package needed only while developing, not when the app actually runs (e.g., test frameworks, linters). Distinct from regular dependencies. See also: dependency.

### diff
The list of exact line-by-line changes between two snapshots. `git diff` shows what's changed since your last commit. See also: commit.

### dotenv
A small library (the `dotenv` package in Node, `python-dotenv` in Python) that reads a `.env` file and loads its values into env vars at startup. See also: .env file, env var.

### .env file
A plain-text file where you stash config and secrets for local development, one `KEY=value` per line. Always add it to `.gitignore`. Never commit it. See also: env var, secret, gitignore.

### entry point
The file your project starts running from. In Node, often `index.js` or whatever `package.json`'s `main` or `start` script points at. In Python, often `main.py` or a `__main__.py`. See also: manifest.

### env var (environment variable)
A named value that lives in your shell or process environment, not in code. Used to feed config and secrets into a program without hardcoding them. See also: .env file, secret.

### error
A message from a program saying it can't do what you asked. Not a personal failure — it's information. Common categories: syntax, runtime, type, dependency, network, permission. See also: stack trace.

### fork
Your own copy of someone else's repo on GitHub, hosted under your account. Used when you want to change someone else's project (you can't push to theirs, but you can to your fork). See also: clone, pull request.

### gitignore (`.gitignore`)
A file listing patterns git should ignore. Anything matched is never staged, committed, or pushed. Where `.env`, `node_modules`, build outputs, secrets all go.

### global install
Installing a package system-wide instead of into one project (e.g., `npm install -g`). Avoid by default — projects should declare what they need and install locally. See also: dependency, package manager.

### graduation signal
An observable behavior showing you've internalized a concept — the skill teaching it can step back. Each skill in this pack lists its own. Skills don't disable, they soften.

### HEAD
Git's name for "where you are right now" — the latest commit on your current branch. When you commit, HEAD moves forward. See also: branch, commit.

### hot reload
Dev-server feature that re-runs your code automatically when you save a file, so you see changes without restarting. Doesn't work for every kind of change (config, dependencies). See also: dev server.

### HTTPS auth
Authenticating to GitHub over HTTPS, using a username and a personal access token (PAT) instead of a password. The recommended path for beginners. See also: PAT, SSH key.

### lockfile
The file (`package-lock.json`, `yarn.lock`, `bun.lockb`, `requirements.txt` if pinned, `poetry.lock`, etc.) that records the exact resolved versions of every dependency. Commit it. It's how everyone gets the same install. See also: dependency, manifest, semver.

### localhost
Your own machine, addressable as `localhost` or `127.0.0.1`. A dev server on `localhost:3000` is only reachable by you. See also: port, dev server.

### manifest (package manifest)
The file that declares your project's name, version, scripts, and dependencies — `package.json` in Node, `pyproject.toml` or `requirements.txt` in Python, etc. See also: dependency, lockfile.

### merge
Combine the changes from one branch into another. Often clean; sometimes produces a conflict. See also: branch, conflict.

### node_modules
The folder Node package managers (npm, yarn, bun) install dependencies into. Huge, regenerable, and gitignored — you never commit it. See also: dependency, gitignore.

### origin
The default name git gives to the remote you cloned from. `git push origin main` means "push my main branch to the remote called origin." See also: remote, upstream.

### PAT (personal access token)
A long random string GitHub generates for you to use instead of a password when authenticating over HTTPS. Created in GitHub Settings → Developer settings. See also: HTTPS auth.

### package manager
A tool that installs dependencies for you and tracks them. Examples: `npm`, `yarn`, `bun`, `pnpm` (Node); `pip`, `poetry`, `uv` (Python); `cargo` (Rust); `bundler` (Ruby). See also: dependency, manifest, lockfile.

### port
A number identifying a specific channel on your machine. Dev servers usually pick `3000`, `4000`, `5173`, `8080`. Two programs can't use the same port — that's the `EADDRINUSE` error. See also: localhost.

### pull
Fetch the latest commits from a remote and merge them into your current branch. `git pull` = `git fetch` + `git merge`. See also: push, remote.

### pull request (PR)
A GitHub feature for proposing your changes to someone else's branch (usually `main`). Reviewers comment, approve, and then merge. The unit of collaboration on GitHub. See also: fork, branch, merge.

### push
Send your local commits to a remote so others (or another machine) can see them. `git push origin main`. See also: pull, remote.

### push --force (force push)
Overwriting the remote's history with yours. Dangerous — destroys other people's commits if they're ahead of you. Block-tier in guard-rails, especially against `main`. See also: block tier.

### rebase
Replay your commits on top of a different base (e.g., latest `main`), as if you'd started from there. Cleaner history than merge, but rewrites commits — never rebase shared branches. See also: merge.

### remote
A pointer to a copy of your repo somewhere else, usually on GitHub. `origin` is the default name. You push to and pull from remotes. See also: origin, upstream.

### repo (repository)
A folder under git's control. Has a `.git/` subfolder holding all the history. A GitHub "repo" is the same thing, hosted on GitHub. See also: clone, remote.

### runtime
The thing that runs your code. Node.js for JavaScript on the server, the browser for JavaScript in a page, Python for `.py` files, Bun as a Node alternative, etc. See also: dev server.

### secret
Anything sensitive that authenticates you or grants access: API keys, passwords, tokens, private SSH keys. Treat the same: env vars only, never in git, never in chat. See also: API key, env var, .env file.

### semver (semantic versioning)
The version number scheme `MAJOR.MINOR.PATCH` (e.g., `2.5.1`). In manifests, `^2.5.1` means "any 2.x.x ≥ 2.5.1," `~2.5.1` means "any 2.5.x ≥ 2.5.1," exact `2.5.1` means just that. See also: lockfile.

### SSH key
A pair of cryptographic keys (public + private) used to authenticate to GitHub without a password. Alternative to HTTPS+PAT, more setup, fewer prompts later. See also: HTTPS auth.

### stack trace
The list of function calls that led up to an error, top of the list = where it broke, bottom = where it started. Read top-to-bottom, look for the first line that's *your* code. See also: error.

### staging area (index)
Git's holding zone between your working files and the next commit. `git add` puts changes in staging; `git commit` snapshots whatever's in staging. See also: commit, working directory.

### stash
A temporary save of your current uncommitted changes, set aside so you can switch branches or pull cleanly. `git stash` to save, `git stash pop` to restore. See also: working directory.

### tag
A name pinned to a specific commit, usually for releases (`v1.0.0`). Doesn't move when you commit again. See also: commit.

### undo path
The exact commands or steps you'd run to reverse an action. Every guard-rails block-tier prompt includes one. If there isn't an undo path, the action is irreversible — extra caution.

### upstream
The repo your fork was made from. Different from `origin` (your fork). You pull from upstream to stay current with the project you forked. See also: fork, origin, remote.

### virtualenv (Python)
An isolated Python environment with its own dependencies, separate from your system Python. Created with `python -m venv .venv`, activated with `source .venv/bin/activate`. The Python equivalent of `node_modules` isolation. See also: dependency.

### warn tier
The lighter of guard-rails' two safety levels. Action is reversible or low-risk; guard-rails notes the concern in one line and proceeds unless you object. See also: block tier.

### working directory
The actual files you can see and edit on disk in your repo. Distinct from staging (next commit) and the repo (committed history). See also: staging area, commit.
