# 04 — User Roles

> Full role permission matrix for the Integrated Operations ERP.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This is not a runnable application. See the [example README](../README.md) for full context.

---

## Role Design Principles

1. **Authentication vs Authorization:** Authentication (who you are) is handled at the session level. Authorization (what you can do) is enforced via role assignments and operational scoping at the API and data layer.
2. **System roles vs Operational roles:** Platform Admin is a system role. All other roles are operational roles tied to departments and warehouse assignments.
3. **Approval authority vs Data-entry authority:** Approvers and Dept. Managers have approval authority over requests. Inventory Clerks and Coordinators have data-entry authority. These are never combined in the same role to avoid self-approval.
4. **Operational scoping:** Each user is assigned to one or more departments and/or warehouses. Data access is filtered to their assigned scope.
5. **Least privilege:** Each role can access only what it needs for its defined function. Access to finance, warehouse, and approval sections is explicitly granted, not assumed.

---

## Role Matrix

### 1. Platform Admin

| Attribute | Value |
|-----------|-------|
| **Role Purpose** | Manages users, roles, system configuration, and platform settings. Does not participate in operational workflows. |
| **Allowed Actions** | Create/update/deactivate users; assign/revoke roles; configure departments, warehouses, currencies, system settings; read all operational records for oversight. |
| **Restricted Actions** | Cannot approve operational requests on behalf of business roles. Cannot create business records (stock movements, purchase orders, invoices) unless also assigned an operational role. |
| **Approval Requirements** | N/A — not part of approval workflows. |
| **Audit Events** | User created; user deactivated; role assigned; role revoked; system setting changed. |

---

### 2. Operations Director

| Attribute | Value |
|-----------|-------|
| **Role Purpose** | Monitors cross-departmental operational health and approves escalated requests. |
| **Allowed Actions** | Read all departments, warehouses, stock, POs, sales orders, finance overview; approve escalated purchase requests above department manager authorization limit; access operational dashboard; access all reports and audit logs. |
| **Restricted Actions** | Cannot create stock movements, adjustments, purchase orders, sales orders, or invoices directly. Cannot modify role assignments. Cannot approve their own requests. |
| **Approval Requirements** | Can approve escalated requests. All approvals are recorded with timestamp and actor. |
| **Audit Events** | Approval granted; approval rejected; escalated request reviewed; report exported. |

---

### 3. Warehouse Manager

| Attribute | Value |
|-----------|-------|
| **Role Purpose** | Manages day-to-day warehouse operations within assigned warehouse(s). |
| **Allowed Actions** | Create and update stock movements, receiving records, and dispatch placeholders within assigned warehouse; create and submit stock adjustment requests; approve stock adjustments within their authorization limit; view purchase orders and sales orders (read-only); view stock overview; access warehouse and zone configuration (read). |
| **Restricted Actions** | Cannot approve their own adjustment requests. Cannot create or approve purchase requests or purchase orders. Cannot access invoice or payment records. Cannot modify role assignments. Cannot act outside their assigned warehouse scope. |
| **Approval Requirements** | Stock adjustment requests above a defined quantity threshold require Warehouse Manager approval. Adjustments above a higher threshold escalate to Operations Director. |
| **Audit Events** | Stock movement created; receiving record created; adjustment request submitted; adjustment approved or rejected; dispatch placeholder created. |

---

### 4. Inventory Clerk

| Attribute | Value |
|-----------|-------|
| **Role Purpose** | Executes day-to-day stock recording operations within assigned warehouse. |
| **Allowed Actions** | Create stock movement records within assigned warehouse; create receiving entries; submit stock adjustment requests. |
| **Restricted Actions** | Cannot approve adjustments independently. Cannot create purchase requests, purchase orders, sales orders, or dispatch records. Cannot access finance documents. Cannot access users or settings. |
| **Approval Requirements** | All stock adjustment requests require Warehouse Manager approval regardless of quantity. |
| **Audit Events** | Stock movement created; receiving record created; adjustment request submitted. |

---

### 5. Purchasing Manager

