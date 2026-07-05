# Acadedx Architecture Modules

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the major architecture modules of Acadedx.

It explains how the platform is divided into product and technical domains so engineering teams and AI coding agents can implement features with clear boundaries.

This document should be read together with:

- `SYSTEM_ARCHITECTURE.md`
- `PROJECT_STRUCTURE.md`
- `TECH_STACK.md`
- `CODING_STANDARDS.md`
- `DATABASE_SCHEMA.md`
- `API_GUIDELINES.md`

---

# Architecture Module Philosophy

Acadedx should be organized around business domains, not just technical folders.

The platform must support:

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

The MVP is school-focused, but the architecture must not hardcode school-only assumptions.

Use `Institution` as the flexible education unit.

Use `Organization` as the business owner or parent account.

---

# Recommended Architecture Style

Acadedx starts as a **modular monolith**.

The constraint here is delivery speed and maintainability.

A modular monolith gives the project:

- Faster MVP development
- Simpler deployment
- Easier local development
- Clear module boundaries
- Shared transaction handling
- Lower operational complexity
- Future microservice extraction path

Do not start with distributed microservices unless there is validated scale and operational need.

---

# Module Boundary Rules

Every module must have clear ownership.

A module should own:

- Its business rules
- Its APIs
- Its database entities
- Its validation rules
- Its permissions
- Its events
- Its tests
- Its documentation

A module should not directly modify another module's private data.

Cross-module access must happen through:

- Public service methods
- Events
- Shared interfaces
- Explicit application services

---

# High-Level Module Map

```text
Acadedx Platform

├── Identity
├── Organizations
├── Institutions
├── Academic
├── Users
├── Learning
├── AI
├── OCR
├── Analytics
├── Notifications
├── Commerce
├── Payments
├── Administration
├── Audit
├── Storage
├── Search
├── Reports
├── Support
└── Platform Infrastructure
```

---

# Module Groups

## Core Platform Modules

- Identity
- Organizations
- Institutions
- Academic
- Users

---

## Learning Modules

- Homework
- AI Tutor
- Notes
- Quiz
- Worksheets
- Study Planner

---

## Intelligence Modules

- AI
- OCR
- Analytics
- Personalization
- Recommendations

---

## Business Modules

- Commerce
- Subscriptions
- Entitlements
- Payments
- Invoices

---

## Operations Modules

- Admin
- Audit
- Notifications
- Support
- Reports
- Feature Flags

---

## Infrastructure Modules

- Storage
- Cache
- Queue
- Logger
- Health
- Config

---

# 1. Identity Module

## Purpose

The Identity module manages authentication, sessions, tokens, roles, permissions, and access control.

## Responsibilities

- Registration
- Login
- Logout
- Refresh tokens
- Password reset
- Email verification
- Session management
- Role-based access control
- Permission-based access control
- Authentication guards
- Authorization guards

## Owns

- Users authentication credentials
- Sessions
- Refresh tokens
- Password reset tokens
- Email verification tokens
- Roles
- Permissions
- Role permissions

## Depends On

- Users Module
- Notifications Module
- Audit Module

## Used By

All protected modules.

## MVP Requirement

Required.

---

# 2. Organizations Module

## Purpose

The Organizations module manages business ownership and parent account structure.

An Organization represents the business owner, trust, company, education group, franchise, or enterprise customer.

## Responsibilities

- Organization creation
- Organization profile
- Organization owner
- Organization administrators
- Organization billing relationship
- Organization settings
- Future multi-institution management
- Future organization analytics

## Owns

- Organizations
- Organization users
- Organization roles
- Organization settings
- Organization billing references

## Depends On

- Identity Module
- Users Module
- Commerce Module
- Audit Module

## Used By

- Institutions Module
- Payments Module
- Subscriptions Module
- Admin Module
- Analytics Module

## MVP Requirement

Required in backend and database.

Advanced organization UI may be deferred.

## MVP Constraint

```text
One Organization → One Institution
```

This must be enforced through business rules, not hardcoded schema limitations.

---

# 3. Institutions Module

## Purpose

The Institutions module manages the education unit where learning happens.

A School is an Institution type.

Future Institution types include:

- School
- Academy
- Coaching Institute
- College
- University
- Training Centre
- Learning Centre
- Online Academy

## Responsibilities

- Institution creation
- Institution profile
- Institution type
- Institution administrators
- Institution settings
- Institution users
- Institution-level permissions
- Institution-level analytics
- Institution reports

