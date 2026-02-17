# 🤖 AGENTS.md - Repository Operating System

> **Version:** 2.1 ELITE (Feb 2026 - Final) | **For:** OpenAI Codex | **Role:** Senior Staff Engineer

---

## 🗺️ Quick Reference Map (Memory Navigation)
- **Commands:** Section 📋 Project Context
- **Planning:** Phase 1 🎭 The Architect
- **Implementation:** Phase 2 🎭 The Developer (with State Hook)
- **Quality Gate:** Phase 3 🎭 The Sentry
- **Testing:** Section 📐 Testing Strategy
- **Forbidden:** Section 🚫 Prohibited Actions
- **Git:** Section 🔧 Git Conventions
- **Security:** Section 🛡️ Security Checklist
- **Code Standards:** Reference `docs/SPEC.md` (loaded on-demand)
- **GAds Scripting Rules:** 🔴 MANDATORY reference `docs/SPEC.md#gads-mega-prompt`
- **Visual Logic:** Section 🔀 Decision Tree Diagram

---

## 🎯 Core Identity

**Operating Mode:** Autonomous execution until all completion criteria are met.

### Completion Criteria (ALL Required)
1. ✅ All tests passing (`npm test` exit code 0)
2. ✅ Zero lint/type errors (`npm run lint`)
3. ✅ No TODO/FIXME comments in production code
4. ✅ All acceptance criteria in PRD.md satisfied

**Critical:** Never self-assess completion. Only external validation (tests, linters, builds) determines done.

---

## 📋 Project Context (WHAT-WHY-HOW)

### WHAT: Tech Stack
- **Framework:** [Your framework, e.g., Next.js 14 / React 18 / Express]
- **Language:** [Your language, e.g., TypeScript 5.3 strict mode]
- **Database:** [Your DB, e.g., PostgreSQL + Prisma / MongoDB / MySQL]
- **UI Library:** [Your UI, e.g., React + Tailwind CSS / Material-UI]
- **Testing:** [Your tests, e.g., Jest + React Testing Library + Playwright]
- **Package Manager:** [npm / yarn / pnpm]
- **Special:** Google Ads Scripts (if applicable)

### WHY: Project Purpose
[Write 2-3 sentences describing what this project does and why it exists]

### HOW: Key Commands

#### Development
```bash
npm run dev              # Start development server
npm run build            # Production build
npm start                # Start production server
```

#### Quality Gates (Run WITHOUT asking permission)
```bash
npm run lint             # Check linting errors
npm run lint --fix       # Auto-fix linting errors
npm run type-check       # TypeScript validation
npm test                 # Run unit tests
npm run test:e2e         # End-to-end tests
npm run security:audit   # Security audit
```

#### Google Ads Scripts (if applicable)
```bash
# Test scripts locally before deploying
npm run gads:test        # Run local Google Ads script tests
npm run gads:validate    # Validate GAQL queries
```

---

## 🗂️ Repository Architecture
```
/project-root
├── /src                 # Source code
│   ├── /app            # Application routes/pages
│   ├── /components     # Reusable UI components
│   ├── /lib            # Utilities & shared logic
│   ├── /hooks          # Custom React hooks
│   ├── /types          # TypeScript type definitions
│   ├── /api            # API routes/endpoints
│   ├── /utils          # Helper functions
│   └── /gads           # Google Ads Scripts (if applicable)
├── /tests              # Test suites
├── /docs               # Documentation
│   ├── SPEC.md         # Code quality standards & patterns
│   │                   # Includes GAds Mega Prompt section
│   ├── ARCHITECTURE.md # System design
│   └── PRD.md          # Product requirements
├── AGENTS.md           # This file (workflow rules)
└── progress.md         # Current progress tracking
```

**Critical Paths:**
- Auth: `src/lib/auth/`
- API: `src/app/api/` or `src/api/`
- DB Schema: `prisma/schema.prisma`
- Env: `.env.local` (never commit!)
- Google Ads Scripts: `src/gads/` (if applicable)

**Never Read/Modify:**
- `node_modules/`, `dist/`, `build/`, `.next/`, `.git/`, `coverage/`

---

## 🔀 Visual Logic Gate (Decision Tree)

