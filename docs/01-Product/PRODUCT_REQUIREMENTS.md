# Acadedx Product Requirements

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Product Management  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the high-level product requirements for Acadedx.

It translates the product vision and PRD into structured requirements that guide module specifications, implementation tasks, architecture, user experience, and acceptance criteria.

This document should be used by:

- Product Management
- Solution Architecture
- Engineering
- UI/UX Design
- AI Engineering
- QA
- DevOps
- Business Teams
- AI Coding Agents

---

# Product Requirement Summary

Acadedx must be built as an AI-powered education platform that supports individual learners, parents, teachers, institutions, and future multi-institution organizations.

The MVP should focus on school-based learning workflows while using a future-ready domain model that supports broader institution types later.

---

# Primary Requirement

Acadedx must help students improve learning outcomes through AI-powered explanation, practice, assessment, revision, and personalized recommendations.

The product must not be limited to answering questions.

It must support the full learning loop:

```text
Understand
    ↓
Practice
    ↓
Assess
    ↓
Improve
    ↓
Revise
    ↓
Master
```

---

# Platform Hierarchy Requirement

Acadedx must support the following platform hierarchy:

```text
Platform
    ↓
Organization
    ↓
Institution
    ↓
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

## MVP Constraint

In Version 1.0:

```text
One Organization → One Institution
```

The primary Institution type in MVP is:

```text
School
```

## Future Requirement

In future releases:

```text
One Organization → Multiple Institutions
```

Institution types may include:

- School
- Academy
- Coaching Institute
- College
- University
- Training Centre
- Learning Centre
- Online Academy
- Corporate Training Unit

---

# Domain Requirements

## Organization

The system must support an Organization entity.

An Organization represents the business owner, education group, trust, company, franchise, or enterprise customer.

An Organization must support:

- Organization profile
- Owner
- Administrators
- Billing relationship
- Subscription relationship
- Branding configuration in future
- Multiple institutions in future

MVP Requirement:

- Organization must exist in the data model.
- Organization management UI may be minimal or deferred.
- Every institution must belong to an organization.

---

## Institution

The system must support an Institution entity.

An Institution represents the actual education unit where learning happens.

An Institution must support:

- Institution type
- Institution profile
- Academic sessions
- Grades
- Sections
- Subjects
- Teachers
- Students
- Parents
- Analytics
- Subscription usage

MVP Requirement:

- Institution UI may be school-focused.
- Backend must not hardcode `School` as the only possible institution type.

---

## User

The system must support multiple user roles:

- Platform Administrator
- Organization Owner
- Organization Administrator
- Institution Administrator
- Teacher
- Parent
- Student
- Support Agent

Every user must be associated with appropriate organization and institution context where applicable.

---

# User Role Requirements

## Platform Administrator

Must be able to:

- Manage organizations
- Manage institutions
- Manage users
- Monitor AI usage
- Manage subscriptions
- View payments
- Manage feature flags
- View audit logs
- Access platform analytics

---

## Organization Owner

Must be able to:

- Own organization account
- Manage organization billing
- View organization-level usage
- Assign organization administrators
- Manage institutions in future

MVP Note:

Organization Owner workflows may be minimal in Version 1.0.

---

## Organization Administrator

Must be able to:

- Manage organization settings
- View organization usage
- Manage institution administrators
- View billing where permitted

MVP Note:

Advanced organization administration may be deferred.

---

## Institution Administrator

Must be able to:

- Manage institution profile
- Manage teachers
- Manage students
- Manage parents
- Manage grades and sections
- View institution analytics
- Export reports
- Manage institution-level settings

---

## Teacher

Must be able to:

- View assigned students
- Manage assigned classes
- Create assignments
- Generate quizzes
- Generate worksheets
- Generate notes
- Review student progress
- Use AI teaching tools
- Communicate with parents where supported

---

## Parent

Must be able to:

- Link to one or more students
- View child progress
- View weekly learning reports
- Receive notifications
- Manage family subscription
- View recommendations

---

## Student

Must be able to:

- Upload homework
- Use AI Tutor
- Generate notes
- Attempt quizzes
- Generate worksheets
- Follow study plans
- View dashboard
- Track progress
- Manage learning preferences

---

# Core Product Requirements

## Authentication and Identity

The system must support:

- User registration
- Login
- Logout
- Refresh tokens
- Password reset
- Email verification
- Session management
- Role-based access control
- User profile management

---

## Organization Management

The system must support:

- Organization creation
- Organization profile
- Organization ownership
- Organization administrators
- Organization billing relationship
- Organization-level analytics in future

MVP Scope:

- Required in backend and database.
- Minimal UI required initially.

---

## Institution Management

The system must support:

- Institution creation
- Institution type
- Institution profile
- Academic sessions
- Grades
- Sections
- Subjects
- Teachers
- Students
- Parents
- Institution analytics
- Institution reports

MVP Scope:

- School-focused institution workflows.

---

## AI Homework Solver

The system must allow students to:

- Type a question
- Upload an image
- Upload a PDF
- Capture a question using camera
- Receive step-by-step explanation
- Receive final answer
- View concepts used
- View common mistakes
- Generate follow-up quiz, notes, or worksheet

---

## OCR

The system must support:

- Image processing
- PDF processing
- Printed text extraction
- Handwritten text extraction
- Mathematical expression extraction
- Confidence scoring
- User correction when OCR confidence is low

---

## AI Tutor

The system must support:

- Conversational learning
- Follow-up questions
- Context-aware responses
- Homework context
- Quiz context
- Notes context
- Personalized explanations
- Grade-appropriate responses
- Learning history awareness

---

## Notes Generator

The system must support:

- Generate notes from homework
- Generate notes from AI conversations
- Generate notes from uploaded content
- Edit notes
- Save notes
- Search notes
- Export notes

---

## Quiz Generator

The system must support:

- Generate quizzes from topics
- Generate quizzes from homework
- Generate quizzes from notes
- Timed quiz mode
- Practice quiz mode
- Adaptive difficulty
- Score calculation
- AI feedback
- Weak topic detection

---

## Worksheet Generator

The system must support:

- Generate worksheets from homework
- Generate worksheets from notes
- Generate worksheets from quiz mistakes
- Difficulty selection
- Answer keys
- PDF export
- Weak topic practice

---

## Study Planner

The system must support:

- Daily study plans
- Weekly study plans
- Exam preparation plans
- Revision plans
- Task completion
- Adaptive rescheduling
- Notifications
- Progress tracking

---

## Dashboard

The system must support role-specific dashboards for:

- Students
- Parents
- Teachers
- Institution Administrators
- Organization Administrators in future
- Platform Administrators

Dashboard content must be action-oriented.

---

## Analytics

The system must support:

- Learning Mastery Score
- Weak topic detection
- Study streaks
- Homework analytics
- Quiz analytics
- Worksheet analytics
- Study planner analytics
- AI usage analytics
- Institution analytics
- Organization analytics in future

---

## Notifications

The system must support:

- In-app notifications
- Email notifications
- Push notifications
- Study reminders
- Homework completion alerts
- Quiz reminders
- Parent reports
- Teacher alerts
- Security alerts
- Subscription notifications

---

## Subscriptions and Entitlements

The system must support:

- Free plan
- Student Pro plan
- Family plan
- Teacher plan
- Institution plan
- Enterprise plan in future
- Feature entitlements
- Usage limits
- Trials
- Upgrades
- Downgrades
- Grace periods

The system must not use hardcoded plan checks for feature access.

---

## Payments

The system must support:

- Payment gateway integration
- Provider-agnostic payment layer
- Subscription payments
- One-time purchases in future
- Invoices
- Refunds
- Taxes
- Payment history
- Failed payment handling

---

## Admin Portal

The system must support platform administration for DGIS.

Admin Portal must include:

- User management
- Organization management
- Institution management
- AI monitoring
- Payments
- Subscriptions
- Feature flags
- Reports
- Support
- Audit logs
- Security monitoring

---

# Data Requirements

The product must store and manage:

- Organizations
- Institutions
- Users
- Roles
- Permissions
- Academic structure
- Homework
- OCR jobs
- AI conversations
- Notes
- Quizzes
- Quiz attempts
- Worksheets
- Study plans
- Analytics
- Notifications
- Subscriptions
- Payments
- Audit logs

---

# Data Isolation Requirements

Data isolation is mandatory.

Rules:

- Organization data must be isolated from other organizations.
- Institution data must be isolated from other institutions.
- Students can access only their own data.
- Parents can access only linked children.
- Teachers can access only assigned students and classes.
- Institution administrators can access only their institution.
- Organization administrators can access only their organization.
- Platform administrator access must be audited.

---

# AI Requirements

AI features must support:

- Provider abstraction
- Prompt versioning
- Model routing
- Cost tracking
- Token tracking
- User feedback
- Output moderation
- Prompt injection protection
- Response validation
- Grade-level adaptation
- Context-aware tutoring

---

# UI/UX Requirements

The application must be:

- Simple for students
- Responsive
- Accessible
- Mobile-first
- Consistent
- Fast
- Clear in error states
- Clear in empty states
- Safe for child users
- Friendly for parents and teachers

The MVP should not expose complex enterprise screens unless required.

---

# API Requirements

All APIs must follow:

- REST-first design
- Versioned endpoints
- Standard response format
- Authentication
- Authorization
- Validation
- Pagination where needed
- Rate limiting
- OpenAPI documentation
- Audit logging where required

---

# Security Requirements

The product must include:

- Secure password handling
- JWT authentication
- Refresh tokens
- Role-based access control
- Organization and institution scoping
- Secure file uploads
- Virus scanning for uploads
- Input validation
- Rate limiting
- Audit logs
- Admin activity tracking
- Payment security
- AI safety controls

---

# Performance Requirements

Target performance:

| Capability | Target |
|---|---|
| Dashboard load | < 2 seconds |
| Standard API response | < 500 ms |
| Homework upload acknowledgement | < 2 seconds |
| OCR processing | < 5 seconds |
| AI Tutor response | < 5 seconds |
| Homework solution end-to-end | < 15 seconds |
| Quiz generation | < 10 seconds |
| Notes generation | < 10 seconds |
| Worksheet generation | < 12 seconds |
| Search | < 500 ms |

---

# Reliability Requirements

The platform must support:

- Graceful failure handling
- Retry logic
- Background jobs
- Failed job monitoring
- AI provider fallback
- Payment webhook retry
- Database backups
- Audit log retention
- Error tracking
- Health checks

---

# Accessibility Requirements

The platform must support:

- Keyboard navigation
- Screen readers
- Accessible forms
- Color contrast
- Responsive layouts
- Meaningful error messages
- Readable typography
- Reduced motion support where applicable

---

# Monetization Requirements

The product must support monetization through:

- Individual subscriptions
- Family subscriptions
- Teacher subscriptions
- Institution subscriptions
- Enterprise subscriptions in future
- AI usage limits
- Entitlements
- Coupons
- Invoices
- Regional pricing in future
- Organization-level billing in future

---

# Reporting Requirements

The product must support reports for:

- Students
- Parents
- Teachers
- Institution administrators
- Platform administrators
- Organization administrators in future

Reports may include:

- Learning progress
- Homework completion
- Quiz performance
- Worksheet practice
- Study time
- AI usage
- Subscription usage
- Payments
- Institution engagement

---

# MVP Requirements

Version 1.0 must include:

- Authentication
- User profiles
- Organization entity
- Institution entity
- Student workflows
- Parent basic workflows
- Teacher basic workflows
- Institution basic workflows
- AI Homework Solver
- OCR
- AI Tutor
- Notes Generator
- Quiz Generator
- Worksheet Generator
- Study Planner
- Dashboard
- Notifications
- Analytics foundation
- Subscription foundation
- Entitlement foundation
- Payment foundation
- Admin Portal foundation

---

# Out of Scope for MVP

Version 1.0 must not include:

- Multi-institution organization dashboard
- Full enterprise administration
- White label support
- Marketplace
- Public API
- Enterprise SSO
- Advanced LMS features
- University-specific workflows
- Corporate training workflows
- Government education workflows
- Transport management
- Hostel management
- Library management
- Fee management
- Offline-first learning
- AI video generation

---

# Acceptance Requirements

A requirement is accepted only when:

- It maps to a module specification.
- It maps to one or more implementation tasks.
- APIs are documented.
- Database entities are identified.
- UI states are defined.
- Permissions are defined.
- Analytics events are defined.
- Security considerations are defined.
- Tests are defined.
- Product Owner approves the implementation.

---

# Traceability Requirements

Every requirement should be traceable to:

- PRD section
- Module specification
- User story
- API endpoint
- Database entity
- UI screen
- Acceptance criteria
- Test case
- Analytics event

---

# Product Management Notes

The main product risk is scope creep.

The correct approach is:

```text
Future-ready architecture
+
Focused MVP execution
```

Do not confuse future-ready data modeling with building all future workflows immediately.

The MVP should validate learning value first.

---

# Related Documents

- PRD.md
- PRODUCT_PRINCIPLES.md
- FEATURES.md
- USER_STORIES.md
- ACCEPTANCE_CRITERIA.md
- PROJECT_OVERVIEW.md
- SYSTEM_ARCHITECTURE.md
- DATABASE_SCHEMA.md
- API_GUIDELINES.md