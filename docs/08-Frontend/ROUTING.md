# Frontend Routing

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Frontend Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the frontend routing strategy for Acadedx.

Acadedx is a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

Frontend routes must support the MVP without hardcoding school-only assumptions. Code-level routing, route params, API clients, state keys, and access checks should use:

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

# Routing Goals

Frontend routing must ensure:

1. Organization and Institution context is preserved.
2. Institution-scoped pages are future-ready for multi-institution support.
3. Route names align with backend/API terminology.
4. Protected pages enforce authentication and role-aware navigation.
5. Page-level access is based on permissions and scope.
6. Deep links are stable and shareable where appropriate.
7. Routes remain understandable for frontend, backend, QA, and AI coding agents.
8. MVP routes do not block future Organization → multiple Institutions support.
9. Public, auth, app, admin, and role-specific routes are clearly separated.
10. Error routes handle unauthorized, forbidden, not found, and server failure states.

---

# Recommended Routing Framework

Preferred frontend framework:

```text
Next.js App Router
```

Recommended route directory:

```text
src/app/
```

Recommended route groups:

```text
src/app/
├── (public)/
├── (auth)/
├── (app)/
├── (admin)/
└── api/ or route-handlers/
```

If another framework is used, the same routing principles still apply.

---

# Route Terminology

Use backend-aligned names in routes.

Use:

```text
organizations
institutions
learners
guardians
teachers
academic-years
classes
sections
subjects
attendance
assessments
reports
```

Avoid route segments:

```text
schools
students
parents
```

Allowed UI labels:

| Backend / Route Term | UI Label Allowed           |
| -------------------- | -------------------------- |
| Institution          | School, Institution        |
| Learner              | Student, Learner           |
| Guardian             | Parent, Guardian           |
| Class                | Class, Grade               |
| Organization         | Organization, Group, Trust |

Rule:

Routes and code should stay future-ready. Visible UI text may be adapted to the MVP audience.

---

# Route Parameter Naming

Use explicit route parameters.

Good:

```text
[organizationId]
[institutionId]
[learnerId]
[guardianId]
[teacherId]
[assessmentId]
[attendanceSessionId]
```

Bad:

```text
[id]
[schoolId]
[studentId]
[parentId]
```

Exception:

`[id]` may be acceptable only in highly local component routes where the parent segment makes the entity unambiguous, but explicit names are preferred.

---

# Route Context Strategy

Institution-scoped pages should include both Organization and Institution context.

Canonical route pattern:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/...
```

Example:

```text
/app/organizations/org_123/institutions/inst_123/learners/lrn_123
```

This route is verbose but correct for future multi-institution support.

---

# Why Explicit Context Is Preferred

Explicit Organization and Institution route context provides:

* Stable deep links
* Clear tenant context
* Easier debugging
* Better audit correlation
* Safer frontend access checks
* Better support for future multi-institution users
* Better alignment with backend scoped endpoints

The route must still be validated by the backend.

Frontend route context is not a security boundary.

---

# Alternative Short Route Strategy

A shorter route strategy may be used for user-specific dashboards:

```text
/app/dashboard
/app/learners
/app/attendance
```

If short routes are used, frontend state must explicitly store:

```text
activeOrganizationId
activeInstitutionId
```

and every API call must still include validated backend scope.

Constraint:

Short routes are convenient but easier to misuse. Use explicit scoped routes for admin and institution operations.

Recommended MVP decision:

```text
Use explicit Organization and Institution route context for institution operations.
```

---

# Top-Level Route Groups

Recommended top-level groups:

```text
/
├── public routes
├── /auth
├── /app
├── /admin
├── /legal
├── /maintenance
├── /not-found
└── /error
```

---

# Public Routes

Public routes do not require authentication.

```text
/
 /product
 /pricing
 /contact
 /legal/terms
 /legal/privacy
 /maintenance
```

MVP required:

```text
/
 /contact
 /legal/terms
 /legal/privacy
