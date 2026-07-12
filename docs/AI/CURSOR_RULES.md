# Cursor Rules for DirectoryOS Development

## Project Context

**Project**: DirectoryOS - AI-powered directory operating system
**Tech Stack**: Next.js, TypeScript, Tailwind, shadcn/ui, Supabase, PostgreSQL, Prisma
**Phase**: MVP (12 weeks)
**Target**: Production-ready SaaS platform

## Code Style & Conventions

### TypeScript

```typescript
// ✅ DO: Strict typing
interface CreateListingRequest {
  name: string;
  description: string;
  organizationId: string;
  category: 'plumber' | 'lawyer' | 'restaurant';
}

function createListing(req: CreateListingRequest): Promise<Listing> {
  // Implementation
}

// ❌ DON'T: Use `any`
function processList(items: any[]): void {}

// ❌ DON'T: Implicit return types
const handleSubmit = (data) => { /* ... */ };
```

### File Organization

```
src/
├── app/                    # Next.js app router
│   ├── (auth)/            # Auth layout group
│   │   └── login/
│   ├── (dashboard)/       # Dashboard layout group
│   │   └── listings/
│   └── api/               # API routes
│       ├── listings/
│       ├── leads/
│       └── ai/
├── components/            # React components
│   ├── ui/               # shadcn/ui components
│   ├── forms/            # Form components
│   ├── dialogs/          # Dialog/modal components
│   └── layout/           # Layout components
├── lib/                  # Utilities & helpers
│   ├── api.ts            # API client
│   ├── auth.ts           # Auth utilities
│   ├── db.ts             # Database utilities
│   ├── validators.ts     # Zod schemas
│   └── utils.ts          # General utilities
├── hooks/                # React hooks
│   ├── useListings.ts
│   ├── useLeads.ts
│   └── useAuth.ts
├── types/                # TypeScript types
│   ├── listing.ts
│   ├── lead.ts
│   └── api.ts
├── constants/            # App constants
│   ├── env.ts
│   ├── routes.ts
│   └── config.ts
├── middleware.ts         # Next.js middleware
└── env.ts                # Environment variables
```

### Naming Conventions

```typescript
// Components: PascalCase
function ListingCard() {}
function ImportListingsDialog() {}

// Functions: camelCase
function fetchListings() {}
function validateEmail() {}

// Constants: UPPER_SNAKE_CASE
const MAX_LISTINGS = 1000;
const API_BASE_URL = 'https://api.directoryos.com';

// Boolean functions/variables: is/has prefix
const isLoading = true;
const hasError = false;
function isValidEmail() {}

// API routes: lowercase with hyphens
// GET /api/listings
// POST /api/listings/:id/ai/generate-description

// Database columns: snake_case
// organization_id, created_at, updated_at
```

### React Component Best Practices

```typescript
// ✅ DO: Use functional components
export function ListingCard({ listing }: ListingCardProps) {
  const { id, name, description } = listing;
  
  return (
    <Card>
      <CardHeader>
        <CardTitle>{name}</CardTitle>
      </CardHeader>
      <CardContent>
        <p>{description}</p>
      </CardContent>
    </Card>
  );
}

// ✅ DO: Type props with interface
interface ListingCardProps {
  listing: Listing;
  onEdit?: (id: string) => void;
}

// ✅ DO: Use hooks for logic
function useListings(organizationId: string) {
  const [listings, setListings] = useState<Listing[]>([]);
  const [isLoading, setIsLoading] = useState(true);
  
  useEffect(() => {
    fetchListings(organizationId).then(setListings).finally(() => setIsLoading(false));
  }, [organizationId]);
  
  return { listings, isLoading };
}

// ❌ DON'T: Use class components (unless necessary)
class ListingCard extends React.Component {}

// ❌ DON'T: Store fetched data in component state
// Use React Query or SWR instead
const { data } = useQuery(['listings'], fetchListings);
```

### Error Handling

```typescript
// ✅ DO: Comprehensive error handling
try {
  const result = await createListing(data);
  toast.success('Listing created');
} catch (error) {
  if (error instanceof ValidationError) {
    toast.error(error.message);
  } else if (error instanceof AuthenticationError) {
    router.push('/login');
  } else {
    toast.error('Something went wrong. Please try again.');
    console.error('Unexpected error:', error);
  }
}

// ✅ DO: Use typed errors
class ValidationError extends Error {
  constructor(public details: ValidationDetail[]) {
    super('Validation failed');
  }
}

// ❌ DON'T: Silent failures
try {
  await createListing(data);
} catch (error) {
  // Swallowed error
}
```

