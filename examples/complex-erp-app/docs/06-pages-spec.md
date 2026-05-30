# 06 — Pages Specification

> Page-level specifications for all pages in the Integrated Operations ERP.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This is not a runnable application. See the [example README](../README.md) for full context.

---

## Page Index

1. Sign In
2. Dashboard
3. Supplier List
4. Customer List
5. Product / SKU Catalog
6. Warehouse List
7. Warehouse Zones
8. Stock Overview
9. Stock Movements
10. Receiving
11. Stock Adjustments
12. Purchase Requests
13. Purchase Orders
14. Sales Orders
15. Dispatch / Shipments
16. Finance Overview
17. Invoice List
18. Payment Placeholder List
19. Approval Inbox
20. Reports
21. Audit Log
22. Settings / Admin

---

## Page 1 — Sign In

**Purpose:** Authenticate the user and establish a session.

**Allowed Roles:** All (unauthenticated)

**Key Components:**
- Email and password form
- Submit button
- Error message area
- Redirect to Dashboard on success

**States:**
- Default: empty form
- Loading: submit button disabled, spinner shown
- Error: "Invalid credentials" message shown
- Success: redirect to Dashboard

**Validations:**
- Email: required, valid email format
- Password: required, minimum length placeholder

**Empty / Loading / Error States:**
- Loading: spinner overlay on submit
- Error: inline message below form

**Security Notes:**
- No credential storage in this documentation reference
- Session token must be stored securely (not in localStorage for sensitive apps — see architecture)
- Failed login attempts should be rate-limited (implementation decision)

---

## Page 2 — Dashboard

**Purpose:** Operational overview tailored to the user's role and scope.

**Allowed Roles:** All authenticated roles (content filtered by role)

**Key Components:**
- Pending Approvals counter (Approvers, Purchasing Manager, Warehouse Manager)
- Open Purchase Orders counter (Purchasing Manager, Operations Director)
- Open Sales Orders counter (Sales Coordinator, Operations Director)
- Stock Alert widget (low-stock SKUs — Warehouse Manager, Inventory Clerk)
- Recent Stock Movements list (Warehouse Manager, Inventory Clerk)
- Finance Overview stub (Finance Officer)
- Quick navigation links

**States:**
- Loading: skeleton placeholders per widget
- Default: populated widgets
- Empty: "No data yet" per widget

**Validations:** N/A (read-only view)

**Security Notes:**
- Finance widget is hidden for non-Finance roles
- All data filtered to user's operational scope (department/warehouse)
- No cross-scope data leakage

---

## Page 3 — Supplier List

**Purpose:** List and manage supplier placeholder records.

**Allowed Roles:** Purchasing Manager (read/write), Operations Director (read), Read-only Auditor (read)

**Key Components:**
- Searchable/filterable table: Name, Status, Created Date
- "New Supplier" button (Purchasing Manager only)
- Row actions: View, Edit, Archive (Purchasing Manager only)
- Pagination controls

**States:**
- Loading: table skeleton
- Default: populated table
- Empty: "No suppliers yet" with "New Supplier" prompt
- Error: error message with retry option

**Validations:**
- Name: required, max 200 characters
- Status: required (Active / Archived)

**Security Notes:**
- Create/Edit/Archive actions hidden for non-Purchasing Manager roles
- No real supplier contact data (placeholder only)

---

## Page 4 — Customer List

**Purpose:** List and manage customer placeholder records.

**Allowed Roles:** Sales Coordinator (read/write), Operations Director (read), Read-only Auditor (read)

**Key Components:**
- Searchable/filterable table: Name, Status, Created Date
- "New Customer" button (Sales Coordinator only)
- Row actions: View, Edit, Archive (Sales Coordinator only)
- Pagination controls

**States:** Same pattern as Supplier List

**Validations:**
- Name: required, max 200 characters
- Status: required (Active / Archived)

**Security Notes:** Create/Edit/Archive actions hidden for non-Sales Coordinator roles

---

## Page 5 — Product / SKU Catalog

**Purpose:** List and manage product and SKU records.

**Allowed Roles:** Purchasing Manager (read/write), Platform Admin (read/write), All operational roles (read)

**Key Components:**
- Product table: Name, Unit of Measure, SKU count, Status
- Expandable SKU rows per product
- "New Product" button (Purchasing Manager, Admin)
- "Add SKU" button per product
- Row actions: View, Edit, Archive

**States:**
- Loading: skeleton
- Default: populated with expandable rows
- Empty: "No products yet" with prompt
- Error: retry prompt

