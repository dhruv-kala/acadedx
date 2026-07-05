# Acadedx Microservices Strategy

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the microservices strategy for Acadedx.

It explains why Acadedx should **not** start as a microservices-based system, which modules may become microservices in the future, and what design rules must be followed so future service extraction remains possible.

---

# Executive Decision

Acadedx will start as a **Modular Monolith**, not as microservices.

The constraint here is execution speed, operational simplicity, and MVP delivery.

Microservices introduce complexity that is not justified before product-market fit, usage scale, team size, and operational maturity are proven.

---

# MVP Architecture Decision

## Approved for MVP

```text
Modular Monolith
```

## Not Approved for MVP

```text
Distributed Microservices
```

---

# Why Not Microservices Initially

Starting with microservices would increase complexity in:

- Deployment
- Local development
- Debugging
- Observability
- Distributed transactions
- Network failures
- Data consistency
- DevOps workload
- Testing
- Cost
- AI-agent implementation accuracy

For Acadedx, the first product risk is not whether the system can handle millions of users.

The first product risk is whether students, parents, teachers, and institutions find the platform valuable enough to use regularly.

The architecture should optimize for learning validation first.

---

# Modular Monolith Definition

A modular monolith means the application is deployed as one backend system but internally organized into clear domain modules.

Example:

```text
services/api/src/modules/
├── identity/
├── organizations/
├── institutions/
├── users/
├── academic/
├── homework/
├── ocr/
├── ai/
├── notes/
├── quiz/
├── worksheets/
├── study-planner/
├── analytics/
├── notifications/
├── commerce/
├── payments/
├── admin/
├── audit/
└── storage/
```

Each module should behave like a future service boundary.

---

# Core Rule

Build modules as if they may become services later.

Do not deploy them as services initially.

---

# Future Service Boundary Principles

A module may become a microservice later only when it has:

- Clear ownership
- High independent scaling needs
- High operational isolation needs
- High security isolation needs
- Independent release cadence
- Clear APIs
- Minimal synchronous dependency on other modules
- Business justification

Do not extract a service only because the codebase is growing.

---

# Current Platform Domains

Acadedx contains these major domains:

```text
Identity
Organizations
Institutions
Academic
Users
Learning
AI
OCR
Analytics
Notifications
Commerce
Payments
Administration
Audit
Storage
Reports
Support
```

Not every domain should become a service.

---

# Future Microservice Candidates

The following modules are the strongest candidates for future extraction.

| Candidate Service | Extraction Reason | Likely Timing |
|---|---|---|
| AI Service | High cost, provider routing, prompt testing, model experiments | Growth / V2 |
| OCR Service | CPU/API-heavy file processing | Growth / V2 |
| Notification Service | Delivery retries, multiple channels, high fan-out | Growth / V2 |
| Payment Service | Security isolation, gateway adapters, financial workflows | V2 / Enterprise |
| Analytics Service | High-volume events and aggregation | V2 / Enterprise |
| Reports Service | Long-running report generation | V2 |
| Search Service | Dedicated search indexing | Future |
| File Processing Service | Heavy PDF/image/document processing | Future |

---

# Modules That Should Stay Together Initially

These modules should remain inside the modular monolith during MVP:

- Identity
- Users
- Organizations
- Institutions
- Academic
- Homework
- Notes
- Quiz
- Worksheets
- Study Planner
- Commerce
- Admin
- Audit

Reason:

These modules share business workflows, user context, organization context, institution context, and transaction boundaries.

Splitting them too early would create unnecessary distributed complexity.

---

# Service Extraction Readiness Checklist

A module is eligible for service extraction only when:

- Its module API is stable.
- Its database ownership is clear.
- It has high independent scaling needs.
- It has dedicated tests.
- It has clear event contracts.
- It can operate independently.
- It has observability.
- It has documented failure behavior.
- It has a deployment and rollback strategy.
- Product and architecture approve the extraction.

---

# Data Ownership Rules

Every future service must own its own data.

For MVP modular monolith:

- PostgreSQL may be shared.
- Prisma may manage all schema.
- Modules must still respect ownership boundaries.

Future service extraction may require:

- Separate schemas
- Separate databases
- Event replication
- API-based access
- Data migration strategy

---

# Cross-Module Access Rules

