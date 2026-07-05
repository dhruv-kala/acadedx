# Acadedx Success Metrics

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Product Management  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the success metrics for Acadedx.

The metrics in this document help Product, Engineering, AI, Business, Design, Operations, and Leadership teams measure whether Acadedx is delivering real learning value and business progress.

Success must not be measured only by registrations, downloads, or AI request volume.

The primary measure of success is whether students improve learning outcomes through consistent platform usage.

---

# Measurement Philosophy

Acadedx should measure what matters.

The platform should track:

- Learning improvement
- Student engagement
- AI quality
- Parent visibility
- Teacher productivity
- Institution adoption
- Organization readiness
- Business sustainability
- Platform reliability
- Operational safety

Metrics should support product decisions, not create vanity dashboards.

---

# North Star Metric

## Students Achieving Learning Progress

The North Star Metric is:

> **Number of students who show measurable learning improvement through consistent Acadedx usage within a rolling 30-day period.**

A student is counted when they meet a defined learning improvement threshold based on:

- Quiz score improvement
- Weak topic improvement
- Homework completion
- Worksheet practice
- Study streak
- Revision completion
- Learning Mastery Score growth

---

# Supporting North Star Inputs

A student may be considered to have made learning progress when at least three of the following are true during a 30-day period:

- Completed at least 5 homework solutions.
- Completed at least 3 quizzes.
- Improved quiz accuracy by at least 10%.
- Completed at least 2 worksheets.
- Improved at least one weak topic.
- Maintained a study streak of 5 or more days.
- Completed at least one study plan goal.
- Increased Learning Mastery Score.

Exact thresholds may be adjusted after MVP data is collected.

---

# Metric Categories

Acadedx tracks metrics across the following categories:

1. Learning Metrics
2. Engagement Metrics
3. AI Quality Metrics
4. Product Usage Metrics
5. Parent Metrics
6. Teacher Metrics
7. Institution Metrics
8. Organization Metrics
9. Business Metrics
10. Engineering Metrics
11. Security and Trust Metrics
12. Support Metrics

---

# 1. Learning Metrics

Learning metrics determine whether Acadedx is helping students improve.

## Learning Mastery Score

Measures the student's overall mastery across subjects, chapters, and topics.

Inputs may include:

- Homework solution quality
- Quiz accuracy
- Worksheet completion
- Revision frequency
- Study consistency
- AI Tutor interaction quality

Target:

- MVP baseline only.
- Improvement targets should be set after real usage data is available.

---

## Quiz Accuracy Improvement

Measures whether students improve over time.

Formula:

```text
Current Quiz Accuracy - Previous Quiz Accuracy
```

Target:

- Positive improvement over 30 days.

---

## Weak Topics Improved

Measures how many previously weak topics moved to a better mastery level.

Example levels:

- Needs Practice
- Learning
- Good
- Mastered

---

## Homework Completion Rate

Measures percentage of homework workflows successfully completed.

Formula:

```text
Completed Homework / Started Homework
```

---

## Worksheet Completion Rate

Measures whether students practice after receiving explanations.

Formula:

```text
Completed Worksheets / Generated Worksheets
```

---

## Revision Completion Rate

Measures whether students complete recommended revision activities.

---

## Exam Readiness Score

Measures readiness for upcoming exams based on:

- Syllabus coverage
- Revision completion
- Quiz accuracy
- Weak topics remaining
- Study plan progress

---

# 2. Engagement Metrics

Engagement metrics measure whether users return and continue using the platform.

## Daily Active Users

Unique users active on a given day.

---

## Weekly Active Users

Unique users active during a rolling 7-day period.

---

## Monthly Active Users

Unique users active during a rolling 30-day period.

---

## DAU / MAU Ratio

Measures product stickiness.

Target:

- MVP baseline first.
- Long-term target should be above 25%.
- Strong consumer learning products may aim higher.

---

## Study Streak

Measures consecutive days of meaningful learning activity.

Meaningful activities include:

- Homework solved
- Quiz attempted
- Worksheet completed
- Notes reviewed
- Study task completed
- AI Tutor session completed

---

## Average Session Duration

