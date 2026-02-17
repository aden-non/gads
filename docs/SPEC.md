# 📐 SPEC.md - Code Quality Standards & Patterns

> **Purpose:** Detailed implementation patterns loaded on-demand during code writing  
> **Usage:** Agent loads this ONLY when writing/reviewing code, not during planning  
> **Last Updated:** [Date]

---

## 🗺️ Quick Navigation
- TypeScript: Section 1
- Performance: Section 2
- Components: Section 3
- Error Handling: Section 4
- Security: Section 5
- Testing: Section 6
- Database: Section 7
- API Design: Section 8
- **🔴 Google Ads Scripts: Section 9** (MANDATORY for GAds work)

---

## 1. TypeScript Standards

### Strict Mode Rules
```typescript
// ✅ GOOD TypeScript practices
- Use strict mode (enabled in tsconfig.json)
- Never use `any` - use `unknown` if type truly unknown
- Prefer interfaces over types for object shapes
- Use type guards for runtime type checking
- Explicit return types on functions

// Example
interface User {
  id: string;
  name: string;
  email: string;
  createdAt: Date;
}

function getUser(id: string): Promise<User> {
  // Implementation
}
```

### Type Safety Patterns
```typescript
// ❌ BAD: Using any
function process(data: any) {
  return data.value;
}

// ✅ GOOD: Using unknown with type guard
function process(data: unknown): string {
  if (typeof data === 'object' && data !== null && 'value' in data) {
    return String(data.value);
  }
  throw new Error('Invalid data structure');
}

// ✅ GOOD: Generic constraints
function findById<T extends { id: string }>(
  items: T[],
  id: string
): T | undefined {
  return items.find(item => item.id === id);
}
```

### Import/Export Conventions
```typescript
// ❌ BAD: Default exports
export default function Button() {}

// ✅ GOOD: Named exports (better for tree-shaking and refactoring)
export function Button() {}
export { Button };

// ❌ BAD: Barrel exports loading everything
import { Check, X, Plus } from 'lucide-react'; // Loads 1,583 modules

// ✅ GOOD: Direct imports only load what's needed
import Check from 'lucide-react/dist/esm/icons/check';
import X from 'lucide-react/dist/esm/icons/x';
import Plus from 'lucide-react/dist/esm/icons/plus';
```

---

## 2. Performance Patterns

### Eliminate Waterfalls (CRITICAL)
```javascript
// ❌ BAD: Sequential awaits add full network latency per call
const user = await fetchUser(userId);
const posts = await fetchPosts(user.id);
const comments = await fetchComments(posts[0].id);
// Total time: 300ms + 200ms + 150ms = 650ms

// ✅ GOOD: Parallel fetching when dependencies allow
const [user, posts, comments] = await Promise.all([
  fetchUser(userId),
  fetchPosts(userId),  // Can fetch with userId directly
  fetchComments(postId) // If postId known upfront
]);
// Total time: max(300ms, 200ms, 150ms) = 300ms

// ✅ GOOD: Two-phase when sequential required
const user = await fetchUser(userId);
const [posts, profile] = await Promise.all([
  fetchPosts(user.id),
  fetchProfile(user.id)
]);
```

### Tree-Shaking Imports
```javascript
// ❌ BAD: Massive bundle size
import { Button, Input, Select, Modal } from 'ui-library';
// Loads entire library even if tree-shaking configured

// ✅ GOOD: Direct imports
import Button from 'ui-library/Button';
import Input from 'ui-library/Input';
// Only loads what you use
```

### Lazy Loading
```javascript
// ❌ BAD: Load everything upfront
import HeavyComponent from './HeavyComponent';
import HugeChart from './charts/HugeChart';

// ✅ GOOD: Code splitting with lazy loading
const HeavyComponent = lazy(() => import('./HeavyComponent'));
const HugeChart = lazy(() => import('./charts/HugeChart'));

// Use with Suspense
<Suspense fallback={<Spinner />}>
  <HeavyComponent />
</Suspense>
```

### Memoization (React)
```javascript
// ❌ BAD: Recalculate every render
function ExpensiveList({ items }) {
  const sorted = items
    .filter(item => item.active)
    .sort((a, b) => a.priority - b.priority)
    .map(item => ({ ...item, formatted: formatItem(item) }));
  
  return <List items={sorted} />;
}

// ✅ GOOD: Memoize expensive calculations
function ExpensiveList({ items }) {
  const sorted = useMemo(
    () => items
      .filter(item => item.active)
      .sort((a, b) => a.priority - b.priority)
      .map(item => ({ ...item, formatted: formatItem(item) })),
    [items] // Only recalculate when items change
  );
  
  return <List items={sorted} />;
}

// ✅ GOOD: Memoize callbacks
function Parent() {
  const [count, setCount] = useState(0);
  
  const handleClick = useCallback(() => {
    setCount(c => c + 1);
  }, []); // Stable reference
  
  return <Child onClick={handleClick} />;
}
```

