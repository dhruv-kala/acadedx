# Acadedx User Stories

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Product Management  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the major user stories for Acadedx.

User stories describe product behavior from the perspective of users and stakeholders. They help Product, Design, Engineering, QA, AI Engineering, and AI Coding Agents understand what users need to accomplish and why.

Every feature, API, screen, workflow, and test case should be traceable to one or more user stories.

---

# Story Format

User stories follow this format:

```text
As a <user role>,
I want <capability>,
so that <outcome or benefit>.
```

---

# Platform Model

Acadedx follows the platform hierarchy below:

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

The MVP primarily supports schools as the first Institution type.

Future versions will support multiple institutions per organization and additional institution types such as academies, coaching institutes, colleges, universities, training centers, and online academies.

---

# User Roles

This document covers stories for:

- Guest
- Student
- Parent
- Teacher
- Institution Administrator
- Organization Owner
- Organization Administrator
- Platform Administrator
- Support Agent

---

# Priority Levels

| Priority | Meaning |
|---|---|
| P0 | Required for MVP |
| P1 | Required for early release |
| P2 | Growth feature |
| P3 | Future or enterprise feature |

---

# Guest User Stories

## GUEST-001 — View Product Information

**As a guest, I want to understand what Acadedx offers, so that I can decide whether to register.**

Priority: P0  
Phase: MVP

Acceptance Notes:

- Guest can view landing page.
- Guest can view core features.
- Guest can view pricing.
- Guest can start registration.

---

## GUEST-002 — View Pricing

**As a guest, I want to compare free and paid plans, so that I can choose the right plan.**

Priority: P0  
Phase: MVP

---

## GUEST-003 — Register Account

**As a guest, I want to create an account, so that I can start using Acadedx.**

Priority: P0  
Phase: MVP

---

# Student User Stories

## Authentication and Profile

### STUD-001 — Register as Student

**As a student, I want to create an account, so that I can save my learning progress.**

Priority: P0  
Phase: MVP

---

### STUD-002 — Log In

**As a student, I want to log in securely, so that I can access my dashboard and learning history.**

Priority: P0  
Phase: MVP

---

### STUD-003 — Complete Profile

**As a student, I want to set my grade, subjects, language, and study goals, so that Acadedx can personalize my learning experience.**

Priority: P0  
Phase: MVP

---

### STUD-004 — Manage Learning Preferences

**As a student, I want to manage my learning preferences, so that explanations and recommendations match my needs.**

Priority: P1  
Phase: V1

---

## Dashboard

### STUD-010 — View Student Dashboard

**As a student, I want to see my learning dashboard, so that I know what to study next.**

Priority: P0  
Phase: MVP

---

### STUD-011 — View Daily Recommendations

**As a student, I want AI to recommend what I should study today, so that I can focus on the most important learning activity.**

Priority: P1  
Phase: V1

---

### STUD-012 — View Learning Progress

**As a student, I want to see my study streak, mastery score, weak topics, and recent activity, so that I understand my progress.**

Priority: P0  
Phase: MVP

---

## Homework

### STUD-020 — Type Homework Question

**As a student, I want to type a homework question, so that I can receive a step-by-step explanation.**

Priority: P0  
Phase: MVP

---

### STUD-021 — Upload Homework Image

**As a student, I want to upload a photo of my homework, so that Acadedx can extract and solve the question.**

Priority: P0  
Phase: MVP

---

### STUD-022 — Capture Homework Using Camera

**As a student, I want to capture a homework question using my camera, so that I can quickly get help without typing.**

Priority: P0  
Phase: MVP

---

### STUD-023 — Upload Homework PDF

**As a student, I want to upload a homework PDF, so that Acadedx can process multi-page assignments.**

Priority: P1  
Phase: V1

---

### STUD-024 — View Step-by-Step Solution

**As a student, I want to see a step-by-step explanation, so that I understand how the answer was reached.**

Priority: P0  
Phase: MVP

---

### STUD-025 — View Common Mistakes

**As a student, I want to see common mistakes, so that I can avoid repeating them.**

Priority: P1  
Phase: V1

---

### STUD-026 — Save Homework History

**As a student, I want my solved homework to be saved, so that I can revise it later.**

Priority: P1  
Phase: V1

---

## OCR

### STUD-030 — Review Extracted Text

**As a student, I want to review OCR extracted text before AI solves it, so that I can correct mistakes.**

Priority: P1  
Phase: V1

---

### STUD-031 — Correct OCR Output

**As a student, I want to edit incorrectly extracted text, so that the AI receives the correct question.**

Priority: P1  
Phase: V1

---

## AI Tutor

### STUD-040 — Ask AI Tutor

**As a student, I want to ask follow-up questions, so that I can understand the concept fully.**

Priority: P0  
Phase: MVP

---

### STUD-041 — Ask for Simpler Explanation