Inside the modular monolith, modules should communicate through public services or interfaces.

Allowed:

```text
HomeworkService → AiService
HomeworkService → EntitlementService
QuizService → AnalyticsEventService
```

Avoid:

```text
HomeworkRepository directly querying AI module tables
QuizService directly modifying subscription records
PaymentService directly changing user roles
```

---

# Event-Driven Architecture

Acadedx should use domain events where useful.

Examples:

```text
UserRegistered
OrganizationCreated
InstitutionCreated
HomeworkSolved
QuizCompleted
WorksheetGenerated
StudyPlanCreated
SubscriptionActivated
PaymentSuccessful
PaymentFailed
AiRequestCompleted
```

MVP implementation may use in-process events.

Future implementation may use:

- Message broker
- Queue
- Event bus
- Stream processing

Do not introduce Kafka or similar infrastructure in MVP unless justified.

---

# Synchronous vs Asynchronous Communication

## Use Synchronous Calls For

- Authentication
- Authorization
- Entitlement checks
- Simple reads
- User-facing immediate workflows
- Required validation

## Use Asynchronous Jobs For

- OCR processing
- AI generation that may take time
- Email delivery
- Push notifications
- Report generation
- Analytics aggregation
- Payment webhook processing
- File processing

---

# Future Service Communication

When services are extracted, communication may use:

- REST APIs
- Events
- Queues
- Webhooks
- gRPC only if strongly justified

REST and events should be preferred initially because they are easier to develop, debug, test, and operate.

---

# Organization and Institution Context

Every future service must preserve:

- organizationId
- institutionId where applicable
- userId
- role
- permissions
- requestId

This context must flow through:

- API calls
- Events
- Jobs
- Logs
- Audit records
- Analytics events

Loss of tenant context is a critical architecture defect.

---

# Tenant Isolation in Future Services

Every service must enforce tenant isolation independently.

Do not assume the API Gateway or frontend has already filtered data correctly.

Rules:

- Organization data must be scoped by organizationId.
- Institution data must be scoped by institutionId.
- Student data must be scoped by ownership or relationship.
- Teacher access must be scoped by assignments.
- Parent access must be scoped by linked students.
- Platform admin access must be audited.

---

# AI Service Extraction Strategy

The AI module is the most likely first extraction candidate.

## Current MVP

AI lives inside the modular monolith as a domain module.

## Future AI Service

May own:

- Prompt templates
- Prompt versions
- Model routing
- Provider adapters
- AI safety
- AI evaluations
- Token usage
- Cost tracking
- AI request logs

## Extraction Trigger

Extract AI when:

- AI traffic becomes high.
- AI cost optimization requires specialized logic.
- Multiple applications need direct AI capabilities.
- AI experimentation requires independent releases.
- AI failures need isolation from the main API.

---

# OCR Service Extraction Strategy

OCR may become a service when file processing becomes heavy.

## Future OCR Service May Own

- OCR jobs
- OCR provider adapters
- Image preprocessing
- PDF processing
- OCR confidence scoring
- OCR result storage
- OCR retry handling

## Extraction Trigger

Extract OCR when:

- OCR processing affects API performance.
- OCR jobs require independent scaling.
- OCR providers require specialized retry logic.
- File processing workload becomes large.

---

# Notification Service Extraction Strategy

Notifications may become a service when channels and volume grow.

## Future Notification Service May Own

- Notification templates
- Notification preferences
- Delivery queues
- Email provider adapters
- Push provider adapters
- SMS provider adapters
- WhatsApp provider adapters
- Delivery analytics

## Extraction Trigger

Extract Notifications when:

- Delivery volume grows.
- Multi-channel retry logic becomes complex.
- Notification failures should not affect main API.
- Marketing and transactional messages require separate operations.

---

# Payment Service Extraction Strategy

Payments may become a service for security and operational isolation.

## Future Payment Service May Own

- Payment providers
- Webhooks
- Transactions
- Invoices
- Refunds
- Taxes
- Reconciliation
- Financial audit logs

## Extraction Trigger

Extract Payments when:

- Multiple gateways are active.
- Financial workflows become complex.
- Compliance requirements increase.
- Enterprise billing is introduced.
- Security isolation is required.

---

# Analytics Service Extraction Strategy