### React Performance Anti-Patterns
```javascript
// ❌ BAD: Inline object/function creation
<Component 
  style={{ margin: 10 }}  // New object every render
  onClick={() => handleClick(id)}  // New function every render
/>

// ✅ GOOD: Stable references
const buttonStyle = { margin: 10 };
const handleButtonClick = useCallback(() => handleClick(id), [id]);

<Component 
  style={buttonStyle}
  onClick={handleButtonClick}
/>

// ❌ BAD: Index as key in dynamic lists
items.map((item, index) => (
  <Item key={index} {...item} />
))

// ✅ GOOD: Stable unique ID as key
items.map(item => (
  <Item key={item.id} {...item} />
))
```

---

## 3. Component Structure

### React Components
```typescript
// ✅ GOOD: Pure functional component with single responsibility
interface ButtonProps {
  label: string;
  onClick: () => void;
  variant?: 'primary' | 'secondary' | 'danger';
  disabled?: boolean;
  loading?: boolean;
}

export function Button({ 
  label, 
  onClick, 
  variant = 'primary',
  disabled = false,
  loading = false
}: ButtonProps) {
  return (
    <button 
      onClick={onClick} 
      disabled={disabled || loading}
      className={`btn btn-${variant}`}
      aria-label={label}
      aria-busy={loading}
    >
      {loading ? <Spinner /> : label}
    </button>
  );
}
```

### Composition Over Props Drilling
```typescript
// ❌ BAD: Props drilling through many levels
<Dashboard user={user} theme={theme} permissions={permissions}>
  <Sidebar user={user} theme={theme}>
    <Menu user={user} permissions={permissions}>
      <MenuItem user={user} />
    </Menu>
  </Sidebar>
</Dashboard>

// ✅ GOOD: Use Context for cross-cutting concerns
const UserContext = createContext<User | null>(null);
const ThemeContext = createContext<Theme>('light');

function App() {
  return (
    <UserContext.Provider value={user}>
      <ThemeContext.Provider value={theme}>
        <Dashboard>
          <Sidebar>
            <Menu>
              <MenuItem />
            </Menu>
          </Sidebar>
        </Dashboard>
      </ThemeContext.Provider>
    </UserContext.Provider>
  );
}
```

---

## 4. Error Handling

### Explicit Error Handling
```typescript
// ❌ BAD: Silent failures
try {
  await riskyOperation();
} catch (e) {
  // Error swallowed
}

// ✅ GOOD: Explicit handling with logging
try {
  await riskyOperation();
} catch (error) {
  logger.error('Risky operation failed', { 
    error, 
    context: { userId, operation: 'riskyOperation' }
  });
  
  // Rethrow with additional context
  throw new AppError('Operation failed', {
    code: 'RISKY_OP_FAILED',
    cause: error,
    recoverable: true
  });
}
```

### Custom Error Classes
```typescript
// ✅ GOOD: Typed error hierarchy
class AppError extends Error {
  constructor(
    message: string,
    public code: string,
    public statusCode: number = 500,
    public recoverable: boolean = false
  ) {
    super(message);
    this.name = 'AppError';
  }
}

class ValidationError extends AppError {
  constructor(message: string, public field: string) {
    super(message, 'VALIDATION_ERROR', 400, true);
    this.name = 'ValidationError';
  }
}

class AuthenticationError extends AppError {
  constructor(message: string) {
    super(message, 'AUTH_ERROR', 401, false);
    this.name = 'AuthenticationError';
  }
}

// Usage
throw new ValidationError('Invalid email format', 'email');
```

### Error Boundaries (React)
```typescript
// ✅ GOOD: Error boundary for graceful degradation
class ErrorBoundary extends React.Component
  { fallback: React.ReactNode; children: React.ReactNode },
  { hasError: boolean; error: Error | null }
> {
  state = { hasError: false, error: null };
  
  static getDerivedStateFromError(error: Error) {
    return { hasError: true, error };
  }
  
  componentDidCatch(error: Error, errorInfo: React.ErrorInfo) {
    logger.error('Component error caught', { error, errorInfo });
  }
  
  render() {
    if (this.state.hasError) {
      return this.props.fallback;
    }
    return this.props.children;
  }
}

// Usage
<ErrorBoundary fallback={<ErrorMessage />}>
  <SuspiciousComponent />
</ErrorBoundary>
```

---

## 5. Security Patterns

### Input Validation (Zod)
```typescript
// ❌ BAD: No validation
const { email, age } = req.body;
await createUser(email, age);

// ✅ GOOD: Schema validation with Zod
import { z } from 'zod';

const userSchema = z.object({
  email: z.string().email().max(255),
  age: z.number().int().min(18).max(120),
  password: z.string()
    .min(12, 'Password must be at least 12 characters')
    .max(128)
    .regex(/[A-Z]/, 'Must contain uppercase')
    .regex(/[a-z]/, 'Must contain lowercase')
    .regex(/[0-9]/, 'Must contain number')
    .regex(/[^A-Za-z0-9]/, 'Must contain special character')
});

try {
  const validated = userSchema.parse(req.body);
  await createUser(validated);
} catch (error) {
  if (error instanceof z.ZodError) {
    return res.status(400).json({
      error: 'Validation failed',
      details: error.errors
    });
  }
  throw error;
}
```

