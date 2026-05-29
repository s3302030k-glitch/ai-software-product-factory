# 04 — User Roles

> Defines all user roles, their permissions, page access, and data scoping rules for Invoice Tracker.

---

## Purpose

Establish a clear, enforceable permission model that every document and agent must follow. This document is the single authority on who can do what.

## Status

`Approved`

---

## Role Definitions

### Role: Owner

| Attribute | Value |
|-----------|-------|
| **Internal Name** | `owner` |
| **Display Name** | Owner |
| **Description** | Small business founder or freelancer. Has full read, write, edit, delete, and settings permissions across the system. |
| **Can Self-Register** | Yes (first user registers as Owner) |
| **Created By** | Self / System |
| **Default for New Users** | Yes (if first user in system) |

### Role: Staff

| Attribute | Value |
|-----------|-------|
| **Internal Name** | `staff` |
| **Display Name** | Staff |
| **Description** | Employee, billing assistant, or contractor who drafts invoices, inputs line items, and logs client payments. |
| **Can Self-Register** | No |
| **Created By** | Owner |
| **Default for New Users** | No |

---

## Permission Matrix

Map roles to permissions across all entities/features.

| Permission | Owner | Staff | Guest |
|-----------|-------|-------|-------|
| Create Client | ✅ | ✅ | ❌ |
| Read Client | ✅ | ✅ | ❌ |
| Update Client | ✅ | ✅ | ❌ |
| Delete Client | ✅ | ❌ | ❌ |
| Create Invoice | ✅ | ✅ | ❌ |
| Read Invoice | ✅ | ✅ | ❌ |
| Update Invoice | ✅ | ✅ | ❌ |
| Delete Invoice | ✅ | ❌ | ❌ |
| Record Payment | ✅ | ✅ | ❌ |
| Update Settings | ✅ | ❌ | ❌ |

### Permission Legend

- ✅ = Allowed
- ❌ = Denied

---

## Page Access

Which pages are accessible to which roles?

| Page | Route | Owner | Staff | Guest |
|------|-------|-------|-------|-------|
| Login | `/login` | ✅ | ✅ | ✅ |
| Dashboard | `/dashboard` | ✅ | ✅ | ❌ |
| Clients List | `/clients` | ✅ | ✅ | ❌ |
| Client Detail | `/clients/[id]` | ✅ | ✅ | ❌ |
| Create/Edit Client | `/clients/form` | ✅ | ✅ | ❌ |
| Invoices List | `/invoices` | ✅ | ✅ | ❌ |
| Invoice Detail | `/invoices/[id]` | ✅ | ✅ | ❌ |
| Create/Edit Invoice | `/invoices/form` | ✅ | ✅ | ❌ |
| Record Payment | `/invoices/[id]/payment` | ✅ | ✅ | ❌ |
| Settings | `/settings` | ✅ | ❌ | ❌ |

### Unauthorized Access Behavior

- Unauthenticated users (Guests attempting to visit authenticated routes) → Redirect to `/login` page.
- Authenticated but unauthorized (Staff visiting `/settings` or attempting Client/Invoice deletion) → Show 403 Forbidden page or toast message and redirect to `/dashboard`.

---

## Data Scoping

Since the MVP is a single-tenant application representing a single business or freelancer, both Owner and Staff operate on the **same shared organization data** (all clients, all invoices, all payments).

| Role | Data Scope | Rule |
|------|-----------|------|
| Owner | All organization data | No restrictions |
| Staff | All organization data | Restricted only by action (cannot delete records or update settings) |

---

## Future Roles

Roles planned for future versions but explicitly out of scope for v1.

| Role | Planned For | Notes |
|------|-------------|-------|
| Client Portal User | v2 | A read-only client user who can log in to view and download their own invoices. |
| Accountant | v2 | Read-only access to invoices and payments, plus ability to export general ledgers. |

---

## Scope

- This document defines **roles, permissions, and data access rules**.
- All page specs, API endpoints, and data queries must comply with this document.

## Out of Scope

- Authentication mechanisms (see `10-security-model.md`)
- UI component design (see `06-pages-spec.md`)
- API authorization implementation details (see `09-api-design.md`)

## Guardrails

- [x] No page or API endpoint may be created without a corresponding entry in the page access table
- [x] Data scoping must be enforced server-side, never client-side only
- [x] Role changes require human approval and a decision log entry
- [x] AI agents must not add, remove, or modify roles without explicit authorization

## Related Files

- `01-product-brief.md` — Target users that map to roles
- `02-discovery.md` — User personas that inform role design
- `06-pages-spec.md` — Page-level specs that reference roles
- `09-api-design.md` — API authorization rules
- `10-security-model.md` — Authentication and security framework

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial version for Invoice Tracker | Product Owner |
