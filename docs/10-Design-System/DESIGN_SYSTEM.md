# Design System

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Product Design  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the Acadedx design system.

The design system provides reusable visual, interaction, layout, and component standards for the Acadedx product experience.

Acadedx is a school-focused MVP with a future-ready platform architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

The user interface may use school-friendly labels such as “School”, “Student”, and “Parent” for the MVP where appropriate, but product design, information architecture, route structure, state, API integration, and component naming must remain aligned with:

```text
Organization
Institution
Learner
Guardian
Teacher
```

---

# Design System Goals

The Acadedx design system must ensure:

1. A consistent experience across all product surfaces.
2. Fast MVP delivery using reusable components.
3. Clear support for Organization and Institution context.
4. School-friendly UX without hardcoding school-only architecture.
5. Accessible and responsive interfaces.
6. Clean workflows for institution admins, teachers, guardians, learners, and platform admins.
7. Scalable design patterns for future multi-institution customers.
8. Safe handling of learner and guardian data.
9. Clear visual hierarchy for academic operations.
10. Compatibility with AI-assisted development and implementation.

---

# Product Experience Principles

## 1. Clarity Over Decoration

Acadedx is an operational education platform.

Screens should help users understand:

* Where they are
* What context they are working in
* What action is available
* What data is important
* What requires attention

Avoid unnecessary visual complexity.

---

## 2. Institution Context Must Be Visible

Users must always understand which Institution they are working in.

Institution-scoped pages should show:

```text
Organization name
Institution name
Institution type, where useful
Active academic year, where useful
```

For the school MVP, the UI may display:

```text
School
Student
Parent
```

but the underlying system must remain Institution, Learner, and Guardian based.

---

## 3. Role-Aware Experience

Different users need different interfaces.

Examples:

* Platform Admin needs cross-customer operational controls.
* Organization Admin needs Organization-level visibility.
* Institution Admin needs academic and operational management.
* Teacher needs assigned class, section, attendance, and assessment workflows.
* Guardian needs linked learner summaries.
* Learner needs own academic and learning experience.

Do not force every role into the same navigation or dashboard.

---

## 4. Safety and Privacy by Design

Design must avoid accidental exposure of sensitive data.

Examples:

* Do not show detailed learner records in notification previews.
* Do not expose guardian contact data unnecessarily.
* Do not reveal cross-tenant information in errors.
* Do not show AI-generated insights to every role by default.
* Use confirmation dialogs for sensitive changes.

---

## 5. Progressive Complexity

MVP screens should be simple.

Advanced functions should appear only when needed.

Examples:

* Start with basic attendance marking before advanced analytics.
* Start with basic assessment results before complex grading workflows.
* Start with basic reports before export automation.
* Start with simple Institution setup before multi-institution comparison.

---

# Design System Scope

The design system applies to:

* Public website
* Authentication pages
* Onboarding flows
* Admin application
* Institution application
* Teacher workspace
* Guardian workspace
* Learner workspace
* Reports
* Forms
* Tables
* Dashboards
* Notifications
* AI-assisted workflows
* Future mobile and desktop experiences

---

# User Roles and Design Needs

| Role               | Primary Need                               | Design Priority               |
| ------------------ | ------------------------------------------ | ----------------------------- |
| Platform Admin     | Operate and support the platform           | Clarity, auditability, search |
| Organization Owner | Manage business-level education operations | Overview, control, trust      |
| Organization Admin | Manage Institutions and users              | Efficient navigation          |
| Institution Admin  | Run daily academic operations              | Fast workflows, data clarity  |
| Teacher            | Manage assigned teaching work              | Focused task completion       |
| Guardian           | Understand linked learner updates          | Simple summaries, privacy     |
| Learner            | View own progress and learning tasks       | Age-appropriate clarity       |
| Support Agent      | Troubleshoot safely                        | Limited data, clear context   |

---

# Terminology Design Rules

Code-level and architecture-level terminology:

```text
Organization
Institution
Learner
Guardian
Teacher
Academic Year
Class
Section
Subject
```

Allowed MVP UI labels:

| Architecture Term | School MVP Label           | Future Generic Label |
| ----------------- | -------------------------- | -------------------- |
| Organization      | Organization, Trust, Group | Organization         |
| Institution       | School                     | Institution          |
| Learner           | Student                    | Learner              |
| Guardian          | Parent                     | Guardian             |
| Academic Year     | Academic Session           | Academic Year        |
| Class             | Class, Grade               | Class                |
| Section           | Section                    | Section              |

Design rule:

Use UI labels based on target user comprehension, but do not rename domain concepts in code, API, database, route names, or state keys.

---

# Visual Brand Direction

Acadedx should feel:

```text
Trustworthy
Modern
Calm
Academic
Efficient
AI-enabled
Professional
Accessible
```

