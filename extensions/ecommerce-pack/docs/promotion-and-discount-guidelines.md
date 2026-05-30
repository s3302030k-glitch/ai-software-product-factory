# Promotion and Discount Guidelines

> Coupon model, automatic discount model, eligibility rules, discount stacking, timing, percentage vs. fixed handling, usage limits, abuse prevention, and audit trail requirements.

This document supplements the core factory documents. It does not replace them.

---

## Purpose

Define the rules for modeling, applying, and governing promotions and discounts in ecommerce products. This prevents the most common discount mistakes: client-side discount application, unclear stacking rules, race conditions on usage limits, and missing audit trails for promotion changes.

## Status

`Active` — Applies to all products that include coupons, automatic discounts, promotional pricing, bundle offers, or free shipping rules.

---

## Promotion and Discount Principles

1. **Discount calculations are server-side only.** No discount amount may be calculated, applied, or finalized on the client. The server is the single authority for discount eligibility and amount.
2. **Stacking rules must be explicit.** Whether multiple discounts can combine, which take priority, and how they interact must be defined by the product owner and documented before implementation.
3. **Coupon usage limits are transactional.** Usage counters must be incremented atomically to prevent race conditions where multiple users redeem the same single-use coupon simultaneously.
4. **Promotion changes require audit trails.** Any change to a promotion (price, eligibility, activation, deactivation) must be logged with actor and timestamp.
5. **Agents must not invent pricing, tax, or consumer protection policy.** Discount rules are a product owner decision.

---

## Coupon Model

A coupon is a customer-facing code that triggers a discount when applied at checkout.

| Field | Description |
|-------|-------------|
| `code` | The customer-facing redemption code |
| `discount_type` | `percentage` or `fixed_amount` |
| `discount_value` | The percentage or fixed currency amount |
| `minimum_order_value` | Optional minimum cart subtotal for eligibility |
| `eligible_products` | Optional list of SKUs or categories the coupon applies to |
| `usage_limit_total` | Maximum total redemptions across all customers |
| `usage_limit_per_customer` | Maximum redemptions per customer account |
| `valid_from` | Start date/time of the coupon's validity |
| `valid_until` | Expiry date/time of the coupon's validity |
| `is_active` | Whether the coupon is currently active |

**Rules:**
- Coupon validity must be checked server-side at checkout initiation and again at order confirmation.
- Usage limits must be enforced using database-level atomic operations (e.g., conditional update with a lock) to prevent race conditions.
- Expired or exhausted coupons must return a clear error at checkout, not a silent failure.

---

## Automatic Discount Model

An automatic discount is applied by the system without a coupon code, based on rules evaluated at checkout.

| Field | Description |
|-------|-------------|
| `name` | Internal name of the promotion (e.g., "Summer 10% Off") |
| `discount_type` | `percentage` or `fixed_amount` |
| `discount_value` | The discount amount or percentage |
| `eligibility_conditions` | Cart value, product category, customer segment, etc. |
| `priority` | Used when multiple automatic discounts are eligible (higher = applied first) |
| `combinable` | Whether this discount can stack with other discounts |
| `valid_from` | Start date/time |
| `valid_until` | Expiry date/time |
| `is_active` | Whether the promotion is currently active |

---

## Discount Eligibility Rules

Eligibility conditions must be defined per discount:

| Condition Type | Example |
|----------------|---------|
| Minimum cart subtotal | "Cart total must be at least 50.00" |
| Product category | "Applies to items in the 'Sale' category" |
| Specific SKUs | "Applies only to SKU-001 and SKU-002" |
| First order | "Applies to customer's first order only" |
| Customer segment | "Applies to registered customers only" |
| Date/time range | "Valid from 2026-06-01 to 2026-06-07" |

**Rules:**
- All eligibility conditions are evaluated server-side.
- First-order eligibility must be verified against the customer's actual order history in the database.
- Customer segment eligibility must be verified against the customer's account record, not client-provided claims.

---

## Discount Stacking Rules

Discount stacking behavior must be explicitly defined by the product owner:

| Scenario | Must Be Documented |
|----------|--------------------|
| Can a coupon stack with an automatic discount? | Yes or No, with owner approval |
| Can two automatic discounts combine? | Yes or No; if yes, which takes priority? |
| Can a free shipping discount combine with a percentage discount? | Yes or No |
| Is there a maximum total discount cap? | Owner-defined |

**Rules:**
- Default behavior is no stacking unless explicitly approved.
- Stacking rules must be implemented server-side and tested.
- Agents must not assume stacking behavior based on similar products or industry conventions.

---

## Discount Timing

- Discounts are applied during checkout recalculation — not at cart add time.
- Coupon codes are entered during checkout. Eligibility is evaluated at checkout initiation.
- Applied discounts are recalculated at the final checkout confirmation step before payment initiation, to catch any changes (e.g., coupon expired between initiation and confirmation).
- The discount amount is stored as a snapshot on the order at order creation.

---

## Percentage vs. Fixed Discount Handling