### SQL Injection Prevention
```typescript
// ❌ BAD: String concatenation (SQL injection risk)
const user = await db.raw(
  `SELECT * FROM users WHERE email = '${userInput}'`
);

// ✅ GOOD: Parameterized with Prisma ORM
const user = await prisma.user.findFirst({
  where: { email: userInput }
});

// ✅ GOOD: Parameterized raw query (if ORM not available)
const user = await db.raw(
  'SELECT * FROM users WHERE email = ?',
  [userInput]
);
```

### Authentication & Password Hashing
```typescript
// ✅ GOOD: Secure password hashing with bcrypt
import bcrypt from 'bcrypt';

const SALT_ROUNDS = 10;

async function hashPassword(password: string): Promise<string> {
  return bcrypt.hash(password, SALT_ROUNDS);
}

async function verifyPassword(
  password: string, 
  hash: string
): Promise<boolean> {
  return bcrypt.compare(password, hash);
}

// Usage
const hashedPassword = await hashPassword(userPassword);
await prisma.user.create({
  data: {
    email: user.email,
    password: hashedPassword // Never store plaintext!
  }
});
```

### XSS Prevention
```typescript
// ✅ GOOD: Sanitize user input before rendering
import DOMPurify from 'dompurify';

function SafeHtml({ html }: { html: string }) {
  const sanitized = DOMPurify.sanitize(html, {
    ALLOWED_TAGS: ['b', 'i', 'em', 'strong', 'a'],
    ALLOWED_ATTR: ['href']
  });
  
  return <div dangerouslySetInnerHTML={{ __html: sanitized }} />;
}
```

---

## 6. Testing Patterns

### AAA Pattern (Arrange-Act-Assert)
```typescript
describe('UserService', () => {
  describe('createUser', () => {
    it('should create user with hashed password', async () => {
      // ARRANGE: Set up test data and dependencies
      const userData = {
        email: 'test@example.com',
        password: 'SecurePass123!'
      };
      const mockHashFn = jest.fn().mockResolvedValue('hashed_pw');
      const mockDb = createMockDatabase();
      
      // ACT: Execute the function being tested
      const result = await userService.createUser(
        userData,
        { hashPassword: mockHashFn, db: mockDb }
      );
      
      // ASSERT: Verify expected outcomes
      expect(result.id).toBeDefined();
      expect(result.email).toBe(userData.email);
      expect(mockHashFn).toHaveBeenCalledWith(userData.password);
      expect(result.password).toBe('hashed_pw');
    });
    
    it('should throw error for invalid email', async () => {
      // ARRANGE
      const userData = {
        email: 'invalid-email',
        password: 'SecurePass123!'
      };
      
      // ACT & ASSERT
      await expect(
        userService.createUser(userData)
      ).rejects.toThrow('Invalid email format');
    });
  });
});
```

### Mocking Best Practices
```typescript
// ❌ BAD: Over-mocking (can hide bugs)
jest.mock('./everything');
jest.mock('../utils/all');

// ✅ GOOD: Mock only external boundaries
jest.mock('./api/client'); // External API
jest.mock('./db'); // Database
// Keep internal business logic unmocked for integration testing

// ✅ GOOD: Factory functions for test data
function createMockUser(overrides?: Partial<User>): User {
  return {
    id: 'test-id',
    email: 'test@example.com',
    name: 'Test User',
    createdAt: new Date('2024-01-01'),
    ...overrides
  };
}

// Usage
const user = createMockUser({ email: 'custom@example.com' });
```

---

## 7. Database Patterns

### Query Optimization
```typescript
// ❌ BAD: N+1 query problem
const users = await prisma.user.findMany();
for (const user of users) {
  user.posts = await prisma.post.findMany({
    where: { userId: user.id }
  });
}
// This runs 1 query + N queries = O(N) database calls

// ✅ GOOD: Eager loading with include
const users = await prisma.user.findMany({
  include: {
    posts: true
  }
});
// This runs 1 query with JOIN = O(1) database call
```

### Transaction Handling
```typescript
// ✅ GOOD: Atomic operations in transaction
await prisma.$transaction(async (tx) => {
  // Create order
  const order = await tx.order.create({
    data: {
      userId: user.id,
      total: totalAmount,
      items: {
        create: orderItems
      }
    }
  });
  
  // Decrement inventory
  await tx.inventory.updateMany({
    where: { productId: { in: productIds } },
    data: { quantity: { decrement: 1 } }
  });
  
  // Send confirmation email (idempotent)
  await sendOrderConfirmation(order.id);
  
  return order;
});
// All-or-nothing: if any step fails, entire transaction rolls back
```

---

## 8. API Design

### REST Conventions
```typescript
// ✅ GOOD: RESTful route structure
GET    /api/users              // List users (with pagination)
GET    /api/users/:id          // Get specific user
POST   /api/users              // Create new user
PUT    /api/users/:id          // Full update (replace)
PATCH  /api/users/:id          // Partial update
DELETE /api/users/:id          // Delete user

// ✅ GOOD: Nested resources
GET    /api/users/:id/posts    // User's posts
POST   /api/users/:id/posts    // Create post for user
GET    /api/posts/:id/comments // Post's comments
```

