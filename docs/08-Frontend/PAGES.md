# Frontend Pages

**Document Version:** 1.0.0

**Status:** Approved

**Owner:** Product Management

**Framework:** React + TypeScript + Vite

**UI Library:** shadcn/ui

**Last Updated:** July 2026

---

# Purpose

This document defines every page of the Acadedx platform.

Each page specifies:

- Purpose
- Target users
- Features
- Navigation
- Required APIs
- Permissions

This serves as the blueprint for frontend development.

---

# User Roles

- Guest
- Student
- Parent
- Teacher
- School Admin
- Platform Admin

---

# Public Pages

## Landing Page

Route

```
/
```

Purpose

Introduce Acadedx.

Sections

- Hero
- Features
- AI Demo
- Testimonials
- Pricing
- FAQ
- Footer

---

## Login

```
/login
```

Features

- Email Login
- Google Login
- Microsoft Login
- Forgot Password

---

## Register

```
/register
```

Features

- Student Registration
- Parent Registration
- Teacher Registration

---

## Pricing

```
/pricing
```

Displays

- Free Plan
- Premium Plan
- School Plan

---

## Contact

```
/contact
```

---

## About

```
/about
```

---

## Privacy Policy

```
/privacy
```

---

## Terms

```
/terms
```

---

# Student Application

---

## Dashboard

```
/dashboard
```

Widgets

- Homework Progress
- Recent Chats
- Quiz Scores
- Study Streak
- AI Usage
- Upcoming Study Plan

---

## Homework Solver

```
/homework
```

Features

- Upload Image
- Type Question
- Camera Capture
- Solution History

---

## AI Tutor

```
/ai-tutor
```

Features

- Chat
- Voice
- Image Input
- Conversation History

---

## Notes Generator

```
/notes
```

Features

- Generate Notes
- Save Notes
- Export PDF

---

## Worksheet Generator

```
/worksheets
```

Features

- Generate Worksheet
- Difficulty Selection
- Answer Key

---

## Quiz Generator

```
/quiz
```

Features

- Generate Quiz
- Attempt Quiz
- Review Answers
- Score Report

---

## Study Planner

```
/study-planner
```

Features

- Goals
- Calendar
- Daily Tasks
- Progress

---

## Learning Analytics

```
/analytics
```

Displays

- Weekly Progress
- Subject Performance
- AI Usage
- Study Time

---

## Notifications

```
/notifications
```

---

## User Profile

```
/profile
```

Features

- Personal Details
- Password
- Avatar
- Preferences

---

## Settings

```
/settings
```

Features

- Theme
- Language
- AI Preferences
- Notification Preferences

---

# Parent Portal

Routes

```
/parent/*
```

Pages

- Dashboard
- Child Progress
- Homework
- Reports
- Notifications

---

# Teacher Portal

Routes

```
/teacher/*
```

Pages

- Dashboard
- Students
- Homework
- Quizzes
- Reports
- AI Insights

---

# School Admin Portal

Routes

```
/school/*
```

Pages

- Dashboard
- Teachers
- Students
- Classes
- Subjects
- Reports
- Billing

---

# Platform Admin

Routes

```
/admin/*
```

Pages

- Dashboard
- User Management
- School Management
- Subscription Management
- AI Usage
- Prompt Management
- Analytics
- Audit Logs
- System Settings

---

# Common Components

Every authenticated page contains

- Header
- Sidebar
- Breadcrumb
- Notifications
- User Menu
- Footer (optional)

---

# Error Pages

404

500

403

Maintenance

Offline

---

# Loading States

Every page must support

- Skeleton Loading
- Empty State
- Error State

---

# Responsive Support

Desktop

Laptop

Tablet

Mobile

---

# Accessibility

WCAG 2.2 AA

Keyboard Navigation

Screen Reader Support

High Contrast

---

# Future Pages

- Marketplace
- AI Classroom
- Live Tutor
- Community Forum
- Course Marketplace

---

# Related Documents

- COMPONENTS.md
- ROUTING.md
- STATE_MANAGEMENT.md
- DESIGN_SYSTEM.md
- API_GUIDELINES.md