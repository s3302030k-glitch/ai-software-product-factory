# Operational Audit Trail Guidelines

> Defines requirements for tracking changes to operational records, recording lifecycle transitions, capturing administrative overrides, and protecting audit integrity.

---

## Purpose

This document outlines the design and implementation standards for system-wide auditing of inventory, warehouse locations, and workflow states. It prevents unauthorized history erasure and ensures accountability. It supplements the security model in [10-security-model.md](../../../core/docs/10-security-model.md) and relates to [workflow-and-approval-guidelines.md](workflow-and-approval-guidelines.md).

## Status

`Active` — Must be implemented for all core data tracking and mutation endpoints.

---

## Operational Audit Principles

### 1. Actor, Timestamp, and Reason Pattern
Any write operation altering the status, physical location, quantity, or financial value of a record must capture:
- `actor_id`: The system user ID responsible for the action.
- `timestamp`: The server-generated datetime of the transaction.
- `reason`: A structured or textual description of why the change was made (required for all manual adjustments or overrides).

### 2. Before/After Value snapshots
For data modifications, the audit log must capture a snapshot of the changed columns:
- Save both the original value and the new value.
- **Format**: Store these fields in a structured format (such as a JSONB column `changed_values` containing `{"field_name": {"before": X, "after": Y}}`).

### 3. Event Logs vs. Audit Logs
- **Event Logs**: Temporary, high-volume logs containing system activities (e.g., page views, search queries). Can be rotated or archived.
- **Audit Logs**: Permanent, immutable records of state changes, stock movements, and financial authorizations. **Must never be modified, deleted, or rotated.**

---

## What Must Be Audited

The audit trail must capture changes to the following domains:

### 1. Stock Movement Audit
- Every addition, subtraction, transfer, or adjustment of stock.
- The history of inventory balances must be reconstructible from inception by running the ledger.

### 2. Workflow State Audit
- Transition of state machines (e.g., from `Pending Approval` to `Approved`).
- Approver identification, timestamp, and signature validation.

### 3. Warehouse Location Audit
- Transfers of stock between zones/bins.
- Re-classification of warehouse zones or bin configuration changes.

---

## Admin Override Rules

Administrators or system operators must not have a silent backdoor to modify operational logs.
- If an admin overrides a block (e.g., forcing a shipment despite a stock mismatch):
  - The system must require entering a justification reason.
  - The audit log entry must flag `is_override: true` and escalate a notification to the product owner or operations director.
  - The original error or block message must be recorded along with the override.

---

## Soft Delete vs. Hard Delete

- **Hard Deletes**: Hard deleting operational history, stock movements, receiving logs, or shipments is **strictly blocked** at the database layer (e.g., by restricting DELETE privileges or using database triggers).
- **Soft Deletes**: Soft deletes (`deleted_at` timestamp) are used only for configuration records (e.g., disabling a warehouse zone or a product SKU).
- If a soft deleted record has historical stock movements, the movements must remain accessible in the audit reports to preserve the historical balance totals.

---

## Audit Trail Security and Protection

To protect the integrity of the audit logs:
- **Role Isolation**: Normal database users (including application connections) must have read-only or insert-only access to audit logs. They must not possess update or delete access.
- **Immutability**: Configure database triggers to block updates or deletions on audit tables.

---

## Out of Scope

- Client-side application debug log storage.
- External security information and event management (SIEM) integrations.
- User session tracking (analytics cookies).

---

## Guardrails

- [ ] **NO ERASING HISTORY**: Operational movements or state logs must never be deleted.
- [ ] **MANDATORY REASONS**: Manual adjustments and admin overrides require a reason input.
- [ ] **IMMUTABLE AUDITS**: Normal app roles must not have edit or delete access to audit log tables.
- [ ] **STRUCTURED DIFFS**: Audit records must capture specific before/after values, not just "Record Updated."

---

## QA Checklist

- [ ] Verify that saving a change to a workflow status writes an audit record capturing the user, time, and transition.
- [ ] Attempt to run a SQL update/delete query on the audit ledger table and verify the database blocks it.
- [ ] Verify that performing a stock adjustment records the before and after quantities.
- [ ] Check that admin overrides require entering a reason and successfully tag `is_override: true` in the logs.

---

## Related Core Files

- [10-security-model.md](../../../core/docs/10-security-model.md) — Security model and database safety constraints.
- [07-data-model.md](../../../core/docs/07-data-model.md) — Core database schema layout rules.
- [inventory-and-stock-guidelines.md](inventory-and-stock-guidelines.md) — Stock ledger requirements.
- [README.md](../README.md) — ERP Extension Pack README.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial operational audit trail guidelines for ERP Operations Pack | Antigravity |