## Owns

- Institutions
- Institution settings
- Institution users
- Institution administrator relationships

## Depends On

- Organizations Module
- Identity Module
- Users Module
- Academic Module
- Audit Module

## Used By

- Teachers Module
- Students Module
- Parents Module
- Learning Modules
- Analytics Module
- Commerce Module
- Admin Module

## MVP Requirement

Required.

The UI may use school-friendly labels, but backend and database should use Institution terminology.

---

# 4. Academic Module

## Purpose

The Academic module manages the academic structure inside an institution.

## Responsibilities

- Academic sessions
- Grades
- Sections
- Subjects
- Chapters
- Topics
- Curriculum mapping
- Academic hierarchy

## Owns

- Academic sessions
- Grades
- Sections
- Subjects
- Chapters
- Topics

## Depends On

- Institutions Module

## Used By

- Homework Module
- Quiz Module
- Notes Module
- Worksheets Module
- Study Planner Module
- Analytics Module
- Teacher Workspace

## MVP Requirement

Required at basic level.

---

# 5. Users Module

## Purpose

The Users module manages user profiles, preferences, relationships, and learning identity.

## Responsibilities

- User profile
- Student profile
- Parent profile
- Teacher profile
- Admin profile
- Learning profile
- User preferences
- Parent-student relationships
- Teacher-student relationships
- Institution user associations
- Organization user associations

## Owns

- User profiles
- Learning profiles
- User preferences
- Parent-student links
- Teacher-student links
- User institution mappings
- User organization mappings

## Depends On

- Identity Module
- Organizations Module
- Institutions Module

## Used By

All user-facing modules.

## MVP Requirement

Required.

---

# 6. Homework Module

## Purpose

The Homework module manages homework input, processing, solving, history, and follow-up learning actions.

## Responsibilities

- Typed question input
- Image upload
- Camera capture
- PDF upload in later releases
- Homework status tracking
- Homework history
- AI solution generation
- Follow-up quiz generation
- Follow-up notes generation
- Follow-up worksheet generation
- Homework feedback

## Owns

- Homework
- Homework attachments
- Homework solutions
- Homework feedback
- Homework processing status

## Depends On

- Users Module
- Academic Module
- OCR Module
- AI Module
- Storage Module
- Analytics Module
- Entitlements Module

## MVP Requirement

Required.

---

# 7. OCR Module

## Purpose

The OCR module extracts text, mathematical expressions, and structured content from images and PDFs.

## Responsibilities

- Image processing
- PDF processing
- Text extraction
- Handwriting extraction
- Math expression extraction
- Confidence scoring
- OCR result review
- OCR correction
- OCR job tracking

## Owns

- OCR jobs
- OCR results
- OCR confidence scores
- OCR provider metadata

## Depends On

- Storage Module
- AI Module where applicable
- Analytics Module
- Entitlements Module

## Used By

- Homework Module
- Notes Module
- Quiz Module
- Worksheets Module

## MVP Requirement

Required for image extraction.

Advanced handwriting and multi-page OCR may be V1 or V2.

---

# 8. AI Module

## Purpose

The AI module is the central intelligence layer for Acadedx.

It provides provider-agnostic AI capabilities to learning modules.

## Responsibilities

- AI Tutor
- Homework solution generation
- Notes generation
- Quiz generation
- Worksheet generation
- Study plan generation
- Prompt building
- Prompt versioning
- LLM routing
- Provider adapters
- AI safety checks
- Prompt injection protection
- Output validation
- AI usage tracking
- AI cost tracking

## Owns

- AI requests
- AI responses metadata
- Prompt templates
- Prompt versions
- AI provider configuration
- AI usage records
- AI feedback

## Depends On

- Users Module
- Academic Module
- Analytics Module
- Entitlements Module

## Used By

- Homework Module
- AI Tutor Module
- Notes Module
- Quiz Module
- Worksheets Module
- Study Planner Module
- Admin Module

## MVP Requirement

Required.

Provider abstraction is mandatory from day one.

---

# 9. AI Tutor Module

## Purpose

The AI Tutor module manages conversational tutoring and contextual learning support.

## Responsibilities

- AI chat sessions
- Context-aware tutoring
- Homework follow-up
- Simplified explanations
- Examples
- Conversation history
- Learning context
- Student feedback

## Owns

- Conversations
- Messages
- Tutor sessions
- Conversation feedback

