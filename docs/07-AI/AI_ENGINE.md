# AI Engine

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** AI Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the AI Engine architecture for Acadedx.

The AI Engine powers AI-assisted educational, administrative, reporting, communication, OCR, and personalization workflows across the Acadedx platform.

Acadedx is a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

AI workflows must use:

```text
organizationId
institutionId
learnerId
guardianId
teacherId
```

Do not use:

```text
schoolId
studentId
parentId
```

except in explicitly documented UI copy, prompt output labels, or legacy import adapters.

---

# AI Engine Goals

The Acadedx AI Engine must ensure:

1. AI features support real educational and operational workflows.
2. AI requests are tenant-safe and scoped by Organization and Institution.
3. AI does not bypass authentication, authorization, RBAC, privacy, or entitlements.
4. AI-generated content is assistive, not authoritative.
5. AI outputs are traceable, reviewable, and auditable where required.
6. AI workflows are modular and provider-agnostic.
7. AI context retrieval is minimized and privacy-aware.
8. AI cost, latency, safety, and quality are actively managed.
9. MVP AI scope remains practical.
10. Future RAG, personalization, OCR, and multi-model routing are supported without redesign.

---

# AI Product Positioning

Acadedx AI should primarily support:

```text
Institution operations
Academic insights
Teacher productivity
Learner support
Guardian communication
Administrative automation
```

The MVP should avoid becoming an unbounded direct-to-student AI homework app unless that remains an explicit product strategy.

Recommended AI positioning:

```text
AI-assisted Institution platform
```

not:

```text
Generic chatbot for everything
```

---

# AI Engine Scope

The AI Engine may support:

* Learner academic insights
* Assessment feedback
* Attendance pattern summaries
* Guardian communication drafts
* Teacher support drafts
* Report summaries
* OCR-assisted document extraction
* Learning content explanation
* Personalized learning recommendations
* Moderation and safety checks
* AI usage analytics
* Prompt management
* Model routing
* RAG over approved educational or institutional knowledge
* Future tutoring workflows

---

# MVP AI Scope

The MVP should include AI only where it directly supports institution-first value.

Recommended MVP AI features:

```text
AI learner insight summary
AI assessment feedback draft
AI communication draft for guardians
AI report summary
AI-assisted OCR, if document extraction is required
```

MVP optional:

```text
AI tutor
Homework solving
Quiz generation
Worksheet generation
Study planner
Advanced personalization
```

Constraint:

Do not let optional AI features delay the core Organization → Institution academic operations MVP.

---

# AI Non-Goals for MVP

The MVP AI Engine should not attempt to solve:

* Fully autonomous teaching
* Fully autonomous grading decisions
* High-stakes learner classification
* Medical, psychological, or legal diagnosis
* Unreviewed disciplinary recommendations
* Fully open-ended public chatbot access
* Complex adaptive learning engine
* Multi-agent orchestration
* Fine-tuned model lifecycle
* Customer-specific model training
* Full AI marketplace

These may be considered later only with clear product, privacy, safety, and legal review.

---

# AI Architecture Overview

Recommended AI Engine architecture:

```text
AI Feature Controller
    ↓
AI Application Service
    ↓
Authorization / Entitlement / Usage Checks
    ↓
AI Context Service
    ↓
Prompt Service
    ↓
Model Router
    ↓
Provider Adapter
    ↓
Safety Service
    ↓
Result Persistence
    ↓
Audit / Usage / Notification
```

---

# Core AI Modules

Recommended backend modules:

```text
AiModule
PromptModule
ModelRoutingModule
AiSafetyModule
AiUsageModule
AiContextModule
OcrModule
RagModule
PersonalizationModule
ContentModerationModule
```

MVP may implement these inside a single `AiModule` while preserving internal service boundaries.

---

# Recommended AiModule Structure

```text
src/modules/ai/
├── controllers/
│   ├── ai-insights.controller.ts
│   ├── ai-feedback.controller.ts
│   └── ai-drafts.controller.ts
├── services/
│   ├── ai.service.ts
│   ├── ai-context.service.ts
│   ├── ai-prompt.service.ts
│   ├── ai-model-router.service.ts
│   ├── ai-provider.service.ts
│   ├── ai-safety.service.ts
│   ├── ai-usage.service.ts
│   └── ai-result.service.ts
├── providers/
│   ├── openai.provider.ts
│   ├── anthropic.provider.ts
│   └── local.provider.ts
├── dto/
├── policies/
├── jobs/
├── types/
└── ai.module.ts
```

