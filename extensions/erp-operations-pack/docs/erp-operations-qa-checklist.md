# ERP Operations QA Checklist

> Comprehensive testing checklists, regression matrices, bug report formats, and release readiness rules for validating ERP and operational software features.

---

## Purpose

This document provides a structured quality assurance verification matrix to ensure that any ERP or operations implementation is robust, secure, and accurate. It supplements the core QA plan in [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) and relates to all guidelines in this extension pack.

## Status

`Active` — Must be executed during pre-release testing cycles and QA audits.

---

## 1. Domain Model QA Checklist

- [ ] **Planning vs. Execution separation**: Verify that editing an order (planning) does not silently alter stock records (execution) until a movement document is completed.
- [ ] **State Machine Constraints**: Attempt to update an entity to an invalid lifecycle state (e.g., transitioning an order from `Draft` directly to `Completed`) and verify the system blocks the transition.
- [ ] **State Fields Location**: Audit code to ensure state logic is executed via database constraints/triggers or backend services, and is not defined solely in the UI.

---

## 2. Inventory / Stock QA Checklist

- [ ] **Quantity/Unit precision**: Verify that quantities are tracked as integers or high-precision decimals, preventing floating point rounding errors during math.
- [ ] **On-Hand vs. Available vs. Reserved vs. Allocated**: Verify that reserving an item decreases `Available` stock but does not decrease physical `On-Hand` stock.
- [ ] **Ledger-only updates**: Verify that updating product stock levels creates a corresponding record in the stock movement ledger table.
- [ ] **Negative Stock Validation**: With the default negative stock block enabled, attempt to deduct stock below zero and verify the transaction is rejected.
- [ ] **Stock Adjustments Audit**: Verify that recording stock adjustments saves a log containing:
  - Actor ID and Timestamp.
  - Reason for adjustment.
  - Before and after quantities.

---

## 3. Warehouse Operation QA Checklist

- [ ] **Location Hierarchies**: Verify that stock ledger records reference a specific site, zone, and bin location.
- [ ] **Mismatches (Planned vs. Actual)**:
  - **Partial Receiving**: Receive only 5 of 10 ordered items. Verify the purchase order status stays `In Progress` or `Partially Received` and on-hand stock increases by 5.
  - **Over-Receiving**: Attempt to receive 12 of 10 items. Verify the system alerts the worker and blocks the transaction until authorized.
  - **Partial Shipping**: Ship a partial quantity. Verify the shipment order captures the partial dispatch, leaving the remaining items unfulfilled.
- [ ] **Transfers Traceability**: Verify that moving stock between sites transitions the items to an `In-Transit` virtual warehouse, preventing them from being sold or allocated at the target site until receipt is confirmed.
- [ ] **Exception workflows**: Move damaged items to a QC zone. Verify they are excluded from available-for-sale lists.

---

## 4. Workflow / Approval QA Checklist

- [ ] **Four-Eyes Control (Dual Authorization)**: Submit an order for approval using User A's credentials. Attempt to approve it using User A's credentials and verify the system blocks it. Verify that User B (an authorized manager) can successfully approve it.
- [ ] **Approval Bypass Block**: Attempt to transition a transaction status directly via the API without the necessary manager approval trigger and verify the server rejects the request.
- [ ] **Rejections Handling**: Verify that rejecting a workflow transitions the record back to `Draft` or `Rejected` and requires entering a textual rejection reason.
- [ ] **Reversals History**: Reopen a completed order. Verify that the previous approval state is kept in the workflow audit log, and that a new approval cycle is initialized.

---

## 5. Audit Trail QA Checklist

- [ ] **Immutable Logs**: Attempt to run `UPDATE` or `DELETE` SQL queries on the stock movement log or workflow history tables and verify the database permissions block the action.
- [ ] **Diff Integrity**: Verify that update operations capture both the original and new column values (before/after snapshots) inside the audit table.
- [ ] **Admin Overrides Auditing**: Perform an administrator override action. Verify that the action creates an audit trail entry with `is_override: true` and includes the mandatory justification reason text.

---

## 6. Operational Reporting QA Checklist

- [ ] **Reconciliation Check**: Run the inventory valuation report. Calculate `sum(stock_movements.quantity)` and verify that the totals match down to the last decimal place.
- [ ] **TimeZone Boundary Validation**: Verify that transactions logged close to midnight UTC are correctly grouped and reported when switching report view filters between different timezone offsets.
- [ ] **Filter Transparencies**: Check that reports list all active filter options (e.g., "Warehouse: B, Status: QC Blocked") in the print layout or header.

---

## 7. Role / Permission QA Checklist

Verify the system blocks unauthorized operations based on the roles defined in [04-user-roles.md](../../../core/docs/04-user-roles.md):

- [ ] **Warehouse Worker**: Can pick, pack, and receive items; cannot edit product costs or delete warehouses.
- [ ] **Inventory Planner**: Can view stock levels and create purchase orders; cannot approve adjustments exceeding threshold limits.
- [ ] **Operations Manager**: Can approve adjustments, overrides, and change site structures.
- [ ] **Guest / Viewer**: Read-only access; cannot trigger state machine transitions.

---

## 8. Soft Delete / Cancellation QA Checklist

- [ ] **Compensating Records**: Cancel a shipment that has been processed. Verify that the system writes positive stock movements to restore the item quantities, and does not perform a hard delete on the original shipment record.
- [ ] **Archived References**: Soft delete a product SKU. Verify that historical stock movement logs referencing that SKU remain intact and visible in historical ledger reports.

---

## Regression Checklist

Before every release, run these scenario tests:
- Concurrent order reservation test (race conditions check).
- Bulk inventory upload validation (verifying all SKU records create corresponding stock movement logs).
- End-to-end purchasing cycle: PO creation → Receiving (Partial) → Putaway → Inventory Valuation reconciliation.
- End-to-end sales cycle: SO creation → Allocation → Picking → Packing → Shipping → Ledger Check.

---

## Bug Report Format

Use this format when documenting bugs found during testing:

```markdown
### Bug: [Short Description of Issue]

- **Domain Area**: [e.g., Inventory, Receiving, Approval Workflow]
- **Environment**: [e.g., Staging, Local CLI]
- **Steps to Reproduce**:
  1. [Step 1]
  2. [Step 2]
- **Expected Behavior**: [What should have happened]
- **Actual Behavior**: [What actually happened]
- **Audit Logs / Error Output**:
  `[Log extract or database error]`
- **Related Files**: [Path to files/code]
```

---

## Release Readiness Checklist

- [ ] All checklist items above are marked as passed or verified.
- [ ] No regression scenarios failed.
- [ ] No critical database performance bottlenecks are found on ledger summation queries.
- [ ] The system architecture conforms to the guardrails defined in [erp-domain-model-guidelines.md](erp-domain-model-guidelines.md).
- [ ] Product owner has approved the implementation design.

---

## Related Core Files

- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — Core quality assurance checklists.
- [04-user-roles.md](../../../core/docs/04-user-roles.md) — Roles and permission matrices.
- [07-data-model.md](../../../core/docs/07-data-model.md) — Database design specs.
- [README.md](../README.md) — ERP Extension Pack README.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial QA checklist for ERP Operations Pack | Antigravity |