```

`/pricing` may be deferred if the first MVP is sales-led or institution-onboarding-led.

---

# Authentication Routes

Authentication routes use `AuthLayout`.

```text
/auth/login
/auth/forgot-password
/auth/reset-password/[token]
/auth/verify-email/[token]
/auth/invitations/[token]
/auth/logout
```

MVP required:

```text
/auth/login
/auth/forgot-password
/auth/reset-password/[token]
/auth/invitations/[token]
```

Required if email verification is enabled:

```text
/auth/verify-email/[token]
```

Logout may be a route or an action.

---

# App Entry Routes

Authenticated app entry routes:

```text
/app
/app/select-context
/app/unauthorized
/app/forbidden
/app/account/profile
/app/account/preferences
/app/account/change-password
/app/account/sessions
```

MVP required:

```text
/app
/app/select-context
/app/account/profile
/app/account/preferences
/app/account/change-password
/app/unauthorized
/app/forbidden
```

`/app/account/sessions` may be V1.

---

# App Redirect Rules

## `/app`

Route:

```text
/app
```

Behavior:

* If unauthenticated, redirect to `/auth/login`.
* If authenticated with no valid membership, redirect to onboarding or support state.
* If authenticated with one valid context, redirect to role-specific dashboard.
* If authenticated with multiple contexts, redirect to `/app/select-context`.

Role-based examples:

| Role                 | Redirect Target                                                                       |
| -------------------- | ------------------------------------------------------------------------------------- |
| `PLATFORM_ADMIN`     | `/admin/dashboard`                                                                    |
| `ORGANIZATION_OWNER` | `/app/organizations/[organizationId]/dashboard` or Institution dashboard in MVP       |
| `ORGANIZATION_ADMIN` | `/app/organizations/[organizationId]/dashboard` or Institution dashboard in MVP       |
| `INSTITUTION_ADMIN`  | `/app/organizations/[organizationId]/institutions/[institutionId]/dashboard`          |
| `TEACHER`            | `/app/organizations/[organizationId]/institutions/[institutionId]/teacher/dashboard`  |
| `GUARDIAN`           | `/app/organizations/[organizationId]/institutions/[institutionId]/guardian/dashboard` |
| `LEARNER`            | `/app/organizations/[organizationId]/institutions/[institutionId]/learner/dashboard`  |

MVP may redirect Teachers, Guardians, and Learners to the Institution dashboard or a limited portal depending on implemented scope.

---

# Context Selection Route

Route:

```text
/app/select-context
```

Purpose:

Allow users with multiple memberships to choose active Organization and Institution.

MVP behavior:

* If only one active Organization and Institution exist, auto-select and redirect.
* If no active membership exists, show access unavailable state.
* If multiple memberships exist, show selector.

Future behavior:

* Allow switching between Institutions.
* Persist last selected context.
* Support Organization-level dashboards.

---

# Platform Admin Routes

Platform admin routes are for DGIS operational users.

Base route:

```text
/admin
```

Recommended routes:

```text
/admin/dashboard
/admin/organizations
/admin/organizations/[organizationId]
/admin/organizations/[organizationId]/institutions
/admin/organizations/[organizationId]/institutions/[institutionId]
/admin/institutions
/admin/users
/admin/users/[userId]
/admin/audit-logs
/admin/feature-flags
/admin/ai-usage
/admin/payments
/admin/subscriptions
```

MVP required:

```text
/admin/dashboard
/admin/organizations
/admin/organizations/[organizationId]
/admin/institutions
/admin/organizations/[organizationId]/institutions/[institutionId]
/admin/users
/admin/audit-logs
```

Deferred:

```text
/admin/feature-flags
/admin/ai-usage
/admin/payments
/admin/subscriptions
```

---

# Organization Routes

Organization routes are scoped by `organizationId`.

Base route:

```text
/app/organizations/[organizationId]
```

Recommended routes:

```text
/app/organizations/[organizationId]/dashboard
/app/organizations/[organizationId]/settings
/app/organizations/[organizationId]/users
/app/organizations/[organizationId]/institutions
/app/organizations/[organizationId]/audit-logs
/app/organizations/[organizationId]/reports
```

MVP recommendation:

Because MVP supports one Organization → one Institution, the Organization dashboard may be minimal or redirect to the Institution dashboard.

MVP required:

```text
/app/organizations/[organizationId]/institutions
```

Optional MVP:

```text
/app/organizations/[organizationId]/users
```

V1:

```text
/app/organizations/[organizationId]/dashboard
/app/organizations/[organizationId]/settings
/app/organizations/[organizationId]/audit-logs
/app/organizations/[organizationId]/reports
```

---

# Institution Routes

Institution routes are scoped by both `organizationId` and `institutionId`.

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]
```

