# Sprint 1: First Issue Kickoff — E1-1 Scaffold Next.js Project

## Overview

**Milestone**: M1 — Core Infrastructure (Weeks 1-3)  
**Issue**: E1-1  
**Epic**: E1 — Core Infrastructure & Deployment  
**Complexity**: Small (S) — 0.5-1 day  
**Priority**: P0 — Blocking / Foundational  

---

## ⏱️ Before You Start (5 minutes)

### Prerequisites

1. **Node.js 18+** installed
   ```bash
   node --version  # Should be v18.0.0 or higher
   ```

2. **npm or pnpm** available
   ```bash
   npm --version  # or: pnpm --version
   ```

3. **Git** configured with your credentials
   ```bash
   git config user.name
   git config user.email
   ```

4. **GitHub account** with access to traemillercode/directory-os

5. **Code editor** (VS Code, Cursor, or similar)

### Environment Setup

If this is your first time working on DirectoryOS:

```bash
# 1. Clone the repository
git clone https://github.com/traemillercode/directory-os.git
cd directory-os

# 2. Verify the project structure exists
ls -la docs/
ls -la .github/  # Should have workflows/ directory

# 3. Read Sprint 0 completion checklist
cat docs/Engineering/SPRINT_0_DONE.md
```

---

## 🎯 What You're Building

### Issue Description

**Title**: Scaffold Next.js + TypeScript + Tailwind + shadcn/ui project

**Problem Statement**:  
We need a production-ready Next.js foundation with TypeScript, Tailwind CSS, and shadcn/ui components. This is the root dependency for all subsequent issues — nothing else can start until this is working.

**Implementation Description**:  
Initialize a Next.js 14+ project with the App Router, TypeScript support, Tailwind CSS for styling, and shadcn/ui for accessible components. Configure build tooling, linting, and type checking.

---

## ✅ Acceptance Criteria

Your work is acceptable when **ALL** of the following are true:

- [ ] **Next.js 14+ with App Router**
  - `npm run dev` starts the dev server on localhost:3000
  - `npm run build` completes without errors
  - `npm run start` runs the production build locally

- [ ] **TypeScript Configuration**
  - `tsconfig.json` is strict mode enabled
  - `npm run type-check` passes with zero errors
  - All component files are `.tsx`, all utilities are `.ts`

- [ ] **Tailwind CSS**
  - `tailwind.config.ts` is configured
  - Tailwind classes are available in components
  - `npm run build` includes Tailwind in the output

- [ ] **shadcn/ui Setup**
  - `components.json` exists with proper configuration
  - At least one shadcn component (e.g., Button) is installed and importable
  - Components can be rendered without errors

- [ ] **Linting & Formatting**
  - `.eslintrc.json` (or `.eslintrc.cjs`) is configured for Next.js + TypeScript
  - `.prettierrc.json` is configured
  - `npm run lint` passes with zero errors and warnings
  - `npm run lint --fix` can auto-fix formatting issues

- [ ] **Project Structure**
  - `app/` directory exists with Next.js App Router structure
  - `components/` directory exists for reusable React components
  - `lib/` directory exists for utilities
  - `public/` directory exists for static assets

- [ ] **GitHub Actions CI Ready**
  - `npm run lint`, `npm run type-check`, `npm run test` commands exist in `package.json`
  - `.github/workflows/ci.yml` template is in place (reference provided)
  - CI workflow would pass if triggered on a PR to `develop`

---

## 📝 Definition of Done

From `docs/Engineering/MVP_BACKLOG.md §5` (Common Definition of Done), this issue is **Done** when:

- [ ] Code implemented and matches ARCHITECTURE.md §Frontend Layer
- [ ] No new architecture/tables/endpoints introduced beyond documentation
- [ ] Unit/integration tests written and passing for the change
  - Minimum: Project builds without errors, type-checking passes
- [ ] Linting and type-checking pass with no new errors
  - Run: `npm run lint && npm run type-check`
