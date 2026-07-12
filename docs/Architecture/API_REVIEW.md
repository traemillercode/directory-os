# DirectoryOS API Review

**Prepared by**: Architecture Review
**Purpose**: Evaluate `API.md` for endpoint consistency, authentication, authorization, pagination, filtering, versioning, error handling, and REST conventions before implementation begins.
**Scope**: `docs/Architecture/API.md`
**Non-goals**: This review does not rewrite `API.md` or remove any endpoints/features. All recommendations are additive and intended to close gaps prior to engineering work.

---

## 1. Endpoint Consistency

✅ **Good**: Resource-oriented naming is mostly consistent (`/listings`, `/leads`, `/analytics/*`), and CRUD verbs (GET/POST/PATCH/DELETE) map correctly to HTTP methods for `/listings` and `/leads`.

⚠️ **Gaps identified**:
- **`GET /listings/export` collides with the `:id` pattern.** `GET /listings/:id` is defined directly above it, so a request for `GET /listings/export` is ambiguous with "retrieve listing with id=export" unless the router special-cases it. This should be documented as `GET /listings:export` (action-style) or moved to a non-colliding path such as `GET /listings/exports`.
- **Inconsistent resource depth**: `/analytics/listing/:id` uses a singular, non-standard sub-path (`listing` rather than `listings/:id`), breaking from the plural convention used everywhere else (`/listings`, `/leads`). Recommend `/analytics/listings/:id`.
- **No update/delete endpoints for other primary resources**: `/leads` only exposes `PATCH` (no `DELETE`, no single-resource `GET /leads/:id`), and `/analytics/*` and `/ai/*` have no resource identifiers of their own (they act on `listing_id` in the body/path of other resources). This is likely intentional, but the document doesn't state which resources are read-only/action-only vs. full CRUD, making it hard to know if the omission is by design or an oversight.
- **AI endpoints are actions, not resources**: `/ai/generate-description`, `/ai/seo-analysis`, `/ai/content-score` are RPC-style verbs-as-paths, inconsistent with the resource-based convention elsewhere. This is a common and acceptable pattern for non-CRUD operations, but it should be explicitly called out as an intentional "action endpoint" category so future endpoints follow the same pattern rather than mixing conventions ad hoc.
- **No `organization` or `users` endpoints** are documented despite being core entities in `DATABASE.md` (organizations, users, api_keys, categories). Given multi-tenancy and RBAC are foundational (per `DATABASE.md`), the absence of `/organizations`, `/users`, `/api-keys`, and `/categories` endpoints is a significant documentation gap, not just an inconsistency.

**Recommendation**: Add a short "Endpoint Conventions" section defining the naming rules (plural nouns for resources, verb-suffixed paths reserved for actions/exports) and reconcile `/listings/export` and `/analytics/listing/:id` against it. Document endpoints (or explicitly mark as "not yet specified") for organizations, users, and categories, since they are first-class entities in the data model.

---

## 2. Authentication

✅ **Good**: JWT bearer token scheme is clearly specified with a concrete header example, and the `/auth/*` group covers signup, login, logout, refresh, and password reset — a reasonably complete auth lifecycle.

⚠️ **Gaps identified**:
- **No mention of which endpoints are unauthenticated.** The document states "All API requests require authentication via JWT bearer token," but `POST /leads` is explicitly called out later as "public endpoint, rate-limited" — a direct contradiction. `/auth/signup` and `/auth/login` are also necessarily unauthenticated. The blanket statement should be corrected to identify the authenticated-by-default rule plus an explicit list of public exceptions (`/auth/signup`, `/auth/login`, `/auth/password-reset`, `/auth/refresh`, `POST /leads`).
- **No token/session details**: No mention of JWT expiry claims beyond `expires_in`, refresh token rotation/invalidation behavior, or what happens to the refresh token on `/auth/logout` (is it revoked server-side, or only the access token discarded client-side?).
- **No API key authentication documented** despite `api_keys` being a table in `DATABASE.md` — presumably intended for server-to-server or third-party integrations. If API keys are a supported auth method, they should be documented alongside JWT; if not yet supported, that should be noted as a future item (consistent with the "Webhooks (Future)" pattern already used in the doc).
- **No password-reset completion endpoint**: `POST /auth/password-reset` only requests the reset email; there's no documented endpoint to actually submit a new password with a reset token (e.g., `POST /auth/password-reset/confirm`). This appears to be a missing endpoint rather than an intentional omission.

**Recommendation**: Correct the blanket authentication statement to explicitly enumerate public vs. authenticated endpoints. Document refresh-token revocation semantics on logout, add the missing password-reset confirmation endpoint, and either document API-key auth or explicitly mark it "planned."

---

## 3. Authorization

⚠️ **This is the weakest section of the document — authorization is not addressed at all.**

