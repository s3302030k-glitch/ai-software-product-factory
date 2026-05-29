# Operational Reporting Guidelines

> Defines rules for building inventory, warehouse, and workflow reports, ensuring data reconciliation, parameter consistency, and alignment with print and export standards.

---

## Purpose

This document outlines standard guidelines for designing and building operational dashboards, summaries, and reports. It prevents reporting discrepancies and ensures numbers presented in reports match underlying transaction records. It supplements the reporting rules in [extensions/print-reporting-pack/README.md](../../print-reporting-pack/README.md) and relates to [inventory-and-stock-guidelines.md](inventory-and-stock-guidelines.md).

## Status

`Active` — Must be referenced during the implementation of dashboards, data exports, and PDF reports.

---

## Operational Reporting Principles

### 1. Reconciliation Against Source Records
Operational reports must reconcile with the underlying transaction logs (source of truth).
- **Inventory Reports**: Must reconcile directly with the historical sum of stock movements.
- **Financial Values**: Must reconcile with invoice ledger logs (aligned with [financial-business-logic-pack](../../financial-business-logic-pack/README.md)).
- **Rule**: If a report displays cached or aggregated numbers that deviate from transaction-level sums, the system must trigger a validation warning.

### 2. Explicit Time Range and Timezone Boundaries
To prevent midnight-cutoff reporting errors, all operational reports must:
- Declare the exact timezone offset used for query grouping (e.g., UTC, local system time).
- Use explicit date boundaries (e.g., `>= 2026-05-01 00:00:00+00` and `< 2026-06-01 00:00:00+00` instead of a vague "May 2026").
- Display the query execution timestamp and timezone on the report layout.

### 3. Parameter and Filter Consistency
Reports must print, display, or log all filters that affect the totals shown (e.g., "Filtered by Zone: Cold Storage, Status: Available").
- **No Hidden Filters**: Never apply silent default exclusions (such as excluding damaged stock from inventory valuations) without displaying a notification.

---

## Operational Report Types

### 1. Inventory Reports
- **On-Hand Valuation**: Displays quantities in stock, cost valuation per location, and total assets.
- **Stock Movement Log**: Audit log of historical additions, subtractions, and adjustments.
- **Low Stock/Reorder Report**: Shows items whose `Available` quantity is below the reorder point.

### 2. Warehouse Reports
- **Receiving Mismatch Report**: Highlights discrepancies between purchase order quantities and received quantities.
- **Shipment Lead Time Report**: Tracks elapsed time between pick list generation, packaging, and dispatch.
- **Location Utilization Report**: Displays empty vs. populated bins in different warehouse zones.

### 3. Workflow Reports
- **Pending Approvals Summary**: List of documents waiting for authorization, categorized by elapsed waiting time.
- **Rejection Log**: Tracks rejected documents and common rejection reasons.

### 4. Exception Reports
- **Audit Discrepancy Log**: Logs automated daily reconciliation mismatches.
- **Damaged Goods/Write-Off Report**: Summarizes inventory lost to damages or shrinkage.

---

## Live vs. Snapshot Operational Reports

- **Live Reports** (e.g., Warehouse Dispatch Queue, Live Inventory Count):
  - Used for real-time operations.
  - Queries active tables directly.
  - Must not degrade system performance during high transaction volume.
- **Snapshot Reports** (e.g., Monthly Inventory Valuation, EOD Reconciliation):
  - Used for administrative audits and records.
  - Saved as frozen records in the database (or as static exports/PDFs).
  - Contain immutable historical snapshots of a specific timestamp.

---

## Export, Print, and Reporting Alignment

When reports are exported to CSV/Excel or printed as PDFs, they must align with the standards defined in [print-reporting-pack](../../print-reporting-pack/README.md):
- **Precision Matching**: Aggregated values on the screen dashboard must match the exported file totals down to the last decimal place.
- **Formatting Constraints**: Large tables must handle page breaks gracefully in PDFs without truncating columns.
- **CSV Formats**: Standardize on CSV format conventions, matching localized decimal separators to avoid parse failures.

---

## Out of Scope

- Designing specific styling sheets, fonts, or charts for dashboards.
- Integrations with external BI analytics tools (e.g., Tableau, PowerBI).
- Automated email subscription dispatch cron setups.

---

## Guardrails

- [ ] **RECONCILIATION GATE**: Reports must reconcile with the underlying transaction tables; discrepancies must be highlighted.
- [ ] **EXPLICIT FILTER DISPLAY**: All filters affecting calculations must be printed on the output report.
- [ ] **TIMEZONE TRANSPARENCY**: Reports must display the generation timezone and execution range.
- [ ] **NO HIDDEN EXCLUSIONS**: Damaged or blocked stock exclusions must be explicitly declared on inventory totals.

---

## QA Checklist

- [ ] Verify that the sum of quantities in the stock movement ledger matches the total stock reported on the inventory valuation report.
- [ ] Test reporting with different timezone offsets and confirm that the correct date boundaries are applied to transactions.
- [ ] Export the inventory valuation report to CSV and verify that the column totals match the UI dashboard exactly.
- [ ] Check that generating a report displays all active filters (e.g., warehouse, zone, product category).

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Core database schema layout.
- [09-api-design.md](../../../core/docs/09-api-design.md) — Standard reporting API endpoints guidelines.
- [inventory-and-stock-guidelines.md](inventory-and-stock-guidelines.md) — Inventory calculation source rules.
- [README.md](../README.md) — ERP Extension Pack README.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial operational reporting guidelines for ERP Operations Pack | Antigravity |
