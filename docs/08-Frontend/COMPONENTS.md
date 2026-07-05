# Frontend Components

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Frontend Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the frontend component architecture for Acadedx.

Acadedx is a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

Frontend components must support the MVP while preserving future multi-institution readiness. Code-level components, props, state, API clients, and domain models should use:

```text
Organization
Institution
Learner
Guardian
Teacher
```

Do not use these as core code-level terms:

```text
School
Student
Parent
```

except for UI labels, copy, localization, or explicitly documented compatibility aliases.

---

# Component Goals

Frontend components must ensure:

1. Consistent user experience across Acadedx.
2. Reusable UI primitives.
3. Clear separation between presentational and feature components.
4. Organization and Institution context awareness where required.
5. Role-aware and permission-aware rendering.
6. Accessibility by default.
7. Type-safe props and event handlers.
8. Minimal duplication across pages.
9. Stable components for AI coding agents and developers.
10. Future readiness for multi-institution, mobile, and role-specific experiences.

---

# Recommended Component Stack

Preferred stack:

```text
React
TypeScript
Tailwind CSS
shadcn/ui or equivalent primitives
Radix UI primitives where useful
React Hook Form
Zod
TanStack Query
Lucide or equivalent icon set
```

Component choices should prioritize:

* Accessibility
* Composability
* Type safety
* Maintainability
* Design-system consistency
* Low unnecessary abstraction

---

# Component Architecture Principles

## 1. Components Should Be Composable

Prefer small reusable components that can be composed into feature screens.

Good:

```tsx
<PageHeader />
<DataTable />
<FilterBar />
<CreateLearnerDialog />
```

Avoid large monolithic page components that contain layout, fetching, forms, validation, and business rules in one file.

---

## 2. Separate UI Components from Domain Components

UI components should be generic.

Domain components should understand Acadedx concepts.

Example:

```text
Button
Card
Dialog
DataTable
```

are UI components.

```text
LearnerProfileCard
InstitutionSwitcher
GuardianLinkForm
AttendanceStatusBadge
```

are domain or feature components.

---

## 3. Components Must Not Own Backend Authorization

Components may hide or disable UI based on permissions.

Backend remains authoritative.

A hidden button is not a security control.

Example:

```tsx
<PermissionGate permission="learner.create" scope={{ organizationId, institutionId }}>
  <CreateLearnerButton />
</PermissionGate>
```

The API must still enforce `learner.create`.

---

## 4. Components Must Be Context-Aware Where Needed

Institution-scoped components must receive or resolve:

```text
organizationId
institutionId
```

Good:

```tsx
<LearnersTable
  organizationId={organizationId}
  institutionId={institutionId}
/>
```

Avoid:

```tsx
<LearnersTable schoolId={schoolId} />
```

---

## 5. Prefer Explicit Props for Domain Scope

For domain components, prefer explicit scope props.

Good:

```tsx
type LearnerListProps = {
  organizationId: string;
  institutionId: string;
};
```

Avoid relying on hidden global state for every scoped component.

Global active context may exist, but components should make scope dependencies visible when practical.

---

## 6. Avoid Premature Abstraction

Do not create generic abstractions before patterns are proven.

Bad:

```text
UniversalEntityManager
GenericCrudPage
MegaFormBuilder
```

Better:

```text
LearnersTable
GuardiansTable
TeachersTable
AssessmentResultsForm
```

Abstract only after repeated patterns are clear.

---

# Component Categories

Frontend components should be grouped into:

1. Layout components
2. Navigation components
3. UI primitive components
4. Form components
5. Data display components
6. Feedback components
7. Permission and access components
8. Domain components
9. Feature components
10. Page-level components
11. Provider components

---

# Recommended Directory Structure

Recommended structure:

```text
src/
├── components/
│   ├── ui/
│   ├── layout/
│   ├── navigation/
│   ├── feedback/
│   ├── access/
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

Feature-specific components should live inside their feature folder.

Example:

```text
src/features/learners/components/LearnerProfileCard.tsx
src/features/learners/components/LearnersTable.tsx
src/features/learners/components/LearnerForm.tsx
```

Shared UI primitives should live in:

```text
src/components/ui/
```

---

# Component Naming Rules

Use PascalCase for React components.

Good:

```text
InstitutionSwitcher
LearnerProfileCard
GuardianLinkForm
TeacherAssignmentsTable
AttendanceStatusBadge
AssessmentResultsForm
```

Avoid:

```text
SchoolSwitcher
StudentProfileCard
ParentLinkForm
```

unless the component is explicitly UI-label-only and not a domain model.

---

# File Naming Rules

Use kebab-case or PascalCase consistently.

Recommended:

```text
LearnerProfileCard.tsx
GuardianLinkForm.tsx
InstitutionSwitcher.tsx
```

or:

```text
learner-profile-card.tsx
guardian-link-form.tsx
institution-switcher.tsx
```

Do not mix conventions randomly.

For Acadedx, recommended convention:

```text
PascalCase component files
kebab-case utility files
```

Example:

```text
LearnerProfileCard.tsx
learner-formatters.ts
```

---

# UI Label Mapping

Components may render user-friendly labels based on context.

| Code Term    | MVP School UI Label  | Future Generic UI Label |
| ------------ | -------------------- | ----------------------- |
| Institution  | School               | Institution             |
| Learner      | Student              | Learner                 |
| Guardian     | Parent               | Guardian                |
| Class        | Class / Grade        | Class                   |
| Organization | Organization / Trust | Organization            |

Example:

```tsx
<FieldLabel>{labels.learner}</FieldLabel>
```

Where `labels.learner` may be:

```text
Student
```

for school MVP.

Do not rename code models to match UI labels.

---

# Layout Components

Layout components define page structure.

Recommended layout components:

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
PageShell
PageHeader
PageContent
PageSection
```

---

## PageShell

Purpose:

Provide consistent page spacing and structure.

Example:

```tsx
<PageShell>
  <PageHeader title="Learners" description="Manage learners for this institution." />
  <PageContent>
    <LearnersTable />
  </PageContent>
</PageShell>
```

---

## PageHeader

Purpose:

Render title, description, breadcrumbs, and actions.

Props:

```ts
type PageHeaderProps = {
  title: string;
  description?: string;
  breadcrumbs?: BreadcrumbItem[];
  actions?: React.ReactNode;
};
```

Use for all major app pages.

---

## InstitutionLayout

Purpose:

Provide institution-scoped app shell.

Responsibilities:

* Render institution navigation.
* Show active Institution.
* Show active Organization context.
* Provide institution switcher where applicable.
* Enforce layout-level loading and error states.

Must use:

```text
organizationId
institutionId
```

---

# Navigation Components

Recommended navigation components:

```text
MainSidebar
TopNav
MobileNav
Breadcrumbs
InstitutionSwitcher
OrganizationSwitcher
UserMenu
RoleAwareNavItem
CommandPalette
```

---

## InstitutionSwitcher

Purpose:

Allow switching between Institutions where user has access.

MVP behavior:

* If only one Institution exists, show current Institution or hide switcher.
* If multiple Institutions exist in future, allow selection.

Props:

```ts
type InstitutionSwitcherProps = {
  organizationId: string;
  institutionId: string;
  institutions: InstitutionSummary[];
  onChange: (institutionId: string) => void;
};
```

Do not call this `SchoolSwitcher` in code.

---

## RoleAwareNavItem

Purpose:

Render navigation item only if user has required permission.

Props:

```ts
type RoleAwareNavItemProps = {
  href: string;
  label: string;
  icon?: React.ComponentType;
  requiredPermissions?: string[];
  scope?: {
    organizationId?: string;
    institutionId?: string;
  };
};
```

Hidden navigation does not replace backend authorization.

---

# UI Primitive Components

UI primitives are generic and should not contain Acadedx business logic.

Recommended primitives:

```text
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
Sheet
Drawer
DropdownMenu
Popover
Tabs
Accordion
Tooltip
Badge
Avatar
Table
DataTable
Pagination
Skeleton
Alert
Toast
Progress
Separator
Calendar
DatePicker
TimePicker
Command
```

These should live in:

```text
src/components/ui/
```

---

# Form Components

Form components should standardize validation and error display.

Recommended components:

```text
Form
FormField
FormItem
FormLabel
FormControl
FormDescription
FormMessage
SubmitButton
PasswordInput
PhoneInput
DateField
FileUploadField
SearchInput
FilterSelect
```

Forms should use:

```text
React Hook Form
Zod
```

Backend validation errors should map to field errors.

---

## Form Error Handling

Field-level errors:

```tsx
<FormMessage />
```

Page-level errors:

```tsx
<FormErrorSummary />
```

Backend error mapping should use:

```text
error.details[].field
```

