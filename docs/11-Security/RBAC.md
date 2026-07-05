# Role-Based Access Control

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the Role-Based Access Control model for Acadedx.

RBAC must support the current school-focused MVP while remaining ready for the future platform architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

The backend, database, API, and authorization logic must use `institutionId`, not `schoolId`.

---

# RBAC Goals

Acadedx RBAC must ensure:

1. Users access only resources they are authorized to access.
2. Roles are scoped to Platform, Organization, or Institution contexts.
3. Organization and Institution boundaries are enforced consistently.
4. Learner data is protected through role, scope, ownership, and relationship checks.
5. Guardian access is limited to linked learners.
6. Teacher access is limited to assigned academic contexts.
7. Platform Admin access is powerful but tightly audited.
8. MVP supports one Organization → one Institution without blocking future multi-institution support.
9. Roles map to permissions instead of hardcoded conditionals.
10. Authorization is enforced server-side.

---

# Authorization Model

Acadedx authorization is based on:

```text
User → Membership → Role → Permissions → Scope → Resource Policy
```

A user identity is global.

Access is granted through memberships.

A membership may be scoped to:

```text
Platform
Organization
Institution
```

A user may have multiple memberships.

Example future scenario:

```text
User A
├── Organization Admin in Organization 1
├── Institution Admin in Institution 1
└── Teacher in Institution 2
```

The MVP may use only one Organization and one Institution per customer, but the model must support the future scenario.

---

# Scope Levels

## Platform Scope

Platform scope applies to DGIS-operated administrative access.

Examples:

* Platform administration
* Global system configuration
* Global audit visibility
* Customer onboarding
* Support operations
* AI operations
* Security operations

Platform scope must be restricted and audited.

---

## Organization Scope

Organization scope applies to the business owner, education group, trust, company, or enterprise customer.

Examples:

* Organization profile
* Organization settings
* Organization users
* Organization-level memberships
* Institution list
* Organization-level reports
* Billing and subscription records
* Organization audit logs

Organization-scoped roles may access multiple Institutions under the same Organization when permissions allow.

---

## Institution Scope

Institution scope applies to the educational unit where learning happens.

Examples:

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
* Institution announcements
* Institution files
* Institution reports
* AI learner insights

Institution-scoped roles must not access other Institutions.

---

# MVP Scope Rule

In MVP:

```text
One Organization → One Institution
```

Even so, authorization must still validate both:

```text
organizationId
institutionId
```

Do not implement shortcuts such as:

```text
user.institutionId == resource.institutionId
```

without also validating Organization context and active membership.

Correct pattern:

```text
1. Validate authenticated user.
2. Resolve active membership.
3. Validate organizationId.
4. Validate institutionId where applicable.
5. Validate permission.
6. Validate resource ownership or assignment.
7. Execute operation.
```

---

# Role Categories

Acadedx roles are grouped into:

1. Platform roles
2. Organization roles
3. Institution roles
4. Academic staff roles
5. Learner and guardian roles
6. Support and operational roles
7. System roles

---

# Platform Roles

## PLATFORM_ADMIN

Highest operational role for DGIS platform administration.

Scope:

```text
Platform
```

Typical access:

* Manage Organizations
* Manage Institutions
* Manage platform users
* View platform-wide audit logs
* Manage feature flags
* Review AI usage
* Support customer onboarding
* Access admin dashboard

Restrictions:

* Must be audited.
* Should require MFA in future.
* Should not bypass privacy rules without explicit permission.
* Should not access sensitive learner data unless operationally required.

---

## PLATFORM_SUPPORT

Support role for customer assistance.

Scope:

```text
Platform
```

Typical access:

* View limited customer metadata
* View support-related user information
* Assist with account issues
* View non-sensitive diagnostics
* Escalate issues to Platform Admin

Restrictions:

* Cannot change roles.
* Cannot change billing.
* Cannot export sensitive learner data.
* Cannot access full AI conversation history unless explicitly permitted.

---

## PLATFORM_SECURITY_ADMIN

Security operations role.

Scope:

```text
Platform
```

Typical access:

* View security logs
* View audit logs
* Investigate suspicious activity
* Suspend compromised accounts
* Review tenant scope violations
* Manage security policies

Restrictions:

* Changes must be audited.
* Access should be limited to security use cases.

---

## PLATFORM_AI_OPS

AI operations role.

