# Backend Authentication

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Backend Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the backend implementation requirements for authentication in Acadedx.

It is implementation-specific and should be read together with:

```text
docs/06-API/AUTHENTICATION.md
docs/11-Security/SECURITY.md
docs/11-Security/RBAC.md
docs/09-Backend/MODULES.md
docs/09-Backend/SERVICES.md
````

Acadedx is a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
```

A School is an Institution type.

Authentication must support global user identity while authorization and access are granted through Organization and Institution memberships.

---

# Authentication Goals

Backend authentication must ensure:

1. Users are securely identified before accessing protected APIs.
2. Passwords are never stored in plaintext.
3. Access tokens are short-lived.
4. Refresh tokens are rotatable and revocable.
5. Sessions can be invalidated.
6. Account status is enforced server-side.
7. Email verification and password reset are secure.
8. Authentication produces a request context usable by RBAC and tenant scoping.
9. Authentication does not hardcode one Institution per user.
10. Future MFA and enterprise SSO can be added without redesigning the core model.

---

# Core Authentication Model

Authentication answers:

```text
Who is the user?
```

Authorization answers:

```text
What can this user do, and in which Organization or Institution?
```

Do not merge these concepts.

A user account is global.

A user's access is resolved through:

```text
User → Membership → Role → Permission → Scope
```

The `users` table must not be treated as the only source of Organization or Institution access.

A user may belong to multiple Organizations or Institutions in the future.

---

# Backend Modules Involved

Authentication depends on these modules:

```text
AuthModule
UsersModule
MembershipsModule
AuthorizationModule
ConfigModule
DatabaseModule
CommonModule
AuditModule
NotificationsModule
```

Optional or future dependencies:

```text
MfaModule
SsoModule
IdentityProviderModule
SecurityEventsModule
```

---

# AuthModule Responsibilities

The `AuthModule` is responsible for:

* User login
* User registration, where enabled
* Invitation-based onboarding
* Password verification
* Password reset
* Email verification
* Access token generation
* Refresh token generation
* Refresh token rotation
* Logout
* Logout all sessions
* Session revocation
* Current user context
* Authentication guards
* Token strategy
* Authentication-related audit events

The `AuthModule` must not own business authorization rules.

RBAC and resource access must be handled by `AuthorizationModule`.

---

# Recommended AuthModule Structure

```text
src/modules/auth/
├── controllers/
│   └── auth.controller.ts
├── services/
│   ├── auth.service.ts
│   ├── token.service.ts
│   ├── password.service.ts
│   ├── session.service.ts
│   ├── email-verification.service.ts
│   └── password-reset.service.ts
├── strategies/
│   ├── jwt.strategy.ts
│   └── refresh-token.strategy.ts
├── guards/
│   ├── jwt-auth.guard.ts
│   ├── refresh-token.guard.ts
│   └── active-user.guard.ts
├── dto/
│   ├── login.dto.ts
│   ├── register.dto.ts
│   ├── refresh-token.dto.ts
│   ├── forgot-password.dto.ts
│   ├── reset-password.dto.ts
│   └── change-password.dto.ts
├── types/
│   ├── auth-token-payload.ts
│   └── authenticated-user.ts
└── auth.module.ts
```

---

# Authentication Flow

## Login Flow

```text
1. Client submits email and password.
2. Backend validates request DTO.
3. AuthService finds active user by email.
4. PasswordService verifies password hash.
5. Backend checks account status.
6. Backend checks email verification requirement.
7. TokenService creates access token.
8. SessionService creates refresh-token-backed session.
9. Backend records login event.
10. Backend returns tokens and safe user context.
```

Failure must use safe errors.

Do not reveal whether an email exists.

Use:

```text
AUTH_INVALID_CREDENTIALS
```

for invalid email or password.

---

# Login Endpoint

```http
POST /api/v1/auth/login
```

Request:

```json
{
  "email": "admin@example.com",
  "password": "StrongPassword123!"
}
```

Response:

```json
{
  "success": true,
  "message": "Login successful.",
  "data": {
    "accessToken": "jwt_access_token",
    "refreshToken": "jwt_refresh_token",
    "expiresIn": 900,
    "user": {
      "id": "usr_123",
      "email": "admin@example.com",
      "fullName": "Amit Sharma"
    }
  }
}
```

