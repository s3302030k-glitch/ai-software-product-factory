# 05 — User Flows

> Step-by-step operational flows for the Integrated Operations ERP.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This is not a runnable application. See the [example README](../README.md) for full context.

---

## Flow Index

1. Create Supplier / Customer Placeholder
2. Create Product / SKU / Item
3. Create Warehouse and Zone Placeholder
4. Create Purchase Request
5. Approve Purchase Request
6. Create Purchase Order Placeholder
7. Receive Goods into Warehouse
8. Record Stock Movement / Transfer
9. Record Stock Adjustment
10. Create Sales Order Placeholder
11. Prepare Dispatch / Shipment Placeholder
12. Create Invoice Placeholder
13. Record Payment Placeholder
14. View Operational Dashboard
15. Export Report Placeholder
16. Review Audit Log

---

## Flow 1 — Create Supplier / Customer Placeholder

**Actor:** Purchasing Manager (for suppliers) / Sales Coordinator (for customers)

**Preconditions:**
- User is authenticated and assigned the relevant role.
- Department scope is set.

**Steps:**
1. Navigate to Supplier List or Customer List page.
2. Click "New Supplier" or "New Customer".
3. Enter placeholder fields: name, contact placeholder, status (Active).
4. Submit the form.
5. System validates required fields.
6. Record is created with status Active and a system-generated ID.
7. AuditEvent is recorded: `supplier_created` or `customer_created`.

**Success State:** New supplier/customer placeholder appears in the list with status Active.

**Failure / Edge Cases:**
- Duplicate name: system warns but does not block (names are not unique keys).
- Missing required field: form validation blocks submission.

**Audit Events:** `supplier_created`, `customer_created`

---

## Flow 2 — Create Product / SKU / Item

**Actor:** Purchasing Manager or Platform Admin

**Preconditions:**
- User is authenticated with Purchasing Manager or Platform Admin role.
- Unit of measure values are configured in system settings.

**Steps:**
1. Navigate to Product / SKU Catalog page.
2. Click "New Product".
3. Enter product name, description, and unit of measure.
4. Save product.
5. Optionally, add one or more SKU records linked to this product (SKU code, unit, status).
6. System validates SKU code uniqueness.
7. AuditEvent is recorded: `product_created`, `sku_created`.

**Success State:** Product and associated SKUs appear in the catalog with status Active.

**Failure / Edge Cases:**
- Duplicate SKU code: system blocks and shows validation error.
- No SKUs added: product is saved without SKUs (SKUs can be added later).

**Audit Events:** `product_created`, `sku_created`

---

## Flow 3 — Create Warehouse and Zone Placeholder

**Actor:** Platform Admin or Operations Director

**Preconditions:**
- User is authenticated with Platform Admin or Operations Director role.

**Steps:**
1. Navigate to Warehouse List page.
2. Click "New Warehouse".
3. Enter warehouse name, location placeholder, status (Active).
4. Save warehouse.
5. Navigate into the new warehouse record.
6. Click "Add Zone".
7. Enter zone code, zone type (receiving, storage, dispatch), and status.
8. Save zone.
9. AuditEvent recorded: `warehouse_created`, `zone_created`.

**Success State:** New warehouse appears in the list with its zones visible on the Warehouse Zones page.

**Failure / Edge Cases:**
- Duplicate zone code within warehouse: system blocks.
- Warehouse with no zones: allowed, but stock operations are blocked until at least one zone exists.

**Audit Events:** `warehouse_created`, `zone_created`

---

## Flow 4 — Create Purchase Request

**Actor:** Purchasing Manager or any user with purchase request creation permission

**Preconditions:**
- User is authenticated.
- At least one product/SKU exists.
- Department scope is assigned.

