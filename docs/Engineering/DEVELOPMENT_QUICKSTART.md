# Development Environment Quickstart

**Target Time**: < 30 minutes from clone to first successful test run

---

## Prerequisites

### Required Software

- **Node.js**: v18.0.0 or higher
  ```bash
  node --version  # Verify: v18.x.x or v20.x.x
  ```

- **npm**: v9.0.0 or higher (comes with Node.js)
  ```bash
  npm --version  # Verify: v9.x.x or v10.x.x
  ```

- **Git**: v2.40.0 or higher
  ```bash
  git --version  # Verify: git version 2.x.x
  ```

- **Code Editor**: VS Code, Cursor, WebStorm, or similar with TypeScript support

---

## Step 1: Clone Repository (2 minutes)

```bash
# Clone the repository
git clone https://github.com/traemillercode/directory-os.git
cd directory-os

# Verify directory structure
ls -la
# Expected: docs/, .github/, package.json, tsconfig.json, etc.
```

---

## Step 2: Install Dependencies (3 minutes)

```bash
# Install npm packages
npm install

# Verify installation
npm ls --depth=0
# Expected: next, react, typescript, tailwindcss, eslint, prettier, etc.
```

---

## Step 3: Set Up Environment Variables (2 minutes)

### Local Development Setup

```bash
# Copy example env file
cp .env.example .env.local

# View required variables
cat .env.local
```

### Local Database Options

Choose one: **Option A (Recommended for Phase 1)** or **Option B (Simpler, requires Docker)**

#### Option A: Supabase Local (Recommended)

```bash
# Install Supabase CLI globally
npm install -g supabase

# Initialize Supabase (if not already done)
supabase init

# Start local Supabase instance
supabase start

# This will start:
# - PostgreSQL database
# - Supabase Auth
# - Supabase Storage
# - Supabase Realtime

# Copy connection string to .env.local
# The CLI will output something like:
# postgresql://postgres:postgres@127.0.0.1:54322/postgres

# Update .env.local:
echo "DATABASE_URL=postgresql://postgres:postgres@127.0.0.1:54322/postgres" >> .env.local
```

#### Option B: Docker PostgreSQL (Simpler)

```bash
# Install Docker: https://docs.docker.com/get-docker/

# Start PostgreSQL container
docker run --name postgres-local \
  -e POSTGRES_PASSWORD=password \
  -e POSTGRES_DB=directoryos \
  -p 5432:5432 \
  -d postgres:15

# Update .env.local:
echo "DATABASE_URL=postgresql://postgres:password@localhost:5432/directoryos" >> .env.local
```

### Verify Database Connection

```bash
# Test connection (after Supabase or Docker is running)
psql $DATABASE_URL -c "SELECT 1;"
# Expected output: should return 1
```

---

## Step 4: Initialize Database Schema (3 minutes)

```bash
# Run Prisma migrations
npx prisma migrate dev --name init

# This will:
# 1. Create tables (organizations, users, listings, etc.)
# 2. Enable Row-Level Security policies
# 3. Generate Prisma client

# Verify schema
npx prisma db push
# Expected: No migrations needed (already applied)
```

---

## Step 5: Seed Database (2 minutes)

```bash
# Run seed script (if available)
npm run prisma:seed 2>/dev/null || echo "Seed script not yet created"

# Verify data
npx prisma studio
# Opens http://localhost:5555 with database GUI
# View: organizations, users, categories tables
# Stop with Ctrl+C
```

---

## Step 6: Verify Build Tools (3 minutes)

### Type Checking

```bash
npm run type-check
# Expected: No errors
```

### Linting

```bash
npm run lint
# Expected: No errors (warnings are OK)

# Auto-fix formatting issues
npm run lint -- --fix
```

### Build

```bash
npm run build
# Expected: Compiles successfully in < 30 seconds
```

### Tests

```bash
# Unit tests
npm run test
# Expected: Pass or no tests yet (if not implemented)

# Integration tests (requires database)
npm run test:integration
# Expected: Pass or no tests yet
```

---

## Step 7: Start Development Server (2 minutes)

```bash
# Start the development server
npm run dev

# Expected output:
# > next dev
# ▲ Next.js 14.x
# - Local:        http://localhost:3000
# - Environments: .env.local

# Open browser: http://localhost:3000
# Expected: Page loads (may be blank if not yet implemented)

# Stop server: Press Ctrl+C
```

---

## Verification Checklist