The response may include memberships or active context if required by the frontend.

Do not include sensitive fields.

---

# Access Token

Access tokens must be:

* JWT-based for MVP.
* Short-lived.
* Signed with a secure secret or private key.
* Validated on every protected API request.
* Free of sensitive personal data.
* Free of authorization-heavy state that may become stale.

Recommended lifetime:

```text
15 minutes
```

Actual lifetime should be environment-configurable.

---

# Access Token Payload

Recommended payload:

```json
{
  "sub": "usr_123",
  "sid": "sess_123",
  "typ": "access",
  "iat": 1783256400,
  "exp": 1783257300
}
```

Recommended fields:

| Field | Purpose    |
| ----- | ---------- |
| `sub` | User ID    |
| `sid` | Session ID |
| `typ` | Token type |
| `iat` | Issued at  |
| `exp` | Expiration |

Avoid storing these directly in the token unless there is a clear reason:

```text
roles
permissions
organizationId
institutionId
learnerId
guardianId
teacherId
```

Reason:

* Roles may change.
* Memberships may be revoked.
* Institution access may change.
* Token would remain stale until expiry.

The backend should resolve current memberships and permissions from trusted storage when needed.

---

# Refresh Token

Refresh tokens must be:

* Long-lived relative to access tokens.
* Rotated on use.
* Revocable.
* Stored hashed in the database.
* Bound to a session record.
* Invalidated on logout.
* Invalidated on suspicious reuse.

Do not store raw refresh tokens.

Store only a secure hash.

Recommended lifetime:

```text
7 to 30 days
```

Final lifetime should depend on product risk, user type, and environment.

---

# Refresh Token Rotation

Refresh flow:

```text
1. Client sends refresh token.
2. Backend verifies token signature and token type.
3. Backend finds session by session ID.
4. Backend compares hashed refresh token.
5. Backend checks session status and expiry.
6. Backend revokes or rotates previous refresh token.
7. Backend creates new access token.
8. Backend creates new refresh token.
9. Backend stores hash of new refresh token.
10. Backend returns new token pair.
```

If an old refresh token is reused after rotation, treat it as suspicious.

Recommended response:

```text
AUTH_REFRESH_TOKEN_INVALID
```

Recommended action:

* Revoke the affected session.
* Optionally revoke all user sessions if risk is high.
* Record a security event.

---

# Session Model

A session represents an authenticated login state.

Recommended fields:

```text
sessionId
userId
refreshTokenHash
status
ipAddress
userAgent
deviceName
lastUsedAt
expiresAt
revokedAt
createdAt
updatedAt
```

Optional future fields:

```text
mfaVerifiedAt
riskScore
deviceId
locationSummary
revokedReason
```

Session status values:

```text
ACTIVE
REVOKED
EXPIRED
SUSPICIOUS
```

---

# Logout

Logout must revoke the current refresh token session.

Endpoint:

```http
POST /api/v1/auth/logout
```

Request:

```json
{
  "refreshToken": "jwt_refresh_token"
}
```

Response:

```json
{
  "success": true,
  "message": "Logged out successfully.",
  "data": null
}
```

Logout should be idempotent from the client perspective.

If the token is already invalid, do not expose sensitive details.

---

# Logout All Sessions

Endpoint:

```http
POST /api/v1/auth/logout-all
```

Authentication:

```text
Required
```

Behavior:

* Revoke all active sessions for the authenticated user.
* Keep current access token valid only until expiry unless token denylist is implemented.
* Record audit/security event.

MVP status:

```text
V1 or MVP Optional
```

---

# Current User Context

Endpoint:

```http
GET /api/v1/auth/me
```

Purpose:

Return current authenticated user and access context.

Recommended response:

```json
{
  "success": true,
  "data": {
    "user": {
      "id": "usr_123",
      "email": "admin@example.com",
      "fullName": "Amit Sharma",
      "status": "ACTIVE"
    },
    "memberships": [
      {
        "organizationId": "org_123",
        "institutionId": "inst_123",
        "role": "INSTITUTION_ADMIN",
        "status": "ACTIVE"
      }
    ],
    "activeContext": {
      "organizationId": "org_123",
      "institutionId": "inst_123"
    }
  }
}
```

