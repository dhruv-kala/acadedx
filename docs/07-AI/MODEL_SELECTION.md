# Model Selection

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** AI Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the model selection standards for the Acadedx AI Engine.

Model selection determines which AI model families, providers, capabilities, safety profiles, cost tiers, latency targets, and deployment options are appropriate for each Acadedx AI workflow.

Acadedx is a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

Model selection must use:

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

except in explicitly approved UI labels, generated user-facing copy, or legacy adapters.

---

# Model Selection Goals

Model selection must ensure:

1. Each AI workflow uses a model appropriate to the task.
2. Learner and guardian-facing outputs use safer model configurations.
3. Costly models are used only where justified.
4. Low-cost models are used for simple or high-volume tasks.
5. OCR, RAG, moderation, and personalization use specialized model capabilities where needed.
6. Provider-specific implementation details do not leak into product services.
7. Models are evaluated for safety, reliability, latency, cost, and output quality.
8. Organization and Institution data boundaries are preserved.
9. MVP implementation remains simple.
10. Future multi-provider, tenant-specific, and regional routing can be added without redesign.

---

# Relationship to LLM Routing

`MODEL_SELECTION.md` defines how models are evaluated and chosen.

`LLM_ROUTING.md` defines how runtime routing decisions are made.

In short:

```text
MODEL_SELECTION.md = selection criteria and approved model classes
LLM_ROUTING.md = runtime routing and fallback behavior
```

Both documents must stay aligned.

---

# Model Selection Principles

## 1. Fit the Model to the Workflow

Do not use one generic model for every task.

Different workflows need different capabilities:

| Workflow               | Required Capability                                |
| ---------------------- | -------------------------------------------------- |
| Learner insight        | Reasoning, structured output, safety               |
| Assessment feedback    | Educational tone, structured feedback              |
| Guardian communication | Writing quality, safety, empathy                   |
| Report summary         | Summarization, long context                        |
| OCR                    | Vision or OCR capability                           |
| RAG                    | Embeddings, retrieval quality, grounded generation |
| Moderation             | Classification, safety                             |
| AI tutor               | Conversational quality, safety, latency            |
| Quiz generation        | Structured output, curriculum alignment            |

---

## 2. Safety Before Creativity

Acadedx handles learner, guardian, teacher, and academic data.

For learner-facing, guardian-facing, or high-impact workflows, prefer models and configurations with stronger safety and lower hallucination risk.

Creative generation should not override safety.

---

## 3. Cost Must Be Explicit

Every selected model must have a cost rationale.

Model selection should consider:

```text
input token cost
output token cost
embedding cost
OCR/image cost
average latency
expected usage volume
fallback cost
retry cost
```

Do not default all workflows to the most expensive model.

---

## 4. Structured Workflows Need Structured Output

For workflows that feed application logic, select models that reliably support structured output.

Structured output is required for:

```text
learner insights
assessment feedback
communication drafts
report summaries
quiz generation
worksheet generation
moderation
OCR extraction
classification
```

---

## 5. Provider Independence

Acadedx product services should not depend directly on a vendor-specific model.

Use:

```text
AiProviderService
AiModelRouterService
AiPromptService
AiSafetyService
```

Do not call vendor SDKs directly from controllers or domain services.

---

# Model Capability Categories

Models should be classified using capability tags.

Recommended capability tags:

```text
FAST_TEXT
QUALITY_TEXT
REASONING
LONG_CONTEXT
STRUCTURED_OUTPUT
VISION
OCR
EMBEDDINGS
MODERATION
CLASSIFICATION
MULTILINGUAL
LOW_COST
HIGH_SAFETY
LOCAL_DEPLOYABLE
STREAMING
```

A model may have multiple capabilities.

Example:

```json
{
  "model": "example-quality-model",
  "capabilities": [
    "QUALITY_TEXT",
    "STRUCTURED_OUTPUT",
    "HIGH_SAFETY"
  ]
}
```

---

# Model Classes

## Fast Text Models

Used for simple, low-risk, low-latency tasks.

Suitable for:

```text
short summaries
simple rewrites
classification
low-risk internal drafts
metadata cleanup
```

Avoid for:

```text
high-impact learner insights
guardian communication without review
complex reasoning
long report summarization
```

