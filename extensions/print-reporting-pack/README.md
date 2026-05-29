# Extension Pack: Print & Reporting

> Adds PDF generation, print layout specifications, export design, and data reconciliation audit templates.

This is a fully implemented extension pack, supplementing the core factory templates.

---

## Purpose and Scope

This pack **supplements** the core factory documents; it does **not** replace them. It is designed for software products that require:
- **Printable page layouts** configured via CSS media queries or browser print setups.
- **PDF report generation** (e.g., automated summaries, statements, certifications).
- **Official documents** such as invoices, contracts, receipts, and account statements.
- **Data exports** in CSV, Excel (XLSX), or JSON formats.
- **Consistency and reconciliation rules** between UI dashboards, exported files, and generated PDF reports.

This pack is useful for SaaS dashboards, ERP systems, finance modules, invoicing apps, CRM exports, operational logs, logistics/warehouse management, and executive/regulatory reporting.

This pack is strictly template-based and generic. It does not contain product-specific details, private business information, real translations, real customer data, real credentials, real contract texts, or real invoices.

> [!WARNING]
> **NO LEGAL, TAX, ACCOUNTING, OR REGULATED REPORTING ADVICE**: This extension pack does not provide legal, tax, accounting, or regulated reporting advice. All layout designs, statement parameters, contract layouts, and export criteria must be audited and approved by the product owner's legal, compliance, and tax advisors before production use.

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| **UI Totals Mismatched from Exports** | Establishes strict data reconciliation rules between stored numbers, frontend views, and exported files. |
| **PDF Layout Breakage** | Defines page dimension boundaries, safe margins, and responsive rules for PDF rendering tools. |
| **Cut-off Tables or Signatures** | Sets table pagination rules and page-break requirements to prevent split signatures or half-hidden rows. |
| **Missing Headers, Footers, or Page Numbers** | Mandates consistent page metadata structures, including dynamic page counters and generation timestamps. |
| **Locale Formatting Errors** | Imposes rules for currency, date, and number localized presentation in report layers. |
| **RTL Print Failures** | Guides directional text alignment, mirroring, and font choices for Hebrew, Arabic, Persian, etc. |
| **Unreconciled Source Data** | Requires audit-trail reconciliation verification checking before outputs are released. |
| **CSV Delimiter/Decimal Clashes** | Standardizes column delimiter behavior in European and other locales using comma as a decimal. |
| **Version Drift on Re-generation** | Enforces revision tagging and immutability controls on official printed files. |
| **Hidden Business Logic** | Pulls reporting logic out of UI views into testable, dedicated query and calculation modules. |

---

## Pack Components

### Documentation Guidelines (`docs/`)

- [Print Layout Guidelines](docs/print-layout-guidelines.md) — Sizing, margins, safe areas, page breaks, typography, and RTL layouts.
- [PDF Report Guidelines](docs/pdf-report-guidelines.md) — Static/dynamic rendering rules, template versioning, access control, and generation tracking.
- [Export Guidelines](docs/export-guidelines.md) — CSV/Excel formatting rules, delimiter localizations, large data pagination, and download logs.
- [Invoice and Contract Document Guidelines](docs/invoice-contract-document-guidelines.md) — Metadata structures, serial numbering, amendment marking, formatting, and legal disclaimers.
- [Report Data Reconciliation Guidelines](docs/report-data-reconciliation-guidelines.md) — Source-of-truth alignment, live vs. snapshot values, timezone offsets, and filter parameters consistency.
- [Print & Reporting QA Checklist](docs/print-reporting-qa-checklist.md) — Testing plan for long reports, export formatting, permissions, device previews, and totals reconciliation.

### AI Agent Role Prompts (`prompts/`)

- [Print and Reporting Architect](prompts/print-report-architect-prompt.md) — Audits and designs print/export schemas and data-gathering workflows.
- [PDF Layout Review Agent](prompts/pdf-layout-review-agent-prompt.md) — Audits rendered documents, page-breaking, safe areas, and RTL text layout rules.
- [Export Reconciliation Review Agent](prompts/export-reconciliation-review-agent-prompt.md) — Verifies exported datasets against database models and frontend values.
- [Print and Reporting QA Agent](prompts/print-reporting-qa-agent-prompt.md) — Conducts pre-release checklist validation of printable pages, PDFs, and exports.

---

## Recommended Usage

Follow these steps to integrate this extension pack into your product project:

1. **Initialize Core Kit First**: Copy the core factory documents (`core/docs/`) and prompt templates (`core/prompts/`) into your product project workspace.
2. **Apply Print & Reporting Pack**: Copy this pack's folders (`docs/` and `prompts/`) into your project *only* if printable layouts, PDF generation, or structured CSV/Excel downloads are required features.
3. **Add to Product Documentation**: Merge the guidelines from `docs/` directly into your active product design documentation.
4. **Use Prompts in Dev & QA Cycles**: Assign specialized prompts to your AI agents to guide print layout design, export query logic, and reconciliation testing before code merges.
5. **Enforce Governance Gatekeeping**: Never release official reports, contracts, or tax documents without setting up automated reconciliation against source records and receiving human owner review and sign-off.

For workspace setup instructions and core governance rules, link back to [START_HERE.md](../../START_HERE.md).