| Attribute | Value |
|-----------|-------|
| **Role Purpose** | Manages procurement lifecycle from purchase request through purchase order placeholder. |
| **Allowed Actions** | Create and update supplier placeholder records; review, approve, or reject purchase requests; create and update purchase order placeholders; link POs to receiving records; access approval inbox for procurement requests. |
| **Restricted Actions** | Cannot approve their own purchase requests. Cannot create stock movements or invoices directly. Cannot act outside their assigned department scope. |
| **Approval Requirements** | Purchase requests above their authorization limit escalate to Operations Director. Cannot approve their own submissions. |
| **Audit Events** | Supplier record created; purchase request approved or rejected; purchase order placeholder created; PO linked to receiving. |

---

### 6. Sales Coordinator

| Attribute | Value |
|-----------|-------|
| **Role Purpose** | Manages sales order lifecycle through dispatch placeholder. |
| **Allowed Actions** | Create and update customer placeholder records; create and update sales order placeholders; create and update dispatch placeholders; read stock overview for availability checking. |
| **Restricted Actions** | Cannot create stock movements or adjustments. Cannot create purchase requests or purchase orders. Cannot access invoice or payment records. Cannot act outside their assigned department scope. |
| **Approval Requirements** | High-value sales orders may require Approver/Dept. Manager sign-off (if configured). |
| **Audit Events** | Customer record created; sales order placeholder created; dispatch placeholder created; dispatch status updated. |

---

### 7. Finance Officer

| Attribute | Value |
|-----------|-------|
| **Role Purpose** | Manages invoice and payment placeholder records. |
| **Allowed Actions** | Create and update invoice placeholders; create and update payment placeholders; access finance overview; read purchase orders and sales orders for reference; export finance report placeholders. |
| **Restricted Actions** | Cannot create stock movements, adjustments, purchase orders, or sales orders. Cannot approve operational requests. Finance documents are placeholders only — not real accounting or legal instruments. |
| **Approval Requirements** | N/A — Finance Officers do not participate in operational approval workflows. |
| **Audit Events** | Invoice placeholder created or updated; payment placeholder recorded; finance report exported. |

---

### 8. Approver / Department Manager

| Attribute | Value |
|-----------|-------|
| **Role Purpose** | Holds formal approval authority for operational requests within their department scope. This role is additive — a user may hold both Purchasing Manager and Approver roles, but they cannot approve their own submissions. |
| **Allowed Actions** | Review and approve or reject purchase requests; review and approve or reject stock adjustment requests; escalate requests above their authorization limit; access approval inbox. |
| **Restricted Actions** | Cannot approve their own requests (system-enforced). Cannot modify the records they are approving. Cannot create operational records unless also holding the relevant operational role. |
| **Approval Requirements** | Must have explicit approval authority assignment in their department scope. Authorization limits define maximum approvable values. |
| **Audit Events** | Request approved; request rejected; request escalated; approval limit updated (by admin). |

---

### 9. Read-only Auditor

| Attribute | Value |
|-----------|-------|
| **Role Purpose** | Internal or external auditor with full read access to all operational records, audit logs, and reports. No write access. |
| **Allowed Actions** | Read all entities, records, audit log, reports. Download report exports (CSV/PDF placeholders). |
| **Restricted Actions** | Cannot create, edit, approve, or delete any record. Cannot access admin settings. Cannot modify any configuration. |
| **Approval Requirements** | N/A — No write access of any kind. |
| **Audit Events** | Report downloaded; audit log accessed. |

---

## Approval Authority Hierarchy

```
Operations Director (escalated approvals — highest authority)
      ↑
Approver / Department Manager (department-scoped approvals)
      ↑
Warehouse Manager (warehouse-scoped stock adjustments)
```

- Self-approval is **never permitted** regardless of role combination.
- Escalation is triggered when request value exceeds the approver's authorization limit.
- All approval decisions are logged as immutable AuditEvents.

---

## Authentication vs Authorization Summary

| Concept | Definition |
|---------|-----------|
| Authentication | Verifying the user's identity (session token, login). |
| Authorization | Determining what the authenticated user may do (role + operational scope). |
| Operational Scope | The set of departments and/or warehouses the user is permitted to access. |
| Role | Defines allowed and restricted actions within the user's operational scope. |
| Approval Authority | A specific permission that allows a role to approve or reject requests. Separate from data-entry rights. |

---

## Related Files

- [02-target-users.md](02-target-users.md) — Persona goals and pain points per role
- [05-user-flows.md](05-user-flows.md) — Per-role user flows
- [10-security-model.md](10-security-model.md) — Technical authorization model
- [15-ai-agent-operating-rules.md](15-ai-agent-operating-rules.md) — Agent constraints on role changes
