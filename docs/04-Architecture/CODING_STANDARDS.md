# Acadedx Coding Standards

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the coding standards for Acadedx.

These standards must be followed by:

- Developers
- AI coding agents
- Code reviewers
- QA engineers
- Technical leads
- Solution architects

The objective is to maintain a codebase that is:

- Secure
- Maintainable
- Scalable
- Testable
- Readable
- Consistent
- AI-agent friendly

---

# Core Engineering Principles

Every implementation must follow:

- Clean Architecture
- SOLID principles
- DRY where appropriate
- KISS
- YAGNI
- Composition over inheritance
- Explicit over implicit
- Secure by default
- Testable by design

Do not introduce unnecessary abstraction before there is a real need.

Do not take shortcuts that compromise security, tenant isolation, or long-term maintainability.

---

# Product-Aware Coding Rule

Acadedx is not only a school application.

Code must support the future-ready platform model:

```text
Platform
    ↓
Organization
    ↓
Institution
    ↓
Academic Structure
    ↓
Teachers / Parents / Students
    ↓
AI Learning Engine
```

MVP constraint:

```text
One Organization → One Institution
```

This constraint must be implemented as a business rule, not as a permanent database limitation.

---

# Organization and Institution Rule

Use:

```text
organizationId
institutionId
```

Do not create new future-facing fields named:

```text
schoolId
```

unless the domain is specifically about a school-only UI label or legacy compatibility.

In backend, database, APIs, and shared types, prefer:

```text
Institution
```

instead of:

```text
School
```

A School is an Institution type.

---

# Language Standard

Approved language:

```text
TypeScript
```

Rules:

- Use TypeScript for frontend and backend.
- Avoid plain JavaScript files.
- Enable strict TypeScript mode.
- Avoid `any`.
- Prefer explicit types for public APIs.
- Prefer inferred types only for obvious local variables.

---

# TypeScript Rules

## Avoid Any

Bad:

```typescript
function processUser(data: any) {
  return data.name;
}
```

Good:

```typescript
interface UserInput {
  name: string;
}

function processUser(data: UserInput): string {
  return data.name;
}
```

---

## Use Domain Types

Use shared domain types where appropriate.

Examples:

```typescript
Organization
Institution
User
Homework
Quiz
Subscription
Entitlement
```

Do not duplicate equivalent types across apps and services.

---

## Use Enums Carefully

Use enums or union types for stable controlled values.

Example:

```typescript
type InstitutionType =
  | "SCHOOL"
  | "ACADEMY"
  | "COACHING_INSTITUTE"
  | "COLLEGE"
  | "UNIVERSITY"
  | "TRAINING_CENTRE"
  | "ONLINE_ACADEMY";
```

---

# Naming Standards

## Folders

Use lowercase kebab-case.

Good:

```text
study-planner/
ai-tutor/
admin-portal/
```

Bad:

```text
StudyPlanner/
AI_Tutor/
AdminPortal/
```

---

## React Components

Use PascalCase.

```text
HomeworkCard.tsx
InstitutionSelector.tsx
StudyPlanCalendar.tsx
```

---

## Hooks

Use camelCase and start with `use`.

```text
useAuth.ts
useHomework.ts
useEntitlements.ts
useInstitutionContext.ts
```

---

## Services

Use PascalCase.

```text
HomeworkService.ts
OrganizationService.ts
InstitutionService.ts
EntitlementService.ts
```

---

## Variables

Use camelCase.

```typescript
const currentInstitutionId = "";
const organizationContext = {};
```

---

## Constants

Use UPPER_SNAKE_CASE.

```typescript
const MAX_UPLOAD_SIZE_MB = 20;
const DEFAULT_PAGE_SIZE = 20;
```

---

## Database Fields

Use camelCase in Prisma models.

Use snake_case in PostgreSQL mappings where needed.

Preferred logical fields:

```text
organizationId
institutionId
createdAt
updatedAt
deletedAt
createdBy
updatedBy
```

---

# Frontend Coding Standards

## React

Use functional components only.

Good:

```tsx
export function HomeworkCard() {
  return <div />;
}
```

Do not use class components.

---

## Component Size

Keep components focused.

Guideline:

```text
Maximum 300 lines per component
```

When a component grows too large, split it into:

- Presentational components
- Hooks
- Subcomponents
- Utility functions

---

## Component Responsibility

A component should have one clear responsibility.

Bad:

```text
Dashboard component handles API calls, formatting, charts, permissions, and AI recommendations directly.
```

Good:

```text
Dashboard page composes smaller widgets and uses hooks/services for data.
```

---

## Business Logic in UI

Do not put business logic inside React components.

Bad:

```tsx
if (user.plan === "Student Pro") {
  showPremiumFeature();
}
```

Good:

```tsx
if (entitlements.canUse("homework.unlimited")) {
  showPremiumFeature();
}
```

---

## API Calls

Do not call APIs directly from random components.

Use:

- API client
- Feature service
- TanStack Query hooks

Example:

```typescript
const { data } = useHomeworkHistory();
```

---

## Forms

Use:

```text
React Hook Form
Zod
```

Frontend validation improves user experience only.

Backend validation is still mandatory.

---

## UI Components

Use shared components from:

```text
packages/ui
```

Do not duplicate buttons, inputs, cards, dialogs, or layout components across apps.

---

# Backend Coding Standards

## Framework

Use:

```text
NestJS
```

---

## Module Pattern

Every backend module should follow:

```text
controller
    ↓
service
    ↓
repository
    ↓
database
```

---

## Controller Rules

Controllers must:

- Handle routing
- Apply guards
- Validate input
- Call services
- Return structured responses

Controllers must not contain business logic.

Bad:

```typescript
@Post()
async create(@Body() dto: CreateHomeworkDto) {
  const user = await this.prisma.user.findUnique(...);
  if (user.plan === "Free") ...
}
```

Good:

```typescript
@Post()
async create(@Body() dto: CreateHomeworkDto, @CurrentUser() user: AuthUser) {
  return this.homeworkService.createHomework(user, dto);
}
```

---

## Service Rules

Services own business logic.

Services should:

- Enforce business rules
- Call repositories
- Call other module services through public interfaces
- Emit domain events
- Apply entitlements
- Apply organization and institution context rules

---

## Repository Rules

Repositories own persistence logic.

Repositories should:

- Query database
- Apply common filters
- Respect soft delete
- Respect organization and institution scope where applicable
- Avoid business decisions

---

# DTO Standards

Every API request must use a DTO.

DTOs must include:

- Field types
- Validation decorators
- Clear names
- Optional fields marked clearly

Example:

```typescript
export class CreateInstitutionDto {
  name: string;
  type: InstitutionType;
  organizationId: string;
}
```

---

# Validation Standards

Backend validation is mandatory.

Validate:

- Request body
- Query parameters
- Route parameters
- Uploaded files
- Enum values
- Organization and institution context
- Entitlement access

Invalid input should return a structured validation error.

---

# API Response Standard

Success response:

```json
{
  "success": true,
  "message": "Request completed successfully.",
  "data": {}
}
```

