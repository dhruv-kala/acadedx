# Acadedx Technology Stack

**Document Version:** 1.0.0

**Status:** Approved

**Owner:** Solution Architecture

**Last Updated:** July 2026

---

# Purpose

This document defines the official technology stack for Acadedx.

Every engineering team should follow this document when building new features. Technology choices should not be changed without an approved Architecture Decision Record (ADR).

---

# Technology Principles

The technology stack must be:

- Cross-platform
- Cloud Native
- AI First
- API First
- Secure by Design
- Highly Scalable
- Maintainable
- Developer Friendly
- Enterprise Ready

---

# High-Level Stack

| Layer | Technology |
|---------|------------|
| Frontend | React + TypeScript |
| Web Build Tool | Vite |
| Mobile | Capacitor |
| Windows Desktop | Electron |
| Backend | Node.js + NestJS |
| Language | TypeScript |
| Database | PostgreSQL |
| Cache | Redis |
| Object Storage | S3 Compatible Storage |
| ORM | Prisma |
| Authentication | JWT + Refresh Tokens |
| API | REST |
| AI Providers | OpenAI, Anthropic, Google Gemini |
| OCR | Mistral OCR / Azure AI Vision |
| Search | PostgreSQL Full Text (Future: OpenSearch) |
| Queue | BullMQ |
| Monitoring | OpenTelemetry |
| Logging | Pino |
| Testing | Vitest + Playwright |
| CI/CD | GitHub Actions |
| Containerization | Docker |
| Reverse Proxy | Nginx |
| Cloud | AWS / Azure / GCP |

---

# Frontend

Framework

React

Reason

- Large ecosystem
- Fast development
- Reusable components
- Excellent TypeScript support

---

Language

TypeScript

Reason

- Better code quality
- Type safety
- Easier maintenance
- Better IDE support

---

Build Tool

Vite

Reason

- Extremely fast
- Modern architecture
- Excellent React support

---

Styling

Tailwind CSS

Reason

- Utility-first
- Easy maintenance
- Consistent UI
- Responsive by default

---

UI Components

shadcn/ui

Reason

- Accessible
- Modern
- Customizable
- Production-ready

---

Icons

Lucide Icons

---

Charts

Recharts

---

State Management

Redux Toolkit

Reason

- Predictable state
- Enterprise standard
- Excellent developer tools

---

Data Fetching

TanStack Query

Reason

- Automatic caching
- Background updates
- Retry support
- Optimistic updates

---

Forms

React Hook Form

Validation

Zod

---

# Mobile

Framework

Capacitor

Reason

- Single codebase
- Native APIs
- Android support
- iOS support

---

# Desktop

Framework

Electron

Reason

- Windows desktop application
- Same React codebase
- Native desktop capabilities

---

# Backend

Runtime

Node.js LTS

---

Framework

NestJS

Reason

- Modular architecture
- Dependency Injection
- Enterprise patterns
- Excellent scalability
- Built-in testing support

---

Language

TypeScript

---

API Style

REST

Future

GraphQL

WebSockets

---

ORM

Prisma

Reason

- Type-safe database access
- Easy migrations
- Excellent developer experience

---

# Database

Primary Database

PostgreSQL

Reason

- Reliable
- ACID compliant
- Open source
- Scalable

---

Cache

Redis

Purpose

- Session storage
- AI caching
- Rate limiting
- Performance optimization

---

Object Storage

Amazon S3 Compatible Storage

Stores

- Homework Images
- PDFs
- Worksheets
- User Uploads
- AI Generated Files

---

# AI Layer

Supported Providers

- OpenAI
- Anthropic Claude
- Google Gemini

Architecture

Provider abstraction layer allowing easy switching between AI providers without changing business logic.

---

# OCR

Primary

Mistral OCR

Secondary

Azure AI Vision

Purpose

- Homework image recognition
- Mathematical formulas
- Printed text
- Handwritten text (future)

---

# Authentication

Authentication

JWT Access Tokens

Refresh Tokens

Role-Based Access Control (RBAC)

Supported Roles

- Student
- Parent
- Teacher
- School Administrator
- Platform Administrator

---

# Background Jobs

Framework

BullMQ

Purpose

- Email sending
- Notification delivery
- Report generation
- AI processing
- Scheduled jobs

---

# Monitoring

Logging

Pino

Metrics

OpenTelemetry

Error Tracking

Sentry

---

# Security

Password Hashing

Argon2

Encryption

AES-256

Transport

HTTPS TLS 1.3

Secrets

Environment Variables

Future

Cloud Secret Manager

---

# Testing

Unit Testing

Vitest

API Testing

Supertest

Frontend Testing

React Testing Library

End-to-End

Playwright

---

# Documentation

API Documentation

OpenAPI (Swagger)

Architecture

Markdown

Developer Docs

Markdown

---

# DevOps

Containerization

Docker

Reverse Proxy

Nginx

CI/CD

GitHub Actions

Infrastructure

Docker Compose (Development)

Kubernetes (Future)

---

# Cloud Deployment

Supported Providers

- Amazon Web Services (AWS)
- Microsoft Azure
- Google Cloud Platform (GCP)

The architecture should remain cloud-agnostic.

---

# Development Tools

IDE

Visual Studio Code

Version Control

Git

Repository

GitHub

Package Manager

pnpm

Code Formatting

Prettier

Linting

ESLint

Commit Standards

Conventional Commits

---

# Versioning

API Versioning

Semantic Versioning (SemVer)

Application Releases

Major.Minor.Patch

Example

1.0.0

1.1.0

1.1.1

---

# Future Technologies

Potential future additions

- GraphQL
- OpenSearch
- Kubernetes
- gRPC
- Event Bus
- AI Voice Models
- Edge Functions

---

# Technology Governance

Any change to this technology stack requires:

- Architecture Review
- ADR Approval
- Engineering Approval
- Product Approval
- Documentation Update

---

# Related Documents

- SYSTEM_ARCHITECTURE.md
- PROJECT_STRUCTURE.md
- DATABASE_SCHEMA.md
- SECURITY.md
- DEPLOYMENT.md
- ADR/