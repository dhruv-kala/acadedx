# Frontend State Management

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Frontend Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the frontend state management strategy for Acadedx.

Acadedx is a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

Frontend state must support the MVP while preserving future multi-institution behavior.

Code-level state must use:

```text
organizationId
institutionId
learnerId
guardianId
teacherId
```

Do not use:

```text
schoolId
studentId
parentId
```

except in explicitly documented UI copy or legacy adapter layers.

---

# State Management Goals

Frontend state management must ensure:

1. Server state and client UI state are clearly separated.
2. Organization and Institution context is explicit.
3. Authenticated user state supports multiple memberships.
4. API data is cached safely and invalidated predictably.
5. Cross-tenant cache leakage is impossible.
6. Forms are validated consistently.
7. List filters are shareable through URL query parameters.
8. Sensitive data is not stored unnecessarily in browser state.
9. Role-aware and permission-aware UI is reliable.
10. MVP state design does not block future multi-institution users.

---

# Recommended State Stack

Recommended frontend state tools:

```text
TanStack Query / React Query
React Context
Zustand or lightweight client store
React Hook Form
Zod
URL query parameters
```

Recommended usage:

| State Type                              | Tool                             |
| --------------------------------------- | -------------------------------- |
| Server data                             | TanStack Query                   |
| Authentication/session summary          | Auth context + server validation |
| Active Organization/Institution context | Context store or Zustand         |
| Forms                                   | React Hook Form + Zod            |
| Filters/search/sort/pagination          | URL query parameters             |
| UI-only state                           | Local component state            |
| Global UI state                         | Lightweight store                |
| Feature flags                           | Query cache + context            |
| Permissions                             | Query cache + derived helpers    |

Avoid using one global store for everything.

---

# State Categories

Frontend state should be divided into:

1. Authentication state
2. Active context state
3. Permission and feature state
4. Server state
5. Form state
6. URL state
7. UI state
8. Sensitive transient state
9. Offline or optimistic state, future only

---

# Authentication State

Authentication state represents the current signed-in user.

Minimum shape:

```ts
export interface AuthState {
  isAuthenticated: boolean;
  isLoading: boolean;
  user: CurrentUser | null;
  memberships: Membership[];
}
```

Recommended user shape:

```ts
export interface CurrentUser {
  id: string;
  email: string;
  fullName: string;
  status: 'ACTIVE' | 'PENDING_VERIFICATION' | 'DISABLED' | 'SUSPENDED';
}
```

Authentication state must not be treated as the complete authorization state.

Access must still be resolved through memberships, permissions, Organization scope, Institution scope, and backend checks.

---

# Membership State

Membership state defines where a user has access.

Recommended shape:

```ts
export interface Membership {
  organizationId?: string;
  institutionId?: string;
  role: string;
  status: 'ACTIVE' | 'INACTIVE' | 'SUSPENDED';
  permissions?: string[];
}
```

Rules:

* A user may have multiple memberships.
* MVP may usually return one Organization and one Institution.
* Frontend must not assume only one membership permanently.
* Inactive memberships must not be selectable.
* Membership changes should force permission and context refresh.

---

# Active Context State

Active context identifies the current Organization and Institution selected by the user.

Recommended shape:

```ts
export interface ActiveContextState {
  organizationId: string | null;
  institutionId: string | null;
  role: string | null;
  permissions: string[];
  isResolved: boolean;
}
```

For Institution-scoped pages:

```text
organizationId and institutionId are required.
```

For Organization-scoped pages:

```text
organizationId is required.
institutionId may be null.
```

For Platform Admin pages:

```text
organizationId may be null.
institutionId may be null.
```

---

# Active Context Resolution

On app load:

```text
1. Fetch current user using /auth/me.
2. Load memberships.
3. If route contains organizationId and institutionId, validate against memberships.
4. If user has one valid context, auto-select it.
5. If user has multiple contexts, use selected context or redirect to selector.
6. If no valid context exists, show unauthorized state.
```

The backend remains the source of truth.

Frontend context validation is for UX only.

---

# Context Persistence

The selected context may be persisted for user convenience.

Allowed storage:

```text
localStorage
sessionStorage
cookie, if needed for server rendering
```

Persist only IDs and safe metadata.

Allowed:

```json
{
  "organizationId": "org_123",
  "institutionId": "inst_123"
}
```

Do not persist:

```text
tokens
permissions as final source of truth
sensitive learner data
guardian contact data
assessment records
AI prompts
OCR text
payment data
```

Persisted context must be revalidated on every app load.

---

# Context Switching

Future multi-institution users must be able to switch context.

