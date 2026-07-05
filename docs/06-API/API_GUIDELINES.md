# Acadedx API Guidelines

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the API design standards for Acadedx.

All APIs must be consistent, secure, versioned, documented, testable, and designed to support the future-ready Acadedx platform model.

These guidelines apply to:

- Web application
- Admin application
- Mobile application
- Desktop application
- Backend services
- AI workflows
- Payment workflows
- Notification workflows
- Future public APIs

---

# API Design Philosophy

Acadedx APIs must support a school-focused MVP while remaining ready for organizations and multiple institution types.

The platform model is:

```text
Platform
    ↓
Organization
    ↓
Institution
    ↓
Academic Structure
    ↓
Teachers / Parents / Students
    ↓
AI Learning Engine
```

API design must not assume that every institution is always a school.

Use:

```text
/institutions
```

Do not use:

```text
/schools
```

unless the endpoint is explicitly school-only, which should be rare.

---

# API Style

Acadedx uses REST-first APIs.

Base path:

```text
/api/v1
```

Example:

```text
/api/v1/auth/login
/api/v1/organizations
/api/v1/institutions
/api/v1/homework
```

GraphQL is not required for MVP.

Public APIs for partners or enterprise customers are future scope.

---

# API Versioning

API versioning is mandatory.

Current version:

```text
v1
```

Example:

```text
/api/v1/homework
```

Do not introduce unversioned APIs.

Bad:

```text
/api/homework
```

Good:

```text
/api/v1/homework
```

---

# Resource Naming

Use plural nouns for resources.

Good:

```text
/users
/organizations
/institutions
/homework
/quizzes
/worksheets
/notifications
/subscriptions
/payments
```

Bad:

```text
/getUsers
/createInstitution
/deleteQuiz
```

Actions may be used only when the operation is not naturally represented by CRUD.

Examples:

```text
/auth/login
/auth/logout
/homework/{id}/solve
/quizzes/{id}/submit
/subscriptions/{id}/cancel
/payments/{id}/refund
```

---

# Organization and Institution API Rules

## Organization

Use Organization for business ownership, billing, and enterprise context.

Example endpoints:

```text
GET /api/v1/organizations
POST /api/v1/organizations
GET /api/v1/organizations/{organizationId}
PATCH /api/v1/organizations/{organizationId}
```

---

## Institution

Use Institution for the education unit where learning happens.

Example endpoints:

```text
GET /api/v1/institutions
POST /api/v1/institutions
GET /api/v1/institutions/{institutionId}
PATCH /api/v1/institutions/{institutionId}
```

Institution types include:

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

---

# MVP Institution Constraint

In MVP:

```text
One Organization → One Institution
```

This constraint must be enforced by business logic.

The API should still use organization and institution identifiers in a future-ready way.

Do not design APIs that permanently prevent multiple institutions under one organization.

---

# HTTP Methods

| Method | Purpose |
|---|---|
| GET | Read resources |
| POST | Create resources or trigger non-idempotent actions |
| PUT | Replace full resource |
| PATCH | Partially update resource |
| DELETE | Soft delete or remove resource |

---

# Standard Response Format

All APIs must return a consistent response format.

## Success Response

```json
{
  "success": true,
  "message": "Request completed successfully.",
  "data": {}
}
```

## List Response

```json
{
  "success": true,
  "message": "Records retrieved successfully.",
  "data": [],
  "pagination": {
    "limit": 20,
    "nextCursor": "eyJpZCI6IjEyMyJ9",
    "hasMore": true
  }
}
```

