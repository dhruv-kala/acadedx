# Organizations Specification

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Product Management  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This specification defines the Organization domain for Acadedx.

An Organization is the business owner, education group, trust, company, enterprise customer, or administrative owner of one or more Institutions.

Acadedx is currently a school-focused MVP, but the platform architecture must support:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

The Organization must be treated as a first-class product, backend, database, API, security, and billing concept.

---

# Product Context

In the MVP:

```text
One Organization → One Institution
```

In the future:

```text
One Organization → Multiple Institutions
```

This means the MVP may feel like a single-school system to users, but the underlying platform must not hardcode that assumption.

Correct backend terminology:

```text
organizationId
institutionId
```

Incorrect backend terminology:

```text
schoolId
```

---

# Definition

An Organization represents the legal, commercial, administrative, or ownership entity responsible for one or more Institutions.

Examples:

```text
Education group
School trust
Private school company
Coaching business
Academy chain
University group
Training enterprise
NGO education program
Government education body
```

An Organization may own or manage one Institution in MVP and multiple Institutions in later releases.

---

# Organization Responsibilities

An Organization may be responsible for:

* Institution ownership
* Institution creation and management
* Organization-level users
* Organization-level roles
* Billing and subscriptions
* Feature entitlements
* Organization-level settings
* Multi-institution reporting
* Legal and compliance ownership
* Data governance
* Audit visibility
* Support and administration

MVP may implement only a subset of these responsibilities.

---

# Organization vs Institution

| Concept          | Meaning                                 | Example               |
| ---------------- | --------------------------------------- | --------------------- |
| Organization     | Business owner or customer entity       | DGIS Education Group  |
| Institution      | Educational unit where learning happens | Acadedx Public School |
| Institution Type | Type of educational unit                | SCHOOL                |
| Learner          | Person receiving education              | Student in UI         |
| Guardian         | Parent or guardian linked to learner    | Parent in UI          |

Important rule:

```text
Organization is not the same as Institution.
Institution is not the same as School.
School is one Institution type.
```

---

# MVP Rule

The MVP supports:

```text
One Organization → One Institution
```

This constraint must be enforced by business logic, not by schema limitations that prevent future expansion.

Bad future-limiting design:

```text
User has one schoolId.
School owns all data.
```

Correct design:

```text
Organization owns Institutions.
Institution owns academic operations.
User access is granted through scoped memberships.
```

---

# Future Rule

Future Acadedx must support:

```text
One Organization → Multiple Institutions
```

Examples:

```text
One education group manages five schools.
One coaching company manages multiple centers.
One trust manages schools in different cities.
One enterprise runs training centers across regions.
```

The data model, API, RBAC, frontend routes, AI context, and reporting must all support this direction.

---

# Organization Types

Recommended Organization type enum:

```text
EDUCATION_GROUP
SCHOOL_TRUST
PRIVATE_COMPANY
COACHING_BUSINESS
ACADEMY_CHAIN
UNIVERSITY_GROUP
TRAINING_ENTERPRISE
NGO
GOVERNMENT_BODY
INDIVIDUAL_OWNER
OTHER
```

MVP may start with:

```text
EDUCATION_GROUP
SCHOOL_TRUST
PRIVATE_COMPANY
INDIVIDUAL_OWNER
OTHER
```

---

# Organization Status

Recommended Organization status enum:

```text
ACTIVE
PENDING_SETUP
SUSPENDED
INACTIVE
ARCHIVED
```

## ACTIVE

Organization is active and can access enabled features.

## PENDING_SETUP

Organization exists but onboarding is incomplete.

## SUSPENDED

Organization access is restricted due to administrative, billing, security, or compliance reasons.

## INACTIVE

Organization is temporarily inactive.

## ARCHIVED

Organization is no longer operational but retained for audit or historical records.

---

# Organization Core Fields

Recommended fields:

```text
id
name
legalName
type
status
email
phone
website
country
timezone
defaultCurrency
billingEmail
address
metadata
createdAt
updatedAt
deletedAt
```

---

# Organization Address Fields

Recommended address object:

```text
line1
line2
city
state
postalCode
country
```

Address should be optional for MVP unless required by onboarding, billing, or compliance.

---

# Organization Settings

Organization settings may include:

```text
defaultTimezone
defaultCurrency
defaultLanguage
enabledFeatures
billingSettings
securitySettings
notificationSettings
dataRetentionSettings
aiSettings
brandingSettings
```

MVP settings should be minimal.

Recommended MVP Organization settings:

```text
defaultTimezone
defaultCurrency
enabledFeatures
```

---

# Organization Branding

Future Organization branding may include:

```text
logo
primaryColor
accentColor
publicName
reportHeader
emailFooter
```

