# DirectoryOS Architecture

## System Overview

DirectoryOS is built on a modern, scalable SaaS architecture optimized for performance, security, and maintainability. The system handles high concurrency, multi-tenancy, and real-time updates using battle-tested technologies.

## Architecture Diagram

```
┌─────────────────────────────────────────────────────────────┐
│                    Client Layer (Browser)                   │
│  Next.js (React) + TypeScript + Tailwind CSS + shadcn/ui   │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│                  Cloudflare CDN / Edge                       │
│            (Static assets, DDoS protection)                 │
└──────────────────────┬──────────────────────────────────────┘
                       │
                       ▼
┌─────────────────────────────────────────────────────────────┐
│              Vercel Serverless Functions                    │
│     (Next.js API Routes with automatic scaling)            │
│                                                             │
│  ┌──────────────────────────────────────────────────────┐  │
│  │ API Layer (REST endpoints, GraphQL, Webhooks)       │  │
│  │ - Authentication & Authorization                    │  │
│  │ - Business Logic & Validation                       │  │
│  │ - Rate Limiting & CORS                              │  │
│  │ - Request/Response Transformation                   │  │
│  └──────────────────────────────────────────────────────┘  │
└──────────────────────┬──────────────────────────────────────┘
                       │
        ┌──────────────┼──────────────┐
        │              │              │
        ▼              ▼              ▼
   ┌────────┐  ┌──────────────┐  ┌──────────┐
   │Supabase│  │  External    │  │  AI/ML   │
   │ (DB +  │  │   Services   │  │  Services│
   │ Auth)  │  │              │  │          │
   └────────┘  └──────────────┘  └──────────┘
        │              │              │
        ▼              ▼              ▼
   PostgreSQL  ┌────────────┐  ┌──────────┐
   + Realtime  │ Stripe     │  │ Claude   │
   + Storage   │ Resend     │  │ Gemini   │
               │ Mapbox     │  │ Google   │
               │ n8n        │  │ Analytics│
               │ Sentry     │  │ Clarity  │
               └────────────┘  └──────────┘
```

## Technology Stack Details

### Frontend Layer

**Next.js 14+ (App Router)**
- Server-side rendering for SEO
- Incremental Static Regeneration (ISR)
- API routes collocated with frontend
- Built-in optimization (images, fonts, scripts)
- Zero-config deployment to Vercel

**TypeScript**
- Full type safety across codebase
- Better IDE support and refactoring
- Self-documenting code for AI tools

**Tailwind CSS + shadcn/ui**
- Utility-first CSS framework
- Accessible component library
- Dark mode support
- Responsive design out of the box

### Backend Layer

**Supabase PostgreSQL**
- Managed PostgreSQL database
- Row-level security (RLS) for multi-tenancy
- Built-in real-time subscriptions
- Full-text search capabilities
- PostGIS for geospatial queries

**Prisma ORM**
- Type-safe database access
- Auto-generated migrations
- Efficient query optimization
- Developer-friendly API

**Supabase Real-time**
- WebSocket connections for live updates
- Presence tracking
- Broadcast messaging
- Conflict-free replicated data types

**Vercel Serverless Functions**
- Auto-scaling API endpoints
- Built-in analytics
- Automatic HTTPS and HTTP/2
- Environment variable management

### Integrations

**Stripe**
- Subscription management
- Payment processing
- Webhook handling
- Billing portal

**Resend**
- Transactional emails
- Template engine
- Delivery tracking
- SMTP reliability

**Mapbox**
- Geocoding API
- Address autocomplete
- Map visualization
- Location analytics

**Claude & Gemini APIs**
- Text generation
- Content analysis
- Prompt engineering
- Streaming responses

**n8n**
- Workflow automation
- No-code integrations
- Scheduling
- Conditional logic

### Observability

**Sentry**
- Error tracking and debugging
- Performance monitoring
- Release tracking
- Alerts and notifications

**Google Analytics 4**
- User behavior tracking
- Conversion funnel analysis
- Audience segmentation

**Microsoft Clarity**
- Session recordings
- Heatmaps
- User journey mapping

**Vercel Analytics**
- Web Vitals monitoring
- Performance insights
- Deployment tracking

## Database Design Principles

### Multi-Tenancy

- **Organization-scoped data**: Every table includes `organization_id` foreign key
- **Row-Level Security (RLS)**: PostgreSQL policies enforce tenant isolation
- **Shared infrastructure**: Cost-efficient while maintaining data isolation

### Schema Design

- **UUIDs for all primary keys** (not sequential integers)
- **Soft deletes** for audit trail (not hard deletes)
- **Timestamp tracking** (created_at, updated_at)
- **Normalized schema** to reduce duplication
- **JSONB columns** for flexible data (metadata, settings)
- **Indexes on foreign keys** and frequently-queried columns

### Performance Optimization

- **Connection pooling** via Supabase
- **Query optimization** via Prisma
- **Caching layer** (in-memory for session data)
- **Batch operations** for bulk imports/exports
- **Pagination** for large result sets

## API Architecture

### REST Design

