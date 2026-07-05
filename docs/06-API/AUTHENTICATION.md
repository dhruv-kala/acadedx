# Acadedx API Authentication

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines authentication and authorization standards for Acadedx APIs.

It covers login, registration, token handling, sessions, password reset, email verification, role-based access, organization scope, institution scope, and security requirements.

This document must be followed by all applications and services that access Acadedx APIs.

---

# Authentication Philosophy

Authentication must be secure, simple for users, and scalable for future enterprise use.

Acadedx must support:

- Students
- Parents
- Teachers
- Institution Administrators
- Organization Owners
- Organization Administrators
- Platform Administrators
- Support Agents

The MVP should support email and password authentication.

Future versions may support social login, MFA, passkeys, and enterprise SSO.

---

# Platform Context

Authentication must support the Acadedx platform hierarchy:

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

Every authenticated request should be able to resolve user context, role context, organization context, and institution context where applicable.

---

# Authentication Method

MVP authentication uses:

```text
Email + Password
```

Token model:

```text
JWT Access Token
Refresh Token
```

Password hashing:

```text
Argon2
```

---

# Authentication Endpoints

## Register

```text
POST /api/v1/auth/register
```

Purpose:

Create a new user account.

Supported roles during public registration:

- Student
- Parent
- Teacher

Platform administrator accounts should not be created through public registration.

---

### Request

```json
{
  "firstName": "Dhruv",
  "lastName": "Singh",
  "email": "dhruv@example.com",
  "password": "StrongPassword@123",
  "role": "STUDENT",
  "preferredLanguage": "en"
}
```

---

### Response

```json
{
  "success": true,
  "message": "Registration successful. Please verify your email.",
  "data": {
    "userId": "uuid",
    "email": "dhruv@example.com",
    "requiresEmailVerification": true
  }
}
```

---

# Login

```text
POST /api/v1/auth/login
```

Purpose:

Authenticate a user and create a session.

---

### Request

```json
{
  "email": "dhruv@example.com",
  "password": "StrongPassword@123"
}
```

---

### Response

```json
{
  "success": true,
  "message": "Login successful.",
  "data": {
    "accessToken": "jwt",
    "refreshToken": "refresh_token",
    "expiresIn": 900,
    "user": {
      "id": "uuid",
      "email": "dhruv@example.com",
      "roles": ["STUDENT"],
      "organizationId": "uuid",
      "institutionId": "uuid"
    }
  }
}
```

---

# Refresh Token

```text
POST /api/v1/auth/refresh
```

Purpose:

Issue a new access token using a valid refresh token.

---

### Request

```json
{
  "refreshToken": "refresh_token"
}
```

---

### Response

```json
{
  "success": true,
  "message": "Token refreshed successfully.",
  "data": {
    "accessToken": "new_jwt",
    "refreshToken": "new_refresh_token",
    "expiresIn": 900
  }
}
```

---

# Logout

```text
POST /api/v1/auth/logout
```

Purpose:

Invalidate the current refresh token/session.

---

### Request

```json
{
  "refreshToken": "refresh_token"
}
```

---

### Response

```json
{
  "success": true,
  "message": "Logged out successfully.",
  "data": null
}
```

---

# Logout All Sessions

```text
POST /api/v1/auth/logout-all
```

Purpose:

Invalidate all active sessions for the current user.

Use cases:

- Password changed
- Suspicious activity
- User wants to sign out from all devices

---

# Current User

```text
GET /api/v1/auth/me
```

Purpose:

Return current authenticated user context.

---

### Response

```json
{
  "success": true,
  "message": "Current user retrieved successfully.",
  "data": {
    "id": "uuid",
    "email": "dhruv@example.com",
    "roles": ["STUDENT"],
    "permissions": ["homework.solve"],
    "organizationId": "uuid",
    "institutionId": "uuid",
    "profile": {
      "firstName": "Dhruv",
      "lastName": "Singh"
    }
  }
}
```

---

# Forgot Password

```text
POST /api/v1/auth/forgot-password
```

Purpose:

Start password reset flow.

---

### Request

```json
{
  "email": "dhruv@example.com"
}
```

---

### Response

```json
{
  "success": true,
  "message": "If an account exists, password reset instructions have been sent.",
  "data": null
}
```

Security rule:

Do not reveal whether the email exists.

---

# Reset Password

```text
POST /api/v1/auth/reset-password
```

Purpose:

Reset password using a valid reset token.

---

### Request

```json
{
  "token": "reset_token",
  "newPassword": "NewStrongPassword@123"
}
```

---

### Response

```json
{
  "success": true,
  "message": "Password reset successfully.",
  "data": null
}
```

---

# Change Password

```text
POST /api/v1/auth/change-password
```

Purpose:

Allow authenticated users to change password.

---

### Request

```json
{
  "currentPassword": "OldPassword@123",
  "newPassword": "NewStrongPassword@123"
}
```

---

# Verify Email

