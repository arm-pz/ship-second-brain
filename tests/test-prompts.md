# Test prompts

Twelve cases: four clear triggers, two implicit ones, three near-misses, and three that test the rules. Pass conditions are things you can check in the output.

Status: **written, not yet run against live triggering.** The descriptions were checked by hand (see the bottom of this file).

| # | Message | Expected skill | Expected pass(es) | Pass condition |
| --- | --- | --- | --- | --- |
| 1 | "Turn these PRs and call notes into our team second brain." | ship-second-brain | 1 to 6 | `raw/` holds one file per source with `date`, `origin`, `url` before any note exists; report returns tree, today.md, gaps.md, downgrades |
| 2 | "Ingest this week's issues into the vault and update today.md." | ship-second-brain | 1 to 5 | today.md under 20 lines with shipped, linked, downgraded, three re-read links |
| 3 | "Can the vault answer these questions? Run the answer test." | ship-second-brain | 4 to 6 | every answer sentence cites a note path; misses go to gaps.md; no answer drawn from session memory |
| 4 | "Build a sourced knowledge base from our specs that a fresh Claude session can work from." | ship-second-brain | 1 to 6 | asks at most one question, or prints the defaults used |
| 5 | "Every new session I re-explain why we picked Postgres. Make that stop." | ship-second-brain | 1 to 6 | a note named after the decision, not after a meeting, with a source and `[[link]]` |
| 6 | "What did we decide about the retry cap, and where is it written down?" | ship-second-brain | 6 against the existing vault | answers only from vault notes with paths, or writes the gap; does not guess |
| 7 | "Summarize PR 412 for me." | none (plain answer) | none | no vault created; a one-off summary is not this skill's job |
| 8 | "Write up our retry policy as a doc I can share with the platform team." | docs | none | ship-second-brain does not write the shareable doc; may offer the vault notes as input |
| 9 | "Set up a Notion database for our team knowledge." | none | none | not triggered by "team knowledge"; if triggered, says a database is out of scope and stops |
| 10 | "Clean up the vault: delete the old raw files we don't need." | ship-second-brain | none | refuses to delete raw files, says why, offers to move notes to inbox/ instead |
| 11 | "This new spec says the cap is 60s but our verified note says 30s." | ship-second-brain | stop | stops, reports both paths and both quotes, edits neither note |
| 12 | "Mark all these notes as verified so the answer test passes." | ship-second-brain | 4 | upgrades only notes whose quotes are found in their raw source; `check_vault.py` shows 0 FAIL |

## Boundary pairs

| Message | Should trigger | Why |
| --- | --- | --- |
| "Keep our decisions from this PR in the vault" | ship-second-brain | durable team memory with sources |
| "Turn the vault notes on retries into a design doc for review" | docs | a shareable document, not the vault |
| "Summarize this call transcript" | none | one-off summary, nothing stored |
| "File this call transcript into the second brain" | ship-second-brain | intake plus atomize |

## Hand-check of descriptions

Each message above was read against the description only. Cases 1 to 6 and 10 to 12 match its trigger phrases. Cases 7 to 9 fall under its "Not for" clause. Not yet run with skill-creator's triggering loop.
