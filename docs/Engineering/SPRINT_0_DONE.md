# Sprint 0 Completion Checklist

## Sprint 0 Overview

**Objective**: Establish GitHub project management infrastructure to enable Sprint 1 execution without writing application code.

**Duration**: 2-3 working days

**Scope**: GitHub Milestones, Labels, Issues, Project Board, and supporting documentation

**Success Criteria**: A developer can read this document and immediately start Sprint 1 with complete clarity on what to build.

---

## ✅ Sprint 0 Completed Deliverables

### 1. GitHub Milestones Created

The following 5 milestones have been created in the GitHub repository to organize work across the 12-week MVP:

- [ ] **M1: Core Infrastructure** (Weeks 1-3)
  - Covers: E1 (Deployment), E2 (Database Foundation), E3 (Authentication & Authorization)
  - Status: Foundational work — nothing else can start until M1 is complete
  - Issues: 18 total (E1-1 through E1-5, E2-1 through E2-8, E3-1 through E3-5)
  - Exit Criteria: Deployed app with auth, database, CI/CD pipeline working
  - Link: `https://github.com/traemillercode/directory-os/milestone/1`

- [ ] **M2: Listing Management** (Weeks 4-6)
  - Covers: E4 (Listing CRUD & UI), E5 (Bulk Import/Export)
  - Status: Depends on M1 complete
  - Issues: 12 total (E4-1 through E4-8, E5-1 through E5-4)
  - Exit Criteria: Users can CRUD, search, filter, bulk import/export 1,000+ listings
  - Link: `https://github.com/traemillercode/directory-os/milestone/2`

- [ ] **M3: AI Features** (Weeks 7-9)
  - Covers: E6 (AI Description Generation, SEO Analysis, Content Scoring)
  - Status: Depends on M2 (Listing CRUD)
  - Issues: 4 total (E6-1 through E6-4)
  - Exit Criteria: AI endpoints working, generation <2s, human review enforced
  - Link: `https://github.com/traemillercode/directory-os/milestone/3`

- [ ] **M4: Analytics** (Weeks 10-11)
  - Covers: E7 (Analytics API & Dashboards)
  - Status: Depends on M1 (Database), M2 (Listing data)
  - Issues: 5 total (E7-1 through E7-5)
  - Exit Criteria: Dashboards load <1s, metrics display real data
  - Link: `https://github.com/traemillercode/directory-os/milestone/4`

- [ ] **M5: Lead Management** (Weeks 11-12)
  - Covers: E8 (Lead Capture, Management, Notifications)
  - Status: Depends on M1 (Auth), M2 (Listings), M4 (Analytics)
  - Issues: 4 total (E8-1 through E8-4)
  - Exit Criteria: Leads captured, managed, exported; notifications working
  - Link: `https://github.com/traemillercode/directory-os/milestone/5`

---

### 2. GitHub Labels Created

All labels from `docs/Engineering/MVP_BACKLOG.md §3` have been created:

**Type Labels** (identifies the kind of work):
- [ ] `type:epic` — High-level feature grouping (Epic tracking issues)
- [ ] `type:feature` — User-facing functionality
- [ ] `type:infra` — Infrastructure/operations work
- [ ] `type:chore` — Maintenance, config, tooling

**Area Labels** (identifies the component):
- [ ] `area:infra` — Deployment, CI/CD, monitoring
- [ ] `area:database` — Schema, migrations, queries
- [ ] `area:auth` — Authentication, authorization, multi-tenancy
- [ ] `area:listings` — Listing CRUD, dashboard, search
- [ ] `area:import-export` — Bulk CSV import/export
- [ ] `area:ai` — AI integrations (Claude, content generation, SEO)
- [ ] `area:analytics` — Analytics endpoints, dashboards, reports
- [ ] `area:leads` — Lead capture, management, notifications

**Complexity Labels** (effort estimate for solo developer):
- [ ] `complexity:XS` — < 0.5 day (config, small features)
- [ ] `complexity:S` — 0.5-1 day (single component, low complexity)
- [ ] `complexity:M` — 2-3 days (multiple components or one integration)
- [ ] `complexity:L` — 4-5 days (cross-cutting feature, multiple endpoints)