**Steps:**
1. Navigate to Purchase Requests page.
2. Click "New Purchase Request".
3. Select department (defaults to user's department).
4. Add one or more line items: select SKU, enter quantity requested.
5. Enter notes/justification (optional).
6. Submit request.
7. System sets status to `Pending Approval`.
8. ApprovalRequest record is created and routed to the department Approver.
9. AuditEvent recorded: `purchase_request_submitted`.

**Success State:** Purchase Request appears in list with status `Pending Approval`. Approver sees it in their Approval Inbox.

**Failure / Edge Cases:**
- No SKU selected: form blocks submission.
- Zero quantity: form blocks submission.
- Requester is also the designated approver: system blocks self-approval and escalates to Operations Director.

**Audit Events:** `purchase_request_submitted`, `approval_request_created`

---

## Flow 5 — Approve Purchase Request

**Actor:** Approver / Department Manager or Operations Director (escalated)

**Preconditions:**
- A Purchase Request is in status `Pending Approval`.
- Approver is assigned to the relevant department and has approval authority.
- Approver did not create the request.

**Steps:**
1. Navigate to Approval Inbox.
2. Select the pending Purchase Request.
3. Review line items, quantities, justification, and requester.
4. Click "Approve" or "Reject".
5. If rejecting: enter rejection reason (required).
6. If approving: system checks authorization limit.
   - If within limit: approval is recorded, status changes to `Approved`.
   - If above limit: system escalates to Operations Director automatically.
7. AuditEvent recorded: `purchase_request_approved` or `purchase_request_rejected`.
8. Requester is notified (notification placeholder).

**Success State:** Purchase Request status changes to `Approved`. Purchasing Manager can now create a Purchase Order Placeholder.

**Failure / Edge Cases:**
- Self-approval attempt: system blocks with error.
- Approver has no active department assignment: system blocks.
- Escalated request: Operations Director must approve separately.

**Audit Events:** `purchase_request_approved`, `purchase_request_rejected`, `approval_escalated`

---

## Flow 6 — Create Purchase Order Placeholder

**Actor:** Purchasing Manager

**Preconditions:**
- At least one Purchase Request is in status `Approved`.
- At least one Supplier Placeholder exists.

**Steps:**
1. Navigate to Purchase Orders page.
2. Click "New Purchase Order".
3. Select an approved Purchase Request (or create independently).
4. Select Supplier Placeholder.
5. Add line items: SKU, quantity, unit price placeholder.
6. Set expected delivery date placeholder.
7. Submit PO.
8. System sets status to `Draft` initially, then `Submitted` upon confirmation.
9. AuditEvent recorded: `po_created`.

**Success State:** Purchase Order Placeholder appears in list. Warehouse team can reference it during receiving.

**Failure / Edge Cases:**
- No supplier selected: form blocks submission.
- Quantities exceed approved request quantities: system warns (does not block in documentation reference).

**Audit Events:** `po_created`, `po_status_changed`

---

## Flow 7 — Receive Goods into Warehouse

**Actor:** Warehouse Manager or Inventory Clerk

**Preconditions:**
- A Purchase Order Placeholder exists in status `Submitted`.
- A receiving zone exists in the target warehouse.
- User is assigned to the target warehouse.

**Steps:**
1. Navigate to Receiving page.
2. Click "New Receiving Record".
3. Select the related Purchase Order Placeholder.
4. Select target warehouse and receiving zone.
5. For each line item: enter SKU and quantity actually received.
6. Submit receiving record.
7. System creates StockMovement records (type: `receiving_in`) for each line.
8. StockBalance is updated (derived from movements — not directly edited).
9. Receiving record status set to `Complete`.
10. AuditEvent recorded: `goods_received`, `stock_movement_created`.

**Success State:** Goods are recorded as received. StockBalance for the relevant SKU/zone reflects the addition. PO status updates to `Partially Received` or `Fully Received`.

**Failure / Edge Cases:**
- Quantity received exceeds PO quantity: system warns, records actual quantity, flags for review.
- No receiving zone configured: system blocks.
- User not assigned to warehouse: system blocks.

**Audit Events:** `goods_received`, `stock_movement_created`, `stock_balance_updated`

---

## Flow 8 — Record Stock Movement / Transfer

**Actor:** Inventory Clerk or Warehouse Manager

**Preconditions:**
- User is authenticated and assigned to the source warehouse.
- Source zone has sufficient stock balance for the SKU.

**Steps:**
1. Navigate to Stock Movements page.
2. Click "New Movement".
3. Select movement type: transfer, internal move.
4. Select SKU.
5. Select source zone and destination zone.
6. Enter quantity.
7. Enter notes (optional).
8. Submit movement.
9. System deducts from source zone StockBalance and credits to destination zone.
10. StockMovement record is created (immutable).
11. AuditEvent recorded: `stock_movement_created`.

**Success State:** Stock movement appears in movement history. StockBalance reflects updated quantities in source and destination zones.

**Failure / Edge Cases:**
- Insufficient stock in source zone: system blocks and shows current balance.
- Source and destination zone are the same: system blocks.
- Movement quantity is zero or negative: form validation blocks.

**Audit Events:** `stock_movement_created`, `stock_balance_updated`

---

## Flow 9 — Record Stock Adjustment

**Actor:** Inventory Clerk (submit) → Warehouse Manager or Approver (approve)

**Preconditions:**
- User is authenticated and assigned to the relevant warehouse.
- A valid reason for adjustment exists.

**Steps:**
1. Navigate to Stock Adjustments page.
2. Click "New Adjustment".
3. Select SKU and warehouse zone.
4. Enter quantity delta (positive = addition, negative = reduction).
5. Enter reason (required).
6. Submit adjustment request.
7. System creates ApprovalRequest and routes to Warehouse Manager.
8. Warehouse Manager reviews and approves or rejects.
9. If approved: StockMovement record of type `adjustment` is created.
10. StockBalance is updated (derived from movements).
11. AuditEvent recorded throughout.

**Success State:** Adjustment is approved, stock movement created, balance updated. Adjustment record shows status `Approved`.

**Failure / Edge Cases:**
- Self-approval: system blocks (submitter cannot approve own adjustment).
- Large negative adjustment causing negative balance: system warns approver.
- Rejected adjustment: adjustment record shows `Rejected` with reason.

**Audit Events:** `adjustment_submitted`, `adjustment_approved`, `adjustment_rejected`, `stock_movement_created`

---

## Flow 10 — Create Sales Order Placeholder

**Actor:** Sales Coordinator

**Preconditions:**
- User is authenticated with Sales Coordinator role.
- At least one Customer Placeholder exists.
- At least one SKU/product is in catalog.

**Steps:**
1. Navigate to Sales Orders page.
2. Click "New Sales Order".
3. Select Customer Placeholder.
4. Add line items: SKU, quantity, unit price placeholder.
5. Enter expected dispatch date placeholder.
6. Submit sales order.
7. System sets status to `Open`.
8. AuditEvent recorded: `sales_order_created`.

**Success State:** Sales Order Placeholder appears in list with status `Open`. Warehouse team can reference it during dispatch preparation.

**Failure / Edge Cases:**
- No customer selected: form blocks.
- Zero-quantity line item: form blocks.

**Audit Events:** `sales_order_created`

---

## Flow 11 — Prepare Dispatch / Shipment Placeholder

**Actor:** Warehouse Manager or Sales Coordinator

**Preconditions:**
- A Sales Order Placeholder is in status `Open`.
- Stock balance is available in a dispatch-capable zone.

**Steps:**
1. Navigate to Dispatch / Shipments page.
2. Click "New Dispatch".
3. Select the related Sales Order Placeholder.
4. Select warehouse and dispatch zone.
5. For each line item: confirm SKU and quantity to dispatch.
6. Submit dispatch placeholder.
7. System creates StockMovement records (type: `dispatch_out`) for each line.
8. StockBalance is decremented.
9. Dispatch placeholder status set to `Prepared`.
10. AuditEvent recorded: `dispatch_created`, `stock_movement_created`.

**Success State:** Dispatch Placeholder appears with status `Prepared`. Stock balances reflect outgoing stock.

**Failure / Edge Cases:**
- Insufficient stock: system warns, does not block dispatch (gap is documented for resolution).
- No dispatch zone configured: system blocks.

**Audit Events:** `dispatch_created`, `stock_movement_created`, `stock_balance_updated`

---

## Flow 12 — Create Invoice Placeholder

**Actor:** Finance Officer

**Preconditions:**
- A Purchase Order Placeholder or Sales Order Placeholder exists in a received/dispatched state.

**Steps:**
1. Navigate to Invoice List page.
2. Click "New Invoice".
3. Select invoice type: purchase invoice or sales invoice.
4. Select the related PO or sales order placeholder.
5. Enter amount (display value placeholder — not a real accounting entry).
6. Enter due date placeholder.
7. Submit invoice placeholder.
8. System sets status to `Draft`.
9. AuditEvent recorded: `invoice_created`.

**Success State:** Invoice Placeholder appears in Invoice List. Finance Officer can track status.

**Failure / Edge Cases:**
- No related order selected: form blocks.
- Amount is zero: form warns.

**Audit Events:** `invoice_created`

---

## Flow 13 — Record Payment Placeholder

**Actor:** Finance Officer

**Preconditions:**
- An Invoice Placeholder exists in status `Draft` or `Issued`.

**Steps:**
1. Navigate to Payment Placeholder List page.
2. Click "Record Payment".
3. Select related Invoice Placeholder.
4. Enter amount paid (display value placeholder).
5. Enter payment method placeholder.
6. Enter payment date placeholder.
7. Submit.
8. System sets Payment Placeholder status to `Recorded`.
9. Invoice Placeholder status may update to `Partially Paid` or `Paid`.
10. AuditEvent recorded: `payment_recorded`.

**Success State:** Payment Placeholder recorded and visible in list. Invoice status updated.

**Failure / Edge Cases:**
- Payment amount exceeds invoice amount: system warns.
- No invoice selected: form blocks.

**Audit Events:** `payment_recorded`, `invoice_status_changed`

---

## Flow 14 — View Operational Dashboard

**Actor:** Operations Director, Warehouse Manager, Purchasing Manager (each sees their relevant section)

**Preconditions:**
- User is authenticated.

**Steps:**
1. Navigate to Dashboard.
2. System loads KPI widgets filtered to user's operational scope.
3. User views: pending approval count, open POs, open sales orders, stock alerts, recent movements.
4. User can click through to relevant list pages.

**Success State:** Dashboard displays current operational status for the user's scope.

**Failure / Edge Cases:**
- No data yet: empty state shown per widget.
- Permission boundary: Finance section is hidden for non-Finance roles.

**Audit Events:** None (read-only view, no audit trigger)

---

## Flow 15 — Export Report Placeholder

**Actor:** Operations Director, Read-only Auditor, Finance Officer

**Preconditions:**
- User is authenticated with report access.

**Steps:**
1. Navigate to Reports page.
2. Select report type (stock movement report, PO summary, sales order summary).
3. Apply date range and filter placeholders.
4. Click "Export".
5. System generates a CSV or PDF placeholder.
6. File is made available for download.
7. AuditEvent recorded: `report_exported`.

**Success State:** Report file is downloaded by the user.

**Failure / Edge Cases:**
- No data in range: report exports with empty dataset and header row.
- Large dataset: pagination or streaming placeholder is noted.

**Audit Events:** `report_exported`

---

## Flow 16 — Review Audit Log

**Actor:** Read-only Auditor, Operations Director

**Preconditions:**
- User is authenticated with audit log access.

**Steps:**
1. Navigate to Audit Log page.
2. Apply filter placeholders: entity type, date range, actor, event type.
3. View list of AuditEvents with entity, action, actor, before/after snapshot, and timestamp.
4. Click on a specific event to see detail.

**Success State:** User can see a full immutable history of all relevant events.

**Failure / Edge Cases:**
- Large log: pagination is required.
- Filtered view returns no results: empty state shown.

**Audit Events:** None (read-only view of audit log itself)

---

## Related Files

- [04-user-roles.md](04-user-roles.md) — Role permissions referenced in each flow
- [06-pages-spec.md](06-pages-spec.md) — Page-level specs for each flow step
- [07-data-model.md](07-data-model.md) — Entity definitions referenced in flows
- [10-security-model.md](10-security-model.md) — Authorization rules enforced in flows
