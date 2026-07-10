# Personalization

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** AI Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the personalization architecture, principles, data requirements, privacy controls, and implementation standards for Acadedx.

Personalization enables Acadedx to adapt academic insights, recommendations, learning support, dashboards, communication, and AI workflows based on authorized learner, teacher, guardian, Institution, and Organization context.

Acadedx is a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

Personalization workflows must use:

```text
organizationId
institutionId
learnerId
guardianId
teacherId
userId
```

Do not use:

```text
schoolId
studentId
parentId
```

except in explicitly documented UI labels, generated user-facing copy, or legacy adapters.

---

# Personalization Goals

Personalization in Acadedx must ensure:

1. Learners receive more relevant academic support.
2. Teachers receive useful context for assigned learners and classes.
3. Guardians receive relevant updates for linked learners only.
4. Institution Admins receive scoped operational insights.
5. Personalization respects Organization and Institution boundaries.
6. Personalization does not bypass RBAC, privacy, consent, or entitlements.
7. Learner data is protected, especially where learners may be minors.
8. AI-generated personalization is explainable and reviewable where needed.
9. MVP personalization remains limited and practical.
10. Future adaptive learning, RAG, recommendations, and analytics can evolve without redesign.

---

# Personalization Scope

Personalization may apply to:

* Learner dashboard summaries
* Learner academic recommendations
* Teacher dashboard priorities
* Guardian linked learner updates
* Institution dashboard insights
* AI learner insight generation
* AI assessment feedback
* AI communication drafts
* Report summaries
* Study recommendations
* Notification preferences
* Content recommendations
* Future adaptive learning workflows

---

# MVP Personalization Scope

The MVP should use lightweight, rule-based personalization before advanced AI personalization.

Recommended MVP personalization:

```text
Role-based dashboard content
Institution-scoped active academic year context
Learner attendance summary
Learner assessment summary
Teacher assigned class/section views
Guardian linked learner views
Institution Admin setup and operational prompts
Basic AI learner insight using attendance and assessment summaries, if AI is enabled
Basic communication draft context, if AI is enabled
```

MVP optional:

```text
Personalized learner study recommendations
AI tutor memory
Adaptive practice difficulty
Personalized worksheet generation
Long-term learner profile modeling
Cross-session AI personalization
```

Constraint:

Do not let advanced personalization delay core Organization → Institution academic operations.

---

# Personalization Non-Goals for MVP

The MVP should not attempt to implement:

* Fully adaptive learning engine
* High-stakes learner risk scoring
* Psychological or behavioral profiling
* Automated promotion or retention recommendations
* Automated disciplinary recommendations
* Cross-institution learner profiling
* Cross-organization benchmarking
* Long-term AI memory across all learner activity
* Unreviewed learner-facing recommendations for sensitive outcomes
* Personalized advertising or marketing based on learner data

These require additional privacy, safety, product, and legal review.

---

# Personalization Architecture Overview

Recommended personalization flow:

```text
Authenticated Request
    ↓
Request Context Resolution
    ↓
Organization / Institution Scope Validation
    ↓
Role and Permission Validation
    ↓
Personalization Context Retrieval
    ↓
Privacy and Consent Checks
    ↓
Rule-Based or AI-Based Personalization
    ↓
Output Validation
    ↓
Human Review, if required
    ↓
Response / Persistence
    ↓
Audit and Usage Logging, where required
```

---

# Personalization Types

Acadedx should support multiple levels of personalization.

## Role-Based Personalization

Adapts UI and workflows by role.

Examples:

```text
Institution Admin sees setup tasks and operational summaries.
Teacher sees assigned classes and pending attendance tasks.
Guardian sees linked learner updates.
Learner sees own academic summaries.
Platform Admin sees platform operations.
```

MVP:

```text
Required
```

---

## Context-Based Personalization

Adapts content based on active Organization, Institution, academic year, class, section, subject, or assignment.

Examples:

```text
Show current academic year by default.
Filter teacher dashboard to assigned sections.
Show Institution-specific settings.
Show class-specific attendance tasks.
```

MVP:

```text
Required
```

---

## Learner Academic Personalization

