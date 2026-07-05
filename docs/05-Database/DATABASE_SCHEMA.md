# Acadedx Database Schema

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)  
**Database:** PostgreSQL  
**ORM:** Prisma

---

# Purpose

This document defines the logical database schema for Acadedx.

The schema must support:

- Students
- Parents
- Teachers
- Institutions
- Organizations
- AI-powered learning
- Subscriptions
- Payments
- Analytics
- Notifications
- Administration
- Audit logging

This document is a logical schema reference. The final physical schema will be implemented in Prisma.

---

# Database Design Philosophy

Acadedx must be designed as a future-ready education platform.

The MVP may focus on schools, but the database must not be school-only.

Use:

```text
Organization
    ↓
Institution
    ↓
Academic Structure
    ↓
Users
    ↓
Learning Activity
```

Do not design the schema in a way that blocks future support for multiple institutions per organization.

---

# Core Platform Hierarchy

```text
Platform
    ↓
Organization
    ↓
Institution
    ↓
AcademicSession
    ↓
Grade
    ↓
Section
    ↓
Subject
    ↓
Chapter
    ↓
Topic
```

---

# MVP Constraint

In Version 1.0:

```text
One Organization → One Institution
```

This constraint must be enforced through business rules, not by preventing multiple institutions in the schema.

Future versions must support:

```text
One Organization → Multiple Institutions
```

---

# Naming Standards

## Prisma Models

Use PascalCase.

Examples:

```text
Organization
Institution
User
Homework
QuizAttempt
```

---

## Database Tables

Use snake_case plural table names.

Examples:

```text
organizations
institutions
users
homework
quiz_attempts
```

---

## Fields

Use camelCase in Prisma.

Examples:

```text
organizationId
institutionId
createdAt
updatedAt
```

---

# Primary Key Standard

Every primary table must use UUID primary keys.

```text
id UUID PRIMARY KEY
```

---

# Common Audit Fields

Business tables should include:

| Field | Required | Purpose |
|---|---|---|
| id | Yes | Primary key |
| createdAt | Yes | Record creation timestamp |
| updatedAt | Yes | Last update timestamp |
| createdBy | Conditional | User who created the record |
| updatedBy | Conditional | User who last updated the record |
| deletedAt | Optional | Soft delete timestamp |
| deletedBy | Optional | User who soft deleted the record |

---

# Tenant Scope Fields

Tables containing organization or institution-owned data must include relevant scope fields.

| Field | Purpose |
|---|---|
| organizationId | Business owner scope |
| institutionId | Education unit scope |

Use `institutionId`, not `schoolId`.

A school is an institution type.

---

# Major Schema Groups

The database is divided into these groups:

1. Identity
2. Organizations
3. Institutions
4. Academic Structure
5. Users and Profiles
6. Learning
7. AI and OCR
8. Analytics
9. Notifications
10. Commerce
11. Payments
12. Administration
13. Audit
14. Storage

---

# 1. Identity Tables

## User

Stores the base user account.

| Field | Type | Required | Notes |
|---|---|---|---|
| id | UUID | Yes | Primary key |
| email | String | Yes | Unique |
| phone | String | No | Unique where provided |
| passwordHash | String | Yes | Argon2 hash |
| status | Enum | Yes | ACTIVE, INACTIVE, SUSPENDED, DELETED |
| emailVerifiedAt | DateTime | No | Email verification timestamp |
| lastLoginAt | DateTime | No | Last successful login |
| createdAt | DateTime | Yes | Audit |
| updatedAt | DateTime | Yes | Audit |
| deletedAt | DateTime | No | Soft delete |

---

## Role

Stores platform roles.

| Field | Type | Required | Notes |
|---|---|---|---|
| id | UUID | Yes | Primary key |
| code | String | Yes | Unique |
| name | String | Yes | Display name |
| description | String | No | Role description |
| scope | Enum | Yes | PLATFORM, ORGANIZATION, INSTITUTION |
| createdAt | DateTime | Yes | Audit |
| updatedAt | DateTime | Yes | Audit |

Example role codes:

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

---

## Permission

Stores permission definitions.

| Field | Type | Required | Notes |
|---|---|---|---|
| id | UUID | Yes | Primary key |
| code | String | Yes | Unique permission code |
| name | String | Yes | Display name |
| description | String | No | Description |
| module | String | Yes | Related module |

Examples:

