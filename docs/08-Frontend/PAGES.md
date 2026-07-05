# Frontend Pages

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Frontend Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the frontend page structure for Acadedx.

Acadedx is a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

Frontend pages may use user-friendly labels such as “School”, “Student”, and “Parent” where appropriate for the MVP user experience, but frontend routing, state, API calls, and code-level domain concepts should remain aligned with:

```text
Organization
Institution
Learner
Guardian
Teacher
```

Do not build frontend assumptions that permanently limit Acadedx to a single school.

---

# Frontend Page Goals

Frontend pages must:

1. Support the school-focused MVP.
2. Preserve Organization and Institution context.
3. Avoid hardcoded school-only assumptions.
4. Use backend-aligned terminology in code.
5. Present user-friendly education terminology in UI where appropriate.
6. Support future multi-institution navigation.
7. Enforce role-aware navigation and page access.
8. Keep learner, guardian, teacher, and admin experiences separated.
9. Keep page ownership clear.
10. Support progressive enhancement as modules mature.

---

# Frontend Application Surfaces

Acadedx may eventually include multiple frontend surfaces:

| Surface              | Purpose                                      |          MVP |
| -------------------- | -------------------------------------------- | -----------: |
| Web App              | Main application for institution users       |          Yes |
| Admin App            | Platform and DGIS admin operations           | Yes, minimal |
| Mobile App           | Learner, guardian, and teacher mobile access |       Future |
| Desktop App          | Optional desktop packaging                   |       Future |
| Public Website       | Marketing and landing pages                  |   Yes, basic |
| Documentation Portal | Help, onboarding, and support docs           |       Future |

This document focuses primarily on the authenticated web application and minimal platform admin pages.

---

# Recommended Frontend Stack

Preferred stack:

```text
Next.js
React
TypeScript
Tailwind CSS
shadcn/ui or equivalent component primitives
React Query / TanStack Query
Zod
```

The page structure should work with either:

```text
Next.js App Router
```

or a similar route-based frontend framework.

---

# Page Naming Rules

Use code-level names aligned with the backend model.

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

Avoid code-level names:

```text
schools
students
parents
```

Allowed UI labels:

| Code Term    | UI Label Allowed           |
| ------------ | -------------------------- |
| Institution  | School, Institution        |
| Learner      | Student, Learner           |
| Guardian     | Parent, Guardian           |
| Class        | Class, Grade               |
| Section      | Section                    |
| Organization | Organization, Group, Trust |

Rule:

Use backend-aligned names in routes, API clients, state keys, and components. Use user-friendly labels in visible copy.

---

# Route Context Model

Authenticated application pages should preserve active context:

```text
organizationId
institutionId
```