Recommended routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/dashboard
/app/organizations/[organizationId]/institutions/[institutionId]/profile
/app/organizations/[organizationId]/institutions/[institutionId]/settings
/app/organizations/[organizationId]/institutions/[institutionId]/users
```

MVP required:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/dashboard
/app/organizations/[organizationId]/institutions/[institutionId]/profile
/app/organizations/[organizationId]/institutions/[institutionId]/settings
/app/organizations/[organizationId]/institutions/[institutionId]/users
```

---

# Onboarding Routes

Onboarding routes are authenticated and may be platform-admin-led or organization-owner-led.

```text
/app/onboarding/organization
/app/onboarding/institution
/app/onboarding/academic-setup
/app/onboarding/invite-users
```

MVP required:

```text
/app/onboarding/organization
/app/onboarding/institution
/app/onboarding/academic-setup
/app/onboarding/invite-users
```

Rules:

* Organization setup creates or confirms Organization.
* Institution setup creates or confirms Institution.
* Academic setup creates academic year, classes, sections, and subjects.
* Invite users creates scoped memberships.

UI may say “School Setup”, but route and code should use `institution`.

---

# Academic Routes

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/academic
```

Recommended routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/academic
/app/organizations/[organizationId]/institutions/[institutionId]/academic/academic-years
/app/organizations/[organizationId]/institutions/[institutionId]/academic/classes
/app/organizations/[organizationId]/institutions/[institutionId]/academic/classes/[classId]
/app/organizations/[organizationId]/institutions/[institutionId]/academic/sections
/app/organizations/[organizationId]/institutions/[institutionId]/academic/subjects
/app/organizations/[organizationId]/institutions/[institutionId]/academic/timetable
```

MVP required:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/academic
/app/organizations/[organizationId]/institutions/[institutionId]/academic/academic-years
/app/organizations/[organizationId]/institutions/[institutionId]/academic/classes
/app/organizations/[organizationId]/institutions/[institutionId]/academic/classes/[classId]
/app/organizations/[organizationId]/institutions/[institutionId]/academic/sections
/app/organizations/[organizationId]/institutions/[institutionId]/academic/subjects
```

V1:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/academic/timetable
```

---

# Learner Routes

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners
```

Recommended routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners
/app/organizations/[organizationId]/institutions/[institutionId]/learners/new
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/edit
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/guardians
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/assessments
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/fees
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/files
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/ai-insights
```

MVP required:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners
/app/organizations/[organizationId]/institutions/[institutionId]/learners/new
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/edit
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/guardians
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/assessments
```

Deferred:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/fees
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/files
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/ai-insights
```

UI may display “Students”.

Code and route must use `learners`.

---

# Guardian Routes

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardians
```

Recommended routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardians
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/new
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]/edit
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]/learners
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]/communication
```

MVP required:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardians
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/new
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]/edit
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]/learners
```

Deferred:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]/communication
```

UI may display “Parents”.

Code and route must use `guardians`.

---

