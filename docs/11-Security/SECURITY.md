# Security

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the security principles, controls, and implementation requirements for Acadedx.

Acadedx is an AI-powered education platform designed as a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

Security must protect:

* Organizations
* Institutions
* Learners
* Guardians
* Teachers
* Administrators
* Academic records
* Attendance data
* Assessment data
* Fee data
* Communication records
* Files and documents
* AI interactions and outputs
* Platform administration workflows

A School is an Institution type.

Do not create security assumptions based on a hardcoded school-only model.

---

# Security Goals

Acadedx security must ensure:

1. Only authenticated users access protected resources.
2. Users can access only Organizations and Institutions they are authorized for.
3. Institution data remains isolated from other Institutions.
4. Organization data remains isolated from other Organizations.
5. Learner data is handled with extra care because it may include minor data.
6. AI features do not bypass authorization, privacy, or tenant isolation.
7. Sensitive operations are audited.
8. Security controls are enforced server-side.
9. Infrastructure secrets are protected.
10. System behavior is observable, testable, and incident-ready.

---

# Security Scope

This document applies to:

* Web application
* Admin application
* Mobile application
* Desktop application
* Backend APIs
* Background jobs
* AI services
* OCR workflows
* Notification workflows
* Payment workflows
* File storage
* Database access
* Logs and monitoring
* DevOps and CI/CD
* Future public APIs

---

# Platform Security Model

The platform hierarchy is:

```text
Platform
    ↓
Organization
    ↓
Institution
    ↓
Academic Structure
    ↓
Learners / Guardians / Teachers
    ↓
AI Learning Engine
```

Security must enforce this hierarchy at every access point.

MVP constraint:

```text
One Organization → One Institution
```

Future state:

```text
One Organization → Multiple Institutions
```

Implementation must not assume the MVP constraint is permanent.

---

# Core Security Principles

## 1. Deny by Default

Access must be denied unless explicitly allowed.

No backend endpoint, service method, background job, or AI workflow should assume access by default.

---

## 2. Server-Side Enforcement

Frontend checks are not security controls.

The backend must enforce:

* Authentication
* Authorization
* RBAC
* Permission checks
* Organization scope
* Institution scope
* Ownership checks
* Entitlement checks
* Input validation

---

## 3. Least Privilege

Users, services, API keys, database accounts, and background workers must receive only the minimum permissions required.

Examples:

* A Teacher should access only assigned classes, sections, subjects, and learners.
* A Guardian should access only linked learners.
* An Institution Admin should access only their Institution.
* An Organization Admin should access only Institutions under their Organization.
* A Platform Admin should be tightly audited.

---

## 4. Tenant Isolation

Organization and Institution boundaries are mandatory.

No user or service may access resources outside the permitted Organization or Institution.

Tenant isolation must be enforced in:

* API guards
* Service methods
* Database queries
* Background jobs
* File storage paths
* AI context retrieval
* Reports
* Analytics
* Audit logs
* Exports

---

## 5. Secure by Design

Security must be included during design, not added after implementation.

Every feature must define:

* User roles
* Required permissions
* Data scope
* Sensitive fields
* Validation rules
* Audit requirements
* Privacy considerations
* Abuse cases
* Rate limits
* AI safety requirements, where applicable

---

## 6. Privacy by Design

Learner, guardian, teacher, and institution data must be collected and processed only for clear product purposes.

Do not collect data unless it supports an approved feature, legal requirement, operational requirement, or safety requirement.

---

## 7. Auditability

Sensitive actions must create audit logs.

Security-sensitive activity must be traceable using:

* request ID
* actor user ID
* Organization ID
* Institution ID
* action
* resource type
* resource ID
* timestamp
* outcome

---

# Identity and Authentication

Authentication must follow the standards defined in:

```text
docs/06-API/AUTHENTICATION.md
```

Protected APIs must require:

```http
Authorization: Bearer <access_token>
```

The authentication system must support:

* Access tokens
* Refresh tokens
* Secure logout
* Token rotation
* Password reset
* Email verification
* Session invalidation
* Account suspension
* Future MFA support

---

# Password Security

Passwords must follow secure storage and validation requirements.

## Requirements