## API Development

### Route Handler Structure

```typescript
// app/api/listings/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { validateAuth, validateInput } from '@/lib/middleware';
import { createListingSchema } from '@/lib/validators';

// ✅ DO: Explicit HTTP method handlers
export async function GET(request: NextRequest) {
  try {
    const auth = await validateAuth(request);
    const { searchParams } = new URL(request.url);
    
    const page = searchParams.get('page') || '1';
    const limit = searchParams.get('limit') || '20';
    
    const listings = await db.listings.findMany({
      where: { organizationId: auth.organizationId },
      skip: (parseInt(page) - 1) * parseInt(limit),
      take: parseInt(limit),
    });
    
    return NextResponse.json({ data: listings });
  } catch (error) {
    return handleError(error);
  }
}

export async function POST(request: NextRequest) {
  try {
    const auth = await validateAuth(request);
    const body = await request.json();
    
    // Validate input
    const validated = createListingSchema.parse(body);
    
    // Create listing
    const listing = await db.listings.create({
      data: {
        ...validated,
        organizationId: auth.organizationId,
      },
    });
    
    return NextResponse.json({ data: listing }, { status: 201 });
  } catch (error) {
    return handleError(error);
  }
}
```

### Input Validation

```typescript
// lib/validators.ts
import { z } from 'zod';

export const createListingSchema = z.object({
  name: z.string().min(3).max(255),
  description: z.string().min(20).max(5000),
  phone: z.string().regex(/^\+?1?\d{9,15}$/).optional(),
  email: z.string().email().optional(),
  website: z.string().url().optional(),
  address: z.string().min(10),
  category: z.enum(['plumber', 'lawyer', 'restaurant']),
});

export type CreateListingInput = z.infer<typeof createListingSchema>;
```

## Database Access

### Prisma Usage

```typescript
// ✅ DO: Use Prisma for type safety
const listing = await db.listing.findUnique({
  where: { id },
  include: { leads: { take: 10 } },
});

// ✅ DO: Select specific fields
const listings = await db.listing.findMany({
  select: {
    id: true,
    name: true,
    description: true,
    // Omit large fields like images
  },
});

// ✅ DO: Paginate results
const [listings, total] = await Promise.all([
  db.listing.findMany({
    where: { organizationId },
    skip: (page - 1) * limit,
    take: limit,
  }),
  db.listing.count({ where: { organizationId } }),
]);

// ❌ DON'T: N+1 queries
const listings = await db.listing.findMany();
for (const listing of listings) {
  const leads = await db.lead.findMany({ where: { listingId: listing.id } });
}

// ✅ DO: This instead
const listings = await db.listing.findMany({
  include: { leads: true },
});
```

## Testing

### Unit Tests

```typescript
// lib/__tests__/validators.test.ts
import { createListingSchema } from '@/lib/validators';

describe('Validators', () => {
  it('should validate listing input', () => {
    const valid = {
      name: 'Test Business',
      description: 'A test business description with minimum length',
      address: '123 Main St, Springfield, IL',
      category: 'plumber',
    };
    
    expect(createListingSchema.safeParse(valid).success).toBe(true);
  });
  
  it('should reject invalid listing', () => {
    const invalid = {
      name: 'Ab', // Too short
      description: 'Short',
    };
    
    expect(createListingSchema.safeParse(invalid).success).toBe(false);
  });
});
```

### Integration Tests

```typescript
// tests/listings.spec.ts
import { test, expect } from '@playwright/test';

test('User can create a listing', async ({ page }) => {
  // Login
  await page.goto('/login');
  await page.fill('[name="email"]', 'user@example.com');
  await page.fill('[name="password"]', 'password123');
  await page.click('button:has-text("Sign in")');
  
  // Create listing
  await page.goto('/dashboard/listings');
  await page.click('button:has-text("New Listing")');
  await page.fill('[name="name"]', 'My Business');
  await page.fill('[name="description"]', 'Business description');
  await page.click('button:has-text("Create")');
  
  // Verify
  await expect(page.locator('text=My Business')).toBeVisible();
});
```

## Git & Commits

### Commit Message Format

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
- `ci`: CI/CD changes

