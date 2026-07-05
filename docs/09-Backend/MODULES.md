# Backend Modules

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Backend Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the backend module structure for Acadedx.

This file is implementation-specific for the backend application, especially a NestJS-based architecture.

It complements but does not replace:

```text
docs/04-Architecture/MODULES.md
````

The architecture must support the current school-focused MVP while remaining ready for:

```text
Organization → Institution → Academic Operations
```

A School is an Institution type.

Backend modules must use `institutionId`, not `schoolId`.

---

# Backend Module Goals

Backend modules must ensure:

1. Clear separation of business domains.
2. Consistent Organization and Institution scoping.
3. Reusable authorization and validation patterns.
4. Testable service boundaries.
5. Minimal cross-module coupling.
6. Support for MVP delivery without blocking future multi-institution support.
7. Clean integration with AI, OCR, files, notifications, and background jobs.
8. Consistent audit logging for sensitive actions.
9. Backend terminology aligned with Organization, Institution, Learner, and Guardian.
10. Compatibility with API, database, security, and product documentation.

---

# Backend Architecture Style

Recommended backend style:

```text
Modular Monolith first
```

The MVP should start as a well-structured modular monolith rather than distributed microservices.

Reason:

* Faster MVP delivery
* Lower operational complexity
* Easier transaction management
* Easier authorization consistency
* Easier debugging
* Lower infrastructure cost

Future extraction into services should be possible where module boundaries are clean.

Potential future service boundaries:

```text
Identity Service
Organization Service
Institution Service
Academic Service
AI Service
Notification Service
Billing Service
Reporting Service
```

---

# Recommended Backend Framework

Preferred backend framework:

```text
NestJS
```

Recommended implementation patterns:

* Modules
* Controllers
* Services
* Repositories or Prisma services
* DTOs
* Guards
* Interceptors
* Pipes
* Exception filters
* Background processors
* Domain policies
* OpenAPI decorators
* Integration tests

---

# Module Structure Standard

Each backend module should generally follow this structure:

```text
src/modules/<module-name>/
├── controllers/
├── services/
├── dto/
├── entities/ or models/
├── repositories/
├── policies/
├── guards/
├── events/
├── jobs/
├── tests/
└── <module-name>.module.ts
```

Not every module needs every folder.

Smaller modules may use:

```text
src/modules/<module-name>/
├── <module-name>.controller.ts
├── <module-name>.service.ts
├── dto/
└── <module-name>.module.ts
```

---

# Naming Rules

Use backend and API terminology consistently.

Use:

```text
organization
institution
learner
guardian
teacher
academicYear
class
section
subject
```

Do not use as backend module names:

```text
school
student
parent
```

Exceptions:

* UI labels may show School, Student, or Parent.
* Import adapters may map legacy data fields.
* Compatibility aliases must be isolated and documented.

---

# Core Context Object

Most backend modules must rely on a resolved request context.

Recommended context shape:

```ts
export interface RequestContext {
  requestId: string;
  userId?: string;
  organizationId?: string;
  institutionId?: string;
  roles: string[];
  permissions: string[];
  memberships: Array<{
    organizationId?: string;
    institutionId?: string;
    role: string;
    status: string;
  }>;
  entitlements?: string[];
}
```

The context should be available to:

* Guards
* Controllers
* Services
* Policies
* Repositories
* Audit logging
* Background job creation
* AI workflows
* File access checks

---

# Common Backend Layers

## Controllers

Controllers handle:

* HTTP routing
* Request DTO validation
* Response formatting
* OpenAPI metadata
* Auth guard application
* Permission guard application

Controllers must not contain business logic.

---

## Services

Services handle:

* Business rules
* Transaction orchestration
* Domain validation
* Cross-module coordination
* Audit event creation
* Background job creation
* Integration calls

Services must not trust frontend-provided authorization decisions.

---

## Repositories

Repositories handle:

* Database access
* Tenant-safe queries
* Persistence operations
* Query composition
* Pagination

Repository methods for tenant-scoped resources must include:

```text
organizationId
institutionId
```

where applicable.

Bad:

```ts
findLearnerById(learnerId: string)
```

Good:

```ts
findLearnerById(params: {
  organizationId: string;
  institutionId: string;
  learnerId: string;
})
```

---

## DTOs

DTOs define:

* Request body shape
* Query parameter shape
* Path parameter validation
* Response shape where useful

DTOs must prevent mass assignment.

Do not allow clients to set restricted fields such as:

```text
role
permissions
organizationId
institutionId
createdBy
updatedBy
deletedAt
isPlatformAdmin
```

unless the endpoint explicitly owns that operation.

---

## Policies

Policies enforce resource-specific authorization.

Examples:

```text
LearnerAccessPolicy
GuardianLearnerPolicy
TeacherAssignmentPolicy
AttendanceUpdatePolicy
AssessmentResultPolicy
FileAccessPolicy
AIContextAccessPolicy
```

Policy checks must happen before sensitive data is returned or modified.

---

## Guards

Recommended guards:

```text
JwtAuthGuard
ActiveUserGuard
MembershipGuard
PermissionsGuard
TenantScopeGuard
EntitlementGuard
FeatureFlagGuard
ResourcePolicyGuard
```

Guards should be reusable across modules.

---

## Interceptors

Recommended interceptors:

```text
RequestIdInterceptor
LoggingInterceptor
ResponseFormatInterceptor
AuditContextInterceptor
TimeoutInterceptor
```

---

## Exception Filters

Exception filters should convert internal exceptions into standard API error responses.

Error codes must align with:

```text
docs/06-API/ERROR_CODES.md
```

---

# Backend Module Catalog

The following modules define the recommended backend implementation structure.

---

# 1. App Module

## Module Name

```text
AppModule
```

## Purpose

Root module for bootstrapping the backend application.

## Responsibilities

* Load configuration
* Register global modules
* Register feature modules
* Configure global pipes
* Configure global filters
* Configure global interceptors
* Initialize OpenAPI documentation
* Configure CORS
* Configure health endpoints

## Dependencies

* ConfigModule
* DatabaseModule
* AuthModule
* CommonModule
* All feature modules

## MVP Priority

```text
MVP
```

---

# 2. Config Module

## Module Name

```text
ConfigModule
```

## Purpose

Centralized configuration management.

## Responsibilities

* Environment variable loading
* Environment validation
* Application config
* Database config
* Redis config
* JWT config
* File storage config
* AI provider config
* Email/SMS config
* Payment provider config, if enabled
* Feature flag defaults

## Security Rules

* Never expose secrets through API responses.
* Validate required environment variables at startup.
* Use separate config per environment.

## MVP Priority

```text
MVP
```

---

# 3. Database Module

## Module Name

```text
DatabaseModule
```

## Purpose

Database connection and persistence access.

## Responsibilities

* Prisma or ORM client setup
* Database lifecycle management
* Transaction helpers
* Tenant-safe query helpers
* Pagination helpers
* Migration support
* Soft delete conventions

## Recommended Exports

```text
PrismaService
TransactionService
PaginationService
TenantQueryService
```

## Security Rules

All tenant-scoped queries must validate:

```text
organizationId
institutionId
```

where applicable.

## MVP Priority

```text
MVP
```

---

# 4. Common Module

## Module Name

```text
CommonModule
```

## Purpose

Shared utilities used across backend modules.

## Responsibilities

* Request ID generation
* Standard response helpers
* Error helpers
* Date utilities
* Pagination utilities
* Slug/code utilities
* Enum helpers
* Logging helpers
* Validation helpers

## Recommended Exports

```text
RequestContextService
ResponseService
ErrorFactory
LoggerService
DateTimeService
```

## MVP Priority

```text
MVP
```

---

# 5. Auth Module

## Module Name

```text
AuthModule
```

## Purpose

Authentication and session management.

## Responsibilities

* Login
* Registration or invitation-based onboarding
* Password hashing
* Refresh token rotation
* Logout
* Logout all sessions
* Password reset
* Email verification
* Current user context
* Session revocation
* Future MFA hooks

## Main Controllers

```text
AuthController
```

## Main Services

```text
AuthService
TokenService
PasswordService
SessionService
EmailVerificationService
PasswordResetService
```

## Key Guards

```text
JwtAuthGuard
RefreshTokenGuard
ActiveUserGuard
```

## Related Error Codes

```text
AUTH_REQUIRED
AUTH_INVALID_CREDENTIALS
AUTH_INVALID_TOKEN
AUTH_TOKEN_EXPIRED
AUTH_REFRESH_TOKEN_INVALID
AUTH_ACCOUNT_DISABLED
AUTH_EMAIL_NOT_VERIFIED
```

## MVP Priority

```text
MVP
```

---

# 6. Users Module

## Module Name

```text
UsersModule
```

## Purpose

Global user identity and profile management.

## Responsibilities

* User profile
* User preferences
* Account status
* User sessions
* Avatar metadata
* User lookup for admin workflows
* User identity used across memberships

## Important Rule

Users are global identities.

Organization and Institution access must come from memberships, not from hardcoding a single `institutionId` on the user as the only access source.

## Main Controllers

```text
UsersController
```

## Main Services

```text
UsersService
UserPreferencesService
UserSessionsService
```

## MVP Priority

```text
MVP
```

---

# 7. Memberships Module

## Module Name

```text
MembershipsModule
```

## Purpose

Manage user access to Organizations and Institutions.

## Responsibilities

* Organization memberships
* Institution memberships
* Role assignment
* Membership status
* Invitations
* Invitation acceptance
* Permission resolution
* Active membership validation

## Recommended Entities

```text
Membership
Invitation
Role
Permission
```

## Scope Rules

A membership may be scoped to:

```text
Platform
Organization
Institution
```

Institution memberships must include:

```text
organizationId
institutionId
```

## Main Services

```text
MembershipsService
InvitationsService
PermissionsResolverService
```

## Related Error Codes

```text
MEMBERSHIP_NOT_FOUND
MEMBERSHIP_ALREADY_EXISTS
MEMBERSHIP_INVALID_SCOPE
MEMBERSHIP_INVALID_ROLE
RBAC_PERMISSION_REQUIRED
RBAC_ROLE_ASSIGNMENT_NOT_ALLOWED
```

## MVP Priority

```text
MVP
```

---

# 8. Authorization Module

## Module Name

```text
AuthorizationModule
```

## Purpose

Centralized RBAC, permission, scope, and policy enforcement.

## Responsibilities

* Permission guards
* Scope guards
* Resource policy execution
* Role-to-permission mapping
* Authorization service
* Policy registry
* Tenant context validation
* Entitlement integration

## Recommended Services

```text
AuthorizationService
PermissionsService
PolicyRegistryService
TenantScopeService
```

## Recommended Guards

```text
PermissionsGuard
TenantScopeGuard
ResourcePolicyGuard
```

## Important Rule

Do not scatter role checks across services.

Bad:

```ts
if (user.role === 'INSTITUTION_ADMIN') {
  return true;
}
```

Good:

```ts
await authorizationService.assertCan({
  context,
  permission: 'learner.read',
  organizationId,
  institutionId,
  resource,
  policy: LearnerAccessPolicy,
});
```

## MVP Priority

```text
MVP
```

---

# 9. Organizations Module

## Module Name

```text
OrganizationsModule
```

## Purpose

Manage Organization records and Organization-level workflows.

## Responsibilities

* Create Organization
* List Organizations
* Get Organization
* Update Organization
* Deactivate Organization
* Organization settings
* Organization dashboard placeholder
* Organization-level audit access
* Organization-level reports placeholder

## Main Controllers

```text
OrganizationsController
OrganizationSettingsController
```

## Main Services

```text
OrganizationsService
OrganizationSettingsService
```

## Scope

```text
organizationId
```

## MVP Rule

MVP may create one Organization per customer onboarding flow.

## MVP Priority

```text
MVP
```

---

# 10. Institutions Module

## Module Name

```text
InstitutionsModule
```

## Purpose

Manage Institutions under Organizations.

## Responsibilities

* Create Institution
* List Institutions under Organization
* Get Institution
* Update Institution
* Deactivate Institution
* Institution settings
* Institution dashboard placeholder
* Institution type handling

## Main Controllers

```text
InstitutionsController
InstitutionSettingsController
```

## Main Services

```text
InstitutionsService
InstitutionSettingsService
```

## Scope

```text
organizationId
institutionId
```

## MVP Rule

MVP supports:

```text
One Organization → One Institution
```

The module must reject additional Institution creation unless multi-institution support is enabled.

## Institution Types

```text
SCHOOL
ACADEMY
COACHING_INSTITUTE
COLLEGE
UNIVERSITY
TRAINING_CENTRE
LEARNING_CENTRE
ONLINE_ACADEMY
```

## Related Error Codes

```text
INST_NOT_FOUND
INST_ALREADY_EXISTS
INST_INVALID_TYPE
INST_MVP_SINGLE_INSTITUTION_LIMIT
INST_MULTI_INSTITUTION_FEATURE_DISABLED
TENANT_INSTITUTION_NOT_IN_ORGANIZATION
```

## MVP Priority

```text
MVP
```

---

# 11. Academic Module

## Module Name

```text
AcademicModule
```

## Purpose

Manage institution-scoped academic structure.

## Responsibilities

* Academic years
* Classes
* Sections
* Subjects
* Timetable entries
* Teacher academic assignments, where needed
* Class-section structure
* Subject assignment metadata

## Main Controllers

```text
AcademicYearsController
ClassesController
SectionsController
SubjectsController
TimetableController
```

## Main Services

```text
AcademicYearsService
ClassesService
SectionsService
SubjectsService
TimetableService
```

## Scope

```text
organizationId
institutionId
```

## Naming Rule

Use:

```text
academicYear
class
section
subject
```

Avoid:

```text
academicSession
grade
schoolClass
```

unless mapping legacy or UI-specific terminology.

## MVP Priority

```text
MVP
```

---

# 12. Learners Module

## Module Name

```text
LearnersModule
```

## Purpose

Manage learner records and learner academic assignment.

## Responsibilities

* Create learner
* List learners
* Get learner
* Update learner
* Archive learner
* Admission number validation
* Learner class assignment
* Learner profile summary
* Learner reports placeholder

## Main Controllers

```text
LearnersController
LearnerAssignmentsController
```

## Main Services

```text
LearnersService
LearnerAssignmentsService
LearnerProfileService
```

## Scope

```text
organizationId
institutionId
```

## Access Policies

```text
LearnerAccessPolicy
LearnerUpdatePolicy
LearnerAssignmentPolicy
```

## Important Rule

Use `Learner`, not `Student`, in backend code and database naming.

UI may display Student where appropriate.

## Related Error Codes

```text
LEARNER_NOT_FOUND
LEARNER_ALREADY_EXISTS
LEARNER_ADMISSION_NUMBER_EXISTS
LEARNER_INVALID_CLASS_ASSIGNMENT
LEARNER_ACCESS_DENIED
```

## MVP Priority

```text
MVP
```

---

# 13. Guardians Module

## Module Name

```text
GuardiansModule
```

## Purpose

Manage guardian records and Guardian-Learner relationships.

## Responsibilities

* Create guardian
* List guardians
* Get guardian
* Update guardian
* Archive guardian
* Link guardian to learner
* Unlink guardian from learner
* Primary guardian handling
* Guardian communication preferences

## Main Controllers

```text
GuardiansController
GuardianLearnerLinksController
```

## Main Services

```text
GuardiansService
GuardianLearnerLinksService
GuardianAccessService
```

## Scope

```text
organizationId
institutionId
```

## Access Policies

```text
GuardianAccessPolicy
GuardianLearnerPolicy
```

## Important Rule

Use `Guardian`, not `Parent`, in backend code and database naming.

UI may display Parent where appropriate.

## MVP Priority

```text
MVP
```

---

# 14. Teachers Module

## Module Name

```text
TeachersModule
```

## Purpose

Manage teacher profiles and teacher academic assignments.

## Responsibilities

* Create teacher profile for a user
* List teachers
* Get teacher
* Update teacher profile
* Archive teacher profile
* Assign teacher to academic year, class, section, and subject
* Remove teacher assignment
* Validate teacher access to assigned learners

## Main Controllers

```text
TeachersController
TeacherAssignmentsController
```

## Main Services

```text
TeachersService
TeacherAssignmentsService
TeacherAccessService
```

## Scope

```text
organizationId
institutionId
```

## Access Policies

```text
TeacherAccessPolicy
TeacherAssignmentPolicy
```

## MVP Priority

```text
MVP
```

---

# 15. Attendance Module

## Module Name

```text
AttendanceModule
```

## Purpose

Manage learner attendance workflows.

## Responsibilities

* Create attendance session
* List attendance sessions
* Submit attendance records
* Update attendance records
* Lock attendance session
* Learner attendance summary
* Attendance reports

## Main Controllers

```text
AttendanceSessionsController
AttendanceRecordsController
AttendanceReportsController
```

## Main Services

```text
AttendanceSessionsService
AttendanceRecordsService
AttendanceReportsService
```

## Scope

```text
organizationId
institutionId
```

## Access Policies

```text
AttendanceSessionPolicy
AttendanceRecordUpdatePolicy
```

## Audit Requirements

Audit required for:

* Attendance correction after submission
* Attendance update after lock
* Attendance session deletion or archive

## MVP Priority

```text
MVP
```

---

# 16. Assessments Module

## Module Name

```text
AssessmentsModule
```

## Purpose

Manage assessments, results, and learner performance records.

## Responsibilities

* Create assessment
* List assessments
* Get assessment
* Update assessment
* Archive assessment
* Submit assessment results
* Update assessment results
* Lock assessment
* Learner assessment summary
* Assessment reports

## Main Controllers

```text
AssessmentsController
AssessmentResultsController
AssessmentReportsController
```

## Main Services

```text
AssessmentsService
AssessmentResultsService
AssessmentReportsService
```

## Scope

```text
organizationId
institutionId
```

## Access Policies

```text
AssessmentAccessPolicy
AssessmentResultUpdatePolicy
```

## Audit Requirements

Audit required for:

* Result submission
* Result correction
* Locked result update
* Assessment deletion or archive

## MVP Priority

```text
MVP
```

---

# 17. Fees Module

## Module Name

```text
FeesModule
```

## Purpose

Manage institution fee structures and fee payments.

## Responsibilities

* Fee categories
* Fee structures
* Learner fee assignments
* Fee payment records
* Receipts
* Fee reports

## Main Controllers

```text
FeeCategoriesController
FeeStructuresController
FeeAssignmentsController
FeePaymentsController
FeeReportsController
```

## Main Services

```text
FeeCategoriesService
FeeStructuresService
FeeAssignmentsService
FeePaymentsService
FeeReportsService
```

## Scope

```text
organizationId
institutionId
```

## Audit Requirements

Audit required for:

* Fee payment record creation
* Fee payment modification
* Fee waiver or discount
* Receipt regeneration

## MVP Priority

```text
V1 or MVP Optional
```

## Product Constraint

Fee management should be included in MVP only if institution administration requires it for launch validation.

---

# 18. Communication Module

## Module Name

```text
CommunicationModule
```

## Purpose

Manage announcements and direct communication workflows.

## Responsibilities

* Announcements
* Audience targeting
* Messages
* Guardian communication
* Teacher communication
* Communication drafts
* Delivery status

## Main Controllers

```text
AnnouncementsController
MessagesController
```

## Main Services

```text
AnnouncementsService
MessagesService
AudienceResolverService
```

## Scope

```text
organizationId
institutionId
```

## Privacy Rule

Recipient eligibility must be validated before sending.

Do not expose recipients across unrelated classes, sections, Institutions, or Organizations.

## MVP Priority

```text
MVP for announcements
V1 for direct messages
```

---

# 19. Notifications Module

## Module Name

```text
NotificationsModule
```

## Purpose

Manage in-app notifications and notification preferences.

## Responsibilities

* Create notification
* List user notifications
* Mark notification as read
* Mark all as read
* Notification preferences
* Notification delivery events

## Main Controllers

```text
NotificationsController
NotificationPreferencesController
```

## Main Services

```text
NotificationsService
NotificationPreferencesService
NotificationDeliveryService
```

## Scope

```text
User-owned
Organization and Institution metadata where applicable
```

## MVP Priority

```text
MVP
```

---

# 20. Files Module

## Module Name

```text
FilesModule
```

## Purpose

Manage file uploads, metadata, signed URLs, and file access.

## Responsibilities

* Upload file
* Validate file
* Store metadata
* Generate signed URL
* Delete file
* File access policy
* File safety scan hook
* File relation to learner, institution, AI, OCR, or reports

## Main Controllers

```text
FilesController
```

## Main Services

```text
FilesService
FileStorageService
FileAccessService
FileValidationService
SignedUrlService
```

## Scope

```text
organizationId
institutionId
uploadedByUserId
relatedEntityType
relatedEntityId
```

## Supported MVP File Types

```text
JPG
JPEG
PNG
WEBP
PDF
```

## MVP Priority

```text
MVP
```

---

# 21. AI Module

## Module Name

```text
AiModule
```

## Purpose

Coordinate AI-powered workflows.

## Responsibilities

* Learner insights
* Assessment feedback
* Communication drafts
* AI prompt rendering
* Model routing
* AI provider abstraction
* AI safety checks
* AI usage tracking
* AI audit metadata
* AI entitlement checks

## Main Controllers

```text
AiInsightsController
AiFeedbackController
AiDraftsController
```

## Main Services

```text
AiService
AiProviderService
AiPromptService
AiModelRouterService
AiSafetyService
AiUsageService
AiContextService
```

## Scope

```text
organizationId
institutionId
resource access policy
```

## Important Rule

AI context retrieval must be tenant-safe.

AI must not retrieve or process data outside the authorized Organization and Institution.

## MVP Priority

```text
V1 or MVP Optional
```

## Product Constraint

AI should support the institution-first MVP without becoming a separate direct-to-student product unless explicitly prioritized.

---

# 22. OCR Module

## Module Name

```text
OcrModule
```

## Purpose

Manage OCR processing workflows.

## Responsibilities

* Create OCR job
* Validate source file
* Extract text
* Store OCR output
* Confirm or correct OCR result
* Track provider status
* Handle OCR failures

## Main Controllers

```text
OcrJobsController
```

## Main Services

```text
OcrService
OcrProviderService
OcrResultService
```

## Dependencies

* FilesModule
* JobsModule
* AiModule, if OCR is AI-assisted

## Scope

```text
organizationId
institutionId
file access policy
```

## MVP Priority

```text
V1 or MVP Optional
```

---

# 23. Reports Module

## Module Name

```text
ReportsModule
```

## Purpose

Generate operational reports.

## Responsibilities

* Attendance reports
* Assessment reports
* Learner profile reports
* Fee reports, if enabled
* Institution reports
* Export jobs
* Report access control
* Report file generation

## Main Controllers

```text
ReportsController
```

## Main Services

```text
ReportsService
ReportExportService
ReportAccessService
```

## Scope

```text
organizationId
institutionId
filters
```

## Audit Requirements

Audit required for exports containing learner, guardian, assessment, attendance, or fee data.

## MVP Priority

```text
MVP for basic reports
V1 for exports
```

---

# 24. Audit Module

## Module Name

```text
AuditModule
```

## Purpose

Record and expose audit logs for sensitive actions.

## Responsibilities

* Create audit log records
* Query audit logs
* Audit sensitive actions
* Audit security events
* Audit admin activity
* Audit data exports
* Audit AI-sensitive operations

## Main Controllers

```text
AuditLogsController
```

## Main Services

```text
AuditService
AuditQueryService
```

## Scope

```text
organizationId
institutionId optional
```

## Important Rule

Audit logging should be easy to call from all modules.

Audit failures should be visible but must not expose sensitive data.

## MVP Priority

```text
MVP
```

---

# 25. Jobs Module

## Module Name

```text
JobsModule
```

## Purpose

Manage background jobs and asynchronous processing.

## Responsibilities

* Job creation
* Job status
* Queue integration
* Worker registration
* Retry handling
* Failure handling
* Job result metadata
* Tenant context propagation

## Main Controllers

```text
JobsController
```

## Main Services

```text
JobsService
QueueService
JobStatusService
```

## Job Metadata

Jobs must include:

```text
jobId
jobType
requestedByUserId
organizationId
institutionId
resourceType
resourceId
status
createdAt
updatedAt
```

## MVP Priority

```text
MVP
```

---

# 26. Entitlements Module

## Module Name

```text
EntitlementsModule
```

## Purpose

Control access to premium, costly, or feature-gated workflows.

## Responsibilities

* Feature definitions
* Entitlement validation
* Usage limits
* Quota checks
* Feature access checks
* Plan mapping, if subscription module is enabled

## Main Controllers

```text
EntitlementsController
UsageController
```

## Main Services

```text
EntitlementsService
UsageService
FeatureAccessService
```

## Important Rule

Check feature access, not plan names.

Bad:

```ts
if (plan === 'PREMIUM') {}
```

Good:

```ts
canUseFeature('ai.insight.generate')
```

## MVP Priority

```text
MVP if AI or paid limits are included
V1 otherwise
```

---

# 27. Billing Module

## Module Name

```text
BillingModule
```

## Purpose

Manage subscriptions, plans, invoices, and payments if commerce is included.

## Responsibilities

* Plans
* Subscriptions
* Invoices
* Payments
* Payment confirmation
* Refunds
* Provider webhooks
* Billing audit records

## Main Controllers

```text
PlansController
SubscriptionsController
PaymentsController
InvoicesController
PaymentWebhooksController
```

## Main Services

```text
PlansService
SubscriptionsService
PaymentsService
InvoicesService
PaymentProviderService
WebhookVerificationService
```

## MVP Priority

```text
Deferred or MVP Optional
```

## Product Constraint

Billing should not be included in the first backend build unless it is necessary for launch.

Institution-first academic workflows should not be blocked by billing complexity.

---

# 28. Webhooks Module

## Module Name

```text
WebhooksModule
```

## Purpose

Handle external provider callbacks.

## Responsibilities

* Verify webhook signatures
* Validate payloads
* Enforce idempotency
* Persist webhook events
* Dispatch async processing
* Return provider-safe responses

## Main Controllers

```text
WebhooksController
```

## Main Services

```text
WebhooksService
WebhookVerificationService
WebhookEventService
```

## MVP Priority

```text
Deferred unless payments or external integrations are included
```

---

# 29. Admin Module

## Module Name

```text
AdminModule
```

## Purpose

Expose platform administration workflows.

## Responsibilities

* Platform dashboard
* Admin user search
* Organization administration
* Institution administration
* Feature flags
* AI usage review
* Audit log access
* Support workflows

## Main Controllers

```text
AdminController
AdminUsersController
AdminOrganizationsController
AdminInstitutionsController
AdminAuditController
AdminFeatureFlagsController
```

## Security Rule

Admin operations must require explicit platform permissions and audit logging.

## MVP Priority

```text
MVP for minimal platform admin
V1 for extended admin
```

---

# 30. Health Module

## Module Name

```text
HealthModule
```

## Purpose

Expose health and readiness endpoints.

## Responsibilities

* Liveness check
* Readiness check
* Dependency checks
* Database health
* Cache health
* Queue health

## Main Controllers

```text
HealthController
```

## Endpoints

```text
GET /health
GET /health/live
GET /health/ready
```

## MVP Priority

```text
MVP
```

---

# Module Dependency Rules

## Allowed Dependency Direction

Preferred dependency direction:

```text
Common / Config / Database
    ↓