```text
users.manage
organizations.manage
institutions.manage
homework.solve
quiz.generate
payments.view
admin.audit.view
```

---

## RolePermission

Maps roles to permissions.

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| roleId | UUID | Yes |
| permissionId | UUID | Yes |

Unique constraint:

```text
roleId + permissionId
```

---

## UserRole

Maps users to roles in optional organization or institution context.

| Field | Type | Required | Notes |
|---|---|---|---|
| id | UUID | Yes | Primary key |
| userId | UUID | Yes | User reference |
| roleId | UUID | Yes | Role reference |
| organizationId | UUID | No | Organization scope |
| institutionId | UUID | No | Institution scope |
| createdAt | DateTime | Yes | Audit |

This table enables one user to have different roles in different contexts.

---

## Session

Tracks user sessions and refresh tokens.

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | Yes |
| refreshTokenHash | String | Yes |
| deviceName | String | No |
| ipAddress | String | No |
| userAgent | String | No |
| expiresAt | DateTime | Yes |
| revokedAt | DateTime | No |
| createdAt | DateTime | Yes |

---

## PasswordResetToken

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | Yes |
| tokenHash | String | Yes |
| expiresAt | DateTime | Yes |
| usedAt | DateTime | No |
| createdAt | DateTime | Yes |

---

## EmailVerificationToken

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | Yes |
| tokenHash | String | Yes |
| expiresAt | DateTime | Yes |
| usedAt | DateTime | No |
| createdAt | DateTime | Yes |

---

# 2. Organization Tables

## Organization

Represents the business owner, trust, company, education group, franchise, or enterprise customer.

| Field | Type | Required | Notes |
|---|---|---|---|
| id | UUID | Yes | Primary key |
| name | String | Yes | Organization name |
| code | String | Yes | Unique organization code |
| type | Enum | Yes | INDIVIDUAL, SCHOOL_GROUP, COMPANY, TRUST, ENTERPRISE |
| ownerUserId | UUID | No | Organization owner |
| status | Enum | Yes | ACTIVE, INACTIVE, SUSPENDED |
| country | String | No | Country |
| timezone | String | No | Default timezone |
| createdAt | DateTime | Yes | Audit |
| updatedAt | DateTime | Yes | Audit |
| deletedAt | DateTime | No | Soft delete |

---

## OrganizationSettings

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| organizationId | UUID | Yes |
| settingsJson | Json | Yes |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

## OrganizationBranding

Future-ready branding configuration.

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| organizationId | UUID | Yes |
| logoUrl | String | No |
| primaryColor | String | No |
| secondaryColor | String | No |
| customDomain | String | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

MVP UI may not expose this.

---

# 3. Institution Tables

## Institution

Represents the educational unit where learning happens.

A school is one institution type.

| Field | Type | Required | Notes |
|---|---|---|---|
| id | UUID | Yes | Primary key |
| organizationId | UUID | Yes | Parent organization |
| name | String | Yes | Institution name |
| code | String | Yes | Unique within organization |
| type | Enum | Yes | SCHOOL, ACADEMY, COACHING_INSTITUTE, COLLEGE, UNIVERSITY, TRAINING_CENTRE, ONLINE_ACADEMY |
| status | Enum | Yes | ACTIVE, INACTIVE, SUSPENDED |
| board | String | No | CBSE, ICSE, State Board, etc. |
| medium | String | No | English, Hindi, etc. |
| country | String | No | Country |
| state | String | No | State |
| city | String | No | City |
| address | String | No | Address |
| timezone | String | No | Institution timezone |
| contactEmail | String | No | Contact email |
| contactPhone | String | No | Contact phone |
| website | String | No | Website |
| logoUrl | String | No | Logo |
| createdAt | DateTime | Yes | Audit |
| updatedAt | DateTime | Yes | Audit |
| deletedAt | DateTime | No | Soft delete |

Unique constraint:

```text
organizationId + code
```

---

## InstitutionSettings

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| organizationId | UUID | Yes |
| institutionId | UUID | Yes |
| settingsJson | Json | Yes |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

# 4. Academic Structure Tables

## AcademicSession

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| organizationId | UUID | Yes |
| institutionId | UUID | Yes |
| name | String | Yes |
| startDate | Date | Yes |
| endDate | Date | Yes |
| status | Enum | Yes |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

Status values:

```text
UPCOMING
ACTIVE
ARCHIVED
```

---

