# Example: beginner clones a repo

A user with little programming experience just cloned a Markdown-to-HTML CLI from GitHub. They open Claude Code in the project folder and ask what it is. Below: the same question, with and without the Coding Mentor Pack active.

---

## Without the pack

> **User:** I just cloned this. What is it?
>
> **Claude:** This is a Node.js CLI tool that converts Markdown to HTML using the `marked` library. The entry point is `src/index.ts` which parses CLI arguments and calls `convert()` from `src/convert.ts`. The build is configured via TypeScript with `tsconfig.json` targeting Node 20. There's no test suite. Run `npm install` then `npm run build` and `npm start -- <input> <output>`. Want me to walk through the code structure?

That's not wrong. It's also dense, jargon-heavy, and ends with an open-ended question that paralyzes a beginner. The user types "yeah I guess" and the conversation drifts.

---

## With the pack

(`explain-this-project` fires; `coding-mentor` sets the tone.)

> **User:** I just cloned this. What is it?
>
> **Claude:** Let me look around.
>
> *(reads README, package.json, src/index.ts)*
>
> **What this is:** A small command-line tool that converts Markdown files to HTML. You give it a folder of `.md` files, you get a folder of `.html` files.
>
> **Stack:** Node.js + TypeScript. Uses `marked` for the actual Markdown parsing.
>
> **How to run it:**
> ```
> npm install
> npm run build
> npm start -- ./input ./output
> ```
>
> **Key files:**
> - `src/index.ts` — entry point; reads CLI args, calls the converter.
> - `src/convert.ts` — the actual `.md → .html` function. This is the heart.
> - `src/io.ts` — file reading and writing.
> - `package.json` — declares dependencies and the `start`/`build` scripts.
> - `tsconfig.json` — TypeScript config; targets Node 20+.
> - `README.md` — usage examples and a TODO list at the bottom.
>
> **Health check:**
> - ✅ README, lockfile (`package-lock.json`), TypeScript config all present.
> - ⚠️ No tests. The TODO list mentions "add tests" — they didn't.
> - ⚠️ `.env.example` missing but `src/index.ts` reads `process.env.DEBUG` — minor.
>
> **What we did:** Toured the project — it's a Markdown→HTML CLI in Node + TypeScript.
>
> **What it means:** You can now point to specific files. The actual conversion lives in `src/convert.ts`.
>
> **What's next:** I suggest we just *run it* on a tiny input first, so you see what it does. Alternatives: read `src/convert.ts` together, or skip ahead to adding a test. Want me to make a sample input and run it?
>
> ---
>
> **User:** yes
>
> **Claude:** Cool. Two prep steps before the run:
>
> 1. Install the dependencies — that's `npm install`. Quick teach: when you clone a Node project, the `node_modules/` folder isn't included (it's huge), so we install fresh. *(See glossary: dependency, manifest.)*
> 2. Build the TypeScript — that's `npm run build`. Compiles the `.ts` files in `src/` to `.js` in `dist/`.
>
> Then I'll create a sample `input/hello.md` and run.
>
> *(runs `npm install`)*
> *(runs `npm run build`)*
> *(creates `input/hello.md` with three lines of Markdown)*
> *(runs `npm start -- ./input ./output`)*
>
> Worked. `output/hello.html` now has:
>
> ```html
> <h1>Hello</h1>
> <p>This is a test of the converter.</p>
> ```
>
> **What we did:** Installed deps, built the project, ran it on a sample input.
>
> **What it means:** You've now seen the whole loop end to end — the program reads `.md`, writes `.html`. The conversion lived inside `src/convert.ts`.
>
> **What's next:** I suggest we open `src/convert.ts` and read it together — it's short (about 30 lines) and now you'll see exactly what the program did. Want to?

---

## What's different

- **Front-loaded structure.** Every section is named so the user can scan.
- **Plain English first, jargon defined.** "Entry point" appears, but glossed in context.
- **Health check.** Beginner doesn't know what they don't know — the ⚠️ section flags it.
- **Concrete next step.** "Run it on a sample" is finishable in 2 minutes; "walk through the code structure" is open-ended.
- **End-of-turn footer.** Every turn ends with three lines. The user always knows where they are and what's next.
- **One concept per turn.** Second turn introduces dependencies and build separately, with definitions, instead of dumping the build pipeline at once.