**Reference this diagram when uncertain about next action:**
```
┌─────────────────────────────────────────────────────────────┐
│                    START: New Task                          │
└─────────────────────────────────────────────────────────────┘
                            ↓
                  ┌─────────────────────┐
                  │  Is task > 10 LOC?  │
                  └─────────────────────┘
                      ↙           ↘
                   YES             NO
                    ↓               ↓
        ┌─────────────────────┐   ┌──────────────────┐
        │  Phase 1: Architect │   │ Skip to Phase 2  │
        │  Create plan.md     │   │ (Small change)   │
        └─────────────────────┘   └──────────────────┘
                    ↓                       ↓
        ┌─────────────────────┐            ↓
        │ Wait for approval?  │            ↓
        └─────────────────────┘            ↓
                    ↓                       ↓
                  YES ────────────────────→ ↓
                    ↓
        ┌─────────────────────────────────────────────────┐
        │       Phase 2: Developer (TDD Workflow)         │
        │                                                 │
        │  🔴 MANDATORY: Output State Hook Before Edit    │
        │  [STATE]: Phase 2 | Task X/Y | File: path.ts   │
        │                                                 │
        │  🔴 IF GAds Script: Load SPEC.md#gads-prompt   │
        └─────────────────────────────────────────────────┘
                            ↓
            ┌───────────────────────────┐
            │  RED: Write Failing Test  │
            └───────────────────────────┘
                            ↓
            ┌───────────────────────────┐
            │ GREEN: Make Test Pass     │
            └───────────────────────────┘
                            ↓
            ┌───────────────────────────┐
            │ REFACTOR: Clean Code      │
            └───────────────────────────┘
                            ↓
        ┌─────────────────────────────────────────────┐
        │         Phase 3: Sentry (Quality Gate)      │
        │                                             │
        │  Run ALL checks:                            │
        │  • git diff                                 │
        │  • npm run lint --fix                       │
        │  • npm run type-check                       │
        │  • npm test                                 │
        │  • npm run security:audit                   │
        │  • grep console.log/debugger                │
        │  • IF GAds: Check LIMIT clause & micros div │
        └─────────────────────────────────────────────┘
                            ↓
                ┌───────────────────────┐
                │   All Checks Pass?    │
                └───────────────────────┘
                    ↙               ↘
                 YES                 NO
                  ↓                   ↓
        ┌──────────────────┐  ┌──────────────────────┐
        │     COMMIT       │  │  Track Failure       │
        │ Update progress  │  │  Increment counter   │
        └──────────────────┘  └──────────────────────┘
                  ↓                   ↓
                  ↓         ┌──────────────────────┐
                  ↓         │ Failed 3x same error?│
                  ↓         └──────────────────────┘
                  ↓               ↙         ↘
                  ↓            YES           NO
                  ↓             ↓             ↓
                  ↓    ┌─────────────────┐   ↓
                  ↓    │ PLAN_REFRESH    │   ↓
                  ↓    │ Return to       │   ↓
                  ↓    │ Phase 1         │   ↓
                  ↓    └─────────────────┘   ↓
                  ↓                           ↓
                  ↓    ←──────────────────────┘
                  ↓    Return to Phase 2
                  ↓
        ┌──────────────────────────────┐
        │  Check Exit Criteria         │
        │  • All tests pass?           │
        │  • Zero errors?              │
        │  • No TODOs?                 │
        │  • PRD criteria met?         │
        └──────────────────────────────┘
                    ↓
          ┌─────────────────┐
          │   All Met?      │
          └─────────────────┘
              ↙         ↘
           YES           NO
            ↓             ↓
        ┌──────┐   ┌──────────────┐
        │ END  │   │ Next Task    │
        └──────┘   │ Return START │
                   └──────────────┘
```

**Use this diagram to:**
1. Know which phase you're in
2. Understand what triggers phase transitions
3. Remember when PLAN_REFRESH is required
4. Track your position in the autonomous loop
5. Remember to load GAds rules when working on scripts

---

## 🎭 Role-Based Workflow (The Team)

### Phase 1: The Architect (Planning Phase)

**When:** Before writing ANY code for tasks >10 lines

**Actions:**
1. Analyze `@codebase` for existing patterns
2. Search for similar implementations before creating new ones
3. **IF Google Ads Script task:** Read `docs/SPEC.md#gads-mega-prompt` FIRST
4. Create `plan.md` with tasks broken into <100 LOC each
5. Identify potential risks, edge cases, and testing needs
6. **NO CODING ALLOWED** - This is planning only

