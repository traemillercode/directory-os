# DirectoryOS Automation Philosophy

## Core Principles

1. **Automation for Leverage** - Automate repetitive tasks to free up human time for strategy
2. **Human-in-the-Loop** - Critical decisions always require human review
3. **Fail-Safe Defaults** - Automation defaults to safe, conservative behavior
4. **Transparency** - Users always see what automation did and can override
5. **Progressive Enhancement** - Features work without automation, but are enhanced by it

## GitHub Actions Automation

### CI/CD Pipeline

**Triggers**: Push, Pull Request, Manual
**Location**: `.github/workflows/`

#### 1. Lint & Format Check

```yaml
name: Lint & Format
on: [push, pull_request]
jobs:
  lint:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: '20'
      - run: npm ci
      - run: npm run lint
      - run: npm run format:check
```

#### 2. Type Checking

```yaml
name: TypeScript
on: [push, pull_request]
jobs:
  typecheck:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npm run type-check
```

#### 3. Testing

```yaml
name: Tests
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    services:
      postgres:
        image: postgres:15
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npm run test:unit
      - run: npm run test:integration
      - uses: codecov/codecov-action@v3
```

#### 4. Security Scanning

```yaml
name: Security
on: [push, pull_request]
jobs:
  security:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: github/super-linter@v4
      - uses: snyk/actions/node@master
```

#### 5. Deployment

**To Staging**: Automatic on merge to `develop`
**To Production**: Manual approval required

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
      - run: npm ci
      - run: npm run build
      - run: npx vercel deploy ${{ env.VERCEL_FLAGS }}
```

### Code Quality Automation

#### Dependabot

**Purpose**: Automatic dependency updates
**Config**: `.github/dependabot.yml`

```yaml
version: 2
updates:
  - package-ecosystem: npm
    directory: "/"
    schedule:
      interval: weekly
    auto-merge:
      enabled: true
      rules:
        - dependency-type: "minor"
        - dependency-type: "patch"
```

#### CodeQL

**Purpose**: Security vulnerability scanning
**Languages**: JavaScript/TypeScript, SQL

#### Renovate

**Purpose**: Automated dependency management
**Rules**:
- Auto-merge minor/patch updates
- Group updates by type
- Schedule: Tuesday mornings
- Require tests to pass before merge

## Development Automation

### Pre-commit Hooks

**Tool**: Husky + lint-staged
**Location**: `.husky/`

```bash
#!/bin/sh
. "$(dirname "$0")/_/husky.sh"

npm run lint-staged
```

**What it does**:
- ✅ Run ESLint on staged files
- ✅ Run Prettier to format code
- ✅ Check TypeScript types
- ✅ Verify commit message format

### Commit Message Automation

**Tool**: Commitizen
**Format**: Conventional Commits

```
<type>(<scope>): <subject>
<body>
<footer>
```

**Types**:
- `feat`: New feature
- `fix`: Bug fix
- `docs`: Documentation
- `style`: Code formatting
- `refactor`: Code refactoring
- `test`: Test changes
- `chore`: Maintenance

**Example**:
```
feat(listings): add bulk import for CSV files

Implement CSV parsing and validation with error reporting.

Closes #123
```

## Database Automation

### Schema Migrations

**Tool**: Prisma Migrations
**Automatic on Deploy**: Yes

```bash
# Create migration
npx prisma migrate dev --name add_listings_table

