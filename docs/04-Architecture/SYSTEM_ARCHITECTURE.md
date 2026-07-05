# Acadedx System Architecture

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the high-level system architecture for Acadedx.

It explains how the platform should be structured across frontend applications, backend services, databases, AI services, authentication, authorization, organizations, institutions, analytics, commerce, notifications, administration, and deployment.

This document is the primary architecture reference for engineering implementation.

---

# Architecture Summary

Acadedx is an AI-powered education platform designed to support:

- Students
- Parents
- Teachers
- Institutions
- Organizations
- Platform administrators

The MVP will focus on students, parents, teachers, and schools.

The architecture must still support the future platform hierarchy:

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

---

# Core Architectural Principle

The system must be school-focused in MVP but institution-ready and organization-ready from day one.

This means:

- Do not hardcode `School` as the only education entity.
- Use `Institution` as the flexible academic unit.
- Use `Organization` as the business owner or parent account.
- Enforce one institution per organization in MVP through business rules.
- Do not design the database in a way that prevents multiple institutions later.

---

# Recommended Architecture Style

Acadedx should start as a **modular monolith** with clear domain boundaries.

The constraint here is execution efficiency.

A modular monolith is recommended because:

- MVP development is faster.
- Deployment is simpler.
- Local development is easier.
- Transactions are easier to manage.
- Domain boundaries can still be enforced.
- Future microservice extraction remains possible.

Do not start with distributed microservices for MVP.

---

# High-Level Architecture

```text
Client Applications
    ↓
API Gateway / Backend API
    ↓
Domain Modules
    ↓
Database / Cache / Object Storage / Queues
    ↓
AI Providers / Payment Providers / Notification Providers
```

---

# Platform Context

```text
+------------------------------------------------------+
|                    Acadedx Platform                  |
+------------------------------------------------------+
| Identity | Organizations | Institutions | Learning   |
| AI       | Analytics     | Commerce     | Admin      |
+------------------------------------------------------+
| PostgreSQL | Redis | Object Storage | Job Queue    |
+------------------------------------------------------+
| OpenAI / Claude / Gemini | OCR | Payment Gateways  |
+------------------------------------------------------+
```

---

# Domain Architecture

Acadedx should be organized around business domains.

## Identity Domain

Responsible for:

- Authentication
- Users
- Roles
- Permissions
- Sessions
- Password reset
- Email verification
- Access control

---

## Organization Domain

Responsible for:

- Organization profile
- Organization ownership
- Organization administrators
- Organization billing relationship
- Organization-level future analytics
- Multi-institution future support

MVP Scope:

- Organization must exist in backend and database.
- Advanced organization UI may be deferred.

---

## Institution Domain

Responsible for:

- Institution profile
- Institution type
- Academic sessions
- Grades
- Sections
- Subjects
- Teachers
- Students
- Parents
- Institution analytics

MVP Scope:

- Institution UI may be school-focused.
- Backend must support Institution as a flexible entity.

---

## Learning Domain

Responsible for:

- Homework
- Notes
- Quizzes
- Worksheets
- Study planner
- Learning progress
- Student learning history

---

## AI Domain

Responsible for:

- AI Tutor
- Homework solving
- Prompt templates
- LLM routing
- OCR integration
- AI safety
- AI usage tracking
- AI cost tracking

---

## Analytics Domain

Responsible for:

- Learning Mastery Score
- Weak topic detection
- Study streaks
- Product analytics
- Institution analytics
- Organization analytics in future
- AI quality metrics

---

## Commerce Domain

Responsible for:

- Plans
- Entitlements
- Subscriptions
- Payments
- Invoices
- Coupons
- Usage limits

---

## Notification Domain

Responsible for:

- In-app notifications
- Email notifications
- Push notifications
- Study reminders
- Parent reports
- Security alerts
- Subscription alerts

---

## Administration Domain

Responsible for:

- Platform admin console
- User management
- Organization management
- Institution management
- AI monitoring
- Payment monitoring
- Feature flags
- Audit logs
- Support operations

---

# Client Applications

Acadedx may include the following applications.

## Web Application

Primary application for:

- Students
- Parents
- Teachers
- Institution administrators

Technology:

- React
- TypeScript
- Vite
- Tailwind CSS
- shadcn/ui

---

## Admin Application

Internal DGIS platform console.

Used by:

- Platform administrators
- Support agents
- Finance administrators
- AI operations administrators

---

## Landing Website

Public marketing and conversion site.

Includes:

- Landing page
- Pricing
- About
- Contact
- Blog
- SEO pages

---

## Mobile Applications

Mobile support may be implemented through:

- React + Capacitor

or future native mobile architecture.

MVP decision should optimize for code reuse and shipping speed.

---

## Desktop Application

Desktop support may be implemented later using:

- Electron

Desktop is not required for MVP unless explicitly prioritized.

---

# Backend Architecture

Backend should be implemented using:

- Node.js
- NestJS
- TypeScript
- Prisma
- PostgreSQL
- Redis
- BullMQ or equivalent queue system

