# Backend Services

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Backend Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the backend service layer standards for Acadedx.

It describes how services should be designed, named, scoped, tested, and integrated across backend modules.

This document is implementation-specific for the backend application and should be read together with:

```text
docs/09-Backend/MODULES.md
````

Acadedx is a school-focused MVP with a future-ready platform architecture:

```text
Organization → Institution → Academic Operations
```

A School is an Institution type.

Backend services must use `institutionId`, not `schoolId`.

---

# Service Layer Goals

Backend services must ensure:

1. Business logic is centralized and testable.
2. Controllers remain thin.
3. Organization and Institution scope is enforced consistently.
4. Authorization policies are reusable.
5. Database access is tenant-safe.
6. Sensitive actions are audited.
7. Background jobs preserve tenant context.
8. AI and OCR workflows do not bypass privacy or security.
9. Services are modular enough for future extraction.
10. MVP delivery remains practical without hardcoding school-only assumptions.

---

# Service Layer Position

Recommended request flow:

```text
Controller
  ↓
Guards
  ↓
DTO Validation
  ↓
Service
  ↓
Policy / Authorization Service
  ↓
Repository / Prisma Service
  ↓
Database
```

For asynchronous workflows:

```text
Controller
  ↓
Service
  ↓
Authorization / Entitlement Check
  ↓
Job Service
  ↓
Queue Worker
  ↓
Domain Service
  ↓
