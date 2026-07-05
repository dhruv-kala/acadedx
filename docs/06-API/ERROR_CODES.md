# API Error Codes

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the standard API error codes for Acadedx.

Error codes must be stable, machine-readable, predictable, and consistent across all backend services.

This document supports:

- Frontend error handling
- Mobile and desktop client behavior
- Backend service consistency
- AI workflow failures
- QA test planning
- API documentation
- Observability and incident debugging
- Future public API compatibility

This document must be read together with:

- API_GUIDELINES.md
- AUTHENTICATION.md
- ENDPOINTS.md
- SECURITY.md
- RBAC.md
- PRIVACY.md

---

# Error Response Format

All API errors must follow this structure:

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message.",
    "details": []
  }
}
````

Optional extended form:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Invalid request.",
    "details": [
      {
        "field": "email",
        "message": "Email is required.",
        "rule": "required"
      }
    ],
    "requestId": "req_123"
  }
}
```

---

# Error Code Principles

Error codes must follow these rules:

1. Use `UPPER_SNAKE_CASE`.
2. Be stable across releases.
3. Be machine-readable.
4. Be specific enough for client handling.
5. Avoid exposing sensitive internal details.
6. Avoid exposing database, infrastructure, provider, or stack traces.
7. Include a safe human-readable message.
8. Include structured details where helpful.
9. Use consistent HTTP status codes.
10. Support Organization and Institution tenant scoping.

---

# HTTP Status Code Mapping

| HTTP Status | Meaning               | Typical Use                                       |
| ----------- | --------------------- | ------------------------------------------------- |
| 400         | Bad Request           | Malformed request or invalid request shape        |
| 401         | Unauthorized          | Authentication missing or invalid                 |
| 403         | Forbidden             | Authenticated but not allowed                     |
| 404         | Not Found             | Resource does not exist or is not visible to user |
| 409         | Conflict              | Duplicate or conflicting state                    |
| 410         | Gone                  | Resource permanently unavailable                  |
| 422         | Unprocessable Entity  | Validation failed                                 |
| 423         | Locked                | Resource locked or restricted                     |
| 429         | Too Many Requests     | Rate limit exceeded                               |
| 500         | Internal Server Error | Unexpected server failure                         |
| 502         | Bad Gateway           | Upstream provider failed                          |
| 503         | Service Unavailable   | Dependency unavailable or maintenance             |
| 504         | Gateway Timeout       | Upstream provider timeout                         |

---

# Error Code Namespaces

Error codes should use clear prefixes where helpful.

| Prefix          | Area                               |
| --------------- | ---------------------------------- |
| `AUTH_`         | Authentication                     |
| `USER_`         | User profile and account           |
| `ORG_`          | Organization                       |
| `INST_`         | Institution                        |
| `TENANT_`       | Organization / Institution scoping |
| `RBAC_`         | Roles and permissions              |
| `ENTITLEMENT_`  | Feature access                     |
| `VALIDATION_`   | Request validation                 |
| `RESOURCE_`     | Generic resources                  |
| `ACADEMIC_`     | Academic structure                 |
| `LEARNER_`      | Learner records                    |
| `GUARDIAN_`     | Guardian relationships             |
| `TEACHER_`      | Teacher records and assignments    |
| `ATTENDANCE_`   | Attendance                         |
| `ASSESSMENT_`   | Assessments and results            |
| `FEE_`          | Fee management                     |
| `FILE_`         | File upload and storage            |
| `AI_`           | AI workflows                       |
| `OCR_`          | OCR workflows                      |
| `NOTIFICATION_` | Notifications                      |
| `PAYMENT_`      | Payments                           |
| `SUBSCRIPTION_` | Subscriptions                      |
| `WEBHOOK_`      | Webhooks                           |
| `RATE_LIMIT_`   | Rate limiting                      |
| `SYSTEM_`       | Platform or service failures       |

---

# Generic Errors

| Code                      | HTTP Status | Message                                            |
| ------------------------- | ----------: | -------------------------------------------------- |
| `BAD_REQUEST`             |         400 | Invalid request.                                   |
| `INVALID_REQUEST_BODY`    |         400 | Request body is invalid.                           |
| `INVALID_QUERY_PARAMETER` |         400 | One or more query parameters are invalid.          |
| `INVALID_PATH_PARAMETER`  |         400 | One or more path parameters are invalid.           |
| `MISSING_REQUIRED_FIELD`  |         422 | A required field is missing.                       |
| `VALIDATION_ERROR`        |         422 | Request validation failed.                         |
| `RESOURCE_NOT_FOUND`      |         404 | The requested resource was not found.              |
| `RESOURCE_ALREADY_EXISTS` |         409 | The resource already exists.                       |
| `RESOURCE_CONFLICT`       |         409 | The resource conflicts with the current state.     |
| `RESOURCE_LOCKED`         |         423 | The resource is locked.                            |
| `RESOURCE_GONE`           |         410 | The resource is no longer available.               |
| `OPERATION_NOT_ALLOWED`   |         403 | This operation is not allowed.                     |
| `UNSUPPORTED_OPERATION`   |         400 | This operation is not supported.                   |
| `METHOD_NOT_ALLOWED`      |         405 | This HTTP method is not allowed for this resource. |
| `INTERNAL_SERVER_ERROR`   |         500 | An unexpected error occurred.                      |
| `SERVICE_UNAVAILABLE`     |         503 | Service is temporarily unavailable.                |

---

# Authentication Errors

| Code                                    | HTTP Status | Message                                      |
| --------------------------------------- | ----------: | -------------------------------------------- |
| `AUTH_REQUIRED`                         |         401 | Authentication is required.                  |
| `AUTH_INVALID_CREDENTIALS`              |         401 | Invalid email or password.                   |
| `AUTH_INVALID_TOKEN`                    |         401 | Authentication token is invalid.             |
| `AUTH_TOKEN_EXPIRED`                    |         401 | Authentication token has expired.            |
| `AUTH_REFRESH_TOKEN_INVALID`            |         401 | Refresh token is invalid.                    |
| `AUTH_REFRESH_TOKEN_EXPIRED`            |         401 | Refresh token has expired.                   |
| `AUTH_SESSION_EXPIRED`                  |         401 | Session has expired.                         |
| `AUTH_SESSION_REVOKED`                  |         401 | Session has been revoked.                    |
| `AUTH_EMAIL_NOT_VERIFIED`               |         403 | Email verification is required.              |
| `AUTH_ACCOUNT_DISABLED`                 |         403 | Account is disabled.                         |
| `AUTH_ACCOUNT_SUSPENDED`                |         403 | Account is suspended.                        |
| `AUTH_PASSWORD_RESET_TOKEN_INVALID`     |         400 | Password reset token is invalid.             |
| `AUTH_PASSWORD_RESET_TOKEN_EXPIRED`     |         400 | Password reset token has expired.            |
| `AUTH_EMAIL_VERIFICATION_TOKEN_INVALID` |         400 | Email verification token is invalid.         |
| `AUTH_EMAIL_VERIFICATION_TOKEN_EXPIRED` |         400 | Email verification token has expired.        |
| `AUTH_MFA_REQUIRED`                     |         401 | Multi-factor authentication is required.     |
| `AUTH_MFA_INVALID_CODE`                 |         401 | Multi-factor authentication code is invalid. |
| `AUTH_TOO_MANY_ATTEMPTS`                |         429 | Too many authentication attempts.            |

---

# Authorization and RBAC Errors

| Code                                  | HTTP Status | Message                                            |
| ------------------------------------- | ----------: | -------------------------------------------------- |
| `AUTH_FORBIDDEN`                      |         403 | You do not have permission to perform this action. |
| `RBAC_ROLE_REQUIRED`                  |         403 | Required role is missing.                          |
| `RBAC_PERMISSION_REQUIRED`            |         403 | Required permission is missing.                    |
| `RBAC_INVALID_ROLE`                   |         400 | Role is invalid.                                   |
| `RBAC_INVALID_PERMISSION`             |         400 | Permission is invalid.                             |
| `RBAC_ROLE_ASSIGNMENT_NOT_ALLOWED`    |         403 | You cannot assign this role.                       |
| `RBAC_ROLE_REMOVAL_NOT_ALLOWED`       |         403 | You cannot remove this role.                       |
| `RBAC_ROLE_SCOPE_INVALID`             |         400 | Role scope is invalid.                             |
| `RBAC_SELF_ROLE_CHANGE_NOT_ALLOWED`   |         403 | You cannot change your own role in this context.   |
| `RBAC_LAST_ADMIN_REMOVAL_NOT_ALLOWED` |         409 | At least one administrator must remain.            |
| `RBAC_PLATFORM_ADMIN_REQUIRED`        |         403 | Platform administrator access is required.         |
| `RBAC_ORGANIZATION_ADMIN_REQUIRED`    |         403 | Organization administrator access is required.     |
| `RBAC_INSTITUTION_ADMIN_REQUIRED`     |         403 | Institution administrator access is required.      |

---

# Tenant Scope Errors

Tenant scoping protects Organization and Institution data boundaries.

| Code                                      | HTTP Status | Message                                                    |
| ----------------------------------------- | ----------: | ---------------------------------------------------------- |
| `TENANT_SCOPE_REQUIRED`                   |         400 | Tenant scope is required.                                  |
| `TENANT_SCOPE_INVALID`                    |         400 | Tenant scope is invalid.                                   |
| `TENANT_SCOPE_VIOLATION`                  |         403 | Requested resource is outside your permitted scope.        |
| `TENANT_ORGANIZATION_REQUIRED`            |         400 | Organization context is required.                          |
| `TENANT_INSTITUTION_REQUIRED`             |         400 | Institution context is required.                           |
| `TENANT_ORGANIZATION_MISMATCH`            |         403 | Resource does not belong to the specified organization.    |
| `TENANT_INSTITUTION_MISMATCH`             |         403 | Resource does not belong to the specified institution.     |
| `TENANT_INSTITUTION_NOT_IN_ORGANIZATION`  |         403 | Institution does not belong to the specified organization. |
| `TENANT_CROSS_ORGANIZATION_ACCESS_DENIED` |         403 | Cross-organization access is not allowed.                  |
| `TENANT_CROSS_INSTITUTION_ACCESS_DENIED`  |         403 | Cross-institution access is not allowed.                   |
| `TENANT_MEMBERSHIP_REQUIRED`              |         403 | Active tenant membership is required.                      |
| `TENANT_MEMBERSHIP_INACTIVE`              |         403 | Tenant membership is inactive.                             |
| `TENANT_CONTEXT_UNRESOLVED`               |         400 | Request tenant context could not be resolved.              |

---

# Organization Errors

| Code                          | HTTP Status | Message                               |
| ----------------------------- | ----------: | ------------------------------------- |
| `ORG_NOT_FOUND`               |         404 | Organization was not found.           |
| `ORG_ALREADY_EXISTS`          |         409 | Organization already exists.          |
| `ORG_INACTIVE`                |         403 | Organization is inactive.             |
| `ORG_SUSPENDED`               |         403 | Organization is suspended.            |
| `ORG_NAME_REQUIRED`           |         422 | Organization name is required.        |
| `ORG_INVALID_TYPE`            |         422 | Organization type is invalid.         |
| `ORG_INVALID_STATUS`          |         422 | Organization status is invalid.       |
| `ORG_CREATION_NOT_ALLOWED`    |         403 | Organization creation is not allowed. |
| `ORG_UPDATE_NOT_ALLOWED`      |         403 | Organization update is not allowed.   |
| `ORG_DELETE_NOT_ALLOWED`      |         403 | Organization deletion is not allowed. |
| `ORG_HAS_ACTIVE_INSTITUTIONS` |         409 | Organization has active institutions. |
| `ORG_LIMIT_EXCEEDED`          |         409 | Organization limit exceeded.          |

---

# Institution Errors

| Code                                      | HTTP Status | Message                                               |
| ----------------------------------------- | ----------: | ----------------------------------------------------- |
| `INST_NOT_FOUND`                          |         404 | Institution was not found.                            |
| `INST_ALREADY_EXISTS`                     |         409 | Institution already exists.                           |
| `INST_INACTIVE`                           |         403 | Institution is inactive.                              |
| `INST_SUSPENDED`                          |         403 | Institution is suspended.                             |
| `INST_NAME_REQUIRED`                      |         422 | Institution name is required.                         |
| `INST_INVALID_TYPE`                       |         422 | Institution type is invalid.                          |
| `INST_INVALID_STATUS`                     |         422 | Institution status is invalid.                        |
| `INST_INVALID_TIMEZONE`                   |         422 | Institution timezone is invalid.                      |
| `INST_INVALID_ACADEMIC_YEAR_START_MONTH`  |         422 | Academic year start month is invalid.                 |
| `INST_CREATION_NOT_ALLOWED`               |         403 | Institution creation is not allowed.                  |
| `INST_UPDATE_NOT_ALLOWED`                 |         403 | Institution update is not allowed.                    |
| `INST_DELETE_NOT_ALLOWED`                 |         403 | Institution deletion is not allowed.                  |
| `INST_MVP_SINGLE_INSTITUTION_LIMIT`       |         409 | MVP supports only one institution per organization.   |
| `INST_MULTI_INSTITUTION_FEATURE_DISABLED` |         403 | Multi-institution support is not enabled.             |
| `INST_CODE_ALREADY_EXISTS`                |         409 | Institution code already exists in this organization. |

---

# User and Membership Errors

| Code                                     | HTTP Status | Message                                               |
| ---------------------------------------- | ----------: | ----------------------------------------------------- |
| `USER_NOT_FOUND`                         |         404 | User was not found.                                   |
| `USER_ALREADY_EXISTS`                    |         409 | User already exists.                                  |
| `USER_EMAIL_ALREADY_EXISTS`              |         409 | Email is already registered.                          |
| `USER_PHONE_ALREADY_EXISTS`              |         409 | Phone number is already registered.                   |
| `USER_INACTIVE`                          |         403 | User is inactive.                                     |
| `USER_SUSPENDED`                         |         403 | User is suspended.                                    |
| `USER_PROFILE_INCOMPLETE`                |         422 | User profile is incomplete.                           |
| `USER_INVALID_STATUS`                    |         422 | User status is invalid.                               |
| `USER_SELF_DELETE_NOT_ALLOWED`           |         403 | You cannot delete your own account from this context. |
| `MEMBERSHIP_NOT_FOUND`                   |         404 | Membership was not found.                             |
| `MEMBERSHIP_ALREADY_EXISTS`              |         409 | Membership already exists.                            |
| `MEMBERSHIP_INACTIVE`                    |         403 | Membership is inactive.                               |
| `MEMBERSHIP_INVALID_SCOPE`               |         422 | Membership scope is invalid.                          |
| `MEMBERSHIP_INVALID_ROLE`                |         422 | Membership role is invalid.                           |
| `MEMBERSHIP_INVITATION_NOT_FOUND`        |         404 | Invitation was not found.                             |
| `MEMBERSHIP_INVITATION_EXPIRED`          |         400 | Invitation has expired.                               |
| `MEMBERSHIP_INVITATION_ALREADY_ACCEPTED` |         409 | Invitation has already been accepted.                 |
| `MEMBERSHIP_INVITATION_ALREADY_REVOKED`  |         409 | Invitation has been revoked.                          |

---

# Academic Structure Errors

Academic structure resources are Institution-scoped.

| Code                               | HTTP Status | Message                                                |
| ---------------------------------- | ----------: | ------------------------------------------------------ |
| `ACADEMIC_YEAR_NOT_FOUND`          |         404 | Academic year was not found.                           |
| `ACADEMIC_YEAR_ALREADY_EXISTS`     |         409 | Academic year already exists.                          |
| `ACADEMIC_YEAR_INVALID_DATE_RANGE` |         422 | Academic year date range is invalid.                   |
| `ACADEMIC_YEAR_OVERLAP`            |         409 | Academic year overlaps with an existing academic year. |
| `ACADEMIC_YEAR_INACTIVE`           |         403 | Academic year is inactive.                             |
| `CLASS_NOT_FOUND`                  |         404 | Class was not found.                                   |
| `CLASS_ALREADY_EXISTS`             |         409 | Class already exists.                                  |
| `CLASS_INVALID_STATUS`             |         422 | Class status is invalid.                               |
| `CLASS_HAS_ACTIVE_SECTIONS`        |         409 | Class has active sections.                             |
| `SECTION_NOT_FOUND`                |         404 | Section was not found.                                 |
| `SECTION_ALREADY_EXISTS`           |         409 | Section already exists.                                |
| `SECTION_CAPACITY_EXCEEDED`        |         409 | Section capacity exceeded.                             |
| `SECTION_INVALID_STATUS`           |         422 | Section status is invalid.                             |
| `SUBJECT_NOT_FOUND`                |         404 | Subject was not found.                                 |
| `SUBJECT_ALREADY_EXISTS`           |         409 | Subject already exists.                                |
| `SUBJECT_INVALID_STATUS`           |         422 | Subject status is invalid.                             |
| `SUBJECT_NOT_ASSIGNED`             |         404 | Subject assignment was not found.                      |
| `TIMETABLE_ENTRY_NOT_FOUND`        |         404 | Timetable entry was not found.                         |
| `TIMETABLE_CONFLICT`               |         409 | Timetable entry conflicts with an existing entry.      |
| `TIMETABLE_INVALID_TIME_RANGE`     |         422 | Timetable time range is invalid.                       |

---

# Learner Errors

Use `learner` in backend, database, and API terminology.

A school UI may display this concept as Student, but the API should avoid `student` as a core backend entity.

| Code                                    | HTTP Status | Message                                              |
| --------------------------------------- | ----------: | ---------------------------------------------------- |
| `LEARNER_NOT_FOUND`                     |         404 | Learner was not found.                               |
| `LEARNER_ALREADY_EXISTS`                |         409 | Learner already exists.                              |
| `LEARNER_ADMISSION_NUMBER_EXISTS`       |         409 | Admission number already exists in this institution. |
| `LEARNER_INACTIVE`                      |         403 | Learner is inactive.                                 |
| `LEARNER_ARCHIVED`                      |         410 | Learner has been archived.                           |
| `LEARNER_INVALID_STATUS`                |         422 | Learner status is invalid.                           |
| `LEARNER_INVALID_CLASS_ASSIGNMENT`      |         422 | Learner class assignment is invalid.                 |
| `LEARNER_CLASS_ASSIGNMENT_NOT_FOUND`    |         404 | Learner class assignment was not found.              |
| `LEARNER_TRANSFER_NOT_ALLOWED`          |         403 | Learner transfer is not allowed.                     |
| `LEARNER_TRANSFER_NOT_SUPPORTED_IN_MVP` |         403 | Learner transfer is not supported in MVP.            |
| `LEARNER_PROFILE_INCOMPLETE`            |         422 | Learner profile is incomplete.                       |
| `LEARNER_ACCESS_DENIED`                 |         403 | You do not have access to this learner.              |

---

# Guardian Errors

Use `guardian` in backend, database, and API terminology.

A school UI may display this concept as Parent.

| Code                                   | HTTP Status | Message                                                 |
| -------------------------------------- | ----------: | ------------------------------------------------------- |
| `GUARDIAN_NOT_FOUND`                   |         404 | Guardian was not found.                                 |
| `GUARDIAN_ALREADY_EXISTS`              |         409 | Guardian already exists.                                |
| `GUARDIAN_INACTIVE`                    |         403 | Guardian is inactive.                                   |
| `GUARDIAN_INVALID_RELATIONSHIP`        |         422 | Guardian relationship is invalid.                       |
| `GUARDIAN_LEARNER_LINK_NOT_FOUND`      |         404 | Guardian-learner link was not found.                    |
| `GUARDIAN_LEARNER_LINK_ALREADY_EXISTS` |         409 | Guardian is already linked to this learner.             |
| `GUARDIAN_PRIMARY_REQUIRED`            |         422 | At least one primary guardian is required.              |
| `GUARDIAN_PRIMARY_REMOVAL_NOT_ALLOWED` |         409 | Primary guardian cannot be removed without replacement. |
| `GUARDIAN_ACCESS_DENIED`               |         403 | You do not have access to this guardian relationship.   |

---

# Teacher Errors

| Code                                | HTTP Status | Message                                                   |
| ----------------------------------- | ----------: | --------------------------------------------------------- |
| `TEACHER_NOT_FOUND`                 |         404 | Teacher was not found.                                    |
| `TEACHER_PROFILE_NOT_FOUND`         |         404 | Teacher profile was not found.                            |
| `TEACHER_ALREADY_EXISTS`            |         409 | Teacher profile already exists.                           |
| `TEACHER_INACTIVE`                  |         403 | Teacher is inactive.                                      |
| `TEACHER_EMPLOYEE_CODE_EXISTS`      |         409 | Employee code already exists in this institution.         |
| `TEACHER_ASSIGNMENT_NOT_FOUND`      |         404 | Teacher assignment was not found.                         |
| `TEACHER_ASSIGNMENT_ALREADY_EXISTS` |         409 | Teacher assignment already exists.                        |
| `TEACHER_ASSIGNMENT_CONFLICT`       |         409 | Teacher assignment conflicts with an existing assignment. |
| `TEACHER_ACCESS_DENIED`             |         403 | You do not have access to this teacher resource.          |

---

# Attendance Errors

| Code                                | HTTP Status | Message                                |
| ----------------------------------- | ----------: | -------------------------------------- |
| `ATTENDANCE_SESSION_NOT_FOUND`      |         404 | Attendance session was not found.      |
| `ATTENDANCE_SESSION_ALREADY_EXISTS` |         409 | Attendance session already exists.     |
| `ATTENDANCE_SESSION_LOCKED`         |         423 | Attendance session is locked.          |
| `ATTENDANCE_SESSION_DATE_INVALID`   |         422 | Attendance session date is invalid.    |
| `ATTENDANCE_RECORD_NOT_FOUND`       |         404 | Attendance record was not found.       |
| `ATTENDANCE_RECORD_ALREADY_EXISTS`  |         409 | Attendance record already exists.      |
| `ATTENDANCE_RECORD_INVALID_STATUS`  |         422 | Attendance status is invalid.          |
| `ATTENDANCE_SUBMISSION_EMPTY`       |         422 | Attendance submission cannot be empty. |
| `ATTENDANCE_UPDATE_NOT_ALLOWED`     |         403 | Attendance update is not allowed.      |

---

# Assessment Errors

| Code                                   | HTTP Status | Message                                       |
| -------------------------------------- | ----------: | --------------------------------------------- |
| `ASSESSMENT_NOT_FOUND`                 |         404 | Assessment was not found.                     |
| `ASSESSMENT_ALREADY_EXISTS`            |         409 | Assessment already exists.                    |
| `ASSESSMENT_INVALID_TYPE`              |         422 | Assessment type is invalid.                   |
| `ASSESSMENT_INVALID_DATE`              |         422 | Assessment date is invalid.                   |
| `ASSESSMENT_INVALID_MAX_MARKS`         |         422 | Maximum marks value is invalid.               |
| `ASSESSMENT_LOCKED`                    |         423 | Assessment is locked.                         |
| `ASSESSMENT_RESULT_NOT_FOUND`          |         404 | Assessment result was not found.              |
| `ASSESSMENT_RESULT_ALREADY_EXISTS`     |         409 | Assessment result already exists.             |
| `ASSESSMENT_RESULT_INVALID_MARKS`      |         422 | Assessment result marks are invalid.          |
| `ASSESSMENT_RESULT_UPDATE_NOT_ALLOWED` |         403 | Assessment result update is not allowed.      |
| `ASSESSMENT_RESULT_SUBMISSION_EMPTY`   |         422 | Assessment result submission cannot be empty. |

---

# Fee Errors

Fee management may be MVP, V1, or deferred depending on final product scope.

| Code                             | HTTP Status | Message                                |
| -------------------------------- | ----------: | -------------------------------------- |
| `FEE_CATEGORY_NOT_FOUND`         |         404 | Fee category was not found.            |
| `FEE_CATEGORY_ALREADY_EXISTS`    |         409 | Fee category already exists.           |
| `FEE_STRUCTURE_NOT_FOUND`        |         404 | Fee structure was not found.           |
| `FEE_STRUCTURE_ALREADY_EXISTS`   |         409 | Fee structure already exists.          |
| `FEE_ASSIGNMENT_NOT_FOUND`       |         404 | Fee assignment was not found.          |
| `FEE_ASSIGNMENT_ALREADY_EXISTS`  |         409 | Fee assignment already exists.         |
| `FEE_PAYMENT_NOT_FOUND`          |         404 | Fee payment was not found.             |
| `FEE_PAYMENT_ALREADY_RECORDED`   |         409 | Fee payment has already been recorded. |
| `FEE_INVALID_AMOUNT`             |         422 | Fee amount is invalid.                 |
| `FEE_INVALID_CURRENCY`           |         422 | Currency is invalid.                   |
| `FEE_INVALID_DUE_DATE`           |         422 | Due date is invalid.                   |
| `FEE_PAYMENT_UPDATE_NOT_ALLOWED` |         403 | Fee payment update is not allowed.     |
| `FEE_RECEIPT_GENERATION_FAILED`  |         500 | Fee receipt generation failed.         |

---

# Communication and Notification Errors

| Code                               | HTTP Status | Message                               |
| ---------------------------------- | ----------: | ------------------------------------- |
| `ANNOUNCEMENT_NOT_FOUND`           |         404 | Announcement was not found.           |
| `ANNOUNCEMENT_ALREADY_SENT`        |         409 | Announcement has already been sent.   |
| `ANNOUNCEMENT_INVALID_AUDIENCE`    |         422 | Announcement audience is invalid.     |
| `MESSAGE_NOT_FOUND`                |         404 | Message was not found.                |
| `MESSAGE_DELIVERY_FAILED`          |         503 | Message delivery failed.              |
| `MESSAGE_INVALID_RECIPIENT`        |         422 | Message recipient is invalid.         |
| `NOTIFICATION_NOT_FOUND`           |         404 | Notification was not found.           |
| `NOTIFICATION_DELIVERY_FAILED`     |         503 | Notification delivery failed.         |
| `NOTIFICATION_CHANNEL_UNAVAILABLE` |         503 | Notification channel is unavailable.  |
| `NOTIFICATION_PREFERENCES_INVALID` |         422 | Notification preferences are invalid. |

---

# File and Storage Errors

| Code                          | HTTP Status | Message                              |
| ----------------------------- | ----------: | ------------------------------------ |
| `FILE_NOT_FOUND`              |         404 | File was not found.                  |
| `FILE_UPLOAD_REQUIRED`        |         422 | File upload is required.             |
| `FILE_UPLOAD_FAILED`          |         500 | File upload failed.                  |
| `FILE_DELETE_FAILED`          |         500 | File deletion failed.                |
| `FILE_INVALID_TYPE`           |         422 | File type is not supported.          |
| `FILE_INVALID_EXTENSION`      |         422 | File extension is not supported.     |
| `FILE_TOO_LARGE`              |         413 | File size exceeds allowed limit.     |
| `FILE_EMPTY`                  |         422 | File is empty.                       |
| `FILE_STORAGE_QUOTA_EXCEEDED` |         409 | Storage quota exceeded.              |
| `FILE_SCAN_FAILED`            |         500 | File safety scan failed.             |
| `FILE_UNSAFE_CONTENT`         |         422 | File failed safety validation.       |
| `FILE_ACCESS_DENIED`          |         403 | You do not have access to this file. |
| `FILE_SIGNED_URL_FAILED`      |         500 | Signed URL generation failed.        |

---

# AI Workflow Errors

AI features must be treated as assistive, not authoritative.

| Code                             | HTTP Status | Message                               |
| -------------------------------- | ----------: | ------------------------------------- |
| `AI_REQUEST_INVALID`             |         422 | AI request is invalid.                |
| `AI_PROVIDER_ERROR`              |         502 | AI provider returned an error.        |
| `AI_PROVIDER_UNAVAILABLE`        |         503 | AI provider is unavailable.           |
| `AI_PROVIDER_TIMEOUT`            |         504 | AI provider request timed out.        |
| `AI_OUTPUT_EMPTY`                |         502 | AI provider returned an empty output. |
| `AI_OUTPUT_INVALID`              |         502 | AI output is invalid.                 |
| `AI_OUTPUT_UNSAFE`               |         422 | AI output failed safety checks.       |
| `AI_CONTEXT_TOO_LARGE`           |         422 | AI context exceeds allowed size.      |
| `AI_CONTEXT_MISSING`             |         422 | Required AI context is missing.       |
| `AI_MODEL_NOT_AVAILABLE`         |         503 | Requested AI model is not available.  |
| `AI_MODEL_SELECTION_FAILED`      |         500 | AI model selection failed.            |
| `AI_PROMPT_TEMPLATE_NOT_FOUND`   |         404 | AI prompt template was not found.     |
| `AI_PROMPT_RENDER_FAILED`        |         500 | AI prompt rendering failed.           |
| `AI_PERSONALIZATION_UNAVAILABLE` |         503 | AI personalization is unavailable.    |
| `AI_MODERATION_FAILED`           |         500 | AI moderation failed.                 |
| `AI_GENERATION_CANCELLED`        |         409 | AI generation was cancelled.          |
| `AI_GENERATION_IN_PROGRESS`      |         409 | AI generation is already in progress. |

---

# OCR Errors

| Code                        | HTTP Status | Message                                       |
| --------------------------- | ----------: | --------------------------------------------- |
| `OCR_JOB_NOT_FOUND`         |         404 | OCR job was not found.                        |
| `OCR_JOB_ALREADY_EXISTS`    |         409 | OCR job already exists.                       |
| `OCR_JOB_IN_PROGRESS`       |         409 | OCR job is already in progress.               |
| `OCR_JOB_FAILED`            |         500 | OCR job failed.                               |
| `OCR_JOB_CANCELLED`         |         409 | OCR job was cancelled.                        |
| `OCR_FILE_REQUIRED`         |         422 | OCR file is required.                         |
| `OCR_FILE_UNSUPPORTED`      |         422 | OCR file type is not supported.               |
| `OCR_TEXT_NOT_FOUND`        |         422 | No readable text was found.                   |
| `OCR_CONFIDENCE_LOW`        |         422 | OCR confidence is below acceptable threshold. |
| `OCR_CONFIRMATION_REQUIRED` |         409 | OCR result requires user confirmation.        |
| `OCR_PROVIDER_ERROR`        |         502 | OCR provider returned an error.               |
| `OCR_PROVIDER_UNAVAILABLE`  |         503 | OCR provider is unavailable.                  |
| `OCR_PROVIDER_TIMEOUT`      |         504 | OCR provider request timed out.               |

---

# Entitlement and Usage Errors

Entitlements must check feature access, not plan names.

| Code                           | HTTP Status | Message                                      |
| ------------------------------ | ----------: | -------------------------------------------- |
| `ENTITLEMENT_REQUIRED`         |         403 | This feature requires an active entitlement. |
| `ENTITLEMENT_NOT_FOUND`        |         404 | Entitlement was not found.                   |
| `ENTITLEMENT_INACTIVE`         |         403 | Entitlement is inactive.                     |
| `ENTITLEMENT_EXPIRED`          |         403 | Entitlement has expired.                     |
| `ENTITLEMENT_FEATURE_DISABLED` |         403 | This feature is disabled.                    |
| `ENTITLEMENT_PLAN_REQUIRED`    |         403 | This feature requires a different plan.      |
| `ENTITLEMENT_SCOPE_INVALID`    |         403 | Entitlement scope is invalid.                |
| `USAGE_LIMIT_EXCEEDED`         |         429 | Usage limit exceeded.                        |
| `USAGE_QUOTA_EXCEEDED`         |         429 | Usage quota exceeded.                        |
| `USAGE_COUNTER_UNAVAILABLE`    |         503 | Usage counter is unavailable.                |

---

# Subscription Errors

Subscription and commerce may be deferred if institution-first MVP does not require paid plan workflows at launch.

| Code                                 | HTTP Status | Message                                       |
| ------------------------------------ | ----------: | --------------------------------------------- |
| `SUBSCRIPTION_NOT_FOUND`             |         404 | Subscription was not found.                   |
| `SUBSCRIPTION_ALREADY_ACTIVE`        |         409 | Subscription is already active.               |
| `SUBSCRIPTION_INACTIVE`              |         403 | Subscription is inactive.                     |
| `SUBSCRIPTION_EXPIRED`               |         403 | Subscription has expired.                     |
| `SUBSCRIPTION_CANCELLED`             |         403 | Subscription is cancelled.                    |
| `SUBSCRIPTION_CHANGE_NOT_ALLOWED`    |         403 | Subscription change is not allowed.           |
| `SUBSCRIPTION_PLAN_NOT_FOUND`        |         404 | Subscription plan was not found.              |
| `SUBSCRIPTION_PLAN_INACTIVE`         |         403 | Subscription plan is inactive.                |
| `SUBSCRIPTION_INVALID_BILLING_CYCLE` |         422 | Billing cycle is invalid.                     |
| `SUBSCRIPTION_COUPON_INVALID`        |         422 | Coupon is invalid.                            |
| `SUBSCRIPTION_COUPON_EXPIRED`        |         422 | Coupon has expired.                           |
| `SUBSCRIPTION_PAYMENT_REQUIRED`      |         402 | Payment is required to activate subscription. |

---

# Payment Errors

| Code                           | HTTP Status | Message                                                 |
| ------------------------------ | ----------: | ------------------------------------------------------- |
| `PAYMENT_NOT_FOUND`            |         404 | Payment was not found.                                  |
| `PAYMENT_FAILED`               |         402 | Payment failed.                                         |
| `PAYMENT_DECLINED`             |         402 | Payment was declined.                                   |
| `PAYMENT_CANCELLED`            |         409 | Payment was cancelled.                                  |
| `PAYMENT_ALREADY_CONFIRMED`    |         409 | Payment has already been confirmed.                     |
| `PAYMENT_CONFIRMATION_FAILED`  |         502 | Payment confirmation failed.                            |
| `PAYMENT_REFUND_NOT_ALLOWED`   |         403 | Refund is not allowed.                                  |
| `PAYMENT_REFUND_FAILED`        |         502 | Refund failed.                                          |
| `PAYMENT_GATEWAY_ERROR`        |         502 | Payment gateway returned an error.                      |
| `PAYMENT_GATEWAY_UNAVAILABLE`  |         503 | Payment gateway is unavailable.                         |
| `PAYMENT_GATEWAY_TIMEOUT`      |         504 | Payment gateway request timed out.                      |
| `PAYMENT_INVALID_AMOUNT`       |         422 | Payment amount is invalid.                              |
| `PAYMENT_INVALID_CURRENCY`     |         422 | Payment currency is invalid.                            |
| `PAYMENT_IDEMPOTENCY_REQUIRED` |         400 | Idempotency key is required for this payment operation. |
| `PAYMENT_DUPLICATE_REQUEST`    |         409 | Duplicate payment request detected.                     |

---

# Webhook Errors

Webhook endpoints should not expose detailed failure reasons to external providers when doing so may leak internal state.

| Code                           | HTTP Status | Message                                   |
| ------------------------------ | ----------: | ----------------------------------------- |
| `WEBHOOK_SIGNATURE_MISSING`    |         401 | Webhook signature is missing.             |
| `WEBHOOK_SIGNATURE_INVALID`    |         401 | Webhook signature is invalid.             |
| `WEBHOOK_PROVIDER_UNSUPPORTED` |         400 | Webhook provider is not supported.        |
| `WEBHOOK_EVENT_UNSUPPORTED`    |         400 | Webhook event is not supported.           |
| `WEBHOOK_EVENT_DUPLICATE`      |         409 | Webhook event has already been processed. |
| `WEBHOOK_PAYLOAD_INVALID`      |         400 | Webhook payload is invalid.               |
| `WEBHOOK_PROCESSING_FAILED`    |         500 | Webhook processing failed.                |
| `WEBHOOK_IDEMPOTENCY_FAILED`   |         409 | Webhook idempotency check failed.         |

---

# Job and Async Processing Errors

| Code                       | HTTP Status | Message                     |
| -------------------------- | ----------: | --------------------------- |
| `JOB_NOT_FOUND`            |         404 | Job was not found.          |
| `JOB_ALREADY_EXISTS`       |         409 | Job already exists.         |
| `JOB_IN_PROGRESS`          |         409 | Job is already in progress. |
| `JOB_FAILED`               |         500 | Job failed.                 |
| `JOB_CANCELLED`            |         409 | Job was cancelled.          |
| `JOB_TIMEOUT`              |         504 | Job timed out.              |
| `JOB_RETRY_LIMIT_EXCEEDED` |         429 | Job retry limit exceeded.   |
| `JOB_RESULT_NOT_READY`     |         409 | Job result is not ready.    |
| `JOB_QUEUE_UNAVAILABLE`    |         503 | Job queue is unavailable.   |

---

# Rate Limit Errors

| Code                                 | HTTP Status | Message                           |
| ------------------------------------ | ----------: | --------------------------------- |
| `RATE_LIMIT_EXCEEDED`                |         429 | Too many requests.                |
| `RATE_LIMIT_LOGIN_EXCEEDED`          |         429 | Too many login attempts.          |
| `RATE_LIMIT_PASSWORD_RESET_EXCEEDED` |         429 | Too many password reset attempts. |
| `RATE_LIMIT_AI_EXCEEDED`             |         429 | Too many AI requests.             |
| `RATE_LIMIT_OCR_EXCEEDED`            |         429 | Too many OCR requests.            |
| `RATE_LIMIT_UPLOAD_EXCEEDED`         |         429 | Too many file upload requests.    |
| `RATE_LIMIT_ADMIN_EXCEEDED`          |         429 | Too many admin requests.          |

Rate-limited responses should include headers where possible:

```http
Retry-After: 60
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 0
X-RateLimit-Reset: 1783256400
```

---

# Security Errors

| Code                                           | HTTP Status | Message                                           |
| ---------------------------------------------- | ----------: | ------------------------------------------------- |
| `SECURITY_POLICY_VIOLATION`                    |         403 | Request violates security policy.                 |
| `SECURITY_CSRF_INVALID`                        |         403 | CSRF token is invalid.                            |
| `SECURITY_CORS_DENIED`                         |         403 | Origin is not allowed.                            |
| `SECURITY_IP_BLOCKED`                          |         403 | Request source is blocked.                        |
| `SECURITY_DEVICE_NOT_TRUSTED`                  |         403 | Device is not trusted.                            |
| `SECURITY_SESSION_RISK_DETECTED`               |         403 | Session risk detected.                            |
| `SECURITY_DATA_EXPORT_DENIED`                  |         403 | Data export is not allowed.                       |
| `SECURITY_IMPERSONATION_NOT_ALLOWED`           |         403 | User impersonation is not allowed.                |
| `SECURITY_SENSITIVE_OPERATION_REQUIRES_REAUTH` |         401 | Re-authentication is required for this operation. |

---

# Privacy and Data Protection Errors

| Code                                | HTTP Status | Message                                   |
| ----------------------------------- | ----------: | ----------------------------------------- |
| `PRIVACY_CONSENT_REQUIRED`          |         403 | Consent is required for this operation.   |
| `PRIVACY_CONSENT_WITHDRAWN`         |         403 | Consent has been withdrawn.               |
| `PRIVACY_DATA_ACCESS_DENIED`        |         403 | Access to this personal data is denied.   |
| `PRIVACY_DATA_EXPORT_NOT_FOUND`     |         404 | Data export was not found.                |
| `PRIVACY_DATA_EXPORT_IN_PROGRESS`   |         409 | Data export is already in progress.       |
| `PRIVACY_DATA_DELETION_NOT_ALLOWED` |         403 | Data deletion is not allowed.             |
| `PRIVACY_RETENTION_POLICY_BLOCKED`  |         403 | Retention policy prevents this operation. |
| `PRIVACY_MINOR_DATA_RESTRICTED`     |         403 | Minor learner data is restricted.         |

---

# External Provider Errors

| Code                        | HTTP Status | Message                                |
| --------------------------- | ----------: | -------------------------------------- |
| `PROVIDER_ERROR`            |         502 | External provider returned an error.   |
| `PROVIDER_UNAVAILABLE`      |         503 | External provider is unavailable.      |
| `PROVIDER_TIMEOUT`          |         504 | External provider request timed out.   |
| `PROVIDER_RESPONSE_INVALID` |         502 | External provider response is invalid. |
| `EMAIL_PROVIDER_ERROR`      |         502 | Email provider returned an error.      |
| `SMS_PROVIDER_ERROR`        |         502 | SMS provider returned an error.        |
| `STORAGE_PROVIDER_ERROR`    |         502 | Storage provider returned an error.    |
| `AI_PROVIDER_ERROR`         |         502 | AI provider returned an error.         |
| `PAYMENT_PROVIDER_ERROR`    |         502 | Payment provider returned an error.    |

---

# Database and Persistence Errors

These codes should not expose database implementation details to clients.

| Code                       | HTTP Status | Message                      |
| -------------------------- | ----------: | ---------------------------- |
| `DATA_PERSISTENCE_ERROR`   |         500 | Data could not be saved.     |
| `DATA_READ_ERROR`          |         500 | Data could not be read.      |
| `DATA_CONFLICT`            |         409 | Data conflict detected.      |
| `DATA_INTEGRITY_VIOLATION` |         409 | Data integrity violation.    |
| `DATA_TRANSACTION_FAILED`  |         500 | Data transaction failed.     |
| `DATA_UNAVAILABLE`         |         503 | Data service is unavailable. |

---

# Error Details Format

Validation errors should include field-level details.

Example:

```json
{
  "success": false,
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Request validation failed.",
    "details": [
      {
        "field": "institutionId",
        "message": "Institution ID must be a valid UUID.",
        "rule": "uuid"
      },
      {
        "field": "email",
        "message": "Email must be valid.",
        "rule": "email"
      }
    ]
  }
}
```

Authorization errors should not expose excessive internal details.

Acceptable:

```json
{
  "success": false,
  "error": {
    "code": "TENANT_SCOPE_VIOLATION",
    "message": "Requested resource is outside your permitted scope.",
    "details": []
  }
}
```

Avoid:

```json
{
  "success": false,
  "error": {
    "code": "TENANT_SCOPE_VIOLATION",
    "message": "Learner lrn_123 belongs to institution inst_999, but user only belongs to inst_123.",
    "details": []
  }
}
```

---

# Safe Error Message Rules

API error messages must not reveal:

* Password validity details beyond generic login failure
* Whether a private email exists during password reset
* Internal database table names
* Internal stack traces
* Provider credentials
* Tenant boundary details
* Sensitive learner information
* Sensitive guardian information
* Sensitive AI prompt content
* Payment provider secrets
* File storage paths
* Signed URL secrets

---

# Recommended Error Handling by Client

Client applications should handle errors by code first, not by message.

Bad:

```ts
if (error.message === "Token expired") {
  refreshToken();
}
```

Good:

```ts
if (error.code === "AUTH_TOKEN_EXPIRED") {
  refreshToken();
}
```

Messages may change.

Codes should remain stable.

---

# Retry Rules

Clients and backend services may retry only safe or explicitly idempotent operations.

| Error Code                | Retry?      | Notes                               |
| ------------------------- | ----------- | ----------------------------------- |
| `AI_PROVIDER_TIMEOUT`     | Yes         | Use exponential backoff.            |
| `AI_PROVIDER_UNAVAILABLE` | Yes         | Use exponential backoff.            |
| `OCR_PROVIDER_TIMEOUT`    | Yes         | Use exponential backoff.            |
| `PAYMENT_GATEWAY_TIMEOUT` | Conditional | Only if idempotency key is present. |
| `PROVIDER_TIMEOUT`        | Yes         | Use exponential backoff.            |
| `SERVICE_UNAVAILABLE`     | Yes         | Retry after delay.                  |
| `RATE_LIMIT_EXCEEDED`     | Yes         | Respect `Retry-After`.              |
| `VALIDATION_ERROR`        | No          | User or client must fix request.    |
| `AUTH_FORBIDDEN`          | No          | Permission must change first.       |
| `TENANT_SCOPE_VIOLATION`  | No          | Scope or access must change first.  |

---

# Logging Requirements

Every error log should include:

* `requestId`
* `errorCode`
* `httpStatus`
* `method`
* `path`
* `userId`, if authenticated
* `organizationId`, if available
* `institutionId`, if available
* `durationMs`
* safe diagnostic metadata

Error logs must not include:

* Passwords
* Tokens
* Refresh tokens
* OTPs
* Private keys
* Raw payment card data
* Sensitive learner data
* Sensitive guardian data
* Sensitive AI prompts or conversations unless explicitly approved and protected

---

# Audit Requirements

The following error conditions should create or enrich audit records:

* Repeated authentication failures
* Account suspension access attempts
* Tenant scope violations
* Unauthorized role changes
* Unauthorized data export attempts
* Failed sensitive settings changes
* Failed payment refund attempts
* Failed webhook signature validation
* Failed admin impersonation attempts
* Suspicious rate-limit violations

---

# OpenAPI Requirements

Every documented endpoint must define possible error responses in OpenAPI.

Minimum required error responses:

```text
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
422 Validation Error
429 Too Many Requests
500 Internal Server Error
```

Endpoint-specific errors must also be documented.

Example:

```yaml
responses:
  "403":
    description: Forbidden
    content:
      application/json:
        schema:
          $ref: "#/components/schemas/ErrorResponse"
        examples:
          tenantScopeViolation:
            value:
              success: false
              error:
                code: TENANT_SCOPE_VIOLATION
                message: Requested resource is outside your permitted scope.
                details: []
