# 07 — Data Model

> Logical entity definitions for the Integrated Operations ERP.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This is not a runnable application. No real database schema or migration is included.
> See the [example README](../README.md) for full context.

> [!WARNING]
> This document defines a **logical data model only**. No SQL schema, no migration files, no ORM models, and no real database connections are part of this documentation reference.

---

## Source-of-Truth Rules

- **Stock Quantity Source-of-Truth:** The `StockBalance` per SKU per zone is always **derived** from the sum of all `StockMovement` records for that SKU/zone combination. It is never directly edited. Any correction must be made via a new `StockAdjustment` record → approved → creates a new `StockMovement`.
- **Financial Amounts:** Invoice and Payment amounts are display placeholders only. They are not authoritative accounting entries.
- **Approval State:** The `ApprovalRequest` record is the authoritative source of approval status. The parent entity (e.g., PurchaseRequest) mirrors this status for display convenience only.
- **Audit Events:** `AuditEvent` records are immutable and authoritative. No business logic relies on UI state or conversation history.

---

## Derived vs Stored Fields

| Field | Entity | Stored or Derived |
|-------|--------|-------------------|
| `current_balance` | StockBalance | Derived (sum of StockMovements) |
| `approval_status` (on PurchaseRequest) | PurchaseRequest | Mirrored from ApprovalRequest |
| `total_amount` (on InvoicePlaceholder) | InvoicePlaceholder | Stored as placeholder display value |
| `remaining_balance` (on InvoicePlaceholder) | InvoicePlaceholder | Derived from PaymentPlaceholder records |

---

## Soft Delete / Archive Policy

- Supplier, Customer, Product, SKU, Warehouse, and WarehouseZone records use an `is_archived` flag and a `status` field.
- Archived records are hidden from active lists but retained for historical reference.
- Stock movements, receiving records, audit events, and approval records are **never** soft-deleted or archived.

---

## Entity Definitions

---

### User

**Purpose:** Represents an authenticated user of the platform.

**Key Fields:**
- `id` — unique system identifier
- `display_name` — fictional placeholder name (e.g., "Alex Operations")
- `email_placeholder` — placeholder email format (e.g., user@example-erp.internal)
- `status` — Active / Inactive
- `created_at`, `updated_at`

**Relationships:**
- Has many `Role` assignments
- Has many `Department` assignments
- Has many `Warehouse` assignments
- Has many `AuditEvent` records as actor

**Lifecycle:** Created by Platform Admin. Deactivated (not deleted) when a user leaves.

**Audit Requirements:** User created, user deactivated, role assigned, role revoked.

---

### Department

**Purpose:** Represents an organizational department (e.g., Procurement, Warehouse, Sales, Finance).

**Key Fields:**
- `id`
- `name` — e.g., "Procurement Department", "Warehouse Operations"
- `department_head_user_id` — FK to User
- `status` — Active / Inactive
- `created_at`

**Relationships:**
- Has many Users assigned
- Has many PurchaseRequests scoped to department
- Has one department head

**Lifecycle:** Configured by Platform Admin. Deactivated when department is dissolved.

**Operational Scoping:** Users with department scope only see records within their assigned departments.

---

### Role

**Purpose:** Defines a named permission set.

**Key Fields:**
- `id`
- `name` — e.g., "WarehouseManager", "InventoryClerk", "FinanceOfficer"
- `description`

**Relationships:**
- Many-to-many with User via UserRole junction

**Lifecycle:** Defined at system setup. Not user-editable.

**Audit Requirements:** Role assigned to user, role revoked from user.

---

### Permission

**Purpose:** Defines a specific allowed action within a role.

**Key Fields:**
- `id`
- `role_id` — FK to Role
- `action` — e.g., "stock_movement.create", "purchase_request.approve"
- `scope` — "own_warehouse", "own_department", "all"

**Relationships:**
- Belongs to Role

**Lifecycle:** Defined at system setup. Not user-editable at runtime.

---

### SupplierPlaceholder

**Purpose:** Master data record for a fictional supplier entity.

**Key Fields:**
- `id`
- `name` — e.g., "Alpha Goods Supplier Co."
- `contact_placeholder` — e.g., "contact@alpha-supplier.example"
- `status` — Active / Archived
- `created_by_user_id`
- `created_at`, `updated_at`

**Relationships:**
- Referenced by PurchaseOrderPlaceholder

**Lifecycle:** Created by Purchasing Manager. Archived when no longer active.

**Operational Scoping:** Visible to Purchasing Manager and Operations Director.

**Audit Requirements:** `supplier_created`, `supplier_archived`