Adapts recommendations and insights based on learner academic data.

Examples:

```text
Attendance support suggestion
Assessment improvement area
Subject-specific practice recommendation
Learner progress summary
```

MVP:

```text
Basic summary-based only
```

---

## Communication Personalization

Adapts messages based on audience, relationship, and context.

Examples:

```text
Guardian message uses linked learner context.
Teacher message uses class or section context.
Institution announcement uses audience context.
```

MVP:

```text
Basic if communication workflows are included
```

---

## AI-Based Personalization

Uses AI to generate tailored summaries, recommendations, drafts, or explanations.

Examples:

```text
AI learner insight
AI assessment feedback
AI guardian communication draft
AI report summary
AI tutor response
```

MVP:

```text
Optional
```

---

## Long-Term Personalization

Uses accumulated learner behavior or academic history over time.

Examples:

```text
Learning preference model
Adaptive difficulty
Long-term mastery profile
Personalized study plan
AI tutor memory
```

MVP:

```text
Deferred
```

---

# Personalization Data Sources

Personalization may use approved data sources.

## Institution Context

Examples:

```text
Institution type
Institution name
Academic year
Class
Section
Subject
Institution settings
Timezone
Enabled features
```

Use:

```text
institutionId
```

not:

```text
schoolId
```

---

## Learner Context

Examples:

```text
Learner profile summary
Academic year assignment
Class and section assignment
Attendance summary
Assessment summary
Subject performance summary
Learning activity, if enabled
AI interaction summary, if enabled
```

Use:

```text
learnerId
```

not:

```text
studentId
```

---

## Guardian Context

Examples:

```text
Guardian-learner relationship
Communication preferences
Linked learners
Allowed report access
Notification settings
```

Use:

```text
guardianId
```

not:

```text
parentId
```

---

## Teacher Context

Examples:

```text
Teacher profile
Assigned classes
Assigned sections
Assigned subjects
Assigned learners
Pending attendance sessions
Pending assessment results
```

---

## Academic Context

Examples:

```text
Academic year
Class
Section
Subject
Assessment
Attendance session
Timetable
```

---

## AI and RAG Context

Examples:

```text
Approved AI result summaries
Approved RAG document snippets
OCR-confirmed text
Prompt outputs
Usage metadata
```

AI and RAG context must be explicitly approved and tenant-scoped.

---

# Restricted Data Sources

The following must not be used for personalization unless explicitly approved:

```text
Medical notes
Disciplinary records
Sensitive guardian notes
Payment details
Raw audit logs
Security events
Raw AI prompts
Raw AI conversations
Raw OCR text
Private documents
Government IDs
Highly sensitive learner records
```

Use of these data sources requires:

* Product approval
* Privacy review
* Security review
* Access policy
* Retention rule
* Audit rule

---

# Personalization Context Shape

Recommended context object:

```ts
export interface PersonalizationContext {
  requestId: string;
  userId: string;
  organizationId: string;
  institutionId?: string;
  role: string;
  permissions: string[];
  activeAcademicYearId?: string;
  learnerId?: string;
  guardianId?: string;
  teacherId?: string;
  classId?: string;
  sectionId?: string;
  subjectId?: string;
  featureCode?: string;
  consentState?: ConsentState;
}
```

Institution-scoped personalization must include:

```text
organizationId
institutionId
```

---

# Tenant Isolation Requirements

Every personalization query must include tenant scope.

Bad:

```sql
SELECT * FROM learner_performance WHERE learner_id = $1;
```

Good:

```sql
SELECT * FROM learner_performance
WHERE learner_id = $1
  AND organization_id = $2
  AND institution_id = $3;
```

This applies to:

* Learner summaries
* Attendance summaries
* Assessment summaries
* Teacher assignments
* Guardian relationships
* AI insights
* RAG context
* Notifications
* Reports
* Recommendations

---

# Authorization Requirements

Personalization must pass access checks before data retrieval.

Required checks:

```text
Authentication
Active user
Active Organization
Active Institution where applicable
Active membership
Required permission
Resource access policy
Guardian-learner relationship
Teacher assignment relationship
Learner self-access
Consent where required
Entitlement where required
```

