# Acadedx API Endpoints

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the planned REST API endpoints for Acadedx.

It provides a module-by-module endpoint catalog for frontend, backend, mobile, desktop, admin, AI services, QA, and AI coding agents.

This document must be read together with:

- API_GUIDELINES.md
- AUTHENTICATION.md
- ERROR_CODES.md
- DATABASE_SCHEMA.md
- PRODUCT_REQUIREMENTS.md
- SECURITY.md

---

# API Base URL

## Production

```text
https://api.acadedx.com/api/v1
```

## Development

```text
http://localhost:3000/api/v1
```

---

# API Design Rules

All endpoints must follow:

- REST-first design
- Versioned path `/api/v1`
- Standard response format
- JWT authentication where required
- Role-based authorization
- Permission-based authorization
- Organization and institution scoping
- Entitlement checks where required
- OpenAPI documentation
- Stable error codes
- Request ID logging

---

# Platform Model

Acadedx APIs must support the future-ready platform hierarchy:

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

MVP constraint:

```text
One Organization → One Institution
```

The API must not hardcode school-only assumptions.

Use:

```text
/institutions
```

Do not use:

```text
/schools
```

A School is an Institution type.

---

# Authentication Header

Protected endpoints require:

```http
Authorization: Bearer <access_token>
```

---

# Standard Success Response

```json
{
  "success": true,
  "message": "Request completed successfully.",
  "data": {}
}
```

---

# Standard Error Response

```json
{
  "success": false,
  "error": {
    "code": "ERROR_CODE",
    "message": "Human-readable error message.",
    "details": []
  }
}
```

---

# Endpoint Status Values

| Status | Meaning |
|---|---|
| MVP | Required for MVP |
| V1 | Early public release |
| V2 | Growth release |
| Enterprise | Enterprise or multi-institution release |
| Future | Long-term roadmap |

---

# Authentication Endpoints

## Register User

```http
POST /auth/register
```

Status: MVP  
Authentication: Public  
Primary Users: Guest, Student, Parent, Teacher

Purpose:

Create a new user account.

Request:

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

Response:

```json
{
  "success": true,
  "message": "Registration successful. Please verify your email.",
  "data": {
    "userId": "uuid",
    "requiresEmailVerification": true
  }
}
```

---

## Login

```http
POST /auth/login
```

Status: MVP  
Authentication: Public  
Primary Users: All Users

Purpose:

Authenticate user and create session.

Request:

```json
{
  "email": "dhruv@example.com",
  "password": "StrongPassword@123"
}
```

Response:

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

## Refresh Token

```http
POST /auth/refresh
```

Status: MVP  
Authentication: Public with refresh token

Purpose:

Issue new access token and rotate refresh token.

---

## Logout

```http
POST /auth/logout
```

Status: MVP  
Authentication: Required

Purpose:

Invalidate current session or refresh token.

---

## Logout All Sessions

```http
POST /auth/logout-all
```

Status: V1  
Authentication: Required

Purpose:

Invalidate all active sessions for the user.

---

## Current User

```http
GET /auth/me
```

Status: MVP  
Authentication: Required

Purpose:

Return current authenticated user context.

---

## Forgot Password

```http
POST /auth/forgot-password
```

Status: MVP  
Authentication: Public

Purpose:

Start password reset flow.

---

## Reset Password

```http
POST /auth/reset-password
```

Status: MVP  
Authentication: Public with reset token

Purpose:

Reset password using valid reset token.

---

## Change Password

```http
POST /auth/change-password
```

Status: MVP  
Authentication: Required

Purpose:

Change password for authenticated user.

---

## Verify Email

```http
POST /auth/verify-email
```

Status: MVP  
Authentication: Public with verification token

Purpose:

Verify email address.

---

## Resend Verification Email

```http
POST /auth/resend-verification
```

Status: MVP  
Authentication: Public or Required depending on flow

Purpose:

Send new verification email.

---

# User Endpoints

## Get Current User Profile

```http
GET /users/me
```

Status: MVP  
Authentication: Required

Purpose:

Return current user's profile.

---

## Update Current User Profile

```http
PATCH /users/me
```

Status: MVP  
Authentication: Required

Purpose:

Update current user's profile.

---

## Get User Preferences

```http
GET /users/me/preferences
```

Status: MVP  
Authentication: Required

Purpose:

Return user preferences.

---

## Update User Preferences

```http
PATCH /users/me/preferences
```

Status: MVP  
Authentication: Required

Purpose:

Update user preferences.

---

## Get User Sessions

```http
GET /users/me/sessions
```

Status: V1  
Authentication: Required

