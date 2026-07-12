# DirectoryOS Database Schema

## Database Overview

**Engine**: PostgreSQL 15+
**Host**: Supabase managed
**Type**: Relational with JSONB support
**Scaling**: Vertical (Supabase tiers)

## Core Tables

### 1. organizations

Represents a team/workspace using DirectoryOS.

```sql
CREATE TABLE organizations (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  name VARCHAR(255) NOT NULL,
  slug VARCHAR(255) UNIQUE NOT NULL,
  description TEXT,
  logo_url TEXT,
  website TEXT,
  timezone VARCHAR(50) DEFAULT 'UTC',
  industry VARCHAR(100),
  subscription_tier VARCHAR(50) DEFAULT 'starter',
  subscription_status VARCHAR(50) DEFAULT 'active',
  max_listings INTEGER DEFAULT 100,
  max_team_members INTEGER DEFAULT 5,
  settings JSONB DEFAULT '{}',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  deleted_at TIMESTAMP
);

CREATE INDEX idx_organizations_slug ON organizations(slug);
CREATE INDEX idx_organizations_subscription_tier ON organizations(subscription_tier);
```

### 2. users

Represents team members with access to the platform.

```sql
CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
  email VARCHAR(255) NOT NULL UNIQUE,
  full_name VARCHAR(255) NOT NULL,
  avatar_url TEXT,
  role VARCHAR(50) NOT NULL CHECK (role IN ('admin', 'editor', 'viewer')),
  password_hash VARCHAR(255),
  email_verified BOOLEAN DEFAULT false,
  verified_at TIMESTAMP,
  last_login TIMESTAMP,
  settings JSONB DEFAULT '{}',
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  deleted_at TIMESTAMP
);

CREATE INDEX idx_users_organization_id ON users(organization_id);
CREATE INDEX idx_users_email ON users(email);
CREATE UNIQUE INDEX idx_users_org_email ON users(organization_id, email) WHERE deleted_at IS NULL;
```

### 3. listings

Core business directory entries.

```sql
CREATE TABLE listings (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
  name VARCHAR(255) NOT NULL,
  slug VARCHAR(255),
  category VARCHAR(100),
  categories JSONB DEFAULT '[]',
  description TEXT,
  short_description VARCHAR(160),
  phone VARCHAR(20),
  email VARCHAR(255),
  website TEXT,
  address TEXT NOT NULL,
  city VARCHAR(100),
  state VARCHAR(100),
  postal_code VARCHAR(20),
  country VARCHAR(100),
  latitude DECIMAL(10, 8),
  longitude DECIMAL(11, 8),
  hours JSONB DEFAULT '{}',
  images JSONB DEFAULT '[]',
  social_links JSONB DEFAULT '{}',
  status VARCHAR(50) DEFAULT 'draft' CHECK (status IN ('draft', 'published', 'archived')),
  featured BOOLEAN DEFAULT false,
  seo_title VARCHAR(60),
  seo_description VARCHAR(160),
  seo_keywords JSONB DEFAULT '[]',
  view_count INTEGER DEFAULT 0,
  click_count INTEGER DEFAULT 0,
  lead_count INTEGER DEFAULT 0,
  quality_score DECIMAL(3, 2) DEFAULT 0.0,
  metadata JSONB DEFAULT '{}',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  published_at TIMESTAMP,
  deleted_at TIMESTAMP
);

CREATE INDEX idx_listings_organization_id ON listings(organization_id);
CREATE INDEX idx_listings_status ON listings(status);
CREATE INDEX idx_listings_slug ON listings(slug);
CREATE INDEX idx_listings_city_state ON listings(city, state);
CREATE INDEX idx_listings_location ON listings USING GIST (ll_to_earth(latitude, longitude));
CREATE UNIQUE INDEX idx_listings_org_slug ON listings(organization_id, slug) WHERE deleted_at IS NULL;
```

### 4. leads

Captured leads from listing forms.

```sql
CREATE TABLE leads (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  listing_id UUID NOT NULL REFERENCES listings(id) ON DELETE CASCADE,
  organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
  first_name VARCHAR(100),
  last_name VARCHAR(100),
  email VARCHAR(255),
  phone VARCHAR(20),
  message TEXT,
  source VARCHAR(50) DEFAULT 'form',
  status VARCHAR(50) DEFAULT 'new' CHECK (status IN ('new', 'contacted', 'converted', 'lost')),
  quality_score INTEGER DEFAULT 0,
  metadata JSONB DEFAULT '{}',
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  deleted_at TIMESTAMP
);

CREATE INDEX idx_leads_organization_id ON leads(organization_id);
CREATE INDEX idx_leads_listing_id ON leads(listing_id);
CREATE INDEX idx_leads_status ON leads(status);
CREATE INDEX idx_leads_created_at ON leads(created_at);
```

### 5. analytics_events

Tracking user interactions and listing performance.