**Validations:**
- Product Name: required, max 200 characters
- SKU Code: required, unique per product catalog, max 50 characters
- Unit of Measure: required, selected from configured list

**Security Notes:**
- Write actions restricted to Purchasing Manager and Admin
- SKU codes must be unique — validated server-side

---

## Page 6 — Warehouse List

**Purpose:** List and manage warehouse records.

**Allowed Roles:** Platform Admin (read/write), Operations Director (read), Warehouse Manager (read), Read-only Auditor (read)

**Key Components:**
- Table: Warehouse Name, Location Placeholder, Zone Count, Status
- "New Warehouse" button (Admin only)
- Row actions: View, Edit, Deactivate
- Link to Warehouse Zones page per warehouse

**States:** Standard loading/empty/error pattern

**Validations:**
- Warehouse Name: required, unique, max 200 characters
- Status: required (Active / Inactive)

**Security Notes:** Write actions restricted to Platform Admin

---

## Page 7 — Warehouse Zones

**Purpose:** List and manage zones within a specific warehouse.

**Allowed Roles:** Platform Admin (read/write), Warehouse Manager of that warehouse (read), All operational roles (read for their warehouse)

**Key Components:**
- Zone table: Zone Code, Type (receiving / storage / dispatch), Status
- "Add Zone" button (Admin only)
- Row actions: View, Edit, Deactivate

**States:** Standard pattern

**Validations:**
- Zone Code: required, unique within warehouse
- Zone Type: required (receiving / storage / dispatch)

**Security Notes:**
- Users only see zones for warehouses they are assigned to
- Zone type drives valid stock movement routing

---

## Page 8 — Stock Overview

**Purpose:** Real-time view of current stock balances per SKU per warehouse zone.

**Allowed Roles:** Warehouse Manager (write scope), Inventory Clerk (write scope), Purchasing Manager (read), Sales Coordinator (read), Operations Director (read), Read-only Auditor (read)

**Key Components:**
- Filterable table: SKU, Product Name, Warehouse, Zone, Current Balance, Unit
- Filter: Warehouse, Zone, SKU, low-stock threshold
- Export button (Operations Director, Auditor)

**States:**
- Loading: skeleton table
- Default: populated table
- Empty zone: balance shown as 0
- Low-stock alert: highlighted row

**Validations:** N/A (read-only display derived from StockMovements)

**Security Notes:**
- Balances are derived from StockMovement records — never directly editable
- Users see only their assigned warehouse scope

---

## Page 9 — Stock Movements

**Purpose:** Record and view stock movement history.

**Allowed Roles:** Inventory Clerk (create within scope), Warehouse Manager (create within scope), All roles (read within scope), Read-only Auditor (read all)

**Key Components:**
- Movement history table: Date, SKU, Type, Source Zone, Destination Zone, Quantity, Actor
- "New Movement" button (Clerk, Manager)
- Movement type filter: receiving, transfer, adjustment, dispatch
- Date range filter
- Detail view per movement (immutable)

**States:** Standard pattern

**Validations:**
- SKU: required
- Source zone: required for transfers and dispatch
- Destination zone: required for receiving and transfers
- Quantity: required, positive integer, must not exceed source balance for outgoing movements

**Security Notes:**
- Stock movement records are immutable once created
- No direct edit or delete allowed — corrections via stock adjustments only

---

## Page 10 — Receiving

**Purpose:** Record goods receipt from supplier into warehouse stock.

**Allowed Roles:** Inventory Clerk (create), Warehouse Manager (create), Purchasing Manager (read), Read-only Auditor (read)

**Key Components:**
- Receiving record table: Date, PO Reference, Warehouse, Zone, SKU, Quantity Received, Status
- "New Receiving Record" button
- Link to related Purchase Order Placeholder
- Status badge: Complete / Partial

**States:** Standard pattern

**Validations:**
- PO reference: required (selected from open POs)
- Warehouse + zone (receiving type): required
- SKU + quantity: required per line item
- Quantity must be positive

**Security Notes:**
- Only users assigned to the target warehouse can create receiving records
- Receiving creates immutable StockMovement records

---

## Page 11 — Stock Adjustments

**Purpose:** Submit, review, and approve stock quantity corrections.

**Allowed Roles:** Inventory Clerk (submit), Warehouse Manager (submit + approve), Approver/Dept. Manager (approve), Read-only Auditor (read)

**Key Components:**
- Adjustment table: Date, SKU, Zone, Delta, Reason, Status, Approver
- "New Adjustment" button (Clerk, Manager)
- Approve / Reject actions (Manager, Approver)
- Status filter: Pending / Approved / Rejected

