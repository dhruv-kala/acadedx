# API Guidelines

**Document Version:** 1.0.0

**Status:** Approved

**Owner:** Solution Architecture

**Last Updated:** July 2026

---

# Purpose

This document defines the API standards for the Acadedx platform.

Every backend service, frontend application, mobile app, desktop application, AI service, and third-party integration must follow these standards.

Objectives:

- Consistency
- Scalability
- Security
- Performance
- Maintainability
- Backward Compatibility

---

# API Style

Acadedx follows a REST-first architecture.

Future versions may introduce GraphQL for analytics and reporting, but REST APIs remain the primary interface.

Base URL

```
https://api.acadedx.com/v1
```

Development

```
http://localhost:3000/api/v1
```

---

# API Versioning

Versioning is mandatory.

Example

```
/api/v1/users

/api/v1/homework

/api/v1/quiz
```

Never remove existing endpoints without deprecation.

---

# Resource Naming

Use nouns.

Good

```
/users

/homework

/quizzes

/notes

/schools

/worksheets
```

Bad

```
/getUsers

/createHomework

/deleteQuiz
```

---

# HTTP Methods

GET

Retrieve resources.

POST

Create resources.

PUT

Replace a resource.

PATCH

Update part of a resource.

DELETE

Soft delete a resource unless permanent deletion is explicitly required.

---

# Standard Response Format

Success

```json
{
  "success": true,
  "message": "Homework generated successfully.",
  "data": {}
}
```

Failure

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request.",
    "details": []
  }
}
```

---

# HTTP Status Codes

200 OK

201 Created

204 No Content

400 Bad Request

401 Unauthorized

403 Forbidden

404 Not Found

409 Conflict

422 Validation Error

429 Too Many Requests

500 Internal Server Error

---

# Pagination

Use cursor-based pagination wherever possible.

Request

```
GET /homework?cursor=abc123&limit=20
```

Response

```json
{
  "success": true,
  "data": [],
  "pagination": {
    "nextCursor": "xyz987",
    "hasMore": true
  }
}
```

Maximum limit

100

Default limit

20

---

# Filtering

Example

```
GET /users?role=student

GET /homework?subject=science

GET /quiz?grade=7
```

---

# Sorting

```
GET /users?sort=name

GET /users?sort=-createdAt
```

Negative indicates descending.

---

# Searching

```
GET /users?search=dhruv

GET /schools?search=global
```

---

# Authentication

Authentication uses JWT.

Authorization uses Role-Based Access Control (RBAC).

Authorization Header

```
Authorization: Bearer <access_token>
```

---

# User Roles

Platform Admin

School Admin

Teacher

Parent

Student

Guest

---

# Validation

All APIs must validate:

- Request body
- Query parameters
- Route parameters
- Uploaded files

Validation errors return HTTP 422.

---

# Idempotency

POST endpoints that process payments or AI-intensive operations should support idempotency keys.

Header

```
Idempotency-Key: <uuid>
```

---

# Rate Limiting

Default

100 requests per minute.

AI Endpoints

20 requests per minute.

Authentication

10 login attempts per 15 minutes.

---

# File Upload Standards

Supported

Images

PDF

DOCX

TXT

Maximum upload size

20 MB

Virus scanning is mandatory before processing.

---

# AI Endpoints

Examples

```
POST /ai/homework/solve

POST /ai/quiz/generate

POST /ai/notes/generate

POST /ai/worksheet/generate

POST /ai/chat
```

AI requests should be asynchronous for long-running operations.

---

# Error Codes

Examples

```
AUTH_INVALID_TOKEN

AUTH_TOKEN_EXPIRED

VALIDATION_ERROR

RESOURCE_NOT_FOUND

RATE_LIMIT_EXCEEDED

AI_PROVIDER_ERROR

OCR_FAILED

FILE_TOO_LARGE
```

---

# Logging

Every request must include:

- Request ID
- User ID (if authenticated)
- Timestamp
- Execution Time
- HTTP Status

Sensitive information must never be logged.

---

# Security

All endpoints must:

- Require HTTPS
- Validate JWT
- Enforce RBAC
- Sanitize inputs
- Escape outputs
- Protect against SQL Injection
- Protect against XSS
- Protect against CSRF where applicable

---

# API Documentation

Every endpoint must include:

- Description
- Parameters
- Request Example
- Response Example
- Error Responses
- Authentication Requirements

Swagger/OpenAPI documentation is mandatory.

---

# Deprecation Policy

Deprecated APIs remain supported for at least one major release.

Deprecated endpoints must include deprecation warnings in documentation and response headers where appropriate.

---

# Performance Targets

Average response time

< 300 ms

AI endpoints

< 10 seconds

File upload acknowledgment

< 2 seconds

Availability

99.9%

---

# Naming Conventions

Endpoints

Plural nouns

JSON

camelCase

Enums

UPPER_SNAKE_CASE

UUIDs

RFC 4122 compliant

---

# Definition of Done

An API is complete when:

- Endpoint implemented
- Validation added
- Authentication enforced
- Authorization verified
- Tests written
- Swagger updated
- Performance reviewed
- Security reviewed
- Product Owner approved

---

# Related Documents

- AUTHENTICATION.md
- ENDPOINTS.md
- ERROR_CODES.md
- DATABASE_SCHEMA.md
- SECURITY.md