**As a student, I want AI to explain in simpler language, so that I can understand difficult topics.**

Priority: P0  
Phase: MVP

---

### STUD-042 — Ask for Examples

**As a student, I want AI to give examples, so that I can understand how the concept is used.**

Priority: P1  
Phase: V1

---

### STUD-043 — Continue from Homework Context

**As a student, I want the AI Tutor to understand my homework context, so that I do not need to repeat the question again.**

Priority: P0  
Phase: MVP

---

### STUD-044 — View Conversation History

**As a student, I want to view past AI Tutor conversations, so that I can revise previous explanations.**

Priority: P1  
Phase: V1

---

## Notes

### STUD-050 — Generate Notes

**As a student, I want to generate notes from a topic or explanation, so that I can revise faster.**

Priority: P0  
Phase: MVP

---

### STUD-051 — Generate Notes from Homework

**As a student, I want to generate notes from solved homework, so that I can remember the related concept.**

Priority: P1  
Phase: V1

---

### STUD-052 — Edit Notes

**As a student, I want to edit generated notes, so that I can add my own understanding.**

Priority: P1  
Phase: V1

---

### STUD-053 — Search Notes

**As a student, I want to search my notes, so that I can quickly find revision material.**

Priority: P1  
Phase: V1

---

## Quiz

### STUD-060 — Generate Quiz

**As a student, I want to generate a quiz from a topic, so that I can test my understanding.**

Priority: P0  
Phase: MVP

---

### STUD-061 — Attempt Quiz

**As a student, I want to attempt a quiz, so that I can assess my learning.**

Priority: P0  
Phase: MVP

---

### STUD-062 — View Quiz Results

**As a student, I want to see my quiz score and explanations, so that I understand my mistakes.**

Priority: P0  
Phase: MVP

---

### STUD-063 — Retry Wrong Questions

**As a student, I want to retry questions I answered incorrectly, so that I can improve weak areas.**

Priority: P1  
Phase: V1

---

## Worksheets

### STUD-070 — Generate Worksheet

**As a student, I want to generate a worksheet, so that I can practice more questions.**

Priority: P0  
Phase: MVP

---

### STUD-071 — Generate Worksheet from Weak Topics

**As a student, I want worksheets based on weak topics, so that I can practice exactly where I need improvement.**

Priority: P1  
Phase: V1

---

### STUD-072 — Download Worksheet

**As a student, I want to download worksheets as PDF, so that I can practice offline.**

Priority: P1  
Phase: V1

---

## Study Planner

### STUD-080 — Generate Study Plan

**As a student, I want AI to generate a study plan, so that I can prepare consistently.**

Priority: P0  
Phase: MVP

---

### STUD-081 — Complete Study Tasks

**As a student, I want to mark study tasks as complete, so that my progress is tracked.**

Priority: P0  
Phase: MVP

---

### STUD-082 — Prepare for Exams

**As a student, I want to create an exam preparation plan, so that I can revise before exams.**

Priority: P1  
Phase: V1

---

## Analytics

### STUD-090 — View Learning Mastery

**As a student, I want to see my Learning Mastery Score, so that I understand how well I know each subject.**

Priority: P0  
Phase: MVP

---

### STUD-091 — View Weak Topics

**As a student, I want to see weak topics, so that I know what to practice next.**

Priority: P0  
Phase: MVP

---

# Parent User Stories

## PARENT-001 — Register as Parent

**As a parent, I want to create an account, so that I can monitor my child's learning progress.**

Priority: P0  
Phase: MVP

---

## PARENT-002 — Link Child

**As a parent, I want to link my child to my account, so that I can access their learning reports.**

Priority: P0  
Phase: MVP

---

## PARENT-003 — View Child Dashboard

**As a parent, I want to view my child's progress dashboard, so that I can understand their learning status.**

Priority: P1  
Phase: V1

---

## PARENT-004 — Receive Weekly Report

**As a parent, I want to receive a weekly learning report, so that I know how my child is progressing.**

Priority: P1  
Phase: V1

---

## PARENT-005 — View Weak Topic Recommendations

**As a parent, I want to see recommended actions for my child's weak topics, so that I can support them at home.**

Priority: P1  
Phase: V1

---

## PARENT-006 — Manage Family Subscription

**As a parent, I want to manage a family subscription, so that multiple children can use Acadedx under one plan.**

Priority: P1  
Phase: V1

---

# Teacher User Stories

## TEACH-001 — Register as Teacher

**As a teacher, I want to create a teacher account, so that I can use Acadedx teaching tools.**

Priority: P0  
Phase: MVP

---

## TEACH-002 — Complete Teacher Profile

**As a teacher, I want to set my subjects and institution, so that Acadedx can personalize my workspace.**

Priority: P1  
Phase: V1

---

## TEACH-003 — View Teacher Dashboard