## Grade

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| organizationId | UUID | Yes |
| institutionId | UUID | Yes |
| academicSessionId | UUID | No |
| name | String | Yes |
| displayOrder | Int | Yes |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

Examples:

```text
Class 1
Class 7
Class 10
Grade 12
```

---

## Section

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| organizationId | UUID | Yes |
| institutionId | UUID | Yes |
| gradeId | UUID | Yes |
| name | String | Yes |
| classTeacherId | UUID | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

Examples:

```text
A
B
Science
Commerce
```

---

## Subject

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| organizationId | UUID | No |
| institutionId | UUID | No |
| name | String | Yes |
| code | String | No |
| description | String | No |
| isGlobal | Boolean | Yes |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

Global subjects may be reused across institutions.

Institution-specific subjects may override or extend global subjects.

---

## Chapter

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| subjectId | UUID | Yes |
| gradeId | UUID | No |
| title | String | Yes |
| sequence | Int | Yes |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

## Topic

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| chapterId | UUID | Yes |
| title | String | Yes |
| sequence | Int | Yes |
| difficulty | Enum | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

# 5. User Profile Tables

## UserProfile

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | Yes |
| firstName | String | Yes |
| lastName | String | Yes |
| displayName | String | No |
| avatarUrl | String | No |
| dateOfBirth | Date | No |
| gender | String | No |
| country | String | No |
| timezone | String | No |
| preferredLanguage | String | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

## StudentProfile

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | Yes |
| organizationId | UUID | No |
| institutionId | UUID | No |
| academicSessionId | UUID | No |
| gradeId | UUID | No |
| sectionId | UUID | No |
| rollNumber | String | No |
| board | String | No |
| status | Enum | Yes |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

## ParentProfile

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | Yes |
| occupation | String | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

## TeacherProfile

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | Yes |
| organizationId | UUID | No |
| institutionId | UUID | No |
| employeeCode | String | No |
| department | String | No |
| qualification | String | No |
| experienceYears | Int | No |
| status | Enum | Yes |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

## ParentStudent

Links parents and students.

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| parentUserId | UUID | Yes |
| studentUserId | UUID | Yes |
| relationship | String | No |
| status | Enum | Yes |
| createdAt | DateTime | Yes |

Unique constraint:

```text
parentUserId + studentUserId
```

---

## LearningProfile

Stores personalization data for a student.

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| studentUserId | UUID | Yes |
| preferredDifficulty | Enum | No |
| preferredLanguage | String | No |
| dailyStudyGoalMinutes | Int | No |
| learningStyle | String | No |
| goalsJson | Json | No |
| weakSubjectsJson | Json | No |
| strongSubjectsJson | Json | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

## UserPreference

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | Yes |
| theme | String | No |
| language | String | No |
| notificationPreferencesJson | Json | No |
| aiPreferencesJson | Json | No |
| accessibilityJson | Json | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

# 6. Learning Tables

## Homework

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| organizationId | UUID | No |
| institutionId | UUID | No |
| studentUserId | UUID | Yes |
| subjectId | UUID | No |
| chapterId | UUID | No |
| topicId | UUID | No |
| inputType | Enum | Yes |
| questionText | Text | No |
| status | Enum | Yes |
| difficulty | Enum | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |
| deletedAt | DateTime | No |

Input type values:

```text
TEXT
IMAGE
PDF
CAMERA
```

Status values:

```text
DRAFT
UPLOADED
PROCESSING
COMPLETED
FAILED
```

---

## HomeworkAttachment

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| homeworkId | UUID | Yes |
| fileId | UUID | Yes |
| attachmentType | Enum | Yes |
| createdAt | DateTime | Yes |

---

## HomeworkSolution

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| homeworkId | UUID | Yes |
| aiRequestId | UUID | No |
| finalAnswer | Text | No |
| stepByStepExplanation | Text | Yes |
| conceptExplanation | Text | No |
| formulasUsedJson | Json | No |
| commonMistakesJson | Json | No |
| confidenceScore | Decimal | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

## Note

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| organizationId | UUID | No |
| institutionId | UUID | No |
| studentUserId | UUID | Yes |
| subjectId | UUID | No |
| chapterId | UUID | No |
| topicId | UUID | No |
| title | String | Yes |
| content | Text | Yes |
| sourceType | Enum | No |
| sourceId | UUID | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |
| deletedAt | DateTime | No |

---

