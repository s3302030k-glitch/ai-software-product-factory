# 20 — Print & Reporting Notes

> How the Print & Reporting Pack applies to the Integrated Operations ERP documentation reference.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> See the [example README](../README.md) for full context.
> Extension pack reference: [print-reporting-pack README](../../../extensions/print-reporting-pack/README.md)

---

## Pack Application Summary

The [Print & Reporting Pack](../../../extensions/print-reporting-pack/README.md) governs how print layouts, PDF document placeholders, data exports, and report consistency are documented in this ERP reference. No real PDF generation library is configured — all print and export documents are placeholder layouts only.

> [!WARNING]
> No PDF generation library, no print rendering engine, and no export pipeline is implemented in this documentation reference. All print and export documents are conceptual placeholders. No document produced from this reference constitutes a legal invoice, a legally binding purchase order, or a regulated financial statement.

---

## Purchase Order Placeholder PDF

The Invoice and Contract Document Guidelines from the Print & Reporting Pack apply to the PO PDF placeholder:

**Conceptual PO PDF layout:**
- Header: "PURCHASE ORDER — DOCUMENTATION PLACEHOLDER"
- PO number placeholder (e.g., "PO-2026-0042")
- Supplier placeholder name and contact placeholder
- Issuing department placeholder
- Line items table: SKU code, description, quantity, unit price placeholder
- Total amount placeholder (display value only)
- Expected delivery date placeholder
- Issued by user placeholder
- Footer: "This document is a documentation reference placeholder. Not a legally binding purchase order."

**Pack Rules Applied:**
- Page dimensions must be defined (A4 or Letter — TBD in implementation phase).
- Table rows must not split across page breaks.
- Footer with "DOCUMENTATION PLACEHOLDER" watermark note is mandatory.
- PDF file naming convention placeholder: `PO-{po_number}-{date}.pdf`

---

## Invoice PDF Placeholder

**Conceptual Invoice PDF layout:**
- Header: "INVOICE — DOCUMENTATION PLACEHOLDER"
- Invoice number placeholder (e.g., "INV-2026-0033")
- Invoice type: Purchase or Sales
- Supplier/Customer placeholder name
- Related order reference placeholder
- Line items table: description, quantity, unit price placeholder, line total placeholder
- Subtotal placeholder, tax placeholder (labeled "N/A — not implemented"), total placeholder
- Due date placeholder
- Payment status placeholder
- Footer: "This document is a documentation reference placeholder. Not a legal or tax invoice."

**Pack Rules Applied:**
- No real tax amount, no VAT/GST line, no tax registration number.
- Currency display label included (e.g., "USD") — not a real currency conversion.
- Invoice PDF is never regenerated after payment is recorded (version immutability concept).

---

## Stock Movement Report Export

The Export Guidelines from the Print & Reporting Pack apply:

**Stock Movement Report (CSV export placeholder):**
- Columns: Movement ID, Date, SKU Code, SKU Name, Movement Type, Source Zone, Destination Zone, Quantity, Actor, Reference ID, Notes
- Filters: date range, warehouse, zone, SKU, movement type
- All column headers in English (i18n-ready column key structure documented in [22-rtl-i18n-notes.md](22-rtl-i18n-notes.md))
- CSV delimiter: comma (`,`) — locale-specific delimiter handling documented but not implemented
- Empty dataset: exports with header row and no data rows (not an error)

**Pack Rules Applied:**
- Report data sourced from authoritative `StockMovement` records — not from UI display state.
- UI totals and export totals must match (reconciliation rule).
- Export events logged as AuditEvents.

---

## Operational Report Export Placeholders

**Purchase Order Summary Report:**
- Columns: PO Number, Supplier Name, Status, Line Item Count, Total Amount Placeholder, Expected Delivery Date
- Filters: date range, status, supplier

**Sales Order Summary Report:**
- Columns: Order Number, Customer Name, Status, Line Item Count, Total Amount Placeholder, Dispatch Status
- Filters: date range, status, customer

**Finance Summary Report (Finance Officer only):**
- Columns: Invoice Number, Type, Related Order, Amount Placeholder, Status, Payment Status
- Filters: date range, invoice type, status

**Pack Rules Applied:**
- All report exports filtered to user's operational scope.
- Finance reports restricted to Finance Officer and Operations Director.
- Export PDF placeholder: paginated layout with header, footer, page number, and generation timestamp.

---

## Print / Export Consistency

The Report Data Reconciliation Guidelines from the Print & Reporting Pack apply:

- **Source of truth:** All report and export data must be drawn from the same authoritative data layer as the UI display.
- **No client-side recalculation:** Totals shown in exports must equal totals computed from stored records — not recalculated from UI state.
- **Timezone consistency:** All timestamps in reports use a single defined timezone (to be defined in implementation phase — UTC is the recommended default).
- **Filter parameters logged:** All applied filters are included in the report metadata so exported data can be reproduced.

---

## Source-of-Truth Notes

- Stock movement export values must reconcile with `StockBalance.current_balance` derived figures.
- Invoice export amounts must match `InvoicePlaceholder.amount_placeholder` stored values.
- Any discrepancy between UI display and export values is a documentation bug — report using [16-bug-report-template.md](16-bug-report-template.md).

---

## No Legal Invoice / Accounting Claim

> [!WARNING]
> No PDF, CSV, or report export from this system constitutes a legally binding invoice, a tax document, a VAT receipt, a purchase contract, an accounting statement, or any regulated financial instrument. All export documents are documentation reference placeholders only.

---

## Related Files

- [09-api-design.md](09-api-design.md) — Reports API group (API Group 11)
- [06-pages-spec.md](06-pages-spec.md) — Reports page and Finance pages
- [19-financial-business-logic-notes.md](19-financial-business-logic-notes.md) — Invoice/payment placeholder rules
- [../../../extensions/print-reporting-pack/README.md](../../../extensions/print-reporting-pack/README.md) — Source pack
