# Export Guidelines

> Rules, standards, and data formatting guidelines for implementing CSV, Excel, and JSON exports.

This document supplements the core product specifications, data models, and security models. It does not replace them.

---

## Purpose

Define structural, formatting, and security rules for data downloads. This guidelines document ensures that exported spreadsheets and data feeds are localized correctly, secure, readable, and align perfectly with UI calculations.

## Status

`Active` — These rules apply whenever a feature allows users to export, download, or stream data from the product.

---

## Export Principles

1. **Consistent Totals**: Exported totals and lists must match UI totals and screen records under the same filters, unless a different data scope is explicitly requested and documented.
2. **Precision and Type Preservation**: Do not strip decimals, truncate numbers, or format dates into non-standard local strings that spreadsheet software cannot interpret. Preserve raw underlying values while configuring the spreadsheet display.
3. **No Silent Columns**: Do not silently export columns containing internal database primary keys, audit metrics, or private tenant IDs that the user does not see on screen, unless the column is explicitly scoped as part of an audit/administrative data export.

---

## CSV Export Rules

- **Delimiter Handling**:
  - For standard English and LTR locales, use a comma (`,`) as the delimiter.
  - In locales where the comma is used as a decimal separator (e.g., Germany, France, Italy), use a semicolon (`;`) as the column delimiter to prevent column splitting.
- **Escaping and Quoting**: Wrap all string fields in double quotes (`"`). If a string field contains a double quote, escape it by doubling it (`""`).
- **UTF-8 Encoding**: Write CSV files with UTF-8 encoding. Include a Byte Order Mark (BOM) at the beginning of the file so that Excel opens UTF-8 encoded files with international characters correctly.

---

## Excel/XLSX Export Rules

- **Native Data Types**: Set the cell value types explicitly (e.g., Boolean, Numeric, Date, String). Do not write all data as strings.
- **Format Masks**: Apply Excel number formatting masks for currencies and dates:
  - Currencies: Format cells with currency styling (`$#,##0.00` or local equivalents) instead of prepending raw symbols to strings.
  - Dates: Apply ISO dates (`YYYY-MM-DD`) or local formats that Excel recognizes as dates, allowing users to sort and filter by time series.
- **Style Constraints**: Keep layouts simple. Avoid excessive cell merging, colorful fills, or complex fonts that reduce readability. Use standard bold styling for headers and totals rows.

---

## JSON Export Rules

- **Standard Layout**: Maintain a flat array of objects for simple tabular datasets.
- **ISO Standard Standards**: Format dates using ISO 8601 strings (`YYYY-MM-DDTHH:mm:ssZ`).
- **Numeric Precision**: Render money fields as exact integers (e.g., cents or minor units) or strings/decimals, depending on the programming language, to avoid floating point precision losses during JSON parsing.

---

## File Naming Conventions

- **Structure**: Format filenames systematically:
  `[Tenant/Organization]_[ExportType]_[Date]_[Time].[extension]`
- **Format**:
  - Date/Time: Use UTC timestamps or local product date strings (e.g., `company-acme_invoices_2026-05-29_1910.csv`).
  - Safe Character Sets: Use only alphanumeric characters, dashes, and underscores. Do not include spaces or special characters in filenames.

---

## Column Naming and Ordering

- **Header Labeling**: Headers must match the labels displayed in the UI tables exactly (e.g., if UI shows "Due Date", the export header must be "Due Date" or `due_date`, not `dt_due`).
- **Logical Ordering**: Arrange columns logically, from left to right: identifiers, primary metadata (dates, names), main details, calculations/monetary columns, and status flags.

---

## Data Type Preservation

- **Monetary Values**: Export money as numbers, not strings. Do not append currency symbols (e.g., `$`) inside the raw numeric cells of spreadsheets, as this converts the cell type to string and blocks formulas.
- **Telephone/Postal Codes**: Export telephone numbers, postal codes, and tax/company IDs as string variables. If written as numbers, spreadsheet programs will strip leading zeros (e.g. `00210` becomes `210`).

---

## Locale and Delimiter Rules

- **Decimals**: Use the correct localized decimal separator (e.g., `.` for US/UK, `,` for EU) in numeric exports.
- **Currency Symbols**: For multi-currency systems, include a separate column for ISO currency code (e.g. `USD`, `EUR`) next to the amount column. Do not combine them in one numeric column.

---

## Large Export Handling

- **Limit and Page Scoping**: Limit exports of extremely large datasets. If an export exceeds a specified limit (e.g., 10,000 rows), stream the download or chunk it into multiple files.
- **Background Tasks**: For heavy exports, run the export process as an asynchronous background worker task and email a secure link to the user when compilation is complete. Do not block the web server.

---

## Sensitive Data Handling

- **Permission Checking**: Always run user permission checks before generating exports. Having access to a page does not automatically grant permission to download the entire dataset.
- **Data Masking**: Apply data masking to fields containing personally identifiable information (PII) or security tokens in export sheets (e.g. masking card numbers `**** **** **** 1234`).

---

## Export Audit Logging

- **Audit Trails**: Every export action of sensitive or official data must be logged.
- **Logged Metrics**: Track:
  1. Actor ID (User ID).
  2. Timestamp.
  3. Parameters used (filters).
  4. Number of rows exported.
  5. File hash or name.
- **Immutability**: Audit logs for exports must be write-only (cannot be edited or deleted).

---

## Out of Scope

- Implementing the Excel or CSV library functions.
- Configuring mail servers to send export files to users.
- Writing raw SQL queries for data retrieval.

---

## Guardrails

- [ ] Delimiter rules must match the user's localized settings. Semicolons must be used for CSVs when commas act as decimal separators.
- [ ] Exported totals must match UI/report totals.
- [ ] Explicitly check user permissions before compiling an export.
- [ ] Prevent data types from converting to strings (monetary cells must be numeric, postal codes must remain strings with leading zeros).
- [ ] Log every sensitive data export transaction to an audit trail database table.

---

## QA Checklist

- [ ] Check CSV delimiter behavior in both English and German/European locales.
- [ ] Verify that leading zeros in zip codes or telephone numbers are preserved.
- [ ] Verify that exported monetary values are numeric cells (can be summed using `=SUM()` in Excel).
- [ ] Verify that sensitive fields are masked in files downloaded by non-admin roles.
- [ ] Verify that export activities generate audit log records.

---

## Related Files

- [09 — API Design](../../../core/docs/09-api-design.md) — Source endpoints that serve data.
- [10 — Security Model](../../../core/docs/10-security-model.md) — Authentication and permission check rules.
- [pdf-report-guidelines.md](pdf-report-guidelines.md) — Sibling report guidelines.
- [report-data-reconciliation-guidelines.md](report-data-reconciliation-guidelines.md) — Reconciliation standards for exports.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial guidelines implementation | AI Software Product Factory |