```

---

# Error Code Lifecycle

Error codes are part of the API contract.

## Adding Error Codes

New error codes may be added when:

* A new feature is introduced.
* Client behavior needs to distinguish a specific failure.
* Observability requires precise classification.
* A provider integration introduces meaningful failure modes.

## Changing Error Codes

Do not rename or repurpose an error code without versioning.

Changing an error code is a breaking change.

## Deprecating Error Codes

Deprecated codes should remain documented until the next major API version.

Deprecated codes should include a replacement where possible.

Example:

| Deprecated Code     | Replacement          |
| ------------------- | -------------------- |
| `STUDENT_NOT_FOUND` | `LEARNER_NOT_FOUND`  |
| `PARENT_NOT_FOUND`  | `GUARDIAN_NOT_FOUND` |
| `SCHOOL_NOT_FOUND`  | `INST_NOT_FOUND`     |

---

# Legacy Terminology Mapping

Acadedx backend and API terminology must use Organization and Institution architecture.

Legacy or UI-facing terms must be mapped carefully.

| Legacy / UI Term | API Term      | Preferred Error Code |
| ---------------- | ------------- | -------------------- |
| School           | Institution   | `INST_*`             |
| Student          | Learner       | `LEARNER_*`          |
| Parent           | Guardian      | `GUARDIAN_*`         |
| Class / Grade    | Class         | `CLASS_*`            |
| Academic Session | Academic Year | `ACADEMIC_YEAR_*`    |

Do not introduce new error codes with these prefixes unless there is a deliberate compatibility layer:

```text
SCHOOL_*
STUDENT_*
PARENT_*
```

---

# MVP Error Code Set

The MVP should implement at minimum:

```text
BAD_REQUEST
INVALID_REQUEST_BODY
INVALID_QUERY_PARAMETER
INVALID_PATH_PARAMETER
VALIDATION_ERROR
RESOURCE_NOT_FOUND
RESOURCE_ALREADY_EXISTS
RESOURCE_CONFLICT
INTERNAL_SERVER_ERROR
SERVICE_UNAVAILABLE

