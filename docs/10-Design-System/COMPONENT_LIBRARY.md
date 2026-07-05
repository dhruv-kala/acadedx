# Component Library

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Product Design  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the Acadedx component library.

The component library provides reusable UI, layout, navigation, form, data display, feedback, access-control, and domain components for the Acadedx frontend.

Acadedx is a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

Components may render school-friendly UI labels such as “School”, “Student”, and “Parent” where appropriate for the MVP, but component names, props, route parameters, state keys, API clients, and domain models must use:

```text
Organization
Institution
Learner
Guardian
Teacher
```

Do not use these as core code-level component concepts:

```text
School
Student
Parent
```

unless the component is explicitly a UI label adapter or legacy compatibility wrapper.

---

# Component Library Goals

The component library must ensure:

1. Consistent UI across Acadedx product surfaces.
2. Fast MVP implementation through reusable components.
3. Clear separation between primitive, shared, and domain components.
4. Accessibility by default.
5. Organization and Institution context support.
6. Role-aware and permission-aware rendering.
7. Safe handling of learner and guardian data.
8. Reusable patterns for forms, tables, dashboards, reports, and workflows.
9. Future readiness for multi-institution, mobile, AI, and white-label capabilities.
10. Stable component contracts for developers, QA, and AI coding agents.

---

# Component Categories

The Acadedx component library is organized into:

```text
UI primitives
Layout components
Navigation components
Access-control components
Feedback components
Form components
Data display components
Dashboard components
Domain components
Workflow components
Report components
AI components
Admin components
```

---

# Recommended Directory Structure

```text
src/
├── components/
│   ├── ui/
│   ├── layout/
│   ├── navigation/
│   ├── access/
│   ├── feedback/
│   ├── forms/
│   ├── data-display/
│   └── shared/
├── features/
│   ├── auth/
│   ├── organizations/
│   ├── institutions/
│   ├── academic/
│   ├── learners/
│   ├── guardians/
│   ├── teachers/
│   ├── attendance/
│   ├── assessments/
│   ├── communication/
│   ├── reports/
│   ├── files/
│   └── ai/
└── app/
```

Shared, generic components belong under:

```text
src/components/
```

Feature-specific domain components belong under:

```text
src/features/<feature-name>/components/
```

Examples:

```text
src/features/learners/components/LearnersTable.tsx
src/features/guardians/components/GuardianLinkForm.tsx
src/features/attendance/components/AttendanceMarkingGrid.tsx
```

---

# Component Naming Rules

Use PascalCase for React components.

Good:

```text
InstitutionSwitcher
LearnerProfileCard
GuardianLinkForm
TeacherAssignmentForm
AttendanceStatusBadge
AssessmentResultsEntryGrid
```

Avoid:

```text
SchoolSwitcher
StudentProfileCard
ParentLinkForm
```

Allowed exceptions:

* UI copy components that map domain terms to display labels.
* Legacy import/export adapters.
* Compatibility wrappers clearly documented as temporary.

---

# Component Props Rules

Props must be explicit and typed.

Good:

```ts
type LearnerProfileCardProps = {
  organizationId: string;
  institutionId: string;
  learnerId: string;
};
```

Bad:

```ts
type Props = any;
```

Institution-scoped components must include or receive:

```text
organizationId
institutionId
```

Do not pass:

```text
schoolId
```

---

# Component Composition Rules

Components should be composed from smaller primitives.

Good:

```tsx
<PageShell>
  <PageHeader title="Learners" />
  <LearnerFilters />
  <LearnersTable />
</PageShell>
```

Avoid large components that combine:

```text
routing
data fetching
authorization
forms
tables
business logic
modals
```

into one file.

---

# UI Primitive Components

UI primitives are generic and reusable. They must not contain Acadedx domain logic.

## Button

Purpose:

Render clickable actions.

Variants:

```text
primary
secondary
ghost
outline
destructive
link
```

Sizes:

```text
sm
md
lg
icon
```

Usage examples:

```text
Add Learner
Save Changes
Invite User
Mark Attendance
Archive Learner
```