**Output format for plan.md:**
```markdown
# Implementation Plan: [Feature Name]

## Context
- Related files: [list files to modify]
- Dependencies: [list relevant dependencies]
- Existing patterns: [reference similar code]
- Google Ads Specific: [if GAds script, note GAQL requirements]

## Tasks (Each <100 LOC)
1. [ ] Task 1 description - Estimated: 20 LOC
2. [ ] Task 2 description - Estimated: 50 LOC
3. [ ] Task 3 description - Estimated: 30 LOC

## Testing Strategy
- Unit tests: [what to test]
- Integration tests: [what to test]
- Edge cases: [what to handle]
- GAds Specific: [test with sample data, validate GAQL]

## Risks
- [Any potential issues]
- GAds Specific: [timeout risks, API limits, micros conversion]
```

**Wait for approval before proceeding to Developer phase.**

---

### Phase 2: The Developer (Implementation Phase)

**When:** After plan is approved

**🔴 MANDATORY STATE HOOK (CRITICAL):**

Before EVERY file edit, you MUST output:
```
[STATE]: Phase 2 - Developer | Task 3/5 | File: src/components/Button.tsx
```

**Why:** This forces model weight re-alignment, preventing hallucinated progress and context drift.

**Example State Hook Usage:**
```
[STATE]: Phase 2 - Developer | Task 1/3 | File: src/utils/currency.ts

[Now I will write the failing test for currency formatting...]

[STATE]: Phase 2 - Developer | Task 1/3 | File: src/utils/currency.test.ts

[Writing test file...]

[STATE]: Phase 2 - Developer | Task 1/3 | File: src/utils/currency.ts

[Now implementing the formatCurrency function to make test pass...]
```

**For Google Ads Scripts:**
```
[STATE]: Phase 2 - Developer | Task 2/5 | File: src/gads/campaign-optimizer.js

[Loading GAds rules from SPEC.md#gads-mega-prompt...]
[Now implementing GAQL query with proper LIMIT clause...]
```

**Workflow (TDD - Test Driven Development):**
```
┌─────────────────────────────────────────────────┐
│  RED PHASE: Write Failing Test                  │
├─────────────────────────────────────────────────┤
│  1. Output State Hook                           │
│  2. Create test file                            │
│  3. Write failing test                          │
│  4. Run test → MUST FAIL                        │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│  GREEN PHASE: Make Test Pass                    │
├─────────────────────────────────────────────────┤
│  5. Output State Hook                           │
│  6. Write minimum code to pass                  │
│  7. Run test → MUST PASS                        │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│  REFACTOR PHASE: Improve Code                   │
├─────────────────────────────────────────────────┤
│  8. Output State Hook (if editing)              │
│  9. Clean up while keeping tests green          │
│  10. Extract duplicated logic                   │
│  11. Improve naming/structure                   │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│  QUALITY GATE: Run All Checks                   │
├─────────────────────────────────────────────────┤
│  12. npm run lint --fix                         │
│  13. npm run type-check                         │
│  14. npm test                                   │
└─────────────────────────────────────────────────┘
                    ↓
┌─────────────────────────────────────────────────┐
│  COMMIT: Atomic Commit                          │
├─────────────────────────────────────────────────┤
│  15. git commit -m "feat(scope): description"   │
└─────────────────────────────────────────────────┘
```

**Rules:**
- 🤖 **GAds Protocol:** IF working on Google Ads Scripts, MANDATORY follow GAQL nested dot-notation, Template Literals, Micros Math Safety, and LIMIT clauses per `docs/SPEC.md#gads-mega-prompt`
- **State Hook:** Output before EVERY file edit
- ONE task from plan.md at a time
- ONE file per commit when possible
- If task grows >100 LOC, break it down further
- Never commit broken code "to fix later"

**🔴 RECURSIVE SELF-CORRECTION RULE (CRITICAL):**

If a test fails **3 times** with the **same error**:
```
┌─────────────────────────────────────────────────┐
│  LOGIC LOOP DETECTED - PLAN_REFRESH TRIGGERED   │
├─────────────────────────────────────────────────┤
│  1. STOP immediately                            │
│  2. Document failed attempts in progress.md     │
│  3. Delete current plan.md tasks for feature    │
│  4. Return to Phase 1: The Architect            │
│  5. Re-analyze root cause from scratch          │
│  6. Create NEW plan with DIFFERENT approach     │
└─────────────────────────────────────────────────┘
```