The MVP may return one active Organization and one active Institution.

Do not design the response so that multiple memberships become impossible later.

---

# Request Context Resolution

Every authenticated request should resolve a backend request context.

Recommended context:

```ts
export interface RequestContext {
  requestId: string;
  userId: string;
  sessionId?: string;
  organizationId?: string;
  institutionId?: string;
  roles: string[];
  permissions: string[];
  memberships: Array<{
    organizationId?: string;
    institutionId?: string;
    role: string;
    status: string;
  }>;
  ipAddress?: string;
  userAgent?: string;
}
```

This context must be available to:

* Guards
* Controllers
* Services
* Policies
* Repositories where needed
* Audit logging
* Background job creation
* AI context retrieval
* File access checks

---

# Context Resolution Rules

The backend should resolve request context from:

1. Valid access token.
2. Active user record.
3. Active session, where session validation is required.
4. Active memberships.
5. Route parameters such as `organizationId` and `institutionId`.
6. Headers only where explicitly approved.

Do not trust client-provided Organization or Institution IDs unless they are validated against memberships and resource ownership.

---

# Active Context Selection

In the MVP, a user may usually have one Organization and one Institution.

In the future, a user may have multiple memberships.

The backend should support active context selection.

Options:

## Path-Based Context

Preferred for scoped APIs:

```text
/organizations/{organizationId}/institutions/{institutionId}/learners
```

The route provides explicit scope.

## User-Selected Context

For UI convenience, the frontend may store selected Organization and Institution.

The backend must still validate the selected context.

## Header-Based Context

Optional future pattern:

```http
X-Organization-Id: org_123
X-Institution-Id: inst_123
```

Headers must never replace server-side validation.

---

# Registration Strategy

Registration should be controlled.

Recommended MVP approach:

```text
Invitation-based onboarding
```

Reason:

* Institution-first product
* Better tenant control
* Prevents orphan users
* Reduces abuse
* Aligns user with Organization/Institution membership

Open public registration may be added only if direct-to-learner product flows remain part of the strategy.

---

# Invitation-Based Onboarding

Invitation flow:

```text
1. Authorized admin invites user by email.
2. Backend creates invitation with role and scope.
3. Email is sent to invited user.
4. User accepts invitation.
5. User sets password or links existing account.
6. Backend creates or updates user.
7. Backend creates membership.
8. Invitation is marked accepted.
9. Audit log is recorded.
```

Invitation must include:

```text
invitationId
email
organizationId
institutionId
role
status
tokenHash
expiresAt
invitedByUserId
acceptedAt
createdAt
```

Do not store raw invitation token.

---

# Public Registration

If public registration is enabled, it must:

* Rate-limit registrations.
* Verify email.
* Avoid creating privileged roles.
* Avoid creating Institution-scoped access without a valid onboarding flow.
* Prevent duplicate user accounts.
* Record source and consent metadata where required.

Public registration should not grant admin roles.

---

# Email Verification

Email verification may be required before full access.

Flow:

```text
1. User registers or accepts invitation.
2. Backend creates email verification token.
3. Token hash is stored.
4. Email is sent.
5. User submits token.
6. Backend validates token.
7. User email is marked verified.
```

Email verification tokens must:

* Be random and unguessable.
* Be stored hashed.
* Expire.
* Be single-use.
* Be rate-limited for resend.

---

# Password Reset

Password reset flow:

```text
1. User submits email.
2. Backend responds generically.
3. If account exists, backend creates reset token.
4. Token hash is stored.
5. Email is sent.
6. User submits token and new password.
7. Backend validates token.
8. Backend updates password hash.
9. Backend revokes active sessions where appropriate.
10. Backend records security event.
```

Password reset response must not reveal whether an email exists.

Use generic response:

```json
{
  "success": true,
  "message": "If the email exists, password reset instructions have been sent.",
  "data": null
}
```

---

# Password Hashing

Passwords must be hashed using a strong algorithm.

Recommended:

```text
Argon2id
```

Acceptable:

```text
bcrypt
```

Requirements:

* Never store plaintext passwords.
* Never log passwords.
* Use strong cost parameters.
* Use unique salts.
* Support cost upgrades.
* Rehash on login if password hash parameters are outdated.
* Keep password hashing parameters configurable.

---

# Password Policy

MVP password policy should include:

```text
Minimum length: 10 characters
Require at least one letter
Require at least one number or symbol
Reject common weak passwords where practical
```

Do not enforce overly complex rules that encourage unsafe password reuse.

Future improvements:

* Breached password detection
* Passkeys
* MFA
* Enterprise SSO
* Passwordless login

---

# Change Password

Endpoint:

```http
POST /api/v1/auth/change-password
```

Authentication:

```text
Required
```

Request:

```json
{
  "currentPassword": "OldStrongPassword123!",
  "newPassword": "NewStrongPassword123!"
}
```

Rules:

* Verify current password.
* Validate new password policy.
* Update password hash.
* Revoke other sessions where appropriate.
* Audit the event.
* Do not log password values.

---

# Account Status Checks

Authentication must enforce user status.

User statuses:

```text
ACTIVE
PENDING_VERIFICATION
DISABLED
SUSPENDED
ARCHIVED
```

Rules:

| Status                 | Login Behavior                         |
| ---------------------- | -------------------------------------- |
| `ACTIVE`               | Allowed                                |
| `PENDING_VERIFICATION` | Limited or blocked depending on policy |
| `DISABLED`             | Blocked                                |
| `SUSPENDED`            | Blocked                                |
| `ARCHIVED`             | Blocked                                |

Relevant error codes:

```text
AUTH_ACCOUNT_DISABLED
AUTH_ACCOUNT_SUSPENDED
AUTH_EMAIL_NOT_VERIFIED
```

---

# Organization and Institution Status Checks

Authentication itself should validate user identity.

Authorization or context resolution should validate:

* Active Organization
* Active Institution
* Active membership

A user with a valid password must still be denied access to a suspended Organization or inactive Institution.

Relevant errors:

```text
ORG_INACTIVE
INST_INACTIVE
TENANT_MEMBERSHIP_INACTIVE
```

---

# Guards

## JwtAuthGuard

Purpose:

* Validate access token.
* Attach authenticated user payload.
* Reject missing or invalid token.

Failure errors:

```text
AUTH_REQUIRED
AUTH_INVALID_TOKEN
AUTH_TOKEN_EXPIRED
```

---

## ActiveUserGuard

Purpose:

* Ensure user exists.
* Ensure user status allows access.
* Reject disabled or suspended accounts.

Failure errors:

```text
USER_NOT_FOUND
AUTH_ACCOUNT_DISABLED
AUTH_ACCOUNT_SUSPENDED
```

---

## RefreshTokenGuard

Purpose:

* Validate refresh token.
* Ensure token type is refresh.
* Ensure session exists and is active.
* Validate hashed token match.

Failure errors:

```text
AUTH_REFRESH_TOKEN_INVALID
AUTH_REFRESH_TOKEN_EXPIRED
AUTH_SESSION_REVOKED
```

---

## MembershipGuard

Purpose:

* Ensure user has active membership for requested scope.
* Load effective memberships.
* Reject inactive membership.

Failure errors:

```text
TENANT_MEMBERSHIP_REQUIRED
TENANT_MEMBERSHIP_INACTIVE
TENANT_SCOPE_VIOLATION
```

---

# JWT Strategy

JWT strategy must:

* Verify token signature.
* Verify expiration.
* Verify token type.
* Extract `sub` as `userId`.
* Extract `sid` as `sessionId`.
* Attach minimal payload to request.
* Avoid loading excessive user data inside the strategy.

Recommended payload type:

```ts
export interface AccessTokenPayload {
  sub: string;
  sid: string;
  typ: 'access';
  iat: number;
  exp: number;
}
```

---

# Refresh Token Strategy

Refresh token strategy must:

* Verify token signature.
* Verify token type is `refresh`.
* Extract user ID and session ID.
* Validate the token hash against stored session.
* Validate session status.
* Reject reused or revoked tokens.

