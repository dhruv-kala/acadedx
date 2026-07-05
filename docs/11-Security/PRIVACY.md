# Privacy

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the privacy principles, data handling requirements, and implementation expectations for Acadedx.

Acadedx is an AI-powered education platform built as a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

Privacy design must not assume that every customer is permanently a single school. The platform must support future Organizations managing multiple Institutions.

---

# Privacy Goals

Acadedx privacy requirements must ensure:

1. Personal data is collected only for legitimate product, educational, operational, legal, or safety purposes.
2. Learner data receives heightened protection, especially where learners may be minors.
3. Guardian access is relationship-based and limited to linked learners.
4. Teacher access is limited to assigned academic contexts.
5. Organization and Institution boundaries are preserved.
6. AI workflows do not expose or misuse personal data.
7. Data retention and deletion rules are explicit.
8. Users and institutions can understand how data is used.
9. Sensitive data is not leaked through logs, analytics, exports, AI prompts, or support workflows.
10. The architecture remains ready for future compliance requirements.

---

# Scope

This privacy document applies to:

* Web application
* Admin application
* Mobile application
* Desktop application
* Backend APIs
* Databases
* File storage
* AI workflows
* OCR workflows
* Analytics
* Notifications
* Communications
* Reports and exports
* Audit logs
* Support workflows
* Payment workflows, if enabled
* Future public APIs

---

# Platform Data Model

The privacy model follows the Acadedx hierarchy:

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

MVP constraint:

```text
One Organization → One Institution
```

Future state:

```text
One Organization → Multiple Institutions
```

Privacy controls must support both models.

---

# Privacy Principles

## 1. Data Minimization

Collect only the data required for approved product functionality.

Do not collect data because it may be useful later.

Examples:

* Do not collect government IDs unless required by a validated feature or legal need.
* Do not collect medical information unless explicitly required and approved.
* Do not collect unnecessary guardian demographic information.
* Do not send unnecessary learner profile data to AI providers.

---

## 2. Purpose Limitation

Data must be used only for the purpose for which it was collected.

Example:

Learner attendance data may be used for attendance reporting, alerts, academic insights, and institutional operations.

It must not be reused for unrelated profiling, advertising, or unrelated commercial purposes without explicit approval and consent where required.

---

## 3. Least Privilege Access

Users and services must access only the minimum personal data required.

Examples:

* A Teacher sees only assigned learner data.
* A Guardian sees only linked learner data.
* An Institution Admin sees only institution-scoped data.
* An Organization Admin sees only organization-scoped data.
* A Platform Admin access path must be audited.

---

## 4. Tenant Isolation

Privacy protection depends on strict tenant isolation.

Every data access must validate:

```text
organizationId
institutionId
```

Institution-scoped data must never be visible outside the authorized Institution unless an Organization-level role has explicit permission.

---

## 5. Transparency

Acadedx should make data usage understandable to Organizations, Institutions, and users.

Where applicable, product flows should explain:

* What data is collected
* Why it is collected
* Who can access it
* How long it may be retained
* How AI may use it
* What controls are available

---

## 6. Privacy by Design

Privacy requirements must be included during product and technical design.

Every new feature must identify:

* Data collected
* Data source
* Data owner/controller context
* Data subjects affected
* Sensitive fields
* Access rules
* Retention rules
* Deletion behavior
* AI usage, if any
* Audit requirements
* Export behavior

---

# Data Subject Categories

Acadedx may process data related to the following groups.

| Category            | Description                                                   |
| ------------------- | ------------------------------------------------------------- |
| Learners            | Students or education participants enrolled in an Institution |
| Guardians           | Parents or guardians linked to learners                       |
| Teachers            | Educators and academic staff                                  |
| Institution Staff   | Administrative or operational staff                           |
| Organization Admins | Users managing the Organization                               |
| Platform Admins     | DGIS operational users                                        |
| Support Users       | DGIS or authorized support personnel                          |
| Billing Contacts    | Users responsible for payment or subscription workflows       |

Backend and API terminology must use:

```text
Learner
Guardian
Institution
Organization
```

UI may display terms such as Student, Parent, or School where appropriate, but backend terminology must remain consistent.

---

# Data Categories

## Organization Data

Examples:

* Organization name
* Legal name
* Contact details
* Billing details
* Subscription details
* Organization settings
* Organization users and memberships
* Organization audit logs

Privacy rule:

Organization data is scoped to the Organization and authorized Platform roles.

---

## Institution Data

Examples:

* Institution name
* Institution type
* Institution code
* Address
* Contact details
* Academic configuration
* Institution settings
* Institution users
* Institution audit logs

Privacy rule:

Institution data is scoped to its parent Organization and authorized Institution roles.

---

## Learner Data

Examples:

* Name
* Admission number
* Date of birth
* Gender, if collected
* Class and section assignment
* Attendance records
* Assessment results
* Learning activity
* AI-generated insights
* Uploaded homework or documents
* Guardian links

Privacy rule:

Learner data is sensitive and may include minor data. It must be protected with stricter access controls.

---

## Guardian Data

Examples:

* Name
* Email
* Phone number
* Relationship to learner
* Notification preferences
* Linked learner relationships
* Communication records

Privacy rule:

Guardian data must be accessible only to authorized institution staff, linked learners where applicable, and the guardian themselves.

---

## Teacher Data

Examples:

* Name
* Email
* Phone number
* Employee code
* Department
* Institution assignment
* Subject/class/section assignment
* Teaching activity
* Communication records

Privacy rule:

Teacher data must be protected as staff personal data and must not be exposed unnecessarily to learners or guardians.

---

## Academic Data

Examples:

* Academic years
* Classes
* Sections
* Subjects
* Timetable
* Attendance
* Assessments
* Grades or marks
* Reports

Privacy rule:

Academic data must be accessed according to role, institution scope, and assignment relationship.

---

## AI Data

Examples:

* AI prompts
* AI responses
* Learner insights
* Assessment feedback
* Communication drafts
* AI tutor conversations, if enabled
* Model routing metadata
* Usage metadata
* Safety moderation metadata

Privacy rule:

AI data may contain personal or educational data and must follow the same tenant, access, retention, and deletion rules as source data.

---

## File and OCR Data

Examples:

* Uploaded images
* PDFs
* Learner documents
* Homework scans
* OCR extracted text
* Generated reports
* Exported files

Privacy rule:

Files and OCR output must be treated as sensitive unless explicitly marked public.

---

## Payment and Billing Data

Payment and billing data may be included if commerce or fee workflows are enabled.

Examples:

* Subscription plan
* Invoice records
* Payment status
* Payment references
* Fee payment records
* Receipt metadata

Privacy rule:

Do not store raw card data. Use PCI-compliant payment providers for payment processing.

---

# Sensitive Data Classification

Acadedx data should be classified by sensitivity.

| Classification   | Examples                                                                  | Handling Requirement                                 |
| ---------------- | ------------------------------------------------------------------------- | ---------------------------------------------------- |
| Public           | Public marketing content, public help docs                                | Normal protection                                    |
| Internal         | Feature flags, system metadata                                            | Authenticated internal access                        |
| Confidential     | Organization and Institution records                                      | Role and scope access                                |
| Sensitive        | Learner, guardian, teacher, assessment, attendance, fee records           | Strict access control and audit where needed         |
| Highly Sensitive | Minor learner data, private documents, payment identifiers, security logs | Strongest protection, minimal access, audit required |

---

# Minor Learner Data

Learners may be minors.

Minor learner data must be handled with extra care.

Rules:

* Collect only necessary learner data.
* Avoid collecting highly sensitive learner data unless required.
* Limit teacher access to assigned learners.
* Limit guardian access to linked learners.
* Avoid exposing learner data in logs.
* Avoid exporting learner data unless authorized.
* Avoid sending unnecessary learner identifiers to AI providers.
* Apply strong access control to assessment and attendance records.
* Make deletion and retention rules explicit.

---

# Consent and Notice

Consent requirements depend on jurisdiction, institution policy, learner age, and product feature.

Acadedx must support privacy notices and consent tracking where required.

Consent may be required for:

* AI-powered learner personalization
* Guardian communications
* Marketing communications
* Optional data collection
* Use of uploaded documents for AI processing
* Product analytics beyond operational analytics
* Future integrations with third-party systems

Consent records should include:

```text
consentId
userId
learnerId
guardianId
organizationId
institutionId
consentType
status
version
source
createdAt
withdrawnAt
```

---

# Guardian Consent

Where learners are minors, guardian consent may be required for some features.

Examples:

* Learner AI personalization
* Direct learner account creation
* External communication channels
* Optional document uploads
* Data exports
* Third-party integrations

Guardian consent must be linked to:

```text
guardianId
learnerId
institutionId
organizationId
```

A Guardian must not be able to grant consent for an unlinked learner.

---

# Data Access Rules

## Learner Access

A Learner may access:

* Own learner profile summary
* Own academic progress where enabled
* Own attendance summary where enabled
* Own assessment results where enabled
* Own AI learning activity where enabled
* Own files where permitted
* Own notifications

A Learner must not access:

* Other learners
* Other guardians
* Teacher private notes
* Institution admin data
* Organization admin data
* Staff-only reports

---

## Guardian Access

A Guardian may access:

* Linked learner profile summary
* Linked learner attendance summary, if allowed
* Linked learner assessment summary, if allowed
* Linked learner fee information, if allowed
* Institution announcements relevant to linked learner
* Guardian communication history
* Own notification preferences

A Guardian must not access:

* Unlinked learners
* Other guardians' private details
* Full institution reports
* Teacher private records
* Staff-only disciplinary notes unless explicitly allowed
* Cross-institution learner data unless a valid link exists

---

## Teacher Access

A Teacher may access:

* Assigned classes
* Assigned sections
* Assigned subjects
* Learners in assigned teaching context
* Attendance for assigned sections
* Assessment workflows for assigned subjects/classes
* Academic resources needed for teaching

A Teacher must not access:

* Unassigned learner records
* Fee data unless explicitly permitted
* Organization-level settings
* Institution-wide sensitive reports unless granted
* Guardian private data beyond educational communication needs

---

## Institution Admin Access

An Institution Admin may access:

* Institution profile
* Institution academic data
* Institution learners
* Institution guardians
* Institution teachers
* Attendance and assessment records
* Institution reports
* Institution settings
* Institution audit logs where permitted

An Institution Admin must not access:

* Other Institutions
* Other Organizations
* Platform-wide administrative data
* Payment provider secrets
* Internal platform security data unless explicitly permitted

---

## Organization Admin Access

An Organization Admin may access:

* Organization profile
* Organization users
* Institutions under the Organization
* Organization-level settings
* Organization-level reports
* Institution summaries under the Organization
* Organization audit logs where permitted

An Organization Admin must not access:

* Other Organizations
* Platform-wide system data
* Sensitive learner-level data unless explicitly permitted by role and policy

---

## Platform Admin Access

Platform Admins may access broad data only for legitimate operational, support, security, or compliance purposes.

Platform Admin access must be:

* Role-controlled
* Permission-controlled
* Audited
* Logged
* Reviewed where practical

Platform Admins must not use access for unrelated purposes.

---

# AI Privacy Requirements

AI workflows are privacy-sensitive because prompts and context may include personal or educational data.

AI workflows must follow:

* Data minimization
* Tenant isolation
* Access control
* Consent requirements, where applicable
* Entitlement checks
* Usage limits
* Safe logging
* Output safety review where applicable

---

# AI Context Minimization

AI requests should include only the minimum context needed.

Bad:

```text
Send full learner profile, all attendance records, all assessment records, guardian contact details, and full institution metadata for a simple grammar explanation.
```

Good:

```text
Send only the relevant question, subject, grade/class level, and limited learning context required to generate the answer.
```

---

# AI Provider Data Handling

When using external AI providers:

* Do not send secrets.
* Do not send unnecessary personal data.
* Do not send raw tenant-wide data unless required and approved.
* Prefer pseudonymized or minimized context.
* Track provider, model, request type, and usage metadata.
* Follow retention and deletion requirements.
* Ensure provider usage aligns with DGIS legal and contractual obligations.

---

# AI Logs and Prompt Storage

AI logs may contain sensitive content.

Rules:

* Do not log raw prompts by default.
* Do not log raw AI conversations by default.
* Store prompt templates separately from user prompt content.
* Protect prompt execution logs.
* Mask learner and guardian identifiers where possible.
* Limit access to AI logs.
* Audit access to sensitive AI traces.
* Define retention for AI traces.

