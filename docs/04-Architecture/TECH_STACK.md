# Acadedx Technology Stack

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the approved technology stack for Acadedx.

The stack must support:

- AI-powered learning workflows
- Students, parents, teachers, institutions, and organizations
- Web-first MVP delivery
- Future mobile and desktop applications
- Secure multi-tenant architecture
- Organization and institution scoping
- Subscription and entitlement-based monetization
- AI usage tracking and cost control
- Long-term maintainability

Technology choices should not be changed without an approved architecture decision.

---

# Technology Philosophy

Acadedx should use a pragmatic stack that balances:

- Speed of MVP delivery
- Developer productivity
- Long-term scalability
- AI integration capability
- Security
- Maintainability
- Cost control

The platform should avoid unnecessary infrastructure complexity during MVP.

Do not start with microservices, Kubernetes, or distributed architecture unless the product has usage that justifies it.

---

# Architecture Approach

Recommended MVP architecture:

```text
Modular Monolith
    +
API-first backend
    +
Web-first frontend
    +
Provider-agnostic AI layer
    +
PostgreSQL relational data model
```

Future extraction into services should be possible, but not required for MVP.

---

# High-Level Stack Summary

| Layer | Approved Technology |
|---|---|
| Frontend Web | React + TypeScript |
| Build Tool | Vite |
| Styling | Tailwind CSS |
| UI Components | shadcn/ui |
| Icons | Lucide Icons |
| State Management | Redux Toolkit |
| Server State | TanStack Query |
| Forms | React Hook Form |
| Validation | Zod |
| Backend | NestJS |
| Runtime | Node.js LTS |
| Backend Language | TypeScript |
| ORM | Prisma |
| Database | PostgreSQL |
| Cache | Redis |
| Queue | BullMQ |
| Object Storage | S3-compatible storage |
| API Style | REST |
| API Documentation | OpenAPI / Swagger |
| Authentication | JWT + Refresh Tokens |
| Authorization | RBAC + Permissions + Tenant Scope |
| AI Providers | OpenAI, Anthropic Claude, Google Gemini |
| OCR Providers | Provider-agnostic OCR adapter |
| Payments | Gateway adapter pattern |
| Notifications | Email, In-app, Push future |
| Logging | Pino |
| Monitoring | OpenTelemetry-compatible approach |
| Error Tracking | Sentry or equivalent |
| Testing | Vitest, Supertest, Playwright |
| Package Manager | pnpm |
| Monorepo Orchestration | Turborepo |
| CI/CD | GitHub Actions |
| Containerization | Docker |
| Reverse Proxy | Nginx |
| Deployment | Cloud VPS / AWS / Azure / GCP compatible |

---

# Frontend Stack

## Framework

Approved:

```text
React
```

Language:

```text
TypeScript
```

Build tool:

```text
Vite
```

Reasoning:

- Fast MVP development
- Large ecosystem
- Strong TypeScript support
- Good AI coding assistant compatibility
- Reusable component model
- Works well with Capacitor and Electron if needed later

---

# Frontend Styling

Approved:

```text
Tailwind CSS
```

Reasoning:

- Fast UI development
- Strong responsive design support
- Good design token compatibility
- Reduces custom CSS drift
- Works well with shadcn/ui

---

# UI Component Library

Approved:

```text
shadcn/ui
```

Reasoning:

- Modern component patterns
- Accessible foundation
- Tailwind compatible
- Customizable
- Source-owned components rather than black-box dependency

---

# Icon Library

Approved:

```text
Lucide Icons
```

Reasoning:

- Clean visual style
- Consistent icon set
- Strong React support
- Lightweight

---

# Frontend State Management

## Client State

Approved:

```text
Redux Toolkit
```

Use for:

- Auth state
- User session context
- UI preferences
- Global layout state
- Feature flags
- Entitlement state

---

## Server State

Approved:

```text
TanStack Query
```

Use for:

- API data fetching
- Caching
- Background refresh
- Mutations
- Retry handling
- Optimistic updates where safe

---

# Frontend Forms and Validation

Forms:

```text
React Hook Form
```

Validation:

```text
Zod
```

Use for:

- Login
- Registration
- Profile forms
- Homework input
- Study planner forms
- Subscription forms
- Admin forms