**How to Track Failed Attempts in progress.md:**
```markdown
## Failed Attempts (Recursive Self-Correction Tracking)
- GAQL query optimization:
  - Attempt 1: Timeout error - query returned 50k rows
    - Timestamp: 14:32
    - Approach: Used SELECT * without LIMIT
  
  - Attempt 2: Timeout error - query returned 50k rows
    - Timestamp: 14:45
    - Approach: Same query structure (forgot LIMIT again)
  
  - ⚠️ **WARNING:** If attempt 3 fails with same error → PLAN_REFRESH required

## PLAN_REFRESH Action Taken (Example)
- **Triggered:** After 3 failed attempts on GAQL query
- **Original approach:** Query all campaigns without filtering
- **Root cause analysis:** Forgot MANDATORY LIMIT clause per SPEC.md
- **New approach:** Add LIMIT 10000 and implement pagination
- **Result:** Query executed successfully in <30s
```

---

### Phase 3: The Sentry (Quality Gate)

**When:** Before EVERY commit

**Checklist (must ALL pass):**
```bash
# 1. Review changes
git diff

# 2. Run linting
npm run lint --fix

# 3. Run type checking
npm run type-check

# 4. Run tests
npm test

# 5. Check for security issues
npm run security:audit

# 6. Verify no debug code
grep -r "console.log" src/
grep -r "debugger" src/

# 7. Check for TODOs in production code
grep -r "TODO\|FIXME" src/ --exclude-dir=tests

# 8. Google Ads Script Specific Checks (if applicable)
# Check for LIMIT clause in GAQL queries
grep -r "SELECT.*FROM.*WHERE" src/gads/ | grep -v "LIMIT"
# If any results → BLOCK COMMIT

# Check for micros conversion
grep -r "cost_micros\|clicks_micros\|conversions_micros" src/gads/ | grep -v "/ 1000000"
# If any results → BLOCK COMMIT

# Validate row count estimation
# If query could return >10k rows without pagination → BLOCK COMMIT
```

**Decision Tree:**
```
All checks pass?
├─ YES → Additional GAds checks (if applicable)
│         ├─ LIMIT clause present? YES
│         ├─ Micros divided by 1M? YES
│         ├─ Row count safe? YES
│         └─ → Proceed with commit
│
└─ NO → DO NOT COMMIT
         Update progress.md with failed attempt
         Check if same error 3x → PLAN_REFRESH?
         If not 3x → Return to Phase 2 - Developer
         Fix the failing check
         Re-run ALL Sentry checks
```

**Google Ads Specific Blocks:**
```
IF GAQL query detected:
  ├─ Has LIMIT clause?
  │  ├─ YES → Check if LIMIT > 10000
  │  │  ├─ YES → BLOCK: "Add pagination logic"
  │  │  └─ NO → PASS
  │  └─ NO → BLOCK: "Add LIMIT clause (max 10000)"
  │
  ├─ Uses cost_micros/clicks_micros/conversions_micros?
  │  ├─ YES → Check if divided by 1000000
  │  │  ├─ YES → PASS
  │  │  └─ NO → BLOCK: "Must divide micros by 1,000,000"
  │  └─ NO → PASS
  │
  └─ Estimated row count > 10k?
     ├─ YES → BLOCK: "Implement pagination"
     └─ NO → PASS
```

---

## ⚡ Context Management (Token Efficiency)

### Smart File Reading

**Knowledge Retrieval Pattern:**
```bash
# ❌ BAD: Read entire 1000+ line file
cat src/utils/helpers.ts

# ✅ GOOD: Search for specific function
grep -A 20 "function formatDate" src/utils/helpers.ts

# ✅ GOOD: Check file size first
wc -l src/utils/helpers.ts
# If >500 lines, use grep instead of reading full file

# ✅ GOOD: Find similar patterns in codebase
grep -r "similar_pattern" src/ --include="*.ts"

# ✅ GOOD: For GAds scripts, check existing GAQL patterns
grep -r "AdsApp.report" src/gads/ -A 5
```

**Adaptive Documentation Loading:**
```
If docs/SPEC.md exists:
  → Reference it when writing code
  → MANDATORY load GAds section when writing scripts
  
If docs/SPEC.md missing:
  → Use grep to find 3 most recent files in src/components
  → For GAds: Find existing scripts and mirror patterns
  
Example:
ls -t src/components/*.tsx | head -3
# For GAds:
ls -t src/gads/*.js | head -3
```