## Depends On

- AI Module
- Users Module
- Homework Module
- Academic Module
- Analytics Module
- Entitlements Module

## MVP Requirement

Required.

---

# 10. Notes Module

## Purpose

The Notes module manages AI-generated and user-managed study notes.

## Responsibilities

- Generate notes
- Save notes
- Edit notes
- Search notes
- Generate notes from homework
- Generate notes from AI Tutor conversations
- Export notes in future
- Revision notes
- Formula sheets in future

## Owns

- Notes
- Note folders
- Note tags
- Note history

## Depends On

- Users Module
- Academic Module
- AI Module
- Storage Module
- Analytics Module
- Entitlements Module

## MVP Requirement

Required at basic level.

---

# 11. Quiz Module

## Purpose

The Quiz module manages quiz generation, attempts, scoring, feedback, and analytics.

## Responsibilities

- Generate quiz
- Attempt quiz
- Submit quiz
- Score calculation
- Result display
- AI feedback
- Retry wrong questions
- Adaptive difficulty in future
- Teacher-assigned quizzes in future

## Owns

- Quizzes
- Quiz questions
- Quiz attempts
- Quiz answers
- Quiz feedback

## Depends On

- Users Module
- Academic Module
- AI Module
- Analytics Module
- Entitlements Module

## MVP Requirement

Required.

---

# 12. Worksheets Module

## Purpose

The Worksheets module generates practice worksheets and answer keys.

## Responsibilities

- Generate worksheet
- Generate answer key
- Generate from weak topics
- Export worksheet PDF
- Worksheet history
- Teacher assignment in future

## Owns

- Worksheets
- Worksheet questions
- Worksheet answers
- Worksheet downloads

## Depends On

- Users Module
- Academic Module
- AI Module
- Storage Module
- Analytics Module
- Entitlements Module

## MVP Requirement

Required.

---

# 13. Study Planner Module

## Purpose

The Study Planner module creates personalized study plans and tracks learning tasks.

## Responsibilities

- Generate study plans
- Daily tasks
- Weekly tasks
- Study goals
- Exam preparation plans
- Revision schedule
- Task completion
- Adaptive rescheduling in future
- Notifications

## Owns

- Study plans
- Study tasks
- Study goals
- Study progress

## Depends On

- Users Module
- Academic Module
- AI Module
- Analytics Module
- Notifications Module

## MVP Requirement

Required.

---

# 14. Analytics Module

## Purpose

The Analytics module tracks learning progress, product usage, AI quality, institution usage, and business metrics.

## Responsibilities

- Learning Mastery Score
- Weak topic detection
- Study streaks
- Homework analytics
- Quiz analytics
- Worksheet analytics
- Study planner analytics
- AI usage analytics
- Parent insights
- Teacher analytics
- Institution analytics
- Organization analytics in future

## Owns

- Learning analytics
- Activity events
- Mastery records
- Recommendation records
- Aggregated metrics

## Depends On

- Users Module
- Academic Module
- Organizations Module
- Institutions Module
- AI Module

## Used By

- Dashboard Module
- Study Planner Module
- Notifications Module
- Admin Module
- Parent Workspace
- Teacher Workspace

## MVP Requirement

Required at foundation level.

---

# 15. Notifications Module

## Purpose

The Notifications module manages messages, reminders, alerts, and delivery tracking.

## Responsibilities

- In-app notifications
- Email notifications
- Study reminders
- Homework completion alerts
- Parent alerts
- Teacher alerts
- Security alerts
- Subscription alerts
- Notification preferences
- Notification templates
- Delivery tracking

## Owns

- Notifications
- Notification preferences
- Notification templates
- Notification deliveries

## Depends On

- Users Module
- Events
- Email Provider
- Push Provider in future

## MVP Requirement

Required for in-app and email notifications.

---

# 16. Dashboard Module

## Purpose

The Dashboard module provides role-specific home screens and action-oriented summaries.

## Responsibilities

- Student dashboard
- Parent dashboard
- Teacher dashboard
- Institution dashboard
- Platform admin dashboard
- Organization dashboard in future
- Widgets
- Quick actions
- Recommendations
- Recent activity

## Owns

- Dashboard layouts
- Dashboard widgets
- Dashboard preferences
- Quick action metadata

## Depends On

- Users Module
- Analytics Module
- Homework Module
- Quiz Module
- Worksheets Module
- Study Planner Module
- Notifications Module
- Entitlements Module

