# Colors

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Product Design  
**Company:** Digital Global Information Systems (DGIS)

---

# Purpose

This document defines the color system for Acadedx.

The color system must support a trustworthy, modern, accessible, and education-focused product experience.

Acadedx is a school-focused MVP with a future-ready architecture:

```text
Organization → Institution → Academic Operations
````

A School is an Institution type.

Color usage must support school-friendly UI labels where appropriate while keeping the underlying product design scalable for Institutions, Organizations, and future multi-institution customers.

---

# Color System Goals

The Acadedx color system must ensure:

1. Consistent visual identity across product surfaces.
2. Clear visual hierarchy.
3. Accessible contrast.
4. Professional and calm education-focused UI.
5. Safe handling of status, alerts, errors, and sensitive actions.
6. Clear distinction between neutral, primary, semantic, and data colors.
7. Support for light and dark themes.
8. Compatibility with Tailwind CSS and design tokens.
9. Future support for Organization or Institution-level branding.
10. Avoidance of color-only communication.

---

# Brand Color Direction

Acadedx should feel:

```text
Trustworthy
Calm
Modern
Academic
Efficient
AI-enabled
Professional
Accessible
```

The color palette should avoid:

```text
Overly playful colors
Excessive saturation
Distracting gradients
Unclear status colors
Low contrast text
Color-only meaning
```

---

# Recommended Base Palette

The recommended Acadedx MVP palette should use:

| Color Role | Direction                                      |
| ---------- | ---------------------------------------------- |
| Primary    | Deep blue or indigo for trust and intelligence |
| Secondary  | Teal or cyan accent for AI and innovation      |
| Neutral    | Slate/gray scale for app structure             |
| Success    | Green                                          |
| Warning    | Amber                                          |
| Error      | Red                                            |
| Info       | Blue                                           |
| Background | Light neutral                                  |
| Surface    | White or near-white                            |
| Border     | Soft neutral                                   |
| Text       | High-contrast neutral                          |

---

# Core Color Tokens

Use semantic color tokens rather than raw color names in components.

Good:

```text
bg-primary
text-primary
border-border
bg-success
text-error
```

Avoid:

```text
bg-blue-600
text-red-500
border-gray-200
```

Raw values may exist in the Tailwind configuration, but component code should prefer semantic tokens where practical.

---

# Primary Colors

Primary color is used for the main Acadedx brand and primary actions.

Recommended primary palette:

| Token         | Suggested Value | Usage                    |
| ------------- | --------------: | ------------------------ |
| `primary-50`  |       `#EFF6FF` | Light primary background |
| `primary-100` |       `#DBEAFE` | Subtle primary surface   |
| `primary-200` |       `#BFDBFE` | Soft primary border      |
| `primary-300` |       `#93C5FD` | Disabled or soft accent  |
| `primary-400` |       `#60A5FA` | Hover accent             |
| `primary-500` |       `#3B82F6` | General primary          |
| `primary-600` |       `#2563EB` | Primary button           |
| `primary-700` |       `#1D4ED8` | Primary hover            |
| `primary-800` |       `#1E40AF` | Strong primary           |
| `primary-900` |       `#1E3A8A` | Deep primary             |
| `primary-950` |       `#172554` | Darkest primary          |

Recommended default:

```text
primary: primary-600
primary-hover: primary-700
```

Use primary for:

* Primary buttons
* Active navigation
* Links
* Focus rings
* Selected tabs
* Key actions

Do not overuse primary for every visual element.

---

# Secondary Colors

Secondary color supports AI, innovation, and secondary emphasis.

Recommended secondary palette:

| Token           | Suggested Value | Usage                          |
| --------------- | --------------: | ------------------------------ |
| `secondary-50`  |       `#ECFEFF` | Light AI/innovation background |
| `secondary-100` |       `#CFFAFE` | Subtle secondary surface       |
| `secondary-200` |       `#A5F3FC` | Soft secondary border          |
| `secondary-300` |       `#67E8F9` | Light accent                   |
| `secondary-400` |       `#22D3EE` | Accent                         |
| `secondary-500` |       `#06B6D4` | Secondary                      |
| `secondary-600` |       `#0891B2` | Strong secondary               |
| `secondary-700` |       `#0E7490` | Hover secondary                |
| `secondary-800` |       `#155E75` | Deep secondary                 |
| `secondary-900` |       `#164E63` | Dark secondary                 |
| `secondary-950` |       `#083344` | Darkest secondary              |