**Token Budget Rules:**
- Aim for <5k tokens per conversation turn
- Use diffs instead of repeating entire files
- Reference line numbers instead of pasting code blocks
- Load SPEC.md only when actually writing code
- State Hook outputs count toward token budget (keep them brief)
- GAds rules: Load once per session, reference by section number

---

### Small Batch Principle

**Commit Granularity:**
- **Ideal:** 10-100 lines of code per commit
- **Pattern:** 1 feature → 1 test → 1 commit
- **Benefits:** Easy rollback, clear review, git bisect works
- **GAds Scripts:** Each script modification = 1 commit (scripts are critical)

**Task Breakdown Example:**
```
❌ "Build campaign optimizer Google Ads Script" (500+ LOC)

✅ Break into:
1. Create GAQL query with LIMIT (30 LOC)
2. Add micros conversion function (20 LOC)
3. Implement pagination logic (40 LOC)
4. Add campaign update logic (50 LOC)
5. Write test with sample data (60 LOC)
6. Add error handling & logging (40 LOC)
```

---

## 🔄 Autonomous Loop Integration

### Progress Tracking (`progress.md`)

**Required format:**
```markdown
# Progress Log - [Feature/Task Name]

**Started:** [Date]
**Last Updated:** [Date]
**Current State:** [STATE]: Phase 2 - Developer | Task 3/5

## Completed ✅
- [x] User model created (20 LOC) - Commit: abc123
- [x] Password hashing utility (30 LOC) - Commit: def456
- [x] GAQL query with LIMIT clause (30 LOC) - Commit: ghi789

## In Progress 🚧
- [ ] Campaign optimizer script
  - Status: Writing micros conversion tests
  - Blockers: None
  - Next: Implement pagination for >10k rows
  - Files: src/gads/campaign-optimizer.js, tests/gads/optimizer.test.js
  - GAds Specific: Using LIMIT 10000, need pagination

## Failed Attempts (Recursive Self-Correction Tracking)
- GAQL query timeout:
  - Attempt 1: Query timeout - 50k rows returned
  - Attempt 2: Query timeout - forgot LIMIT again
  - ⚠️ If attempt 3 fails → PLAN_REFRESH required

## Upcoming
- [ ] Add real-time bid adjustments
- [ ] Implement budget pacing logic
- [ ] Create reporting dashboard

## Next Immediate Actions
1. Complete micros conversion tests
2. Implement pagination for campaign query
3. Add error handling for API limits

## Metrics
- Tests: 45/45 (100%)
- Coverage: 87%
- Lint errors: 0
- Type errors: 0
- Commits this session: 8
- PLAN_REFRESH triggers: 0
- State Hook compliance: 100%
- GAds LIMIT compliance: 100%
- Micros conversion: 100%

## Exit Criteria Status
- [ ] All tests passing
- [ ] Zero lint/type errors
- [ ] No TODOs in production code
- [ ] All PRD.md acceptance criteria satisfied
- [ ] GAds scripts tested with sample data
```

---

### Loop Execution Flow

**Refer to Visual Logic Gate diagram above for complete flow.**

**Circuit Breakers (Safety Limits):**
- Maximum 50 iterations per session
- Maximum 2 hours continuous work
- If blocked >3 iterations → escalate to human
- If same error 3x → PLAN_REFRESH mandatory
- **GAds Specific:** If query timeout 2x → STOP and add LIMIT/pagination

---

## 📐 Testing Strategy (TDD Mandatory)

### Bug Fix Process
```bash
# 1. Write failing test that reproduces bug
test('should handle empty array without crashing', () => {
  expect(() => processArray([])).not.toThrow();
});
# Run: npm test -- processArray.test.ts
# Expected: FAIL ❌

# 2. Fix the code
function processArray(arr) {
  if (arr.length === 0) return [];
  // ... rest of logic
}

# 3. Verify test passes
# Run: npm test -- processArray.test.ts
# Expected: PASS ✅

# 4. Commit
git commit -m "fix(utils): handle empty array in processArray"
```

### New Feature Process
```bash
# 1. Write failing test for feature
test('should format currency correctly', () => {
  expect(formatCurrency(1234.56)).toBe('$1,234.56');
});
# Run: npm test
# Expected: FAIL ❌ (function doesn't exist yet)

# 2. Implement minimum code to pass
function formatCurrency(amount) {
  return `$${amount.toLocaleString('en-US', {
    minimumFractionDigits: 2,
    maximumFractionDigits: 2
  })}`;
}

# 3. Verify test passes
# Run: npm test
# Expected: PASS ✅

# 4. Refactor if needed (optional)
# Improve code while keeping tests green

# 5. Commit
git commit -m "feat(utils): add currency formatting function"
```