Do not parse backend error message text for logic.

---

# Data Display Components

Recommended components:

```text
DataTable
EntityTable
DetailCard
SummaryCard
MetricCard
StatusBadge
Timeline
ActivityFeed
AuditLogTable
EmptyState
DescriptionList
KeyValueList
```

---

## DataTable

Purpose:

Reusable table for list pages.

Required capabilities:

* Loading state
* Empty state
* Error state
* Pagination
* Sorting
* Filtering hooks
* Row actions
* Column visibility where useful
* Accessible table structure

Do not make `DataTable` responsible for fetching domain data.

Good:

```tsx
<LearnersTable data={learners} isLoading={isLoading} />
```

Avoid:

```tsx
<DataTable entity="learners" fetchAutomatically />
```

unless carefully justified.

---

## StatusBadge

Purpose:

Render consistent entity statuses.

Examples:

```text
ACTIVE
INACTIVE
SUSPENDED
ARCHIVED
PENDING
PROCESSING
FAILED
SUCCEEDED
```

Status badges must not rely only on color. Include text.

---

# Feedback Components

Recommended components:

```text
LoadingState
SkeletonState
EmptyState
ErrorState
ForbiddenState
UnauthorizedState
NotFoundState
RateLimitState
SuccessToast
ErrorToast
ConfirmDialog
UnsavedChangesPrompt
```

---

## EmptyState

Purpose:

Show useful action-oriented empty screens.

Example:

```tsx
<EmptyState
  title="No learners found"
  description="Add your first learner to start managing attendance and assessments."
  action={<CreateLearnerButton />}
/>
```

Avoid:

```text
No data.
```

---

## ErrorState

Purpose:

Render safe error information.

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

Show request ID where available.

Do not show stack traces or raw provider errors.

---

# Permission and Access Components

Recommended components:

```text
PermissionGate
RoleGate
ScopeGate
FeatureGate
EntitlementGate
ProtectedRoute
ForbiddenState
```

---

## PermissionGate

Purpose:

Render children only when user has required permission.

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

* Used for UX only.
* Backend authorization remains mandatory.
* Must be scope-aware.

---

## FeatureGate

Purpose:

Render feature only when enabled.

Example:

```tsx
<FeatureGate feature="ai.insights">
  <LearnerInsightsPanel />
</FeatureGate>
```

Feature flags must not replace permissions or entitlements.

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

# Domain Components

Domain components understand Acadedx concepts.

Recommended domain component groups:

```text
organizations
institutions
academic
learners
guardians
teachers
attendance
assessments
fees
communication
reports
files
ai
```

---

# Organization Components

Recommended components:

```text
OrganizationCard
OrganizationForm
OrganizationsTable
OrganizationStatusBadge
OrganizationSettingsForm
OrganizationUsersTable
```

Code term:

```text
Organization
```

---

# Institution Components

Recommended components:

```text
InstitutionCard
InstitutionForm
InstitutionsTable
InstitutionProfileForm
InstitutionSettingsForm
InstitutionTypeBadge
InstitutionStatusBadge
InstitutionSwitcher
InstitutionSummaryCards
```

Code term:

```text
Institution
```

UI may display:

```text
School
```

for school-focused MVP.

---