Use secondary for:

* AI feature highlights
* Secondary action accents
* Informational illustrations
* Non-critical emphasis

Do not use secondary for error, warning, or destructive actions.

---

# Neutral Colors

Neutral colors provide app structure, text, borders, and surfaces.

Recommended neutral palette:

| Token         | Suggested Value | Usage               |
| ------------- | --------------: | ------------------- |
| `neutral-50`  |       `#F8FAFC` | App background      |
| `neutral-100` |       `#F1F5F9` | Subtle surface      |
| `neutral-200` |       `#E2E8F0` | Border              |
| `neutral-300` |       `#CBD5E1` | Strong border       |
| `neutral-400` |       `#94A3B8` | Muted text          |
| `neutral-500` |       `#64748B` | Secondary text      |
| `neutral-600` |       `#475569` | Body text muted     |
| `neutral-700` |       `#334155` | Body text           |
| `neutral-800` |       `#1E293B` | Heading text        |
| `neutral-900` |       `#0F172A` | Strong text         |
| `neutral-950` |       `#020617` | Dark app background |

Use neutral colors for:

* Body text
* Headings
* Borders
* Dividers
* Backgrounds
* Cards
* Tables
* Sidebar surfaces

---

# Semantic Colors

Semantic colors communicate status and state.

Semantic color must always be paired with text, icon, or label. Do not rely on color alone.

---

# Success Colors

Use for successful completion, active positive states, and safe confirmations.

| Token         | Suggested Value |
| ------------- | --------------: |
| `success-50`  |       `#F0FDF4` |
| `success-100` |       `#DCFCE7` |
| `success-200` |       `#BBF7D0` |
| `success-500` |       `#22C55E` |
| `success-600` |       `#16A34A` |
| `success-700` |       `#15803D` |
| `success-900` |       `#14532D` |

Use for:

```text
Successful save
Active status
Payment received
Attendance completed
Report generated
Job succeeded
```

Avoid using success green for general decoration.

---

# Warning Colors

Use for caution, pending decisions, incomplete setup, and recoverable issues.

| Token         | Suggested Value |
| ------------- | --------------: |
| `warning-50`  |       `#FFFBEB` |
| `warning-100` |       `#FEF3C7` |
| `warning-200` |       `#FDE68A` |
| `warning-500` |       `#F59E0B` |
| `warning-600` |       `#D97706` |
| `warning-700` |       `#B45309` |
| `warning-900` |       `#78350F` |

Use for:

```text
Pending setup
Unverified email
Incomplete learner profile
Attendance not submitted
Assessment results pending
Job waiting
```

Do not use warning where error is more appropriate.

---

# Error Colors

Use for destructive actions, validation errors, failed operations, and security-sensitive failures.

| Token       | Suggested Value |
| ----------- | --------------: |
| `error-50`  |       `#FEF2F2` |
| `error-100` |       `#FEE2E2` |
| `error-200` |       `#FECACA` |
| `error-500` |       `#EF4444` |
| `error-600` |       `#DC2626` |
| `error-700` |       `#B91C1C` |
| `error-900` |       `#7F1D1D` |

Use for:

```text
Validation error
Delete/archive confirmation
Failed job
Failed payment
Unauthorized access
Security warning
Suspended account
```

Destructive primary buttons should use error styling only when the user is confirming the destructive action.

---

# Info Colors

Use for neutral information, guidance, and system notices.

| Token      | Suggested Value |
| ---------- | --------------: |
| `info-50`  |       `#EFF6FF` |
| `info-100` |       `#DBEAFE` |
| `info-200` |       `#BFDBFE` |
| `info-500` |       `#3B82F6` |
| `info-600` |       `#2563EB` |
| `info-700` |       `#1D4ED8` |
| `info-900` |       `#1E3A8A` |

Use for:

```text
Tips
Help text
System notices
Informational banners
Context guidance
```

---

# AI Accent Colors

AI features should be visually identifiable but not distracting.

Recommended AI accent:

```text
secondary/cyan + primary/blue
```

Use AI accent for:

* AI-generated label
* AI insight card accent
* AI draft indicator
* AI job status icon
* Model-assisted feature hints

Do not use AI colors to imply certainty or authority.

AI outputs should always be labeled clearly:

```text
AI-generated
Suggested
Review before sharing
```

---

# Background and Surface Tokens

