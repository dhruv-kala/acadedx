# Typography

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Product Design  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the typography system for Acadedx.

Typography must support a clear, professional, accessible, and education-focused product experience across public pages, authentication, onboarding, admin workflows, institution operations, teacher workflows, guardian experiences, learner experiences, reports, and AI-assisted features.

Acadedx is a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

UI copy may use school-friendly labels such as “School”, “Student”, and “Parent” where appropriate, but code-level naming, route naming, API contracts, and documentation terminology should remain aligned with:

```text
Organization
Institution
Learner
Guardian
Teacher
```

---

# Typography Goals

The Acadedx typography system must ensure:

1. Clear hierarchy across dashboards, forms, tables, reports, and detail pages.
2. High readability for operational education workflows.
3. Accessibility across desktop, tablet, and basic mobile web.
4. Consistency across public, app, admin, and role-specific surfaces.
5. Support for data-heavy screens without visual clutter.
6. Calm, professional, and trustworthy tone.
7. Future readiness for localization and multi-language support.
8. Clear distinction between labels, helper text, body text, metadata, and status text.
9. Safe treatment of sensitive learner, guardian, and AI-generated information.
10. Compatibility with Tailwind CSS and design tokens.

---

# Typography Principles

## 1. Readability First

Acadedx users will work with records, tables, forms, reports, and academic data.

Typography should prioritize:

* Clear labels
* Scannable tables
* Readable form fields
* Strong page hierarchy
* Legible status indicators
* Comfortable line height
* Avoidance of overly decorative type

---

## 2. Professional and Calm

The typography should feel:

```text
Professional
Academic
Clear
Modern
Trustworthy
Efficient
```

Avoid typography that feels:

```text
Playful by default
Overly decorative
Dense without hierarchy
Too corporate
Too casual
Difficult to scan
```

---

## 3. Hierarchy Must Be Obvious

Users should immediately understand:

* Page title
* Section title
* Primary action
* Form label
* Field helper text
* Table column heading
* Error message
* Status label
* Metadata

Typography should create hierarchy without relying only on color.

---

## 4. Data Screens Need Moderate Density

Acadedx has many operational screens:

```text
Learners
Guardians
Teachers
Attendance
Assessments
Reports
Audit Logs
Users
Institutions
```

Typography must support compact but readable data displays.

Do not use oversized text in dense admin tables.

Do not use tiny text for critical academic or learner information.

---

## 5. AI Content Must Be Clearly Labeled

AI-generated text should be visually and textually identified.

Required labels may include:

```text
AI-generated
Suggested
Review before sharing
Generated at
Source context
```

Do not make AI-generated content visually indistinguishable from official human-entered academic records when the distinction matters.

---

# Font Family

Recommended primary font family:

```text
Inter
```

Fallback stack:

```css
font-family: Inter, ui-sans-serif, system-ui, -apple-system, BlinkMacSystemFont, "Segoe UI", sans-serif;
```

Reason:

* Highly readable
* Strong UI support
* Good numeric rendering
* Works well in dashboards and forms
* Common in modern SaaS interfaces
* Compatible with dense tables

---

# Alternative Font Options

Acceptable alternatives if Inter is not used:

```text
Geist Sans
Noto Sans
Source Sans 3
IBM Plex Sans
Roboto
```

Selection criteria:

* Good readability
* Good Indian language expansion path where needed
* Strong number rendering
* Works at small sizes
* Good browser rendering
* Open licensing

---

# Monospace Font

Use monospace fonts only for technical content.

Recommended monospace stack:

```css
font-family: "JetBrains Mono", "SFMono-Regular", Consolas, "Liberation Mono", Menlo, monospace;
```

Use monospace for:

```text
Request IDs
Error codes
API paths
Code examples
Technical logs
Developer documentation
```

Do not use monospace for normal user-facing academic content.

---

# Font Weight Scale

Recommended font weights:

| Token           | Weight | Usage                                         |
| --------------- | -----: | --------------------------------------------- |
| `font-normal`   |    400 | Body text                                     |
| `font-medium`   |    500 | Labels, table headers, subtle emphasis        |
| `font-semibold` |    600 | Section titles, card titles, important labels |
| `font-bold`     |    700 | Page titles and major headings                |

