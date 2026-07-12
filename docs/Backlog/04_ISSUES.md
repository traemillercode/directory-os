# Issues

Complete backlog of GitHub Issues, grouped by epic. Each issue lists suggested labels and its source documentation reference. Issues marked **[Phase 2 — deferred]** belong to `EPIC-12` (milestone M6) even though they originate from an MVP-phase document section; this follows the reclassification in `docs/Product/PRODUCT_REVIEW.md` §3.

---

## EPIC-1: Product Documentation Closure (Milestone: M0)

1. **Draft `docs/Product/PERSONAS.md`** — define primary persona, pain points, jobs-to-be-done
   Labels: `type:docs`, `phase:mvp`, `area:docs`, `priority:p0-blocker`
   Source: `PRODUCT_REVIEW.md` §2, §9

2. **Draft `docs/Product/USER_STORIES.md`** — acceptance criteria per MVP feature
   Labels: `type:docs`, `phase:mvp`, `area:docs`, `priority:p1-high`
   Source: `PRODUCT_REVIEW.md` §9; `02_MVP.md` User Flows

3. **Draft `docs/Product/PRICING_STRATEGY.md`** — tier pricing, billing lifecycle, trial/cancellation
   Labels: `type:docs`, `phase:mvp`, `area:docs`, `priority:p0-blocker`
   Source: `PRODUCT_REVIEW.md` §5, §9

4. **Draft `docs/Product/PRODUCT_METRICS.md`** — KPI ownership and review cadence
   Labels: `type:docs`, `phase:mvp`, `area:docs`, `priority:p2-medium`
   Source: `PRODUCT_REVIEW.md` §6, §9

5. **Draft `docs/Product/AI_GOVERNANCE.md`** — approval workflows, prompt versioning, cost tracking, model fallback, AI data privacy
   Labels: `type:docs`, `phase:mvp`, `area:ai`, `area:docs`, `priority:p0-blocker`
   Source: `PRODUCT_REVIEW.md` §7, §9

6. **Resolve Directory-vs-Organization data model ambiguity before schema lock**
   Labels: `type:spike`, `phase:mvp`, `area:database`, `priority:p0-blocker`, `status:blocked`
   Source: `PRODUCT_REVIEW.md` §4

7. **Define billing/subscription data model (tables) to support Stripe**
   Labels: `type:task`, `phase:mvp`, `area:database`, `priority:p0-blocker`
   Source: `PRODUCT_REVIEW.md` §5

---

## EPIC-2: Authentication & Multi-Tenancy (Milestone: M1)

8. **Implement email/password signup and login**
   Labels: `type:feature`, `phase:mvp`, `area:auth`, `priority:p0-blocker`
   Source: `02_MVP.md` Phase 1A.1; `API.md` `POST /auth/signup`, `POST /auth/login`

9. **Implement organization creation on signup (multi-tenancy)**
   Labels: `type:feature`, `phase:mvp`, `area:auth`, `priority:p0-blocker`
   Source: `02_MVP.md` Phase 1A.1

10. **Implement RBAC (admin/editor/viewer)**
    Labels: `type:feature`, `phase:mvp`, `area:auth`, `priority:p0-blocker`
    Source: `02_MVP.md` Phase 1A.1; `DATABASE.md` `users.role`

11. **Implement JWT session management and refresh tokens**
    Labels: `type:feature`, `phase:mvp`, `area:auth`, `priority:p0-blocker`
    Source: `API.md` `POST /auth/refresh`; `ARCHITECTURE.md` Security Measures

12. **Implement password reset via Resend**
    Labels: `type:feature`, `phase:mvp`, `area:auth`, `priority:p1-high`
    Source: `API.md` `POST /auth/password-reset`

13. **Implement logout / session invalidation**
    Labels: `type:feature`, `phase:mvp`, `area:auth`, `priority:p1-high`
    Source: `API.md` `POST /auth/logout`

14. **Enforce Row-Level Security policies for tenant isolation**
    Labels: `type:task`, `phase:mvp`, `area:auth`, `area:security`, `priority:p0-blocker`
    Source: `DATABASE.md` Row-Level Security (RLS) Policies

---