Scope:

```text
Platform
```

Typical access:

* View AI usage metrics
* Monitor AI provider failures
* Manage AI prompt templates where permitted
* Review model routing configuration
* Monitor safety events

Restrictions:

* Must not access learner-identifiable AI content unless explicitly authorized.
* Prompt changes must be audited.

---

## PLATFORM_FINANCE_ADMIN

Finance operations role.

Scope:

```text
Platform
```

Typical access:

* View subscriptions
* View invoices
* View payment records
* Process refunds where allowed
* Review billing disputes

Restrictions:

* Cannot access academic records unless separately authorized.
* Refunds and billing changes must be audited.

---

# Organization Roles

## ORGANIZATION_OWNER

Primary accountable owner of an Organization.

Scope:

```text
Organization
```

Typical access:

* View and update Organization profile
* Manage Organization Admins
* View Institutions under the Organization
* Manage institution-level administrators
* View organization-level reports
* Manage billing and subscription information
* View organization audit logs
* Configure organization-wide settings

Restrictions:

* Cannot access other Organizations.
* Cannot perform DGIS platform-level operations.
* Sensitive actions may require re-authentication.

---

## ORGANIZATION_ADMIN

Administrative manager for an Organization.

Scope:

```text
Organization
```

Typical access:

* Manage Institutions under the Organization, if permitted
* Manage institution admins
* View users across the Organization
* View organization reports
* Configure organization settings
* Review organization audit logs

Restrictions:

* Cannot transfer Organization ownership unless explicitly permitted.
* Cannot access another Organization.
* Billing access may require additional permission.

---

## ORGANIZATION_VIEWER

Read-only organization-level role.

Scope:

```text
Organization
```

Typical access:

* View Organization profile
* View Institution list
* View allowed reports
* View limited audit summaries

Restrictions:

* Cannot update settings.
* Cannot manage users.
* Cannot access sensitive learner data unless explicitly granted.

---

## ORGANIZATION_FINANCE_MANAGER

Finance role for Organization-level billing and fee operations.

Scope:

```text
Organization
```

Typical access:

* View invoices
* View subscriptions
* View payment summaries
* View fee reports, if institution fee module is enabled
* Export finance reports where permitted

Restrictions:

* Cannot change academic data.
* Cannot change RBAC roles.
* Refunds or payment adjustments require audit logging.

---

# Institution Roles

## INSTITUTION_ADMIN

Primary administrator for an Institution.

Scope:

```text
Institution
```

Typical access:

* View and update Institution profile
* Manage academic years
* Manage classes
* Manage sections
* Manage subjects
* Manage learners
* Manage guardians
* Manage teachers
* Assign teachers
* Manage attendance
* Manage assessments
* Manage announcements
* View institution reports
* Manage institution settings
* View institution audit logs

Restrictions:

* Cannot access other Institutions.
* Cannot manage Organization ownership.
* Cannot perform platform-level operations.
* Sensitive changes must be audited.

---

## INSTITUTION_STAFF_ADMIN

Operational staff administrator with limited institution management access.

Scope:

```text
Institution
```

Typical access:

* Manage learner profiles
* Manage guardian contact records
* Manage class and section assignment
* Assist with attendance workflows
* Assist with announcements

Restrictions:

* Cannot update high-risk settings.
* Cannot manage roles unless explicitly permitted.
* Cannot access finance or sensitive reports unless granted.

---

## INSTITUTION_VIEWER

Read-only institution role.

Scope:

```text
Institution
```

Typical access:

* View institution profile
* View academic structure
* View allowed institution reports

Restrictions:

* Cannot modify records.
* Cannot access sensitive learner details unless explicitly permitted.
* Cannot export data unless separately granted.

---

## INSTITUTION_FINANCE_MANAGER

Finance role for fee and payment operations within an Institution.

Scope:

```text
Institution
```

Typical access:

* Manage fee categories
* Manage fee structures
* Assign fees to learners
* Record fee payments
* View fee reports
* Generate receipts

Restrictions:

* Cannot update academic results.
* Cannot change roles.
* Cannot access other Institutions.
* Payment adjustments must be audited.

---

# Academic Staff Roles

## TEACHER

Teacher role for assigned academic responsibilities.

Scope:

```text
Institution
```

Additional access constraints:

```text
Assigned academic year
Assigned class
Assigned section
Assigned subject
Assigned learner, where applicable
```