- **RESTful endpoints** following standard conventions
- **Status codes** (200, 201, 400, 401, 403, 404, 409, 422, 429, 500)
- **JSON request/response** bodies
- **Consistent error format**:

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request",
    "details": [
      { "field": "email", "message": "Invalid email format" }
    ]
  }
}
```

### Security Measures

- **JWT authentication** (exp: 15 minutes)
- **Refresh tokens** (exp: 30 days, httpOnly cookies)
- **CORS** properly configured per environment
- **Rate limiting** (100 req/min per user, 1000 req/min per IP)
- **Input validation** via Zod schemas
- **SQL injection prevention** via Prisma ORM
- **XSS protection** via Content Security Policy
- **CSRF tokens** on state-changing operations

### API Versioning Strategy

- **No version in URL** (v1, v2, etc.)
- **Backward compatible** API changes (add fields, don't remove)
- **Deprecation notices** in response headers 60 days before removal
- **Feature flags** for experimental APIs

## Caching Strategy

### Client-Side Caching

- **HTTP cache headers** (Cache-Control, ETag)
- **Service Worker** for offline support (future)
- **React Query** for server state
- **Local Storage** for preferences

### Server-Side Caching

- **Database query results** (Redis if needed for Phase 2)
- **API responses** (CDN with Cloudflare)
- **Static assets** (CDN)
- **Session data** (in-memory, short-lived)

## Scalability Considerations

### Horizontal Scaling

- **Stateless API layer** (Vercel handles auto-scaling)
- **Database connection pooling** (handled by Supabase)
- **CDN distribution** (Cloudflare global network)

### Vertical Scaling

- **Database** scales within Supabase tiers
- **Storage** scales with Supabase bucket
- **Compute** auto-scales with Vercel

### Performance Targets

- **Page load** <1s (Largest Contentful Paint)
- **API response** <100ms (p99)
- **Database query** <50ms (p99)
- **Search indexing** <2s for 10K listings
- **Concurrent users** 1,000+ supported

## Deployment Architecture

### Environments

**Production**
- Live user data
- Full monitoring & alerting
- Automated backups
- CDN caching enabled

**Staging**
- Copy of production data (weekly refresh)
- Same infrastructure as production
- Full testing before release
- No monitoring alerts

**Development**
- Local database (or Supabase dev branch)
- No external integrations (mocked)
- Loose rate limiting

### CI/CD Pipeline

1. **Developer** pushes to `feature/*` branch
2. **GitHub Actions** runs:
   - Linting (ESLint, Prettier)
   - Type checking (TypeScript)
   - Unit tests (Jest)
   - Integration tests (Playwright)
3. **Pull request** requires review + passing checks
4. **Merge to develop** deploys to staging automatically
5. **Manual promotion** to production after QA
6. **Rollback** available via Vercel dashboard

## Security Architecture

### Authentication Flow

```
Client                        Supabase Auth              Database
  │                               │                          │
  ├─ POST /auth/signup ────────────>                         │
  │                               │                          │
  │                         Create user                      │
  │                               │                          │
  │                               ├─────────────────────────>│
  │                               │                          │
  │<─ access_token, refresh_token─┤                          │
  │                               │                          │
  ├─ GET /api/listings ────────────>                         │
  │ (with access_token)           │                          │
  │                         Verify JWT                       │
  │                               │                          │
  │                               ├─────────────────────────>│
  │                         RLS policy enforcement           │
  │<─────────────── listings ──────────────────────────────┤
  │                               │                          │
```

### Data Protection

- **Encryption at rest** (Supabase default)
- **Encryption in transit** (HTTPS/TLS)
- **Password hashing** (bcrypt via Supabase)
- **Secrets management** (Vercel env vars, never in code)
- **PII handling** (GDPR compliant storage)

### Monitoring & Alerting

- **Error tracking** (Sentry)
- **Performance monitoring** (Vercel Analytics)
- **Uptime monitoring** (external service)
- **Log aggregation** (Supabase logs)
- **Security scanning** (GitHub secret scanning)

## Disaster Recovery

### Backup Strategy

- **Daily database backups** (Supabase managed)
- **Point-in-time recovery** up to 30 days
- **File storage backups** (Supabase)
- **Configuration backups** (Infrastructure as Code)

### Failover Plan

- **Multi-region database** (Phase 2+)
- **Automatic failover** for critical services
- **RTO** (Recovery Time Objective): <1 hour
- **RPO** (Recovery Point Objective): <15 minutes

## Maintenance & Operations

### Monitoring Checklist

- ✅ Database performance (query times, disk usage)
- ✅ API availability and latency
- ✅ Error rates and stack traces
- ✅ Security events and access logs
- ✅ Cost tracking (Vercel, Supabase, third-party)

### Regular Tasks

- **Weekly**: Review monitoring dashboards
- **Monthly**: Security audit, cost analysis
- **Quarterly**: Performance optimization, capacity planning
- **Annually**: Compliance audit, disaster recovery test

## Future Architecture Enhancements

### Phase 2 (Weeks 13-24)
- **Redis caching layer** for performance
- **Message queue** (n8n webhooks)
- **Search engine** (Meilisearch or Algolia)
- **GraphQL API** alongside REST

### Phase 3 (Weeks 25-52)
- **Multi-region deployment**
- **Kubernetes orchestration** (if needed)
- **Custom analytics engine**
- **Real-time collaboration** (WebSockets)
- **Microservices** (if appropriate)