## Quiz

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| organizationId | UUID | No |
| institutionId | UUID | No |
| createdByUserId | UUID | Yes |
| subjectId | UUID | No |
| chapterId | UUID | No |
| topicId | UUID | No |
| title | String | Yes |
| difficulty | Enum | No |
| mode | Enum | Yes |
| totalQuestions | Int | Yes |
| timeLimitMinutes | Int | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |
| deletedAt | DateTime | No |

Mode values:

```text
PRACTICE
EXAM
REVISION
DAILY_CHALLENGE
```

---

## QuizQuestion

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| quizId | UUID | Yes |
| questionText | Text | Yes |
| questionType | Enum | Yes |
| optionsJson | Json | No |
| correctAnswerJson | Json | Yes |
| explanation | Text | No |
| difficulty | Enum | No |
| sequence | Int | Yes |

---

## QuizAttempt

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| quizId | UUID | Yes |
| studentUserId | UUID | Yes |
| startedAt | DateTime | Yes |
| submittedAt | DateTime | No |
| score | Decimal | No |
| percentage | Decimal | No |
| status | Enum | Yes |
| feedbackJson | Json | No |

---

## QuizAnswer

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| quizAttemptId | UUID | Yes |
| quizQuestionId | UUID | Yes |
| answerJson | Json | No |
| isCorrect | Boolean | No |
| marksAwarded | Decimal | No |
| timeTakenSeconds | Int | No |

---

## Worksheet

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| organizationId | UUID | No |
| institutionId | UUID | No |
| createdByUserId | UUID | Yes |
| subjectId | UUID | No |
| chapterId | UUID | No |
| topicId | UUID | No |
| title | String | Yes |
| difficulty | Enum | No |
| questionCount | Int | Yes |
| answerKeyIncluded | Boolean | Yes |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |
| deletedAt | DateTime | No |

---

## WorksheetQuestion

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| worksheetId | UUID | Yes |
| questionText | Text | Yes |
| questionType | Enum | Yes |
| answerJson | Json | No |
| explanation | Text | No |
| sequence | Int | Yes |

---

## StudyPlan

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| studentUserId | UUID | Yes |
| title | String | Yes |
| goal | Text | No |
| startDate | Date | Yes |
| endDate | Date | Yes |
| status | Enum | Yes |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

## StudyTask

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| studyPlanId | UUID | Yes |
| title | String | Yes |
| taskType | Enum | Yes |
| scheduledDate | Date | Yes |
| estimatedMinutes | Int | No |
| completedAt | DateTime | No |
| relatedEntityType | String | No |
| relatedEntityId | UUID | No |

---

# 7. AI and OCR Tables

## AiRequest

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | Yes |
| organizationId | UUID | No |
| institutionId | UUID | No |
| feature | String | Yes |
| provider | String | Yes |
| model | String | Yes |
| promptVersion | String | No |
| inputTokens | Int | No |
| outputTokens | Int | No |
| estimatedCost | Decimal | No |
| latencyMs | Int | No |
| status | Enum | Yes |
| errorCode | String | No |
| createdAt | DateTime | Yes |

---

## AiFeedback

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| aiRequestId | UUID | Yes |
| userId | UUID | Yes |
| rating | Int | No |
| feedbackText | Text | No |
| createdAt | DateTime | Yes |

---

## Conversation

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| studentUserId | UUID | Yes |
| organizationId | UUID | No |
| institutionId | UUID | No |
| title | String | No |
| sourceType | String | No |
| sourceId | UUID | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

## Message

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| conversationId | UUID | Yes |
| role | Enum | Yes |
| content | Text | Yes |
| aiRequestId | UUID | No |
| createdAt | DateTime | Yes |

Role values:

```text
USER
ASSISTANT
SYSTEM
```

---

## OcrJob

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | Yes |
| organizationId | UUID | No |
| institutionId | UUID | No |
| fileId | UUID | Yes |
| provider | String | No |
| status | Enum | Yes |
| confidenceScore | Decimal | No |
| extractedText | Text | No |
| structuredResultJson | Json | No |
| errorCode | String | No |
| createdAt | DateTime | Yes |
| completedAt | DateTime | No |

---

# 8. Analytics Tables

## ActivityEvent

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | No |
| organizationId | UUID | No |
| institutionId | UUID | No |
| eventName | String | Yes |
| entityType | String | No |
| entityId | UUID | No |
| metadataJson | Json | No |
| createdAt | DateTime | Yes |

