# 20 — Financial Business Logic Notes: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document specifies the money representation guidelines, calculation rounding modes, and audit trail controls for the Team Subscription Manager, in alignment with the **[Financial Business Logic Pack](../../../extensions/financial-business-logic-pack/README.md)**.

> [!WARNING]
> **NO TAX, LEGAL, OR ACCOUNTING ADVICE**: This document contains generic guidelines for reference validation. All financial procedures must be reviewed and approved by certified financial, tax, and legal professionals. No live payment integrations or bank transfer processing logic are supported.

---

## 1. Money Representation (Subunits)
- **Float Prohibition**: Storing monetary values as binary floating-point numbers (`float`, `double`) is strictly banned. Floating-point arithmetic introduces rounding drift (e.g. `0.1 + 0.2 = 0.30000000000000004`), which is unacceptable for billing records.
- **Integer Storage**: All prices and invoice placeholders are stored in database columns as 64-bit positive integers representing the smallest currency subunit (cents).
  - For example:
    - **$15.00 USD** is stored as `1500` cents.
    - **$250.00 USD** is stored as `25000` cents.

---

## 2. Rounding Logic
- **Rounding Mode**: Calculations involving seat count averages or plan adjustments must utilize **Round-Half-To-Even** (Banker's rounding). This minimizes statistical rounding drift across lists.
- **Calculations Location**: All billing math must be executed within backend services or stored database routines. The frontend UI layers are restricted to displaying the numbers and must not calculate base prices.

---

## 3. Currency Display & Formatting
- **ISO 4217 Currency Codes**: All monetary records must link to an explicit currency code (e.g., `USD`, `EUR`, `ILS`).
- **Formatting separation**: The database stores raw integer cents. UI components format the numbers dynamically based on the active browser locale settings (e.g., USD formats to `$15.00`, whereas EUR formats to `15,00 €`).

---

## 4. Financial Auditability
- **Immutable Log Trails**: Any manual invoice status modifications or fee adjustments must write a tracking entry to the audit log. The audit log record must capture:
  1. The User ID of the actor performing the change.
  2. The timestamp of the operation.
  3. The reason input explaining the override.
- **No In-Place Edits**: Invoices once marked as Paid or Uncollectible are immutable. Any adjustments require creating a separate credit note or adjustment record.

---

## Related Files

- [07-data-model.md](07-data-model.md) — Integer cents field mappings.
- [09-api-design.md](09-api-design.md) — API schemas for invoices.
- [21-print-reporting-notes.md](21-print-reporting-notes.md) — Invoice PDF guidelines.