---

# AI-Generated Learner Insights

AI-generated learner insights must be treated as sensitive educational data.

Rules:

* Insights must be institution-scoped.
* Insights must be linked to source data where practical.
* Insights must be labeled as AI-generated.
* Insights must not be treated as final authoritative decisions.
* Human review may be required for high-impact use.
* Do not expose insights to guardians or learners unless product policy allows it.
* Do not generate insights from unauthorized data.

---

# OCR Privacy

OCR output may expose sensitive personal or academic information.

Rules:

* OCR jobs must validate file ownership.
* OCR jobs must validate Organization and Institution scope.
* OCR extracted text must inherit source file access rules.
* OCR text must not be logged casually.
* OCR output must be deleted or retained according to retention policy.
* OCR correction workflows must preserve audit records where needed.

---

# File Privacy

Files must be private by default.

Rules:

* Store ownership metadata.
* Store Organization and Institution metadata.
* Use signed URLs for private access.
* Expire signed URLs.
* Validate access before generating URLs.
* Do not expose raw storage keys.
* Do not make files public unless explicitly required.
* Delete or purge files according to retention policy.
* Scan files where practical.

---

# Analytics Privacy

Analytics should be privacy-safe by design.

Preferred approach:

* Use aggregate metrics where possible.
* Avoid unnecessary personal identifiers.
* Restrict learner-level analytics to authorized users.
* Apply tenant scoping to analytics queries.
* Avoid cross-tenant analytics unless anonymized and approved.
* Do not expose small-group analytics where re-identification risk is high.

Examples of acceptable analytics:

* Institution attendance trends
* Class-level assessment summary
* AI usage count by Institution
* Feature usage by Organization

Examples requiring stricter controls:

* Individual learner risk prediction
* Guardian behavior analytics
* Teacher performance analytics
* Cross-institution learner comparison

---

# Reports and Exports

Reports and exports are high-risk privacy operations.

Rules:

* Require explicit permission.
* Validate Organization and Institution scope.
* Validate row-level access.
* Audit export generation.
* Use short-lived signed URLs.
* Expire exported files.
* Mask sensitive fields where appropriate.
* Include only requested fields.
* Prevent bulk export by unauthorized users.
* Rate-limit export operations.

Export audit records should include:

```text
exportId
requestedByUserId
organizationId
institutionId
reportType
filters
recordCount
fileId
createdAt
expiresAt
```

---

# Communication Privacy

Communication workflows must protect recipient privacy.

Examples:

* Announcements
* Guardian messages
* Teacher messages
* Attendance alerts
* Assessment alerts
* Fee reminders
* Email notifications
* SMS notifications
* In-app notifications

Rules:

* Validate recipient eligibility.
* Avoid exposing other recipients unnecessarily.
* Use BCC or provider-safe batching where needed.
* Do not include sensitive learner details in subject lines.
* Allow communication preferences where applicable.
* Log delivery metadata, not full sensitive content unless required.
* Respect consent and opt-out rules where applicable.

---

# Notification Privacy

Notifications should contain minimal sensitive information.

Bad:

```text
Riya Sharma scored 12/50 in Mathematics and was absent for 9 days this month.
```

Better:

```text
A new academic update is available for your linked learner.
```

Detailed data should be accessed only after authenticated login.

---

# Data Retention

Retention rules must balance:

* Educational record needs
* Institution policy
* Legal requirements
* Operational needs
* Audit requirements
* User privacy
* Storage cost
* AI safety and debugging needs

Retention periods should be configurable where institution policy requires.

---

# Suggested Retention Categories

| Data Type                | Suggested MVP Retention Approach                         |
| ------------------------ | -------------------------------------------------------- |
| User account             | Retain while account is active                           |
| Organization records     | Retain while customer relationship exists                |
| Institution records      | Retain while Institution is active                       |
| Learner academic records | Retain according to Institution policy                   |
| Attendance records       | Retain according to Institution policy                   |
| Assessment records       | Retain according to Institution policy                   |
| Guardian contact data    | Retain while relationship is active                      |
| Teacher records          | Retain while employment/account relationship is active   |
| Files                    | Retain according to file category and policy             |
| OCR text                 | Retain according to source file policy                   |
| AI conversations         | Retain for limited period unless product requires longer |
| AI generated insights    | Retain according to academic insight policy              |
| Audit logs               | Retain for security/compliance period                    |
| Payment records          | Retain according to finance/legal requirements           |