Preferred route structure for institution-scoped app pages:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/...
```

Example:

```text
/app/organizations/org_123/institutions/inst_123/learners
```

This is verbose but future-ready.

An alternative route strategy may use an active context selector and shorter URLs:

```text
/app/institution/learners
```

If shorter URLs are used, the selected `organizationId` and `institutionId` must still be validated server-side and stored explicitly in frontend state.

Recommended for MVP:

```text
Use explicit context in route for admin and institution operations.
```

---

# Page Access Model

Every protected page must define:

```text
Authentication required
Required role or permission
Required Organization scope
Required Institution scope
Feature entitlement, where applicable
```

Frontend page guards improve UX but do not replace backend authorization.

Backend APIs remain the source of truth.

---

# Layout Model

Recommended layouts:

```text
RootLayout
PublicLayout
AuthLayout
AppLayout
OrganizationLayout
InstitutionLayout
AdminLayout
LearnerLayout
GuardianLayout
TeacherLayout
ErrorLayout
```

---

# Public Pages

Public pages do not require authentication.

## Home Page

Route:

```text
/
```

Purpose:

Introduce Acadedx and route users to login, demo, or contact flow.

MVP:

```text
Yes
```

---

## Product Page

Route:

```text
/product
```

Purpose:

Describe Acadedx platform capabilities.

MVP:

```text
Optional
```

---

## Pricing Page

Route:

```text
/pricing
```

Purpose:

Show plans and pricing if subscriptions are part of launch.

MVP:

```text
Optional
```

Recommendation:

Defer detailed pricing page if institution-first MVP is sales-led.

---

## Contact Page

Route:

```text
/contact
```

Purpose:

Lead capture, demos, and institution onboarding inquiries.

MVP:

```text
Yes
```

---

## Terms Page

Route:

```text
/legal/terms
```

Purpose:

Terms of service.

MVP:

```text
Yes before production
```

---

## Privacy Policy Page

Route:

```text
/legal/privacy
```

Purpose:

Public privacy policy.

MVP:

```text
Yes before production
```

---

# Authentication Pages

Authentication pages use `AuthLayout`.

## Login Page

Route:

```text
/auth/login
```

Purpose:

Authenticate users.

Primary users:

```text
Platform Admin
Organization Owner
Organization Admin
Institution Admin
Teacher
Guardian
Learner
```

Required features:

* Email input
* Password input
* Forgot password link
* Error handling
* Rate-limit friendly messaging
* Redirect after login
* Future MFA state support

MVP:

```text
Yes
```

---

## Accept Invitation Page

Route:

```text
/auth/invitations/[token]
```

Purpose:

Allow invited users to create or connect an account.

Required features:

* Validate invitation token
* Show Organization and Institution context
* Show invited role
* Set password for new user
* Accept terms where required
* Create membership after successful acceptance

MVP:

```text
Yes
```

---

## Forgot Password Page

Route:

```text
/auth/forgot-password
```

Purpose:

Start password reset flow.

Required UX rule:

Always show generic success message.

MVP:

```text
Yes
```

---

## Reset Password Page

Route:

```text
/auth/reset-password/[token]
```

Purpose:

Set new password using reset token.

Required features:

* Password validation
* Token expiry handling
* Safe error messaging

MVP:

```text
Yes
```

---

## Verify Email Page

Route:

```text
/auth/verify-email/[token]
```

Purpose:

Verify user email address.

MVP:

```text
Yes if email verification is required
```

---

## Logout Page

Route:

```text
/auth/logout
```

Purpose:

Clear session and redirect to login.

MVP:

```text
Optional
```

Logout may also be handled as an action rather than a page.

---

# Onboarding Pages

Onboarding pages help configure an Organization and Institution.

## Organization Setup Page

Route:

```text
/app/onboarding/organization
```

Purpose:

Create or confirm Organization profile.

Primary users:

```text
Platform Admin
Organization Owner
Organization Admin
```

MVP:

```text
Yes for first customer setup
```

---

## Institution Setup Page

Route:

```text
/app/onboarding/institution
```

Purpose:

Create or confirm the first Institution under the Organization.

UI may say “School Setup” for MVP.

Code should use Institution.

Required fields:

* Institution name
* Institution type
* Institution code
* Address
* Timezone
* Academic year start month

MVP:

```text
Yes
```

---

## Initial Academic Setup Page

Route:

```text
/app/onboarding/academic-setup
```

Purpose:

Create first academic year, classes, sections, and subjects.

MVP:

```text
Yes
```

---

## Invite Users Page

Route:

```text
/app/onboarding/invite-users
```

Purpose:

Invite initial Institution Admins, Teachers, and Staff.

MVP:

```text
Yes
```

---

# Authenticated App Landing

## Context Selector Page

Route:

```text
/app/select-context
```

Purpose:

Allow users with multiple memberships to choose active Organization and Institution.

MVP behavior:

If user has only one Organization and one Institution, redirect automatically.

Future behavior:

Allow switching between Institutions.

MVP:

```text
Yes, minimal
```

---

## Main Dashboard Redirect Page

Route:

```text
/app
```

Purpose:

Redirect authenticated user to the correct dashboard based on role and active context.

Redirect examples:

| Role                 | Destination            |
| -------------------- | ---------------------- |
| `PLATFORM_ADMIN`     | `/admin/dashboard`     |
| `ORGANIZATION_OWNER` | Organization dashboard |
| `ORGANIZATION_ADMIN` | Organization dashboard |
| `INSTITUTION_ADMIN`  | Institution dashboard  |
| `TEACHER`            | Teacher dashboard      |
| `GUARDIAN`           | Guardian dashboard     |
| `LEARNER`            | Learner dashboard      |

MVP:

```text
Yes
```

---

# Platform Admin Pages

Platform Admin pages are for DGIS operations.

Base route:

```text
/admin
```

These pages use `AdminLayout`.

---

## Platform Admin Dashboard

Route:

```text
/admin/dashboard
```

Purpose:

Show platform-level operational overview.

Widgets:

* Organizations count
* Institutions count
* Active users
* Recent onboarding
* System health
* AI usage summary, if enabled
* Recent audit events

MVP:

```text
Yes, minimal
```

---

## Organizations Admin Page

Route:

```text
/admin/organizations
```

Purpose:

List and manage Organizations.

Required features:

* Search
* Filter by status
* Create Organization
* View Organization
* Status indicators

MVP:

```text
Yes
```

---

## Organization Detail Admin Page

Route:

```text
/admin/organizations/[organizationId]
```

Purpose:

View Organization details from platform perspective.

Tabs:

```text
Overview
Institutions
Users
Settings
Audit Logs
```

MVP:

```text
Yes, basic
```

---

## Institutions Admin Page

Route:

```text
/admin/institutions
```

Purpose:

List Institutions across Organizations.

Required features:

* Search
* Filter by Organization
* Filter by type
* Filter by status

MVP:

```text
Yes
```

---

## Institution Detail Admin Page

Route:

```text
/admin/organizations/[organizationId]/institutions/[institutionId]
```

Purpose:

View Institution details from platform perspective.

MVP:

```text
Yes, basic
```

---

## Admin Users Page

Route:

```text
/admin/users
```

Purpose:

Search and manage platform-visible users.

MVP:

```text
Yes, minimal
```

---

## Admin Audit Logs Page

Route:

```text
/admin/audit-logs
```

Purpose:

View platform-level audit logs.

MVP:

```text
Yes, minimal
```

---

## Admin Feature Flags Page

Route:

```text
/admin/feature-flags
```

Purpose:

Manage feature rollout.

MVP:

```text
V1
```

---

## Admin AI Usage Page

Route:

```text
/admin/ai-usage
```

Purpose:

Monitor AI usage, cost, errors, and safety events.

MVP:

```text
V1 or MVP Optional
```

---

# Organization Pages

Organization pages are for Organization Owners and Organization Admins.

Base route:

```text
/app/organizations/[organizationId]
```

---

## Organization Dashboard

Route:

```text
/app/organizations/[organizationId]/dashboard
```

Purpose:

Show Organization-level overview.

MVP behavior:

Because MVP has one Institution, this may redirect to Institution dashboard or show a simple Organization summary.

Future widgets:

* Institution count
* Active learners
* Attendance overview
* Assessment overview
* Fee overview
* AI usage across Institutions
* Institution performance summaries

MVP:

```text
MVP Optional
```

Recommendation:

Implement minimal page or redirect to Institution dashboard.

---

## Organization Settings Page

Route:

```text
/app/organizations/[organizationId]/settings
```

Purpose:

Manage Organization settings.

Sections:

* Profile
* Contact details
* Default timezone
* Default currency
* Feature settings
* Billing settings, if enabled

MVP:

```text
V1
```

---

## Organization Users Page

Route:

```text
/app/organizations/[organizationId]/users
```

Purpose:

Manage users across Organization scope.

MVP:

```text
Yes if Organization Admin exists in MVP
```

---

## Organization Institutions Page

Route:

```text
/app/organizations/[organizationId]/institutions
```

Purpose:

List Institutions under the Organization.

MVP behavior:

Shows one Institution.

Future behavior:

Supports multiple Institutions.

MVP:

```text
Yes
```

---

## Organization Audit Logs Page

Route:

```text
/app/organizations/[organizationId]/audit-logs
```

Purpose:

View Organization-scoped audit logs.

MVP:

```text
V1
```

---

# Institution Pages

Institution pages are the primary MVP experience.

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]
```

