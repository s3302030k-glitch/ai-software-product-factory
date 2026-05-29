# 04 — User Roles

> Defines all user roles, their permissions, page access, and data scoping rules.

---

## Purpose

Establish a clear, enforceable permission model that every document and agent must follow. This document is the single authority on who can do what.

## Status

`Draft` | `In Review` | `Approved` | `Locked`

---

## Role Definitions

_Define each role in the system._

### Role: [Role Name]

| Attribute | Value |
|-----------|-------|
| **Internal Name** | _e.g., `admin`_ |
| **Display Name** | _e.g., Administrator_ |
| **Description** | _What this role represents_ |
| **Can Self-Register** | Yes / No |
| **Created By** | _e.g., System / Admin / Self_ |
| **Default for New Users** | Yes / No |

### Role: [Role Name]

| Attribute | Value |
|-----------|-------|
| **Internal Name** | |
| **Display Name** | |
| **Description** | |
| **Can Self-Register** | |
| **Created By** | |
| **Default for New Users** | |

_(Copy this block for each role.)_

---

## Permission Matrix

_Map roles to permissions across all entities/features._

| Permission | Admin | Manager | User | Guest |
|-----------|-------|---------|------|-------|
| Create [Entity A] | ✅ | ✅ | ❌ | ❌ |
| Read [Entity A] (own) | ✅ | ✅ | ✅ | ❌ |
| Read [Entity A] (all) | ✅ | ✅ | ❌ | ❌ |
| Update [Entity A] (own) | ✅ | ✅ | ✅ | ❌ |
| Update [Entity A] (all) | ✅ | ✅ | ❌ | ❌ |
| Delete [Entity A] | ✅ | ❌ | ❌ | ❌ |
| _Add more rows..._ | | | | |

### Permission Legend

- ✅ = Allowed
- ❌ = Denied
- 🔒 = Allowed with conditions (explain in notes)

---

## Page Access

_Which pages are accessible to which roles?_

| Page | Route | Admin | Manager | User | Guest |
|------|-------|-------|---------|------|-------|
| Dashboard | `/dashboard` | ✅ | ✅ | ✅ | ❌ |
| User Management | `/admin/users` | ✅ | ❌ | ❌ | ❌ |
| Settings | `/settings` | ✅ | ✅ | ✅ | ❌ |
| _Add more..._ | | | | | |

### Unauthorized Access Behavior

- Unauthenticated users → Redirect to login page
- Authenticated but unauthorized → Show 403 forbidden page or redirect to dashboard
- Direct URL access to unauthorized page → Same as above (no silent failure)

---

## Data Scoping

_Define what data each role can see and operate on._

| Role | Data Scope | Rule |
|------|-----------|------|
| Admin | All data | No restrictions |
| Manager | Department/team data | Filter by `team_id` or `department_id` |
| User | Own data only | Filter by `user_id` = current user |
| Guest | Public data only | Filter by `is_public` = true |

### Data Scoping Rules

1. Data scoping is applied at the **query level**, not the UI level
2. Even if a user crafts a direct API request, scoping must be enforced server-side
3. Scoping rules must be applied consistently across list views, detail views, and API endpoints
4. Scoping must be tested as part of QA for every feature

---

## Future Roles

_Roles planned for future versions but explicitly out of scope for v1._

| Role | Planned For | Notes |
|------|-------------|-------|
| _e.g., Auditor_ | _v2_ | _Read-only access to all data + audit logs_ |
| _e.g., API User_ | _v2_ | _Machine-to-machine access via API keys_ |

---

## Scope

- This document defines **roles, permissions, and data access rules**.
- All page specs, API endpoints, and data queries must comply with this document.

## Out of Scope

- Authentication mechanisms (see `10-security-model.md`)
- UI component design (see `06-pages-spec.md`)
- API authorization implementation details (see `09-api-design.md`)

## Guardrails

- [ ] No page or API endpoint may be created without a corresponding entry in the page access table
- [ ] Data scoping must be enforced server-side, never client-side only
- [ ] Role changes require human approval and a decision log entry
- [ ] AI agents must not add, remove, or modify roles without explicit authorization

## Related Files

- `01-product-brief.md` — Target users that map to roles
- `02-discovery.md` — User personas that inform role design
- `06-pages-spec.md` — Page-level specs that reference roles
- `09-api-design.md` — API authorization rules
- `10-security-model.md` — Authentication and security framework

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
