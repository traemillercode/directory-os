# DirectoryOS API Specification

## Base URL

```
Production: https://app.directoryos.com/api
Staging: https://staging.directoryos.com/api
Development: http://localhost:3000/api
```

## Authentication

All API requests require authentication via JWT bearer token.

```bash
Authorization: Bearer <access_token>
```

### Authentication Endpoints

#### POST /auth/signup

Create a new user account.

**Request**:
```json
{
  "email": "user@example.com",
  "password": "SecurePassword123!",
  "full_name": "John Doe",
  "organization_name": "My Directory"
}
```

**Response** (201 Created):
```json
{
  "user": {
    "id": "uuid",
    "email": "user@example.com",
    "full_name": "John Doe",
    "organization_id": "uuid"
  },
  "access_token": "jwt_token",
  "refresh_token": "refresh_jwt",
  "expires_in": 900
}
```

#### POST /auth/login

**Request**:
```json
{
  "email": "user@example.com",
  "password": "SecurePassword123!"
}
```

**Response** (200 OK): Same as signup

#### POST /auth/logout

Invalidate current session.

**Response** (200 OK):
```json
{ "message": "Successfully logged out" }
```

#### POST /auth/refresh

Renew access token using refresh token.

**Request**:
```json
{ "refresh_token": "refresh_jwt" }
```

**Response** (200 OK):
```json
{
  "access_token": "new_jwt_token",
  "expires_in": 900
}
```

#### POST /auth/password-reset

Request password reset email.

**Request**:
```json
{ "email": "user@example.com" }
```

**Response** (200 OK):
```json
{ "message": "Password reset email sent" }
```

## Listings Endpoints

### GET /listings

List all listings for organization with pagination and filtering.

**Query Parameters**:
- `page` (integer, default: 1)
- `limit` (integer, default: 20, max: 100)
- `status` (string: draft|published|archived)
- `category` (string)
- `search` (string: searches name and description)
- `sort` (string: created_at|updated_at|name)
- `order` (string: asc|desc)

**Response** (200 OK):
```json
{
  "data": [
    {
      "id": "uuid",
      "name": "Business Name",
      "slug": "business-name",
      "description": "...",
      "status": "published",
      "view_count": 123,
      "lead_count": 5,
      "created_at": "2026-01-15T10:30:00Z",
      "updated_at": "2026-01-16T14:20:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 150,
    "pages": 8
  }
}
```

### POST /listings

Create a new listing.

**Request**:
```json
{
  "name": "Business Name",
  "category": "plumber",
  "description": "Full business description",
  "phone": "+1-555-0123",
  "email": "contact@business.com",
  "website": "https://business.com",
  "address": "123 Main St, Springfield, IL 62701",
  "hours": {
    "monday": { "open": "09:00", "close": "17:00" },
    "tuesday": { "open": "09:00", "close": "17:00" }
  },
  "images": ["url1", "url2"],
  "social_links": {
    "facebook": "https://facebook.com/business",
    "instagram": "https://instagram.com/business"
  }
}
```

**Response** (201 Created):
```json
{
  "id": "uuid",
  "name": "Business Name",
  "slug": "business-name",
  "status": "draft",
  "created_at": "2026-01-15T10:30:00Z"
}
```

### GET /listings/:id

Retrieve a single listing by ID.

**Response** (200 OK): Full listing object

### PATCH /listings/:id

Update a listing.

**Request**: Partial listing object (only fields to update)

**Response** (200 OK): Updated listing object

### DELETE /listings/:id

Soft delete a listing (marks deleted_at).

**Response** (204 No Content)

### GET /listings/export

Export listings to CSV.

**Query Parameters**:
- `status` (optional)
- `category` (optional)

**Response** (200 OK): CSV file download

## AI Endpoints

### POST /ai/generate-description

Generate a listing description using Claude.

**Request**:
```json
{
  "listing_id": "uuid",
  "category": "plumber",
  "current_description": "Optional existing description",
  "tone": "professional",
  "length": "medium"
}
```

**Response** (200 OK):
```json
{
  "description": "Generated description text",
  "tokens_used": 150
}
```

### POST /ai/seo-analysis

Analyze and score listing for SEO.

**Request**:
```json
{
  "listing_id": "uuid",
  "title": "Business Name",
  "description": "Description text",
  "keywords": ["keyword1", "keyword2"]
}
```

**Response** (200 OK):
```json
{
  "score": 75,
  "issues": [
    {
      "type": "title_length",
      "severity": "warning",
      "message": "Title should be 50-60 characters"
    }
  ],
  "recommendations": [
    "Add more keywords to description",
    "Include location in title"
  ]
}
```

