---
name: using-env-vars
description: First-encounter skill for environment variables and secrets. Activate on first appearance of any env-var concept — `.env`, `process.env`, `os.environ`, `Deno.env.get`, `import.meta.env`, `export FOO=`, "API key", "secret", "config", "token", or "password" — with a soft-teach (one-line "secrets and config don't belong in code" + glossary pointer). Deepen into the full model (`.env` lifecycle, gitignore-first ordering, per-platform loading, dev vs prod) if the user shows a confusion signal: asks "where do I put my API key", "what's a `.env` file", "is it safe to put this in code", or after `guard-rails` flags a pasted secret. Defer / stay quiet if the user is already env-var-fluent.
---

# using-env-vars

The user is meeting environment variables — the standard way to feed config and secrets into a program without hardcoding them. Two ideas to install: (1) **why** env vars exist, and (2) **how** the `.env` file lifecycle works without leaking secrets.

If the user is here because they pasted a secret into chat, **`guard-rails` runs first** to handle the redaction; you arrive after to set up the proper place to put it.

---

## Triggers

Two-stage activation.

**Soft activate on first appearance** (one-line "secrets and config don't belong in code" + glossary pointer, then handle the move at hand):
- `.env`, `.env.local`, `process.env.X`, `os.environ["X"]`, `Deno.env.get("X")`, `import.meta.env.X`, `export FOO=…`, `dotenv`, `python-dotenv`.

**Deepen into the full model** (the two ideas — why env vars exist, `.env` file lifecycle — plus per-platform loading) when you see a confusion signal:
- Phrases: "where do I put my API key", "how do I store a secret", "what's a `.env` file", "is it safe to put this in the code", "config file", "where does this token go".
- Behavioral: user is about to hardcode a key in source; user is reading a README that says "set `FOO_API_KEY` in your environment" and asks how.

**Always deepen** on handoff from `guard-rails` (user pasted a secret and we redirect here for the proper home) — confusion is presumed.

**Defer / stay quiet** if the user is fluent ("add `STRIPE_KEY` to .env"). Drop straight to the move.

---

## The two ideas

### Why env vars exist

> "There's a basic rule in code: **secrets and config don't belong in the code itself.** Two reasons:
>
> 1. **Secrets in code end up in git.** If your API key is in `index.js`, it's in your git history forever — every clone, every fork, every backup. Even if you delete it later. So we keep secrets *out of files git tracks.*
>
> 2. **Config changes between environments.** The same code runs on your machine, on staging, on production. The database URL, the API endpoint, the feature flags — all different. If you hardcode `localhost:5432`, you can't deploy without editing code. Bad."

So config and secrets live in **environment variables** — values your operating system or runtime hands the program at startup, separate from the code.

### The `.env` file lifecycle

For local development, the convention is:

```
# .env  (lives in your project root)
DATABASE_URL=postgres://localhost:5432/myapp
API_KEY=sk-proj-abc123def456
DEBUG=true
```

> "One `KEY=value` per line. Plain text. No quotes needed (most loaders accept them, but skip 'em). Comments start with `#`."

Three rules:

1. **`.env` is gitignored.** The single most important step. Add `.env` to `.gitignore` *first*, before you create the `.env` file. Show the user:

   ```
   # .gitignore
   .env
   .env.local
   .env.*.local
   node_modules/
   ```

2. **Commit a `.env.example` instead.** Same shape as `.env` but with empty or placeholder values. This lets new developers know what variables they need to set.

   ```
   # .env.example
   DATABASE_URL=
   API_KEY=
   DEBUG=
   ```

3. **Never commit a real `.env`.** If you do by mistake — rotate every secret in it immediately, then remove it from git history (`git rm --cached .env`, plus rewrite history if it was pushed). Treat as compromised.

---

## Per-platform loading

Each runtime reads env vars slightly differently. Pick the one for your stack.

### Node.js

```bash
npm install dotenv
```

```js
// at the top of your entry point
import 'dotenv/config';

// then anywhere
const apiKey = process.env.API_KEY;
```

For modern Node (v20.6+) and Bun, you can skip the package and use `--env-file`:

```bash
node --env-file=.env app.js
bun --env-file=.env app.js
```

### Python

```bash
pip install python-dotenv
```

```python
from dotenv import load_dotenv
load_dotenv()

import os
api_key = os.environ["API_KEY"]
```

Or if you'd rather not have a runtime dependency: read `.env` manually with a tiny parser, or run `set -a; source .env; set +a` before launching.

### Shell (just for one-off)

```bash
export API_KEY=sk-...
node app.js          # API_KEY is now in process.env

# or inline
API_KEY=sk-... node app.js
```

These don't persist — they're only for the current shell session. Useful for testing.

### Vite / Astro / front-end frameworks

