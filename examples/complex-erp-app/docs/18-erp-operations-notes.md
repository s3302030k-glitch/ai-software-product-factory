# 18 — ERP Operations Notes

> How the ERP Operations Pack applies to the Integrated Operations ERP documentation reference.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> See the [example README](../README.md) for full context.
> Extension pack reference: [erp-operations-pack README](../../../extensions/erp-operations-pack/README.md)

---

## Pack Application Summary

The [ERP Operations Pack](../../../extensions/erp-operations-pack/README.md) is the primary extension pack for this documentation reference. It governs ERP domain modeling, warehouse operations, inventory rules, workflow design, audit trail requirements, and operational reporting patterns. All major ERP design decisions in this example derive from this pack's guidelines.

---

## Master Data

The ERP Operations Pack's domain model guidelines apply to:
- **SupplierPlaceholder** and **CustomerPlaceholder** records — managed as master data with lifecycle states (Active / Archived). See [07-data-model.md](07-data-model.md).
- **Product** and **SKU** records — catalog master data with unique SKU codes.
- **Warehouse** and **WarehouseZone** records — facility layout with zone types (receiving, storage, dispatch).
- **Department** and **User** records — organizational structure with operational scope assignments.

**Pack Rule Applied:** Master data records use soft delete (archived) rather than hard delete. Archived records are hidden from active lists but retained for historical reference and audit trail integrity.

---

## Warehouse / Zones

The Warehouse Operations Guidelines from the ERP Operations Pack apply to:
- **Zone type separation** — receiving zones accept incoming goods; storage zones hold stock; dispatch zones stage outgoing goods. Stock movement routing is constrained by zone type.
- **Warehouse scope enforcement** — users are assigned to specific warehouses; cross-warehouse access is blocked at the API level.
- **Physical-logical separation** — the warehouse zone layout (logical) is documented separately from physical dimensions (not documented in this reference — out of scope).

**Pack Rule Applied:** Zone code uniqueness is enforced within each warehouse. Zone type determines valid movement routing (receiving zone can only be a destination for receiving_in movements; dispatch zone can only be a source for dispatch_out movements).

---

## Inventory and Stock Movements

The Inventory and Stock Guidelines from the ERP Operations Pack directly govern:
- **StockBalance derivation** — always computed from StockMovement records. Never directly edited. This is the single most important inventory rule in this system.
- **StockMovement immutability** — movement records are append-only. No edit or delete endpoint. Corrections create new movement records.
- **Movement types** — `receiving_in`, `transfer_out`, `transfer_in`, `adjustment_in`, `adjustment_out`, `dispatch_out`. Each type has clear directionality rules.
- **Insufficient stock handling** — outgoing movements that would cause a negative balance trigger a warning; the documentation reference notes this as a condition for operator resolution.

**Source-of-Truth Rule (from ERP Operations Pack):**
> StockBalance = SUM of all StockMovement.quantity WHERE movement increases the zone balance MINUS SUM of all decreasing movements for that SKU/zone pair.

This rule is locked in [14-decision-log.md](14-decision-log.md) Decision 4 and cannot be changed without owner approval.

---

## Receiving

The Warehouse Operations Guidelines apply to receiving:
- **ReceivingRecord** links to a PurchaseOrderPlaceholder and a warehouse receiving zone.
- Receiving creates `receiving_in` StockMovement records — one per SKU line.
- Over-receipt (quantity received > PO quantity) is flagged as a discrepancy for operator review.
- Partial receipt sets PO status to `Partially Received`; full receipt sets to `Fully Received`.
- Receiving zone must be of type `receiving` — enforced at API level.

---

## Dispatch Placeholders

The Warehouse Operations Guidelines apply to dispatch:
- **DispatchPlaceholder** links to a SalesOrderPlaceholder and a warehouse dispatch zone.
- Dispatch creates `dispatch_out` StockMovement records — one per SKU line.
- Dispatch zone must be of type `dispatch` — enforced at API level.
- No real logistics carrier or shipping API integration — dispatch is a placeholder workflow only.

---

## Stock Adjustments

The Inventory Guidelines apply to adjustments:
- **StockAdjustment** requires a reason field (mandatory) and formal approval before any movement is created.
- Large negative adjustments (causing near-zero or negative balances) trigger a warning to the approver.
- Approved adjustment creates a `adjustment_in` or `adjustment_out` StockMovement — maintaining the immutable movement log.
- Rejected adjustment has no effect on stock balance.

---

## Approvals

The Workflow and Approval Guidelines from the ERP Operations Pack apply to:
- **ApprovalRequest** records for purchase requests and stock adjustments.
- Self-approval block — enforced at API level, not just UI.
- Authorization limits — approvers have defined maximum approvable values; requests above the limit escalate.
- Escalation path — Operations Director handles all escalated requests.
- All approval decisions are recorded as immutable AuditEvents.

**Pack Rule Applied:** The four-eyes principle requires that the submitter and approver are different users. This is a locked design decision (Decision 5 in decision log).

---

## Operational Reporting

The Operational Reporting Guidelines from the ERP Operations Pack apply to:
- Report data is sourced from the authoritative data layer, not from UI display state.
- Exported CSV/PDF values must match what is displayed in the UI — no client-side recalculation.
- Report generation events are logged as AuditEvents.
- Report filters (date range, warehouse, department, SKU) are applied consistently to all data sources.

---

## Audit Trail

The Operational Audit Trail Guidelines from the ERP Operations Pack apply to:
- **AuditEvent** records are immutable (no edit, no delete).
- Every state change (creation, approval, rejection, status change, archive) produces an AuditEvent.
- AuditEvents include: entity_type, entity_id, action, actor_user_id, before_snapshot, after_snapshot, occurred_at.
- Sensitive data (credentials, tokens) must be redacted from before/after snapshots.
- Audit log accessible only to Read-only Auditor and Operations Director.

---

## Owner Decisions Derived from ERP Operations Pack

| Decision | Status |
|----------|--------|
| StockBalance derived from StockMovements (not directly edited) | ✅ Locked — Decision 4 |
| StockMovement records are immutable | ✅ Locked — Decision 4 |
| Approval authority separated from data-entry authority | ✅ Locked — Decision 5 |
| Self-approval blocked at API level | ✅ Locked — Decision 5 |
| Zone type constrains movement routing | ✅ Documented |
| Warehouse scope enforced at API level | ✅ Documented |
| No production WMS automation in MVP | ✅ Locked — Decision 7 |

---

## Related Files

- [07-data-model.md](07-data-model.md) — Entity definitions applying ERP Operations Pack rules
- [10-security-model.md](10-security-model.md) — Warehouse/department scope enforcement
- [14-decision-log.md](14-decision-log.md) — Locked ERP design decisions
- [../../../extensions/erp-operations-pack/README.md](../../../extensions/erp-operations-pack/README.md) — Source pack
