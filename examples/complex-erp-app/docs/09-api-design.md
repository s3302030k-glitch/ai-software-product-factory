# 09 — API Design

> Conceptual API groups for the Integrated Operations ERP.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This is not a runnable application. No real API implementation exists.
> See the [example README](../README.md) for full context.

> [!WARNING]
> This document defines **conceptual API groups and placeholder endpoint names only**. No real API code, no real routes, no real database connections, and no real finance/bank/payment integration is implemented.

---

## API Design Principles

- All endpoints require authentication.
- Authorization is enforced server-side on every request — client-side checks are UI convenience only.
- Operational scoping filters are applied automatically based on the authenticated user's department/warehouse assignments.
- Endpoints that mutate state must be idempotent where applicable (receiving, stock movements).
- All state changes produce AuditEvent records server-side.
- Finance endpoints are placeholder display only — no real payment processing.

---

## API Group 1 — Auth / Session

**Purpose:** Manage user authentication and session lifecycle.

**Example Endpoint Names (placeholders):**
- `POST /api/auth/sign-in` — Authenticate user with credentials, return session token
- `POST /api/auth/sign-out` — Invalidate current session
- `GET /api/auth/session` — Return current session user and role summary

**Request Fields (conceptual):** email_placeholder, password_placeholder
**Response Fields (conceptual):** session_token, user_id, display_name, roles, operational_scope

**Auth Requirements:** Sign-in is unauthenticated. All others require valid session.

**Error Cases:**
- Invalid credentials → 401
- Session expired → 401
- Account inactive → 403

**Idempotency:** Sign-out is idempotent.

---

## API Group 2 — Suppliers / Customers

**Purpose:** CRUD for SupplierPlaceholder and CustomerPlaceholder master data.

**Example Endpoint Names (placeholders):**
- `GET /api/suppliers` — List all active suppliers (Purchasing Manager, Ops Director)
- `POST /api/suppliers` — Create supplier placeholder (Purchasing Manager)
- `GET /api/suppliers/:id` — Get supplier detail
- `PUT /api/suppliers/:id` — Update supplier placeholder
- `DELETE /api/suppliers/:id` — Archive supplier (soft delete)
- `GET /api/customers` — List all active customers (Sales Coordinator, Ops Director)
- `POST /api/customers` — Create customer placeholder (Sales Coordinator)
- `PUT /api/customers/:id` — Update customer placeholder
- `DELETE /api/customers/:id` — Archive customer

**Request Fields (conceptual):** name, contact_placeholder, status
**Response Fields (conceptual):** id, name, contact_placeholder, status, created_at

**Auth Requirements:** All endpoints require authentication and role check.

**Operational Scoping:** Purchasing Manager sees all suppliers in their department scope.

**Error Cases:**
- Missing required field → 400
- Unauthorized role → 403
- Record not found → 404

---

## API Group 3 — Products / SKUs

**Purpose:** CRUD for Product and SKU catalog records.

**Example Endpoint Names (placeholders):**
- `GET /api/products` — List products (all operational roles)
- `POST /api/products` — Create product (Purchasing Manager, Admin)
- `PUT /api/products/:id` — Update product
- `DELETE /api/products/:id` — Archive product
- `GET /api/products/:id/skus` — List SKUs for product
- `POST /api/products/:id/skus` — Add SKU to product
- `PUT /api/skus/:id` — Update SKU
- `DELETE /api/skus/:id` — Archive SKU

**Request Fields (conceptual):** name, description, unit_of_measure (products); sku_code, unit, status (SKUs)
**Response Fields (conceptual):** id, name, description, unit_of_measure, skus[], status

**Auth Requirements:** Write operations restricted to Purchasing Manager and Platform Admin.

**Error Cases:**
- Duplicate SKU code → 409 Conflict
- Unauthorized → 403
- Product not found → 404

---

## API Group 4 — Warehouses / Zones

**Purpose:** CRUD for Warehouse and WarehouseZone configuration.

**Example Endpoint Names (placeholders):**
- `GET /api/warehouses` — List warehouses (operational roles see their assigned ones)
- `POST /api/warehouses` — Create warehouse (Admin)
- `PUT /api/warehouses/:id` — Update warehouse
- `GET /api/warehouses/:id/zones` — List zones for warehouse
- `POST /api/warehouses/:id/zones` — Create zone (Admin)
- `PUT /api/zones/:id` — Update zone
- `DELETE /api/zones/:id` — Deactivate zone