---

## Quality Text Models

Used for polished writing and nuanced language.

Suitable for:

```text
guardian communication drafts
teacher feedback drafts
announcement drafts
learner-friendly explanations
```

Requirements:

* Good instruction following
* Stable tone
* Low hallucination rate
* Safe handling of sensitive topics

---

## Reasoning Models

Used for deeper analysis.

Suitable for:

```text
learner insight generation
assessment pattern analysis
report interpretation
multi-step recommendation drafting
```

Requirements:

* Strong structured output support
* Clear limitation handling
* High safety performance
* Good groundedness

Avoid using reasoning models for high-volume simple tasks where a cheaper model is enough.

---

## Long-Context Models

Used when the input context is large.

Suitable for:

```text
report summaries
document summaries
RAG with multiple retrieved chunks
institution policy analysis
curriculum document analysis
```

Requirements:

* Long context handling
* Good summarization quality
* Reliable source grounding
* Token budget controls

Do not use long-context models as a substitute for proper context minimization.

---

## Vision / OCR Models

Used for image and document understanding.

Suitable for:

```text
OCR extraction
document image reading
worksheet image interpretation
scanned PDF understanding
```

Requirements:

* File type support
* Confidence or uncertainty handling
* Safe handling of uploaded documents
* Provider timeout handling
* Privacy-safe logging

OCR-specific standards are defined in:

```text
docs/07-AI/OCR.md
```

---

## Embedding Models

Used for vector search and RAG.

Suitable for:

```text
document embeddings
curriculum retrieval
institution policy retrieval
help center search
approved educational content retrieval
```

Requirements:

* Stable embedding dimension
* Good retrieval quality
* Reasonable cost
* Version tracking
* Reindexing plan for model changes

Embedding model choice must be recorded with each vector entry.

---

## Moderation Models

Used to classify unsafe or policy-violating content.

Suitable for:

```text
AI output moderation
user prompt moderation
guardian communication safety
prompt injection detection
privacy leak detection
learner-facing safety
```

Requirements:

* Low latency
* Stable classifications
* Low false-negative rate for critical categories
* Safe handling of sensitive content

Moderation standards are defined in:

```text
docs/07-AI/CONTENT_MODERATION.md
```

---

## Local or Self-Hosted Models

Local models may be useful for:

```text
development
cost control
privacy-sensitive workflows
offline experimentation
non-production testing
simple classification
```

MVP production should not depend on local models unless DGIS can operate them reliably.

Requirements for production local models:

* Security review
* Infrastructure capacity
* Monitoring
* Evaluation
* Update process
* Incident plan
* Cost analysis
* Data isolation controls

---

# Selection Criteria

Every model must be evaluated against these criteria.

## Functional Quality

Evaluate:

```text
instruction following
structured output reliability
reasoning quality
summarization quality
writing quality
OCR quality
retrieval quality
classification quality
```

## Safety

Evaluate:

```text
unsafe content refusal
learner-safe tone
guardian-safe communication
privacy leakage risk
prompt injection resistance
high-impact claim handling
moderation compatibility
```

## Privacy

Evaluate:

```text
provider data handling
retention terms
training-on-customer-data policy
regional processing options
sensitive data handling
logging controls
```

Legal and contractual review may be required before production use.

## Cost

Evaluate:

```text
per-token cost
per-image cost
embedding cost
minimum billing units
expected usage volume
retry cost
fallback cost
```

## Latency

Evaluate:

```text
median latency
p95 latency
timeout behavior
streaming support
batch support
provider reliability
```

## Reliability

Evaluate:

```text
provider uptime
rate limits
error rate
timeout rate
fallback availability
version stability
```

## Integration Fit

Evaluate:

```text
API maturity
SDK quality
structured output support
tool/function calling support
vision support
embedding support
moderation support
observability support
```

---

# Model Selection Matrix

Each candidate model should be evaluated using a matrix.

| Criterion              |             Weight | Notes                                            |
| ---------------------- | -----------------: | ------------------------------------------------ |
| Workflow quality       |               High | Must solve the task well                         |
| Safety                 |               High | Especially for learner/guardian-facing workflows |
| Privacy                |               High | Required for education data                      |
| Cost                   |             Medium | High-volume workflows need cost control          |
| Latency                |             Medium | Important for interactive workflows              |
| Structured output      |  High where needed | Required for app workflows                       |
| Context length         | Workflow-dependent | Important for reports/RAG                        |
| Provider reliability   |               High | Needed for production workflows                  |
| Operational complexity |             Medium | MVP should stay simple                           |

