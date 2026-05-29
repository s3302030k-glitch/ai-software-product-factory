# 07 — Data Model

> Defines all entities, fields, relationships, business rules, and data integrity constraints.

---

## Purpose

Provide a complete, implementation-ready data model that the Database Engineer and Coding Agent use to build the database schema. Every entity, field, relationship, and business rule is specified here.

## Status

`Draft` | `In Review` | `Approved` | `Locked`

---

## Entity Template

_Copy this template for each entity in the data model._

---

### Entity: [Entity Name]

**Table Name:** `entity_name` _(snake_case, plural)_
**Description:** _What this entity represents_
**Owner Role:** _Which role "owns" records of this type (for data scoping)_

#### Fields

| Field | Column Name | Type | Nullable | Default | Unique | Notes |
|-------|------------|------|----------|---------|--------|-------|
| ID | `id` | `uuid` / `bigint` | No | Auto-generated | Yes | Primary key |
| _Name_ | `name` | `varchar(100)` | No | — | No | |
| _Status_ | `status` | `enum('active','inactive')` | No | `'active'` | No | |
| _Owner_ | `user_id` | `uuid` / `bigint` | No | — | No | FK → `users.id` |
| Created At | `created_at` | `timestamp` | No | `now()` | No | Auto-set |
| Updated At | `updated_at` | `timestamp` | No | `now()` | No | Auto-updated |
| Deleted At | `deleted_at` | `timestamp` | Yes | `null` | No | Soft delete |

#### Relationships

| Relationship | Type | Related Entity | Foreign Key | On Delete | Notes |
|-------------|------|---------------|-------------|-----------|-------|
| _Owner_ | Many-to-One | `users` | `user_id` | Cascade / Restrict / Set Null | |
| _Items_ | One-to-Many | `items` | `entity_id` on items | Cascade | |
| _Tags_ | Many-to-Many | `tags` | via `entity_tags` junction table | Cascade | |

#### Business Rules

_Rules that must be enforced at the data level or application level._

| # | Rule | Enforcement | Notes |
|---|------|------------|-------|
| 1 | _e.g., Name must be unique per user_ | _Unique constraint on (user_id, name)_ | |
| 2 | _e.g., Cannot delete if has active children_ | _Application-level check_ | |
| 3 | _e.g., Status can only transition Active → Inactive_ | _Application-level validation_ | |

#### Validation Rules

| Field | Rule | Error Message |
|-------|------|---------------|
| `name` | Required, 1-100 chars, no special chars | "Name is required" |
| `status` | Must be valid enum value | "Invalid status" |
| `user_id` | Must reference existing user | "Invalid user" |

#### Indexing Notes

| Index | Columns | Type | Reason |
|-------|---------|------|--------|
| _Primary_ | `id` | Primary Key | |
| _Owner lookup_ | `user_id` | B-tree | _Frequent queries by owner_ |
| _Status filter_ | `status` | B-tree | _Frequent filtering_ |
| _Unique name per user_ | `user_id, name` | Unique | _Business rule enforcement_ |
| _Soft delete filter_ | `deleted_at` | Partial (where null) | _Exclude deleted in queries_ |

---

## Migration Rules

_Rules for managing database changes over time._

1. All schema changes must be done through versioned migrations
2. Migrations must be reversible (include up and down)
3. Data migrations must be separated from schema migrations
4. No destructive migrations (column drops, table drops) without human approval
5. Migration files must be named with timestamp prefix: `YYYYMMDDHHMMSS_description`
6. AI agents must not create or run migrations unless explicitly requested in the batch

---

## Seed / Test Data Rules

_Rules for generating test and development data._

1. Seed data must be clearly marked as non-production
2. Seed users must use obviously fake emails (e.g., `admin@example.com`)
3. Seed passwords must not be production-grade secrets
4. Seed data should cover:
   - At least one user per role
   - Edge cases (empty strings, max-length values, special characters)
   - Relationships (orphaned records, cascading scenarios)
5. Test data generators must be deterministic (seeded random)

---

## Entity Index

_List all entities for quick reference._

| Entity | Table Name | Priority | Status |
|--------|-----------|----------|--------|
| _User_ | `users` | Must Have | `Draft` |
| _[Entity A]_ | `entity_a` | Must Have | `Draft` |
| _[Entity B]_ | `entity_b` | Should Have | `Draft` |

---

## Scope

- This document defines **data structures, relationships, and integrity rules**.
- It is the single authority on what data exists and how it relates.

## Out of Scope

- API contracts (see `09-api-design.md`)
- Page display logic (see `06-pages-spec.md`)
- Query optimization details (handled during implementation)

## Guardrails

- [ ] Every entity must have `created_at` and `updated_at` timestamps
- [ ] Soft delete (`deleted_at`) should be used unless explicitly decided otherwise
- [ ] No direct database modifications without a migration
- [ ] AI agents must not add entities or fields not specified in this document
- [ ] Business rules must be enforced — not just documented

## Related Files

- `01-product-brief.md` — Product that the data model serves
- `04-user-roles.md` — Roles referenced in data scoping
- `06-pages-spec.md` — Pages that display and modify this data
- `09-api-design.md` — API endpoints that expose this data

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
