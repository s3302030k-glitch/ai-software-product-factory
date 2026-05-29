# PDF Report Guidelines

> Rules and guidelines for generating programmatic PDF reports, maintaining design standards, ensuring data consistency, and protecting document privacy.

This document supplements the core product specifications and security models. It does not replace them.

---

## Purpose

Define the standards for backend and programmatic PDF report generation. This document ensures that generated PDFs look professional, are secure, run on correct data snapshots, and reconcile perfectly with source system records.

## Status

`Active` — These guidelines apply to all programmatic PDF generation features.

---

## PDF Report Principles

1. **Snapshots are Final**: Once a PDF is generated as an official record (e.g., invoices, closing statements, tax summaries), the content must reflect a frozen point-in-time snapshot. It must not dynamically change if database values are edited later.
2. **Reconciliation Integrity**: PDF report generation engines must use the same underlying calculations, currency schemas, and rounding rules as the UI and database. PDF engines must never independently calculate business or financial logic.
3. **Traceability by Default**: Every generated report must clearly display its data sources, parameter inputs, and generation metadata.

---

## Static vs. Dynamic PDF Generation

- **Static PDFs (Official Documents)**: Generated once, stored in a secure storage bucket, and referenced via a database ID (e.g. an invoice or contract). They are immutable. Any correction requires issuing a new version (e.g. Credit Note or Amendment).
- **Dynamic PDFs (Ad-hoc Reports)**: Generated on the fly based on current filters (e.g., date ranges, status filters) and are not stored long-term. They reflect the live state of the database at the moment of request.

---

## Template Ownership

- PDF layout templates should be versioned alongside the code.
- If templates are stored in a database (e.g., HTML/JSON schemas), they must be audited and revision-controlled.
- Modifications to core templates for official documents require compliance sign-off.

---

## Data Source Rules

- Every report must identify its data sources in the metadata footer or header (e.g., "Source: Transaction Ledger").
- If the report aggregates data from multiple sources (e.g., database, third-party APIs), the status and synchronization state of each source at the time of generation must be logged.

---

## Report Parameter Rules

- The exact parameters used to run the report (e.g., date range, department filter, tenant ID) must be printed on the cover page or metadata block of the PDF.
- A report must never hide the filters or parameters that modify its totals.

---

## Header/Footer/Page Metadata

Every PDF report must include:
- **Title and Subtitle**: Describing the scope of the data.
- **Generation Timestamp**: The exact date, time, and timezone offset of when the file was compiled.
- **Generator ID**: The ID or email of the actor (human or system background task) that requested the report.
- **Page Numbers**: Format `Page X of Y` at the bottom of every page.
- **Document UUID**: A unique ID linking the physical document to its database log.

---

## Table and Chart Rendering Rules

- **Tables**: Follow the pagination and layout rules in [Print Layout Guidelines](print-layout-guidelines.md). Ensure columns do not overflow the right margin.
- **Charts**: Use vector graphics (SVG) or high-resolution PNG exports. Avoid charts with dark background elements or complex legends that bleed in greyscale printing.
- **Legends**: Always output raw data tables alongside charts, in case color charts are printed on monochrome printers.

---

## Long Content and Page Break Rules

- **Overlapping Prevention**: Content blocks must not overlap page headers or footers.
- **Orphan Prevention**: Prevent headers from printing alone at the bottom of a page.
- **Table Breaking**: Apply page-break avoidance to rows, keeping rows intact and pushing complete rows to subsequent pages.

---

## Attachments and Embedded Assets

- **Signature Images**: Keep signature graphics locked to a maximum size (e.g., 200px wide) and ensure they cannot bleed into other page elements.
- **Sub-reports**: Attachments or referenced files must be appended logically, with their page numbers continuing from the parent document or distinctly marked as Annexes.

---

## Versioning and Regeneration Rules

- Official PDFs must never be silently overwritten in storage.
- If a report must be regenerated:
  1. Maintain the original file in the archive.
  2. Write a new revision log with an incremented revision number (e.g. Rev 2).
  3. Include a reason for regeneration and track the actor who authorized it.
  4. Ensure the UI points to the latest approved version while retaining access to historical revisions.

---

## Access Control and Document Privacy

- **Scoping Boundaries**: PDF generation queries must enforce the same user role restrictions defined in [04 — User Roles](../../../core/docs/04-user-roles.md). Users must never generate reports containing data outside their tenant or organizational boundary.
- **Secure Storage**: Save official PDFs in private storage buckets. Access links must be short-lived, pre-signed URLs. Do not expose public URLs to sensitive PDFs.
- **Metadata Stripping**: Strip sensitive internal server information or raw system credentials from PDF metadata fields before generation.

---

## Out of Scope

- Writing code to integrate specific PDF libraries (e.g., pdfkit, puppeteer, dompdf).
- Managing storage bucket configurations or CDN distribution networks.
- Defining specific legal disclaimer texts.

---

## Guardrails

- [ ] PDF generation must not recalculate financial totals using a different algorithm than the source-of-truth database records.
- [ ] Every generated PDF must show the generation timestamp and a unique document ID.
- [ ] No sensitive PDF may be exposed via a public URL. All file links must be authenticated and expire.
- [ ] If a document is regenerated, the original must remain archived for compliance auditing.

---

## QA Checklist

- [ ] Verify that headers and footers do not overlap the main content on pages 2+.
- [ ] Verify that the document title, timestamp, parameters, and UUID are rendered correctly.
- [ ] Verify that financial figures match the UI and exports exactly.
- [ ] Verify that a user cannot access another user's or tenant's PDF report URL.

---

## Related Files

- [07 — Data Model](../../../core/docs/07-data-model.md) — Source schemas for report data.
- [10 — Security Model](../../../core/docs/10-security-model.md) — Permissions and access control guidelines.
- [print-layout-guidelines.md](print-layout-guidelines.md) — Sibling formatting rules.
- [report-data-reconciliation-guidelines.md](report-data-reconciliation-guidelines.md) — Sibling verification rules.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial guidelines implementation | AI Software Product Factory |