Purpose:

List active sessions.

---

## Revoke User Session

```http
DELETE /users/me/sessions/{sessionId}
```

Status: V1  
Authentication: Required

Purpose:

Revoke one session.

---

## Upload Avatar

```http
POST /users/me/avatar
```

Status: V1  
Authentication: Required

Purpose:

Upload user profile avatar.

---

# Organization Endpoints

## List Organizations

```http
GET /organizations
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator

Purpose:

List organizations.

---

## Create Organization

```http
POST /organizations
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator

Purpose:

Create organization.

---

## Get Organization

```http
GET /organizations/{organizationId}
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator, Organization Owner, Organization Administrator

Purpose:

Get organization details.

---

## Update Organization

```http
PATCH /organizations/{organizationId}
```

Status: V1  
Authentication: Required  
Roles: Platform Administrator, Organization Owner, Organization Administrator

Purpose:

Update organization details.

---

## Get Organization Institutions

```http
GET /organizations/{organizationId}/institutions
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator, Organization Owner, Organization Administrator

Purpose:

List institutions under an organization.

MVP Note:

Returns one institution due to MVP business constraint.

---

## Get Organization Dashboard

```http
GET /organizations/{organizationId}/dashboard
```

Status: Enterprise  
Authentication: Required  
Roles: Organization Owner, Organization Administrator

Purpose:

Return organization-level dashboard.

MVP Note:

Deferred.

---

# Institution Endpoints

## List Institutions

```http
GET /institutions
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator, Organization Owner, Organization Administrator

Purpose:

List institutions visible to current user.

---

## Create Institution

```http
POST /institutions
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator, Organization Owner, Organization Administrator

Purpose:

Create institution.

MVP Rule:

Reject creation if organization already has an institution unless multi-institution feature is enabled.

---

## Get Institution

```http
GET /institutions/{institutionId}
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator, Organization Admin, Institution Admin, Teacher

Purpose:

Get institution details.

---

## Update Institution

```http
PATCH /institutions/{institutionId}
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator, Institution Administrator

Purpose:

Update institution profile.

---

## Delete Institution

```http
DELETE /institutions/{institutionId}
```

Status: Enterprise  
Authentication: Required  
Roles: Platform Administrator

Purpose:

Soft delete institution.

MVP Note:

Usually disabled or restricted.

---

## Get Institution Dashboard

```http
GET /institutions/{institutionId}/dashboard
```

Status: V1  
Authentication: Required  
Roles: Institution Administrator, Platform Administrator

Purpose:

Return institution-level dashboard.

---

## Get Institution Settings

```http
GET /institutions/{institutionId}/settings
```

Status: V1  
Authentication: Required  
Roles: Institution Administrator

Purpose:

Get institution settings.

---

## Update Institution Settings

```http
PATCH /institutions/{institutionId}/settings
```

Status: V1  
Authentication: Required  
Roles: Institution Administrator

Purpose:

Update institution settings.

---

# Academic Endpoints

## List Academic Sessions

```http
GET /institutions/{institutionId}/academic-sessions
```

Status: MVP  
Authentication: Required  
Roles: Institution Administrator, Teacher

Purpose:

List academic sessions for institution.

---

## Create Academic Session

```http
POST /institutions/{institutionId}/academic-sessions
```

Status: MVP  
Authentication: Required  
Roles: Institution Administrator

Purpose:

Create academic session.

---

## Update Academic Session

```http
PATCH /institutions/{institutionId}/academic-sessions/{academicSessionId}
```

Status: V1  
Authentication: Required  
Roles: Institution Administrator

Purpose:

Update academic session.

---

## List Grades

```http
GET /institutions/{institutionId}/grades
```

Status: MVP  
Authentication: Required

Purpose:

List grades for institution.

---

## Create Grade

```http
POST /institutions/{institutionId}/grades
```

Status: MVP  
Authentication: Required  
Roles: Institution Administrator

Purpose:

Create grade.

---

## List Sections

```http
GET /institutions/{institutionId}/sections
```

Status: MVP  
Authentication: Required

Purpose:

List sections.

---

## Create Section

```http
POST /institutions/{institutionId}/sections
```

Status: MVP  
Authentication: Required  
Roles: Institution Administrator

Purpose:

Create section.

---

## List Subjects

```http
GET /subjects
```

Status: MVP  
Authentication: Required

Purpose:

List global and accessible institution subjects.

---

## Create Institution Subject

```http
POST /institutions/{institutionId}/subjects
```

Status: V1  
Authentication: Required  
Roles: Institution Administrator

Purpose:

Create institution-specific subject.

