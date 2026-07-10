# LLM Routing

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** AI Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the Large Language Model routing strategy for Acadedx.

LLM routing determines which AI model, provider, configuration, and fallback path should be used for each AI workflow.

Acadedx is a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

LLM routing must use:

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

except in explicitly approved UI copy, prompt output labels, or legacy adapters.

---

# LLM Routing Goals

LLM routing must ensure:

1. The right model is selected for each workflow.
2. AI outputs are safe, useful, and cost-aware.
3. Tenant context is preserved across all model calls.
4. Institution data never leaks across Organizations or Institutions.
5. Expensive models are used only when justified.
6. Fallbacks are predictable and safe.
7. Model selection is observable and auditable.
8. Prompt templates remain compatible with selected models.
9. AI workflows can evolve without rewriting product logic.
10. MVP implementation remains simple while supporting future multi-provider routing.

---

# Routing Philosophy

Acadedx should not call a single generic model for every workflow.

Different workflows require different tradeoffs:

| Workflow                     | Primary Need                                |
| ---------------------------- | ------------------------------------------- |
| Learner insight              | Reasoning, structure, safety                |
| Assessment feedback          | Educational quality, tone, editability      |
| Guardian communication draft | Writing quality, safety, empathy            |
| Report summary               | Summarization, long context                 |
| OCR correction               | Vision or document understanding            |
| AI tutor                     | Conversational quality, latency, safety     |
| Quiz generation              | Structured generation, curriculum alignment |
| Moderation                   | Safety classification, speed                |
| Embeddings/RAG               | Retrieval quality, cost efficiency          |

Routing must balance:

```text
quality
latency
cost
safety
privacy
context length
structured output support
provider availability
```

---

# Routing Architecture

Recommended routing flow:

```text
AI Feature Service
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
Result Validator
    ↓
Persistence / Response
```

The `ModelRouterService` should own model selection.

Domain services must not call provider SDKs directly.

---

# ModelRouterService Responsibilities

The `ModelRouterService` is responsible for:

* Selecting model family.
* Selecting provider.
* Selecting model variant.
* Selecting temperature and generation settings.
* Selecting structured output mode.
* Selecting fallback chain.
* Enforcing workflow constraints.
* Applying tenant or feature configuration.
* Recording routing decisions.
* Returning safe errors when no model is available.

Recommended service name:

```text
AiModelRouterService
```

---

# Provider Abstraction

LLM providers must be hidden behind provider adapters.

Recommended abstraction:

```ts
export interface LlmProvider {
  generateText(request: LlmTextRequest): Promise<LlmTextResponse>;
  generateStructured<T>(request: LlmStructuredRequest<T>): Promise<LlmStructuredResponse<T>>;
  generateVision?(request: LlmVisionRequest): Promise<LlmVisionResponse>;
  embed?(request: EmbeddingRequest): Promise<EmbeddingResponse>;
}
```

Domain services should depend on Acadedx AI interfaces, not directly on vendor SDKs.

---

# Provider Adapter Responsibilities

Provider adapters must:

* Normalize provider-specific requests.
* Normalize provider-specific responses.
* Apply provider-specific timeouts.
* Map provider errors to Acadedx error codes.
* Hide raw provider errors from users.
* Record model and provider metadata.
* Support retries where safe.
* Support structured output where available.
* Support fallback compatibility.

---

# Routing Inputs

The Model Router should consider these inputs:

```text
workflow
featureCode
organizationId
institutionId
userRole
resourceType
resourceId
contextSize
outputFormat
safetyLevel
latencyTarget
costTier
language
requiresVision
requiresEmbeddings
requiresStructuredOutput
requiresHumanReview
```

Recommended TypeScript shape:

```ts
export interface LlmRoutingRequest {
  workflow: AiWorkflow;
  featureCode: string;
  organizationId: string;
  institutionId?: string;
  userRole: string;
  resourceType?: string;
  resourceId?: string;
  contextSizeTokens?: number;
  outputFormat: 'TEXT' | 'JSON' | 'MARKDOWN' | 'STRUCTURED';
  safetyLevel: 'LOW' | 'STANDARD' | 'HIGH';
  latencyTarget?: 'LOW' | 'STANDARD' | 'BATCH';
  costTier?: 'LOW' | 'STANDARD' | 'HIGH';
  language?: string;
  requiresVision?: boolean;
  requiresEmbeddings?: boolean;
  requiresStructuredOutput?: boolean;
  requiresHumanReview?: boolean;
}
```

---