# Teacher Routes

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teachers
```

Recommended routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teachers
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/new
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/[teacherId]
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/[teacherId]/edit
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/[teacherId]/assignments
```

MVP required:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teachers
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/new
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/[teacherId]
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/[teacherId]/assignments
```

Optional:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/[teacherId]/edit
```

---

# Attendance Routes

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/attendance
```

Recommended routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/attendance/mark
/app/organizations/[organizationId]/institutions/[institutionId]/attendance/sessions/[attendanceSessionId]
/app/organizations/[organizationId]/institutions/[institutionId]/attendance/reports
```

MVP required:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/attendance/mark
/app/organizations/[organizationId]/institutions/[institutionId]/attendance/sessions/[attendanceSessionId]
/app/organizations/[organizationId]/institutions/[institutionId]/attendance/reports
```

---

# Assessment Routes

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/assessments
```

Recommended routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/assessments
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/new
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/[assessmentId]
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/[assessmentId]/results
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/reports
```

MVP required:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/assessments
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/new
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/[assessmentId]
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/[assessmentId]/results
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/reports
```

---

# Fee Routes

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/fees
```

Recommended routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/fees
/app/organizations/[organizationId]/institutions/[institutionId]/fees/categories
/app/organizations/[organizationId]/institutions/[institutionId]/fees/structures
/app/organizations/[organizationId]/institutions/[institutionId]/fees/payments
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/fees
```

MVP status:

```text
MVP Optional or V1
```

Do not include fee routes in MVP if they delay the academic core.

---

# Communication Routes

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/communication
```

Recommended routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/communication/announcements
/app/organizations/[organizationId]/institutions/[institutionId]/communication/announcements/new
/app/organizations/[organizationId]/institutions/[institutionId]/communication/announcements/[announcementId]
/app/organizations/[organizationId]/institutions/[institutionId]/communication/messages
```

MVP required:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/communication/announcements
/app/organizations/[organizationId]/institutions/[institutionId]/communication/announcements/new
/app/organizations/[organizationId]/institutions/[institutionId]/communication/announcements/[announcementId]
```

V1:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/communication/messages
```

---

# Report Routes

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/reports
```

Recommended routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/reports
/app/organizations/[organizationId]/institutions/[institutionId]/reports/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/reports/assessments
/app/organizations/[organizationId]/institutions/[institutionId]/reports/learners
/app/organizations/[organizationId]/institutions/[institutionId]/reports/fees
```

MVP required:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/reports
/app/organizations/[organizationId]/institutions/[institutionId]/reports/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/reports/assessments
/app/organizations/[organizationId]/institutions/[institutionId]/reports/learners
```

Deferred:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/reports/fees
```

---

# File Routes

Recommended standalone file route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/files
```

MVP recommendation:

Do not build a broad file manager initially.

Embed file actions into domain pages:

```text
Learner files
Assessment files
Report exports
OCR source files
Institution profile assets
```

Standalone file route may be V1.

---

# AI Routes

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/ai
```

Recommended routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/ai/insights
/app/organizations/[organizationId]/institutions/[institutionId]/ai/communication-drafts
/app/organizations/[organizationId]/institutions/[institutionId]/ai/ocr-jobs
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/ai-insights
```

MVP status:

```text
MVP Optional or V1
```

Constraint:

AI routes must support Institution workflows. Avoid direct-to-student AI app routing unless explicitly included in product scope.

---

# Teacher Workspace Routes

Context-aware teacher routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/dashboard
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/classes
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/assessments
```

MVP status:

```text
MVP if Teacher workspace is included
```

For MVP, teacher workflows may also use admin-style attendance and assessment pages with role-limited actions.

---

# Guardian Workspace Routes