# Academic Components

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
TimetableGrid
AcademicStructureTree
```

Use:

```text
AcademicYear
Class
Section
Subject
```

Avoid code-level:

```text
Grade
AcademicSession
```

unless explicitly mapped for UI copy.

---

# Learner Components

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

Code term:

```text
Learner
```

UI may display:

```text
Student
```

for school-focused MVP.

---

# Guardian Components

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

Code term:

```text
Guardian
```

UI may display:

```text
Parent
```

for school-focused MVP.

---

# Teacher Components

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

# Attendance Components

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

Attendance status values should use consistent enum labels:

```text
PRESENT
ABSENT
LATE
EXCUSED
HALF_DAY
```

---

# Assessment Components

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

Assessment result entry should be designed carefully because it is high-risk and audited.

---

# Fee Components

Fee components may be MVP optional or V1.

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

Do not include fee components in MVP if they distract from academic core.

---

# Communication Components

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

Audience selection must be tenant-safe and permission-aware.

---

# Report Components

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

Report components must be explicit about scope and filters.

---

# File Components

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

File components must validate file type and size before upload.

Signed URL generation must happen only after backend authorization.

---

# AI Components

AI components may be MVP optional or V1.

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

AI components must not expose raw prompts, internal system messages, provider debug payloads, or cross-tenant data.

---

# Page-Level Components

Page-level components should compose feature components and data hooks.

Example:

```text
LearnersPage
LearnerDetailPage
GuardianDetailPage
AttendanceMarkPage
AssessmentResultsPage
InstitutionDashboardPage
```

Page-level components may:

* Read route params
* Call data hooks
* Configure page metadata
* Render feature components

They should not contain deep business logic.

---

# Data Hooks

Feature components should use typed hooks.

Examples:

```text
useCurrentUser()
useActiveContext()
usePermissions()
useOrganizations()
useInstitution()
useLearners()
useLearner()
useGuardians()
useTeachers()
useAttendanceSessions()
useAssessments()
useAnnouncements()
```

Hooks should use tenant-safe query keys.

Example:

```ts
useLearner({
  organizationId,
  institutionId,
  learnerId,
});
```

---

# Component Props Standards

Props should be typed explicitly.

Example:

```ts
type LearnerProfileCardProps = {
  organizationId: string;
  institutionId: string;
  learnerId: string;
  showSensitiveFields?: boolean;
};
```

Avoid loose types:

```ts
type Props = any;
```

Avoid passing large raw objects when IDs and hooks are enough, unless data has already been fetched intentionally.

---

# Component State Rules

Use local state for:

```text
dialog open state
dropdown open state
selected table rows
temporary input state
expanded sections
active tab where not URL-backed
```

Use URL state for:

```text
search
filters
sort
pagination
shareable tabs
report filters
```

Use query state for:

```text
API data
server mutations
background job status
notifications
```

Use global state only for:

```text
auth summary
active context
theme
sidebar state
global toasts
```

---

# Accessibility Requirements

All components must follow accessibility basics:

* Semantic HTML
* Keyboard navigation
* Visible focus states
* ARIA only when needed
* Labelled form fields
* Error messages associated with fields
* Dialog focus trapping
* Screen-reader-friendly loading states
* Sufficient contrast
* Do not rely on color alone

---

# Responsive Requirements

Components should support:

```text
Desktop
Tablet
Mobile web basic usability
```

Priority mobile-friendly components:

```text
LoginForm
InvitationAcceptanceForm
InstitutionSwitcher
LearnersTable responsive view
AttendanceMarkingGrid
AnnouncementCard
Guardian dashboard components
Teacher attendance components
```

Data-heavy tables should provide responsive alternatives where needed.

---

# Loading States

Every data-dependent component must handle:

```text
loading
success
empty
error
forbidden where applicable
```

Example:

```tsx
if (isLoading) return <LearnerProfileSkeleton />;
if (isError) return <ErrorState code={error.code} requestId={error.requestId} />;
if (!learner) return <EmptyState title="Learner not found" />;
```

---

# Skeleton Components

Use skeletons for layout-preserving loading states.

Recommended skeletons:

```text
DashboardSkeleton
TableSkeleton
ProfileCardSkeleton
FormSkeleton
ReportSkeleton
TimelineSkeleton
```

Do not overuse skeletons for very fast interactions.

---

# Confirmation Components

Sensitive actions must use confirmation dialogs.

Examples:

```text
Deactivate Institution
Archive Learner
Unlink Guardian
Remove Teacher Assignment
Delete File
Update Locked Attendance
Update Assessment Result
Cancel Export
Suspend User
```

Confirmation dialog should show:

* Action being taken
* Affected resource
* Risk or consequence
* Confirm button
* Cancel button

High-risk actions may require re-authentication in future.

---

# Status and Badge Standards

Use badges for clear statuses.

Common statuses:

```text
ACTIVE
INACTIVE
PENDING
SUSPENDED
ARCHIVED
PROCESSING
FAILED
SUCCEEDED
LOCKED
DRAFT
PUBLISHED
```

Every badge must include readable text.

Color alone is insufficient.

---

# Table Action Standards

Row actions should be permission-aware.

Example actions:

```text
View
Edit
Archive
Assign
Link Guardian
Download
Export
```

Rules:

* Hide or disable actions the user cannot perform.
* Explain disabled actions where helpful.
* Backend must enforce authorization.
* Sensitive actions require confirmation.

---

# Component Error Handling

Components should handle API errors by code.

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

Do not parse error messages for logic.

---

# Component Testing Requirements

Component tests should cover:

* Renders expected content
* Loading state
* Empty state
* Error state
* Permission-hidden action
* Disabled action
* Form validation
* Backend validation mapping
* User interaction
* Accessibility basics

High-risk components requiring stronger tests:

```text
LoginForm
InvitationAcceptanceForm
InstitutionSwitcher
PermissionGate
LearnerForm
GuardianLinkForm
TeacherAssignmentForm
AttendanceMarkingGrid
AssessmentResultsEntryGrid
FileUpload
ReportExportButton
RoleAwareNavItem
```

---

# Storybook / Component Documentation

Recommended future addition:

```text
Storybook
```

Use Storybook or equivalent for:

* UI primitives
* Form components
* Data tables
* Empty states
* Error states
* Domain cards
* High-risk forms

MVP may defer full Storybook, but reusable components should be documented with examples.

---

# Component Security Requirements

Components must not:

* Render sensitive fields unless user has permission.
* Store secrets in state.
* Log tokens or sensitive data.
* Expose raw AI prompts.
* Expose OCR text outside allowed context.
* Expose signed URLs unnecessarily.
* Trust hidden buttons as authorization.
* Use unsafe HTML rendering without sanitization.

If rendering HTML content is required, sanitize it.

---

# Component Privacy Requirements

Components must minimize displayed sensitive data.

Examples:

* Guardian phone/email should be shown only where needed.
* Learner assessment details should be role-aware.
* Notifications should avoid sensitive preview text.
* AI insights should be shown only to authorized roles.
* Export buttons should require explicit permission.
* Support/admin components should mask sensitive fields where practical.

---

# Internationalization Readiness

MVP may be English-first.

Components should still avoid hardcoded domain labels where terminology may change.

Recommended pattern:

```ts
const labels = useDomainLabels();