# Routing Output

The Model Router should return a routing decision.

Recommended TypeScript shape:

```ts
export interface LlmRoutingDecision {
  provider: string;
  model: string;
  modelFamily: string;
  workflow: AiWorkflow;
  temperature: number;
  maxOutputTokens: number;
  timeoutMs: number;
  responseFormat: 'TEXT' | 'JSON' | 'STRUCTURED';
  fallbackModels: Array<{
    provider: string;
    model: string;
  }>;
  safetyLevel: 'LOW' | 'STANDARD' | 'HIGH';
  reason: string;
}
```

The `reason` field should be internal metadata.

Do not expose detailed routing reasons to normal users.

---

# Workflow Categories

Acadedx AI workflows should be categorized before routing.

Recommended categories:

```text
INSIGHT_GENERATION
FEEDBACK_GENERATION
COMMUNICATION_DRAFT
REPORT_SUMMARY
OCR_EXTRACTION
OCR_CORRECTION
TUTORING
QUIZ_GENERATION
WORKSHEET_GENERATION
STUDY_PLAN_GENERATION
MODERATION
EMBEDDING
RAG_ANSWERING
CLASSIFICATION
TRANSLATION
ADMIN_ANALYSIS
```

---

# Model Capability Categories

Models should be classified by capability.

Recommended capability categories:

```text
FAST_TEXT
QUALITY_TEXT
REASONING
LONG_CONTEXT
STRUCTURED_OUTPUT
VISION
EMBEDDINGS
MODERATION
LOW_COST
HIGH_SAFETY
MULTILINGUAL
```

Example:

| Capability          | Use                                       |
| ------------------- | ----------------------------------------- |
| `FAST_TEXT`         | Short drafts, simple summaries            |
| `QUALITY_TEXT`      | Guardian communication, polished feedback |
| `REASONING`         | Learner insights, complex analysis        |
| `LONG_CONTEXT`      | Report summaries, document analysis       |
| `STRUCTURED_OUTPUT` | JSON-based insights, quiz generation      |
| `VISION`            | Image/PDF OCR support                     |
| `EMBEDDINGS`        | RAG retrieval                             |
| `MODERATION`        | Safety classification                     |
| `LOW_COST`          | High-volume simple tasks                  |
| `HIGH_SAFETY`       | Learner-facing or guardian-facing content |

---

# Recommended Routing Strategy

## MVP Strategy

MVP should start simple.

Recommended MVP routing:

```text
One primary text model
One fallback text model
One OCR/vision-capable model if OCR is included
One embedding model if RAG is included
One moderation path
```

Do not overbuild multi-provider orchestration before product usage validates it.

## Future Strategy

Future routing may support:

```text
multi-provider fallback
cost-based routing
latency-based routing
tenant-specific model preferences
model A/B testing
prompt-model compatibility matrix
regional routing
self-hosted model fallback
specialized education models
```

---

# Workflow-to-Model Routing Matrix

## Learner Insight Summary

Workflow:

```text
AI_LEARNER_INSIGHT
```

Recommended capabilities:

```text
REASONING
STRUCTURED_OUTPUT
HIGH_SAFETY
```

Settings:

| Setting      | Recommendation                                        |
| ------------ | ----------------------------------------------------- |
| Temperature  | Low to medium                                         |
| Output       | Structured JSON                                       |
| Human review | Required                                              |
| Context      | Attendance + assessment summary, not full raw records |
| Fallback     | Quality text or reasoning model                       |

Notes:

* Avoid deterministic high-stakes claims.
* Do not diagnose learner conditions.
* Do not expose insight externally without review.

---

## Assessment Feedback Draft

Workflow:

```text
AI_ASSESSMENT_FEEDBACK
```

Recommended capabilities:

```text
QUALITY_TEXT
STRUCTURED_OUTPUT
HIGH_SAFETY
```

Settings:

| Setting      | Recommendation                                    |
| ------------ | ------------------------------------------------- |
| Temperature  | Medium                                            |
| Output       | Structured feedback sections                      |
| Human review | Required                                          |
| Context      | Assessment, learner result, subject/class context |
| Fallback     | Quality text model                                |

Notes:

* Feedback should be editable.
* Teacher must review before publishing.
* Avoid harsh or discouraging language.

---

## Guardian Communication Draft

Workflow:

```text
AI_COMMUNICATION_DRAFT
```

Recommended capabilities:

```text
QUALITY_TEXT
HIGH_SAFETY
MULTILINGUAL optional
```

Settings:

| Setting      | Recommendation                             |
| ------------ | ------------------------------------------ |
| Temperature  | Medium                                     |
| Output       | Text or structured draft                   |
| Human review | Required                                   |
| Context      | Purpose, allowed learner summary, audience |
| Fallback     | Quality text model                         |

Notes:

* Never send automatically.
* Avoid sensitive details in subject lines.
* Use respectful, professional tone.

---

## Report Summary

Workflow:

```text
AI_REPORT_SUMMARY
```

Recommended capabilities:

```text
LONG_CONTEXT
REASONING
STRUCTURED_OUTPUT
```

Settings:

| Setting      | Recommendation                             |
| ------------ | ------------------------------------------ |
| Temperature  | Low                                        |
| Output       | Structured summary                         |
| Human review | Required before export or external sharing |
| Context      | Aggregated report data                     |
| Fallback     | Long-context text model                    |

Notes:

* Prefer aggregate data.
* Include filters and scope in summary metadata.
* Do not include unauthorized rows.

---

## OCR Extraction or Correction

Workflow:

```text
OCR_EXTRACT_TEXT
OCR_CORRECT_TEXT
```

Recommended capabilities:

```text
VISION
STRUCTURED_OUTPUT
```

Settings:

| Setting      | Recommendation                |
| ------------ | ----------------------------- |
| Temperature  | Low                           |
| Output       | Text or structured extraction |
| Human review | Required for low confidence   |
| Context      | Source file only              |
| Fallback     | OCR provider or vision model  |

Notes:

* OCR output may contain sensitive data.
* Do not log extracted text casually.
* Respect file access rules.

---

## AI Tutor

Workflow:

```text
AI_TUTOR_CHAT
```

Recommended capabilities:

```text
QUALITY_TEXT
REASONING
HIGH_SAFETY
LOW_LATENCY
```

Settings:

| Setting      | Recommendation                                |
| ------------ | --------------------------------------------- |
| Temperature  | Medium                                        |
| Output       | Conversational text                           |
| Human review | Not per message, but safety controls required |
| Context      | Minimal learner/task context                  |
| Fallback     | Fast safe text model                          |

MVP status:

```text
Optional / Deferred
```

Notes:

* Learner-facing AI requires stronger safety controls.
* Must not provide harmful or inappropriate guidance.
* Must avoid overconfident unsupported claims.

---

## Quiz Generation

Workflow:

```text
AI_QUIZ_GENERATION
```

Recommended capabilities:

```text
STRUCTURED_OUTPUT
QUALITY_TEXT
```

Settings:

| Setting      | Recommendation                                                       |
| ------------ | -------------------------------------------------------------------- |
| Temperature  | Medium                                                               |
| Output       | JSON question schema                                                 |
| Human review | Optional for learner self-practice, required for official assessment |
| Context      | Subject, topic, class level, difficulty                              |
| Fallback     | Structured output model                                              |

MVP status:

```text
Optional / Deferred
```

---

## Worksheet Generation

Workflow:

```text
AI_WORKSHEET_GENERATION
```

Recommended capabilities:

```text
STRUCTURED_OUTPUT
QUALITY_TEXT
```

Settings:

| Setting      | Recommendation                       |
| ------------ | ------------------------------------ |
| Temperature  | Medium                               |
| Output       | Structured worksheet                 |
| Human review | Required before teacher distribution |
| Context      | Subject, class, topic, difficulty    |
| Fallback     | Quality text model                   |

MVP status:

```text
Optional / Deferred
```

---

## Content Moderation

Workflow:

```text
AI_CONTENT_MODERATION
```

Recommended capabilities:

```text
MODERATION
FAST_TEXT
HIGH_SAFETY
```

Settings:

| Setting      | Recommendation                                      |
| ------------ | --------------------------------------------------- |
| Temperature  | Low                                                 |
| Output       | Structured classification                           |
| Human review | Required for ambiguous high-risk cases              |
| Context      | Content to classify only                            |
| Fallback     | Rule-based classifier or alternate moderation model |

Notes:

* Moderation should be fast and cheap.
* Use structured classifications.
* Do not send unnecessary tenant data.

---

## Embeddings

Workflow:

```text
AI_EMBEDDING
```

Recommended capabilities:

```text
EMBEDDINGS
LOW_COST
```

Settings:

| Setting      | Recommendation                                                    |
| ------------ | ----------------------------------------------------------------- |
| Output       | Vector                                                            |
| Human review | Not applicable                                                    |
| Context      | Approved document chunks                                          |
| Scope        | Organization and Institution metadata required                    |
| Fallback     | Alternate embedding model only if vector compatibility is handled |