---

# Workflow Model Requirements

## Learner Insight

Required capabilities:

```text
REASONING
STRUCTURED_OUTPUT
HIGH_SAFETY
```

Selection priority:

1. Safety
2. Grounded reasoning
3. Structured output reliability
4. Cost
5. Latency

Human review:

```text
Required
```

Avoid models that frequently produce overconfident learner judgments.

---

## Assessment Feedback

Required capabilities:

```text
QUALITY_TEXT
STRUCTURED_OUTPUT
HIGH_SAFETY
```

Selection priority:

1. Supportive tone
2. Structured output
3. Safety
4. Cost
5. Latency

Human review:

```text
Required before publishing or sharing externally
```

---

## Guardian Communication Draft

Required capabilities:

```text
QUALITY_TEXT
HIGH_SAFETY
MULTILINGUAL optional
```

Selection priority:

1. Safety
2. Tone
3. Privacy compliance
4. Writing quality
5. Cost

Human review:

```text
Required
```

Do not select a model that tends to include sensitive details unnecessarily.

---

## Report Summary

Required capabilities:

```text
LONG_CONTEXT
REASONING
STRUCTURED_OUTPUT
```

Selection priority:

1. Grounded summarization
2. Long-context handling
3. Structured output
4. Cost
5. Latency

Human review:

```text
Required before official export or external sharing
```

---

## OCR

Required capabilities:

```text
VISION
OCR
STRUCTURED_OUTPUT optional
```

Selection priority:

1. Extraction accuracy
2. Confidence handling
3. File support
4. Cost
5. Latency

Human review:

```text
Required for low-confidence or official-record use
```

---

## RAG Answering

Required capabilities:

```text
QUALITY_TEXT
REASONING
STRUCTURED_OUTPUT optional
LONG_CONTEXT optional
```

Selection priority:

1. Groundedness
2. Source-use discipline
3. Safety
4. Context length
5. Cost

The model must be able to follow instructions such as:

```text
Use only retrieved context.
State limitations when context is insufficient.
```

---

## Moderation

Required capabilities:

```text
MODERATION
CLASSIFICATION
HIGH_SAFETY
LOW_LATENCY
```

Selection priority:

1. False-negative reduction for high-risk content
2. Latency
3. Cost
4. Structured classification

---

## Embeddings

Required capabilities:

```text
EMBEDDINGS
LOW_COST
```

Selection priority:

1. Retrieval quality
2. Cost
3. Dimension stability
4. Provider reliability
5. Multilingual performance, if needed

---

## AI Tutor

MVP status:

```text
Deferred or optional
```

Required capabilities if enabled:

```text
QUALITY_TEXT
REASONING
HIGH_SAFETY
LOW_LATENCY
```

Selection priority:

1. Learner safety
2. Teaching quality
3. Latency
4. Cost
5. Tone control

Learner-facing chat requires additional moderation and safety evaluation.

---

# MVP Model Selection Strategy

The MVP should avoid overcomplicated model portfolios.

Recommended MVP model portfolio:

```text
1 primary quality text model
1 fallback quality or fast text model
1 moderation path
1 OCR-capable model or OCR provider if OCR is included
1 embedding model if RAG is included
```

MVP should not require:

```text
many providers
model A/B testing
tenant-specific model routing
fine-tuned models
self-hosted production models
multi-agent orchestration
```

unless there is a clear launch requirement.

---

# Approved Model Registry

Acadedx should maintain an internal model registry.

Recommended fields:

```text
modelId
provider
modelName
modelFamily
capabilities
status
approvedWorkflows
safetyLevel
supportsStructuredOutput
supportsVision
supportsEmbeddings
supportsStreaming
maxContextTokens
maxOutputTokens
costTier
latencyTier
dataHandlingNotes
approvedBy
approvedAt
deprecatedAt
```

Model statuses:

```text
EVALUATING
APPROVED
APPROVED_FOR_DEV
APPROVED_FOR_PRODUCTION
DEPRECATED
BLOCKED
```

MVP may store this registry in code.

Future versions may store it in database or configuration management.

---

# Model Registry Example

```json
{
  "modelId": "quality-text-v1",
  "provider": "primary-provider",
  "modelName": "quality-text-model",
  "modelFamily": "QUALITY_TEXT",
  "capabilities": [
    "QUALITY_TEXT",
    "STRUCTURED_OUTPUT",
    "HIGH_SAFETY"
  ],
  "status": "APPROVED_FOR_PRODUCTION",
  "approvedWorkflows": [
    "AI_COMMUNICATION_DRAFT",
    "AI_ASSESSMENT_FEEDBACK"
  ],
  "supportsStructuredOutput": true,
  "supportsVision": false,
  "supportsEmbeddings": false,
  "maxContextTokens": 128000,
  "costTier": "STANDARD",
  "latencyTier": "STANDARD"
}
```

---

# Provider Selection

Provider selection must consider:

```text
security posture
privacy terms
data retention
training policy
regional availability
reliability
cost
model quality
API maturity
support
legal review
```

Before using a provider in production, confirm:

* Provider keys can be stored securely.
* Provider terms allow intended education use.
* Sensitive data handling is acceptable.
* Provider does not train on customer data unless explicitly approved.
* Provider supports required region or data handling requirements.
* Provider can meet expected volume and reliability.

---

# Data Handling Requirements

For any selected model/provider, document:

```text
whether prompts are retained
whether outputs are retained
whether customer data is used for training
available data processing controls
regional processing options
subprocessor considerations
logging behavior
deletion options
```

Do not send sensitive learner or guardian data to a provider without approved data handling terms.

---

# Model Configuration Standards

Each selected model must define default configuration.

Recommended configuration fields:

```text
temperature
topP
maxOutputTokens
timeoutMs
responseFormat
safetyLevel
retryPolicy
fallbackModels
```

Example:

```json
{
  "workflow": "AI_LEARNER_INSIGHT",
  "modelId": "reasoning-structured-v1",
  "temperature": 0.3,
  "maxOutputTokens": 1200,
  "timeoutMs": 60000,
  "responseFormat": "STRUCTURED_JSON",
  "safetyLevel": "HIGH"
}
```

---

# Temperature Selection

Suggested defaults:

| Workflow                  | Temperature |
| ------------------------- | ----------: |
| Moderation                |   0.0 - 0.2 |
| OCR correction            |   0.0 - 0.2 |
| Learner insight           |   0.2 - 0.4 |
| Report summary            |   0.2 - 0.4 |
| Assessment feedback       |   0.4 - 0.7 |
| Guardian communication    |   0.4 - 0.7 |
| AI tutor                  |   0.5 - 0.8 |
| Creative learning content |   0.6 - 0.9 |

Use lower temperature for factual, structured, or high-risk outputs.

---

# Structured Output Support

A model may be approved for a workflow only if it can reliably produce the required output format.

Required for:

```text
learner insights
assessment feedback
communication drafts
report summaries
moderation
OCR extraction
quiz generation
worksheet generation
classification
```

If structured output reliability is weak, either:

* Use a different model.
* Add validation and repair.
* Avoid using the model for that workflow.

---

# Context Length Requirements

Model context length must match workflow needs.

Examples:

| Workflow              | Context Requirement        |
| --------------------- | -------------------------- |
| Communication draft   | Low to medium              |
| Assessment feedback   | Low to medium              |
| Learner insight       | Medium                     |
| Report summary        | Medium to high             |
| RAG answer            | Medium to high             |
| OCR document analysis | Depends on file/page count |

Do not send large context just because the model supports it.

Context minimization still applies.

---

# Fallback Model Selection

Fallback models should be selected explicitly.

Fallback must be compatible with:

```text
prompt format
output schema
safety level
context length
workflow requirements
```

Fallback should not silently downgrade safety.

Bad fallback:

```text
High-safety guardian draft → unreviewed low-safety cheap model
```

Better fallback:

```text
High-safety guardian draft → alternate quality model with same review requirement
```

---

# Model Deprecation

Models may be deprecated due to:

