# DirectoryOS Architecture Review

**Prepared by**: Chief Software Architect
**Purpose**: Evaluate whether `ARCHITECTURE.md` supports the product's stated capabilities and roadmap, using `docs/Product/*.md` (01_PROJECT.md, 02_MVP.md, 03_ROADMAP.md, 04_IDEAS.md, PRODUCT_REVIEW.md) as the source of truth.
**Scope**: `docs/Architecture/ARCHITECTURE.md` (cross-referenced with `DATABASE.md` and `API.md` where relevant)
**Non-goals**: This review does not rewrite `ARCHITECTURE.md`, remove any documented decision, or expand product scope. All findings are additive.

---

# 1. Evaluation Summary by Capability

| Capability | Supported today? | Evidence |
|---|---|---|
| **Multi-tenancy** | ⚠️ Partially | RLS + `organization_id` pattern is documented (`ARCHITECTURE.md` "Database Design Principles"), but the Organization-vs-Directory ambiguity flagged in `PRODUCT_REVIEW.md` Section 4 is never resolved architecturally. |
| **Scalability** | ✅ Mostly | Stateless serverless API, CDN, connection pooling, and explicit performance targets (p99 latency, 1,000+ concurrent users) are documented. Targets stop short of the product's "100,000+ listings" / "millions of listings" claims (`01_PROJECT.md`). |
| **Modular design** | ⚠️ Partially | Layered diagram (Client → Edge → Serverless → Data/External/AI) implies modularity, but no internal module boundaries, domain boundaries, or service-ownership model are defined within the "Vercel Serverless Functions" box. |
| **Future roadmap features** | ⚠️ Partially | Phase 2/3 section exists and lists Redis, search engine, GraphQL, multi-region, microservices — but several Phase 2/3 product commitments (lead marketplace, white-label, workflow automation triggers, SSO/compliance) have no architectural hook. |
| **AI services** | ⚠️ Partially | Claude/Gemini are named as integrations; no orchestration, prompt versioning, cost tracking, or fallback strategy — all flagged as gaps in `PRODUCT_REVIEW.md` Section 7 and still unaddressed here. |
| **Automation (n8n)** | ⚠️ Partially | n8n listed as an external service box; no event/webhook model, trigger catalog, or retry/idempotency strategy is defined to support Phase 3 "custom triggers" and "conditional logic." |
| **Billing** | ❌ Largely missing | Stripe is named as an integration for "Subscription management... Billing portal," but no billing/subscription/invoice data model, usage metering, or entitlement/feature-gating design exists (consistent with the gap already flagged in `PRODUCT_REVIEW.md` Section 5). |
| **Search** | ⚠️ Partially | "Full-text search capabilities" claimed under Supabase PostgreSQL; Phase 2 lists "Search engine (Meilisearch or Algolia)" as a future enhancement, but no current search architecture (indexing pipeline, ranking, search-term analytics per `API.md` `GET /analytics/search-terms`) is defined. |
| **Analytics** | ⚠️ Partially | GA4, Clarity, Vercel Analytics, and Sentry are named; internal `analytics_events` pipeline exists in `DATABASE.md`, but there's no described data pipeline/warehouse strategy for the Phase 3 "predictive analytics" and "custom analytics engine" commitments. |
| **Maps** | ⚠️ Partially | Mapbox is named as an integration and PostGIS is listed as a Supabase capability, but Phase 2 commitments (cluster visualization, heat maps, radius search) have no described geospatial indexing or query strategy beyond "PostGIS for geospatial queries." |
| **Integrations (general)** | ❌ Missing generic layer | Each third party (Stripe, Resend, Mapbox, n8n, Claude, Gemini) is documented as a bespoke box in the diagram; no generic integration/connection-management abstraction (API keys per org, OAuth token storage, webhook registry) is defined, despite this being flagged in `PRODUCT_REVIEW.md` Section 4 as a required abstraction. |
| **API-first design** | ✅ Mostly | Dedicated REST design, status codes, error format, versioning strategy, and security measures are documented in both `ARCHITECTURE.md` and `API.md`. GraphQL is only a "Phase 2" future item despite being shown in the architecture diagram today as if already available. |

---

# 2. Strengths