Frontend personalization is not a security boundary.

Backend remains authoritative.

---

# Consent Requirements

Some personalization may require consent depending on jurisdiction, Institution policy, learner age, and feature type.

Consent may be required for:

```text
AI learner personalization
Long-term learning profile
AI tutor memory
Guardian communication preferences
Product analytics beyond operational analytics
Use of uploaded documents for personalization
Third-party AI provider processing
```

Consent record should include:

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

If consent is withdrawn, personalization must stop for affected features.

---

# Minor Learner Data Rules

Learners may be minors.

Personalization using minor learner data must be conservative.

Rules:

* Use minimum necessary data.
* Avoid sensitive profiling.
* Avoid unsupported predictions.
* Avoid high-stakes automated decisions.
* Avoid behavioral advertising.
* Avoid sharing personalized insights externally without policy approval.
* Allow Institution policy controls.
* Require guardian consent where applicable.
* Log and audit sensitive AI personalization.

---

# Rule-Based Personalization

Rule-based personalization should be the MVP default.

Examples:

```text
If teacher has assigned sections, show those sections first.
If learner attendance is below configured threshold, show attendance attention indicator.
If assessment results are pending, show result entry task.
If Institution setup is incomplete, show setup checklist.
If guardian has multiple linked learners, show learner selector.
```

Benefits:

* Easier to explain
* Easier to test
* Lower cost
* Lower privacy risk
* More predictable for MVP

---

# AI-Based Personalization

AI-based personalization may generate:

```text
learner insight summaries
subject support recommendations
assessment feedback
guardian communication drafts
study suggestions
report summaries
```

AI-based personalization must:

* Use tenant-safe context.
* Use minimized context.
* Use approved prompts.
* Validate output.
* Avoid high-impact final decisions.
* Label AI-generated outputs.
* Require human review for sensitive workflows.
* Track usage and cost.

---

# Personalization Output Types

Recommended output types:

```text
DASHBOARD_WIDGET
RECOMMENDATION
INSIGHT
SUMMARY
DRAFT
ALERT
TASK
CONTENT_SUGGESTION
LEARNING_SUGGESTION
```

Each output should include:

```text
source data type
generated by rule or AI
confidence where applicable
requiresHumanReview
createdAt
scope
```

---

# Personalization Result Data Model

Recommended fields:

```text
personalizationResultId
organizationId
institutionId
userId
learnerId
guardianId
teacherId
resultType
featureCode
sourceType
sourceId
generatedBy
resultPayload
confidence
requiresHumanReview
status
createdAt
updatedAt
expiresAt
```

`generatedBy` values:

```text
RULE
AI
SYSTEM
```

Status values:

```text
ACTIVE
DISMISSED
EXPIRED
REVIEW_REQUIRED
APPROVED
REJECTED
```

---

# Learner Insight Personalization

Learner insight personalization may use:

```text
attendance summary
assessment summary
subject performance
class and section context
academic year context
teacher notes, only if approved
```

Rules:

* Do not diagnose.
* Do not make final decisions.
* Do not compare unfairly to other learners.
* Do not expose to guardian or learner unless policy allows.
* Require human review for sensitive insights.
* Keep language supportive and evidence-aware.

Good phrasing:

```text
The learner may benefit from additional support in fractions based on recent assessment patterns.
```

Bad phrasing:

```text
The learner is weak and will fail mathematics.
```

---

# Teacher Personalization

Teacher personalization may include:

```text
assigned classes
assigned sections
pending attendance tasks
pending result entry
learners needing academic review
upcoming assessments
recent announcements
```

Rules:

* Teacher must only see assigned contexts.
* Teacher should not see unassigned learners.
* Teacher should not see fee data unless explicitly permitted.
* Teacher recommendations should be operational and actionable.

---

# Guardian Personalization

Guardian personalization may include:

```text
linked learner summaries
attendance update availability
assessment update availability
announcements relevant to linked learner
fee reminders, if enabled
communication preferences
```

Rules:

* Guardian must only see linked learners.
* Avoid sensitive notification preview text.
* Avoid exposing internal teacher notes.
* Avoid exposing AI-generated sensitive insights unless approved.
* Use plain language.