Typical access:

* View assigned classes and sections
* View assigned learners
* Mark attendance for assigned sections
* Enter assessment results for assigned subjects/classes
* View learner academic progress for assigned context
* Create learning materials where permitted
* Generate AI-assisted teaching resources where entitled

Restrictions:

* Cannot access unassigned learners.
* Cannot access fee data unless explicitly granted.
* Cannot manage institution settings.
* Cannot manage roles.
* Cannot access other Institutions.

---

## CLASS_TEACHER

Teacher with additional responsibility for a class or section.

Scope:

```text
Institution + Class/Section Assignment
```

Typical access:

* View assigned section learner list
* Manage daily attendance for assigned section
* View section-level academic summaries
* Communicate with guardians for assigned learners, if permitted
* Review learner profile summaries

Restrictions:

* Cannot access unrelated sections.
* Cannot change learner core records unless granted.
* Cannot access sensitive guardian data beyond allowed communication fields.

---

## ACADEMIC_COORDINATOR

Academic management role within an Institution.

Scope:

```text
Institution
```

Typical access:

* Manage academic structure
* Review teacher assignments
* Review assessment schedules
* View academic reports
* Coordinate subjects and curriculum metadata
* Monitor academic progress

Restrictions:

* Cannot manage finance unless granted.
* Cannot change Organization settings.
* Cannot access platform-level operations.

---

## ATTENDANCE_MANAGER

Role for attendance operations.

Scope:

```text
Institution
```

Typical access:

* Create attendance sessions
* Mark attendance
* Correct attendance before lock
* View attendance reports
* Export attendance reports where permitted

Restrictions:

* Cannot update assessment results.
* Cannot manage learner non-attendance data unless granted.
* Locked attendance corrections require elevated permission and audit.

---

## ASSESSMENT_MANAGER

Role for assessment operations.

Scope:

```text
Institution
```

Typical access:

* Create assessments
* Update assessments
* Enter results
* Correct results before lock
* View assessment reports
* Export assessment summaries where permitted

Restrictions:

* Cannot access fee records.
* Locked result changes require elevated permission and audit.

---

# Learner and Guardian Roles

## LEARNER

Learner account role.

Scope:

```text
User-owned + Institution-linked
```

Typical access:

* View own learner profile summary
* View own attendance summary, where enabled
* View own assessment results, where enabled
* Access assigned learning content
* Use AI learning features, if entitled
* View own notifications
* Upload own learning files, if enabled

Restrictions:

* Cannot access other learners.
* Cannot access institution administrative data.
* Cannot modify official academic records.
* Cannot bypass guardian or institution policies.

---

## GUARDIAN

Guardian account role.

Scope:

```text
Guardian-Learner relationship
```

Typical access:

* View linked learners
* View allowed learner attendance summaries
* View allowed learner assessment summaries
* Receive announcements
* Receive communication from Institution
* View fee information, if permitted
* Update own contact preferences

Restrictions:

* Cannot access unlinked learners.
* Cannot access other guardians' private details.
* Cannot update official learner records unless explicitly permitted.
* Cannot access staff-only academic notes.
* Cannot access institution-wide private reports.

---

# Support and Operational Roles

## SUPPORT_AGENT

Operational support role.

Scope:

```text
Platform or Organization, depending on assignment
```

Typical access:

* View limited user metadata
* Assist with support tickets
* View operational diagnostics
* Escalate issues

Restrictions:

* Cannot change roles.
* Cannot access sensitive learner data unless explicitly authorized.
* Cannot perform financial adjustments.

---

## CONTENT_MODERATOR

Role for reviewing content safety and policy issues.

Scope:

```text
Platform or Institution, depending on assignment
```

Typical access:

* Review flagged AI outputs
* Review reported content
* Review safety moderation events
* Escalate unsafe cases

Restrictions:

* Access to learner-identifiable content must be minimized.
* Review actions must be audited.

---

# System Roles

## SYSTEM_SERVICE

Internal service role used by backend jobs or services.

Scope:

```text
System
```

Typical access:

* Process background jobs
* Send notifications
* Run OCR workflows
* Generate reports
* Execute AI workflows
* Process webhooks

Restrictions:

* Must operate only with explicit job context.
* Must include Organization and Institution metadata where applicable.
* Must not bypass tenant isolation.
* Must not use unrestricted database access casually.
* Must be auditable.

