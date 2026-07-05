# Acadedx Goals

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Product Management  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the strategic goals for Acadedx.

The goals provide measurable direction for product planning, engineering execution, AI development, business growth, user experience, and operational readiness.

Every roadmap item should contribute to at least one goal defined in this document.

---

# Goal Philosophy

Acadedx should be ambitious in architecture but disciplined in execution.

The MVP should remain focused on students, parents, teachers, and schools.

The platform architecture must remain ready for future expansion into organizations, multiple institutions, coaching institutes, academies, colleges, universities, and enterprise education customers.

The product goal is not to build everything at once.

The product goal is to build the correct foundation and release value incrementally.

---

# Platform Goal

Build an AI-first education platform that supports the following hierarchy:

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

In the MVP:

- One organization may have one institution.
- The primary institution type is School.
- Multi-institution management is not required in the first release.

Long term:

- One organization may manage multiple institutions.
- Institutions may include schools, academies, coaching institutes, colleges, universities, and training centers.

---

# Goal Categories

Acadedx goals are divided into the following categories:

1. Learning Goals
2. Product Goals
3. Platform Goals
4. Business Goals
5. AI Goals
6. User Experience Goals
7. Engineering Goals
8. Operational Goals

---

# 1. Learning Goals

Acadedx must improve real learning outcomes.

## Goals

- Help students understand concepts instead of copying answers.
- Improve homework completion quality.
- Increase regular study habits.
- Improve quiz performance over time.
- Identify weak topics accurately.
- Recommend personalized practice.
- Support revision before exams.
- Build long-term concept mastery.

## Success Indicators

- Higher quiz accuracy after using homework explanations.
- Increased worksheet completion.
- Improved Learning Mastery Score.
- Increased study streaks.
- Reduction in repeated mistakes.
- More students completing recommended revision activities.

---

# 2. Product Goals

Acadedx must become a complete AI learning companion.

## MVP Product Goals

- Launch AI Homework Solver.
- Launch OCR-based question extraction.
- Launch AI Tutor.
- Launch Notes Generator.
- Launch Quiz Generator.
- Launch Worksheet Generator.
- Launch Study Planner.
- Launch Student Dashboard.
- Launch basic Parent Dashboard.
- Launch basic Teacher Workspace.
- Launch basic Institution support for schools.

## Future Product Goals

- Launch Organization Management.
- Launch multi-institution support.
- Launch advanced Teacher Workspace.
- Launch Institution Analytics.
- Launch Organization Analytics.
- Launch AI Operations Console.
- Launch Educational Marketplace.
- Launch enterprise integrations.

---

# 3. Platform Goals

The platform must support both B2C and B2B education.

## MVP Platform Goals

- Support individual students.
- Support parent-child relationships.
- Support teacher-student relationships.
- Support one institution per organization.
- Support institution-level users.
- Support institution-level analytics.
- Support institution-level subscription and billing.

## Future Platform Goals

- Support multiple institutions per organization.
- Support centralized organization billing.
- Support organization-level branding.
- Support organization-level administration.
- Support organization-level analytics.
- Support multi-region deployment.
- Support multiple currencies.
- Support multiple curricula.
- Support multiple languages.

---

# 4. Business Goals

Acadedx must become a sustainable education SaaS business.

## Year 1 Goals

- Launch MVP.
- Acquire early student users.
- Validate AI Homework Solver usage.
- Validate parent and teacher interest.
- Validate pricing assumptions.
- Launch free and paid plans.
- Build initial school pilot program.

## Years 2–3 Goals

- Expand into schools and coaching institutes.
- Launch institution subscriptions.
- Improve AI personalization.
- Launch teacher productivity tools.
- Support multiple curricula.
- Launch mobile applications.
- Grow recurring revenue.

## Years 4–5 Goals

- Support multi-institution organizations.
- Expand internationally.
- Serve education groups and enterprise customers.
- Launch marketplace and API capabilities.
- Build Acadedx as a globally trusted AI education platform.

---

# 5. AI Goals

AI must be used responsibly and meaningfully.

## Goals

- Provide accurate educational explanations.
- Adapt explanations by grade and learning level.
- Generate high-quality quizzes, notes, and worksheets.
- Detect weak topics.
- Support personalized study plans.
- Reduce hallucination risk.
- Support multiple AI providers.
- Track AI quality and cost.

## AI Quality Targets

- AI response satisfaction above 4.5 out of 5.
- Homework solution success rate above 90%.
- OCR accuracy above 95% for printed text.
- AI hallucination rate below 2% after moderation and validation.
- AI response time within accepted performance limits.

---

# 6. User Experience Goals

Acadedx must be simple enough for students and powerful enough for institutions.

## Goals

- Keep the student experience simple.
- Reduce unnecessary navigation.
- Make the dashboard action-oriented.
- Make AI interactions clear and safe.
- Use consistent UI patterns.
- Support mobile-first usage.
- Support accessibility standards.
- Avoid enterprise complexity in MVP screens.

## UX Success Indicators

- Students can upload homework within 30 seconds.
- Students can understand AI explanations without additional help.
- Parents can understand progress reports quickly.
- Teachers can create assignments faster than manual workflows.
- Users can discover the next best action from the dashboard.