Final retention periods must be confirmed with legal and business requirements before production launch.

---

# Data Deletion

Deletion behavior must be explicit.

Deletion types:

| Type             | Description                                                    |
| ---------------- | -------------------------------------------------------------- |
| Soft delete      | Mark record inactive or deleted but retain for audit/recovery  |
| Hard delete      | Permanently remove data                                        |
| Anonymization    | Remove personal identifiers while preserving aggregate utility |
| Pseudonymization | Replace identifiers with indirect references                   |
| Archive          | Move inactive data to restricted long-term storage             |

---

# Deletion Rules

General rules:

* Use soft delete for operational records where auditability is required.
* Use hard delete only when safe and legally permitted.
* Do not orphan dependent records.
* Do not delete audit logs casually.
* Deleting a learner must consider academic record retention.
* Deleting a guardian must preserve required historical communication records where needed.
* Deleting a file must eventually purge storage object.
* Deleting AI source data should trigger review of AI-derived data.
* Deletion must be tenant-scoped.

---

# Data Export and Access Requests

Acadedx should support future data access and export requests.

Potential request types:

* User profile export
* Learner record export
* Guardian-linked learner summary
* Institution data export
* Organization data export
* AI interaction export
* File export

Access requests must validate:

* Identity
* Role
* Relationship
* Organization scope
* Institution scope
* Legal authority, where applicable

---

# Data Correction

Authorized users should be able to correct inaccurate data.

Examples:

* User profile correction
* Guardian contact correction
* Learner profile correction
* Class or section assignment correction
* Attendance correction
* Assessment result correction

Correction of official records must follow role permissions and audit rules.

---

# Data Sharing

Data may be shared only with authorized parties.

Potential recipients:

* Authorized Organization users
* Authorized Institution users
* Linked Guardians
* Assigned Teachers
* DGIS support or operations users
* Approved service providers
* Payment providers, where applicable
* Notification providers, where applicable
* AI providers, where applicable

Data sharing must follow purpose limitation and minimum necessary principles.

---

# Third-Party Providers

Third-party providers may include:

* Cloud hosting providers
* Email providers
* SMS providers
* Push notification providers
* Payment providers
* AI providers
* OCR providers
* Storage providers
* Analytics providers

Requirements:

* Use providers with appropriate security controls.
* Share only necessary data.
* Avoid sending sensitive data to providers unless required.
* Use contracts or terms aligned with privacy obligations.
* Store provider credentials securely.
* Audit provider integrations where needed.

---

# Cross-Border Data Considerations

Acadedx may eventually serve Organizations and Institutions in multiple jurisdictions.

Future architecture should support:

* Region-specific hosting
* Region-specific retention policies
* Data residency requirements
* Provider selection by region
* Tenant-specific compliance configuration

MVP does not need full multi-region data residency unless required by the launch market.

---

# Children and Education Privacy Considerations

Because Acadedx processes education-related data and may process minor learner data, the platform must be designed conservatively.

Implementation must support:

* Guardian relationship validation
* Institution-controlled access
* Limited data collection
* Restricted data exports
* Strong audit logs
* AI data minimization
* Privacy-safe notifications
* Configurable retention policies

Specific legal obligations depend on launch geography and customer contracts.

Legal review is required before production launch.

---

# Marketing and Product Analytics

Product analytics must not override privacy expectations.

Rules:

* Do not use learner personal data for advertising.
* Do not use minor learner data for behavioral advertising.
* Avoid tracking sensitive learner behavior unless needed for learning features.
* Prefer aggregate and pseudonymized analytics.
* Respect opt-out preferences where required.
* Keep marketing consent separate from product-required notifications.

---

# Support Access Privacy

Support workflows must use least privilege.

Support users should access:

* Account metadata
* Error diagnostics
* Request IDs
* Safe logs
* Non-sensitive configuration
* Support tickets

Support users should not access:

* Full learner records unless necessary
* Full guardian contact data unless necessary
* Raw AI conversations unless explicitly permitted
* Uploaded documents unless necessary
* Payment secrets
* Security credentials

Support access to sensitive tenant data must be audited.

---

# Audit Logs and Privacy

Audit logs are necessary for security and compliance but may contain personal data.

Rules:

* Audit logs must be protected.
* Audit logs must not contain secrets.
* Audit logs should avoid unnecessary personal data.
* Audit logs should preserve security-relevant facts.
* Audit log access must be restricted.
* Audit log retention must be defined.
* Audit logs should not be editable by normal admins.

---

# Logging and Privacy

Application logs must not include sensitive personal data by default.

Log:

```text
requestId
userId
organizationId
institutionId
method
path
statusCode
durationMs
errorCode
safe diagnostic metadata
```

Do not log:

```text
passwords
tokens
OTP values
full learner records
full guardian records
raw AI prompts
raw AI conversations
OCR text
uploaded document contents
payment secrets
private file URLs
```

---

# Privacy Incident Examples

Potential privacy incidents include:

* Learner data exposed to wrong Institution
* Guardian viewing unlinked learner data
* Teacher viewing unassigned learner records
* AI prompt includes unauthorized tenant data
* Export contains records outside requested scope
* Logs contain sensitive learner data
* File signed URL generated for unauthorized user
* Email notification sent to wrong recipient
* Platform support user accesses data without valid reason
* Payment data exposed beyond finance role

Privacy incidents must follow the security incident response process.

---

# Privacy Testing Requirements

Privacy tests must cover:

* Guardian cannot access unlinked learner.
* Learner cannot access another learner.
* Teacher cannot access unassigned learner.
* Institution Admin cannot access another Institution.
* Organization Admin cannot access another Organization.
* File access is denied outside scope.
* AI context retrieval is tenant-scoped.
* Reports do not include unauthorized records.
* Exports are scoped correctly.
* Notifications are sent only to eligible recipients.
* Sensitive fields are not returned in API responses.
* Sensitive data is not logged in common error paths.

---

# Privacy Review Checklist

Before approving a feature, confirm:

* Data collected is necessary.
* Purpose is clear.
* Organization scope is enforced.
* Institution scope is enforced.
* Role and permission access is defined.
* Ownership or relationship rules are defined.
* Minor learner data risks are considered.
* Guardian access rules are defined.
* Teacher assignment access rules are defined.
* AI usage is minimized and justified.
* Logs do not expose sensitive data.
* Reports and exports are scoped and audited.
* Retention behavior is defined.
* Deletion behavior is defined.
* Consent requirement is evaluated.
* Third-party sharing is documented.
* OpenAPI documentation does not expose sensitive examples.

---

# MVP Privacy Requirements

The MVP must implement at minimum:

```text
Organization and Institution data scoping
Learner data access controls
Guardian-learner relationship checks
Teacher assignment checks
Role and permission enforcement
Sensitive field filtering
Secure file access
Signed URLs for private files
AI context minimization
OCR access control
Privacy-safe logs
Audit logs for sensitive actions
Report/export scoping
Notification recipient validation
Basic consent record support where required
Configurable retention placeholders
Privacy tests for core access boundaries
```

---

# Deferred Privacy Capabilities

The following capabilities may be deferred beyond MVP:

* Full self-service data subject request portal
* Advanced consent management UI
* Regional data residency controls
* Customer-managed encryption keys
* Automated data discovery and classification
* Advanced anonymization pipeline
* Privacy impact assessment automation
* Enterprise DPA workflow
* Full legal hold management
* Advanced minor consent workflows by jurisdiction

Do not block these future capabilities through hardcoded assumptions.

---

# Related Documents

* docs/11-Security/SECURITY.md
* docs/11-Security/RBAC.md
* docs/06-API/API_GUIDELINES.md
* docs/06-API/AUTHENTICATION.md
* docs/06-API/ENDPOINTS.md
* docs/06-API/ERROR_CODES.md
* docs/05-Database/DATABASE_SCHEMA.md
* docs/05-Database/DATA_DICTIONARY.md
* docs/07-AI/AI_ENGINE.md
* docs/07-AI/CONTENT_MODERATION.md
* docs/04-Architecture/SYSTEM_ARCHITECTURE.md
