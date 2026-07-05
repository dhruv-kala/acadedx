# Subscription Module Specification

**Module ID:** SUB

**Version:** 1.0.0

**Priority:** Critical (P0)

**Status:** Ready for Development

**Owner:** Product Management

---

# Overview

The Subscription module manages all pricing plans, feature entitlements, free trials, billing cycles, upgrades, downgrades, renewals, coupon codes, institutional licensing, and subscription analytics.

It enables flexible monetization while ensuring students can continue learning with a free plan.

---

# Goals

Provide a subscription platform that

- Supports individual students
- Supports parents with multiple children
- Supports teachers
- Supports schools
- Supports enterprise customers
- Supports future AI usage pricing

---

# Subscription Types

## Free

Ideal for new users.

Includes

- Limited Homework Solves
- Limited AI Tutor
- Limited Notes
- Limited Quizzes
- Limited Worksheets

---

## Student Pro

Unlimited learning tools.

---

## Family

One subscription for multiple students.

Supports

Up to 6 children

Shared billing

Parent dashboard

---

## Teacher Pro

Includes

- Classroom management
- Student analytics
- Assignment management
- AI lesson planning

---

## School

Institution-wide license.

Supports

Students

Teachers

Administrators

Central billing

---

## Enterprise

Custom pricing.

Dedicated support.

Custom integrations.

---

# Billing Cycles

Supported

Monthly

Quarterly

Yearly

Lifetime (Future)

---

# Free Trial

Supported

7 Days

14 Days

30 Days

Configurable by administrators.

---

# Plan Features

Plans define

Homework Limits

AI Tutor Messages

OCR Usage

Notes Generation

Quiz Generation

Worksheet Generation

Storage

Export Options

Priority AI Models

API Access

---

# Feature Entitlements

Examples

Free

Homework

10 per month

Student Pro

Unlimited

Family

Unlimited

School

Unlimited

Enterprise

Unlimited

---

# Upgrade Flow

Free

↓

Student Pro

↓

Family

↓

School

↓

Enterprise

---

# Downgrade Flow

Downgrades become effective after current billing cycle.

---

# Renewal

Support

Auto Renewal

Manual Renewal

Grace Period

Expired State

---

# Coupons

Support

Percentage Discount

Fixed Amount

Free Trial Extension

Referral Discount

Partner Coupons

---

# Referral Program

Reward

Free AI Credits

Free Premium Days

Subscription Discounts

Future

Revenue sharing

---

# Institutional Licensing

School Admin can

Purchase seats

Assign seats

Revoke seats

Transfer licenses

Monitor usage

---

# AI Usage Limits

Track

Homework Solves

OCR Pages

AI Tutor Tokens

Quiz Generation

Worksheet Generation

Storage

Exports

---

# Subscription Dashboard

Student sees

Current Plan

Usage

Renewal Date

Remaining Credits

Upgrade Options

Invoices

---

# Parent Dashboard

View

Children

Plan Usage

Billing

Renewal

Invoices

---

# School Dashboard

View

Licensed Seats

Used Seats

Available Seats

Billing

Renewal

AI Usage

---

# Notifications

Send

Trial Ending

Subscription Expiring

Payment Failed

Payment Successful

Renewal Reminder

Plan Upgraded

Plan Downgraded

---

# APIs

POST

/subscriptions/create

GET

/subscriptions/current

PUT

/subscriptions/change

POST

/subscriptions/cancel

POST

/subscriptions/resume

GET

/subscriptions/plans

GET

/subscriptions/usage

POST

/subscriptions/coupon

---

# Database Tables

SubscriptionPlans

Subscriptions

SubscriptionUsage

FeatureEntitlements

Coupons

Invoices

Trials

SeatLicenses

---

# Permissions

Student

Manage own subscription

Parent

Manage family subscription

Teacher

Manage teacher subscription

School Admin

Manage school subscription

Platform Admin

Manage all plans

---

# Validation Rules

One active subscription per account

One free trial per user

Coupon expiration enforced

Seat count validated

---

# Error Messages

Plan Not Found

Subscription Expired

Trial Already Used

Coupon Invalid

Seat Limit Reached

Payment Required

---

# Analytics

Track

New Subscriptions

MRR

ARR

Churn Rate

Renewal Rate

Plan Popularity

Upgrade Rate

Downgrade Rate

Free-to-Paid Conversion

Revenue Per User

AI Cost Per User

---

# Performance Targets

Plan Load

<500 ms

Subscription Update

<2 seconds

Usage Refresh

<1 second

---

# Accessibility

Keyboard Navigation

Screen Reader Support

Accessible Billing Pages

High Contrast

---

# Security

Encrypted Billing Data

Role-Based Access

Audit Logging

Fraud Detection

Subscription Validation

---

# Acceptance Criteria

✓ Free plan available

✓ Student Pro works

✓ Family plan works

✓ School licensing works

✓ Usage tracking works

✓ Upgrade and downgrade work

✓ Coupons work

✓ Analytics updated

✓ Unit Tests

✓ Integration Tests

✓ E2E Tests

---

# Future Enhancements

AI Credit Marketplace

Pay-As-You-Go AI

Regional Pricing

Student Scholarships

Corporate Sponsorships

Gift Subscriptions

Educational Grants

Usage-Based Billing