```text
POST /api/v1/auth/verify-email
```

Purpose:

Verify user email address.

---

### Request

```json
{
  "token": "email_verification_token"
}
```

---

# Resend Verification Email

```text
POST /api/v1/auth/resend-verification
```

Purpose:

Send a new email verification link.

Rate limiting is mandatory.

---

# Token Strategy

## Access Token

Access tokens should be short-lived.

Recommended MVP lifetime:

```text
15 minutes
```

Access token contains minimal claims.

Suggested claims:

```json
{
  "sub": "userId",
  "email": "user@example.com",
  "roles": ["STUDENT"],
  "sessionId": "uuid",
  "iat": 1234567890,
  "exp": 1234569999
}
```

Do not store sensitive personal data in JWT.

---

## Refresh Token

Refresh tokens should be long-lived but revocable.

Recommended MVP lifetime:

```text
30 days
```

Refresh tokens must be:

- Random
- Stored hashed in database
- Rotated on refresh
- Revoked on logout
- Revoked on suspicious activity
- Revoked on password change where appropriate

---

# Session Management

Each login creates a session.

Session should track:

- User ID
- Refresh token hash
- Device name
- User agent
- IP address
- Created timestamp
- Expiry timestamp
- Revoked timestamp

Users should eventually be able to view and revoke sessions.

---

# Role Model

Supported roles:

```text
PLATFORM_ADMIN
ORGANIZATION_OWNER
ORGANIZATION_ADMIN
INSTITUTION_ADMIN
TEACHER
PARENT
STUDENT
SUPPORT_AGENT
```

Roles may be scoped.

---

# Role Scopes

## Platform Scope

Used for DGIS internal users.

Examples:

```text
PLATFORM_ADMIN
SUPPORT_AGENT
```

---

## Organization Scope

Used for users managing an organization.

Examples:

```text
ORGANIZATION_OWNER
ORGANIZATION_ADMIN
```

Requires:

```text
organizationId
```

---

## Institution Scope

Used for institution-level users.

Examples:

```text
INSTITUTION_ADMIN
TEACHER
STUDENT
PARENT
```

Usually requires:

```text
institutionId
```

However, B2C students may not have institutionId.

---

# Permissions

Permissions provide fine-grained access control.

Examples:

```text
users.manage
organizations.manage
institutions.manage
homework.solve
ai_tutor.chat
quiz.generate
payments.view
admin.audit.view
```

Roles grant permissions.

Permissions are checked server-side.

---

# Authorization Context

Every authenticated API request should resolve:

```json
{
  "requestId": "req_123",
  "userId": "uuid",
  "roles": ["STUDENT"],
  "permissions": ["homework.solve"],
  "organizationId": "uuid",
  "institutionId": "uuid",
  "sessionId": "uuid"
}
```

This context must be available to:

- Guards
- Services
- Repositories
- Logging
- Audit logging
- Analytics
- Entitlement checks

---

# Organization and Institution Scoping

Authentication identifies the user.

Authorization determines what the user can access.

Tenant scoping determines which organization or institution data the user may access.

## Organization Rules

- Organization admins access only their organization.
- Organization owners access only their organization.
- Platform admins may access multiple organizations but must be audited.

## Institution Rules

- Institution admins access only their institution.
- Teachers access assigned institution data.
- Students access their own data.
- Parents access linked children only.

---

# B2C User Context

A student may use Acadedx without being linked to an institution.

In this case:

```text
organizationId = null
institutionId = null
```

The system must still support:

- Homework
- AI Tutor
- Notes
- Quizzes
- Worksheets
- Study Planner
- Subscriptions

---

# Institution User Context

A school or institution-linked student should have:

```text
organizationId
institutionId
studentProfile
```

Institution features must validate institution scope.

---

# Password Policy

Minimum requirements:

- At least 8 characters
- At least one uppercase letter
- At least one lowercase letter
- At least one number
- At least one special character

Future:

- Password breach detection
- Password strength scoring
- Passkeys

---

# Account Status

Supported statuses:

```text
ACTIVE
INACTIVE
SUSPENDED
DELETED
```

## ACTIVE

User can log in.

## INACTIVE

User exists but cannot fully use platform.

## SUSPENDED

User is blocked from login or sensitive actions.

## DELETED

Soft-deleted account.

---

# Email Verification Rules

Email verification should be required for:

- Full account activation
- Password reset eligibility
- Subscription purchase
- Institution invitation acceptance

MVP may allow limited access before verification, but premium and sensitive actions should require verified email.

---

# Rate Limiting

Authentication endpoints must be rate limited.

Recommended MVP limits:

| Endpoint | Limit |
|---|---|
| Login | 10 attempts per 15 minutes |
| Forgot password | 5 requests per hour |
| Resend verification | 5 requests per hour |
| Refresh token | Reasonable session-based limit |

Repeated failures may trigger account lockout or security notification.

---

# Account Lockout

After repeated failed login attempts, the account or IP may be temporarily restricted.