## EPIC-3: Database Foundation & RLS (Milestone: M1)

15. **Create `organizations` table and indexes**
    Labels: `type:task`, `phase:mvp`, `area:database`, `priority:p0-blocker`
    Source: `DATABASE.md` §1

16. **Create `users` table and indexes**
    Labels: `type:task`, `phase:mvp`, `area:database`, `priority:p0-blocker`
    Source: `DATABASE.md` §2

17. **Create `listings` table and indexes (including geospatial index)**
    Labels: `type:task`, `phase:mvp`, `area:database`, `priority:p0-blocker`
    Source: `DATABASE.md` §3

18. **Create `leads` table and indexes**
    Labels: `type:task`, `phase:mvp`, `area:database`, `priority:p0-blocker`
    Source: `DATABASE.md` §4

19. **Create `analytics_events` table and indexes**
    Labels: `type:task`, `phase:mvp`, `area:database`, `priority:p0-blocker`
    Source: `DATABASE.md` §5

20. **Create `listings_audit` table (change tracking/versioning)**
    Labels: `type:task`, `phase:mvp`, `area:database`, `priority:p1-high`
    Source: `DATABASE.md` §6; gap noted in `PRODUCT_REVIEW.md` §4 (missing `content_versions` table)

21. **Create `categories` table with hierarchy support (parent/child)**
    Labels: `type:task`, `phase:mvp`, `area:database`, `priority:p1-high`
    Source: `DATABASE.md` §7; `PRODUCT_REVIEW.md` §4

22. **Create `api_keys` table (future-proofing)**
    Labels: `type:task`, `phase:mvp`, `area:database`, `priority:p2-medium`
    Source: `DATABASE.md` §8

23. **Set up Prisma migrations pipeline**
    Labels: `type:chore`, `phase:mvp`, `area:database`, `area:infra`, `priority:p0-blocker`
    Source: `ARCHITECTURE.md` CI/CD Pipeline; `AUTOMATION.md` Database Automation

---

## EPIC-4: Deployment & CI/CD Infrastructure (Milestone: M1)

24. **Configure Vercel deployment (staging and production environments)**
    Labels: `type:chore`, `phase:mvp`, `area:infra`, `priority:p0-blocker`
    Source: `02_MVP.md` Phase 1A.3; `ARCHITECTURE.md` Deployment Architecture

25. **Configure Supabase PostgreSQL (managed database)**
    Labels: `type:chore`, `phase:mvp`, `area:infra`, `priority:p0-blocker`
    Source: `02_MVP.md` Phase 1A.3

26. **Set up GitHub Actions: Lint & Format workflow**
    Labels: `type:chore`, `phase:mvp`, `area:infra`, `priority:p0-blocker`
    Source: `AUTOMATION.md` CI/CD Pipeline #1

27. **Set up GitHub Actions: TypeScript type-check workflow**
    Labels: `type:chore`, `phase:mvp`, `area:infra`, `priority:p0-blocker`
    Source: `AUTOMATION.md` CI/CD Pipeline #2

28. **Set up GitHub Actions: Test workflow (unit + integration + coverage)**
    Labels: `type:chore`, `phase:mvp`, `area:infra`, `area:testing`, `priority:p0-blocker`
    Source: `AUTOMATION.md` CI/CD Pipeline #3

29. **Set up GitHub Actions: Security scanning (CodeQL, Snyk)**
    Labels: `type:chore`, `phase:mvp`, `area:infra`, `area:security`, `priority:p1-high`
    Source: `AUTOMATION.md` CI/CD Pipeline #4

30. **Set up GitHub Actions: Deploy workflow (staging auto, production manual approval)**
    Labels: `type:chore`, `phase:mvp`, `area:infra`, `priority:p0-blocker`
    Source: `AUTOMATION.md` CI/CD Pipeline #5

31. **Configure Dependabot**
    Labels: `type:chore`, `phase:mvp`, `area:infra`, `priority:p2-medium`
    Source: `AUTOMATION.md` Dependabot

32. **Configure pre-commit hooks (Husky + lint-staged)**
    Labels: `type:chore`, `phase:mvp`, `area:infra`, `priority:p2-medium`
    Source: `AUTOMATION.md` Pre-commit Hooks