These pages use `InstitutionLayout`.

---

## Institution Dashboard

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/dashboard
```

Purpose:

Show Institution operational overview.

Primary users:

```text
Institution Admin
Organization Admin
Organization Owner
Platform Admin
```

MVP widgets:

* Learner count
* Teacher count
* Class and section count
* Today's attendance summary
* Recent assessments
* Recent announcements
* Pending setup tasks

Future widgets:

* Fee summary
* AI insight summary
* Report shortcuts
* Risk indicators
* Multi-institution comparison

MVP:

```text
Yes
```

---

## Institution Profile Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/profile
```

Purpose:

View and update Institution profile.

UI label may say “School Profile” for MVP.

MVP:

```text
Yes
```

---

## Institution Settings Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/settings
```

Purpose:

Manage Institution settings.

Sections:

* General settings
* Academic settings
* Timezone
* Currency
* Notification settings
* Feature settings
* AI settings, if enabled

MVP:

```text
Yes, basic
```

---

## Institution Users Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/users
```

Purpose:

Manage Institution users and memberships.

Required features:

* Invite user
* List users
* Filter by role
* Deactivate access
* Role assignment where permitted

MVP:

```text
Yes
```

---

# Academic Pages

Academic pages are Institution-scoped.

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/academic
```

---

## Academic Overview Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/academic
```