- `DATABASE.md` defines a `role` column (`admin`, `editor`, `viewer`) on `users` and enforces per-role access via RLS policies (e.g., only `admin`/`editor` can update listings, only `admin` can delete). None of this is reflected in `API.md`.
- There is no statement of which roles can call which endpoints (e.g., can a `viewer` call `POST /listings` or `PATCH /leads/:id`? Can an `editor` call `DELETE /listings/:id`?).
- **403 Forbidden** is listed in the status codes table, but no endpoint documents *when* it would be returned, since role requirements per-endpoint are never specified.
- Multi-tenancy scoping (organization isolation) is implied ("List all listings for organization") but never explicitly stated as an authorization rule (i.e., a JWT for Org A can never read/write Org B's resources) or tied to how the `organization_id` is derived (from the JWT claims vs. a request parameter — the latter would be a serious IDOR risk if not scoped server-side from the token).
- `POST /leads` is public; there's no mention of what (if anything) prevents a public caller from submitting leads against a `listing_id` belonging to a different/incorrect organization, or from spamming the endpoint (rate limiting is mentioned generically later, but not scoped to this specific abuse vector).

**Recommendation**: Add an "Authorization" section (parallel to "Authentication") that: (1) documents the role model (`admin`/`editor`/`viewer`) and maps CRUD operations per resource to minimum required role, consistent with the RLS policies already defined in `DATABASE.md`; (2) explicitly states that `organization_id` scoping is always derived from the authenticated user's JWT/session, never from client-supplied input; (3) clarifies the trust boundary for the public `POST /leads` endpoint (e.g., listing must be `published` status to accept leads).

---

## 4. Pagination

✅ **Good**: `/listings` and `/leads` document `page`/`limit` query parameters with a sensible default (`page=1`, `limit=20`) and cap (`max: 100`), and the response wraps results in a `data` + `pagination` envelope with `page`, `limit`, `total`, `pages`.

⚠️ **Gaps identified**:
- **Inconsistent application**: `/leads` response shows `"pagination": { ... }` without repeating the shape — acceptable as shorthand, but the `/leads` query parameters section doesn't restate the `max: 100` limit or default values documented for `/listings`, leaving it ambiguous whether the same defaults/caps apply.
- **No pagination on `/analytics/search-terms`**, which returns a `terms` array that could grow unbounded over time. Given it's structurally similar to a list endpoint, it should either document pagination or explicitly state a fixed/capped result size (e.g., "top 50 terms").
- **Offset-based only**: Only `page`/`limit` (offset-based) pagination is supported. This is fine for admin dashboards at current scale, but there's no discussion of performance at high offset values (common issue with `OFFSET` in Postgres) or plans for cursor-based pagination for high-volume tables (e.g., `analytics_events`, which `DATABASE.md` indexes by `(organization_id, timestamp)` — a pattern that suggests cursor pagination was anticipated at the DB layer but isn't exposed at the API layer).

**Recommendation**: State explicitly that all list endpoints share the same `page`/`limit` contract and defaults (or document per-endpoint exceptions), add pagination (or explicit caps) to `/analytics/search-terms`, and note offset-pagination as a known scaling limitation for future cursor-based iteration.

---

## 5. Filtering

✅ **Good**: `/listings` supports `status`, `category`, and free-text `search`; `/leads` supports `status` and `listing_id`. Enum-valued filters (`status`) are documented with their allowed values inline, which is helpful.

⚠️ **Gaps identified**:
- **No filtering on `/analytics/dashboard` or `/analytics/listing/:id` beyond date range** — no filter by category, listing status, etc., which may be fine, but isn't stated as an explicit design decision.
- **`search` on `/listings` is underspecified**: "searches name and description" doesn't specify whether it's substring match, full-text search, case sensitivity, or minimum query length — relevant since this affects index design (`DATABASE.md` would need a corresponding text-search index).
- **No documented filter combination rules** (e.g., can `status` and `category` be combined with `search` simultaneously? Are multiple values allowed per filter, e.g., `status=draft,published`?).
- **Date filters (`date_from`/`date_to`) lack validation rules**: no stated max range, no behavior when `date_from > date_to`, and no default range when omitted (e.g., does omitting both return all-time data, or a default last-30-days window?).

**Recommendation**: Specify search semantics (match type, min length) for `/listings`, define default/max date ranges for analytics endpoints, and state whether filters combine with AND semantics (the implicit but unstated assumption).

---

## 6. Versioning

❌ **Not addressed at all.**

- The base URL (`https://app.directoryos.com/api`) contains no version segment (`/v1/`, etc.) and no `Accept`/custom header-based versioning scheme is mentioned.
- Given the roadmap already anticipates SDKs (TypeScript now, Python/Ruby/Go planned) and webhooks ("Future"), the absence of a versioning strategy is a significant gap — any breaking change to a response shape (e.g., adding required fields, changing `pagination` structure) would break all consumers with no migration path.

