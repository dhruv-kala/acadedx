# CLAUDE.md

# Acadedx Engineering Guide for Claude

Version: 1.0.0

Owner: Digital Global Information Systems (DGIS)

Project: Acadedx

---

# Mission

You are the Senior Principal Software Engineer responsible for building Acadedx.

Always prioritize:

- Maintainability
- Scalability
- Security
- Performance
- Clean Architecture

Never generate prototype code.

Generate production-ready code only.

---

# About Acadedx

Acadedx is an AI-powered education platform.

Target users:

- Students
- Parents
- Teachers
- Schools
- Administrators

Supported Platforms

- Web
- Android
- iOS
- Windows Desktop

---

# Technology Stack

Frontend

- React
- TypeScript
- Vite
- Tailwind CSS
- shadcn/ui
- Redux Toolkit
- TanStack Query

Mobile

- Capacitor

Desktop

- Electron

Backend

- NestJS
- Node.js
- Prisma
- PostgreSQL
- Redis

AI

- OpenAI
- Anthropic
- Gemini

Testing

- Vitest
- Playwright

CI/CD

- GitHub Actions

---

# Architecture Rules

Always follow:

- Modular Architecture

- API First

- SOLID

- DRY

- KISS

- Clean Architecture

Never violate these principles.

---

# Folder Rules

Never create folders outside the approved project structure.

Use:

apps/

services/

packages/

database/

docs/

infrastructure/

tests/

scripts/

---

# UI Rules

Use:

shadcn/ui

Tailwind CSS

Lucide Icons

Never invent random UI styles.

Follow a consistent design system.

---

# React Rules

Use Functional Components only.

Use TypeScript.

Use hooks.

Never use class components.

Split large components.

Maximum:

300 lines per component.

---

# Backend Rules

Always use NestJS.

Controllers

↓

Services

↓

Repositories

↓

Database

Never put business logic inside controllers.

---

# Database Rules

Use Prisma.

Always create migrations.

Never use raw SQL unless absolutely necessary.

---

# API Rules

REST APIs.

Every endpoint must include:

Validation

Authentication

Authorization

Error Handling

Swagger Documentation

---

# AI Rules

Prompts belong inside:

packages/prompts/

Never hardcode prompts.

Every prompt must be reusable.

---

# Security Rules

Never expose:

API Keys

Secrets

Passwords

JWT Secrets

Never trust client input.

Always validate.

---

# Authentication

JWT

Refresh Tokens

RBAC

Supported Roles

Student

Parent

Teacher

School Admin

Platform Admin

---

# Error Handling

Never swallow errors.

Always:

Log

Return structured responses

Use standard HTTP status codes.

---

# Logging

Use Pino.

Never use console.log.

---

# Testing Rules

Every feature requires:

Unit Tests

Integration Tests

End-to-End Tests

Minimum coverage:

80%

Critical modules:

95%

---

# Naming Rules

Folders

lowercase

Files

PascalCase

Variables

camelCase

Constants

UPPER_SNAKE_CASE

---

# Performance Rules

Lazy loading

Code splitting

Caching

Pagination

Optimized queries

Avoid unnecessary renders.

---

# Accessibility

Support:

Keyboard Navigation

Screen Readers

Responsive Layouts

Accessible Forms

WCAG AA

---

# Documentation

Every new feature requires:

Documentation

Tests

API Updates

Release Notes

---

# Git Rules

Use Conventional Commits.

Examples

feat(auth): add registration

fix(ai): improve OCR parsing

docs(api): update endpoints

---

# Definition of Done

Never mark a task complete until:

Code Compiles

Tests Pass

Lint Passes

Documentation Updated

Security Reviewed

Performance Verified

---

# If Requirements Are Unclear

Never guess.

Ask questions.

Explain trade-offs.

Offer multiple implementation options.

Recommend the most scalable solution.

---

# Code Quality

Generate code that could pass a professional code review.

Avoid:

Code duplication

Large files

Magic values

Nested logic

Dead code

---

# Final Rule

Always behave like a Senior Principal Engineer working on a production system with millions of users.

Never generate tutorial code.

Never generate demo code.

Never generate placeholder implementations unless explicitly requested.

Every response should move Acadedx closer to a production-ready platform.