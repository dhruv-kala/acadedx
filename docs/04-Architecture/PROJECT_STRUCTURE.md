# Acadedx Project Structure

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the official repository and source-code structure for Acadedx.

The project structure must support:

- Modular product development
- AI-assisted implementation
- Shared code reuse
- Clear domain ownership
- Future organization and institution expansion
- Web, admin, mobile, and desktop applications
- Backend modular monolith architecture
- Long-term migration to service extraction if needed

No major folder or module structure should be changed without architecture approval.

---

# Project Structure Philosophy

Acadedx should be organized as a **monorepo**.

The constraint here is maintainability.

A monorepo allows shared packages, shared types, shared UI components, shared API clients, shared prompts, common tooling, unified CI/CD, and simpler AI-agent navigation.

The repository should not be organized as disconnected projects unless the product reaches a scale where separate repositories become operationally necessary.

---

# Root Structure

```text
acadedx/
│
├── AGENTS.md
├── CLAUDE.md
├── README.md
├── CHANGELOG.md
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
├── LICENSE
│
├── .gitignore
├── .editorconfig
├── .gitattributes
├── .env.example
│
├── package.json
├── pnpm-workspace.yaml
├── turbo.json
│
├── apps/
├── services/
├── packages/
├── database/
├── infrastructure/
├── scripts/
├── tests/
├── tools/
├── docs/
├── specifications/
├── tasks/
└── .ai/
```

---

# Top-Level Folder Responsibilities

| Folder | Purpose |
|---|---|
| `apps/` | User-facing applications |
| `services/` | Backend API, workers, and service modules |
| `packages/` | Shared code, UI, types, utilities, SDKs, prompts |
| `database/` | Prisma schema, migrations, seeds, database assets |
| `infrastructure/` | Docker, deployment, Nginx, Terraform, monitoring |
| `scripts/` | Developer and automation scripts |
| `tests/` | Cross-application tests |
| `tools/` | Internal developer tools and generators |
| `docs/` | Product, business, architecture, database, API, security documentation |
| `specifications/` | Module-level product and engineering specifications |
| `tasks/` | Small implementation tasks for AI agents and developers |
| `.ai/` | AI operational context, status, rules, task log, review checklist |

---

# AI Instruction Files

## AGENTS.md

Primary instruction file for AI coding agents.

Used by:

- Claude
- Codex
- Gemini CLI
- Cursor
- Other AI coding assistants

It should explain:

- What to read before coding
- How tasks are executed
- Where specifications live
- Where task files live
- How to update status and logs
- Which coding standards apply

---

## CLAUDE.md

Compatibility file for Claude Code.

Recommended content:

```text
See AGENTS.md for repository instructions.
```

Do not duplicate full AI instructions in both `AGENTS.md` and `CLAUDE.md`.

---

# apps/

The `apps/` folder contains user-facing applications.

```text
apps/
├── web/
├── admin/
├── landing/
├── mobile/
└── desktop/
```

---

## apps/web

Primary Acadedx learning application.

Used by:

- Students
- Parents
- Teachers
- Institution Administrators

Technology:

- React
- TypeScript
- Vite
- Tailwind CSS
- shadcn/ui

Suggested structure:

```text
apps/web/
├── public/
├── src/
│   ├── app/
│   ├── assets/
│   ├── components/
│   ├── features/
│   ├── hooks/
│   ├── layouts/
│   ├── pages/
│   ├── routes/
│   ├── services/
│   ├── stores/
│   ├── types/
│   ├── utils/
│   └── main.tsx
│
├── index.html
├── vite.config.ts
├── tsconfig.json
└── package.json
```

---

## apps/admin

Internal DGIS administration portal.

Used by:

- Platform Administrators
- Support Agents
- Finance Administrators
- AI Operations Administrators
- Security Administrators

Contains:

- User management
- Organization management
- Institution management
- Subscription management
- Payment management
- AI monitoring
- Feature flags
- Audit logs
- Support operations

Suggested structure:

```text
apps/admin/
├── public/
├── src/
│   ├── app/
│   ├── components/
│   ├── features/
│   ├── layouts/
│   ├── pages/
│   ├── routes/
│   ├── services/
│   ├── stores/
│   └── main.tsx
│
├── vite.config.ts
├── tsconfig.json
└── package.json
```

---

## apps/landing

Public marketing website.

Used by:

- Guests
- Prospective students
- Parents
- Teachers
- Institutions
- Organizations

Contains:

- Landing page
- Pricing page
- About page
- Contact page
- Blog
- SEO pages
- Terms
- Privacy Policy

Suggested structure:

```text
apps/landing/
├── public/
├── src/
│   ├── components/
│   ├── pages/
│   ├── sections/
│   ├── content/
│   ├── styles/
│   └── main.tsx
│
├── vite.config.ts
├── tsconfig.json
└── package.json
```