33. **Integrate Sentry error tracking**
    Labels: `type:chore`, `phase:mvp`, `area:infra`, `area:security`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1A.3; `ARCHITECTURE.md` Observability

---

## EPIC-5: Listing Dashboard & CRUD (Milestone: M2)

34. **Build listings table view (search/filter/sort/pagination)**
    Labels: `type:feature`, `phase:mvp`, `area:listings`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1B.1

35. **Build bulk actions on listing dashboard**
    Labels: `type:feature`, `phase:mvp`, `area:listings`, `priority:p2-medium`
    Source: `02_MVP.md` Phase 1B.1

36. **Build quick stats widget**
    Labels: `type:feature`, `phase:mvp`, `area:listings`, `priority:p2-medium`
    Source: `02_MVP.md` Phase 1B.1

37. **Build create/edit listing form with validation**
    Labels: `type:feature`, `phase:mvp`, `area:listings`, `priority:p0-blocker`
    Source: `02_MVP.md` Phase 1B.2

38. **Integrate Mapbox address autocomplete**
    Labels: `type:feature`, `phase:mvp`, `area:listings`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1B.2; `ARCHITECTURE.md` Integrations

39. **Implement image upload via Supabase Storage**
    Labels: `type:feature`, `phase:mvp`, `area:listings`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1B.2

40. **Implement auto-save / draft protection**
    Labels: `type:feature`, `phase:mvp`, `area:listings`, `priority:p2-medium`
    Source: `02_MVP.md` Phase 1B.2

41. **Implement rich text editor for descriptions**
    Labels: `type:feature`, `phase:mvp`, `area:listings`, `priority:p2-medium`
    Source: `02_MVP.md` Phase 1B.2

42. **Implement listing preview mode**
    Labels: `type:feature`, `phase:mvp`, `area:listings`, `priority:p3-low`
    Source: `02_MVP.md` Phase 1B.2

43. **Implement `GET/POST/PATCH/DELETE /api/listings` endpoints**
    Labels: `type:feature`, `phase:mvp`, `area:listings`, `priority:p0-blocker`
    Source: `API.md` Listings Endpoints

---

## EPIC-6: Bulk Import/Export (Milestone: M2)

44. **Build CSV upload with validation**
    Labels: `type:feature`, `phase:mvp`, `area:import-export`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1B.3

45. **Build column-mapping wizard**
    Labels: `type:feature`, `phase:mvp`, `area:import-export`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1B.3

46. **Implement duplicate detection on import**
    Labels: `type:feature`, `phase:mvp`, `area:import-export`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1B.3

47. **Implement import progress tracking**
    Labels: `type:feature`, `phase:mvp`, `area:import-export`, `priority:p2-medium`
    Source: `02_MVP.md` Phase 1B.3

48. **Implement error reporting / import logs**
    Labels: `type:feature`, `phase:mvp`, `area:import-export`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1B.3

49. **Implement rollback capability for failed imports**
    Labels: `type:feature`, `phase:mvp`, `area:import-export`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1B.3

50. **Implement CSV export (all/filtered) with audit trail**
    Labels: `type:feature`, `phase:mvp`, `area:import-export`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1B.4; `API.md` `GET /listings/export`

---

## EPIC-7: AI Description Generation (Milestone: M3)

51. **Integrate Claude API client**
    Labels: `type:feature`, `phase:mvp`, `area:ai`, `priority:p0-blocker`
    Source: `02_MVP.md` Phase 1C.1; `ARCHITECTURE.md` Claude & Gemini APIs

52. **Build category-specific smart prompts**
    Labels: `type:feature`, `phase:mvp`, `area:ai`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1C.1; `AUTOMATION.md` Prompt Engineering Automation

53. **Implement one-click single-listing description generation**
    Labels: `type:feature`, `phase:mvp`, `area:ai`, `priority:p0-blocker`
    Source: `02_MVP.md` Phase 1C.1; `API.md` `POST /ai/generate-description`