> [!WARNING]
> No real supplier names, addresses, tax IDs, bank details, or contact data are included in this documentation reference.

---

### CustomerPlaceholder

**Purpose:** Master data record for a fictional customer entity.

**Key Fields:**
- `id`
- `name` — e.g., "Beta Retail Customer Ltd."
- `contact_placeholder` — e.g., "contact@beta-customer.example"
- `status` — Active / Archived
- `created_by_user_id`
- `created_at`, `updated_at`

**Relationships:**
- Referenced by SalesOrderPlaceholder

**Lifecycle:** Created by Sales Coordinator. Archived when no longer active.

**Audit Requirements:** `customer_created`, `customer_archived`

> [!WARNING]
> No real customer names, addresses, tax IDs, bank details, or contact data are included.

---

### Product

**Purpose:** A product definition in the catalog.

**Key Fields:**
- `id`
- `name` — e.g., "Standard Widget Type A"
- `description`
- `unit_of_measure` — e.g., "unit", "kg", "box"
- `status` — Active / Archived
- `created_by_user_id`
- `created_at`, `updated_at`

**Relationships:**
- Has many SKU records

**Lifecycle:** Created by Purchasing Manager or Admin. Archived when discontinued.

**Audit Requirements:** `product_created`, `product_archived`

---

### SKU

**Purpose:** A stockkeeping unit linked to a product.

**Key Fields:**
- `id`
- `product_id` — FK to Product
- `sku_code` — unique identifier (e.g., "WGT-A-001")
- `unit` — unit of measure for this SKU
- `status` — Active / Archived
- `created_at`, `updated_at`

**Relationships:**
- Belongs to Product
- Referenced by StockBalance, StockMovement, PurchaseRequest lines, PO lines, Sales Order lines

**Lifecycle:** Created with product. Archived when discontinued.

**Audit Requirements:** `sku_created`, `sku_archived`

---

### Warehouse

**Purpose:** Represents a physical warehouse facility.

**Key Fields:**
- `id`
- `name` — e.g., "North Distribution Centre"
- `location_placeholder` — e.g., "Zone A, Industrial Area"
- `status` — Active / Inactive
- `created_at`, `updated_at`

**Relationships:**
- Has many WarehouseZone records
- Has many Users assigned

**Lifecycle:** Configured by Admin. Deactivated (not deleted) when closed.

**Audit Requirements:** `warehouse_created`, `warehouse_deactivated`

> [!WARNING]
> No real warehouse addresses, GPS coordinates, or geographic data are included.

---

### WarehouseZone

**Purpose:** A named zone within a warehouse with a defined function.

**Key Fields:**
- `id`
- `warehouse_id` — FK to Warehouse
- `zone_code` — unique within warehouse (e.g., "RECV-01", "STOR-A", "DISP-01")
- `zone_type` — receiving / storage / dispatch
- `status` — Active / Inactive
- `created_at`

**Relationships:**
- Belongs to Warehouse
- Referenced by StockBalance, StockMovement, ReceivingRecord, DispatchPlaceholder

**Lifecycle:** Created with warehouse zones setup. Deactivated when retired.

---

### StockBalance

**Purpose:** Represents the current on-hand quantity of a SKU in a specific warehouse zone.

**Key Fields:**
- `id`
- `sku_id` — FK to SKU
- `warehouse_zone_id` — FK to WarehouseZone
- `current_balance` — **derived** from StockMovement records; never directly edited
- `last_updated_at`

**Relationships:**
- Belongs to SKU and WarehouseZone
- Derived from StockMovement records

**Source-of-Truth Rule:** `current_balance` is always recomputed from StockMovement records. Any attempt to directly edit this field is prohibited. Corrections must flow through StockAdjustment → approval → StockMovement.

**Lifecycle:** Record created on first stock movement for a SKU/zone pair.

---

### StockMovement

**Purpose:** An immutable record of any quantity change for a SKU in a zone.

**Key Fields:**
- `id`
- `movement_type` — receiving_in / transfer_out / transfer_in / adjustment_in / adjustment_out / dispatch_out
- `sku_id` — FK to SKU
- `source_zone_id` — FK to WarehouseZone (nullable for receiving_in)
- `destination_zone_id` — FK to WarehouseZone (nullable for dispatch_out)
- `quantity` — positive integer; direction implied by movement_type
- `reference_id` — optional FK to ReceivingRecord, StockAdjustment, or DispatchPlaceholder
- `actor_user_id` — who performed the action
- `notes`
- `created_at`