Repository / External Provider
```

---

# Service Design Principles

## 1. Controllers Must Stay Thin

Controllers should handle:

* Route binding
* DTO parsing
* Guard decorators
* OpenAPI metadata
* Calling services
* Returning service output

Controllers should not contain:

* Business rules
* Tenant queries
* Authorization logic
* AI orchestration
* Payment logic
* Complex data transformations

Bad:

```ts
@Post()
async createLearner(@Body() dto: CreateLearnerDto) {
  if (!dto.institutionId) throw new BadRequestException();
  const exists = await this.prisma.learner.findFirst({
    where: { admissionNumber: dto.admissionNumber },
  });
  if (exists) throw new ConflictException();
  return this.prisma.learner.create({ data: dto });
}
```

Better:

```ts
@Post()
@RequirePermissions('learner.create')
async createLearner(
  @CurrentContext() context: RequestContext,
  @Param() params: InstitutionParamsDto,
  @Body() dto: CreateLearnerDto,
) {
  return this.learnersService.createLearner(context, params, dto);
}
```

---

## 2. Services Own Business Rules

Services should enforce:

* Domain invariants
* Valid state transitions
* Tenant scope validation
* Authorization policy calls
* Audit decisions
* Job orchestration
* Integration workflows
* Error code mapping

Example:

```ts
async createLearner(
  context: RequestContext,
  params: InstitutionParamsDto,
  dto: CreateLearnerDto,
): Promise<LearnerDto> {
  await this.authorizationService.assertCan({
    context,
    permission: 'learner.create',
    organizationId: params.organizationId,
    institutionId: params.institutionId,
  });

  await this.institutionsService.assertInstitutionBelongsToOrganization({
    organizationId: params.organizationId,
    institutionId: params.institutionId,
  });

  await this.assertAdmissionNumberAvailable({
    organizationId: params.organizationId,
    institutionId: params.institutionId,
    admissionNumber: dto.admissionNumber,
  });

  const learner = await this.learnersRepository.create({
    organizationId: params.organizationId,
    institutionId: params.institutionId,
    ...dto,
    createdByUserId: context.userId,
  });

  await this.auditService.record({
    context,
    action: 'learner.create',
    resourceType: 'Learner',
    resourceId: learner.id,
    organizationId: params.organizationId,
    institutionId: params.institutionId,
  });

  return LearnerMapper.toDto(learner);
}
```

---

## 3. Services Must Be Tenant-Aware

Any service method handling tenant-scoped resources must accept scope explicitly.

Preferred method shape:

```ts
service.method(context, scope, dto)
```

Example:

```ts
createAssessment(
  context: RequestContext,
  scope: {
    organizationId: string;
    institutionId: string;
  },
  dto: CreateAssessmentDto,
)
```

Avoid:

```ts
createAssessment(dto: CreateAssessmentDto)
```

because scope becomes implicit and easy to bypass.

---

## 4. Services Must Not Trust Client Scope

Client-supplied IDs must be validated.

If request path contains:

```text
/organizations/{organizationId}/institutions/{institutionId}/learners/{learnerId}
```

the service must validate:

* User has access to `organizationId`.
* User has access to `institutionId`.
* Institution belongs to Organization.
* Learner belongs to Institution and Organization.

---

## 5. Services Should Return DTOs

Services should not return raw ORM entities directly.

Use response DTOs or mappers.

Reason:

* Prevent leaking internal fields.
* Enforce consistent response shape.
* Protect sensitive fields.
* Decouple API from database schema.

Bad:

```ts
return this.prisma.user.findUnique(...);
```

Better:

```ts
const user = await this.usersRepository.findById(...);
return UserMapper.toDto(user);
```

---

## 6. Services Should Use Domain-Specific Errors

Do not throw generic exceptions where domain error codes are required.

Bad:

```ts
throw new Error('Not found');
```

Better:

```ts
throw new AppException({
  code: 'LEARNER_NOT_FOUND',
  message: 'Learner was not found.',
  statusCode: 404,
});
```

Error codes must align with:

```text
docs/06-API/ERROR_CODES.md
```

---

# Service Naming Standards

Use clear domain names.

Good:

```text
OrganizationsService
InstitutionsService
LearnersService
GuardiansService
TeacherAssignmentsService
AttendanceRecordsService
AssessmentResultsService
FilesService
AiInsightsService
```

Avoid vague names:

```text
DataService
ManagerService
HelperService
CommonService
SchoolService
StudentService
ParentService
```

Use `Guardian`, not `Parent`.

Use `Learner`, not `Student`.

Use `Institution`, not `School`.

---

# Service Method Naming Standards

Use intention-revealing names.

Good:

```ts
createLearner()
getLearnerById()
listLearners()
updateLearner()
archiveLearner()
assignLearnerToClass()
linkGuardianToLearner()
markAttendance()
submitAssessmentResults()
generateLearnerInsight()
```

Avoid ambiguous names:

```ts
process()
handle()
doUpdate()
saveData()
manage()
```

Generic method names may be acceptable only in private helpers when context is clear.

---

# Service Categories

Acadedx backend services can be grouped into these categories:

1. Domain services
2. Application services
3. Policy services
4. Repository services
5. Integration services
6. Infrastructure services
7. Background job services
8. Mapping services
9. Validation services
10. Reporting services

---

# Domain Services

Domain services implement business rules for a specific domain.

Examples:

```text
LearnersService
GuardiansService
TeachersService
AttendanceRecordsService
AssessmentResultsService
InstitutionsService
OrganizationsService
```

Responsibilities:

* Validate domain state.
* Enforce business invariants.
* Coordinate repositories.
* Call policies where needed.
* Emit audit events.
* Return DTOs.

Domain services should not directly handle HTTP request objects.

---

# Application Services

Application services orchestrate workflows across multiple domain services.

Examples:

```text
OnboardingService
InstitutionSetupService
LearnerImportService
AcademicYearRolloverService
AssessmentPublishingService
ReportExportService
```

Responsibilities:

* Coordinate multi-step use cases.
* Manage transactions.
* Trigger background jobs.
* Call multiple domain services.
* Emit audit events.
* Handle workflow-level errors.

Example:

```text
InstitutionSetupService
```

may create:

* Institution
* Default academic year
* Default classes
* Default sections
* Initial institution admin membership

---

# Policy Services

Policy services enforce resource-specific access rules.

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

Responsibilities:

* Evaluate whether a user can access or modify a specific resource.
* Combine role, permission, scope, ownership, assignment, and relationship checks.
* Return allow/deny decisions.
* Avoid database over-fetching.

Policy services must be test-heavy.

---

# Repository Services

Repository services handle persistence.

Examples:

```text
LearnersRepository
GuardiansRepository
InstitutionsRepository
AttendanceRepository
AssessmentsRepository
FilesRepository
```

Responsibilities:

* Database queries
* Tenant-safe filtering
* Pagination
* Sorting
* Persistence
* Soft delete handling

Repository services should not contain high-level business rules.

Repository methods for scoped resources must include scope.

Good:

```ts
findByIdInInstitution(params: {
  organizationId: string;
  institutionId: string;
  learnerId: string;
})
```

Bad:

```ts
findById(learnerId: string)
```

---

# Integration Services

Integration services wrap external providers.

Examples:

```text
AiProviderService
OcrProviderService
EmailProviderService
SmsProviderService
PaymentProviderService
StorageProviderService
```

Responsibilities:

* Provider API calls
* Provider error mapping
* Retry-safe behavior
* Timeouts
* Circuit breaker support where needed
* Provider-specific request/response translation

Integration services must not leak provider-specific models into domain services.

---

# Infrastructure Services

Infrastructure services provide platform-level capabilities.

Examples:

```text
ConfigService
LoggerService
RequestContextService
AuditService
QueueService
CacheService
FeatureFlagService
EntitlementsService
UsageService
```

Responsibilities:

* Cross-cutting concerns
* Configuration
* Logging
* Audit
* Rate limits
* Jobs
* Feature access
* Usage tracking

---

# Background Job Services

Background job services manage async workflows.

Examples:

```text
JobsService
OcrJobProcessor
ReportExportProcessor
AiGenerationProcessor
NotificationDeliveryProcessor
```

Responsibilities:

* Create jobs
* Persist job status
* Store tenant context
* Execute async work
* Retry failures safely
* Record job result
* Audit sensitive completion events

Every job must include:

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
```