54. **Implement human-review gate before publishing AI content**
    Labels: `type:feature`, `phase:mvp`, `area:ai`, `priority:p0-blocker`
    Source: `02_MVP.md` Phase 1C.1; `AUTOMATION.md` AI-Powered Description Generation flow

55. **Implement version history for AI-generated descriptions**
    Labels: `type:feature`, `phase:mvp`, `area:ai`, `area:database`, `priority:p1-high`, `status:blocked`
    Source: `02_MVP.md` Phase 1C.1 (depends on Issue #20 / `content_versions` table gap per `PRODUCT_REVIEW.md` §4)

56. **[Phase 2 — deferred] Bulk description generation with rate limiting**
    Labels: `type:feature`, `phase:2`, `area:ai`, `priority:p3-low`
    Source: `02_MVP.md` Phase 1C.1; `PRODUCT_REVIEW.md` §3

57. **[Phase 2 — deferred] SEO Analysis (title/meta/keyword density/readability)**
    Labels: `type:feature`, `phase:2`, `area:ai`, `priority:p3-low`
    Source: `02_MVP.md` Phase 1C.2; `API.md` `POST /ai/seo-analysis`; `PRODUCT_REVIEW.md` §3

58. **[Phase 2 — deferred] Content Quality Scoring**
    Labels: `type:feature`, `phase:2`, `area:ai`, `priority:p3-low`
    Source: `02_MVP.md` Phase 1C.3; `API.md` `POST /ai/content-score`; `PRODUCT_REVIEW.md` §3

---

## EPIC-8: Analytics Dashboard — MVP subset (Milestone: M4)

59. **Build overview dashboard (key metrics, quick actions, activity feed)**
    Labels: `type:feature`, `phase:mvp`, `area:analytics`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1D.1; `API.md` `GET /analytics/dashboard`

60. **Build minimal listing analytics (views, CTR, leads, device breakdown)**
    Labels: `type:feature`, `phase:mvp`, `area:analytics`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1D.2; `API.md` `GET /analytics/listing/:id`

61. **[Phase 2 — deferred] Search analytics (keywords, position, impressions)**
    Labels: `type:feature`, `phase:2`, `area:analytics`, `priority:p3-low`
    Source: `02_MVP.md` Phase 1D.3; `API.md` `GET /analytics/search-terms`; `PRODUCT_REVIEW.md` §3

62. **[Phase 2 — deferred] Downloadable/scheduled PDF reports and period comparisons**
    Labels: `type:feature`, `phase:2`, `area:analytics`, `priority:p3-low`
    Source: `02_MVP.md` Phase 1D.4; `API.md` `GET /analytics/report`; `PRODUCT_REVIEW.md` §3

---

## EPIC-9: Lead Capture & Management (Milestone: M5)

63. **Build embedded lead capture form with reCAPTCHA**
    Labels: `type:feature`, `phase:mvp`, `area:leads`, `priority:p0-blocker`
    Source: `02_MVP.md` Phase 1E.1

64. **Implement `POST /api/leads` public rate-limited endpoint**
    Labels: `type:feature`, `phase:mvp`, `area:leads`, `priority:p0-blocker`
    Source: `API.md` Leads Endpoints

65. **Build lead list and status workflow (new/contacted/converted/lost)**
    Labels: `type:feature`, `phase:mvp`, `area:leads`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1E.2

66. **Implement automated lead quality scoring**
    Labels: `type:feature`, `phase:mvp`, `area:leads`, `priority:p1-high`
    Source: `02_MVP.md` Phase 1E.2

67. **Implement lead export (CSV)**
    Labels: `type:feature`, `phase:mvp`, `area:leads`, `priority:p2-medium`
    Source: `02_MVP.md` Phase 1E.2; `API.md` `GET /leads/export`

68. **[Phase 2 — deferred] Email alerts / in-app notifications / daily digest**
    Labels: `type:feature`, `phase:2`, `area:leads`, `priority:p3-low`
    Source: `02_MVP.md` Phase 1E.3; `PRODUCT_REVIEW.md` §3

---

## EPIC-10: Security, Compliance & Observability (Cross-cutting)

69. **Enforce HTTPS-only, CORS configuration, and rate limiting**
    Labels: `type:task`, `phase:mvp`, `area:security`, `priority:p0-blocker`
    Source: `ARCHITECTURE.md` Security Measures; `API.md` Rate Limiting

70. **Implement JWT rotation, CSRF tokens, and input validation (Zod)**
    Labels: `type:task`, `phase:mvp`, `area:security`, `priority:p0-blocker`
    Source: `ARCHITECTURE.md` Security Measures

71. **Implement audit logging of sensitive actions**
    Labels: `type:task`, `phase:mvp`, `area:security`, `priority:p1-high`
    Source: `02_MVP.md` Security Requirements; `DATABASE.md` `listings_audit`

72. **Implement GDPR/CCPA data export and deletion workflows**
    Labels: `type:feature`, `phase:mvp`, `area:security`, `priority:p0-blocker`
    Source: `02_MVP.md` Compliance; gap closed per `PRODUCT_REVIEW.md` §5

73. **Publish Terms of Service, Privacy Policy, and cookie consent management**
    Labels: `type:docs`, `phase:mvp`, `area:security`, `priority:p1-high`
    Source: `02_MVP.md` Compliance

74. **Set up uptime monitoring and alerting**
    Labels: `type:chore`, `phase:mvp`, `area:security`, `area:infra`, `priority:p1-high`
    Source: `ARCHITECTURE.md` Monitoring & Alerting

---

## EPIC-11: Testing & QA Automation (Cross-cutting)

75. **Set up Jest unit tests (90%+ coverage target)**
    Labels: `type:chore`, `phase:mvp`, `area:testing`, `priority:p0-blocker`
    Source: `AUTOMATION.md` Unit Tests

76. **Set up Playwright integration tests (signup, listings CRUD, CSV import, lead capture, analytics)**
    Labels: `type:chore`, `phase:mvp`, `area:testing`, `priority:p0-blocker`
    Source: `AUTOMATION.md` Integration Tests

77. **Set up Lighthouse CI performance tests**
    Labels: `type:chore`, `phase:mvp`, `area:testing`, `priority:p2-medium`
    Source: `AUTOMATION.md` Performance Tests

78. **Validate MVP performance targets (dashboard <1s, search <500ms, AI generation <2s, CSV import of 1,000 rows <30s)**
    Labels: `type:task`, `phase:mvp`, `area:testing`, `priority:p0-blocker`
    Source: `02_MVP.md` Performance Targets

---

## EPIC-12: Phase 2 Deferred Features Backlog (Milestone: M6)

79. **Social media scheduling (Instagram, Facebook, LinkedIn, Twitter/X, TikTok)**
    Labels: `type:feature`, `phase:2`, `priority:p3-low`
    Source: `03_ROADMAP.md` 2.1

80. **Email marketing automation (campaign builder, sequences, A/B testing)**
    Labels: `type:feature`, `phase:2`, `priority:p3-low`
    Source: `03_ROADMAP.md` 2.2

81. **Advanced Maps integration (embedded maps, clustering, radius search, heat maps)**
    Labels: `type:feature`, `phase:2`, `priority:p3-low`
    Source: `03_ROADMAP.md` 2.3

82. **Lead qualification & scoring, CRM integration, webhook delivery**
    Labels: `type:feature`, `phase:2`, `priority:p3-low`
    Source: `03_ROADMAP.md` 2.4

83. **Advanced reporting (custom reports, GA4/Clarity integration, BI export)**
    Labels: `type:feature`, `phase:2`, `priority:p3-low`
    Source: `03_ROADMAP.md` 2.5

84. **Track all MVP-deferred items** (Issues #56–58, #61–62, #68) under this epic for Phase 2 grooming
    Labels: `type:task`, `phase:2`, `priority:p3-low`
    Source: `PRODUCT_REVIEW.md` §3

85. **Groom top-voted `04_IDEAS.md` candidates for Phase 2 consideration**: Bulk Editing with AI, Lead Scoring via AI, Mobile App (iOS & Android), Google My Business Sync, Custom Fields & Metadata, Two-Factor Authentication (2FA)
    Labels: `idea`, `phase:2`, `priority:p3-low`
    Source: `04_IDEAS.md` Top 10 Most Requested Ideas