Rules:

* Use one primary button per main action area where possible.
* Use destructive variant only for destructive confirmed actions.
* Button text must be action-oriented.
* Disabled buttons should explain why when the reason is not obvious.

---

## Input

Purpose:

Single-line text input.

Use for:

```text
name
email
phone
admission number
employee code
search
```

Rules:

* Must have visible label.
* Must show validation errors.
* Must support disabled and readonly states.
* Placeholder must not replace label.

---

## Textarea

Purpose:

Multi-line text entry.

Use for:

```text
remarks
announcement body
assessment comments
guardian notes
AI draft editing
```

Rules:

* Use character count where limits exist.
* Avoid storing sensitive long text unnecessarily in browser state.
* Validate length on frontend and backend.

---

## Select

Purpose:

Single-value selection.

Use for:

```text
role
status
institution type
academic year
class
section
subject
relationship
assessment type
attendance status
```

Rules:

* Use clear labels.
* Support empty state.
* Support disabled options where useful.
* Avoid very long select lists; use searchable combobox when needed.

---

## Combobox

Purpose:

Searchable selection.

Use for:

```text
select learner
select guardian
select teacher
select institution
select subject
```

Rules:

* Use for long lists.
* Must be keyboard accessible.
* Must show loading and empty states.
* Must preserve tenant scope in query.

---

## Checkbox

Purpose:

Boolean or multi-select option.

Use for:

```text
notification preferences
permissions
feature selection
bulk row selection
```

Rules:

* Label must be clickable.
* Avoid using checkbox for mutually exclusive options.

---

## RadioGroup

Purpose:

Single choice among visible options.

Use for:

```text
gender, if collected
relationship type where options are small
attendance session type
billing frequency
```

Rules:

* Prefer radio group when choices are few and should be visible.
* Use select for longer lists.

---

## Switch

Purpose:

Toggle setting on/off.

Use for:

```text
feature enabled
notification preference
active setting
```

Rules:

* Do not use switch for destructive or high-risk changes without confirmation.
* Label must describe the result.

---

## Card

Purpose:

Group related content.

Use for:

```text
dashboard metrics
profile summary
settings sections
report summaries
AI insight panels
```

Rules:

* Use consistent padding.
* Include title where needed.
* Avoid nesting too many cards.

---

## Dialog

Purpose:

Focused modal interaction.

Use for:

```text
confirm action
invite user
link guardian
assign teacher
preview announcement
```

Rules:

* Must trap focus.
* Must have accessible title.
* Must close on cancel.
* Must not be used for complex long workflows that deserve a page.

---

## Sheet / Drawer

Purpose:

Secondary panel interaction.

Use for:

```text
detail preview
filter panel
quick edit
activity panel
```

Rules:

* Use carefully on mobile.
* Do not hide critical workflows in sheets.

---

## DropdownMenu

Purpose:

Compact secondary actions.

Use for:

```text
row actions
user menu
more actions
```

Rules:

* Destructive actions should be visually separated.
* Important primary actions should not be hidden only in dropdowns.

---

## Tabs

Purpose:

Peer-level sections within a detail page.

Use for:

```text
Learner detail sections
Institution settings sections
Teacher assignment sections
Reports views
```

Rules:

* Avoid too many tabs.
* Use URL-backed tabs where shareability matters.
* Do not hide urgent actions deep in tabs.

---

## Badge

Purpose:

Compact status or category indicator.

Use for:

```text
ACTIVE
PENDING
SUSPENDED
ARCHIVED
AI-GENERATED
LOCKED
```

Rules:

* Badge must include text.
* Do not rely on color alone.

---

## Avatar

Purpose:

User or entity visual identity.

Use for:

```text
user menu
teacher profile
guardian profile
learner profile
```

Rules:

* Use initials fallback.
* Do not require profile image.
* Do not expose private images without permission.

---

## Tooltip

Purpose:

Short contextual help.

Rules:

* Do not put essential information only inside tooltips.
* Must be keyboard accessible.
* Use for clarification, not long explanations.

---

## Toast