**Recommendation**: Add a "Versioning" section before endpoints are finalized for implementation. At minimum, adopt a URL-prefixed version (`/api/v1/...`) matching the existing base-URL convention, and document the deprecation/sunset policy for future breaking changes (even a simple "N months notice, old version returns `Deprecation` header" policy is sufficient at this stage).

---

## 7. Error Handling

✅ **Good**: A single, consistent error envelope (`error.code`, `error.message`, `error.details[]`) is defined, and the status code table is thorough and covers the standard range (400/401/403/404/409/422/429/500/503).

⚠️ **Gaps identified**:
- **No enumerated `error.code` values.** Only `VALIDATION_ERROR` is shown as an example; there's no canonical list of error codes (e.g., `NOT_FOUND`, `UNAUTHORIZED`, `RATE_LIMITED`, `DUPLICATE_SLUG`) that clients can programmatically branch on. Without this, clients are forced to parse HTTP status + free-text `message`, defeating the purpose of a structured `code` field.
- **400 vs. 422 distinction is undefined.** Both are listed as status codes ("Invalid request data" vs. "Validation failed") but the doc never clarifies when to use one over the other (a common source of inconsistent implementation — e.g., malformed JSON vs. semantically invalid fields).
- **409 Conflict** is listed but no endpoint documents a scenario that would trigger it (e.g., duplicate `slug` on `POST /listings`, given `DATABASE.md`'s `idx_listings_org_slug` unique index). This should be tied back to the concrete constraint it maps to.
- **No documented behavior for partial failures** in bulk-oriented operations (`/listings/export`, future bulk import mentioned in rate limiting section) — e.g., what happens if some rows in a bulk operation are valid and others aren't.

**Recommendation**: Publish an enumerated, stable list of `error.code` values (at least one per resource + validation/auth/rate-limit categories), clarify 400 vs. 422 usage, and tie 409 explicitly to the unique constraints it's expected to guard (slug, email-per-org per `DATABASE.md`).

---

## 8. REST Conventions

✅ **Good**: Correct use of `POST` for creation (201), `PATCH` for partial updates, `DELETE` returning `204 No Content`, and `GET` for retrieval — overall this is a reasonably idiomatic REST design.

⚠️ **Gaps identified**:
- **Soft-delete semantics conflict with `204 No Content` + REST idioms**: `DELETE /listings/:id` is documented as a soft delete ("marks `deleted_at`"), but there's no documented way to *restore* a soft-deleted resource, nor any indication of how soft-deleted resources are excluded from `GET /listings` (e.g., is there an `include_deleted` filter for admins?). Soft delete is a valid pattern, but the API contract should state that a `DELETE` is not permanent and describe any recovery path.
- **`/ai/*` and `/analytics/*` endpoints are not resource-oriented (RPC style)**, as noted in Section 1 — acceptable as a hybrid REST+RPC design, but should be explicitly documented as such rather than left implicit, since REST purists and REST-adjacent tooling (OpenAPI codegen, HATEOAS-style clients) treat these differently.
- **No HATEOAS / hypermedia links**, no `Location` header documented on `201 Created` responses (a common REST convention for pointing clients to the newly created resource), and no ETag/If-Match support for optimistic concurrency on `PATCH` (relevant given multiple editors could race on the same listing per the `editor` role in `DATABASE.md`).
- **Idempotency is not addressed** for `POST` endpoints (e.g., double-submitting `POST /leads` on network retry could create duplicate leads; no `Idempotency-Key` header or dedupe strategy is documented).

**Recommendation**: Document soft-delete/restore behavior and exclusion rules for list endpoints, add a `Location` header convention for `201` responses, and consider an `Idempotency-Key` header for `POST /leads` and `POST /listings` given retry-prone client scenarios (mobile forms, flaky networks).

---

## Summary Table

| Area | Status | Key Gap |
|---|---|---|
| Endpoint Consistency | ⚠️ Partial | `/listings/export` path collision; missing organizations/users/categories endpoints |
| Authentication | ⚠️ Partial | Blanket "all requests require auth" contradicts documented public endpoints |
| Authorization | ❌ Missing | No role-to-endpoint mapping despite RBAC existing in `DATABASE.md` |
| Pagination | ⚠️ Partial | Inconsistent restatement across endpoints; unbounded `/analytics/search-terms` |
| Filtering | ⚠️ Partial | Undocumented search semantics, filter combination rules, date range validation |
| Versioning | ❌ Missing | No version segment or deprecation policy in base URL/spec |
| Error Handling | ⚠️ Partial | No enumerated `error.code` list; 400 vs. 422 boundary undefined |
| REST Conventions | ⚠️ Partial | Soft-delete/restore, idempotency, and concurrency control undocumented |

**Overall**: `API.md` provides a solid first draft of the primary CRUD surface and a consistent error envelope, but two structural gaps — **authorization** and **versioning** — should be resolved before implementation begins, since both are difficult to retrofit once clients depend on the API. The remaining items are refinements that can be addressed incrementally without breaking the current design.
