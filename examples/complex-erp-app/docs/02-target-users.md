# 02 — Target Users

> Detailed persona definitions for all roles in the Integrated Operations ERP.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This is not a runnable application. See the [example README](../README.md) for full context.

---

## Overview

The Integrated Operations ERP serves nine distinct user roles across warehouse, procurement, sales, finance, and administration departments. Each role has defined goals, permissions, key pages, pain points addressed by this system, and explicit out-of-scope abilities.

Full role permissions are defined in [04-user-roles.md](04-user-roles.md).

---

## 1. Operations Director

**Who they are:** A senior manager responsible for cross-departmental operational performance. Has visibility into procurement, warehouse, sales, and finance operations.

**Goals:**
- Monitor overall operational health via the dashboard.
- Identify bottlenecks in procurement, stock, or fulfillment.
- Approve high-value or escalated requests.
- Access operational reports and audit logs.

**Permissions Summary:**
- Read access to all departments.
- Can approve escalated purchase requests and purchase orders.
- Cannot create stock movements, adjustments, or invoices directly.
- Cannot modify role assignments.

**Key Pages:**
- Dashboard
- Approval Inbox
- Reports
- Audit Log

**Pain Points Addressed:**
- No real-time cross-department visibility.
- Approval bottlenecks and escalation paths not defined.
- No unified report across warehouse and finance status.

**Out-of-Scope Abilities:**
- Cannot create operational records (stock movements, invoices, orders).
- Cannot modify system settings or user roles.
- Cannot access raw database or system administration.

---

## 2. Warehouse Manager

**Who they are:** Manages day-to-day warehouse operations including receiving, stock movements, zone management, and dispatch coordination.

**Goals:**
- Maintain accurate stock levels across all zones.
- Coordinate receiving of goods from suppliers.
- Oversee stock adjustments with proper approval.
- Prepare dispatch records for sales orders.

**Permissions Summary:**
- Full read/write access to warehouse and stock operations within their assigned warehouse(s).
- Can initiate stock adjustments (require approval for large quantities).
- Can complete receiving records.
- Cannot approve their own purchase requests.
- Cannot access finance documents directly.

**Key Pages:**
- Stock Overview
- Warehouses & Zones
- Receiving
- Stock Movements
- Stock Adjustments
- Dispatch / Shipments

**Pain Points Addressed:**
- Inventory mismatch between physical count and records.
- No formal receiving workflow.
- Adjustment records lost in spreadsheets.

**Out-of-Scope Abilities:**
- Cannot create or approve purchase orders.
- Cannot create sales orders.
- Cannot view or edit invoice/payment documents.
- Cannot assign roles to users.

---

## 3. Inventory Clerk

**Who they are:** Executes day-to-day stock operations including recording movements, completing receiving, and recording adjustments under manager supervision.

**Goals:**
- Record stock movements accurately and promptly.
- Complete receiving records as goods arrive.
- Record stock adjustments when instructed and approved.
- Maintain clean movement history.

**Permissions Summary:**
- Can create and record stock movements within their assigned warehouse.
- Can record receiving entries.
- Can submit stock adjustment requests (pending approval).
- Cannot approve adjustments independently.
- Read-only access to purchase orders and sales orders.

**Key Pages:**
- Stock Movements
- Receiving
- Stock Adjustments
- Stock Overview (read-only)

**Pain Points Addressed:**
- No structured tool to record movements.
- Adjustment records not formally approved.
- Receiving not tracked against purchase orders.

**Out-of-Scope Abilities:**
- Cannot approve adjustments, purchase requests, or sales orders.
- Cannot access finance documents.
- Cannot modify product catalog or warehouse configurations.

---

## 4. Purchasing Manager

**Who they are:** Manages the procurement lifecycle from purchase request through purchase order placeholder and supplier relationship management.

**Goals:**
- Review and approve/reject purchase requests.
- Create and track purchase order placeholders.
- Manage supplier placeholder records.
- Coordinate with warehouse on receiving.

**Permissions Summary:**
- Full read/write access to purchase requests and purchase orders.
- Can approve purchase requests within their authorization limit.
- Can create and manage supplier placeholder records.
- Cannot directly create stock movements or invoices.

**Key Pages:**
- Purchase Requests
- Purchase Orders
- Supplier List
- Approval Inbox

**Pain Points Addressed:**
- Purchase requests lost in email chains.
- No formal PO tracking against receiving records.
- Supplier master data scattered across files.

**Out-of-Scope Abilities:**
- Cannot approve their own purchase requests.
- Cannot edit sales orders or dispatch records.
- Cannot access finance payment records directly.

---

## 5. Sales Coordinator

**Who they are:** Manages the sales order lifecycle from creation through dispatch placeholder, and maintains customer placeholder records.

**Goals:**
- Create and track sales order placeholders.
- Coordinate dispatch and shipment placeholders.
- Manage customer placeholder records.
- Monitor stock availability for fulfillment planning.

**Permissions Summary:**
- Full read/write access to sales orders, dispatch placeholders, and customer records.
- Read-only access to stock overview for availability checks.
- Cannot directly modify stock records or invoke adjustments.
- Cannot access finance documents directly.

**Key Pages:**
- Sales Orders
- Dispatch / Shipments
- Customer List
- Stock Overview (read-only)