Purpose:

Short temporary feedback.

Use for:

```text
Learner created successfully.
Attendance saved.
Invitation sent.
Report export started.
```

Rules:

* Do not use toast for complex errors.
* Do not include sensitive learner details.
* Keep text concise.

---

## Skeleton

Purpose:

Loading placeholder.

Use for:

```text
tables
cards
profile sections
dashboards
reports
```

Rules:

* Preserve layout shape.
* Avoid unnecessary skeletons for very fast content.

---

# Layout Components

## PageShell

Purpose:

Standard wrapper for authenticated pages.

Props:

```ts
type PageShellProps = {
  children: React.ReactNode;
  maxWidth?: 'full' | 'content' | 'narrow';
};
```

Use for:

```text
dashboard pages
list pages
detail pages
forms
reports
```

---

## PageHeader

Purpose:

Render page title, description, breadcrumbs, and actions.

Props:

```ts
type PageHeaderProps = {
  title: string;
  description?: string;
  breadcrumbs?: BreadcrumbItem[];
  actions?: React.ReactNode;
};
```

Rules:

* Every major page should use it.
* Primary action belongs in `actions`.
* Do not include sensitive data unnecessarily in title.

---

## PageSection

Purpose:

Group related page content.

Props:

```ts
type PageSectionProps = {
  title?: string;
  description?: string;
  children: React.ReactNode;
};
```

Use for:

```text
form sections
settings groups
profile sections
report sections
```

---

## AppLayout

Purpose:

Authenticated app shell.

Responsibilities:

* App navigation
* User menu
* Context display
* Notification access
* Main content wrapper

---

## AdminLayout

Purpose:

Platform admin shell.

Responsibilities:

* Platform admin navigation
* Search and operational tools
* Clear admin context
* High-risk action awareness

---

## InstitutionLayout

Purpose:

Institution-scoped shell.

Responsibilities:

* Active Organization display
* Active Institution display
* Institution navigation
* Future InstitutionSwitcher support
* Institution-scoped breadcrumbs

---

# Navigation Components

## MainSidebar

Purpose:

Primary authenticated app navigation.

Rules:

* Role-aware.
* Permission-aware.
* Context-aware.
* Should not show unauthorized nav items.
* Hidden items are not a security control.

---

## TopNav

Purpose:

Top application controls.

May include:

```text
active institution
search
notifications
help
user menu
```

---

## Breadcrumbs

Purpose:

Show hierarchy.

Example:

```text
Organization → Institution → Learners → Riya Sharma
```

Rules:

* Keep short.
* Avoid sensitive private details.
* Use domain label mapping for UI where needed.

---

## InstitutionSwitcher

Purpose:

Switch active Institution where the user has multiple Institution memberships.

MVP behavior:

* Show active Institution.
* Hide or disable switching if there is only one Institution.

Future behavior:

* Support multiple Institutions under one Organization.
* Support role differences across Institutions.

Component must not be named `SchoolSwitcher`.

---

## UserMenu

Purpose:

Current user actions.

Items:

```text
Profile
Preferences
Change Password
Switch Context
Logout
```

Future:

```text
Sessions
MFA
Support
```

---

# Access-Control Components

Access-control components improve UX only. Backend authorization remains authoritative.

## PermissionGate

Purpose:

Render content only when user has permission.

Example:

```tsx
<PermissionGate
  permission="learner.create"
  scope={{ organizationId, institutionId }}
>
  <Button>Add Learner</Button>
</PermissionGate>
```

Rules:

* Must support scope.
* Must not replace backend authorization.
* Should support fallback rendering where useful.

---

## RoleGate

Purpose:

Render content for specific roles.

Use sparingly.

Prefer permission checks over role checks.

Example:

```tsx
<RoleGate roles={['INSTITUTION_ADMIN']}>
  <InstitutionSettingsPanel />
</RoleGate>
```

---

## FeatureGate

Purpose:

Render content only when feature flag is enabled.

Example:

```tsx
<FeatureGate feature="ai.insights">
  <AIInsightCard />
</FeatureGate>
```