- [ ] No hardcoded secrets; environment variables used per 02_MVP.md Security Requirements
- [ ] Code reviewed (self-review checklist if solo)
- [ ] Relevant documentation updated if behavior differs from source doc
  - Update: `docs/Engineering/DEVELOPMENT_QUICKSTART.md` with actual setup commands if they differ
- [ ] Merged to `develop` without breaking CI (GitHub Actions)
- [ ] **Issue-specific DoD**:
  - [ ] `npm run dev` starts cleanly and server is accessible at localhost:3000
  - [ ] `npm run build` completes in < 30 seconds
  - [ ] `npm run lint` produces zero errors
  - [ ] `npm run type-check` produces zero errors
  - [ ] Project structure matches organization in CODING_STANDARDS.md
  - [ ] At least one shadcn/ui component (Button) is installed and renders

---

## 🔧 How to Implement

### Step 1: Create Feature Branch (2 minutes)

```bash
# Ensure you're on develop
git checkout develop
git pull origin develop

# Create feature branch
git checkout -b feature/e1-1-scaffold-nextjs
```

### Step 2: Initialize Next.js Project (5 minutes)

```bash
# Use create-next-app with specific configuration
npx create-next-app@latest . --typescript --tailwind --app --eslint --skip-git

# When prompted:
# - Use TypeScript? → Yes
# - Use ESLint? → Yes
# - Use Tailwind CSS? → Yes
# - Use `src/` directory? → No
# - Use App Router? → Yes
# - Use Turbopack? → No (optional, for speed)
# - Customize import alias? → Yes (keep @/*)
```

### Step 3: Install shadcn/ui (3 minutes)

```bash
# Install shadcn/ui CLI
npx shadcn-ui@latest init

# When prompted:
# - Would you like to use TypeScript (recommended)? → Yes
# - Which style would you like to use? → Default (choose what you prefer)
# - Which color would you like as the base color? → Slate
# - Where is your global CSS file? → app/globals.css

# Install a base component to verify setup
npx shadcn-ui@latest add button
```

### Step 4: Verify Build (3 minutes)

```bash
# Type checking
npm run type-check
# Expected: No errors

# Linting
npm run lint
# Expected: Zero errors (warnings are OK)

# Build
npm run build
# Expected: Builds successfully in < 30 seconds

# Start dev server
npm run dev
# Expected: Server starts on http://localhost:3000
# Visit in browser and verify page loads
# Stop with Ctrl+C
```

### Step 5: Update Documentation (2 minutes)

Update `docs/Engineering/DEVELOPMENT_QUICKSTART.md` with actual commands that worked:

```bash
# If you used any different options or commands
git add docs/Engineering/DEVELOPMENT_QUICKSTART.md
git commit -m "docs: update setup guide with actual commands from E1-1 implementation"
```

### Step 6: Commit and Push (2 minutes)

```bash
# Stage all changes
git add .

# Commit with conventional commit message
git commit -m "feat(infra): scaffold Next.js + TypeScript + Tailwind + shadcn/ui

Initialize Next.js 14+ with App Router, TypeScript, Tailwind CSS, and
shadcn/ui component library. Includes ESLint and Prettier configuration.

- Next.js 14+ with App Router
- TypeScript with strict mode
- Tailwind CSS styling
- shadcn/ui components installed (Button example)
- ESLint + Prettier configuration
- npm run dev/build/lint/type-check working

Closes #[ISSUE_NUMBER]"

# Push to GitHub
git push origin feature/e1-1-scaffold-nextjs
```

---

## 📤 Creating Your First Pull Request

### On GitHub:

1. Go to: https://github.com/traemillercode/directory-os/pulls

2. Click **"New pull request"**

3. **Base branch**: `develop` (target)
   **Compare branch**: `feature/e1-1-scaffold-nextjs` (your work)

4. **Title**: `feat(infra): scaffold Next.js + TypeScript + Tailwind + shadcn/ui`

