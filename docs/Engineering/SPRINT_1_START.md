# Sprint 1 Kickoff Guide - Issue E1-1

**Sprint**: Sprint 1 (Foundation & Auth)  
**Milestone**: M1 - Foundation & Auth  
**Epic**: E1 - Project Initialization & Setup  
**Lead Issue**: E1-1 - Scaffold Next.js Application  
**Start Date**: Day 1 of Sprint 1  

---

## 🏃 What You're Building

**E1-1: Scaffold Next.js Application**

Set up a production-ready Next.js 14 application with TypeScript, ESLint, Prettier, and the foundation for the Directory-OS project.

### Success Criteria

When complete, you'll have:
- ✅ Next.js 14 app running locally on `http://localhost:3000`
- ✅ TypeScript configured with strict mode
- ✅ ESLint & Prettier set up and working
- ✅ Basic project structure for features
- ✅ Welcome page displaying correctly
- ✅ All tests passing
- ✅ README updated with setup instructions

---

## 📋 Step-by-Step Implementation

### Phase 1: Initialize Next.js (30 min)

#### Step 1.1: Create Next.js Project

```bash
# Navigate to project root
cd directory-os

# Remove any existing src directory or start fresh
rm -rf src/

# Create Next.js app
npx create-next-app@14 . --typescript --tailwind --eslint --no-git
```

