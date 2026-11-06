# Changelog

## 0.1.0 (2026-11-06)

- Initial release from the "Second Brain for Shipping" six-pass prompt: intake, atomize, link, verify, daily note, answer test. Includes the note-format reference and `check_vault.py`, which can snapshot raw files.
- Description tuned twice against live triggering in Claude Code to cover vault maintenance (verify, clean up, conflicts).
- Tests: 16 messages run in fresh sessions. Routing: 14 pass, 2 fail (cases 5 and 6). Behavior in plan mode: 9 pass. End-to-end write run: not run. See `tests/test-prompts.md`.
