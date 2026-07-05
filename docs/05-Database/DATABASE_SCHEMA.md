# Database Schema

**Document Version:** 1.0.0

**Status:** Approved

**Owner:** Solution Architecture

**Database:** PostgreSQL

**ORM:** Prisma

**Last Updated:** July 2026

---

# Purpose

This document defines the logical database schema for the Acadedx platform.

The schema is designed to support:

- AI-powered learning
- Multi-school architecture
- Student, Parent, Teacher, and Administrator roles
- Subscription management
- Homework solving
- AI tutoring
- Quiz generation
- Notes generation
- Worksheets
- Analytics
- Notifications

---

# Database Principles

The database must be:

- Highly normalized
- Scalable
- Secure
- Auditable
- Cloud-ready
- Multi-tenant capable

---

# Primary Entities

## Identity

- Users
- Roles
- Permissions
- Sessions

---

## Education

- Schools
- Classes
- Subjects
- Chapters
- Topics

---

## Learning

- Homework
- Homework Solutions
- AI Chats
- Notes
- Worksheets
- Quizzes
- Quiz Attempts
- Study Plans

---

## Subscription

- Plans
- Subscriptions
- Payments
- Invoices

---

## Communication

- Notifications
- Emails
- Push Notifications

---

## Analytics

- Activity Logs
- Learning Progress
- AI Usage
- Performance Metrics

---

# Core Tables

---

## Users

Stores every user of the platform.

Fields

- id (UUID)
- firstName
- lastName
- email
- phone
- passwordHash
- profileImage
- roleId
- schoolId
- isVerified
- status
- createdAt
- updatedAt

---

## Roles

Fields

- id
- name
- description

Example Roles

- Platform Admin
- School Admin
- Teacher
- Parent
- Student

---

## Permissions

Stores application permissions.

Examples

- homework.read
- homework.write
- ai.chat
- users.manage
- schools.manage

---

## RolePermissions

Maps permissions to roles.

---

## Schools

Fields

- id
- name
- address
- city
- state
- country
- contactEmail
- contactPhone
- logo
- website
- status

---

## Classes

Example

Class 7

Class 8

Class 9

Fields

- id
- schoolId
- name
- section

---

## Subjects

Examples

- Mathematics
- Science
- English
- Hindi
- Social Science

---

## Chapters

Each subject contains chapters.

---

## Topics

Each chapter contains topics.

---

# Homework Module

---

## Homework

Fields

- id
- studentId
- subjectId
- chapterId
- question
- imageUrl
- status
- createdAt

---

## HomeworkSolutions

Stores AI generated solutions.

Fields

- id
- homeworkId
- aiModel
- explanation
- answer
- confidence
- processingTime

---

# AI Chat

---

## Conversations

Fields

- id
- userId
- title
- createdAt

---

## Messages

Fields

- id
- conversationId
- role
- content
- model
- tokens
- createdAt

---

# Notes

---

## Notes

Fields

- id
- userId
- title
- subjectId
- chapterId
- content
- createdAt

---

# Worksheets

---

## Worksheets

Fields

- id
- title
- subjectId
- chapterId
- difficulty
- content
- answerKey

---

# Quiz

---

## Quizzes

Fields

- id
- title
- subjectId
- chapterId
- difficulty
- totalQuestions
- createdBy

---

## QuizQuestions

Fields

- id
- quizId
- question
- optionA
- optionB
- optionC
- optionD
- correctAnswer

---

## QuizAttempts

Fields

- id
- quizId
- studentId
- score
- percentage
- completedAt

---

# Study Planner

---

## StudyPlans

Fields

- id
- userId
- title
- goal
- startDate
- endDate
- status

---

# Subscription

---

## Plans

Examples

- Free
- Premium
- School

---

## Subscriptions

Fields

- id
- userId
- planId
- startDate
- endDate
- status

---

## Payments

Fields

- id
- subscriptionId
- amount
- currency
- provider
- transactionId
- paymentStatus

---

# Notifications

---

## Notifications

Fields

- id
- userId
- type
- title
- message
- isRead
- createdAt

---

# Analytics

---

## ActivityLogs

Stores every important action.

Examples

- Login
- Homework Solved
- Quiz Attempted
- Notes Generated

---

## AIUsage

Stores AI statistics.

Examples

- Model Used
- Tokens
- Cost
- Response Time

---

# Relationships

Users

↓

Roles

↓

Permissions

↓

Homework

↓

HomeworkSolutions

↓

AI Chats

↓

Quizzes

↓

Quiz Attempts

↓

Study Plans

↓

Subscriptions

↓

Payments

---

# Primary Keys

Every table uses

UUID

Example

```
550e8400-e29b-41d4-a716-446655440000
```

---

# Soft Delete

Business tables must support soft delete.

Fields

- deletedAt
- deletedBy

---

# Audit Fields

Every business table contains

- createdAt
- updatedAt
- createdBy
- updatedBy

---

# Indexing

Indexes should exist on:

- email
- phone
- schoolId
- userId
- subjectId
- chapterId
- createdAt

Composite indexes should be added for frequently queried combinations.

---

# Data Retention

Activity Logs

12 months

AI Logs

24 months

Audit Logs

7 years

Payments

7 years

---

# Security

Sensitive fields

- passwordHash
- payment references
- API usage

must never be exposed through public APIs.

---

# Migration Strategy

All schema changes must use Prisma Migrations.

Manual database changes are prohibited.

---

# Related Documents

- DATA_DICTIONARY.md
- ER_DIAGRAM.md
- MIGRATIONS.md
- API_GUIDELINES.md
- AUTHENTICATION.md