---

## List Chapters

```http
GET /subjects/{subjectId}/chapters
```

Status: MVP  
Authentication: Required

Purpose:

List chapters for subject.

---

## List Topics

```http
GET /chapters/{chapterId}/topics
```

Status: MVP  
Authentication: Required

Purpose:

List topics for chapter.

---

# Homework Endpoints

## Create Homework

```http
POST /homework
```

Status: MVP  
Authentication: Required  
Roles: Student

Purpose:

Create homework from text, image, camera, or file reference.

Request:

```json
{
  "inputType": "TEXT",
  "questionText": "Explain photosynthesis",
  "subjectId": "uuid",
  "chapterId": "uuid",
  "topicId": "uuid"
}
```

---

## List Homework

```http
GET /homework
```

Status: MVP  
Authentication: Required  
Roles: Student, Teacher, Institution Administrator

Purpose:

List homework records visible to current user.

Student sees own homework.

Teacher sees assigned students only.

Institution admin sees institution scope where permitted.

---

## Get Homework

```http
GET /homework/{homeworkId}
```

Status: MVP  
Authentication: Required

Purpose:

Get homework details.

---

## Solve Homework

```http
POST /homework/{homeworkId}/solve
```

Status: MVP  
Authentication: Required  
Roles: Student  
Entitlement: homework.solve

Purpose:

Start AI homework solving workflow.

Response:

```json
{
  "success": true,
  "message": "Homework processing started.",
  "data": {
    "homeworkId": "uuid",
    "status": "PROCESSING"
  }
}
```

---

## Delete Homework

```http
DELETE /homework/{homeworkId}
```

Status: V1  
Authentication: Required  
Roles: Student

Purpose:

Soft delete homework owned by student.

---

## Submit Homework Feedback

```http
POST /homework/{homeworkId}/feedback
```

Status: V1  
Authentication: Required  
Roles: Student

Purpose:

Submit feedback on homework solution.

---

## Generate Quiz from Homework

```http
POST /homework/{homeworkId}/quiz
```

Status: V1  
Authentication: Required  
Roles: Student  
Entitlement: quiz.generate

Purpose:

Generate quiz from solved homework.

---

## Generate Notes from Homework

```http
POST /homework/{homeworkId}/notes
```

Status: V1  
Authentication: Required  
Roles: Student  
Entitlement: notes.generate

Purpose:

Generate notes from homework.

---

## Generate Worksheet from Homework

```http
POST /homework/{homeworkId}/worksheet
```

Status: V1  
Authentication: Required  
Roles: Student  
Entitlement: worksheets.generate

Purpose:

Generate worksheet from homework.

---

# File Upload Endpoints

## Upload File

```http
POST /files/upload
```

Status: MVP  
Authentication: Required

Purpose:

Upload homework image, PDF, avatar, institution logo, or generated file.

Content-Type:

```text
multipart/form-data
```

---

## Get File Metadata

```http
GET /files/{fileId}
```

Status: MVP  
Authentication: Required

Purpose:

Get file metadata.

---

## Get Signed File URL

```http
GET /files/{fileId}/download-url
```

Status: V1  
Authentication: Required

Purpose:

Generate signed URL for authorized file access.

---

## Delete File

```http
DELETE /files/{fileId}
```

Status: V1  
Authentication: Required

Purpose:

Soft delete file asset.

---

# OCR Endpoints

## Create OCR Job

```http
POST /ocr/jobs
```

Status: MVP  
Authentication: Required  
Entitlement: ocr.extract

Purpose:

Start OCR processing for uploaded file.

Request:

```json
{
  "fileId": "uuid",
  "sourceType": "HOMEWORK",
  "sourceId": "uuid"
}
```

---

## Get OCR Job

```http
GET /ocr/jobs/{ocrJobId}
```

Status: MVP  
Authentication: Required

Purpose:

Get OCR processing status and result.

---

## Confirm OCR Result

```http
POST /ocr/jobs/{ocrJobId}/confirm
```

Status: V1  
Authentication: Required

Purpose:

Confirm or correct OCR extracted text.

---

# AI Tutor Endpoints

## Create Conversation

```http
POST /ai-tutor/conversations
```

Status: MVP  
Authentication: Required  
Roles: Student  
Entitlement: ai_tutor.chat

Purpose:

Create AI Tutor conversation.

---

## List Conversations

```http
GET /ai-tutor/conversations
```

Status: V1  
Authentication: Required  
Roles: Student

Purpose:

List student's AI Tutor conversations.

---

## Get Conversation

```http
GET /ai-tutor/conversations/{conversationId}
```