Feature flags do not replace permissions or entitlements.

---

## EntitlementGate

Purpose:

Render premium or usage-limited features.

Example:

```tsx
<EntitlementGate entitlement="ai.insight.generate">
  <GenerateInsightButton />
</EntitlementGate>
```

Backend must still enforce entitlement.

---

## ScopeGate

Purpose:

Validate that the current UI has required Organization or Institution context.

Use for:

```text
institution-scoped page sections
organization-scoped widgets
platform-only controls
```

---

# Feedback Components

## LoadingState

Purpose:

Generic loading state.

Use for:

```text
page loading
section loading
small async action
```

---

## EmptyState

Purpose:

Useful empty data state.

Props:

```ts
type EmptyStateProps = {
  title: string;
  description?: string;
  action?: React.ReactNode;
};
```

Good:

```text
No learners found.
Add your first learner to start managing attendance and assessments.
```

Bad:

```text
No data.
```

---

## ErrorState

Purpose:

Safe error display.

Props:

```ts
type ErrorStateProps = {
  title?: string;
  message?: string;
  code?: string;
  requestId?: string;
  retry?: () => void;
};
```

Rules:

* Show request ID where available.
* Do not expose stack traces.
* Do not expose tenant boundary details.

---

## ForbiddenState

Purpose:

Show authenticated user lacks permission.

Example:

```text
You do not have permission to view this page.
Contact your administrator if you believe this is incorrect.
```

---

## UnauthorizedState

Purpose:

Show unauthenticated or invalid access context state.

Example:

```text
Please sign in to continue.
```

---

## ConfirmDialog

Purpose:

Confirm high-risk actions.

Use for:

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

Props:

```ts
type ConfirmDialogProps = {
  title: string;
  description: string;
  confirmLabel: string;
  cancelLabel?: string;
  variant?: 'default' | 'destructive';
  onConfirm: () => void | Promise<void>;
};
```

---

# Form Components

## FormShell

Purpose:

Standard layout for forms.

Includes:

* Title
* Description
* Sections
* Actions
* Error summary

---

## FormField

Purpose:

Standard form field wrapper.

Should include:

* Label
* Control
* Helper text
* Error message

---

## FormErrorSummary

Purpose:

Show form-level error summary.

Use when:

* Multiple fields fail
* Backend returns non-field error
* Permission or conflict error occurs

---

## PasswordInput

Purpose:

Password field with visibility toggle.

Rules:

* Do not log value.
* Support password manager.
* Show policy hints where useful.

---

## PhoneInput

Purpose:

Phone number input.

Rules:

* Support country code.
* Validate format where practical.
* Do not assume India-only forever.

---

## DatePicker

Purpose:

Date selection.

Use for:

```text
date of birth
assessment date
attendance date
academic year date range
due date
```

Rules:

* Use Institution timezone where relevant.
* Show clear date format.

---

## FileUpload

Purpose:

Upload files.

Rules:

* Show allowed file types.
* Show max file size.
* Validate before upload.
* Show progress.
* Return `fileId` after upload.
* Do not store raw file globally.

Supported MVP file types:

```text
JPG
JPEG
PNG
WEBP
PDF
```

---

# Data Display Components

## DataTable

Purpose:

Reusable data table shell.

Required support:

```text
columns
rows
loading state
empty state
error state
pagination
sorting
filters
row actions
selection optional
```

Rules:

* DataTable should not fetch domain data directly.
* Feature table components should fetch data and pass it in.
* Row actions must be permission-aware.

---

## EntityTable

Purpose:

Domain-friendly table wrapper for entities.

Can standardize:

```text
status column
createdAt column
updatedAt column
actions column
```

Use carefully. Avoid over-generalizing early.

---

## DetailCard

Purpose:

Show record details.

Use for:

```text
learner profile
guardian profile
teacher profile
institution profile
assessment detail
```

---

## SummaryCard

Purpose:

Dashboard or report summary.

Use for:

```text
learner count
teacher count
attendance percentage
assessment average
pending actions
```

---

## StatusBadge

Purpose:

Show status.

Examples:

```text
ACTIVE
PENDING
SUSPENDED
ARCHIVED
LOCKED
PROCESSING
FAILED
SUCCEEDED
```

Rules:

* Include readable text.
* Use semantic colors.
* Do not rely on color alone.

---

## Timeline

Purpose:

Show ordered events.

Use for:

```text
audit activity
communication history
learner timeline
job status history
```

---

## DescriptionList

Purpose:

Show key-value details.

Use for:

```text
profile details
settings summary
report metadata
```

---

# Dashboard Components

## MetricCard

Purpose:

Display metric and optional trend.

Props:

```ts
type MetricCardProps = {
  label: string;
  value: string | number;
  helperText?: string;
  trend?: React.ReactNode;
};
```

Use for:

```text
Learners
Teachers
Attendance today
Assessments pending
```

---

## DashboardGrid

Purpose:

Responsive dashboard layout.

Rules:

* Keep MVP dashboard simple.
* Avoid excessive charts.
* Prioritize actionable information.

---

## SetupProgressCard

Purpose:

Show onboarding or setup completion.

Use for:

```text
Institution profile
Academic year
Classes and sections
Subjects
Users invited
Learners added
```

---

# Domain Components

## Organization Components

Recommended components:

```text
OrganizationsTable
OrganizationForm
OrganizationCard
OrganizationStatusBadge
OrganizationSettingsForm
OrganizationUsersTable
```

---

## Institution Components

Recommended components:

```text
InstitutionsTable
InstitutionForm
InstitutionCard
InstitutionProfileForm
InstitutionSettingsForm
InstitutionTypeBadge
InstitutionStatusBadge
InstitutionSwitcher
InstitutionSummaryCards
```

UI may display “School” for Institution where appropriate.

Code must use Institution.

---

## Academic Components

Recommended components:

```text
AcademicYearForm
AcademicYearsTable
ClassForm
ClassesTable
ClassDetailCard
SectionForm
SectionsTable
SubjectForm
SubjectsTable
AcademicStructureTree
TimetableGrid
```

MVP required:

```text
AcademicYearForm
AcademicYearsTable
ClassForm
ClassesTable
SectionForm
SectionsTable
SubjectForm
SubjectsTable
```

Deferred:

```text
TimetableGrid
AcademicStructureTree
```

---

## Learner Components

Recommended components:

```text
LearnersTable
LearnerForm
LearnerProfileCard
LearnerSummaryCard
LearnerStatusBadge
LearnerClassAssignmentForm
LearnerGuardiansPanel
LearnerAttendanceSummary
LearnerAssessmentSummary
LearnerFilesPanel
LearnerInsightsPanel
```

UI may display “Student”.

Code must use Learner.

---

## Guardian Components

Recommended components:

```text
GuardiansTable
GuardianForm
GuardianProfileCard
GuardianStatusBadge
GuardianLearnerLinksPanel
GuardianLinkForm
GuardianRelationshipBadge
GuardianCommunicationPreferencesForm
```

UI may display “Parent”.

Code must use Guardian.

---

## Teacher Components

Recommended components:

```text
TeachersTable
TeacherForm
TeacherProfileCard
TeacherStatusBadge
TeacherAssignmentsTable
TeacherAssignmentForm
TeacherClassList
TeacherSubjectList
```

---

## Attendance Components

Recommended components:

```text
AttendanceDashboardCards
AttendanceSessionForm
AttendanceSessionTable
AttendanceMarkingGrid
AttendanceStatusSelect
AttendanceStatusBadge
AttendanceSummaryCard
AttendanceReportFilters
AttendanceReportTable
```

MVP required:

```text
AttendanceDashboardCards
AttendanceSessionForm
AttendanceSessionTable
AttendanceMarkingGrid
AttendanceStatusBadge
AttendanceReportFilters
```

---

## Assessment Components

Recommended components:

```text
AssessmentsTable
AssessmentForm
AssessmentDetailCard
AssessmentTypeBadge
AssessmentResultsEntryGrid
AssessmentResultStatusBadge
AssessmentReportFilters
AssessmentReportTable
LearnerAssessmentSummary
```