Purpose:

Overview of academic structure.

MVP:

```text
Yes
```

---

## Academic Years Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/academic/academic-years
```

Purpose:

Manage academic years.

UI may say “Academic Session” if preferred for schools, but code should use `academicYear`.

MVP:

```text
Yes
```

---

## Classes Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/academic/classes
```

Purpose:

Manage classes.

UI may say “Grades” for some institution types, but route and code should use `classes`.

MVP:

```text
Yes
```

---

## Class Detail Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/academic/classes/[classId]
```

Purpose:

View class details, sections, subjects, learners, and teachers.

MVP:

```text
Yes
```

---

## Sections Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/academic/sections
```

Purpose:

Manage sections.

MVP:

```text
Yes
```

---

## Subjects Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/academic/subjects
```

Purpose:

Manage subjects.

MVP:

```text
Yes
```

---

## Timetable Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/academic/timetable
```

Purpose:

Manage timetable entries.

MVP:

```text
V1
```

---

# Learner Pages

Learner pages are Institution-scoped.

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners
```

UI may say “Students” for school MVP.

Code should use `learners`.

---

## Learners List Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners
```

Purpose:

List, search, filter, and manage learners.

Required features:

* Search by name or admission number
* Filter by academic year
* Filter by class
* Filter by section
* Filter by status
* Create learner
* Import learners, future
* View learner detail

MVP:

```text
Yes
```

---

## Create Learner Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners/new
```

Purpose:

Create learner record.

MVP:

```text
Yes
```

---

## Learner Detail Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]
```

Purpose:

View learner profile and related records.

Tabs:

```text
Overview
Profile
Guardians
Class Assignment
Attendance
Assessments
Files
AI Insights
Audit
```

MVP tabs:

```text
Overview
Profile
Guardians
Class Assignment
Attendance
Assessments
```

MVP:

```text
Yes
```

---

## Learner Edit Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/edit
```

Purpose:

Edit learner profile.

MVP:

```text
Yes
```

---

## Learner Attendance Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/attendance
```

Purpose:

View learner attendance summary.

MVP:

```text
Yes
```

---

## Learner Assessment Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/assessments
```

Purpose:

View learner assessment summary.

MVP:

```text
Yes
```

---

# Guardian Pages

Guardian pages are Institution-scoped.

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardians
```

UI may say “Parents” for school MVP.

Code should use `guardians`.

---

## Guardians List Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardians
```

Purpose:

List, search, filter, and manage guardians.

MVP:

```text
Yes
```

---

## Create Guardian Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/new
```

Purpose:

Create guardian record and optionally link to learner.

MVP:

```text
Yes
```

---