AUTH_REQUIRED
AUTH_INVALID_CREDENTIALS
AUTH_INVALID_TOKEN
AUTH_TOKEN_EXPIRED
AUTH_REFRESH_TOKEN_INVALID
AUTH_REFRESH_TOKEN_EXPIRED
AUTH_FORBIDDEN
AUTH_EMAIL_NOT_VERIFIED
AUTH_ACCOUNT_DISABLED
AUTH_TOO_MANY_ATTEMPTS

RBAC_ROLE_REQUIRED
RBAC_PERMISSION_REQUIRED
RBAC_ROLE_ASSIGNMENT_NOT_ALLOWED
RBAC_LAST_ADMIN_REMOVAL_NOT_ALLOWED

TENANT_SCOPE_REQUIRED
TENANT_SCOPE_VIOLATION
TENANT_ORGANIZATION_REQUIRED
TENANT_INSTITUTION_REQUIRED
TENANT_ORGANIZATION_MISMATCH
TENANT_INSTITUTION_MISMATCH
TENANT_INSTITUTION_NOT_IN_ORGANIZATION
TENANT_MEMBERSHIP_REQUIRED

ORG_NOT_FOUND
ORG_ALREADY_EXISTS
ORG_INACTIVE

INST_NOT_FOUND
INST_ALREADY_EXISTS
INST_INACTIVE
INST_MVP_SINGLE_INSTITUTION_LIMIT

