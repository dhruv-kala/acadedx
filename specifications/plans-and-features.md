# Plans & Feature Matrix Specification

**Module ID:** PLAN-MATRIX

**Version:** 1.0.0

**Priority:** Critical (P0)

**Status:** Ready for Development

**Owner:** Product Management

---

# Overview

This document defines every Acadedx subscription plan and the features available in each plan.

It serves as the single source of truth for:

- Product
- Engineering
- Billing
- Sales
- Customer Success
- Marketing

Every feature in Acadedx must reference this specification before implementation.

---

# Objectives

The subscription system must

- Support individual learners
- Support families
- Support teachers
- Support schools
- Support enterprise customers
- Support future AI usage pricing

---

# Subscription Plans

## Free

Perfect for new students.

Target

Individual Students

Price

₹0

---

## Student Pro

Target

Individual Students

Monthly

Yearly

---

## Family

Target

Parents

Supports multiple children.

---

## Teacher Pro

Target

Teachers

Private tutors

---

## School

Target

Schools

Institutions

---

## Enterprise

Target

Large organizations

Universities

Government

---

# Feature Matrix

| Feature | Free | Student Pro | Family | Teacher | School | Enterprise |
|----------|------|-------------|---------|----------|----------|-------------|
| Homework Solver | 10/month | Unlimited | Unlimited | Unlimited | Unlimited | Unlimited |
| AI Tutor | 20 chats/day | Unlimited | Unlimited | Unlimited | Unlimited | Unlimited |
| OCR Pages | 20/month | Unlimited | Unlimited | Unlimited | Unlimited | Unlimited |
| Notes Generation | 10/month | Unlimited | Unlimited | Unlimited | Unlimited | Unlimited |
| Quiz Generation | 10/month | Unlimited | Unlimited | Unlimited | Unlimited | Unlimited |
| Worksheet Generation | 10/month | Unlimited | Unlimited | Unlimited | Unlimited | Unlimited |
| Study Planner | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Dashboard | ✓ | ✓ | ✓ | ✓ | ✓ | ✓ |
| Analytics | Basic | Advanced | Advanced | Advanced | Advanced | Enterprise |
| Parent Dashboard | ✗ | ✗ | ✓ | ✗ | ✓ | ✓ |
| Teacher Dashboard | ✗ | ✗ | ✗ | ✓ | ✓ | ✓ |
| School Dashboard | ✗ | ✗ | ✗ | ✗ | ✓ | ✓ |
| Admin Portal | ✗ | ✗ | ✗ | ✗ | ✓ | ✓ |
| API Access | ✗ | ✗ | ✗ | ✗ | Optional | ✓ |
| White Label | ✗ | ✗ | ✗ | ✗ | Optional | ✓ |
| SSO | ✗ | ✗ | ✗ | ✗ | Optional | ✓ |

---

# AI Limits

## Free

Homework

10/month

AI Tutor

20 chats/day

OCR

20 pages/month

Storage

500 MB

---

## Student Pro

Unlimited AI

Unlimited OCR

Unlimited Homework

5 GB Storage

---

## Family

Everything in Student Pro

Up to 6 students

20 GB shared storage

---

## Teacher Pro

Unlimited AI

Unlimited students

Assignment tools

Lesson planning

25 GB storage

---

## School

Unlimited

Institution license

Seat management

Central analytics

100 GB storage

---

## Enterprise

Unlimited everything

Custom AI models

Private deployment

Unlimited storage

Dedicated support

---

# Storage Limits

| Plan | Storage |
|--------|----------|
| Free | 500 MB |
| Student Pro | 5 GB |
| Family | 20 GB |
| Teacher | 25 GB |
| School | 100 GB |
| Enterprise | Unlimited |

---

# Export Limits

Free

PDF only

Student Pro

PDF

DOCX

Markdown

TXT

Family

All formats

Teacher

All formats

School

All formats

Enterprise

All formats + API

---

# AI Models

Free

Standard model

Student Pro

Premium routing

Family

Premium routing

Teacher

Premium routing

School

Premium routing

Enterprise

Dedicated routing

Custom model selection

---

# Family Plan

Supports

Maximum

6 Students

Includes

Parent Dashboard

Shared Billing

Shared Invoices

Shared AI Credits

---

# Teacher Plan

Supports

Unlimited Classes

Unlimited Assignments

Student Analytics

AI Lesson Planner

Question Bank

---

# School Plan

Supports

Unlimited Students

Unlimited Teachers

Departments

Class Management

Institution Analytics

Central Billing

---

# Enterprise Plan

Supports

Unlimited Organizations

SSO

API Access

Custom Branding

White Label

Dedicated Infrastructure

Priority Support

Custom Integrations

---

# Feature Flags

Engineering must implement every premium feature using feature flags.

Example

feature.ai_tutor

feature.homework

feature.notes

feature.analytics

feature.school_dashboard

feature.enterprise_api

---

# Entitlement Validation

Every request must verify

User

↓

Subscription

↓

Feature

↓

Limit

↓

Remaining Usage

↓

Access Granted

---

# Upgrade Benefits

Immediately unlock

Premium AI

Unlimited usage

Advanced analytics

Additional storage

---

# Downgrade Rules

Current billing cycle completes.

Premium features become read-only.

Data is never deleted automatically.

---

# Grace Period

7 Days

Users can renew before restrictions apply.

---

# Trial

One trial per account.

Configurable

7

14

30 Days

---

# APIs

GET

/plans

GET

/features

GET

/entitlements

POST

/validate-feature

GET

/usage

---

# Database Tables

Plans

PlanFeatures

Entitlements

UsageCounters

Trials

FeatureFlags

---

# Analytics

Track

Most popular plans

Feature adoption

Upgrade conversion

Downgrade reasons

AI cost by plan

Storage usage

Average revenue

---

# Acceptance Criteria

✓ Feature validation works

✓ Usage limits enforced

✓ Unlimited plans unrestricted

✓ Family sharing works

✓ School licensing works

✓ Enterprise features enabled

✓ Feature flags implemented

✓ Usage counters accurate

✓ Unit Tests

✓ Integration Tests

✓ E2E Tests

---

# Future Enhancements

Credit-Based AI Usage

Regional Pricing

Educational Discounts

Student Scholarships

Partner Programs

Marketplace Add-ons

Custom Feature Bundles

Usage-Based AI Billing