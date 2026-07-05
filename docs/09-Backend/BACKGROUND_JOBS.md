# Background Jobs

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Backend Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the background job and asynchronous processing standards for the Acadedx backend.

Background jobs are required for long-running, costly, retryable, or provider-dependent workflows.

Acadedx is a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

Background jobs must preserve Organization and Institution context and must use `institutionId`, not `schoolId`.

---

# Background Job Goals

Background job design must ensure:

1. Long-running work does not block API requests.
2. AI, OCR, reports, notifications, and file processing are reliable.
3. Jobs preserve Organization and Institution scope.
4. Jobs never bypass authentication, authorization, privacy, or tenant isolation.
5. Job status is observable by authorized users.
6. Failed jobs are retryable where safe.
7. Duplicate jobs are prevented where needed.
8. Sensitive job actions are audited.
9. Provider failures are handled safely.
10. MVP implementation remains simple but future-ready.

---

# When to Use Background Jobs

Use background jobs for workflows that are:

* Long-running
* Provider-dependent
* Costly
* Retryable
* Batch-oriented
* File-processing-heavy
* AI-processing-heavy
* Notification-heavy
* Export-heavy

Examples:

```text
OCR extraction
AI learner insight generation
AI assessment feedback generation
AI communication draft generation
Report export generation
PDF generation
Bulk learner import
Bulk guardian import
Bulk result upload
File safety scan
Email delivery
SMS delivery
Notification fanout
Payment webhook processing
Data cleanup
Scheduled reminders
Usage counter aggregation
Audit archive
```

Do not use background jobs for simple synchronous CRUD operations unless there is a clear reason.

---

# Recommended Queue Technology

Recommended MVP queue approach:

```text
BullMQ + Redis
```

Reasons:

* Works well with NestJS.
* Simple enough for MVP.
* Supports retries, delays, priorities, and job status.
* Can be scaled with workers later.
* Familiar operational model.

Alternative future options:

```text
AWS SQS
Google Pub/Sub
Azure Service Bus
RabbitMQ
Kafka
Temporal
```

Do not introduce heavyweight orchestration such as Temporal for the MVP unless workflow complexity justifies it.

---

# Backend Modules Involved

Background jobs should be implemented through:

```text
JobsModule
```

Related modules:

```text
FilesModule
AiModule
OcrModule
ReportsModule
NotificationsModule
CommunicationModule
AuditModule
EntitlementsModule
BillingModule
WebhooksModule
```

---

# Recommended JobsModule Structure

```text
src/modules/jobs/
├── controllers/
│   └── jobs.controller.ts
├── services/
│   ├── jobs.service.ts
│   ├── queue.service.ts
│   └── job-status.service.ts
├── processors/
│   ├── ocr-job.processor.ts
│   ├── ai-generation.processor.ts
│   ├── report-export.processor.ts
│   ├── notification-delivery.processor.ts
│   ├── file-scan.processor.ts
│   └── cleanup.processor.ts
├── dto/
│   ├── get-job.dto.ts
│   └── job-status-query.dto.ts
├── types/
│   ├── job-context.ts
│   ├── job-result.ts
│   └── job-type.ts
└── jobs.module.ts
```

---

# Job Lifecycle

Standard lifecycle:

```text
QUEUED
PROCESSING
SUCCEEDED
FAILED
CANCELLED
EXPIRED
```

Optional future statuses:

```text
RETRYING
WAITING_FOR_CONFIRMATION
PARTIALLY_SUCCEEDED
```

---

# Job State Meaning

| Status       | Meaning                                                    |
| ------------ | ---------------------------------------------------------- |
| `QUEUED`     | Job has been accepted and is waiting for worker processing |
| `PROCESSING` | Worker has started processing                              |
| `SUCCEEDED`  | Job completed successfully                                 |
| `FAILED`     | Job failed and no more retries are pending                 |
| `CANCELLED`  | Job was intentionally cancelled                            |
| `EXPIRED`    | Job expired before completion or result retrieval          |

---

# Job Creation Pattern

API should return quickly with a job reference.

Example:

```http
POST /api/v1/organizations/{organizationId}/institutions/{institutionId}/ai/learner-insights
```

Response:

```json
{
  "success": true,
  "message": "Job queued successfully.",
  "data": {
    "jobId": "job_123",
    "status": "QUEUED"
  }
}
```

Client checks status:

```http
GET /api/v1/jobs/{jobId}
```

