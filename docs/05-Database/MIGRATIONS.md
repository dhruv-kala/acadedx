# Acadedx Database Migrations

**Document Version:** 1.1.0  
**Status:** Approved  
**Last Updated:** July 2026  
**Owner:** Solution Architecture  
**Company:** Digital Global Information Systems (DGIS)  
**Database:** PostgreSQL  
**ORM:** Prisma

---

# Purpose

This document defines the database migration strategy for Acadedx.

It explains how schema changes must be created, reviewed, tested, deployed, rolled back, and documented.

Database migrations are critical because Acadedx stores sensitive learning, institution, organization, billing, AI usage, and analytics data.

---

# Migration Philosophy

Acadedx must use controlled, versioned, reviewable database migrations.

Manual schema changes are prohibited.

Every database change must be:

- Intentional
- Version controlled
- Reviewed
- Tested
- Reversible where practical
- Compatible with production data
- Safe for organization and institution isolation

---

# Approved Migration Tool

Acadedx uses:

```text
Prisma Migrate
```

Database:

```text
PostgreSQL
```

All schema changes must originate from:

```text
database/prisma/schema.prisma
```

and generate migration files under:

```text
database/prisma/migrations/
```

---

# Migration Directory

Expected structure:

```text
database/
└── prisma/
    ├── schema.prisma
    ├── migrations/
    │   ├── 202607050001_init_identity/
    │   │   └── migration.sql
    │   ├── 202607050002_add_organizations/
    │   │   └── migration.sql
    │   ├── 202607050003_add_institutions/
    │   │   └── migration.sql
    │   └── ...
    └── seed/
        ├── seed.ts
        └── data/
```

---

# Migration Naming Convention

Migration names must be descriptive.

Good:

```text
202607050001_init_identity
202607050002_add_organizations
202607050003_add_institutions
202607050004_add_academic_structure
202607050005_add_homework_tables
202607050006_add_ai_usage_tracking
```

Bad:

```text
update
changes
new_tables
fix_db
migration1
```

---

# Migration Principles

## 1. Organization and Institution First

The schema must include Organization and Institution early.

Do not build a school-only schema and plan to fix it later.

MVP supports:

```text
One Organization → One Institution
```

But schema must support:

```text
One Organization → Multiple Institutions
```

---

## 2. No School-Only Schema

Do not create core tables such as:

```text
schools
school_users
school_id
```

Use:

```text
institutions
institution_users
institution_id
```

A school is an Institution type.

---

## 3. Preserve Tenant Isolation

Any table containing tenant-scoped data must include the correct scope fields.

Examples:

```text
organizationId
institutionId
studentUserId
```

Migration review must verify that tenant isolation is not weakened.

---

## 4. Never Modify Production Manually

Do not run manual SQL changes in production.

Do not modify production tables directly.

Do not use database GUI tools to alter schema outside migrations.

---

## 5. Migrations Must Be Tested

Every migration must be tested against:

- Empty database
- Seed database
- Existing development database
- Staging database before production

---

# Migration Lifecycle

```text
Schema Change
    ↓
Generate Migration
    ↓
Review Migration SQL
    ↓
Run Locally
    ↓
Run Tests
    ↓
Apply to Staging
    ↓
Verify Application
    ↓
Backup Production
    ↓
Apply to Production
    ↓
Monitor
```

---

# Local Development Workflow

## Step 1 — Modify Prisma Schema

Update:

```text
database/prisma/schema.prisma
```

---

## Step 2 — Generate Migration

Use:

```bash
pnpm prisma migrate dev --name add_institutions
```

or project script:

```bash
pnpm db:migrate:dev
```

---

## Step 3 — Review Generated SQL

Before committing, inspect:

```text
database/prisma/migrations/<migration_name>/migration.sql
```

Check for:

- Unexpected table drops
- Unexpected column drops
- Unsafe type conversions
- Missing indexes
- Missing foreign keys
- Missing tenant scope fields

---

## Step 4 — Run Tests

Run:

```bash
pnpm test
pnpm test:integration
```

---

## Step 5 — Commit Migration

Commit both:

```text
schema.prisma
migration.sql
```

Never commit schema changes without migration files.

---

# Production Migration Workflow

Production migrations must be treated as release operations.

## Required Steps