---

## LearningMastery

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| studentUserId | UUID | Yes |
| subjectId | UUID | No |
| chapterId | UUID | No |
| topicId | UUID | No |
| masteryScore | Decimal | Yes |
| masteryLevel | Enum | Yes |
| lastCalculatedAt | DateTime | Yes |

---

## Recommendation

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| studentUserId | UUID | Yes |
| recommendationType | String | Yes |
| title | String | Yes |
| description | Text | No |
| targetEntityType | String | No |
| targetEntityId | UUID | No |
| status | Enum | Yes |
| createdAt | DateTime | Yes |

---

# 9. Notification Tables

## Notification

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | Yes |
| organizationId | UUID | No |
| institutionId | UUID | No |
| category | String | Yes |
| title | String | Yes |
| message | Text | Yes |
| actionUrl | String | No |
| priority | Enum | Yes |
| readAt | DateTime | No |
| createdAt | DateTime | Yes |

---

## NotificationPreference

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | Yes |
| preferencesJson | Json | Yes |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

## NotificationTemplate

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| code | String | Yes |
| channel | Enum | Yes |
| subject | String | No |
| body | Text | Yes |
| status | Enum | Yes |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

# 10. Commerce Tables

## Plan

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| code | String | Yes |
| name | String | Yes |
| planType | Enum | Yes |
| status | Enum | Yes |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

Plan types:

```text
FREE
STUDENT_PRO
FAMILY
TEACHER
INSTITUTION
ENTERPRISE
```

---

## Feature

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| code | String | Yes |
| name | String | Yes |
| description | String | No |
| module | String | Yes |
| createdAt | DateTime | Yes |

Example feature codes:

```text
homework.solve
ai_tutor.chat
notes.generate
quiz.generate
worksheets.generate
analytics.advanced
institution.dashboard
```

---

## PlanFeature

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| planId | UUID | Yes |
| featureId | UUID | Yes |
| limitValue | Int | No |
| limitPeriod | Enum | No |
| isUnlimited | Boolean | Yes |

---

## Subscription

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | No |
| organizationId | UUID | No |
| institutionId | UUID | No |
| planId | UUID | Yes |
| status | Enum | Yes |
| startDate | DateTime | Yes |
| endDate | DateTime | No |
| trialEndsAt | DateTime | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

A subscription may belong to:

- Individual user
- Family owner user
- Institution
- Organization

Business rules decide which fields are required by plan type.

---

## UsageCounter

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| userId | UUID | No |
| organizationId | UUID | No |
| institutionId | UUID | No |
| featureId | UUID | Yes |
| periodStart | DateTime | Yes |
| periodEnd | DateTime | Yes |
| usedCount | Int | Yes |
| limitValue | Int | No |

---

# 11. Payment Tables

## Payment

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| subscriptionId | UUID | No |
| userId | UUID | No |
| organizationId | UUID | No |
| institutionId | UUID | No |
| amount | Decimal | Yes |
| currency | String | Yes |
| provider | String | Yes |
| providerPaymentId | String | No |
| status | Enum | Yes |
| idempotencyKey | String | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

## Invoice

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| paymentId | UUID | No |
| subscriptionId | UUID | No |
| invoiceNumber | String | Yes |
| amount | Decimal | Yes |
| taxAmount | Decimal | No |
| totalAmount | Decimal | Yes |
| currency | String | Yes |
| status | Enum | Yes |
| issuedAt | DateTime | Yes |
| dueAt | DateTime | No |
| pdfFileId | UUID | No |

---

## Refund

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| paymentId | UUID | Yes |
| amount | Decimal | Yes |
| reason | String | No |
| status | Enum | Yes |
| providerRefundId | String | No |
| createdAt | DateTime | Yes |

---

# 12. Admin and Audit Tables

## FeatureFlag

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| key | String | Yes |
| description | String | No |
| enabled | Boolean | Yes |
| rolloutJson | Json | No |
| createdAt | DateTime | Yes |
| updatedAt | DateTime | Yes |

---

## AuditLog

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| actorUserId | UUID | No |
| organizationId | UUID | No |
| institutionId | UUID | No |
| action | String | Yes |
| targetType | String | No |
| targetId | UUID | No |
| ipAddress | String | No |
| userAgent | String | No |
| metadataJson | Json | No |
| createdAt | DateTime | Yes |