Status: MVP  
Authentication: Required

Purpose:

Get conversation with messages.

---

## Send Message

```http
POST /ai-tutor/conversations/{conversationId}/messages
```

Status: MVP  
Authentication: Required  
Entitlement: ai_tutor.chat

Purpose:

Send message to AI Tutor and receive response.

Request:

```json
{
  "message": "Can you explain this in simple words?",
  "context": {
    "sourceType": "HOMEWORK",
    "sourceId": "uuid"
  }
}
```

---

## Delete Conversation

```http
DELETE /ai-tutor/conversations/{conversationId}
```

Status: V1  
Authentication: Required

Purpose:

Soft delete conversation.

---

## Submit AI Feedback

```http
POST /ai-tutor/messages/{messageId}/feedback
```

Status: V1  
Authentication: Required

Purpose:

Submit feedback on AI Tutor response.

---

# Notes Endpoints

## Generate Notes

```http
POST /notes/generate
```

Status: MVP  
Authentication: Required  
Roles: Student  
Entitlement: notes.generate

Purpose:

Generate notes using AI.

---

## Create Note

```http
POST /notes
```

Status: MVP  
Authentication: Required  
Roles: Student

Purpose:

Create manual note.

---

## List Notes

```http
GET /notes
```

Status: MVP  
Authentication: Required  
Roles: Student

Purpose:

List notes owned by student.

---

## Get Note

```http
GET /notes/{noteId}
```

Status: MVP  
Authentication: Required

Purpose:

Get note details.

---

## Update Note

```http
PATCH /notes/{noteId}
```

Status: V1  
Authentication: Required

Purpose:

Update note.

---

## Delete Note

```http
DELETE /notes/{noteId}
```

Status: V1  
Authentication: Required

Purpose:

Soft delete note.

---

## Export Note

```http
POST /notes/{noteId}/export
```

Status: V2  
Authentication: Required  
Entitlement: notes.export

Purpose:

Export note as PDF, DOCX, Markdown, or TXT.

---

# Quiz Endpoints

## Generate Quiz

```http
POST /quizzes/generate
```

Status: MVP  
Authentication: Required  
Roles: Student  
Entitlement: quiz.generate

Purpose:

Generate quiz from topic, homework, note, or custom prompt.

---

## List Quizzes

```http
GET /quizzes
```

Status: MVP  
Authentication: Required

Purpose:

List quizzes visible to current user.

---

## Get Quiz

```http
GET /quizzes/{quizId}
```

Status: MVP  
Authentication: Required

Purpose:

Get quiz details and questions.

---

## Start Quiz Attempt

```http
POST /quizzes/{quizId}/attempts
```

Status: MVP  
Authentication: Required  
Roles: Student

Purpose:

Start quiz attempt.

---

## Get Quiz Attempt

```http
GET /quizzes/{quizId}/attempts/{attemptId}
```

Status: MVP  
Authentication: Required

Purpose:

Get attempt details.

---

## Submit Quiz Attempt

```http
POST /quizzes/{quizId}/attempts/{attemptId}/submit
```

Status: MVP  
Authentication: Required  
Roles: Student

Purpose:

Submit quiz attempt and calculate result.

---

## Retry Wrong Questions

```http
POST /quizzes/{quizId}/retry-wrong
```

Status: V1  
Authentication: Required  
Roles: Student

Purpose:

Create quiz from incorrect answers.

---

# Worksheet Endpoints

## Generate Worksheet

```http
POST /worksheets/generate
```

Status: MVP  
Authentication: Required  
Roles: Student  
Entitlement: worksheets.generate

Purpose:

Generate worksheet using AI.

---

## List Worksheets

```http
GET /worksheets
```

Status: MVP  
Authentication: Required

Purpose:

List worksheets visible to current user.

---

## Get Worksheet

```http
GET /worksheets/{worksheetId}
```

Status: MVP  
Authentication: Required

Purpose:

Get worksheet details.

---

## Delete Worksheet

```http
DELETE /worksheets/{worksheetId}
```

Status: V1  
Authentication: Required

Purpose:

Soft delete worksheet.

---

## Export Worksheet

```http
POST /worksheets/{worksheetId}/export
```

Status: V1  
Authentication: Required  
Entitlement: worksheets.export

Purpose:

Export worksheet as PDF.

---

# Study Planner Endpoints

## Generate Study Plan

```http
POST /study-plans/generate
```

Status: MVP  
Authentication: Required  
Roles: Student  
Entitlement: study_planner.generate

Purpose:

Generate personalized study plan.

---

## List Study Plans

```http
GET /study-plans
```