---

# Backend Module Pattern

Each backend module should follow this structure:

```text
module/
    controllers/
    services/
    repositories/
    dto/
    validators/
    guards/
    events/
    jobs/
    tests/
```

Controllers must remain thin.

Business logic belongs in services.

Database access belongs in repositories or Prisma service abstractions.

---

# Request Flow

```text
Client
    ↓
Controller
    ↓
Guard / Validation
    ↓
Service
    ↓
Repository
    ↓
Database
```

For AI workflows:

```text
Client
    ↓
Controller
    ↓
Entitlement Check
    ↓
AI Service
    ↓
Prompt Builder
    ↓
LLM Router
    ↓
Provider Adapter
    ↓
AI Provider
    ↓
Response Validator
    ↓
Storage / Analytics
```

---

# Organization and Institution Context

Every relevant authenticated request must resolve:

- User ID
- Role
- Organization ID
- Institution ID where applicable
- Permissions
- Entitlements

This context must be available to authorization guards and services.

---

# Tenant Isolation

Tenant isolation is mandatory.

Rules:

- Organization data must be isolated from other organizations.
- Institution data must be isolated from other institutions.
- Institution administrators may access only their institution.
- Organization administrators may access only their organization.
- Teachers may access only assigned classes or students.
- Parents may access only linked children.
- Students may access only their own learning data.
- Platform administrator access must be audited.

---

# Database Architecture

Primary database:

```text
PostgreSQL
```

ORM:

```text
Prisma
```

Core database groups:

- Identity
- Organizations
- Institutions
- Academic structure
- Learning activity
- AI activity
- Analytics
- Commerce
- Notifications
- Administration
- Audit logs

---

# Data Model Hierarchy

```text
Organization
    ↓
Institution
    ↓
AcademicSession
    ↓
Grade
    ↓
Section
    ↓
Subject
    ↓
Chapter
    ↓
Topic
```

Users may be associated with:

- Organization
- Institution
- Role
- Student profile
- Parent profile
- Teacher profile
- Admin profile

---

# MVP Data Constraint

The MVP should enforce:

```text
One Organization → One Institution
```

This should be enforced through application-level business rules or configurable constraints.

The database schema must not permanently prevent:

```text
One Organization → Multiple Institutions
```

---

# Caching Architecture

Redis should be used for:

- Rate limiting
- Session-related cache
- Frequently accessed configuration
- Entitlement cache
- AI response cache where appropriate
- Background job coordination

Do not cache sensitive student data without clear expiration and security rules.

---

# Object Storage

Object storage should be used for:

- Homework images
- Uploaded PDFs
- Generated worksheets
- Exported notes
- Reports
- Profile avatars
- Institution logos

Storage provider should be abstracted.

Supported providers may include:

- AWS S3
- Azure Blob Storage
- S3-compatible storage

---

# Background Jobs

Background jobs should be used for:

- OCR processing
- Long-running AI generation
- Email delivery
- Notification delivery
- Report generation
- Payment webhook processing
- Analytics aggregation
- AI cost calculation

Every job must support:

- Retry
- Failure logging
- Dead-letter handling
- Observability

---

# AI Architecture

The AI layer must be provider-agnostic.

Supported providers may include:

- OpenAI
- Anthropic Claude
- Google Gemini

The application must not call providers directly from business modules.

Use this pattern:

```text
Feature Module
    ↓
AI Service
    ↓
Prompt Builder
    ↓
LLM Router
    ↓
Provider Adapter
    ↓
External AI Provider
```

---

# AI Safety Architecture

AI workflows must include:

- Prompt templates
- Prompt versioning
- Prompt injection protection
- Output validation
- Content moderation
- Grade-level adaptation
- Usage tracking
- Cost tracking
- User feedback

---

# OCR Architecture

OCR processing should follow this flow:

```text
Upload
    ↓
File Validation
    ↓
Virus / Safety Check
    ↓
Image Enhancement
    ↓
OCR Provider
    ↓
Confidence Score
    ↓
User Correction where required
    ↓
AI Processing
```

OCR should be treated as a separate capability from AI tutoring.

---

# Commerce Architecture

Commerce must be entitlement-driven.

Do not write feature logic like:

```text
if plan == "Student Pro"
```

Use entitlement checks:

```text
canUseFeature(userId, "homework.solve")
```

Commerce modules:

- Plans
- Plan features
- Entitlements
- Usage counters
- Subscriptions
- Payments
- Invoices
- Coupons

---

# Payment Architecture

Payment processing must use a gateway adapter pattern.

```text
Payment Service
    ↓
Gateway Adapter
    ↓
Razorpay / Stripe / PayPal / Cashfree / PayU
```

The business logic must not depend on a specific gateway.

---

# Notification Architecture

Notifications should be event-driven.

```text
Domain Event
    ↓
Notification Rule
    ↓
Template
    ↓
Channel Selection
    ↓
Delivery Provider
    ↓
Delivery Tracking
```

Supported channels:

- In-app
- Email
- Push
- SMS future
- WhatsApp future

---

# Event Architecture

Acadedx should use domain events for important business actions.

Examples:

- UserRegistered
- HomeworkSolved
- QuizCompleted
- WorksheetGenerated
- StudyPlanCreated
- SubscriptionActivated
- PaymentFailed
- InstitutionCreated
- OrganizationCreated

MVP may use in-process events.

Future versions may use a message broker.

---

# API Architecture

Acadedx is REST-first.

API base:

```text
/api/v1
```

API requirements:

- Versioned endpoints
- Standard response format
- Authentication
- Authorization
- Validation
- Pagination
- Rate limiting
- OpenAPI documentation
- Request ID logging

---

# Authentication Architecture

Authentication should support:

- Email and password
- JWT access tokens
- Refresh tokens
- Password reset
- Email verification
- Session tracking

Future support:

- Social login
- MFA
- Passkeys
- Enterprise SSO

---

# Authorization Architecture

Authorization should include:

- Role-based access control
- Permission-based access control
- Organization scope
- Institution scope
- Ownership checks
- Entitlement checks

Authorization must be enforced server-side.

Frontend checks are only for user experience and must not be trusted.

---

# Observability Architecture

The platform must support:

- Structured logs
- Request IDs
- Error tracking
- Metrics
- Tracing
- Job monitoring
- AI usage monitoring
- Payment monitoring
- Security event monitoring

---

# Security Architecture

Security must include:

- HTTPS
- Secure password hashing
- JWT validation
- Refresh token rotation
- Rate limiting
- Input validation
- Secure file upload
- Virus scanning where practical
- Prompt injection protection
- Payment webhook signature validation
- Audit logging
- Least privilege access

---

# Audit Architecture

Audit logs must capture sensitive actions.

Examples:

- Admin user changes
- Role changes
- Permission changes
- Subscription changes
- Payment refunds
- Institution changes
- Organization changes
- AI configuration changes
- Feature flag changes
- Impersonation actions

Audit logs should be tamper-resistant.

---

# Deployment Architecture

Recommended MVP deployment:

```text
Web App
    ↓
Backend API
    ↓
PostgreSQL
    ↓
Redis
    ↓
Object Storage
```

Deployment environments:

- Local
- Development
- Staging
- Production

---

# Scalability Strategy

MVP scaling approach:

- Modular monolith
- Horizontal API scaling
- PostgreSQL indexes
- Redis caching
- Background jobs
- Object storage
- CDN for static assets

Future scaling approach:

- Extract AI service
- Extract notification service
- Extract payment service
- Extract analytics service
- Introduce event bus
- Introduce search service
- Multi-region deployment

---

# Future Microservice Candidates

The following modules may become independent services later:

- AI
- OCR
- Notifications
- Payments
- Analytics
- Search
- Reports
- File processing

Do not split them prematurely.

---

# Technology Stack Summary

| Layer | Technology |
|---|---|
| Frontend | React + TypeScript |
| Build Tool | Vite |
| UI | Tailwind CSS + shadcn/ui |
| Backend | NestJS |
| Runtime | Node.js |
| ORM | Prisma |
| Database | PostgreSQL |
| Cache | Redis |
| Queue | BullMQ or equivalent |
| Storage | S3-compatible object storage |
| AI | OpenAI / Claude / Gemini |
| API | REST |
| Documentation | OpenAPI |
| CI/CD | GitHub Actions |
| Containerization | Docker |

---

# Architecture Constraints

## MVP Constraint

Build only what is required to validate the core learning platform.

---

## Domain Constraint

Use Organization and Institution as first-class concepts.

---

## Cost Constraint

Track AI usage and cost from day one.

---

## Security Constraint

Never bypass tenant isolation.

---

## Product Constraint

Do not expose enterprise complexity before users need it.

---

# Architecture Risks

| Risk | Mitigation |
|---|---|
| Overbuilding enterprise features | Keep MVP UI simple |
| Hardcoding school-only logic | Use Institution domain model |
| High AI cost | Track tokens and enforce entitlements |
| Data leakage across tenants | Enforce organization and institution scoping |
| AI hallucination | Use moderation, validation, feedback |
| Payment provider lock-in | Use gateway adapter |
| Scaling too early | Start modular monolith |
| Poor maintainability | Follow module boundaries |

---

# Architecture Decision Rules

Architecture decisions must optimize for:

1. MVP delivery speed
2. Long-term extensibility
3. Security and privacy
4. AI cost control
5. Maintainability
6. Tenant isolation
7. Clear domain boundaries

Do not choose complex infrastructure only because it may be needed later.

Do not choose shortcuts that block future organization and institution expansion.

---

# Related Documents

- TECH_STACK.md
- PROJECT_STRUCTURE.md
- CODING_STANDARDS.md
- DATABASE_SCHEMA.md
- API_GUIDELINES.md
- SECURITY.md
- DEPLOYMENT.md
- PRD.md
- PRODUCT_REQUIREMENTS.md