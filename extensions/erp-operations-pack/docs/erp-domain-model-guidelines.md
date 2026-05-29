# ERP Domain Model Guidelines

> Establishes the core principles for designing and structuring the database entities, relationships, states, and business logic boundaries for ERP-like software products.

---

## Purpose

This document provides guidelines for domain modeling in products with ERP-like features. It ensures data integrity, strict auditability, and clear separation of concerns. It supplements the core data model specifications in [07-data-model.md](../../../core/docs/07-data-model.md) and security rules in [10-security-model.md](../../../core/docs/10-security-model.md).

## Status

`Active` — Must be followed by domain architects and engineers during schema design and review.

---

## When to Use

Use these guidelines when modeling entities that represent:
- Multi-step business processes (e.g., Procurement, Sales, Order Fulfillment).
- Physical items and locations (e.g., Inventory, Warehouses, Equipment).
- Workflows requiring state transitions or multi-role approvals.
- Records that require historical traceability and cannot be silently edited or deleted.

---

## ERP Domain Modeling Principles

### 1. Separation of Planning vs. Execution

A fundamental rule of ERP design is the strict separation between what is planned (expected) and what is executed (actual). Mixing these into a single record leads to data loss, concurrency issues, and reconciliation failures.

- **Planning Records** (e.g., `purchase_orders`, `sales_orders`, `production_plans`):
  - Represent intent, authorization, or commitments.
  - Contain planned quantities, dates, and expected costs.
  - Can be edited or cancelled before execution begins.
- **Execution Records** (e.g., `receiving_logs`, `shipment_records`, `production_logs`):
  - Represent actual physical occurrences.
  - Capture real-world quantities, exact timestamps, and specific actors.
  - Once created, they are **immutable** (or adjusted only via offsetting entries).

### 2. Entity Separation Principles

Do not combine different logical concepts into a single table. Keep boundaries clean:
- **Order/Procurement**: Represents the commercial agreement and quantity commitment.
- **Inventory**: Represents the logical and physical quantities currently on-hand.
- **Warehouse**: Represents physical positioning, locations, bins, and routing.
- **Shipment/Logistics**: Represents transport, dispatch, and delivery.
- **Production/Manufacturing**: Represents conversion of materials, scheduling, and labor.

---

## Operational Lifecycle States

Every operationally meaningful entity must have an explicit lifecycle state (typically a `status` enum field). Do not infer states from dates or flags.

### Standard Lifecycle States

```
                 ┌───────────┐
                 │   Draft   │
                 └─────┬─────┘
                       │ Confirm / Approve
                       ▼
                 ┌───────────┐
                 │ Confirmed │
                 └─────┬─────┘
            ┌──────────┴──────────┐
            │ Start               │ Cancel
            ▼                     ▼
     ┌─────────────┐       ┌─────────────┐
     │ In Progress │       │  Cancelled  │
     └──────┬──────┘       └─────────────┘
            │ Complete
            ▼
     ┌─────────────┐
     │  Completed  │
     └──────┬──────┘
            │ Void / Reverse
            ▼
     ┌─────────────┐
     │   Voided    │
     └─────────────┘
```

1. **Draft**: The record is being prepared. It is not visible to downstream execution. Can be deleted or modified.
2. **Confirmed / Approved**: The record is committed. It is visible to downstream systems (e.g., a confirmed Purchase Order is visible to the Warehouse Receiving team). Modifications are restricted.
3. **In Progress**: Execution has started but is not finished (e.g., partial receipt of goods).
4. **Completed**: Execution is finished. The record is permanently locked against changes.
5. **Cancelled**: Stopped before execution started. The record is preserved for history.
6. **Voided**: Invalidated after completion. Requires offsetting adjustments to inventory or financial ledgers.

---

## Adjustment and Correction Patterns

Once an execution record is committed or completed, it **must not** be edited directly or deleted. Errors must be corrected using explicit adjustment patterns.

- **Traceable Corrections**:
  - To change a quantity or value, write an adjustment record containing the difference, referencing the original record.
  - Save the actor, timestamp, reason, and before/after values.
- **Reversal (Voiding)**:
  - If a transaction must be undone, create a reversing entry that negates the original values.
  - Transition the original record's status to `Voided` or `Reversed` and link the reversing entry.

---

## Derived Operational Values vs. Stored Values

To prevent drift and performance bottlenecks, clearly distinguish between:
- **Stored Values (Source of Truth)**: Individual transactions, stock movements, and ledger entries.
- **Derived Values (Aggregates)**: Total on-hand quantity, available balance, or total order cost.
- **Rule**: Derived values must be recalculable at any time from the stored source-of-truth records. If cached in the database for speed (e.g., a `cached_qty_on_hand` on a product table), they must be backed by a synchronization check and updated only via database triggers or transaction hooks.

---

## Business Rule Ownership

All business logic must live in the backend database layer (triggers, constraints) or core server-side services.
- **DO NOT hide operational rules inside UI components.** The frontend must only display options based on state; it must never be the sole enforcer of rules.
- **Four-Eyes Principle**: Actions exceeding specific thresholds (e.g., adjusting stock values, approving orders) must require a separate, authorized user to sign off.

---

## Out of Scope

- Legal or regulatory reporting compliance details.
- Accounting ledger configurations (covered by [financial-business-logic-pack](../../financial-business-logic-pack/README.md)).
- Database execution plan optimizations.

---

## Guardrails

- [ ] **NO SILENT EDITS**: Direct SQL or UI updates to committed or completed execution records are prohibited.
- [ ] **EXPLICIT Lifecycle**: No entity may use dates or flags to represent its lifecycle state; a status field must be explicitly declared.
- [ ] **RULE LOCATION**: Business logic must not reside in UI views.
- [ ] **HUMAN APPROVAL REQUIRED**: Any state change altering business meaning (e.g., cancelling an in-transit shipment) requires owner/admin authorization.

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Core database schema layout rules.
- [04-user-roles.md](../../../core/docs/04-user-roles.md) — Authorization boundaries and access scope.
- [10-security-model.md](../../../core/docs/10-security-model.md) — Security boundaries and data safety.
- [README.md](../README.md) — ERP Extension Pack README.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial domain model guidelines for ERP Operations Pack | Antigravity |