### POST /ai/content-score

Score content completeness and quality.

**Request**:
```json
{
  "listing_id": "uuid"
}
```

**Response** (200 OK):
```json
{
  "completeness_score": 85,
  "quality_score": 72,
  "missing_fields": ["social_links", "hours"],
  "suggestions": ["Add business hours", "Link social media profiles"]
}
```

## Analytics Endpoints

### GET /analytics/dashboard

Get organization analytics overview.

**Query Parameters**:
- `date_from` (ISO 8601)
- `date_to` (ISO 8601)

**Response** (200 OK):
```json
{
  "total_listings": 150,
  "published_listings": 140,
  "total_views": 5432,
  "total_clicks": 234,
  "total_leads": 45,
  "average_lead_quality": 7.2,
  "content_completion": 92,
  "top_listings": [
    {
      "id": "uuid",
      "name": "Business Name",
      "views": 500,
      "leads": 12
    }
  ],
  "trends": {
    "views_trend": 5.2,
    "leads_trend": -2.1
  }
}
```

### GET /analytics/listing/:id

Get detailed analytics for a single listing.

**Query Parameters**:
- `date_from` (ISO 8601)
- `date_to` (ISO 8601)

**Response** (200 OK):
```json
{
  "listing_id": "uuid",
  "name": "Business Name",
  "views": 234,
  "clicks": 45,
  "leads": 8,
  "lead_quality_average": 7.5,
  "view_sources": {
    "organic_search": 120,
    "direct": 80,
    "referral": 34
  },
  "device_breakdown": {
    "mobile": 140,
    "desktop": 94
  },
  "hourly_breakdown": [...]
}
```

### GET /analytics/search-terms

Get search terms that led to listings.

**Response** (200 OK):
```json
{
  "terms": [
    {
      "term": "plumber near me",
      "impressions": 234,
      "clicks": 45,
      "position": 3.2
    }
  ]
}
```

## Leads Endpoints

### GET /leads

List leads for organization.

**Query Parameters**:
- `page` (integer)
- `limit` (integer)
- `status` (new|contacted|converted|lost)
- `listing_id` (filter by listing)

**Response** (200 OK):
```json
{
  "data": [
    {
      "id": "uuid",
      "listing_id": "uuid",
      "first_name": "John",
      "last_name": "Doe",
      "email": "john@example.com",
      "phone": "+1-555-0123",
      "message": "I'm interested in your services",
      "status": "new",
      "quality_score": 8,
      "created_at": "2026-01-16T10:30:00Z"
    }
  ],
  "pagination": { ... }
}
```

### POST /leads

Capture a new lead (public endpoint, rate-limited).

**Request**:
```json
{
  "listing_id": "uuid",
  "first_name": "John",
  "last_name": "Doe",
  "email": "john@example.com",
  "phone": "+1-555-0123",
  "message": "I'm interested"
}
```

**Response** (201 Created):
```json
{
  "id": "uuid",
  "status": "new",
  "created_at": "2026-01-16T10:30:00Z"
}
```

### PATCH /leads/:id

Update lead status.

**Request**:
```json
{
  "status": "contacted"
}
```

**Response** (200 OK): Updated lead object

## Error Handling

All errors follow this format:

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

### Status Codes

- **200 OK** - Successful request
- **201 Created** - Resource created
- **204 No Content** - Successful deletion
- **400 Bad Request** - Invalid request data
- **401 Unauthorized** - Missing or invalid auth
- **403 Forbidden** - Insufficient permissions
- **404 Not Found** - Resource not found
- **409 Conflict** - Resource already exists
- **422 Unprocessable Entity** - Validation failed
- **429 Too Many Requests** - Rate limit exceeded
- **500 Internal Server Error** - Server error
- **503 Service Unavailable** - Temporary outage

## Rate Limiting

**Limits**:
- **Authenticated**: 100 requests per minute
- **Public endpoints**: 10 requests per minute per IP
- **Bulk operations**: 1 request per second

**Headers**:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 87
X-RateLimit-Reset: 1610786400
```

## Webhooks (Future)

Supported events:
- `listing.created`
- `listing.updated`
- `listing.published`
- `lead.captured`
- `lead.status_changed`
- `subscription.created`
- `subscription.cancelled`

## SDK Support

- TypeScript/JavaScript (native)
- Python (planned Phase 2)
- Ruby (planned Phase 2)
- Go (planned Phase 3)