```text
provider retirement
quality degradation
cost increase
safety concerns
privacy concerns
better replacement
regional restriction
```

Deprecation process:

1. Mark model as deprecated in registry.
2. Stop assigning new workflows.
3. Route new requests to replacement model.
4. Monitor fallback behavior.
5. Reprocess or preserve old outputs with metadata.
6. Remove after migration window where safe.

---

# Model Versioning

AI results must record model metadata.

Store:

```text
provider
model
modelVersion where available
modelRegistryId
routingConfigVersion
promptTemplateId
promptVersion
```

Reason:

* Debugging
* Auditability
* Reproducibility
* Cost analysis
* Quality review
* Incident investigation

---

# Model Evaluation

Before production approval, evaluate each model against representative test cases.

Evaluation categories:

```text
workflow quality
schema validity
groundedness
tone
safety
privacy leakage
prompt injection resistance
latency
cost
provider reliability
```

For high-risk workflows, manual review is required.

---

# Evaluation Dataset

MVP evaluation dataset should include synthetic and anonymized examples.

Do not use real learner data unless approved and protected.

Recommended test sets:

```text
learner insight examples
assessment feedback examples
guardian communication examples
report summary examples
OCR examples, if enabled
RAG queries, if enabled
moderation examples
prompt injection attempts
insufficient context examples
```

---

# Model Benchmarking

Benchmark metrics:

```text
valid output rate
schema failure rate
unsafe output rate
hallucination rate
insufficient-context handling rate
average latency
p95 latency
average cost per request
fallback rate
timeout rate
```

MVP benchmarking may be manual and lightweight, but high-risk AI workflows must be reviewed before production.

---

# Model Monitoring

Production monitoring should track:

```text
request count
success rate
failure rate
latency
timeout rate
provider error rate
fallback usage
cost estimate
token usage
moderation failure rate
unsafe output rate
schema failure rate
workflow-specific quality feedback
```

Alert on:

```text
provider outage
cost spike
schema failure spike
unsafe output spike
high fallback rate
latency degradation
model deprecation notice
privacy or safety incident
```

---

# Model Change Management

Model changes can alter product behavior.

Changes requiring review:

```text
primary model change
fallback model change
temperature change
max token change
safety level change
provider change
structured output mode change
embedding model change
moderation model change
```

High-risk workflow model changes require:

* Testing
* Safety review
* Prompt compatibility review
* Rollback plan
* Audit log

---

# Embedding Model Change Rules

Changing embedding models may invalidate existing vectors.

Before changing embedding model:

1. Confirm new dimension.
2. Confirm retrieval quality.
3. Create new index or versioned namespace.
4. Re-embed documents.
5. Avoid mixing incompatible embeddings.
6. Plan migration.
7. Monitor retrieval quality.

Embedding model metadata must be stored with every chunk.

---

# Local Development Models

Developers may use local models for development if configured.

Rules:

* Local models must not be assumed production-equivalent.
* Test results from local models do not guarantee production quality.
* Local models must not receive production data.
* Local provider behavior must be clearly marked.
* Prompt and schema tests should still run against approved production-like models before release.

---

# Environment-Specific Model Rules

## Local

Allowed:

```text
local mock provider
local LLM provider
stubbed AI responses
synthetic test data
```

Not allowed:

```text
production learner data
production provider keys
production prompts with sensitive data
```

## Development

Allowed:

```text
approved dev provider
synthetic data
limited testing
```

## Staging

Allowed:

```text
production-like provider
anonymized or synthetic data
release validation
```

## Production

Allowed:

```text
approved production models only
approved provider keys
monitored workflows
audited sensitive operations
```

---

# Model Selection and Privacy

Before approving a model for sensitive workflows, confirm:

* Provider data handling is acceptable.
* Sensitive learner data is minimized.
* Guardian contact data is avoided unless required.
* Raw prompts are not stored unnecessarily.
* Outputs are not used for provider training unless explicitly approved.
* Regional processing requirements are satisfied where needed.
* Legal review is completed where required.

---

# Model Selection and Security

Selected models and providers must support secure operation.

Security requirements:

```text
API keys stored securely
provider access restricted
timeouts configured
safe error mapping
no secrets in prompts
no raw provider errors to users
tenant context preserved
audit metadata recorded
```