**Pain Points Addressed:**
- No formal sales order tracking tied to stock.
- Dispatch status unknown without warehouse communication.
- Customer records not centralized.

**Out-of-Scope Abilities:**
- Cannot create or approve purchase requests.
- Cannot edit stock movements or adjustments.
- Cannot access invoice or payment records.

---

## 6. Finance Officer

**Who they are:** Manages invoice placeholder creation, payment placeholder recording, and financial document tracking.

**Goals:**
- Create and track invoice placeholders for purchase and sales orders.
- Record payment placeholder entries.
- Access finance overview and reconciliation placeholders.
- Maintain accurate financial document trail.

**Permissions Summary:**
- Full read/write access to invoice and payment placeholders.
- Read-only access to purchase orders and sales orders.
- Cannot create stock movements or modify warehouse records.
- Cannot approve purchase requests or sales orders.

**Key Pages:**
- Finance Overview
- Invoice List
- Payment Placeholder List
- Reports (finance section)

**Pain Points Addressed:**
- Invoice records not linked to orders.
- Payment records not formally tracked.
- No unified finance overview per department.

**Out-of-Scope Abilities:**
- Cannot create or edit purchase orders or sales orders.
- Cannot modify stock records.
- Cannot manage users or roles.
- Finance documents are placeholders only — not real accounting or tax records.

---

## 7. Approver / Department Manager

**Who they are:** A role held by department managers across procurement, warehouse, or sales who have explicit approval authority for requests within their scope.

**Goals:**
- Review and approve/reject incoming approval requests.
- Maintain approval audit trail.
- Escalate high-value requests to the Operations Director.

**Permissions Summary:**
- Can approve or reject purchase requests within their authorization limit.
- Can approve stock adjustment requests.
- Cannot modify the records they are approving.
- Cannot approve their own requests.

**Key Pages:**
- Approval Inbox
- Purchase Requests (read-only with approve/reject action)
- Stock Adjustments (read-only with approve/reject action)

**Pain Points Addressed:**
- Approval requests not centralized.
- No formal approval trail.
- Escalation paths not defined.

**Out-of-Scope Abilities:**
- Cannot create operational records.
- Cannot approve finance documents unless also assigned Finance Officer role.
- Cannot modify role assignments.

---

## 8. Read-only Auditor

**Who they are:** An internal or external auditor with full read access to all operational records, audit logs, and reports — with no ability to create or modify records.

**Goals:**
- Review operational records for compliance.
- Access full audit log.
- Export reports for review.

**Permissions Summary:**
- Read-only access to all entities, records, audit logs, and reports.
- Cannot create, edit, approve, or delete any record.
- Cannot modify any configuration.

**Key Pages:**
- Audit Log
- Reports
- All list pages (read-only)

**Pain Points Addressed:**
- No unified audit access without IT intervention.
- Audit log not immutable or centralized.

**Out-of-Scope Abilities:**
- Cannot create, edit, approve, or delete any record.
- Cannot export data beyond report downloads.

---

## 9. Platform Admin

**Who they are:** A technical administrator who manages users, roles, system configuration, and platform-level settings.

**Goals:**
- Create and manage user accounts.
- Assign and modify roles.
- Configure system settings (warehouses, departments, currencies).
- Monitor system health.

**Permissions Summary:**
- Full access to Settings / Admin panel.
- Can create, update, and deactivate users.
- Can assign and revoke roles.
- Cannot approve operational requests on behalf of business roles.
- Cannot directly create business records (invoices, stock movements) outside their scope.

**Key Pages:**
- Settings / Admin
- All list pages (read-only oversight)

**Pain Points Addressed:**
- No structured user management in spreadsheet era.
- Role boundaries not enforced technically.

**Out-of-Scope Abilities:**
- Cannot approve purchase requests or operational approvals unless also holding a business role.
- Cannot modify financial calculations or accounting rules.
- System admin access does not grant operational approval authority.

---

## Role-to-Page Quick Reference

| Role | Dashboard | Stock | Receiving | POs | Sales | Finance | Approval Inbox | Audit Log | Admin |
|------|-----------|-------|-----------|-----|-------|---------|----------------|-----------|-------|
| Operations Director | ✓ | R | R | R | R | R | ✓ (escalated) | R | — |
| Warehouse Manager | ✓ | ✓ | ✓ | R | R | — | ✓ (adj.) | R | — |
| Inventory Clerk | — | ✓ | ✓ | R | — | — | — | — | — |
| Purchasing Manager | ✓ | R | R | ✓ | R | — | ✓ (PO) | R | — |
| Sales Coordinator | ✓ | R | — | R | ✓ | — | — | — | — |
| Finance Officer | — | — | — | R | R | ✓ | — | R | — |
| Approver / Dept. Mgr | — | R | R | R | R | — | ✓ | R | — |
| Read-only Auditor | R | R | R | R | R | R | R | R | — |
| Platform Admin | — | — | — | — | — | — | — | R | ✓ |

`✓` = Read + Write, `R` = Read-only, `—` = No access

---

## Related Files

- [04-user-roles.md](04-user-roles.md) — Full role permission matrix
- [05-user-flows.md](05-user-flows.md) — Per-role user flows
- [10-security-model.md](10-security-model.md) — Authorization boundaries