where applicable.

---

# Mapping Services

Mapping services or mapper classes convert internal models to DTOs.

Examples:

```text
LearnerMapper
GuardianMapper
InstitutionMapper
AssessmentMapper
AttendanceMapper
UserMapper
```

Responsibilities:

* Output filtering
* Field renaming
* Sensitive data removal
* UI/API-friendly response shaping

Mappers should not query the database.

---

# Validation Services

Validation services enforce reusable validation beyond DTO shape validation.

Examples:

```text
InstitutionValidationService
AcademicYearValidationService
ClassSectionValidationService
AdmissionNumberValidationService
TeacherAssignmentValidationService
FileValidationService
```

Responsibilities:

* Cross-field validation
* State validation
* Uniqueness validation
* Business-rule validation

---

# Reporting Services

Reporting services generate read models, summaries, and exports.

Examples:

```text
AttendanceReportsService
AssessmentReportsService
LearnerProfileReportService
FeeReportsService
OrganizationReportsService
InstitutionReportsService
```

Responsibilities:

* Validate report scope
* Apply row-level access control
* Query aggregated data
* Generate export jobs
* Mask sensitive fields
* Audit exports

---

# Required Core Services

The backend MVP should include these service groups.

---

## Auth Services

### AuthService

Responsibilities:

* Login
* Registration or invitation onboarding
* Current user context
* Logout coordination
* Authentication errors

### TokenService

Responsibilities:

* Access token generation
* Refresh token generation
* Token validation
* Token rotation
* Token revocation

### PasswordService

Responsibilities:

* Password hashing
* Password verification
* Password reset token validation
* Password policy validation

### SessionService

Responsibilities:

* Session creation
* Session listing
* Session revocation
* Logout all sessions

MVP Priority:

```text
MVP
```

---

## User Services

### UsersService

Responsibilities:

* User lookup
* User profile update
* User status management
* Safe user DTO mapping

### UserPreferencesService

Responsibilities:

* User preferences
* Notification defaults
* Locale and timezone preferences

### UserSessionsService

Responsibilities:

* Active session listing
* Session revoke workflow

MVP Priority:

```text
MVP
```

---

## Membership and Authorization Services

### MembershipsService

Responsibilities:

* Membership creation
* Membership lookup
* Membership activation/deactivation
* Role assignment
* Membership scope validation

### InvitationsService

Responsibilities:

* Invitation creation
* Invitation acceptance
* Invitation revocation
* Invitation expiry handling

### PermissionsResolverService

Responsibilities:

* Resolve permissions from roles.
* Apply scoped memberships.
* Apply permission overrides where allowed.
* Return effective permissions.

### AuthorizationService

Responsibilities:

* Central `assertCan` API
* Permission validation
* Scope validation
* Resource policy dispatch
* Deny-by-default enforcement

### TenantScopeService

Responsibilities:

* Validate Organization access.
* Validate Institution access.
* Validate Institution belongs to Organization.
* Prevent cross-tenant access.

MVP Priority:

```text
MVP
```

---

## Organization and Institution Services

### OrganizationsService

Responsibilities:

* Organization CRUD
* Organization status validation
* Organization type validation
* Organization deactivation rules