**Relationships:**
- Belongs to SKU
- References source and destination zones
- May reference ReceivingRecord, StockAdjustment, or DispatchPlaceholder

**Lifecycle:** Created once and **immutable**. Never edited or deleted. Corrections create new movement records of opposite type.

**Audit Requirements:** `stock_movement_created` with full before/after snapshot of affected balances.

---

### ReceivingRecord

**Purpose:** Documents the receipt of goods from a supplier into a warehouse zone.

**Key Fields:**
- `id`
- `po_placeholder_id` — FK to PurchaseOrderPlaceholder
- `warehouse_zone_id` — FK to WarehouseZone (must be receiving type)
- `received_by_user_id` — FK to User
- `received_at`
- `status` — Complete / Partial
- Line items: SKU, quantity_received

**Relationships:**
- Belongs to PurchaseOrderPlaceholder
- Creates StockMovement records on completion

**Lifecycle:** Created by Inventory Clerk or Warehouse Manager. Completed when all items are received.

**Audit Requirements:** `goods_received`, `stock_movement_created`

---

### StockAdjustment

**Purpose:** A requested and approved correction to stock quantity.

**Key Fields:**
- `id`
- `sku_id` — FK to SKU
- `warehouse_zone_id` — FK to WarehouseZone
- `quantity_delta` — positive or negative integer
- `reason` — required, free text
- `submitted_by_user_id`
- `status` — Pending / Approved / Rejected
- `approved_by_user_id` — nullable until decided
- `approved_at`
- `rejection_reason` — nullable
- `created_at`

**Relationships:**
- Belongs to SKU and WarehouseZone
- Creates StockMovement on approval
- Has one ApprovalRequest

**Lifecycle:** Submitted → Pending → Approved or Rejected.

**Self-Approval Rule:** `submitted_by_user_id` must not equal `approved_by_user_id` — enforced at API level.

---

### PurchaseRequest

**Purpose:** A formal request to procure goods, subject to approval.

**Key Fields:**
- `id`
- `requester_user_id` — FK to User
- `department_id` — FK to Department
- `status` — Draft / Pending Approval / Approved / Rejected
- `notes`
- `created_at`, `updated_at`
- Line items: SKU, quantity_requested

**Relationships:**
- Belongs to Department
- Has one ApprovalRequest
- May link to PurchaseOrderPlaceholder

**Lifecycle:** Draft → Submitted (Pending Approval) → Approved or Rejected.

---

### PurchaseOrderPlaceholder

**Purpose:** A placeholder record representing a purchase order sent to a supplier.

**Key Fields:**
- `id`
- `po_number_placeholder` — display reference (e.g., "PO-2026-0042")
- `supplier_placeholder_id` — FK to SupplierPlaceholder
- `purchase_request_id` — optional FK
- `status` — Draft / Submitted / Partially Received / Fully Received / Closed
- `expected_delivery_date_placeholder`
- Line items: SKU, quantity, unit_price_placeholder
- `created_by_user_id`
- `created_at`, `updated_at`

**Relationships:**
- Belongs to SupplierPlaceholder
- May reference PurchaseRequest
- Has many ReceivingRecords
- May have InvoicePlaceholders

**Audit Requirements:** `po_created`, `po_status_changed`

> [!WARNING]
> `unit_price_placeholder` is a display value only. No real pricing, tax, or accounting logic is applied.

---

### SalesOrderPlaceholder

**Purpose:** A placeholder record representing a sales order for a customer.

**Key Fields:**
- `id`
- `order_number_placeholder` — display reference (e.g., "SO-2026-0017")
- `customer_placeholder_id` — FK to CustomerPlaceholder
- `status` — Open / In Progress / Dispatched / Closed
- `expected_dispatch_date_placeholder`
- Line items: SKU, quantity, unit_price_placeholder
- `created_by_user_id`
- `created_at`, `updated_at`

**Relationships:**
- Belongs to CustomerPlaceholder
- Has many DispatchPlaceholders
- May have InvoicePlaceholders

---

### DispatchPlaceholder

**Purpose:** A placeholder record representing a shipment or dispatch event.

**Key Fields:**
- `id`
- `sales_order_placeholder_id` — FK to SalesOrderPlaceholder
- `warehouse_zone_id` — FK to WarehouseZone (must be dispatch type)
- `status` — Pending / Prepared / Dispatched (placeholder)
- `dispatched_by_user_id`
- `created_at`
- Line items: SKU, quantity_dispatched

**Relationships:**
- Belongs to SalesOrderPlaceholder
- Creates StockMovement records on preparation

---

### InvoicePlaceholder