Context-aware guardian routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardian/dashboard
/app/organizations/[organizationId]/institutions/[institutionId]/guardian/learners
/app/organizations/[organizationId]/institutions/[institutionId]/guardian/learners/[learnerId]
```

MVP status:

```text
V1 or MVP Optional
```

UI may label this “Parent Portal”.

---

# Learner Workspace Routes

Context-aware learner routes:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learner/dashboard
/app/organizations/[organizationId]/institutions/[institutionId]/learner/profile
/app/organizations/[organizationId]/institutions/[institutionId]/learner/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/learner/assessments
```

MVP status:

```text
V1 or MVP Optional
```

UI may label this “Student Portal”.

---

# Route Guarding

Frontend route guards should protect UX but must not be treated as security controls.

Every protected route must check:

```text
isAuthenticated
activeUserStatus
activeOrganizationId where required
activeInstitutionId where required
requiredPermissions
featureAvailability where required
```

Backend must enforce the final decision.

---

# Route Metadata

Every route should define metadata.

Example:

```ts
export const routeMeta = {
  title: 'Learners',
  description: 'Manage learners for the active institution.',
  requiresAuth: true,
  scope: 'institution',
  requiredPermissions: ['learner.read'],
  navigationGroup: 'institution',
};
```

Metadata may be used for:

* Page title
* Breadcrumbs
* Navigation
* Permission-aware menu rendering
* Tests
* Documentation

---

# Layout Strategy

Recommended layouts:

```text
PublicLayout
AuthLayout
AppLayout
AdminLayout
OrganizationLayout
InstitutionLayout
TeacherLayout
GuardianLayout
LearnerLayout
AccountLayout
ErrorLayout
```

Layout hierarchy for Institution pages:

```text
AppLayout
  ↓
OrganizationLayout
  ↓
InstitutionLayout
  ↓
Page
```

---

# Next.js App Router Example Structure

Recommended structure:

```text
src/app/
├── (public)/
│   ├── page.tsx
│   ├── product/page.tsx
│   ├── contact/page.tsx
│   └── legal/
│       ├── terms/page.tsx
│       └── privacy/page.tsx
├── (auth)/
│   └── auth/
│       ├── login/page.tsx
│       ├── forgot-password/page.tsx
│       ├── reset-password/[token]/page.tsx
│       ├── verify-email/[token]/page.tsx
│       └── invitations/[token]/page.tsx
├── (admin)/
│   └── admin/
│       ├── dashboard/page.tsx
│       ├── organizations/page.tsx
│       ├── organizations/[organizationId]/page.tsx
│       ├── organizations/[organizationId]/institutions/[institutionId]/page.tsx
│       ├── institutions/page.tsx
│       ├── users/page.tsx
│       └── audit-logs/page.tsx
└── (app)/
    └── app/
        ├── page.tsx
        ├── select-context/page.tsx
        ├── account/
        │   ├── profile/page.tsx
        │   ├── preferences/page.tsx
        │   └── change-password/page.tsx
        ├── onboarding/
        │   ├── organization/page.tsx
        │   ├── institution/page.tsx
        │   ├── academic-setup/page.tsx
        │   └── invite-users/page.tsx
        └── organizations/[organizationId]/
            ├── institutions/page.tsx
            └── institutions/[institutionId]/
                ├── dashboard/page.tsx
                ├── profile/page.tsx
                ├── settings/page.tsx
                ├── users/page.tsx
                ├── academic/
                ├── learners/
                ├── guardians/
                ├── teachers/
                ├── attendance/
                ├── assessments/
                ├── communication/
                └── reports/
```

---

# Dynamic Route Validation

Every dynamic route parameter must be validated.

Validation should include:

* Required parameter exists
* Parameter format is valid
* User has access to the scoped Organization
* User has access to the scoped Institution
* Resource exists within the scoped tenant
* Resource has not been archived unless archived view is allowed

