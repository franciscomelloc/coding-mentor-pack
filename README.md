# Coding Mentor Pack

A pack of [Claude Code](https://claude.ai/code) skills that turn Claude into a **patient teacher** for absolute beginners — people learning to code while building. Capability stays full; what changes is *how* Claude communicates, paces, and proposes work, so a non-programmer can keep up and **learn enough to outgrow the pack**.

## Philosophy

Built on three ideas:

1. **Recommend, never ask open-ended.** Beginners freeze at "what kind of database do you want?" Replace with "I suggest SQLite because it's one file and needs no setup. Alternatives: Postgres, no DB yet. SQLite — yes?"
2. **One new concept per turn.** Pacing beats impressiveness. Every turn ends with three sections: "What we did / What it means / What's next." The "next" is always a concrete yes/no proposal.
3. **Auto-obsolescence.** Every skill has a graduation signal — observable behavior showing the user has internalized the concept. When the signal appears, the skill softens. The teaching loop fades; the safety net (block-tier guard-rails) does not.

The pack does **not** reduce Claude Code's capability. Everything Claude Code can normally do, it can still do — these skills change voice, pacing, and confirmation, not power.

## The 12 skills

| # | Name | Mode | What it does |
|---|---|---|---|
| 1 | `coding-mentor` | always-on | Sets the patient-teacher tone every other skill speaks in. |
| 2 | `guard-rails` | always-on | Catches anti-patterns before they happen — force-pushes, secret commits, `rm -rf`, pasted keys. Two tiers (block / warn). |
| 3 | `explain-this-project` | reactive | Tours an unfamiliar codebase. 5–8 key files, one-line role each, plus a health check. |
| 4 | `safe-mode` | on-demand | Gates every write/delete/install behind explicit "yes." Reads stay free. |
| 5 | `translate-the-error` | reactive | Decodes errors into plain English, names the category, proposes (but doesn't apply) a fix. |
| 6 | `what-changed` | reactive | Recap hook after dense turns — files touched, why, what's now possible, what to watch. |
| 7 | `next-step` | reactive | Surveys state, lists three concrete options, recommends one. For when the user is stuck. |
| 8 | `using-git` | first-encounter | Teaches git's mental model (snapshots, working dir / staging / repo, branches as parallel timelines). |
| 9 | `using-github` | first-encounter | Splits git from GitHub. Auth (HTTPS+PAT recommended), remotes, fork vs clone, PR lifecycle. |
| 10 | `using-env-vars` | first-encounter | `.env` lifecycle, secrets out of git, per-platform loading, dev vs prod. |
| 11 | `installing-dependencies` | first-encounter | Package managers, manifest vs lockfile, semver, project-local vs global, why `node_modules/` is gitignored. |
| 12 | `running-locally` | first-encounter | Runtime, dev server as a long-running process, localhost, ports, hot reload, Ctrl+C. |

Plus two shared assets:

- **`glossary.md`** — canonical plain-English definitions of every term any skill uses. One source of truth.
- **`anti-patterns.md`** — the catalog `guard-rails` watches for. Each entry tagged `block` (stop, ask) or `warn` (advise, proceed).

## Install

# Install instructions go here

(For now, two paths. Both work today; the marketplace path will be fleshed out as Claude Code's plugin tooling stabilizes.)

**Path 1 — copy into your Claude Code skills directory:**

```bash
git clone https://github.com/franciscomelloc/coding-mentor-pack.git
cp -r coding-mentor-pack/skills/* ~/.claude/skills/
cp coding-mentor-pack/glossary.md ~/.claude/skills/coding-mentor-pack-glossary.md
cp coding-mentor-pack/anti-patterns.md ~/.claude/skills/coding-mentor-pack-anti-patterns.md
```

Restart Claude Code. The skills auto-load via Claude Code's skill discovery.

**Path 2 — plugin install (if available in your Claude Code version):**

```bash
claude plugin install franciscomelloc/coding-mentor-pack
```

## Examples

The `examples/` folder contains before/after conversation transcripts showing what changes when the pack is active:

- `beginner-clones-repo.md` — first-time clone of a repo the user doesn't understand
- `beginner-hits-error.md` — an error the user pastes in panic
- `beginner-first-commit.md` — first git commit and push to GitHub

Each shows the same beginner scenario with vanilla Claude Code vs Claude Code + this pack, so you can see the difference in voice, pacing, and structure.

## Contributing

Friction reports welcome. If a skill misfires, an anti-pattern is missing, or the graduation signal is wrong, open an issue with:

- The conversation snippet (sanitized — no real keys, please).
- What the skill did vs what would have helped.
- A proposed fix to the SKILL.md (if you have one).

Pull requests against any of the 12 SKILL.md files, the glossary, or the anti-pattern catalog are welcome. Keep the voice — direct, second-person, no fluff.

## Language

The pack is written in **English**. Claude Code translates per-conversation if the user works in another language; the skills themselves stay English to keep one source of truth.

## License

MIT — see `LICENSE`.