**As a teacher, I want to view my dashboard, so that I can see classes, assignments, and student alerts.**

Priority: P1  
Phase: V1

---

## TEACH-004 — Create Assignment

**As a teacher, I want to create assignments, so that students can practice assigned topics.**

Priority: P1  
Phase: V1

---

## TEACH-005 — Generate Quiz

**As a teacher, I want AI to generate quizzes, so that I can assess students faster.**

Priority: P2  
Phase: V2

---

## TEACH-006 — Generate Worksheet

**As a teacher, I want AI to generate worksheets, so that I can provide differentiated practice.**

Priority: P2  
Phase: V2

---

## TEACH-007 — View Student Analytics

**As a teacher, I want to view student performance analytics, so that I can identify who needs help.**

Priority: P2  
Phase: V2

---

## TEACH-008 — Use AI Lesson Assistant

**As a teacher, I want AI to help create lesson plans, so that I save preparation time.**

Priority: P2  
Phase: V2

---

# Institution Administrator User Stories

## INSTADMIN-001 — Manage Institution Profile

**As an institution administrator, I want to manage institution details, so that the institution profile remains accurate.**

Priority: P0  
Phase: MVP

---

## INSTADMIN-002 — Manage Academic Structure

**As an institution administrator, I want to manage academic sessions, grades, sections, and subjects, so that users can be organized correctly.**

Priority: P0  
Phase: MVP

---

## INSTADMIN-003 — Invite Teachers

**As an institution administrator, I want to invite teachers, so that they can use Acadedx for their classes.**

Priority: P1  
Phase: V1

---

## INSTADMIN-004 — Manage Students

**As an institution administrator, I want to manage students, so that learners are assigned to the correct grade and section.**

Priority: P1  
Phase: V1

---

## INSTADMIN-005 — View Institution Dashboard

**As an institution administrator, I want to view institution usage and learning analytics, so that I understand adoption and performance.**

Priority: P1  
Phase: V1

---

## INSTADMIN-006 — Export Reports

**As an institution administrator, I want to export reports, so that I can share progress with leadership.**

Priority: P2  
Phase: V2

---

# Organization Owner User Stories

## ORGOWNER-001 — Own Organization Account

**As an organization owner, I want to own an organization account, so that billing and institutions are linked to my organization.**

Priority: P0  
Phase: MVP

---

## ORGOWNER-002 — Manage Organization Billing

**As an organization owner, I want to manage organization billing, so that subscriptions and invoices are centralized.**

Priority: P2  
Phase: V2

---

## ORGOWNER-003 — View Organization Usage

**As an organization owner, I want to view organization-level usage, so that I can understand adoption across my institution.**

Priority: P3  
Phase: Enterprise

---

## ORGOWNER-004 — Manage Multiple Institutions

**As an organization owner, I want to manage multiple institutions, so that my education group can operate from one platform.**

Priority: P3  
Phase: Enterprise

---

# Organization Administrator User Stories

## ORGADMIN-001 — Manage Organization Settings

**As an organization administrator, I want to manage organization settings, so that the organization is configured correctly.**

Priority: P3  
Phase: Enterprise

---

## ORGADMIN-002 — Manage Institution Administrators

**As an organization administrator, I want to manage institution administrators, so that each institution has responsible operators.**

Priority: P3  
Phase: Enterprise

---

## ORGADMIN-003 — View Organization Dashboard

**As an organization administrator, I want to view a dashboard across institutions, so that I can compare performance and usage.**

Priority: P3  
Phase: Enterprise

---

# Platform Administrator User Stories

## ADMIN-001 — Manage Users

**As a platform administrator, I want to manage users, so that DGIS can operate and support the platform.**

Priority: P0  
Phase: MVP

---

## ADMIN-002 — Manage Organizations

**As a platform administrator, I want to manage organizations, so that business accounts are configured correctly.**

Priority: P0  
Phase: MVP

---

## ADMIN-003 — Manage Institutions

**As a platform administrator, I want to manage institutions, so that schools and future institution types are correctly onboarded.**

Priority: P0  
Phase: MVP

---

## ADMIN-004 — Monitor AI Usage

**As a platform administrator, I want to monitor AI usage, so that DGIS can control cost and quality.**

Priority: P1  
Phase: V1

---

## ADMIN-005 — Manage Subscriptions

**As a platform administrator, I want to manage subscriptions, so that billing issues can be resolved.**

Priority: P1  
Phase: V1

---

## ADMIN-006 — View Payments

**As a platform administrator, I want to view payment history, so that finance and support teams can assist customers.**

Priority: P1  
Phase: V1

---

## ADMIN-007 — Manage Feature Flags

**As a platform administrator, I want to manage feature flags, so that DGIS can safely roll out features.**

Priority: P1  
Phase: V1

---