**Request Fields (conceptual):** name, location_placeholder, status (warehouses); zone_code, zone_type, status (zones)

**Auth Requirements:** Write operations restricted to Platform Admin.

**Operational Scoping:** Non-admin users see only warehouses they are assigned to.

**Error Cases:**
- Duplicate zone code → 409
- Unauthorized → 403

---

## API Group 5 — Stock Balances / Movements

**Purpose:** Read stock balances (derived) and create/list stock movements.

**Example Endpoint Names (placeholders):**
- `GET /api/stock/balances` — List current stock balances by SKU/zone (filtered by warehouse scope)
- `GET /api/stock/movements` — List stock movement history (paginated, filtered)
- `POST /api/stock/movements` — Create a manual stock movement (Inventory Clerk, Warehouse Manager)
- `GET /api/stock/movements/:id` — Get movement detail (immutable)

**Request Fields for movement creation (conceptual):** movement_type, sku_id, source_zone_id, destination_zone_id, quantity, notes
**Response Fields (conceptual):** id, movement_type, sku, source_zone, destination_zone, quantity, actor, created_at

**Auth Requirements:** Balance reads available to all operational roles within scope. Movement creation restricted to Inventory Clerk and Warehouse Manager.

**Operational Scoping:** All results filtered to user's assigned warehouse(s).

**Idempotency:** Movement creation is not idempotent — duplicate submission creates a second movement. UI should guard against double-submit.

**Error Cases:**
- Insufficient source balance → 422 Unprocessable Entity
- Source and destination zone same → 400
- Invalid movement type → 400
- Unauthorized → 403

> [!WARNING]
> StockBalance is read-only via API. It is derived from movement records. No endpoint allows direct balance mutation.

---

## API Group 6 — Receiving

**Purpose:** Create and list goods receiving records.

**Example Endpoint Names (placeholders):**
- `GET /api/receiving` — List receiving records (filtered by warehouse scope)
- `POST /api/receiving` — Create receiving record (Inventory Clerk, Warehouse Manager)
- `GET /api/receiving/:id` — Get receiving detail
- `PUT /api/receiving/:id` — Update receiving record (before completion only)

**Request Fields (conceptual):** po_placeholder_id, warehouse_zone_id, line_items (sku_id, quantity_received)
**Response Fields (conceptual):** id, po_reference, warehouse_zone, line_items[], status, created_at

**Auth Requirements:** Write restricted to Inventory Clerk and Warehouse Manager in the target warehouse.

**Idempotency:** Receiving record creation should use a client-generated idempotency key to prevent duplicate submissions.

**Error Cases:**
- PO not found or not in valid status → 400
- Warehouse zone not of type receiving → 400
- Unauthorized warehouse scope → 403

---

## API Group 7 — Purchase Requests / Orders

**Purpose:** Manage procurement lifecycle.

**Example Endpoint Names (placeholders):**
- `GET /api/purchase-requests` — List purchase requests (Purchasing Manager, Approver, Ops Director)
- `POST /api/purchase-requests` — Create purchase request
- `GET /api/purchase-requests/:id` — Get detail with approval status
- `POST /api/purchase-requests/:id/submit` — Submit for approval
- `GET /api/purchase-orders` — List PO placeholders
- `POST /api/purchase-orders` — Create PO placeholder (Purchasing Manager)
- `GET /api/purchase-orders/:id` — Get PO detail
- `PUT /api/purchase-orders/:id` — Update PO (draft only)

**Auth Requirements:** Purchase request creation open to Purchasing Manager. PO creation restricted to Purchasing Manager.

**Error Cases:**
- Missing line items → 400
- Supplier not found → 404
- Unauthorized → 403

---

## API Group 8 — Sales Orders / Dispatch

**Purpose:** Manage sales order and dispatch placeholder lifecycle.

**Example Endpoint Names (placeholders):**
- `GET /api/sales-orders` — List sales orders (Sales Coordinator, Ops Director)
- `POST /api/sales-orders` — Create sales order placeholder
- `GET /api/sales-orders/:id` — Get detail
- `PUT /api/sales-orders/:id` — Update (open status only)
- `GET /api/dispatch` — List dispatch placeholders
- `POST /api/dispatch` — Create dispatch placeholder (Warehouse Manager, Sales Coordinator)
- `PUT /api/dispatch/:id/status` — Update dispatch status placeholder

