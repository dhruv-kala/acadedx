# UI Guidelines

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Product Design  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines user interface guidelines for Acadedx.

These guidelines ensure that Acadedx screens are consistent, accessible, role-aware, privacy-safe, and aligned with the product architecture:

```text
Organization → Institution → Academic Operations
````

Acadedx is a school-focused MVP, but the UI must remain ready for future Organization → multiple Institutions support.

A School is an Institution type.

UI labels may use “School”, “Student”, and “Parent” where appropriate for the MVP experience, but code-level concepts, routes, state, API calls, and component names must remain aligned with:

```text
Organization
Institution
Learner
Guardian
Teacher
```

---

# UI Goals

The Acadedx UI must:

1. Help users complete education operations quickly.
2. Make Organization and Institution context clear.
3. Support school-friendly MVP terminology without hardcoding school-only structure.
4. Reduce cognitive load for administrators, teachers, guardians, and learners.
5. Protect sensitive learner and guardian data.
6. Make permissions and unavailable actions clear.
7. Provide accessible, responsive, and predictable interactions.
8. Treat AI features as assistive and reviewable.
9. Support future multi-institution navigation.
10. Stay consistent across pages, forms, tables, dashboards, and reports.

---

# Core UI Principles

## 1. Context First

Users must always know where they are working.

Institution-scoped pages should show:

```text
Organization name
Institution name
Active academic year, where useful
Current section/class context, where useful
```

Example:

```text
DGIS Education Group → Acadedx Public School → Learners
```

For school MVP UI, this may display as:

```text
Acadedx Public School → Students
```

But route, state, and API must still use `institutionId` and `learnerId`.

---

## 2. Task-Oriented Screens

Screens should be organized around user tasks, not database tables alone.

Examples:

| User Goal                | UI Pattern                                  |
| ------------------------ | ------------------------------------------- |
| Add a learner            | Form with clear required fields             |
| Mark attendance          | Class/section selector + attendance grid    |
| Enter assessment results | Assessment context + result entry grid      |
| Invite teacher           | User invite form + role selector            |
| Link guardian            | Learner context + guardian search/link form |
| Generate report          | Filters + preview + export action           |

---

## 3. Progressive Disclosure

Show essential information first.

Reveal advanced details only when needed.

Examples:

* Learners list shows name, class, section, status, and primary guardian.
* Learner detail page shows tabs for attendance, assessments, guardians, and files.
* Advanced audit metadata is hidden unless user has permission.
* AI insight details are collapsed behind review panels.

---

## 4. Safe Defaults

UI defaults should reduce risk.

Examples:

* Private files by default.
* Destructive actions require confirmation.
* Export actions require explicit click.
* Sensitive details are hidden unless needed.
* AI-generated drafts require review before sending.
* Role assignment defaults to least privilege.

---

## 5. Consistency Over Novelty

Use consistent patterns for:

* Page headers
* Forms
* Tables
* Filters
* Empty states
* Error states
* Confirmation dialogs
* Status badges
* Navigation
* Reports

Do not create one-off patterns unless the workflow genuinely requires it.

---

# Terminology Guidelines

## Architecture Terms

Use these terms in product architecture, code, routes, API, state, and documentation:

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

## MVP UI Labels

For school-focused MVP, user-facing labels may use:

| Architecture Term | MVP UI Label                 |
| ----------------- | ---------------------------- |
| Institution       | School                       |
| Learner           | Student                      |
| Guardian          | Parent                       |
| Academic Year     | Academic Session             |
| Class             | Class / Grade                |
| Organization      | Organization / Group / Trust |

## Rule

Do not let UI labels leak into backend/API/state naming.

Good code:

```tsx
<LearnerProfileCard learnerId={learnerId} institutionId={institutionId} />
```

Acceptable UI text:

```text
Student Profile
```

Bad code:

```tsx
<StudentProfileCard studentId={studentId} schoolId={schoolId} />
```

---

# Page Layout Guidelines

## Standard Page Structure

Most authenticated pages should follow this structure:

```text
Breadcrumbs
Page Header
Description
Primary Actions
Filters / Search
Main Content
Secondary Content
```

Example:

```text
Breadcrumbs: Institution → Learners
Title: Learners
Description: Manage learner records, guardians, attendance, and assessments.
Primary Action: Add Learner
Filters: Search, Academic Year, Class, Section, Status
Content: Learners Table
```

---

## Page Header

Every major page should include:

```text
Title
Short description
Breadcrumbs where useful
Primary action
Secondary actions where useful
```

Good:

```text
Learners
Manage learner records, guardians, attendance, and assessments.
[Add Learner]
```

School MVP UI may show:

```text
Students
Manage student records, parents, attendance, and assessments.
[Add Student]
```

---

## Page Width

Recommended layout behavior:

| Page Type         | Width                            |
| ----------------- | -------------------------------- |
| Dashboard         | Full content width               |
| Table/list page   | Full content width               |
| Detail page       | Full content width with sections |
| Form page         | Medium width where practical     |
| Auth page         | Narrow centered layout           |
| Report page       | Full width                       |
| Admin search page | Full width                       |

Avoid extremely wide single-column forms.

---

# Navigation Guidelines

## Navigation Must Be Role-Aware

Show only navigation items relevant to the current user.

Institution Admin may see:

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

Teacher may see:

```text
Dashboard
My Classes
Attendance
Assessments
Announcements
```

Guardian may see:

```text
Dashboard
Linked Learners
Announcements
Messages
Fees
```

Learner may see:

```text
Dashboard
Profile
Attendance
Assessments
Learning
Announcements
```

Hidden navigation is not a security control.

Backend authorization remains mandatory.

---

## Navigation Labels

Use clear nouns.

Good:

```text
Learners
Guardians
Teachers
Attendance
Assessments
Reports
Settings
```

Avoid vague labels:

```text
Management
Data
Operations
Miscellaneous
```

---

## Active Context Display

The app shell should show the active Institution.

Examples:

```text
Acadedx Public School
2026-2027
```

Future multi-institution UI should allow switching through `InstitutionSwitcher`.

Do not name this component `SchoolSwitcher`.

---

# Breadcrumb Guidelines

Use breadcrumbs on nested pages.

Examples:

```text
Institutions → Acadedx Public School → Learners → Riya Sharma
```

For school MVP UI:

```text
School → Students → Riya Sharma
```

Guidelines:

* Keep breadcrumbs short.
* Do not include sensitive details unnecessarily.
* Avoid long guardian contact values or private assessment details.
* Link parent levels where useful.

---

# Dashboard Guidelines

Dashboards should answer:

```text
What needs attention?
What changed recently?
What action should I take next?
```

## Institution Admin Dashboard MVP Widgets

Recommended widgets:

```text
Learner count
Teacher count
Class and section count
Today's attendance summary
Recent assessments
Recent announcements
Setup completion
Pending actions
```

## Teacher Dashboard Widgets

Recommended widgets:

```text
Assigned classes
Today's attendance tasks
Upcoming assessments
Pending result entry
Recent announcements
```

## Guardian Dashboard Widgets

Recommended V1 widgets:

```text
Linked learners
Attendance summary
Assessment summary
Announcements
Fee summary, if enabled
```

## Learner Dashboard Widgets

Recommended V1 widgets:

```text
Attendance summary
Assessment summary
Announcements
Assigned learning resources
AI learning support, if enabled
```

Avoid overloaded dashboards in MVP.

---

# Table Guidelines

Tables are core to Acadedx operations.

## Table Requirements

Tables should support:

```text
Search
Filters
Sorting
Pagination
Loading state
Empty state
Error state
Row actions
Permission-aware actions
Responsive behavior
```

## Common Table Columns

### Learners Table

Recommended columns:

```text
Name
Admission Number
Class
Section
Primary Guardian
Status
Actions
```

UI may label “Learners” as “Students”.

### Guardians Table

Recommended columns:

```text
Name
Relationship
Phone
Email
Linked Learners
Status
Actions
```

UI may label “Guardians” as “Parents”.

### Teachers Table

Recommended columns:

```text
Name
Employee Code
Department
Assigned Classes
Status
Actions
```

### Attendance Sessions Table

Recommended columns:

```text
Date
Class
Section
Session Type
Marked Count
Status
Actions
```

### Assessments Table

Recommended columns:

```text
Name
Class
Section
Subject
Assessment Type
Date
Status
Actions
```

---

## Table Action Guidelines

Actions should be clear and permission-aware.

Common actions:

```text
View
Edit
Archive
Assign
Link
Download
Export
```

Destructive actions should not appear as the primary action.

Use confirmation dialogs for destructive actions.

---

## Bulk Actions

Bulk actions are risky.

MVP should limit bulk actions.

If bulk actions are used:

* Show selected count.
* Validate permission.
* Show confirmation.
* Explain impact.
* Audit sensitive operations.
* Support undo only if technically safe.

---

# Form Guidelines

## Form Structure

Forms should be grouped into clear sections.

Example learner form:

```text
Basic Details
Academic Assignment
Guardian Information
Additional Details
```

Avoid long unstructured forms.

---

## Required Fields

Required fields should be visibly marked.

Example:

```text
Full Name *
```

Do not rely only on placeholder text.

---

## Field Labels

Labels should be clear and stable.

Good:

```text
Admission Number
Academic Year
Class
Section
Primary Guardian
```

Avoid:

```text
Code
Type
Value
Data
```

unless context is obvious.

---

## Helper Text

Use helper text to prevent mistakes.

Example:

```text
Admission number must be unique within this institution.
```

---

## Validation

Show validation errors:

* Near the relevant field
* In plain language
* Without exposing internal error details

Example:

```text
Admission number is already used in this institution.
```

Do not show raw backend stack traces or database constraint names.

---

## Save Actions

Recommended form actions:

```text
Save
Save Changes
Create Learner
Invite User
Cancel
```

Avoid ambiguous actions:

```text
Submit
Done
Proceed
```

unless the flow context is obvious.

---

# Search and Filter Guidelines

List pages should provide search and filters where useful.

Recommended filters:

| Page        | Filters                                      |
| ----------- | -------------------------------------------- |
| Learners    | Academic year, class, section, status        |
| Guardians   | Relationship, status, linked learner         |
| Teachers    | Department, subject, status                  |
| Attendance  | Date, class, section, session type           |
| Assessments | Academic year, class, section, subject, type |
| Reports     | Academic year, class, section, date range    |

Filters should be reflected in URL query parameters for shareable pages.

---

# Empty State Guidelines

Empty states should explain:

1. What is missing.
2. Why it matters.
3. What to do next.

Good:

```text
No learners found.
Add your first learner to start managing attendance and assessments.
[Add Learner]
```

Bad:

```text
No data.
```

When user lacks permission to create, remove the action and explain safely:

```text
No learners found.
You can view learners once they are added by an administrator.
```

---

# Loading State Guidelines

Use loading states that preserve layout.

Recommended patterns:

```text
Skeleton card
Skeleton table rows
Inline spinner for small actions
Progress bar for long jobs
```

Avoid full-screen loaders for page-level data where the app shell can render.

---

# Error State Guidelines

Error states should be safe and actionable.

Include:

```text
Friendly message
Retry action where useful
Request ID where available
Support guidance where useful
```

Good:

```text
We could not load learners.
Try again. If the issue continues, contact support with Request ID req_123.
```

Avoid:

```text
SQL constraint failed on learners.organization_id.
```

---

# Permission State Guidelines

When a user lacks permission:

```text
You do not have permission to perform this action.
```

For disabled actions, explain briefly:

```text
Only Institution Admins can invite users.
```

Do not reveal tenant details:

Bad:

```text
This learner belongs to another institution.
```

Better:

```text
This record was not found or you do not have access to it.
```

---

# Confirmation Dialog Guidelines

Use confirmations for actions with lasting impact.

Examples:

```text
Archive learner
Unlink guardian
Remove teacher assignment
Delete file
Deactivate institution
Suspend user
Update locked attendance
Update assessment result
```

Dialog content should include:

```text
Action
Affected record
Consequence
Cancel button
Confirm button
```

Example:

```text
Archive learner?
This learner will no longer appear in active learner lists. Historical records will be retained.
[Cancel] [Archive Learner]
```

---

# Status Badge Guidelines

Statuses must be visible through text and visual treatment.

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

Rules:

* Use consistent labels.
* Do not rely only on color.
* Avoid inventing similar statuses with overlapping meaning.

---

# Notification Guidelines

Notifications should be concise and privacy-safe.

## Good Notification

```text
A new academic update is available for your linked learner.
```

## Bad Notification

```text
Riya Sharma scored 12/50 in Mathematics and was absent 9 days this month.
```

Detailed information should require authenticated page access.

---

# Toast Guidelines

Use toasts for short feedback.

Good toast examples:

```text
Learner created successfully.
Attendance saved.
Invitation sent.
Report export started.
```

Avoid using toasts for:

* Long explanations
* Critical blocking errors
* Complex validation errors
* Sensitive details

Use inline or page-level states instead.

---

# Modal and Dialog Guidelines

Use dialogs for focused tasks.

Good modal candidates:

```text
Invite user
Confirm archive
Link guardian
Assign teacher
Preview announcement
```

Use full pages for complex forms:

```text
Create learner
Enter assessment results
Academic setup
Institution setup
Bulk import
```

---

# Tabs Guidelines

Use tabs when sections are peer-level.

Example learner detail tabs:

```text
Overview
Profile
Guardians
Class Assignment
Attendance
Assessments
Files
AI Insights
```

Rules:

* Do not hide critical actions deep inside tabs.
* Keep tab names short.
* URL-backed tabs are preferred for shareable detail pages.

---

# Report UI Guidelines

Reports should include:

```text
Clear title
Scope summary
Filters
Data table or summary
Export action where permitted
Generated timestamp
Empty state
```

Report pages must show current scope:

```text
Institution: Acadedx Public School
Academic Year: 2026-2027
Class: Grade 8
Section: A
```

Exports should be explicit and permission-aware.

---

# File Upload Guidelines

File upload UI must show:

```text
Allowed file types
Maximum file size
Upload progress
Validation errors
Uploaded file name
Remove action
```

Supported MVP types:

```text
JPG
JPEG
PNG
WEBP
PDF
```

Do not imply a file is accepted until backend confirms it.

---

# AI UI Guidelines

AI features must be presented as assistive.

## Required AI UI Cues

AI-generated content should show:

```text
AI-generated label
Generated timestamp
Source context where appropriate
Review required where applicable
Regenerate or discard option where useful
```

## AI Drafts

AI-generated communication drafts must not be sent automatically.

Required flow:

```text
Generate draft
Review draft
Edit draft
Confirm audience
Send manually
```

## AI Insights

AI learner insights should not be treated as final decisions.

Use language such as:

```text
Suggested insight
AI-generated summary
Review before sharing
```

Avoid:

```text
Final diagnosis
Guaranteed prediction
Definitive risk score
```

---

# Privacy UI Guidelines

UI must minimize sensitive data exposure.

Rules:

* Avoid sensitive data in page titles.
* Avoid sensitive data in notifications.
* Avoid sensitive data in URLs.
* Avoid unnecessary guardian contact display.
* Avoid exposing full learner records in tables.
* Do not show raw AI prompts or OCR text unless required.
* Show sensitive details only on authorized detail pages.

---

# Accessibility Guidelines

All UI must support:

```text
Keyboard navigation
Visible focus states
Semantic headings
Accessible form labels
Screen-reader-friendly validation
Dialog focus trapping
Text alternatives for icons
Sufficient contrast
Non-color-only status indicators
Reduced motion preference
```

Accessibility should be built into reusable components.

---

# Responsive Guidelines

MVP must support:

```text
Desktop
Tablet
Basic mobile web
```

Priority mobile workflows:

```text
Login
Invitation acceptance
Teacher attendance
Guardian linked learner view
Announcements
Password reset
```

Tables on mobile should use:

* Horizontal scroll where acceptable
* Card layout where better
* Reduced columns
* Sticky key identifiers where useful

---

# Data Density Guidelines

Operational screens should use moderate density.

Guidelines:

* Use compact tables for admin workflows.
* Use cards for dashboards and summaries.
* Avoid excessive whitespace in high-volume screens.
* Avoid cramped forms.
* Use section grouping for complex records.

---

# Copy Guidelines

UI copy should be:

```text
Clear
Direct
Specific
Calm
Action-oriented
```

Good:

```text
Add Learner
Mark Attendance
Enter Results
Invite User
Archive Learner
```

Avoid vague labels:

```text
Submit
Proceed
Manage
Execute
```

unless context is obvious.

---

# Date and Time Guidelines

Display dates and times using the user or Institution timezone.

Backend stores timestamps in UTC.

UI should show:

```text
Date
Time
Timezone where ambiguity matters
```

Examples:

```text
05 Jul 2026
05 Jul 2026, 10:30 AM
Asia/Kolkata
```

For attendance and academic records, Institution timezone is usually preferred.

---

# Number and Currency Guidelines

Use locale-aware formatting.

Currency examples:

```text
₹2,500.00
INR 2,500
```

Fee and payment UI must show currency clearly.

Do not assume INR forever if future multi-region support is expected.

---

# Internationalization Readiness

MVP may be English-first.

Design should still support future localization.

Guidelines:

* Avoid hardcoded labels inside reusable domain logic.
* Allow label mapping for Institution/Learner/Guardian.
* Avoid text embedded in images.
* Avoid layouts that break with longer translated strings.
* Use locale-aware date and number formatting.

---

# Content Security Guidelines

Do not render untrusted HTML unless sanitized.

Applies to:

```text
Announcements
Messages
AI-generated content
OCR output
Imported content
Rich text fields
```

If rich text is supported, sanitize before rendering.

---

# Admin UI Guidelines

Admin UI is high-risk.

Admin screens should:

* Show clear scope.
* Require confirmation for sensitive actions.
* Show audit context where useful.
* Avoid exposing excessive tenant data.
* Provide search and filters.
* Avoid bulk destructive actions in MVP.

Platform Admin access must feel deliberate, not casual.

---

# Teacher UI Guidelines

Teacher UI should prioritize daily tasks.

Important teacher workflows:

```text
View assigned classes
Mark attendance
Enter assessment results
View announcements
Review learner summaries
```

Avoid exposing full institution administration UI to teachers.

---

# Guardian UI Guidelines

Guardian UI should be simple and privacy-safe.

Important guardian workflows:

```text
View linked learners
View academic updates
View announcements
Receive communication
View fees, if enabled
```

Avoid dense admin-style tables for guardians.

---

# Learner UI Guidelines

Learner UI should be simple, focused, and age-appropriate.

Important learner workflows:

```text
View own profile
View attendance summary
View assessment results
Access learning tasks
Use AI learning support, if enabled
```

Avoid exposing institutional administrative language to learners.

---

# MVP UI Scope

The MVP UI must include guidelines and components for:

```text
Public landing
Login
Invitation acceptance
Password reset
Context selection
Platform admin dashboard
Organization onboarding
Institution onboarding
Institution dashboard
Institution profile
Institution users
Academic setup
Learner management
Guardian management
Teacher management
Attendance
Assessments
Announcements
Reports
Account profile
Error states
Forbidden and unauthorized states
```

---

# Deferred UI Scope

The following may be deferred:

```text
Advanced analytics dashboards
Full guardian portal
Full learner portal
Full teacher workspace
Fee management UI
AI insight center
OCR review workflow
Message inbox
Bulk import UI
Feature flag editor
Advanced audit explorer
Mobile app-specific UI
White-label theme editor
```

Do not block these future features with MVP-only design assumptions.

---

# UI Anti-Patterns

Avoid:

```text
Hardcoded school-only labels everywhere
Using Student in component/state/API code
Using Parent in component/state/API code
Unscoped dashboards
Tables without empty states
Forms without field errors
Buttons hidden as the only security control
Raw backend errors shown to users
Sensitive data in notifications
Unclear AI-generated outputs
Overloaded dashboards
Too many modal forms
Unconfirmed destructive actions
```

---

# UI Testing Checklist

UI tests should verify:

* Page renders correct title and context.
* Loading state appears.
* Empty state is useful.
* Error state is safe.
* Forbidden state does not leak tenant details.
* Forms validate required fields.
* Backend validation maps to fields.
* Role-based actions appear or disappear correctly.
* Destructive actions require confirmation.
* Tables support filters and pagination.
* Responsive layout remains usable.
* Keyboard navigation works for core components.

---

# UI Review Checklist

Before approving a UI screen, confirm:

* Active Organization or Institution context is clear.
* UI labels match target user comprehension.
* Code terminology remains Organization, Institution, Learner, Guardian.
* No `schoolId`, `studentId`, or `parentId` is introduced in code.
* Primary action is obvious.
* Secondary actions are not distracting.
* Loading, empty, error, and forbidden states exist.
* Sensitive data is minimized.
* Permission behavior is clear.
* Forms are accessible.
* Tables are scannable.
* Destructive actions are confirmed.
* AI output is clearly labeled where applicable.
* Page works acceptably on desktop, tablet, and basic mobile web.

---

# Related Documents

* docs/10-Design-System/DESIGN_SYSTEM.md
* docs/10-Design-System/COLORS.md
* docs/10-Design-System/TYPOGRAPHY.md
* docs/10-Design-System/COMPONENT_LIBRARY.md
* docs/08-Frontend/PAGES.md
* docs/08-Frontend/ROUTING.md
* docs/08-Frontend/STATE_MANAGEMENT.md
* docs/08-Frontend/COMPONENTS.md
* docs/11-Security/PRIVACY.md
* docs/11-Security/RBAC.md