---

## apps/mobile

Mobile application wrapper.

MVP recommendation:

- Keep mobile deferred until web MVP is usable.
- Prefer responsive web first.
- Use Capacitor when mobile app packaging is required.

Suggested future structure:

```text
apps/mobile/
├── android/
├── ios/
├── src/
├── capacitor.config.ts
└── package.json
```

---

## apps/desktop

Desktop application.

Future option:

- Electron

Desktop is not required for MVP.

Suggested future structure:

```text
apps/desktop/
├── electron/
├── src/
├── package.json
└── electron.config.ts
```

---

# services/

The `services/` folder contains backend applications and workers.

```text
services/
├── api/
├── worker/
└── gateway/
```

---

## services/api

Primary NestJS backend API.

Architecture:

```text
services/api/
├── src/
│   ├── main.ts
│   ├── app.module.ts
│   ├── config/
│   ├── common/
│   ├── modules/
│   └── infrastructure/
│
├── test/
├── tsconfig.json
└── package.json
```

---

## services/api/src/modules

Backend modules must be organized by domain.

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
├── storage/
└── health/
```

---

# Backend Module Structure

Every backend module should follow this structure:

```text
module-name/
├── controllers/
├── services/
├── repositories/
├── dto/
├── validators/
├── guards/
├── policies/
├── events/
├── jobs/
├── types/
├── tests/
└── module-name.module.ts
```

---

# Backend Domain Rules

## Identity

Contains:

- Authentication
- Sessions
- Password reset
- Email verification
- Role-based access
- Permission checks

---

## Organizations

Contains:

- Organization entity
- Organization owner
- Organization administrators
- Organization billing context
- Future multi-institution management

MVP note:

- Organization exists in backend and database.
- Advanced UI can be deferred.

---

## Institutions

Contains:

- Institution entity
- Institution type
- Institution profile
- Institution administrators
- Institution settings
- Institution-level isolation

MVP note:

- School is the first institution type.
- Backend should not hardcode `School`.

---

## Academic

Contains:

- Academic sessions
- Grades
- Sections
- Subjects
- Chapters
- Topics

---

## Learning Modules

Contains:

- Homework
- Notes
- Quiz
- Worksheets
- Study planner

These should be separate modules because they have different workflows and data models.

---

## AI

Contains:

- AI Tutor
- Prompt builder
- Prompt templates
- LLM routing
- Provider adapters
- AI usage logging
- AI cost tracking
- AI moderation

---

## Commerce

Contains:

- Plans
- Entitlements
- Subscriptions
- Usage counters
- Trials
- Coupons

---

## Payments

Contains:

- Payment gateway adapter
- Payment creation
- Payment confirmation
- Invoices
- Refunds
- Webhooks

---

# services/worker

Background job worker.

Used for:

- OCR jobs
- AI generation jobs
- Email sending
- Notification delivery
- Payment webhook processing
- Report generation
- Analytics aggregation

Suggested structure:

```text
services/worker/
├── src/
│   ├── main.ts
│   ├── queues/
│   ├── processors/
│   ├── jobs/
│   └── config/
│
├── tsconfig.json
└── package.json
```

---

# services/gateway

Optional future API gateway or BFF.

Not required for MVP unless frontend and backend complexity requires it.

---

# packages/

Shared code belongs in `packages/`.

```text
packages/
├── ui/
├── types/
├── config/
├── utils/
├── api-client/
├── auth/
├── validation/
├── prompts/
├── logger/
├── eslint-config/
└── tsconfig/
```

---

## packages/ui

Shared UI components.

Used by:

- Web app
- Admin app
- Landing app where appropriate

Contains:

```text
packages/ui/
├── src/
│   ├── components/
│   ├── forms/
│   ├── layout/
│   ├── feedback/
│   ├── data-display/
│   ├── ai/
│   └── index.ts
│
├── package.json
└── tsconfig.json
```

---

## packages/types

Shared TypeScript types.

Contains:

- User types
- Organization types
- Institution types
- Homework types
- AI types
- Quiz types
- Subscription types
- Payment types
- API response types

Rule:

Types shared across apps and services belong here.

---

## packages/api-client

Shared API client.

Used by:

- Web app
- Admin app
- Mobile app
- Desktop app

Responsibilities:

- HTTP client
- Auth token handling
- Request IDs
- Error parsing
- Typed API methods

---

## packages/prompts

Centralized AI prompt templates.

```text
packages/prompts/
├── homework/
├── tutor/
├── notes/
├── quiz/
├── worksheets/
├── study-planner/
├── moderation/
└── shared/
```

Rules:

- Prompts must be versioned.
- Prompts must not be hardcoded inside feature services.
- Prompt changes must be reviewed.

---

## packages/config

Shared configuration.

Contains:

- Environment validation
- App constants
- Feature flag keys
- Entitlement keys
- Route constants where appropriate

---

## packages/validation

Shared validation schemas.

Used for:

- Frontend forms
- Backend DTO validation where applicable
- Shared request contracts

---

# database/

Database structure.

```text
database/
├── prisma/
│   ├── schema.prisma
│   ├── migrations/
│   └── seed/
│
├── backups/
└── README.md
```

Rules:

- All schema changes must use migrations.
- Manual production database changes are prohibited.
- Seed data must not include real secrets or personal data.

---

# infrastructure/

Infrastructure and deployment files.

```text
infrastructure/
├── docker/
├── nginx/
├── monitoring/
├── terraform/
└── scripts/
```

---

## infrastructure/docker

Contains:

- Dockerfiles
- Docker Compose files
- Local development containers

---

## infrastructure/nginx

Contains:

- Reverse proxy configuration
- Production routing templates
- SSL-related configuration templates

---

## infrastructure/monitoring

Contains:

- Logging configuration
- Metrics configuration
- Health check configuration
- Observability dashboards where applicable

---

## infrastructure/terraform

Future infrastructure as code.

Not required for MVP unless cloud deployment requires it.

---

# scripts/

Developer automation scripts.

```text
scripts/
├── dev.ps1
├── build.ps1
├── test.ps1
├── lint.ps1
├── db-migrate.ps1
├── db-seed.ps1
└── clean.ps1
```

Scripts should be cross-platform where practical.

Windows PowerShell scripts are acceptable during initial development because the primary development environment is Windows.

---

# tests/

Cross-application tests.

```text
tests/
├── e2e/
├── integration/
├── performance/
└── security/
```

Application-specific tests may live inside each app or service.

Critical cross-module tests should live here.

---

# tools/

Internal developer tools.

```text
tools/
├── generators/
├── validators/
├── prompt-testers/
├── migration-checkers/
└── docs-tools/
```

Use for:

- Code generation
- Prompt validation
- Documentation validation
- Database checks
- AI evaluation utilities

---

# docs/

Human-readable product and engineering documentation.

```text
docs/
├── 00-Overview/
├── 01-Product/
├── 02-Business/
├── 03-Research/
├── 04-Architecture/
├── 05-Database/
├── 06-API/
├── 07-AI/
├── 08-Frontend/
├── 09-Backend/
├── 10-Design-System/
├── 11-Security/
├── 12-Testing/
├── 13-Deployment/
├── 14-Roadmap/
└── 15-Meetings/
```

Rules:

- `docs/` is the human source of truth.
- Do not put task execution logs in `docs/`.
- Do not mix AI operational state with long-term documentation.

---

# specifications/

Module-level specifications.

```text
specifications/
├── authentication.md
├── users.md
├── organizations.md
├── institutions.md
├── academic.md
├── homework.md
├── ocr.md
├── ai-tutor.md
├── notes.md
├── quiz.md
├── worksheets.md
├── study-planner.md
├── analytics.md
├── notifications.md
├── dashboard.md
├── subscriptions.md
├── plans-and-features.md
├── payments.md
├── admin-portal.md
├── teachers.md
├── parents.md
├── settings.md
└── landing-site.md
```

Rules:

- Every major module must have a specification.
- Implementation tasks must reference specifications.
- Specifications are more implementation-oriented than product documents.
- Specifications should not contradict `docs/`.

---

# tasks/

Implementation tasks for developers and AI agents.

```text
tasks/
├── authentication/
├── users/
├── organizations/
├── institutions/
├── academic/
├── homework/
├── ocr/
├── ai-tutor/
├── notes/
├── quiz/
├── worksheets/
├── study-planner/
├── analytics/
├── notifications/
├── dashboard/
├── subscriptions/
├── payments/
├── admin/
├── teachers/
├── parents/
├── settings/
└── landing-site/
```

Task files should be small and independently executable.

Example:

```text
tasks/authentication/AUTH-001.md
tasks/authentication/AUTH-002.md
tasks/homework/HW-001.md
tasks/homework/HW-002.md
```

---

# Task Naming Convention

Use stable module prefixes.

| Module | Prefix |
|---|---|
| Authentication | AUTH |
| Users | USER |
| Organizations | ORG |
| Institutions | INST |
| Academic | ACAD |
| Homework | HW |
| OCR | OCR |
| AI Tutor | AIT |
| Notes | NOTE |
| Quiz | QUIZ |
| Worksheets | WS |
| Study Planner | PLAN |
| Analytics | ANLY |
| Notifications | NOTIF |
| Dashboard | DASH |
| Subscriptions | SUB |
| Payments | PAY |
| Admin | ADMIN |
| Teachers | TEACH |
| Parents | PARENT |
| Settings | SET |
| Landing Site | LAND |

---

# .ai/

AI operational workspace.

```text
.ai/
├── CURRENT_STATUS.md
├── ROADMAP.md
├── TASK_EXECUTION_RULES.md
├── TASK_LOG.md
├── MODULE_DEPENDENCIES.md
├── DATABASE_STANDARDS.md
├── API_STANDARDS.md
├── UI_STANDARDS.md
├── SECURITY_RULES.md
└── REVIEW_CHECKLIST.md
```

Purpose:

- AI execution state
- AI task rules
- Current roadmap
- Task log
- Implementation checklist
- Operational context

Rules:

- `.ai/` is not the primary source of product truth.
- `.ai/` summarizes and operationalizes the documentation.
- `.ai/` must stay aligned with `docs/`, `specifications/`, and `tasks/`.

---

# Naming Conventions

## Folders

Use lowercase kebab-case.

Good:

```text
study-planner/
ai-tutor/
admin-portal/
```

Bad:

```text
StudyPlanner/
AI_Tutor/
AdminPortal/
```

---

## React Components

Use PascalCase.

```text
HomeworkCard.tsx
StudyPlanCalendar.tsx
InstitutionSelector.tsx
```

---

## Hooks

Use camelCase beginning with `use`.

```text
useAuth.ts
useHomework.ts
useEntitlements.ts
```

---

## Services

Use PascalCase or domain-specific naming consistently.

```text
HomeworkService.ts
EntitlementService.ts
OrganizationService.ts
```

---

## Types

Use PascalCase.

```text
User.ts
Institution.ts
Organization.ts
Homework.ts
```

---

## Task Files

Use module prefix and number.

```text
AUTH-001.md
HW-001.md
INST-001.md
ORG-001.md
```

---

# Import Rules

Use path aliases for shared packages.

Examples:

```typescript
import { Button } from "@acadedx/ui";
import { User } from "@acadedx/types";
import { apiClient } from "@acadedx/api-client";
```

Avoid long relative imports.

Bad:

```typescript
import { Button } from "../../../../packages/ui/src/Button";
```

---

# Domain Boundary Rules

Modules should not directly access another module's private database logic.

Preferred:

```text
Module A Service
    ↓
