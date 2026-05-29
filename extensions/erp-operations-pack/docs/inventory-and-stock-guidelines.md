# Inventory and Stock Guidelines

> Defines rules for inventory calculations, stock movements, reservations, adjustments, and reconciliation logic.

---

## Purpose

This document establishes standards for managing stock levels, recording stock movements, handling reservations, and executing adjustments. It prevents stock leakage, double-allocations, and historical drift. It supplements the core data model specifications in [07-data-model.md](../../../core/docs/07-data-model.md) and relates to [erp-domain-model-guidelines.md](erp-domain-model-guidelines.md).

## Status

`Active` — Must be referenced during inventory database structure and calculation logic implementation.

---

## Inventory Principles

### 1. Stock Movements as Source of Truth
Inventory levels must never be modified by direct, silent edits to a product's "quantity" column. Every change in stock must be represented by a **stock movement record** (ledger entry).
- The current on-hand quantity is the sum of all historic stock movements.
- Each movement must capture:
  - `product_id`: The item being moved.
  - `location_id` / `bin_id`: The source and destination physical locations.
  - `quantity`: Positive for addition, negative for subtraction.
  - `movement_type`: Enum (e.g., `receiving`, `shipping`, `adjustment`, `transfer`, `production_consume`, `production_yield`).
  - `reference_id`: Link to the initiating document (e.g., `purchase_order_id`, `shipment_id`, `stock_adjustment_id`).
  - `actor_id` and `timestamp`: Who performed the movement and when.

---

## Stock States: On-Hand vs. Available vs. Reserved vs. Allocated

To prevent double-selling and operational delays, these terms must be explicitly distinguished and calculated:

| Stock State | Formula / Meaning | Usage |
|---|---|---|
| **On-Hand** | Total physical quantity currently located inside the warehouse. | Physical audit, valuation. |
| **Reserved** | Quantity committed to open orders but not yet prepared for shipping. | Sales order placement. |
| **Allocated** | Quantity picked and packed, specifically assigned to a shipment. | Dispatch staging. |
| **Available** | `On-Hand` - `Reserved` - `Allocated` | Sales availability limit. |

- **Rule**: These values must not be collapsed into a single database field. They must be tracked separately through reservations and allocations tables, or calculated dynamically from movement and order status tables.

---

## Stock Adjustment Rules

Stock adjustments (e.g., after finding damaged goods or performing a cycle count) are high-risk operations and must follow these rules:
- **No Direct Column Editing**: You cannot overwrite on-hand quantities.
- **Adjustment Records**: Create an explicit `stock_adjustment` entry capturing:
  - Product, location, and bin.
  - Direction and quantity.
  - Reason (e.g., `damaged`, `theft`, `cycle_count_discrepancy`).
  - Actor, timestamp, and human approval signature.
  - **Before and After** snapshots of the on-hand quantity at the time of adjustment.
- **Approval Limits**: Adjustments exceeding a specific value threshold must require owner/manager approval before applying the movement.

---

## Reservation and Allocation Rules

- **Reservation**:
  - Occurs when a Sales Order is placed (`Confirmed` state).
  - Deducts from `Available` stock but does not change physical `On-Hand` stock.
  - Must have an expiration timestamp (e.g., release reservation if order is unpaid after 24 hours).
- **Allocation**:
  - Occurs when a Pick List is generated.
  - Converts a `Reserved` status to `Allocated`, locking the stock to a specific shipment.
- **De-allocation/Cancellation**:
  - If an order is cancelled, reservations and allocations must be explicitly reversed.

---

## Negative Stock Policy

Allowing negative stock (selling items you do not physically have on-hand) is highly risky and must follow a strict policy:
- **Default Policy**: **Blocked**. The system must reject stock movements that would cause available/on-hand quantity at a location to fall below zero.
- **Exceptions**: Any configuration allowing negative stock must be explicitly approved by the product owner in the product design document and implemented as a configurable toggle at the warehouse/product level, never as a hardcoded bypass.

---

## Lot, Batch, and Serial Tracking

For products requiring traceability:
- Every stock movement must reference a `lot_number`, `batch_number`, or `serial_number`.
- Serial numbers must have a unique constraint per product to prevent duplicate serialization in stock.

---

## Unit and Quantity Handling

- **Base Units**: Store all quantities in the base unit of measure (e.g., `pcs`, `grams`, `ml`) as integers or high-precision decimals. Avoid fractional floats.
- **Alignment**: Quantity and unit handling must align with the rounding and calculation rules in [financial-business-logic-pack](../financial-business-logic-pack/README.md) if quantities are multiplied by prices or used in financial reporting.

---

## Soft Delete and Cancellation Rules

- **Stock Records**: Warehouse inventory records and stock movement logs must never be soft deleted or hard deleted.
- **Cancellation**: If a stock-affecting document (e.g., a shipment) is cancelled, a compensating stock movement record must be written to restore the inventory balance.

---

## Reconciliation Rules

- **Audit Trigger**: Daily automated checks must verify that:
  - Current product `cached_qty_on_hand` matches `sum(stock_movements.quantity)`.
  - `Available` stock calculation is consistent across active order tables.
- Discrepancies must trigger an operational alert and block automated inventory syncs to external sales channels.

---

## Out of Scope

- Physical picking path optimization algorithms.
- Procurement price negotiations.
- Tax depreciation of inventory assets.

---

## Guardrails

- [ ] **LEDGER ONLY**: Every change in inventory must write a row to the stock movement table.
- [ ] **NO DUPLICATE RESERVATIONS**: Reservation logic must use transaction locks (e.g., `SELECT ... FOR UPDATE`) to prevent over-reserving.
- [ ] **EXPLICIT REVERSALS**: Document cancellations must write counter stock movements.
- [ ] **NO SILENT CORRECTIONS**: Adjustments must capture actor, timestamp, reason, and before/after levels.

---

## QA Checklist

- [ ] Verify that saving a stock adjustment writes a record to the movement history table.
- [ ] Verify that order cancellations restore reserved/allocated quantities to available stock.
- [ ] Test transaction safety: place concurrent orders for the last available item and confirm only one succeeds.
- [ ] Test negative stock block: attempt to ship more than is on-hand and verify database constraint blocks it.

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Core database design guidelines.
- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — Standard test plans.
- [erp-domain-model-guidelines.md](erp-domain-model-guidelines.md) — Lifecycle states and correction rules.
- [README.md](../README.md) — ERP Extension Pack README.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial inventory and stock guidelines for ERP Operations Pack | Antigravity |
