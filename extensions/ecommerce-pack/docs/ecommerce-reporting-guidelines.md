# Ecommerce Reporting Guidelines

> Source-of-truth rules, sales and order reports, cart and checkout reports, promotion and discount reports, refund and return reports, inventory and fulfillment reports, timezone boundaries, privacy scoping, export alignment, and reconciliation rules.

This document supplements the core factory documents. It does not replace them.

---

## Purpose

Define the rules for generating, validating, and reconciling ecommerce reports and exports. This prevents the most common ecommerce reporting mistakes: totals that differ between UI, exports, and PDFs; reports that leak customer data or cross-tenant data; timezone boundary errors; and reports built from UI state rather than source order records.

## Status

`Active` — Applies to all products that include order reports, sales summaries, refund reports, discount reports, inventory reports, or ecommerce data exports.

---

## Ecommerce Reporting Principles

1. **Reports reconcile with source order records.** Every report total must be derivable from and reconcilable with the underlying order records, payment records, and refund records in the database.
2. **UI totals, export totals, and PDF totals must match.** If a sales total appears in the admin dashboard, in a CSV export, and in a PDF report, all three must show the same value for the same time range and filter set. Discrepancies must be documented and owner-approved.
3. **Reports must not use UI display state as source.** Reports must query authoritative stored values from the database, not derive from UI-layer computed fields.
4. **Reports must not leak customer or cross-tenant data.** Each report must be scoped to the requesting user's authorized context. Admin reports are scoped to the authorized admin's tenant or scope. Customer reports are scoped to the customer's own orders.

---

## Source-of-Truth Rules

| Report Type | Source of Truth |
|-------------|-----------------|
| Sales/order totals | Order records (stored order total at order creation) |
| Refund totals | Refund records (stored refund amount at refund creation) |
| Discount totals | Order records (stored discount snapshot at order creation) |
| Inventory levels | Stock records (via erp-operations-pack if applicable) |
| Fulfillment status | Shipment and fulfillment records |
| Payment totals | Payment records (confirmed via webhook or provider API) |

**Rules:**
- Reports must not recompute totals from live catalog prices or current SKU prices.
- Reports must use the price, discount, and tax snapshots stored on the order at order creation.
- Any derived/aggregated totals must be verifiable by summing the underlying records.

---

## Sales and Order Reports

Sales and order reports typically include:

- Total orders (count)
- Total revenue (gross sales)
- Total discounts applied
- Total shipping fees
- Total taxes (if applicable and owner-defined)
- Net revenue (gross minus discounts, refunds, taxes as defined)
- Orders by status (pending, processing, fulfilled, cancelled, returned)
- Orders by date range

**Rules:**
- The definition of "revenue" (gross, net, tax-inclusive, tax-exclusive) must be owner-defined and documented.
- Cancelled and returned orders must be accounted for in revenue calculations per the owner-defined formula.
- Report filters (date range, status, product, customer segment) must be applied consistently to all data sources.

---

## Cart and Checkout Reports

Cart and checkout reports are analytics-focused and typically include:

- Cart creation rate
- Checkout initiation rate
- Checkout completion rate (conversion)
- Abandoned checkout rate
- Most abandoned checkout step

**Rules:**
- Cart and checkout analytics are separate from order revenue reports.
- Abandoned checkout data must be anonymized or scoped per privacy rules.
- Cart analytics must not include actual customer payment data.

---

## Promotion and Discount Reports

Promotion and discount reports typically include:

- Total discounts issued (by coupon or automatic promotion)
- Coupon redemption count and amount
- Revenue impact of promotions (gross discount cost)
- Usage limit utilization rate

**Rules:**
- Discount amounts in reports must match the discount snapshots stored on order records.
- Reports must reconcile coupon redemption counts with usage limit records.
- Promotion performance reports must not include real coupon codes or customer identifiers in shared/exported views without explicit authorization.

---

## Refund and Return Reports

Refund and return reports typically include:

- Total refunds issued (count and amount)
- Partial refunds issued
- Return rate
- Refunds by reason
- Refunds by product or category

**Rules:**
- Refund amounts must be sourced from refund records, not inferred from payment state changes.
- Partial refunds must be accurately reflected (not rounded to full-order refunds in reports).
- Refund reports must not alter the original order revenue totals; they are separate line items.
- Chargebacks and disputes must be distinguishable from voluntary refunds in reports.

---

## Inventory and Fulfillment Reports

Inventory and fulfillment reports may include:

- Stock levels by SKU
- Reserved vs. available stock
- Fulfillment rate
- Items shipped and delivered
- Backorder or out-of-stock events

**Rules:**
- Inventory reports must align with [erp-operations-pack](../../erp-operations-pack/README.md) when ERP-style stock management is in use.
- Inventory report totals must reconcile with stock movement records, not with catalog availability fields alone.
- Fulfillment reports must source from shipment records, not from order state alone.