## MVP Requirement

Student dashboard is required.

Other dashboards may be basic or phased.

---

# 17. Commerce Module

## Purpose

The Commerce module manages plans, subscriptions, entitlements, usage limits, trials, coupons, and monetization rules.

## Responsibilities

- Plan management
- Feature entitlements
- Usage counters
- Free plan limits
- Paid plan access
- Trial logic
- Subscription status
- Grace periods
- Coupons in future
- Organization billing in future

## Owns

- Plans
- Plan features
- Entitlements
- Usage counters
- Subscriptions
- Trials
- Coupons

## Depends On

- Users Module
- Organizations Module
- Institutions Module
- Payments Module

## Used By

All premium or usage-limited modules.

## MVP Requirement

Required.

Entitlement engine is mandatory.

---

# 18. Payments Module

## Purpose

The Payments module manages payment processing, gateways, invoices, refunds, and billing events.

## Responsibilities

- Payment creation
- Payment confirmation
- Gateway adapter
- Payment webhooks
- Payment history
- Invoices
- Refunds in future
- Taxes in future
- Failed payment handling

## Owns

- Payments
- Transactions
- Payment methods
- Invoices
- Refunds
- Billing addresses
- Tax records

## Depends On

- Commerce Module
- Users Module
- Organizations Module
- Audit Module

## MVP Requirement

Payment foundation required.

Invoices may be V1 depending on scope.

---

# 19. Administration Module

## Purpose

The Administration module provides internal DGIS operational controls.

## Responsibilities

- Admin dashboard
- User management
- Organization management
- Institution management
- Subscription management
- Payment monitoring
- AI usage monitoring
- Feature flags
- Audit logs
- Support tools
- Security monitoring

## Owns

- Admin roles
- Admin permissions
- Admin settings
- Feature flags
- Support metadata where applicable

## Depends On

All major platform modules.

## MVP Requirement

Admin foundation required.

---

# 20. Audit Module

## Purpose

The Audit module records sensitive actions for security, compliance, and operations.

## Responsibilities

- Admin action logs
- User security logs
- Role change logs
- Payment action logs
- Subscription change logs
- Organization change logs
- Institution change logs
- AI prompt change logs
- Feature flag change logs

## Owns

- Audit logs
- Audit event metadata

## Depends On

- Identity Module
- Users Module

## Used By

All sensitive modules.

## MVP Requirement

Required for admin, payments, roles, subscriptions, organization, and institution changes.

---

# 21. Storage Module

## Purpose

The Storage module abstracts file storage.

## Responsibilities

- File uploads
- Homework image storage
- PDF storage
- Report storage
- Export storage
- Avatar storage
- Institution logo storage
- Signed URLs
- File deletion
- Storage quota enforcement

## Owns

- File metadata
- Storage provider configuration

## Depends On

- Object storage provider
- Users Module
- Entitlements Module

## MVP Requirement

Required.

---

# 22. Search Module

## Purpose

The Search module provides searchable access to user and learning content.

## Responsibilities

- Homework search
- Notes search
- Quiz search
- Worksheet search
- Admin user search
- Institution search
- Future global search

## Owns

- Search indexing metadata where needed

## Depends On

- PostgreSQL search in MVP
- OpenSearch in future if needed

## MVP Requirement

Basic search may be V1.

---

# 23. Reports Module

## Purpose

The Reports module generates exports and structured reports.

## Responsibilities

- Student reports
- Parent reports
- Teacher reports
- Institution reports
- Subscription reports
- Payment reports
- AI usage reports
- Export to PDF / CSV / Excel

## Owns

- Report jobs
- Generated report metadata

## Depends On

- Analytics Module
- Storage Module
- Background Jobs

## MVP Requirement

Basic reports may be deferred.

---

# 24. Support Module

## Purpose

The Support module helps DGIS support users and institutions.

## Responsibilities

- Support tickets
- User issue lookup
- Support notes
- Escalation
- Issue categories
- SLA tracking

## Owns

- Support tickets
- Support activity

## Depends On

- Users Module
- Admin Module
- Audit Module

## MVP Requirement

Can be deferred.

---

# 25. Platform Infrastructure Modules

## Config Module

Manages environment configuration and validation.

## Logger Module

Manages structured application logging.

## Health Module

Provides health checks.

## Cache Module

Provides Redis access.

## Queue Module

Provides background job queues.

## Feature Flag Module

