# Print and Reporting QA Checklist

> Comprehensive pre-release QA matrix and validation test templates for page printing, PDF reports, exports, and document reconciliation.

This document supplements the core product specs and QA test plan. It does not replace them.

---

## Purpose

Define the mandatory testing processes and QA standards for print views, generated files, and reporting outputs before release. This ensures all formats display correctly, respect user permissions, and present accurate, reconciled data.

## Status

`Active` — This checklist must be executed and passed for any release containing print, PDF, reporting, or data export changes.

---

## Print Layout QA Checklist

- [ ] **Interactive Elements Hidden**: Confirm navigation bars, sidebars, buttons, tooltips, and scrollbars are stripped from printed layouts.
- [ ] **Color Contrast**: Verify background fills are white or light, text is dark grey/black, and colored elements print legibly in black-and-white previews.
- [ ] **Table Pagination**:
  - [ ] Repeated Headers: Verify table headers (`<thead>`) repeat at the top of page 2+.
  - [ ] Row Splitting: Verify table rows do not break in half across page boundaries.
- [ ] **Orphan Control**: Confirm headings (`h1`, `h2`, `h3`) are not orphaned at the bottom of pages.
- [ ] **Signature Placement**: Confirm signature/approval fields are kept together with preceding content blocks and never appear on a page alone.

---

## PDF QA Checklist

- [ ] **Page Bounds & Safe Areas**: Verify headers, body text, tables, and footers do not overlap margins or bleed into safe zones.
- [ ] **Page Numbering**: Verify page numbers display as `Page X of Y` on all pages and are centered/aligned consistently.
- [ ] **Dynamic Content Overflows**: Test with maximum text limits (long names, description fields) to ensure lines wrap rather than clipping or breaking layouts.
- [ ] **Multi-Page Overflow**: Test at least one long multi-page report to verify correct formatting.
- [ ] **Timestamping & Metadata**: Confirm the generation timestamp, UTC offset, user ID, parameters list, and unique UUID are present.

---

## Export QA Checklist

- [ ] **CSV Delimiter Behavior**:
  - [ ] Test in LTR English (using commas `,`).
  - [ ] Test in German/European locales (confirm CSV delimiters mirror to semicolons `;` when decimal numbers use commas `,`).
- [ ] **Data Type Integrity**:
  - [ ] Numeric Preservation: Confirm money cells are readable as numbers and can be used in `=SUM()` calculations directly in Excel.
  - [ ] Date Interpretation: Verify dates are recognized by Excel (usable in filters and date sorting).
  - [ ] Leading Zeros: Confirm postal codes or telephone numbers do not lose leading zeros.
- [ ] **No Hidden Columns**: Verify no internal database UUIDs, system status values, or tenant variables are leaked unless explicitly requested.

---

## Invoice/Contract/Statement QA Checklist

- [ ] **Stable Numbering**: Verify document serial numbers increment correctly and do not collide across threads.
- [ ] **Voiding Watermark**: Verify that cancelling an invoice renders the "VOID" watermark overlay.
- [ ] **Revisions**: Confirm amended contracts display revision logs linking back to the source records.
- [ ] **Boundary Verification**: Ensure disclaimers regarding legal and tax boundaries are correctly displayed in templates.

---

## Reconciliation QA Checklist

- [ ] **Dashboard vs. Export Mappings**: Verify that data counts and aggregates displayed on dashboards match CSV downloads and PDF totals exactly under identical filter parameters.
- [ ] **Re-run Alignment**: Test live reports to verify they show data updates, and check that snapshot reports remain completely immutable once run.
- [ ] **Calculation Checks**: Cross-verify:
  `Opening Balance + Credits - Debits == Closing Balance`
- [ ] **No Recalculation Drift**: Verify that PDF generators do not implement separate calculation formulas from the core database records.

---

## RTL/i18n Print QA Checklist

- [ ] **Mirror Alignment**: Verify layout grids mirror correctly in RTL languages (logo, tables, header tags).
- [ ] **Font Glyphs**: Check that Arabic/Hebrew/Persian characters display cleanly without truncation, broken ligatures, or question marks (`?`).
- [ ] **Mixed Direction Handling**: Verify that English strings (like phone numbers, email addresses, or transaction codes) print legibly inside RTL pages.

---

## Permission/Privacy QA Checklist

- [ ] **Tenant Boundaries**: Verify that a user cannot generate or download reports containing another tenant's data.
- [ ] **Secure Storage Pre-signing**: Verify that PDF downloads generate secure, short-lived URLs.
- [ ] **Audit Trail Entries**: Confirm every export action generates a permanent, immutable record in the download audit logs.

---

## Browser/Device QA Checklist

- [ ] **Browser Testing**: Test print-preview functions in Chrome, Safari, and Firefox.
- [ ] **PDF Engine Verification**: Check that generated PDFs render consistently across Adobe Acrobat, Google Chrome, and mobile PDF readers.

---

## Regression Checklist

- [ ] Verify that changes to print/export functions do not disrupt general screen layouts.
- [ ] Verify that performance limits (bulk export requests) do not lock database connections or block user traffic.

---

## Bug Report Format

Use this format for reporting print/reporting bugs:

```markdown
### Bug Report: [Short Title]

- **Environment**: [Browser / PDF engine / Locale]
- **Document / Export Type**: [e.g. Invoice / Transaction Export]
- **Steps to Reproduce**:
  1.
  2.
- **Expected Behavior**:
- **Actual Behavior**:
- **Reconciliation Impact**: [Does this affect totals/numbers?]
- **Screenshots / Files Attached**:
```

---

## Release Readiness Checklist

- [ ] 1. All automated build, lint, and unit checks pass.
- [ ] 2. Print views are verified across Letter and A4 previews.
- [ ] 3. Export delimiter changes are verified in standard locales.
- [ ] 4. PDF templates pass page-break and overflow checks.
- [ ] 5. Data values are reconciled back to source transactional logs.
- [ ] 6. Human owner has approved the document template formats.

---

## Related Files

- [12 — QA Test Plan](../../../core/docs/12-qa-test-plan.md) — Base test protocols.
- [13 — Release Checklist](../../../core/docs/13-release-checklist.md) — Release approval steps.
- [print-layout-guidelines.md](print-layout-guidelines.md) — Sibling layout rules.
- [report-data-reconciliation-guidelines.md](report-data-reconciliation-guidelines.md) — Data reconciliation rules.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial QA checklist implementation | AI Software Product Factory |
