# 21 — Print and Reporting Notes: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document specifies printable page specifications, export layout configurations, and data reconciliation rules for the Team Subscription Manager, in alignment with the **[Print & Reporting Pack](../../../extensions/print-reporting-pack/README.md)**.

> [!WARNING]
> **NO OFFICIAL FINANCIAL OR LEGAL CLAIM**: These templates and reports represent conceptual placeholders. Generated outputs must be audited by certified tax and compliance professionals before use as official corporate statements.

---

## 1. Printable Page Layout (PDF Placeholder)
- **CSS Media Queries**: PDF generation relies on standard browser print rendering via `@media print` rules.
- **Hidden UI Elements**: Sidebars, top navigation bars, interactive buttons, and theme togglers must be set to `display: none;` during print mode.
- **Margins & Sizing**: Uses standard margins (`margin: 20mm;`) and explicit page-break directives:
  - `page-break-inside: avoid;` on invoice details and signature blocks.
  - `page-break-after: always;` on multi-page summary cards.

---

## 2. CSV & Excel Export Guidelines
- **Tenant Isolation Safeguard**: The query assembling export data must filter records utilizing the active tenant key `organization_id` inside database JOIN routines.
- **Formatting Conversions**: Stored integers (representing cents) must be formatted back into standard decimal strings (e.g. `15.00`) before CSV assembly.
- **Delimiter Localization**: Exports must support dynamic character separation (e.g., standard comma `,` for US/UK locales, and semicolon `;` for European locales where comma is a decimal separator).

---

## 3. Data Reconciliation Rules
- **Consistency Check**: Values displayed on UI dashboards must match the numbers contained in generated CSV exports and PDF invoices.
- **Source of Truth**: Live active user list counts are the source of truth. Snapshots (`SeatUsageSnapshot`) are generated at the end of each day to freeze daily stats. Report queries must use these snapshot numbers rather than live recalculations to prevent date-shifting errors.

---

## Related Files

- [06-pages-spec.md](06-pages-spec.md) — Invoice details view specs.
- [12-qa-test-plan.md](12-qa-test-plan.md) — QA print validation steps.
- [20-financial-business-logic-notes.md](20-financial-business-logic-notes.md) — Subunit storage guidelines.
