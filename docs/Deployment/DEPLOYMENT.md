# DirectoryOS Deployment Guide

## Overview

DirectoryOS is deployed on Vercel (Next.js application + serverless functions) with Supabase providing the managed PostgreSQL database, auth, real-time, and storage layers. GitHub Actions drives CI/CD, running quality gates before every deployment.

## Environments

### Production

- Live user data and traffic
- Full monitoring & alerting enabled
- Automated daily backups
- CDN caching enabled
- Deploys from the `main` branch
- Requires manual promotion / approval

### Staging

- Mirrors production infrastructure
- Weekly refresh of a production data copy
- Used for full QA and pre-release testing
- No production alerting
- Deploys automatically on merge to `develop`

### Development

- Local database or a Supabase development branch
- External integrations mocked
- Loose rate limiting
- Deploys via preview deployments per branch/PR

### Environment Variables

Each environment has its own set of secrets/config (Supabase URL & keys, Vercel project settings, third-party API keys). Secrets are managed in Vercel's environment variable dashboard and GitHub Actions secrets, never committed to source control.

## CI/CD

**Location**: `.github/workflows/`
**Triggers**: Push, Pull Request, Manual (`workflow_dispatch`)

Pipeline stages, run on every push/PR:

1. **Lint & Format** - ESLint, Prettier
2. **Type Checking** - TypeScript (`tsc --noEmit`)
3. **Testing** - Unit tests (Jest), integration tests (Playwright), coverage reporting
4. **Security Scanning** - CodeQL, dependency/secret scanning
5. **Build** - Production build validation
6. **Deploy** - Vercel deployment (staging or production depending on branch)

Pull requests require passing checks and at least one review before merge.

## GitHub Actions

Key workflows:

| Workflow | Trigger | Purpose |
|---|---|---|
| Lint & Format | push, pull_request | Enforce code style |
| TypeScript | push, pull_request | Type safety |
| Tests | push, pull_request | Unit + integration tests |
| Security | push, pull_request | CodeQL, Snyk, secret scanning |
| Deploy | push to `main`/`develop` | Deploy to production/staging |
| Dependabot | scheduled (weekly) | Dependency updates |

Example deploy workflow:

```yaml
name: Deploy
on:
  push:
    branches: [main, develop]
jobs:
  deploy:
    runs-on: ubuntu-latest
    environment:
      name: ${{ github.ref == 'refs/heads/main' && 'production' || 'staging' }}
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npm run build
      - run: npx vercel deploy ${{ env.VERCEL_FLAGS }}
```

- Merges to `develop` deploy to **staging** automatically.
- Merges to `main` deploy to **production**, gated by required checks and (for production) manual approval via a GitHub Environment protection rule.

## Vercel Deployment

- **Hosting**: Next.js frontend + serverless API functions
- **Preview deployments**: Automatically generated for every pull request, enabling review before merge
- **Production/staging deployments**: Triggered by pushes to `main`/`develop` via GitHub Actions (`vercel deploy`)
- **Environment variables**: Managed per-environment (Production, Preview, Development) in the Vercel dashboard
- **Zero-config**: Vercel auto-detects the Next.js build; no custom server config required
- **Rollback**: Prior deployments remain available; promote any previous deployment to production instantly from the Vercel dashboard or CLI (`vercel rollback`)
- **Analytics**: Vercel Analytics tracks performance and deployment history

## Supabase Migrations

- **Tool**: Prisma Migrations
- **Location**: `prisma/migrations/`
- **Execution**: Automatic on deployment (`npx prisma migrate deploy`)

Workflow:

```bash
# Create a new migration locally
npx prisma migrate dev --name add_listings_table

# Apply pending migrations in production/staging
npx prisma migrate deploy
```

Guidelines:

- All schema changes must be created as numbered migrations (never edited directly in Supabase).
- Migrations should include both up and down paths where feasible.
- Review migrations in pull requests before merge; run against a staging/dev branch first.
- Take a manual database backup before applying migrations tied to a major release.

## Rollback Strategy

1. **Application rollback**: Use the Vercel dashboard or `vercel rollback` to instantly re-promote the last known-good deployment (no rebuild required).
2. **Database rollback**: Prefer forward-fixing with a new migration. If a rollback is required, use the corresponding "down" migration or restore from a Supabase point-in-time recovery snapshot.
3. **Feature-level rollback**: Use feature flags/environment configuration to disable a problematic feature without a full deployment rollback, where available.
4. **Communication**: Document the rollback in the incident log, including cause, action taken, and follow-up.

## Monitoring

- **Error tracking**: Sentry (alerts on first exception, error spikes >10x, daily digest)
- **Performance monitoring**: Vercel Analytics + custom metrics
  - API response time (>100ms warning)
  - Database query time (>50ms warning)
  - Page load time (>1s warning)
  - Error rate (>0.1% alert)
- **Uptime monitoring**: External service (e.g., Uptime Robot), 5-minute checks, alert if down >5 minutes
- **Log aggregation**: Supabase logs
- **Security scanning**: GitHub secret scanning, CodeQL

### Monitoring Checklist

- ✅ Database performance (query times, disk usage)
- ✅ API availability and latency
- ✅ Error rates and stack traces
- ✅ Security events and access logs
- ✅ Cost tracking (Vercel, Supabase, third-party)

## Backups

- **Database**: Automatic daily backups via Supabase (AES-256 encrypted), 30-day retention
- **Point-in-time recovery**: Available up to 30 days
- **File storage**: Supabase Storage backups
- **Configuration**: Infrastructure/config tracked as code in this repository
- **Manual backups**: Taken before major deployments or schema migrations

## Disaster Recovery

### Failover Plan

- Multi-region database (planned, Phase 2+)
- Automatic failover for critical services (planned)
- **RTO** (Recovery Time Objective): < 1 hour
- **RPO** (Recovery Point Objective): < 15 minutes

### Recovery Steps

1. Identify scope of the incident (application, database, or infrastructure provider outage).
2. If application-level: roll back to the last known-good Vercel deployment.
3. If database-level: restore from the most recent Supabase backup or point-in-time recovery snapshot.
4. Validate data integrity and application health in staging before restoring production traffic.
5. Communicate status to stakeholders and document the incident post-mortem.

### Regular Tasks

- **Weekly**: Review monitoring dashboards
- **Monthly**: Security audit, cost analysis
- **Quarterly**: Performance optimization, capacity planning
- **Annually**: Compliance audit, disaster recovery test