Auth / Users / Memberships / Authorization
    ↓
Organizations / Institutions
    ↓
Academic / Learners / Guardians / Teachers
    ↓
Attendance / Assessments / Fees / Communication
    ↓
Reports / AI / OCR / Notifications / Jobs
    ↓
Admin
```

## Avoid Circular Dependencies

Avoid circular module imports.

If two modules need shared logic, extract it into:

```text
CommonModule
Domain policy
Shared service
Repository abstraction
```

## Cross-Module Access

One module should not directly manipulate another module's data unless it owns that workflow.

Example:

AttendanceModule should not directly update Learner records.

It should call LearnersService or use a documented domain method if needed.

---

# MVP Backend Module Set

The first backend implementation should include:

```text
AppModule
ConfigModule
DatabaseModule
CommonModule
AuthModule
UsersModule
MembershipsModule
AuthorizationModule
OrganizationsModule
InstitutionsModule
AcademicModule
LearnersModule
GuardiansModule
TeachersModule
AttendanceModule
AssessmentsModule
CommunicationModule
NotificationsModule
FilesModule
ReportsModule
AuditModule
JobsModule
HealthModule
AdminModule
```

Optional for MVP depending on product scope:

```text
AiModule
OcrModule
FeesModule
EntitlementsModule
BillingModule
WebhooksModule
```

---

# Suggested MVP Build Order

Recommended implementation order:

```text
1. AppModule
2. ConfigModule
3. DatabaseModule
4. CommonModule
5. AuthModule
6. UsersModule
7. MembershipsModule
8. AuthorizationModule
9. OrganizationsModule
10. InstitutionsModule
11. AcademicModule
12. LearnersModule
13. GuardiansModule
14. TeachersModule
15. AttendanceModule
16. AssessmentsModule
17. FilesModule
18. CommunicationModule
19. NotificationsModule
20. ReportsModule
21. AuditModule
22. JobsModule
23. HealthModule
24. AdminModule
```

AI, OCR, Fees, Billing, and Webhooks should be added only after core Institution operations are stable unless they are mandatory for the launch proposition.

---

# Module Testing Requirements

Each backend module must include tests for:

* Service success cases
* DTO validation failures
* Authentication failures
* Authorization failures
* Organization scope violations
* Institution scope violations
* Ownership or assignment failures
* Repository tenant-safe queries
* Audit logging where required
* Error code consistency

High-risk modules require integration tests:

```text
AuthModule
AuthorizationModule
MembershipsModule
OrganizationsModule
InstitutionsModule
LearnersModule
GuardiansModule
TeachersModule
AttendanceModule
AssessmentsModule
FilesModule
AI Module
ReportsModule
AdminModule
```

---

# Module Documentation Requirements

Each backend module should document:

* Purpose
* Owned entities
* Main controllers
* Main services
* Required permissions
* Scope rules
* Error codes
* Audit requirements
* Events emitted
* Jobs created
* External dependencies
* Test coverage expectations

---

# Backend Module Review Checklist

Before approving a backend module, confirm:

* Module has clear ownership.
* Module uses Organization and Institution terminology correctly.
* No `schoolId` is introduced.
* No `student` or `parent` backend entity is introduced unless explicitly mapped as UI terminology.
* Controllers are thin.
* Services contain business logic.
* DTOs prevent mass assignment.
* Repositories use tenant-safe queries.
* Guards and policies enforce access.
* Error codes match `ERROR_CODES.md`.
* Audit logs exist for sensitive actions.
* Tests cover denied access paths.
* OpenAPI decorators are planned or implemented.
* Background jobs preserve tenant context.
* AI workflows, if any, are tenant-safe and privacy-aware.

---

# Related Documents

* docs/04-Architecture/MODULES.md
* docs/04-Architecture/SYSTEM_ARCHITECTURE.md
* docs/04-Architecture/PROJECT_STRUCTURE.md
* docs/04-Architecture/CODING_STANDARDS.md
* docs/05-Database/DATABASE_SCHEMA.md
* docs/05-Database/DATA_DICTIONARY.md
* docs/06-API/API_GUIDELINES.md
* docs/06-API/ENDPOINTS.md
* docs/06-API/ERROR_CODES.md
* docs/11-Security/SECURITY.md
* docs/11-Security/RBAC.md
* docs/11-Security/PRIVACY.md