Provider files are examples only. The implementation should remain provider-agnostic.

---

# AI Request Lifecycle

Standard AI request lifecycle:

```text
1. User initiates AI action.
2. Backend validates authentication.
3. Backend validates Organization and Institution scope.
4. Backend validates permission.
5. Backend validates resource access policy.
6. Backend validates entitlement and usage limits.
7. Backend creates synchronous request or background job.
8. AI Context Service retrieves minimized tenant-safe context.
9. Prompt Service renders approved prompt template.
10. Model Router selects model/provider.
11. Provider Adapter sends request.
12. Safety Service validates output.
13. Result is persisted or returned.
14. Usage is recorded.
15. Audit log is created where required.
16. User sees AI-generated output with review cues.
```

---

# AI Request Context

Every AI request must include context.

Recommended shape:

```ts
export interface AiRequestContext {
  requestId: string;
  userId: string;
  organizationId: string;
  institutionId: string;
  role: string;
  permissions: string[];
  featureCode: string;
  resourceType?: string;
  resourceId?: string;
  learnerId?: string;
  teacherId?: string;
  guardianId?: string;
}
```

Institution-scoped AI workflows require:

```text
organizationId
institutionId
```

Do not allow AI workflows to process Institution data without tenant scope.

---

# AI Feature Codes

AI features should be identified by feature codes.

Examples:

```text
ai.insight.generate
ai.insight.read
ai.assessmentFeedback.generate
ai.communicationDraft.generate
ai.reportSummary.generate
ai.ocr.create
ai.ocr.read
ai.tutor.chat
ai.quiz.generate
ai.worksheet.generate
ai.studyPlan.generate
```

Feature codes are used for:

* Entitlements
* Usage limits
* Feature flags
* Permissions
* Audit logs
* Analytics
* Cost tracking

---

# Authorization Requirements

AI workflows must pass authorization before processing.

Required checks:

```text
Authentication
Active user
Active Organization
Active Institution
Active membership
Required permission
Resource access policy
Entitlement
Usage limit
Feature flag
Privacy rule
```

Example:

A teacher generating assessment feedback must have:

```text
ai.assessmentFeedback.generate
```

and must be assigned to the relevant class, section, subject, or assessment context.

A guardian must not generate AI insight for an unlinked learner.

A learner must not use AI context from another learner.

---

# Tenant Isolation Requirements

AI must never retrieve or generate content using data outside authorized scope.

Every AI context query must include:

```text
organizationId
institutionId
```

where applicable.

Bad:

```sql
SELECT * FROM assessment_results WHERE learner_id = $1;
```

Good:

```sql
SELECT * FROM assessment_results
WHERE learner_id = $1
  AND organization_id = $2
  AND institution_id = $3;
```

This applies to:

* Database queries
* File retrieval
* OCR text retrieval
* Vector search
* RAG retrieval
* Analytics context
* Report summaries
* Personalization context

---

# AI Context Service

The AI Context Service is responsible for retrieving safe context for AI workflows.

Responsibilities:

* Validate tenant scope.
* Retrieve only necessary data.
* Apply resource access policies.
* Minimize personal data.
* Apply field masking.
* Prepare structured context.
* Avoid cross-tenant retrieval.
* Avoid unnecessary raw documents.
* Support future RAG context retrieval.

Example context request:

```ts
type LearnerInsightContextRequest = {
  organizationId: string;
  institutionId: string;
  learnerId: string;
  academicYearId: string;
  includeAttendance?: boolean;
  includeAssessments?: boolean;
};
```

---

# Context Minimization

AI prompts should include only the data needed for the task.

Bad:

```text
Send full learner profile, guardian contact details, all documents, all attendance records, all assessment records, teacher comments, and institution metadata.
```

Better:

```text
Send learner academic level, relevant attendance summary, relevant assessment summary, and the specific task instruction.
```

Minimum necessary principle applies to all AI workflows.

---

# Prompt Service

The Prompt Service manages prompt templates.

Responsibilities:

* Store prompt templates.
* Version prompt templates.
* Render prompts with validated context.
* Prevent accidental prompt injection.
* Separate system instructions from user-provided content.
* Support localization later.
* Support model-specific formatting.
* Record prompt version used for traceability.

Prompt templates should be treated as product assets.

Changes to prompt templates must be reviewed and audited where they affect learner outcomes, communication, moderation, or institutional reports.

