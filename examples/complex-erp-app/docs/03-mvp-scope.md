# 03 — MVP Scope

> MoSCoW prioritization of all features for the Integrated Operations ERP.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This is not a runnable application. See the [example README](../README.md) for full context.

---

## Status

`Approved` — Scope locked for this documentation reference.

---

## Scope Rules

- **Must Have:** Required for the system to fulfill its core operational purpose.
- **Should Have:** High-value features included if documentation effort allows.
- **Could Have:** Lower-priority features included in this documentation reference for completeness.
- **Out of Scope:** Explicitly excluded from this documentation reference.
- **Future Scope:** Planned for a future version or extension.

---

## Must Have

### Master Data

- [x] Supplier placeholder records (name, contact placeholder, status)
- [x] Customer placeholder records (name, contact placeholder, status)
- [x] Product records (name, description, unit of measure, status)
- [x] SKU records linked to products (SKU code, unit, status)
- [x] Warehouse records (name, location placeholder, status)
- [x] Warehouse zone records linked to warehouses (zone code, type, status)
- [x] Department records (name, department head)
- [x] User records with role assignments

### Inventory & Stock

- [x] StockBalance records per SKU per warehouse zone (derived from movements)
- [x] StockMovement records (type, quantity, source zone, destination zone, actor, timestamp)
- [x] ReceivingRecord records (linked to PO placeholder, SKU, quantity, zone, date)
- [x] StockAdjustment records (quantity delta, reason, approval status, actor, timestamp)
- [x] Stock transfer between zones (recorded as stock movements)

### Procurement

- [x] PurchaseRequest records (SKUs, quantities, requester, department, status)
- [x] PurchaseOrderPlaceholder records (supplier placeholder, line items, status)
- [x] Purchase request approval workflow (submit → approve/reject → PO creation)
- [x] Purchase order placeholder linked to receiving records

### Sales & Dispatch

- [x] SalesOrderPlaceholder records (customer placeholder, line items, status)
- [x] DispatchPlaceholder records (linked to sales order, SKUs, warehouse, status)
- [x] Sales order creation workflow placeholder

### Finance

- [x] InvoicePlaceholder records (linked to PO or sales order, amounts as display values)
- [x] PaymentPlaceholder records (linked to invoice placeholder, amount, status)
- [x] Finance overview page (read-only summary of placeholders)

### Approvals & Audit

- [x] ApprovalRequest records (type, requestor, approver, status, timestamp)
- [x] Approval inbox for designated approvers
- [x] AuditEvent records (entity type, entity ID, actor, action, before/after snapshot, timestamp)
- [x] Audit log page (read-only for auditors and directors)

### Dashboard & Reports

- [x] Operational dashboard with KPI placeholders (stock summary, pending approvals, open POs, open orders)
- [x] Report definition placeholders (stock movement report, PO summary, sales order summary)
- [x] Basic report export placeholder (CSV/PDF)

### Security & Access

- [x] Authentication (session-based or token-based per architecture)
- [x] Role-based authorization for all pages and actions
- [x] Operational scoping (users see only their department/warehouse records)
- [x] Approval authority separated from data-entry authority

### RTL/i18n Readiness

- [x] Text direction attribute set at app root level
- [x] Translation key structure defined
- [x] Date, number, and currency display formatting documented (not implemented)
- [x] Layout mirroring documented for RTL readiness

---

## Should Have

- [x] Multi-warehouse support (stock scoped per warehouse)
- [x] Approval escalation path for high-value requests (to Operations Director)
- [x] Soft delete / archive for suppliers, customers, products, and warehouses
- [x] Pagination on all list pages
- [x] Empty, loading, and error states documented for all pages
- [x] Report filter placeholders (date range, department, warehouse)
- [x] Print layout placeholders for purchase order and invoice PDFs

---

## Could Have

- [x] Stock count / cycle count workflow placeholder
- [x] Notification placeholder for approval results
- [x] Dispatch tracking status updates
- [x] Finance summary report by department
- [x] User profile page
- [x] Basic search on list pages

---

## Out of Scope

The following are explicitly excluded from this documentation reference and must not be added:

- **Real accounting ledger** — No double-entry bookkeeping, no GL accounts, no chart of accounts.
- **Real tax calculation** — No VAT, GST, sales tax, or any tax engine.
- **Real bank integration** — No bank API, no SWIFT/IBAN processing, no direct debit.
- **Real payment provider integration** — No Stripe, PayPal, Adyen, or equivalent.
- **Production-grade WMS automation** — No barcode scanner hardware, no RFID, no conveyor integration.
- **Barcode hardware integration** — No label printing, no scanner SDKs.
- **Real logistics carrier integration** — No FedEx, DHL, UPS, or shipping API.
- **Production planning / manufacturing** — No BOM, no work orders, no MRP.
- **HR / payroll modules** — No employee management, leave, or payroll.
- **Real supplier portal or customer portal** — No external-facing login for suppliers or customers.
- **Real e-commerce storefront** — No public product catalog or shopping cart.
- **Mobile app implementation** — Documented as future/optional scope only.
- **Real translations** — Translation keys are documented; actual translation files are out of scope.
- **Real financial reporting (P&L, balance sheet)** — Finance documents are placeholders only.

---

## Future Scope

The following are documented as potential future extensions but are not part of this reference:

- **Mobile warehouse flows** — Using [Mobile App Pack](../../../extensions/mobile-app-pack/README.md) for receiving and stock count on mobile devices.
- **SaaS Multitenant extension** — Multi-organization support using [SaaS Multitenant Pack](../../../extensions/saas-multitenant-pack/README.md).
- **Advanced reporting** — Pivot tables, scheduled report exports, email delivery.
- **Full RTL/i18n implementation** — Full translation files, locale switching in production.
- **Supplier self-service portal** — External portal for suppliers to submit invoices.
- **Customer self-service portal** — External portal for customers to view orders and invoices.
- **Real payment processing** — Integration with a payment provider for actual payment flows.
- **Barcode and RFID scanning** — Hardware-integrated stock operations.

---

## Related Files

- [01-product-brief.md](01-product-brief.md) — Product goals that drive this scope
- [04-user-roles.md](04-user-roles.md) — Role boundaries that constrain scope
- [11-development-roadmap.md](11-development-roadmap.md) — Staged delivery of this scope
- [14-decision-log.md](14-decision-log.md) — Scope decisions recorded here