**Purpose:** A placeholder financial document linked to a purchase or sales order.

**Key Fields:**
- `id`
- `invoice_number_placeholder` — display reference (e.g., "INV-2026-0033")
- `invoice_type` — purchase / sales
- `related_po_id` — optional FK to PurchaseOrderPlaceholder
- `related_so_id` — optional FK to SalesOrderPlaceholder
- `amount_placeholder` — decimal display value
- `currency_display` — e.g., "USD" (display only)
- `due_date_placeholder`
- `status` — Draft / Issued / Partially Paid / Paid / Overdue (placeholders)
- `created_by_user_id`
- `created_at`, `updated_at`

**Relationships:**
- May belong to PurchaseOrderPlaceholder or SalesOrderPlaceholder
- Has many PaymentPlaceholders

> [!WARNING]
> Invoice records are documentation placeholders only. No real accounting, tax, VAT, or payment processing is implied.

---

### PaymentPlaceholder

**Purpose:** A placeholder record representing a payment recorded against an invoice.

**Key Fields:**
- `id`
- `invoice_placeholder_id` — FK to InvoicePlaceholder
- `payment_reference_placeholder` — display reference
- `amount_placeholder` — decimal display value
- `payment_method_placeholder` — e.g., "Bank Transfer (placeholder)"
- `payment_date_placeholder`
- `status` — Recorded
- `recorded_by_user_id`
- `created_at`

**Relationships:**
- Belongs to InvoicePlaceholder

> [!WARNING]
> Payment records are documentation placeholders only. No real bank account, IBAN, SWIFT, or payment provider data is included.

---

### ApprovalRequest

**Purpose:** A formal approval record linking a pending request to an approver.

**Key Fields:**
- `id`
- `request_type` — purchase_request / stock_adjustment
- `related_entity_id` — FK to the relevant entity
- `requester_user_id`
- `approver_user_id` — assigned approver
- `status` — Pending / Approved / Rejected / Escalated
- `decision_reason` — nullable
- `decided_at`
- `created_at`

**Relationships:**
- Belongs to PurchaseRequest or StockAdjustment
- Has one AuditEvent per decision

**Self-Approval Rule:** `requester_user_id` must not equal `approver_user_id`.

---

### ReportDefinition

**Purpose:** Defines a named report type with its data source and filter parameters.

**Key Fields:**
- `id`
- `name` — e.g., "Stock Movement Report", "PO Summary"
- `data_source` — e.g., "stock_movements", "purchase_orders"
- `available_filters` — list of supported filter keys
- `export_formats` — ["csv", "pdf_placeholder"]

**Relationships:** Referenced by Reports page

**Lifecycle:** Defined at system setup. Not user-editable.

---

### AuditEvent

**Purpose:** An immutable record of every state change in the system.

**Key Fields:**
- `id`
- `entity_type` — e.g., "StockMovement", "PurchaseRequest", "User"
- `entity_id` — FK to the relevant entity
- `action` — e.g., "created", "approved", "rejected", "archived"
- `actor_user_id` — FK to User who triggered the event
- `before_snapshot` — JSON representation of entity state before change (redacted for sensitivity)
- `after_snapshot` — JSON representation after change
- `occurred_at` — timestamp

**Relationships:**
- References any entity

**Lifecycle:** Created once and **immutable**. No edit or delete allowed.

**Audit Requirements:** All state changes must produce an AuditEvent. AuditEvents may not be deleted or modified.

---

## Entity Relationship Summary

```
User ─── UserRole ─── Role ─── Permission
  │
  ├── Department
  │
  ├── SupplierPlaceholder ─── PurchaseOrderPlaceholder
  │                                  │
  ├── CustomerPlaceholder ─── SalesOrderPlaceholder
  │                                  │
  ├── Product ─── SKU ─── StockBalance (derived)
  │                   │
  │                   └── StockMovement (immutable)
  │                            │
  │               ReceivingRecord ─── PurchaseOrderPlaceholder
  │               StockAdjustment ─── ApprovalRequest
  │               DispatchPlaceholder ─── SalesOrderPlaceholder
  │
  ├── InvoicePlaceholder ─── PaymentPlaceholder
  │
  └── AuditEvent (all entities)
```

---

## Related Files

- [09-api-design.md](09-api-design.md) — API groups per entity
- [10-security-model.md](10-security-model.md) — RLS and operational scoping per entity
- [18-erp-operations-notes.md](18-erp-operations-notes.md) — ERP Operations Pack application notes
- [19-financial-business-logic-notes.md](19-financial-business-logic-notes.md) — Financial placeholder notes