## Guardian Detail Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]
```

Purpose:

View guardian profile and linked learners.

Tabs:

```text
Overview
Profile
Linked Learners
Communication
Preferences
```

MVP:

```text
Yes
```

---

## Guardian Edit Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]/edit
```

Purpose:

Edit guardian profile.

MVP:

```text
Yes
```

---

# Teacher Pages

Teacher pages are Institution-scoped.

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teachers
```

---

## Teachers List Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teachers
```

Purpose:

List, search, filter, and manage teachers.

MVP:

```text
Yes
```

---

## Create Teacher Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/new
```

Purpose:

Create teacher profile or link a user account to teacher profile.

MVP:

```text
Yes
```

---

## Teacher Detail Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/[teacherId]
```

Purpose:

View teacher profile and assignments.

Tabs:

```text
Overview
Profile
Assignments
Attendance
Assessments
Communication
```

MVP tabs:

```text
Overview
Profile
Assignments
```

MVP:

```text
Yes
```

---

## Teacher Assignments Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teachers/[teacherId]/assignments
```

Purpose:

Assign teacher to classes, sections, and subjects.

MVP:

```text
Yes
```

---

# Attendance Pages

Attendance pages are Institution-scoped.

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/attendance
```

---

## Attendance Dashboard Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/attendance
```

Purpose:

Show attendance overview.

MVP:

```text
Yes
```

---

## Mark Attendance Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/attendance/mark
```

Purpose:

Mark attendance for class and section.

Required filters:

* Academic year
* Class
* Section
* Date
* Session type

MVP:

```text
Yes
```

---

## Attendance Session Detail Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/attendance/sessions/[attendanceSessionId]
```

Purpose:

View and edit attendance records where permitted.

MVP:

```text
Yes
```

---

## Attendance Reports Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/attendance/reports
```

Purpose:

View attendance reports.

MVP:

```text
Yes, basic
```

---

# Assessment Pages

Assessment pages are Institution-scoped.

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/assessments
```

---

## Assessments List Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/assessments
```

Purpose:

List assessments.

Filters:

* Academic year
* Class
* Section
* Subject
* Assessment type
* Date range

MVP:

```text
Yes
```

---

## Create Assessment Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/new
```

Purpose:

Create assessment.

MVP:

```text
Yes
```

---

## Assessment Detail Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/[assessmentId]
```

Purpose:

View assessment details and learner results.

MVP:

```text
Yes
```

---

## Enter Assessment Results Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/[assessmentId]/results
```

Purpose:

Enter or update learner results.

MVP:

```text
Yes
```

---

## Assessment Reports Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/assessments/reports
```

Purpose:

View assessment reports.

MVP:

```text
Yes, basic
```

---

# Fee Pages

Fee pages are Institution-scoped.

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/fees
```

MVP status depends on product scope.

---

## Fees Dashboard Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/fees
```

Purpose:

Show fee overview.

MVP:

```text
MVP Optional or V1
```

---

## Fee Categories Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/fees/categories
```

Purpose:

Manage fee categories.

MVP:

```text
MVP Optional or V1
```

---

## Fee Structures Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/fees/structures
```

Purpose:

Manage fee structures.

MVP:

```text
MVP Optional or V1
```

---

## Fee Payments Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/fees/payments
```

Purpose:

Record and view fee payments.

MVP:

```text
MVP Optional or V1
```

---

## Learner Fee Detail Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/fees
```

Purpose:

View learner fee assignments and payments.

MVP:

```text
MVP Optional or V1
```

---

# Communication Pages

Communication pages are Institution-scoped.

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/communication
```

---

## Announcements Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/communication/announcements
```

Purpose:

List announcements.

MVP:

```text
Yes
```

---

## Create Announcement Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/communication/announcements/new
```

Purpose:

Create announcement.

MVP:

```text
Yes
```

---

## Announcement Detail Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/communication/announcements/[announcementId]
```

Purpose:

View announcement details and delivery status.

MVP:

```text
Yes
```

---

## Messages Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/communication/messages
```

Purpose:

Direct communication between staff, guardians, teachers, and learners where enabled.

MVP:

```text
V1
```

---

# Reports Pages

Reports pages are Institution-scoped.

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/reports
```

