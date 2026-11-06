# ship-second-brain

An agent skill that turns your team's issues, PRs, specs and call notes into a second brain you can ship from. The second brain is one folder of markdown, written by Claude, read by people, and never trusted until it has been checked against its source. Raw sources are saved first. Each idea gets its own note with a source, an owner and a confidence (`seen`, `verified`, `opinion`). Every note links to the decision it supports or contradicts. Verified quotes are re-checked word for word. A fresh session must then answer your questions from the vault alone. Anything it cannot answer goes to `gaps.md`.

Install: `npx skills add arm-pz/ship-second-brain --skill ship-second-brain`

Ask it things like:

- "Turn these PRs and call notes into our team second brain."
- "Ingest this week's issues into the vault and update today.md."
- "Can the vault answer these questions? Run the answer test."
- "What did we decide about the retry cap, and where is it written down?"

## What you get

| Pass | Output |
|---|---|
| 1. Intake | `raw/` files, copied as-is with date, origin and exact URL |
| 2. Atomize | One note per idea, named after the idea, with date, source, owner and confidence |
| 3. Link | Every note linked to a decision it supports, contradicts or answers; orphan notes go to `inbox/` |
| 4. Verify | Quotes re-checked against the source; mismatches downgraded and listed |
| 5. Daily note | `today.md`: what shipped, what got linked, what got downgraded, three notes to re-read, under 20 lines |
| 6. Answer test | A fresh session answers your questions from the vault alone and cites a note for every sentence; misses go to `gaps.md` |

`scripts/check_vault.py` handles the mechanical checks. It looks for missing front matter, missing sources, orphan notes, broken links, verified quotes that are not in their source, raw files edited or deleted after saving, and a `today.md` that is 20 lines or longer. It needs Python 3 and no other dependencies.

## Inputs

`VAULT` (default `./vault`), `SOURCES`, `QUESTIONS`, `SCOPE` (default: only the vault), `LIMIT`. If a missing field would change the result, the skill asks one question. Otherwise it starts and tells you which defaults it used.

## Not for

A one-off summary of a single document. A shareable doc or wiki page built from the vault: use `docs`. A database, Notion space or plugin: a folder of markdown outlives all of them.

## Test results

Version 0.1.0: 16 trigger cases run in fresh Claude Code sessions. Routing: 14 pass, 2 fail. Behavior: 9 pass in plan mode. The full six-pass write run has not been run yet. Details and the two known misses are in [`tests/test-prompts.md`](tests/test-prompts.md).

## Install

- Zip `skills/ship-second-brain/` (the folder itself is the top-level entry) and upload it in your Claude skills settings, or
- Copy `skills/ship-second-brain/` into your skills directory, or
- Install from GitHub:

```
npx skills add arm-pz/ship-second-brain --skill ship-second-brain
```

Skills directories: Claude Code reads `~/.claude/skills/` or `.claude/skills/`; other agents read `.agents/skills/`, `.cursor/skills/` or `~/.qoder/skills/`. With Claude Code plugins, `.claude-plugin/plugin.json` makes the repo installable as a plugin.

To confirm the install, start a new session and check that `ship-second-brain` appears in the agent's skill list. In Claude Code, type `/` and look for it.

## Structure

```
ship-second-brain/
├── README.md
├── CHANGELOG.md
├── .claude-plugin/
│   └── plugin.json
├── skills/
│   └── ship-second-brain/
│       ├── SKILL.md                 # rules, inputs, the six passes, stop conditions, report
│       ├── references/
│       │   └── note-format.md       # raw file, note, today.md and gaps.md formats
│       └── scripts/
│           └── check_vault.py       # deterministic vault checks, --snapshot for raw/
└── tests/
    └── test-prompts.md
```

## Credit

Adapted from the "Second Brain for Shipping" six-pass prompt, built with the skillsmith.tech workflow "Build a skill and make it installable".
