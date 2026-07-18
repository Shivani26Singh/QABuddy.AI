# Source 05: Company Docs

Drop `.pdf` and `.md` files here (guides, handbooks, policies).

## Current Contents

| File | Type | Description | Key Topics |
|---|---|---|---|
| `vwo_qa_standards.md` | Process Doc | QA process: test strategy, bug triage, release checklist, code review standards, environments | 4-tier testing gate, flaky test quarantine, sprint 43 focus areas |
| `vwo_test_strategy.md` | Strategy Doc | Full test strategy: pyramid, environments, risk-based matrix, automation standards, flaky policy, CI/CD pipeline, tooling | Test pyramid (E2E→API→Unit), 4 testing gates, risk matrix per module |
| `vwo_accessibility_guide.md` | Guide | WCAG 2.1 AA compliance: tools, checklist (Perceivable/Operable/Understandable/Robust), known defects, test scripts | KAN-1013, KAN-1018, KAN-1024, KAN-1027, axe-core CI integration |
| `vwo_security_testing_guide.md` | Guide | OWASP Top 10 for VWO: auth security, XSS prevention, API security, GDPR, incident response | KAN-1012 (XSS), KAN-1019 (API key), KAN-1022 (ZAP scan) |

**Product Name:** VWO – Digital Experience Optimization Platform  
**Product URL:** https://app.vwo.com/

## What to Add

- `.pdf` employee handbook, onboarding guide, compliance docs
- `.md` coding standards, code review checklist, deployment runbook
- `.md` incident response playbook, disaster recovery plan
- Test data management policy, environment access policy

## Ingestion Notes

- **Chunking:** Heading-aware, ~512 tokens with 15% overlap
- **Citation format:** `file.md §section-heading` or `file.pdf p.N`
- **Indexed:** All markdown headings and body text, PDF page numbers preserved