or uses a resource-specific status endpoint.

---

# Job Status Endpoint

```http
GET /api/v1/jobs/{jobId}
```

Authentication:

```text
Required
```

Authorization:

```text
User must have access to the job's Organization and Institution context.
```

Response:

```json
{
  "success": true,
  "data": {
    "jobId": "job_123",
    "jobType": "AI_LEARNER_INSIGHT",
    "status": "SUCCEEDED",
    "progress": 100,
    "resourceType": "LearnerInsight",
    "resourceId": "insight_123",
    "createdAt": "2026-07-05T10:30:00.000Z",
    "updatedAt": "2026-07-05T10:31:12.000Z"
  }
}
```

Do not expose internal queue payloads.

---

# Job Context

Every tenant-scoped job must persist context.

Required fields:

```text
jobId
jobType
status
requestedByUserId
organizationId
institutionId
resourceType
resourceId
requiredPermission
idempotencyKey
correlationId
requestId
createdAt
updatedAt
startedAt
completedAt
failedAt
expiresAt
```

Optional fields:

```text
progress
attemptCount
maxAttempts
lastErrorCode
lastErrorMessage
resultResourceType
resultResourceId
provider
providerRequestId
metadata
```

---

# Job Context Type

Recommended TypeScript shape:

```ts
export interface JobContext {
  jobId: string;
  jobType: JobType;
  requestedByUserId: string;
  organizationId?: string;
  institutionId?: string;
  resourceType?: string;
  resourceId?: string;
  requiredPermission?: string;
  idempotencyKey?: string;
  correlationId?: string;
  requestId?: string;
  metadata?: Record<string, unknown>;
}
```

For Institution-scoped jobs, both `organizationId` and `institutionId` are required.

---

# Tenant Scope Requirements

Background jobs must never run without tenant context when processing tenant data.

Required rule:

```text
If a job reads or writes Institution data, it must include organizationId and institutionId.
```

Bad:

```ts
queue.add('generate-report', {
  reportType: 'ATTENDANCE',
  classId,
});
```

Good:

```ts
queue.add('generate-report', {
  requestedByUserId,
  organizationId,
  institutionId,
  reportType: 'ATTENDANCE',
  classId,
});
```

---

# Authorization Requirements

Authorization must happen at two stages.

## 1. Job Creation Time

Before queuing the job:

* Validate authenticated user.
* Validate active membership.
* Validate Organization scope.
* Validate Institution scope.
* Validate required permission.
* Validate resource ownership or assignment.
* Validate entitlement where needed.
* Validate usage limit where needed.

## 2. Job Execution Time

Before writing results:

* Validate job has valid tenant context.
* Revalidate target resource still belongs to Organization and Institution.
* Revalidate resource status where required.
* Check cancellation status.
* Apply safe write rules.

Do not store user access tokens in job payloads.

---

# Permission Rules

Every job type must define a required permission.

Examples:

| Job Type                        | Required Permission               |
| ------------------------------- | --------------------------------- |
| `OCR_EXTRACT_TEXT`              | `ai.ocr.create`                   |
| `AI_LEARNER_INSIGHT`            | `ai.insight.generate`             |
| `AI_ASSESSMENT_FEEDBACK`        | `ai.assessmentFeedback.generate`  |
| `AI_COMMUNICATION_DRAFT`        | `ai.communicationDraft.generate`  |
| `REPORT_EXPORT`                 | specific report export permission |
| `FILE_SAFETY_SCAN`              | system internal                   |
| `NOTIFICATION_DELIVERY`         | system internal                   |
| `BULK_LEARNER_IMPORT`           | `learner.create`                  |
| `BULK_ASSESSMENT_RESULT_IMPORT` | `assessment.result.create`        |
| `PAYMENT_WEBHOOK_PROCESSING`    | system internal                   |

System-internal jobs must still include tenant context where applicable.

---

# Idempotency

Idempotency is required for jobs that may be retried or duplicated.

Required for:

```text
AI generation
OCR extraction
Report export
Bulk import
Payment webhook processing
Notification delivery
File processing
```

Idempotency key sources:

* `Idempotency-Key` request header
* Provider event ID
* Resource-based natural key
* Generated deterministic key for safe retries

Example deterministic key:

```text
AI_LEARNER_INSIGHT:{organizationId}:{institutionId}:{learnerId}:{academicYearId}:{insightType}
```

Idempotency behavior:

* If identical job already succeeded, return existing result.
* If identical job is processing, return existing job ID.
* If identical job failed, allow retry based on policy.
* If payload differs for same key, return conflict.

Relevant error codes:

```text
JOB_ALREADY_EXISTS
JOB_IN_PROGRESS
RESOURCE_CONFLICT
PAYMENT_DUPLICATE_REQUEST
WEBHOOK_EVENT_DUPLICATE
```

---

# Retry Strategy

Jobs should retry only when failure is likely transient.

Retryable examples:

```text
AI_PROVIDER_TIMEOUT
AI_PROVIDER_UNAVAILABLE
OCR_PROVIDER_TIMEOUT
OCR_PROVIDER_UNAVAILABLE
EMAIL_PROVIDER_ERROR
SMS_PROVIDER_ERROR
STORAGE_PROVIDER_ERROR
PAYMENT_GATEWAY_TIMEOUT
PROVIDER_TIMEOUT
SERVICE_UNAVAILABLE
```

Non-retryable examples:

```text
VALIDATION_ERROR
AUTH_FORBIDDEN
TENANT_SCOPE_VIOLATION
ENTITLEMENT_REQUIRED
USAGE_LIMIT_EXCEEDED
FILE_INVALID_TYPE
OCR_FILE_UNSUPPORTED
AI_OUTPUT_UNSAFE
```

Recommended retry pattern:

```text
Exponential backoff with jitter
```

Suggested MVP defaults:

```text
maxAttempts: 3
initialDelay: 30 seconds
maxDelay: 10 minutes
```

Provider-specific jobs may override defaults.

---

# Timeout Rules

Every worker must define timeouts.

Suggested limits:

| Job Type              | Suggested Timeout |
| --------------------- | ----------------: |
| OCR extraction        |    2 to 5 minutes |
| AI generation         |    1 to 3 minutes |
| Report export         |   5 to 15 minutes |
| File scan             |    1 to 3 minutes |
| Notification delivery |  30 to 60 seconds |
| Bulk import           |  10 to 30 minutes |

Timeouts should be configurable.

Timeout failures should map to relevant error codes.

---

# Progress Reporting

Jobs may report progress when useful.

Examples:

```text
Bulk import
Report export
OCR processing
Large notification fanout
```

Progress field:

```text
0 to 100
```

For jobs where progress cannot be estimated, return status only.

---

# Cancellation

Cancellation may be supported for long-running or queued jobs.

Cancellation rules:

* User must have access to job.
* Job must be cancellable.
* Job must not already be completed.
* Worker must periodically check cancellation state.
* Cancellation must be audited for sensitive jobs.

MVP status:

```text
V1 or Deferred
```

---

# Job Results

Job result should reference created resources rather than embedding large payloads.

Good:

```json
{
  "jobId": "job_123",
  "status": "SUCCEEDED",
  "resultResourceType": "ReportFile",
  "resultResourceId": "file_123"
}
```

Avoid:

```json
{
  "jobId": "job_123",
  "status": "SUCCEEDED",
  "result": {
    "largePdfBase64": "..."
  }
}
```

Large outputs should be stored as files or domain resources.

---

# Job Error Handling

Failed job response:

```json
{
  "success": true,
  "data": {
    "jobId": "job_123",
    "status": "FAILED",
    "lastErrorCode": "AI_PROVIDER_TIMEOUT",
    "lastErrorMessage": "AI provider request timed out."
  }
}
```

Do not expose:

* Stack traces
* Provider secrets
* Raw provider responses
* Internal queue payloads
* Raw AI prompts
* Raw OCR text
* Storage paths
* Tenant boundary details

---

# Queue Naming Standard

Use clear queue names.

Recommended queues:

```text
ai-generation
ocr-processing
report-export
notification-delivery
file-processing
bulk-import
webhook-processing
maintenance
```

Use separate queues for workloads with different scaling or risk profiles.

Do not mix high-cost AI jobs with simple notification jobs in the same queue.

---

# Job Type Naming Standard

Use `UPPER_SNAKE_CASE`.

Examples:

```text
AI_LEARNER_INSIGHT
AI_ASSESSMENT_FEEDBACK
AI_COMMUNICATION_DRAFT
OCR_EXTRACT_TEXT
REPORT_EXPORT
FILE_SAFETY_SCAN
NOTIFICATION_DELIVERY
EMAIL_DELIVERY
SMS_DELIVERY
BULK_LEARNER_IMPORT
BULK_GUARDIAN_IMPORT
BULK_ASSESSMENT_RESULT_IMPORT
PAYMENT_WEBHOOK_PROCESSING
USAGE_AGGREGATION
DATA_RETENTION_PURGE
```