---

# 7. Engineering Goals

The platform must be scalable, secure, modular, and maintainable.

## Goals

- Use modular architecture.
- Avoid hardcoded school-only assumptions.
- Use Organization and Institution as core platform entities.
- Keep MVP implementation simple while preserving extensibility.
- Build API-first services.
- Maintain strong authentication and authorization.
- Use feature flags and entitlements.
- Support observability from the beginning.
- Maintain automated tests.

## Technical Success Indicators

- Clean module boundaries.
- No circular dependencies.
- APIs documented using OpenAPI.
- Database migrations version-controlled.
- Unit and integration tests available for critical modules.
- Common functionality implemented as reusable packages.
- Secrets never committed to the repository.

---

# 8. Operational Goals

Acadedx must be reliable and manageable.

## Goals

- Provide admin tools for platform operations.
- Monitor AI usage and cost.
- Monitor payment and subscription health.
- Track security events.
- Support audit logs.
- Support customer support workflows.
- Support operational reporting.

## Operational Success Indicators

- Platform uptime above 99.9%.
- Critical incidents detected quickly.
- Admin actions audited.
- Failed payments visible.
- AI provider issues visible.
- Support team can identify user issues without developer involvement.

---

# North Star Metric

The primary North Star Metric for Acadedx is:

> **Students achieving measurable learning improvement through consistent platform usage.**

This is better than measuring only downloads, registrations, or AI requests because it reflects the actual mission of the product.

---

# Supporting Metrics

## Learning Metrics

- Learning Mastery Score
- Quiz Accuracy Improvement
- Weak Topics Improved
- Homework Completion Rate
- Worksheet Completion Rate
- Study Streak
- Revision Completion Rate

## Engagement Metrics

- Daily Active Users
- Weekly Active Users
- Monthly Active Users
- Session Duration
- Feature Adoption
- Dashboard Return Rate

## AI Metrics

- AI Response Rating
- AI Accuracy Review Score
- OCR Success Rate
- AI Cost Per User
- AI Response Time
- Token Usage

## Business Metrics

- Free-to-paid Conversion
- Monthly Recurring Revenue
- Annual Recurring Revenue
- Churn Rate
- Customer Lifetime Value
- Customer Acquisition Cost
- Institution Subscription Count

## Platform Metrics

- Active Organizations
- Active Institutions
- Active Teachers
- Active Students
- Institution Seat Utilization
- Organization-level Usage

---

# MVP Goals

The MVP should prove the following:

1. Students find AI homework help useful.
2. Students understand explanations better than generic AI answers.
3. Students return for quizzes, notes, and worksheets.
4. Parents see value in progress insights.
5. Teachers see value in AI-generated learning material.
6. Schools see potential in institution-level learning analytics.
7. The architecture can support future organization and institution expansion.

---

# Out of Scope for MVP Goals

The MVP is not expected to prove:

- Multi-institution organization management.
- Enterprise SSO.
- White-label deployments.
- Marketplace monetization.
- Global curriculum coverage.
- University-specific workflows.
- Government education deployments.
- Corporate learning workflows.
- Offline-first learning.
- Public API business model.

These are future goals, not MVP goals.

---

# Objective and Key Results

## Objective 1: Launch a usable AI learning MVP

### Key Results

- AI Homework Solver released.
- OCR pipeline released.
- AI Tutor released.
- Notes, Quiz, and Worksheet modules released.
- Student Dashboard released.
- Basic analytics available.

---

## Objective 2: Improve student learning behavior

### Key Results

- Average study streak reaches 7 days.
- At least 50% of active students complete one quiz per week.
- At least 40% of solved homework generates a follow-up learning action.
- At least 30% of users return within 7 days.

---

## Objective 3: Validate monetization

### Key Results

- Free plan launched.
- Premium plan launched.
- Subscription and entitlement system implemented.
- Free-to-paid conversion measured.
- AI cost per user tracked.

---

## Objective 4: Validate institution readiness

### Key Results

- Institution entity implemented.
- Teacher role implemented.
- Basic institution dashboard available.
- Institution-level users supported.
- Institution-level analytics available.

---

## Objective 5: Build future-ready architecture

### Key Results

- Organization entity implemented.
- Institution entity implemented.
- One-institution-per-organization MVP constraint enforced through business rules.
- No code assumes only schools exist.
- Institution type field available.
- APIs support organization and institution context.

---

# Product Management Rules

Every roadmap item must be classified as one of the following:

- MVP Required
- Post-MVP Growth
- Enterprise Future
- Experimental
- Deferred

Features should not be added to MVP only because they may be useful later.

The constraint here is execution focus.

A future-ready data model is valuable.

A bloated MVP is dangerous.

---

# Review Schedule

This document should be reviewed:

- Before every major roadmap update.
- Before MVP development begins.
- After MVP user feedback.
- Before adding institution or organization-level features.
- Quarterly after launch.

---

# Related Documents

- PROJECT_OVERVIEW.md
- VISION.md
- MISSION.md
- SUCCESS_METRICS.md
- PRODUCT_PRINCIPLES.md
- PRD.md
- ROADMAP.md