**When prompted, choose:**
- ✅ TypeScript: **Yes**
- ✅ ESLint: **Yes**
- ✅ Tailwind CSS: **Yes**
- ✅ `src/` directory: **Yes** (recommended)
- ✅ App Router: **Yes** (use app directory)
- ✅ Default import alias: **@/** (accept default)

#### Step 1.2: Verify Installation

```bash
# Install dependencies
npm install

# Start dev server
npm run dev

# Check: Navigate to http://localhost:3000
# You should see the Next.js welcome page
```

**Expected Output**:
```
▲ Next.js 14.0.0
- Local: http://localhost:3000
```

✅ **Checkpoint**: Project running and accessible

---

### Phase 2: Configure TypeScript & Linting (30 min)

#### Step 2.1: Verify TypeScript Configuration

Check `tsconfig.json`:

```json
{
  "compilerOptions": {
    "strict": true,
    "noImplicitAny": true,
    "strictNullChecks": true,
    "strictFunctionTypes": true,
    "esModuleInterop": true,
    "skipLibCheck": true,
    "forceConsistentCasingInFileNames": true,
    "moduleResolution": "node",
    "resolveJsonModule": true,
    "isolatedModules": true,
    "jsx": "preserve",
    "incremental": true,
    "lib": ["ES2020", "dom", "dom.iterable"],
    "module": "esnext",
    "target": "es2020",
    "paths": {
      "@/*": ["./src/*"]
    }
  },
  "include": ["next-env.d.ts", "**/*.ts", "**/*.tsx"],
  "exclude": ["node_modules"]
}
```

#### Step 2.2: Configure ESLint

Verify `.eslintrc.json`:

```json
{
  "extends": ["next/core-web-vitals"],
  "rules": {
    "no-console": ["warn", { "allow": ["warn", "error"] }],
    "@next/next/no-html-link-for-pages": "off"
  }
}
```

#### Step 2.3: Set Up Prettier

Create `.prettierrc`:

```json
{
  "semi": true,
  "singleQuote": true,
  "tabWidth": 2,
  "trailingComma": "es5",
  "printWidth": 100,
  "bracketSpacing": true,
  "arrowParens": "always"
}
```

Create `.prettierignore`:

```
node_modules
.next
.git
dist
build
*.md
```

#### Step 2.4: Update `package.json` Scripts

Add to `package.json`:

```json
"scripts": {
  "dev": "next dev",
  "build": "next build",
  "start": "next start",
  "lint": "eslint src/ --max-warnings=0",
  "lint:fix": "eslint src/ --fix",
  "format": "prettier --write src/",
  "type-check": "tsc --noEmit",
  "test": "jest",
  "test:watch": "jest --watch"
}
```

#### Step 2.5: Test Linting & Formatting

```bash
# Run type check
npm run type-check

# Run ESLint
npm run lint

# Format code
npm run format
```

✅ **Checkpoint**: TypeScript strict mode working, ESLint passing

---

### Phase 3: Project Structure (20 min)

#### Step 3.1: Create Folder Structure

```bash
mkdir -p src/app
mkdir -p src/components
mkdir -p src/lib
mkdir -p src/types
mkdir -p src/utils
mkdir -p tests
```

#### Step 3.2: Verify Files

Should have these key files:

```
src/
├── app/
│   ├── layout.tsx
│   ├── page.tsx
│   └── globals.css
├── components/
├── lib/
├── types/
├── utils/
and folders created above
```

✅ **Checkpoint**: Folder structure created

---

### Phase 4: Create Welcome Page (20 min)

#### Step 4.1: Update `src/app/page.tsx`

```typescript
'use client';

export default function Home() {
  return (
    <main className="flex min-h-screen flex-col items-center justify-center bg-gradient-to-b from-blue-50 to-white p-4">
      <div className="text-center space-y-6">
        <h1 className="text-6xl font-bold text-blue-900">Directory-OS</h1>
        <p className="text-xl text-gray-700">
          A modern, scalable directory service platform
        </p>
        <div className="space-y-2">
          <p className="text-sm text-gray-600">Sprint 1 - Scaffold Complete ✅</p>
          <p className="text-sm text-gray-600">Status: Development</p>
        </div>
      </div>
    </main>
  );
}
```

#### Step 4.2: Verify Page Displays

```bash
# If dev server not running:
npm run dev

# Check: http://localhost:3000
# Should see Directory-OS welcome page
```

✅ **Checkpoint**: Welcome page displaying correctly

---

### Phase 5: Testing Setup (20 min)

#### Step 5.1: Install Jest & Testing Library

```bash
npm install --save-dev jest @testing-library/react @testing-library/jest-dom @types/jest
```

#### Step 5.2: Create `jest.config.ts`

```typescript
import type { Config } from 'jest';
import nextJest from 'next/jest.js';

const createJestConfig = nextJest({
  dir: './',
});

const config: Config = {
  coverageProvider: 'v8',
  testEnvironment: 'jsdom',
  setupFilesAfterEnv: ['<rootDir>/jest.setup.ts'],
  moduleNameMapper: {
    '^@/(.*)$': '<rootDir>/src/$1',
  },
};

export default createJestConfig(config);
```

#### Step 5.3: Create `jest.setup.ts`

```typescript
import '@testing-library/jest-dom';
```

#### Step 5.4: Create Sample Test

Create `tests/home.test.tsx`:

```typescript
import { render, screen } from '@testing-library/react';
import Home from '@/app/page';

describe('Home Page', () => {
  it('renders Directory-OS title', () => {
    render(<Home />);
    const title = screen.getByText('Directory-OS');
    expect(title).toBeInTheDocument();
  });

  it('displays welcome message', () => {
    render(<Home />);
    const message = screen.getByText(/modern, scalable directory service/i);
    expect(message).toBeInTheDocument();
  });
});
```

#### Step 5.5: Run Tests

```bash
# Run tests
npm test

# Expected output:
# PASS  tests/home.test.tsx
# ✓ renders Directory-OS title
# ✓ displays welcome message
```

✅ **Checkpoint**: Tests passing

---

### Phase 6: Production Build & Verification (15 min)

#### Step 6.1: Build for Production

```bash
npm run build

# Expected output:
# ▲ Next.js 14.0.0
# Compiled successfully
```

#### Step 6.2: Start Production Server

```bash
npm run start

# Expected output:
# ▲ Next.js 14.0.0
# - Local: http://localhost:3000

# Check: http://localhost:3000 still works
```

✅ **Checkpoint**: Production build successful

---

### Phase 7: Documentation & README (15 min)

#### Step 7.1: Update Root `README.md`

```markdown
# Directory-OS

A modern, scalable directory service platform for enterprise user management.

## Quick Start

### Prerequisites

- Node.js 18+
- npm or yarn
- PostgreSQL (local or Supabase)

### Development Setup

```bash
# 1. Clone and navigate
git clone https://github.com/traemillercode/directory-os.git
cd directory-os

# 2. Install dependencies
npm install

# 3. Set up environment
cp .env.example .env.local
# Edit .env.local with your database credentials

# 4. Start dev server
npm run dev
```

### Available Scripts

```bash
npm run dev          # Start development server
npm run build        # Build for production
npm run start        # Start production server
npm run lint         # Run ESLint
npm run format       # Format with Prettier
npm test             # Run tests
npm run type-check   # TypeScript check
```

### Project Structure

```
src/
├── app/              # Next.js app router pages
├── components/       # Reusable React components
├── lib/              # Utility functions and helpers
├── types/            # TypeScript type definitions
└── utils/            # Helper functions
```

## Documentation

- [Development Quick Start](docs/Engineering/DEVELOPMENT_QUICKSTART.md)
- [Architecture](docs/Architecture/)
- [API Reference](docs/API/)
- [Contributing](CONTRIBUTING.md)

## License

MIT
```

#### Step 7.2: Create `.env.example`

```env
# Database
DATABASE_URL=postgresql://user:password@localhost:5432/directory_os_dev

# Next.js
NEXT_PUBLIC_API_URL=http://localhost:3000/api

# Supabase (if using)
NEXT_PUBLIC_SUPABASE_URL=your_supabase_url
NEXT_PUBLIC_SUPABASE_ANON_KEY=your_anon_key
```

✅ **Checkpoint**: Documentation updated

---

## 🔗 Creating & Submitting the Pull Request

### Step 1: Create Feature Branch

```bash
git checkout -b feature/E1-1-scaffold-nextjs
```

### Step 2: Commit Changes

```bash
# Stage all changes
git add .

# Commit with descriptive message
git commit -m "feat: E1-1 - Scaffold Next.js 14 application with TypeScript

- Initialize Next.js 14 project with TypeScript
- Configure ESLint and Prettier
- Set up project folder structure
- Create welcome page with Tailwind CSS styling
- Configure Jest and React Testing Library
- Add development and production build scripts
- Update README with setup instructions

Closes #E1-1"
```

### Step 3: Push to GitHub

```bash
git push origin feature/E1-1-scaffold-nextjs
```

### Step 4: Create Pull Request

On GitHub:

1. Go to **Pull requests** tab
2. Click **New pull request**
3. Base: `main`, Compare: `feature/E1-1-scaffold-nextjs`
4. Fill PR template:

```markdown
## Description

Scaffold Next.js 14 application with TypeScript, ESLint, and Prettier configuration.

## Type of Change

- [x] New feature
- [ ] Bug fix
- [ ] Breaking change

## Related Issues

Closes #E1-1

## How Has This Been Tested?

- [x] Dev server runs: `npm run dev`
- [x] Build succeeds: `npm run build`
- [x] Tests pass: `npm test`
- [x] Linting passes: `npm run lint`
- [x] TypeScript check passes: `npm run type-check`

## Checklist

- [x] Code follows project style guidelines
- [x] Self-review completed
- [x] Comments added for complex sections
- [x] README updated
- [x] Tests added/updated
- [x] No new warnings generated
```

5. Click **Create pull request**
6. Request review from Tech Lead
7. Address any feedback
8. Once approved, click **Squash and merge**

✅ **Checkpoint**: PR submitted and approved

---

## ✅ Acceptance Criteria Checklist

Before submitting PR, verify:

- [ ] **Next.js 14 installed** with TypeScript
- [ ] **dev server runs** on `http://localhost:3000`
- [ ] **Welcome page displays** correctly
- [ ] **ESLint passes** with no errors
- [ ] **Prettier formatting** applied
- [ ] **TypeScript strict mode** enabled
- [ ] **Production build** succeeds
- [ ] **Tests pass** (sample test created)
- [ ] **README updated** with setup instructions
- [ ] **`.env.example`** created with key vars
- [ ] **All scripts work**: `dev`, `build`, `start`, `lint`, `format`, `test`
- [ ] **No console warnings** in dev server

---

## 🐛 Troubleshooting

### Issue: Port 3000 already in use

```bash
# Find and kill process on port 3000
lsof -ti:3000 | xargs kill -9

# Or use different port
PORT=3001 npm run dev
```

### Issue: Module not found errors

```bash
# Clear node_modules and reinstall
rm -rf node_modules package-lock.json
npm install
npm run type-check
```

### Issue: ESLint errors

```bash
# Auto-fix common issues
npm run lint:fix

# Check remaining errors
npm run lint
```

### Issue: Tests failing

```bash
# Run tests with verbose output
npm test -- --verbose

# Watch mode for debugging
npm run test:watch
```

### Issue: TypeScript errors

```bash
# Run type check
npm run type-check

# Check tsconfig.json configuration
cat tsconfig.json
```

---

## 📞 Getting Help

- **Check**: [DEVELOPMENT_QUICKSTART.md](DEVELOPMENT_QUICKSTART.md)
- **Docs**: [Next.js Documentation](https://nextjs.org/docs)
- **Issues**: Post in GitHub issues with `help-wanted` label
- **Slack**: Tag @tech-lead in #engineering channel

---

**Sprint 1 E1-1 is ready for implementation! 🚀**
