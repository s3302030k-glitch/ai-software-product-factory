# Ecommerce QA Checklist

> Pre-release QA checklist covering catalog, cart, checkout, orders, payments, refunds, promotions, inventory, reporting, permissions, privacy, and regression scenarios.

This document supplements the core factory documents. It does not replace them.

---

## Purpose

Provide a comprehensive pre-release QA checklist for products with ecommerce behavior. This checklist helps QA agents and human reviewers verify that all ecommerce domain rules are correctly implemented before release.

## Status

`Active` — Run this checklist before any release that includes ecommerce features.

---

## Catalog and Product QA Checklist

- [ ] Products with variants each have distinct SKUs, not a merged record.
- [ ] Each SKU has its own price, stock status, and availability state.
- [ ] Inactive and draft products are hidden from the customer-facing storefront.
- [ ] Archived products are still accessible in historical order views.
- [ ] Archived products do not appear in customer-facing search results.
- [ ] Price changes in the catalog do not retroactively alter existing order item records.
- [ ] The base price is stored on the SKU record, not derived from a UI display component.
- [ ] Product image paths contain no local filesystem paths or private infrastructure URLs.
- [ ] Category and collection membership is stored on the product record.
- [ ] Admin product search includes all statuses; customer search excludes inactive/archived.
- [ ] Hard delete of a product is blocked unless the product has no associated order items.
- [ ] Soft delete or archive is the default when removing a product from the storefront.

---

## Cart and Checkout QA Checklist

- [ ] Cart totals displayed in the UI are estimates and are not passed to the server as authoritative.
- [ ] The server recalculates all prices, discounts, taxes, and shipping fees before confirming the order.
- [ ] A price change between cart add and checkout is surfaced to the customer.
- [ ] Guest checkout is supported or explicitly documented as out of scope.
- [ ] Cart merge behavior on login is defined, tested, and owner-approved.
- [ ] Discounts are applied server-side during checkout recalculation.
- [ ] Discount eligibility is validated server-side (not only on the client).
- [ ] The inventory reservation strategy is explicitly defined and tested.
- [ ] Abandoned checkout releases reserved inventory.
- [ ] Checkout is idempotent: re-submitting the form does not create a duplicate order.
- [ ] Payment initiation is blocked until server-side total recalculation is confirmed.
- [ ] All checkout state transitions are validated server-side.
- [ ] Checkout state machine is documented and owner-approved.
- [ ] Session-based cart identifiers do not expose internal database IDs to the client.

---

## Order Lifecycle QA Checklist

- [ ] Order state, payment state, and fulfillment state are independent fields on the order record.
- [ ] Order items store price, product name, SKU, variant, discount, and subtotal snapshots at order creation.
- [ ] Modifying a product in the catalog does not alter historical order items.
- [ ] Archiving or deleting a product does not alter historical order items.
- [ ] Cancelling an order marks it as cancelled without deleting any records.
- [ ] Cancellation releases reserved inventory.
- [ ] Partial fulfillment is supported or explicitly documented as out of scope.
- [ ] Return requests are stored as separate records, not modifications to the original order.
- [ ] All order state changes are recorded in the audit log with actor and timestamp.
- [ ] Cancellations and refund events include a reason field in the audit log.
- [ ] Customer notifications are triggered only by owner-defined events.
- [ ] Order state transitions are validated server-side.

---

## Payment and Refund Boundary QA Checklist

- [ ] Payment state is a separate record from order state.
- [ ] Payment state transitions are only triggered by confirmed provider events (webhook or API response).
- [ ] Webhook authenticity is verified before processing.
- [ ] Duplicate webhooks are handled idempotently (no duplicate state transitions or orders).
- [ ] Webhooks are routed to the correct order, customer, and tenant.
- [ ] Payment initiation is idempotent (no double charge on retry).
- [ ] Order creation from checkout is idempotent (no duplicate orders).
- [ ] The payment intent ID and session ID are stored on the payment record.
- [ ] Raw card data is absent from the application database and logs.
- [ ] Payment data is excluded from general application logs and error messages.
- [ ] Refunds are stored as separate records, not modifications to the original order.
- [ ] A partial refund correctly updates payment state to `partially_refunded`.
- [ ] Over-refunding is prevented (sum of refunds ≤ original payment amount).
- [ ] Chargebacks are recorded without silently cancelling the order.
- [ ] Failed webhook events are logged and retried.

---

## Promotion and Discount QA Checklist

- [ ] All discounts are calculated server-side.
- [ ] Coupon eligibility is validated server-side at checkout initiation and again at confirmation.
- [ ] Coupon usage limits are enforced atomically (no race conditions on simultaneous redemptions).
- [ ] Checkout fails gracefully if a coupon is exhausted between checkout steps.
- [ ] Discount stacking rules are tested (can two discounts combine?).
- [ ] The discount amount is stored as a snapshot on the order at creation.
- [ ] A percentage discount applies the owner-defined rounding mode.
- [ ] A fixed discount caps at the order subtotal (total does not go negative).
- [ ] Free shipping is implemented as a shipping fee override, not a subtotal discount.
- [ ] Bundle rules are tested with the exact required SKUs and quantities.
- [ ] All promotion changes are recorded in the audit log with actor and timestamp.
- [ ] Coupon codes are not easily guessable (sufficient entropy).
- [ ] Per-customer usage limits are tested across multiple accounts.

---

## Inventory and Fulfillment QA Checklist