---

# Learner Personalization

Learner personalization may include:

```text
own attendance summary
own assessment summary
assigned learning tasks
basic study suggestions
AI learning support, if enabled
```

Rules:

* Learner sees only own data.
* Avoid discouraging language.
* Avoid sensitive predictions.
* Use age-appropriate explanations.
* Respect guardian/institution policy.

---

# Institution Admin Personalization

Institution Admin personalization may include:

```text
setup checklist
attendance tasks needing review
assessment result completion status
learners needing administrative follow-up
teacher assignment gaps
recent announcements
report shortcuts
```

Rules:

* Must be Institution-scoped.
* Must not include other Institutions.
* Should prioritize operational clarity.
* Sensitive learner insights should be review-gated.

---

# Organization Admin Personalization

Organization Admin personalization may include:

```text
Institution summaries
multi-institution comparison in future
user access tasks
Organization setup tasks
billing or entitlement status
cross-Institution reports where permitted
```

MVP may show only one Institution.

Future multi-Institution support must be preserved.

---

# Personalization and Notifications

Personalization may influence notifications.

Examples:

```text
Notify teacher about pending attendance.
Notify guardian that academic update is available.
Notify Institution Admin about setup completion.
Notify user that report export is ready.
```

Privacy rule:

Notifications should avoid sensitive details.

Good:

```text
A new academic update is available for your linked learner.
```

Bad:

```text
Riya Sharma scored 12/50 in Mathematics and was absent 9 days this month.
```

---

# Personalization and RAG

RAG may support personalization by retrieving approved context.

Examples:

```text
Institution policy relevant to a recommendation
Curriculum document relevant to a subject
Approved teacher resource for a learner support suggestion
```

Rules:

* RAG retrieval must be tenant-scoped.
* RAG source visibility must be respected.
* Learner-specific RAG should be deferred unless access controls are mature.
* Retrieved content must be treated as context, not instruction.

---

# Personalization and OCR

OCR text may support personalization only when:

```text
source file is authorized
OCR result is confirmed or confidence is acceptable
use case is approved
personalization feature is enabled
consent requirements are satisfied
```

Do not automatically use OCR text for personalization.

Do not use low-confidence OCR for sensitive personalization without review.

---

# Personalization and Entitlements

Some personalization may be feature-gated.

Possible feature codes:

```text
personalization.basic
personalization.learnerInsights
personalization.aiRecommendations
personalization.teacherDashboard
personalization.guardianDashboard
personalization.adaptiveLearning
```

AI personalization may also use:

```text
ai.insight.generate
ai.assessmentFeedback.generate
ai.communicationDraft.generate
```

Do not check plan names directly.

Use entitlement and feature access checks.

---

# Personalization and Feature Flags

Feature flags may control rollout.

Examples:

```text
learner-insights-v1
teacher-dashboard-priorities
guardian-linked-learner-summary
ai-study-suggestions
adaptive-practice
```

Feature flags do not replace:

* Authentication
* Authorization
* Tenant scoping
* Consent
* Entitlements
* Privacy checks

---

# Personalization Explainability

Personalized outputs should be explainable where practical.

Examples:

```text
Based on recent attendance records
Based on the latest assessment results
Because this class has pending attendance
Because this learner is linked to your guardian account
```

Avoid opaque AI-driven recommendations for sensitive decisions.

For AI outputs, include limitations where applicable.

---

# Personalization Confidence

AI-generated personalization may include confidence.

Confidence must be used carefully.

Rules:

* Confidence should not imply certainty.
* Low-confidence outputs should require review or be hidden.
* Confidence should not be shown to all users unless meaningful.
* Use human-readable limitations alongside confidence.

Recommended internal range:

```text
0.0 to 1.0
```

---

# Human Review Requirements

Human review is required for personalization that may affect:

```text
learner risk interpretation
guardian communication
official reports
assessment feedback
disciplinary follow-up
high-impact academic recommendations
sensitive learner insight sharing
```

Review statuses:

```text
REVIEW_REQUIRED
APPROVED
REJECTED
PUBLISHED
ARCHIVED
```

---

# Privacy Requirements

