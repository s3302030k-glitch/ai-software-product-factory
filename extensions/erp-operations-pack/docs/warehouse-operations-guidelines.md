# Warehouse Operations Guidelines

> Defines rules for warehouse physical structure modeling, location tracing, receiving, shipping, picking, packing, and inventory transfer operations.

---

## Purpose

This document provides instructions on how to structure warehouse entities and model warehouse operations to ensure physical location traceability, separate operational steps, and handle exceptions. It supplements the core data model in [07-data-model.md](../../../core/docs/07-data-model.md) and relates to [inventory-and-stock-guidelines.md](inventory-and-stock-guidelines.md).

## Status

`Active` — Must be referenced during the design of warehouse structure, receiving, shipping, and routing components.

---

## Warehouse Operation Principles

### 1. Location Traceability
Every physical stock movement within or between facilities must be recorded. Items do not move "silently." Any change in location requires a stock transfer transaction.

### 2. Multi-Step Separation
Physical locations, logical stock status, and document status must not be collapsed into a single database field.
- **Physical Location**: Where the item is (e.g., `Zone A, Bin 12`).
- **Logical Stock Status**: The status of the items (e.g., `Available`, `QC Blocked`, `Damaged`).
- **Document Status**: The status of the transaction (e.g., `Receiving`, `Putaway In-Progress`, `Shipped`).

---

## Warehouse modeling: Sites, Zones, and Bins

To accurately model the physical footprint, use a hierarchical location structure:

```
┌────────────────────────────────────────────────────────┐
│ Site / Facility (e.g., Warehouse A)                    │
│   ┌──────────────────────────────────────────────────┐ │
│   │ Zone (e.g., Cold Storage, Receiving Area)        │ │
│   │   ┌────────────────────────────────────────────┐ │ │
│   │   │ Bin / Location (e.g., Row 4, Shelf 2, Bin A)│ │ │
│   │   └────────────────────────────────────────────┘ │ │
│   └──────────────────────────────────────────────────┘ │
└────────────────────────────────────────────────────────┘
```

- **Site/Facility**: Represents a physical building, address, or campus.
- **Zone**: Represents a logical subdivision within a site based on storage conditions (e.g., dry, cold, hazardous) or operational roles (e.g., receiving area, dispatch staging).
- **Bin/Location**: The lowest level of addressable storage. All physical stock ledger entries must reference a specific Bin ID.

---

## Core Warehouse Processes

Unless explicitly simplified by product owner decision, model these steps as distinct database documents and status flows:

### 1. Receiving Process
- **Document**: `receiving_note` or `goods_receipt`.
- **Flow**: Starts as `Draft`, transitions to `In Progress` as items are scanned, and `Completed` when matching against the Purchase Order.
- **Action**: Items are initially placed in a designated `Receiving Zone` location, not directly into storage bins.

### 2. Putaway Process
- **Document**: `putaway_task`.
- **Flow**: Directs the worker to move items from the `Receiving Zone` to storage bins.
- **Traceability**: The stock movement from the receiving zone to the destination storage bin is recorded with actor, timestamp, and bin confirmation.

### 3. Picking Process
- **Document**: `pick_list`.
- **Flow**: Allocates stock from specific bins to a shipment.
- **Action**: Stock status updates from `Available` to `Allocated`.

### 4. Packing Process
- **Document**: `pack_log`.
- **Flow**: Validates that picked items match the shipping requirements.
- **Action**: Items are packed into containers, and their location is updated to a `Packing/Dispatch Zone`.

### 5. Shipping / Dispatch Process
- **Document**: `shipment_record` or `bill_of_lading`.
- **Flow**: Marks the departure of items from the facility.
- **Action**: Reduces physical `On-Hand` stock and writes a negative stock movement from the `Dispatch Zone`.

### 6. Transfer Process
- **Document**: `inventory_transfer`.
- **Flow**: Coordinates movements between different bins (internal transfer) or different warehouses (inter-site transfer).
- **Inter-site**: Inter-site transfers require an in-transit step, routing items through a virtual `In-Transit` location until received at the destination site.

### 7. Cycle Count and Stocktake Process
- **Document**: `cycle_count_sheet`.
- **Flow**: Freezes activities in the targeted zone/bin, captures counted quantities, and triggers adjustments for discrepancies.

---

## Exception Handling

Real-world warehouse operations frequently encounter exceptions. The system must support clear handling flows for:

- **Damaged Goods**: Move damaged items to a dedicated `QC Blocked` zone/bin, changing logical stock status to `Blocked` to prevent shipping or reserving.
- **Missing Items**: If an item is not found during picking, mark the bin as `Pending Audit` and create a stock adjustment record to deduct the missing quantity.
- **Over-Receipt**: Receiving more items than ordered must be flagged. The system must block completion of receiving until the owner decides to accept the excess or reject it.
- **Under-Receipt**: Capture the discrepancy, complete the partial receiving, and leave the purchase order open for the remaining balance or mark it as short-closed.

---

## Mobile and Scanner Considerations

For warehouse apps intended for mobile scanner use (handheld terminals):
- **Barcodes**: Model entities with barcode search mappings (`sku_barcode`, `bin_barcode`, `serial_barcode`).
- **Single-Action Confirmation**: Require scanning the bin barcode to confirm putaway or picking. Do not rely on manual text inputs on screen.
- **Connection Loss**: Design the client-side state machine to handle intermittent network connectivity in dead zones.

---

## Out of Scope

- Carrier routing and optimization.
- Warehouse layout CAD rendering or 3D mapping.
- Labor scheduling and timesheet tracking.

---

## Guardrails

- [ ] **NO DIRECT PHYSICAL MOVEMENT WITHOUT TRANSFER LOG**: Moving items from one bin to another must write an `inventory_transfer` log.
- [ ] **SEPARATE STEPS**: Receiving, putaway, picking, packing, and shipping must have separate document lifecycles unless simplified by approved owner decision.
- [ ] **EXCEPTIONS LOGGED**: Damaged, missing, or mismatched items must trigger dedicated exception workflows rather than manual text edits in general fields.

---

## QA Checklist

- [ ] Verify that a putaway task accurately updates the physical bin location of the item in the stock ledger.
- [ ] Verify that items placed in a "QC Blocked" zone cannot be picked for customer shipments.
- [ ] Test the transfer of inventory between two sites, ensuring the items show as "In-Transit" during the transfer.
- [ ] Verify that scanning a bin barcode updates the location of the picked item in the picking database record.

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Database model specs.
- [04-user-roles.md](../../../core/docs/04-user-roles.md) — Role specifications (Warehouse Worker, Manager, QC Inspector).
- [inventory-and-stock-guidelines.md](inventory-and-stock-guidelines.md) — Stock ledger and reservation rules.
- [README.md](../README.md) — ERP Extension Pack README.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial warehouse operations guidelines for ERP Operations Pack | Antigravity |