**Priority Labels** (determines sequencing):
- [ ] `priority:P0` — Blocking / foundational (affects multiple downstream issues)
- [ ] `priority:P1` — Core MVP feature (required for MVP launch)
- [ ] `priority:P2` — Rounding out MVP (nice-to-have for launch)

**Phase Labels** (MVP phase from 02_MVP.md):
- [ ] `phase:1a-core-infra` — Phase 1A: Weeks 1-3 (Auth, Database, Deployment)
- [ ] `phase:1b-listings` — Phase 1B: Weeks 4-6 (Listing CRUD, Import/Export)
- [ ] `phase:1c-ai` — Phase 1C: Weeks 7-9 (AI Features)
- [ ] `phase:1d-analytics` — Phase 1D: Weeks 10-11 (Analytics)
- [ ] `phase:1e-leads` — Phase 1E: Weeks 11-12 (Lead Management)

---

### 3. GitHub Issues Created

All 43 MVP issues have been created according to `docs/Engineering/MVP_BACKLOG.md`.

**Issue Creation Summary:**

Total Issues: 43
- **Epic E1** (Infrastructure): 5 issues (E1-1 through E1-5)
- **Epic E2** (Database): 8 issues (E2-1 through E2-8)
- **Epic E3** (Auth): 5 issues (E3-1 through E3-5)
- **Epic E4** (Listings): 8 issues (E4-1 through E4-8)
- **Epic E5** (Import/Export): 4 issues (E5-1 through E5-4)
- **Epic E6** (AI): 4 issues (E6-1 through E6-4)
- **Epic E7** (Analytics): 5 issues (E7-1 through E7-5)
- **Epic E8** (Leads): 4 issues (E8-1 through E8-4)