Frontend validation improves UX only.

Backend validation remains mandatory.

---

# Mobile Strategy

MVP priority:

```text
Responsive Web
```

Recommended mobile wrapper when required:

```text
Capacitor
```

Reasoning:

- Reuses React web application
- Faster delivery than separate mobile codebase
- Access to camera and native APIs
- Suitable for education MVP

Alternative future option:

```text
React Native + Expo
```

Use React Native only if mobile usage becomes dominant and native UX limitations appear.

Flutter is not selected for the initial Acadedx architecture because it would require maintaining a separate Dart-based UI stack while Acadedx already requires a strong web application, admin portal, landing site, and dashboards.

---

# Desktop Strategy

Desktop is not MVP priority.

Future approved option:

```text
Electron
```

Use when:

- Institutions require desktop app
- Offline workflows become important
- Windows school lab deployment becomes a business requirement

---

# Backend Stack

Framework:

```text
NestJS
```

Runtime:

```text
Node.js LTS
```

Language:

```text
TypeScript
```

Reasoning:

- Modular backend architecture
- Dependency injection
- Strong TypeScript support
- Good testing ecosystem
- Suitable for enterprise SaaS
- Aligns well with domain modules

---

# Backend Architecture Pattern

Approved:

```text
Modular Monolith
```

Pattern:

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

Rules:

- Controllers must remain thin.
- Services own business logic.
- Repositories own persistence logic.
- Guards own authentication and authorization checks.
- DTOs own request and response contracts.
- Validators own input validation.

---

# Database Stack

Primary database:

```text
PostgreSQL
```

ORM:

```text
Prisma
```

Reasoning:

- Strong relational data model
- ACID transactions
- Good fit for users, organizations, institutions, roles, subscriptions, learning data, and analytics
- Mature indexing and query capabilities
- Works well with Prisma migrations

---

# Database Design Requirements

The database must support:

- Organizations
- Institutions
- Institution types
- Academic sessions
- Grades
- Sections
- Subjects
- Users
- Roles
- Permissions
- Learning activity
- AI usage
- Subscriptions
- Payments
- Notifications
- Audit logs

The schema must not assume that every institution is always a school.

---

# Cache Stack

Approved:

```text
Redis
```

Use for:

- Rate limiting
- Entitlement cache
- Feature flag cache
- Session-related cache
- Frequently accessed configuration
- AI response cache where appropriate
- Queue coordination

Do not cache sensitive student data unless expiration, encryption, and access controls are clear.

---

# Background Job Stack

Approved:

```text
BullMQ
```

Use for:

- OCR processing
- AI generation jobs
- Email delivery
- Notification delivery
- Report generation
- Payment webhook handling
- Analytics aggregation
- AI cost calculation

Every job must support:

- Retry
- Failure logging
- Dead-letter handling
- Monitoring

---

# Object Storage

Approved:

```text
S3-compatible object storage
```

Potential providers:

- AWS S3
- Azure Blob Storage
- Cloudflare R2
- MinIO
- Other S3-compatible storage

Use for:

- Homework images
- Uploaded PDFs
- Generated worksheets
- Exported notes
- Reports
- Profile images
- Institution logos

Storage provider access must be abstracted.

---

# API Stack

Primary API style:

```text
REST
```

Base path:

```text
/api/v1
```

Documentation:

```text
OpenAPI / Swagger
```

API requirements:

- Versioning
- Standard response format
- Authentication
- Authorization
- Validation
- Pagination
- Rate limiting
- Request ID logging
- Error code standardization

GraphQL is not required for MVP.

---

# Authentication Stack

Approved:

```text
JWT Access Tokens
Refresh Tokens
Secure Password Hashing
```

Password hashing:

```text
Argon2
```

Authentication features:

- Registration
- Login
- Logout
- Refresh token rotation
- Password reset
- Email verification
- Session tracking

Future:

- Social login
- MFA
- Passkeys
- Enterprise SSO

---

# Authorization Stack

Authorization must combine:

- Role-Based Access Control
- Permission-Based Access Control
- Organization scoping
- Institution scoping
- Ownership checks
- Entitlement checks

Roles include:

- Platform Administrator
- Organization Owner
- Organization Administrator
- Institution Administrator
- Teacher
- Parent
- Student
- Support Agent

Frontend authorization is only a user experience layer.

Backend authorization is mandatory.

---

# AI Stack

Supported AI providers:

- OpenAI
- Anthropic Claude
- Google Gemini

AI integration pattern:

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
AI Provider
```

No feature module should call an AI provider directly.

---

# AI Requirements

Every AI request must track:

- User ID
- Organization ID where applicable
- Institution ID where applicable
- Feature
- Provider
- Model
- Prompt version
- Token usage
- Estimated cost
- Latency
- Success or failure
- User feedback where available

---

# OCR Stack

OCR must use a provider-agnostic adapter.

Potential providers may include:

- Azure AI Vision
- Google Vision
- Mistral OCR
- Other OCR APIs

The application must not bind business logic directly to one OCR provider.

---

# Payment Stack

Payment processing must use an adapter pattern.

Supported or future providers:

- Razorpay
- Stripe
- Cashfree
- PayU
- PayPal

Gateway selection may depend on:

- Country
- Currency
- Plan type
- Business account
- Availability
- Cost

The application must call internal Payment Service APIs only.

---

# Notification Stack

MVP channels:

- In-app notifications
- Email notifications

Future channels:

- Push notifications
- SMS
- WhatsApp

Email provider should be abstracted.

Potential providers:

- AWS SES
- SendGrid
- Resend
- SMTP provider

---

# Search Stack

MVP:

```text
PostgreSQL full-text search
```

Future:

```text
OpenSearch / Elasticsearch
```

Use search for:

- Notes
- Homework history
- Quiz history
- Worksheets
- Admin user search
- Institution search

Do not introduce OpenSearch in MVP unless PostgreSQL search becomes insufficient.

---

# Analytics Stack

MVP analytics should use:

- PostgreSQL event tables
- Application-level analytics events
- Aggregated reporting tables where needed

Future options:

- ClickHouse
- BigQuery
- Snowflake
- OpenSearch
- Data warehouse

Do not introduce heavy analytics infrastructure before validated usage.

---

# Logging Stack

Approved:

```text
Pino
```

Logs must include:

- Request ID
- User ID where available
- Organization ID where available
- Institution ID where available
- Endpoint
- Status code
- Duration
- Error code where applicable

Never log:

- Passwords
- Tokens
- Payment card data
- Secrets
- Full sensitive AI conversations unless explicitly approved and protected

---

# Observability Stack

Use OpenTelemetry-compatible patterns.

Monitor:

- API latency
- Error rate
- Queue jobs
- AI provider latency
- AI provider failures
- OCR failures
- Payment failures
- Notification delivery
- Database performance
- Redis health

---

# Error Tracking

Approved:

```text
Sentry or equivalent
```

Use for:

- Frontend runtime errors
- Backend exceptions
- API errors
- Job failures
- Critical workflow failures

---

# Testing Stack

## Unit Testing

Approved:

```text
Vitest
```

Use for:

- Business logic
- Services
- Utility functions
- Entitlement logic
- Permission logic
- Prompt builders

---

## Backend API Testing

Approved:

```text
Supertest
```

Use for:

- API endpoint tests
- Authentication flows
- Authorization flows
- Validation tests

---

## Frontend Testing

Approved:

```text
React Testing Library
```

Use for:

- Component behavior
- Form behavior
- UI states
- Accessibility checks where practical

---

## End-to-End Testing

Approved:

```text
Playwright
```

Use for:

- Registration
- Login
- Homework solve flow
- AI Tutor flow
- Quiz flow
- Subscription upgrade
- Payment confirmation
- Admin user management

---

# Monorepo Tooling

Package manager:

```text
pnpm
```

Monorepo orchestration:

```text
Turborepo
```

Repository should support:

- Shared packages
- Shared UI
- Shared types
- Shared config
- Shared prompts
- Shared SDK utilities

---

# Code Quality Tooling

Approved:

- ESLint
- Prettier
- TypeScript strict mode
- Conventional Commits
- lint-staged optional
- Husky optional

Rules:

- No `any` unless justified.
- No hardcoded secrets.
- No direct provider calls from feature modules.
- No business logic in controllers.
- No unauthorized tenant data access.

---

# CI/CD Stack

Approved:

```text
GitHub Actions
```

CI should run:

- Install dependencies
- Type check
- Lint
- Unit tests
- Integration tests where practical
- Build
- Security checks where available

Deployment workflows should be added after application scaffolding.

---

# Containerization

Approved:

```text
Docker
```

Use for:

- Backend API
- PostgreSQL local development
- Redis local development
- Job worker
- Nginx reverse proxy where needed

Docker Compose may be used for local development.

---

# Reverse Proxy

Approved:

```text
Nginx
```

Use for:

- API reverse proxy
- HTTPS termination where applicable
- Static asset routing where needed
- Production routing

---

# Deployment Strategy

MVP deployment may use:

- VPS
- AWS
- Azure
- GCP
- Managed PostgreSQL
- Managed Redis where practical

Architecture should remain cloud-provider flexible.

Avoid cloud-specific lock-in unless there is a strong business reason.

---

# Environment Strategy

Supported environments:

- Local
- Development
- Staging
- Production

Every environment must have separate:

- Database
- Redis
- Object storage bucket/container
- AI provider credentials
- Payment credentials
- Email credentials
- Environment variables

---

# Secrets Management

MVP:

```text
Environment variables
```

Future:

- AWS Secrets Manager
- Azure Key Vault
- Google Secret Manager
- HashiCorp Vault

Secrets must never be committed to Git.

---

# Configuration Management

Configuration should support:

- Environment variables
- Central configuration module
- Feature flags
- Entitlement configuration
- AI provider routing configuration
- Payment provider configuration

---

# Feature Flags

Feature flags must be supported for:

- New modules
- Beta features
- AI provider experiments
- Regional rollouts
- Institution pilots
- Plan-specific capabilities

Feature flags must not replace authorization or entitlements.

---

# Internationalization

MVP language:

```text
English
```

Next priority:

```text
Hindi
```

Architecture must support future:

- Multiple languages
- Regional curricula
- Regional formatting
- Currency localization
- Date and time localization

---

# Accessibility Technology Requirements

Frontend must support:

- Semantic HTML
- Accessible shadcn/ui patterns
- Keyboard navigation
- Form labels
- Focus states
- Screen reader labels
- Sufficient contrast

Accessibility checks should be part of UI review.

---

# Security Tooling

Recommended:

- Dependency vulnerability scanning
- Secret scanning
- Static analysis where available
- Rate limiting
- Input validation
- File validation
- Webhook signature validation
- Audit logs

---

# Technology Decisions Not Approved for MVP

The following should not be introduced in MVP unless explicitly approved:

- Kubernetes
- Microservices
- Kafka
- GraphQL
- OpenSearch
- Data warehouse
- Native mobile app rewrite
- Flutter
- Blockchain
- Custom AI model training
- Full offline-first sync
- Multi-region deployment

These may be useful later, but they add unnecessary complexity during MVP.

---

# Future Technology Candidates

Future additions may include:

- React Native + Expo
- Electron
- OpenSearch
- ClickHouse
- Kafka or equivalent event bus
- Kubernetes
- Vector database
- Data warehouse
- Enterprise SSO
- Passkeys
- AI evaluation framework
- Custom model fine-tuning

Any future technology must have a clear business or scaling justification.

---

# Technology Governance

A technology change requires:

- Clear problem statement
- Alternatives considered
- Architecture impact
- Product impact
- Cost impact
- Security impact
- Migration plan
- Approved Architecture Decision Record

---

# Architecture Decision Records

Major technology decisions must be documented under:

```text
docs/04-Architecture/ADR/
```

Examples:

- ADR-001 Technology Stack
- ADR-002 Frontend Architecture
- ADR-003 Backend Architecture
- ADR-004 Database Design
- ADR-005 AI Provider Strategy
- ADR-006 Organization and Institution Model
- ADR-007 Payment Adapter Strategy

---

# Related Documents

- SYSTEM_ARCHITECTURE.md
- PROJECT_STRUCTURE.md
- CODING_STANDARDS.md
- API_GUIDELINES.md
- DATABASE_SCHEMA.md
- SECURITY.md
- DEPLOYMENT.md