**Auth Requirements:** Sales Coordinator creates orders. Warehouse Manager creates dispatch records.

**Error Cases:**
- Customer not found → 404
- Sales order not in valid status → 400
- Unauthorized → 403

---

## API Group 9 — Invoices / Payments (Placeholders)

**Purpose:** Manage invoice and payment placeholder records.

**Example Endpoint Names (placeholders):**
- `GET /api/invoices` — List invoice placeholders (Finance Officer, Ops Director)
- `POST /api/invoices` — Create invoice placeholder (Finance Officer)
- `GET /api/invoices/:id` — Get invoice detail
- `PUT /api/invoices/:id` — Update invoice placeholder
- `GET /api/payments` — List payment placeholders
- `POST /api/payments` — Record payment placeholder (Finance Officer)
- `GET /api/payments/:id` — Get payment detail

**Request Fields (conceptual):** invoice_type, related_order_id, amount_placeholder, currency_display, due_date_placeholder
**Response Fields (conceptual):** id, invoice_number_placeholder, type, amount_placeholder, status, payments[]

**Auth Requirements:** All finance endpoints restricted to Finance Officer and Ops Director (read).

**Error Cases:**
- Related order not found → 404
- Amount is zero or negative → 400
- Unauthorized → 403

> [!WARNING]
> No real bank, payment provider, tax, or accounting integration exists. Amount fields are display placeholders only.

---

## API Group 10 — Approvals

**Purpose:** Manage approval request lifecycle.

**Example Endpoint Names (placeholders):**
- `GET /api/approvals` — List pending approval requests for current user's approval scope
- `GET /api/approvals/:id` — Get approval request detail
- `POST /api/approvals/:id/approve` — Approve a request (with optional notes)
- `POST /api/approvals/:id/reject` — Reject a request (reason required)

**Request Fields (conceptual):** decision_reason (required for reject)
**Response Fields (conceptual):** id, request_type, related_entity, requester, status, decided_at, decision_reason

**Auth Requirements:** Restricted to Approver/Dept. Manager, Warehouse Manager (for adjustments), and Operations Director (for escalated).

**Self-Approval Block:** Server checks that `actor_user_id != requester_user_id`. Returns 403 if self-approval attempted.

**Idempotency:** Approve/reject are idempotent for already-decided requests (returns current state with no mutation).

**Error Cases:**
- Self-approval → 403
- Request already decided → 409
- Unauthorized → 403

---

## API Group 11 — Reports

**Purpose:** Generate and export operational reports.

**Example Endpoint Names (placeholders):**
- `GET /api/reports` — List available report definitions
- `POST /api/reports/generate` — Generate a report for given type and filters
- `GET /api/reports/:id/export/csv` — Export report as CSV
- `GET /api/reports/:id/export/pdf` — Export report as PDF placeholder

**Request Fields (conceptual):** report_type, date_range_start, date_range_end, warehouse_filter, department_filter, sku_filter
**Response Fields (conceptual):** report_id, rows[], total_rows, generated_at, filters_applied

**Auth Requirements:** Operations Director, Read-only Auditor (all reports). Finance Officer (finance reports). Warehouse Manager (warehouse reports).

**Operational Scoping:** Report data filtered to user's assigned scope.

**Error Cases:**
- Invalid report type → 400
- Date range missing → 400
- Unauthorized report type → 403

---

## API Group 12 — Audit Log

**Purpose:** Read-only access to the system audit log.

**Example Endpoint Names (placeholders):**
- `GET /api/audit-log` — List audit events (paginated, filtered)
- `GET /api/audit-log/:id` — Get audit event detail

**Request Fields (conceptual, query params):** entity_type, entity_id, actor_user_id, action, date_from, date_to
**Response Fields (conceptual):** id, entity_type, entity_id, action, actor, before_snapshot, after_snapshot, occurred_at

**Auth Requirements:** Restricted to Read-only Auditor and Operations Director.

**Error Cases:**
- Unauthorized → 403
- Event not found → 404

> [!WARNING]
> Audit log is **read-only**. No create, update, or delete endpoints exist for audit events.

---

## Related Files

- [07-data-model.md](07-data-model.md) — Entity definitions backing each API group
- [10-security-model.md](10-security-model.md) — Authorization enforcement rules
- [05-user-flows.md](05-user-flows.md) — Flows that invoke these API groups
