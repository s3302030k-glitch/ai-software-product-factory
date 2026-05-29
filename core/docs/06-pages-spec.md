# 06 — Pages Spec

> Detailed specification for every page in the application, including fields, actions, states, and permissions.

---

## Purpose

Define exactly what appears on each page, how it behaves, what data it shows, and who can access it. This document is the implementation blueprint for frontend development.

## Status

`Draft` | `In Progress` | `Complete`

---

## Page Template

_Copy this template for each page in the application._

---

### Page: [Page Name]

**Route:** `/path/to/page`
**Layout:** _e.g., Dashboard layout with sidebar / Full-width / Auth layout_
**Access:** _Roles from `04-user-roles.md` that can view this page_
**Related Flow(s):** `FLOW-XXX`, `FLOW-YYY`
**Priority:** Must Have | Should Have | Could Have

#### Page Description

_One paragraph describing what this page does and why it exists._

#### Fields

_For form pages — define every field._

| Field | Type | Required | Validation | Default | Editable | Notes |
|-------|------|----------|------------|---------|----------|-------|
| _e.g., Name_ | `text` | Yes | _Max 100 chars_ | _Empty_ | Yes | |
| _e.g., Email_ | `email` | Yes | _Valid email format_ | _Empty_ | Yes | _Unique_ |
| _e.g., Status_ | `select` | Yes | _Options: Active, Inactive_ | _Active_ | Admin only | |
| _e.g., Created At_ | `datetime` | Auto | _N/A_ | _Current time_ | No | _Display only_ |

#### Actions

_What can the user do on this page?_

| Action | Label | Type | Confirmation | Permission | Behavior |
|--------|-------|------|-------------|------------|----------|
| _Save_ | "Save" | Primary button | No | _Role_ | _Validate and submit form_ |
| _Delete_ | "Delete" | Danger button | Yes — "Are you sure?" | _Admin_ | _Soft delete, redirect to list_ |
| _Cancel_ | "Cancel" | Text link | No | _All_ | _Navigate back without saving_ |

#### Table Columns (for list pages)

| Column | Source Field | Sortable | Default Sort | Width | Format |
|--------|------------|----------|-------------|-------|--------|
| _Name_ | `name` | Yes | — | _Auto_ | _Plain text_ |
| _Status_ | `status` | Yes | — | _100px_ | _Badge (green/gray)_ |
| _Created_ | `created_at` | Yes | Desc | _150px_ | _Relative date_ |
| _Actions_ | — | No | — | _100px_ | _Edit / Delete icons_ |

#### Filters (for list pages)

| Filter | Type | Options | Default | Behavior |
|--------|------|---------|---------|----------|
| _Status_ | `select` | _All, Active, Inactive_ | _All_ | _Filter table rows_ |
| _Search_ | `text` | _N/A_ | _Empty_ | _Search by name, email_ |
| _Date Range_ | `date_range` | _N/A_ | _Last 30 days_ | _Filter by created_at_ |

#### Loading State

_What does the user see while data is loading?_

```
[Describe: skeleton screen, spinner, loading text, etc.]
```

#### Empty State

_What does the user see when there is no data?_

```
[Describe: illustration, message, CTA button. E.g., "No items yet. Create your first one."]
```

#### Error State

_What does the user see when something goes wrong?_

| Error Type | Display | Recovery Action |
|-----------|---------|----------------|
| Network error | _Toast: "Failed to load data"_ | _Retry button_ |
| Server error (500) | _Error page / inline message_ | _Retry or contact support_ |
| Not found (404) | _"Item not found" page_ | _Back to list_ |
| Forbidden (403) | _Redirect to dashboard_ | _N/A_ |

#### Validation Rules

| Field | Rule | Error Message |
|-------|------|---------------|
| _Name_ | _Required, max 100 chars_ | _"Name is required" / "Name must be under 100 characters"_ |
| _Email_ | _Required, valid format, unique_ | _"Email is required" / "Invalid email" / "Email already exists"_ |

#### Permissions

| Action | Admin | Manager | User | Guest |
|--------|-------|---------|------|-------|
| View page | ✅ | ✅ | ✅ | ❌ |
| Create | ✅ | ✅ | ❌ | ❌ |
| Edit (own) | ✅ | ✅ | ✅ | ❌ |
| Edit (all) | ✅ | ❌ | ❌ | ❌ |
| Delete | ✅ | ❌ | ❌ | ❌ |

---

## Page Index

_List all pages for quick navigation._

| Page Name | Route | Priority | Status |
|-----------|-------|----------|--------|
| _Dashboard_ | `/dashboard` | Must Have | `Draft` |
| _[Entity] List_ | `/entities` | Must Have | `Draft` |
| _[Entity] Form_ | `/entities/new`, `/entities/:id/edit` | Must Have | `Draft` |
| _Login_ | `/login` | Must Have | `Draft` |
| _Register_ | `/register` | Must Have | `Draft` |

---

## Scope

- This document defines **what each page contains and how it behaves**.
- One page per section. No combining multiple pages into one spec.

## Out of Scope

- Visual design / mockups (handled by UX/UI Designer)
- API implementation (see `09-api-design.md`)
- Database schema (see `07-data-model.md`)

## Guardrails

- [ ] Every page must have loading, empty, and error states defined
- [ ] Every form must have validation rules for all required fields
- [ ] Permissions must match `04-user-roles.md`
- [ ] Pages must not expose data outside the user's scoping rules

## Related Files

- `04-user-roles.md` — Permission definitions referenced in each page
- `05-user-flows.md` — Flows that are implemented across pages
- `07-data-model.md` — Data structures displayed and edited on pages
- `09-api-design.md` — API endpoints that pages consume

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
