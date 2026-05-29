# Extension Pack: Print & Reporting

> Adds PDF generation, print layout specifications, and report template documentation.

---

## When to Use This Pack

Use this extension pack when your product:

- Needs to **generate PDF documents** (invoices, receipts, reports, certificates)
- Requires **print-friendly layouts** for specific pages
- Has **report templates** with specific formatting requirements
- Needs **bulk report generation** (e.g., monthly statements for all users)
- Must produce documents that match **legal or business formatting standards**

---

## What This Pack Will Add (When Built)

### Additional Documents

| Document | Purpose |
|----------|---------|
| `report-template-spec.md` | Template definitions for each report type (layout, fields, formatting) |
| `pdf-generation-guide.md` | Technology choices, rendering rules, and performance considerations |
| `print-layout-spec.md` | Print-specific CSS, page breaks, margins, and header/footer rules |
| `bulk-generation-spec.md` | Queue-based generation for large batches, progress tracking |

### Additional Prompts

| Prompt | Purpose |
|--------|---------|
| `report-engineer-prompt.md` | AI agent role for implementing print/PDF generation features |

### Additional Guardrails

- Report outputs must match the template spec exactly
- Print layouts must be tested on actual printers (or print preview)
- PDF generation must handle edge cases (empty data, long text, many pages)
- Bulk generation must not block the main application
- Reports must respect data scoping — users see only their authorized data

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| Reports look wrong in print | Print layout spec with explicit formatting |
| PDF generation crashes on edge cases | Edge case documentation and testing requirements |
| Bulk generation overloads server | Queue-based generation spec |
| Report data doesn't match screen data | Data consistency rules |
| Sensitive data leaks via reports | Data scoping enforcement in report generation |

---

## Example Project Types

- Invoicing systems (PDF invoices)
- HR platforms (employee reports, pay stubs)
- Educational platforms (certificates, transcripts)
- Healthcare (patient summaries)
- Legal document management
- Financial reporting dashboards

---

## Status

> **Status: Placeholder / Planned Future Pack**
>
> This extension pack is currently a **placeholder**. The folder contains only this README. Full templates, prompts, and instructions will be added in a future version.
>
> **Core Governance Rule:** Extension packs are optional and exist to **supplement** core documents for specific product needs — they do **not** replace core documents.
>
> For workspace setup instructions and core rules, link back to [START_HERE.md](../../START_HERE.md).