---

# Model Selection and Entitlements

Some models may be tied to premium or costly features.

Entitlement checks should happen before model invocation.

Possible feature codes:

```text
ai.insight.generate
ai.assessmentFeedback.generate
ai.communicationDraft.generate
ai.reportSummary.generate
ai.ocr.create
ai.rag.query
ai.tutor.chat
```

Do not select model based directly on plan name.

Use feature entitlement and routing policy.

---

# Model Selection and Human Review

Model selection does not remove human review requirements.

Even if a high-quality model is used, human review is still required for:

```text
guardian communication drafts
learner insights
official report summaries
assessment feedback shared externally
high-impact educational recommendations
```

---

# Model Selection Error Codes

Relevant error codes:

```text
AI_MODEL_NOT_AVAILABLE
AI_MODEL_SELECTION_FAILED
AI_PROVIDER_ERROR
AI_PROVIDER_UNAVAILABLE
AI_PROVIDER_TIMEOUT
AI_OUTPUT_INVALID
AI_OUTPUT_UNSAFE
AI_CONTEXT_TOO_LARGE
ENTITLEMENT_REQUIRED
USAGE_LIMIT_EXCEEDED
TENANT_SCOPE_VIOLATION
```

Definitions are maintained in:

```text
docs/06-API/ERROR_CODES.md
```

---

# MVP Model Selection Requirements

The MVP must define:

```text
approved model registry
primary model for each enabled AI workflow
fallback model where practical
model capability tags
model configuration defaults
provider abstraction
structured output support
moderation path for high-risk workflows
embedding model if RAG is included
OCR model/provider if OCR is included
usage and cost metadata
model metadata persistence with AI results
safe error mapping
model evaluation examples
```

MVP enabled AI workflows should be limited to:

```text
AI_LEARNER_INSIGHT
AI_ASSESSMENT_FEEDBACK
AI_COMMUNICATION_DRAFT
AI_REPORT_SUMMARY
OCR_EXTRACT_TEXT, if OCR is included
AI_CONTENT_MODERATION, if moderation is included
RAG_QUERY, if RAG is included
```

---

# Deferred Model Selection Capabilities

The following may be deferred beyond MVP:

```text
tenant-specific model selection
regional model routing
automatic cost optimizer
model A/B testing
fine-tuned model selection
self-hosted production model fleet
custom education model benchmarks
advanced hallucination scoring
human feedback ranking
automated model regression pipeline
multi-agent model selection
model marketplace
```

Do not block these future capabilities with hardcoded single-model implementation.

---

# Model Selection Anti-Patterns

Avoid:

```text
one global model forever
provider SDK calls inside controllers
no model registry
no model version stored with result
no fallback policy
fallback to unsafe model
using expensive model for trivial tasks
using cheap model for high-risk guardian communication
no structured output validation
no provider timeout
no cost tracking
no safety evaluation
using real learner data in tests without approval
using schoolId, studentId, or parentId in AI backend design
```

---

# Model Selection Review Checklist

Before approving a model for production, confirm:

* Workflow purpose is clear.
* Required capability tags are defined.
* Provider is approved.
* Privacy terms are acceptable.
* Safety evaluation is completed.
* Prompt compatibility is confirmed.
* Structured output support is verified where needed.
* Token budget is defined.
* Cost estimate is documented.
* Latency is acceptable.
* Timeout is configured.
* Fallback is defined where needed.
* Usage tracking is configured.
* Model metadata will be persisted.
* Human review requirement is preserved.
* Tests use synthetic or approved data.
* No `schoolId`, `studentId`, or `parentId` is introduced in backend model selection design.

---

# Related Documents

* docs/07-AI/AI_ENGINE.md
* docs/07-AI/LLM_ROUTING.md
* docs/07-AI/PROMPTS.md
* docs/07-AI/OCR.md
* docs/07-AI/RAG.md
* docs/07-AI/PERSONALIZATION.md
* docs/07-AI/CONTENT_MODERATION.md
* docs/07-AI/PROMPT_ENGINEERING.md
* docs/06-API/ERROR_CODES.md
* docs/09-Backend/BACKGROUND_JOBS.md
* docs/11-Security/PRIVACY.md
* docs/11-Security/SECURITY.md