Recommended light theme tokens:

| Token              | Suggested Value | Usage            |
| ------------------ | --------------: | ---------------- |
| `background`       |       `#F8FAFC` | App background   |
| `foreground`       |       `#0F172A` | Main text        |
| `surface`          |       `#FFFFFF` | Cards, panels    |
| `surface-muted`    |       `#F1F5F9` | Secondary panels |
| `border`           |       `#E2E8F0` | Default border   |
| `border-strong`    |       `#CBD5E1` | Strong border    |
| `muted`            |       `#64748B` | Muted text       |
| `muted-foreground` |       `#475569` | Secondary text   |

Recommended dark theme tokens:

| Token              | Suggested Value | Usage            |
| ------------------ | --------------: | ---------------- |
| `background`       |       `#020617` | App background   |
| `foreground`       |       `#F8FAFC` | Main text        |
| `surface`          |       `#0F172A` | Cards, panels    |
| `surface-muted`    |       `#1E293B` | Secondary panels |
| `border`           |       `#334155` | Default border   |
| `border-strong`    |       `#475569` | Strong border    |
| `muted`            |       `#94A3B8` | Muted text       |
| `muted-foreground` |       `#CBD5E1` | Secondary text   |

Dark theme may be deferred if MVP needs speed, but token naming must not block it.

---

# Text Colors

Recommended text usage:

| Token             | Usage                      |
| ----------------- | -------------------------- |
| `text-foreground` | Main text                  |
| `text-heading`    | Page titles and headings   |
| `text-muted`      | Secondary text             |
| `text-subtle`     | Helper text                |
| `text-disabled`   | Disabled content           |
| `text-primary`    | Links and primary emphasis |
| `text-error`      | Error messages             |
| `text-success`    | Success messages           |
| `text-warning`    | Warning messages           |

Do not use low-contrast muted text for important information.

---

# Border Colors

Recommended border usage:

| Token            | Usage                          |
| ---------------- | ------------------------------ |
| `border`         | Default border                 |
| `border-muted`   | Subtle separators              |
| `border-strong`  | Table boundaries, active cards |
| `border-primary` | Focus or selected state        |
| `border-error`   | Validation errors              |
| `border-warning` | Warning states                 |
| `border-success` | Success states                 |

---

# Button Color Usage

## Primary Button

Use for the main action on a page.

Examples:

```text
Add Learner
Save Changes
Mark Attendance
Create Assessment
Invite User
```

Color:

```text
background: primary-600
hover: primary-700
text: white
```

---

## Secondary Button

Use for secondary actions.

Examples:

```text
Cancel
Back
Preview
View Details
```

Color:

```text
background: surface
border: border
text: foreground
hover: surface-muted
```

---

## Destructive Button

Use for destructive confirmed actions.

Examples:

```text
Archive Learner
Delete File
Suspend User
Deactivate Institution
```

Color:

```text
background: error-600
hover: error-700
text: white
```

Do not use destructive color for non-destructive warnings.

---

## Ghost Button

Use for low-emphasis actions.

Examples:

```text
Open menu
Clear filters
Dismiss
```

Color:

```text
background: transparent
hover: surface-muted
text: foreground
```

---

# Link Colors

Links should use primary color.

Recommended:

```text
text-primary
hover: text-primary-hover
underline on hover or clear affordance
```

Links must meet accessibility contrast requirements.

Do not use low-contrast blue on light backgrounds.

---

# Status Badge Colors

Status badges must include text labels.

Recommended mapping:

| Status       | Color              |
| ------------ | ------------------ |
| `ACTIVE`     | Success            |
| `INACTIVE`   | Neutral            |
| `PENDING`    | Warning            |
| `SUSPENDED`  | Error              |
| `ARCHIVED`   | Neutral            |
| `DRAFT`      | Neutral            |
| `PUBLISHED`  | Success            |
| `PROCESSING` | Info               |
| `FAILED`     | Error              |
| `SUCCEEDED`  | Success            |
| `LOCKED`     | Warning or Neutral |

Do not rely on color alone.

---

# Attendance Status Colors

Attendance statuses should be consistent.

| Attendance Status | Color   |
| ----------------- | ------- |
| `PRESENT`         | Success |
| `ABSENT`          | Error   |
| `LATE`            | Warning |
| `EXCUSED`         | Info    |
| `HALF_DAY`        | Warning |

Each status must show text.

---