Module B Public Service / Interface
```

Avoid:

```text
Module A Repository directly querying Module B internals
```

---

# Organization and Institution Rules

Every module that stores scoped data must include organization and institution context where applicable.

Examples:

- Homework belongs to a student and may have institution context.
- Teacher assignments belong to an institution.
- Payments may belong to user, subscription, organization, or institution depending on plan.
- Analytics should include organization and institution dimensions where applicable.

Do not create new `schoolId` fields for future-facing modules.

Use:

```text
institutionId
```

Use:

```text
organizationId
```

where business ownership or billing scope is relevant.

---

# Generated Files

Generated files should be clearly identified.

Examples:

- Prisma generated client
- OpenAPI generated SDK
- Generated type definitions

Generated files should not be manually edited.

---

# Environment Files

Use:

```text
.env.example
.env.local
.env.development
.env.staging
.env.production
```

Never commit real `.env` files.

---

# Documentation Update Rules

When implementation changes behavior, update:

- Relevant specification
- Relevant task
- API docs
- Database docs
- Architecture docs if architectural impact exists
- `.ai/CURRENT_STATUS.md` where status changes
- `.ai/TASK_LOG.md` after task completion

---

# AI Agent Rules

AI agents must:

- Read `AGENTS.md`.
- Read `.ai/CURRENT_STATUS.md`.
- Read the relevant specification.
- Read the relevant task file.
- Implement only the requested task.
- Avoid unrelated refactoring.
- Update documentation required by the task.
- Run or describe required tests.
- Update `.ai/TASK_LOG.md`.

---

# Anti-Patterns

Avoid:

- Creating random top-level folders.
- Duplicating UI components across apps.
- Hardcoding school-only logic.
- Putting business logic in React components.
- Putting business logic in NestJS controllers.
- Direct AI provider calls from feature modules.
- Direct payment gateway calls from feature modules.
- Storing prompts inside random services.
- Bypassing organization or institution scoping.
- Hardcoding subscription plan names.
- Creating huge tasks that AI agents cannot complete safely.

---

# Future Expansion

The structure should allow future additions such as:

```text
apps/browser-extension/
apps/offline-client/
services/search/
services/reports/
services/ai-worker/
packages/mobile-ui/
packages/analytics-sdk/
packages/enterprise-sdk/
```

Add these only when needed.

---

# Related Documents

- SYSTEM_ARCHITECTURE.md
- TECH_STACK.md
- CODING_STANDARDS.md
- DATABASE_SCHEMA.md
- API_GUIDELINES.md
- PRODUCT_REQUIREMENTS.md