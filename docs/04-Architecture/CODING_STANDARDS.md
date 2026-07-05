# Acadedx Coding Standards

**Document Version:** 1.0.0

**Status:** Approved

**Owner:** Solution Architecture

**Last Updated:** July 2026

---

# Purpose

This document defines the official coding standards for Acadedx.

All engineers, AI coding assistants, contributors, and automated code generators must follow these standards.

These rules exist to ensure:

- Clean Code
- Maintainability
- Scalability
- Readability
- Security
- Performance
- Consistency

---

# Core Engineering Principles

Every line of code should follow:

- SOLID Principles
- DRY (Don't Repeat Yourself)
- KISS (Keep It Simple)
- YAGNI (You Aren't Gonna Need It)
- Composition over Inheritance
- Single Responsibility Principle

---

# Language Standards

Official language:

TypeScript

Never introduce JavaScript files unless absolutely necessary.

---

# File Naming

React Components

```
HomeworkCard.tsx
QuizDialog.tsx
StudentProfile.tsx
```

Hooks

```
useHomework.ts
useAuth.ts
useQuiz.ts
```

Services

```
HomeworkService.ts
QuizService.ts
AuthService.ts
```

Utilities

```
date.ts
string.ts
validation.ts
```

---

# Folder Naming

Always lowercase.

Example:

```
components/
hooks/
services/
utils/
features/
```

---

# Variable Naming

Use camelCase.

Good

```ts
const studentName = "";
const totalMarks = 0;
const homeworkResult = {};
```

Bad

```ts
const Student_Name = "";
const TOTAL = 0;
```

---

# Constants

Use UPPER_SNAKE_CASE.

```ts
const MAX_UPLOAD_SIZE = 10;
const DEFAULT_LANGUAGE = "en";
```

---

# TypeScript Rules

Always define types.

Good

```ts
interface Student {
  id: string;
  name: string;
}
```

Bad

```ts
const student: any = {};
```

Avoid using `any`.

Prefer:

- interfaces
- types
- generics

---

# React Standards

Use Functional Components only.

Good

```tsx
export function HomeworkCard() {
  return <div />;
}
```

Avoid Class Components.

---

# Component Guidelines

One component = One responsibility.

Maximum:

- 300 lines per component
- Split large components

---

# Hooks

Custom hooks should begin with:

```
use
```

Examples:

```
useAuth
useHomework
useStudent
```

---

# State Management

Global state:

Redux Toolkit

Server state:

TanStack Query

Component state:

React useState

Avoid unnecessary global state.

---

# Styling

Use Tailwind CSS.

Avoid inline styles.

Prefer reusable UI components.

---

# Imports

Order imports as follows:

1. React
2. Third-party libraries
3. Internal packages
4. Relative imports
5. CSS

Example

```ts
import React from "react";
import { useQuery } from "@tanstack/react-query";

import { Button } from "@ui/Button";
import { useAuth } from "@hooks/useAuth";

import "./styles.css";
```

---

# API Standards

Never call APIs directly inside components.

Always use:

```
services/
```

Example

```
HomeworkService
```

---

# Error Handling

Never ignore errors.

Good

```ts
try {
   await service.save();
} catch (error) {
   logger.error(error);
}
```

Bad

```ts
catch {}
```

---

# Logging

Never use:

```ts
console.log()
```

Use:

```
logger.info()
logger.warn()
logger.error()
```

---

# Security Standards

Never:

- Hardcode API keys
- Hardcode passwords
- Store secrets in Git
- Trust client input

Always:

- Validate input
- Sanitize data
- Escape output
- Use HTTPS
- Hash passwords
- Validate file uploads

---

# Environment Variables

Use:

```
.env.local
.env.development
.env.production
```

Never commit:

```
.env
```

---

# Database Standards

Use Prisma.

Never write raw SQL unless necessary.

Always use migrations.

---

# API Response Format

Success

```json
{
  "success": true,
  "data": {}
}
```

Error

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid input"
  }
}
```

---

# Validation

Frontend

Zod

Backend

class-validator

Never trust frontend validation alone.

---

# Authentication

Always:

- Verify JWT
- Check roles
- Validate permissions

Never expose sensitive user information.

---

# AI Standards

All prompts must:

- Be version controlled
- Be stored in packages/prompts
- Be reusable
- Avoid duplication

Never embed prompts directly inside business logic.

---

# Testing Standards

Every feature requires:

- Unit Tests
- Integration Tests
- End-to-End Tests

Target Coverage

Minimum:

80%

Critical modules:

95%

---

# Git Standards

Branch naming

```
feature/homework-solver
feature/quiz-generator
bugfix/login-error
hotfix/payment
```

---

Commit Messages

Use Conventional Commits.

Examples

```
feat(auth): add email verification

fix(homework): correct OCR parsing

refactor(ai): simplify prompt routing

docs(api): update endpoint documentation
```

---

# Code Review Checklist

Before merging:

- Code builds successfully
- Tests pass
- No lint errors
- No security issues
- Documentation updated
- Performance considered
- Naming conventions followed

---

# Performance Standards

Avoid:

- Unnecessary renders
- Duplicate API requests
- Large bundle sizes
- Blocking operations

Prefer:

- Lazy loading
- Code splitting
- Memoization where appropriate
- Pagination for large datasets

---

# Documentation Standards

Every public function should include:

- Purpose
- Parameters
- Return value

Complex business logic should include comments explaining *why*, not *what*.

---

# Definition of Done

Code is complete when:

- Requirements implemented
- Tests passing
- Code reviewed
- Documentation updated
- Security reviewed
- Performance verified
- Product Owner approved

---

# Related Documents

- PROJECT_STRUCTURE.md
- TECH_STACK.md
- SYSTEM_ARCHITECTURE.md
- API_GUIDELINES.md
- SECURITY.md