1. Confirm migration has passed staging.
2. Confirm latest production backup exists.
3. Confirm migration does not drop required data.
4. Confirm rollback plan exists.
5. Deploy application version compatible with migration.
6. Apply migration.
7. Monitor logs, errors, and database health.

---

# Backup Requirement

Before production migration:

- Database backup must be created.
- Backup restore procedure must be known.
- Backup completion must be verified.

For high-risk migrations, test restore on a non-production environment.

---

# Migration Risk Levels

## Low Risk

Examples:

- Add nullable column
- Add new table
- Add index concurrently where supported
- Add enum value carefully

Approval:

- Developer review
- Architecture review if tenant-related

---

## Medium Risk

Examples:

- Add non-null column with default
- Modify relation
- Add unique constraint
- Rename column
- Change enum usage
- Backfill data

Approval:

- Developer review
- Architecture review
- QA validation

---

## High Risk

Examples:

- Drop column
- Drop table
- Change primary key
- Change foreign key strategy
- Large data migration
- Split table
- Merge table
- Change tenant scoping
- Change payment or audit records

Approval:

- Architecture approval
- Product approval
- Backup verification
- Rollback plan
- Staging validation
- Release window

---

# Safe Migration Patterns

## Adding a Column

Preferred:

1. Add nullable column.
2. Deploy application that writes both old and new fields where needed.
3. Backfill data.
4. Add not-null constraint later if required.

---

## Renaming a Column

Avoid direct rename in production when possible.

Safer approach:

1. Add new column.
2. Write to both columns.
3. Backfill.
4. Read from new column.
5. Remove old column in later release.

---

## Dropping a Column

Dropping columns is high risk.

Process:

1. Stop using column in application.
2. Deploy.
3. Confirm no reads or writes.
4. Wait one release cycle where practical.
5. Drop column through migration.

---

## Changing Column Type

Use staged migration.

1. Add new column with new type.
2. Backfill.
3. Switch application.
4. Drop old column later.

---

## Adding Unique Constraint

Before adding unique constraint:

- Check duplicate data.
- Clean duplicates.
- Add index.
- Add constraint.

---

## Adding Foreign Keys

Before adding foreign key:

- Check orphaned records.
- Clean invalid references.
- Add foreign key.
- Add indexes for relationship fields.

---

# Tenant Scope Migration Rules

When adding a tenant-owned table, verify whether it needs:

```text
organizationId
institutionId
studentUserId
```

## Organization-Scoped Tables

Examples:

- Organization settings
- Organization billing
- Enterprise subscription

Must include:

```text
organizationId
```

---

## Institution-Scoped Tables

Examples:

- Academic sessions
- Grades
- Sections
- Institution users
- Institution analytics

Must include:

```text
organizationId
institutionId
```

---

## Student-Owned Tables

Examples:

- Homework
- Notes
- Quiz attempts
- Worksheets
- Study plans

Must include:

```text
studentUserId
```

May also include:

```text
organizationId
institutionId
```

when student belongs to an institution.

---

# Required Indexes

Migrations must include indexes for common access patterns.

Examples:

```text
organizationId
institutionId
userId
studentUserId
createdAt
status
eventName
featureId
subscriptionId
payment provider IDs
```

Composite indexes should be added for frequent scoped queries.

Examples:

```text
institutionId + studentUserId
organizationId + institutionId
studentUserId + createdAt
featureId + periodStart + periodEnd
```

---

# Soft Delete Migration Rules

Business tables should support soft delete where appropriate.

Fields:

```text
deletedAt
deletedBy
```

Tables that should usually support soft delete:

- Users
- Organizations
- Institutions
- Homework
- Notes
- Quizzes
- Worksheets
- Study plans
- Files

Tables that should not be soft deleted normally:

- Payments
- Invoices
- Audit logs
- AI usage records
- Security logs

---

# Audit Field Migration Rules

Business tables should include:

```text
createdAt
updatedAt
createdBy
updatedBy
```

Where user attribution is not available, `createdBy` and `updatedBy` may be nullable.

---

# Enum Migration Rules

Enums must be handled carefully.

## Adding Enum Values

Allowed when backward compatible.

## Removing Enum Values

High risk.

Must ensure no data uses removed values.

## Renaming Enum Values

Prefer adding a new value, migrating data, then removing old value later.

---

# Seed Data Rules

Seed data may include:

- Default roles
- Default permissions
- Default plans
- Default features
- Default institution types
- Default global subjects
- Development test users

Seed data must not include:

- Real user data
- Real student data
- Real payment data
- Real API keys
- Real AI provider secrets

---

# Required Seed Data for MVP

## Roles

```text
PLATFORM_ADMIN
ORGANIZATION_OWNER
ORGANIZATION_ADMIN
INSTITUTION_ADMIN
TEACHER
PARENT
STUDENT
SUPPORT_AGENT
```

---

## Institution Types

```text
SCHOOL
ACADEMY
COACHING_INSTITUTE
COLLEGE
UNIVERSITY
TRAINING_CENTRE
LEARNING_CENTRE
ONLINE_ACADEMY
```

---

## Default Features

```text
homework.solve
ai_tutor.chat
ocr.extract
notes.generate
quiz.generate
worksheets.generate
study_planner.generate
dashboard.student
analytics.basic
notifications.in_app
```

---

## Default Plans

```text
FREE
STUDENT_PRO
FAMILY
TEACHER
INSTITUTION
ENTERPRISE
```

---

# Migration Review Checklist

Before approving any migration, verify:

- Migration name is descriptive.
- Migration SQL has been reviewed.
- No unexpected table drops.
- No unexpected column drops.
- Tenant scope is preserved.
- Organization and institution fields are present where needed.
- Required indexes are present.
- Foreign keys are appropriate.
- Soft delete rules are followed.
- Audit fields are included.
- Payment and audit records are protected.
- Backward compatibility is considered.
- Rollback plan exists for risky changes.
- Tests pass.

---

# AI Agent Migration Rules

AI coding agents must not:

- Create migrations without updating `schema.prisma`.
- Modify migration files randomly after generation.
- Drop tables or columns without explicit instruction.
- Rename tenant fields from `institutionId` to `schoolId`.
- Remove organization or institution fields.
- Create schema changes outside the requested task.
- Add raw SQL unless required and explained.

AI coding agents must:

- Use Organization and Institution terminology.
- Include tenant scope fields where required.
- Include indexes for scoped queries.
- Update database documentation when schema changes.
- Mention migration impact in task completion summary.

---

# Rollback Strategy

Prisma does not automatically provide safe production rollback for every migration.

Rollback must be planned per migration.

Possible rollback strategies:

- Restore database backup.
- Apply reverse migration.
- Deploy compatibility code.
- Disable feature flag.
- Revert application release.
- Run data repair script.

High-risk migrations require explicit rollback notes.

---

# Data Backfill Strategy

Backfills must be planned when adding fields to existing data.

Examples:

- Adding organizationId to existing institution-linked records.
- Adding institutionId to learning records.
- Adding feature usage counters.
- Adding Learning Mastery records.

Backfills should be:

- Idempotent
- Logged
- Tested
- Safe to re-run
- Batched for large datasets

---

# Zero-Downtime Migration Guidance

For production systems, prefer expand-and-contract migrations.

## Expand

Add new tables or columns without breaking old code.

## Migrate

Backfill data and update application to use new structure.

## Contract

Remove old fields only after the new path is stable.

---

# Migration Documentation Requirements

Every significant migration should document:

- Purpose
- Tables changed
- Columns changed
- Indexes added
- Data migration required
- Risk level
- Rollback plan
- Related task ID
- Related specification

This may be added to the task file or migration notes.

---

# Environment Rules

## Local

Developers may reset local databases.

## Development

Can be reset with approval.

## Staging

Should mimic production migration process.

## Production

Must never be reset.

---

# Commands Reference

Example local commands:

```bash
pnpm prisma migrate dev --name add_organizations
pnpm prisma migrate reset
pnpm prisma generate
pnpm prisma studio
```

Example production command:

```bash
pnpm prisma migrate deploy
```

Actual scripts should be defined in root `package.json`.

---

# Production Safety Rules

Before production migration:

- Confirm deployment window.
- Confirm backup.
- Confirm migration reviewed.
- Confirm application compatibility.
- Confirm environment variables.
- Confirm database connection.
- Confirm monitoring.
- Confirm rollback plan.

After production migration:

- Check application health.
- Check database logs.
- Check error monitoring.
- Check API failures.
- Check background jobs.
- Check critical workflows.

---

# Related Documents

- DATABASE_SCHEMA.md
- DATA_DICTIONARY.md
- ER_DIAGRAM.md
- SYSTEM_ARCHITECTURE.md
- CODING_STANDARDS.md
- DEPLOYMENT.md