## Error Response

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request.",
    "details": []
  }
}
```

---

# HTTP Status Codes

| Code | Meaning |
|---|---|
| 200 | Successful read or update |
| 201 | Resource created |
| 202 | Accepted for asynchronous processing |
| 204 | Successful request with no body |
| 400 | Bad request |
| 401 | Not authenticated |
| 403 | Not authorized |
| 404 | Resource not found |
| 409 | Conflict |
| 422 | Validation error |
| 429 | Rate limit exceeded |
| 500 | Internal server error |
| 503 | External provider or service unavailable |

---

# Error Code Standards

Error codes must be stable and machine-readable.

Examples:

```text
VALIDATION_ERROR
AUTH_INVALID_CREDENTIALS
AUTH_INVALID_TOKEN
AUTH_TOKEN_EXPIRED
AUTH_FORBIDDEN
RESOURCE_NOT_FOUND
TENANT_SCOPE_VIOLATION
ENTITLEMENT_REQUIRED
USAGE_LIMIT_EXCEEDED
AI_PROVIDER_ERROR
OCR_FAILED
PAYMENT_FAILED
WEBHOOK_SIGNATURE_INVALID
RATE_LIMIT_EXCEEDED
```

Error messages may change.

Error codes should remain stable.

---

# Authentication

Protected APIs must use:

```text
Authorization: Bearer <access_token>
```

Authentication must be enforced server-side.

Frontend authentication checks are not trusted.

---

# Authorization

Authorization must include:

- Role check
- Permission check
- Organization scope
- Institution scope
- Ownership check
- Entitlement check where applicable

Examples:

A student may access:

```text
GET /api/v1/homework/{id}
```

only if the homework belongs to that student.

A teacher may access student data only if assigned to that student or class.

An institution administrator may access only their institution.

An organization administrator may access only their organization.

Platform administrator access must be audited.

---

# Request Context

Every authenticated request should resolve a context object similar to:

```json
{
  "requestId": "req_123",
  "userId": "uuid",
  "roles": ["STUDENT"],
  "organizationId": "uuid",
  "institutionId": "uuid",
  "permissions": ["homework.solve"],
  "entitlements": ["homework.solve"]
}
```

This context should be available to guards, services, logs, audit records, and analytics events.

---

# Tenant Scoping

Tenant scoping is mandatory.

## Organization-Scoped Requests

Must validate:

```text
organizationId
```

## Institution-Scoped Requests

Must validate:

```text
institutionId
```

## Student-Owned Requests

Must validate:

```text
studentUserId
```

## Parent Requests

Must validate relationship through:

```text
ParentStudent
```

## Teacher Requests

Must validate assignment or institution permission.

---

# Route Parameter Naming

Use explicit identifiers.

Good:

```text
/organizations/{organizationId}
/institutions/{institutionId}
/homework/{homeworkId}
/quizzes/{quizId}
```

Avoid ambiguous route params.

Bad:

```text
/items/{id}
/data/{id}
```

---

# Query Parameters

Use camelCase.

Examples:

```text
?pageSize=20
?cursor=abc123
?status=ACTIVE
?institutionId=uuid
?subjectId=uuid
?search=algebra
```

---

# Pagination

Use cursor-based pagination for large lists.

Request:

```text
GET /api/v1/homework?limit=20&cursor=abc123
```

Response:

```json
{
  "success": true,
  "data": [],
  "pagination": {
    "limit": 20,
    "nextCursor": "xyz987",
    "hasMore": true
  }
}
```

Default limit:

```text
20
```

Maximum limit:

```text
100
```

Offset pagination may be used only for small admin lists where appropriate.

---

# Sorting

Use `sort` query parameter.

Ascending:

```text
?sort=createdAt
```

Descending:

```text
?sort=-createdAt
```

Multiple sort fields may be supported only where needed.

---

# Filtering

Use query parameters.

Examples:

```text
GET /api/v1/users?role=STUDENT
GET /api/v1/homework?status=COMPLETED
GET /api/v1/institutions?type=SCHOOL
GET /api/v1/quizzes?subjectId=uuid
```

Filters must be validated.

Invalid filters should return validation error.

---

# Search

Use `search` query parameter for simple search.

Examples:

```text
GET /api/v1/notes?search=photosynthesis
GET /api/v1/admin/users?search=dhruv
GET /api/v1/institutions?search=academy
```

MVP search may use PostgreSQL search.

Advanced search is future scope.

---

# Idempotency

Idempotency is required for operations that may be retried or charged.

Required for:

- Payment creation
- Subscription changes
- AI-intensive generation where duplicate cost matters
- Webhook processing
- File processing jobs

Header:

```text
Idempotency-Key: <uuid>
```

Duplicate requests with the same idempotency key should return the original result where safe.

---

# Rate Limiting

Rate limits must protect expensive and sensitive APIs.

Suggested MVP limits:

| API Category | Suggested Limit |
|---|---|
| Login | 10 attempts per 15 minutes |
| Password reset | 5 attempts per hour |
| AI Tutor | Plan-specific |
| Homework solving | Plan-specific |
| OCR | Plan-specific |
| Quiz generation | Plan-specific |
| Worksheet generation | Plan-specific |
| Admin APIs | Role-specific |

Rate limit responses must use:

```text
429 Too Many Requests
```

---

# Entitlement Validation

Any premium, limited, or AI-costly feature must validate entitlements.

Examples:

```text
homework.solve
ocr.extract
ai_tutor.chat
notes.generate
quiz.generate
worksheets.generate
study_planner.generate
analytics.advanced
institution.dashboard
```

Do not check plan names directly.

Bad:

```text
if plan == STUDENT_PRO
```

Good:

```text
canUseFeature("homework.solve")
```

---

# Feature Flags

Feature flags may control rollout.

Feature flags must not replace:

- Authentication
- Authorization
- Entitlements
- Tenant scoping

A feature flag only controls availability.

It does not grant permission.

---

# File Upload APIs

File upload APIs must validate:

- File size
- MIME type
- File extension
- User permission
- Entitlement
- Storage quota
- Virus or safety scan where practical

Supported MVP file types:

```text
JPG
JPEG
PNG
WEBP
PDF
```

Example:

```text
POST /api/v1/files/upload
```

Response:

```json
{
  "success": true,
  "message": "File uploaded successfully.",
  "data": {
    "fileId": "uuid",
    "fileName": "homework.png",
    "mimeType": "image/png",
    "sizeBytes": 102400
  }
}
```

---

# Asynchronous Processing

Long-running tasks should use asynchronous processing.

Examples:

- OCR jobs
- Homework solving
- Notes generation
- Quiz generation
- Worksheet generation
- Report generation
- Email delivery

Pattern:

```text
POST /api/v1/homework/{homeworkId}/solve
```

Returns:

```json
{
  "success": true,
  "message": "Homework processing started.",
  "data": {
    "jobId": "uuid",
    "status": "PROCESSING"
  }
}
```

Status endpoint:

```text
GET /api/v1/jobs/{jobId}
```

or resource status:

```text
GET /api/v1/homework/{homeworkId}
```

---

# Webhooks

Webhook endpoints must:

- Verify provider signature
- Validate payload
- Use idempotency
- Log event
- Avoid duplicate processing
- Return quickly
- Process heavy work asynchronously

Example:

```text
POST /api/v1/webhooks/payments/razorpay
```

Webhook routes must not expose sensitive internal behavior.

---

# API Endpoint Groups

## Authentication

```text
POST /api/v1/auth/register
POST /api/v1/auth/login
POST /api/v1/auth/logout
POST /api/v1/auth/refresh
POST /api/v1/auth/forgot-password
POST /api/v1/auth/reset-password
POST /api/v1/auth/verify-email
GET  /api/v1/auth/me
```

---

## Users

```text
GET   /api/v1/users/me
PATCH /api/v1/users/me
GET   /api/v1/users/me/preferences
PATCH /api/v1/users/me/preferences
GET   /api/v1/users/me/sessions
DELETE /api/v1/users/me/sessions/{sessionId}
```

---

## Organizations

```text
GET   /api/v1/organizations
POST  /api/v1/organizations
GET   /api/v1/organizations/{organizationId}
PATCH /api/v1/organizations/{organizationId}
GET   /api/v1/organizations/{organizationId}/institutions
```

---

## Institutions

```text
GET   /api/v1/institutions
POST  /api/v1/institutions
GET   /api/v1/institutions/{institutionId}
PATCH /api/v1/institutions/{institutionId}
GET   /api/v1/institutions/{institutionId}/dashboard
```

---

## Academic

```text
GET  /api/v1/institutions/{institutionId}/academic-sessions
POST /api/v1/institutions/{institutionId}/academic-sessions