Error response:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request.",
    "details": []
  }
}
```

---

# Error Handling Standards

Never swallow errors.

Bad:

```typescript
try {
  await process();
} catch {}
```

Good:

```typescript
try {
  await process();
} catch (error) {
  this.logger.error({ error }, "Failed to process request");
  throw new AppException("PROCESSING_FAILED", "Processing failed.");
}
```

---

# Error Code Standards

Use stable error codes.

Examples:

```text
VALIDATION_ERROR
AUTH_INVALID_TOKEN
AUTH_FORBIDDEN
RESOURCE_NOT_FOUND
ENTITLEMENT_REQUIRED
USAGE_LIMIT_EXCEEDED
AI_PROVIDER_ERROR
OCR_FAILED
PAYMENT_FAILED
TENANT_SCOPE_VIOLATION
```

Error messages may change.

Error codes should remain stable.

---

# Authentication Standards

Every protected endpoint must require authentication.

Use:

- JWT access tokens
- Refresh tokens
- Server-side token validation
- Session tracking where required

Do not trust frontend authentication state.

---

# Authorization Standards

Authorization must check:

- Role
- Permission
- Organization scope
- Institution scope
- Resource ownership
- Entitlement where applicable

Frontend authorization is for user experience only.

Backend authorization is mandatory.

---

# Tenant Scoping Standards

Every scoped query must include the correct organization or institution context where applicable.

Examples:

```typescript
where: {
  id,
  institutionId: context.institutionId,
}
```

Avoid unscoped queries for tenant-owned data.

Bad:

```typescript
findUnique({ where: { id } })
```

Good:

```typescript
findFirst({
  where: {
    id,
    institutionId: context.institutionId,
  },
})
```

---

# Entitlement Standards

Do not hardcode plan names in feature logic.

Bad:

```typescript
if (user.plan === "Premium") {
  allow();
}
```

Good:

```typescript
await this.entitlementService.assertCanUse(
  user.id,
  "homework.solve"
);
```

Feature access must be driven by:

- Entitlements
- Usage limits
- Feature flags where applicable
- Subscription status

---

# AI Coding Standards

AI provider calls must go through the AI abstraction layer.

Bad:

```typescript
openai.chat.completions.create(...)
```

inside HomeworkService.

Good:

```typescript
this.aiService.generateHomeworkSolution(...)
```

---

# Prompt Standards

Prompts must live in:

```text
packages/prompts
```

Prompts must be:

- Versioned
- Named
- Reviewed
- Reusable
- Testable

Do not hardcode long prompts inside services or controllers.

---

# AI Usage Tracking

Every AI request must track:

- User ID
- Organization ID where applicable
- Institution ID where applicable
- Feature
- Provider
- Model
- Prompt version
- Token usage
- Estimated cost
- Latency
- Success or failure
- Error code where applicable

---

# AI Safety Standards

AI workflows must include:

- Prompt injection protection
- Content moderation
- Output validation
- Grade-level adaptation
- Safety filtering
- User feedback

AI should not encourage cheating.

AI should prioritize explanation and learning.

---

# Payment Coding Standards

Payment provider calls must go through the payment adapter layer.

Bad:

```typescript
razorpay.orders.create(...)
```

inside SubscriptionService.

Good:

```typescript
this.paymentService.createPayment(...)
```

Payment code must:

- Validate gateway response
- Verify webhook signatures
- Prevent duplicate payment processing
- Never store raw card details
- Log payment events safely
- Use idempotency where required

---

# File Upload Standards

File uploads must validate:

- File type
- File size
- File extension
- MIME type
- Virus or safety checks where practical
- User permissions
- Entitlements
- Storage quota

Allowed upload types should be centrally configured.

---

# Logging Standards

Use structured logging.

Approved:

```text
Pino
```

Every log should include where applicable:

- requestId
- userId
- organizationId
- institutionId
- module
- action
- status
- durationMs

Never log:

- Passwords
- Tokens
- Secrets
- Raw payment details
- Full private AI conversations unless explicitly approved and protected

---

# Audit Logging Standards

Audit sensitive actions.

Examples:

- Admin changes user role
- Admin suspends user
- Organization settings changed
- Institution settings changed
- Subscription changed
- Payment refunded
- Feature flag changed
- AI prompt changed
- User impersonation started
- User data exported

Audit logs should include:

- actorUserId
- action
- targetType
- targetId
- organizationId
- institutionId where applicable
- timestamp
- metadata

---

# Database Coding Standards

Use Prisma.

All schema changes must use migrations.

Do not manually change production database schema.

Do not use raw SQL unless:

- Prisma cannot express the query efficiently.
- Performance requires it.
- Architecture review approves it.

Raw SQL must be parameterized.

---

# Soft Delete Standards

Business entities should use soft delete where appropriate.

Fields:

```text
deletedAt
deletedBy
```

Queries should exclude soft-deleted records by default.

---

# Audit Fields

Business tables should include:

```text
createdAt
updatedAt
createdBy
updatedBy
```

Where applicable:

```text
organizationId
institutionId
```

---

# Security Standards

Every feature must consider:

- Authentication
- Authorization
- Tenant scoping
- Input validation
- Output encoding
- Rate limiting
- Secure uploads
- Secret protection
- Audit logging
- Abuse prevention

---

# Secrets Standards

Never commit secrets.

Do not commit:

```text
.env
.env.local
.env.production
API keys
JWT secrets
Database passwords
Payment secrets
AI provider keys
```

Use `.env.example` for placeholder names only.

---

# Environment Variable Standards

Environment variable names should be clear and uppercase.

Examples:

```text
DATABASE_URL
REDIS_URL
JWT_ACCESS_SECRET
JWT_REFRESH_SECRET
OPENAI_API_KEY
ANTHROPIC_API_KEY
GEMINI_API_KEY
RAZORPAY_KEY_ID
RAZORPAY_KEY_SECRET
```

---

# Testing Standards

Every feature requires tests appropriate to its risk.

## Unit Tests

Required for:

- Business rules
- Entitlement logic
- Permission checks
- Prompt builders
- Cost calculations
- Utility functions

---

## Integration Tests

Required for:

- API endpoints
- Database flows
- Authentication
- Authorization
- AI adapters
- Payment adapters
- Notification flows

---

## E2E Tests

Required for critical user journeys:

- Registration
- Login
- Homework solve
- AI Tutor follow-up
- Quiz attempt
- Subscription upgrade
- Payment confirmation
- Admin user management

---

# Test Coverage Targets

Minimum:

```text
80%
```

Critical modules:

```text
90%+
```

Critical modules include:

- Authentication
- Authorization
- Entitlements
- Payments
- Tenant isolation
- AI usage tracking
- Admin actions

---

# Git Standards

Use meaningful branch names.

Examples:

```text
feature/auth-registration
feature/homework-solver
feature/institution-management
fix/payment-webhook
docs/update-api-guidelines
```

---

# Commit Standards

Use Conventional Commits.

Examples:

```text
feat(auth): add registration endpoint
fix(homework): handle OCR failure
docs(api): update response format
refactor(ai): introduce provider adapter
test(payments): add webhook validation tests
```

---

# Code Review Standards

A pull request should not be approved unless:

- Code compiles
- Tests pass
- Lint passes
- Type checks pass
- Security is considered
- Tenant scoping is correct
- Entitlement checks are correct
- No secrets are committed
- Documentation is updated
- Scope matches the task

---

# AI Agent Coding Rules

AI agents must:

- Read `AGENTS.md`
- Read `.ai/CURRENT_STATUS.md`
- Read the relevant specification
- Read the relevant task file
- Implement only the requested task
- Avoid unrelated refactoring
- Follow this coding standard
- Update task status where required
- Update `.ai/TASK_LOG.md`
- Report tests run

AI agents must not:

- Create random architecture
- Invent unsupported libraries
- Ignore tenant scoping
- Hardcode school-only logic
- Hardcode subscription plan checks
- Skip validation
- Skip tests
- Add secrets
- Implement future tasks without instruction

---

# Documentation Standards

Every implemented feature should update:

- Module specification if behavior changed
- API documentation
- Database documentation if schema changed
- Task file
- Test notes
- Release notes where applicable

Complex logic should include comments explaining why, not obvious comments explaining what.

---

# Performance Standards

Avoid:

- Unbounded queries
- N+1 queries
- Large unpaginated responses
- Repeated AI calls for the same request
- Blocking long-running work in request handlers
- Loading unnecessary dashboard widgets

Use:

- Pagination
- Indexes
- Caching
- Background jobs
- Lazy loading
- Query optimization

---

# Accessibility Coding Standards

Frontend code should support:

- Semantic HTML
- Keyboard navigation
- Focus states
- Form labels
- Screen reader labels
- Accessible buttons
- Error messages associated with fields
- Sufficient contrast through design tokens

---

# Internationalization Standards

Do not hardcode user-facing strings deep inside business logic.

Prepare for:

- English in MVP
- Hindi next
- Additional languages later

Use centralized text handling where practical.

---

# Feature Flag Standards

Feature flags may control rollout.

Feature flags must not replace:

- Authorization
- Entitlements
- Tenant scoping
- Security rules

A user should not gain access to a paid or restricted capability only because a feature flag is enabled.

---

# Prohibited Patterns

Do not:

- Put business logic in controllers.
- Put business logic in React components.
- Query tenant data without scoping.
- Hardcode `schoolId` for future-facing modules.
- Hardcode subscription plan names.
- Call AI providers directly from feature modules.
- Call payment gateways directly from subscription logic.
- Store secrets in code.
- Ignore errors.
- Return raw database entities directly from APIs.
- Create huge files that mix multiple responsibilities.
- Build future enterprise UI during MVP unless approved.

---

# Definition of Done for Code

Code is complete when:

- It satisfies the task.
- It satisfies acceptance criteria.
- It follows architecture.
- It respects organization and institution scoping.
- It enforces authorization.
- It enforces entitlements where needed.
- It validates input.
- It handles errors.
- It logs important events.
- It includes tests.
- It updates documentation.
- It passes review.

---

# Related Documents

- SYSTEM_ARCHITECTURE.md
- TECH_STACK.md
- PROJECT_STRUCTURE.md
- API_GUIDELINES.md
- DATABASE_SCHEMA.md
- SECURITY.md
- ACCEPTANCE_CRITERIA.md