MVP required:

```text
AssessmentsTable
AssessmentForm
AssessmentDetailCard
AssessmentResultsEntryGrid
AssessmentReportFilters
```

---

## Communication Components

Recommended components:

```text
AnnouncementsTable
AnnouncementForm
AnnouncementDetailCard
AudienceSelector
MessageComposer
RecipientSelector
DeliveryStatusBadge
CommunicationTimeline
```

MVP required:

```text
AnnouncementsTable
AnnouncementForm
AnnouncementDetailCard
AudienceSelector
```

Deferred:

```text
MessageComposer
RecipientSelector
CommunicationTimeline
```

---

## Report Components

Recommended components:

```text
ReportsDashboard
ReportCard
ReportFilters
AttendanceReportTable
AssessmentReportTable
LearnerReportCard
ExportReportButton
ReportExportStatus
```

MVP required:

```text
ReportsDashboard
ReportCard
ReportFilters
AttendanceReportTable
AssessmentReportTable
LearnerReportCard
```

Export components may be V1 if asynchronous exports are deferred.

---

## File Components

Recommended components:

```text
FileUpload
FileDropzone
FileList
FilePreview
FileTypeIcon
FileStatusBadge
SignedDownloadButton
FileDeleteButton
```

MVP required:

```text
FileUpload
FileTypeIcon
FileStatusBadge
```

Standalone file manager can be deferred.

---

## Fee Components

Fee components are MVP optional or V1.

Recommended components:

```text
FeeDashboardCards
FeeCategoriesTable
FeeCategoryForm
FeeStructuresTable
FeeStructureForm
FeeAssignmentsTable
FeePaymentForm
FeePaymentTable
LearnerFeeSummary
FeeStatusBadge
```

Do not include fee components if they delay core academic workflows.

---

## AI Components

AI components are MVP optional or V1.

Recommended components:

```text
AIInsightCard
AIInsightGenerateButton
AIInsightStatusBadge
AIDraftEditor
AIUsageBadge
AIJobStatus
OCRUploadPanel
OCRResultReview
```

Rules:

* Label AI-generated content.
* Do not expose raw prompts.
* Do not imply AI is authoritative.
* Require review for communication drafts.
* Respect privacy and tenant scope.

---

# Workflow Components

## OnboardingWizard

Purpose:

Guide initial setup.

Steps:

```text
Organization setup
Institution setup
Academic setup
Invite users
```

Rules:

* Show progress.
* Validate each step.
* Save state where useful.
* Use Institution terminology in code.

---

## InviteUserForm

Purpose:

Invite users into Organization or Institution scope.

Fields:

```text
email
full name
role
organizationId
institutionId where applicable
```

Rules:

* Role options must be permission-aware.
* Do not allow role escalation.
* Show invitation status.

---

## GuardianLinkForm

Purpose:

Link Guardian to Learner.

Fields:

```text
guardian
learner
relationship
isPrimary
canReceiveNotifications
canAccessReports
```

Rules:

* Prevent duplicate links.
* Require permission.
* Validate learner and guardian belong to same Institution.

---

## TeacherAssignmentForm

Purpose:

Assign Teacher to academic context.

Fields:

```text
teacher
academicYear
class
section
subject
```

Rules:

* Validate assignment conflicts.
* Validate teacher belongs to Institution.
* Validate class/section belongs to Institution.

---

## AttendanceMarkingGrid

Purpose:

Efficiently mark attendance for a class/section.

Requirements:

* Show learner list.
* Allow status selection.
* Support bulk mark present.
* Show unsaved changes.
* Validate before submit.
* Respect locked sessions.
* Work on tablet where practical.

---

## AssessmentResultsEntryGrid

Purpose:

Enter or update assessment results.

Requirements:

* Show learner list.
* Show max marks.
* Validate marks.
* Support grade/comment where configured.
* Show unsaved changes.
* Respect locked assessments.
* Audit sensitive changes.

---

# Admin Components

Recommended platform admin components:

```text
AdminDashboardCards
AdminOrganizationsTable
AdminInstitutionsTable
AdminUsersTable
AdminAuditLogTable
AdminFeatureFlagTable
AdminAIUsageSummary
AdminStatusBadge
```

MVP required:

```text
AdminDashboardCards
AdminOrganizationsTable
AdminInstitutionsTable
AdminUsersTable
AdminAuditLogTable
```

Admin components must avoid unnecessary exposure of tenant-sensitive data.

---

# Component State Requirements

Components should use:

| State Type     | Recommended Location      |
| -------------- | ------------------------- |
| Local UI state | Component state           |
| Server state   | TanStack Query            |
| Form state     | React Hook Form           |
| URL filters    | Query parameters          |
| Active context | Context/Zustand           |
| Auth state     | Auth provider/query       |
| Permissions    | Derived from auth/context |

Do not store server data globally unless there is a strong reason.

---

# Component Accessibility Requirements

Every reusable component must support:

```text
keyboard navigation
visible focus state
semantic HTML
screen-reader labels
form error association
dialog focus trap
non-color-only status
sufficient contrast
responsive zoom
```

Components that fail accessibility should not be promoted to shared library.

---

# Component Responsive Requirements

Components should support:

```text
desktop
tablet
basic mobile web
```

Priority responsive components:

```text
LoginForm
InvitationAcceptanceForm
InstitutionSwitcher
MainSidebar
DataTable
LearnersTable
AttendanceMarkingGrid
AssessmentResultsEntryGrid
AnnouncementCard
Guardian dashboard components
Teacher attendance components
```

Tables may use card layouts or horizontal scroll on small screens.

---

# Component Security Requirements

Components must not:

```text
store tokens in component state unnecessarily
log sensitive props
render raw HTML without sanitization
show sensitive fields without permission
expose raw AI prompts
expose OCR text broadly
persist signed URLs
treat hidden buttons as security
```

High-risk components must be reviewed carefully:

```text
PermissionGate
FileUpload
SignedDownloadButton
AssessmentResultsEntryGrid
AttendanceMarkingGrid
GuardianLinkForm
InviteUserForm
AdminUsersTable
ReportExportButton
AIInsightCard
```

---

# Component Privacy Requirements

Privacy-sensitive components include:

```text
LearnerProfileCard
LearnerAssessmentSummary
LearnerAttendanceSummary
GuardianProfileCard
GuardianLearnerLinksPanel
TeacherProfileCard
AIInsightCard
OCRResultReview
ReportExportButton
NotificationPreview
```

Rules:

* Show only necessary information.
* Mask or hide sensitive fields where appropriate.
* Require permission for detailed views.
* Avoid sensitive preview text.
* Avoid sensitive data in browser titles, URLs, or logs.

---

# Component Error Handling

Components should handle errors using backend error codes.

Examples:

```text
VALIDATION_ERROR
AUTH_REQUIRED
RBAC_PERMISSION_REQUIRED
TENANT_SCOPE_VIOLATION
LEARNER_NOT_FOUND
GUARDIAN_NOT_FOUND
RATE_LIMIT_EXCEEDED
```

Rules:

* Use field-level errors for validation.
* Use `ForbiddenState` for permission failures.
* Use safe not-found message for scoped missing resources.
* Show request ID where useful.
* Do not show raw stack traces.

---

# Component Testing Standards

Reusable components must have tests for:

```text
default rendering
loading state
empty state
error state
disabled state
keyboard interaction
permission-hidden behavior
form validation
field error rendering
responsive behavior where practical
```

High-risk components requiring stronger tests:

```text
LoginForm
InvitationAcceptanceForm
PermissionGate
InstitutionSwitcher
LearnerForm
GuardianLinkForm
TeacherAssignmentForm
AttendanceMarkingGrid
AssessmentResultsEntryGrid
FileUpload
ReportExportButton
AdminUsersTable
```

---

# Storybook Standards

Storybook or an equivalent component documentation tool is recommended.

MVP may defer full Storybook, but components should still have examples.