**Each Issue Contains:**
- [ ] Title (clear, descriptive)
- [ ] Epic reference (E1-1 format)
- [ ] Problem statement (what are we solving)
- [ ] Implementation description (how to approach it)
- [ ] Acceptance criteria (what must be true to mark done)
- [ ] Definition of Done (includes common DoD from MVP_BACKLOG.md §5)
- [ ] Dependency links (GitHub "blocked by" relationships)
- [ ] Complexity label (XS, S, M, L, or XL)
- [ ] Priority label (P0, P1, or P2)
- [ ] Phase label (1a-core-infra, 1b-listings, etc.)
- [ ] Area label (infra, database, auth, listings, etc.)
- [ ] Milestone assignment (M1–M5)
- [ ] Type label (epic, feature, infra, chore)
- [ ] Source documentation reference (link to docs/Architecture/*, docs/Product/*)

**Issues by Milestone:**

- [ ] **M1 (18 issues)**: Infrastructure bootstrap
  - E1-1 through E1-5 (Infrastructure & Deployment)
  - E2-1 through E2-8 (Database Foundation)
  - E3-1 through E3-5 (Authentication & Authorization)
  
- [ ] **M2 (12 issues)**: Listing management
  - E4-1 through E4-8 (Listing CRUD & Dashboard)
  - E5-1 through E5-4 (Bulk Import/Export)
  
- [ ] **M3 (4 issues)**: AI features
  - E6-1 through E6-4 (AI Integration)
  
- [ ] **M4 (5 issues)**: Analytics
  - E7-1 through E7-5 (Analytics & Reporting)
  
- [ ] **M5 (4 issues)**: Lead management
  - E8-1 through E8-4 (Lead Capture & Management)

**See:** `docs/Engineering/GITHUB_ISSUES_FULL.md` for complete issue specifications

---

### 4. GitHub Project Board Created

A GitHub Project board has been created to visualize and track work across sprints.

**Project Board Name**: DirectoryOS MVP — Engineering Execution

**Board URL**: `https://github.com/traemillercode/directory-os/projects/1`

**Columns**:
- [ ] **Backlog** — All issues not yet assigned to a sprint (M2–M5)
- [ ] **Sprint 1** — M1 issues (18 total); active sprint starting immediately after Sprint 0
- [ ] **In Progress** — Issues currently being worked on
- [ ] **In Review** — Issues with PRs submitted, awaiting review/merge
- [ ] **Done** — Merged and deployed
- [ ] **Blocked** — Issues waiting on dependencies or external resources

**Board Features**:
- [ ] All 43 issues visible and movable between columns
- [ ] Filterable by epic, milestone, label, priority
- [ ] Automated rules:
  - PR creation → moves issue to "In Review"
  - PR merge → moves to "Done"
  - Issue assignment → moves to "In Progress"
- [ ] Sprint 1 (M1 issues) are pre-populated in the "Sprint 1" column

**How to Use the Board**:
1. View Sprint 1 column to see the next 18 issues to implement
2. Click an issue to see acceptance criteria and dependencies
3. Drag issues to "In Progress" when you start them
4. Create a PR and link it to the issue (GitHub auto-moves to "In Review")
5. Merge the PR (GitHub auto-moves to "Done")

---

### 5. Supporting Documentation Created

The following documentation files have been created to support Sprint 1 execution:

- [ ] **docs/Engineering/SPRINT_1_START.md**
  - First issue to implement (E1-1)
  - Expected branch name
  - Files changed
  - Testing requirements
  - Definition of Done checklist
  - How to open first PR

- [ ] **docs/Engineering/DEVELOPMENT_QUICKSTART.md**
  - Environment setup (< 30 min)
  - How to clone, install dependencies, run tests
  - Local database setup (Supabase or Docker)
  - How to run dev server
  - Troubleshooting

- [ ] **docs/Engineering/GITHUB_ISSUES_FULL.md**
  - Complete specifications for all 43 issues
  - Copy-paste ready for GitHub issue creation
  - All acceptance criteria, DoD, dependencies

- [ ] **docs/Engineering/RATE_LIMITING_SPEC.md**
  - Rate limiting strategy (Upstash Redis for per-user, Vercel for per-IP)
  - Implementation details
  - Test requirements
  - Cost estimates

- [ ] **docs/Engineering/SECRETS_ROTATION_SCHEDULE.md**
  - Quarterly rotation schedule (Jan, Apr, Jul, Oct)
  - Which secrets to rotate (Stripe, Claude, Mapbox, Resend, Supabase)
  - Rotation procedure
  - Verification steps
  - Calendar reminder setup

- [ ] **docs/Engineering/RLS_TESTING_APPROACH.md**
  - How to test Row-Level Security policies
  - Integration test template
  - Cross-org isolation verification
  - When tests run (every push to develop/main)

- [ ] **docs/Engineering/CONCURRENT_WRITES_STRATEGY.md**
  - Last-Write-Wins (LWW) semantics for MVP
  - Why chosen (simplicity over accuracy for Phase 1)
  - How audit trail captures overwrites
  - Upgrade path (optimistic locking for Phase 2+)

- [ ] **docs/Engineering/ERROR_HANDLING_SPEC.md**
  - Error normalization utility specification
  - Standard error envelope format (from API.md)
  - What to log vs. what to hide
  - Sentry integration for unhandled errors

---

### 6. GitHub Actions CI/CD Pipeline Configured

The CI/CD pipeline has been set up to enforce quality standards on every push and PR:

**GitHub Actions Workflow File**: `.github/workflows/ci.yml` (reference template provided)

**Workflow Stages**:
- [ ] **Lint & Format Check** (ESLint, Prettier)
  - Runs: `npm run lint`
  - Fails merge if violations found
  - Enforced on every PR
  
- [ ] **Type Check** (TypeScript)
  - Runs: `npm run type-check`
  - Fails merge if type errors found
  - Enforced on every PR
  
- [ ] **Unit Tests**
  - Runs: `npm run test`
  - Reports coverage
  - Fails merge if coverage below 80% (critical paths require 95%+)
  - Enforced on every PR
  
- [ ] **Integration Tests**
  - Runs: `npm run test:integration`
  - Requires test database provisioning
  - Verifies RLS policies, API contracts
  - Fails merge if any test fails
  - Enforced on every PR
  
- [ ] **Security Scanning**
  - Runs: GitHub CodeQL + dependency scanning
  - Detects vulnerabilities in dependencies
  - Fails merge if critical vulnerability found
  - Enforced on every PR
  
- [ ] **Secret Scanning**
  - GitHub native secret scanning enabled
  - Blocks commits containing API keys, tokens
  - Fails merge if secrets detected
  - Enforced on every push

**Deployment Rules**:
- [ ] Merge to `main` → Automatic production deployment
- [ ] Merge to `develop` → Automatic staging deployment
- [ ] PR/feature branches → Preview URL on Vercel (temporary, auto-deleted)
- [ ] Failed CI → Blocks merge (no exceptions, no force-push)

---

### 7. Documentation Links Updated

All official documentation has been updated to reference Sprint 0 completion:

- [ ] **README.md**: Added link to Sprint 0 completion checklist and project board
- [ ] **docs/Engineering/SPRINT_PLAN.md**: Confirmed Sprint 0 scope and exit criteria
- [ ] **docs/Engineering/MVP_BACKLOG.md**: Issues now linked to GitHub issue numbers
- [ ] **docs/Backlog/README.md**: References to GitHub issue conversion

---

## ✅ Sprint 0 Verification Checklist

Before starting Sprint 1, verify that ALL of the following are true:

### GitHub Repository State
- [ ] 5 milestones exist (M1–M5) with correct descriptions and dates
- [ ] 15 labels exist (type, area, complexity, priority, phase)
- [ ] 43 issues created and visible in GitHub
- [ ] Each issue has title, description, acceptance criteria, DoD, labels, milestone
- [ ] Each issue has correct complexity and priority assigned
- [ ] Dependency links are correct (E3-1 blocked by E2-3, etc.)
- [ ] Project board exists with 6 columns and all 43 issues
- [ ] Sprint 1 column contains exactly 18 issues (M1)
- [ ] First 10 issues clearly identified and marked in Sprint 1 column

### Local Development Environment
- [ ] `.github/workflows/ci.yml` exists and is valid YAML
- [ ] GitHub Actions available and logs show successful workflow creation
- [ ] Secret scanning is enabled on the repository
- [ ] Branch protection rule exists on `main` (require PR review, require CI passing)
- [ ] `.env.example` exists with all required environment variables documented
- [ ] `.gitignore` includes `.env.local`, `.env*.local`, `node_modules/`, `.DS_Store`
- [ ] `package.json` has scripts: `dev`, `build`, `start`, `lint`, `type-check`, `test`, `test:integration`

### Documentation State
- [ ] `docs/Engineering/SPRINT_0_DONE.md` (this file) created and merged
- [ ] `docs/Engineering/SPRINT_1_START.md` created with first issue details
- [ ] `docs/Engineering/DEVELOPMENT_QUICKSTART.md` created with < 30 min setup guide
- [ ] `docs/Engineering/GITHUB_ISSUES_FULL.md` created with all 43 issue specs
- [ ] `docs/Engineering/RATE_LIMITING_SPEC.md` created
- [ ] `docs/Engineering/SECRETS_ROTATION_SCHEDULE.md` created
- [ ] `docs/Engineering/RLS_TESTING_APPROACH.md` created
- [ ] `docs/Engineering/CONCURRENT_WRITES_STRATEGY.md` created
- [ ] `docs/Engineering/ERROR_HANDLING_SPEC.md` created

### Team Readiness
- [ ] CTO has reviewed all 43 issues for accuracy against MVP_BACKLOG.md
- [ ] First developer has read `SPRINT_1_START.md` and understands E1-1
- [ ] First developer can follow `DEVELOPMENT_QUICKSTART.md` and be productive in < 30 min
- [ ] First developer can clone repo, run tests, and see them pass (or appropriately fail)
- [ ] All team members have access to GitHub project board

---

## 🚀 Sprint 1 Kickoff Instructions

### Ready to Start Building?

1. **Read Your Marching Orders**:
   ```bash
   cat docs/Engineering/SPRINT_1_START.md
   ```

2. **Set Up Your Development Environment**:
   ```bash
   # Follow the 30-minute quickstart
   cat docs/Engineering/DEVELOPMENT_QUICKSTART.md
   ```

3. **View Your Sprint 1 Work**:
   - Open the Project Board: `https://github.com/traemillercode/directory-os/projects/1`
   - Look at the "Sprint 1" column: 18 issues from M1 (Core Infrastructure)
   - First 10 issues are the critical path (marked in the board)

4. **Start with Issue #1 (E1-1: Scaffold Next.js)**:
   - Read the issue description in GitHub
   - Read `docs/Engineering/SPRINT_1_START.md` for detailed instructions
   - Create branch: `feature/e1-1-scaffold-nextjs`
   - Expected output: `npm run dev` works, `npm run build` succeeds, `npm run lint` passes
   - Follow the acceptance criteria in the GitHub issue

5. **Create Your First PR**:
   ```bash
   git checkout -b feature/e1-1-scaffold-nextjs
   # ... make changes ...
   git add .
   git commit -m "feat(infra): scaffold Next.js project with TypeScript and Tailwind
   
   - Initialize Next.js 14+ with App Router
   - Add TypeScript configuration
   - Add Tailwind CSS and shadcn/ui
   - Verify build and lint pass
   
   Closes #[ISSUE_NUMBER]"
   git push origin feature/e1-1-scaffold-nextjs
   # Go to GitHub and create a PR from your branch to `develop`
   ```

6. **Get Feedback**:
   - CI will automatically run (lint, type-check, tests)
   - Once green, request a code review (or self-review if solo)
   - Address any feedback, push again
   - Merge to `develop` when approved

7. **Next Issue**:
   - Once E1-1 PR is merged, GitHub project board moves it to "Done"
   - Move to E1-2 (Provision Supabase)
   - Follow the same process

---

## 📋 Key Metrics to Track

During Sprint 1, track these metrics to stay on pace:

| Metric | Target | Frequency |
|--------|--------|----------|
| Issues completed per week | 5-6 issues/week (18 issues ÷ 3 weeks) | Weekly |
| CI pass rate | 100% (no broken builds) | Per PR |
| Test coverage on critical paths | 95%+ | Per PR |
| Average PR cycle time | < 2 hours (from submit to merge) | Per PR |
| Deployment success rate | 100% (no rollbacks) | Per deploy |
| Average issue cycle time | 2-4 days per issue | Per issue |

---

## 📞 Support & Escalation

If you get stuck during Sprint 1:

1. **Check the Issue Description**: Each GitHub issue links to the relevant section in `docs/Architecture/`, `docs/Product/`, etc.
2. **Read DEVELOPMENT_WORKFLOW.md**: Clarifies branching, commit conventions, and PR process
3. **Read DEVELOPMENT_QUICKSTART.md**: Local setup help
4. **Review Test Examples**: Look at `tests/` directory for patterns (will be created in E1-1)
5. **Check ARCHITECTURE.md**: Verify you're following the tech stack choices (Next.js, Supabase, Prisma, etc.)
6. **Ask in Comments**: Leave a comment on the GitHub issue with specific blockers

**CTO is available for**:
- Architecture decisions
- Dependency clarification
- Milestone reclassification
- Emergency scope changes
- Definition of Done disputes

---

## ✅ Sprint 0 Sign-Off

Sprint 0 is complete when this checklist is 100% checked and the first developer can start Sprint 1 immediately.

**Completed by**: [CTO Name]  
**Date Completed**: [YYYY-MM-DD]  
**Verification**: All 43 issues created, labeled, milestoned, and visible on GitHub Project board  
**Next Step**: Start Sprint 1 with E1-1 (Scaffold Next.js project)  

**Sign-off checklist:**
- [ ] All sections above verified complete
- [ ] First developer has read SPRINT_1_START.md
- [ ] Project board is live at https://github.com/traemillercode/directory-os/projects/1
- [ ] First 10 issues are clearly marked in Sprint 1 column
- [ ] Merge this file to `main`
- [ ] CTO has confirmed: "Ready to build"

---

## 📚 Reference Links

- **GitHub Project Board**: `https://github.com/traemillercode/directory-os/projects/1`
- **GitHub Issues**: `https://github.com/traemillercode/directory-os/issues`
- **MVP Backlog Source**: `docs/Engineering/MVP_BACKLOG.md`
- **First Issue Guide**: `docs/Engineering/SPRINT_1_START.md`
- **Setup Guide**: `docs/Engineering/DEVELOPMENT_QUICKSTART.md`
- **All Issue Specs**: `docs/Engineering/GITHUB_ISSUES_FULL.md`
- **Architecture**: `docs/Architecture/ARCHITECTURE.md`
- **API Spec**: `docs/Architecture/API.md`
- **Database Schema**: `docs/Architecture/DATABASE.md`
- **Product MVP**: `docs/Product/02_MVP.md`
- **Implementation Order**: `docs/Engineering/IMPLEMENTATION_ORDER.md`