```sql
CREATE TABLE analytics_events (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
  listing_id UUID REFERENCES listings(id) ON DELETE SET NULL,
  user_id UUID REFERENCES users(id) ON DELETE SET NULL,
  event_type VARCHAR(50) NOT NULL CHECK (event_type IN ('view', 'click', 'lead', 'share', 'call')),
  event_source VARCHAR(50),
  device_type VARCHAR(20),
  browser VARCHAR(100),
  referrer TEXT,
  country VARCHAR(100),
  city VARCHAR(100),
  metadata JSONB DEFAULT '{}',
  timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_analytics_organization_id ON analytics_events(organization_id);
CREATE INDEX idx_analytics_listing_id ON analytics_events(listing_id);
CREATE INDEX idx_analytics_event_type ON analytics_events(event_type);
CREATE INDEX idx_analytics_timestamp ON analytics_events(timestamp);
CREATE INDEX idx_analytics_org_time ON analytics_events(organization_id, timestamp);
```

### 6. listings_audit

Change tracking for audit trail and versioning.

```sql
CREATE TABLE listings_audit (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  listing_id UUID NOT NULL REFERENCES listings(id) ON DELETE CASCADE,
  organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
  user_id UUID REFERENCES users(id) ON DELETE SET NULL,
  action VARCHAR(50) NOT NULL CHECK (action IN ('create', 'update', 'delete', 'publish', 'archive')),
  changes JSONB NOT NULL,
  previous_values JSONB,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_listings_audit_listing_id ON listings_audit(listing_id);
CREATE INDEX idx_listings_audit_organization_id ON listings_audit(organization_id);
CREATE INDEX idx_listings_audit_user_id ON listings_audit(user_id);
CREATE INDEX idx_listings_audit_created_at ON listings_audit(created_at);
```

### 7. categories

Available listing categories.

```sql
CREATE TABLE categories (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  organization_id UUID REFERENCES organizations(id) ON DELETE CASCADE,
  name VARCHAR(100) NOT NULL,
  slug VARCHAR(100) NOT NULL,
  description TEXT,
  icon_url TEXT,
  parent_id UUID REFERENCES categories(id),
  sort_order INTEGER DEFAULT 0,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

CREATE INDEX idx_categories_organization_id ON categories(organization_id);
CREATE INDEX idx_categories_parent_id ON categories(parent_id);
```

### 8. api_keys

For future API access and integrations.

```sql
CREATE TABLE api_keys (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  organization_id UUID NOT NULL REFERENCES organizations(id) ON DELETE CASCADE,
  name VARCHAR(255) NOT NULL,
  key_hash VARCHAR(255) NOT NULL UNIQUE,
  permissions JSONB DEFAULT '[]',
  rate_limit INTEGER DEFAULT 1000,
  last_used_at TIMESTAMP,
  expires_at TIMESTAMP,
  is_active BOOLEAN DEFAULT true,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
  created_by UUID REFERENCES users(id)
);

CREATE INDEX idx_api_keys_organization_id ON api_keys(organization_id);
CREATE INDEX idx_api_keys_is_active ON api_keys(is_active);
```

## Row-Level Security (RLS) Policies

### Enable RLS on all tables

```sql
ALTER TABLE organizations ENABLE ROW LEVEL SECURITY;
ALTER TABLE users ENABLE ROW LEVEL SECURITY;
ALTER TABLE listings ENABLE ROW LEVEL SECURITY;
ALTER TABLE leads ENABLE ROW LEVEL SECURITY;
ALTER TABLE analytics_events ENABLE ROW LEVEL SECURITY;
ALTER TABLE listings_audit ENABLE ROW LEVEL SECURITY;
ALTER TABLE categories ENABLE ROW LEVEL SECURITY;
ALTER TABLE api_keys ENABLE ROW LEVEL SECURITY;
```

### Example RLS Policy (for listings)

```sql
CREATE POLICY listings_select_policy
  ON listings FOR SELECT
  USING (
    organization_id = (
      SELECT organization_id FROM users WHERE id = auth.uid() AND deleted_at IS NULL
    )
  );

CREATE POLICY listings_update_policy
  ON listings FOR UPDATE
  USING (
    organization_id = (
      SELECT organization_id FROM users WHERE id = auth.uid() AND role IN ('admin', 'editor') AND deleted_at IS NULL
    )
  );

CREATE POLICY listings_delete_policy
  ON listings FOR DELETE
  USING (
    organization_id = (
      SELECT organization_id FROM users WHERE id = auth.uid() AND role = 'admin' AND deleted_at IS NULL
    )
  );
```

## Migrations

**Tool**: Prisma Migrations
**Location**: `prisma/migrations/`
**Execution**: Automatic on deployment

All schema changes must:
- ✅ Be created as numbered migrations
- ✅ Be backwards compatible
- ✅ Include both up and down migrations
- ✅ Be tested in staging before production

## Backup & Recovery

### Backup Schedule
- **Automatic daily backups** via Supabase
- **Point-in-time recovery** available for 30 days
- **Manual backups** before major deployments

### Data Retention
- **Soft deletes** (deleted_at timestamp) for audit trail
- **Audit logs** retained for 1 year
- **Analytics events** retained for 1 year
- **User activity logs** retained for 90 days

## Performance Tuning

### Indexes
- Foreign key columns are indexed
- Frequently-filtered columns (status, organization_id)
- Frequently-sorted columns (created_at, updated_at)
- Search columns (name, slug)
- Geospatial columns (location)

### Query Optimization
- Use `SELECT` specific columns, not `SELECT *`
- Limit result sets with pagination
- Use indexes for WHERE and JOIN clauses
- Avoid N+1 queries (use JOINs)

### Connection Pooling
- Supabase manages connection pooling
- Max connections: 100 (development), 200+ (production)
- Idle timeout: 30 seconds
