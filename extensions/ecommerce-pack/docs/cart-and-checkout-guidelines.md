# Cart and Checkout Guidelines

> Cart principles, guest vs. authenticated cart, checkout state machine, server-side recalculation, discount timing, inventory reservation, payment initiation, and idempotency rules.

This document supplements the core factory documents. It does not replace them.

---

## Purpose

Define the rules for modeling, validating, and governing cart and checkout behavior. This prevents the most critical ecommerce checkout mistakes: trusting client-side totals, unclear inventory reservation behavior, non-idempotent payment initiation, and ambiguous checkout state transitions.

## Status

`Active` — Applies to all products that include a shopping cart and checkout flow.

---

## Cart Principles

1. **Cart totals are estimates, not authoritative.** A cart total shown in the UI is a convenience display. It must never be used as the authoritative total for an order.
2. **Server-side recalculation is mandatory at checkout.** Before an order is confirmed and payment is initiated, the server must recalculate all line item prices, discounts, taxes, shipping fees, and the total. Client-provided totals must not be trusted.
3. **Cart items reflect a point-in-time intent.** A cart item holds a reference to a product/SKU and a quantity. It does not hold a price snapshot until checkout recalculation.
4. **Cart expiry must be defined.** The product owner must define how long a cart persists (e.g., session-based, 30-day cookie, account-linked). Expired carts must release any held inventory reservations.

---

## Guest Cart vs. Authenticated Cart

| Scenario | Cart Behavior |
|----------|---------------|
| **Guest (anonymous)** | Cart is session-scoped or tied to a browser identifier. Order can be completed without an account. Customer email is captured at checkout. |
| **Authenticated** | Cart is linked to the customer's account. Persists across sessions. |
| **Guest → Authenticated (login during checkout)** | Cart merge behavior (whether the guest cart merges with any existing account cart) must be explicitly owner-defined. |

**Rules:**
- Guest checkout must be supported or explicitly out of scope with owner approval.
- Cart merge on login is a business rule that must be defined and documented by the owner.
- Session-based cart identifiers must not expose internal database IDs to the client.

---

## Cart Item Model

A cart item must include at minimum:

| Field | Description |
|-------|-------------|
| `cart_id` | Reference to the parent cart |
| `product_id` | Reference to the product |
| `sku_id` | Reference to the specific SKU (variant) being added |
| `quantity` | Quantity requested |
| `added_at` | Timestamp when the item was added |

A cart item must not include:

- A stored price (price is recalculated at checkout, not stored on the cart item)
- A discount amount (discounts are applied at checkout, not on the cart item)

**Rules:**
- The SKU ID (not the product ID alone) is the authoritative item identifier on a cart.
- If a SKU is deleted or archived before checkout, the cart item must surface an error at checkout — not silently fail.

---

## Price Refresh Rules

- Cart item prices displayed to the customer are fetched from the current catalog price at display time.
- If a product price changes between when an item was added to the cart and when checkout is initiated, the customer must be informed of the price change.
- The product owner must define whether a price increase blocks checkout or merely warns the customer.
- Price refresh must be server-side; client-cached prices must not be used.

---

## Discount Application Timing

- Discounts (coupons and automatic promotions) are applied during checkout server-side, not at cart add time.
- Discount eligibility (coupon validity, minimum order value, eligible SKUs) is evaluated at checkout initiation.
- Applied discounts are recalculated at the final checkout confirmation step before payment initiation.
- Discount amounts are stored on the order at order creation — not on the cart item.

See [promotion-and-discount-guidelines.md](promotion-and-discount-guidelines.md) for discount stacking rules, eligibility, and abuse prevention.

---

## Inventory Reservation Timing

Inventory reservation behavior must be explicitly defined and owner-approved:

| Option | Description | Risk |
|--------|-------------|------|
| **Reserve at cart add** | Stock is held when item is added to cart | May block stock for long periods; increases abandonment impact |
| **Reserve at checkout initiation** | Stock is held when checkout begins | Race condition possible between checkout start and payment |
| **Reserve at order confirmation** | Stock is held when order is created after payment | Risk of overselling between payment and reservation |

**Rules:**
- The product owner must choose and document one of the above options.
- Agents must not invent a reservation strategy.
- Reservation must be released if checkout is abandoned, payment fails, or the cart expires.
- If ERP-style inventory management is used, reservation must align with [erp-operations-pack](../../erp-operations-pack/README.md).

---

## Checkout State Machine

Checkout is a multi-step process with distinct states. The product owner must approve the exact state list and transitions.

Typical checkout states:

| State | Meaning |
|-------|---------|
| `cart` | Customer is browsing and adding items |
| `checkout_started` | Customer has initiated checkout (address, shipping selection) |
| `payment_initiated` | Customer has been directed to payment; awaiting payment result |
| `order_created` | Order has been confirmed; payment captured or pending |
| `abandoned` | Checkout was started but not completed within the defined window |

