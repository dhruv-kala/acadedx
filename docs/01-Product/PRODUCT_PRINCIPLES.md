# Acadedx Product Principles

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Product Management  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the product principles for Acadedx.

These principles guide product strategy, user experience, AI behavior, engineering decisions, feature prioritization, and roadmap planning.

Every feature, workflow, screen, API, AI interaction, and administrative capability should follow these principles.

---

# Product Philosophy

Acadedx exists to improve learning outcomes.

The product must not become a collection of disconnected AI tools.

Every feature should support one or more of the following:

- Help students understand better.
- Help students practice effectively.
- Help parents stay informed.
- Help teachers teach more efficiently.
- Help institutions improve academic outcomes.
- Help organizations manage education operations at scale.

---

# Core Platform Principle

Acadedx should be built as a future-ready education platform.

The product experience in MVP may focus on schools, but the architecture must support the broader platform hierarchy:

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

The constraint here is execution focus.

Do not overbuild multi-institution workflows in the MVP.

Do not hardcode assumptions that Acadedx only supports schools.

---

# Principle 1 — Learning Before Answers

Acadedx must prioritize understanding before final answers.

The product should:

- Explain concepts.
- Break problems into steps.
- Encourage thinking.
- Provide hints where appropriate.
- Generate practice questions.
- Help students revise weak areas.

The final answer should support learning, not replace learning.

---

# Principle 2 — Responsible AI

AI must behave as a learning companion, not a shortcut engine.

AI should:

- Use age-appropriate explanations.
- Avoid encouraging cheating.
- Admit uncertainty when needed.
- Avoid fabricated facts.
- Explain reasoning.
- Encourage critical thinking.
- Respect privacy.
- Follow content safety rules.

AI should never be positioned as a replacement for teachers.

---

# Principle 3 — Personalization

Acadedx should adapt to each learner.

Personalization should consider:

- Grade
- Subject
- Curriculum
- Learning history
- Homework patterns
- Quiz scores
- Weak topics
- Strong topics
- Study goals
- Preferred language
- Learning speed

Personalization should improve over time as more learning data becomes available.

---

# Principle 4 — Simplicity for MVP

The MVP must remain focused and usable.

Version 1.0 should not attempt to expose every future enterprise capability.

MVP should focus on:

- Students
- Parents
- Teachers
- One institution per organization
- School as the primary institution type
- Core AI learning workflows

The product should avoid unnecessary configuration screens, enterprise-only workflows, and complex administration unless required for the MVP.

---

# Principle 5 — Future-Ready Domain Model

The product must use flexible domain concepts.

Use:

- Organization
- Institution
- Institution Type
- Academic Session
- Grade
- Section
- User Role

Avoid hardcoding:

- School-only assumptions
- One business equals one school
- One institution type forever
- One curriculum forever
- One country forever

This enables future expansion into academies, coaching institutes, colleges, universities, and enterprise education groups.

---

# Principle 6 — Organization and Institution Separation

Acadedx must clearly separate business ownership from education operations.

## Organization

Represents the business owner, trust, company, education group, franchise, or enterprise customer.

Organizations may eventually manage:

- Multiple institutions
- Central billing
- Shared branding
- Organization-level analytics
- Licensing
- Administrators
- Enterprise settings

## Institution

Represents the actual education unit where learning happens.

Institution types may include:

- School
- Academy
- Coaching Institute
- College
- University
- Training Centre
- Online Academy

In MVP, one organization may operate one institution.

---

# Principle 7 — Teacher Empowerment

Teachers are central to education.

Acadedx should reduce repetitive work and improve teaching quality.

Teacher-facing features should help with:

- Lesson planning
- Homework creation
- Quiz generation
- Worksheet generation
- Revision material
- Student feedback
- Class analytics
- Parent communication

AI should act as a co-teacher, not a replacement teacher.

---

# Principle 8 — Parent Clarity

Parents need meaningful insights, not overwhelming dashboards.

Parent experiences should be:

- Simple
- Actionable
- Easy to understand
- Focused on progress
- Focused on recommended actions

Parents should understand what their child is learning, where help is needed, and what action they can take.

---

# Principle 9 — Institution Readiness

Acadedx must support institutions from the beginning.

Institution-level features should support:

- Institution administrators
- Teachers
- Students
- Parents
- Classes
- Subjects
- Assignments
- Analytics
- Billing
- Reports

MVP may implement only the minimum required institution functionality.

The architecture must still support institution-level isolation and permissions.

---

# Principle 10 — Multi-Tenant Data Isolation

Acadedx must protect data across organizations and institutions.

Users must only access data they are authorized to view.

Rules:

- Students access their own learning data.
- Parents access linked children only.
- Teachers access assigned students and classes only.
- Institution administrators access their institution only.
- Organization administrators access their organization only.
- Platform administrators require audited access.

No organization or institution should ever access another organization's or institution's data.

---

# Principle 11 — Trust, Privacy, and Safety

Acadedx handles sensitive education data.

The product must protect:

- Student profiles
- Parent relationships
- Teacher records
- Institution data
- AI conversations
- Homework uploads
- Learning analytics
- Billing data

Privacy and security must be designed into every feature, not added later.

---

# Principle 12 — Action-Oriented Dashboards

Dashboards should not only show data.

They should help users decide what to do next.

A student dashboard should answer:

- What should I study today?
- What is pending?
- Where am I weak?
- What should I practice next?

A teacher dashboard should answer:

- Which students need help?
- What assignments need attention?
- What topic should be revised?

An institution dashboard should answer:

- Are students engaged?
- Are teachers using the platform?
- Which subjects need attention?

---

# Principle 13 — Measurement Before Scale

Acadedx should scale based on evidence.

Before expanding into broader institution types or enterprise markets, the product should validate:

- Student engagement
- Learning improvement
- AI usefulness
- Parent value
- Teacher productivity
- Institution adoption
- Subscription conversion
- AI cost sustainability

Do not scale complexity before validating usage.

---

# Principle 14 — Modular Product Growth

Acadedx should grow module by module.

Core product domains:

- Identity
- Organizations
- Institutions
- Learning
- AI
- Analytics
- Commerce
- Notifications
- Administration
- Support
- Content

Each module should have clear ownership, responsibilities, data boundaries, APIs, and acceptance criteria.

---

# Principle 15 — Entitlements Over Plan Checks

Premium access should be controlled through feature entitlements, not hardcoded plan names.

Avoid logic such as:

```text
if user.plan == "Student Pro"
```

Prefer:

```text
if user.hasFeature("homework.unlimited")
```

This enables flexible packaging, add-ons, regional pricing, institutional plans, scholarships, and future AI credit systems.

---

# Principle 16 — AI Cost Awareness

AI features must be designed with cost visibility.

Every AI workflow should track:

- Provider
- Model
- Tokens
- Cost
- Latency
- Success rate
- User satisfaction
- Prompt version

AI cost must be monitored from MVP to avoid unsustainable economics.

---

# Principle 17 — Accessibility

Acadedx should be usable by as many learners as possible.

The product should support:

- Keyboard navigation
- Screen readers
- Responsive design
- Readable typography
- High contrast support
- Clear form labels
- Error messages that are easy to understand

Accessibility is part of product quality.

---

# Principle 18 — Global Readiness

Acadedx should be designed for future international use.

The platform should support:

- Multiple languages
- Multiple currencies
- Multiple time zones
- Regional curricula
- Regional payment methods
- Local compliance requirements

MVP does not need to implement all regions, but architecture should avoid blocking future expansion.

---

# Principle 19 — Consistent User Experience

The same action should behave consistently across the platform.

Consistency applies to:

- Navigation
- Buttons
- Forms
- Dialogs
- Empty states
- Error states
- Loading states
- AI interactions
- Notifications
- Permissions
- Terminology

Users should not need to relearn the interface in every module.

---

# Principle 20 — Build for Learning Loops

Every core feature should contribute to a learning loop.

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

Examples:

- Homework should lead to explanation, quiz, notes, or worksheet.
- Quiz mistakes should generate revision and practice.
- Weak topics should update study plans.
- Analytics should recommend next actions.
- Notifications should drive meaningful learning activities.

---

# Decision Framework

Before approving a feature, answer:

1. Does it improve learning outcomes?
2. Does it support students, parents, teachers, institutions, or organizations?
3. Does it align with responsible AI usage?
4. Does it preserve MVP simplicity?
5. Does it support future Organization and Institution architecture?
6. Does it avoid hardcoded school-only assumptions?
7. Does it protect privacy and data isolation?
8. Can success be measured?
9. Is the feature economically sustainable?
10. Can it scale without major redesign?

Features that fail these checks should be redesigned, deferred, or rejected.

---

# MVP Product Principles

For Version 1.0:

- Build school-focused workflows.
- Support one institution per organization.
- Keep organization management mostly internal or minimal.
- Implement Institution as a flexible domain entity.
- Avoid enterprise UI complexity.
- Focus on the core learning loop.
- Measure learning improvement and AI cost.
- Use entitlements for feature access.
- Build modular APIs and data models.

---

# Anti-Principles

Acadedx should avoid:

- Building every future feature in the MVP.
- Treating AI as only a chatbot.
- Treating schools as the only possible institution type.
- Mixing organization billing with institution operations incorrectly.
- Creating dashboards full of vanity metrics.
- Allowing users to access unauthorized data.
- Hardcoding subscription plan logic.
- Ignoring AI cost.
- Generating answers without educational value.
- Designing only for one country or curriculum.

---

# Product Promise

Acadedx should help users:

- Learn better
- Teach better
- Practice smarter
- Track progress clearly
- Save time
- Build confidence
- Improve outcomes

---

# Related Documents

- PROJECT_OVERVIEW.md
- VISION.md
- MISSION.md
- GOALS.md
- SUCCESS_METRICS.md
- PRD.md
- FEATURES.md
- ACCEPTANCE_CRITERIA.md