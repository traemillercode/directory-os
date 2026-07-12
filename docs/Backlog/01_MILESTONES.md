# Milestones

Milestones map to the 12-week MVP schedule defined in `docs/Product/02_MVP.md`, adjusted per the reclassification in `docs/Product/PRODUCT_REVIEW.md` Section 3.

| Milestone | Weeks | Theme | Source |
|---|---|---|---|
| **M0 – Foundations & Docs Debt** | Pre-Week 1 | Close documentation gaps flagged in `PRODUCT_REVIEW.md` before schema/architecture lock | `PRODUCT_REVIEW.md` §9 |
| **M1 – Core Infrastructure** | 1–3 | Auth, multi-tenancy, DB foundation, deployment/CI | `02_MVP.md` Phase 1A |
| **M2 – Listing Management** | 4–6 | Dashboard, CRUD, bulk import/export | `02_MVP.md` Phase 1B |
| **M3 – AI Integration** | 7–9 | AI description generation only (SEO/content-scoring deferred per review) | `02_MVP.md` Phase 1C; `PRODUCT_REVIEW.md` §3 |
| **M4 – Analytics & Dashboard** | 10–11 | Minimal analytics dashboard (search analytics/reports deferred) | `02_MVP.md` Phase 1D; `PRODUCT_REVIEW.md` §3 |
| **M5 – Lead Capture & Launch Readiness** | 11–12 | Lead capture/management, security/compliance sign-off, launch | `02_MVP.md` Phase 1E |
| **M6 – Phase 2 Backlog (post-MVP)** | 13–24 | Deferred MVP items + roadmap Phase 2 features | `03_ROADMAP.md` Phase 2; `PRODUCT_REVIEW.md` §3 |

## Milestone Due-Date Guidance

Due dates should be set relative to the MVP kickoff date once confirmed:

- `M0`: kickoff date minus 1–2 weeks (pre-Week 1)
- `M1`: kickoff + 3 weeks
- `M2`: kickoff + 6 weeks
- `M3`: kickoff + 9 weeks
- `M4`: kickoff + 11 weeks
- `M5`: kickoff + 12 weeks (launch)
- `M6`: kickoff + 24 weeks (Phase 2 close), created as a rolling milestone with no fixed exit date

## Cross-cutting Milestones

`EPIC-10 (Security, Compliance & Observability)` and `EPIC-11 (Testing & QA Automation)` are **not** assigned to a single milestone. Their issues should be attached to whichever milestone (`M1`–`M5`) the corresponding work lands in, since they run continuously across the MVP build (see `06_IMPLEMENTATION_ORDER.md`).