MVP may defer branding unless required for launch.

White-labeling should not override safety, error, warning, or security color semantics.

---

# Organization Relationships

An Organization may have:

```text
many Institutions
many Users through Memberships
many Invitations
many Settings
many Audit Logs
many Feature Entitlements
many Subscription or Billing records
many Reports
```

MVP:

```text
one Organization has one Institution
```

but schema should support many Institutions later.

---

# Organization Data Ownership

Organization is the top-level tenant boundary for customer-owned data.

Organization-scoped data includes:

* Organization profile
* Organization settings
* Organization users and memberships
* Institution list
* Organization-level audit logs
* Organization-level reports
* Billing and subscription records
* Feature entitlements

Institution-scoped data must still include `organizationId` for tenant safety.

---

# Institution Relationship

Each Institution must belong to one Organization.

Institution fields must include:

```text
organizationId
```

Institution creation must validate:

```text
Organization exists.
Organization is active or pending setup.
User has permission to create Institution.
MVP single-Institution limit is not violated unless multi-Institution feature is enabled.
```

---

# User and Membership Model

Users are global identities.

Organization access is granted through memberships.

Recommended membership model:

```text
userId
organizationId
institutionId
role
status
permissionsOverride
createdAt
updatedAt
```

For Organization-scoped roles:

```text
organizationId is required
institutionId may be null
```

For Institution-scoped roles:

```text
organizationId is required
institutionId is required
```

Platform roles may not require Organization or Institution scope.

---

# Organization Roles

Recommended Organization-level roles:

```text
ORGANIZATION_OWNER
ORGANIZATION_ADMIN
ORGANIZATION_VIEWER
ORGANIZATION_FINANCE_MANAGER
```

MVP roles:

```text
ORGANIZATION_OWNER
ORGANIZATION_ADMIN
```

---

# Organization Permissions

Recommended Organization permissions:

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
institution.create
institution.read
institution.update
institution.deactivate
```

MVP should implement only required permissions.

---

# Organization Access Rules

## Platform Admin

Can:

* Create Organization
* View Organizations
* Update Organization
* Deactivate Organization
* View Organization audit logs
* Assist with onboarding

Must be audited for sensitive actions.

## Organization Owner

Can:

* View own Organization
* Update own Organization
* Manage Organization Admins
* Manage Institutions under Organization
* View Organization-level reports where enabled
* Manage billing where enabled

Cannot:

* Access other Organizations
* Perform platform-level operations

## Organization Admin

Can:

* View own Organization
* Manage Institutions where permitted
* Invite Institution users where permitted
* View Organization users
* View Organization reports where permitted

Cannot:

* Transfer ownership unless explicitly allowed
* Access other Organizations
* Manage platform settings

---

# Organization API Endpoints

Recommended API endpoints:

```http
GET    /api/v1/organizations
POST   /api/v1/organizations
GET    /api/v1/organizations/{organizationId}
PATCH  /api/v1/organizations/{organizationId}
DELETE /api/v1/organizations/{organizationId}

GET    /api/v1/organizations/{organizationId}/institutions
POST   /api/v1/organizations/{organizationId}/institutions

GET    /api/v1/organizations/{organizationId}/users
POST   /api/v1/organizations/{organizationId}/invitations

GET    /api/v1/organizations/{organizationId}/settings
PATCH  /api/v1/organizations/{organizationId}/settings

GET    /api/v1/organizations/{organizationId}/audit-logs
```

MVP required:

```http
POST  /api/v1/organizations
GET   /api/v1/organizations/{organizationId}
PATCH /api/v1/organizations/{organizationId}
GET   /api/v1/organizations/{organizationId}/institutions
```

Platform Admin listing may be required:

```http
GET /api/v1/organizations
```

---

# Organization Frontend Routes

Recommended frontend routes:

```text
/admin/organizations
/admin/organizations/[organizationId]