1. **Clear layered diagram** — Client → CDN/Edge → Serverless API → Data/External/AI is easy to reason about and matches the Next.js/Vercel/Supabase stack chosen in `01_PROJECT.md`.
2. **Multi-tenancy foundation is real, not aspirational** — RLS policies plus `organization_id` scoping is a concrete, testable mechanism (also demonstrated with an example policy in `DATABASE.md`), not just a diagram label.
3. **Security architecture is genuinely detailed** — JWT/refresh token lifetimes, rate limiting numbers, CSRF/XSS/SQLi mitigations, and a sequence diagram for the auth flow go beyond typical high-level docs.
4. **API design discipline** — consistent error format, explicit status code catalog, and an explicit no-URL-versioning strategy with deprecation-notice policy reduce ambiguity for API-first consumers.
5. **Operational maturity signals** — backup/RPO/RTO targets, environment separation (prod/staging/dev), and a defined CI/CD pipeline show the architecture was written with production operations in mind, not just feature delivery.
6. **Roadmap-aware structure** — the "Future Architecture Enhancements" section explicitly ties Phase 2/3 technical bets (Redis, search engine, GraphQL, multi-region, microservices) back to the same week-numbered phases used in `03_ROADMAP.md`, showing intentional alignment rather than a document written in isolation.
7. **Performance targets are quantified**, not vague ("<100ms p99 API," "<50ms p99 query," "<2s search indexing for 10K listings"), which makes the architecture testable against the product's stated success criteria.

---

# 3. Weaknesses

1. **Billing is a named integration, not an architecture.** Stripe appears once as a box with four bullet points. There is no subscription/invoice/entitlement schema, no webhook-handling design (subscription created/updated/canceled, payment failed), and no tie-in to the tiered feature limits described in `01_PROJECT.md` ("Starter/Professional/Enterprise"). This is the single largest gap relative to how central billing is to the business model.
2. **No generic integrations layer.** Every third-party service (Stripe, Resend, Mapbox, n8n, Claude, Gemini) is treated as a one-off box in the diagram and a handful of bullets. There is no described pattern for per-organization API keys/OAuth tokens, webhook registration/verification, retry/backoff, or credential rotation — needed by Phase 2/3 "CRM integration," "Salesforce, HubSpot," and "premium integrations" revenue stream (`01_PROJECT.md`).
3. **AI is under-architected relative to its stated importance.** `01_PROJECT.md` lists "AI-First Design" as Key Principle #1, yet `ARCHITECTURE.md` only lists Claude/Gemini as boxes with capability bullets ("Text generation," "Streaming responses"). Missing: prompt storage/versioning, model routing/fallback (what happens if Claude is down given the "<2s description generation" SLA), per-organization AI cost/usage metering (a direct COGS driver), and where human-approval gates (already required per `02_MVP.md`) are enforced architecturally.
4. **Search is described as a future enhancement, but is already a product commitment today.** `API.md` already defines `GET /analytics/search-terms`, and `ARCHITECTURE.md` itself sets a "Search indexing <2s for 10K listings" performance target — implying search exists now — while simultaneously listing "Search engine (Meilisearch or Algolia)" as Phase 2. The current-state search mechanism (Postgres full-text search) and its limits vs. the future dedicated engine are never reconciled.
5. **Modularity is only diagrammatic.** The single "API Layer" box in the Vercel Serverless Functions section bundles auth, business logic, validation, rate limiting, and transformation with no stated internal module/domain boundaries (e.g., listings, leads, billing, AI, analytics as separable modules). This makes the later "Microservices (if appropriate)" Phase 3 bullet unactionable — there's no described seam to split along.
6. **Organization-vs-Directory ambiguity is inherited, not resolved.** `PRODUCT_REVIEW.md` (Section 4) already flagged that it's unclear whether one Organization maps to one Directory or many. `ARCHITECTURE.md`'s multi-tenancy model only scopes by `organization_id`; if one org can operate multiple directories (relevant to the "SaaS Directory Builders" and white-label personas), the RLS/tenant model as documented today would need a second scoping dimension that isn't mentioned.
7. **Maps/geospatial capability is underspecified for committed Phase 2 features.** "PostGIS for geospatial queries" is the entire geospatial architecture statement, yet Phase 2 commits to cluster visualization, heat maps, and radius search — each with different indexing/query-performance implications (e.g., spatial indexes, clustering algorithms, tile generation) that aren't discussed.
8. **Analytics architecture stops at collection.** `analytics_events` (per `DATABASE.md`) plus GA4/Clarity cover event capture, but there's no described aggregation/rollup/warehouse strategy needed for Phase 2 "custom reports," "benchmarking," or Phase 3 "predictive analytics" / "custom analytics engine."
9. **GraphQL appears "already live" in the diagram** ("API Layer (REST endpoints, GraphQL, Webhooks)") but is simultaneously listed as a Phase 2 future item — an internal inconsistency that could mislead implementers about current API surface.
10. **Scalability targets undersell the product's own stated ambition.** `01_PROJECT.md` states "Scalability: Architecture supports millions of listings" and "100,000+ listings without performance degradation," while `ARCHITECTURE.md`'s only quantified concurrency target is "1,000+ concurrent users" with no listing-volume-specific target, index strategy for large tables, or partitioning/sharding discussion.

