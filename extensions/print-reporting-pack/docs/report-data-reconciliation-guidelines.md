# Report Data Reconciliation Guidelines

> Rules and validation checklists for ensuring data consistency and reconciliation between databases, UI screens, PDF reports, and data exports.

This document supplements the core product specifications, data models, and QA test plans. It does not replace them.

---

## Purpose

Define validation standards for verifying that reports and data exports reflect accurate, consistent numbers matching the underlying source-of-truth records. This document prevents reporting discrepancies, alignment drift, and calculation mismatches across system views.

## Status

`Active` — These rules apply to all dashboard, reporting, PDF document, and data export development.

---

## Reconciliation Principles

1. **Reconciliation is Mandatory**: All totals, counts, aggregates, and financial summaries presented on screens, PDFs, or spreadsheet downloads must be traceably reconcilable back to primary transaction ledgers or audit logs.
2. **Matching Output Rules**: Frontend views (UI), generated PDFs, and CSV/Excel exports must present identical values when run under the same filter parameters and timestamps, unless differences are explicitly documented and expected (e.g., timezone offsets).
3. **No Dynamic Logic in Views**: UI elements and document templates must display computed values passed from secure backend queries; they must not recalculate values locally.

---

## Source-of-Truth Rules

- The source of truth for any reporting indicator must be clearly defined in the [07 — Data Model](../../../core/docs/07-data-model.md) (e.g., "Ledger table for balances").
- Reports must never calculate totals using draft, soft-deleted, or unverified records unless those categories are explicitly checked and labeled in the report metadata.

---

## UI vs. Report vs. Export Matching

- **Visual Alignment**: A user downloading a report must get the exact same figures shown in the dashboard table.
- **Verification Routine**: QA agents must cross-check numbers:
  `UI Total == PDF Total == Export Total == Raw SQL Query Sum`
- **Exceptions Mapping**: If a discrepancy exists (e.g., due to background synchronization lags), the UI must display a clear warning about data freshness.

---

## Stored Totals vs. Recalculated Totals

- **Financial Invariance**: Financial reports must draw values from closed ledger transactions rather than dynamically calculating values. Do not recalculate historical billing totals from live pricing tables, as pricing rules may have changed.
- **Immutable Postings**: Once a total is posted (e.g., an invoice total or month-end balance), it must be stored as an immutable attribute in the database, and reports must reference that stored total directly.

---

## Financial Report Reconciliation

- **Ledger Controls**: Total debits must equal total credits in double-entry systems.
- **Rounding Adjustment Columns**: Explicitly report rounding differences (e.g., half-cent discrepancies) as separate line items in financial summaries rather than silently adjusting transaction values.
- **Validation Controls**: Check for:
  `Opening Balance + Sum(Credits) - Sum(Debits) == Closing Balance`

---

## Operational Report Reconciliation

- **Audit Metrics**: Operational reports (e.g. inventory logs, shipment counts) must reconcile by checking:
  `Starting Stock + Units Received - Units Shipped == Ending Stock`
- **Validation**: Any inventory discrepancies must be flagged with adjustment references.

---

## Time Range and Timezone Boundaries

- **Timezone Invariance**: Reports must display the timezone offset used for date bounds (e.g., "Report Period: 2026-05-01 to 2026-05-31 (UTC)").
- **Boundary Precision**: Define whether date ranges are inclusive of start and end times (e.g., `2026-05-01 00:00:00.000` to `2026-05-31 23:59:59.999`).
- **Client Offset Handling**: If a user runs a report using their local time (e.g. UTC+3), the backend must query boundaries using matching timezone conversions, and print the conversion offset on the PDF cover page.

---

## Filter and Parameter Consistency

- Parameters applied to queries must map exactly to UI filters.
- A report must never hide parameters that influence its totals (e.g., excluding inactive users or pending records). Any active hidden filters must be noted in the report header/footer.

---

## Snapshot vs. Live Report Distinction

- **Live Reports**: Query active transactional tables directly. Totals will change as new records are added. Useful for operational monitoring. Must indicate "Live Data as of YYYY-MM-DD HH:MM:SS".
- **Snapshot Reports**: Query frozen summary tables or point-in-time snapshots. Totals are fixed. Mandatory for financial statements, month-end closures, and tax records. Must indicate "Snapshot Date: YYYY-MM-DD".

---

## Audit Trail and Regeneration Rules

- Keep a history log of when reports are run.
- If a snapshot report must be regenerated, log:
  1. Original report ID.
  2. Regeneration reason.
  3. Authorizer.
  4. Differences in totals (if any).
  - (See sibling [pdf-report-guidelines.md](pdf-report-guidelines.md)).

---

## Out of Scope

- Implementing backend data reconciliation scripts or batch auditing workers.
- Configuring database replication or data warehousing synchronization schedules.
- Writing data aggregation SQL scripts.

---

## Guardrails

- [ ] Reports must not hide active filter variables that modify totals.
- [ ] Financial totals must match source ledger logs exactly and must not be recalculated using frontend methods.
- [ ] Timezone offsets and date range boundaries must be printed on all reporting outputs.
- [ ] UI totals, generated PDFs, and downloaded CSV/Excel exports must align perfectly under the same parameters.

---

## QA Checklist

- [ ] Verify that UI dashboard indicators match Excel export columns.
- [ ] Confirm that running the same report in different client timezones queries correct date boundaries.
- [ ] Test calculation edge cases: verify that rounding rules do not create 0.01 discrepancies on PDF statements.
- [ ] Confirm that hidden filters (e.g., excluding test accounts) are explicitly documented on reports.

---

## Related Files

- [07 — Data Model](../../../core/docs/07-data-model.md) — Base data structures.
- [12 — QA Test Plan](../../../core/docs/12-qa-test-plan.md) — Verification protocols.
- [pdf-report-guidelines.md](pdf-report-guidelines.md) — PDF rendering rules.
- [export-guidelines.md](export-guidelines.md) — Spreadsheet download rules.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial guidelines implementation | AI Software Product Factory |