---

# 13. Storage Tables

## FileAsset

| Field | Type | Required |
|---|---|---|
| id | UUID | Yes |
| uploadedByUserId | UUID | No |
| organizationId | UUID | No |
| institutionId | UUID | No |
| storageProvider | String | Yes |
| bucket | String | Yes |
| objectKey | String | Yes |
| fileName | String | Yes |
| mimeType | String | Yes |
| sizeBytes | BigInt | Yes |
| visibility | Enum | Yes |
| createdAt | DateTime | Yes |
| deletedAt | DateTime | No |

---

# Required Indexes

Minimum recommended indexes:

```text
users.email
users.phone
user_roles.userId
user_roles.organizationId
user_roles.institutionId
organizations.code
institutions.organizationId
institutions.organizationId + institutions.code
academic_sessions.institutionId
grades.institutionId
sections.gradeId
homework.studentUserId
homework.institutionId
quiz_attempts.studentUserId
activity_events.userId
activity_events.organizationId
activity_events.institutionId
activity_events.eventName
ai_requests.userId
ai_requests.feature
payments.providerPaymentId
audit_logs.actorUserId
audit_logs.organizationId
audit_logs.institutionId
```

---

# Data Isolation Rules

Every query for scoped data must enforce appropriate scope.

## Organization-scoped data

Must filter by:

```text
organizationId
```

## Institution-scoped data

Must filter by:

```text
institutionId
```

## Student-owned data

Must filter by:

```text
studentUserId
```

## Parent access

Must validate through:

```text
ParentStudent
```

## Teacher access

Must validate through teacher assignment or institution permission.

---

# Soft Delete Rules

Use soft delete for:

- Users
- Organizations
- Institutions
- Homework
- Notes
- Quizzes
- Worksheets
- Study plans
- Files
- Subscriptions where appropriate

Do not soft delete:

- Payments
- Invoices
- Audit logs
- AI usage records
- Security logs

These require historical integrity.

---

# Data Retention Guidelines

| Data Type | Retention |
|---|---|
| Audit logs | 7 years |
| Payment records | 7 years or legal requirement |
| AI usage metadata | 24 months initially |
| Activity events | 24 months initially |
| Deleted user data | According to privacy policy |
| Homework uploads | Based on plan and retention settings |
| Reports | Configurable |

---

# MVP Schema Requirements

The MVP must include:

- User
- Role
- Permission
- UserRole
- Session
- Organization
- Institution
- AcademicSession
- Grade
- Section
- Subject
- UserProfile
- StudentProfile
- ParentProfile
- TeacherProfile
- ParentStudent
- LearningProfile
- Homework
- HomeworkAttachment
- HomeworkSolution
- OcrJob
- AiRequest
- Conversation
- Message
- Note
- Quiz
- QuizQuestion
- QuizAttempt
- QuizAnswer
- Worksheet
- WorksheetQuestion
- StudyPlan
- StudyTask
- ActivityEvent
- LearningMastery
- Notification
- Plan
- Feature
- PlanFeature
- Subscription
- UsageCounter
- Payment
- AuditLog
- FileAsset

---

# Deferred or Optional Tables

The following may be introduced after MVP:

- OrganizationBranding
- InstitutionBranding
- Refund
- Invoice PDF storage
- SupportTicket
- ReportJob
- SearchIndex
- TeacherClassAssignment
- Assignment
- AssignmentSubmission
- CalendarIntegration
- WebhookEvent
- ApiKey
- MarketplaceProduct

---

# Migration Rules

- All database changes must use Prisma migrations.
- Manual production database changes are prohibited.
- Migration names must be descriptive.
- Migration rollback strategy must be considered.
- Seed data must not contain real personal data.
- Production seed data must be reviewed.

---

# Schema Review Checklist

Before approving schema changes:

- Does it support Organization?
- Does it support Institution?
- Does it avoid hardcoded school-only design?
- Does it preserve tenant isolation?
- Does it support MVP simplicity?
- Does it support future multi-institution expansion?
- Are indexes defined?
- Are relationships clear?
- Are soft delete rules clear?
- Are audit fields included?
- Are sensitive records protected?

---

# Related Documents

- DATA_DICTIONARY.md
- ER_DIAGRAM.md
- MIGRATIONS.md
- SYSTEM_ARCHITECTURE.md
- API_GUIDELINES.md
- PRODUCT_REQUIREMENTS.md