---

## INTEGRATION_SERVICE

Internal or external integration role.

Scope:

```text
Configured integration scope
```

Typical access:

* Payment provider callbacks
* Notification delivery callbacks
* Future LMS/SIS integration
* Future public API integration

Restrictions:

* Must use signed or authenticated requests.
* Must be rate-limited.
* Must validate payloads.
* Must use idempotency.
* Must be limited to approved resources.

---

# Role Scope Matrix

| Role                         | Platform Scope | Organization Scope |                Institution Scope | Ownership / Assignment Scope |
| ---------------------------- | -------------: | -----------------: | -------------------------------: | ---------------------------: |
| PLATFORM_ADMIN               |            Yes |                Yes |                              Yes |                          Yes |
| PLATFORM_SUPPORT             |        Limited |            Limited |                          Limited |                      Limited |
| PLATFORM_SECURITY_ADMIN      |            Yes |                Yes |                              Yes |                      Limited |
| PLATFORM_AI_OPS              |            Yes |            Limited |                          Limited |                      Limited |
| PLATFORM_FINANCE_ADMIN       |            Yes |            Limited |                          Limited |                           No |
| ORGANIZATION_OWNER           |             No |                Yes |          Yes, under Organization |                      Limited |
| ORGANIZATION_ADMIN           |             No |                Yes |          Yes, under Organization |                      Limited |
| ORGANIZATION_VIEWER          |             No |          Read-only |    Read-only, under Organization |                           No |
| ORGANIZATION_FINANCE_MANAGER |             No |       Finance only | Finance only, under Organization |                           No |
| INSTITUTION_ADMIN            |             No |                 No |                              Yes |                      Limited |
| INSTITUTION_STAFF_ADMIN      |             No |                 No |                          Limited |                      Limited |
| INSTITUTION_VIEWER           |             No |                 No |                        Read-only |                           No |
| INSTITUTION_FINANCE_MANAGER  |             No |                 No |                     Finance only |                           No |
| TEACHER                      |             No |                 No |                          Limited |                Assigned only |
| CLASS_TEACHER                |             No |                 No |                          Limited |  Assigned class/section only |
| ACADEMIC_COORDINATOR         |             No |                 No |                    Academic only |                      Limited |
| ATTENDANCE_MANAGER           |             No |                 No |                  Attendance only |                      Limited |
| ASSESSMENT_MANAGER           |             No |                 No |                  Assessment only |                      Limited |
| LEARNER                      |             No |                 No |                          Limited |             Own records only |
| GUARDIAN                     |             No |                 No |                          Limited |         Linked learners only |
| SUPPORT_AGENT                |        Limited |            Limited |                          Limited |                      Limited |
| CONTENT_MODERATOR            |        Limited |            Limited |                          Limited |                      Limited |
| SYSTEM_SERVICE               |       Internal |         Contextual |                       Contextual |                   Contextual |
| INTEGRATION_SERVICE          |             No |         Contextual |                       Contextual |                   Contextual |

---

# Permission Naming Standard

Permissions must use dot notation:

```text
resource.action
```

Examples:

```text
organization.read
organization.update
institution.read
institution.update
learner.create
learner.read
learner.update
guardian.link
teacher.assign
attendance.mark
assessment.result.update
fee.payment.record
file.upload
ai.insight.generate
audit.read
```

Use lowercase.

Use singular resource names unless the domain requires otherwise.

---

# Permission Categories

## Organization Permissions

```text
organization.create
organization.read
organization.update
organization.deactivate
organization.settings.read
organization.settings.update
organization.users.read
organization.users.invite
organization.memberships.update
organization.audit.read
organization.reports.read
```

---

## Institution Permissions

```text
institution.create
institution.read
institution.update
institution.deactivate
institution.settings.read
institution.settings.update
institution.users.read
institution.users.invite
institution.audit.read
institution.reports.read
```

---

## Academic Permissions

```text
academicYear.create
academicYear.read
academicYear.update
academicYear.archive

class.create
class.read
class.update
class.archive

section.create
section.read
section.update
section.archive

subject.create
subject.read
subject.update
subject.archive

timetable.create
timetable.read
timetable.update
timetable.delete
```

---

## Learner Permissions

```text
learner.create
learner.read
learner.update
learner.archive
learner.assignClass
learner.transfer
learner.report.read
learner.report.export
```