labels.institution; // School or Institution
labels.learner;     // Student or Learner
labels.guardian;    // Parent or Guardian
```

This allows MVP school-friendly labels without corrupting code terminology.

---

# Design System Alignment

All components must align with:

```text
docs/10-Design-System/DESIGN_SYSTEM.md
docs/10-Design-System/COMPONENT_LIBRARY.md
docs/10-Design-System/UI_GUIDELINES.md
```

Do not create one-off visual patterns without checking design system components first.

---

# MVP Component Set

The MVP should implement these shared components:

```text
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
FeatureGate
EntitlementGate placeholder
LoadingState
EmptyState
ErrorState
ForbiddenState
UnauthorizedState
ConfirmDialog
FormErrorSummary
```

The MVP should implement these domain components:

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
AttendanceReportFilters
AssessmentForm
AssessmentsTable
AssessmentResultsEntryGrid
AssessmentReportFilters
AnnouncementsTable
AnnouncementForm
ReportsDashboard
ReportCard
```

---

# Deferred Components

The following may be deferred beyond MVP:

```text
TimetableGrid
FeeDashboardCards
FeeStructureForm
FeePaymentForm
MessageComposer
RecipientSelector
FilesManager
AIInsightCard
AIInsightGenerateButton
AIDraftEditor
OCRUploadPanel
OCRResultReview
AdvancedAnalyticsCharts
BulkImportWizard
SessionManager
FeatureFlagEditor
AdminAIUsageDashboard
```

Do not block these future components with MVP-only assumptions.

---

# Component Review Checklist

Before approving a component, confirm:

* Component has a clear purpose.
* Component name uses correct domain terminology.
* No `schoolId`, `studentId`, or `parentId` is introduced in code.
* Props are typed.
* Scope props include `organizationId` and `institutionId` where needed.
* Loading, empty, and error states are handled where applicable.
* Permission-aware rendering is implemented where needed.
* Backend authorization is not assumed.
* Sensitive fields are not displayed unnecessarily.
* Component is accessible.
* Component is responsive enough for MVP.
* Component uses design system primitives.
* Component does not duplicate existing functionality.
* Tests cover important states.

---

# Related Documents

* docs/08-Frontend/PAGES.md
* docs/08-Frontend/ROUTING.md
* docs/08-Frontend/STATE_MANAGEMENT.md
* docs/10-Design-System/DESIGN_SYSTEM.md
* docs/10-Design-System/UI_GUIDELINES.md
* docs/10-Design-System/COMPONENT_LIBRARY.md
* docs/06-API/API_GUIDELINES.md
* docs/06-API/ENDPOINTS.md
* docs/11-Security/RBAC.md
* docs/11-Security/PRIVACY.md