### OrganizationSettingsService

Responsibilities:

* Organization settings read/update
* Default timezone and currency
* Organization-level feature configuration

### InstitutionsService

Responsibilities:

* Institution CRUD
* Institution type validation
* Institution status validation
* Institution belongs-to-Organization validation
* MVP one-Institution rule

### InstitutionSettingsService

Responsibilities:

* Institution settings read/update
* Academic year start month
* Institution timezone
* Institution-level feature configuration

MVP Priority:

```text
MVP
```

---

## Academic Services

### AcademicYearsService

Responsibilities:

* Academic year creation
* Academic year date validation
* Active academic year selection
* Overlap prevention
* Archive rules

### ClassesService

Responsibilities:

* Class creation
* Class ordering
* Class status
* Class archive rules

### SectionsService

Responsibilities:

* Section creation
* Section capacity
* Section status
* Section archive rules

### SubjectsService

Responsibilities:

* Subject creation
* Subject assignment
* Subject archive rules

### TimetableService

Responsibilities:

* Timetable entry creation
* Conflict detection
* Teacher/class/section availability checks

MVP Priority:

```text
MVP for academic years, classes, sections, subjects
V1 for timetable
```

---

## Learner Services

### LearnersService

Responsibilities:

* Learner creation
* Learner profile update
* Learner listing
* Learner archive
* Learner status validation
* Admission number uniqueness

### LearnerAssignmentsService

Responsibilities:

* Assign learner to academic year, class, and section.
* Validate class-section relationship.
* Preserve assignment history.
* Prevent invalid active assignments.

### LearnerProfileService

Responsibilities:

* Learner profile summaries
* Learner academic summary composition
* Privacy-safe learner response shaping

### LearnerAccessService

Responsibilities:

* Learner access checks
* Guardian relationship checks
* Teacher assignment checks
* Learner self-access checks

MVP Priority:

```text
MVP
```

---

## Guardian Services

### GuardiansService

Responsibilities:

* Guardian creation
* Guardian profile update
* Guardian listing
* Guardian archive
* Guardian status validation

### GuardianLearnerLinksService

Responsibilities:

* Link guardian to learner.
* Unlink guardian from learner.
* Validate relationship type.
* Ensure primary guardian rules.
* Prevent duplicate links.

### GuardianAccessService

Responsibilities:

* Validate guardian access to linked learner.
* Validate guardian data visibility.
* Validate communication eligibility.

MVP Priority:

```text
MVP
```

---

## Teacher Services

### TeachersService

Responsibilities:

* Teacher profile creation
* Teacher profile update
* Teacher listing
* Teacher archive
* Employee code uniqueness

### TeacherAssignmentsService

Responsibilities:

* Assign teacher to academic year, class, section, and subject.
* Prevent conflicting assignments.
* Remove assignments.
* Query teacher assignment context.

### TeacherAccessService

Responsibilities:

* Validate teacher access to learners.
* Validate teacher access to attendance sessions.
* Validate teacher access to assessment workflows.

MVP Priority:

```text
MVP
```

---

## Attendance Services

### AttendanceSessionsService

Responsibilities:

* Create attendance sessions.
* Prevent duplicate attendance session.
* Validate class and section.
* Lock attendance sessions.
* List attendance sessions.

### AttendanceRecordsService

Responsibilities:

* Submit attendance records.
* Update attendance records.
* Validate attendance status.
* Prevent unauthorized corrections.
* Audit corrections.

### AttendanceReportsService

Responsibilities:

* Learner attendance summary.
* Class/section attendance summary.
* Institution attendance report.
* Export support in future.

MVP Priority:

```text
MVP
```

---

## Assessment Services

### AssessmentsService

Responsibilities:

* Create assessments.
* Update assessments.
* Archive assessments.
* Lock assessments.
* Validate assessment type, date, and max marks.

### AssessmentResultsService

Responsibilities:

* Submit results.
* Update results.
* Validate marks and grades.
* Prevent unauthorized corrections.
* Audit result changes.

### AssessmentReportsService

Responsibilities:

* Learner assessment summary.
* Class/section assessment summary.
* Subject performance summary.
* Export support in future.

MVP Priority:

```text
MVP
```

---

## Fee Services

### FeeCategoriesService

Responsibilities:

* Fee category creation
* Fee category updates
* Fee category archive rules

