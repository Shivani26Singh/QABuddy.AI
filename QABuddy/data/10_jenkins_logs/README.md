# Source 10: Jenkins Logs and Results

Drop build console logs (`.log`, `.txt`) and JUnit result XMLs here.
Only failure blocks (stack trace + context) and a per-build summary are indexed; passing-spam is skipped.

## Current Contents

| File | Build # | Scenario | Key Content |
|---|---|---|---|
| `build_142_console.log` | #142 | **Failed** (original) | API sanity 12/12, E2E 26/28, 2 failures: Checkout coupon flaky (KAN-1002), Experiment goal tracking mismatch (KAN-1003) |
| `build_143_console.log` | #143 | **API failures** | Stripe sandbox 502 → 3/18 API tests fail, E2E skipped (gate failed), external dependency outage |
| `build_144_console.log` | #144 | **Deploy smoke** | Post-deploy to staging: 8/8 smoke tests pass, deployment gate approved, canary healthy, all metrics green |
| `build_145_console.log` | #145 | **All-passing** | 400 tests (Lint, Unit 342/342, API Sanity 18/18, VWO API 26/26, E2E 14/14, A11y scan 94/100), 8m 14s |
| `build_146_console.log` | #146 | **Unstable (quarantine)** | 25/28 pass, 1 flaky (KAN-1002), 2 quarantine skip (KAN-1003, KAN-1011), quarantine count: 3 (stable) |

## Pipeline Stages (Typical)
```
Checkout → Build → Lint & Unit Tests → API Sanity → VWO API Tests → E2E Regression → Accessibility Scan → Results
```

## What to Add

- `.log` files for: failed deploy, performance test run, security scan results
- `.xml` JUnit result files (test suites with pass/fail/skip counts)
- `.log` from different nodes (jenkins-agent-01, -02, -03, -04)
- `.log` for scheduled nightly runs vs manual trigger runs vs deploy-triggered runs

## Ingestion Notes

- **Chunking:** Only failure blocks (stack traces + 10 lines of context) and per-build summary (test counts, duration, status)
- **Citation format:** `build_NNN` (e.g., `build_142`)
- **Indexed:** Build number, test results (pass/fail/skip counts), stack traces, failure context, build status
- **Skipped:** Passing test logs (spam reduction), timestamps, agent info
