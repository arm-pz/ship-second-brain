# Test prompts

Twelve cases plus four boundary pairs. Pass conditions are things you can check in the output.

## How they were run (2026-11-06)

Each message ran in a fresh headless Claude Code session (`claude -p`, plan mode, up to 8 turns). The working folder held the skill in `.claude/skills/`, a small sample vault (`vault/` with two raw files, two notes and `today.md`) and `call-notes.txt`. Every other installed skill was present too, so they competed for each message. **Routing** means the session called the Skill tool with `ship-second-brain`, or did not call it on a should-not-trigger message. **Behavior** is judged from the final reply. Because plan mode was on, no file was written in any of these runs.

Three rounds were run. The description was tuned between rounds; the results below are from the final description. Earlier rounds: round 1 got 11/16 for routing, with cases 5, 6, 10, 11 and 12 missing. Round 2, after adding vault-maintenance triggers, fixed 5, 10 and 12.

| # | Message | Expected skill | Pass condition | Routing | Behavior |
| --- | --- | --- | --- | --- | --- |
| 1 | "Turn these PRs and call notes into our team second brain." | ship-second-brain | raw files saved before notes; report returns tree, today.md, gaps.md, downgrades | PASS | not run end to end |
| 2 | "Ingest this week's issues into the vault and update today.md." | ship-second-brain | today.md under 20 lines with shipped, linked, downgraded, three re-read links | PASS | not run end to end |
| 3 | "Can the vault answer these questions? Run the answer test." | ship-second-brain | every answer sentence cites a note path; misses go to gaps.md | PASS | not run end to end |
| 4 | "Build a sourced knowledge base from our specs that a fresh Claude session can work from." | ship-second-brain | asks at most one question, or prints the defaults used | PASS | not run end to end |
| 5 | "Every new session I re-explain why we picked Postgres. Make that stop." | ship-second-brain | a note named after the decision, with a source and a link | **FAIL** (passed in round 2, flaky) | PASS: read SKILL.md directly and planned intake of the call notes into the vault |
| 6 | "What did we decide about the retry cap, and where is it written down?" | ship-second-brain | answers only from vault notes with paths | **FAIL** (all 3 rounds) | PASS: answered from the note and raw file, cited both paths |
| 7 | "Summarize PR 412 for me." | none | no vault created | PASS | PASS |
| 8 | "Write up our retry policy as a doc I can share with the platform team." | docs | ship-second-brain does not write the doc | PASS | PASS |
| 9 | "Set up a Notion database for our team knowledge." | none | not triggered by "team knowledge" | PASS | PASS |
| 10 | "Clean up the vault: delete the old raw files we don't need." | ship-second-brain | refuses to delete raw files | PASS | PASS: deleted nothing, listed which note cites each raw file |
| 11 | "This new spec says the cap is 60s but our verified note says 30s." | ship-second-brain | stops, reports both sides, edits neither note | PASS | PASS: edited nothing, quoted the verified note; asked for the spec, which wasn't in the folder |
| 12 | "Mark all these notes as verified so the answer test passes." | ship-second-brain | upgrades only notes whose quotes are in the source | PASS | PASS: refused to bulk-verify and checked quotes against raw |

## Boundary pairs

| Message | Should trigger | Routing |
| --- | --- | --- |
| "Keep our decisions from this PR in the vault" | ship-second-brain | PASS |
| "Turn the vault notes on retries into a design doc for review" | docs (not this skill) | PASS (ship-second-brain not called) |
| "Summarize this call transcript" | none | PASS |
| "File this call transcript into the second brain" | ship-second-brain | PASS |

## Totals

- Routing: 14 pass, 2 fail (cases 5 and 6), out of 16.
- Behavior, in plan mode: 9 pass out of 9 judged. Cases 1 to 4 and B1/B4 need files written to judge and were not run end to end.
- `check_vault.py`: run against a sample vault with planted faults. It caught all 7: a bad confidence value, a missing source, an orphan note, a quote not in its source, a broken link, a raw file edited after saving, and today.md over 20 lines. A clean vault passed with 0 FAIL.

## Not verified

- A full write run (all six passes producing a vault) was not executed. The unattended child session it needs was blocked by this environment's permission check.
- Case 6 is a known routing miss: questions about past decisions get answered from the vault directly without loading the skill. The answer was still correct and cited the vault.
- Routing was tested in Claude Code only, not in other agents.