---

# Worker Design

Workers should:

* Be stateless where possible.
* Load job metadata from trusted storage.
* Validate tenant context before processing.
* Use service methods rather than duplicating domain logic.
* Use provider wrappers for external calls.
* Map errors to stable error codes.
* Update job status consistently.
* Avoid logging sensitive payloads.
* Support graceful shutdown.

Workers should not:

* Trust raw queue payload blindly.
* Store access tokens.
* Bypass service authorization logic.
* Query tenant data without scope filters.
* Return large results directly through the queue.

---

# Job Persistence

Do not rely only on queue storage for business-visible jobs.

Persist job records in the database for:

* User-visible job status
* Auditability
* Retry tracking
* Result references
* Tenant-safe access
* Debugging
* Support workflows

Recommended `jobs` table fields:

```text
id
jobType
status
requestedByUserId
organizationId
institutionId
resourceType
resourceId
requiredPermission
idempotencyKey
correlationId
requestId
progress
attemptCount
maxAttempts
lastErrorCode
lastErrorMessage
resultResourceType
resultResourceId
provider
providerRequestId
metadata
createdAt
updatedAt
startedAt
completedAt
failedAt
cancelledAt
expiresAt
```

---

# Sensitive Payload Rules

Avoid placing sensitive data directly in queue payloads.

Bad queue payload:

```json
{
  "learnerName": "Riya Sharma",
  "assessmentResults": [
    {
      "subject": "Math",
      "marks": 12
    }
  ],
  "rawPrompt": "..."
}
```

Better queue payload:

```json
{
  "jobId": "job_123",
  "organizationId": "org_123",
  "institutionId": "inst_123",
  "resourceType": "Learner",
  "resourceId": "lrn_123"
}
```

The worker should fetch authorized data using tenant-safe services.

---

# AI Job Rules

AI jobs must follow AI privacy and security rules.

Before queuing AI jobs:

* Validate permission.
* Validate Organization and Institution scope.
* Validate resource access.
* Validate entitlement.
* Validate usage limit.
* Validate input size.
* Create job record.
* Record usage reservation if required.

During execution:

* Retrieve tenant-safe context.
* Minimize data sent to provider.
* Render approved prompt template.
* Call provider through AiProviderService.
* Validate output safety.
* Persist AI result.
* Update usage counters.
* Audit sensitive actions.

AI jobs must not use data from another Organization or Institution.

---

# OCR Job Rules

OCR jobs must:

* Validate file ownership.
* Validate file access.
* Validate file type.
* Validate file size.
* Validate Organization and Institution scope.
* Validate entitlement and usage limits.
* Store OCR output as sensitive data.
* Avoid logging OCR text.
* Support manual confirmation where needed.

OCR extracted text inherits the source file's access rules.

---

# Report Export Job Rules

Report export jobs must:

* Validate report permission.
* Validate Organization and Institution scope.
* Validate filters.
* Apply row-level access control.
* Generate export file.
* Store file metadata.
* Generate signed URL only after authorization.
* Audit export request and completion.
* Expire exported files.

Reports must not include records outside requested tenant scope.

---

# Notification Job Rules

Notification jobs must:

* Validate recipient eligibility before enqueueing or before delivery.
* Avoid exposing unrelated recipients.
* Respect notification preferences.
* Use channel-specific templates.
* Avoid sensitive details in notification preview text.
* Track delivery status.
* Retry transient provider failures.

For learner or guardian notifications, include minimal sensitive data.

---

# File Processing Job Rules

File processing jobs may include:

```text
Safety scan
Thumbnail generation
PDF text extraction
Metadata extraction
Virus scan
Storage cleanup
```

Rules:

* Validate file belongs to Organization and Institution where applicable.
* Do not expose raw storage paths.
* Do not process unsupported file types.
* Mark unsafe files as blocked.
* Prevent signed URL generation for blocked files.

---

# Bulk Import Job Rules

Bulk import jobs may be used for:

```text
Learners
Guardians
Teachers
Academic structure
Assessment results
Fee assignments
```

Rules:

* Validate file access.
* Validate import permission.
* Validate Organization and Institution scope.
* Validate rows before committing.
* Support partial failure reporting where appropriate.
* Produce import summary.
* Do not leak sensitive row data in logs.
* Audit import completion.

MVP status:

```text
V1 or Deferred
```

---

# Webhook Processing Job Rules

Webhook handlers should validate and persist events quickly, then process asynchronously.

Rules:

* Verify provider signature before queueing.
* Use provider event ID for idempotency.
* Store event metadata.
* Avoid duplicate processing.
* Process heavy logic in job worker.
* Return provider-compatible response quickly.

Examples:

```text
Payment success
Payment failed
Refund processed
Email delivery status
SMS delivery status
```

---

# Scheduled Jobs

Scheduled jobs may be needed for:

```text
Session cleanup
Expired invitation cleanup
Expired password reset cleanup
Expired email verification cleanup
Expired export cleanup
Usage counter reset
Retention policy processing
Notification reminders
Academic year reminders
Audit archive
```

MVP scheduled jobs:

```text
Expired token cleanup
Expired invitation cleanup
Expired export cleanup
Basic usage counter reset, if entitlements are enabled
```

Scheduled jobs must be tenant-aware where they process tenant data.

---

# Data Retention Jobs

Data retention jobs must follow privacy policy.

Examples:

```text
Purge deleted files after retention period
Expire report exports
Archive old audit logs
Delete expired reset tokens
Delete expired verification tokens
Apply AI conversation retention
Apply OCR retention
```

Data retention jobs must be auditable if they delete or anonymize sensitive data.

---

# Audit Requirements

Audit logs are required for sensitive jobs.

Audit-required job events:

```text
AI insight generation
OCR extraction on sensitive file
Report export
Bulk import
Fee import
Payment webhook processing
File deletion or purge
Data retention purge
Admin-triggered job retry
Admin-triggered job cancellation
```

Audit record should include:

```text
jobId
jobType
actorUserId
organizationId
institutionId
resourceType
resourceId
action
status
requestId
createdAt
```

---

# Logging Requirements

Job logs should include:

```text
jobId
jobType
status
organizationId
institutionId
attemptCount
durationMs
errorCode
provider
providerRequestId
requestId
```

Job logs must not include:

```text
passwords
tokens
secrets
raw AI prompts
raw AI responses containing personal data
OCR text
full learner records
full guardian records
payment secrets
signed URLs
storage keys
```

---

# Monitoring and Metrics

Production job monitoring should include:

* Queue depth
* Job duration
* Job success rate
* Job failure rate
* Retry count
* Dead-letter count
* Provider timeout rate
* AI job cost/usage
* OCR failure rate
* Notification delivery failure rate
* Report export volume

Alert on:

* High failure rate
* Stuck jobs
* Queue backlog
* Dead-letter spike
* Provider outage
* Excessive AI cost
* Repeated tenant scope violations
* Repeated webhook signature failures

---

# Dead Letter Handling

Jobs that fail after all retries should be marked:

```text
FAILED
```

and optionally moved to a dead-letter queue.

Dead-letter records should include:

```text
jobId
jobType
lastErrorCode
attemptCount
failedAt
```

Admin retry should be permission-controlled and audited.

---

# Concurrency Rules

Use concurrency limits by queue and job type.

Examples:

| Queue                   | Suggested MVP Concurrency |
| ----------------------- | ------------------------: |
| `notification-delivery` |                      5-20 |
| `ocr-processing`        |                       2-5 |
| `ai-generation`         |                       2-5 |
| `report-export`         |                       1-3 |
| `bulk-import`           |                       1-2 |

Concurrency should be configurable per environment.

AI and OCR concurrency should respect provider limits and cost controls.

---

# Priority Rules

Some jobs may require priority.

Suggested priorities:

| Priority | Examples                                               |
| -------- | ------------------------------------------------------ |
| High     | Password reset email, invitation email, critical alert |
| Normal   | AI generation, OCR extraction, regular notification    |
| Low      | Reports, exports, cleanup, analytics aggregation       |

Do not allow users to set arbitrary priority.

Priority must be assigned by backend policy.

---

# Job Security Checklist

Every job must answer:

* Who requested this job?
* Which Organization does it belong to?
* Which Institution does it belong to?
* What resource does it operate on?
* What permission was required?
* Was authorization checked before queueing?
* Is the worker tenant-safe?
* Is the result access-controlled?
* Is the job retry-safe?
* Is idempotency required?
* Is audit logging required?
* Does the job avoid sensitive logs?