Status: MVP  
Authentication: Required  
Roles: Student

Purpose:

List study plans.

---

## Get Study Plan

```http
GET /study-plans/{studyPlanId}
```

Status: MVP  
Authentication: Required

Purpose:

Get study plan details.

---

## Update Study Plan

```http
PATCH /study-plans/{studyPlanId}
```

Status: V1  
Authentication: Required

Purpose:

Update study plan metadata.

---

## Complete Study Task

```http
POST /study-plans/{studyPlanId}/tasks/{taskId}/complete
```

Status: MVP  
Authentication: Required  
Roles: Student

Purpose:

Mark study task completed.

---

## Regenerate Study Plan

```http
POST /study-plans/{studyPlanId}/regenerate
```

Status: V1  
Authentication: Required  
Entitlement: study_planner.generate

Purpose:

Regenerate plan based on updated progress.

---

# Analytics Endpoints

## Get Student Analytics

```http
GET /analytics/student
```

Status: MVP  
Authentication: Required  
Roles: Student

Purpose:

Get current student's analytics.

---

## Get Learning Mastery

```http
GET /analytics/mastery
```

Status: MVP  
Authentication: Required  
Roles: Student

Purpose:

Get learning mastery scores.

---

## Get Recommendations

```http
GET /analytics/recommendations
```

Status: MVP  
Authentication: Required  
Roles: Student

Purpose:

Get personalized learning recommendations.

---

## Get Parent Child Analytics

```http
GET /analytics/children/{studentUserId}
```

Status: V1  
Authentication: Required  
Roles: Parent

Purpose:

Get analytics for linked child.

---

## Get Teacher Class Analytics

```http
GET /analytics/classes/{sectionId}
```

Status: V2  
Authentication: Required  
Roles: Teacher

Purpose:

Get analytics for assigned class or section.

---

## Get Institution Analytics

```http
GET /analytics/institutions/{institutionId}
```

Status: V1  
Authentication: Required  
Roles: Institution Administrator, Platform Administrator

Purpose:

Get institution analytics.

---

## Get Organization Analytics

```http
GET /analytics/organizations/{organizationId}
```

Status: Enterprise  
Authentication: Required  
Roles: Organization Owner, Organization Administrator, Platform Administrator

Purpose:

Get organization-level analytics.

MVP Note:

Deferred.

---

# Dashboard Endpoints

## Get Student Dashboard

```http
GET /dashboard/student
```

Status: MVP  
Authentication: Required  
Roles: Student

Purpose:

Get student dashboard.

---

## Get Parent Dashboard

```http
GET /dashboard/parent
```

Status: V1  
Authentication: Required  
Roles: Parent

Purpose:

Get parent dashboard.

---

## Get Teacher Dashboard

```http
GET /dashboard/teacher
```

Status: V1  
Authentication: Required  
Roles: Teacher

Purpose:

Get teacher dashboard.

---

## Get Institution Dashboard

```http
GET /dashboard/institution/{institutionId}
```

Status: V1  
Authentication: Required  
Roles: Institution Administrator

Purpose:

Get institution dashboard.

---

## Get Organization Dashboard

```http
GET /dashboard/organization/{organizationId}
```

Status: Enterprise  
Authentication: Required  
Roles: Organization Owner, Organization Administrator

Purpose:

Get organization dashboard.

---

## Get Platform Admin Dashboard

```http
GET /dashboard/admin
```

Status: V1  
Authentication: Required  
Roles: Platform Administrator

Purpose:

Get platform admin dashboard.

---

# Notification Endpoints

## List Notifications

```http
GET /notifications
```

Status: MVP  
Authentication: Required

Purpose:

List notifications for current user.

---

## Mark Notification Read

```http
PATCH /notifications/{notificationId}/read
```

Status: MVP  
Authentication: Required

Purpose:

Mark notification as read.

---

## Mark All Notifications Read

```http
PATCH /notifications/read-all
```

Status: V1  
Authentication: Required

Purpose:

Mark all notifications as read.

---

## Delete Notification

```http
DELETE /notifications/{notificationId}
```

Status: V1  
Authentication: Required

Purpose:

Delete or hide notification.

---

## Get Notification Preferences

```http
GET /notification-preferences
```

Status: V1  
Authentication: Required

Purpose:

Get notification preferences.

---

## Update Notification Preferences

```http
PATCH /notification-preferences
```

Status: V1  
Authentication: Required

Purpose:

Update notification preferences.

---

# Commerce and Subscription Endpoints

## List Plans

```http
GET /plans
```

Status: MVP  
Authentication: Public or Required

Purpose:

List available subscription plans.

---

## List Features