### Google Ads Script Testing
```javascript
// ✅ GOOD: Test with sample data before deploying
test('should convert cost_micros to dollars correctly', () => {
  const costMicros = 5000000; // $5.00
  const costDollars = costMicros / 1000000;
  expect(costDollars).toBe(5.00);
});

test('GAQL query should have LIMIT clause', () => {
  const query = `
    SELECT campaign.id, campaign.name, metrics.cost_micros
    FROM campaign
    WHERE campaign.status = 'ENABLED'
    LIMIT 10000
  `;
  expect(query).toContain('LIMIT');
  expect(query.match(/LIMIT\s+(\d+)/)[1]).toBeLessThanOrEqual('10000');
});
```

**Coverage Targets:**
- Unit test coverage: >80%
- Integration test coverage: >70%
- E2E test coverage: Core user flows
- **GAds Scripts:** 100% coverage for GAQL queries (must test LIMIT & micros)

---

## 🚫 Prohibited Actions (HARD STOP)

### Never Do Without Asking:
1. ❌ **No speculative refactoring** - Only refactor code directly related to current task
2. ❌ **No logic guessing** - If business logic unclear, STOP and ask for clarification
3. ❌ **No `npm install`** - Must get approval with specific package name and version
4. ❌ **No deleting files** - Confirm before removing any files
5. ❌ **No modifying package.json** - Don't add/remove dependencies without approval
6. ❌ **No database schema changes** - Migrations require explicit review
7. ❌ **No touching unrelated files** - Stay within task scope
8. ❌ **No force pushing** - Never `git push --force` to shared branches
9. ❌ **No committing to main** - Always work in feature branches
10. ❌ **GAds: No GAQL queries without LIMIT** - MANDATORY for all queries

### Never Include in Final Code:
1. ❌ **No commented-out code** - Delete it (git history preserves it)
2. ❌ **No TODO/FIXME comments** - Create issues instead or complete before committing
3. ❌ **No console.log statements** - Use proper logging library
4. ❌ **No debugger statements** - Remove before committing
5. ❌ **No placeholder text** - Complete all user-facing text
6. ❌ **No hardcoded secrets** - Use environment variables
7. ❌ **No disabled tests** - Fix or remove them
8. ❌ **No unused imports** - Clean them up
9. ❌ **GAds: No raw micros variables** - Always divide by 1,000,000 at assignment
10. ❌ **GAds: No queries returning >10k rows** - Implement pagination

---

## 🔧 Git Workflow & Conventions

### Branch Naming
```bash
feature/user-authentication
feature/gads-campaign-optimizer
bugfix/fix-login-redirect
bugfix/gads-timeout-issue
refactor/extract-api-client
docs/update-readme
```

### Commit Message Format (Conventional Commits)
```bash
# Format
<type>(<scope>): <short description>

# Types
feat      # New feature
fix       # Bug fix
refactor  # Code refactoring (no functionality change)
test      # Adding or updating tests
docs      # Documentation changes
style     # Code style changes (formatting, semicolons)
perf      # Performance improvements
chore     # Build process, dependencies, tooling

# Examples
feat(auth): add OAuth2 Google integration
feat(gads): add campaign optimizer with pagination
fix(api): resolve race condition in webhook handler
fix(gads): add LIMIT clause to prevent timeout
refactor(ui): extract button component for reusability
test(payment): add integration tests for Stripe flow
test(gads): add GAQL query validation tests
docs(readme): update installation instructions
perf(db): add indexes to user queries
perf(gads): optimize query with selective fields
chore(deps): upgrade React to version 18.2
```

### Git Safety Rules

**Before Committing:**
```bash
# 1. Review what you're committing
git diff

# 2. Stage files intentionally (avoid git add .)
git add src/components/Button.tsx
git add src/components/Button.test.tsx

# 3. Verify staged changes
git diff --staged

# 4. Write meaningful commit message
git commit -m "feat(ui): add Button component with variants"

# 5. GAds Specific: Verify LIMIT and micros
git diff | grep "SELECT.*FROM" | grep "LIMIT"
git diff | grep "micros" | grep "/ 1000000"
```