# Apply in production
npx prisma migrate deploy
```

### Backup Automation

**Tool**: Supabase automated backups
**Frequency**: Daily
**Retention**: 30 days
**Encryption**: AES-256

## Monitoring & Alerting Automation

### Error Tracking

**Tool**: Sentry
**Alert Channels**: Email, Slack
**Rules**:
- Alert on first exception
- Group similar errors
- Alert on error spike (>10x)
- Daily digest of top errors

### Performance Monitoring

**Tool**: Vercel Analytics + Custom
**Metrics**:
- API response time (>100ms triggers warning)
- Database query time (>50ms triggers warning)
- Page load time (>1s triggers warning)
- Error rate (>0.1% triggers alert)

### Uptime Monitoring

**Tool**: External service (e.g., Uptime Robot)
**Check Interval**: Every 5 minutes
**Alert On**: Down for >5 minutes
**Notification**: Email, SMS, Slack

## Content Automation

### AI-Powered Description Generation

**Trigger**: Manual user action (one-click)
**Flow**:
1. User clicks "Generate Description"
2. System calls Claude API with:
   - Listing category
   - Current description (if exists)
   - Best practices for category
3. AI generates new description
4. User reviews (human approval required)
5. User saves to publish

### SEO Analysis

**Trigger**: Automatic on listing update
**Flow**:
1. User edits listing
2. System analyzes:
   - Title length (50-60 chars optimal)
   - Meta description (150-160 chars optimal)
   - Keyword density
   - Readability score
3. Display suggestions (no automatic changes)
4. User can apply suggestions manually

### Image Optimization

**Trigger**: Automatic on upload
**Optimizations**:
- Resize to web-friendly dimensions
- Convert to WebP format
- Compress (lossy for photos, lossless for graphics)
- Generate thumbnails
- Strip EXIF data (privacy)

## Notification Automation

### Email Notifications

**Tool**: Resend
**Triggers**:
- New lead captured (immediate)
- Daily digest (morning at 8 AM)
- Weekly summary (Monday 9 AM)
- Monthly report (1st of month)
- Action required (high priority, immediate)

**Template Variables**: Personalized with user data, organization, listings

### In-App Notifications

**Triggers**:
- New lead received (bell icon, badge)
- Listing published successfully
- Error occurred
- Action requires attention

## Workflow Automation with n8n (Phase 2+)

### Pre-built Workflows

#### 1. Social Media Auto-Post

**Trigger**: New listing published
**Steps**:
1. Get listing details
2. Generate social copy (Claude)
3. Generate hashtags (Claude)
4. Post to:
   - Instagram
   - Facebook
   - LinkedIn
   - Twitter
5. Log results

#### 2. Lead Capture & CRM Sync

**Trigger**: New lead from form
**Steps**:
1. Validate lead data
2. Score lead quality (Claude)
3. Send confirmation email to lead
4. Create/update in Salesforce
5. Notify team (Slack/Email)
6. Log to analytics

#### 3. Weekly Report Generation

**Trigger**: Cron (Monday 9 AM)
**Steps**:
1. Calculate metrics
2. Generate PDF report
3. Send via email
4. Archive to storage

#### 4. Backup & Archival

**Trigger**: Cron (Daily 2 AM)
**Steps**:
1. Export organization data
2. Compress
3. Encrypt
4. Upload to S3
5. Log backup metadata

## AI Integration Automation

### Batch Processing

**Use Case**: Generate descriptions for 500 listings

**Flow**:
1. User initiates batch operation
2. Queue listings for processing
3. Process in batches of 10 (rate limiting)
4. Generate descriptions via Claude
5. Store versions (don't auto-apply)
6. Notify user when complete
7. User reviews and approves

**Safety**: Requires explicit human approval before applying changes

### Prompt Engineering Automation

**Approach**: Store category-specific prompts

```typescript
const prompts = {
  plumber: "You are a plumbing service marketing expert...",
  lawyer: "You are a legal service marketing expert...",
  restaurant: "You are a restaurant marketing expert...",
};
```

## Analytics Automation

### Event Tracking

**Tool**: Supabase Analytics + GA4
**Automatic Events**:
- Page views
- Listing views
- Lead submissions
- User actions (create, edit, delete)
- API calls
- Errors

**No PII captured**: Privacy-first approach

### Report Generation

**Monthly Reports** (automated):
- Total listings and trends
- Lead volume and quality
- Top performing listings
- User activity
- Suggestions for improvement

## Testing Automation

### Unit Tests

**Tool**: Jest
**Coverage Target**: 90%+
**Run On**: Every push

### Integration Tests

**Tool**: Playwright
**Scenarios**:
- User signup and onboarding
- Creating and editing listings
- Importing CSV
- Capturing leads
- Viewing analytics

**Run On**: Pull requests, before deployment

### Performance Tests

**Tool**: Lighthouse CI
**Metrics**:
- Largest Contentful Paint
- First Input Delay
- Cumulative Layout Shift

**Thresholds**:
- Performance: >90
- Accessibility: >95
- Best Practices: >90
- SEO: >95

## Release Automation

### Version Bumping

**Tool**: Semantic Release
**Rules**:
- `fix:` → patch version (0.0.1)
- `feat:` → minor version (0.1.0)
- `BREAKING CHANGE:` → major version (1.0.0)

### Changelog Generation

**Automatic**: From conventional commits
**Published**: In GitHub releases and docs

### Deployment Pipeline

1. Merge to `develop` → Deploy to staging (automatic)
2. Create release PR (automatic)
3. Manual approval → Merge to `main`
4. Deploy to production (automatic)
5. Rollback available (manual, one-click)

## Operations Automation

### Cost Monitoring

**Tool**: Custom script
**Frequency**: Daily
**Alert**: Monthly costs exceed $5,000
**Action**: Review and optimize

### Security Scanning

**Tools**:
- Snyk (dependencies)
- GitHub Security Alerts
- Supabase security checks

**Frequency**: Continuous
**Auto-fix**: Low-risk patches only

## Documentation Automation

### API Documentation

**Tool**: OpenAPI + Swagger UI
**Auto-generated**: From code annotations
**Hosted**: `/api/docs`

### Code Documentation

**Tool**: TSDoc / JSDoc
**Coverage**: All public functions
**Validation**: Linted in CI

## Best Practices

✅ **DO**:
- Start with manual process, automate when proven
- Automate repetitive, error-prone tasks
- Test automation thoroughly before rollout
- Monitor automation for failures
- Document why automation exists
- Allow human override for all automation
- Log all automated actions for audit trail

❌ **DON'T**:
- Automate without understanding process first
- Automate complex business logic
- Skip monitoring and alerting
- Create black-box automation
- Force users into automated workflows
- Automate without fallback plan
- Ignore automation failures
