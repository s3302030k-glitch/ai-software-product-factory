# 10 — Security Model

> Authorization, access control, and audit security for the Integrated Operations ERP.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This is not a runnable application. No production security claim is made.
> See the [example README](../README.md) for full context.

> [!WARNING]
> This document describes a **conceptual security model** for documentation purposes only. It does not constitute a production security guarantee, a penetration test result, or a compliance certification.

---

## Authentication vs Authorization

| Concept | Description |
|---------|-------------|
| **Authentication** | Verifying who the user is. Handled via session token / JWT issued on sign-in. All API endpoints verify the token server-side. |
| **Authorization** | Determining what the authenticated user may do. Enforced via role assignments and operational scope at the API layer. |
| **Client-side checks** | UI shows/hides elements based on role. These are **convenience only** — they do not provide security. All enforcement is server-side. |
| **Operational Scope** | The set of departments and/or warehouses assigned to a user. All data queries are filtered to the user's scope. |

---

## Role Boundaries

Each role has a defined permission set. See [04-user-roles.md](04-user-roles.md) for full details.

| Role | Can Create Records | Can Approve | Can Manage Users | Finance Access |
|------|--------------------|-------------|-------------------|---------------|
| Platform Admin | Config only | No | Yes | No |
| Operations Director | No | Yes (escalated) | No | Read-only |
| Warehouse Manager | Stock/Receiving/Dispatch | Yes (adjustments) | No | No |
| Inventory Clerk | Stock/Receiving | No | No | No |
| Purchasing Manager | Suppliers/POs | Yes (POs) | No | No |
| Sales Coordinator | Customers/SOs/Dispatch | No | No | No |
| Finance Officer | Invoices/Payments | No | No | Full |
| Approver / Dept. Mgr | No (approval actions only) | Yes | No | No |
| Read-only Auditor | No | No | No | Read-only |

---

## Operational Scoping

- **Department Scope:** Users assigned to specific departments can only create and view records within those departments. Cross-department records are hidden.
- **Warehouse Scope:** Warehouse Manager and Inventory Clerk can only create and view stock records for their assigned warehouses. Cross-warehouse access is blocked.
- **Scope Enforcement:** Scope is enforced at the query level — not by hiding UI elements alone. All API queries include a `WHERE warehouse_id IN (user_assigned_warehouses)` or equivalent filter.
- **Platform Admin** has no operational scope filter — they see all records for configuration purposes but cannot perform operational actions (stock movements, approvals) unless also assigned an operational role.

---

## Warehouse / Inventory Access Controls

- **Stock Movement Creation:** Only Inventory Clerk and Warehouse Manager within their assigned warehouse scope.
- **Receiving Creation:** Only Inventory Clerk and Warehouse Manager within assigned warehouse.
- **Adjustment Submission:** Any user with stock access within their warehouse scope.
- **Adjustment Approval:** Warehouse Manager (within scope) or Approver/Dept. Manager. Self-approval blocked.
- **Stock Balance Read:** All operational roles within their scope. Read-only Auditor sees all.
- **StockMovement records are immutable:** No edit or delete endpoint exists.
- **Direct balance mutation is prohibited:** `StockBalance.current_balance` is never written directly — only recomputed from `StockMovement` records.

---

## Finance Section Access Controls

- Finance pages (Invoice List, Payment List, Finance Overview) are accessible only to Finance Officer and Operations Director (read-only).
- All other roles receive a 403 on finance API endpoints.
- Finance amounts are display placeholders — no real accounting, bank, or payment data.
- Finance Officer cannot modify stock records.
- Finance data is not included in warehouse-scoped API responses.

---

## Approval Authority

- Approval authority is a separate permission from data-entry authority.
- A user who submitted a record **cannot approve it** — enforced at the API level, not just the UI.
- Approval routing is determined by the department/warehouse scope of the request.
- Approval limits define the maximum approvable value. Requests above the limit are escalated.
- Escalation routes to Operations Director. Operations Director can approve all escalated requests.
- All approval decisions (approve, reject, escalate) are recorded as immutable AuditEvents.

**Self-Approval Enforcement:**
```
IF approval_request.requester_user_id == current_user.id:
    RETURN 403 Forbidden ("Self-approval is not permitted")
```

---

## Support / Admin Restrictions

- Platform Admin has configuration authority (users, roles, departments, warehouses) but does not have operational approval authority.
- Platform Admin cannot act as an approver for purchase requests or stock adjustments unless also assigned the Approver role.
- Platform Admin actions (user creation, role assignment) are logged as AuditEvents.
- There is no "super admin bypass" of approval workflows.

---

## Audit Logging

- Every state change in the system produces an `AuditEvent` record.
- AuditEvents are **immutable** — no edit or delete endpoint or mechanism.
- AuditEvents capture: entity_type, entity_id, action, actor_user_id, before_snapshot, after_snapshot, occurred_at.
- Before/after snapshots must not include plaintext passwords, session tokens, or raw financial credentials.
- Audit log is accessible only to Read-only Auditor and Operations Director.
- Audit log cannot be purged, truncated, or modified by any role including Platform Admin.

---

## Sensitive Data Handling

| Data Type | Policy |
|-----------|--------|
| Passwords / credentials | Never stored in plaintext. Never logged. Never returned in API responses. |
| Session tokens | Server-side only or stored in httpOnly cookies. Never in localStorage for sensitive operations. |
| Financial amounts | Placeholder display values only. No real bank/payment data. |
| Supplier/Customer contact | Placeholder email format only. No real PII in this documentation reference. |
| Audit snapshots | Must redact credentials and tokens before storing. |

> [!WARNING]
> No real credentials, API keys, bank account data, tax IDs, IBANs, SWIFT codes, or private business information are included in this documentation reference.

---

## RLS / Security Policy Concept (Supabase)

If the implementation uses Supabase as the backend database:

- Row Level Security (RLS) must be enabled on all operational tables.
- RLS policies must enforce operational scope (warehouse, department) at the database level — not just at the application layer.
- The Supabase service role key must never be exposed to the client.
- Auth is handled via Supabase Auth. All API access uses the user's JWT, not the service role.
- Edge Functions (if used) must verify the user's JWT and enforce role checks — not trust client claims.

See [21-supabase-notes.md](21-supabase-notes.md) for full Supabase-specific security notes.

---

## No Production Security Claim

> [!WARNING]
> This security model is a documentation reference. It has not been independently audited, penetration tested, or certified for any compliance standard (SOC 2, ISO 27001, PCI-DSS, HIPAA, GDPR, etc.). Before any production deployment, the actual implementation must be reviewed by qualified security professionals.

---

## Related Files

- [04-user-roles.md](04-user-roles.md) — Role permission matrix
- [07-data-model.md](07-data-model.md) — Entity-level access requirements
- [09-api-design.md](09-api-design.md) — API auth and error handling
- [21-supabase-notes.md](21-supabase-notes.md) — Supabase RLS concept
- [15-ai-agent-operating-rules.md](15-ai-agent-operating-rules.md) — Agent constraints on security changes