Notes:

* Embedding model changes affect vector compatibility.
* Store embedding model version.
* Scope every vector with Organization and Institution metadata.

---

# Routing Configuration

Routing should be configuration-driven.

Recommended configuration shape:

```ts
export const aiRoutingConfig = {
  workflows: {
    AI_LEARNER_INSIGHT: {
      primary: {
        provider: 'primaryProvider',
        model: 'reasoning-model',
      },
      fallback: [
        {
          provider: 'secondaryProvider',
          model: 'quality-text-model',
        },
      ],
      responseFormat: 'STRUCTURED',
      safetyLevel: 'HIGH',
      temperature: 0.3,
      timeoutMs: 60000,
      maxOutputTokens: 1200,
    },
  },
};
```

MVP may keep this in code.

Future versions may store routing config in the database with admin controls and audit logs.

---

# Tenant-Specific Routing

Future enterprise customers may require tenant-specific routing.

Examples:

```text
Organization-specific provider
Institution-specific AI feature disablement
Regional provider selection
Data residency rules
Cost controls by Organization
Custom safety settings
```

MVP should not implement full tenant-specific routing unless required.

However, routing architecture must include:

```text
organizationId
institutionId
```

so future configuration can be applied.

---

# Model Selection Criteria

Models should be selected based on these criteria.

## Quality

Use higher-quality models when:

* Output affects learner records.
* Output may be shared with guardians.
* Output summarizes official reports.
* Output requires nuanced reasoning.
* Output requires safer tone.

## Cost

Use lower-cost models when:

* Workflow is high-volume.
* Task is simple.
* Output is internal only.
* Task is classification or extraction.
* Human review is expected.

## Latency

Use faster models when:

* User is waiting interactively.
* Workflow is chat-like.
* Output is short.
* Task is simple.

Use background jobs when:

* Output can take longer.
* Workflow is report-based.
* Context is large.
* Provider latency is variable.

## Context Length

Use long-context models when:

* Summarizing reports.
* Processing long documents.
* Combining multiple academic summaries.
* RAG context is large.

Do not send large context unnecessarily.

## Safety

Use safer models or stricter safety checks when:

* Learners are involved.
* Guardians receive output.
* Output may influence academic decisions.
* Sensitive personal data is included.
* AI is learner-facing.

---

# Temperature Guidelines

Suggested temperature by workflow:

| Workflow                   | Temperature |
| -------------------------- | ----------: |
| Moderation                 |   0.0 - 0.2 |
| OCR correction             |   0.0 - 0.2 |
| Structured learner insight |   0.2 - 0.4 |
| Report summary             |   0.2 - 0.4 |
| Assessment feedback        |   0.4 - 0.7 |
| Communication draft        |   0.4 - 0.7 |
| Tutor chat                 |   0.5 - 0.8 |
| Creative learning content  |   0.6 - 0.9 |

Use lower temperature for factual or structured workflows.

Use moderate temperature for communication and teaching tone.

---

# Token and Context Budgeting

Every workflow must define token budgets.

Recommended budget fields:

```text
maxInputTokens
maxOutputTokens
reservedSystemTokens
reservedOutputTokens
```

Rules:

* Trim context before sending to model.
* Prefer summaries over raw records where possible.
* Reject oversized requests safely.
* Use `AI_CONTEXT_TOO_LARGE` for context overflow.
* Do not silently drop critical context without marking it.

---

# Context Trimming Strategy

When context is too large:

1. Remove non-essential metadata.
2. Use aggregated summaries instead of raw records.
3. Limit date range.
4. Limit number of records.
5. Ask user to narrow scope, where appropriate.
6. Use long-context model only if justified.
7. Return safe error if still too large.

Relevant error:

```text
AI_CONTEXT_TOO_LARGE
```

---

# Structured Output Routing

Workflows requiring application logic should use structured output.

Structured output workflows:

```text
learner insights
assessment feedback
quiz generation
worksheet generation
moderation
report summary
OCR extraction
```

Structured output should define:

* JSON schema
* Required fields
* Validation rules
* Safety rules
* Fallback behavior

If model cannot reliably produce structured output, use another model or validation-repair flow.

---

# Output Repair

Output repair may be used when AI returns invalid structured output.

Rules:

* Repair only if safe.
* Limit repair attempts.
* Do not repair unsafe content into acceptable content without review.
* Log repair attempt metadata.
* Return `AI_OUTPUT_INVALID` if repair fails.