### Response Format
```typescript
// ✅ GOOD: Consistent response structure
interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: {
    code: string;
    message: string;
    details?: unknown;
  };
  meta?: {
    page?: number;
    perPage?: number;
    totalPages?: number;
    totalItems?: number;
  };
}

// Success response
res.json({
  success: true,
  data: users,
  meta: { 
    page: 1, 
    perPage: 20,
    totalPages: 5, 
    totalItems: 98 
  }
});

// Error response
res.status(400).json({
  success: false,
  error: {
    code: 'VALIDATION_ERROR',
    message: 'Email format is invalid',
    details: { 
      field: 'email', 
      value: sanitizedInput,
      expected: 'valid email format'
    }
  }
});
```

---

## 9. Google Ads Scripts - Mega Prompt v2 {#gads-mega-prompt}

> **🔴 CRITICAL:** This section is MANDATORY for all Google Ads Scripts work  
> **Load this section FIRST** before writing any GAds code

### Overview

Google Ads Scripts are JavaScript-based automations that run directly in the Google Ads platform. They have unique constraints and requirements that differ from standard web development.

**Key Constraints:**
- 30-minute execution time limit
- Memory constraints (~250MB)
- GAQL (Google Ads Query Language) uses nested dot-notation
- All cost/conversion metrics stored as "micros" (millions)
- API rate limits apply

---

### 🔴 Rule 1: GAQL Nested Dot-Notation (MANDATORY)

**CRITICAL:** Google Ads API uses nested object notation in GAQL queries.
```javascript
// ❌ BAD: Flat field names (will fail)
const query = `
  SELECT campaign_id, campaign_name, cost_micros
  FROM campaign
  WHERE campaign_status = 'ENABLED'
`;

// ✅ GOOD: Nested dot-notation
const query = `
  SELECT 
    campaign.id,
    campaign.name,
    campaign.status,
    metrics.cost_micros,
    metrics.clicks,
    metrics.impressions,
    metrics.conversions
  FROM campaign
  WHERE campaign.status = 'ENABLED'
    AND metrics.impressions > 0
  ORDER BY metrics.cost_micros DESC
  LIMIT 10000
`;
```

**Common Field Patterns:**
```javascript
// Campaign fields
campaign.id
campaign.name
campaign.status
campaign.advertising_channel_type
campaign.bidding_strategy_type

// Ad Group fields
ad_group.id
ad_group.name
ad_group.status
ad_group.type

// Keyword fields
ad_group_criterion.keyword.text
ad_group_criterion.keyword.match_type
ad_group_criterion.status

// Metrics (always under 'metrics.')
metrics.cost_micros
metrics.clicks
metrics.impressions
metrics.conversions
metrics.conversions_value
metrics.average_cpc
metrics.ctr

// Segments (for date ranges, devices, etc.)
segments.date
segments.device
segments.day_of_week
```

---

### 🔴 Rule 2: LIMIT Clause is MANDATORY

**CRITICAL:** EVERY GAQL query MUST have a LIMIT clause to prevent timeouts.
```javascript
// ❌ BAD: No LIMIT clause (will timeout on large accounts)
const query = `
  SELECT campaign.id, campaign.name, metrics.cost_micros
  FROM campaign
  WHERE campaign.status = 'ENABLED'
`;

// ✅ GOOD: LIMIT clause present
const query = `
  SELECT campaign.id, campaign.name, metrics.cost_micros
  FROM campaign
  WHERE campaign.status = 'ENABLED'
  LIMIT 10000
`;

// ✅ BETTER: Pagination for >10k rows
function* fetchAllCampaigns() {
  let pageToken = null;
  const BATCH_SIZE = 10000;
  
  do {
    const query = `
      SELECT campaign.id, campaign.name, metrics.cost_micros
      FROM campaign
      WHERE campaign.status = 'ENABLED'
      ORDER BY campaign.id
      LIMIT ${BATCH_SIZE}
    `;
    
    const report = pageToken 
      ? AdsApp.report(query, { pageToken })
      : AdsApp.report(query);
    
    const rows = report.rows();
    while (rows.hasNext()) {
      yield rows.next();
    }
    
    pageToken = report.nextPageToken();
  } while (pageToken);
}

// Usage
for (const row of fetchAllCampaigns()) {
  // Process each campaign
  const costDollars = row['metrics.cost_micros'] / 1000000;
  Logger.log(`${row['campaign.name']}: $${costDollars.toFixed(2)}`);
}
```

**LIMIT Guidelines:**
- **Single batch:** LIMIT 10000 (max safe for 30min timeout)
- **Pagination required:** Any query that could return >10k rows
- **Test queries:** Use LIMIT 10 during development
- **Production:** Always estimate row count before deploying

---

### 🔴 Rule 3: Micros Math Safety (MANDATORY)