Recommended stories:

```text
Default
Loading
Empty
Error
Disabled
Permission denied
With data
With long text
Mobile layout
Dark theme where supported
```

Storybook should not include real learner, guardian, or institution data.

Use synthetic examples.

---

# MVP Component Library

The MVP must include these primitives:

```text
Button
Input
Textarea
Select
Combobox
Checkbox
RadioGroup
Switch
Label
Card
Dialog
Sheet
DropdownMenu
Tabs
Badge
Avatar
Tooltip
Toast
Skeleton
Alert
Table
DataTable
Pagination
DatePicker
FileUpload
```

The MVP must include these layout/navigation/access components:

```text
PageShell
PageHeader
PageSection
AppLayout
AdminLayout
InstitutionLayout
MainSidebar
TopNav
Breadcrumbs
UserMenu
InstitutionSwitcher
PermissionGate
FeatureGate
EntitlementGate placeholder
ScopeGate
```

The MVP must include these feedback components:

```text
LoadingState
EmptyState
ErrorState
ForbiddenState
UnauthorizedState
ConfirmDialog
FormErrorSummary
```

The MVP must include these domain/workflow components:

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
AttendanceDashboardCards
AttendanceSessionForm
AttendanceSessionTable
AttendanceMarkingGrid
AttendanceReportFilters
AssessmentsTable
AssessmentForm
AssessmentDetailCard
AssessmentResultsEntryGrid
AssessmentReportFilters
AnnouncementsTable
AnnouncementForm
AnnouncementDetailCard
AudienceSelector
ReportsDashboard
ReportCard
ReportFilters
AdminDashboardCards
AdminOrganizationsTable
AdminInstitutionsTable
AdminUsersTable
AdminAuditLogTable
```

---

# Deferred Components

The following components may be deferred beyond MVP:

```text
TimetableGrid
AcademicStructureTree
FeeDashboardCards
FeeCategoryForm
FeeStructureForm
FeePaymentForm
MessageComposer
RecipientSelector
CommunicationTimeline
FilesManager
FilePreview
SignedDownloadButton
AIInsightCard
AIInsightGenerateButton
AIDraftEditor
AIUsageBadge
OCRUploadPanel
OCRResultReview
BulkImportWizard
SessionManager
FeatureFlagEditor
AdminAIUsageSummary
AdvancedAnalyticsCharts
WhiteLabelThemeEditor
```

Do not block these future components with MVP-only naming or scoping assumptions.

---

# Component Anti-Patterns

Avoid:

```text
GenericCrudManager for every entity
SchoolSwitcher as code component name
StudentProfileCard as code component name
ParentLinkForm as code component name
Components that fetch unscoped data
Components that hide unauthorized actions but assume backend allows them
One-off table implementations everywhere
Raw backend error rendering
Sensitive data in notification previews
Unlabeled AI-generated content
Huge page components with all logic inline
```

---

# Component Review Checklist

Before approving a component, confirm:

* Component has a clear purpose.
* Component belongs in the correct folder.
* Component uses correct domain terminology.
* Props are typed.
* Institution-scoped props include `organizationId` and `institutionId`.
* Component does not introduce `schoolId`, `studentId`, or `parentId`.
* Component supports loading, empty, and error states where applicable.
* Component supports permissions where needed.
* Component is accessible.
* Component is responsive enough for MVP.
* Sensitive data is minimized.
* AI-generated content is labeled where applicable.
* Component uses design system tokens.
* Component does not duplicate existing components.
* Tests are planned or implemented.

---

# Related Documents

* docs/10-Design-System/DESIGN_SYSTEM.md
* docs/10-Design-System/UI_GUIDELINES.md
* docs/10-Design-System/COLORS.md
* docs/10-Design-System/TYPOGRAPHY.md
* docs/08-Frontend/COMPONENTS.md
* docs/08-Frontend/PAGES.md
* docs/08-Frontend/ROUTING.md
* docs/08-Frontend/STATE_MANAGEMENT.md
* docs/11-Security/RBAC.md
* docs/11-Security/PRIVACY.md