---

## Guardian Permissions

```text
guardian.create
guardian.read
guardian.update
guardian.archive
guardian.linkLearner
guardian.unlinkLearner
guardian.communication.read
```

---

## Teacher Permissions

```text
teacher.create
teacher.read
teacher.update
teacher.archive
teacher.assign
teacher.unassign
teacher.report.read
```

---

## Attendance Permissions

```text
attendance.session.create
attendance.session.read
attendance.session.update
attendance.session.lock
attendance.record.mark
attendance.record.update
attendance.report.read
attendance.report.export
```

---

## Assessment Permissions

```text
assessment.create
assessment.read
assessment.update
assessment.archive
assessment.lock
assessment.result.create
assessment.result.read
assessment.result.update
assessment.report.read
assessment.report.export
```

---

## Fee Permissions

```text
fee.category.create
fee.category.read
fee.category.update
fee.structure.create
fee.structure.read
fee.structure.update
fee.assignment.create
fee.assignment.read
fee.assignment.update
fee.payment.record
fee.payment.read
fee.payment.update
fee.report.read
fee.report.export
```

---

## Communication Permissions

```text
announcement.create
announcement.read
announcement.update
announcement.archive
message.send
message.read
notification.read
notification.manage
```

---

## File Permissions

```text
file.upload
file.read
file.delete
file.download
file.scan.read
```

---

## AI Permissions

```text
ai.insight.generate
ai.insight.read
ai.assessmentFeedback.generate
ai.communicationDraft.generate
ai.ocr.create
ai.ocr.read
ai.prompt.read
ai.prompt.update
ai.usage.read
```

---

## Administration Permissions

```text
admin.dashboard.read
admin.users.read
admin.users.update
admin.organizations.read
admin.organizations.create
admin.institutions.read
admin.institutions.create
admin.audit.read
admin.featureFlags.read
admin.featureFlags.update
```

---

## Security Permissions

```text
security.audit.read
security.sessions.revoke
security.user.suspend
security.user.reactivate
security.impersonation.start
security.impersonation.stop
security.policy.read
security.policy.update
```

---

# Default Role-to-Permission Mapping

The final mapping may evolve, but MVP implementation must start from explicit mappings.

## PLATFORM_ADMIN

```text
organization.create
organization.read
organization.update
organization.deactivate
institution.create
institution.read
institution.update
institution.deactivate
admin.dashboard.read
admin.users.read
admin.users.update
admin.organizations.read
admin.organizations.create
admin.institutions.read
admin.institutions.create
admin.audit.read
admin.featureFlags.read
admin.featureFlags.update
security.audit.read
security.user.suspend
security.user.reactivate
ai.usage.read
```

---

## ORGANIZATION_OWNER

```text
organization.read
organization.update
organization.settings.read
organization.settings.update
organization.users.read
organization.users.invite
organization.memberships.update
organization.audit.read
organization.reports.read
institution.create
institution.read
institution.update
institution.users.read
institution.users.invite
institution.reports.read
```

---

## ORGANIZATION_ADMIN

```text
organization.read
organization.update
organization.settings.read
organization.users.read
organization.users.invite
organization.memberships.update
organization.audit.read
organization.reports.read
institution.read
institution.update
institution.users.read
institution.users.invite
institution.reports.read
```

---

## INSTITUTION_ADMIN

```text
institution.read
institution.update
institution.settings.read
institution.settings.update
institution.users.read
institution.users.invite
institution.audit.read
institution.reports.read
academicYear.create
academicYear.read
academicYear.update
academicYear.archive
class.create
class.read
class.update
class.archive
section.create
section.read
section.update
section.archive
subject.create
subject.read
subject.update
subject.archive
learner.create
learner.read
learner.update
learner.archive
learner.assignClass
guardian.create
guardian.read
guardian.update
guardian.archive
guardian.linkLearner
guardian.unlinkLearner
teacher.create
teacher.read
teacher.update
teacher.assign
teacher.unassign
attendance.session.create
attendance.session.read
attendance.record.mark
attendance.record.update
attendance.report.read
assessment.create
assessment.read
assessment.update
assessment.result.create
assessment.result.read
assessment.result.update
assessment.report.read
announcement.create
announcement.read
announcement.update
file.upload
file.read
file.download
file.delete
ai.insight.generate
ai.insight.read
```

---

## TEACHER