Recommended payload type:

```ts
export interface RefreshTokenPayload {
  sub: string;
  sid: string;
  typ: 'refresh';
  iat: number;
  exp: number;
}
```

---

# Token Secrets and Signing

Token signing secrets must be environment-specific.

Rules:

* Do not commit secrets to source control.
* Use strong secrets or asymmetric keys.
* Rotate secrets if exposed.
* Keep access and refresh token secrets separate where practical.
* Support key rotation in future.

Recommended config keys:

```text
JWT_ACCESS_SECRET
JWT_REFRESH_SECRET
JWT_ACCESS_EXPIRES_IN
JWT_REFRESH_EXPIRES_IN
```

---

# Token Storage on Client

Backend recommendation for clients:

* Store access token only as long as needed.
* Store refresh token securely.
* Prefer secure, HttpOnly cookies for browser clients where architecture supports it.
* Avoid localStorage for highly sensitive token storage if cookie-based sessions are feasible.

Final browser token strategy should be confirmed with frontend architecture and CSRF requirements.

---

# CSRF Considerations

If refresh tokens are stored in HttpOnly cookies, CSRF protection is required for state-changing routes.

Controls may include:

* SameSite cookies
* CSRF token
* Origin checking
* CORS allowlist
* Double-submit cookie pattern

If tokens are stored in Authorization headers, CSRF risk is reduced but XSS risk must still be managed.

---

# CORS Requirements

Production CORS must allow only approved origins.

Examples:

```text
https://app.acadedx.com
https://admin.acadedx.com
https://www.acadedx.com
```

Do not allow wildcard origins in production.

---

# Rate Limiting

Authentication endpoints must be rate-limited.

Minimum MVP rate limits:

| Endpoint                         | Suggested Limit                                     |
| -------------------------------- | --------------------------------------------------- |
| `POST /auth/login`               | 10 attempts per 15 minutes per IP/email combination |
| `POST /auth/forgot-password`     | 5 attempts per hour per email/IP                    |
| `POST /auth/reset-password`      | 5 attempts per hour per token/IP                    |
| `POST /auth/resend-verification` | 5 attempts per hour per email/user                  |
| `POST /auth/register`            | Environment-specific, stricter if public            |

Rate-limit errors:

```text
RATE_LIMIT_LOGIN_EXCEEDED
RATE_LIMIT_PASSWORD_RESET_EXCEEDED
RATE_LIMIT_EXCEEDED
```

---

# Brute Force Protection

Login protection should include:

* IP-based rate limiting
* Email-based rate limiting
* Generic error messages
* Delayed responses where appropriate
* Security event logging
* Temporary lockout only if carefully designed

Avoid permanent account lockout from unauthenticated attempts unless admin recovery exists.

---

# Error Handling

Authentication errors must be safe.

Bad:

```json
{
  "success": false,
  "error": {
    "code": "USER_NOT_FOUND",
    "message": "No user exists with this email."
  }
}
```

Good:

```json
{
  "success": false,
  "error": {
    "code": "AUTH_INVALID_CREDENTIALS",
    "message": "Invalid email or password."
  }
}
```

Password reset must always return a generic success response.

---

# Authentication Error Codes

Required MVP codes:

```text
AUTH_REQUIRED
AUTH_INVALID_CREDENTIALS
AUTH_INVALID_TOKEN
AUTH_TOKEN_EXPIRED
AUTH_REFRESH_TOKEN_INVALID
AUTH_REFRESH_TOKEN_EXPIRED
AUTH_SESSION_EXPIRED
AUTH_SESSION_REVOKED
AUTH_EMAIL_NOT_VERIFIED
AUTH_ACCOUNT_DISABLED
AUTH_ACCOUNT_SUSPENDED
AUTH_PASSWORD_RESET_TOKEN_INVALID
AUTH_PASSWORD_RESET_TOKEN_EXPIRED
AUTH_EMAIL_VERIFICATION_TOKEN_INVALID
AUTH_EMAIL_VERIFICATION_TOKEN_EXPIRED
AUTH_TOO_MANY_ATTEMPTS
```

Error definitions are maintained in:

```text
docs/06-API/ERROR_CODES.md
```

---

