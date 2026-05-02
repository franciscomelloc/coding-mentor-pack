---
name: running-locally
description: First-encounter skill for runtimes, processes, dev servers, ports, and "run on my machine." Activate on first appearance of any local-run concept — `npm run dev`, `npm start`, `python app.py`, `bun dev`, `localhost:3000`, "the server", "port" — with a soft-teach (one-line "long-running process listening on a port" + the URL to open). Deepen into the full model (runtime, process, localhost, ports, hot reload) if the user shows a confusion signal: asks "how do I run this", "what's localhost", "what port", "can my friend see this URL", hits `EADDRINUSE` / `Address already in use`, says the page is blank or the server "crashed". Defer / stay quiet if the user is already fluent.
---

# running-locally

The user is meeting the dev-server world for the first time. The mental model is small but specific: a long-running process listens on a port on your local machine; your browser talks to it; nobody else can. Stop it with Ctrl+C. Most beginner confusion comes from missing one of those pieces.

---

## Triggers

Two-stage activation.

**Soft activate on first appearance** (one-line "long-running process listening on a port" + the URL to open + Ctrl+C to stop):
- `npm run dev`, `npm start`, `python app.py`, `flask run`, `uvicorn`, `bun dev`, `astro dev`, `next dev`, `localhost:3000`, `127.0.0.1:5173`, `http://localhost`.

**Deepen into the full model** (runtime, process, localhost, ports, hot reload) when you see a confusion signal:
- Phrases: "how do I run this", "what's localhost", "what's the dev server", "what port", "Ctrl+C", "hot reload", "is this online?", "can my friend see this?", "the page is blank", "it says it's running but nothing happens".
- Errors: `EADDRINUSE`, `Address already in use`, `port already in use`, `cannot bind to port`, `Connection refused` to localhost.

**Defer / stay quiet** if the user is fluent ("bun dev, then localhost:4321"). Start the server and stop talking.

---

## The mental model

### Runtime — what runs your code

> "Code doesn't run on its own; something *runs* the code. For JavaScript on the server, that's **Node.js** (or **Bun**, or **Deno**). For Python `.py` files, the **Python interpreter**. For browser JavaScript, the **browser** itself. (See glossary: runtime.)
>
> Different runtimes have different versions, and code can require a specific minimum (Node 20+, Python 3.11+). The README usually says which."

If the user doesn't have the runtime installed, that's a 5-minute side-quest before anything else works:

- **Node:** install via [nvm](https://github.com/nvm-sh/nvm) (mac/linux) or [Volta](https://volta.sh/) — gives you per-project versions.
- **Python:** install via [pyenv](https://github.com/pyenv/pyenv) (mac/linux) or the official installer (windows).
- **Bun:** `curl -fsSL https://bun.sh/install | bash` — but route through `guard-rails` (warn-tier curl-pipe; trusted source, but explain).

### Process — the long-running thing

> "When you run `npm run dev`, your terminal starts a *process* — a running program. It doesn't finish; it sits there listening. Output streams as you go. Your terminal is occupied by it.
>
> To stop it, press **Ctrl+C** (Cmd+C on Mac is *not* the same — for stopping a terminal process, it's always Ctrl+C, even on Mac). The process exits, the terminal returns."

For beginners using a single terminal: opening a second terminal is the trick to keep the dev server running while you do other things. Show them.

### Localhost — only you can see it

> "**`localhost`** is a name that always means 'this very machine.' It's also written `127.0.0.1`. When the dev server says 'running at http://localhost:3000', that URL only works *on your machine* — nobody else on the internet, not even on your same Wi-Fi (usually), can reach it.
>
> If you want a friend to see it, you need either: deploy it (a separate skill), or use a tunnel like `ngrok` or `cloudflared tunnel` to expose your local server temporarily. Tunnels are fine for demos; not how you ship."

(See glossary: localhost.)

### Ports — channels on your machine

> "A **port** is a number (1–65535) that identifies which channel on your machine traffic is going to. `localhost:3000` means 'the server listening on port 3000 on this machine.' Each port can have only one process listening at a time.
>
> Conventional ports: web servers usually pick `3000` (Node), `5173` (Vite), `8000` or `8080`, `4321` (Astro), `5000` (older). The framework picks one; you usually don't choose."

When two processes try the same port, you get **`EADDRINUSE`** ("address already in use"). Common causes:

1. The previous run didn't fully stop. Kill it: `lsof -i :3000` to find the PID, `kill <pid>`.
2. Another app uses the port (rare on dev ports).
3. You're running two dev servers. Stop one.

### Hot reload — auto-restart on save

> "Most dev servers watch your files and reload automatically when you save. So you save `index.js`, the server notices, and your browser refreshes (sometimes you have to manually reload).
>
> Hot reload doesn't catch *everything*. Changes to config (`vite.config.js`, `package.json`, `.env`) often need a full restart — Ctrl+C, then run again."

---

## A typical first run

For a Node project:

```bash
# 1. Install deps (first time, or after pulling new ones)
npm install

# 2. Start the dev server
npm run dev
```

Server prints something like:

```
  ➜  Local:   http://localhost:5173/
  ➜  press h to show help
```

Open that URL in a browser. See the page.

When done:

```
^C
# Ctrl+C — server stops, terminal returns to you
```

---

## Composition

- **`coding-mentor`** — tone.
- **`installing-dependencies`** — `npm install` precedes the first run.
- **`using-env-vars`** — if the app reads `.env`, the `.env` must exist before the server can read it. Sequence matters.
- **`translate-the-error`** — `EADDRINUSE` and `Module not found` and runtime-version errors all flow through here naturally.
- **`safe-mode`** — starting a dev server is gated; you teach, the gate confirms.
- **`guard-rails`** — `curl … | bash` for runtime installers is warn-tier (or block depending on source).

---

## Tone

- "Long-running process listening on a port" is the line. Repeat it.
- The "blank page" failure mode is real — the server runs, but the browser shows nothing. Triage in this order: (1) is the URL right (port matches)? (2) is it loaded but the route is wrong? (3) JavaScript error in the browser console? (4) network error in browser dev tools?
- Don't start with "okay let's deploy this" if the user just wants it running locally. Localhost is a stopping point.

---

## Graduation signal

- User starts a dev server, navigates to localhost, stops with Ctrl+C — all unprompted.
- User troubleshoots `EADDRINUSE` themselves (or finds the right port).
- User notices the dev server isn't reflecting their change and tries a full restart.
- User says "running locally" or "in dev" with the right meaning.

When this happens, drop to one-line answers — "`bun dev`, then localhost:4321" — without re-explaining what localhost is.

---

## Examples

### Example: very first run

User: "ok how do I run this?"

> The README in this project says `npm run dev`. Quick teach before we run it:
>
> When you run that command, your terminal starts a long-running process — a *dev server* — that listens for browser requests. Output streams in your terminal. The server keeps running until you press Ctrl+C to stop it.
>
> The URL you'll open in your browser is `http://localhost:5173` (or whatever the server tells you). `localhost` means "this machine" — only your browser can reach this URL, no one else on the internet.
>
> Two prep steps before we run:
>
> 1. Install dependencies (one-time): `npm install`.
> 2. Start the server: `npm run dev`.
>
> [runs `npm install`, then `npm run dev`]
>
> Server's up:
>
> ```
> ➜ Local: http://localhost:5173/
> ```
>
> Open that URL in your browser. The terminal stays busy until you press Ctrl+C.
>
> **What we did:** Installed deps and started the dev server.
> **What it means:** Your machine is now serving the site at localhost:5173. Saving a file should reload the page automatically (hot reload).
> **What's next:** Want to make a tiny change to confirm hot reload works?

### Example: EADDRINUSE

User pastes `Error: listen EADDRINUSE: address already in use :::3000`

> Port 3000 is already taken — something else (probably a previous run that didn't fully stop) is listening there.
>
> Quick fix: find and kill it.
>
> ```
> lsof -i :3000          # shows the process ID using port 3000
> kill <pid>             # stops it
> ```
>
> Then re-run your dev server.
>
> Want me to do it? (I'll show what it found before killing.)

### Example: "can my friend see this URL?"

User: "I sent localhost:3000 to my friend and they got nothing"

> Right — `localhost` only resolves on *your* machine. When your friend types it, their `localhost` means *their* machine, where nothing is running.
>
> Two ways to share a running local server:
>
> 1. **Tunnel** (temporary, fastest): `ngrok http 3000` or `cloudflared tunnel --url http://localhost:3000` gives you a public URL that proxies to your machine. Good for demos. Stops when you stop the tunnel.
> 2. **Deploy** (permanent): push to a host (Vercel, Render, Cloudflare Pages, Fly). Different setup per host; some are free for small sites.
>
> If this is a one-off "show my friend right now," I'd use option 1.
>
> **What's next:** Want to set up a tunnel? Tell me ngrok or cloudflared.

### Example: graduated user

User: "EADDRINUSE on 4321"

> `lsof -i :4321; kill <pid>`. Want me to run it?

---

## What this skill is NOT

- **Not deployment.** Localhost is the focus; going public is a separate (out-of-scope-for-v1) skill.
- **Not a runtime installer.** If Node/Python/Bun isn't installed, walk through it once, then return to running.
- **Not always-on.** Quiet once the user can start a server, find a URL, stop with Ctrl+C, and recognize port collisions.