```text
institution.read
class.read
section.read
subject.read
learner.read
attendance.session.read
attendance.record.mark
assessment.read
assessment.result.create
assessment.result.read
assessment.result.update
announcement.read
file.upload
file.read
file.download
ai.assessmentFeedback.generate
ai.communicationDraft.generate
```

Scope restriction:

```text
Assigned academic year, class, section, subject, and learner context only.
```

---

## CLASS_TEACHER

Includes TEACHER permissions plus:

```text
attendance.session.create
attendance.record.update
learner.report.read
guardian.communication.read
message.send
```

Scope restriction:

```text
Assigned class or section only.
```

---

## GUARDIAN

```text
learner.read
learner.report.read
attendance.report.read
assessment.report.read
guardian.communication.read
announcement.read
notification.read
fee.payment.read
```

Scope restriction:

```text
Linked learners only.
```

---

## LEARNER

```text
learner.read
learner.report.read
assessment.result.read
attendance.report.read
announcement.read
notification.read
file.upload
file.read
file.download
ai.insight.read
```

Scope restriction:

```text
Own learner record only.
```

---

# Policy-Based Authorization

RBAC must be implemented through policy checks.

Recommended authorization flow:

```text
Authentication Guard
    ↓
Request Context Resolver
    ↓
Membership Resolver
    ↓
Role Resolver
    ↓
Permission Guard
    ↓
Scope Guard
    ↓
Resource Policy
    ↓
Service Method
```

A permission guard checks whether the user has the required permission.

A scope guard checks whether the user has that permission in the requested Organization or Institution.

A resource policy checks ownership, assignment, or relationship rules.

---

# Resource Policy Examples

## Learner Read Policy

A user may read learner data if one of the following is true:

1. User is Platform Admin with permitted operational purpose.
2. User is Organization Owner/Admin for the learner's Organization.
3. User is Institution Admin for the learner's Institution.
4. User is Teacher assigned to the learner's class, section, or subject.
5. User is Guardian linked to the learner.
6. User is the Learner account linked to the learner profile.

All cases must still validate Organization and Institution boundaries.

---

## Attendance Update Policy

A user may update attendance if:

1. User has `attendance.record.update`.
2. User belongs to the same Institution.
3. Attendance session belongs to the same Institution.
4. User is Institution Admin, Attendance Manager, assigned Teacher, or assigned Class Teacher.
5. Attendance session is not locked, unless user has elevated correction permission.

Locked attendance updates must be audited.

---

## Assessment Result Update Policy

A user may update assessment results if:

1. User has `assessment.result.update`.
2. Assessment belongs to the same Institution.
3. User is Institution Admin, Assessment Manager, or assigned Teacher for the subject/class.
4. Assessment is not locked, unless elevated correction permission is granted.

Locked result changes must be audited.

---

## Guardian-Learner Link Policy

A Guardian may access learner data only when:

1. A verified Guardian-Learner relationship exists.
2. The relationship is active.
3. The requested data type is allowed for guardian access.
4. The learner belongs to the requested Institution.
5. The Institution allows guardian access for that feature.

---

## AI Insight Generation Policy

A user may generate AI insight if:

1. User is authenticated.
2. User has `ai.insight.generate`.
3. User has access to the learner or resource context.
4. Resource belongs to the requested Organization and Institution.
5. Feature is enabled.
6. Entitlement is active.
7. Usage limit is not exceeded.
8. Input data passes privacy and safety checks.

---

# Denied Operations

The following must be denied by default:

* Cross-Organization resource access
* Cross-Institution resource access
* Access to unassigned learners by teachers
* Guardian access to unlinked learners
* Learner access to another learner
* Role escalation by non-authorized users
* Self-assignment of admin permissions
* Removing the last active admin
* Updating locked attendance without elevated permission
* Updating locked assessment results without elevated permission
* Exporting sensitive data without export permission
* Running AI workflows on unauthorized data
* Generating signed file URLs without file access permission

---

# Membership Model

A membership represents a user's access to an Organization or Institution.

Recommended fields:

```text
membershipId
userId
organizationId
institutionId
role
status
permissionsOverride
createdAt
updatedAt
createdBy
```

Rules:

* `organizationId` is required for Organization and Institution memberships.
* `institutionId` is required for Institution-scoped memberships.
* Platform roles may not require `organizationId` or `institutionId`.
* Membership status must be active before permissions apply.
* A user may have multiple memberships.
* A user may have different roles in different Institutions.
* Permissions override should be used sparingly and audited.

