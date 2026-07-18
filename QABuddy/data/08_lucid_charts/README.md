# Source 08: Lucid Charts

Export diagrams to text (Lucid: File → Export → Text/CSV outline) and drop them here as `.txt` / `.md`.

## Current Contents

| File | Type | Flow | Key Elements |
|---|---|---|---|
| `vwo_checkout_flow.txt` | Text Export | 4-step checkout: Shipping → Payment → Order Review → Confirmation | Integration points (Stripe, PayPal, Tax, Email, Analytics), known failure points (KAN-1001, KAN-1002, KAN-1010), test coverage status |
| `vwo_experiment_flow.txt` | Text Export | 9-step experiment lifecycle: Config → Variants → Goals → Targeting → Draft → Running → Results → Winner → Archive | Integration points (GA4, Slack, JIRA, Webhook, Segment), known failure points (KAN-1012 XSS, KAN-1030 traffic validation, KAN-1020 OAuth loop), test coverage |
| `vwo_funnel_analytics_flow.txt` | Text Export | Funnel data pipeline: Config → Snippet → Data Collection (Kafka/Flink/PostgreSQL) → Metric Calculation → Dashboard → Export | Data flow architecture (Kafka→Flink→PG→Redis→API→React), known failure points (KAN-1005 double-counting, KAN-1021 slow load, KAN-1026 timeout) |
| `vwo_heatmap_architecture.txt` | Text Export | 5-layer heatmap pipeline: Browser Capture → Ingestion → Aggregation → Rendering → Playback | Resilience architecture (KAN-1017 SQS fix), Web Worker processing (KAN-1014 fix), integration points (S3, Redis, SQS, Datadog, Puppeteer) |

## What to Add

- `.txt` exports for: Reporting Architecture (ETL pipeline), Integration Architecture (Slack/GA4/JIRA webhooks), User Management flow (RBAC model)
- `.md` versions with embedded Mermaid diagrams
- `.csv` outline exports for structured flow data

## Ingestion Notes

- **Chunking:** As document-level text (heading-aware for `.md`, full content for `.txt`)
- **Citation format:** `chart_name.txt` (e.g., `vwo_checkout_flow.txt`)
- **Indexed:** Flow steps, integration points, failure points, test coverage status