```http
GET /features
```

Status: MVP  
Authentication: Required

Purpose:

List feature definitions.

---

## Get Current Entitlements

```http
GET /entitlements
```

Status: MVP  
Authentication: Required

Purpose:

Return current user's feature access.

---

## Validate Feature Access

```http
POST /entitlements/validate
```

Status: MVP  
Authentication: Required

Purpose:

Validate access to a feature.

Request:

```json
{
  "featureCode": "homework.solve"
}
```

---

## Get Usage

```http
GET /usage
```

Status: MVP  
Authentication: Required

Purpose:

Return usage counters and limits.

---

## Create Subscription

```http
POST /subscriptions
```

Status: MVP  
Authentication: Required

Purpose:

Create subscription.

---

## Get Current Subscription

```http
GET /subscriptions/current
```

Status: MVP  
Authentication: Required

Purpose:

Get current subscription.

---

## Change Subscription

```http
PATCH /subscriptions/{subscriptionId}
```

Status: V1  
Authentication: Required

Purpose:

Upgrade or downgrade subscription.

---

## Cancel Subscription

```http
POST /subscriptions/{subscriptionId}/cancel
```

Status: V1  
Authentication: Required

Purpose:

Cancel subscription.

---

## Resume Subscription

```http
POST /subscriptions/{subscriptionId}/resume
```

Status: V1  
Authentication: Required

Purpose:

Resume cancelled subscription where allowed.

---

## Apply Coupon

```http
POST /subscriptions/coupons/apply
```

Status: V2  
Authentication: Required

Purpose:

Apply coupon to subscription or checkout.

---

# Payment Endpoints

## Create Payment

```http
POST /payments
```

Status: MVP  
Authentication: Required

Purpose:

Create payment request through gateway adapter.

Requires idempotency key.

---

## Confirm Payment

```http
POST /payments/{paymentId}/confirm
```

Status: MVP  
Authentication: Required

Purpose:

Confirm payment after gateway authorization.

---

## List Payments

```http
GET /payments
```

Status: V1  
Authentication: Required

Purpose:

List payments for current user or authorized scope.

---

## Get Payment

```http
GET /payments/{paymentId}
```

Status: V1  
Authentication: Required

Purpose:

Get payment details.

---

## Request Refund

```http
POST /payments/{paymentId}/refund
```

Status: V2  
Authentication: Required  
Roles: Platform Administrator, Finance Administrator

Purpose:

Create refund request.

---

## List Invoices

```http
GET /invoices
```

Status: V1  
Authentication: Required

Purpose:

List invoices.

---

## Get Invoice

```http
GET /invoices/{invoiceId}
```

Status: V1  
Authentication: Required

Purpose:

Get invoice details.

---

## Download Invoice

```http
GET /invoices/{invoiceId}/download
```

Status: V1  
Authentication: Required

Purpose:

Download invoice PDF.

---

# Payment Webhook Endpoints

## Razorpay Webhook

```http
POST /webhooks/payments/razorpay
```

Status: MVP  
Authentication: Webhook signature

Purpose:

Receive Razorpay payment events.

---

## Stripe Webhook

```http
POST /webhooks/payments/stripe
```

Status: Future  
Authentication: Webhook signature

Purpose:

Receive Stripe payment events.

---

# Teacher Workspace Endpoints

## Get Teacher Dashboard

```http
GET /teachers/dashboard
```

Status: V1  
Authentication: Required  
Roles: Teacher

Purpose:

Get teacher dashboard.

---

## List Teacher Classes

```http
GET /teachers/classes
```

Status: V1  
Authentication: Required  
Roles: Teacher

Purpose:

List assigned classes or sections.

---

## List Teacher Students

```http
GET /teachers/students
```

Status: V1  
Authentication: Required  
Roles: Teacher

Purpose:

List students assigned to teacher.

---

## Create Assignment

```http
POST /teachers/assignments
```

Status: V2  
Authentication: Required  
Roles: Teacher

Purpose:

Create assignment.

---

## List Assignments

```http
GET /teachers/assignments
```

Status: V2  
Authentication: Required  
Roles: Teacher

Purpose:

List teacher assignments.

---

## Get Assignment

```http
GET /teachers/assignments/{assignmentId}
```

Status: V2  
Authentication: Required  
Roles: Teacher

Purpose:

Get assignment details.

---

## Generate Teacher Quiz

```http
POST /teachers/quizzes/generate
```

Status: V2  
Authentication: Required  
Roles: Teacher  
Entitlement: quiz.generate

Purpose:

Generate quiz for class or students.

---

## Generate Teacher Worksheet

