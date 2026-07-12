# Dependencies

Dependency relationships between epics (and key issues) to sequence the backlog correctly.

## Epic-Level Dependencies

- **EPIC-1** (docs closure) → should precede/parallel **EPIC-3** schema work. The Directory-vs-Organization ambiguity (Issue #6) and billing data model (Issue #7) must be resolved before the database schema is locked, per `PRODUCT_REVIEW.md` §4–5.
- **EPIC-3** (Database) → blocks:
  - **EPIC-2** (Auth needs `users`/`organizations` tables — Issues #15–16 block Issues #8–14)
  - **EPIC-5** (Listings needs the `listings` table — Issue #17 blocks Issue #43)
  - **EPIC-9** (Leads needs the `leads` table — Issue #18 blocks Issue #64)
  - **EPIC-8** (Analytics needs the `analytics_events` table — Issue #19 blocks Issues #59–60)
- **EPIC-4** (CI/CD/Deployment) → should be stood up early; blocks safe delivery of all later epics since lint/type-check/test gates apply to every PR.
- **EPIC-2** (Auth) → blocks:
  - **EPIC-5** and **EPIC-6** (need authenticated organization context)
  - RLS enforcement in **EPIC-10** (Issue #14 informs Issue #69–70)
- **EPIC-5** (Listing CRUD) → blocks:
  - **EPIC-6** (import/export operates on listings)
  - **EPIC-7** (AI generation operates on listing descriptions)
  - **EPIC-8** (listing-level analytics)
  - **EPIC-9** (leads attach to listings; a listing page must exist to embed the capture form)
- **EPIC-7** (AI generation) depends on **EPIC-5**'s listing data model being stable. The version-history sub-task (Issue #55) depends on resolving the missing `content_versions` table gap (Issue #20 in **EPIC-3**, flagged by **EPIC-1**/Issue #6).
- **EPIC-9** (Leads) depends on **EPIC-5** (listing pages must exist to embed the capture form).
- **EPIC-10** (Security/Compliance) is cross-cutting: RLS and audit logging must be applied incrementally within **EPIC-2**/**EPIC-3**, and all remaining Security/Compliance issues must be finalized before the **M5** launch-readiness gate.
- **EPIC-11** (Testing) runs continuously alongside **EPIC-2** through **EPIC-9**; the final full-suite validation (Issue #78) gates the **M5** launch.
- **EPIC-12** (Phase 2 backlog) has no hard blocking dependency on MVP completion for planning purposes, but implementation should not start until **M5** is delivered.

## Issue-Level Dependency Highlights

| Issue | Depends On | Reason |
|---|---|---|
| #8–14 (Auth) | #15, #16 | Auth requires `users` and `organizations` tables to exist |
| #34–43 (Listing CRUD) | #8–14, #17 | Requires authenticated org context and `listings` table |
| #44–50 (Bulk import/export) | #34–43 | Operates on existing listing CRUD/API |
| #51–55 (AI generation) | #34–43 | AI operates on listing description field |
| #55 (Version history) | #20 | Requires `content_versions` table (or equivalent) to be resolved |
| #59–60 (Analytics) | #19, #34–43 | Requires `analytics_events` table and listing data to report on |
| #63–67 (Lead capture) | #18, #34–43 | Requires `leads` table and an existing listing to embed the form on |
| #14, #69–72 (Security/RLS) | #15–22 | RLS policies apply to tables created in EPIC-3 |
| #75–78 (Testing) | ongoing | Test suites are added incrementally as each feature epic delivers, not as a single final phase |

## Dependency Diagram (textual)

```
EPIC-1 (Docs) ─┬─> EPIC-3 (Database/RLS) ─┬─> EPIC-2 (Auth) ─┬─> EPIC-5 (Listings) ─┬─> EPIC-6 (Import/Export)
               │                          │                  │                      ├─> EPIC-7 (AI Generation)
               │                          │                  │                      ├─> EPIC-8 (Analytics)
               │                          │                  │                      └─> EPIC-9 (Leads)
               │                          └─────────────────────────────────────────────> EPIC-10 (Security) [continuous]
               │
               └─> EPIC-4 (CI/CD) [runs in parallel, gates all PRs from the start]

EPIC-11 (Testing) runs continuously alongside EPIC-2 → EPIC-9

All MVP epics (EPIC-2 → EPIC-11) ──> EPIC-12 (Phase 2 Backlog) [starts only after M5 launch]
```