* Never store plaintext passwords.
* Hash passwords using a strong password hashing algorithm such as Argon2id or bcrypt.
* Use unique salts.
* Enforce minimum password complexity.
* Block commonly compromised passwords where practical.
* Rate-limit login and password reset attempts.
* Do not reveal whether an email exists during password reset.
* Require re-authentication for sensitive actions.

## Sensitive Actions Requiring Re-Authentication

Examples:

* Password change
* Email change
* MFA setup or disablement
* Role change
* Payment refund
* Data export
* Account deletion
* Organization ownership transfer
* Institution deletion or suspension

---

# Session Security

Session handling must include:

* Short-lived access tokens
* Refresh token rotation
* Refresh token revocation
* Logout support
* Logout from all sessions
* Device/session listing in future release
* Server-side invalidation for compromised accounts

Access token lifetime should be short.

Refresh tokens must be stored securely and revocable.

---

# Multi-Factor Authentication

MFA is not required for the first MVP unless business risk requires it.

MFA should be planned for:

* Platform Admins
* Organization Owners
* Organization Admins
* Institution Admins
* Finance Admins
* Security Admins

Future MFA methods may include:

* Authenticator app
* Email OTP
* SMS OTP
* Passkeys

SMS OTP should not be the preferred high-security method.

---

# Authorization Model

Authorization must be based on:

```text
User → Membership → Role → Permissions → Scope
```

A user may have different roles across different Organizations and Institutions.

Authorization must evaluate:

1. Is the user authenticated?
2. Is the user active?
3. Is the Organization active?
4. Is the Institution active, where applicable?
5. Does the user have an active membership?
6. Does the user have the required role?
7. Does the user have the required permission?
8. Does the resource belong to the permitted Organization?
9. Does the resource belong to the permitted Institution?
10. Does the user own or have assigned access to the resource?
11. Does the feature require an entitlement?
12. Is the operation blocked by security, privacy, or retention policy?

---

# Role-Based Access Control

RBAC is documented separately in:

```text
docs/11-Security/RBAC.md
```

Security implementation must not hardcode role checks throughout services.

Preferred pattern:

```text
Guard → Policy → Permission → Scope Validation → Service
```

Avoid scattering checks such as:

```ts
if (user.role === 'ADMIN') {
  // allow
}
```

Use policy-based authorization instead.

---

# Permission-Based Authorization

Roles should map to permissions.

Examples:

```text
organization.read
organization.update
institution.read
institution.update
learner.read
learner.create
learner.update
guardian.read
teacher.assign
attendance.mark
assessment.result.update
fee.payment.record
ai.insight.generate
file.upload
audit.read
```

A permission alone is not enough.

The permission must be valid for the current scope.

Example:

A user with:

```text
learner.read
```

may still only read learners within their permitted Institution or assigned learner relationship.

---

# Organization Scope

Organization-scoped resources must validate:

```text
organizationId
```

Examples:

* Organization profile
* Organization settings
* Organization users
* Organization memberships
* Organization audit logs
* Organization-level reporting
* Institution list under Organization
* Organization billing or subscription records

Organization Admins must not access another Organization.

---

# Institution Scope

Institution-scoped resources must validate:

```text
institutionId
```

Examples:

* Institution profile
* Academic years
* Classes
* Sections
* Subjects
* Learners
* Guardians
* Teachers
* Attendance
* Assessments
* Fees
* Announcements
* Institution files
* Institution reports
* Institution settings
* AI insights

Institution Admins must not access another Institution.

---

# MVP Tenant Rule

In MVP:

```text
One Organization → One Institution
```

Security implementation must still validate both:

```text
organizationId
institutionId
```

Do not simplify backend authorization to a single global institution assumption.

Bad:

```text
currentUser.institutionId is enough
```

Good:

```text
Validate user membership against organizationId and institutionId.
Validate requested resource belongs to organizationId and institutionId.
```

---

# Multi-Institution Future Readiness

The security model must support:

* One user with roles in multiple Institutions.
* One Organization with multiple Institutions.
* Organization Admins viewing aggregated Institution data.
* Institution Admins restricted to one Institution.
* Teachers assigned across multiple Institutions, if allowed.
* Guardians linked to learners in multiple Institutions, if allowed.
* Institution-specific feature entitlements.
* Institution-specific audit and reporting.