Example:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]
```

Must validate:

```text
organizationId
institutionId
learnerId
learner belongs to organizationId and institutionId
user has learner.read permission or valid relationship
```

---

# Breadcrumb Rules

Breadcrumbs should reflect tenant hierarchy.

Example:

```text
Organizations → DGIS Education Group → Acadedx Public School → Learners → Riya Sharma
```

MVP UI may show:

```text
School → Students → Riya Sharma
```

but code and route context must use Institution and Learner.

---

# Query Parameter Standards

Use query parameters for filters, sorting, search, and pagination.

Examples:

```text
?search=riya
?status=ACTIVE
?academicYearId=ay_123
?classId=cls_123
?sectionId=sec_123
?sort=-createdAt
?page=1
?limit=20
```

Preferred frontend state rule:

* Use URL query params for shareable filters.
* Use local component state for transient UI state.
* Use global state only for active context and authenticated user state.

---

# Search Route Behavior

List pages should keep search state in URL query params.

Example:

```text
/app/organizations/org_123/institutions/inst_123/learners?search=riya&classId=cls_123
```

This supports:

* Refresh persistence
* Shareable URLs
* Browser back/forward
* Better QA reproducibility

---

# Modal Route Strategy

Use modal routes sparingly.

Acceptable modal routes:

```text
Create learner
Invite user
Create announcement
Link guardian
Assign teacher
```

Preferred MVP approach:

Use normal pages for complex forms.

Reason:

* Easier validation
* Easier deep linking
* Easier testing
* Less state complexity

---

# Redirect Rules

Common redirect rules:

| Condition                                        | Redirect              |
| ------------------------------------------------ | --------------------- |
| Not authenticated                                | `/auth/login`         |
| Authenticated user visits `/auth/login`          | `/app`                |
| No active membership                             | `/app/unauthorized`   |
| Multiple memberships and no active context       | `/app/select-context` |
| Missing permission                               | `/app/forbidden`      |
| Resource not found                               | `/not-found`          |
| Platform admin route without platform permission | `/app/forbidden`      |

Redirects must avoid loops.

---

# Unauthorized and Forbidden Difference

Use separate pages:

```text
/app/unauthorized
/app/forbidden
```

## Unauthorized

Meaning:

```text
User is not authenticated or has no valid access context.
```

## Forbidden

Meaning:

```text
User is authenticated but lacks permission or tenant access.
```

Do not reveal sensitive tenant details on either page.

---

# Not Found Behavior

For tenant-scoped resources, backend may return `404` instead of `403` when revealing the resource existence would leak information.

Frontend should display a generic not-found state:

```text
This resource was not found or you do not have access to it.
```

Avoid:

```text
This learner belongs to another institution.
```

---

# Route-Level Code Splitting

Large route groups should be lazy-loaded by default.

High-value code splitting targets:

```text
Admin pages
Reports
AI pages
Fees
Assessment result entry
Bulk import
Charts and analytics
```

Next.js App Router handles much of this through route segment splitting.

---

# Navigation Mapping

Navigation should be generated from route metadata and permissions.

Example nav item:

```ts
{
  label: 'Learners',
  href: '/app/organizations/[organizationId]/institutions/[institutionId]/learners',
  requiredPermissions: ['learner.read'],
  scope: 'institution',
  icon: 'users'
}
```

Rules:

* Do not show nav items the user cannot access.
* Do not rely on hidden nav items for security.
* Backend must still enforce access.

---

# Route Constants

Maintain route constants or route builders.

Example:

```ts
export const routes = {
  institutionDashboard: (organizationId: string, institutionId: string) =>
    `/app/organizations/${organizationId}/institutions/${institutionId}/dashboard`,

  learnerDetail: (
    organizationId: string,
    institutionId: string,
    learnerId: string,
  ) =>
    `/app/organizations/${organizationId}/institutions/${institutionId}/learners/${learnerId}`,
};
```

Avoid hardcoding route strings throughout the app.

---

# API Route Alignment

Frontend routes should align with API scoping.

Frontend route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners
```

API route:

```text
/api/v1/organizations/{organizationId}/institutions/{institutionId}/learners
```