## ADMIN-008 — View Audit Logs

**As a platform administrator, I want to view audit logs, so that security and compliance events can be reviewed.**

Priority: P0  
Phase: MVP

---

# Support Agent User Stories

## SUPPORT-001 — Search User

**As a support agent, I want to search users, so that I can help resolve issues.**

Priority: P2  
Phase: V2

---

## SUPPORT-002 — View Support Context

**As a support agent, I want limited access to user context, so that I can troubleshoot without violating privacy.**

Priority: P2  
Phase: V2

---

## SUPPORT-003 — Create Support Ticket

**As a support agent, I want to create and manage support tickets, so that customer issues are tracked.**

Priority: P2  
Phase: V2

---

# Commerce User Stories

## COM-001 — View Plans

**As a user, I want to view available plans, so that I can choose the right subscription.**

Priority: P0  
Phase: MVP

---

## COM-002 — Upgrade Plan

**As a user, I want to upgrade my plan, so that I can access premium features.**

Priority: P0  
Phase: MVP

---

## COM-003 — View Usage Limits

**As a user, I want to view my usage limits, so that I understand remaining access.**

Priority: P0  
Phase: MVP

---

## COM-004 — Download Invoice

**As a paying user, I want to download invoices, so that I can keep billing records.**

Priority: P1  
Phase: V1

---

## COM-005 — Apply Coupon

**As a user, I want to apply a coupon, so that I can receive a discount.**

Priority: P2  
Phase: V2

---

# Notification User Stories

## NOTIF-001 — Receive Study Reminder

**As a student, I want to receive study reminders, so that I can stay consistent.**

Priority: P1  
Phase: V1

---

## NOTIF-002 — Receive Homework Completion Alert

**As a student, I want to receive a notification when homework processing is complete, so that I can view the solution.**

Priority: P0  
Phase: MVP

---

## NOTIF-003 — Receive Parent Report

**As a parent, I want to receive weekly reports, so that I can monitor my child's progress.**

Priority: P1  
Phase: V1

---

## NOTIF-004 — Receive Security Alert

**As a user, I want to receive security alerts, so that I know when important account activity occurs.**

Priority: P0  
Phase: MVP

---

# Cross-Cutting User Stories

## CROSS-001 — Role-Based Access

**As the platform, I want users to access only the data they are authorized to see, so that privacy and tenant isolation are protected.**

Priority: P0  
Phase: MVP

---

## CROSS-002 — Organization and Institution Context

**As the platform, I want every relevant action to be scoped to organization and institution context, so that future multi-institution support is possible.**

Priority: P0  
Phase: MVP

---

## CROSS-003 — Entitlement Validation

**As the platform, I want feature access to be validated through entitlements, so that subscription logic remains flexible.**

Priority: P0  
Phase: MVP

---

## CROSS-004 — AI Cost Tracking

**As DGIS, I want every AI request to track provider, model, tokens, and cost, so that AI economics remain sustainable.**

Priority: P0  
Phase: MVP

---

## CROSS-005 — Audit Trail

**As DGIS, I want sensitive actions to be audited, so that security and compliance can be reviewed.**

Priority: P0  
Phase: MVP

---

# MVP User Story Set

The MVP must include at minimum:

- GUEST-001
- GUEST-002
- GUEST-003
- STUD-001
- STUD-002
- STUD-003
- STUD-010
- STUD-020
- STUD-021
- STUD-022
- STUD-024
- STUD-040
- STUD-041
- STUD-043
- STUD-050
- STUD-060
- STUD-061
- STUD-062
- STUD-070
- STUD-080
- STUD-081
- STUD-090
- STUD-091
- PARENT-001
- PARENT-002
- TEACH-001
- INSTADMIN-001
- INSTADMIN-002
- ORGOWNER-001
- ADMIN-001
- ADMIN-002
- ADMIN-003
- ADMIN-008
- COM-001
- COM-002
- COM-003
- NOTIF-002
- NOTIF-004
- CROSS-001
- CROSS-002
- CROSS-003
- CROSS-004
- CROSS-005

---

# Traceability Rules

Every implementation task must reference:

- User Story ID
- Feature ID
- Requirement
- Acceptance Criteria
- API Endpoint
- Database Entity
- Permission Rule
- Analytics Event

---

# Product Manager Notes

The user story set intentionally includes future organization and institution stories, but not all of them are MVP scope.

The MVP should implement the organization and institution foundation in the backend and database, while keeping user-facing enterprise workflows minimal.

Do not build multi-institution UI during MVP unless explicitly approved.

---

# Related Documents

- PRD.md
- PRODUCT_REQUIREMENTS.md
- FEATURES.md
- ACCEPTANCE_CRITERIA.md
- PRODUCT_PRINCIPLES.md
- SYSTEM_ARCHITECTURE.md
- DATABASE_SCHEMA.md