# Audit and Security Events

Authentication should record security events for:

* Login success
* Login failure above threshold
* Logout
* Logout all sessions
* Refresh token reuse
* Password change
* Password reset request
* Password reset completion
* Email verification completion
* Account disabled login attempt
* Suspicious session activity

Audit/security event fields:

```text
eventId
eventType
userId
emailHash
requestId
ipAddress
userAgent
sessionId
status
reason
createdAt
```

Do not store plaintext passwords, tokens, or reset tokens.

---

# Logging Rules

Authentication logs may include:

```text
requestId
eventType
userId where known
emailHash where useful
ipAddress
userAgent
status
errorCode
durationMs
```

Authentication logs must not include:

```text
password
accessToken
refreshToken
resetToken
verificationToken
OTP
raw authorization header
private keys
```

---

# Account Enumeration Protection

Avoid revealing account existence in:

* Login
* Forgot password
* Resend verification
* Invitation lookup
* Email change
* Public registration

Use generic responses where needed.

Example:

```text
If the email exists, instructions have been sent.
```

---

# Invitation Token Security

Invitation tokens must be:

* Random
* Unguessable
* Stored hashed
* Single-use
* Expiring
* Scoped to intended email and role
* Scoped to Organization and Institution where applicable

Invitation acceptance must validate:

* Token is valid.
* Token is not expired.
* Token is not already accepted.
* Token is not revoked.
* Email matches or is verified.
* Role assignment is still permitted.
* Organization and Institution are active.

---

# Email Change Security

Email change is a sensitive action.

If implemented, it must require:

* Authentication
* Re-authentication
* Verification of new email
* Audit event
* Optional notification to old email
* Session review or revocation

MVP status:

```text
Deferred unless required
```

---

# MFA Readiness

MFA is not mandatory for MVP, but backend authentication must leave room for it.

Future MFA concepts:

```text
mfaFactor
mfaChallenge
mfaRecoveryCode
mfaVerifiedAt
```

MFA should be prioritized for:

* Platform Admin
* Organization Owner
* Organization Admin
* Institution Admin
* Finance Admin
* Security Admin

Authentication flow should support a future intermediate state:

```text
PASSWORD_VERIFIED_MFA_REQUIRED
```

---

# Enterprise SSO Readiness

Enterprise SSO may be future scope.

Future SSO concepts:

```text
identityProvider
externalSubjectId
samlConnection
oidcConnection
domainVerification
jitProvisioning
scimProvisioning
```

Do not design authentication in a way that requires password-only users forever.

A user may eventually authenticate through:

* Password
* Magic link
* OAuth/OIDC
* SAML
* Passkey
* Enterprise SSO

---

# Service-to-Service Authentication

Internal service authentication is not a major MVP concern in a modular monolith.

If services are extracted later, service-to-service calls must use:

* mTLS, signed service tokens, or private network identity
* Least privilege service identities
* Request signing where appropriate
* Auditable service actions
* Tenant context propagation

System service actions must not bypass tenant isolation.

---

# Background Job Authentication Context

Background jobs do not use user access tokens directly.

When creating a job, persist:

```text
jobId
requestedByUserId
organizationId
institutionId
requiredPermission
resourceType
resourceId
createdAt
```

Worker execution must:

* Use job context.
* Revalidate tenant scope before writing results.
* Avoid storing tokens in job payloads.
* Audit sensitive results.

---

# AI Workflow Authentication Context

AI workflows must use authenticated user context.

Before AI processing:

* Validate user identity.
* Validate membership.
* Validate Organization scope.
* Validate Institution scope.
* Validate resource access.
* Validate entitlement.
* Validate usage limit.

Do not allow unauthenticated AI workflows except explicitly public marketing demos with no user or tenant data.

---

# File Access Authentication Context

File download and signed URL generation must validate authentication.

Rules:

* File metadata must include ownership and tenant scope.
* User must have access to the related entity.
* Signed URL generation must happen only after authorization passes.
* Signed URLs must expire.
* Raw storage paths must not be exposed.

---

# Admin Authentication Requirements

Admin routes must require strong authentication.

For MVP:

* JWT authentication
* Active user check
* Platform or scoped admin role
* Audit logging for sensitive actions

