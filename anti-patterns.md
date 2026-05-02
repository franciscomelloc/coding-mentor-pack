# Anti-Patterns Catalog

The detection table for `guard-rails`. Each entry has a **tier**, a **trigger** Claude can recognize, and a **safer alternative** to propose.

**Tiers:**

- **`block`** — irreversible or expensive to recover. Stop, explain risk in one sentence, propose alternative, require explicit `yes` before proceeding.
- **`warn`** — reversible or low-risk. Note the concern in one line, proceed unless the user objects.

When `safe-mode` is active, all entries effectively become `block`-equivalent — no double-prompt.

---

## Block tier

### 1. Force-pushing main / master
**Trigger:** `git push --force` or `git push -f` against `main`, `master`, or any default branch.
**Risk:** overwrites everyone else's commits on the remote — gone, no easy recovery.
**Safer:** `git pull --rebase origin main` first, resolve conflicts, then a normal `git push`. Or push to a feature branch and open a PR.
**Undo path if it already happened:** `git reflog` on a teammate's machine to find the lost commits, then they push them back.

### 2. `rm -rf` of a directory
**Trigger:** `rm -rf <anything>`, especially with variables (`rm -rf $VAR/`), wildcards (`rm -rf *`), or paths that resolve up (`rm -rf ../`).
**Risk:** deletes the directory tree with no Trash, no Recycle Bin, no undo. Empty `$VAR` becomes `rm -rf /`.
**Safer:** delete files individually with `rm <file>`, or move to a `.trash/` folder, or use `git clean -nf` (dry-run) first.

### 3. `git reset --hard`
**Trigger:** `git reset --hard <anything>` when there are uncommitted changes or unpushed commits.
**Risk:** discards your uncommitted work and any local commits past the reset point. Reflog can save you, but only for ~30 days.
**Safer:** `git stash` to set aside changes, or `git reset` (without `--hard`) to keep changes in your working directory.

### 4. Committing `.env` / secret files
**Trigger:** `git add .env`, `git add *.pem`, `git add id_rsa`, or any `git add` of a file whose contents contain `API_KEY=`, `SECRET=`, `PASSWORD=`, `TOKEN=`, or PEM headers.
**Risk:** secrets in git history are forever, even if you delete the file in the next commit. Anyone who clones can read them.
**Safer:** add the file to `.gitignore` first, then `git rm --cached <file>` if it was already staged. If it's already pushed, **rotate the secret immediately** — assume it's compromised.

### 5. Pasting secrets into chat
**Trigger:** user paste contains a string matching `sk-...`, `ghp_...`, `xoxb-...`, AWS access key patterns, JWT (`eyJ...`), or anything labeled "key", "secret", "token" with a value.
**Risk:** the value is now logged in conversation history; consider it leaked.
**Safer:** Claude immediately asks the user to (a) rotate the key, (b) paste only after redaction in their next reply. Do not store, repeat, or echo the original.

### 6. `DROP TABLE` / destructive SQL
**Trigger:** `DROP TABLE`, `DROP DATABASE`, `TRUNCATE`, `DELETE FROM <table>` without a `WHERE`, `ALTER TABLE ... DROP COLUMN`.
**Risk:** data loss; on production, possibly irrecoverable.
**Safer:** run on a backup or staging copy first, take a snapshot, write the inverse migration alongside.

### 7. `chmod 777` on system or user-data paths
**Trigger:** `chmod 777` or `chmod -R 777` on `/`, `~`, `~/.ssh`, `/etc`, `/var`, or other non-project paths.
**Risk:** opens files to anyone on the system, breaks SSH key trust, can lock you out of services.
**Safer:** narrow permissions (`chmod 644` for files, `755` for dirs), narrow scope (only the directory you're working in).

### 8. Curl-piping unverified scripts
**Trigger:** `curl ... | sh`, `curl ... | bash`, `wget ... | sh`, or downloading a `.sh` and running without reading.
**Risk:** runs arbitrary code from a URL with your full user privileges.
**Safer:** download to a file first, read it, then run. For trusted installers (e.g., rustup), accept after explaining the trust assumption explicitly.

### 9. `git branch -D` of an unmerged branch
**Trigger:** `git branch -D <branch>` (capital `-D` = force delete) when the branch has commits not present on any other branch.
**Risk:** loses all commits on that branch unless you remember the SHA. Reflog only.
**Safer:** `git branch -d <branch>` (lowercase) refuses if unmerged. Merge first, or push the branch to a remote as a backup, before force-deleting.

### 10. Editing migrations after they've been applied
**Trigger:** modifying a migration file that's already been run on any environment (dev, staging, prod).
**Risk:** schema drift between environments, cryptic future failures.
**Safer:** write a new migration that does what you want; never edit the old one.

---

## Warn tier

### 11. `rm` of a single named file the user just edited
**Trigger:** `rm <file>` where the file was modified in this session.
**Concern:** uncommitted work goes away.
**Note:** "Heads up — `<file>` has unsaved changes. Continuing will lose them." Proceed unless user stops.

### 12. Global package install
**Trigger:** `npm install -g`, `pip install --user`, `pip install` outside a virtualenv, `bun add -g`, `gem install` without a Gemfile.
**Concern:** pollutes the system; project doesn't declare what it needs; reproducibility breaks.
**Note:** "This installs system-wide. Usually you want this in the project (`npm install <pkg>` instead of `-g`)."

### 13. `git stash drop` / `git stash clear`
**Trigger:** explicitly removing stashes.
**Concern:** stashed changes go to the reflog, recoverable but not obvious.
**Note:** "This drops the stash. If you want to restore later, do `git stash pop` instead."

### 14. Editing a config file the user hasn't viewed in this session
**Trigger:** modifying `tsconfig.json`, `.eslintrc`, `package.json` scripts, `vite.config.*`, etc., when the user hasn't read it this session.
**Concern:** invisible behavior changes.
**Note:** "I'll change `<file>`. Want to see what's in it first?"

### 15. Running a one-off shell from an LLM-suggested command
**Trigger:** Claude suggests a shell command the user hasn't seen before, and the user asks to run it.
**Concern:** trust calibration — beginner can't yet vet commands.
**Note:** "Here's what each part does: [breakdown]. Want me to run it?"

### 16. Installing a package not on the popular package registry top-list
**Trigger:** `npm install <pkg>` where `<pkg>` is unfamiliar or recently published.
**Concern:** typosquatting, malicious packages.
**Note:** "I haven't seen `<pkg>` before — let me check it's the package you mean. Did you mean `<similarly-named-popular-pkg>`?"

---

## Adding entries

When `guard-rails` observes a new pattern in the wild that should fire next time, append it here with: trigger, tier, risk, safer alternative, and (if block tier) undo path. Both `guard-rails` and the user benefit from the catalog being complete.