---

# Prompt Template Metadata

Recommended prompt template metadata:

```text
promptTemplateId
name
workflow
version
status
modelFamily
inputSchema
outputSchema
safetyCategory
createdBy
approvedBy
createdAt
updatedAt
```

Statuses:

```text
DRAFT
APPROVED
DEPRECATED
ARCHIVED
```

MVP may store prompt templates in code, but should preserve versioning through constants or metadata.

---

# Model Router

The Model Router selects which AI model or provider to use.

Routing factors:

```text
workflow type
required capability
latency requirement
cost target
safety requirement
context length
language
availability
fallback policy
```

Example:

| Workflow                     | Model Need                    |
| ---------------------------- | ----------------------------- |
| Guardian communication draft | High writing quality, safety  |
| Learner insight summary      | Reasoning + structured output |
| OCR correction               | Vision/OCR support            |
| Report summary               | Long context summarization    |
| Simple explanation           | Low-cost text model           |

Detailed model routing is documented in:

```text
docs/07-AI/LLM_ROUTING.md
```

---

# AI Provider Abstraction

AI providers must be wrapped behind provider adapters.

Provider adapter responsibilities:

* Normalize request shape.
* Normalize response shape.
* Map provider errors to Acadedx error codes.
* Apply timeouts.
* Record provider metadata.
* Avoid leaking provider-specific response bodies to clients.
* Support retries where safe.
* Support fallback where configured.

Domain services must not call provider SDKs directly.

---

# AI Provider Errors

Provider errors must map to stable error codes.

Examples:

```text
AI_PROVIDER_ERROR
AI_PROVIDER_UNAVAILABLE
AI_PROVIDER_TIMEOUT
AI_OUTPUT_EMPTY
AI_OUTPUT_INVALID
AI_MODEL_NOT_AVAILABLE
AI_MODEL_SELECTION_FAILED
```

Do not expose raw provider error payloads to frontend users.

---

# Structured Outputs

Prefer structured outputs for application workflows.

Example learner insight output:

```json
{
  "summary": "The learner is performing consistently in Mathematics but needs support with fractions.",
  "strengths": ["Consistent homework completion", "Strong arithmetic accuracy"],
  "areasForSupport": ["Fractions", "Word problems"],
  "recommendedActions": ["Provide targeted practice on fraction comparison"],
  "confidence": 0.78,
  "requiresHumanReview": true
}
```

Structured outputs help:

* Validate AI responses
* Improve UI rendering
* Reduce hallucination impact
* Support audit and review
* Prevent unsafe free-form outputs

---

# AI Output Validation

AI outputs must be validated before use.

Validation should check:

* Required fields exist.
* Output matches schema.
* Content is safe.
* Content is not empty.
* Content does not include forbidden data.
* Content does not claim unsupported certainty.
* Content does not expose internal prompts.
* Content does not contain cross-tenant data.

Invalid output should return or persist:

```text
AI_OUTPUT_INVALID
```

Unsafe output should return:

```text
AI_OUTPUT_UNSAFE
```

---

# AI Safety Service

The AI Safety Service is responsible for safety checks.

Responsibilities:

* Input moderation where needed.
* Output moderation where needed.
* Prompt injection detection where practical.
* Sensitive data leakage detection where practical.
* Unsafe educational guidance detection.
* High-impact decision risk detection.
* Provider safety metadata handling.
* Escalation or block decisions.

Detailed content moderation is documented in:

```text
docs/07-AI/CONTENT_MODERATION.md
```

---

# AI as Assistive, Not Authoritative

AI outputs must not be treated as final authority for high-impact educational decisions.

High-impact actions requiring human review:

```text
Learner risk classification
Assessment feedback sent to guardians
Disciplinary recommendations
Promotion or retention guidance
Fee or financial decisions
Sensitive guardian communication
Official report content
```

UI must label AI outputs clearly.

Examples:

```text
AI-generated
Suggested insight
Review before sharing
```

Avoid:

```text
Final decision
Guaranteed prediction
Definitive diagnosis
```

---

# AI Audit Requirements

Audit logs are required for sensitive AI actions.

Audit required for:

```text
AI learner insight generation
AI communication draft generation
AI report summary generation
AI prompt template update
AI model routing configuration update
AI output shared externally
AI moderation failure
AI access to sensitive learner context
```

Audit record should include:

```text
actorUserId
organizationId
institutionId
workflow
resourceType
resourceId
promptTemplateId
promptVersion
model
provider
status
errorCode
requestId
createdAt
```