Manages feature rollout configuration.

---

# Module Dependency Overview

```text
Identity
    ↓
Users
    ↓
Organizations
    ↓
Institutions
    ↓
Academic
    ↓
Learning Modules
    ↓
AI / OCR / Analytics
    ↓
Dashboard / Notifications
    ↓
Commerce / Payments
    ↓
Admin / Audit
```

This is a conceptual dependency map, not a strict import graph.

---

# Critical Dependency Rules

## Rule 1 — Identity Is Foundational

No protected module works without Identity.

---

## Rule 2 — Organization and Institution Context Must Exist Early

Even if MVP UI is school-focused, backend modules must support organization and institution context.

---

## Rule 3 — AI Is Shared Infrastructure

Homework, Notes, Quiz, Worksheets, Study Planner, and AI Tutor must all use the shared AI module.

No feature module should call AI providers directly.

---

## Rule 4 — Entitlements Are Cross-Cutting

Any feature with usage limits or premium access must call Commerce/Entitlements.

---

## Rule 5 — Analytics Should Receive Events

Modules should emit events rather than tightly coupling to analytics internals.

---

## Rule 6 — Notifications Should Be Event-Driven

Modules should emit domain events that the Notifications module can consume.

---

# Future Microservice Extraction Candidates

The following modules may be extracted later:

| Module | Reason |
|---|---|
| AI | High cost, provider routing, scaling needs |
| OCR | CPU/API-heavy processing |
| Notifications | Delivery retries and channel scaling |
| Payments | Security and provider isolation |
| Analytics | High-volume events |
| Reports | Background processing |
| Search | Specialized indexing |

Do not extract these prematurely.

---

# MVP Required Modules

MVP must include at least:

- Identity
- Users
- Organizations foundation
- Institutions foundation
- Academic foundation
- Homework
- OCR foundation
- AI foundation
- AI Tutor
- Notes foundation
- Quiz foundation
- Worksheets foundation
- Study Planner foundation
- Analytics foundation
- Notifications foundation
- Dashboard foundation
- Commerce and Entitlements foundation
- Payments foundation
- Admin foundation
- Audit foundation
- Storage foundation

---

# Deferred Modules or Features

The following may be deferred:

- Full Support module
- Advanced Reports
- Organization dashboard
- Multi-institution UI
- Enterprise SSO
- White labeling
- Marketplace
- Public APIs
- Full LMS workflows
- Full ERP workflows
- Advanced Search
- Offline-first sync

---

# Module Specification Mapping

Each architecture module should map to a file under:

```text
specifications/
```

Expected mappings:

| Architecture Module | Specification |
|---|---|
| Identity | authentication.md |
| Users | users.md |
| Organizations | organizations.md |
| Institutions | institutions.md |
| Academic | academic.md |
| Homework | homework.md |
| OCR | ocr.md |
| AI Tutor | ai-tutor.md |
| Notes | notes.md |
| Quiz | quiz.md |
| Worksheets | worksheets.md |
| Study Planner | study-planner.md |
| Analytics | analytics.md |
| Notifications | notifications.md |
| Dashboard | dashboard.md |
| Commerce | subscriptions.md, plans-and-features.md |
| Payments | payments.md |
| Administration | admin-portal.md |
| Teachers | teachers.md |
| Parents | parents.md |

---

# Task Mapping

Each module should have tasks under:

```text
tasks/<module-name>/
```

Example:

```text
tasks/authentication/AUTH-001.md
tasks/organizations/ORG-001.md
tasks/institutions/INST-001.md
tasks/homework/HW-001.md
```

---

# Definition of Module Ready

A module is ready for implementation when:

- Product specification exists.
- User stories exist.
- Acceptance criteria exist.
- Database entities are defined.
- API endpoints are defined.
- Permissions are defined.
- Analytics events are defined.
- Security considerations are defined.
- Implementation tasks are created.

---

# Definition of Module Complete

A module is complete when:

- Required tasks are implemented.
- APIs are working.
- UI is working where applicable.
- Database migrations are applied.
- Tests pass.
- Documentation is updated.
- Security review is complete.
- Product Owner approves.

---

# Related Documents

- SYSTEM_ARCHITECTURE.md
- TECH_STACK.md
- PROJECT_STRUCTURE.md
- CODING_STANDARDS.md
- DATABASE_SCHEMA.md
- API_GUIDELINES.md
- PRODUCT_REQUIREMENTS.md