**CRITICAL:** All cost, click, and conversion metrics are stored as "micros" (millionths). You MUST divide by 1,000,000 immediately at variable assignment.
```javascript
// ❌ BAD: Storing raw micros (will cause math errors)
const campaigns = [];
const rows = report.rows();
while (rows.hasNext()) {
  const row = rows.next();
  campaigns.push({
    name: row['campaign.name'],
    cost: row['metrics.cost_micros'], // WRONG: Raw micros
    clicks: row['metrics.clicks']
  });
}

// Later math will be wrong
const totalCost = campaigns.reduce((sum, c) => sum + c.cost, 0);
Logger.log(`Total: $${totalCost}`); // Shows $5000000 instead of $5.00

// ✅ GOOD: Convert micros immediately at assignment
const campaigns = [];
const rows = report.rows();
while (rows.hasNext()) {
  const row = rows.next();
  campaigns.push({
    name: row['campaign.name'],
    cost: row['metrics.cost_micros'] / 1000000, // Convert immediately
    clicks: row['metrics.clicks'],
    conversions: row['metrics.conversions'] / 1000000 // Also micros!
  });
}

// Math works correctly
const totalCost = campaigns.reduce((sum, c) => sum + c.cost, 0);
Logger.log(`Total: $${totalCost.toFixed(2)}`); // Shows $5.00
```

**Micros Fields (MUST divide by 1,000,000):**
```javascript
// Cost metrics
metrics.cost_micros → divide by 1000000
metrics.average_cpc → divide by 1000000
metrics.average_cpm → divide by 1000000

// Conversion metrics
metrics.conversions → divide by 1000000
metrics.conversions_value → divide by 1000000
metrics.all_conversions → divide by 1000000

// Bid metrics
ad_group.cpc_bid_micros → divide by 1000000
ad_group_criterion.cpc_bid_micros → divide by 1000000

// NOT micros (use as-is)
metrics.clicks → integer, use directly
metrics.impressions → integer, use directly
metrics.ctr → percentage (0-1), use directly
```

**Helper Function (recommended):**
```javascript
// ✅ BEST: Helper function for consistent conversion
function convertMicros(micros) {
  return micros / 1000000;
}

function formatCurrency(micros) {
  return `$${(micros / 1000000).toFixed(2)}`;
}

// Usage
const cost = convertMicros(row['metrics.cost_micros']);
Logger.log(`Cost: ${formatCurrency(row['metrics.cost_micros'])}`);
```

---

### 🔴 Rule 4: Template Literals for Dynamic Queries

**Use template literals for dynamic date ranges and filters.**
```javascript
// ❌ BAD: String concatenation (hard to read, error-prone)
var query = 'SELECT campaign.id, campaign.name, metrics.cost_micros FROM campaign WHERE segments.date >= "' + startDate + '" AND segments.date <= "' + endDate + '" LIMIT 10000';

// ✅ GOOD: Template literals (readable, maintainable)
const query = `
  SELECT 
    campaign.id,
    campaign.name,
    metrics.cost_micros,
    metrics.clicks,
    segments.date
  FROM campaign
  WHERE segments.date >= "${startDate}"
    AND segments.date <= "${endDate}"
    AND campaign.status = 'ENABLED'
  ORDER BY metrics.cost_micros DESC
  LIMIT 10000
`;

// ✅ BEST: Parameterized function
function buildCampaignQuery(dateRange, minImpressions = 0) {
  return `
    SELECT 
      campaign.id,
      campaign.name,
      campaign.status,
      metrics.cost_micros,
      metrics.clicks,
      metrics.impressions,
      metrics.conversions
    FROM campaign
    WHERE segments.date BETWEEN "${dateRange.start}" AND "${dateRange.end}"
      AND campaign.status = 'ENABLED'
      AND metrics.impressions > ${minImpressions}
    ORDER BY metrics.cost_micros DESC
    LIMIT 10000
  `;
}

// Usage
const query = buildCampaignQuery(
  { start: '2024-01-01', end: '2024-01-31' },
  100 // minimum impressions
);
```

---

### 🔴 Rule 5: Error Handling & Timeout Prevention

**Always wrap API calls in try-catch and monitor execution time.**
```javascript
// ❌ BAD: No error handling
function main() {
  const query = `SELECT campaign.id FROM campaign LIMIT 10000`;
  const report = AdsApp.report(query);
  const rows = report.rows();
  
  while (rows.hasNext()) {
    const row = rows.next();
    // Process row
  }
}

// ✅ GOOD: Error handling and timeout monitoring
function main() {
  const startTime = new Date().getTime();
  const MAX_EXECUTION_TIME = 27 * 60 * 1000; // 27 minutes (safe margin)
  
  try {
    const query = `
      SELECT campaign.id, campaign.name, metrics.cost_micros
      FROM campaign
      WHERE campaign.status = 'ENABLED'
      LIMIT 10000
    `;
    
    const report = AdsApp.report(query);
    const rows = report.rows();
    let processedCount = 0;
    
    while (rows.hasNext()) {
      // Check timeout
      const elapsed = new Date().getTime() - startTime;
      if (elapsed > MAX_EXECUTION_TIME) {
        Logger.log(`Timeout warning: Processed ${processedCount} rows before stopping`);
        break;
      }
      
      const row = rows.next();
      processRow(row);
      processedCount++;
      
      // Log progress every 1000 rows
      if (processedCount % 1000 === 0) {
        Logger.log(`Processed ${processedCount} rows in ${Math.floor(elapsed / 1000)}s`);
      }
    }
    
    Logger.log(`✅ Successfully processed ${processedCount} campaigns`);
    
  } catch (error) {
    Logger.log(`❌ ERROR: ${error.message}`);
    Logger.log(`Stack trace: ${error.stack}`);
    
    // Send alert email
    MailApp.sendEmail({
      to: 'alerts@example.com',
      subject: 'Google Ads Script Failed',
      body: `Error: ${error.message}\n\nStack: ${error.stack}`
    });
  }
}

function processRow(row) {
  // Your processing logic
  const cost = convertMicros(row['metrics.cost_micros']);
  // ...
}
```

