# Development Quick Start Guide

**Objective**: Get your development environment running in 30 minutes  
**Target**: macOS, Linux, or Windows (WSL2)  
**Updated**: 2026-07-12  

---

## ⏱️ Timeline

| Phase | Task | Duration |
|-------|------|----------|
| 1 | Clone & Install | 5 min |
| 2 | Database Setup | 10 min |
| 3 | Environment Config | 5 min |
| 4 | Verification | 5 min |
| 5 | Troubleshooting | As needed |
| **Total** | | **30 min** |

---

## 📋 Prerequisites

Before starting, ensure you have:

- [ ] **Node.js** 18+ ([Download](https://nodejs.org/))
- [ ] **npm** 9+ (included with Node.js)
- [ ] **Git** ([Download](https://git-scm.com/))
- [ ] **PostgreSQL** (local or Supabase account)
- [ ] **Text Editor** (VS Code recommended)

### Verify Installations

```bash
# Check versions
node --version    # Should be v18+
npm --version     # Should be 9+
git --version     # Any recent version
```

---

## 🚀 Quick Start (5 minutes)

### Step 1: Clone Repository

```bash
# Clone the repo
git clone https://github.com/traemillercode/directory-os.git

# Navigate to project
cd directory-os
```

### Step 2: Install Dependencies

```bash
# Install npm packages
npm install

# Verify successful installation
npm list --depth=0
```

### Step 3: Set Up Database

**Choose ONE option below:**

#### Option A: Use Supabase (Cloud) - Recommended for Quick Start

```bash
# 1. Go to https://supabase.com and create account
# 2. Create new project
# 3. Go to Project Settings → API → Copy:
#    - Project URL
#    - Service Role Key (anon_key)

# 4. Create .env.local
cp .env.example .env.local

# 5. Edit .env.local and add:
# DATABASE_URL=postgresql://[user]:[password]@[host]:[port]/postgres
# NEXT_PUBLIC_SUPABASE_URL=[your_project_url]
# NEXT_PUBLIC_SUPABASE_ANON_KEY=[your_anon_key]
```

#### Option B: Use Local PostgreSQL

```bash
# macOS - Install via Homebrew
brew install postgresql
brew services start postgresql

# Linux (Ubuntu/Debian)
sudo apt-get update
sudo apt-get install postgresql postgresql-contrib
sudo systemctl start postgresql

# Windows - Download installer
# https://www.postgresql.org/download/windows/

# Create database
psql postgres -c "CREATE DATABASE directory_os_dev;"
psql postgres -c "CREATE USER dev WITH PASSWORD 'dev_password';"
psql postgres -c "ALTER ROLE dev WITH CREATEDB;"
GRANT ALL PRIVILEGES ON DATABASE directory_os_dev TO dev;"

# Create .env.local
cp .env.example .env.local

# Edit .env.local:
# DATABASE_URL=postgresql://dev:dev_password@localhost:5432/directory_os_dev
```

### Step 4: Verify Installation

```bash
# Run all verification checks
npm run type-check
npm run lint
npm test

# Start development server
npm run dev
```

✅ **You're done!** Open http://localhost:3000

---

## 📚 Database Setup Details (10 minutes)

### Supabase Setup (Recommended)

#### Sign Up & Create Project

1. Go to [https://supabase.com](https://supabase.com)
2. Click "Start your project"
3. Sign up with GitHub (or email)
4. Create new organization
5. Create new project:
   - Name: `directory-os-dev`
   - Region: Closest to you
   - Password: Store securely

#### Get Connection String

1. Go to **Project Settings** (gear icon)
2. Click **Database** tab
3. Under **Connection Strings**, select **URI**
4. Copy the connection string
5. Paste into `.env.local` as `DATABASE_URL`

#### Verify Connection

```bash
# Test connection
psql $DATABASE_URL -c "SELECT NOW();"

# Should return current timestamp
# If successful: connection is working
```

### Local PostgreSQL Setup

#### macOS Setup

```bash
# Install PostgreSQL
brew install postgresql

# Start service
brew services start postgresql

# Verify
psql --version
```

#### Linux Setup (Ubuntu)

```bash
# Update packages
sudo apt-get update

# Install PostgreSQL
sudo apt-get install postgresql postgresql-contrib

# Start service
sudo systemctl start postgresql

# Verify
psql --version
```

#### Windows Setup

1. Download installer: [PostgreSQL Windows](https://www.postgresql.org/download/windows/)
2. Run installer
3. Remember password (needed for `.env.local`)
4. Verify: Open PowerShell and run `psql --version`

#### Create Development Database

```bash
# Connect to PostgreSQL
psql postgres

# Create database
CREATE DATABASE directory_os_dev;

# Create user
CREATE USER dev WITH PASSWORD 'dev_password';

# Grant privileges
ALTER ROLE dev WITH CREATEDB;
GRANT ALL PRIVILEGES ON DATABASE directory_os_dev TO dev;

# Verify
\l  # List databases
\du # List users

# Exit
\q
```

#### Set Connection String

```bash
# Edit .env.local
echo "DATABASE_URL=postgresql://dev:dev_password@localhost:5432/directory_os_dev" >> .env.local
```

---

## ⚙️ Environment Configuration (5 minutes)

### Create `.env.local`

```bash
cp .env.example .env.local
```

### Edit `.env.local`

**For Supabase:**

```env
# Supabase Connection
DATABASE_URL=postgresql://postgres:[password]@[id].supabase.co:5432/postgres
NEXT_PUBLIC_SUPABASE_URL=https://[id].supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=[anon_key]

# API Configuration
NEXT_PUBLIC_API_URL=http://localhost:3000/api

# Environment
NODE_ENV=development
```

**For Local PostgreSQL:**

```env
# Local PostgreSQL
DATABASE_URL=postgresql://dev:dev_password@localhost:5432/directory_os_dev

# API Configuration
NEXT_PUBLIC_API_URL=http://localhost:3000/api

# Environment
NODE_ENV=development
```

### Verify Variables

```bash
# Ensure .env.local exists
ls -la .env.local

# Check variables are set (don't print values)
grep DATABASE_URL .env.local
```

---

## ✅ Verification Checklist (5 minutes)

### Run All Checks

```bash
# 1. TypeScript check
echo "[1/5] Running TypeScript check..."
npm run type-check

# Expected: No errors, "Successfully compiled..."

# 2. Linting
echo "[2/5] Running ESLint..."
npm run lint

# Expected: No errors or warnings

# 3. Unit tests
echo "[3/5] Running tests..."
npm test -- --passWithNoTests

# Expected: "PASS" status

# 4. Build
echo "[4/5] Building for production..."
npm run build

# Expected: "compiled successfully"

# 5. Development server
echo "[5/5] Starting dev server..."
npm run dev

# Expected: Server running on http://localhost:3000
```

### Browser Check

1. Open http://localhost:3000
2. Should see Directory-OS welcome page
3. Check browser console: No red errors

✅ **All checks passing? Setup complete!**

---

## 🐛 Troubleshooting

### Issue: "Cannot find module 'next'"

**Solution:**

```bash
# Reinstall dependencies
rm -rf node_modules package-lock.json
npm install
```

### Issue: Database connection failed

**Solution:**

```bash
# Verify connection string
grep DATABASE_URL .env.local

# Test connection manually
psql $DATABASE_URL -c "SELECT NOW();"

# If using Supabase, verify:
# - Project is active on Supabase dashboard
# - Connection string is correct (check special chars)
# - If using local: PostgreSQL service is running
#   - macOS: brew services list | grep postgresql
#   - Linux: sudo systemctl status postgresql
#   - Windows: Services app → PostgreSQL
```

### Issue: Port 3000 already in use

**Solution:**

```bash
# Find process using port 3000
lsof -i :3000

# Kill process
kill -9 <PID>

# Or use different port
PORT=3001 npm run dev
```

### Issue: npm install hangs

**Solution:**

```bash
# Clear npm cache
npm cache clean --force

# Retry with verbose output
npm install --verbose

# If still failing, try yarn
curl https://raw.githubusercontent.com/yarnpkg/yarn/master/scripts/install-yarn.sh | bash
yarn install
```

### Issue: TypeScript errors on startup

**Solution:**

```bash
# Regenerate TypeScript cache
rm -rf .next
npm run type-check

# If errors persist, check tsconfig.json
cat tsconfig.json
```

### Issue: Tests won't run

**Solution:**

```bash
# Verify Jest is installed
npm list jest

# Ensure jest.config.ts exists
ls jest.config.ts jest.setup.ts

# Run with verbose output
npm test -- --verbose

# Check test file exists
ls tests/*.test.tsx
```

---

## 📖 Common Commands

### Development

```bash
npm run dev         # Start dev server (http://localhost:3000)
npm run type-check  # Check TypeScript
npm run lint        # Run ESLint
npm run lint:fix    # Fix ESLint issues
npm run format      # Format with Prettier
```

### Testing

```bash
npm test            # Run tests once
npm run test:watch  # Watch mode
npm test -- --cov   # With coverage
```

### Production

```bash
npm run build       # Build for production
npm run start       # Start production server
```

---

## 🔗 Useful Links

- **Next.js Docs**: https://nextjs.org/docs
- **TypeScript Docs**: https://www.typescriptlang.org/docs/
- **Supabase Docs**: https://supabase.com/docs
- **PostgreSQL Docs**: https://www.postgresql.org/docs/
- **ESLint Rules**: https://eslint.org/docs/rules/
- **Jest Docs**: https://jestjs.io/docs/getting-started

---

## ✨ Next Steps

After setup completes:

1. ✅ Create a feature branch: `git checkout -b feature/your-task`
2. ✅ Read [SPRINT_1_START.md](SPRINT_1_START.md)
3. ✅ Pick first issue to work on
4. ✅ Create a pull request when done

---

**Setup complete! Ready to start coding? 🚀**