---

# API Error Codes

Relevant job error codes:

```text
JOB_NOT_FOUND
JOB_ALREADY_EXISTS
JOB_IN_PROGRESS
JOB_FAILED
JOB_CANCELLED
JOB_TIMEOUT
JOB_RETRY_LIMIT_EXCEEDED
JOB_RESULT_NOT_READY
JOB_QUEUE_UNAVAILABLE

AI_PROVIDER_ERROR
AI_PROVIDER_UNAVAILABLE
AI_PROVIDER_TIMEOUT

OCR_PROVIDER_ERROR
OCR_PROVIDER_UNAVAILABLE
OCR_PROVIDER_TIMEOUT

PROVIDER_ERROR
PROVIDER_UNAVAILABLE
PROVIDER_TIMEOUT

TENANT_SCOPE_VIOLATION
ENTITLEMENT_REQUIRED
USAGE_LIMIT_EXCEEDED
```

Definitions are maintained in:

```text
docs/06-API/ERROR_CODES.md
```

---

# MVP Background Job Requirements

The MVP should implement:

```text
JobsModule
JobsService
QueueService
JobStatusService
Persistent jobs table
GET /api/v1/jobs/{jobId}
Tenant-aware job context
Basic retry handling
Basic failure handling
Audit hooks for sensitive jobs
Safe job logging
Scheduled cleanup for expired tokens and exports
Notification delivery jobs, if notifications are included
Report export jobs, if exports are included
AI/OCR jobs, if AI/OCR is included
```

Minimum required queues:

```text
notification-delivery
report-export
maintenance
```

Optional MVP queues:

```text
ai-generation
ocr-processing
file-processing
bulk-import
webhook-processing
```

---

# Deferred Capabilities

The following may be deferred beyond MVP:

* Job cancellation
* User-visible progress for every job
* Dead-letter UI
* Admin retry UI
* Workflow orchestration engine
* Distributed workers across regions
* Tenant-specific queue isolation
* Advanced job priority management
* Full event sourcing
* Complex academic year rollover automation
* Advanced data retention automation

Do not design the MVP in a way that blocks these future capabilities.

---

# Testing Requirements

Background job tests must cover:

## Job Creation

* Successful job creation
* Missing authentication
* Missing permission
* Organization mismatch
* Institution mismatch
* Entitlement failure
* Usage limit failure
* Duplicate idempotency key
* Invalid resource

## Job Execution

* Successful processing
* Missing tenant context
* Resource no longer exists
* Resource moved or archived
* Provider timeout
* Provider unavailable
* Non-retryable failure
* Retryable failure
* Max retry exceeded

## Job Status

* Authorized user can view job
* Unauthorized user cannot view job
* Cross-Institution access denied
* Cross-Organization access denied
* Failed job shows safe error
* Succeeded job returns result reference

## Sensitive Jobs

* AI job does not retrieve cross-tenant context
* OCR job does not expose extracted text in logs
* Report export respects filters and scope
* Notification job sends only to eligible recipients
* Webhook event is idempotent

---

# Implementation Review Checklist

Before approving background job implementation, confirm:

* Job records are persisted outside queue storage.
* Job context includes Organization and Institution where needed.
* Job creation performs authorization checks.
* Workers do not store or use user access tokens.
* Workers revalidate tenant context before writing results.
* Idempotency is implemented for duplicate-prone workflows.
* Retry policy distinguishes retryable and non-retryable failures.
* Provider errors are mapped to stable error codes.
* Logs do not contain sensitive payloads.
* Audit logs exist for sensitive jobs.
* Job status endpoint enforces access control.
* Queue names and job types follow naming standards.
* AI/OCR/report jobs are privacy-safe.
* Scheduled jobs do not process tenant data without scope rules.
* Tests cover negative and cross-tenant cases.

---

# Related Documents

* docs/09-Backend/MODULES.md
* docs/09-Backend/SERVICES.md
* docs/09-Backend/AUTHENTICATION.md
* docs/06-API/API_GUIDELINES.md
* docs/06-API/ENDPOINTS.md
* docs/06-API/ERROR_CODES.md
* docs/11-Security/SECURITY.md
* docs/11-Security/RBAC.md
* docs/11-Security/PRIVACY.md
* docs/07-AI/AI_ENGINE.md
* docs/07-AI/OCR.md
* docs/10-Design-System/DESIGN_SYSTEM.md