Suggested MVP rule:

```text
5 failed login attempts → temporary cooldown
```

Do not permanently lock account without recovery path.

---

# Security Notifications

Notify users for:

- New device login
- Password changed
- Password reset requested
- Email changed
- Suspicious login
- Sessions revoked

---

# Admin Authentication

Admin access must be stricter than normal user access.

Requirements:

- Separate admin route/app
- Strong password requirements
- MFA in future
- Audit logging
- Session timeout
- IP allow-list in future

Platform admin accounts must not be created through public registration.

---

# Support Agent Access

Support agents must have limited access.

Rules:

- No unnecessary student content access.
- No unrestricted AI conversation access.
- No payment modification unless permission granted.
- All support access must be audited.

---

# Impersonation

Impersonation is not MVP unless explicitly approved.

If implemented, it must require:

- Platform admin permission
- Reason capture
- Full audit log
- Visible banner
- Time limit
- No payment actions during impersonation
- No password changes during impersonation

---

# Social Login

Future supported providers may include:

- Google
- Microsoft
- Apple

Social login should link to existing accounts by verified email.

---

# MFA

Future MFA methods:

- Authenticator app
- Email OTP
- SMS OTP
- Hardware key
- Passkeys

MFA should be required first for:

- Platform admins
- Organization admins
- Institution admins
- Finance users

---

# Enterprise SSO

Enterprise SSO is future scope.

Potential methods:

- SAML
- OpenID Connect

SSO applies mainly to:

- Organizations
- Institutions
- Enterprise customers

Not required for MVP.

---

# Error Responses

## Invalid Credentials

```json
{
  "success": false,
  "error": {
    "code": "AUTH_INVALID_CREDENTIALS",
    "message": "Invalid email or password.",
    "details": []
  }
}
```

---

## Token Expired

```json
{
  "success": false,
  "error": {
    "code": "AUTH_TOKEN_EXPIRED",
    "message": "Your session has expired.",
    "details": []
  }
}
```

---

## Forbidden

```json
{
  "success": false,
  "error": {
    "code": "AUTH_FORBIDDEN",
    "message": "You do not have permission to perform this action.",
    "details": []
  }
}
```

---

## Tenant Scope Violation

```json
{
  "success": false,
  "error": {
    "code": "TENANT_SCOPE_VIOLATION",
    "message": "You do not have access to this resource.",
    "details": []
  }
}
```

---

# Authentication Analytics Events

Track:

```text
user_registered
user_logged_in
user_logged_out
refresh_token_used
password_reset_requested
password_reset_completed
email_verified
login_failed
session_revoked
```

Events should include:

- userId where known
- organizationId where available
- institutionId where available
- IP metadata where appropriate
- device metadata where appropriate

---

# Audit Events

Audit:

- Admin login
- Failed admin login
- Role changes
- Permission changes
- Password reset by admin
- User suspension
- Session revocation
- Organization admin assignment
- Institution admin assignment
- Impersonation

---

# Security Requirements

Authentication implementation must ensure:

- Passwords are hashed with Argon2.
- Refresh tokens are stored hashed.
- JWT secrets are never committed.
- Tokens are transmitted only over HTTPS in production.
- Sensitive errors do not reveal account existence.
- Sessions can be revoked.
- Expired tokens are rejected.
- Suspended users cannot access protected APIs.
- Deleted users cannot access protected APIs.

---

# Testing Requirements

Authentication tests must cover:

- Registration success
- Duplicate email rejection
- Login success
- Invalid login
- Suspended account login rejection
- Token refresh success
- Expired token rejection
- Logout
- Password reset request
- Password reset completion
- Email verification
- Role-based access
- Institution-scoped access
- Organization-scoped access

---

# MVP Requirements

MVP must include:

- Registration
- Login
- Logout
- Refresh token
- Password reset
- Email verification
- Current user endpoint
- User roles
- Basic permissions
- Organization context
- Institution context
- Session storage
- Rate limiting
- Audit events for sensitive admin actions

---

# Deferred Requirements

Deferred from MVP:

- MFA
- Passkeys
- Social login
- Enterprise SSO
- Impersonation
- IP allow-list
- Hardware security keys
- Advanced risk-based authentication

---

# API Review Checklist

Before authentication APIs are approved:

- Password hashing is implemented.
- Refresh tokens are hashed.
- Access tokens expire.
- Refresh tokens rotate.
- Invalid tokens are rejected.
- Suspended users are blocked.
- Roles are loaded correctly.
- Permissions are loaded correctly.
- Organization context is resolved.
- Institution context is resolved.
- Rate limits are applied.
- Security events are logged.
- Tests are included.

---

# Related Documents

- API_GUIDELINES.md
- ENDPOINTS.md
- ERROR_CODES.md
- SECURITY.md
- DATABASE_SCHEMA.md
- PRODUCT_REQUIREMENTS.md
- CODING_STANDARDS.md