**States:** Standard pattern

**Validations:**
- SKU + zone: required
- Quantity delta: required, non-zero integer, reason is required for negative deltas
- Reason: required (free text, max 500 characters)

**Security Notes:**
- Self-approval is blocked at the system level
- Approved adjustments create immutable StockMovement records
- Rejected adjustments do not affect stock balance

---

## Page 12 — Purchase Requests

**Purpose:** Create and track purchase requests.

**Allowed Roles:** Purchasing Manager (create/read), Approver/Dept. Manager (approve/read), Operations Director (read), Read-only Auditor (read)

**Key Components:**
- Request table: Date, Requester, Department, Items Summary, Status
- "New Purchase Request" button
- Line item editor: SKU selector, quantity input
- Status filter: Pending / Approved / Rejected / Draft
- Detail view with approval history

**States:** Standard pattern

**Validations:**
- At least one line item required
- SKU: required per line
- Quantity: required, positive integer
- Department: required

**Security Notes:**
- Requester cannot approve their own request
- Approval routing is determined by department assignment

---

## Page 13 — Purchase Orders

**Purpose:** Create and track purchase order placeholders.

**Allowed Roles:** Purchasing Manager (create/read), Warehouse Manager (read — for receiving reference), Operations Director (read), Finance Officer (read), Read-only Auditor (read)

**Key Components:**
- PO table: PO Number (placeholder), Supplier, Date, Status, Line Item Count
- "New Purchase Order" button
- Supplier selector
- Line item editor: SKU, quantity, unit price placeholder
- Link to related Purchase Request
- Link to related Receiving Records
- Status badge: Draft / Submitted / Partially Received / Fully Received / Closed

**States:** Standard pattern

**Validations:**
- Supplier: required
- At least one line item
- SKU + quantity: required per line

**Security Notes:**
- Only Purchasing Manager can create POs
- Unit price is a display placeholder — not a real financial instrument
- No bank or payment processing connection

---

## Page 14 — Sales Orders

**Purpose:** Create and track sales order placeholders.

**Allowed Roles:** Sales Coordinator (create/read), Warehouse Manager (read — for dispatch reference), Operations Director (read), Finance Officer (read), Read-only Auditor (read)

**Key Components:**
- Order table: Order Number (placeholder), Customer, Date, Status, Line Item Count
- "New Sales Order" button
- Customer selector
- Line item editor: SKU, quantity, unit price placeholder
- Link to Dispatch Placeholder
- Link to Invoice Placeholder
- Status badge: Open / In Progress / Dispatched / Closed

**States:** Standard pattern

**Validations:**
- Customer: required
- At least one line item
- SKU + quantity: required per line

**Security Notes:**
- Only Sales Coordinator can create sales orders
- Unit price is a display placeholder only

---

## Page 15 — Dispatch / Shipments

**Purpose:** Prepare and track dispatch placeholder records.

**Allowed Roles:** Warehouse Manager (create/read), Sales Coordinator (read), Operations Director (read), Read-only Auditor (read)

**Key Components:**
- Dispatch table: Date, Sales Order Reference, Warehouse, Zone, SKU, Quantity, Status
- "New Dispatch" button
- Sales order selector
- Line item confirmation editor
- Status badge: Pending / Prepared / Dispatched (placeholder)

**States:** Standard pattern

**Validations:**
- Sales order reference: required
- Warehouse + zone (dispatch type): required
- SKU + quantity: required per line

**Security Notes:**
- Dispatch creates StockMovement records (dispatch_out type)
- Insufficient stock triggers a warning, not a block (documented for resolution)

---

## Page 16 — Finance Overview

**Purpose:** High-level summary of invoice and payment placeholder statuses.

**Allowed Roles:** Finance Officer (read/write), Operations Director (read), Read-only Auditor (read)

**Key Components:**
- Summary widgets: Total open invoices, Total paid placeholders, Total pending payments
- Quick links to Invoice List and Payment List
- Finance report export button (placeholder)

**States:** Standard loading/empty/error

**Security Notes:**
- Finance pages are hidden from all non-Finance roles
- All amounts are placeholder display values only — not real accounting entries

---

## Page 17 — Invoice List

**Purpose:** List and manage invoice placeholder records.

**Allowed Roles:** Finance Officer (create/read), Operations Director (read), Read-only Auditor (read)