---

# Role Assignment Rules

Only authorized roles may assign or remove roles.

Examples:

| Actor Role         | May Assign                                                             |
| ------------------ | ---------------------------------------------------------------------- |
| PLATFORM_ADMIN     | Platform, Organization, and Institution roles                          |
| ORGANIZATION_OWNER | Organization Admin, Organization Viewer, Institution Admin             |
| ORGANIZATION_ADMIN | Institution Admin, Institution Staff Admin, Teacher, Guardian, Learner |
| INSTITUTION_ADMIN  | Institution Staff Admin, Teacher, Guardian, Learner                    |
| TEACHER            | None by default                                                        |
| GUARDIAN           | None                                                                   |
| LEARNER            | None                                                                   |

Rules:

* Users cannot assign roles higher than their own authority.
* Users cannot assign Platform roles unless Platform Admin.
* Institution Admins cannot assign Organization roles.
* Teachers cannot assign roles.
* Learners and Guardians cannot assign roles.
* Removing the final active admin must be blocked.
* Role changes must be audited.

---

# Permission Override Rules

Permission overrides are risky.

Allowed use cases:

* Temporary operational access
* Support escalation
* Pilot feature access
* Compliance-driven restriction
* Emergency access

Rules:

* Overrides must be time-bound where possible.
* Overrides must be audited.
* Overrides must be visible to admins.
* Overrides must not silently bypass tenant scoping.
* Overrides must not grant Platform Admin privileges except through Platform Admin workflow.

---

# Entitlements and RBAC

RBAC decides whether a user is allowed to attempt an action.

Entitlements decide whether the Organization, Institution, or user has access to a feature.

Both must pass.

Example:

A Teacher may have:

```text
ai.communicationDraft.generate
```

But if the Institution does not have the AI communication feature enabled, the request must fail with:

```text
ENTITLEMENT_REQUIRED
```

Entitlements must not replace RBAC.

RBAC must not replace entitlements.

---

# Feature Flags and RBAC

Feature flags control rollout.

A feature flag does not grant permission.

Request must pass:

```text
Authentication
RBAC permission
Scope validation
Resource policy
Entitlement check
Feature flag availability
```

---

# Audit Requirements

Audit logs are required for:

* Role assignment
* Role removal
* Permission override
* Membership creation
* Membership deactivation
* Invitation creation
* Invitation acceptance
* Invitation revocation
* Admin access to tenant data
* Sensitive learner access by elevated roles
* Data export
* Security policy changes
* Failed privilege escalation attempts

Audit events must include:

```text
actorUserId
targetUserId
organizationId
institutionId
oldRole
newRole
permissionsChanged
action
resourceType
resourceId
requestId
ipAddress
userAgent
createdAt
```

---

# API Enforcement

Every protected endpoint must declare required permissions.

Example:

```text
GET /organizations/{organizationId}/institutions/{institutionId}/learners/{learnerId}
Required permission: learner.read
Required scope: organizationId + institutionId
Resource policy: learner access policy
```

Another example:

```text
PATCH /organizations/{organizationId}/institutions/{institutionId}/assessment-results/{assessmentResultId}
Required permission: assessment.result.update
Required scope: organizationId + institutionId
Resource policy: assessment result update policy
Audit: required
```

---

# Backend Implementation Guidance

Preferred NestJS implementation pattern:

```text
JwtAuthGuard
MembershipGuard
PermissionsGuard
TenantScopeGuard
ResourcePolicyGuard
```

Recommended decorators:

```ts
@RequirePermissions('learner.read')
@RequireScope('institution')
@RequirePolicy(LearnerAccessPolicy)
```

Avoid hardcoding role names inside service methods unless there is no alternative.

Bad:

```ts
if (user.role === 'TEACHER') {
  return learners;
}
```

Better:

```ts
await authorizationService.assertCan({
  user,
  permission: 'learner.read',
  organizationId,
  institutionId,
  resource: learner,
  policy: LearnerAccessPolicy,
});
```

---

# Database Enforcement Guidance

Application authorization is required.

Database queries must still be tenant-safe.

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

This is required even when IDs are globally unique.

---

# Background Job Enforcement

Background jobs must preserve authorization context.

Required metadata:

```text
jobId
requestedByUserId
organizationId
institutionId
requiredPermission
resourceType
resourceId
createdAt
```

Rules:

* Validate access before creating job.
* Persist tenant context with job.
* Revalidate scope before writing result.
* Do not process unscoped cross-tenant jobs.
* Audit sensitive job results.

---

# AI Authorization

AI workflows must follow RBAC.

Examples:

| AI Workflow         | Required Permission              | Additional Checks              |
| ------------------- | -------------------------------- | ------------------------------ |
| Learner insight     | `ai.insight.generate`            | Learner access policy          |
| Assessment feedback | `ai.assessmentFeedback.generate` | Assessment and learner access  |
| Communication draft | `ai.communicationDraft.generate` | Audience and sender permission |
| OCR job             | `ai.ocr.create`                  | File access and entitlement    |
| AI prompt update    | `ai.prompt.update`               | Platform or AI Ops role        |

AI workflows must not retrieve context outside the user's permitted Organization and Institution.

---

# File Authorization

File access must validate:

1. User authentication.
2. Organization scope.
3. Institution scope, where applicable.
4. File ownership or related entity access.
5. Required permission such as `file.read` or `file.download`.

Signed URLs must be generated only after authorization succeeds.

---

# Report and Export Authorization

Reports and exports require explicit permissions.

Examples:

```text
attendance.report.export
assessment.report.export
learner.report.export
fee.report.export
organization.reports.read
institution.reports.read
```

Export jobs must be audited.

Exports should expire.

Sensitive fields should be masked where appropriate.

---

# MVP RBAC Requirements

The MVP must implement at minimum these roles:

```text
PLATFORM_ADMIN
ORGANIZATION_OWNER
ORGANIZATION_ADMIN
INSTITUTION_ADMIN
TEACHER
GUARDIAN
LEARNER
SYSTEM_SERVICE
```

The MVP must implement at minimum these controls:

```text
Authentication required for protected APIs
Active user check
Active Organization check
Active Institution check
Membership check
Role-to-permission mapping
Permission guard
Organization scope validation
Institution scope validation
Learner ownership check
Guardian-learner relationship check
Teacher assignment check
Sensitive action audit logging
Tenant-safe database queries
```

---

# Deferred RBAC Capabilities

The following may be deferred beyond MVP:

* Custom roles
* Fine-grained permission editor UI
* Time-bound access UI
* Emergency access workflow
* Full impersonation
* Attribute-Based Access Control
* Policy simulator
* SCIM role provisioning
* Enterprise SSO group mapping
* Delegated administration
* Cross-institution teacher assignments

Do not block these future capabilities through hardcoded assumptions.

---

# RBAC Testing Requirements

RBAC tests must cover:

* Missing authentication
* Invalid role
* Missing permission
* Inactive membership
* Organization mismatch
* Institution mismatch
* Teacher accessing unassigned learner
* Guardian accessing unlinked learner
* Learner accessing another learner
* Institution Admin accessing another Institution
* Organization Admin accessing another Organization
* Role assignment by unauthorized user
* Last admin removal prevention
* AI workflow with unauthorized learner context
* File download without access
* Export without permission

High-risk policies must have integration tests.

---

# RBAC Review Checklist

Before implementing or approving a feature, confirm:

* Required role is defined.
* Required permissions are defined.
* Scope is clear: Platform, Organization, Institution, ownership, or assignment.
* Organization boundary is validated.
* Institution boundary is validated.
* Resource ownership or relationship policy is defined.
* Entitlement requirement is defined where applicable.
* Feature flag behavior is defined where applicable.
* Audit requirement is defined.
* Tests cover denied access paths.
* API documentation lists required permissions.
* No school-only identifiers such as `schoolId` are introduced.
* Learner and Guardian terminology is used in backend/API documentation.

---

# Related Documents

* docs/11-Security/SECURITY.md
* docs/11-Security/PRIVACY.md
* docs/06-API/API_GUIDELINES.md
* docs/06-API/AUTHENTICATION.md
* docs/06-API/ENDPOINTS.md
* docs/06-API/ERROR_CODES.md
* docs/05-Database/DATABASE_SCHEMA.md
* docs/05-Database/DATA_DICTIONARY.md
* docs/04-Architecture/SYSTEM_ARCHITECTURE.md
* docs/09-Backend/AUTHENTICATION.md

