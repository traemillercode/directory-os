# Recommended Implementation Order

This is the recommended build sequence across milestones. `EPIC-10` (Security) and `EPIC-11` (Testing) run continuously in parallel with `M1`–`M5` rather than as a discrete phase at the end.

1. **M0**: `EPIC-1` — close doc gaps (`PERSONAS.md`, `USER_STORIES.md`, `PRICING_STRATEGY.md`, `PRODUCT_METRICS.md`, `AI_GOVERNANCE.md`; resolve Directory-vs-Organization and billing model questions).

2. **M1**: `EPIC-4` (CI/CD scaffolding) → `EPIC-3` (database/RLS foundation) → `EPIC-2` (auth/multi-tenancy).
   - Stand up CI/CD first so every subsequent PR is gated by lint/type-check/tests.
   - Build the database schema and RLS policies next, since almost everything downstream depends on it.
   - Implement authentication and multi-tenancy last within this milestone, once the underlying tables exist.

3. **M2**: `EPIC-5` (listing dashboard & CRUD) → `EPIC-6` (bulk import/export).
   - Listing CRUD must exist before import/export can operate on it.

4. **M3**: `EPIC-7` (AI description generation only; SEO analysis and content-scoring remain deferred to `EPIC-12`).

5. **M4**: `EPIC-8` (minimal analytics dashboard; search analytics and downloadable reports remain deferred to `EPIC-12`).

6. **M5**: `EPIC-9` (lead capture & management) → `EPIC-10` finalization (security/compliance sign-off) → `EPIC-11` full regression pass → **MVP launch**.

7. **M6 (post-launch)**: `EPIC-12` — Phase 2 backlog grooming and kickoff (social media scheduling, email marketing automation, advanced Maps integration, lead qualification/scoring, advanced reporting, plus all MVP-deferred items and top-voted ideas).

## Continuous Cross-Cutting Work

- `EPIC-10` (Security, Compliance & Observability): RLS and audit logging land alongside `EPIC-2`/`EPIC-3` (M1); rate limiting/CSRF/input validation land alongside each new API surface (M2–M5); GDPR/CCPA export-deletion workflows and ToS/Privacy Policy publication must be complete before the M5 launch gate.
- `EPIC-11` (Testing & QA Automation): Unit and integration test suites are added incrementally as each feature epic (`EPIC-2`–`EPIC-9`) delivers its functionality, not written retroactively. Lighthouse CI and full performance-target validation (Issue #78) run as the final gate before M5 launch.

## Launch Gate (End of M5)

Before declaring the MVP launched, confirm:
- ✅ All `EPIC-1`–`EPIC-9` issues are closed
- ✅ All `EPIC-10` security/compliance issues are closed, including GDPR/CCPA export & deletion
- ✅ `EPIC-11` full regression (unit + integration + Lighthouse/perf) passes against the targets in `docs/Product/02_MVP.md` Success Criteria
- ✅ `EPIC-12` is populated and groomed, ready for Phase 2 kickoff