---

# 4. Risks

| Risk | Why it matters | Likelihood/Impact |
|---|---|---|
| **Billing built ad hoc, then reworked** | Without a documented billing/entitlement architecture, engineering will likely wire Stripe directly into feature checks, creating tight coupling that must be re-architected once tier limits, proration, and dunning logic (already required per `PRODUCT_REVIEW.md` Section 5) are formalized. | High likelihood / High impact |
| **AI cost overrun with no visibility** | AI is core to unit economics (Claude/Gemini are COGS), but no usage-tracking or cost-attribution design exists. A pricing model built on assumed AI costs (`01_PROJECT.md` tiers) could be invalidated by unmetered usage. | Medium likelihood / High impact |
| **Tenant-isolation gap if Organization ≠ Directory** | If a single org can run multiple directories (a stated target persona) but the schema/RLS model only isolates by `organization_id`, data could unintentionally leak across a customer's own directories, or (worse) cross-tenant if the second dimension is bolted on incorrectly later. | Medium likelihood / High impact |
| **Search SLA claims are not backed by an architecture** | The "<2s search indexing for 10K listings" target is stated without describing whether it's measured against Postgres full-text search (current) or a future dedicated engine — teams could ship against an untested assumption. | Medium likelihood / Medium impact |
| **Vendor lock-in / no fallback for AI providers** | Only two AI vendors are named with no failover design; an outage or pricing change at either vendor (a risk already named in `03_ROADMAP.md` "Technical Dependencies") directly breaks a customer-facing SLA. | Medium likelihood / Medium impact |
| **Integration sprawl** | Each new integration (CRM sync, additional social platforms, additional AI providers) added without a generic integrations layer increases bespoke code paths, raising the cost and risk of every future Phase 2/3 integration commitment. | High likelihood / Medium impact |
| **Monolith-to-microservices jump has no migration path** | "Microservices (if appropriate)" is listed as a Phase 3 option with zero described module boundaries today; if scale eventually requires it, the current undifferentiated API layer would require a costly, high-risk decomposition rather than an incremental one. | Low-medium likelihood / High impact (long-term) |
| **Compliance exposure via undocumented AI data handling** | `PRODUCT_REVIEW.md` Section 7 already flagged that AI data handling (PII redaction, vendor training-data use, retention) is undocumented; `ARCHITECTURE.md`'s Security Architecture section covers general data protection but never extends it to AI vendor data flows. | Medium likelihood / High impact |

---

# 5. Missing Components

The following components are referenced or implied by product docs but have no corresponding architectural definition:

1. **Billing/subscription domain model** — subscription state machine, invoice/payment records, usage metering, feature-gating/entitlement checks, dunning/failed-payment handling.
2. **Generic integrations/connections layer** — per-organization credential storage (API keys, OAuth tokens), webhook registry with signature verification, retry/backoff policy, and a provider-abstraction interface so new integrations (CRM, additional social/ad platforms) don't require bespoke wiring each time.
3. **AI orchestration layer** — prompt template storage/versioning, model routing (Claude ↔ Gemini), fallback/circuit-breaker strategy, per-organization/per-feature AI usage and cost metering, and where human-approval gates are enforced (API middleware vs. UI-only).
4. **Search architecture** — explicit description of the current search mechanism (Postgres full-text vs. planned Meilisearch/Algolia), indexing pipeline, and a migration plan between the two so the "<2s indexing" target has a defined owner.
5. **Geospatial/maps query architecture** — spatial indexing strategy, clustering/heat-map computation (client-side vs. server-side), and tile/caching strategy for map-heavy Phase 2 features.
6. **Analytics pipeline beyond event capture** — aggregation/rollup jobs, a data warehouse or materialized-view strategy, and a described path toward Phase 3 predictive analytics (batch ML jobs, feature store, or third-party ML service).
7. **Directory-vs-Organization tenancy model** — an explicit decision on whether Organizations can own multiple Directories, and how that maps to RLS policies, API scoping, and billing (one bill per org vs. per directory).
8. **Domain/module boundaries within the API layer** — no described decomposition (e.g., listings, leads, billing, AI, analytics, integrations as internal modules) to guide either continued modular-monolith growth or an eventual microservices split.
9. **Workflow/automation event model** — no event catalog, trigger registry, or idempotency/retry design to support n8n-driven "custom triggers" and "conditional logic" (Phase 3).
10. **Feature flag / experimentation infrastructure** — API versioning strategy mentions "feature flags for experimental APIs," but no feature-flag service, targeting rules, or rollout mechanism is described, despite being needed for the canary/staged Phase 3 release strategy in `03_ROADMAP.md`.
11. **Compliance/data-residency architecture** — Phase 3 commits to SSO, data residency (EU data in EU), and SOC 2/HIPAA; none of these have an architectural counterpart (e.g., region-pinned storage, audit-log schema, SSO/SAML integration point) in the current document.

---

# 6. Recommended Improvements

1. **Add a Billing & Entitlements section** to `ARCHITECTURE.md` (or a new companion doc) defining the subscription/invoice data model, Stripe webhook event handling, and how tier limits (listings, AI generations, seats, API calls — per `PRODUCT_REVIEW.md` Section 5) are enforced at the API layer.
2. **Introduce a generic Integrations abstraction** — a described pattern for credential storage, webhook registration, and provider adapters — before Phase 2 CRM/social integrations begin, to avoid one-off implementations per vendor.
3. **Formalize an AI Orchestration layer** covering prompt versioning, model routing/fallback between Claude and Gemini, cost/usage metering per organization, and where the existing "human review before publishing" rule (from `02_MVP.md`) is technically enforced.
4. **Reconcile search architecture with current claims** — explicitly state that MVP search runs on Postgres full-text search, define its known limits, and describe the trigger/criteria for migrating to Meilisearch/Algolia in Phase 2, rather than leaving both stated as if concurrently true.
5. **Resolve the Organization-vs-Directory tenancy question** at the architecture level (not just flag it) — document the chosen model and its RLS/API implications before further schema work, since this affects nearly every other capability (billing, search, analytics all scope by tenant).
6. **Define internal module boundaries** within the API layer (e.g., listings, leads, billing, AI, integrations, analytics as logical modules with clear interfaces) so that the existing "Microservices (if appropriate)" Phase 3 option has a real decomposition seam to follow.
7. **Expand geospatial architecture** beyond "PostGIS for geospatial queries" to name the specific indexing (e.g., GiST/SP-GiST indexes), clustering approach, and where heat-map/radius-search computation happens (query-time vs. precomputed).
8. **Describe an analytics aggregation strategy** (scheduled rollups, materialized views, or a lightweight warehouse) as a bridge between current `analytics_events` capture and Phase 3 predictive analytics commitments.
9. **Correct the architecture diagram** so GraphQL and Webhooks are shown consistently with their Phase 2 status (e.g., mark as "planned" rather than co-located with live REST endpoints).
10. **Add a Feature Flags & Experimentation subsection** describing the mechanism (e.g., a flag service or library) that backs both "feature flags for experimental APIs" (already mentioned) and the canary rollout strategy in `03_ROADMAP.md` Phase 3.
11. **Extend Security Architecture to cover AI data handling explicitly** — PII redaction before AI vendor calls, vendor data-retention/training-use terms, and how this ties into the existing GDPR/PII section.
12. **Add listing-volume-specific scalability targets** (e.g., query/index behavior at 100K and 1M+ listings, partitioning strategy) to match the ambition already stated in `01_PROJECT.md`, not just concurrent-user targets.

---

# 7. Architectural Decisions (ADRs)