```http
POST /teachers/worksheets/generate
```

Status: V2  
Authentication: Required  
Roles: Teacher  
Entitlement: worksheets.generate

Purpose:

Generate worksheet for class or students.

---

# Parent Workspace Endpoints

## Get Parent Dashboard

```http
GET /parents/dashboard
```

Status: V1  
Authentication: Required  
Roles: Parent

Purpose:

Get parent dashboard.

---

## List Linked Children

```http
GET /parents/children
```

Status: MVP  
Authentication: Required  
Roles: Parent

Purpose:

List linked children.

---

## Link Child

```http
POST /parents/children/link
```

Status: MVP  
Authentication: Required  
Roles: Parent

Purpose:

Request or confirm child link.

---

## Unlink Child

```http
DELETE /parents/children/{studentUserId}
```

Status: V1  
Authentication: Required  
Roles: Parent

Purpose:

Remove parent-child relationship.

---

## Get Child Report

```http
GET /parents/children/{studentUserId}/report
```

Status: V1  
Authentication: Required  
Roles: Parent

Purpose:

Get child learning report.

---

# Admin Endpoints

## Get Admin Dashboard

```http
GET /admin/dashboard
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator

Purpose:

Get admin dashboard.

---

## Admin List Users

```http
GET /admin/users
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator, Support Agent

Purpose:

Search and list users.

---

## Admin Get User

```http
GET /admin/users/{userId}
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator, Support Agent

Purpose:

Get user details.

---

## Admin Update User

```http
PATCH /admin/users/{userId}
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator

Purpose:

Update user status or administrative fields.

---

## Admin Suspend User

```http
POST /admin/users/{userId}/suspend
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator

Purpose:

Suspend user account.

---

## Admin Reactivate User

```http
POST /admin/users/{userId}/reactivate
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator

Purpose:

Reactivate suspended user.

---

## Admin List Organizations

```http
GET /admin/organizations
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator

Purpose:

List organizations.

---

## Admin Create Organization

```http
POST /admin/organizations
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator

Purpose:

Create organization.

---

## Admin List Institutions

```http
GET /admin/institutions
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator

Purpose:

List institutions.

---

## Admin Create Institution

```http
POST /admin/institutions
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator

Purpose:

Create institution.

---

## Admin List Subscriptions

```http
GET /admin/subscriptions
```

Status: V1  
Authentication: Required  
Roles: Platform Administrator, Finance Administrator

Purpose:

List subscriptions.

---

## Admin List Payments

```http
GET /admin/payments
```

Status: V1  
Authentication: Required  
Roles: Platform Administrator, Finance Administrator

Purpose:

List payments.

---

## Admin AI Usage

```http
GET /admin/ai-usage
```

Status: V1  
Authentication: Required  
Roles: Platform Administrator, AI Operations Administrator

Purpose:

View AI usage and cost.

---

## Admin Feature Flags

```http
GET /admin/feature-flags
```

Status: V1  
Authentication: Required  
Roles: Platform Administrator

Purpose:

List feature flags.

---

## Update Feature Flag

```http
PATCH /admin/feature-flags/{featureFlagId}
```

Status: V1  
Authentication: Required  
Roles: Platform Administrator

Purpose:

Update feature flag.

---

## Admin Audit Logs

```http
GET /admin/audit-logs
```

Status: MVP  
Authentication: Required  
Roles: Platform Administrator, Security Administrator

Purpose:

View audit logs.

---

# Support Endpoints

## List Support Tickets

```http
GET /support/tickets
```

Status: V2  
Authentication: Required  
Roles: Support Agent, Platform Administrator

Purpose:

List support tickets.

---

## Create Support Ticket

```http
POST /support/tickets
```

Status: V2  
Authentication: Required

Purpose:

Create support ticket.

---

## Get Support Ticket

```http
GET /support/tickets/{ticketId}
```

Status: V2  
Authentication: Required

Purpose:

Get support ticket.

---

## Update Support Ticket

```http
PATCH /support/tickets/{ticketId}
```

Status: V2  
Authentication: Required  
Roles: Support Agent, Platform Administrator

Purpose:

Update support ticket.

---

# Job Endpoints

## Get Job Status

```http
GET /jobs/{jobId}
```

Status: V1  
Authentication: Required

Purpose:

Get background job status.

Used for:

- OCR
- AI generation
- Report generation
- Export generation

---

# Health Endpoints

## API Health

```http
GET /health
```

Status: MVP  
Authentication: Public or internal only

Purpose:

Basic API health check.

---

## Readiness Check

```http
GET /health/ready
```