Measures average time spent per learning session.

This should be interpreted carefully.

Longer is not always better.

The goal is effective learning, not screen addiction.

---

## Dashboard Return Rate

Measures how frequently users return to the dashboard.

---

# 3. AI Quality Metrics

AI metrics measure accuracy, safety, usefulness, and cost.

## AI Response Satisfaction

User rating after AI interactions.

Target:

```text
>= 4.5 / 5
```

---

## Homework Solution Success Rate

Measures the percentage of homework questions successfully processed and answered.

Target:

```text
>= 90%
```

---

## AI Explanation Helpfulness

Measures whether the student found the explanation understandable.

Collected through:

- Quick feedback buttons
- Follow-up behavior
- Quiz improvement
- Manual review

---

## OCR Accuracy

Measures accuracy of extracted text from images and PDFs.

Targets:

- Printed text: >= 95%
- Handwritten text: baseline during MVP
- Mathematical expressions: baseline during MVP

---

## AI Hallucination Rate

Measures inaccurate or unsupported AI-generated responses.

Target:

```text
< 2% after moderation and validation
```

---

## AI Response Time

Measures latency for AI workflows.

Targets:

- AI Tutor response: under 5 seconds for normal queries.
- Homework solution: under 15 seconds end-to-end.
- Notes or quiz generation: under 10 seconds where practical.

---

## AI Cost Per Active User

Measures AI provider cost per active user.

This is critical for subscription pricing and profitability.

---

# 4. Product Usage Metrics

These metrics measure adoption of core features.

## Homework Usage

Track:

- Homework uploaded
- Homework solved
- Homework failed
- Average processing time
- Follow-up learning actions

---

## Notes Usage

Track:

- Notes generated
- Notes reviewed
- Notes exported
- Notes converted into quiz or worksheet

---

## Quiz Usage

Track:

- Quizzes generated
- Quizzes attempted
- Quizzes completed
- Average score
- Retry rate

---

## Worksheet Usage

Track:

- Worksheets generated
- Worksheets downloaded
- Worksheets completed
- Worksheets generated from weak topics

---

## Study Planner Usage

Track:

- Study plans created
- Tasks completed
- Missed tasks
- Regenerated plans
- Exam plans created

---

## AI Tutor Usage

Track:

- Conversations started
- Conversations completed
- Follow-up questions
- Average messages per session
- Conversation satisfaction

---

# 5. Parent Metrics

Parent metrics measure whether parents receive meaningful value.

## Parent Activation Rate

Percentage of invited parents who create accounts.

---

## Parent Dashboard Usage

Track:

- Dashboard visits
- Child progress views
- Weekly report views
- Notification clicks

---

## Parent Insight Engagement

Measures whether parents act on recommendations.

Examples:

- Opens weak topic alert.
- Reviews weekly report.
- Starts conversation with child or teacher.
- Downloads progress report.

---

# 6. Teacher Metrics

Teacher metrics measure productivity and classroom value.

## Teacher Activation Rate

Percentage of invited teachers who complete onboarding.

---

## Assignment Creation Time

Measures time required to create homework, quiz, or worksheet.

Target:

- Reduce teacher effort compared to manual creation.

---

## AI Tool Adoption

Track teacher usage of:

- AI lesson planning
- Quiz generation
- Worksheet generation
- Assignment creation
- Student feedback generation

---

## Class Analytics Usage

Measures teacher engagement with student and class performance dashboards.

---

# 7. Institution Metrics

Institution metrics apply to schools in MVP and broader institution types in future.

## Active Institutions

Number of institutions actively using Acadedx.

In MVP, most institutions will be schools.

---

## Institution Activation

An institution is activated when:

- Institution profile is complete.
- At least one administrator is active.
- At least one teacher is active.
- At least five students are active.
- At least one learning activity is completed.

---

## Institution Seat Utilization

Formula:

```text
Active Seats / Purchased Seats
```

---

## Institution Learning Engagement

Measures usage across students and teachers in an institution.

Inputs:

- Active students
- Active teachers
- Homework completed
- Quizzes completed
- AI usage
- Reports viewed

---

## Institution Retention

