# Source 07: Meeting Notes and Recordings

Drop text transcripts here: `.txt`, `.md`, `.vtt`. Speaker names and `[MM:SS]` timestamps are preserved and used in citations.

## Current Contents

| File | Type | Date | Attendees | Key Topics |
|---|---|---|---|---|
| `sprint43_planning.md` | Sprint Planning | 2026-07-15 | Shivani, Rahul, Priya, Dev Team | VWO-1002 fix, VWO-1010 new bug, sprint 43 goals, Playwright API tests (target 50), QABuddy.AI rollout |
| `sprint44_retrospective.md` | Retrospective | 2026-07-18 | Shivani, Rahul, Priya, Arun, Meera, Vikram | Wins (48 Playwright tests, WCAG score 84, Stripe canary deploy), Issues (KAN-1017 data loss, KAN-1002 still flaky, dark mode delayed), 4 P1 incidents |
| `bug_triage_2026_07_20.md` | Bug Triage | 2026-07-20 | Shivani, Rahul, Priya, Ananya (PM) | 8 bugs triaged: 1 promoted P1 (KAN-1024), 1 promoted P2 (KAN-1018), 3 new bugs (KAN-1030-1032), 1 closed as duplicate |
| `test_strategy_session_q3.md` | Strategy Session | 2026-07-14 | Shivani, Rahul, DevOps Lead, CTO | Coverage goals (80% unit, 50 API, 20 E2E), flaky policy (<5%), Playwright vs Cypress eval, SDET hiring, QABuddy.AI integration |
| `production_incident_postmortem_vwo_1025.md` | Postmortem | 2026-07-18 | Shivani, Rahul, DevOps, Meera | KAN-1025 memory leak: timeline, root cause (EventEmitter listener leak), fix (on→once), monitoring improvements |

## What to Add

- `.vtt` transcript files from Zoom/Teams recordings
- `.txt` raw meeting transcripts for longer sessions
- Standup notes (daily)
- Sprint review/demo notes
- Design review meeting notes
- Customer interview / feedback session notes

## Ingestion Notes

- **Chunking:** Speaker-turn windows (groups of 3-5 exchanges with `[MM:SS]` timestamps)
- **Citation format:** `meeting.md [MM:SS]` (e.g., `sprint43_planning.md [00:10]`)
- **Indexed:** All speaker names, timestamps, action items, and decisions