Suggested max repair attempts:

```text
1
```

for MVP.

---

# Fallback Strategy

Fallbacks should be explicit.

Fallback may be used for:

```text
provider timeout
provider unavailable
model unavailable
rate limit
structured output failure, where safe
```

Fallback should not be used blindly for:

```text
authorization failure
tenant scope violation
entitlement failure
unsafe output
validation error
privacy policy violation
```

Fallback chain example:

```text
Primary quality model
    ↓
Secondary quality model
    ↓
Fast text model for simplified output
    ↓
Fail safely
```

---

# Fallback Error Mapping

If all fallbacks fail, return stable errors:

| Condition            | Error Code                  |
| -------------------- | --------------------------- |
| Provider unavailable | `AI_PROVIDER_UNAVAILABLE`   |
| Provider timeout     | `AI_PROVIDER_TIMEOUT`       |
| Model unavailable    | `AI_MODEL_NOT_AVAILABLE`    |
| No routing match     | `AI_MODEL_SELECTION_FAILED` |
| Invalid output       | `AI_OUTPUT_INVALID`         |
| Unsafe output        | `AI_OUTPUT_UNSAFE`          |

---

# Retry Strategy

Retry only transient failures.

Retryable:

```text
AI_PROVIDER_TIMEOUT
AI_PROVIDER_UNAVAILABLE
PROVIDER_TIMEOUT
SERVICE_UNAVAILABLE
```

Non-retryable:

```text
AI_OUTPUT_UNSAFE
TENANT_SCOPE_VIOLATION
AUTH_FORBIDDEN
ENTITLEMENT_REQUIRED
USAGE_LIMIT_EXCEEDED
VALIDATION_ERROR
AI_CONTEXT_TOO_LARGE
```

Recommended MVP retry:

```text
maxAttempts: 2 or 3
exponential backoff with jitter
```

---

# Routing and Background Jobs

Long-running AI routes should use background jobs.

Recommended background workflows:

```text
AI_LEARNER_INSIGHT
AI_REPORT_SUMMARY
OCR_EXTRACT_TEXT
BULK_AI_FEEDBACK
WORKSHEET_GENERATION
```

Interactive workflows may be synchronous:

```text
short communication draft
short assessment feedback
simple summary
```

Use background jobs when latency may exceed user expectations.

---

# Cost Controls

LLM routing must support cost controls.

Cost controls include:

* Usage limits by feature.
* Token budget per workflow.
* Low-cost routing for simple tasks.
* Expensive model approval for high-value workflows.
* Organization-level usage caps.
* Institution-level usage caps.
* User-level rate limits.
* Background processing for expensive jobs.
* Usage tracking.

Do not route every task to the most expensive model by default.

---

# Safety Levels

Recommended safety levels:

```text
LOW
STANDARD
HIGH
```

## LOW

Use for:

```text
internal metadata classification
non-sensitive formatting
simple admin summaries
```

## STANDARD

Use for:

```text
teacher-facing drafts
internal report summaries
non-sensitive academic content
```

## HIGH

Use for:

```text
learner-facing AI
guardian communication
learner insights
assessment feedback
sensitive data processing
```

High safety workflows require stronger output validation and human review where applicable.

---

# Human Review Routing

Some workflows require review regardless of model quality.

Human review required:

```text
guardian communication draft
learner insight
official report summary
assessment feedback shared externally
disciplinary or sensitive guidance
institution-wide announcement draft
```

Routing decision should include:

```text
requiresHumanReview: true
```

The UI must display review-required state.

---

# Regional and Data Residency Routing

Future Acadedx deployments may require regional routing.

Potential factors:

```text
Organization region
Institution country
Data residency requirement
Provider region availability
Legal requirement
Customer contract
```

MVP may not need regional routing, but routing config should not block it.

---

# Prompt-Model Compatibility

Each prompt template should declare compatible model families.

Prompt metadata:

```text
workflow
promptTemplateId
promptVersion
compatibleModelFamilies
requiresStructuredOutput
maxContextTokens
safetyLevel
```

The router must not select a model that is incompatible with the prompt.

---

# Model Versioning

Model versions should be recorded with AI results.

Store:

```text
provider
model
modelVersion where available
routingConfigVersion
promptTemplateId
promptVersion
```

Reason:

* Debugging
* Quality review
* Auditability
* Reproducibility
* Cost analysis

---

# Observability