This makes API calls predictable and tenant-safe.

---

# SEO Rules

Authenticated app pages do not need SEO indexing.

Public pages should support:

* Title
* Description
* Canonical URL
* Open Graph metadata
* Robots directives

Protected app pages should not be indexed.

Use:

```text
noindex, nofollow
```

where appropriate.

---

# Error Boundary Strategy

Use error boundaries at route group level.

Recommended boundaries:

```text
Public route boundary
Auth route boundary
Admin route boundary
App route boundary
Institution route boundary
Reports route boundary
AI route boundary
```

Each should show:

* User-safe error message
* Retry action where appropriate
* Request ID where available
* Support link where appropriate

---

# Loading Strategy

Each route group should define loading states.

Use:

```text
loading.tsx
error.tsx
not-found.tsx
```

where supported by the framework.

Data-heavy pages should show skeleton states.

Avoid blocking the whole app shell for page-level loading.

---

# MVP Route Set

The MVP should implement:

```text
/
 /contact
 /legal/terms
 /legal/privacy

/auth/login
/auth/forgot-password
/auth/reset-password/[token]
/auth/verify-email/[token]
/auth/invitations/[token]

/app
/app/select-context
/app/account/profile
/app/account/preferences
/app/account/change-password
/app/unauthorized
/app/forbidden

/admin/dashboard
/admin/organizations
/admin/organizations/[organizationId]
/admin/institutions
/admin/organizations/[organizationId]/institutions/[institutionId]
/admin/users
/admin/audit-logs

/app/onboarding/organization
/app/onboarding/institution
/app/onboarding/academic-setup
/app/onboarding/invite-users

/app/organizations/[organizationId]/institutions
/app/organizations/[organizationId]/institutions/[institutionId]/dashboard
/app/organizations/[organizationId]/institutions/[institutionId]/profile
/app/organizations/[organizationId]/institutions/[institutionId]/settings
/app/organizations/[organizationId]/institutions/[institutionId]/users

/app/organizations/[organizationId]/institutions/[institutionId]/academic
/app/organizations/[organizationId]/institutions/[institutionId]/academic/academic-years
/app/organizations/[organizationId]/institutions/[institutionId]/academic/classes
/app/organizations/[organizationId]/institutions/[institutionId]/academic/classes/[classId]
/app/organizations/[organizationId]/institutions/[institutionId]/academic/sections
/app/organizations/[organizationId]/institutions/[institutionId]/academic/subjects

/app/organizations/[organizationId]/institutions/[institutionId]/learners
/app/organizations/[organizationId]/institutions/[institutionId]/learners/new
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/edit
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/guardians
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/assessments

/app/organizations/[organizationId]/institutions/[institutionId]/guardians
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/new
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]/edit
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]/learners

/app/organizations/[organizationId]/institutions/[institutionId]/teachers
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/new
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/[teacherId]
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/[teacherId]/assignments

/app/organizations/[organizationId]/institutions/[institutionId]/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/attendance/mark
/app/organizations/[organizationId]/institutions/[institutionId]/attendance/sessions/[attendanceSessionId]
/app/organizations/[organizationId]/institutions/[institutionId]/attendance/reports

/app/organizations/[organizationId]/institutions/[institutionId]/assessments
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/new
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/[assessmentId]
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/[assessmentId]/results
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/reports

/app/organizations/[organizationId]/institutions/[institutionId]/communication/announcements
/app/organizations/[organizationId]/institutions/[institutionId]/communication/announcements/new
/app/organizations/[organizationId]/institutions/[institutionId]/communication/announcements/[announcementId]

/app/organizations/[organizationId]/institutions/[institutionId]/reports
/app/organizations/[organizationId]/institutions/[institutionId]/reports/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/reports/assessments
/app/organizations/[organizationId]/institutions/[institutionId]/reports/learners

/not-found
/error
```

---

