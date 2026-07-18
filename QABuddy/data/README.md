# QABuddy.AI Data Sources

Drop data into these folders and run `qabuddy ingest` to index everything into the vector store for RAG-powered QA analysis.

| # | Folder | Contents | Total Files | Chunking Strategy | Citation Format |
|---|---|---|---|---|---|
| 01 | `01_selenium_framework/` | Selenium Java POM + TestNG tests for VWO (Checkout, Experiments, Funnels, Heatmaps) | ~20 files | 1 method/class per chunk + line numbers | `ClassName.methodName` |
| 02 | `02_playwright_framework/` | Playwright API test framework: VWO clients, fixtures, data, spec files | ~40 files | per function/test block | `file.spec.js:testName` |
| 03 | `03_test_cases/` | `.csv` test cases (500 VWO-specific TCs: TC-2001 – TC-2500) | 2 CSV files | 1 row = 1 chunk | `TC-XXXX` |
| 04 | `04_jira_tickets/` | JSON ticket dumps (28 KAN- tickets + 3 samples) | 3 JSON files | 1 ticket = 1 chunk | `KAN-XXXX` |
| 05 | `05_company_docs/` | `.md` docs: Test Strategy, Accessibility Guide, Security Testing Guide, QA Standards | 4 files | heading-aware, ~512 tok, 15% overlap | `doc.md §section` |
| 06 | `06_figma_designs/` | `.md` design descriptions: Login, Dashboard, Experiment Creator, Checkout, Funnel Builder, Heatmap Viewer | 7 files | heading-aware (text descriptions of visual designs) | `screen_name.md` |
| 07 | `07_meeting_notes/` | `.md` notes: Sprint 43 Planning, Sprint 44 Retro, Bug Triage, Q3 Strategy, Incident Postmortem | 5 files | speaker-turn windows with `[MM:SS]` timestamps | `meeting.md [MM:SS]` |
| 08 | `08_lucid_charts/` | `.txt` chart exports: Checkout Flow, Experiment Flow, Funnel Analytics, Heatmap Architecture | 4 files | as docs | `chart.txt` |
| 09 | `09_prd_srs_brd_frd/` | `.pdf` PRD, `.md` SRS/BRD/FRD, `.docx` Test Plan | 5 files | heading-aware, page cited for PDF | `document.pdf p.N` or `document.md §ID` |
| 10 | `10_jenkins_logs/` | `.log` console logs: all-passing (#145), API failures (#143), regression+quarantine (#146), deploy smoke (#144), failed (#142) | 5 files | failure blocks + build summary | `build_NNN` |