---

## Reports Dashboard Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/reports
```

Purpose:

Show available reports.

MVP:

```text
Yes
```

---

## Attendance Report Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/reports/attendance
```

Purpose:

Attendance reporting.

MVP:

```text
Yes
```

---

## Assessment Report Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/reports/assessments
```

Purpose:

Assessment reporting.

MVP:

```text
Yes
```

---

## Learner Report Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/reports/learners
```

Purpose:

Learner report generation and summaries.

MVP:

```text
Yes, basic
```

---

## Fee Report Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/reports/fees
```

Purpose:

Fee reporting.

MVP:

```text
MVP Optional or V1
```

---

# File Pages

File pages are generally embedded inside domain pages.

Standalone route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/files
```

---

## Files Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/files
```

Purpose:

View institution files where authorized.

MVP:

```text
V1
```

Recommendation:

For MVP, file upload and file viewing should be embedded in learner, assessment, report, and AI workflows instead of a broad file manager.

---

# AI Pages

AI pages are Institution-scoped and should be enabled only where product scope requires.

Base route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/ai
```

---

## AI Insights Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/ai/insights
```

Purpose:

View AI-generated learner or institution insights.

MVP:

```text
MVP Optional or V1
```

---

## Learner AI Insight Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/ai-insights
```

Purpose:

View AI-generated insights for a learner.

MVP:

```text
MVP Optional or V1
```

---

## AI Communication Draft Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/ai/communication-drafts
```

Purpose:

Generate or review AI-assisted communication drafts.

MVP:

```text
V1
```

---

## OCR Jobs Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/ai/ocr-jobs
```

Purpose:

View OCR processing jobs and results.

MVP:

```text
V1
```

---

# Role-Specific Workspace Pages

Role-specific pages should be used when user experience differs significantly from admin workflows.

---

# Teacher Workspace Pages

Base route:

```text
/app/teacher
```

or future context-aware route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teacher
```

Recommendation:

Use context-aware route for future readiness.

---

## Teacher Dashboard

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/dashboard
```

Purpose:

Teacher-specific dashboard.

Widgets:

* Assigned classes
* Today's attendance tasks
* Upcoming assessments
* Recent announcements
* Pending result entry

MVP:

```text
Yes if teacher workflow is included
```

---

## Teacher Classes Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/classes
```

Purpose:

View assigned classes and sections.

MVP:

```text
Yes
```

---

## Teacher Attendance Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/attendance
```

Purpose:

Mark attendance for assigned sections.

MVP:

```text
Yes if teachers mark attendance
```

---

## Teacher Assessments Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/assessments
```

Purpose:

View assigned assessments and enter results.

MVP:

```text
Yes if teachers manage assessment results
```

---

# Guardian Workspace Pages

Base route:

```text
/app/guardian
```

or context-aware route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardian
```

UI may say “Parent Portal”.

---

## Guardian Dashboard

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardian/dashboard
```

Purpose:

Guardian-specific dashboard.

Widgets:

* Linked learners
* Attendance summary
* Assessment summary
* Announcements
* Fee summary, if enabled

MVP:

```text
V1 or MVP Optional
```

---

## Guardian Linked Learners Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardian/learners
```

Purpose:

Show learners linked to guardian.

MVP:

```text
V1 or MVP Optional
```

---

## Guardian Learner Detail Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/guardian/learners/[learnerId]
```

Purpose:

Show allowed learner details to guardian.

MVP:

```text
V1 or MVP Optional
```

---

# Learner Workspace Pages

Base route:

```text
/app/learner
```

or context-aware route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learner
```

UI may say “Student Portal”.

---

## Learner Dashboard

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learner/dashboard
```

Purpose:

Learner-specific dashboard.

Widgets:

* Attendance summary
* Assessment summary
* Announcements
* Assigned learning resources
* AI learning features, if enabled

MVP:

```text
V1 or MVP Optional
```

---

## Learner Profile Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learner/profile
```

Purpose:

Learner self-profile view.