Future requirements:

* MFA
* Re-authentication for high-risk actions
* IP allowlist for platform admin, if needed
* Device trust, if needed

---

# Implementation Checklist

Backend authentication implementation must include:

```text
AuthModule
AuthController
AuthService
TokenService
PasswordService
SessionService
JwtAuthGuard
RefreshTokenGuard
ActiveUserGuard
JWT strategy
Refresh token strategy
Access token generation
Refresh token generation
Refresh token hashing
Refresh token rotation
Logout
Password reset
Email verification
Request context resolver
Authentication error mapping
Authentication rate limits
Authentication tests
```

---

# Database Entities

Authentication requires or interacts with:

```text
User
Session
PasswordResetToken
EmailVerificationToken
Invitation
Membership
AuditLog
SecurityEvent
```

Recommended rule:

* Store raw secrets only long enough to send or return them once.
* Store token hashes in database.
* Store timestamps for expiry and revocation.
* Use soft delete or status fields where auditability is needed.

---

# Testing Requirements

Authentication tests must cover:

## Login

* Successful login
* Invalid email
* Invalid password
* Disabled account
* Suspended account
* Unverified email, if enforced
* Rate limit exceeded

## Token Validation

* Valid access token
* Missing access token
* Invalid access token
* Expired access token
* Wrong token type
* Revoked session behavior, if checked

## Refresh Token

* Successful refresh
* Expired refresh token
* Invalid refresh token
* Revoked session
* Token reuse after rotation
* Session expiry

## Logout

* Logout current session
* Logout with invalid token
* Logout all sessions, if enabled

## Password Reset

* Request reset with existing email
* Request reset with unknown email returns generic response
* Reset with valid token
* Reset with expired token
* Reset with reused token
* Password policy failure

## Email Verification

* Verify with valid token
* Verify with expired token
* Verify with reused token
* Resend verification rate limit

## Invitation Acceptance

* Accept valid invitation
* Expired invitation
* Revoked invitation
* Already accepted invitation
* Invalid role or inactive Institution

## Context Resolution

* User with one membership
* User with multiple memberships
* Inactive membership
* Organization mismatch
* Institution mismatch

---

# Security Review Checklist

Before approving authentication implementation, confirm:

* Passwords are hashed with strong algorithm.
* Tokens do not contain sensitive personal data.
* Access tokens are short-lived.
* Refresh tokens are hashed at rest.
* Refresh tokens rotate on use.
* Logout revokes session.
* Password reset does not reveal account existence.
* Email verification tokens are hashed and expiring.
* Invitation tokens are hashed and expiring.
* Auth endpoints are rate-limited.
* Disabled and suspended users cannot log in.
* Request context supports multiple memberships.
* Authentication does not hardcode single Institution access.
* Organization and Institution access is resolved through memberships.
* Logs do not include secrets.
* Security events are recorded.
* Tests cover negative cases.

---

# MVP Authentication Requirements

The MVP must implement at minimum:

```text
Email/password login
Password hashing
JWT access tokens
Refresh tokens
Refresh token hashing
Refresh token rotation
Logout
Current user endpoint
Invitation-based onboarding
Email verification
Password reset
Active user status checks
Request context resolver
Membership-aware context
Authentication rate limits
Safe authentication errors
Authentication security events
Authentication tests
```

MVP may defer:

```text
MFA
Enterprise SSO
Passkeys
Magic links
Device trust
Full session management UI
IP allowlisting
Risk-based authentication
SCIM provisioning
```

Do not design the MVP in a way that blocks these future capabilities.

---

# Related Documents

* docs/06-API/AUTHENTICATION.md
* docs/06-API/API_GUIDELINES.md
* docs/06-API/ERROR_CODES.md
* docs/09-Backend/MODULES.md
* docs/09-Backend/SERVICES.md
* docs/09-Backend/BACKGROUND_JOBS.md
* docs/11-Security/SECURITY.md
* docs/11-Security/RBAC.md
* docs/11-Security/PRIVACY.md
* docs/05-Database/DATABASE_SCHEMA.md
* docs/04-Architecture/CODING_STANDARDS.md