The following ADRs are recommended to formally record decisions that are currently implicit, inconsistent, or unmade. Each should live under a proposed `docs/Architecture/decisions/` directory following standard ADR format (Context, Decision, Consequences).

### ADR-001: Tenant Isolation Model — Organization vs. Directory
- **Status**: Proposed (decision required before further schema/RLS work)
- **Context**: RLS + `organization_id` is documented, but it's undecided whether an Organization can own multiple Directories.
- **Decision needed**: Either (a) one Organization = one Directory (simpler, matches current schema), or (b) Organizations can own N Directories (requires an additional `directory_id` scoping dimension across RLS, API, and billing).
- **Consequence if deferred**: Continued schema/API work risks being built against an unstated assumption that later requires a breaking migration.

### ADR-002: AI Provider Orchestration Strategy
- **Status**: Proposed
- **Context**: Claude and Gemini are both named integrations with no routing or fallback logic.
- **Decision needed**: Define primary/secondary provider roles per feature, fallback triggers (latency/error thresholds), and whether routing is static (per feature) or dynamic (cost/latency-based).
- **Consequence if deferred**: Provider outage or pricing change directly breaks customer-facing SLAs with no documented mitigation.

### ADR-003: Billing & Entitlement Enforcement Point
- **Status**: Proposed
- **Context**: Subscription tiers exist in product docs; Stripe is a named integration; no enforcement design exists.
- **Decision needed**: Where feature/usage limits are enforced (API middleware vs. database constraints vs. application logic) and how Stripe webhook events synchronize entitlement state.
- **Consequence if deferred**: Ad hoc enforcement scattered across endpoints, inconsistent with future tier changes.

### ADR-004: Search Implementation Path (Postgres FTS → Dedicated Engine)
- **Status**: Proposed
- **Context**: `ARCHITECTURE.md` implies current full-text search via Postgres while also listing Meilisearch/Algolia as a Phase 2 item.
- **Decision needed**: Confirm Postgres full-text search as the MVP search mechanism, define measurable criteria (data volume, latency, relevance needs) that trigger migration to a dedicated engine, and the migration approach (dual-write, cutover).
- **Consequence if deferred**: Ambiguity about which system actually satisfies the "<2s indexing" performance target.

### ADR-005: Internal API Layer Modularization
- **Status**: Proposed
- **Context**: The API layer is currently documented as a single undifferentiated box.
- **Decision needed**: Define logical module boundaries (listings, leads, billing, AI, integrations, analytics) within the modular monolith, including ownership and dependency rules, as a precursor to any future microservices decision.
- **Consequence if deferred**: The Phase 3 "Microservices (if appropriate)" option remains unactionable, and coupling likely increases as more Phase 2/3 features are added to one undivided API layer.

### ADR-006: Generic Integrations Abstraction
- **Status**: Proposed
- **Context**: Each third-party integration is currently documented and presumably implemented as a bespoke code path.
- **Decision needed**: Adopt a standard integration-provider interface (credential storage, webhook handling, retry policy) to be used for all current and future integrations (CRM, additional social/ad platforms).
- **Consequence if deferred**: Rising implementation cost and inconsistent reliability/retry behavior across integrations as Phase 2/3 adds more vendors.

### ADR-007: Data Residency & Compliance Architecture
- **Status**: Proposed
- **Context**: Phase 3 commits to SSO, EU data residency, and SOC 2/GDPR/HIPAA compliance certifications; none have a described architecture today.
- **Decision needed**: Choose the mechanism for region-pinned storage (e.g., Supabase project per region vs. row-level region tagging), SSO/SAML integration point, and audit-log schema/retention.
- **Consequence if deferred**: Enterprise features in Phase 3 (`03_ROADMAP.md` 3.7) would require retrofitting compliance controls onto an architecture not designed for them.

---

# 8. Summary

`ARCHITECTURE.md` is **strong on foundational infrastructure choices, security, and API discipline**, but **weak on the architectural depth needed for the product's own stated differentiators and monetization model** — specifically billing, AI orchestration, integrations, and search. These are the same four areas most likely to require costly rework if implementation begins before the open questions above are resolved. No existing architectural decision in the current document should be reversed as a result of this review; the recommended actions are additive clarifications, new sections, and a small set of ADRs to formally record decisions that are currently implicit or unmade.