5. **Description** (template):
   ```markdown
   ## Issue
   Closes #[ISSUE_NUMBER]
   
   ## Description
   Scaffolds the Next.js foundation with TypeScript, Tailwind CSS, and shadcn/ui.
   
   ## Changes
   - Initialize Next.js 14+ App Router
   - Configure TypeScript (strict mode)
   - Add Tailwind CSS
   - Install shadcn/ui components
   - Configure ESLint + Prettier
   
   ## How to Test
   ```bash
   npm run dev      # Should start on localhost:3000
   npm run type-check  # Should pass
   npm run lint     # Should pass
   npm run build    # Should complete
   ```
   
   ## Checklist
   - [x] Code follows CODING_STANDARDS.md
   - [x] npm run lint passes
   - [x] npm run type-check passes
   - [x] npm run build succeeds
   - [x] No secrets committed
   ```

6. Click **"Create pull request"**

7. **Wait for CI**: GitHub Actions will automatically run.
   - Check should pass (lint, type-check, build tests)
   - If it fails, see "Troubleshooting" below

8. **Get approval** (if not solo, request review)
   - For solo development: self-review against acceptance criteria

9. **Merge**: Click **"Merge pull request"** when ready
   - Select "Squash and merge" (cleaner history)
   - Delete branch after merge

---

## 🚨 Troubleshooting

### Problem: `npm run lint` fails with ESLint errors

**Solution**:
```bash
# Auto-fix formatting issues
npm run lint -- --fix

# Re-run to verify
npm run lint
```

If errors persist, check:
- TypeScript config (`tsconfig.json`) is strict mode
- ESLint config includes Next.js plugin
- All files are `.tsx` or `.ts` (not `.jsx` or `.js`)

### Problem: `npm run build` fails

**Solution**:
```bash
# Check for type errors first
npm run type-check

# If type errors exist, fix them
# Then retry build
npm run build
```

Common causes:
- Implicit `any` types → use explicit types
- Missing imports → verify imports are correct
- Component props not typed → add interface

### Problem: `npm run dev` doesn't start

**Solution**:
```bash
# Clear cache
rm -rf .next

# Retry
npm run dev
```

### Problem: GitHub Actions CI fails

**Solution**:
1. Click the red ❌ on your PR
2. Click "Details" to see the error
3. Common causes:
   - Lint errors → Run `npm run lint --fix` locally, push again
   - Type errors → Run `npm run type-check` locally, fix, push again
   - Build failed → Run `npm run build` locally, verify it works

---

## 📚 Reference Files

If you need guidance, check these files:

- **Architecture**: `docs/Architecture/ARCHITECTURE.md` §Frontend Layer
- **Coding Standards**: `docs/Engineering/CODING_STANDARDS.md`
- **Development Workflow**: `docs/Engineering/DEVELOPMENT_WORKFLOW.md`
- **API Spec** (for reference): `docs/Architecture/API.md`
- **Next.js Docs**: https://nextjs.org/docs
- **TypeScript Docs**: https://www.typescriptlang.org/docs/
- **Tailwind CSS Docs**: https://tailwindcss.com/docs
- **shadcn/ui Docs**: https://ui.shadcn.com/

---

## ✅ After Merge: Next Steps

Once E1-1 is merged to `develop`:

1. **Verify on Staging**: The preview deployment should be live
   - Check GitHub PR for preview URL
   - Verify pages load and are styled correctly

2. **Move to Next Issue**: GitHub project board will auto-move E1-1 to "Done"
   - Next issue: **E1-2 — Provision Supabase project**
   - Read: `docs/Engineering/SPRINT_1_START.md` (updated for E1-2)

3. **Celebrate**: First issue shipped! 🎉

---

## ❓ Questions?

- **Issue details**: See GitHub issue #[NUMBER]
- **Architecture guidance**: See `docs/Architecture/ARCHITECTURE.md`
- **Coding conventions**: See `docs/Engineering/CODING_STANDARDS.md`
- **General help**: Comment on GitHub issue or ask CTO

**Total expected time**: 0.5–1 day (5-8 hours)

Good luck! 🚀