Measures whether institutions continue using Acadedx after onboarding.

---

# 8. Organization Metrics

Organization metrics are future-ready and may be limited in MVP.

## Active Organizations

Number of organizations with at least one active institution.

---

## Institutions Per Organization

Measures organization expansion.

MVP constraint:

```text
1 institution per organization
```

Future:

```text
Multiple institutions per organization
```

---

## Organization Seat Utilization

Measures total usage across all institutions owned by an organization.

---

## Organization Revenue

Measures revenue at the organization level.

Useful for:

- Education groups
- Multi-school businesses
- Enterprise customers
- Franchise models

---

# 9. Business Metrics

Business metrics measure commercial sustainability.

## Free Users

Total users on free plan.

---

## Paid Users

Total users on paid plans.

---

## Free-to-Paid Conversion Rate

Formula:

```text
Paid Users / Free Users
```

---

## Monthly Recurring Revenue

Recurring subscription revenue per month.

---

## Annual Recurring Revenue

Annualized recurring revenue.

---

## Average Revenue Per User

Formula:

```text
Total Revenue / Active Users
```

---

## Customer Acquisition Cost

Marketing and sales cost required to acquire a customer.

---

## Customer Lifetime Value

Estimated total revenue from a customer over their lifetime.

---

## Churn Rate

Percentage of users or institutions canceling or not renewing.

---

## AI Gross Margin

Important because AI costs can reduce profitability.

Formula:

```text
Subscription Revenue - AI Cost - Infrastructure Cost
```

---

# 10. Engineering Metrics

Engineering metrics measure system quality and maintainability.

## API Response Time

Target:

```text
< 500 ms for standard APIs
```

---

## Page Load Time

Target:

```text
< 2 seconds for key pages
```

---

## Platform Uptime

Target:

```text
99.9%
```

---

## Error Rate

Target:

```text
< 1%
```

---

## Deployment Success Rate

Target:

```text
>= 95%
```

---

## Test Coverage

Targets:

- Critical modules: 90%+
- Standard modules: 80%+
- UI components: meaningful coverage for critical flows

---

## Mean Time to Recovery

Measures how quickly incidents are resolved.

---

# 11. Security and Trust Metrics

Security metrics protect students, parents, teachers, institutions, and DGIS.

## Failed Login Attempts

Track suspicious login activity.

---

## Account Lockouts

Track potential brute force attempts or user friction.

---

## Suspicious Activity Events

Examples:

- Unusual login location
- Excessive API requests
- Repeated failed payments
- Unusual AI prompt patterns

---

## Data Access Violations

Target:

```text
0 critical incidents
```

---

## Tenant Isolation Incidents

Target:

```text
0
```

No organization or institution should ever access another organization's or institution's data.

---

## Content Safety Incidents

Track unsafe AI content, harmful responses, policy violations, and moderation failures.

---

# 12. Support Metrics

Support metrics measure customer experience and operational quality.

## First Response Time

Target:

```text
< 2 business hours
```

---

## Resolution Time

Target:

```text
< 24 business hours for standard issues
```

---

## Customer Satisfaction

Target:

```text
>= 4.5 / 5
```

---

## Support Ticket Categories

Track:

- Login issues
- Payment issues
- AI quality issues
- OCR issues
- Institution onboarding
- Subscription questions
- Bug reports
- Feature requests

---

# Analytics Events

The following events must be captured.

## Authentication Events

- user_registered
- user_logged_in
- user_logged_out
- password_reset_requested
- password_changed
- email_verified

---

## Learning Events

- homework_uploaded
- homework_solved
- homework_failed
- ai_tutor_started
- ai_tutor_message_sent
- notes_generated
- quiz_generated
- quiz_started
- quiz_completed
- worksheet_generated
- worksheet_completed
- study_plan_created
- study_task_completed

---

## Dashboard Events

- dashboard_viewed
- recommendation_clicked
- widget_opened
- quick_action_clicked

---

## Parent Events

- parent_invited
- parent_linked_student
- parent_viewed_report
- parent_clicked_recommendation

---

## Teacher Events

- teacher_invited
- teacher_created_assignment
- teacher_generated_quiz
- teacher_generated_worksheet
- teacher_viewed_class_analytics