---

### 🔴 Rule 6: Batch Operations & Rate Limiting

**Modify entities in batches to avoid rate limits.**
```javascript
// ❌ BAD: Individual API calls (slow, hits rate limits)
const campaigns = AdsApp.campaigns()
  .withCondition('Status = ENABLED')
  .get();

while (campaigns.hasNext()) {
  const campaign = campaigns.next();
  campaign.pause(); // Individual API call per campaign
}

// ✅ GOOD: Batch operations
function pauseLowPerformingCampaigns() {
  const BATCH_SIZE = 100;
  const campaignsToPause = [];
  
  const query = `
    SELECT campaign.id, campaign.name, metrics.cost_micros, metrics.conversions
    FROM campaign
    WHERE campaign.status = 'ENABLED'
      AND segments.date = "LAST_30_DAYS"
    LIMIT 10000
  `;
  
  const report = AdsApp.report(query);
  const rows = report.rows();
  
  while (rows.hasNext()) {
    const row = rows.next();
    const cost = convertMicros(row['metrics.cost_micros']);
    const conversions = convertMicros(row['metrics.conversions']);
    
    // Low performance criteria
    if (cost > 1000 && conversions === 0) {
      campaignsToPause.push({
        id: row['campaign.id'],
        name: row['campaign.name'],
        cost: cost
      });
    }
  }
  
  Logger.log(`Found ${campaignsToPause.length} campaigns to pause`);
  
  // Pause in batches
  for (let i = 0; i < campaignsToPause.length; i += BATCH_SIZE) {
    const batch = campaignsToPause.slice(i, i + BATCH_SIZE);
    
    batch.forEach(campaign => {
      const campaignObj = AdsApp.campaigns()
        .withIds([campaign.id])
        .get()
        .next();
      
      campaignObj.pause();
      Logger.log(`Paused: ${campaign.name} ($${campaign.cost.toFixed(2)} spent, 0 conversions)`);
    });
    
    // Rate limit: pause between batches
    if (i + BATCH_SIZE < campaignsToPause.length) {
      Utilities.sleep(1000); // 1 second delay
    }
  }
}
```

---

### 🔴 Rule 7: Date Range Handling

**Use Google Ads date formats and built-in date functions.**
```javascript
// ❌ BAD: Manual date string construction (error-prone)
const today = new Date();
const dateStr = today.getFullYear() + '-' + (today.getMonth() + 1) + '-' + today.getDate();

// ✅ GOOD: Use Google Ads date literals
const query = `
  SELECT campaign.id, metrics.cost_micros
  FROM campaign
  WHERE segments.date DURING LAST_30_DAYS
  LIMIT 10000
`;

// ✅ GOOD: Custom date ranges
function getDateRange(daysAgo) {
  const endDate = new Date();
  const startDate = new Date();
  startDate.setDate(startDate.getDate() - daysAgo);
  
  return {
    start: Utilities.formatDate(startDate, AdsApp.currentAccount().getTimeZone(), 'yyyy-MM-dd'),
    end: Utilities.formatDate(endDate, AdsApp.currentAccount().getTimeZone(), 'yyyy-MM-dd')
  };
}

// Usage
const dateRange = getDateRange(30);
const query = `
  SELECT campaign.id, metrics.cost_micros
  FROM campaign
  WHERE segments.date BETWEEN "${dateRange.start}" AND "${dateRange.end}"
  LIMIT 10000
`;
```

**Google Ads Date Literals:**
```javascript
// Predefined date ranges (use these when possible)
segments.date DURING TODAY
segments.date DURING YESTERDAY
segments.date DURING LAST_7_DAYS
segments.date DURING LAST_14_DAYS
segments.date DURING LAST_30_DAYS
segments.date DURING THIS_MONTH
segments.date DURING LAST_MONTH
segments.date DURING THIS_YEAR
```

---

### 🔴 Rule 8: Logging Best Practices

**Use structured logging for debugging and monitoring.**
```javascript
// ❌ BAD: Minimal logging
function main() {
  const campaigns = getCampaigns();
  Logger.log('Done');
}

// ✅ GOOD: Structured, informative logging
function main() {
  const startTime = new Date();
  Logger.log('=================================');
  Logger.log('Script: Campaign Optimizer v2.1');
  Logger.log(`Start time: ${startTime.toISOString()}`);
  Logger.log(`Account: ${AdsApp.currentAccount().getName()}`);
  Logger.log('=================================');
  
  try {
    // Phase 1: Fetch data
    Logger.log('\n[PHASE 1] Fetching campaign data...');
    const campaigns = fetchCampaigns();
    Logger.log(`✓ Fetched ${campaigns.length} campaigns`);
    
    // Phase 2: Analyze
    Logger.log('\n[PHASE 2] Analyzing performance...');
    const lowPerformers = analyzeCampaigns(campaigns);
    Logger.log(`✓ Found ${lowPerformers.length} low-performing campaigns`);
    
    // Phase 3: Take action
    Logger.log('\n[PHASE 3] Applying optimizations...');
    const results = optimizeCampaigns(lowPerformers);
    Logger.log(`✓ Paused: ${results.paused}`);
    Logger.log(`✓ Adjusted bids: ${results.bidAdjusted}`);
    
    // Summary
    const endTime = new Date();
    const duration = (endTime - startTime) / 1000;
    Logger.log('\n=================================');
    Logger.log('✅ Script completed successfully');
    Logger.log(`Duration: ${duration.toFixed(2)}s`);
    Logger.log(`Total campaigns processed: ${campaigns.length}`);
    Logger.log('=================================');
    
  } catch (error) {
    Logger.log('\n❌ ERROR OCCURRED');
    Logger.log(`Message: ${error.message}`);
    Logger.log(`Stack: ${error.stack}`);
    throw error; // Re-throw for Google Ads to mark as failed
  }
}
```

