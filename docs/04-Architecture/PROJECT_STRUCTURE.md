# Acadedx Project Structure

**Document Version:** 1.0.0

**Status:** Approved

**Owner:** Solution Architecture

**Last Updated:** July 2026

---

# Purpose

This document defines the official repository structure for Acadedx.

Every source file, configuration, documentation, asset, and deployment artifact must follow this structure.

No new top-level folders should be added without architecture approval.

---

# Repository Overview

Acadedx uses a **Monorepo Architecture**.

Advantages:

- Shared code
- Easier maintenance
- Consistent standards
- Shared UI components
- Shared TypeScript types
- Unified CI/CD
- Better developer experience

---

# Root Structure

```
acadedx/

├── apps/
├── services/
├── packages/
├── infrastructure/
├── database/
├── docs/
├── scripts/
├── tools/
├── tests/
├── .github/

├── .env.example
├── .gitignore
├── package.json
├── pnpm-workspace.yaml
├── turbo.json
├── README.md
├── LICENSE
├── CONTRIBUTING.md
├── CODE_OF_CONDUCT.md
```

---

# apps/

Contains user-facing applications.

```
apps/

web/
mobile/
desktop/
admin/
landing/
```

---

## apps/web

React + Vite

Contains the primary web application.

```
apps/web/

src/
public/
assets/
components/
pages/
hooks/
layouts/
contexts/
services/
stores/
types/
utils/
styles/
router/

vite.config.ts
package.json
```

---

## apps/mobile

Capacitor application.

```
apps/mobile/

android/
ios/
src/
capacitor.config.ts
package.json
```

---

## apps/desktop

Electron application.

```
apps/desktop/

electron/
src/
package.json
```

---

## apps/admin

Administrative portal.

Contains:

- User Management
- Analytics
- AI Configuration
- Subscription Management

---

## apps/landing

Marketing website.

Contains:

- Landing Pages
- Pricing
- Blog
- Documentation
- SEO Pages

---

# services/

Backend services.

```
services/

api/
auth/
ai/
ocr/
quiz/
notes/
worksheet/
planner/
notification/
analytics/
gateway/
```

Each service contains:

```
src/

controllers/
services/
repositories/
entities/
dto/
middleware/
guards/
interceptors/
filters/
validators/
utils/

test/
```

---

# packages/

Shared code.

```
packages/

ui/
types/
config/
eslint-config/
tsconfig/
utils/
sdk/
prompts/
```

---

## packages/ui

Reusable UI components.

Examples:

- Buttons
- Inputs
- Cards
- Dialogs
- Tables
- Navigation
- Charts

---

## packages/types

Shared TypeScript interfaces.

Examples:

- User
- Homework
- Quiz
- Notes
- Subscription

---

## packages/prompts

Centralized AI prompts.

```
packages/prompts/

homework/
tutor/
quiz/
notes/
planner/
translation/
```

---

# database/

Database assets.

```
database/

prisma/
migrations/
seed/
backups/
```

---

# infrastructure/

Infrastructure as Code.

```
infrastructure/

docker/
kubernetes/
terraform/
nginx/
monitoring/
```

---

# docs/

All documentation.

```
docs/

01-Product/
02-Business/
03-Design/
04-Architecture/
05-Backend/
06-Frontend/
07-AI/
08-Database/
09-API/
10-Security/
11-DevOps/
12-QA/
13-Operations/
14-Legal/
15-Marketing/
16-Features/
17-Release/
ADR/
```

---

# scripts/

Automation scripts.

Examples:

- Build
- Deploy
- Database
- Backup
- Seed
- Code Generation

---

# tools/

Developer tools.

Examples:

- Prompt generators
- Code generators
- Documentation tools
- Migration helpers

---

# tests/

Global testing.

```
tests/

unit/
integration/
e2e/
performance/
security/
```

---

# .github/

GitHub configuration.

```
.github/

workflows/
ISSUE_TEMPLATE/
PULL_REQUEST_TEMPLATE.md
CODEOWNERS
```

---

# Naming Conventions

Folders

```
lowercase
```

Examples

```
services
packages
components
hooks
```

---

Files

```
PascalCase

UserCard.tsx
HomeworkForm.tsx
```

Configuration Files

```
kebab-case

docker-compose.yml
pnpm-workspace.yaml
```

---

# Import Rules

Always use path aliases.

Example

```
@ui
@types
@utils
@services
```

Avoid long relative imports.

❌

```
../../../../components/Button
```

✅

```
@ui/Button
```

---

# Repository Rules

- No duplicate components
- No business logic in UI
- No hardcoded secrets
- No circular dependencies
- Shared code belongs in packages
- AI prompts belong in packages/prompts
- Documentation belongs in docs

---

# Future Expansion

Reserved folders:

```
apps/browser-extension
apps/chrome-extension
apps/watch
apps/tv

services/video
services/search
services/recommendation

packages/ml
packages/voice

tools/ai-agents
```

---

# Repository Governance

Changes to the project structure require:

- Architecture Review
- Engineering Approval
- Documentation Update

---

# Related Documents

- SYSTEM_ARCHITECTURE.md
- TECH_STACK.md
- CODING_STANDARDS.md
- DATABASE_SCHEMA.md
- API_GUIDELINES.md