### FeeStructuresService

Responsibilities:

* Fee structure creation
* Fee amount validation
* Billing frequency validation
* Academic year/class mapping

### FeeAssignmentsService

Responsibilities:

* Assign fees to learners
* Apply discounts
* Validate due dates
* Prevent duplicate active assignments

### FeePaymentsService

Responsibilities:

* Record payments
* Validate payment amount
* Generate receipt metadata
* Audit payment changes

### FeeReportsService

Responsibilities:

* Learner fee summary
* Pending fee report
* Payment collection report

MVP Priority:

```text
MVP Optional or V1
```

Constraint:

Fees should not delay the academic-core MVP unless fee management is part of the launch promise.

---

## Communication Services

### AnnouncementsService

Responsibilities:

* Create announcement
* Resolve audience
* Publish announcement
* Archive announcement
* Track visibility

### MessagesService

Responsibilities:

* Send direct message
* Validate recipient
* Store communication record
* Coordinate notification delivery

### AudienceResolverService

Responsibilities:

* Resolve audience by Institution, class, section, role, guardian group, or learner group.
* Prevent cross-tenant recipients.
* Validate communication eligibility.

MVP Priority:

```text
MVP for announcements
V1 for direct messages
```

---

## Notification Services

### NotificationsService

Responsibilities:

* Create notification
* List user notifications
* Mark notification read
* Mark all notifications read
* Hide/delete notification

### NotificationPreferencesService

Responsibilities:

* User notification preferences
* Channel preferences
* Opt-in/opt-out rules

### NotificationDeliveryService

Responsibilities:

* In-app delivery
* Email delivery coordination
* SMS delivery coordination where enabled
* Delivery status tracking

MVP Priority:

```text
MVP
```

---

## File Services

### FilesService

Responsibilities:

* File upload coordination
* File metadata creation
* File retrieval
* File deletion
* Related entity validation

### FileStorageService

Responsibilities:

* Storage provider abstraction
* Object upload
* Object deletion
* Object metadata
* Storage key generation

### FileValidationService

Responsibilities:

* File size validation
* MIME validation
* Extension validation
* Safety scan hook
* File category validation

### FileAccessService

Responsibilities:

* Validate file access
* Validate related entity access
* Generate access decisions

### SignedUrlService

Responsibilities:

* Generate signed URLs
* Enforce expiration
* Avoid exposing raw storage keys

MVP Priority:

```text
MVP
```

---

## AI Services

### AiService

Responsibilities:

* AI workflow orchestration
* AI request validation
* AI provider selection
* AI output handling

### AiContextService

Responsibilities:

* Retrieve tenant-safe context.
* Minimize data sent to AI.
* Apply learner, guardian, teacher, and institution policies.

### AiPromptService

Responsibilities:

* Prompt template loading
* Prompt rendering
* Prompt versioning
* Prompt safety metadata

### AiModelRouterService

Responsibilities:

* Select model by workflow, cost, latency, safety, and capability.
* Support future model fallback.

### AiSafetyService

Responsibilities:

* Input safety checks
* Output safety checks
* Policy enforcement
* Unsafe output handling

### AiUsageService

Responsibilities:

* Track usage
* Track token/cost metadata
* Enforce limits through EntitlementsService

MVP Priority:

```text
MVP Optional or V1
```

Constraint:

AI services must support institution workflows first. Avoid turning the backend into a separate direct-to-student AI app unless that remains an explicit product direction.

---

## OCR Services

### OcrService

Responsibilities:

* OCR job orchestration
* File validation
* OCR provider call
* OCR result persistence
* OCR status handling

### OcrProviderService

Responsibilities:

* External OCR provider abstraction
* Provider request/response mapping
* Provider error mapping

### OcrResultService

Responsibilities:

* Store OCR output
* Confirm OCR result
* Correct OCR result
* Protect extracted text

MVP Priority:

```text
MVP Optional or V1
```

---

## Report Services

### ReportsService

Responsibilities:

* Report request validation
* Report routing
* Report permission checks
* Report metadata

### ReportExportService

Responsibilities:

* Export job creation
* File generation
* Signed URL generation
* Export audit records

### ReportAccessService

Responsibilities:

* Validate report-level access.
* Validate row-level scope.
* Prevent unauthorized data exposure.

MVP Priority:

```text
MVP for read reports
V1 for exports
```

---