After completing all steps, verify:

- [ ] `npm run type-check` passes with zero errors
- [ ] `npm run lint` passes with zero errors
- [ ] `npm run build` completes successfully
- [ ] `npm run dev` starts server on localhost:3000
- [ ] `DATABASE_URL` is set in `.env.local`
- [ ] Database connection works: `psql $DATABASE_URL -c "SELECT 1;"`
- [ ] Prisma schema is initialized: tables visible in `npx prisma studio`
- [ ] Git is configured: `git config user.name` and `git config user.email` are set

---

## 🚨 Troubleshooting

### "npm: command not found"

**Solution**: Install Node.js from https://nodejs.org/  
Verify: `npm --version`

### "DATABASE_URL not recognized"

**Solution**:
```bash
# Ensure .env.local exists
ls .env.local

# Ensure DATABASE_URL is in the file
grep DATABASE_URL .env.local

# Reload shell (or restart code editor)
```

### "connection refused" when running migrations

**Solution**:

**If using Supabase**:
```bash
# Verify Supabase is running
supabase status

# If not running, start it
supabase start
```

**If using Docker**:
```bash
# Verify container is running
docker ps | grep postgres

# If not running, start it
docker start postgres-local
```

### "npm run build" fails with type errors

**Solution**:
```bash
# Check type errors
npm run type-check

# View full error messages
# Fix errors in source files
# Retry build
npm run build
```

### "Cannot find module" errors

**Solution**:
```bash
# Reinstall dependencies
rm -rf node_modules package-lock.json
npm install
```

### "Port 3000 already in use"

**Solution**:
```bash
# Kill process using port 3000
lsof -i :3000
# Note the PID, then kill it:
kill -9 [PID]

# Or use a different port
PORT=3001 npm run dev
```

---

## 📂 Project Structure

After setup, your directory should look like:

```
directory-os/
├── app/                      # Next.js App Router pages
├── components/               # React components
├── lib/                      # Utilities and helpers
├── public/                   # Static assets
├── prisma/
│   ├── schema.prisma        # Database schema
│   ├── migrations/          # Migration files
│   └── seed.ts              # Seed script
├── .env.local               # Local env vars (gitignored)
├── .env.example             # Template for env vars
├── .eslintrc.json           # ESLint configuration
├── .prettierrc.json         # Prettier configuration
├── tsconfig.json            # TypeScript configuration
├── tailwind.config.ts       # Tailwind CSS configuration
├── next.config.js           # Next.js configuration
├── package.json             # Dependencies
└── docs/                    # Documentation
```

---

## 📚 Next Steps

1. **Read Sprint 1 kickoff**: `docs/Engineering/SPRINT_1_START.md`
2. **Start first issue**: E1-1 Scaffold Next.js (already done if you completed Step 6 above)
3. **Create first branch**: `git checkout -b feature/e1-1-scaffold-nextjs`
4. **Make changes**: Follow issue acceptance criteria
5. **Push and create PR**: `git push origin feature/e1-1-scaffold-nextjs`
6. **Get feedback**: Wait for CI and code review
7. **Merge**: Once approved, merge to `develop`

---

## 🎯 Performance Targets

After setup, verify performance benchmarks:

| Task | Target | How to Test |
|------|--------|-------------|
| Build | < 30 seconds | `time npm run build` |
| Type check | < 10 seconds | `time npm run type-check` |
| Lint | < 15 seconds | `time npm run lint` |
| Dev server startup | < 5 seconds | `npm run dev` |
| Page load (localhost) | < 1 second | Browser DevTools Performance tab |

---

## 🔗 Resources

- **Next.js Documentation**: https://nextjs.org/docs
- **TypeScript Handbook**: https://www.typescriptlang.org/docs/
- **Tailwind CSS Docs**: https://tailwindcss.com/docs
- **shadcn/ui Documentation**: https://ui.shadcn.com/
- **Prisma Documentation**: https://www.prisma.io/docs/
- **Supabase Documentation**: https://supabase.com/docs
- **Git Documentation**: https://git-scm.com/doc

---

## ❓ Still Stuck?

1. **Check GitHub Issues**: Search for similar problems
2. **Read DEVELOPMENT_WORKFLOW.md**: More detailed development guidance
3. **Ask in issue comments**: Comment on the relevant GitHub issue
4. **CTO support**: Reach out for architecture/dependency questions

**Success!** You're now ready to start building DirectoryOS. 🚀