MVP:

```text
V1 or MVP Optional
```

---

## Learner Assessments Page

Route:

```text
/app/organizations/[organizationId]/institutions/[institutionId]/learner/assessments
```

Purpose:

Learner assessment result view.

MVP:

```text
V1 or MVP Optional
```

---

# User Account Pages

User account pages are not Institution-specific unless displaying scoped information.

Base route:

```text
/app/account
```

---

## Account Profile Page

Route:

```text
/app/account/profile
```

Purpose:

Manage current user's profile.

MVP:

```text
Yes
```

---

## Account Preferences Page

Route:

```text
/app/account/preferences
```

Purpose:

Manage preferences such as language, timezone, and notifications.

MVP:

```text
Yes, basic
```

---

## Account Sessions Page

Route:

```text
/app/account/sessions
```

Purpose:

View and revoke sessions.

MVP:

```text
V1
```

---

## Change Password Page

Route:

```text
/app/account/change-password
```

Purpose:

Change current user's password.

MVP:

```text
Yes
```

---

# Error Pages

## Not Found Page

Route:

```text
/not-found
```

Purpose:

Show 404 state.

MVP:

```text
Yes
```

---

## Unauthorized Page

Route:

```text
/app/unauthorized
```

Purpose:

Show authenticated user lacks access.

MVP:

```text
Yes
```

---

## Forbidden Page

Route:

```text
/app/forbidden
```

Purpose:

Show user lacks required permission or tenant access.

MVP:

```text
Yes
```

---

## Server Error Page

Route:

```text
/error
```

Purpose:

Show generic recoverable error state.

MVP:

```text
Yes
```

---

## Maintenance Page

Route:

```text
/maintenance
```

Purpose:

Show planned downtime or degraded service.

MVP:

```text
Optional
```

---

# MVP Page Set

The MVP should implement the following pages first:

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
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/learners/[learnerId]/assessments

/app/organizations/[organizationId]/institutions/[institutionId]/guardians
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/new
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]
/app/organizations/[organizationId]/institutions/[institutionId]/guardians/[guardianId]/edit

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

/app/unauthorized
/app/forbidden
/not-found
/error
```

---

# Deferred Pages

The following may be deferred beyond MVP:

```text
/pricing

/app/organizations/[organizationId]/dashboard
/app/organizations/[organizationId]/settings
/app/organizations/[organizationId]/audit-logs

/admin/feature-flags
/admin/ai-usage

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

/app/organizations/[organizationId]/institutions/[institutionId]/teacher/dashboard
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/classes
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/attendance
/app/organizations/[organizationId]/institutions/[institutionId]/teacher/assessments

/app/organizations/[organizationId]/institutions/[institutionId]/guardian/dashboard
/app/organizations/[organizationId]/institutions/[institutionId]/guardian/learners
/app/organizations/[organizationId]/institutions/[institutionId]/guardian/learners/[learnerId]

/app/organizations/[organizationId]/institutions/[institutionId]/learner/dashboard
/app/organizations/[organizationId]/institutions/[institutionId]/learner/profile
/app/organizations/[organizationId]/institutions/[institutionId]/learner/assessments