Do not implement security controls that would require major redesign for multi-institution support.

---

# Ownership Checks

Some resources require ownership validation in addition to role and scope checks.

Examples:

| Resource        | Ownership Rule                               |
| --------------- | -------------------------------------------- |
| User profile    | User may access own profile                  |
| Learner profile | Guardian may access linked learner only      |
| Homework        | Learner may access own homework only         |
| AI conversation | User may access own conversation only        |
| File            | User or scoped admin must have access        |
| Notification    | User may access own notification only        |
| Payment         | User, finance role, or authorized admin only |

Ownership checks must be performed server-side.

---

# Guardian and Learner Access

Guardian access must be based on verified Guardian-Learner links.

A Guardian may access:

* Linked learner profile summary
* Attendance summary, if allowed
* Assessment summary, if allowed
* Institution announcements relevant to the learner
* Communication history relevant to the guardian
* Fee information, if permitted

A Guardian must not access:

* Other learners
* Other guardians
* Internal teacher notes, unless explicitly allowed
* Institution-wide private data
* Staff-only reports
* Sensitive disciplinary records unless policy allows

---

# Teacher Access

Teacher access must be based on Institution membership and assignment.

A Teacher may access:

* Assigned classes
* Assigned sections
* Assigned subjects
* Learners assigned to their teaching context
* Attendance workflows for assigned sections
* Assessment workflows for assigned subjects/classes
* Teaching resources and announcements

A Teacher must not access:

* Unassigned learner records
* Fee records unless explicitly permitted
* Organization-level settings
* Institution-wide administrative reports unless granted
* Other teachers' private records

---

# Platform Admin Access

Platform Admins may require broad access for support and operations.

Platform Admin access must be:

* Strictly role-controlled
* Audited
* Logged with request ID
* Reviewed periodically
* Protected by MFA in future
* Avoided for routine workflows where scoped admin access is enough

Platform Admins must not bypass privacy, audit, or safety controls.

---

# Impersonation

User impersonation is a high-risk capability.

Impersonation is not required for MVP.

If introduced later, it must include:

* Explicit permission
* Strong admin authentication
* Full audit logging
* Visible impersonation state
* No access to passwords, tokens, or secrets
* Restrictions for learner and guardian accounts
* Reason capture
* Time limit
* Revocation support

---

# Entitlement Security

Entitlements control access to premium, limited, costly, or AI-heavy features.

Examples:

```text
ai.insight.generate
ocr.extract
homework.solve
quiz.generate
worksheet.generate
report.export
advanced.analytics
institution.dashboard
```

Entitlements must check feature access, not plan names.

Bad:

```text
if plan == PREMIUM
```

Good:

```text
canUseFeature("ai.insight.generate")
```

Entitlements do not replace authentication, RBAC, or tenant scoping.

---

# Feature Flag Security

Feature flags control rollout, not permission.

A feature flag may enable or disable a feature for:

* Platform
* Organization
* Institution
* Role
* User cohort

Feature flags must not bypass:

* Authentication
* Authorization
* Tenant scoping
* Entitlement checks
* Privacy checks
* Audit logging

---

# API Security

All APIs must follow:

* HTTPS in production
* Versioned paths
* Authentication where required
* Authorization where required
* Tenant scoping
* Input validation
* Output filtering
* Rate limiting where appropriate
* Stable error codes
* Secure error handling
* Request ID logging
* OpenAPI documentation
* Tests for security failure cases

API standards are defined in:

```text
docs/06-API/API_GUIDELINES.md
```

---

# Input Validation

Every API must validate:

* Path parameters
* Query parameters
* Request body
* File uploads
* Enum values
* Date formats
* Numeric ranges
* IDs
* Pagination values
* Sort fields
* Filter fields

Validation must happen before business logic.

Invalid requests should return stable error codes such as:

```text
VALIDATION_ERROR
INVALID_REQUEST_BODY
INVALID_QUERY_PARAMETER
INVALID_PATH_PARAMETER
```

---

# Output Filtering

Responses must not expose fields that the user is not authorized to view.

Examples of sensitive fields that must not be returned casually:

* Password hashes
* Refresh tokens
* OTPs
* Internal security flags
* Internal risk scores
* Provider credentials
* Payment secrets
* Full audit metadata
* Private staff notes
* Sensitive learner records
* Raw AI prompts where restricted