**Key Components:**
- Invoice table: Invoice Number (placeholder), Type, Related Order, Amount (placeholder), Due Date, Status
- "New Invoice" button
- Filter: Status, Date Range, Type
- Status badge: Draft / Issued / Partially Paid / Paid / Overdue (placeholders)

**States:** Standard pattern

**Validations:**
- Type: required (purchase or sales)
- Related order: required
- Amount: required, positive decimal placeholder
- Due date: required

**Security Notes:**
- Invoices are display placeholders only — no real accounting or tax implications
- No bank, payment provider, or tax engine connection

---

## Page 18 — Payment Placeholder List

**Purpose:** List and manage payment placeholder records.

**Allowed Roles:** Finance Officer (create/read), Operations Director (read), Read-only Auditor (read)

**Key Components:**
- Payment table: Payment Reference (placeholder), Invoice Reference, Amount, Method Placeholder, Date, Status
- "Record Payment" button
- Filter: Status, Date Range

**States:** Standard pattern

**Validations:**
- Invoice reference: required
- Amount: required, positive decimal placeholder
- Payment date: required

**Security Notes:**
- Payment records are placeholders — no real payment processing
- No Stripe, PayPal, bank, or financial instrument connection

---

## Page 19 — Approval Inbox

**Purpose:** Centralized inbox for pending approval requests.

**Allowed Roles:** Approver / Dept. Manager (approve/reject), Operations Director (approve escalated), Warehouse Manager (approve adjustments), Purchasing Manager (approve in some configs)

**Key Components:**
- Approval request table: Date, Type, Requester, Summary, Status
- Approve / Reject buttons per item (with reason required on reject)
- Filter: Type (purchase request, stock adjustment), Status
- Detail panel per request

**States:**
- Loading: skeleton
- Default: pending items listed
- Empty: "No pending approvals" message

**Validations:**
- Reject reason: required when rejecting
- Self-approval: blocked — item shown but approve button disabled with tooltip

**Security Notes:**
- Approval actions are role-scoped
- Self-approval is enforced at both UI and API level
- All approval actions are logged as AuditEvents

---

## Page 20 — Reports

**Purpose:** Generate and export operational reports.

**Allowed Roles:** Operations Director (all), Finance Officer (finance reports), Read-only Auditor (all), Warehouse Manager (warehouse reports)

**Key Components:**
- Report type selector: Stock Movement Report, PO Summary, Sales Order Summary, Finance Summary
- Filter panel: Date range, Warehouse, Department, SKU
- Preview table (paginated)
- Export button: CSV and PDF placeholder

**States:**
- Loading: spinner while generating
- Default: report table displayed
- Empty: no data for filters shown
- Error: generation error with retry

**Validations:**
- Report type: required
- Date range: required

**Security Notes:**
- Report data filtered to user's operational scope
- Finance reports hidden for non-Finance roles
- Export events are logged as AuditEvents

---

## Page 21 — Audit Log

**Purpose:** Full immutable event log for all state changes in the system.

**Allowed Roles:** Read-only Auditor (all), Operations Director (all)

**Key Components:**
- Event table: Timestamp, Entity Type, Entity ID, Action, Actor, Before/After Snapshot
- Filter: Entity type, Actor, Date range, Action type
- Detail view per event (read-only)
- Pagination (log may be large)

**States:**
- Loading: skeleton
- Default: event list
- Empty: no events for filter

**Validations:** N/A (read-only)

**Security Notes:**
- Audit log is immutable — no edit or delete allowed
- Accessible only to Read-only Auditor and Operations Director roles
- Before/after snapshots must not include credentials or sensitive tokens

---

## Page 22 — Settings / Admin

**Purpose:** Platform administration including user management, role assignment, and system configuration.

**Allowed Roles:** Platform Admin only

**Key Components:**
- User list: Name, Email Placeholder, Role(s), Status
- "New User" button
- "Edit Roles" per user
- "Deactivate User" action
- Department configuration
- Warehouse assignment
- System settings (currency display, unit of measure options)

**States:** Standard pattern

**Validations:**
- Email: required, valid format
- Role: at least one role required
- Department: required for operational roles

**Security Notes:**
- Only Platform Admin can access this page
- Role assignment changes are logged as AuditEvents
- No real credentials or private user data in this documentation reference

---

## Related Files

- [05-user-flows.md](05-user-flows.md) — Flows that map to these pages
- [07-data-model.md](07-data-model.md) — Entities powering each page
- [10-security-model.md](10-security-model.md) — Authorization rules per page
- [04-user-roles.md](04-user-roles.md) — Role access per page