Avoid feeling:

```text
Toy-like
Overly corporate
Overdecorated
Distracting
Unstructured
Gamified by default
```

Gamification may be introduced for learner experiences later, but it should not dominate admin or teacher workflows.

---

# Design Foundations

The Acadedx design system includes:

```text
Color
Typography
Spacing
Layout
Elevation
Border radius
Iconography
Components
Motion
Accessibility
Content style
Responsive behavior
```

Detailed foundation files:

```text
docs/10-Design-System/COLORS.md
docs/10-Design-System/TYPOGRAPHY.md
docs/10-Design-System/UI_GUIDELINES.md
docs/10-Design-System/COMPONENT_LIBRARY.md
```

---

# Recommended UI Stack

Preferred frontend UI stack:

```text
Tailwind CSS
shadcn/ui or equivalent component primitives
Radix UI primitives where useful
Lucide or equivalent icon set
React Hook Form
Zod
TanStack Query
```

Design choices should favor:

* Accessibility
* Consistency
* Composability
* Type safety
* Developer velocity
* Low visual debt

---

# Layout System

## App Shell

Authenticated pages should use a consistent app shell.

Recommended structure:

```text
Top bar
Sidebar navigation
Main content area
Context indicator
User menu
Optional command palette
```

Institution-scoped pages should clearly show active Institution context.

---

## Page Layout

Standard page structure:

```text
Breadcrumbs
Page header
Page description
Primary actions
Filters or controls
Main content
Supporting panels
```

Example:

```text
Organization → Institution → Learners
Learners
Manage learner records, class assignments, guardians, attendance, and assessments.
[Add Learner]
[Search] [Class] [Section] [Status]
Learners Table
```

---

## Dashboard Layout

Dashboards should use:

* Summary cards
* Action shortcuts
* Recent activity
* Alerts
* Operational widgets
* Empty states for incomplete setup

Avoid overloading MVP dashboards with too many charts.

---

# Navigation System

Navigation must be:

* Role-aware
* Permission-aware
* Context-aware
* Scalable for future modules

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
Attendance
Assessments
Learning
Announcements
```

Only show navigation items that are relevant to the user.

Hidden navigation is not a security control.

---

# Context Switcher

The design system must support an Institution switcher.

MVP behavior:

* Show current Institution.
* Hide switcher or disable switching when only one Institution exists.

Future behavior:

* Allow users with multiple memberships to switch Institutions.
* Show Organization grouping.
* Preserve role-specific access.
* Clear stale data after switching.

Component name:

```text
InstitutionSwitcher
```

Do not name it:

```text
SchoolSwitcher
```

---

# Page Header Standard

Every major page should include a page header.

Required elements:

```text
Title
Description
Breadcrumbs where useful
Primary action
Secondary actions where useful
```

Example:

```text
Title: Learners
Description: Manage learner records for this institution.
Primary action: Add Learner
```

School MVP UI may display:

```text
Students
Manage student records for this school.
```

but code should remain learner/institution-based.

---

# Data Table Standard

Tables are central to admin and institution workflows.

Tables should support:

* Search
* Filters
* Sorting
* Pagination
* Loading state
* Empty state
* Error state
* Row actions
* Permission-aware actions
* Responsive fallback where needed

Table rows must not expose sensitive data unnecessarily.

Common table pages:

```text
Organizations
Institutions
Users
Learners
Guardians
Teachers
Attendance Sessions
Assessments
Announcements
Reports
Audit Logs
```

---

# Forms Standard

Forms should be:

* Clear
* Validated
* Accessible
* Sectioned for long workflows
* Safe for sensitive actions
* Consistent in layout

Form rules:

* Use clear labels.
* Use helper text where needed.
* Show field-level errors.
* Preserve user input on validation failure.
* Use confirmation for destructive actions.
* Avoid long ungrouped forms.

Recommended form structure:

```text
Section title
Short explanation
Fields
Inline validation
Primary action
Secondary action
```

---

# Wizard Standard

Use wizards for multi-step setup flows.

MVP wizard candidates:

```text
Organization setup
Institution setup
Academic setup
Invite users
```

Wizard design should include:

* Step indicator
* Back and next actions
* Save progress where useful
* Clear completion state
* Safe validation at each step

---

# Status System

Use consistent statuses.

Common statuses:

```text
ACTIVE
INACTIVE
PENDING
SUSPENDED
ARCHIVED
DRAFT
PUBLISHED
PROCESSING
FAILED
SUCCEEDED
LOCKED
```

Every status must have:

* Text label
* Visual treatment
* Accessible meaning
* Consistent usage

Do not rely on color alone.

---

# Empty State Standard

Empty states should be specific and action-oriented.

Good:

```text
No learners found.
Add your first learner to start managing attendance and assessments.
```

Bad:

```text
No data.
```

Empty states should include:

* What is missing
* Why it matters
* What action to take
* Optional illustration only if helpful

---

# Error State Standard

Error states must be safe and actionable.

Error state should include:

* Friendly message
* Retry action where appropriate
* Request ID where available
* Support guidance where useful

Do not show:

* Stack traces
* Internal provider errors
* SQL errors
* Tenant boundary details
* Sensitive learner or guardian information

Example:

```text
Something went wrong.
Please try again. If the issue continues, contact support with Request ID req_123.
```

---

# Permission and Forbidden States

When a user lacks access, show a clear but safe message.

Example:

```text
You do not have permission to view this page.
Contact your administrator if you believe this is incorrect.
```

Avoid:

```text
This learner belongs to another institution.
```

because it may leak tenant information.

---

# Confirmation Dialog Standard

Use confirmation dialogs for high-risk actions.

Examples:

* Archive learner
* Remove guardian link
* Remove teacher assignment
* Delete file
* Update locked attendance
* Update assessment result
* Suspend user
* Deactivate institution

Dialog should include:

* Action title
* Affected resource
* Consequence
* Cancel action
* Confirm action

High-risk future actions may require password re-entry or MFA.

---

# Notification Design

Notifications should be concise and privacy-safe.

Bad:

```text
Riya Sharma scored 12/50 in Mathematics and was absent 9 days this month.
```

Better:

```text
A new academic update is available for your linked learner.
```

Detailed data should require authenticated access.

Notification states:

```text
Unread
Read
Action required
Failed delivery
```

---

# AI Experience Design

AI features must be presented as assistive, not authoritative.

AI UI should clearly indicate:

* The output is AI-generated.
* The source data used where appropriate.
* Confidence or limitations where useful.
* User review is required for important decisions.
* Sensitive outputs should not be broadly shared.

AI action examples:

```text
Generate learner insight
Draft guardian communication
Summarize assessment performance
Extract text from document
```

AI UI must not expose:

* System prompts
* Provider debug payloads
* Cross-tenant context
* Raw sensitive data unnecessarily

---

# Privacy in UI

Design must avoid unnecessary exposure of sensitive data.

Examples:

* Mask or minimize guardian contact details where possible.
* Avoid showing full learner details in lists unless needed.
* Use detail pages for sensitive records.
* Avoid including sensitive information in breadcrumbs.
* Avoid sensitive details in browser titles.
* Avoid sensitive data in downloadable filenames where possible.

---

# Accessibility Requirements

All design system components must support:

* Keyboard navigation
* Visible focus states
* Semantic HTML
* Accessible form labels
* Screen-reader-friendly errors
* Dialog focus trapping
* Sufficient contrast
* Text alternatives for icons
* Non-color-only meaning
* Responsive zoom behavior

Accessibility is not optional.

---

# Responsive Design

MVP must support:

```text
Desktop
Tablet
Basic mobile web usability
```

Priority mobile-friendly workflows:

```text
Login
Invitation acceptance
Guardian dashboard
Teacher attendance
Announcements
Learner profile summary
```

Complex admin tables may use responsive table alternatives.

---

# Motion and Animation

Motion should be subtle and functional.

Use motion for:

* Loading transitions
* Drawer/dialog open and close
* Toast feedback
* Step transitions
* Progress indicators

Avoid:

* Distracting animations
* Excessive decorative motion
* Motion that slows task completion

Respect reduced motion preferences.

---

# Iconography

Use a consistent icon set.

Recommended:

```text
Lucide
```

Icon rules:

* Icons should support meaning, not replace labels.
* Use text labels for primary navigation.
* Avoid ambiguous icons without tooltips.
* Do not rely on icons alone for status.

---

# Content Style

Content should be:

```text
Clear
Direct
Action-oriented
Professional
Calm
Consistent
```

Avoid:

```text
Overly playful copy
Technical backend jargon for non-technical users
Ambiguous action labels
Fear-inducing error messages
```

Preferred action labels:

```text
Add Learner
Invite User
Mark Attendance
Create Assessment
Enter Results
Generate Report
Archive Learner
```

UI may use:

```text
Add Student
```

where school MVP context requires it, but component/action names should remain learner-based.

---

# Data Density

Acadedx serves operational users who may manage many records.

Use moderate data density:

* Tables should be scannable.
* Important columns should be visible.
* Avoid excessive whitespace in admin tables.
* Use cards for summaries and dashboards.
* Use progressive disclosure for details.

---

# Dashboard Design

MVP dashboards should answer:

```text
What needs attention today?
What changed recently?
What action should I take next?
```

Institution Admin dashboard MVP widgets:

```text
Learner count
Teacher count
Class/section count
Today's attendance status
Recent assessments
Recent announcements
Setup completion
```

Teacher dashboard MVP widgets:

```text
Assigned classes
Today's attendance tasks
Upcoming assessments
Pending result entry
Announcements
```

Guardian and learner dashboards may be V1 unless part of MVP.

---

# Report Design

Reports should be:

* Filterable
* Scoped
* Export-aware
* Permission-aware
* Clear about date ranges
* Clear about included records

Report filters should show:

```text
Academic year
Class
Section
Subject
Date range
Status
```

Exports should require explicit action and show job status if asynchronous.

---

# Design Tokens

Design tokens should define:

```text
Colors
Typography
Spacing
Radius
Shadows
Z-index
Breakpoints
Motion
```

Token values are documented in:

```text
docs/10-Design-System/COLORS.md
docs/10-Design-System/TYPOGRAPHY.md
```

---

# Component Library

The component library should include:

```text
Foundational UI components
Layout components
Navigation components
Form components
Data display components
Feedback components
Permission gates
Domain components
```

Detailed component standards are defined in:

```text
docs/10-Design-System/COMPONENT_LIBRARY.md
docs/08-Frontend/COMPONENTS.md
```

---

# MVP Design System Scope

The MVP design system must include:

```text
Color tokens
Typography tokens
Spacing rules
Button
Input
Textarea
Select
Checkbox
RadioGroup
Switch
Label
Card
Dialog
DropdownMenu
Tabs
Badge
Avatar
Table
DataTable
Pagination
Skeleton
Alert
Toast
Tooltip
DatePicker
FileUpload
PageShell
PageHeader
Breadcrumbs
MainSidebar
TopNav
UserMenu
InstitutionSwitcher
PermissionGate
LoadingState
EmptyState
ErrorState
ForbiddenState
UnauthorizedState
ConfirmDialog
FormErrorSummary
```

Domain components required for MVP:

```text
OrganizationForm
OrganizationsTable
InstitutionForm
InstitutionsTable
InstitutionProfileForm
InstitutionSettingsForm
AcademicYearForm
AcademicYearsTable
ClassForm
ClassesTable
SectionForm
SectionsTable
SubjectForm
SubjectsTable
LearnersTable
LearnerForm
LearnerProfileCard
LearnerGuardiansPanel
GuardianForm
GuardiansTable
GuardianLearnerLinksPanel
TeacherForm
TeachersTable
TeacherAssignmentForm
TeacherAssignmentsTable
AttendanceMarkingGrid
AttendanceSessionTable
AssessmentForm
AssessmentsTable
AssessmentResultsEntryGrid
AnnouncementsTable
AnnouncementForm
ReportsDashboard
ReportCard
```

---

# Deferred Design System Scope

The following can be deferred:

```text
Advanced chart system
Advanced analytics dashboards
Full mobile design system
Offline state patterns
Gamification patterns
AI streaming components
Voice input components
Bulk import wizard patterns
Advanced file manager
Custom role builder UI
Enterprise branding / white labeling
Theme editor
```

Do not block these future capabilities through MVP-only design decisions.

---

# Anti-Patterns

Avoid:

```text
Hardcoded school-only navigation
Using Student in code models
Using Parent in code models
Using SchoolSwitcher as component name
Showing all menu items to all users
Using frontend-only permission checks
Generic "No data" empty states
Raw backend error messages in UI
Unscoped dashboards
Overloaded admin screens
Unvalidated bulk actions
Sensitive data in notifications
Unclear AI-generated content
```

---

# Design Review Checklist

Before approving a screen or component, confirm:

* Organization and Institution context is clear where needed.
* UI labels are appropriate for the user.
* Code terminology remains Institution, Learner, and Guardian.
* No `schoolId`, `studentId`, or `parentId` is introduced.
* Page has clear title and purpose.
* Primary action is obvious.
* Loading, empty, error, and forbidden states exist.
* Role and permission behavior is defined.
* Sensitive data is minimized.
* AI-generated content is clearly labeled where applicable.
* Forms are accessible and validated.
* Tables are scannable and filterable.
* Mobile behavior is acceptable for MVP.
* Component uses design system primitives.
* Copy is clear and action-oriented.

---

# Related Documents

* docs/10-Design-System/UI_GUIDELINES.md
* docs/10-Design-System/COLORS.md
* docs/10-Design-System/TYPOGRAPHY.md
* docs/10-Design-System/COMPONENT_LIBRARY.md
* docs/08-Frontend/PAGES.md
* docs/08-Frontend/ROUTING.md
* docs/08-Frontend/STATE_MANAGEMENT.md
* docs/08-Frontend/COMPONENTS.md
* docs/11-Security/PRIVACY.md
* docs/11-Security/RBAC.md