---

## Time Range and Timezone Boundaries

- All report time ranges must be defined in explicit timezone-aware terms.
- The product must define a canonical report timezone (e.g., UTC, or a configured business timezone).
- Time range boundaries (e.g., "last 30 days") must be calculated server-side using the defined timezone.
- Date displayed in reports must match the timezone used for filtering.
- Exports must include the timezone label in date/time columns.
- See [financial-business-logic-pack](../../financial-business-logic-pack/README.md) for financial period and timezone alignment.

---

## Tenant and Customer Privacy Scoping

- Admin reports must be scoped to the admin's authorized tenant or scope.
- In multitenant SaaS products, cross-tenant reporting must be explicitly prohibited unless the user has super-admin access. See [saas-multitenant-pack](../../saas-multitenant-pack/README.md).
- Customer-facing order history must only show that customer's own orders.
- Exported reports must not include customer PII (name, email, address) without authorization and appropriate access control.
- Bulk exports containing customer data require owner approval and must follow the product's privacy policy.

---

## Export and Print Alignment

- CSV, Excel, and PDF exports of order data must produce totals consistent with the admin dashboard report for the same filter parameters.
- Export columns must include the same fields and the same values as the UI report, unless differences are explicitly documented.
- PDF order documents (invoices, receipts) must align with order records at the time of generation.
- See [print-reporting-pack](../../print-reporting-pack/README.md) for PDF layout, export formatting, and reconciliation guidelines.

---

## Reconciliation Rules

- Reports must be designed to support reconciliation with source records:
  - Order total in report = Sum of order items subtotals + shipping + tax − discounts (per owner-defined formula).
  - Payment total in report = Sum of captured payment records for the same period.
  - Refund total in report = Sum of refund records for the same period.
- UI totals, export totals, and PDF totals must match for the same filter set and time range.
- If a reconciliation mismatch is identified, it must be logged, investigated, and resolved — not silently dismissed.

---

## Out of Scope

This document does not cover:

- Financial accounting or double-entry ledger reporting (see [financial-business-logic-pack](../../financial-business-logic-pack/README.md))
- Tax reporting or VAT/GST filings
- Legal compliance for financial reporting
- Real order data, customer data, or payment records for reports
- Application source code or database migrations

---

## Guardrails

- [ ] Ecommerce reports reconcile with source order and payment records.
- [ ] UI totals, export totals, and PDF totals match for the same filter set.
- [ ] Timezone and date boundaries are explicit in all reports and exports.
- [ ] Reports do not expose customer PII without authorization.
- [ ] Cross-tenant data leakage is prevented in all reports and exports.
- [ ] Financial reporting aligns with financial-business-logic-pack.
- [ ] Print and export alignment follows print-reporting-pack.
- [ ] No real order, customer, or payment data is included in this document.

---

## QA Checklist

- [ ] Do report totals match the sum of underlying order records?
- [ ] Do UI totals match export (CSV/Excel) totals for the same date range?
- [ ] Do export totals match PDF report totals for the same date range?
- [ ] Are time range boundaries applied server-side in the defined timezone?
- [ ] Is the timezone label included in date/time columns in exports?
- [ ] Are cancelled and returned orders correctly reflected in revenue calculations?
- [ ] Are refunds shown as separate line items, not deducted from original order totals?
- [ ] Are discount totals sourced from order snapshots, not from live catalog prices?
- [ ] Is customer PII excluded from shared or bulk exports without authorization?
- [ ] Is cross-tenant data access blocked in all report queries?
- [ ] Are inventory reports reconciled with stock movement records?
- [ ] Are fulfillment reports sourced from shipment records, not order state alone?
- [ ] Are abandoned checkout reports anonymized per privacy rules?

---

## Related Core Files

- [../../../core/docs/07-data-model.md](../../../core/docs/07-data-model.md) — Order, payment, and refund entity definitions
- [../../../core/docs/09-api-design.md](../../../core/docs/09-api-design.md) — Report and export API endpoint contracts
- [../../../core/docs/10-security-model.md](../../../core/docs/10-security-model.md) — Report access control and customer data handling
- [../../../core/docs/12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — QA testing strategy
- [../../../core/docs/15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Agent behavior constraints

## Related Pack Files

- [ecommerce-domain-model-guidelines.md](ecommerce-domain-model-guidelines.md) — Source-of-truth entity distinctions
- [order-lifecycle-guidelines.md](order-lifecycle-guidelines.md) — Order record structure and audit trail
- [ecommerce-payment-refund-boundary-guidelines.md](ecommerce-payment-refund-boundary-guidelines.md) — Payment and refund record structure
- [promotion-and-discount-guidelines.md](promotion-and-discount-guidelines.md) — Discount snapshot and audit trail
- [ecommerce-qa-checklist.md](ecommerce-qa-checklist.md) — Reporting QA checklist

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation | Factory |