Every routed AI request should log safe metadata:

```text
requestId
organizationId
institutionId
userId
workflow
featureCode
provider
model
routingDecisionId
promptTemplateId
promptVersion
inputTokens
outputTokens
latencyMs
status
errorCode
fallbackUsed
createdAt
```

Do not log:

```text
raw prompts by default
raw AI responses with personal data
learner-sensitive data
guardian contact data
provider secrets
API keys
```

---

# Routing Audit Requirements

Audit routing changes, not every internal routing decision.

Audit required for:

```text
routing configuration update
model enablement or disablement
provider key change
prompt-model compatibility change
safety level change
fallback chain change
tenant-specific routing override
```

Sensitive AI actions may also require audit logs as defined in `AI_ENGINE.md`.

---

# Error Codes

Relevant routing errors:

```text
AI_MODEL_SELECTION_FAILED
AI_MODEL_NOT_AVAILABLE
AI_PROVIDER_ERROR
AI_PROVIDER_UNAVAILABLE
AI_PROVIDER_TIMEOUT
AI_OUTPUT_INVALID
AI_OUTPUT_UNSAFE
AI_CONTEXT_TOO_LARGE
AI_PROMPT_TEMPLATE_NOT_FOUND
AI_PROMPT_RENDER_FAILED
ENTITLEMENT_REQUIRED
USAGE_LIMIT_EXCEEDED
TENANT_SCOPE_VIOLATION
```

Definitions are maintained in:

```text
docs/06-API/ERROR_CODES.md
```

---

# MVP LLM Routing Requirements

The MVP must implement:

```text
AiModelRouterService
Provider abstraction
Workflow-based routing config
Primary model per workflow
Fallback model per workflow where practical
Structured output support where required
Token budget per workflow
Timeout per workflow
Safe provider error mapping
Usage tracking metadata
Prompt-model compatibility metadata
Tenant-aware routing request
Safe logging of routing decisions
```

MVP workflows should focus on:

```text
AI_LEARNER_INSIGHT
AI_ASSESSMENT_FEEDBACK
AI_COMMUNICATION_DRAFT
AI_REPORT_SUMMARY
OCR_EXTRACT_TEXT, if OCR is included
AI_CONTENT_MODERATION, if moderation is included
```

---

# Deferred LLM Routing Capabilities

The following may be deferred beyond MVP:

```text
Multi-provider dynamic routing
Cost optimizer
Latency optimizer
Automatic model benchmarking
Prompt A/B testing
Tenant-specific routing UI
Regional routing
Self-hosted model fallback
Fine-tuned model routing
Advanced evaluation pipeline
Automated hallucination scoring
Multi-agent routing
Streaming response routing
```

Do not block these future capabilities with hardcoded single-model architecture.

---

# LLM Routing Anti-Patterns

Avoid:

```text
Calling provider SDKs directly from controllers
One global model for every task forever
No fallback path for provider outage
No timeout per workflow
No usage tracking
No token budget
No structured output validation
Sending full learner records for simple tasks
Routing without organizationId and institutionId
Using raw provider error messages in UI
Automatically retrying unsafe outputs
Using the most expensive model for every task
Changing model configuration without audit
Hardcoding provider-specific logic in domain services
```

---

# LLM Routing Review Checklist

Before approving LLM routing implementation, confirm:

* Workflow is clearly identified.
* Required model capabilities are defined.
* Organization and Institution context are included.
* Prompt template compatibility is checked.
* Token budget is defined.
* Timeout is defined.
* Temperature is defined.
* Structured output requirement is defined.
* Safety level is defined.
* Human review requirement is defined.
* Fallback chain is defined where needed.
* Provider errors map to stable error codes.
* Usage metadata is recorded.
* Routing decision is safely logged.
* No raw sensitive data is logged.
* Routing config changes are auditable.
* No `schoolId`, `studentId`, or `parentId` appears in AI backend design.

---

# Related Documents

* docs/07-AI/AI_ENGINE.md
* docs/07-AI/PROMPTS.md
* docs/07-AI/OCR.md
* docs/07-AI/RAG.md
* docs/07-AI/PERSONALIZATION.md
* docs/07-AI/CONTENT_MODERATION.md
* docs/07-AI/MODEL_SELECTION.md
* docs/07-AI/PROMPT_ENGINEERING.md
* docs/09-Backend/BACKGROUND_JOBS.md
* docs/06-API/ERROR_CODES.md
* docs/11-Security/PRIVACY.md
* docs/11-Security/SECURITY.md