Do not store raw prompts or outputs in audit logs unless explicitly approved and protected.

---

# AI Usage Tracking

AI usage must be tracked for cost, abuse prevention, and product analytics.

Track:

```text
organizationId
institutionId
userId
workflow
featureCode
model
provider
inputTokens
outputTokens
estimatedCost
latencyMs
status
errorCode
createdAt
```

Usage data supports:

* Entitlements
* Quotas
* Cost management
* Abuse detection
* Product analytics
* Provider monitoring

---

# Entitlement and Usage Limits

AI features may be premium or usage-limited.

Before AI execution, validate:

```text
feature enabled
entitlement active
usage limit not exceeded
rate limit not exceeded
```

Examples:

```text
ai.insight.generate
ai.assessmentFeedback.generate
ai.communicationDraft.generate
ai.ocr.create
ai.tutor.chat
ai.quiz.generate
```

Do not check plan names directly.

Bad:

```text
if plan == PREMIUM
```

Good:

```text
canUseFeature("ai.insight.generate")
```

---

# AI Background Jobs

Most AI workflows should use background jobs if they are:

* Long-running
* Costly
* Provider-dependent
* Retried
* Used for reports
* Generating files
* Processing OCR
* Running bulk operations

Example:

```http
POST /api/v1/organizations/{organizationId}/institutions/{institutionId}/ai/learner-insights
```

Response:

```json
{
  "success": true,
  "data": {
    "jobId": "job_123",
    "status": "QUEUED"
  }
}
```

Job context must include:

```text
requestedByUserId
organizationId
institutionId
resourceType
resourceId
featureCode
requiredPermission
```

Background job standards are documented in:

```text
docs/09-Backend/BACKGROUND_JOBS.md
```

---

# AI Result Persistence

AI results may be persisted when they support product workflows.

Persisted AI results include:

```text
learner insights
assessment feedback drafts
communication drafts
report summaries
OCR outputs
moderation events
usage records
```

Do not persist:

```text
raw prompts by default
raw provider debug payloads
temporary chain-of-thought style internal reasoning
provider secrets
unnecessary personal data
```

---

# AI Result Metadata

Recommended AI result metadata:

```text
aiResultId
organizationId
institutionId
workflow
resourceType
resourceId
generatedByUserId
promptTemplateId
promptVersion
model
provider
status
confidence
requiresHumanReview
createdAt
updatedAt
```

---

# Human Review Requirements

Some AI outputs require review before use.

Review required for:

```text
guardian communication drafts
official report summaries
learner risk insights
assessment feedback sent externally
disciplinary or sensitive guidance
AI-generated institution-wide announcements
```

Review state values:

```text
DRAFT
REVIEW_REQUIRED
APPROVED
REJECTED
PUBLISHED
ARCHIVED
```

MVP may implement a simpler status model, but should not automatically send sensitive AI outputs.

---

# AI Communication Drafts

AI-generated communication drafts must follow a safe flow:

```text
1. User selects communication purpose.
2. User selects allowed audience.
3. Backend validates recipient scope.
4. AI generates draft.
5. User reviews and edits.
6. User confirms recipients.
7. User sends manually.
8. System audits send action.
```

AI must not send messages automatically without user review.

---

# Learner Insight Workflow

Learner insights may summarize:

```text
attendance trends
assessment performance
subject strengths
areas for support
recommended interventions
```

Learner insight rules:

* Must be Institution-scoped.
* Must use authorized learner data only.
* Must indicate AI-generated status.
* Must avoid deterministic claims.
* Must avoid medical or psychological diagnosis.
* Must require review before sharing externally.
* Must not expose insights to guardians or learners unless product policy allows.

---

# Assessment Feedback Workflow

Assessment feedback may help teachers create feedback.

Rules:

* Teacher must have access to the assessment.
* Assessment must belong to the Institution.
* Learner result must belong to the assessment and Institution.
* AI feedback must be editable.
* AI feedback must not be published automatically.
* Output should align with academic tone and learner safety.

---

# Report Summary Workflow

AI may summarize reports.

Examples:

```text
Attendance report summary
Assessment performance summary
Learner progress summary
Institution dashboard summary
```

Rules:

* Use only authorized report data.
* Include source filters.
* Avoid exposing hidden records.
* Label output as AI-generated.
* Require review before export or sharing.

---

# OCR Integration