Use DTOs or response serializers.

Do not return raw ORM entities directly.

---

# Mass Assignment Protection

APIs must prevent users from setting restricted fields.

Examples of restricted fields:

```text
role
permissions
status
organizationId
institutionId
isPlatformAdmin
emailVerified
subscriptionStatus
entitlements
createdBy
updatedBy
deletedAt
```

Use explicit DTOs and field allowlists.

---

# Rate Limiting

Rate limiting must protect expensive and sensitive workflows.

Required categories:

| API Category          | Requirement                              |
| --------------------- | ---------------------------------------- |
| Login                 | Strict rate limits                       |
| Password reset        | Strict rate limits                       |
| Invitation acceptance | Moderate rate limits                     |
| AI generation         | Entitlement and usage-based limits       |
| OCR                   | Entitlement and usage-based limits       |
| File upload           | Size and frequency limits                |
| Admin APIs            | Role-specific limits                     |
| Webhooks              | Provider-aware validation and throttling |
| Public endpoints      | Abuse-resistant limits                   |

Rate limit responses should use:

```text
429 RATE_LIMIT_EXCEEDED
```

---

# File Upload Security

File upload APIs must validate:

* Authentication
* Authorization
* Organization scope
* Institution scope
* File size
* MIME type
* Extension
* Storage quota
* Entitlement where applicable
* Malware or safety scan where practical

Supported MVP file types:

```text
JPG
JPEG
PNG
WEBP
PDF
```

Files must not be trusted based only on extension.

Use MIME sniffing where practical.

---

# File Storage Security

Files must be stored with clear ownership and tenant metadata.

Required metadata:

```text
fileId
organizationId
institutionId
uploadedByUserId
relatedEntityType
relatedEntityId
mimeType
sizeBytes
storageKey
visibility
createdAt
deletedAt
```

Rules:

* Do not expose raw storage paths.
* Use signed URLs for private files.
* Signed URLs must expire.
* File access must validate user permission before URL generation.
* Deleted files should be soft-deleted before permanent purge.
* Public files must be explicitly marked public.

---

# AI Security

AI workflows must not bypass core application security.

AI requests must validate:

* User identity
* Organization scope
* Institution scope
* Resource ownership
* Permissions
* Entitlements
* Usage limits
* Prompt safety
* Data minimization
* Output safety

AI-generated content must be treated as assistive, not authoritative.

---

# AI Data Boundaries

AI context retrieval must respect tenant boundaries.

AI workflows must not retrieve:

* Learner data outside the Institution
* Guardian data outside the permitted relationship
* Teacher data outside assignment scope
* Organization data outside the user's Organization
* Files outside the user's allowed scope
* Private audit records unless explicitly allowed

Vector search, RAG, embeddings, and personalization must include Organization and Institution filters.

---

# AI Prompt Security

Prompt templates and AI orchestration must prevent:

* Prompt injection
* Data exfiltration
* Cross-tenant leakage
* Hidden instruction override
* Unsafe educational guidance
* Exposure of internal system prompts
* Exposure of provider keys or secrets

Do not place secrets in prompts.

Do not send unnecessary personal data to AI providers.

---

# AI Output Safety

AI output should be checked for:

* Unsafe content
* Hallucinated claims presented as facts
* Privacy leakage
* Inappropriate learner guidance
* Harmful instructions
* Bias or discriminatory language
* Unsupported medical, legal, or financial advice
* Exposure of internal data

AI output for academic use should include appropriate disclaimers where needed.

---

# OCR Security

OCR workflows must validate:

* File ownership
* File type
* File size
* Institution scope
* Entitlement
* Usage limits
* Safety scan status

OCR output may contain sensitive data.

OCR text must follow the same access control and retention rules as the source file.

---

# Payment Security

Payment security applies if payment workflows are included in MVP or later phases.

Rules:

* Do not store raw card data.
* Use PCI-compliant payment providers.
* Verify payment gateway callbacks.
* Require idempotency for payment creation.
* Log payment events safely.
* Audit refunds and manual adjustments.
* Do not expose provider secrets.
* Do not trust frontend payment status.

---

# Webhook Security

Webhook endpoints must:

* Verify provider signatures.
* Validate payload shape.
* Use idempotency.
* Avoid duplicate processing.
* Return quickly.
* Process heavy work asynchronously.
* Log event ID and provider.
* Avoid exposing internal errors.
* Rate-limit or throttle suspicious sources.

Examples:

```text
POST /api/v1/webhooks/payments/razorpay
POST /api/v1/webhooks/payments/stripe
```

---

# Data Protection

Acadedx must protect personal and educational data.

Sensitive data includes:

* Learner profile data
* Guardian contact data
* Teacher employment data
* Attendance records
* Assessment results
* Fee records
* Communications
* Uploaded documents
* AI interactions
* OCR text
* Audit logs

Data must be protected using:

* Access control
* Encryption in transit
* Encryption at rest where supported
* Secure backups
* Minimal logging
* Retention policies
* Deletion workflows
* Audit logs

---

# Minor Learner Data

Learners may be minors.

Minor learner data must receive stricter protection.

Rules:

* Collect only necessary learner data.
* Limit staff access to legitimate educational purposes.
* Guardian access must be relationship-based.
* Avoid exposing learner data in logs.
* Avoid using learner personal data in AI prompts unless necessary.
* Use privacy-safe analytics where possible.
* Apply retention and deletion rules carefully.

---

# Encryption

## In Transit

Production traffic must use HTTPS.

Internal service communication should use TLS where practical.

## At Rest

Sensitive data should be encrypted at rest through:

* Managed database encryption
* Storage provider encryption
* Secret manager encryption
* Optional field-level encryption for highly sensitive fields

## Field-Level Encryption Candidates

Consider field-level encryption for:

* Government IDs, if collected
* Medical notes, if collected
* Sensitive disciplinary records
* Payment-related identifiers
* High-risk guardian or learner documents

Avoid collecting these unless product requirements justify it.

---

# Secrets Management

Secrets must not be committed to source control.

Secrets include:

* Database passwords
* JWT secrets
* OAuth secrets
* Payment provider keys
* AI provider keys
* SMTP credentials
* Storage credentials
* Webhook signing secrets
* Private keys

Use environment variables or a secrets manager.

Development secrets must be separate from production secrets.

Rotate secrets after exposure or suspected compromise.

---

# Environment Separation

Separate environments must be used for:

* Local development
* Development
* Staging
* Production

Rules:

* Production data must not be used in local development unless anonymized.
* Staging must not use production secrets.
* Test users must be clearly separated.
* Debug tooling must not be enabled in production.
* Production access must be restricted.

---

# Logging Security

Logs must be useful but safe.

Log:

* requestId
* userId where available
* organizationId where available
* institutionId where available
* method
* path
* statusCode
* durationMs
* errorCode
* safe diagnostic metadata

Never log:

* Passwords
* Access tokens
* Refresh tokens
* OTPs
* Private keys
* Payment secrets
* Raw card data
* Sensitive learner records
* Sensitive guardian records
* Raw AI conversation content unless explicitly approved and protected
* Raw uploaded document contents

---

# Audit Logging

Audit logs are required for high-risk actions.

Audit events must include:

```text
auditLogId
requestId
actorUserId
organizationId
institutionId
action
resourceType
resourceId
beforeState
afterState
ipAddress
userAgent
status
createdAt
```

Sensitive actions requiring audit logs include:

* Login failures above threshold
* Account suspension
* User invitation
* Role assignment
* Permission change
* Membership removal
* Organization update
* Institution update
* Learner record update
* Guardian-learner relationship change
* Teacher assignment change
* Attendance update after lock
* Assessment result update
* Fee payment record change
* File deletion
* AI prompt/template update
* AI insight generation
* Data export
* Payment refund
* Feature flag change
* Security setting change
* Platform Admin access to tenant data

---

# Monitoring and Alerting

Security monitoring should detect:

* Repeated login failures
* Password reset abuse
* Tenant scope violations
* Unusual admin activity
* Excessive AI usage
* Excessive OCR usage
* File upload abuse
* Failed webhook signatures
* Unexpected permission errors
* Sudden data export activity
* High error rates
* Suspicious IP/device patterns

Production systems should expose metrics for:

* Authentication failures
* Authorization failures
* Rate limits
* Tenant scope violations
* AI provider failures
* OCR failures
* Payment failures
* Webhook failures

