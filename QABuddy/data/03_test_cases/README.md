# Source 03: Test Cases

Drop `.csv` / `.xlsx` files here. Expected columns (extra columns are fine, all text is indexed):

`Issue Type, Issue Key, Summary, Description, Priority, Component, Labels, Test Type, Preconditions, Steps, Expected Result, Browser, Device, Status`

## Current Contents

| File | Rows | TC Range | Modules Covered |
|---|---|---|---|
| `vwo_test_cases_200.csv` | 200 | TC-1001 – TC-1200 | Authentication, Checkout, Funnels, Experiments, Reporting, Heatmaps, Settings (template-generated steps) |
| `vwo_test_cases_500.csv` | 500 | **TC-2001 – TC-2500** | Authentication (55), Checkout (55), Funnels (55), Experiments (55), Reporting (50), Heatmaps (50), Integrations (45), Settings (45), User Management (40), Onboarding (25), Cross-Module E2E (25) |

**Key difference:** TC-2001+ series has **domain-specific VWO step vocabulary** (e.g., "Navigate to https://app.vwo.com/login", "Enter Stripe test card 4242-4242-4242-4242", "Set statistical significance threshold to p < 0.05") unlike the generic template steps in TC-1001–1200.

## Priority Distribution (TC-2001+)
- P1 (critical): ~10%
- P2 (high): ~30%
- P3 (medium): ~40%
- P4 (low): ~20%

## Status Distribution
- Active: 70%
- Draft: 20%
- Deprecated: 10%

## What to Add

- `.csv` files with additional test cases for edge cases (0% traffic, 1000+ variant MVT, zero-visitor funnels)
- `.xlsx` files with multiple sheets (one per module)
- Export from TestRail/Zephyr/qTest with custom columns
- Test cases with traceability column linking to JIRA KAN-IDs and PRD REQ-IDs

## Ingestion Notes

- **Chunking:** 1 row = 1 chunk, serialized as `Field: value` lines
- **Citation format:** `TC-XXXX` (e.g., `TC-2078`)
- **Indexed:** All 14 columns plus any additional columns
- **Steps format:** Numbered steps separated by ". " (e.g., "1. Navigate to login. 2. Enter email.")