> "Front-end frameworks expose env vars at build time, not runtime. Variables that should reach the browser have a prefix (Vite: `VITE_`; Next: `NEXT_PUBLIC_`; Astro: `PUBLIC_`). **Anything in the browser is public** — never put secrets in `VITE_…` vars."

If the user is asking how to use an API key from a Vite app, the right answer is "you can't, at least not safely from the client — you proxy through a server." That's a longer conversation; flag it.

---

## Dev vs prod

> "On your machine, `.env` is the source. In production (Heroku, Vercel, Render, AWS, Fly.io, your own server), env vars are set on the host directly — usually in a dashboard or via CLI. Same shape, different values."

Concrete: if your app uses `DATABASE_URL`, you set:

- Locally: in `.env` → `DATABASE_URL=postgres://localhost:5432/myapp_dev`
- Production: in the host dashboard → `DATABASE_URL=postgres://production-host/myapp_prod`

The code reads `process.env.DATABASE_URL` either way. It doesn't know or care which environment.

---

## Composition

- **`guard-rails`** — fires on pasted secrets, takes precedence. After redaction, you teach `.env`.
- **`coding-mentor`** — tone.
- **`using-git`** — `.gitignore` is a git concept; we touch it here. If git itself is new, route to `using-git` for the broader model first.
- **`installing-dependencies`** — `dotenv`/`python-dotenv` install paths flow through there.
- **`safe-mode`** — file creation is gated; we teach, the gate confirms.

---

## Tone

- "Secrets in code end up in git" lands harder than abstract "best practices." Use the concrete frame.
- Don't lecture about all of 12-factor. The user just wants to put their key somewhere; teach the minimum.
- When you set up `.gitignore` and `.env`, do them *in that order* — gitignore first, then env. Tell the user that's the order.

---

## Graduation signal

- User creates `.env`, gitignores it, references the env var correctly without prompting.
- User catches themselves before pasting a secret in chat.
- User asks the right next-level question: "How do I set this in production?"
- User uses `.env.example` to onboard collaborators without being told.

When this happens, the skill becomes a one-line reference. ("API key — yeah, drop it in `.env`, it's gitignored.")

---

## Examples

### Example: user has hardcoded a key

User: "I put my OpenAI key in the code, is that ok?"

> Not ok if the code is in git — the key ends up in git history forever, and anyone who clones can read it.
>
> Two-step fix:
>
> 1. **Move the key out of code, into `.env`.**
> 2. **Make sure `.env` is gitignored** so future you doesn't commit it.
>
> Order matters — gitignore *first*, then create `.env`. Otherwise you can accidentally commit `.env` between the two steps.
>
> Want me to do both? I'll:
> - Add `.env` to `.gitignore` (creating it if missing).
> - Create `.env` with `OPENAI_API_KEY=<your key>`.
> - Update your code to read `process.env.OPENAI_API_KEY` instead of the hardcoded value.
>
> **One thing first:** is the key currently committed and pushed to GitHub? If yes, **rotate it now** at https://platform.openai.com/api-keys before we do anything else. Once it's rotated, the leaked one is harmless.
>
> **What we did:** Diagnosed a hardcoded secret.
> **What it means:** Hardcoded keys leak through git; we move them to env vars.
> **What's next:** Rotate the key first. Tell me when done; I'll run the 3-step fix.

### Example: setting up `.env` from scratch

User: "the README says I need to set `STRIPE_SECRET_KEY` — how?"

> That's an environment variable. The standard local setup is a `.env` file. Quick teach:
>
> Two ideas: (1) secrets and config don't belong in code (they leak via git, they change between environments); (2) the `.env` file is where they go locally, and it's gitignored so it never gets committed.
>
> Three steps:
>
> 1. Make sure `.env` is in `.gitignore` (I'll add it if missing).
> 2. Create `.env` with `STRIPE_SECRET_KEY=sk_test_…` (paste your test key).
> 3. The Node app already reads `process.env.STRIPE_SECRET_KEY` — once `.env` is loaded, it'll find the value.
>
> Step 3 needs a loader if it's not there. Two options:
> - Install `dotenv` (`npm install dotenv`) and add `import 'dotenv/config'` to the top of the entry point.
> - Run with `node --env-file=.env app.js` (Node 20.6+, no install needed).
>
> Want me to do all three? You'll paste the key when I prompt.
>
> **What we did:** Mapped the README requirement to the env-var pattern.
> **What it means:** Stripe key lives in `.env`, gitignored, loaded into `process.env` at startup.
> **What's next:** Set up `.env` and `.gitignore`? (yes / show me how to do it manually)

---

## What this skill is NOT

- **Not a secret manager.** For real production secrets, vault systems exist (1Password, Doppler, AWS Secrets Manager). Mention if asked, don't preempt.
- **Not deployment config.** "How do I set this in production?" gets a one-line "in your host's dashboard" — full deploy is `running-locally` → out-of-scope deploy skill.
- **Not always-on.** Quiet once the user has the model.