Personalization must follow privacy-by-design.

Rules:

* Use minimum necessary data.
* Do not use sensitive data without approval.
* Do not personalize across tenants.
* Do not use learner data for advertising.
* Do not use minor data for unrelated analytics.
* Do not expose internal notes to guardians or learners.
* Do not log sensitive personalization payloads.
* Respect consent and retention policies.

---

# Security Requirements

Personalization must defend against:

```text
cross-tenant data leakage
unauthorized learner access
guardian access to unlinked learner
teacher access to unassigned learner
AI prompt leakage
RAG retrieval leakage
overbroad recommendations
sensitive data in notifications
unsafe outputs
```

All personalization APIs must enforce backend authorization.

---

# Personalization Logging Rules

Safe to log:

```text
requestId
organizationId
institutionId
userId
featureCode
resultType
generatedBy
status
errorCode
latencyMs
createdAt
```

Do not log by default:

```text
full learner records
guardian contact details
raw AI prompt
raw AI output with personal data
OCR text
private file contents
assessment details beyond safe metadata
attendance detail records
sensitive recommendations
tokens
secrets
```

---

# Audit Requirements

Audit logs are required for:

```text
AI learner insight generation
personalized recommendation shared externally
guardian communication draft generation
official report personalization
personalization configuration changes
consent change affecting personalization
sensitive learner insight approval
sensitive learner insight publication
```

Audit metadata should include:

```text
actorUserId
organizationId
institutionId
learnerId where applicable
featureCode
resultType
action
status
requestId
createdAt
```

Do not store raw sensitive result payloads in audit logs unless explicitly approved and protected.

---

# Retention Requirements

Personalization results should have explicit retention rules.

Suggested MVP approach:

| Result Type                     | Suggested Retention                         |
| ------------------------------- | ------------------------------------------- |
| Dashboard hints                 | Short-lived                                 |
| Setup tasks                     | Until completed or dismissed                |
| Teacher pending tasks           | Until resolved                              |
| Guardian linked learner summary | Recomputed from source                      |
| Learner academic summary        | Recomputed from source                      |
| AI learner insight              | Retain according to academic insight policy |
| AI communication draft          | Retain until sent, discarded, or expired    |
| AI report summary               | Retain with report or expire with export    |

Avoid retaining personalization outputs indefinitely without purpose.

---

# Deletion Requirements

When source data is deleted, archived, or access is revoked:

* Personalization output must be invalidated where necessary.
* AI-derived results should be reviewed for deletion or archival.
* Cached personalization must be cleared.
* RAG-derived personalization should stop retrieving deleted source chunks.
* Guardian-linked personalization must stop when relationship is removed.
* Teacher assignment personalization must stop when assignment is removed.

---

# Personalization API Patterns

Personalization may be exposed through domain APIs rather than generic APIs.

Preferred:

```http
GET /api/v1/organizations/{organizationId}/institutions/{institutionId}/dashboard
GET /api/v1/organizations/{organizationId}/institutions/{institutionId}/learners/{learnerId}/insights
GET /api/v1/organizations/{organizationId}/institutions/{institutionId}/teachers/{teacherId}/dashboard
GET /api/v1/notifications
```

Avoid broad unscoped endpoint:

```http
POST /api/v1/personalize
```

If a generic personalization endpoint is needed, it must be scoped:

```http
POST /api/v1/organizations/{organizationId}/institutions/{institutionId}/personalization/evaluate
```

---

# Personalization Error Codes

Relevant error codes:

```text
TENANT_SCOPE_VIOLATION
RBAC_PERMISSION_REQUIRED
LEARNER_ACCESS_DENIED
GUARDIAN_ACCESS_DENIED
TEACHER_ACCESS_DENIED
ENTITLEMENT_REQUIRED
USAGE_LIMIT_EXCEEDED
PRIVACY_CONSENT_REQUIRED
PRIVACY_CONSENT_WITHDRAWN
AI_CONTEXT_MISSING
AI_CONTEXT_TOO_LARGE
AI_OUTPUT_INVALID
AI_OUTPUT_UNSAFE
AI_PERSONALIZATION_UNAVAILABLE
```

