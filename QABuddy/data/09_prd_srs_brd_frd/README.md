# Source 09: PRD / SRS / BRD / FRD

Drop requirement documents here as `.pdf` or `.md`. Chunked heading-aware with page numbers kept for PDFs.

## Current Contents

| File | Type | Description | Key IDs |
|---|---|---|---|
| `Product Requirements Document_VWO.pdf` | PDF | Original VWO PRD from Product team | REQ-VWO-001 through REQ-VWO-150 |
| `vwo_srs.md` | Markdown | Software Requirements Specification: 107 requirements across 8 modules + non-functional requirements | SRS-VWO-001 through SRS-VWO-107 |
| `vwo_brd.md` | Markdown | Business Requirements Document: objectives, scope, stakeholders, ROI ($385K ARR vs $180K investment), risks, success metrics | OBJ-01 through OBJ-06 |
| `vwo_frd.md` | Markdown | Functional Requirements Document: detailed per-module specs with acceptance criteria, SRS traceability, and test case links | FRD-AUTH-001 through FRD-INTEGRATION-006 |
| `Output_TestPlan.docx` | DOCX | Generated test plan document | — |

## Requirement Hierarchy
```
PRD (REQ-VWO-XXX)
  → BRD (OBJ-XX) — business objectives
  → SRS (SRS-VWO-XXX) — system requirements
    → FRD (FRD-MODULE-XXX) — functional requirements with acceptance criteria
      → Test Cases (TC-XXXX) — test implementation
        → JIRA (KAN-XXXX) — bug tracking
```

## What to Add

- `.pdf` updated PRD versions as they're released
- `.md` RTM (Requirements Traceability Matrix) mapping PRD→SRS→FRD→TC→JIRA
- `.md` API specification documents (OpenAPI/Swagger export)
- `.md` architecture decision records (ADRs)

## Ingestion Notes

- **Chunking:** Heading-aware, ~512 tokens with 15% overlap
- **Citation format:** `document.pdf p.N` or `document.md §section-heading` (e.g., `vwo_srs.md §3.4 Experiments Module`)
- **Indexed:** All requirements with IDs, acceptance criteria, dependencies, and cross-references
