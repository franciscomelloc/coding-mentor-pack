---
name: installing-dependencies
description: First-encounter skill for package managers, manifests, lockfiles, and dependency installs. Activate on first appearance of any package-manager concept — `npm install`, `pip install`, `bun install`, `yarn`, `cargo`, `requirements.txt`, `package.json`, `pyproject.toml`, `Gemfile` — with a soft-teach (the relevant single idea: manifest vs lockfile, or what `node_modules` is, etc.). Deepen into the full model (all five ideas — what a package manager does, manifest vs lockfile, where packages live, project-local vs global, semver) if the user shows a confusion signal: hits `Module not found` / `ImportError`, asks "what's package.json", "what does install do", "do I commit node_modules", "what's `^1.2.3`". Defer / stay quiet if the user is already fluent.
---

# installing-dependencies

The user is meeting the package-manager world for the first time. There are several core ideas to install, in order:

1. **What a package manager actually does.**
2. **Manifest vs lockfile.**
3. **Where the installed packages physically go, and why we don't commit them.**
4. **Project-local is the default; global is the exception.**
5. **Semver — what `^` and `~` mean.**

Don't dump all five at once. Let the user's actual task (running `npm install`, fixing a missing-module error, etc.) lead the way; cover the relevant ideas.

---

## Triggers

Two-stage activation.