USER_NOT_FOUND
USER_ALREADY_EXISTS
USER_EMAIL_ALREADY_EXISTS
USER_INACTIVE

MEMBERSHIP_NOT_FOUND
MEMBERSHIP_ALREADY_EXISTS
MEMBERSHIP_INVITATION_NOT_FOUND
MEMBERSHIP_INVITATION_EXPIRED

ACADEMIC_YEAR_NOT_FOUND
ACADEMIC_YEAR_ALREADY_EXISTS
CLASS_NOT_FOUND
CLASS_ALREADY_EXISTS
SECTION_NOT_FOUND
SECTION_ALREADY_EXISTS
SUBJECT_NOT_FOUND
SUBJECT_ALREADY_EXISTS

LEARNER_NOT_FOUND
LEARNER_ALREADY_EXISTS
LEARNER_ADMISSION_NUMBER_EXISTS
LEARNER_ACCESS_DENIED

GUARDIAN_NOT_FOUND
GUARDIAN_ALREADY_EXISTS
GUARDIAN_LEARNER_LINK_NOT_FOUND
GUARDIAN_LEARNER_LINK_ALREADY_EXISTS

TEACHER_NOT_FOUND
TEACHER_ALREADY_EXISTS
TEACHER_ASSIGNMENT_NOT_FOUND
TEACHER_ASSIGNMENT_ALREADY_EXISTS