# Deferred Route Set

The following routes may be deferred:

```text
/product
/pricing

/app/account/sessions

/app/organizations/[organizationId]/dashboard
/app/organizations/[organizationId]/settings
/app/organizations/[organizationId]/audit-logs
/app/organizations/[organizationId]/reports

/admin/feature-flags
/admin/ai-usage
/admin/payments
/admin/subscriptions

/app/organizations/[organizationId]/institutions/[institutionId]/academic/timetable

/app/organizations/[organizationId]/institutions/[institutionId]/fees
/app/organizations/[organizationId]/institutions/[institutionId]/fees/categories
/app/organizations/[organizationId]/institutions/[institutionId]/fees/structures
/app/organizations/[organizationId]/institutions/[institutionId]/fees/payments

/app/organizations/[organizationId]/institutions/[institutionId]/communication/messages

/app/organizations/[organizationId]/institutions/[institutionId]/files

/app/organizations/[organizationId]/institutions/[institutionId]/ai/insights
/app/organizations/[organizationId]/institutions/[institutionId]/ai/communication-drafts
/app/organizations/[organizationId]/institutions/[institutionId]/ai/ocr-jobs
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/ai-insights

/app/organizations/[organizationId]/institutions/[institutionId]/teacher/dashboard
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/classes
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/assessments

/app/organizations/[organizationId]/institutions/[institutionId]/guardian/dashboard
/app/organizations/[organizationId]/institutions/[institutionId]/guardian/learners
/app/organizations/[organizationId]/institutions/[institutionId]/guardian/learners/[learnerId]

/app/organizations/[organizationId]/institutions/[institutionId]/learner/dashboard
/app/organizations/[organizationId]/institutions/[institutionId]/learner/profile
/app/organizations/[organizationId]/institutions/[institutionId]/learner/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/learner/assessments
```

---

# Route Testing Requirements

Routing tests should cover:

* Public routes render without authentication.
* Authenticated routes redirect unauthenticated users.
* Auth routes redirect authenticated users where appropriate.
* `/app` redirects based on role.
* Context selector handles one membership.
* Context selector handles multiple memberships.
* Institution routes require valid Organization and Institution context.
* Missing permissions show forbidden state.
* Tenant mismatch shows forbidden or not-found state.
* Dynamic resource routes validate resource existence.
* Not-found state is safe and does not leak tenant details.
* Navigation hides unauthorized routes.
* Route builders produce valid paths.

High-risk routes requiring E2E tests:

```text
/auth/login
/auth/invitations/[token]
/app/select-context
/admin/organizations/[organizationId]
/app/organizations/[organizationId]/institutions/[institutionId]/dashboard
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]
/app/organizations/[organizationId]/institutions/[institutionId]/attendance/mark
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/[assessmentId]/results
```

---

# Routing Review Checklist

Before approving routes, confirm:

* Route uses Organization and Institution context where required.
* Route does not introduce `schoolId`.
* Route names use `learners` and `guardians`, not `students` and `parents`.
* Dynamic params are explicit.
* Page has metadata.
* Page has required auth and permission information.
* Route aligns with API endpoint structure.
* Route supports future multi-institution users.
* Redirect behavior is defined.
* Unauthorized and forbidden behavior is defined.
* Not-found behavior avoids tenant data leakage.
* Navigation item is permission-aware.
* Route is covered by tests where high-risk.

---

# Related Documents

* docs/08-Frontend/PAGES.md
* docs/08-Frontend/STATE_MANAGEMENT.md
* docs/08-Frontend/COMPONENTS.md
* docs/10-Design-System/DESIGN_SYSTEM.md
* docs/10-Design-System/COMPONENT_LIBRARY.md
* docs/06-API/API_GUIDELINES.md
* docs/06-API/ENDPOINTS.md
* docs/11-Security/RBAC.md
* docs/11-Security/SECURITY.md
* docs/09-Backend/MODULES.md