/app/account/sessions
```

---

# Page Metadata Standard

Each page should define:

```ts
export const pageMeta = {
  title: 'Learners',
  description: 'Manage learners for the active institution.',
  requiredAuth: true,
  requiredPermissions: ['learner.read'],
  scope: 'institution',
};
```

This metadata may be used for:

* Navigation
* Breadcrumbs
* Access checks
* Documentation
* Automated tests

---

# Page Loading States

Every data-driven page must define:

* Initial loading state
* Empty state
* Error state
* Permission denied state
* Retry behavior
* Skeleton UI where appropriate

---

# Empty State Guidelines

Empty states should be specific and action-oriented.

Example:

```text
No learners found.
Add your first learner to start managing attendance and assessments.
```

Avoid generic empty states:

```text
No data.
```

---

# Page Error Handling

Frontend should handle backend error codes, not message text.

Examples:

```text
AUTH_TOKEN_EXPIRED
TENANT_SCOPE_VIOLATION
RBAC_PERMISSION_REQUIRED
LEARNER_NOT_FOUND
INST_NOT_FOUND
VALIDATION_ERROR
```

Rules:

* `401` should trigger authentication recovery.
* `403` should show permission or scope error.
* `404` should show not found or inaccessible resource.
* `422` should show validation errors.
* `429` should show rate-limit guidance.
* `500` should show generic error with request ID.

---

# Navigation Requirements

Navigation must be role-aware and permission-aware.

Institution Admin navigation may include:

```text
Dashboard
Academic
Learners
Guardians
Teachers
Attendance
Assessments
Announcements
Reports
Settings
```

Teacher navigation may include:

```text
Dashboard
My Classes
Attendance
Assessments
Announcements
```

Guardian navigation may include:

```text
Dashboard
Linked Learners
Announcements
Messages
Fees
```

Learner navigation may include:

```text
Dashboard
Profile
Assessments
Attendance
Announcements
Learning
```

Only show navigation items the user is allowed to access.

Hidden navigation is not a security control. Backend must still enforce access.

---

# Breadcrumb Standard

Pages should include breadcrumbs where hierarchy is deep.

Example:

```text
Organization → Institution → Learners → Riya Sharma
```

For MVP UI, breadcrumb labels may display:

```text
School → Students → Riya Sharma
```

but underlying route and state should use Institution and Learner.

---

# Search and Filter Standards

List pages should support:

* Search
* Filter
* Sort
* Pagination
* Clear filters
* Saved filters in future

MVP list pages requiring search and filters:

```text
Organizations
Institutions
Users
Learners
Guardians
Teachers
Attendance sessions
Assessments
Announcements
Reports
```

---

# Responsive Requirements

MVP web pages should support:

```text
Desktop
Tablet
Mobile web basic usability
```

Mobile-native workflows may be future scope.

Critical mobile-friendly pages:

* Login
* Guardian dashboard
* Learner dashboard
* Teacher attendance
* Announcements

---

# Accessibility Requirements

Pages should follow accessibility basics:

* Semantic headings
* Keyboard navigation
* Focus states
* Labelled inputs
* Error messages connected to fields
* Sufficient contrast
* Screen-reader-friendly status messages
* Avoid color-only meaning

---

# Frontend Page Testing Requirements

Page tests should cover:

* Page renders with authorized user
* Page blocks unauthenticated user
* Page blocks missing permission
* Page handles Organization mismatch
* Page handles Institution mismatch
* Page handles loading state
* Page handles empty state
* Page handles validation errors
* Page handles API failure
* Page handles pagination and filters
* Page does not expose restricted actions

High-risk pages need integration or E2E tests:

```text
Login
Accept invitation
Institution setup
User invitation
Learner detail
Guardian link
Teacher assignment
Mark attendance
Enter assessment results
Reports
Admin users
Admin audit logs
```

---

# Frontend Page Review Checklist

Before approving a page, confirm:

* Page route uses Organization and Institution context where required.
* Page does not introduce `schoolId`.
* Code uses Learner and Guardian terminology.
* UI labels are appropriate for the target user.
* Required permissions are defined.
* Backend access is not assumed from frontend state.
* Loading, empty, error, and forbidden states exist.
* Forms use validation.
* Sensitive fields are not displayed unnecessarily.
* Navigation is role-aware.
* Page supports future multi-institution behavior.
* API calls use correct scoped endpoints.
* Tests cover denied access and empty states.

---

# Related Documents

* docs/08-Frontend/ROUTING.md
* docs/08-Frontend/STATE_MANAGEMENT.md
* docs/08-Frontend/COMPONENTS.md
* docs/10-Design-System/DESIGN_SYSTEM.md
* docs/10-Design-System/COMPONENT_LIBRARY.md
* docs/06-API/API_GUIDELINES.md
* docs/06-API/ENDPOINTS.md
* docs/11-Security/RBAC.md
* docs/11-Security/PRIVACY.md
* docs/09-Backend/MODULES.md