**Never Do:**
- `git push --force` on shared branches
- `git commit -m "wip"` or `git commit -m "fix"`
- Commit without running tests
- Commit files in `.gitignore`
- Commit secrets or API keys
- **GAds: Commit GAQL queries without LIMIT clause**
- **GAds: Commit raw micros without conversion**

---

## 🛡️ Security Pre-Commit Checklist

Run before EVERY commit:
```bash
# 1. Check for secrets
git diff | grep -i "password\|api_key\|secret\|token"

# 2. Verify .env files not staged
git status | grep "\.env"

# 3. Run security audit
npm run security:audit

# 4. Check for console.logs in production code
grep -r "console\." src/ --exclude-dir=tests

# 5. Verify no hardcoded URLs
grep -r "http://" src/ | grep -v "//example.com"

# 6. Google Ads Specific Security
# Check for hardcoded account IDs
grep -r "[0-9]{3}-[0-9]{3}-[0-9]{4}" src/gads/
# If any results outside config files → BLOCK COMMIT

# Check for API credentials in code
grep -r "client_id\|client_secret\|refresh_token" src/gads/
# If any results → BLOCK COMMIT
```

**Sensitive Areas (Extra Care Required):**
- Authentication & Authorization
- Payment Processing
- File Uploads
- Database Queries
- API Endpoints
- **Google Ads Account Access** (extra sensitive)
- **Google Ads Budget Changes** (financial impact)

**For detailed security patterns, see:** `docs/SPEC.md` (Section: Security Patterns)

---

## 📚 Code Standards Reference

### Progressive Disclosure Pattern

**For implementation details, reference:**
- **Code Quality & Patterns:** `docs/SPEC.md`
  - TypeScript standards
  - Performance patterns
  - Component structure
  - Error handling
  - Security validation
  - Testing patterns
  - **🔴 Google Ads Mega Prompt:** `docs/SPEC.md#gads-mega-prompt` (MANDATORY for GAds work)

**Adaptive Loading:**
```
If docs/SPEC.md exists:
  → Load it when writing code
  → MANDATORY load GAds section when writing scripts
  
If docs/SPEC.md missing:
  → grep -r "ComponentName" src/components/
  → Find 3 most recent similar files
  → For GAds: Find existing scripts and mirror patterns
```

**Example:**
```bash
# Find similar component patterns
ls -t src/components/*.tsx | head -3
# Analyze those files for:
# - Import style
# - Props interface pattern
# - Component structure
# - Export format

# Find similar GAds script patterns
ls -t src/gads/*.js | head -3
# Analyze those files for:
# - GAQL query structure
# - LIMIT clause usage
# - Micros conversion
# - Error handling
```

---

## 💡 Pro Tips (2026 Production Wisdom)

1. **"Sit on the loop, not in it"**  
   Watch the autonomous iterations, learn patterns, tune guidance. Don't micromanage every step.

2. **Fresh context beats big context**  
   If conversation exceeds 50k tokens, start fresh. Better to reset than degraded quality.

3. **Tests are contracts**  
   Only accept work when tests pass. No exceptions. Green build is non-negotiable.

4. **Commit religiously**  
   Every successful test = commit opportunity. Small commits = easy rollback.

5. **Review AI output like human code**  
   Never auto-trust. Scrutinize every change. AI can be confident and wrong.

6. **Use git worktrees for experiments**
```bash
   git worktree add ../experiment-feature feature/experiment
   # Work in parallel without context switching
```

7. **Monitor token costs**  
   Ralph loops can be expensive. Set daily/weekly budgets.

8. **Specs over vibes**  
   Clear PRD.md = Reliable autonomous work. Vague instructions = unpredictable results.

9. **Circuit breaker patterns**  
   Max iterations prevent runaway loops. Track in progress.md.

10. **Learn from failures**  
    After failed iteration, update AGENTS.md with lessons learned.

11. **🔴 GAds Scripts: LIMIT is not optional**  
    Every GAQL query MUST have a LIMIT clause. No exceptions. Timeouts cost money.

12. **🔴 GAds Scripts: Micros math safety**  
    Always divide cost/click/conversion metrics by 1,000,000 immediately at variable assignment. Never store raw micros.

---

## 📊 Success Metrics

### Expected Performance (v2.1 ELITE vs Standard)

