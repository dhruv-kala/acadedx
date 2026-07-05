# Backend Modules

**Document Version:** 1.0.0

**Status:** Approved

**Owner:** Solution Architecture

**Framework:** NestJS

**Last Updated:** July 2026

---

# Purpose

This document defines the backend modular architecture of Acadedx.

Every backend feature must belong to exactly one module.

Modules should be:

- Independent
- Reusable
- Testable
- Loosely Coupled
- Highly Cohesive

---

# Backend Architecture

Acadedx follows Modular Monolith Architecture during the initial phases.

Each module contains:

```

Controller

↓

Service

↓

Repository

↓

Database

```

Business logic belongs only inside Services.

Controllers must remain thin.

---

# Module Structure

Each module follows this structure.

```

homework/

├── controllers/

├── services/

├── repositories/

├── dto/

├── entities/

├── validators/

├── interfaces/

├── events/

├── jobs/

├── tests/

└── homework.module.ts

```

---

# Core Modules

---

## Authentication Module

Responsibilities

- Login
- Logout
- Registration
- Password Reset
- Refresh Tokens
- JWT
- Email Verification

Owns

- Users
- Sessions
- Tokens

Dependencies

- User Module
- Notification Module

---

## User Module

Responsibilities

- User Profile
- Preferences
- Avatar
- Settings

Owns

- User
- Profile

Dependencies

- Authentication

---

## RBAC Module

Responsibilities

- Roles
- Permissions
- Authorization

Owns

- Roles
- Permissions

Used by every module.

---

## School Module

Responsibilities

- Schools
- Classes
- Sections
- Subjects

Owns

Educational hierarchy.

---

## Homework Module

Responsibilities

- Homework Upload
- OCR Processing
- Homework History
- Homework Status

Owns

Homework lifecycle.

Dependencies

- OCR Module
- AI Module
- Storage Module

---

## OCR Module

Responsibilities

- Image Upload
- OCR Extraction
- Language Detection
- Image Enhancement

Dependencies

- AI Module

---

## AI Module

Responsibilities

- LLM Routing
- Prompt Management
- AI Chat
- Homework Solver
- Notes Generator
- Quiz Generator
- Worksheet Generator

Dependencies

- Prompt Module

- RAG Module

---

## Prompt Module

Responsibilities

- Prompt Templates
- Prompt Versioning
- Prompt Testing

Owns

Every AI prompt.

---

## RAG Module

Responsibilities

- Knowledge Base

- Embeddings

- Retrieval

- Vector Search

---

## Quiz Module

Responsibilities

- Quiz Generation

- Quiz Attempts

- Results

---

## Notes Module

Responsibilities

- Notes Generation

- Notes History

---

## Worksheet Module

Responsibilities

- Worksheet Generation

- Answer Keys

---

## Study Planner Module

Responsibilities

- Study Plans

- Goals

- Progress Tracking

---

## Subscription Module

Responsibilities

- Plans

- Billing

- Renewals

- Limits

Dependencies

- Payment Module

---

## Payment Module

Responsibilities

- Payment Processing

- Webhooks

- Invoices

- Refunds

---

## Notification Module

Responsibilities

- Email

- SMS

- Push Notifications

- In-App Notifications

---

## Analytics Module

Responsibilities

- Learning Analytics

- AI Usage

- Dashboard Metrics

- Reports

---

## Admin Module

Responsibilities

- Platform Management

- User Management

- School Management

- AI Monitoring

- System Settings

---

## Audit Module

Responsibilities

- Audit Logs

- Compliance Logs

- Security Logs

---

## Storage Module

Responsibilities

- File Uploads

- Image Storage

- Document Storage

Supports

- AWS S3

- Azure Blob

- Local Storage

---

## Search Module

Responsibilities

- Global Search

- Homework Search

- Quiz Search

- Notes Search

---

# Shared Modules

---

## Config Module

Application configuration.

---

## Database Module

Prisma configuration.

---

## Cache Module

Redis.

---

## Logger Module

Application logging.

---

## Health Module

Health checks.

---

## Scheduler Module

Cron Jobs.

---

# Module Dependencies

```

Authentication

↓

Users

↓

Homework

↓

OCR

↓

AI

↓

Quiz

↓

Notes

↓

Worksheets

↓

Study Planner

↓

Analytics

```

Circular dependencies are prohibited.

---

# Communication

Preferred

```

Controller

↓

Service

↓

Repository

```

Cross-module communication

↓

Service-to-Service

Avoid direct database access between modules.

---

# Events

Modules should communicate using Domain Events when appropriate.

Examples

- HomeworkSolved

- QuizCompleted

- UserRegistered

- SubscriptionActivated

- PaymentCompleted

---

# Security

Every module must implement

- Authentication

- Authorization

- Validation

- Logging

- Exception Handling

---

# Testing

Every module requires

- Unit Tests

- Integration Tests

- API Tests

Minimum Coverage

80%

Critical Modules

95%

---

# Future Microservices

The following modules can be extracted into independent services without significant refactoring.

- AI
- OCR
- Notifications
- Payments
- Analytics
- Search

---

# Definition of Done

A backend module is complete when:

- Controllers implemented

- Services implemented

- Repository implemented

- DTOs completed

- Validation completed

- Authentication enforced

- Authorization enforced

- Logging added

- Tests passing

- Swagger updated

---

# Related Documents

- SYSTEM_ARCHITECTURE.md

- PROJECT_STRUCTURE.md

- DATABASE_SCHEMA.md

- API_GUIDELINES.md

- AUTHENTICATION.md

- SERVICES.md