---

### Complete Example Script
```javascript
/**
 * Campaign Performance Optimizer
 * 
 * Analyzes campaign performance over last 30 days and:
 * - Pauses campaigns with >$1000 spend and 0 conversions
 * - Reduces bids by 20% for campaigns with high CPA
 * - Sends summary email report
 * 
 * @version 2.1
 * @author Your Name
 */

function main() {
  const CONFIG = {
    DATE_RANGE: 30,
    MIN_SPEND_THRESHOLD: 1000,
    HIGH_CPA_THRESHOLD: 100,
    BID_REDUCTION_PERCENT: 0.20,
    MAX_EXECUTION_TIME: 27 * 60 * 1000, // 27 minutes
    EMAIL_RECIPIENT: 'alerts@example.com'
  };
  
  const startTime = new Date();
  logScriptStart(startTime);
  
  try {
    // Phase 1: Fetch campaign data
    Logger.log('\n[PHASE 1] Fetching campaign performance data...');
    const campaigns = fetchCampaignPerformance(CONFIG.DATE_RANGE);
    Logger.log(`✓ Fetched ${campaigns.length} active campaigns`);
    
    // Phase 2: Analyze
    Logger.log('\n[PHASE 2] Analyzing performance...');
    const analysis = analyzeCampaigns(campaigns, CONFIG);
    Logger.log(`✓ Campaigns to pause: ${analysis.toPause.length}`);
    Logger.log(`✓ Campaigns needing bid reduction: ${analysis.toReduceBids.length}`);
    
    // Phase 3: Apply optimizations
    Logger.log('\n[PHASE 3] Applying optimizations...');
    const results = applyOptimizations(analysis, CONFIG, startTime);
    
    // Phase 4: Report
    Logger.log('\n[PHASE 4] Generating report...');
    sendSummaryEmail(results, CONFIG);
    
    logScriptEnd(startTime);
    
  } catch (error) {
    logError(error, CONFIG);
    throw error;
  }
}

function fetchCampaignPerformance(days) {
  const dateRange = getDateRange(days);
  
  const query = `
    SELECT 
      campaign.id,
      campaign.name,
      campaign.status,
      metrics.cost_micros,
      metrics.conversions,
      metrics.clicks,
      metrics.impressions
    FROM campaign
    WHERE segments.date BETWEEN "${dateRange.start}" AND "${dateRange.end}"
      AND campaign.status = 'ENABLED'
      AND metrics.impressions > 0
    ORDER BY metrics.cost_micros DESC
    LIMIT 10000
  `;
  
  const campaigns = [];
  const report = AdsApp.report(query);
  const rows = report.rows();
  
  while (rows.hasNext()) {
    const row = rows.next();
    campaigns.push({
      id: row['campaign.id'],
      name: row['campaign.name'],
      cost: convertMicros(row['metrics.cost_micros']),
      conversions: convertMicros(row['metrics.conversions']),
      clicks: row['metrics.clicks'],
      impressions: row['metrics.impressions'],
      cpa: calculateCPA(
        convertMicros(row['metrics.cost_micros']),
        convertMicros(row['metrics.conversions'])
      )
    });
  }
  
  return campaigns;
}

function analyzeCampaigns(campaigns, config) {
  const toPause = [];
  const toReduceBids = [];
  
  campaigns.forEach(campaign => {
    // Identify campaigns to pause (high spend, no conversions)
    if (campaign.cost > config.MIN_SPEND_THRESHOLD && campaign.conversions === 0) {
      toPause.push(campaign);
    }
    
    // Identify campaigns with high CPA
    if (campaign.conversions > 0 && campaign.cpa > config.HIGH_CPA_THRESHOLD) {
      toReduceBids.push(campaign);
    }
  });
  
  return { toPause, toReduceBids };
}

function applyOptimizations(analysis, config, startTime) {
  const results = {
    paused: 0,
    bidReduced: 0,
    errors: []
  };
  
  const MAX_TIME = config.MAX_EXECUTION_TIME;
  
  // Pause low performers
  analysis.toPause.forEach(campaign => {
    if (new Date().getTime() - startTime.getTime() > MAX_TIME) {
      Logger.log('⚠️ Approaching timeout, stopping optimizations');
      return;
    }
    
    try {
      const campaignObj = AdsApp.campaigns()
        .withIds([campaign.id])
        .get()
        .next();
      
      campaignObj.pause();
      results.paused++;
      Logger.log(`Paused: ${campaign.name} ($${campaign.cost.toFixed(2)}, 0 conv)`);
      
    } catch (error) {
      results.errors.push({ campaign: campaign.name, error: error.message });
    }
  });
  
  // Reduce bids for high CPA campaigns
  analysis.toReduceBids.forEach(campaign => {
    if (new Date().getTime() - startTime.getTime() > MAX_TIME) {
      return;
    }
    
    try {
      const campaignObj = AdsApp.campaigns()
        .withIds([campaign.id])
        .get()
        .next();
      
      // Get current bid and reduce by percentage
      const adGroups = campaignObj.adGroups().get();
      while (adGroups.hasNext()) {
        const adGroup = adGroups.next();
        const currentBid = adGroup.bidding().getCpc();
        const newBid = currentBid * (1 - config.BID_REDUCTION_PERCENT);
        adGroup.bidding().setCpc(newBid);
      }
      
      results.bidReduced++;
      Logger.log(`Reduced bids: ${campaign.name} (CPA: $${campaign.cpa.toFixed(2)})`);
      
    } catch (error) {
      results.errors.push({ campaign: campaign.name, error: error.message });
    }
  });
  
  return results;
}

function sendSummaryEmail(results, config) {
  const subject = `Google Ads Optimization Report - ${new Date().toLocaleDateString()}`;
  
  const body = `