---

# Secure Error Handling

Error responses must not expose sensitive internals.

Do not expose:

* Stack traces
* SQL errors
* Database table names
* Secret values
* Provider credentials
* Internal hostnames
* Tenant boundary details
* Sensitive learner or guardian details

Use stable error codes from:

```text
docs/06-API/ERROR_CODES.md
```

---

# Dependency Security

Dependencies must be managed carefully.

Requirements:

* Use lockfiles.
* Review major dependency upgrades.
* Run vulnerability scans in CI where practical.
* Remove unused dependencies.
* Avoid abandoned packages.
* Avoid packages with suspicious maintainers or unclear provenance.
* Patch critical vulnerabilities quickly.

---

# CI/CD Security

CI/CD pipelines must protect code and deployment integrity.

Requirements:

* Do not print secrets in logs.
* Restrict production deployments.
* Require reviews for sensitive changes.
* Run tests before deployment.
* Run linting and type checks.
* Run security scans where practical.
* Use separate credentials per environment.
* Rotate leaked CI secrets immediately.

---

# Database Security

Database access must be restricted.

Rules:

* Use least-privilege database users.
* Do not expose database publicly.
* Use migrations for schema changes.
* Validate tenant scope in queries.
* Avoid raw SQL unless necessary.
* Parameterize queries.
* Protect backups.
* Encrypt backups where supported.
* Audit production database access.

---

# Tenant-Safe Querying

Every query for tenant-scoped resources must include scope filters.

Example:

```text
organizationId = currentContext.organizationId
institutionId = currentContext.institutionId
```

Bad:

```sql
SELECT * FROM learners WHERE id = $1;
```

Good:

```sql
SELECT * FROM learners
WHERE id = $1
  AND organization_id = $2
  AND institution_id = $3;
```

This applies even when IDs are globally unique.

---

# Background Job Security

Background jobs must preserve security context.

Required job metadata:

```text
jobId
jobType
requestedByUserId
organizationId
institutionId
resourceType
resourceId
permissionsSnapshot
createdAt
```

Rules:

* Validate access when job is created.
* Revalidate scope before writing results.
* Do not process cross-tenant data in the same unscoped job.
* Do not include secrets in job payloads.
* Avoid storing large sensitive payloads directly in queues.
* Audit sensitive job completion.

---

# Reporting and Export Security

Reports and exports are high-risk.

Rules:

* Require explicit permission.
* Validate Organization and Institution scope.
* Apply row-level access control.
* Audit export creation.
* Use short-lived signed URLs.
* Mask sensitive fields where possible.
* Expire exports.
* Avoid exporting data beyond the requested scope.
* Apply rate limits.

---

# Data Retention and Deletion

Retention and deletion must follow privacy and business rules.

General requirements:

* Soft delete operational records where auditability is needed.
* Hard delete only when legally and technically safe.
* Preserve audit logs according to retention policy.
* Do not orphan learner, guardian, or institution data.
* Ensure deleted files are eventually purged from storage.
* Ensure AI-derived data follows retention rules.
* Ensure backups follow retention schedules.

Detailed privacy requirements are defined in:

```text
docs/11-Security/PRIVACY.md
```

---

# CORS and Browser Security

Production CORS must allow only approved origins.

Examples:

```text
https://app.acadedx.com
https://admin.acadedx.com
https://www.acadedx.com
```

Do not use wildcard origins in production.

Recommended browser security controls:

* Secure cookies where cookies are used
* SameSite cookie settings
* Content Security Policy
* X-Frame-Options or frame-ancestors
* X-Content-Type-Options
* Referrer-Policy
* Permissions-Policy

---

# Admin Application Security

Admin features are high-risk.

Admin application must include:

* Strong authentication
* Role-based access
* Permission checks
* Tenant scoping
* Audit logs
* Sensitive action confirmation
* Re-authentication for high-risk actions
* No broad data access without business purpose

Platform Admin features must be separated from Institution Admin features.

---

# Secure Development Requirements

Developers must follow:

* Secure coding standards
* Code review
* Type safety
* DTO validation
* Avoid raw ORM entity exposure
* Avoid hardcoded secrets
* Avoid unsafe deserialization
* Avoid insecure direct object references
* Avoid dynamic query construction without validation
* Avoid bypassing guards in internal endpoints
* Write tests for negative security cases