Avoid using very heavy weights unless necessary.

Recommended maximum for product UI:

```text
700
```

---

# Type Scale

Recommended typography scale:

| Token       | Size | Line Height | Usage                                        |
| ----------- | ---: | ----------: | -------------------------------------------- |
| `text-xs`   | 12px |        16px | Metadata, badges, helper labels              |
| `text-sm`   | 14px |        20px | Secondary body, table text, form helper text |
| `text-base` | 16px |        24px | Main body text, form inputs                  |
| `text-lg`   | 18px |        28px | Card titles, section emphasis                |
| `text-xl`   | 20px |        28px | Section headings                             |
| `text-2xl`  | 24px |        32px | Page headings                                |
| `text-3xl`  | 30px |        36px | Public page headings, major dashboards       |
| `text-4xl`  | 36px |        40px | Marketing hero heading                       |
| `text-5xl`  | 48px |        56px | Public landing hero only                     |

MVP authenticated app should mostly use:

```text
text-xs
text-sm
text-base
text-lg
text-xl
text-2xl
```

Use larger sizes primarily for public marketing pages.

---

# Recommended UI Typography Tokens

## Display

Use only for public website hero sections or major marketing pages.

```text
display-lg: 48px / 56px / 700
display-md: 36px / 44px / 700
display-sm: 30px / 38px / 700
```

MVP app screens should rarely use display text.

---

## Heading

Use for product app page and section hierarchy.

```text
heading-xl: 30px / 36px / 700
heading-lg: 24px / 32px / 700
heading-md: 20px / 28px / 600
heading-sm: 18px / 28px / 600
heading-xs: 16px / 24px / 600
```

---

## Body

Use for main content.

```text
body-lg: 18px / 28px / 400
body-md: 16px / 24px / 400
body-sm: 14px / 20px / 400
body-xs: 12px / 16px / 400
```

---

## Label

Use for form labels, table headers, navigation, and UI controls.

```text
label-lg: 16px / 24px / 500
label-md: 14px / 20px / 500
label-sm: 12px / 16px / 500
```

---

## Caption

Use for metadata and helper text.

```text
caption-md: 13px / 18px / 400
caption-sm: 12px / 16px / 400
```

---

# Page Typography

## Page Title

Use for main page title.

Recommended:

```text
24px / 32px / 700
```

Example:

```text
Learners
Attendance
Assessments
Institution Settings
```

School MVP UI may show:

```text
Students
School Settings
```

but code and documentation should still use Learners and Institution.

---

## Page Description

Use under page titles.

Recommended:

```text
14px / 20px / 400
```

Example:

```text
Manage learner records, guardians, attendance, and assessments for this institution.
```

Keep descriptions concise.

---

## Section Title

Use for grouped content sections.

Recommended:

```text
18px / 28px / 600
```

Examples:

```text
Basic Details
Academic Assignment
Guardian Information
Attendance Summary
Assessment Results
```

---

## Card Title

Use for dashboard cards or summary panels.

Recommended:

```text
16px / 24px / 600
```

---

## Card Value

Use for metrics.

Recommended:

```text
24px / 32px / 700
```

Example:

```text
1,248
92%
```

Use larger values only when dashboard emphasis requires it.

---

# Form Typography

## Form Label

Recommended:

```text
14px / 20px / 500
```

Example:

```text
Admission Number
Academic Year
Class
Section
Primary Guardian
```

Labels must always be visible.

Do not rely only on placeholders.

---

## Form Input Text

Recommended:

```text
16px / 24px / 400
```

Reason:

* Improves readability
* Helps mobile input usability
* Avoids browser zoom issues on mobile

---

## Helper Text

Recommended:

```text
13px / 18px / 400
```

Example:

```text
Admission number must be unique within this institution.
```

---

## Error Text

Recommended:

```text
13px / 18px / 500
```

Use clear language.

Good:

```text
Admission number is already used in this institution.
```

Bad:

```text
Unique constraint failed on learners_admission_number_key.
```

---

## Required Indicator

Use visible required indicators.

Example:

```text
Full Name *
```

Do not use color alone to indicate required fields.

---

# Table Typography

Tables need high readability at moderate density.

## Table Header

Recommended:

```text
12px / 16px / 600
```

Style:

```text
Uppercase optional
Letter spacing slight optional
Muted foreground
```

Example:

```text
NAME
CLASS
SECTION
STATUS
ACTIONS
```

Avoid excessive uppercase in dense tables if it reduces readability.

---

## Table Cell

Recommended:

```text
14px / 20px / 400
```

Use `font-medium` for primary entity name in each row.

Example:

```text
Riya Sharma
ADM-2026-001
Grade 8
A
ACTIVE
```

---

## Table Metadata

Recommended:

```text
12px / 16px / 400
```

Use for secondary row information:

```text
Admission No. ADM-2026-001
Last updated 05 Jul 2026
```

---

# Navigation Typography

## Sidebar Item

Recommended:

```text
14px / 20px / 500
```

Active item may use:

```text
font-semibold
```

Navigation labels should be concise:

```text
Dashboard
Academic
Learners
Guardians
Teachers
Attendance
Assessments
Reports
Settings
```

---

## Top Navigation

Recommended:

```text
14px / 20px / 500
```

Use clear labels for:

```text
Institution switcher
User menu
Notifications
Help
```

---

## Breadcrumbs

Recommended:

```text
13px / 18px / 400
```

Current page may use:

```text
font-medium
```

Avoid very long breadcrumb labels.

Do not include sensitive details unnecessarily.

---

# Button Typography

## Default Button

Recommended:

```text
14px / 20px / 500
```

## Large Button

Recommended:

```text
16px / 24px / 500
```

## Small Button

Recommended:

```text
13px / 18px / 500
```

Button text should be action-oriented.

Good:

```text
Add Learner
Save Changes
Invite User
Mark Attendance
Enter Results
Generate Report
```

Avoid vague labels:

```text
Submit
Proceed
Execute
```

unless the context is clear.

---

# Badge Typography

Badges should use compact but readable text.

Recommended:

```text
12px / 16px / 500
```

Examples:

```text
ACTIVE
PENDING
LOCKED
AI-GENERATED
```

Badges must include text, not color alone.

---

# Status Typography

Status labels should be concise and consistent.

Good:

```text
Active
Pending
Suspended
Archived
Processing
Failed
Succeeded
Locked
```

Avoid inconsistent alternatives:

```text
Enabled
Live
Running
Done
Complete
```

unless they are explicitly mapped.

---

# Error and Alert Typography

## Alert Title

Recommended:

```text
14px / 20px / 600
```

## Alert Body

Recommended:

```text
14px / 20px / 400
```

Error and alert copy should be short and safe.

Example:

```text
We could not load learners.
Try again. If the issue continues, contact support with Request ID req_123.
```

---

# Empty State Typography

## Empty State Title

Recommended:

```text
18px / 28px / 600
```

## Empty State Body

Recommended:

```text
14px / 20px / 400
```

Example:

```text
No learners found.
Add your first learner to start managing attendance and assessments.
```

Avoid:

```text
No data.
```

---

# Dashboard Typography

Dashboard cards should use consistent hierarchy.

## Metric Label

Recommended:

```text
13px / 18px / 500
```

## Metric Value

Recommended:

```text
24px / 32px / 700
```

## Metric Hint

Recommended:

```text
12px / 16px / 400
```

Example:

```text
Learners
1,248
+32 this academic year
```

---

# Report Typography

Reports should be readable and printable.

## Report Title

Recommended:

```text
24px / 32px / 700
```

## Report Subtitle / Scope

Recommended:

```text
14px / 20px / 400
```

Example:

```text
Academic Year: 2026-2027 · Class: Grade 8 · Section: A
```

## Report Table Text

Recommended:

```text
12px to 14px
```

Print exports may use slightly smaller table text if needed, but must remain readable.

---

# AI Typography

AI-generated content should be clear and visibly labeled.

## AI Label

Recommended:

```text
12px / 16px / 600
```

Example:

```text
AI-generated
Suggested insight
Review before sharing
```

## AI Body Text

Recommended:

```text
14px / 22px / 400
```

AI content should avoid overconfident wording.

Good:

```text
This summary suggests that the learner may need additional support in fractions.
```

Avoid:

```text
The learner will fail mathematics unless immediate action is taken.
```

---

# Notification Typography

Notifications should be short and privacy-safe.

## Notification Title

