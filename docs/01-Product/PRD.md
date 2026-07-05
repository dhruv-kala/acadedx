# Acadedx Product Requirements Document

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Product Management  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This Product Requirements Document defines the product scope, user roles, platform model, functional modules, non-functional requirements, and release boundaries for Acadedx.

This document is the primary product reference for:

- Product Management
- Solution Architecture
- Engineering
- UI/UX Design
- AI Engineering
- QA
- DevOps
- Business Teams
- Future AI Coding Agents

---

# Product Summary

Acadedx is an AI-powered education platform designed to help students understand concepts, complete homework responsibly, practice weak topics, prepare for exams, and improve learning outcomes.

The product also supports parents, teachers, institutions, and future educational organizations through dashboards, analytics, AI tools, subscriptions, notifications, and administrative controls.

Acadedx should not be built as a simple homework solver.

Acadedx should be built as a learning-first education platform.

---

# Product Positioning

Acadedx is positioned as:

> An AI-powered learning platform for students, parents, teachers, institutions, and educational organizations.

The MVP will primarily focus on:

- Students
- Parents
- Teachers
- Schools as the first Institution type

The architecture must support future expansion into:

- Coaching institutes
- Academies
- Colleges
- Universities
- Training centers
- Online learning centers
- Multi-institution education organizations
- Enterprise education customers

---

# Platform Hierarchy

Acadedx follows this future-ready hierarchy:

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

---

# Core Domain Definitions

## Platform

The complete Acadedx ecosystem operated by Digital Global Information Systems.

---

## Organization

The business owner, trust, company, education group, franchise, or enterprise customer that owns one or more institutions.

Examples:

- A school trust
- A coaching company
- An academy brand
- A university group
- A corporate training provider

In MVP:

- An organization may have one institution.

Future:

- An organization may have multiple institutions.

---

## Institution

The actual educational unit where learning happens.

Institution types may include:

- School
- Academy
- Coaching Institute
- College
- University
- Training Centre
- Online Academy

In MVP:

- The main institution type is School.

---

## Academic Structure

Institution-level academic hierarchy.

Example:

```text
Academic Session
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

# MVP Scope Philosophy

The MVP must remain focused.

The product should use a future-ready architecture but should not expose unnecessary enterprise complexity in the first release.

MVP constraint:

```text
One Organization → One Institution
```

The architecture should still support future multi-institution expansion without major redesign.

---

# MVP Product Goals

The MVP should prove:

1. Students find AI homework help useful.
2. AI explanations improve understanding.
3. Students continue into quizzes, notes, worksheets, and study plans.
4. Parents find progress insights valuable.
5. Teachers find AI-generated learning material useful.
6. Institutions can manage basic users and learning activity.
7. Subscription and entitlement logic can support future monetization.
8. The platform model supports future Organization and Institution expansion.

---

# User Roles

## Platform Administrator

DGIS internal administrator with access to platform-level operations.

Responsibilities:

- Manage organizations
- Manage institutions
- Manage users
- Monitor AI usage
- Monitor subscriptions
- Manage feature flags
- View platform analytics
- Review audit logs

---

## Organization Owner

Business owner or authorized representative of an organization.

Responsibilities:

- Own organization account
- Manage organization billing
- View organization-level usage
- Manage institution administrators
- Future: manage multiple institutions

MVP Note:

Organization Owner features may be minimal or internal in Version 1.0.

---

## Organization Administrator

Administrator appointed by an organization.

Responsibilities:

- Manage organization settings
- Manage institutions under the organization
- View organization analytics
- Manage organization users

MVP Note:

Advanced organization administration may be deferred.

---

## Institution Administrator

Administrator for a school or other institution.

Responsibilities:

- Manage institution profile
- Manage teachers
- Manage students
- Manage classes
- View institution analytics
- Manage institution reports
- Coordinate subscription usage

---

## Teacher

Educator using Acadedx to support teaching and learning.

Responsibilities:

- View assigned students
- Create assignments
- Generate quizzes
- Generate worksheets
- Generate lesson material
- Review student progress
- Communicate with parents

---

## Parent

Guardian linked to one or more students.

Responsibilities:

- View child progress
- View study reports
- Receive recommendations
- Manage family subscription
- Communicate with teachers where supported

---

## Student

Primary learner using Acadedx.

Responsibilities:

- Upload homework
- Use AI Tutor
- Generate notes
- Attempt quizzes
- Complete worksheets
- Follow study plans
- Track learning progress

---

# Core Product Modules

## Identity and Access

Includes:

- Authentication
- User accounts
- Role-based access
- Sessions
- Password reset
- Email verification
- User profiles
- Permissions

---

## Organization Management

Future-ready module for managing education businesses or groups.

Includes:

- Organization profile
- Organization owner
- Organization administrators
- Organization billing
- Organization analytics
- Future multi-institution management

MVP Scope:

- Organization entity required in data model.
- Advanced organization UI may be deferred.

---

## Institution Management

Manages schools and future institution types.

Includes:

- Institution profile
- Institution type
- Academic sessions
- Grades
- Sections
- Subjects
- Teachers
- Students
- Parents
- Institution settings
- Institution analytics

MVP Scope:

- School-focused institution management.

---

## AI Homework Solver

Allows students to upload or type homework questions and receive step-by-step explanations.

Inputs:

- Text
- Image
- PDF
- Camera capture

Outputs:

- Final answer
- Step-by-step explanation
- Concept explanation
- Formulas
- Common mistakes
- Practice recommendations

---

## OCR

Extracts text, mathematical expressions, diagrams, and structured content from images and PDFs.

Supports:

- Printed text
- Handwriting
- Mathematical expressions
- Tables
- Multi-page PDFs

---

## AI Tutor

Interactive AI learning companion.

Supports:

- Follow-up questions
- Simplified explanations
- Examples
- Translation
- Revision help
- Personalized tutoring

---

## Notes Generator

Creates structured notes from homework, AI Tutor conversations, PDFs, or manual input.

Supports:

- Summary notes
- Detailed notes
- Formula sheets
- Revision notes
- Export options

---

## Quiz Generator

Creates AI-powered quizzes from topics, homework, notes, or weak areas.

Supports:

- Practice quizzes
- Exam mode
- Timed quizzes
- Adaptive difficulty
- AI feedback

---

## Worksheet Generator

Creates personalized practice worksheets.

Supports:

- Weak topic worksheets
- Revision worksheets
- Challenge worksheets
- Printable PDFs
- Answer keys

---

## Study Planner

Creates personalized study plans based on goals, exam dates, weak topics, and learning history.

Supports:

- Daily plan
- Weekly plan
- Exam plan
- Revision schedule
- Adaptive planning

---

## Analytics

Tracks learning activity, mastery, engagement, AI quality, institution usage, and business metrics.

Includes:

- Learning Mastery Score
- Weak topic detection
- Study streaks
- Quiz accuracy
- Institution analytics
- Organization analytics in future

---

## Notifications

Delivers reminders, progress alerts, achievement notifications, security alerts, subscription messages, and AI recommendations.

Channels:

- In-app
- Email
- Push
- SMS in future
- WhatsApp in future

---

## Dashboard

Role-specific home screen for:

- Students
- Parents
- Teachers
- Institution Administrators
- Organization Administrators
- Platform Administrators

The dashboard must be action-oriented, not only metric-oriented.

---

## Subscriptions and Entitlements

Manages plans, feature access, usage limits, trials, upgrades, downgrades, and licensing.

Must use entitlement-based access control.

Avoid hardcoded plan checks.

---

## Payments

Manages payment gateway integration, invoices, taxes, refunds, payment history, and billing workflows.

Payment logic must be provider-agnostic.

---

## Admin Portal

Internal DGIS operations console.

Includes:

- User management
- Organization management
- Institution management
- AI monitoring
- Subscriptions
- Payments
- Support
- Security
- Reports
- Feature flags

---

# Functional Requirements

Each module must define:

- Business objective
- User stories
- Acceptance criteria
- Database entities
- APIs
- Permissions
- Validation rules
- Error handling
- Analytics events
- Notifications
- Security rules
- Testing requirements

Detailed module specifications are maintained under:

```text
/specifications
```

Implementation tasks are maintained under:

```text
/tasks
```

---

# MVP Functional Scope

The MVP must include:

- User registration and login
- Student profile
- Parent profile
- Teacher profile
- Basic institution profile
- Organization entity in backend
- Institution entity in backend
- AI Homework Solver
- OCR
- AI Tutor
- Notes Generator
- Quiz Generator
- Worksheet Generator
- Study Planner
- Student Dashboard
- Basic Parent Dashboard
- Basic Teacher Workspace
- Basic Institution Dashboard
- Notifications
- Subscription plans
- Entitlement validation
- Basic payment integration
- Admin Portal foundation
- Analytics foundation

---

# MVP Non-Goals

The MVP should not include:

- Multi-institution organization dashboard
- Complex enterprise administration
- White labeling
- Enterprise SSO
- Public APIs
- Marketplace
- University-specific workflows
- Government education workflows
- Corporate training workflows
- Offline-first sync
- AI video generation
- Full LMS replacement
- Full ERP functionality
- Transport, hostel, library, or fee management

These may be future products or modules.

---

# Business Requirements

Acadedx must support:

- Free plan
- Paid student plan
- Family plan
- Institution plan
- Enterprise plan in future
- Usage limits
- AI usage tracking
- Invoices
- Regional pricing in future
- Organization-level billing in future

---

# AI Requirements

AI must:

- Explain before answering where appropriate.
- Adapt explanations by grade and learning level.
- Support responsible learning.
- Avoid encouraging cheating.
- Use prompt safety checks.
- Track model usage and cost.
- Support multiple providers.
- Support quality feedback.
- Support prompt versioning.

---

# Data Requirements

The data model must support:

- Organizations
- Institutions
- Users
- Roles
- Permissions
- Academic structure
- Learning activity
- AI conversations
- Homework
- Notes
- Quizzes
- Worksheets
- Study plans
- Analytics
- Subscriptions
- Payments
- Notifications
- Audit logs

Data isolation must be enforced across organizations and institutions.

---

# Security Requirements

Acadedx must provide:

- Secure authentication
- Role-based access control
- Organization-level isolation
- Institution-level isolation
- Encrypted sensitive data
- Secure file uploads
- Rate limiting
- Audit logging
- Prompt injection protection
- Payment security
- Admin activity tracking

---

# Compliance and Privacy Requirements

Acadedx must be designed with privacy by default.

Rules:

- Students can access only their own learning data.
- Parents can access only linked children.
- Teachers can access only assigned students or classes.
- Institution administrators can access only their institution.
- Organization administrators can access only their organization.
- Platform administrators require audited access.
- Sensitive AI conversations must not be broadly visible.

Future compliance planning should consider:

- GDPR
- COPPA
- FERPA
- Indian data protection regulations
- Regional education data rules

---

# Performance Requirements

Target performance:

| Area | Target |
|---|---|
| Standard API response | < 500 ms |
| Dashboard load | < 2 seconds |
| Homework upload acknowledgement | < 2 seconds |
| OCR processing | < 5 seconds |
| AI Tutor normal response | < 5 seconds |
| Homework solution end-to-end | < 15 seconds |
| Quiz generation | < 10 seconds |
| Notes generation | < 10 seconds |
| Worksheet generation | < 12 seconds |

---

# Reliability Requirements

The platform should target:

- 99.9% uptime
- Graceful error handling
- Retry support for AI and payment workflows
- Background job monitoring
- Failed job recovery
- Database backup strategy
- Audit log preservation

---

# Accessibility Requirements

Acadedx must support:

- Responsive design
- Keyboard navigation
- Screen reader support
- Accessible forms
- Sufficient contrast
- Clear error states
- Readable typography
- Reduced motion support where applicable

---

# Analytics Requirements

The product must track:

- Learning activity
- Homework usage
- OCR success rate
- AI Tutor usage
- Notes generation
- Quiz completion
- Worksheet generation
- Study plan completion
- Parent engagement
- Teacher usage
- Institution usage
- Organization usage in future
- Subscription conversion
- Payment success
- AI cost
- System performance

---

# Product Constraints

## Execution Constraint

The MVP must avoid enterprise overbuild.

Build the platform foundation, but expose only what is needed for Version 1.0.

---

## Architecture Constraint

Do not hardcode school-only assumptions.

Use Institution as the flexible academic unit.

Use Organization as the parent business entity.

---

## Cost Constraint

AI usage must be tracked from day one.

Features that create high AI cost must support:

- Rate limits
- Usage quotas
- Entitlements
- Cost analytics

---

## Privacy Constraint

Education data must be protected across users, institutions, and organizations.

Tenant isolation is mandatory.

---

# Risks

## Product Risks

- Scope creep
- Overbuilding enterprise workflows
- Low student retention
- AI answers perceived as cheating
- Weak parent adoption
- Teacher workflow complexity

---

## Technical Risks

- AI hallucination
- OCR accuracy issues
- High AI cost
- Payment gateway failures
- Data isolation bugs
- Scaling challenges

---

## Business Risks

- Low free-to-paid conversion
- Strong competition
- Pricing sensitivity
- Institution sales cycle complexity
- Regulatory requirements

---

# Risk Mitigation

- Keep MVP scope focused.
- Use Organization and Institution architecture early.
- Track AI quality and cost.
- Use entitlement-based feature access.
- Implement strong RBAC.
- Validate learning impact through analytics.
- Pilot with small user groups before scaling.
- Use modular specifications and task-based implementation.

---

# Release Strategy

## Release 0 — Planning and Architecture

- Product documentation
- Architecture documentation
- API guidelines
- Database schema
- Module specifications
- Implementation tasks

---

## Release 1 — MVP Learning Platform

- Authentication
- Users
- Organization and Institution foundation
- Homework
- OCR
- AI Tutor
- Notes
- Quiz
- Worksheets
- Study Planner
- Dashboard
- Notifications
- Analytics foundation

---

## Release 2 — Monetization

- Plans
- Entitlements
- Subscriptions
- Payments
- Invoices
- Usage limits

---

## Release 3 — Institution Pilot

- Teacher Workspace
- Institution Dashboard
- Student management
- Class management
- Institution reports
- Institution licensing

---

## Release 4 — Growth

- Parent experience improvements
- Teacher productivity improvements
- Advanced analytics
- Mobile apps
- AI personalization
- Institution onboarding

---

## Release 5 — Enterprise Readiness

- Multi-institution organizations
- Organization dashboard
- Enterprise billing
- SSO
- White label
- API access
- Advanced AI operations

---

# Acceptance Criteria for PRD Completion

This PRD is complete when:

- Product scope is clearly defined.
- MVP boundaries are clear.
- Future-ready architecture is documented.
- Organization and Institution concepts are included.
- User roles are defined.
- Core modules are identified.
- Out-of-scope items are explicit.
- Engineering can create implementation tasks from module specifications.

---

# Related Documents

- PROJECT_OVERVIEW.md
- VISION.md
- MISSION.md
- GOALS.md
- SUCCESS_METRICS.md
- PRODUCT_PRINCIPLES.md
- FEATURES.md
- USER_STORIES.md
- ACCEPTANCE_CRITERIA.md
- SYSTEM_ARCHITECTURE.md
- DATABASE_SCHEMA.md