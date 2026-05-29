# Invoice and Contract Document Guidelines

> Rules and standards for generating formal billing invoices, legal contracts, and financial statements.

This document supplements the core product specifications and page layouts. It does not replace them.

---

## Purpose

Define formatting, metadata, tracking, and presentation standards for generating legally or financially binding documents. This document ensures that official records are generated systematically, trace changes, are properly numbered, and remain audit-ready.

## Status

`Active` — These guidelines apply to all invoice, contract, receipt, and statement-generation features.

---

## Formal Document Principles

1. **Governance Disclaimer**: This extension pack does **not** define legal contract language, tax policy, or regulatory accounting practices. All templates must be reviewed and approved by the product owner's legal, accounting, and tax professionals.
2. **Immutability of Released Records**: Once an official document is issued to an external party, it must not be modified. Any changes must occur via formal revisions, amendments, credit notes, or cancellations.
3. **Sequential and Stable Numbering**: All official documents must use sequential, gap-free, non-colliding serial numbers for tracking and compliance.

---

## Invoice Document Rules

- **Header Details**:
  - Logo and Legal Name of issuer.
  - Tax Registration IDs/VAT Numbers (for both issuer and recipient where required).
  - Physical and contact addresses.
- **Line Items Structure**:
  - Distinct column layout containing: Description, Quantity, Unit Price, Tax Rate/VAT %, Net Amount, Tax Amount, and Gross Total.
- **Totals Section**:
  - Distinctly display subtotals, taxes broken down by rate category, rounding adjustments, and final balance due.
- **Due Dates and Payment Terms**:
  - Clearly display the date of issue, tax point date (if different), due date, and payment instructions (bank details, payment links).

---

## Contract Document Rules

- **Identification of Parties**: Distinctly name and describe the contracting parties, listing registration numbers and signing authorities.
- **Terms and Execution**:
  - Clear section layout for key clauses.
  - Execution date and duration fields.
- **Signature and Initialing Areas**:
  - Explicit areas for signatures, dates, print names, and titles.
  - Every page must contain a footer field for initials to prevent page substitution.
- **Legal Boundaries**: Ensure contract generators do not compile dynamic text without pre-approved template frameworks.

---

## Statement Document Rules

- **Period Boundaries**: Clearly print the start date and end date of the statement period.
- **Balance Tracking**: Include opening balance, total debits, total credits, and closing balance in a summary block at the top.
- **Transaction Table**:
  - Detail: Date, Transaction Reference/UUID, Description, Amount (In/Out), and Running Balance.

---

## Required Metadata

Every official document layout must display:
- **Unique Document Identifier**: Serial number or UUID.
- **Timestamp of Issue**: The date and time when the document was finalized and issued.
- **Version/Revision Number**: Distinctly indicate if the document is a draft, revision, or amendment.
- **Source Record Identifiers**: Reference numbers mapping the document back to the underlying database records (e.g., Sales Order ID, Account Number).

---

## Document Numbering and Versioning

- **Stable Formats**: Use prefix strings followed by sequential numbers (e.g., `INV-2026-0001`).
- **Audit Logs**: Maintain a central document registry tracking:
  - Document ID.
  - Type (Invoice, Credit Note, Contract).
  - Issue Timestamp.
  - Recipient ID.
  - Numeric Hash of the PDF file to verify contents.

---

## Legal/Tax/Accounting Boundary Disclaimer

> [!CAUTION]
> This guide outlines template presentation and workflow rules only. It does not ensure compliance with regional tax frameworks (like VAT, sales tax, or electronic invoicing rules) or legal contract validity. These aspects must be coded in collaboration with qualified professionals.

---

## Terms/Notes/Signature Blocks

- Keep terms, payment instructions, and signatures tightly coupled with preceding items.
- Maintain fallback layouts for cases where the signature block pushes onto a new page. (See [Print Layout Guidelines](print-layout-guidelines.md)).

---

## Void/Cancel/Amendment Display Rules

- **Cancellations**: Cancelled documents must display a prominent, semi-transparent overlay watermark (e.g. "VOID" or "CANCELLED") across the pages.
- **Amendments**: Corrected contracts or statements must print the revision history block (e.g., "Amends Document ID: INV-2026-0001 issued YYYY-MM-DD") in a visible location.
- **Credit Notes**: Credit notes must link directly to the original invoice serial number.

---

## Currency/Quantity/Locale Formatting Notes

- **Currency Layouts**: Display currency symbols or ISO codes consistently based on the product's financial rules. (e.g. `$ 1,000.00` vs `1.000,00 €`).
- **Precision**: Align displayed numbers with the calculation schemas in the database. Never let the PDF generator apply rounding that causes line items to sum to a different total than the displayed subtotal.

---

## RTL/Multilingual Document Notes

- **Logical Direction**: The document structure must adapt to the language direction.
- **Mirroring Rules**: Right-align labels and left-align values in RTL documents. Align table grids so that line item totals appear on the left, and descriptions start on the right.
- **Fallback Fonts**: Ensure selected print fonts correctly render diacritics and special characters for Persian/Arabic/Hebrew languages.

---

## Out of Scope

- Drafting actual legal contracts or billing terms.
- Setting up physical printing systems or postage APIs.
- Configuring electronic invoice registration APIs.

---

## Guardrails

- [ ] Every official document must carry a unique, sequential number that cannot be altered once issued.
- [ ] Rounding and total formatting in document templates must align with the approved database values and business logic.
- [ ] Cancelled or voided documents must render clear cancelation watermarks/notices.
- [ ] No document generator should recalculate values; templates must display numbers passed directly from source records.

---

## QA Checklist

- [ ] Verify that document numbers increment correctly and do not collide.
- [ ] Verify that voided invoices render the "VOID" watermark.
- [ ] Check decimal rounding on invoices: confirm the subtotal + tax matches the gross total exactly.
- [ ] Verify that RTL contracts render with correct alignment, layout mirroring, and font styling.
- [ ] Verify that PDF metadata includes the source record ID and issue date.

---

## Related Files

- [04 — User Roles](../../../core/docs/04-user-roles.md) — Permission definitions for document actions.
- [06 — Pages Spec](../../../core/docs/06-pages-spec.md) — UI representations of invoices/contracts.
- [print-layout-guidelines.md](print-layout-guidelines.md) — Formatting rules for document printing.
- [pdf-report-guidelines.md](pdf-report-guidelines.md) — Programmatic PDF generation rules.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial guidelines implementation | AI Software Product Factory |