Analytics may become a service when event volume grows.

## Future Analytics Service May Own

- Event ingestion
- Aggregations
- Learning Mastery calculations
- Institution analytics
- Organization analytics
- Product analytics
- AI analytics
- Reporting datasets

## Extraction Trigger

Extract Analytics when:

- Event volume becomes high.
- Reporting queries slow down transactional systems.
- Dashboards require near-real-time aggregation.
- Institution and organization analytics become commercially important.

---

# Reports Service Extraction Strategy

Reports may become a separate service when report generation becomes heavy.

## Future Reports Service May Own

- PDF generation
- Excel exports
- Scheduled reports
- Report templates
- Report storage
- Report delivery

## Extraction Trigger

Extract Reports when:

- Report generation slows the API.
- Reports need scheduling.
- Large institutions request heavy exports.
- Multi-tenant reporting becomes complex.

---

# Search Service Extraction Strategy

Search should start simple.

## MVP Search

Use PostgreSQL search.

## Future Search

Use OpenSearch or Elasticsearch only when needed.

## Extraction Trigger

Extract Search when:

- Search queries become slow.
- Full-text search requirements expand.
- Cross-module search becomes complex.
- Ranking and relevance become important.

---

# Anti-Patterns

Avoid these patterns:

## Distributed Monolith

Multiple services that cannot be deployed independently.

---

## Shared Database Microservices

Services claiming independence but directly sharing and modifying the same tables.

---

## Premature Event Bus

Adding Kafka or complex event infrastructure before clear need.

---

## Service Per Module

Creating a separate service for every module without operational justification.

---

## Frontend-Driven Service Boundaries

Creating services based on pages instead of business domains.

---

## AI Provider Coupling

Letting feature modules directly call OpenAI, Claude, Gemini, or other providers.

---

## Payment Provider Coupling

Letting subscription logic directly depend on Razorpay, Stripe, PayPal, or other providers.

---

# Migration Path from Modular Monolith to Services

## Step 1 — Modular Boundaries

Keep clean module boundaries inside the monolith.

---

## Step 2 — Public Interfaces

Each module exposes public application services.

---

## Step 3 — Event Contracts

Important business actions emit events.

---

## Step 4 — Data Ownership

Clarify which module owns which tables.

---

## Step 5 — Extract Background Workers

Move heavy processing into worker processes first.

---

## Step 6 — Extract First Service

Extract the module with the strongest operational need.

Likely first candidate:

```text
AI Service
```

or

```text
OCR Service
```

---

## Step 7 — Independent Deployment

Deploy extracted service separately with clear monitoring and rollback.

---

# MVP Deployment Model

MVP should use:

```text
Web App
Admin App
Backend API
Worker
PostgreSQL
Redis
Object Storage
```

Do not deploy each module separately.

---

# Future Deployment Model

Future architecture may include:

```text
Web App
Admin App
API Gateway
Identity Service
Core API
AI Service
OCR Service
Notification Service
Payment Service
Analytics Service
Reports Service
PostgreSQL Databases
Redis
Object Storage
Event Bus
```

This is future-state only.

---

# Service Extraction Decision Framework

Before extracting a service, answer:

1. What problem does extraction solve?
2. Is the problem real today or speculative?
3. Can the problem be solved inside the modular monolith?
4. Does extraction improve scalability, security, cost, or release independence?
5. What data will the service own?
6. How will other modules communicate with it?
7. How will failures be handled?
8. How will local development work?
9. How will testing work?
10. What is the rollback plan?

If the answers are weak, do not extract the service.

---

# Product Manager Position

From a product perspective, microservices are not a user-visible benefit.

Users care about:

- Fast homework solving
- Accurate AI explanations
- Reliable dashboards
- Smooth payments
- Useful analytics
- Secure data

Microservices should be introduced only when they help deliver those outcomes more reliably.

---

# Architecture Decision

For Acadedx:

```text
Start modular.
Design clean boundaries.
Use events where useful.
Extract services only when justified.
```

This is the approved strategy.

---

# Related Documents

- SYSTEM_ARCHITECTURE.md
- TECH_STACK.md
- PROJECT_STRUCTURE.md
- CODING_STANDARDS.md
- MODULES.md
- DATABASE_SCHEMA.md
- API_GUIDELINES.md