GET  /api/v1/institutions/{institutionId}/grades
POST /api/v1/institutions/{institutionId}/grades

GET  /api/v1/institutions/{institutionId}/sections
POST /api/v1/institutions/{institutionId}/sections

GET  /api/v1/subjects
GET  /api/v1/subjects/{subjectId}/chapters
GET  /api/v1/chapters/{chapterId}/topics
```

---

## Homework

```text
POST /api/v1/homework
GET  /api/v1/homework
GET  /api/v1/homework/{homeworkId}
POST /api/v1/homework/{homeworkId}/solve
DELETE /api/v1/homework/{homeworkId}
POST /api/v1/homework/{homeworkId}/feedback
```

---

## OCR

```text
POST /api/v1/ocr/jobs
GET  /api/v1/ocr/jobs/{ocrJobId}
POST /api/v1/ocr/jobs/{ocrJobId}/confirm
```

---

## AI Tutor

```text
POST /api/v1/ai-tutor/conversations
GET  /api/v1/ai-tutor/conversations
GET  /api/v1/ai-tutor/conversations/{conversationId}
POST /api/v1/ai-tutor/conversations/{conversationId}/messages
DELETE /api/v1/ai-tutor/conversations/{conversationId}
```

---

## Notes

```text
POST /api/v1/notes/generate
POST /api/v1/notes
GET  /api/v1/notes
GET  /api/v1/notes/{noteId}
PATCH /api/v1/notes/{noteId}
DELETE /api/v1/notes/{noteId}
```

---

## Quiz

```text
POST /api/v1/quizzes/generate
GET  /api/v1/quizzes
GET  /api/v1/quizzes/{quizId}
POST /api/v1/quizzes/{quizId}/attempts
GET  /api/v1/quizzes/{quizId}/attempts/{attemptId}
POST /api/v1/quizzes/{quizId}/attempts/{attemptId}/submit
```

---

## Worksheets

```text
POST /api/v1/worksheets/generate
GET  /api/v1/worksheets
GET  /api/v1/worksheets/{worksheetId}
DELETE /api/v1/worksheets/{worksheetId}
POST /api/v1/worksheets/{worksheetId}/export
```

---

## Study Planner

```text
POST /api/v1/study-plans/generate
GET  /api/v1/study-plans
GET  /api/v1/study-plans/{studyPlanId}
PATCH /api/v1/study-plans/{studyPlanId}
POST /api/v1/study-plans/{studyPlanId}/tasks/{taskId}/complete
```

---

## Analytics

```text
GET /api/v1/analytics/student
GET /api/v1/analytics/mastery
GET /api/v1/analytics/recommendations
GET /api/v1/analytics/institutions/{institutionId}
GET /api/v1/analytics/organizations/{organizationId}
```

Organization analytics may be deferred.

---

## Notifications

```text
GET   /api/v1/notifications
PATCH /api/v1/notifications/{notificationId}/read
PATCH /api/v1/notifications/read-all
GET   /api/v1/notification-preferences
PATCH /api/v1/notification-preferences
```

---

## Commerce

```text
GET  /api/v1/plans
GET  /api/v1/features
GET  /api/v1/entitlements
GET  /api/v1/usage
POST /api/v1/subscriptions
GET  /api/v1/subscriptions/current
PATCH /api/v1/subscriptions/{subscriptionId}
POST /api/v1/subscriptions/{subscriptionId}/cancel
```

---

## Payments

```text
POST /api/v1/payments
POST /api/v1/payments/{paymentId}/confirm
GET  /api/v1/payments
GET  /api/v1/payments/{paymentId}
GET  /api/v1/invoices
GET  /api/v1/invoices/{invoiceId}
```

---

## Admin

```text
GET /api/v1/admin/dashboard
GET /api/v1/admin/users
GET /api/v1/admin/organizations
GET /api/v1/admin/institutions
GET /api/v1/admin/subscriptions
GET /api/v1/admin/payments
GET /api/v1/admin/ai-usage
GET /api/v1/admin/audit-logs
```

---

# Request Body Standards

Use JSON request bodies.

Content-Type:

```text
application/json
```

Example:

```json
{
  "questionText": "Explain photosynthesis",
  "subjectId": "uuid",
  "gradeId": "uuid"
}
```

---

# Date and Time Standards

Use ISO 8601.

Example:

```text
2026-07-05T10:30:00.000Z
```

Store timestamps in UTC.

Display conversion should happen at the frontend using user or institution timezone.

---

# Enum Standards

Use UPPER_SNAKE_CASE for enum values.

Example:

```json
{
  "institutionType": "SCHOOL",
  "status": "ACTIVE"
}
```

---

# JSON Field Naming

Use camelCase in API JSON.

Good:

```json
{
  "organizationId": "uuid",
  "institutionId": "uuid",
  "createdAt": "2026-07-05T10:30:00.000Z"
}
```

Bad:

```json
{
  "organization_id": "uuid",
  "institution_id": "uuid"
}
```

---

# Validation Rules

Every endpoint must validate:

- Body
- Query parameters
- Path parameters
- File uploads
- Enum values
- User permissions
- Organization scope
- Institution scope
- Entitlements where applicable

Validation failures return:

```text
422 Unprocessable Entity
```

---

# Logging Requirements

Every API request must log:

- requestId
- userId where available
- organizationId where available
- institutionId where available
- method
- path
- statusCode
- durationMs
- errorCode where applicable

Never log:

- Passwords
- Tokens
- Secrets
- Raw card data
- Sensitive AI conversation content unless explicitly protected and approved

---

# Audit Requirements

Audit logs are required for:

- Admin user changes
- Role changes
- Permission changes
- Organization changes
- Institution changes
- Subscription changes
- Payment refunds
- Feature flag changes
- AI prompt changes
- User impersonation
- Data exports

---

# Security Requirements

Every API must follow:

- HTTPS in production
- Authentication where required
- Authorization where required
- Tenant scoping
- Input validation
- Rate limiting where appropriate
- Secure error handling
- No sensitive data leakage
- Audit logging for sensitive actions

---

# CORS

CORS must allow only approved origins.

Examples:

```text
https://app.acadedx.com
https://admin.acadedx.com
https://www.acadedx.com
```

Development origins may be configured separately.

Do not allow unrestricted wildcard origins in production.

---

# API Documentation

Every endpoint must be documented using OpenAPI / Swagger.

Documentation must include:

- Endpoint path
- HTTP method
- Description
- Authentication requirements
- Authorization requirements
- Request schema
- Response schema
- Error responses
- Pagination behavior where applicable
- Rate limits where applicable

---

# Backward Compatibility

Do not break existing API contracts without:

- Versioning
- Deprecation notice
- Migration plan
- Client update plan

Deprecated endpoints should remain available for at least one major release cycle where practical.

---

# API Testing Requirements

Every API must have tests for:

- Success case
- Validation failure
- Authentication failure
- Authorization failure
- Tenant scope violation
- Entitlement failure where applicable
- Not found
- Rate limit where applicable

Critical APIs must include integration tests.

---

# API Review Checklist

Before approving an API:

- Does it use correct resource naming?
- Does it use `/api/v1`?
- Does it avoid school-only terminology?
- Does it support organization and institution context?
- Does it enforce authentication?
- Does it enforce authorization?
- Does it validate entitlements where required?
- Does it return standard response format?
- Does it use stable error codes?
- Does it emit analytics where required?
- Does it log request ID?
- Is it documented in OpenAPI?
- Are tests included?

---

# Related Documents

- AUTHENTICATION.md
- ENDPOINTS.md
- ERROR_CODES.md
- DATABASE_SCHEMA.md
- SECURITY.md
- PRODUCT_REQUIREMENTS.md
- CODING_STANDARDS.md