Campaign Optimization Summary
=============================

Actions Taken:
- Campaigns paused: ${results.paused}
- Campaigns with bid reductions: ${results.bidReduced}
- Errors encountered: ${results.errors.length}

${results.errors.length > 0 ? '\nErrors:\n' + results.errors.map(e => `- ${e.campaign}: ${e.error}`).join('\n') : ''}

Script executed successfully.
  `;
  
  MailApp.sendEmail(config.EMAIL_RECIPIENT, subject, body);
  Logger.log('✓ Summary email sent');
}

// Helper functions
function convertMicros(micros) {
  return micros / 1000000;
}

function calculateCPA(cost, conversions) {
  return conversions > 0 ? cost / conversions : 0;
}

function getDateRange(daysAgo) {
  const endDate = new Date();
  const startDate = new Date();
  startDate.setDate(startDate.getDate() - daysAgo);
  
  const timezone = AdsApp.currentAccount().getTimeZone();
  
  return {
    start: Utilities.formatDate(startDate, timezone, 'yyyy-MM-dd'),
    end: Utilities.formatDate(endDate, timezone, 'yyyy-MM-dd')
  };
}

function logScriptStart(startTime) {
  Logger.log('=================================');
  Logger.log('Campaign Performance Optimizer v2.1');
  Logger.log(`Start: ${startTime.toISOString()}`);
  Logger.log(`Account: ${AdsApp.currentAccount().getName()}`);
  Logger.log('=================================');
}

function logScriptEnd(startTime) {
  const endTime = new Date();
  const duration = (endTime - startTime) / 1000;
  Logger.log('\n=================================');
  Logger.log('✅ Script completed successfully');
  Logger.log(`Duration: ${duration.toFixed(2)}s`);
  Logger.log('=================================');
}

function logError(error, config) {
  Logger.log('\n❌ SCRIPT FAILED');
  Logger.log(`Error: ${error.message}`);
  Logger.log(`Stack: ${error.stack}`);
  
  MailApp.sendEmail({
    to: config.EMAIL_RECIPIENT,
    subject: 'Google Ads Script Error',
    body: `Script failed with error:\n\n${error.message}\n\nStack trace:\n${error.stack}`
  });
}
```

---

## 10. Comments & Documentation

### When to Comment
```typescript
// ❌ BAD: Comments explaining WHAT (code already shows this)
// Loop through users
users.forEach(user => {
  // Send email to user
  sendEmail(user);
});

// ✅ GOOD: Comments explaining WHY
// Email provider rate limit: 100 emails/minute
// Batching prevents 429 errors and reduces API costs by 60%
await sendEmailsInBatches(users, { batchSize: 100 });

// ✅ GOOD: Explain non-obvious business logic
// Tax calculation follows tiered marginal rates per IRS Publication 17
// First $50k at 10%, remainder at 22%
const tax = income <= 50000 
  ? income * 0.10 
  : (50000 * 0.10) + ((income - 50000) * 0.22);

// ✅ GOOD: Explain workarounds
// Safari <14 doesn't support lookbehind regex
// Using older pattern for compatibility until Safari 14 adoption >95%
const pattern = isSafari13 
  ? /(\w+)-pattern/ 
  : /(?<=\w+)-pattern/;

// ✅ GOOD: Document Google Ads Script constraints
// GAQL queries have 30-minute timeout limit
// Using LIMIT 10000 and pagination to stay under 27-minute safe threshold
const query = `SELECT campaign.id FROM campaign LIMIT 10000`;
```

---

**End of SPEC.md**

**This file is loaded on-demand. Reference specific sections as needed during code writing.**

**For Google Ads Scripts:** Section 9 is MANDATORY - load it FIRST before writing any GAds code.