- [ ] Inventory levels are associated with SKUs, not products.
- [ ] The inventory reservation strategy (at cart add / checkout initiation / order confirmation) is explicitly implemented as owner-approved.
- [ ] Reserved inventory is released on cart expiry, checkout abandonment, and order cancellation.
- [ ] Overselling is prevented by the reservation strategy.
- [ ] Fulfillment records are linked to order items, not just order state.
- [ ] Partial shipments are recorded as separate shipment records.
- [ ] Inventory reports reconcile with stock movement records.
- [ ] Fulfillment reports are sourced from shipment records, not order state alone.
- [ ] If erp-operations-pack is used, inventory alignment is verified.

---

## Reporting and Export QA Checklist

- [ ] Report totals match the sum of underlying order records for the same date range and filter.
- [ ] UI totals match CSV/Excel export totals for the same date range and filter.
- [ ] Export totals match PDF report totals for the same date range and filter.
- [ ] Time range boundaries are applied server-side in the defined timezone.
- [ ] Timezone label is included in date/time columns in exports.
- [ ] Cancelled and returned orders are correctly reflected in revenue calculations per owner-defined formula.
- [ ] Refunds are shown as separate line items, not deducted from original order totals.
- [ ] Discount totals are sourced from order snapshots, not live catalog prices.
- [ ] Inventory reports reconcile with stock movement records.
- [ ] Fulfillment reports are sourced from shipment records.

---

## Role, Permission, and Customer Privacy QA Checklist

- [ ] Customers can only access their own orders, payment history, and account data.
- [ ] Admins can access orders within their authorized scope only.
- [ ] In multitenant products, cross-tenant order and report access is blocked.
- [ ] Customer PII (name, email, address) is excluded from shared or bulk exports without authorization.
- [ ] Admin reports do not expose data from other tenants.
- [ ] Abandoned checkout reports are anonymized per owner-defined privacy rules.
- [ ] Payment records are accessible only to authorized roles.
- [ ] Order audit logs are accessible only to authorized admin roles.
- [ ] Export downloads are access-controlled and logged.

---

## Regression Checklist

Run after every release that changes ecommerce behavior:

- [ ] Adding an item to cart still works for guest and authenticated users.
- [ ] Checkout completes successfully with valid payment (test mode).
- [ ] Checkout fails gracefully with invalid payment.
- [ ] An existing order's items are unchanged after a product price change.
- [ ] An archived product still appears correctly in an existing historical order.
- [ ] A coupon with a usage limit cannot be over-redeemed.
- [ ] A refund does not alter the original order record.
- [ ] Report totals still reconcile after new orders are created.
- [ ] Webhook idempotency is verified (replay the same webhook event and check for no duplicate).
- [ ] Cross-tenant data isolation is verified in reports and exports.

---

## Bug Report Format

When a QA issue is found, use the following format:

```
## Bug Report

**Date:** YYYY-MM-DD
**Reporter:** [Name or role]
**Severity:** Critical / High / Medium / Low

**Area:** [Catalog / Cart / Checkout / Order / Payment / Refund / Promotion / Inventory / Reporting / Permissions]

**Summary:** [One-line description]

**Steps to Reproduce:**
1. [Step 1]
2. [Step 2]
3. [Step 3]

**Expected Behavior:** [What should happen]

**Actual Behavior:** [What actually happened]

**Evidence:** [Screenshots, logs, or test output — no real customer/payment data]

**Affected Records:** [Generic description — no real IDs, customer names, or payment data]

**Root Cause (if known):** [Description]

**Recommendation:** pass / fail / needs-fix / needs-owner-decision
```

---

## Release Readiness Checklist

Before releasing any version that includes ecommerce changes, confirm:

- [ ] All catalog/product QA items are passed or explicitly documented as out of scope.
- [ ] All cart/checkout QA items are passed or explicitly documented as out of scope.
- [ ] All order lifecycle QA items are passed or explicitly documented as out of scope.
- [ ] All payment/refund boundary QA items are passed or explicitly documented as out of scope.
- [ ] All promotion/discount QA items are passed or explicitly documented as out of scope.
- [ ] All inventory/fulfillment QA items are passed or explicitly documented as out of scope.
- [ ] All reporting/export QA items are passed or explicitly documented as out of scope.
- [ ] All role/permission/privacy QA items are passed or explicitly documented as out of scope.
- [ ] Regression checklist is fully passed.
- [ ] No open critical or high severity bugs remain.
- [ ] Human owner has reviewed and approved the QA report.
- [ ] No real customer, order, payment, or product data appears in test artifacts.

---

## Related Core Files

- [../../../core/docs/12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — Core QA testing strategy
- [../../../core/docs/13-release-checklist.md](../../../core/docs/13-release-checklist.md) — Release readiness checklist
- [../../../core/docs/15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Agent behavior constraints

## Related Pack Files

- [ecommerce-domain-model-guidelines.md](ecommerce-domain-model-guidelines.md) — Domain modeling principles
- [catalog-and-product-guidelines.md](catalog-and-product-guidelines.md) — Catalog QA reference
- [cart-and-checkout-guidelines.md](cart-and-checkout-guidelines.md) — Cart and checkout QA reference
- [order-lifecycle-guidelines.md](order-lifecycle-guidelines.md) — Order lifecycle QA reference
- [ecommerce-payment-refund-boundary-guidelines.md](ecommerce-payment-refund-boundary-guidelines.md) — Payment and refund QA reference
- [promotion-and-discount-guidelines.md](promotion-and-discount-guidelines.md) — Promotion QA reference
- [ecommerce-reporting-guidelines.md](ecommerce-reporting-guidelines.md) — Reporting QA reference

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation | Factory |