Status: MVP  
Authentication: Internal

Purpose:

Check database, Redis, and dependency readiness.

---

## Liveness Check

```http
GET /health/live
```

Status: MVP  
Authentication: Internal

Purpose:

Container or process liveness check.

---

# Endpoint Security Matrix

| Endpoint Group | Authentication | Authorization | Tenant Scope | Entitlement |
|---|---|---|---|---|
| Auth | Partial | No | No | No |
| Users | Yes | Ownership | User | No |
| Organizations | Yes | Role + Permission | Organization | Plan-dependent |
| Institutions | Yes | Role + Permission | Institution | Plan-dependent |
| Academic | Yes | Role + Permission | Institution | No |
| Homework | Yes | Role + Ownership | Student / Institution | Yes |
| OCR | Yes | Role + Ownership | Student / Institution | Yes |
| AI Tutor | Yes | Role + Ownership | Student / Institution | Yes |
| Notes | Yes | Ownership | Student / Institution | Yes |
| Quiz | Yes | Ownership / Assignment | Student / Institution | Yes |
| Worksheets | Yes | Ownership / Assignment | Student / Institution | Yes |
| Study Planner | Yes | Ownership | Student | Yes |
| Analytics | Yes | Role + Scope | User / Institution / Organization | Plan-dependent |
| Notifications | Yes | Ownership | User | No |
| Commerce | Yes | Ownership / Scope | User / Institution / Organization | No |
| Payments | Yes | Ownership / Finance Role | User / Institution / Organization | No |
| Admin | Yes | Admin Role | Platform / Scoped | No |

---

# MVP Endpoint Set

The MVP should implement at minimum:

```text
POST /auth/register
POST /auth/login
POST /auth/logout
POST /auth/refresh
GET  /auth/me

GET  /users/me
PATCH /users/me

POST /organizations
GET  /organizations/{organizationId}

POST /institutions
GET  /institutions/{institutionId}
PATCH /institutions/{institutionId}

GET  /institutions/{institutionId}/grades
POST /institutions/{institutionId}/grades
GET  /subjects
GET  /subjects/{subjectId}/chapters
GET  /chapters/{chapterId}/topics

POST /files/upload

POST /homework
GET  /homework
GET  /homework/{homeworkId}
POST /homework/{homeworkId}/solve

POST /ocr/jobs
GET  /ocr/jobs/{ocrJobId}

POST /ai-tutor/conversations
GET  /ai-tutor/conversations/{conversationId}
POST /ai-tutor/conversations/{conversationId}/messages

POST /notes/generate
GET  /notes
GET  /notes/{noteId}

POST /quizzes/generate
GET  /quizzes/{quizId}
POST /quizzes/{quizId}/attempts
POST /quizzes/{quizId}/attempts/{attemptId}/submit

POST /worksheets/generate
GET  /worksheets/{worksheetId}

POST /study-plans/generate
GET  /study-plans
POST /study-plans/{studyPlanId}/tasks/{taskId}/complete

GET /dashboard/student
GET /analytics/student
GET /analytics/mastery
GET /analytics/recommendations

GET /notifications
PATCH /notifications/{notificationId}/read

GET /plans
GET /entitlements
GET /usage
POST /subscriptions

POST /payments
POST /payments/{paymentId}/confirm

GET /admin/dashboard
GET /admin/users
GET /admin/organizations
GET /admin/institutions
GET /admin/audit-logs

GET /health
```

---

# Deferred Endpoint Groups

The following may be deferred from MVP:

- Enterprise organization dashboard
- Multi-institution management UI APIs
- Enterprise SSO APIs
- Public partner APIs
- Marketplace APIs
- Full support ticket APIs
- Advanced reports APIs
- White-label branding APIs
- Advanced teacher assignment APIs
- Offline sync APIs
- Mobile push token APIs

---

# Endpoint Review Checklist

Before implementing an endpoint, confirm:

- Endpoint follows REST naming.
- Endpoint uses `/api/v1`.
- Endpoint avoids school-only terminology.
- Organization context is handled where applicable.
- Institution context is handled where applicable.
- Authentication is applied where required.
- Authorization is enforced server-side.
- Entitlement is checked where required.
- Validation is defined.
- Error codes are defined.
- Response format is standard.
- OpenAPI documentation exists.
- Tests are planned.
- Analytics event is emitted where required.
- Audit log is created where required.

---

# Related Documents

- API_GUIDELINES.md
- AUTHENTICATION.md
- ERROR_CODES.md
- DATABASE_SCHEMA.md
- SECURITY.md
- PRODUCT_REQUIREMENTS.md
- FEATURES.md