/app/organizations/[organizationId]/dashboard
/app/organizations/[organizationId]/settings
/app/organizations/[organizationId]/users
/app/organizations/[organizationId]/institutions
/app/organizations/[organizationId]/audit-logs
```

MVP required:

```text
/admin/organizations
/admin/organizations/[organizationId]
/app/organizations/[organizationId]/institutions
```

Organization dashboard may redirect to the single Institution dashboard in MVP.

---

# Organization Onboarding

Recommended onboarding flow:

```text
1. Create Organization.
2. Create first Institution.
3. Configure academic year.
4. Configure classes, sections, and subjects.
5. Invite Institution Admins and Teachers.
6. Add Learners and Guardians.
```

Organization setup fields:

```text
Organization name
Legal name, optional
Organization type
Contact email
Phone, optional
Country
Timezone
Currency
```

---

# MVP Organization Onboarding

MVP onboarding should support:

```text
Create Organization
Create first Institution
Invite initial admin
Configure active academic year
```

The UI may simplify this to “School Setup”, but backend and state must remain Organization and Institution based.

---

# Organization Validation Rules

Validation rules:

* Organization name is required.
* Organization type must be valid.
* Status must be valid.
* Email must be valid if provided.
* Phone must be valid if provided.
* Timezone must be valid.
* Currency must be valid.
* Country must be valid ISO country code where possible.
* Organization name should be unique where required by business policy.

MVP may not require legal name.

---

# Organization Uniqueness Rules

Recommended uniqueness:

```text
Organization id is globally unique.
Organization legal name may be non-unique unless compliance requires otherwise.
Organization display name may be non-unique.
Organization slug/code may be unique if introduced.
```

Avoid relying on name as an identifier.

---

# Organization Deactivation

Deactivation should be a status transition, not immediate hard delete.

Before deactivation, check:

```text
active Institutions
active subscriptions
active users
pending jobs
legal retention requirements
billing state
```

MVP may restrict Organization deletion to Platform Admin only.

---

# Organization Audit Requirements

Audit required for:

```text
organization.create
organization.update
organization.deactivate
organization.settings.update
organization.user.invite
organization.membership.update
organization.billing.update
institution.create under organization
organization.admin.access
```

Audit metadata:

```text
actorUserId
organizationId
action
resourceType
resourceId
beforeState
afterState
requestId
ipAddress
userAgent
createdAt
```

---

# Organization Security Requirements

Organization security must enforce:

* Authentication
* Active user
* Active Organization
* Membership validation
* Role and permission checks
* Scope validation
* Tenant-safe queries
* Audit logging for sensitive actions

Every Organization-scoped query must include:

```text
organizationId
```

---

# Organization Privacy Requirements

Organization data may include business contact and billing information.

Privacy rules:

* Organization data must not be visible to other Organizations.
* Organization Admins must not access other Organizations.
* Platform Admin access must be audited.
* Billing contacts and financial information must be limited to authorized roles.
* Organization-level reports must not expose learner-level sensitive data unless permitted.

---

# Organization AI Requirements

AI workflows must include Organization context.

AI usage tracking should include:

```text
organizationId
institutionId where applicable
userId
featureCode
workflow
model
provider
tokens
estimatedCost
status
createdAt
```

Organization-level AI configuration may be future scope.

MVP may support simple feature enablement only.

---

# Organization Reporting

Future Organization reports may include:

* Institution count
* Learner count across Institutions
* Teacher count across Institutions
* Attendance trends across Institutions
* Assessment trends across Institutions
* Fee summaries across Institutions
* AI usage across Institutions

MVP may defer Organization-level reporting or show a single-Institution summary.

---

# Organization Billing

Billing may be Organization-scoped.

Billing may include:

```text
subscription plan
billing contact
invoices
payments
entitlements
usage limits
```

MVP may defer billing if Acadedx starts as a sales-led or manually onboarded product.

Do not design billing at Institution-only level if Organization is the customer entity.

---

# Organization Feature Entitlements

Feature entitlements may be Organization-scoped or Institution-scoped.

Examples:

```text
attendance.enabled
assessments.enabled
fees.enabled
ai.insight.generate
ocr.extract
report.export
advanced.analytics
```

Entitlements must check feature access, not plan names.

---

# Organization Data Model Example

Example JSON:

```json
{
  "id": "org_123",
  "name": "DGIS Education Group",
  "legalName": "Digital Global Information Systems Pvt Ltd",
  "type": "EDUCATION_GROUP",
  "status": "ACTIVE",
  "email": "admin@acadedx.com",
  "phone": "+919999999999",
  "country": "IN",
  "timezone": "Asia/Kolkata",
  "defaultCurrency": "INR",
  "createdAt": "2026-07-05T10:30:00.000Z",
  "updatedAt": "2026-07-05T10:30:00.000Z"
}
```

---

# Database Requirements

Recommended table:

```text
organizations
```

Required indexes:

```text
id
status
type
createdAt
```

Optional indexes:

```text
name
country
timezone
```

Soft delete field:

```text
deletedAt
```

Avoid hard delete unless legally and technically safe.

---

# Organization Repository Rules

Repository methods must be explicit.

Good:

```ts
findOrganizationById(organizationId: string)
findActiveOrganizationById(organizationId: string)
updateOrganization(params: { organizationId: string; data: UpdateOrganizationInput })
```

Avoid:

```ts
findById(id: string)
```

where domain context is unclear.

---

# Organization Service Rules

Organization service should own:

* Organization creation
* Organization status validation
* Organization update rules
* Organization deactivation rules
* Organization settings update
* Organization audit coordination

Organization service should not own Institution academic operations.

---

# Organization Events

Recommended domain events:

```text
organization.created
organization.updated
organization.deactivated
organization.settingsUpdated
organization.userInvited
organization.membershipUpdated
```

Events must include:

```text
organizationId
actorUserId
requestId
createdAt
```

---

# Error Codes

Relevant error codes:

```text
ORG_NOT_FOUND
ORG_ALREADY_EXISTS
ORG_INACTIVE
ORG_SUSPENDED
ORG_NAME_REQUIRED
ORG_INVALID_TYPE
ORG_INVALID_STATUS
ORG_CREATION_NOT_ALLOWED
ORG_UPDATE_NOT_ALLOWED
ORG_DELETE_NOT_ALLOWED
ORG_HAS_ACTIVE_INSTITUTIONS
ORG_LIMIT_EXCEEDED