OCR may use AI or non-AI providers.

OCR workflows must:

* Validate file ownership.
* Validate file type.
* Validate Organization and Institution scope.
* Validate file access.
* Avoid logging extracted text.
* Store OCR output securely.
* Allow user confirmation where needed.

Detailed OCR standards are documented in:

```text
docs/07-AI/OCR.md
```

---

# RAG Integration

RAG may be used for:

```text
institution policy lookup
curriculum documents
help center answers
approved educational content
learner context retrieval
```

RAG must include:

```text
organizationId filter
institutionId filter where applicable
document access policy
content sensitivity classification
```

Detailed RAG standards are documented in:

```text
docs/07-AI/RAG.md
```

---

# Personalization

Personalization may use learner activity, assessments, attendance, and learning preferences.

Rules:

* Must be consent-aware where required.
* Must be Institution-scoped.
* Must not use unauthorized learner data.
* Must avoid sensitive profiling.
* Must be explainable enough for users and institutions.
* Must allow future opt-out or configuration.

Detailed personalization standards are documented in:

```text
docs/07-AI/PERSONALIZATION.md
```

---

# AI Privacy Requirements

AI workflows must follow privacy principles.

Rules:

* Collect and send minimum necessary data.
* Avoid raw personal data where not needed.
* Avoid guardian contact data in prompts unless required.
* Avoid unnecessary learner identifiers.
* Avoid cross-tenant retrieval.
* Avoid retaining raw prompts by default.
* Avoid using AI outputs for unrelated purposes.
* Follow retention policy.

Privacy standards are documented in:

```text
docs/11-Security/PRIVACY.md
```

---

# AI Security Requirements

AI workflows must defend against:

```text
prompt injection
data exfiltration
cross-tenant leakage
unsafe output
provider failure
usage abuse
unauthorized file processing
RAG retrieval leakage
sensitive logging
```

Security standards are documented in:

```text
docs/11-Security/SECURITY.md
```

---

# AI Error Codes

Relevant AI error codes:

```text
AI_REQUEST_INVALID
AI_PROVIDER_ERROR
AI_PROVIDER_UNAVAILABLE
AI_PROVIDER_TIMEOUT
AI_OUTPUT_EMPTY
AI_OUTPUT_INVALID
AI_OUTPUT_UNSAFE
AI_CONTEXT_TOO_LARGE
AI_CONTEXT_MISSING
AI_MODEL_NOT_AVAILABLE
AI_MODEL_SELECTION_FAILED
AI_PROMPT_TEMPLATE_NOT_FOUND
AI_PROMPT_RENDER_FAILED
AI_PERSONALIZATION_UNAVAILABLE
AI_MODERATION_FAILED
AI_GENERATION_CANCELLED
AI_GENERATION_IN_PROGRESS
OCR_JOB_FAILED
OCR_PROVIDER_ERROR
OCR_PROVIDER_TIMEOUT
ENTITLEMENT_REQUIRED
USAGE_LIMIT_EXCEEDED
TENANT_SCOPE_VIOLATION
```

Definitions are maintained in:

```text
docs/06-API/ERROR_CODES.md
```

---

# AI API Endpoints

AI endpoints should be Institution-scoped.

Example patterns:

```http
POST /api/v1/organizations/{organizationId}/institutions/{institutionId}/ai/learner-insights
POST /api/v1/organizations/{organizationId}/institutions/{institutionId}/ai/assessment-feedback
POST /api/v1/organizations/{organizationId}/institutions/{institutionId}/ai/communication-drafts
POST /api/v1/organizations/{organizationId}/institutions/{institutionId}/ai/report-summaries
GET  /api/v1/organizations/{organizationId}/institutions/{institutionId}/ai/results/{aiResultId}
```

Avoid unscoped AI endpoints for tenant data.

Bad:

```http
POST /api/v1/ai/generate
```

Good:

```http
POST /api/v1/organizations/{organizationId}/institutions/{institutionId}/ai/learner-insights
```

---

# AI Frontend UX Requirements

AI frontend components must:

* Label AI-generated content.
* Show generation status.
* Show review state.
* Allow edit before sending or publishing.
* Show safe error states.
* Avoid exposing raw prompts.
* Avoid sensitive notification previews.
* Use permission and entitlement gates.
* Include Organization and Institution context in API calls.

---

# AI Observability

AI operations must be observable.

Track:

```text
request count
success rate
failure rate
latency
provider timeout rate
model usage
token usage
estimated cost
moderation failures
unsafe output blocks
tenant scope violations
job retry rate
```

Alerts should exist for:

```text
provider outage
cost spike
high failure rate
moderation spike
cross-tenant access attempt
unusual user usage
excessive retries
```

---

# AI Testing Requirements

AI tests must cover:

## Authorization

* Missing authentication
* Missing permission
* Organization mismatch
* Institution mismatch
* Teacher accessing unassigned learner
* Guardian accessing unlinked learner
* Learner accessing another learner

## Context

* Context includes only authorized data
* Context includes Organization and Institution filters
* Context minimization works
* Missing context fails safely
* Oversized context fails safely

## Prompting

* Prompt template renders correctly
* Missing prompt template fails safely
* Prompt version is recorded
* User input is separated from system instructions

## Output

* Valid structured output passes
* Invalid output fails
* Empty output fails
* Unsafe output fails
* Overconfident high-impact output is blocked or flagged

## Provider

* Provider timeout maps to stable error
* Provider unavailable maps to stable error
* Fallback works where configured
* Raw provider error is not exposed

## Jobs

* AI job stores tenant context
* AI job does not store tokens
* AI job updates status
* AI job records usage
* Failed job returns safe error

---

# MVP AI Engine Requirements

The MVP AI Engine should implement at minimum if AI is included:

```text
AiModule
AiService
AiContextService
AiPromptService
AiProviderService
AiSafetyService
AiUsageService
Prompt template versioning
Provider abstraction
Institution-scoped AI endpoints
Permission checks
Entitlement checks where applicable
Usage tracking
Tenant-safe context retrieval
Structured output validation
Safe error mapping
AI result metadata
Human review flag
Audit logging for sensitive AI actions
Background job support for long AI tasks
```

MVP AI workflows should be limited to:

```text
Learner insight summary
Assessment feedback draft
Guardian communication draft
Report summary
OCR-assisted extraction, if required
```

---

# Deferred AI Engine Capabilities

The following may be deferred beyond MVP:

```text
Advanced LLM routing
Multi-provider fallback
Streaming AI responses
Advanced RAG
Personalized learner models
AI tutor
Homework solving
Quiz generation
Worksheet generation
Study planner
Fine-tuned models
Customer-specific model customization
AI evaluation dashboard
Prompt A/B testing
Human feedback training loop
Automated hallucination scoring
Advanced safety classifiers
```

Do not block these future capabilities with hardcoded MVP-only AI architecture.

---

# AI Anti-Patterns

Avoid:

```text
Unscoped AI endpoints
AI workflows without organizationId and institutionId
Sending full learner records unnecessarily
Sending guardian contact details unnecessarily
Storing raw prompts by default
Logging raw AI responses with personal data
Treating AI output as final official decision
Automatically sending AI communication drafts
Using one generic prompt for every workflow
Calling provider SDKs directly from controllers
Hardcoding one AI provider everywhere
Ignoring provider cost and latency
Ignoring moderation and output validation
Using Student/Parent/School as backend AI entities
```

---

# AI Review Checklist

Before approving an AI feature, confirm:

* Feature has clear product value.
* Workflow is Institution-scoped where required.
* Organization and Institution context are included.
* Required permission is defined.
* Resource policy is defined.
* Entitlement and usage checks are defined.
* Context is minimized.
* Prompt template is versioned.
* Model/provider routing is defined.
* Output schema is defined where possible.
* Output safety is checked.
* AI-generated content is labeled.
* Human review is required for sensitive use.
* Audit logging is defined.
* Usage tracking is defined.
* Error codes are mapped.
* Tests cover authorization and tenant boundaries.
* No `schoolId`, `studentId`, or `parentId` is introduced in backend AI design.

---

# Related Documents

* docs/07-AI/LLM_ROUTING.md
* docs/07-AI/PROMPTS.md
* docs/07-AI/OCR.md
* docs/07-AI/RAG.md
* docs/07-AI/PERSONALIZATION.md
* docs/07-AI/CONTENT_MODERATION.md
* docs/07-AI/MODEL_SELECTION.md
* docs/07-AI/PROMPT_ENGINEERING.md
* docs/06-API/ENDPOINTS.md
* docs/06-API/ERROR_CODES.md
* docs/09-Backend/BACKGROUND_JOBS.md
* docs/11-Security/SECURITY.md
* docs/11-Security/PRIVACY.md
* docs/11-Security/RBAC.md