Recommended:

```text
14px / 20px / 600
```

## Notification Body

Recommended:

```text
13px / 18px / 400
```

Good:

```text
A new academic update is available.
```

Avoid sensitive details in notification previews.

---

# Modal Typography

## Modal Title

Recommended:

```text
18px / 28px / 600
```

## Modal Body

Recommended:

```text
14px / 20px / 400
```

## Modal Actions

Use standard button typography.

For destructive confirmations, body text must clearly explain consequence.

---

# Auth Page Typography

Authentication pages should be calm and clear.

## Auth Title

Recommended:

```text
24px / 32px / 700
```

Examples:

```text
Sign in to Acadedx
Accept invitation
Reset password
```

## Auth Helper Text

Recommended:

```text
14px / 20px / 400
```

Avoid long technical explanations.

---

# Public Website Typography

Public pages may use larger typography.

## Hero Title

Recommended:

```text
40px to 48px / 48px to 56px / 700
```

## Hero Subtitle

Recommended:

```text
18px to 20px / 28px to 32px / 400
```

## Section Title

Recommended:

```text
30px to 36px / 38px to 44px / 700
```

Public pages should still remain professional and education-focused.

---

# Line Length

For body text, aim for:

```text
60 to 80 characters per line
```

Avoid long full-width paragraphs.

For dashboards and tables, prioritize scanability over paragraph layout.

---

# Line Height

Recommended line heights:

| Text Type         |  Line Height |
| ----------------- | -----------: |
| Dense table text  | 1.35 to 1.45 |
| Normal body text  |          1.5 |
| Long-form content |          1.6 |
| Headings          |  1.2 to 1.35 |

Avoid cramped multi-line content in reports, forms, and AI summaries.

---

# Letter Spacing

Use normal letter spacing for most UI text.

Optional small positive letter spacing may be used for:

```text
Table headers
Small uppercase labels
Badges
```

Avoid excessive letter spacing.

---

# Text Transform

Use uppercase sparingly.

Acceptable uppercase uses:

```text
Status badges
Short table headers
Small metadata labels
Error codes in developer/admin views
```

Avoid uppercase for long labels or body text.

---

# Numeric Typography

Use tabular numbers where alignment matters.

Recommended for:

```text
Dashboard metrics
Attendance percentages
Fee amounts
Assessment marks
Report tables
Analytics charts
```

CSS:

```css
font-variant-numeric: tabular-nums;
```

Use locale-aware formatting for numbers and currency.

---

# Date and Time Typography

Date and time text should be clear and consistent.

Recommended examples:

```text
05 Jul 2026
05 Jul 2026, 10:30 AM
10:30 AM
```

Use Institution timezone for academic operations where applicable.

Use muted text for metadata timestamps.

---

# Currency Typography

Currency should be explicit.

Examples:

```text
₹2,500
INR 2,500
₹2,500.00
```

Do not assume INR forever in code or design.

Future multi-region support may require different currencies.

---

# Accessibility Requirements

Typography must meet accessibility expectations.

Requirements:

* Body text should generally be at least 14px.
* Form input text should generally be 16px.
* Contrast must meet WCAG expectations.
* Text should remain readable at 200% zoom.
* Do not communicate meaning by typography style alone.
* Avoid very light font weights for important content.
* Avoid small text for critical errors.
* Links must be visually identifiable.

---

# Responsive Typography

Typography should scale carefully by viewport.

Recommended approach:

* Use consistent app typography on desktop and tablet.
* Avoid shrinking body text below readable sizes.
* Public hero text may scale down on mobile.
* Tables may use 13px to 14px but must remain readable.
* Form inputs should remain 16px on mobile.

Example mobile adjustment:

```text
Hero title: 48px desktop → 32px mobile
Page title: 24px desktop → 22px mobile
Body text: keep 16px
Table text: 14px or responsive card layout
```

---

# Localization Readiness

MVP may be English-first, but typography must support future localization.

Guidelines:

* Avoid fixed-width text containers that break with longer strings.
* Avoid embedding text inside images.
* Use flexible buttons.
* Allow labels to wrap.
* Support longer translated headings.
* Avoid relying on uppercase transformations for languages where it is inappropriate.

Potential future language support may require font review.

For Indian language support, consider compatibility with:

```text
Noto Sans
Noto Sans Devanagari
Noto Sans Bengali
Noto Sans Tamil
Noto Sans Telugu
Noto Sans Kannada
Noto Sans Malayalam
```

---

# Content Style and Typography

Typography and copy should work together.

Preferred copy style:

```text
Clear
Direct
Specific
Calm
Professional
Action-oriented
```

Good:

```text
Invite Teacher
Mark Attendance
Enter Assessment Results
Archive Learner
```

Avoid:

```text
Click here
Proceed
Do the needful
Manage data
```

---

# Sensitive Data Typography

Do not visually overemphasize sensitive learner or guardian data unless necessary.

Examples:

* Avoid huge red labels for learner risk.
* Avoid exposing full guardian contact details in list views.
* Avoid placing sensitive details in page titles.
* Avoid putting learner assessment failure in notification preview text.

Use neutral, professional presentation.

---

# Typography in Error Codes

Developer/admin-facing error codes may use monospace.

Example:

```text
TENANT_SCOPE_VIOLATION
AUTH_TOKEN_EXPIRED
LEARNER_NOT_FOUND
```

User-facing explanations should use normal UI typography.

---

# Typography Tokens for Tailwind

Recommended token mapping:

```ts
fontFamily: {
  sans: [
    'Inter',
    'ui-sans-serif',
    'system-ui',
    '-apple-system',
    'BlinkMacSystemFont',
    'Segoe UI',
    'sans-serif',
  ],
  mono: [
    'JetBrains Mono',
    'SFMono-Regular',
    'Consolas',
    'Liberation Mono',
    'Menlo',
    'monospace',
  ],
},
fontSize: {
  xs: ['12px', { lineHeight: '16px' }],
  sm: ['14px', { lineHeight: '20px' }],
  base: ['16px', { lineHeight: '24px' }],
  lg: ['18px', { lineHeight: '28px' }],
  xl: ['20px', { lineHeight: '28px' }],
  '2xl': ['24px', { lineHeight: '32px' }],
  '3xl': ['30px', { lineHeight: '36px' }],
  '4xl': ['36px', { lineHeight: '40px' }],
  '5xl': ['48px', { lineHeight: '56px' }],
}
```

---

# MVP Typography Requirements

The MVP must implement:

```text
Primary sans font stack
Monospace font stack
Type scale from text-xs to text-3xl for app UI
Display styles for public landing page
Heading styles
Body text styles
Label styles
Caption styles
Table typography
Form typography
Button typography
Badge typography
Error text typography
Empty state typography
Dashboard metric typography
AI label typography
Responsive typography rules
Accessibility-safe minimums
```

---

# Deferred Typography Capabilities

The following may be deferred beyond MVP:

```text
Full multilingual typography tuning
Custom institution typography branding
Print-specific report typography system
Advanced chart label typography
Mobile app-specific type scale
Learner gamification typography
White-label font customization
```

Do not block these future capabilities with hardcoded typography assumptions.

---

# Typography Anti-Patterns

Avoid:

```text
Using decorative fonts for operational UI
Using very small text for important data
Using low-contrast muted text for required fields
Using uppercase for long labels
Using vague button labels
Using Student/Parent/School in code-level component names
Using oversized dashboard text everywhere
Using AI-generated content without a label
Using raw error codes as the only user-facing message
```

---

# Typography Review Checklist

Before approving typography usage, confirm:

* Page hierarchy is clear.
* Body text is readable.
* Form labels are visible.
* Table text is scannable.
* Error text is readable and safe.
* Button labels are action-oriented.
* Badges include text.
* AI-generated content is labeled.
* Sensitive learner data is not overemphasized.
* Typography works on desktop, tablet, and basic mobile web.
* Contrast is accessible.
* Text can support future localization.
* Code terminology remains Institution, Learner, and Guardian.

---

# Related Documents

* docs/10-Design-System/DESIGN_SYSTEM.md
* docs/10-Design-System/UI_GUIDELINES.md
* docs/10-Design-System/COLORS.md
* docs/10-Design-System/COMPONENT_LIBRARY.md
* docs/08-Frontend/COMPONENTS.md
* docs/08-Frontend/PAGES.md
* docs/11-Security/PRIVACY.md