**Rules:**
- State transitions must be validated server-side.
- A checkout session must not progress to `payment_initiated` before server-side total recalculation is complete.
- Duplicate order creation from the same checkout session must be prevented. See idempotency rules below.

---

## Address and Shipping Method Notes

- Shipping address must be captured and validated before shipping cost is calculated.
- Shipping method selection determines the shipping fee applied at checkout.
- Shipping cost is included in the server-side total recalculation.
- The product owner must define which address fields are required and what validation applies.
- Agents must not invent shipping rate logic, carrier integrations, or address validation rules.

---

## Payment Initiation Boundary

- Payment initiation is the act of directing the customer to a payment provider or capturing payment credentials.
- Payment initiation must only occur after the server-side total recalculation is complete and the order total is confirmed.
- Payment provider integration code is out of scope for this pack. See [ecommerce-payment-refund-boundary-guidelines.md](ecommerce-payment-refund-boundary-guidelines.md) for the payment boundary model.

---

## Checkout Idempotency

- A single checkout session must only create one order, even if the customer submits the payment form multiple times or the network request is retried.
- The checkout process must use an idempotency key (e.g., checkout session ID) to prevent duplicate order creation.
- Payment initiation must also be idempotent: submitting the same payment request twice must not result in a double charge.
- The idempotency strategy must be documented and owner-approved.

---

## Abandoned Checkout Notes

- An abandoned checkout is a checkout session that was started but not completed within the defined window.
- The product owner must define the abandonment window and any recovery actions (e.g., email reminders).
- Abandoned checkouts must release any held inventory reservations.
- Abandoned checkout data (email, items, step reached) may be retained for analytics if the owner approves and privacy rules permit.
- Agents must not implement abandoned checkout recovery flows without owner approval.

---

## Out of Scope

This document does not cover:

- Payment provider integration code
- Tax calculation algorithms or jurisdiction rules
- Shipping carrier integration or rate calculation
- Fraud detection or risk scoring
- Real cart data, real checkout sessions, or real customer data
- Legal compliance for consumer protection or right of withdrawal
- Application source code or database migrations

---

## Guardrails

- [ ] Cart totals are never used as the authoritative total for order creation.
- [ ] Server-side recalculation always runs before payment initiation.
- [ ] Client-side totals are never treated as authoritative.
- [ ] Payment initiation is idempotent.
- [ ] Inventory reservation behavior is explicitly defined and owner-approved.
- [ ] Duplicate order creation from the same checkout session is prevented.
- [ ] No payment provider integration code is created by this pack.
- [ ] No tax or shipping calculation logic is invented by agents.

---

## QA Checklist

- [ ] Does the server recalculate all prices, discounts, taxes, and shipping before confirming the order?
- [ ] Is the client-side total only a display estimate?
- [ ] Does the checkout correctly handle a price change between cart add and checkout?
- [ ] Is guest checkout supported or explicitly out of scope?
- [ ] Is cart merge on login behavior defined and tested?
- [ ] Are discounts applied server-side at checkout initiation?
- [ ] Is discount eligibility validated server-side (not just on the client)?
- [ ] Is the inventory reservation strategy explicitly defined and tested?
- [ ] Is abandoned checkout handled (reservation released, window defined)?
- [ ] Is checkout idempotent? Does re-submitting the form not create a duplicate order?
- [ ] Is payment initiation blocked until server-side total is confirmed?
- [ ] Are all checkout state transitions validated server-side?
- [ ] Is the checkout state machine documented and owner-approved?

---

## Related Core Files

- [../../../core/docs/05-user-flows.md](../../../core/docs/05-user-flows.md) — Cart and checkout user journeys
- [../../../core/docs/06-pages-spec.md](../../../core/docs/06-pages-spec.md) — Cart and checkout page specifications
- [../../../core/docs/07-data-model.md](../../../core/docs/07-data-model.md) — Cart and order entity definitions
- [../../../core/docs/09-api-design.md](../../../core/docs/09-api-design.md) — Checkout API endpoint contracts
- [../../../core/docs/10-security-model.md](../../../core/docs/10-security-model.md) — Session management and customer data security
- [../../../core/docs/15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Agent behavior constraints

## Related Pack Files

- [ecommerce-domain-model-guidelines.md](ecommerce-domain-model-guidelines.md) — Cart vs. order distinction and entity principles
- [catalog-and-product-guidelines.md](catalog-and-product-guidelines.md) — SKU availability and pricing rules
- [order-lifecycle-guidelines.md](order-lifecycle-guidelines.md) — Order creation from checkout
- [promotion-and-discount-guidelines.md](promotion-and-discount-guidelines.md) — Discount application and stacking at checkout
- [ecommerce-payment-refund-boundary-guidelines.md](ecommerce-payment-refund-boundary-guidelines.md) — Payment initiation boundary

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation | Factory |