Context switch behavior:

```text
1. User selects Organization and Institution.
2. Frontend validates membership locally for UX.
3. Frontend updates active context.
4. Query caches for old context are invalidated or isolated.
5. Navigation redirects to valid dashboard for selected context.
6. Backend validates all subsequent API calls.
```

Important rule:

Never show cached data from the previous Institution after switching context.

---

# Query Key Standards

Server state must use tenant-safe query keys.

Bad:

```ts
['learners']
['learner', learnerId]
['assessments']
```

Good:

```ts
['organizations', organizationId, 'institutions', institutionId, 'learners']
['organizations', organizationId, 'institutions', institutionId, 'learners', learnerId]
['organizations', organizationId, 'institutions', institutionId, 'assessments']
```

Every Institution-scoped query key must include:

```text
organizationId
institutionId
```

Every Organization-scoped query key must include:

```text
organizationId
```

---

# Query Key Factory

Use query key factories instead of hardcoded keys.

Example:

```ts
export const queryKeys = {
  auth: {
    me: ['auth', 'me'] as const,
  },

  organizations: {
    all: ['organizations'] as const,
    detail: (organizationId: string) =>
      ['organizations', organizationId] as const,
    institutions: (organizationId: string) =>
      ['organizations', organizationId, 'institutions'] as const,
  },

  institutions: {
    detail: (organizationId: string, institutionId: string) =>
      ['organizations', organizationId, 'institutions', institutionId] as const,

    learners: (organizationId: string, institutionId: string, filters?: object) =>
      ['organizations', organizationId, 'institutions', institutionId, 'learners', filters] as const,

    learner: (organizationId: string, institutionId: string, learnerId: string) =>
      ['organizations', organizationId, 'institutions', institutionId, 'learners', learnerId] as const,
  },
};
```

---

# Server State

Server state includes data fetched from APIs.

Examples:

```text
Organizations
Institutions
Users
Memberships
Academic years
Classes
Sections
Subjects
Learners
Guardians
Teachers
Attendance sessions
Assessment records
Announcements
Reports
Notifications
Files
AI insights
```

Server state should be managed through TanStack Query.

Do not duplicate server state in global client stores unless there is a strong reason.

---

# Server State Rules

Rules:

* Query keys must include tenant context.
* Stale time should match data volatility.
* Mutations must invalidate affected query keys.
* Sensitive data should have shorter cache lifetime.
* Cross-tenant cache reuse is forbidden.
* Query errors should preserve backend error codes.
* Background refetch should not surprise users during data entry.

---

# Suggested Cache Settings

Suggested defaults:

| Data Type            | Suggested Stale Time |
| -------------------- | -------------------: |
| Current user         |          1-5 minutes |
| Memberships          |          1-5 minutes |
| Organization profile |         5-15 minutes |
| Institution profile  |         5-15 minutes |
| Academic structure   |         5-15 minutes |
| Learners list        |       30-120 seconds |
| Learner detail       |       30-120 seconds |
| Attendance sessions  |        15-60 seconds |
| Assessment results   |        15-60 seconds |
| Notifications        |        15-60 seconds |
| Reports              |       30-300 seconds |
| Audit logs           |        15-60 seconds |
| Feature flags        |          1-5 minutes |

Sensitive or frequently changing data should use shorter stale times.

---

# Mutation Standards

Mutations must:

1. Call typed API client.
2. Validate input before submit.
3. Use backend error codes for handling.
4. Invalidate related query keys.
5. Avoid optimistic updates for high-risk data unless safe.
6. Show success or failure feedback.
7. Preserve form data on validation failure.
8. Avoid cross-context invalidation errors.

Example:

```ts
const mutation = useMutation({
  mutationFn: createLearner,
  onSuccess: (_, variables) => {
    queryClient.invalidateQueries({
      queryKey: queryKeys.institutions.learners(
        variables.organizationId,
        variables.institutionId,
      ),
    });
  },
});
```

---

# Optimistic Updates

Use optimistic updates sparingly.

Allowed low-risk examples:

```text
Mark notification as read
Update local preference
Dismiss UI banner
Toggle table column visibility
```

Avoid optimistic updates for:

```text
Role changes
Membership updates
Learner records
Guardian links
Teacher assignments
Attendance submissions
Assessment results
Fee payments
File deletion
AI outputs
Report exports
```

Reason:

These operations are sensitive, audited, or conflict-prone.

---

# Form State

Use:

```text
React Hook Form
Zod
```

for forms.

Form schemas should align with backend DTOs.

Examples:

```text
CreateLearnerSchema
UpdateLearnerSchema
CreateGuardianSchema
TeacherAssignmentSchema
MarkAttendanceSchema
CreateAssessmentSchema
AssessmentResultsSchema
InviteUserSchema
InstitutionSettingsSchema
```

Do not use raw uncontrolled ad hoc form state for complex forms.

---

# Form Validation Rules

Frontend validation improves UX.

Backend validation remains authoritative.

Frontend should validate:

* Required fields
* Email format
* Phone format where applicable
* Date format
* Numeric ranges
* Enum values
* File size
* File type
* Cross-field constraints where simple
* Password strength where applicable

Backend must repeat validation.

---

# Form Error Handling

Backend validation errors should map to field errors.

Backend error example:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed.",
    "details": [
      {
        "field": "email",
        "message": "Email must be valid.",
        "rule": "email"
      }
    ]
  }
}
```

Frontend behavior:

```text
Set field-level error for email.
Show page-level error only if no field mapping exists.
```

Do not rely on error message text for logic.

Use `error.code`.

---

# URL State

Use URL query parameters for shareable list state.

Examples:

```text
search
status
academicYearId
classId
sectionId
subjectId
fromDate
toDate
page
limit
sort
```

Example route:

```text
/app/organizations/org_123/institutions/inst_123/learners?search=riya&classId=cls_123&status=ACTIVE
```

URL state should be used for:

* List filters
* Search terms
* Sort order
* Pagination
* Tabs where shareable
* Report filters where useful

---

# Local UI State

Use component-local state for transient UI concerns.

Examples:

```text
Dropdown open state
Dialog open state
Selected table rows
Current wizard step
Temporary input state
Expanded sidebar section
Toast visibility
Hovered item
```

Do not put local UI state in a global store unless multiple distant components need it.

---

# Global UI State

Global UI state may include:

```text
Sidebar collapsed
Theme preference
Command palette open
Global toast queue
Active modal registry, if used
Unsaved changes warning
```

Recommended tool:

```text
Zustand or React Context
```

Keep global UI state small.

---

# Permission State

Permission state is derived from memberships and backend-provided permission data.

Recommended helper:

```ts
can(permission: string, scope?: {
  organizationId?: string;
  institutionId?: string;
}): boolean
```

Example:

```ts
if (can('learner.create', { organizationId, institutionId })) {
  // show create learner button
}
```

Rules:

* Frontend permission checks control visibility and UX only.
* Backend APIs remain authoritative.
* Permission state must update after membership or role changes.
* Permission checks must include scope.

---

# Feature Flag State

Feature flags may control UI availability.

Feature flag state may include:

```text
feature code
enabled status
scope
rollout metadata
```

Feature flags must not replace permissions or entitlements.

A feature should be visible only when all apply:

```text
Feature flag enabled
Permission granted
Entitlement active where required
Scope valid
```

---

# Entitlement State

Entitlement state controls premium, costly, or limited features.

Examples:

```text
ai.insight.generate
ocr.extract
report.export
advanced.analytics
```

Frontend may use entitlement state to:

* Hide unavailable actions
* Show upgrade prompts
* Disable costly actions
* Display usage limits

Backend must enforce entitlements.

---

# Notification State

Notification state should use server state.

Recommended behavior:

* Fetch notifications with TanStack Query.
* Poll or subscribe where needed.
* Mark read through mutation.
* Optimistically mark read only if rollback is handled.
* Keep unread count query separate if needed.

Query key example:

```ts
['notifications', 'me']
```

Notifications are user-owned, but may include Organization and Institution metadata.

---

# File Upload State

File upload state is transient and should be handled carefully.

Track:

```text
selected file
upload progress
validation errors
uploaded fileId
upload status
```

Do not store file blobs in global state.

File upload must validate:

* MIME type
* Extension
* Size
* Related entity
* Scope
* Permission

After upload, store only returned file metadata or `fileId`.

---

# Background Job State

Background jobs should use server state polling or subscriptions.

Examples:

```text
OCR job
AI generation job
Report export job
Bulk import job
Notification delivery job
```

Recommended query key:

```ts
['jobs', jobId]
```

For tenant-scoped jobs:

```ts
['organizations', organizationId, 'institutions', institutionId, 'jobs', jobId]
```

Polling should stop when job status is terminal:

```text
SUCCEEDED
FAILED
CANCELLED
EXPIRED
```

---

# AI State

AI state must be privacy-aware.

Do not store raw AI prompts, responses, or learner context in global state unless explicitly required.

Allowed transient state:

```text
current draft input
generation status
jobId
resultResourceId
safe preview content
```

Avoid storing:

```text
raw prompt templates
full learner profile
full assessment history
guardian contact data
OCR text
cross-tenant context
provider debug payloads
```

AI results should be fetched from the backend using scoped APIs.

---

# Report State

Report filters should be URL-backed.

Report results should use server state.

Report exports should use background job state.

Example:

```text
Report filters → URL query params
Report preview → TanStack Query
Export job → Jobs query
Generated file → Files API
```

Reports must include Organization and Institution context in query keys.

---

# Table State

Tables should manage:

```text
pagination
sorting
filters
column visibility
row selection
density
```

Rules:

* Search, filters, sorting, and pagination should be URL-backed when useful.
* Column visibility may be local or user preference.
* Row selection should be local component state.
* Bulk actions must validate permissions.

---

# Wizard State

Onboarding and setup flows may use wizard state.

Examples:

```text
Organization setup
Institution setup
Academic setup
Bulk import
Assessment result upload
```

Wizard state may be stored in:

```text
component state
URL step param
sessionStorage for recovery
backend draft record for long workflows
```

Do not store sensitive form data in localStorage.

---

# Error State

Frontend error state must preserve:

```text
error code
HTTP status
requestId where available
safe message
field errors where available
```

Recommended shape:

```ts
export interface ApiErrorState {
  code: string;
  message: string;
  statusCode?: number;
  requestId?: string;
  details?: Array<{
    field?: string;
    message: string;
    rule?: string;
  }>;
}
```

Use backend error code for decisions.

---

# Sensitive Data State Rules

Do not store sensitive data longer than necessary.

Avoid storing in global state:

```text
passwords
tokens
refresh tokens
OTP values
raw AI prompts
raw AI conversations
OCR text
full learner records beyond page need
guardian contact exports
payment secrets
signed URLs
private file storage keys
```

Signed URLs should be short-lived and not persisted.

---

# Token Storage

Token storage strategy depends on final auth architecture.

Preferred browser options:

| Token         | Recommended Storage                     |
| ------------- | --------------------------------------- |
| Access token  | Memory or secure cookie                 |
| Refresh token | HttpOnly secure cookie where feasible   |
| CSRF token    | Cookie/header pair if using cookie auth |

If tokens are stored client-side:

* Avoid unnecessary persistence.
* Protect against XSS through strict frontend security.
* Never log tokens.
* Clear tokens on logout.
* Clear tokens on auth failure.

Final choice must align with backend authentication and CSRF strategy.

---

# SSR and Hydration

If using Next.js App Router:

* Server components may fetch public or authenticated data only through safe server-side mechanisms.
* Do not expose secrets to client bundles.
* Do not hydrate sensitive server-only data unnecessarily.
* Keep tenant context explicit in route params.
* Validate route params server-side when possible.

Client-side state should not contradict server-rendered authorization state.

---

# Cache Invalidation Rules

Mutation must invalidate affected data.

Examples:

## Create Learner

Invalidate:

```text
learners list
class detail, if learner count shown
dashboard summary, if learner count shown
```

## Update Learner

Invalidate:

```text
learner detail
learners list
learner reports
```

## Link Guardian

Invalidate:

```text
learner guardians
guardian detail
guardians list
```

## Mark Attendance

Invalidate:

```text
attendance session
attendance reports
learner attendance summary
dashboard attendance widget
```

## Submit Assessment Results

Invalidate:

```text
assessment detail
assessment reports
learner assessment summary
dashboard assessment widget
```

## Change Role

Invalidate:

```text
users list
memberships
current user context if affected
permissions
navigation
```

---

# Cross-Context Cache Isolation

When active context changes:

```text
Cancel in-flight queries for old context.
Clear or invalidate old Institution-scoped queries.
Update active context.
Refetch new context data.
Reset forms and selected rows.
Close sensitive modals.
```

Do not let data from one Institution flash on screen after switching to another Institution.

---

# Real-Time State

Real-time updates may be useful for:

```text
notifications
job status
announcement delivery
attendance updates
AI generation progress
report export completion
```

MVP approach:

```text
Polling
```

Future approach:

```text
WebSocket or Server-Sent Events
```

Real-time events must include tenant context and be authorization-safe.

---

# Offline State

Offline support is not required for MVP.

Future offline workflows may include:

```text
teacher attendance marking
mobile learner content
draft announcements
guardian notification cache
```

Offline state must handle:

* Conflict resolution
* Tenant context
* Secure local storage
* Sync status
* Data expiry

Do not add offline complexity to the MVP unless mobile-first requirements demand it.

---

# State Directory Structure

Recommended frontend state structure:

```text
src/
├── app/
├── features/
│   ├── auth/
│   │   ├── api/
│   │   ├── hooks/
│   │   ├── state/
│   │   └── types/
│   ├── organizations/
│   ├── institutions/
│   ├── learners/
│   ├── guardians/
│   ├── teachers/
│   ├── attendance/
│   ├── assessments/
│   └── reports/
├── shared/
│   ├── api/
│   ├── query/
│   ├── state/
│   ├── validation/
│   └── utils/
└── providers/
```

---

# API Client State Integration

Use a typed API client.

API client responsibilities:

* Attach authentication credentials.
* Include request ID where needed.
* Use scoped endpoint paths.
* Parse standard response format.
* Normalize error responses.
* Avoid swallowing backend error codes.

Example response wrapper:

```ts
export interface ApiResponse<T> {
  success: true;
  message?: string;
  data: T;
}