## Audit Services

### AuditService

Responsibilities:

* Record audit event
* Normalize audit payload
* Store before/after state where safe
* Avoid sensitive value leakage

### AuditQueryService

Responsibilities:

* Query audit logs by Organization.
* Query audit logs by Institution.
* Filter by actor, action, resource, date, and outcome.

MVP Priority:

```text
MVP
```

---

## Job Services

### JobsService

Responsibilities:

* Create job
* Get job status
* Update job status
* Store job result metadata
* Store tenant context

### QueueService

Responsibilities:

* Queue abstraction
* Enqueue work
* Retry configuration
* Dead-letter handling

### JobStatusService

Responsibilities:

* Status transitions
* Retry counters
* Failure reasons
* Result readiness checks

MVP Priority:

```text
MVP
```

---

## Entitlement and Usage Services

### EntitlementsService

Responsibilities:

* Validate feature access
* Resolve feature entitlements
* Reject disabled or unavailable features

### UsageService

Responsibilities:

* Track usage counters
* Enforce usage limits
* Reset counters by billing or policy period

### FeatureAccessService

Responsibilities:

* Combine feature flags, entitlements, and scope.
* Return feature access decision.

MVP Priority:

```text
MVP if AI, OCR, exports, or paid limits are included
V1 otherwise
```

---

## Billing Services

### PlansService

Responsibilities:

* Plan definitions
* Plan feature mapping
* Plan visibility

### SubscriptionsService

Responsibilities:

* Subscription creation
* Subscription status
* Subscription changes
* Cancellation and resume workflows

### PaymentsService

Responsibilities:

* Payment creation
* Payment confirmation
* Payment status
* Payment audit records

### InvoicesService

Responsibilities:

* Invoice listing
* Invoice retrieval
* Invoice download metadata

### PaymentProviderService

Responsibilities:

* Payment gateway abstraction
* Provider error mapping
* Idempotent payment operations

MVP Priority:

```text
Deferred or MVP Optional
```

---

## Webhook Services

### WebhooksService

Responsibilities:

* Receive webhook event
* Validate provider
* Persist event
* Dispatch processing

### WebhookVerificationService

Responsibilities:

* Verify provider signature
* Validate timestamp where applicable
* Reject spoofed payloads

### WebhookEventService

Responsibilities:

* Idempotency check
* Event processing status
* Duplicate event handling

MVP Priority:

```text
Deferred unless payments or external integrations are included
```

---

## Admin Services

### AdminUsersService

Responsibilities:

* User search
* User status update
* Support-safe profile view
* Account suspension/reactivation

### AdminOrganizationsService

Responsibilities:

* Platform-level Organization management
* Organization onboarding support

### AdminInstitutionsService

Responsibilities:

* Platform-level Institution management
* Institution status review

### AdminAuditService

Responsibilities:

* Platform audit log search
* Security audit support

### AdminFeatureFlagsService

Responsibilities:

* Feature flag read/update
* Rollout management
* Audit feature changes

MVP Priority:

```text
MVP for minimal admin
V1 for extended admin
```

---

## Health Services

### HealthService

Responsibilities:

* Liveness check
* Readiness check
* Dependency health checks

### DependencyHealthService

Responsibilities:

* Database health
* Cache health
* Queue health
* Storage health
* Provider health where useful

MVP Priority:

```text
MVP
```

---

# Transaction Rules

Use transactions when a workflow changes multiple related records.

Examples:

* Creating an Institution with default settings.
* Creating a learner and class assignment.
* Linking a guardian to a learner.
* Submitting assessment results in bulk.
* Recording fee payment and receipt metadata.
* Accepting invitation and creating membership.
* Creating report export job and audit record.

Transaction boundaries should be owned by application services or domain services.

Avoid opening transactions in controllers.

---

# Tenant-Safe Repository Rule

Every scoped repository method must include tenant filters.

Bad:

```ts
await prisma.assessment.findUnique({
  where: { id: assessmentId },
});
```

Good:

```ts
await prisma.assessment.findFirst({
  where: {
    id: assessmentId,
    organizationId,
    institutionId,
  },
});
```

This is required even when IDs are globally unique.

---

# Audit Integration Rules

Services must create audit logs for sensitive actions.

Audit-required actions include:

```text
user.invite
membership.role.update
organization.update
institution.update
learner.update
guardian.linkLearner
teacher.assign
attendance.record.update
assessment.result.update
fee.payment.record
file.delete
ai.insight.generate
report.export
admin.user.suspend
featureFlag.update
```

Audit payloads must avoid secrets and sensitive raw content.

---

# Error Handling Rules

Services should throw application exceptions with stable error codes.

Example:

```ts
throw new AppException({
  code: 'TENANT_SCOPE_VIOLATION',
  message: 'Requested resource is outside your permitted scope.',
  statusCode: 403,
});
```

Do not expose:

* Database errors
* Stack traces
* Provider secrets
* Tenant boundary details
* Sensitive learner data
* Raw AI prompts

---

# External Provider Rules

External provider services must:

* Use timeouts.
* Map provider errors to internal error codes.
* Avoid leaking provider error bodies directly to clients.
* Use retries only for safe operations.
* Use idempotency for payment, webhook, and costly AI workflows.
* Log safe provider metadata.
* Hide credentials and secrets.

---

# Idempotency Rules

Idempotency is required for:

* Payment creation
* Subscription changes
* Webhook processing
* File processing jobs
* AI generation where duplicate cost matters
* OCR jobs
* Report exports

Service methods handling idempotent workflows must accept or resolve an idempotency key.

---

# Event Rules

Services may emit domain events for asynchronous processing.

Examples:

```text
learner.created
guardian.linkedToLearner
teacher.assigned
attendance.submitted
assessment.resultsSubmitted
announcement.published
file.uploaded
ai.insight.generated
report.exportRequested
payment.confirmed
```

Events must include tenant metadata:

```text
organizationId
institutionId
actorUserId
resourceType
resourceId
```

where applicable.

---

# Background Job Rules

When a service creates a background job, it must persist:

```text
requestedByUserId
organizationId
institutionId
resourceType
resourceId
requiredPermission
jobType
```

The worker must revalidate scope before writing results.

Do not store secrets or large sensitive payloads directly in queue messages.

---

# AI Service Rules

AI-related services must:

* Validate authorization before retrieving context.
* Minimize context.
* Apply tenant filters to RAG/vector retrieval.
* Check entitlements and usage limits.
* Avoid sending unnecessary personal data to providers.
* Validate output safety.
* Store AI results according to retention policy.
* Audit sensitive AI actions.

---

# File Service Rules

File services must:

* Validate file type, MIME, extension, and size.
* Store tenant metadata.
* Validate access before download.
* Generate short-lived signed URLs.
* Avoid exposing raw storage keys.
* Soft-delete metadata before permanent purge.
* Scan files where practical.

---

# Service Testing Standards

Each service must have unit tests for:

* Success path
* Validation failure
* Missing permission
* Organization mismatch
* Institution mismatch
* Ownership failure
* Assignment failure
* Not found
* Conflict
* Audit creation where required
* Transaction rollback where applicable

Integration tests are required for:

* Authentication
* Authorization
* Tenant scope enforcement
* Learner access
* Guardian access
* Teacher access
* Attendance update
* Assessment result update
* File access
* AI context retrieval
* Report exports
* Admin actions

---

# Service Review Checklist

Before approving a service, confirm:

* Service has one clear responsibility.
* Service uses Organization and Institution scope correctly.
* No `schoolId` is introduced.
* Backend terminology uses Learner and Guardian.
* Method signatures include scope where needed.
* Authorization is enforced server-side.
* Resource policies are used for ownership or assignment.
* Repositories use tenant-safe queries.
* DTOs prevent mass assignment.
* Errors use stable error codes.
* Sensitive actions create audit logs.
* External provider errors are safely mapped.
* AI/OCR workflows are privacy-safe.
* Background jobs preserve tenant context.
* Unit tests cover failure paths.
* Integration tests cover access boundaries.

---

# Related Documents

* docs/09-Backend/MODULES.md
* docs/09-Backend/AUTHENTICATION.md
* docs/09-Backend/BACKGROUND_JOBS.md
* docs/06-API/API_GUIDELINES.md
* docs/06-API/ENDPOINTS.md
* docs/06-API/ERROR_CODES.md
* docs/11-Security/SECURITY.md
* docs/11-Security/RBAC.md
* docs/11-Security/PRIVACY.md
* docs/05-Database/DATABASE_SCHEMA.md
* docs/04-Architecture/CODING_STANDARDS.md
