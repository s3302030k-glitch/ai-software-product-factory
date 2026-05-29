# 07A — Database Migration Guidelines

> Outlines conventions, safety standards, rollback mechanisms, and approval gates for database schema changes in Supabase.

---

## Purpose

This document establishes the processes and standards for introducing database schema changes, triggers, functions, and seed data. It supplements the core data model template in [07-data-model.md](../../../core/docs/07-data-model.md).

## Status

`Active` — Must be followed by all developers and AI agents executing migration scripts.

---

## Migration Naming Conventions

Migrations must be created using the Supabase CLI to ensure consistent local database emulation. All migration files must follow the format:

```
supabase/migrations/YYYYMMDDHHMMSS_description.sql
```

- `YYYYMMDDHHMMSS` represents the UTC timestamp of creation.
- `description` should be lowercase, using underscores as separators (e.g., `20260529120000_create_profiles_table.sql`).
- Manual modifications to the migration file name structure are strictly prohibited.

---

## Safe Migration Principles

1. **Small and Focused**: A migration should do one thing (e.g., add a column, create an index, update a function). Do not combine unrelated changes.
2. **Backward Compatibility**: Schema updates must not break the current running version of the application code.
3. **Always Enable RLS on New Tables**: Any `create table` statement must be followed in the same migration file by `alter table ... enable row level security;`.
4. **Define Indexes on Foreign Keys**: To ensure performance of RLS lookups, columns holding references (like `user_id` or `tenant_id`) must be indexed.

---

## Destructive Migration Rules

> [!WARNING]
> Destructive migrations (e.g., `DROP TABLE`, `DROP COLUMN`, `ALTER COLUMN TYPE` that converts incompatibly) can result in data loss or application downtime.
>
> 1. **NO destructive migrations** may be applied to staging or production without explicit written approval from the human owner.
> 2. If a column is no longer needed, deprecate it first (stop referencing it in code) before dropping it in a subsequent release.
> 3. Provide a data preservation plan (e.g., table copy or backup instructions) alongside the migration request.

---

## Backfill Rules

When adding a `NOT NULL` constraint to an existing column, or adding a new column that requires default values populated from existing rows:
- The data migration (backfill) must be executed *before* or *during* the transition to the new schema.
- Write explicit SQL updates inside the migration script to assign default values.
- Document the backfill process in the migration review request, detailing estimated row impact.

---

## Seed and Test Data Rules

- Local test data (seeds) must be kept separate from the structural schema migrations.
- Seeds must be managed in the `supabase/seed.sql` file.
- **NEVER** treat test or seed data configurations as a production policy template. Seed files are for convenience in development environments only.

---

## Rollback Considerations

- Every schema modification should ideally have a corresponding rollback strategy documented in a comments block at the top of the migration file, or a separate rollback script.
- Ensure rollback operations do not accidentally drop unrelated tables or drop data created since the migration was applied.

---

## Owner Approval Requirements

- **ALL schema modifications** targeting production must be reviewed and approved by the human owner.
- AI agents are strictly prohibited from executing migration commands directly on staging or production database instances.

---

## Validation Checklist

Before marking a migration as ready for review:
- [ ] Run the migration locally against a clean database instance (`supabase db reset`).
- [ ] Confirm RLS is explicitly enabled on all new tables.
- [ ] Verify that foreign keys are indexed.
- [ ] Verify no syntax or type errors occur during local execution.
- [ ] Verify that the local test suite passes after the migration is applied.

---

## Common Mistakes

- **Creating columns without enabling RLS**: Exposes the table to public reads or writes.
- **Modifying old migration files**: Causes checksum mismatches in Supabase CLI, breaking staging and production deployments. Always create a *new* migration for schema changes.
- **Missing search path in SECURITY DEFINER functions**: Introduces vulnerability to path hijacking.
- **Running slow operations on big tables**: Performing lock-heavy updates on high-volume tables (e.g., adding a default value to millions of rows).

---

## Stop Conditions

> [!CAUTION]
> Stop and report to the human owner if:
> 1. You are asked to modify an already deployed migration file.
> 2. You are asked to run a destructive database query (`DROP`, `TRUNCATE`) on non-development databases.
> 3. A migration contains schema changes combined with high-volume backfill queries that could lock production tables.
> 4. You find a new table created without RLS enabled.

---

## Related Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Core data model definition.
- [08A — Supabase Architecture](supabase-architecture.md) — Tech stack architecture details.
- [rls-policy-guidelines.md](rls-policy-guidelines.md) — RLS policy principles and setup.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created database migration guidelines | Antigravity |