**Soft activate on first appearance** (lead with the single idea the user's task needs — manifest vs lockfile, `node_modules` is gitignored, etc.):
- `npm install`, `npm i`, `pip install`, `bun install`, `bun add`, `yarn`, `yarn add`, `pnpm`, `cargo add`, `gem install`, `bundle install`, `requirements.txt`, `package.json`, `pyproject.toml`, `poetry`, `Gemfile`.
- Repo has a manifest but no `node_modules`/`venv`; user has cloned and is running before installing.

**Deepen into the full model** (all five ideas in order, paced to the situation) when you see a confusion signal:
- Errors: `Module not found`, `Cannot find module`, `ImportError`, `ModuleNotFoundError`, `Could not find a version that satisfies`, `npm ERR! ENOENT package.json`.
- Phrases: "what's package.json", "what does install do", "where do these go", "do I commit node_modules", "what's `^1.2.3`", "the lockfile changed", "why is `package-lock.json` so big".

**Defer / stay quiet** if the user is fluent ("bun add zod"). Run the install and move on.

---

## The five ideas

### 1. What a package manager does

> "A package manager is a tool that downloads code other people have written (libraries, frameworks) and puts them in your project so you can `import` them.
>
> You declare what you want — by name and roughly which version — and the manager figures out the exact versions and downloads them. Saves you from copying files around and tracking dependencies by hand."

Common ones:

- **JavaScript:** `npm` (default with Node), `yarn`, `pnpm`, `bun` (also a runtime).
- **Python:** `pip`, `poetry`, `uv` (newer, fast), `conda`.
- **Rust:** `cargo` (built into rustup).
- **Ruby:** `bundler` + `gem`.
- **Go:** `go mod` (built into go).

For this pack, recommend the *default* for the stack unless the project already uses something else: `npm` for Node, `pip` (or `uv` if available) for Python, `cargo` for Rust.

### 2. Manifest vs lockfile

> "Two files work together:
>
> The **manifest** says what you want. In Node it's `package.json` and lists `"axios": "^1.6.0"` — meaning 'I want axios, version 1.6.0 or compatible.' Loose, declarative.
>
> The **lockfile** says exactly what you got. `package-lock.json` (or `bun.lockb`, `yarn.lock`, `poetry.lock`, `Cargo.lock`) records the exact resolved version of every dependency *and* every dependency's dependencies, with checksums. Specific.
>
> The manifest is the wish list. The lockfile is the receipt."

(See glossary: manifest, lockfile.)

> "**Both are committed to git.** That's how anyone else who clones your repo gets *exactly* the same install you have. The lockfile is the difference between 'works on my machine' and 'works on every machine'."

### 3. Where the packages live, why we ignore them

> "When you `npm install`, npm creates a `node_modules/` folder and dumps thousands of files in there — your dependencies and their dependencies, recursively. It's huge (often hundreds of MB). It's regenerable from the manifest + lockfile.
>
> So we **gitignore** `node_modules/`. Same for Python's `venv/` or `.venv/`. Anyone can recreate it from `package.json` + lockfile by running `npm install`."

Show the `.gitignore` line:

```
node_modules/
.venv/
venv/
__pycache__/
```

### 4. Project-local vs global

> "By default, package managers install **into the current project** — the deps go into `node_modules/` next to the manifest. The project declares what it needs; reproducible.
>
> Global installs (`npm install -g`, `pip install --user`, `gem install`) put a tool on your system PATH instead. Useful for tools you run as commands (like `tsc`, `eslint`, `vercel`), not for libraries you import. **Default to project-local.**"

This is also where `guard-rails`' warn tier fires for `-g` installs the user didn't intend.

### 5. Semver in 90 seconds

> "Versions look like `MAJOR.MINOR.PATCH` — e.g., `2.5.1`. Roughly:
>
> - Bumping **major** = breaking changes (API removed, behavior changed).
> - Bumping **minor** = new features, backward-compatible.
> - Bumping **patch** = bug fixes, no new behavior.
>
> In a manifest, the prefix says how flexible you are:
>
> - `2.5.1` (exact) — only this version.
> - `~2.5.1` — any `2.5.x` ≥ `2.5.1`.
> - `^2.5.1` — any `2.x.x` ≥ `2.5.1` (same major).
>
> The lockfile pins to one exact version regardless. Run `npm install` again and it uses the lockfile; run `npm update` and it re-resolves within the manifest's range."

(See glossary: semver.)

---

## Common moves

| Goal | npm | pip | bun | cargo |
|---|---|---|---|---|
| Install all declared deps | `npm install` | `pip install -r requirements.txt` | `bun install` | `cargo build` |
| Add a new dep | `npm install <pkg>` | `pip install <pkg>` then update `requirements.txt` (or `poetry add <pkg>`) | `bun add <pkg>` | `cargo add <pkg>` |
| Add a dev-only dep | `npm install --save-dev <pkg>` | (group it; depends on tool) | `bun add --dev <pkg>` | `cargo add --dev <pkg>` |
| Remove a dep | `npm uninstall <pkg>` | `pip uninstall <pkg>` | `bun remove <pkg>` | `cargo remove <pkg>` |
| Update within manifest range | `npm update` | (re-pin and reinstall) | `bun update` | `cargo update` |

For Python, recommend creating a `venv` first if there isn't one (one-time setup):

```bash
python -m venv .venv
source .venv/bin/activate    # mac/linux
.venv\Scripts\activate       # windows
```

> "A venv is a per-project Python environment so this project's deps don't conflict with other projects' deps or your system Python."

(See glossary: virtualenv.)

---

## Composition

- **`guard-rails`** — `-g` global installs are warn-tier; piping `curl … | sh` for a package install is block-tier.
- **`coding-mentor`** — tone.
- **`translate-the-error`** — `Module not found` errors route through both: this skill explains the install model, that skill decodes the specific error.
- **`using-git`** — `.gitignore` for `node_modules`/`.venv` overlaps with git basics.
- **`safe-mode`** — installs are gated; you teach, the gate confirms.

---

## Tone

- "Manifest = wish list, lockfile = receipt" — repeat this analogy until it sticks.
- Don't dump all five ideas in one turn. Lead with the one the user's task needs.
- Don't moralize about `node_modules` size. It is what it is.

---

## Graduation signal

- User runs `npm install` (or stack equivalent) before running code, without prompting.
- User commits the lockfile and gitignores `node_modules` without being told.
- User reads a `Module not found` error and immediately runs install, then re-runs.
- User uses `--save-dev` for tools-only packages.
- User reads `^1.2.3` and knows what range it covers.

When this happens, drop to one-line answers — "`bun add zod`" — without re-explaining manifests.

---

## Examples

### Example: `Module not found` in a fresh clone

User pastes `Error: Cannot find module 'express'`

> The package isn't installed. When you clone a Node project, `node_modules/` isn't included (it's huge and regenerable), so you install fresh.
>
> Quick teach: the project declares its deps in `package.json` (the *manifest*). `npm install` reads it and downloads everything into a new `node_modules/` folder. Run once after every clone.
>
> [runs `npm install`]
>
> Done — pulled `express` and 47 other transitive deps.
>
> **What we did:** Installed the project's declared dependencies.
> **What it means:** `node_modules/` is now populated; imports resolve. The lockfile (`package-lock.json`) records exactly which versions you got — commit it.
> **What's next:** I suggest we re-run the failing command. Ready?

### Example: user wants to add a library

User: "can we use date-fns for the formatting?"

> Yeah. Quick install:
>
> [runs `npm install date-fns`]
>
> Two side effects:
> - `date-fns` gets added to `package.json` under `dependencies`.
> - `package-lock.json` updates to record the exact version.
>
> Both are commit-worthy — that's how anyone else gets the same `date-fns` you do.

### Example: adding a dev tool

User: "add prettier"

> Prettier is a tool, not a runtime dependency — your shipped code doesn't import it. So install as a dev dep:
>
> [runs `npm install --save-dev prettier`]
>
> It goes under `devDependencies` in `package.json`. Production installs (e.g., on a server) can skip it; you and CI still install everything.

### Example: graduated user

User: "add zod"

> [runs `bun add zod`]
>
> Done.

---

## What this skill is NOT

- **Not a tool comparison.** Don't run a yarn-vs-npm-vs-pnpm bake-off unless asked. Pick the default and move on.
- **Not a security audit.** `npm audit` exists but isn't beginner-territory.
- **Not always-on.** Once the user is fluent, drop out.
