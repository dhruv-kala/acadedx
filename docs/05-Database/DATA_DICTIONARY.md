# Acadedx Data Dictionary

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)  
**Database:** PostgreSQL  
**ORM:** Prisma

---

# Purpose

This document defines the key database entities, fields, meanings, constraints, and business rules for Acadedx.

It should be used by:

- Backend developers
- Frontend developers
- QA engineers
- API designers
- AI engineers
- Product managers
- AI coding agents

This document complements `DATABASE_SCHEMA.md`.

---

# Data Dictionary Principles

Acadedx must use a future-ready education platform model.

Use:

```text
Organization
Institution
Academic Session
Grade
Section
Subject
Chapter
Topic
```

Do not use `School` as the core database entity.

A school is an Institution type.

---

# Core Hierarchy

```text
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

MVP constraint:

```text
One Organization → One Institution
```

Future state:

```text
One Organization → Multiple Institutions
```

---

# Common Field Definitions

| Field | Type | Description |
|---|---|---|
| id | UUID | Primary identifier |
| organizationId | UUID | Parent organization context |
| institutionId | UUID | Institution context |
| createdAt | DateTime | Record creation timestamp |
| updatedAt | DateTime | Last update timestamp |
| createdBy | UUID | User who created the record |
| updatedBy | UUID | User who last updated the record |
| deletedAt | DateTime | Soft delete timestamp |
| deletedBy | UUID | User who soft deleted the record |
| status | Enum | Current lifecycle state |

---

# Identity Entities

## User

Represents a login-capable account.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Unique user ID |
| email | String | Yes | User email, globally unique |
| phone | String | No | User phone number |
| passwordHash | String | Yes | Securely hashed password |
| status | UserStatus | Yes | User account status |
| emailVerifiedAt | DateTime | No | Email verification timestamp |
| lastLoginAt | DateTime | No | Last successful login |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |
| deletedAt | DateTime | No | Soft delete timestamp |

Business rules:

- Email must be unique.
- Password must never be stored in plain text.
- Deleted users should be soft deleted unless legal deletion is required.
- Authentication data must not be exposed through public APIs.

---

## Role

Represents a named access role.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Role ID |
| code | String | Yes | Stable role code |
| name | String | Yes | Display name |
| description | String | No | Role description |
| scope | RoleScope | Yes | PLATFORM, ORGANIZATION, INSTITUTION |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

Role examples:

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

Represents a specific capability.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Permission ID |
| code | String | Yes | Stable permission code |
| name | String | Yes | Display name |
| description | String | No | Description |
| module | String | Yes | Related module |

Permission examples:

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

## UserRole

Maps a user to a role in a scope.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | User role ID |
| userId | UUID | Yes | User reference |
| roleId | UUID | Yes | Role reference |
| organizationId | UUID | No | Organization scope |
| institutionId | UUID | No | Institution scope |
| createdAt | DateTime | Yes | Assignment timestamp |

Business rules:

- Platform roles may not require organizationId or institutionId.
- Organization roles require organizationId.
- Institution roles require institutionId.
- One user may have multiple roles across contexts.

---

# Organization Entities

## Organization

Represents the business owner, education group, trust, company, franchise, or enterprise customer.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Organization ID |
| name | String | Yes | Organization name |
| code | String | Yes | Unique organization code |
| type | OrganizationType | Yes | Organization category |
| ownerUserId | UUID | No | Owner user |
| status | OrganizationStatus | Yes | Current status |
| country | String | No | Country |
| timezone | String | No | Default timezone |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |
| deletedAt | DateTime | No | Soft delete timestamp |

Business rules:

- Every institution must belong to one organization.
- MVP allows one institution per organization.
- Future releases may allow multiple institutions per organization.
- Organization may own billing relationship.

---

## OrganizationSettings

Stores configurable organization-level settings.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Settings ID |
| organizationId | UUID | Yes | Organization reference |
| settingsJson | Json | Yes | Flexible settings |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

Business rules:

- Settings should be validated by application logic.
- Do not store secrets in settingsJson.

---

# Institution Entities

## Institution

Represents the education unit where learning happens.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Institution ID |
| organizationId | UUID | Yes | Parent organization |
| name | String | Yes | Institution name |
| code | String | Yes | Unique code within organization |
| type | InstitutionType | Yes | Institution type |
| status | InstitutionStatus | Yes | Current status |
| board | String | No | Education board |
| medium | String | No | Teaching medium |
| country | String | No | Country |
| state | String | No | State |
| city | String | No | City |
| address | String | No | Physical address |
| timezone | String | No | Timezone |
| contactEmail | String | No | Contact email |
| contactPhone | String | No | Contact phone |
| website | String | No | Website |
| logoUrl | String | No | Institution logo |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |
| deletedAt | DateTime | No | Soft delete timestamp |

Institution types:

```text
SCHOOL
ACADEMY
COACHING_INSTITUTE
COLLEGE
UNIVERSITY
TRAINING_CENTRE
LEARNING_CENTRE
ONLINE_ACADEMY
```

Business rules:

- `organizationId + code` must be unique.
- UI may display “School” in MVP, but backend should use Institution.
- Institution data must be isolated from other institutions.

---

## InstitutionSettings

Stores institution-level configuration.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Settings ID |
| organizationId | UUID | Yes | Organization context |
| institutionId | UUID | Yes | Institution reference |
| settingsJson | Json | Yes | Configurable settings |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

---

# Academic Entities

## AcademicSession

Represents an academic year, term, semester, or session.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Academic session ID |
| organizationId | UUID | Yes | Organization context |
| institutionId | UUID | Yes | Institution context |
| name | String | Yes | Session name |
| startDate | Date | Yes | Start date |
| endDate | Date | Yes | End date |
| status | AcademicSessionStatus | Yes | Current status |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

Status values:

```text
UPCOMING
ACTIVE
ARCHIVED
```

---

## Grade

Represents class, grade, year, or level.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Grade ID |
| organizationId | UUID | Yes | Organization context |
| institutionId | UUID | Yes | Institution context |
| academicSessionId | UUID | No | Academic session |
| name | String | Yes | Grade name |
| displayOrder | Int | Yes | Sorting order |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

Examples:

```text
Class 1
Class 7
Class 10
Grade 12
Foundation Batch
```

---

## Section

Represents a section, stream, batch, or group inside a grade.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Section ID |
| organizationId | UUID | Yes | Organization context |
| institutionId | UUID | Yes | Institution context |
| gradeId | UUID | Yes | Grade reference |
| name | String | Yes | Section name |
| classTeacherId | UUID | No | Assigned teacher |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

Examples:

```text
A
B
Science
Commerce
JEE Morning Batch
```

---

## Subject

Represents a subject.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Subject ID |
| organizationId | UUID | No | Organization context for custom subject |
| institutionId | UUID | No | Institution context for custom subject |
| name | String | Yes | Subject name |
| code | String | No | Subject code |
| description | String | No | Description |
| isGlobal | Boolean | Yes | Whether subject is globally reusable |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

Examples:

```text
Mathematics
Science
English
Hindi
Physics
Chemistry
Computer Science
```

---

## Chapter

Represents a chapter inside a subject.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Chapter ID |
| subjectId | UUID | Yes | Subject reference |
| gradeId | UUID | No | Grade reference |
| title | String | Yes | Chapter title |
| sequence | Int | Yes | Display sequence |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

---

## Topic

Represents a topic inside a chapter.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Topic ID |
| chapterId | UUID | Yes | Chapter reference |
| title | String | Yes | Topic title |
| sequence | Int | Yes | Display sequence |
| difficulty | Difficulty | No | Difficulty level |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

---

# User Profile Entities

## UserProfile

Stores general user profile information.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Profile ID |
| userId | UUID | Yes | User reference |
| firstName | String | Yes | First name |
| lastName | String | Yes | Last name |
| displayName | String | No | Preferred display name |
| avatarUrl | String | No | Avatar image |
| dateOfBirth | Date | No | Date of birth |
| gender | String | No | Optional gender |
| country | String | No | Country |
| timezone | String | No | Timezone |
| preferredLanguage | String | No | Preferred language |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

---

## StudentProfile

Stores student-specific data.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Student profile ID |
| userId | UUID | Yes | User reference |
| organizationId | UUID | No | Organization context |
| institutionId | UUID | No | Institution context |
| academicSessionId | UUID | No | Academic session |
| gradeId | UUID | No | Grade |
| sectionId | UUID | No | Section |
| rollNumber | String | No | Institution roll number |
| board | String | No | Board or curriculum |
| status | StudentStatus | Yes | Student status |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

Business rules:

- Student may exist independently without institution for B2C use.
- Institution-linked students must have institutionId.
- Parent access must be controlled through ParentStudent relationship.

---

## ParentProfile

Stores parent-specific data.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Parent profile ID |
| userId | UUID | Yes | User reference |
| occupation | String | No | Occupation |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

---

## TeacherProfile

Stores teacher-specific data.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Teacher profile ID |
| userId | UUID | Yes | User reference |
| organizationId | UUID | No | Organization context |
| institutionId | UUID | No | Institution context |
| employeeCode | String | No | Institution employee code |
| department | String | No | Department |
| qualification | String | No | Qualification |
| experienceYears | Int | No | Years of experience |
| status | TeacherStatus | Yes | Teacher status |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

---

## ParentStudent

Links parents to students.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Relationship ID |
| parentUserId | UUID | Yes | Parent user |
| studentUserId | UUID | Yes | Student user |
| relationship | String | No | Father, Mother, Guardian, etc. |
| status | RelationshipStatus | Yes | Relationship status |
| createdAt | DateTime | Yes | Created timestamp |

Business rules:

- Parent can have multiple students.
- Student can have multiple parents or guardians.
- Parent can access only linked student records.

---

## LearningProfile

Stores student personalization data.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Learning profile ID |
| studentUserId | UUID | Yes | Student user |
| preferredDifficulty | Difficulty | No | Preferred learning difficulty |
| preferredLanguage | String | No | Preferred language |
| dailyStudyGoalMinutes | Int | No | Daily study target |
| learningStyle | String | No | Learning style |
| goalsJson | Json | No | Learning goals |
| weakSubjectsJson | Json | No | Weak subjects |
| strongSubjectsJson | Json | No | Strong subjects |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

---

# Learning Entities

## Homework

Represents a submitted homework question or assignment item.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Homework ID |
| organizationId | UUID | No | Organization context |
| institutionId | UUID | No | Institution context |
| studentUserId | UUID | Yes | Student owner |
| subjectId | UUID | No | Subject |
| chapterId | UUID | No | Chapter |
| topicId | UUID | No | Topic |
| inputType | HomeworkInputType | Yes | TEXT, IMAGE, PDF, CAMERA |
| questionText | Text | No | Typed or extracted question |
| status | HomeworkStatus | Yes | Processing status |
| difficulty | Difficulty | No | Estimated difficulty |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |
| deletedAt | DateTime | No | Soft delete timestamp |

Business rules:

- Student can access only own homework.
- Institution context is optional for B2C students.
- AI usage must be tracked for solved homework.

---

## HomeworkSolution

Stores AI-generated homework solution.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Solution ID |
| homeworkId | UUID | Yes | Homework reference |
| aiRequestId | UUID | No | AI request reference |
| finalAnswer | Text | No | Final answer |
| stepByStepExplanation | Text | Yes | Detailed explanation |
| conceptExplanation | Text | No | Concept explanation |
| formulasUsedJson | Json | No | Formula list |
| commonMistakesJson | Json | No | Common mistakes |
| confidenceScore | Decimal | No | AI confidence |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

---

## Note

Represents saved study notes.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Note ID |
| organizationId | UUID | No | Organization context |
| institutionId | UUID | No | Institution context |
| studentUserId | UUID | Yes | Owner |
| subjectId | UUID | No | Subject |
| chapterId | UUID | No | Chapter |
| topicId | UUID | No | Topic |
| title | String | Yes | Note title |
| content | Text | Yes | Note content |
| sourceType | String | No | Source module |
| sourceId | UUID | No | Source record |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |
| deletedAt | DateTime | No | Soft delete timestamp |

---

## Quiz

Represents a generated or assigned quiz.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Quiz ID |
| organizationId | UUID | No | Organization context |
| institutionId | UUID | No | Institution context |
| createdByUserId | UUID | Yes | Creator |
| subjectId | UUID | No | Subject |
| chapterId | UUID | No | Chapter |
| topicId | UUID | No | Topic |
| title | String | Yes | Quiz title |
| difficulty | Difficulty | No | Difficulty |
| mode | QuizMode | Yes | Quiz mode |
| totalQuestions | Int | Yes | Number of questions |
| timeLimitMinutes | Int | No | Time limit |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |
| deletedAt | DateTime | No | Soft delete timestamp |

---

## QuizAttempt

Represents a student's quiz attempt.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Attempt ID |
| quizId | UUID | Yes | Quiz |
| studentUserId | UUID | Yes | Student |
| startedAt | DateTime | Yes | Start time |
| submittedAt | DateTime | No | Submission time |
| score | Decimal | No | Score |
| percentage | Decimal | No | Percentage |
| status | QuizAttemptStatus | Yes | Attempt status |
| feedbackJson | Json | No | AI or system feedback |

---

## Worksheet

Represents a generated worksheet.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Worksheet ID |
| organizationId | UUID | No | Organization context |
| institutionId | UUID | No | Institution context |
| createdByUserId | UUID | Yes | Creator |
| subjectId | UUID | No | Subject |
| chapterId | UUID | No | Chapter |
| topicId | UUID | No | Topic |
| title | String | Yes | Worksheet title |
| difficulty | Difficulty | No | Difficulty |
| questionCount | Int | Yes | Number of questions |
| answerKeyIncluded | Boolean | Yes | Whether answer key is included |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |
| deletedAt | DateTime | No | Soft delete timestamp |

---

## StudyPlan

Represents a student study plan.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Study plan ID |
| studentUserId | UUID | Yes | Student |
| title | String | Yes | Plan title |
| goal | Text | No | Study goal |
| startDate | Date | Yes | Start date |
| endDate | Date | Yes | End date |
| status | StudyPlanStatus | Yes | Plan status |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

---

# AI and OCR Entities

## AiRequest

Tracks every AI request.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | AI request ID |
| userId | UUID | Yes | Requesting user |
| organizationId | UUID | No | Organization context |
| institutionId | UUID | No | Institution context |
| feature | String | Yes | Feature using AI |
| provider | String | Yes | AI provider |
| model | String | Yes | AI model |
| promptVersion | String | No | Prompt version |
| inputTokens | Int | No | Input tokens |
| outputTokens | Int | No | Output tokens |
| estimatedCost | Decimal | No | Estimated cost |
| latencyMs | Int | No | Latency |
| status | AiRequestStatus | Yes | Status |
| errorCode | String | No | Error code |
| createdAt | DateTime | Yes | Created timestamp |

Business rules:

- Every AI call must create an AiRequest record.
- Cost tracking is mandatory from MVP.
- Sensitive prompt content should be handled carefully.

---

## Conversation

Represents an AI Tutor conversation.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Conversation ID |
| studentUserId | UUID | Yes | Student |
| organizationId | UUID | No | Organization context |
| institutionId | UUID | No | Institution context |
| title | String | No | Conversation title |
| sourceType | String | No | Source module |
| sourceId | UUID | No | Source record |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

---

## Message

Represents a conversation message.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Message ID |
| conversationId | UUID | Yes | Conversation |
| role | MessageRole | Yes | USER, ASSISTANT, SYSTEM |
| content | Text | Yes | Message content |
| aiRequestId | UUID | No | AI request reference |
| createdAt | DateTime | Yes | Created timestamp |

---

## OcrJob

Represents an OCR processing job.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | OCR job ID |
| userId | UUID | Yes | Requesting user |
| organizationId | UUID | No | Organization context |
| institutionId | UUID | No | Institution context |
| fileId | UUID | Yes | Uploaded file |
| provider | String | No | OCR provider |
| status | OcrJobStatus | Yes | Job status |
| confidenceScore | Decimal | No | OCR confidence |
| extractedText | Text | No | Extracted text |
| structuredResultJson | Json | No | Structured output |
| errorCode | String | No | Failure code |
| createdAt | DateTime | Yes | Created timestamp |
| completedAt | DateTime | No | Completion timestamp |

---

# Analytics Entities

## ActivityEvent

Generic analytics event.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Event ID |
| userId | UUID | No | User |
| organizationId | UUID | No | Organization context |
| institutionId | UUID | No | Institution context |
| eventName | String | Yes | Event name |
| entityType | String | No | Related entity type |
| entityId | UUID | No | Related entity ID |
| metadataJson | Json | No | Event metadata |
| createdAt | DateTime | Yes | Event timestamp |

---

## LearningMastery

Tracks student mastery by subject, chapter, or topic.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Mastery ID |
| studentUserId | UUID | Yes | Student |
| subjectId | UUID | No | Subject |
| chapterId | UUID | No | Chapter |
| topicId | UUID | No | Topic |
| masteryScore | Decimal | Yes | Score |
| masteryLevel | MasteryLevel | Yes | Level |
| lastCalculatedAt | DateTime | Yes | Last calculation time |

---

# Commerce Entities

## Plan

Represents a subscription plan.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Plan ID |
| code | String | Yes | Stable plan code |
| name | String | Yes | Display name |
| planType | PlanType | Yes | Plan category |
| status | PlanStatus | Yes | Plan status |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

---

## Feature

Represents an entitlement-controlled feature.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Feature ID |
| code | String | Yes | Stable feature code |
| name | String | Yes | Display name |
| description | String | No | Description |
| module | String | Yes | Module name |
| createdAt | DateTime | Yes | Created timestamp |

Examples:

```text
homework.solve
ai_tutor.chat
notes.generate
quiz.generate
worksheets.generate
institution.dashboard
```

---

## Subscription

Represents active or historical subscription.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Subscription ID |
| userId | UUID | No | Individual owner |
| organizationId | UUID | No | Organization owner |
| institutionId | UUID | No | Institution owner |
| planId | UUID | Yes | Plan |
| status | SubscriptionStatus | Yes | Status |
| startDate | DateTime | Yes | Start date |
| endDate | DateTime | No | End date |
| trialEndsAt | DateTime | No | Trial end |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

Business rules:

- Subscription ownership depends on plan type.
- Individual plans usually use userId.
- Institution plans usually use institutionId.
- Enterprise plans usually use organizationId.

---

## UsageCounter

Tracks feature usage limits.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Usage counter ID |
| userId | UUID | No | User scope |
| organizationId | UUID | No | Organization scope |
| institutionId | UUID | No | Institution scope |
| featureId | UUID | Yes | Feature |
| periodStart | DateTime | Yes | Period start |
| periodEnd | DateTime | Yes | Period end |
| usedCount | Int | Yes | Used quantity |
| limitValue | Int | No | Limit quantity |

---

# Payment Entities

## Payment

Represents payment transaction.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Payment ID |
| subscriptionId | UUID | No | Subscription |
| userId | UUID | No | User |
| organizationId | UUID | No | Organization |
| institutionId | UUID | No | Institution |
| amount | Decimal | Yes | Payment amount |
| currency | String | Yes | Currency |
| provider | String | Yes | Payment gateway |
| providerPaymentId | String | No | Gateway payment ID |
| status | PaymentStatus | Yes | Payment status |
| idempotencyKey | String | No | Duplicate prevention key |
| createdAt | DateTime | Yes | Created timestamp |
| updatedAt | DateTime | Yes | Updated timestamp |

---

## Invoice

Represents billing invoice.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Invoice ID |
| paymentId | UUID | No | Payment |
| subscriptionId | UUID | No | Subscription |
| invoiceNumber | String | Yes | Unique invoice number |
| amount | Decimal | Yes | Base amount |
| taxAmount | Decimal | No | Tax amount |
| totalAmount | Decimal | Yes | Final amount |
| currency | String | Yes | Currency |
| status | InvoiceStatus | Yes | Status |
| issuedAt | DateTime | Yes | Issue date |
| dueAt | DateTime | No | Due date |
| pdfFileId | UUID | No | PDF file reference |

---

# Notification Entities

## Notification

Represents in-app or system notification.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Notification ID |
| userId | UUID | Yes | Recipient |
| organizationId | UUID | No | Organization context |
| institutionId | UUID | No | Institution context |
| category | String | Yes | Notification category |
| title | String | Yes | Title |
| message | Text | Yes | Message |
| actionUrl | String | No | Action link |
| priority | NotificationPriority | Yes | Priority |
| readAt | DateTime | No | Read timestamp |
| createdAt | DateTime | Yes | Created timestamp |

---

# Storage Entities

## FileAsset

Represents uploaded or generated file metadata.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | File ID |
| uploadedByUserId | UUID | No | Uploader |
| organizationId | UUID | No | Organization context |
| institutionId | UUID | No | Institution context |
| storageProvider | String | Yes | Storage provider |
| bucket | String | Yes | Storage bucket |
| objectKey | String | Yes | Storage object key |
| fileName | String | Yes | Original file name |
| mimeType | String | Yes | MIME type |
| sizeBytes | BigInt | Yes | File size |
| visibility | FileVisibility | Yes | Access level |
| createdAt | DateTime | Yes | Created timestamp |
| deletedAt | DateTime | No | Soft delete timestamp |

---

# Audit Entity

## AuditLog

Records sensitive system actions.

| Field | Type | Required | Description |
|---|---|---|---|
| id | UUID | Yes | Audit log ID |
| actorUserId | UUID | No | Acting user |
| organizationId | UUID | No | Organization context |
| institutionId | UUID | No | Institution context |
| action | String | Yes | Action code |
| targetType | String | No | Target entity type |
| targetId | UUID | No | Target entity ID |
| ipAddress | String | No | IP address |
| userAgent | String | No | User agent |
| metadataJson | Json | No | Additional metadata |
| createdAt | DateTime | Yes | Timestamp |

Business rules:

- Audit logs should not be soft deleted.
- Sensitive admin actions must be audited.
- Audit logs must be protected from normal modification.

---

# Standard Enums

## UserStatus

```text
ACTIVE
INACTIVE
SUSPENDED
DELETED
```

## OrganizationStatus

```text
ACTIVE
INACTIVE
SUSPENDED
```

## InstitutionStatus

```text
ACTIVE
INACTIVE
SUSPENDED
```

## Difficulty

```text
EASY
MEDIUM
HARD
ADAPTIVE
```

## HomeworkStatus

```text
DRAFT
UPLOADED
PROCESSING
COMPLETED
FAILED
```

## AiRequestStatus

```text
PENDING
COMPLETED
FAILED
TIMEOUT
```

## SubscriptionStatus

```text
TRIALING
ACTIVE
PAST_DUE
CANCELLED
EXPIRED
```

## PaymentStatus

```text
PENDING
SUCCESS
FAILED
REFUNDED
CANCELLED
```

## NotificationPriority

```text
LOW
MEDIUM
HIGH
CRITICAL
```

---

# Data Quality Rules

- Required fields must be validated at API and database levels.
- Email must be normalized before storage.
- Enum values must be controlled.
- JSON fields must be validated by application schemas.
- Tenant-scoped records must include organizationId or institutionId where applicable.
- Deleted records must not appear in normal queries.
- Audit and payment records must not be physically deleted.

---

# Related Documents

- DATABASE_SCHEMA.md
- ER_DIAGRAM.md
- MIGRATIONS.md
- API_GUIDELINES.md
- PRODUCT_REQUIREMENTS.md