ATTENDANCE_SESSION_NOT_FOUND
ATTENDANCE_SESSION_ALREADY_EXISTS
ATTENDANCE_RECORD_NOT_FOUND
ATTENDANCE_RECORD_INVALID_STATUS

ASSESSMENT_NOT_FOUND
ASSESSMENT_ALREADY_EXISTS
ASSESSMENT_RESULT_NOT_FOUND
ASSESSMENT_RESULT_INVALID_MARKS

FILE_NOT_FOUND
FILE_UPLOAD_REQUIRED
FILE_UPLOAD_FAILED
FILE_INVALID_TYPE
FILE_TOO_LARGE
FILE_ACCESS_DENIED

AI_PROVIDER_ERROR
AI_PROVIDER_UNAVAILABLE
AI_PROVIDER_TIMEOUT
AI_OUTPUT_INVALID
AI_OUTPUT_UNSAFE

OCR_JOB_NOT_FOUND
OCR_JOB_FAILED
OCR_FILE_UNSUPPORTED
OCR_TEXT_NOT_FOUND

ENTITLEMENT_REQUIRED
USAGE_LIMIT_EXCEEDED

RATE_LIMIT_EXCEEDED
RATE_LIMIT_LOGIN_EXCEEDED

SECURITY_POLICY_VIOLATION
PRIVACY_CONSENT_REQUIRED
```

---

# Error Review Checklist

Before adding or approving an error code, confirm:

* The code is stable and machine-readable.
* The code uses `UPPER_SNAKE_CASE`.
* The code does not expose implementation details.
* The message is safe for users.
* The HTTP status code is correct.
* The error is documented in OpenAPI.
* The frontend can handle it predictably.
* The error is logged with `requestId`.
* Tenant scope errors do not leak cross-tenant details.
* Organization and Institution terminology is used correctly.
* Legacy terms such as School, Student, and Parent are avoided in backend/API codes.
* Tests cover the error scenario.

---

# Related Documents

* API_GUIDELINES.md
* AUTHENTICATION.md
* ENDPOINTS.md
* SECURITY.md
* RBAC.md
* PRIVACY.md
* DATABASE_SCHEMA.md
* CODING_STANDARDS.md

````