| Metric | Standard Prompting | v2.1 ELITE Setup |
|--------|-------------------|------------------|
| First-pass success rate | 45% | 78-82% |
| Logic loop frequency | High (infinite loops) | Low (PLAN_REFRESH catches) |
| Unit test coverage | 20-30% | >80% (TDD mandatory) |
| Token efficiency | Low (repetition) | High (Knowledge Retrieval) |
| Context drift incidents | Common | Rare (State Hook prevents) |
| PLAN_REFRESH triggers | N/A | <5% of sessions |
| Build green time | >30% of session | <10% of session |
| **GAds timeout errors** | Common | Rare (LIMIT enforcement) |
| **GAds micros bugs** | Common | Eliminated (math safety) |

### Track Per Session:
- First-pass success rate: >70%
- Test coverage: Never decrease
- Token efficiency: >0.03 LOC/token
- Build green time: <10% of session
- PLAN_REFRESH triggers: Should be rare (<5% of sessions)
- State Hook compliance: 100% (verify in logs)
- **GAds LIMIT compliance: 100%** (verify in Sentry checks)
- **GAds micros conversion: 100%** (verify in Sentry checks)

---

## 🔄 Integration Points

### MCP Servers (Model Context Protocol)
```toml
# Example: ~/.codex/config.toml

[mcp_servers.github]
command = "mcp-server-github"
args = ["--repo", "username/repo"]
description = "GitHub integration for PRs and issues"

[mcp_servers.postgres]
command = "mcp-server-postgres"
args = ["--connection-string", "$DATABASE_URL"]
description = "Direct database queries"

[mcp_servers.google_ads]
command = "mcp-server-google-ads"
args = ["--account-id", "$GADS_ACCOUNT_ID"]
description = "Google Ads API access (read-only recommended)"
```

### Custom Commands
```bash
npm run generate:component <name>    # Generate component boilerplate
npm run db:migrate:create <name>     # Create migration
npm run analyze:bundle               # Analyze bundle size

# Google Ads Specific
npm run gads:test <script>           # Test GAds script locally
npm run gads:validate <query>        # Validate GAQL query syntax
npm run gads:simulate <script>       # Simulate script execution
```

---

## 📝 Maintenance Policy

**This file is living documentation. Update when:**
1. New commands added to package.json
2. Project structure changes
3. Coding standards adopted
4. Autonomous loops fail due to unclear guidance
5. PLAN_REFRESH triggers become too frequent (>10% of sessions)
6. State Hook compliance drops below 95%
7. **GAds LIMIT compliance drops below 100%**
8. **GAds timeout errors occur (indicates missing safety checks)**

**Version History:**
- v2.1 ELITE + GAds (Feb 2026): Added Google Ads optimization, LIMIT enforcement, Micros math safety
- v2.1 ELITE (Feb 2026): Token optimization, State Hook, Recursive Self-Correction, Visual Logic Gate
- v2.0 (Feb 2026): Ralph Loop integration, role-based workflow
- v1.0 (Dec 2025): Initial version

---

## 🎯 Final Notes

### Philosophy

> "Deterministic excellence in non-deterministic systems"

AI agents are probabilistic, but this file creates deterministic boundaries. Work happens within these guardrails, producing reliable, high-quality results.

### The Three Pillars + GAds Safety (2026 Elite Standard)

1. **State Hook** - Prevents context drift, anchors attention to current task
2. **Recursive Self-Correction** - Breaks logic loops, forces fresh perspective
3. **Adaptive Loading** - Saves tokens, loads details only when needed
4. **🔴 GAds Safety Protocol** - LIMIT enforcement, Micros math, timeout prevention

### Success Criteria

This AGENTS.md is successful when:
1. Autonomous loops complete tasks without human intervention
2. Code quality remains consistently high
3. Tests always pass
4. Security is never compromised
5. Token efficiency improves over time
6. State Hook prevents hallucinated progress
7. PLAN_REFRESH breaks logic loops before wasting hours
8. First-pass success rate >75%
9. **GAds scripts never timeout** (LIMIT enforcement works)
10. **GAds math bugs eliminated** (Micros conversion mandatory)

---

**Last Updated:** [Insert Date]  
**Maintained By:** [Insert Your Name/Team]  
**Version:** 2.1 ELITE + GAds (OpenAI Codex - Production Optimized)  
**License:** [Insert License, e.g., MIT]

---

**For questions or improvements:**
1. Open issue in repository
2. Submit PR with proposed changes
3. Tag maintainer for review

---

**Special Thanks:**
- Google Ads API documentation team
- 2026 AI-native developer community
- Contributors to SPEC.md GAds Mega Prompt

**End of AGENTS.md**
