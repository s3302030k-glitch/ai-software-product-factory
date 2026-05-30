# 19 — Financial Business Logic Notes

> How the Financial Business Logic Pack applies to the Integrated Operations ERP documentation reference.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> See the [example README](../README.md) for full context.
> Extension pack reference: [financial-business-logic-pack README](../../../extensions/financial-business-logic-pack/README.md)

---

## Pack Application Summary

The [Financial Business Logic Pack](../../../extensions/financial-business-logic-pack/README.md) governs how monetary values, invoice amounts, payment records, currency display, rounding concepts, and financial auditability are documented in this ERP reference. All financial boundary decisions derive from this pack's guidelines.

> [!WARNING]
> This documentation reference contains **no real accounting ledger, no real tax calculation, no real bank integration, and no real payment processing**. Invoice and payment records are placeholder display values only. Nothing in this document constitutes legal, tax, accounting, investment, or financial advice.

---

## Money Values

The Financial Business Logic Pack's Money and Currency Guidelines apply:

- All monetary amounts in this documentation reference are **display placeholder values only**.
- Amounts are represented as decimal display strings (e.g., `"1,250.00"`) — not as float or double types.
- The pack's rule banning binary floating-point (`float`/`double`) for monetary data is documented here, though no real calculation engine is implemented.
- If a real implementation is built from this reference, all monetary storage must use `NUMERIC(19,4)` or equivalent exact decimal type — never `float` or `double`.

**Entities with monetary fields:**
- `InvoicePlaceholder.amount_placeholder` — display value string, not an accounting entry
- `PaymentPlaceholder.amount_placeholder` — display value string, not a real payment amount
- `PurchaseOrderPlaceholder` line items: `unit_price_placeholder` — display value only
- `SalesOrderPlaceholder` line items: `unit_price_placeholder` — display value only

---

## Invoice / Payment Placeholder Amounts

- `InvoicePlaceholder.amount_placeholder` is a display value representing the face value of a placeholder invoice.
- `InvoicePlaceholder.remaining_balance` is conceptually derived: `amount_placeholder - SUM(PaymentPlaceholder.amount_placeholder)`. This is a display calculation only — not an accounting balance.
- `PaymentPlaceholder.amount_placeholder` is a display value representing a placeholder payment amount.
- No amount field is ever a real accounting entry, a real bank transfer, or a legally binding financial record.

---

## Rounding Concepts

The Calculation and Rounding Guidelines from the Financial Business Logic Pack are documented but not implemented:

- If a real implementation is built, rounding must use a defined rounding mode (e.g., `HALF_UP` / `ROUND_HALF_EVEN`) applied at a single, defined point in the calculation — not across multiple intermediate steps.
- Line item totals: quantity × unit_price, rounded to 2 decimal places.
- Invoice total: sum of line item totals, not re-rounded (to avoid double-rounding errors).
- No rounding is implemented in this documentation reference — amounts are static display values.

---

## Currency Display

- `currency_display` on `InvoicePlaceholder` is a display label only (e.g., `"USD"`, `"EUR"`).
- No real ISO 4217 currency conversion, no exchange rate fetching, no multi-currency calculation is implemented.
- The RTL/i18n Pack's locale formatting guidelines (see [22-rtl-i18n-notes.md](22-rtl-i18n-notes.md)) govern how currency amounts are displayed for different locales.
- All currency display is right-aligned in LTR layouts; left-aligned with RTL layout mirroring.

---

## Auditability

The Audit Trail and Approval Guidelines from the Financial Business Logic Pack apply:

- All InvoicePlaceholder and PaymentPlaceholder creation and status changes are logged as AuditEvents.
- No financial record may be silently edited — all changes must be traceable to an actor and a timestamp.
- The `before_snapshot` and `after_snapshot` in AuditEvent must capture the previous and new state of any changed financial record.
- Financial records are not deleted — only status-changed (e.g., Draft → Issued → Paid).

---

## No Tax / Accounting / Legal Advice

> [!WARNING]
> This documentation reference does **not** provide tax calculation, VAT/GST rules, withholding tax logic, accounting treatment guidance, financial compliance requirements, or any legal or regulated financial advice.
>
> Any real ERP implementation built from this reference **must** be reviewed and approved by qualified accounting, tax, legal, and compliance professionals before production use.

---

## No Real Payment Flow

- No Stripe, PayPal, Adyen, bank API, or payment gateway is referenced, designed, or implied.
- No PCI-DSS scope, no card data handling, no direct debit setup is included.
- The PaymentPlaceholder entity records a human-entered placeholder only.

---

## No Real Ledger Claim

- This system does **not** implement a general ledger, chart of accounts, trial balance, or double-entry bookkeeping.
- Invoice and payment totals are not posted to any accounting system.
- Financial reporting in this reference consists of aggregated placeholder display values — not audited financial statements.

---

## Related Files

- [07-data-model.md](07-data-model.md) — InvoicePlaceholder and PaymentPlaceholder entity definitions
- [06-pages-spec.md](06-pages-spec.md) — Finance Overview, Invoice List, Payment List page specs
- [10-security-model.md](10-security-model.md) — Finance section access restrictions
- [14-decision-log.md](14-decision-log.md) — Decision 2 (no real accounting), Decision 3 (no real payment)
- [../../../extensions/financial-business-logic-pack/README.md](../../../extensions/financial-business-logic-pack/README.md) — Source pack