---

## Institution Events

- institution_created
- institution_onboarded
- institution_user_invited
- institution_dashboard_viewed
- institution_report_exported

---

## Organization Events

- organization_created
- organization_admin_invited
- organization_billing_updated
- organization_dashboard_viewed

---

## Commerce Events

- plan_viewed
- trial_started
- subscription_started
- subscription_upgraded
- subscription_downgraded
- subscription_cancelled
- payment_successful
- payment_failed
- invoice_generated

---

## AI Events

- ai_request_started
- ai_request_completed
- ai_request_failed
- ai_feedback_submitted
- ai_provider_selected
- ai_cost_recorded
- prompt_version_used

---

# Dashboard Requirements

Acadedx should support the following dashboards.

## Product Dashboard

Shows:

- Active users
- Feature adoption
- Retention
- Homework solved
- Quizzes completed
- Notes generated
- Worksheets generated
- Study plans created

---

## Learning Dashboard

Shows:

- Learning Mastery Score
- Weak topic improvement
- Quiz accuracy
- Study streak
- Revision completion
- Exam readiness

---

## AI Dashboard

Shows:

- AI requests
- Provider usage
- Token usage
- AI cost
- Response time
- Error rate
- User satisfaction
- Prompt version performance

---

## Business Dashboard

Shows:

- Free users
- Paid users
- MRR
- ARR
- Churn
- Conversion
- Revenue by plan
- AI gross margin

---

## Institution Dashboard

Shows:

- Active students
- Active teachers
- Homework completion
- Quiz performance
- AI usage
- Seat utilization
- Learning engagement

---

## Organization Dashboard

Future dashboard showing:

- Institutions owned
- Organization-wide usage
- Organization billing
- Seat utilization
- Institution comparison
- Enterprise analytics

---

# Metric Governance

Every metric must have:

- Clear definition
- Owner
- Data source
- Calculation formula
- Update frequency
- Dashboard location
- Privacy classification

Metrics without clear definitions should not be used for major decisions.

---

# Privacy Rules

Analytics must follow privacy-by-design principles.

Rules:

- Do not expose student-level data to unauthorized users.
- Parents can only view linked children.
- Teachers can only view assigned students.
- Institution administrators can only view their institution.
- Organization administrators can only view institutions under their organization.
- Platform administrators require audited access.
- Sensitive AI conversations should not be broadly visible.

---

# MVP Metrics

The MVP must track at minimum:

- Registered users
- Active students
- Homework uploaded
- Homework solved
- OCR success rate
- AI Tutor sessions
- Notes generated
- Quizzes completed
- Worksheets generated
- Study plans created
- Learning Mastery baseline
- Free-to-paid conversion
- AI cost per active user
- System error rate

---

# Out of Scope for MVP Metrics

The following may be deferred:

- Multi-institution organization comparison
- Enterprise analytics
- Marketplace analytics
- Public API analytics
- Advanced predictive analytics
- Career readiness scoring
- University readiness scoring

---

# Review Frequency

| Metric Category | Review Frequency |
|---|---|
| Learning Metrics | Weekly |
| Product Metrics | Weekly |
| AI Metrics | Weekly |
| Business Metrics | Monthly |
| Engineering Metrics | Daily |
| Security Metrics | Daily |
| Institution Metrics | Weekly |
| Organization Metrics | Monthly after launch |
| Executive Metrics | Monthly |

---

# Success Criteria

Acadedx is considered successful when:

- Students show measurable learning progress.
- Students return consistently.
- AI explanations are rated highly.
- Parents understand their child's learning progress.
- Teachers save time using AI tools.
- Institutions adopt Acadedx for real learning workflows.
- Subscriptions generate sustainable revenue.
- AI costs remain commercially viable.
- The platform maintains strong security and reliability.
- The architecture supports future organization and institution expansion.

---

# Related Documents

- PROJECT_OVERVIEW.md
- VISION.md
- MISSION.md
- GOALS.md
- PRODUCT_PRINCIPLES.md
- PRD.md
- ANALYTICS.md
- DATABASE_SCHEMA.md