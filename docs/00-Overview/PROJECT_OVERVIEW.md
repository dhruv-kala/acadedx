# Acadedx - Project Overview

**Document Version:** 1.1.0  
**Status:** Draft  
**Last Updated:** July 2026  
**Owner:** Digital Global Information Systems (DGIS)

---

# Purpose

This document provides a high-level overview of the Acadedx platform.

It defines what Acadedx is, why it exists, who it serves, the problem it solves, the platform hierarchy, and the long-term product direction.

This document is the entry point for Product, Architecture, Design, Engineering, QA, AI Engineering, Business, and Operations teams.

---

# Product Name

**Acadedx**

---

# Product Tagline

**AI-powered learning platform for students, parents, teachers, institutions, and education organizations.**

---

# What is Acadedx?

Acadedx is an AI-first education platform designed to help students learn better through personalized tutoring, homework assistance, OCR-based question recognition, notes generation, quizzes, worksheets, study planning, analytics, and intelligent recommendations.

Acadedx is not only a homework-solving application.

It is a complete learning ecosystem that supports individual learners, parents, teachers, schools, and future educational organizations such as academies, coaching institutes, colleges, universities, and training centers.

The platform is designed to start with a school-focused MVP while using a future-ready architecture based on Organizations and Institutions.

---

# Product Positioning

Acadedx should be positioned as:

> **An AI Learning Platform that helps students understand concepts, practice intelligently, track progress, and improve academic outcomes.**

Acadedx should not be positioned as:

- A simple homework solver
- A generic chatbot wrapper
- A static quiz application
- A school ERP replacement in the MVP
- A content-only learning platform

---

# Problem Statement

Students, parents, teachers, and educational institutions currently face fragmented learning experiences.

Students often use separate tools for:

- Homework help
- Notes
- Quizzes
- Practice worksheets
- Study planning
- Doubt solving
- Progress tracking

These tools usually do not understand the student's long-term learning journey.

Common problems include:

- Students receive answers without understanding concepts.
- Learning is not personalized.
- Weak topics are not detected early.
- Parents lack clear visibility into progress.
- Teachers spend too much time creating learning material.
- Institutions lack AI-powered learning insights.
- Learning data is scattered across disconnected systems.

Acadedx solves this by creating a connected AI learning platform where every activity contributes to a student's learning profile.

---

# Product Philosophy

Acadedx exists to improve learning outcomes.

The platform should help students understand concepts before giving final answers.

Every AI-generated response should support learning by providing explanations, examples, practice, revision guidance, and feedback.

Acadedx should encourage students to become independent learners rather than dependent answer seekers.

---

# Platform Hierarchy

Acadedx uses a future-ready education platform hierarchy.


Platform
    ↓
Organization
    ↓
Institution
    ↓
Academic Session
    ↓
Grade / Class
    ↓
Section / Batch
    ↓
Teacher
    ↓
Student


---

# Organization

An Organization represents the business, trust, company, education group, or owner entity that controls one or more institutions.

Examples:

- A school group
- A coaching company
- An academy chain
- A university group
- A training organization
- A franchise education business

In the MVP, each organization may have only one institution.

The architecture must still support multiple institutions per organization in future releases.

---

# Institution

An Institution represents the actual educational unit where teaching and learning happen.

In the MVP, the primary institution type is:

- School

Future institution types may include:

- Academy
- Coaching Institute
- College
- University
- Training Center
- Online Academy
- Corporate Learning Center

The term **Institution** should be used in architecture and database design.

The term **School** may be used in MVP user-facing screens where it improves clarity for the initial market.

---

# MVP Scope

The MVP focuses on individual students and schools.

## MVP Target Users

- Students
- Parents
- Teachers
- Institution Administrators
- Platform Administrators

## MVP Institution Type

- School

## MVP Organization Rule

Each organization supports one institution in the MVP.

Multiple institutions per organization are future-ready but not exposed in the initial product UI.

---

# Future Scope

The architecture should support future expansion to:

- Multiple institutions per organization
- Coaching institutes
- Academies
- Colleges
- Universities
- Online learning organizations
- Enterprise education providers
- Government education programs
- Marketplace-based learning content
- Public APIs for third-party integrations

---

# Core Product Pillars

## 1. AI Learning Companion

A personalized AI assistant that supports students across homework, notes, quizzes, worksheets, study planning, and revision.

---

## 2. Homework Intelligence

Students can upload or type homework questions and receive step-by-step explanations, concept support, and practice recommendations.

---

## 3. Personalized Practice

The platform generates quizzes and worksheets based on student performance, weak topics, and learning goals.

---

## 4. Study Planning

Acadedx creates adaptive study plans based on exam dates, goals, available time, quiz performance, and learning progress.

---

## 5. Learning Analytics

The platform tracks mastery, weak topics, study behavior, quiz performance, homework history, and exam readiness.

---

## 6. Parent and Teacher Visibility

Parents and teachers receive meaningful insights that help them support students without overwhelming them with raw data.