| Type | Rule |
|------|------|
| **Percentage discount** | Applied to the eligible subtotal (before or after tax, as owner defines). Rounding must follow a defined rounding mode (see [financial-business-logic-pack](../../financial-business-logic-pack/README.md)). |
| **Fixed amount discount** | Applied as a currency amount. If the order subtotal is less than the fixed discount, the discount is capped at the order subtotal (not a negative total). |

**Rules:**
- Rounding mode for percentage discounts must be explicitly defined by the owner.
- Discounts must never produce a negative order total.
- Tax treatment of discounts (discount before or after tax calculation) must be defined by the owner.

---

## Free Shipping and Bundle Notes

- **Free shipping**: A special discount type that sets the shipping fee to zero. Must be implemented as a shipping fee override, not a discount on the subtotal.
- **Bundle discounts**: Discounts applied when specific sets of SKUs are purchased together. Bundle rules must be explicitly defined (required SKUs, quantities, discount amount).
- Agents must not invent free shipping thresholds, bundle compositions, or bundle pricing rules.

---

## Usage Limits

- **Total usage limit**: The maximum number of times a coupon can be redeemed across all customers. Must be enforced atomically.
- **Per-customer usage limit**: The maximum number of times a single customer account can redeem a coupon.
- Both limits must be checked and incremented within the same database transaction as order creation, to prevent race conditions.
- If a usage limit is exhausted between checkout initiation and order confirmation, the checkout must fail gracefully with a clear error.

---

## Abuse Prevention

- Coupon codes should not be easily guessable. Code generation must use sufficient entropy.
- High-value coupons should have per-customer limits enforced.
- Automated or bulk redemption patterns should be detectable via audit trail analysis.
- The product owner must define and approve abuse prevention rules. Agents must not implement anti-abuse logic without owner direction.

---

## Audit Trail Requirements

Every promotion creation, modification, activation, deactivation, and redemption must be logged:

| Event | Fields to Log |
|-------|---------------|
| Promotion created | Actor, promotion ID, all initial values, timestamp |
| Promotion modified | Actor, promotion ID, changed fields (before/after), timestamp |
| Promotion activated/deactivated | Actor, promotion ID, new status, timestamp |
| Coupon redeemed | Customer ID, order ID, coupon code, discount amount, timestamp |
| Usage limit exhausted | Coupon code, timestamp |

**Rules:**
- Audit events must not be editable or deletable.
- See [financial-business-logic-pack](../../financial-business-logic-pack/README.md) for financial audit trail alignment on discount amounts.

---

## Out of Scope

This document does not cover:

- Tax treatment of discounts (jurisdiction-specific rules — owner must consult tax advisors)
- Consumer protection rules for promotional offers (owner must consult legal advisors)
- Payment provider coupon or discount features
- Real promotion data, real coupon codes, or real customer redemption records
- Application source code or database migrations

---

## Guardrails

- [ ] Discount calculations are server-side only; client-side discount amounts are not trusted.
- [ ] Discount stacking rules are explicit and owner-approved.
- [ ] Coupon usage limits are enforced transactionally and race-safe.
- [ ] Promotion changes require audit trail entries with actor and timestamp.
- [ ] Discounts do not produce negative order totals.
- [ ] No real promotion, coupon, or redemption data is included.
- [ ] No pricing, tax, or consumer protection policy is invented by agents.

---

## QA Checklist

- [ ] Are all discounts calculated server-side?
- [ ] Is coupon eligibility validated server-side at checkout initiation and again at confirmation?
- [ ] Are usage limits enforced atomically?
- [ ] Does the checkout fail gracefully if a coupon is exhausted between steps?
- [ ] Are discount stacking rules tested (can two discounts combine?)?
- [ ] Is the discount snapshot stored on the order at creation?
- [ ] Does a percentage discount apply the correct rounding mode?
- [ ] Does a fixed discount cap at the order subtotal (not negative total)?
- [ ] Is free shipping implemented as a shipping fee override, not a subtotal discount?
- [ ] Are bundle rules tested with the exact required SKUs and quantities?
- [ ] Is the audit trail capturing all promotion changes with actor and timestamp?
- [ ] Are coupon codes not easily guessable?
- [ ] Are per-customer usage limits tested across multiple accounts?

---

## Related Core Files

- [../../../core/docs/07-data-model.md](../../../core/docs/07-data-model.md) — Promotion and coupon entity definitions
- [../../../core/docs/09-api-design.md](../../../core/docs/09-api-design.md) — Coupon redemption and discount API contracts
- [../../../core/docs/10-security-model.md](../../../core/docs/10-security-model.md) — Authorization for promotion management
- [../../../core/docs/15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Agent behavior constraints

## Related Pack Files

- [ecommerce-domain-model-guidelines.md](ecommerce-domain-model-guidelines.md) — Domain modeling principles
- [cart-and-checkout-guidelines.md](cart-and-checkout-guidelines.md) — Discount application timing at checkout
- [order-lifecycle-guidelines.md](order-lifecycle-guidelines.md) — Discount snapshot at order creation
- [ecommerce-reporting-guidelines.md](ecommerce-reporting-guidelines.md) — Promotion and discount reporting

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation | Factory |