---

# Security Testing Requirements

Every protected endpoint must have tests for:

* Missing authentication
* Invalid token
* Expired token
* Missing role
* Missing permission
* Organization mismatch
* Institution mismatch
* Ownership violation
* Invalid input
* Rate limit where applicable
* Entitlement failure where applicable

High-risk workflows need integration tests.

Examples:

* Role assignment
* Learner access by guardian
* Teacher access to assigned learners
* Institution Admin access boundary
* Organization Admin multi-institution boundary
* File download authorization
* AI context retrieval
* Payment webhook verification
* Data export authorization

---

# Vulnerability Management

Security vulnerabilities must be triaged by severity.

Suggested severity model:

| Severity | Examples                                                    | Expected Action          |
| -------- | ----------------------------------------------------------- | ------------------------ |
| Critical | Auth bypass, tenant data leak, RCE, exposed secrets         | Immediate fix            |
| High     | Privilege escalation, sensitive data exposure, payment flaw | Urgent fix               |
| Medium   | Rate-limit bypass, limited data exposure, unsafe config     | Planned fix              |
| Low      | Minor hardening issue, non-sensitive leak                   | Backlog or scheduled fix |

Production-impacting critical issues must be handled as incidents.

---

# Incident Response

Security incidents must be handled with a defined process.

Incident process:

1. Detect issue.
2. Triage severity.
3. Contain impact.
4. Preserve evidence.
5. Fix root cause.
6. Rotate affected secrets if needed.
7. Notify stakeholders where required.
8. Review logs and audit trails.
9. Add regression tests.
10. Document lessons learned.

Possible incidents:

* Unauthorized access
* Tenant data exposure
* Credential leak
* Payment abuse
* AI data leakage
* Malware upload
* Account takeover
* Admin abuse
* Webhook spoofing
* Database exposure

---

# Security Non-Goals for MVP

The MVP does not need to fully implement:

* Enterprise SSO
* SCIM provisioning
* Advanced device trust
* Full SIEM integration
* Mandatory MFA for all users
* Customer-managed encryption keys
* Advanced DLP
* Full zero-trust internal networking
* Public developer API security program
* Cross-region data residency controls

However, the architecture must not block these future capabilities.

---

# MVP Security Requirements

The MVP must implement at minimum:

```text
JWT authentication
Refresh token rotation
Password hashing
Email verification
Password reset
Role-based access control
Permission checks for sensitive actions
Organization scoping
Institution scoping
Learner ownership checks
Guardian-learner relationship checks
Teacher assignment checks
DTO validation
Secure error responses
Rate limits for auth and AI-heavy APIs
File upload validation
Signed URLs for private files
Audit logs for sensitive actions
Request ID logging
Environment-specific secrets
HTTPS in production
CORS allowlist
Tenant-safe database queries
Security tests for protected endpoints
```

---

# Security Review Checklist

Before releasing a feature, confirm:

* Authentication is enforced where required.
* Authorization is enforced server-side.
* Organization scope is validated.
* Institution scope is validated.
* Ownership checks are applied where needed.
* Roles and permissions are not hardcoded inconsistently.
* Entitlements are checked for premium or costly features.
* Input validation exists.
* Output filtering exists.
* Error responses are safe.
* Logs do not contain secrets or sensitive data.
* Audit logs exist for sensitive actions.
* Rate limits are applied where needed.
* File upload rules are enforced where applicable.
* AI context retrieval is tenant-safe where applicable.
* Background jobs preserve tenant context.
* Tests cover security failure cases.
* OpenAPI documentation lists auth and error behavior.

---

# Related Documents

* docs/06-API/API_GUIDELINES.md
* docs/06-API/AUTHENTICATION.md
* docs/06-API/ENDPOINTS.md
* docs/06-API/ERROR_CODES.md
* docs/11-Security/RBAC.md
* docs/11-Security/PRIVACY.md
* docs/05-Database/DATABASE_SCHEMA.md
* docs/05-Database/DATA_DICTIONARY.md
* docs/04-Architecture/SYSTEM_ARCHITECTURE.md
* docs/04-Architecture/CODING_STANDARDS.md