**Example**:
```
feat(listings): add bulk import from CSV

Implement CSV file upload with:
- Validation of listing data
- Duplicate detection
- Progress tracking
- Error reporting

Closes #123
```

### Branch Naming

```
feature/add-bulk-import
fix/listings-pagination-bug
docs/api-documentation
refactor/database-queries
```

## Performance

### Code Splitting

```typescript
// ✅ DO: Lazy load heavy components
import dynamic from 'next/dynamic';

const ImportListingsDialog = dynamic(
  () => import('@/components/dialogs/ImportListingsDialog'),
  { loading: () => <Skeleton /> }
);

export function ListingsPage() {
  return (
    <>
      {/* Lightweight content */}
      <ImportListingsDialog />
    </>
  );
}
```

### Image Optimization

```typescript
// ✅ DO: Use Next.js Image component
import Image from 'next/image';

export function ListingImage({ src, alt }: ImageProps) {
  return (
    <Image
      src={src}
      alt={alt}
      width={400}
      height={300}
      priority={false}
      quality={80}
    />
  );
}
```

## Accessibility

```typescript
// ✅ DO: Add ARIA labels
<button
  aria-label="Delete listing"
  onClick={handleDelete}
>
  <Trash2Icon />
</button>

// ✅ DO: Use semantic HTML
<main>
  <h1>Listings</h1>
  <section aria-labelledby="active-listings">
    <h2 id="active-listings">Active Listings</h2>
    {/* Content */}
  </section>
</main>

// ✅ DO: Keyboard navigation
function ListingCard() {
  const [isFocused, setIsFocused] = useState(false);
  
  return (
    <div
      role="article"
      tabIndex={0}
      onFocus={() => setIsFocused(true)}
      onBlur={() => setIsFocused(false)}
    >
      {/* Content */}
    </div>
  );
}
```

## Environment Variables

```bash
# .env.local
NEXT_PUBLIC_APP_URL=http://localhost:3000
NEXT_PUBLIC_SUPABASE_URL=https://xxxxx.supabase.co
NEXT_PUBLIC_SUPABASE_ANON_KEY=xxxxx

SUPABASE_SERVICE_ROLE_KEY=xxxxx
STRIPE_SECRET_KEY=xxxxx
CLAUDE_API_KEY=xxxxx
NEXT_PUBLIC_GOOGLE_ANALYTICS_ID=xxxxx
```

**Never commit secrets** - Use `.env.local` (gitignored)

## Documentation in Code

```typescript
/**
 * Generate an optimized business description using Claude AI.
 * 
 * @param listing - The listing to generate description for
 * @param tone - The tone of the description (professional, friendly, casual)
 * @returns Generated description text and SEO keywords
 * 
 * @example
 * const result = await generateDescription(listing, 'professional');
 * console.log(result.description);
 */
export async function generateDescription(
  listing: Listing,
  tone: 'professional' | 'friendly' | 'casual'
): Promise<GenerateDescriptionResult> {
  // Implementation
}
```

## Debugging Tips

```typescript
// Use console logs strategically
console.log('DEBUG: Listing data:', JSON.stringify(listing, null, 2));

// Use debugger statements
debugger; // Pauses in dev tools

// Use VS Code Debugger
// .vscode/launch.json for Next.js
{
  "version": "0.2.0",
  "configurations": [
    {
      "name": "Next.js",
      "type": "node",
      "request": "launch",
      "runtimeExecutable": "npm",
      "runtimeArgs": ["run", "dev"],
      "console": "integratedTerminal"
    }
  ]
}
```

## When Stuck

1. **Error message unclear?** → Check the stack trace bottom-to-top
2. **Type error?** → Run `npm run type-check` to get full context
3. **Build failing?** → Check `.next` directory isn't corrupted (delete & rebuild)
4. **Database issue?** → Check Supabase dashboard for connection status
5. **API not working?** → Check Network tab in DevTools, verify auth token

## Resources

- [Next.js Docs](https://nextjs.org/docs)
- [TypeScript Handbook](https://www.typescriptlang.org/docs)
- [Prisma Docs](https://www.prisma.io/docs)
- [shadcn/ui Components](https://ui.shadcn.com)
- [Tailwind CSS Docs](https://tailwindcss.com/docs)
- [Project Architecture](./docs/Architecture/ARCHITECTURE.md)
- [API Specification](./docs/Architecture/API.md)