Definitions are maintained in:

```text
docs/06-API/ERROR_CODES.md
```

If additional personalization-specific errors are needed, add them to `ERROR_CODES.md` before implementation.

---

# Personalization Testing Requirements

Personalization tests must cover:

## Tenant Scope

* Institution Admin cannot receive another Institution's personalization.
* Organization Admin cannot receive another Organization's personalization.
* Learner data is scoped by Organization and Institution.

## Role Access

* Teacher sees only assigned learners/classes.
* Guardian sees only linked learners.
* Learner sees only own summary.
* Platform Admin access is audited where sensitive.

## Consent and Privacy

* Feature requiring consent fails without consent.
* Withdrawn consent disables personalization.
* Sensitive data is not included in notification previews.
* Restricted data sources are not used by default.

## AI Personalization

* AI context is minimized.
* AI output handles insufficient data.
* AI output avoids high-impact final decisions.
* AI output is labeled and reviewable.

## Cache and Invalidation

* Removed guardian link invalidates guardian personalization.
* Removed teacher assignment invalidates teacher personalization.
* Archived learner is excluded from active recommendations.
* Context switch clears old Institution personalization.

---

# MVP Personalization Requirements

The MVP must implement:

```text
Role-based dashboard personalization
Active Organization and Institution context
Active academic year defaulting
Teacher assignment-based filtering
Guardian-learner relationship filtering
Learner self-access filtering
Institution setup checklist
Basic attendance and assessment summaries
Permission-aware UI personalization
Tenant-safe personalization query keys
Privacy-safe notifications
Personalization tests for access boundaries
```

If AI personalization is included, MVP must also implement:

```text
AI learner insight with minimized context
AI assessment feedback draft
AI communication draft context
Human review flag
AI output validation
AI usage tracking
Audit logging for sensitive AI personalization
```

---

# Deferred Personalization Capabilities

The following may be deferred beyond MVP:

```text
Adaptive learning engine
AI tutor memory
Long-term learner profile model
Personalized quiz generation
Personalized worksheet generation
Study planner personalization
Cross-session recommendation engine
Advanced guardian personalization
Advanced teacher workload prioritization
Multi-institution benchmarking
Institution-specific personalization rules UI
Automated personalization evaluation dashboard
```

Do not block these future capabilities with MVP-only assumptions.

---

# Personalization Anti-Patterns

Avoid:

```text
Using personalization without tenant scope
Using learner data across Institutions
Using guardian data for unlinked learners
Using teacher recommendations for unassigned learners
Using AI to make final high-impact decisions
Using sensitive learner data in notifications
Using raw OCR text automatically
Indexing all learner data for RAG personalization by default
Logging raw personalization payloads
Using schoolId, studentId, or parentId in backend personalization design
Using plan names instead of feature entitlements
Using personalization for advertising to minors
```

---

# Personalization Review Checklist

Before approving personalization implementation, confirm:

* Product value is clear.
* Personalization type is defined.
* Data sources are approved.
* Organization and Institution scope are enforced.
* Role and permission checks are defined.
* Learner, Guardian, and Teacher access policies are respected.
* Consent requirement is evaluated.
* Minor learner data risk is considered.
* Sensitive data is minimized.
* AI use is clearly labeled where applicable.
* Human review is required for sensitive outputs.
* Retention and invalidation behavior is defined.
* Logs are privacy-safe.
* Tests cover access boundaries.
* No `schoolId`, `studentId`, or `parentId` is introduced in backend design.

---

# Related Documents

* docs/07-AI/AI_ENGINE.md
* docs/07-AI/LLM_ROUTING.md
* docs/07-AI/PROMPTS.md
* docs/07-AI/OCR.md
* docs/07-AI/RAG.md
* docs/07-AI/CONTENT_MODERATION.md
* docs/07-AI/MODEL_SELECTION.md
* docs/07-AI/PROMPT_ENGINEERING.md
* docs/06-API/ERROR_CODES.md
* docs/09-Backend/SERVICES.md
* docs/09-Backend/BACKGROUND_JOBS.md
* docs/11-Security/PRIVACY.md
* docs/11-Security/RBAC.md