export interface ApiErrorResponse {
  success: false;
  error: {
    code: string;
    message: string;
    details?: unknown[];
    requestId?: string;
  };
}
```

---

# State Naming Standards

Use consistent names:

```text
authState
activeContext
currentUser
memberships
permissions
featureFlags
entitlements
learnersQuery
learnerDetailQuery
createLearnerMutation
updateLearnerMutation
```

Avoid:

```text
schoolState
studentStore
parentStore
currentSchool
```

---

# MVP State Requirements

The MVP must implement:

```text
Auth state
Current user query
Membership state
Active Organization and Institution context
Context selector
Tenant-safe query keys
Typed API client
Form validation with Zod
URL-backed filters for major list pages
Permission helper
Feature flag placeholder
Entitlement placeholder if AI/OCR/export limits are enabled
Notification state if notifications are included
Job polling state if background jobs are included
Safe error state handling
Cache invalidation rules for core mutations
Cross-context cache isolation
```

---

# Deferred State Capabilities

The following may be deferred beyond MVP:

```text
Offline sync
WebSocket-based real-time state
Advanced optimistic updates
Complex multi-tab state reconciliation
Session management UI
Admin feature flag editor state
Advanced analytics dashboard state
Bulk import wizard persistence
AI streaming response state
Collaborative editing state
```

Do not block these future capabilities through hardcoded single-Institution state.

---

# Testing Requirements

State management tests should cover:

## Auth State

* Unauthenticated user
* Authenticated user
* Expired session
* Disabled user
* Current user refresh

## Active Context

* One membership auto-selects context
* Multiple memberships require selection
* Invalid persisted context is rejected
* Context switch invalidates old data
* Institution-scoped page requires institutionId

## Permissions

* Permission helper returns true for allowed action
* Permission helper returns false for missing permission
* Scope-specific permission checks work
* Navigation hides unauthorized items

## Query Keys

* Learner queries include organizationId and institutionId
* Guardian queries include organizationId and institutionId
* Teacher queries include organizationId and institutionId
* Organization queries include organizationId
* Cross-context cache leakage does not occur

## Forms

* Required fields validate
* Backend validation maps to fields
* Form preserves values after validation error
* Restricted fields are not submitted

## Errors

* 401 triggers auth recovery
* 403 shows forbidden state
* 404 shows safe not-found state
* 422 maps field errors
* 429 shows rate-limit message
* 500 shows safe error with request ID

---

# State Management Review Checklist

Before approving frontend state implementation, confirm:

* Server state uses TanStack Query or equivalent.
* Global client state is minimal.
* Active context includes Organization and Institution.
* No `schoolId` is introduced.
* Code uses Learner and Guardian terminology.
* Tenant-scoped query keys include organizationId and institutionId.
* Forms use typed validation.
* Backend error codes are preserved.
* Sensitive data is not stored unnecessarily.
* Context switching clears or isolates old tenant data.
* Permissions are scope-aware.
* Feature flags do not replace permissions.
* Entitlements do not replace backend enforcement.
* URL query params are used for shareable filters.
* Tests cover cross-context cache safety.

---

# Related Documents

* docs/08-Frontend/PAGES.md
* docs/08-Frontend/ROUTING.md
* docs/08-Frontend/COMPONENTS.md
* docs/10-Design-System/DESIGN_SYSTEM.md
* docs/10-Design-System/COMPONENT_LIBRARY.md
* docs/06-API/API_GUIDELINES.md
* docs/06-API/ENDPOINTS.md
* docs/06-API/ERROR_CODES.md
* docs/11-Security/RBAC.md
* docs/11-Security/PRIVACY.md
* docs/09-Backend/AUTHENTICATION.md