# Assessment Status Colors

Assessment statuses:

| Assessment Status   | Color              |
| ------------------- | ------------------ |
| `DRAFT`             | Neutral            |
| `SCHEDULED`         | Info               |
| `PUBLISHED`         | Success            |
| `RESULTS_PENDING`   | Warning            |
| `RESULTS_SUBMITTED` | Success            |
| `LOCKED`            | Neutral or Warning |
| `ARCHIVED`          | Neutral            |

---

# Job Status Colors

Background job statuses:

| Job Status   | Color   |
| ------------ | ------- |
| `QUEUED`     | Neutral |
| `PROCESSING` | Info    |
| `SUCCEEDED`  | Success |
| `FAILED`     | Error   |
| `CANCELLED`  | Neutral |
| `EXPIRED`    | Warning |

---

# Role Color Usage

Avoid assigning strong permanent colors to roles unless necessary.

If role badges are used:

| Role Category      | Color Direction          |
| ------------------ | ------------------------ |
| Platform roles     | Purple or neutral strong |
| Organization roles | Blue                     |
| Institution roles  | Indigo                   |
| Teacher roles      | Teal                     |
| Guardian roles     | Amber or neutral         |
| Learner roles      | Green or neutral         |

Role color must not imply access level without text.

Example badge:

```text
Institution Admin
```

not just a colored dot.

---

# Chart and Data Visualization Colors

Charts should use accessible, distinguishable colors.

Guidelines:

* Use limited color sets.
* Use labels and legends.
* Avoid color-only interpretation.
* Use semantic colors only for semantic meaning.
* Do not use red/green alone where colorblind confusion is possible.
* For comparisons, use neutral/primary palette variants.
* For statuses, use semantic colors.

Recommended chart sequence:

```text
primary-600
secondary-600
success-600
warning-600
neutral-600
primary-800
secondary-800
```

Advanced chart palette may be refined later.

---

# Data Risk Color Usage

Do not use alarming colors for learner performance unless the meaning is clear and reviewed.

Bad:

```text
Large red “At Risk” badge without explanation.
```

Better:

```text
Needs Attention
Attendance is below the configured threshold.
```

Use calm warning language for sensitive learner insights.

---

# Accessibility Requirements

Color usage must meet accessibility requirements.

Minimum expectations:

| Element                          | Minimum Contrast |
| -------------------------------- | ---------------: |
| Body text                        |            4.5:1 |
| Large text                       |              3:1 |
| UI controls and focus indicators |              3:1 |
| Icons conveying meaning          |              3:1 |

Do not rely on color alone.

Every color-coded state must also include one or more of:

```text
Text label
Icon
Pattern
Tooltip
Description
ARIA label
```

---

# Focus Colors

Focus states must be visible.

Recommended focus treatment:

```text
outline or ring: primary-500
offset: 2px
minimum visible contrast: 3:1
```

Do not remove outlines without replacing them with accessible focus styles.

---

# Disabled Colors

Disabled UI should be visibly disabled but still readable enough where text matters.

Recommended:

```text
background: neutral-100
text: neutral-400
border: neutral-200
cursor: not-allowed
```

Disabled controls must not be the only way to explain unavailable actions.

Use tooltip or helper text where needed.

---

# Dark Theme Considerations

Dark theme should use tokens, not inverted ad hoc colors.

Rules:

* Avoid pure white text on pure black for large surfaces.
* Use neutral dark surfaces.
* Keep semantic colors readable.
* Adjust status backgrounds for contrast.
* Test charts separately.
* Avoid using raw color classes that fail in dark mode.

Dark theme may be V1, but token architecture must support it.

---

# Institution Branding and White Labeling

Future enterprise versions may support Institution or Organization branding.

Possible future customizations:

```text
Logo
Primary brand color
Accent color
Public portal theme
Report header colors
Email template colors
```

Constraints:

* Custom colors must pass accessibility checks.
* Custom branding must not break semantic colors.
* Error, warning, success, and security states should remain controlled.
* Platform admin views should remain clearly Acadedx/DGIS controlled.

MVP should not implement full white labeling unless required.

---

# Tailwind Token Recommendation

Recommended Tailwind semantic token mapping:

```ts
colors: {
  background: 'var(--background)',
  foreground: 'var(--foreground)',
  surface: 'var(--surface)',
  'surface-muted': 'var(--surface-muted)',
  border: 'var(--border)',
  muted: 'var(--muted)',
  primary: {
    50: '#EFF6FF',
    100: '#DBEAFE',
    200: '#BFDBFE',
    300: '#93C5FD',
    400: '#60A5FA',
    500: '#3B82F6',
    600: '#2563EB',
    700: '#1D4ED8',
    800: '#1E40AF',
    900: '#1E3A8A',
    950: '#172554',
  },
  secondary: {
    50: '#ECFEFF',
    100: '#CFFAFE',
    200: '#A5F3FC',
    300: '#67E8F9',
    400: '#22D3EE',
    500: '#06B6D4',
    600: '#0891B2',
    700: '#0E7490',
    800: '#155E75',
    900: '#164E63',
    950: '#083344',
  },
  success: {},
  warning: {},
  error: {},
  info: {},
}
```

Final implementation should fill semantic color groups consistently.

---

# CSS Variable Recommendation

Use CSS variables for theme tokens.

Example:

```css
:root {
  --background: #F8FAFC;
  --foreground: #0F172A;
  --surface: #FFFFFF;
  --surface-muted: #F1F5F9;
  --border: #E2E8F0;
  --muted: #64748B;

  --primary: #2563EB;
  --primary-hover: #1D4ED8;

  --success: #16A34A;
  --warning: #D97706;
  --error: #DC2626;
  --info: #2563EB;
}

.dark {
  --background: #020617;
  --foreground: #F8FAFC;
  --surface: #0F172A;
  --surface-muted: #1E293B;
  --border: #334155;
  --muted: #94A3B8;

  --primary: #60A5FA;
  --primary-hover: #93C5FD;

  --success: #22C55E;
  --warning: #F59E0B;
  --error: #F87171;
  --info: #60A5FA;
}
```

---

# Color Usage Examples

## Learners Page

Use:

```text
Primary button: Add Learner
Neutral table background
Status badges for ACTIVE/INACTIVE
Warning badge for incomplete profile
Error only for failed or destructive states
```

## Attendance Page

Use:

```text
Success for Present
Error for Absent
Warning for Late
Info for Excused
Neutral for unmarked
```

## Assessment Results Page

Use:

```text
Neutral table layout
Warning for pending entry
Success for submitted
Error only for invalid or failed submission
```

## AI Insight Card

Use:

```text
Secondary accent
AI-generated label
Neutral body text
Warning note for review-required state
```

---

# Color Anti-Patterns

Avoid:

```text
Using red for normal low marks without context
Using green/red alone for attendance
Using primary color for every button
Using too many colors in one dashboard
Using raw Tailwind colors directly throughout components
Using low-contrast gray text for important labels
Using color as the only permission indicator
Using custom Institution brand color for error states
Using bright AI gradients that distract from workflow
```

---

# MVP Color Requirements

The MVP must implement:

```text
Primary palette
Secondary palette
Neutral palette
Success colors
Warning colors
Error colors
Info colors
Background tokens
Surface tokens
Text tokens
Border tokens
Focus token
Status badge mapping
Attendance status mapping
Assessment status mapping
Job status mapping
Light theme
Dark-theme-ready token structure
```

Dark theme implementation may be deferred, but tokens must be ready.

---

# Deferred Color Capabilities

The following may be deferred beyond MVP:

```text
Full dark theme polish
Organization-level theme customization
Institution-level theme customization
Advanced chart palette
White-label report themes
Email template theme editor
Accessibility auto-checker for custom brand colors
```

Do not block these future capabilities with hardcoded component colors.

---

# Color Review Checklist

Before approving UI color usage, confirm:

* Color uses semantic token where possible.
* Text contrast is sufficient.
* Status is not communicated by color alone.
* Error color is used only for error or destructive states.
* Warning color is used only for caution or pending states.
* AI accent does not imply authority or certainty.
* Sensitive learner insights avoid alarming visual treatment.
* Custom colors do not break accessibility.
* Dark-theme readiness is preserved.
* Component does not hardcode raw colors unnecessarily.

---

# Related Documents

* docs/10-Design-System/DESIGN_SYSTEM.md
* docs/10-Design-System/UI_GUIDELINES.md
* docs/10-Design-System/TYPOGRAPHY.md
* docs/10-Design-System/COMPONENT_LIBRARY.md
* docs/08-Frontend/COMPONENTS.md
* docs/08-Frontend/PAGES.md
* docs/11-Security/PRIVACY.md


