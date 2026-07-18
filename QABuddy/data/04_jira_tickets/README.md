# Source 04: JIRA Tickets

JSON ticket dumps. Format:

```json
{
  "tickets": [
    {
      "key": "KAN-XXXX",
      "type": "Bug|Story|Task",
      "summary": "...",
      "description": "...",
      "status": "Open|In Progress|Done",
      "priority": "P1|P2|P3",
      "labels": ["..."],
      "components": ["..."],
      "created": "YYYY-MM-DD",
      "updated": "YYYY-MM-DD",
      "url": "https://learningcoder.atlassian.net/browse/KAN-XXXX",
      "comments": [{ "author": "...", "created": "...", "body": "..." }]
    }
  ]
}
```

## Current Contents

| File | Tickets | Key Range | Categories |
|---|---|---|---|
| `vwo_jira_tickets.json` | 10 | KAN-1001 – KAN-1010 | Bugs (6), Stories (2), Tasks (2) — auth, checkout, experiments, funnels, reporting |
| `vwo_jira_tickets_extended.json` | 18 | **KAN-1011 – KAN-1028** | Production Incidents (4), Security (3), Accessibility (4), Performance (3), Feature Requests (3), Tasks (2) |
| `sample_tickets.json` | 3 | KAN-2001 – KAN-2003 | Sample format for reference |

## KAN-1011+ Categories
- **Production Incidents:** KAN-1011 (Stripe 500s), KAN-1017 (recording data loss), KAN-1020 (SSO redirect loop), KAN-1025 (memory leak OOM)
- **Security:** KAN-1012 (XSS), KAN-1019 (API key leak), KAN-1022 (OWASP ZAP pen test)
- **Accessibility:** KAN-1013 (keyboard nav), KAN-1018 (color contrast), KAN-1024 (screen reader labels), KAN-1027 (full WCAG audit)
- **Performance:** KAN-1014 (heatmap rendering), KAN-1021 (funnel dashboard load), KAN-1026 (PDF timeout)
- **Features:** KAN-1015 (MVT), KAN-1016 (branded PDF), KAN-1023 (dark mode), KAN-1028 (Slack results)

## What to Add

- More JSON dumps from JIRA MCP or `scripts/jira_fetch.py` with REST + JQL
- Tickets with linked issues (blocks/is blocked by/is duplicate of)
- Tickets with attachments (screenshots uploaded via `scripts/jira_attach.py`)
- Sprint-specific ticket dumps (e.g., `sprint_45_tickets.json`)
- Real KAN- project tickets from https://learningcoder.atlassian.net

## Ingestion Notes

- **Chunking:** 1 ticket = 1 chunk (long comment threads spill into linked chunks)
- **Citation format:** `KAN-XXXX` (e.g., `KAN-1002`)
- **Indexed:** All fields including description, comments, labels, and components
- **URL format:** `https://learningcoder.atlassian.net/browse/KAN-XXXX`