---

## 7. Institution Enablement

Institutions can manage students, teachers, classes, assignments, analytics, and AI-powered learning workflows.

---

# Core Modules

The Acadedx platform includes the following core modules:

- Authentication
- User Management
- Organization Management
- Institution Management
- Homework Solver
- OCR
- AI Tutor / AI Companion
- Notes Generator
- Quiz Generator
- Worksheet Generator
- Study Planner
- Dashboard
- Analytics
- Notifications
- Subscription Management
- Payments
- Admin Portal

---

# User Roles

Acadedx supports the following roles.

## Platform Administrator

Manages the entire Acadedx platform on behalf of DGIS.

---

## Organization Owner

Owns one or more institutions under an organization.

In the MVP, this role may not be exposed as a separate user experience.

---

## Organization Administrator

Manages organization-level settings, billing, institution access, and cross-institution analytics.

This is primarily a future-ready role.

---

## Institution Administrator

Manages a school or institution, including teachers, students, classes, sections, and institution-level analytics.

---

## Teacher

Creates assignments, generates quizzes and worksheets, reviews student progress, and uses AI teaching tools.

---

## Parent

Monitors linked student progress, receives reports, manages family subscriptions, and supports learning at home.

---

## Student

Uses Acadedx for homework help, AI tutoring, notes, quizzes, worksheets, study planning, and learning progress tracking.

---

# Product Editions

Acadedx may evolve into multiple product editions.

## Acadedx Learn

For individual students and families.

Focus areas:

- AI Homework
- AI Tutor
- Notes
- Quiz
- Worksheets
- Study Planner
- Parent Dashboard

---

## Acadedx Institution

For schools, academies, coaching institutes, and colleges.

Focus areas:

- Institution Management
- Teacher Workspace
- Student Management
- Assignments
- Institution Analytics
- Seat Licensing

---

## Acadedx Enterprise

For large education groups, universities, government programs, and enterprise learning providers.

Focus areas:

- Multi-institution management
- SSO
- API access
- White-label options
- Advanced analytics
- Dedicated support
- Custom integrations

---

# What Acadedx Is Not in MVP

The MVP is not intended to be:

- A complete school ERP
- A fee management system
- A transport management system
- A hostel management system
- A live class platform
- A marketplace
- A full LMS replacement
- A social media platform for students

These may be considered in future phases if they support the long-term product strategy.

---

# Business Objectives

Acadedx should support both B2C and B2B growth.

## B2C

Students and parents can use Acadedx directly through individual or family subscriptions.

## B2B

Institutions can license Acadedx for teachers and students.

## Enterprise

Organizations with multiple institutions can adopt Acadedx as a broader AI education platform in future phases.

---

# Success Definition

Acadedx is successful when:

- Students improve conceptual understanding.
- Students use the platform consistently.
- Homework solving leads to practice and mastery.
- Parents understand their child's progress.
- Teachers save time using AI-assisted workflows.
- Institutions gain useful learning insights.
- The platform can scale without redesigning the core architecture.

---

# Architecture Direction

Acadedx should be designed as a modular SaaS platform.

The architecture should support:

- Multi-role access
- Organization and institution hierarchy
- Strong tenant isolation
- AI provider abstraction
- Modular backend services
- Reusable frontend components
- Subscription and entitlement-based access
- Analytics-driven personalization
- Future mobile and desktop applications

---

# Product Strategy

The initial product should focus on a strong learning loop:


Homework
    ↓
Explanation
    ↓
AI Tutor
    ↓
Notes
    ↓
Quiz
    ↓
Worksheet
    ↓
Analytics
    ↓
Study Plan
    ↓
Improvement


This learning loop is more important than building broad administrative features too early.

---

# MVP Product Focus

The MVP should prioritize:

1. Student learning experience
2. AI Homework Solver
3. OCR
4. AI Tutor
5. Notes
6. Quiz
7. Worksheets
8. Study Planner
9. Student Dashboard
10. Basic parent and teacher visibility
11. Subscription and usage limits

Institution management should be included only to the extent required for a school-focused MVP.

---

# Long-Term Vision

Acadedx is envisioned as an AI-powered education operating system.

The long-term platform should support:

- Individual learners
- Families
- Schools
- Academies
- Coaching institutes
- Colleges
- Universities
- Enterprise learning organizations
- Education groups with multiple institutions

The platform should help every learner build a long-term learning identity that includes progress, mastery, achievements, preferences, study habits, and AI-powered recommendations.

---

# Guiding Product Question

Every product decision should answer:

> Does this help students learn better while keeping the platform scalable for future educational institutions?

If the answer is no, the feature should be reconsidered.

---

# Related Documents

- VISION.md
- MISSION.md
- GOALS.md
- SUCCESS_METRICS.md
- PRD.md
- PRODUCT_PRINCIPLES.md
- FEATURES.md
- USER_STORIES.md
- SYSTEM_ARCHITECTURE.md
- DATABASE_SCHEMA.md
