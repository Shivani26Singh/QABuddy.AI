# Source 06: Figma Designs

Drop Figma exports here. Phase 1: `.md` design descriptions. Phase 2: actual Figma REST API exports + vision-model description pass.

## Current Contents

| File | Type | Description | Key Screens |
|---|---|---|---|
| `login_screen.md` | Design Desc | Login page with email/password, Google/Microsoft SSO, password reset, MFA challenge | All states: default, validation error, forgot password, MFA, account locked |
| `dashboard_overview.md` | Design Desc | Main dashboard with widget grid (Active Experiments, Total Visitors, Conversion Rate, Funnel Performance, Experiment Results) | Loading skeleton, empty state, error state, drag-and-drop reorder |
| `experiment_creator.md` | Design Desc | 4-step wizard: Configuration → Variants → Goals → Targeting & Schedule | A/B Test + MVT, XSS sanitized input, radio button ARIA labels (KAN-1024 fixed) |
| `checkout_payment.md` | Design Desc | 3-step checkout: Shipping → Payment (CC/PayPal/UPI) → Order Review with coupon | Card validation, coupon states (valid/expired/invalid), special chars (KAN-1010) |
| `funnel_builder.md` | Design Desc | Funnel visualization with connected nodes, step properties panel, goal icons, drag-and-drop reorder | Empty state, no data state, export dialog |
| `heatmap_viewer.md` | Design Desc | Heatmap overlay (click map, scroll map), session recording playback with controls, device preview | Click map + scroll map + session recordings tabs, playback at 4 speeds (KAN-1004) |

## What to Add

- Figma REST API exports (design files with layers, components, styles)
- Screenshot exports (PNG/JPEG) of key screens for vision-model analysis
- Wireframe exports for new features (MVT creator, dark mode)
- Design system token exports (colors, typography, spacing)
- Component library documentation

## Ingestion Notes

- **Phase 1 (current):** Text descriptions ingested as markdown documents (heading-aware chunking)
- **Phase 2 (planned):** Figma REST API → export design files + PNG → vision model description → RAG index
- **Citation format:** `screen_name.md` (e.g., `login_screen.md`)