TENANT_ORGANIZATION_REQUIRED
TENANT_ORGANIZATION_MISMATCH
TENANT_CROSS_ORGANIZATION_ACCESS_DENIED
TENANT_SCOPE_VIOLATION

RBAC_PERMISSION_REQUIRED
AUTH_FORBIDDEN
```

Definitions are maintained in:

```text
docs/06-API/ERROR_CODES.md
```

---

# Testing Requirements

Organization tests must cover:

## Creation

* Platform Admin can create Organization.
* Unauthorized user cannot create Organization.
* Invalid type is rejected.
* Required name validation works.

## Reading

* Platform Admin can view Organizations.
* Organization Admin can view own Organization.
* Organization Admin cannot view another Organization.

## Updating

* Authorized user can update Organization.
* Unauthorized user cannot update Organization.
* Invalid status is rejected.
* Audit log is created.

## Deactivation

* Platform Admin can deactivate Organization.
* Deactivation with active Institutions follows policy.
* Deactivation blocks access where required.
* Audit log is created.

## Tenant Scope

* Organization-scoped queries include `organizationId`.
* Cross-Organization access is denied.
* Error message does not leak sensitive tenant details.

---

# MVP Organization Requirements

The MVP must implement:

```text
Organization entity
Organization create
Organization read
Organization update
Organization status
Organization settings placeholder
Organization membership support
Organization → Institution relationship
MVP one-Institution rule
Organization-scoped authorization
Organization audit logging for sensitive actions
Tenant-safe database queries
Organization onboarding flow
```

MVP may defer:

```text
Organization dashboard
Organization billing UI
Organization-level analytics
Organization-level reports
Organization branding
Multi-Institution management UI
Organization audit log UI
```

---

# Deferred Capabilities

The following may be deferred beyond MVP:

```text
Multiple Institutions per Organization UI
Organization-level analytics
Organization-level billing automation
Organization branding and white labeling
Organization data export portal
Organization-level compliance settings
Organization-level AI configuration
Organization-level retention policy editor
Organization-level delegated administration
Enterprise SSO and SCIM
```

Do not block these future capabilities with MVP-only shortcuts.

---

# Anti-Patterns

Avoid:

```text
Using School as top-level tenant
Using schoolId for backend/API/database design
Storing one institutionId directly on user as the only access source
Treating Organization as optional metadata
Creating Institution without Organization
Building billing only around Institution when Organization is customer owner
Hardcoding one Organization forever
Hardcoding one Institution forever
Allowing Organization Admin to access another Organization
Running Organization reports without tenant scope
```

---

# Review Checklist

Before approving Organization implementation, confirm:

* Organization is first-class.
* Organization has stable ID.
* Institution belongs to Organization.
* MVP one-Institution rule is business logic, not future-blocking schema.
* Users access Organization through memberships.
* Organization roles and permissions are defined.
* Organization routes and APIs use `organizationId`.
* No `schoolId` is introduced.
* Organization-scoped queries are tenant-safe.
* Organization update and deactivation are audited.
* Future multi-Institution support is not blocked.

---

# Related Documents

* docs/00-Overview/PROJECT_OVERVIEW.md
* docs/01-Product/PRODUCT_REQUIREMENTS.md
* docs/04-Architecture/SYSTEM_ARCHITECTURE.md
* docs/05-Database/DATABASE_SCHEMA.md
* docs/05-Database/DATA_DICTIONARY.md
* docs/06-API/ENDPOINTS.md
* docs/06-API/ERROR_CODES.md
* docs/09-Backend/MODULES.md
* docs/11-Security/RBAC.md
* docs/11-Security/PRIVACY.md
* specifications/institutions.md
