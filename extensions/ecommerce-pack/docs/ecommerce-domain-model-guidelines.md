# Ecommerce Domain Model Guidelines

> Core ecommerce modeling principles, entity distinctions, lifecycle states, price snapshot rules, and business rule ownership for products with ecommerce behavior.

This document supplements the core factory documents. It does not replace them.

---

## Purpose

Establish clear boundaries between ecommerce entities — products, SKUs, variants, carts, orders, payments, and refunds — and define the rules that govern how they are modeled, separated, and governed. This prevents the most common ecommerce modeling mistakes: collapsing distinct concepts, hiding business rules inside UI components, and allowing payment state to silently override order state.

## Status

`Active` — Applies to all products that include product catalogs, shopping carts, checkout flows, orders, payments, refunds, or promotions.

---

## When to Use

Use this guideline document when a product includes any of the following:

- A product catalog with multiple prices, variants, or SKUs
- A shopping cart and checkout flow
- Orders with line items, fulfillment, and state tracking
- Payments, refunds, or returns
- Promotions, discounts, or coupons
- Inventory reservation or stock management
- Customer accounts or guest checkout
- Ecommerce reports or order exports

---

## Ecommerce Domain Modeling Principles

1. **Each entity has one source of truth.** Products, carts, orders, and payments are distinct entities. Their data must not be shared, merged, or implied from each other without explicit documented rules.
2. **Business rules live in the backend.** Price calculations, discount logic, inventory reservation, and order state transitions must be implemented in backend services or functions — not inside UI components, display helpers, or frontend utilities.
3. **Snapshots preserve history.** When an order is created, the price, product name, SKU, and variant details must be captured as a snapshot on the order item. Future changes to the product catalog must not retroactively alter historical orders.
4. **State transitions are explicit.** Order state, payment state, and fulfillment state each have their own lifecycle. Transitions between states must follow a documented state machine and must not be collapsed silently.
5. **Derived values are not authoritative.** Totals displayed in UI, cart estimates, or report summaries are derived. The authoritative total is the server-calculated value at the time of order confirmation.
6. **Owner decisions are required.** Any simplification of these entity boundaries — such as merging cart and order, or collapsing payment state into order state — requires explicit product owner approval and documentation.

---

## Entity Distinctions

### Product vs. SKU vs. Variant

| Concept | Definition |
|---------|------------|
| **Product** | The top-level catalog entry. Represents a thing being sold (e.g., "T-Shirt"). May have multiple variants. |
| **SKU** | Stock Keeping Unit. A unique identifier for a specific sellable configuration (e.g., "T-Shirt — Blue — Large"). A product may have one or many SKUs. |
| **Variant** | A specific combination of attribute values (e.g., color = Blue, size = Large) that produces a distinct SKU. |

**Rules:**
- Products, SKUs, and variants must not be merged into a single entity unless the product owner explicitly approves a simplified model and documents the rationale.
- Each SKU must have its own price, stock, and availability state.
- A product with no variants still has one implicit SKU.

### Cart vs. Order

| Concept | Definition |
|---------|------------|
| **Cart** | A temporary, mutable collection of items a customer intends to purchase. Totals are estimates. |
| **Order** | A confirmed, immutable record of a purchase transaction. Totals are authoritative server-calculated values. |

**Rules:**
- Cart and order are never the same entity.
- An order is created from a cart, not converted in place. The cart may persist after order creation for reference, but must be clearly distinguished.
- Cart item quantities, prices, and discount eligibility are recalculated server-side when checkout is initiated — not relied upon from client-side state.

### Customer vs. Account vs. Session

| Concept | Definition |
|---------|------------|
| **Session** | A browser or device session. May be anonymous (guest) or authenticated. |
| **Customer** | A person who places orders. May be a guest (no account) or registered. |
| **Account** | A registered user record associated with a customer. Holds identity, address book, order history, and preferences. |

**Rules:**
- Guest customers do not require an account. Their information is captured at checkout and linked to the order.
- If a guest later registers, the association between the guest order and the new account is an owner-approved business rule.
- Session management must not be used to infer payment state or access control for order history.

---

## Order Item Source-of-Truth Rules

1. When an order is created, each order item must record:
   - Product name at time of order
   - SKU identifier at time of order
   - Variant description at time of order (if applicable)
   - Unit price at time of order (price snapshot)
   - Quantity
   - Any applied discounts at time of order
   - Subtotal calculated at time of order
2. Changes to the product catalog (price changes, product edits, product deletion) must not alter existing order item records.
3. Archived or deleted products must remain visible in historical order views.

---

## Price Snapshot Rules

1. The price recorded on an order item is the price at the time the order was confirmed — not the current catalog price.
2. Price changes in the product catalog do not affect existing orders.
3. Cart prices may refresh (see [cart-and-checkout-guidelines.md](cart-and-checkout-guidelines.md) for refresh rules), but the authoritative order price is only set at order creation.
4. Tax, shipping, and fee amounts applied to an order must also be snapshotted at order creation.

---

## Order Lifecycle States

Order state tracks the fulfillment/administrative status of the order. It is separate from payment state and fulfillment state.

Typical order states (product owner must define and approve the exact state list):

| State | Meaning |
|-------|---------|
| `draft` | Order started but not yet confirmed (if applicable) |
| `pending` | Order confirmed, awaiting payment or processing |
| `processing` | Payment received, fulfillment underway |
| `partially_fulfilled` | Some items shipped, others pending |
| `fulfilled` | All items delivered or fulfilled |
| `cancelled` | Order cancelled; history preserved |
| `returned` | Items returned after fulfillment |
| `refunded` | Refund issued (may overlap with other states) |

**Rules:**
- The product owner must approve the exact state list and valid transitions.
- Cancelled orders must preserve all history. Cancellation must not delete order items, payment records, or audit events.
- Agents must not invent additional states or transitions without owner approval.

---

## Payment and Refund Lifecycle References

Payment state is a separate lifecycle from order state. See [ecommerce-payment-refund-boundary-guidelines.md](ecommerce-payment-refund-boundary-guidelines.md) for:

- Payment state model
- Payment intent and session ID handling
- Webhook handling rules
- Refund model
- Partial refund handling
- Chargeback and dispute notes

---

## Inventory Reservation References

Inventory reservation behavior must be explicit and owner-approved. See [cart-and-checkout-guidelines.md](cart-and-checkout-guidelines.md) for:

- When reservation occurs (at cart add, checkout initiation, or order confirmation)
- When reservation is released (on cart expiry, checkout abandonment, or cancellation)
- Relationship to [erp-operations-pack](../../erp-operations-pack/README.md) if ERP-style stock management is involved

---

## Derived Values vs. Stored Values

| Value | Type | Rule |
|-------|------|------|
| Cart subtotal | Derived (estimate) | Recalculated server-side at checkout; not authoritative |
| Order total | Stored (snapshot) | Set at order confirmation; must not change after confirmation |
| Discount applied | Stored (snapshot) | Captured at order creation; not recalculated retroactively |
| Product current price | Derived (catalog) | Not authoritative for existing orders |
| Report totals | Derived | Must reconcile with stored order records (see [ecommerce-reporting-guidelines.md](ecommerce-reporting-guidelines.md)) |

---

## Business Rule Ownership

The following ecommerce business rules require explicit product owner definition and approval before implementation:

- Price calculation formula (base price, tax, shipping, discount application order)
- Discount stacking rules (which discounts may combine)
- Inventory reservation timing (when reservation is placed and released)
- Order state machine (valid states and transitions)
- Payment state machine (valid states and transitions)
- Refund eligibility rules
- Return and exchange rules
- Tax and shipping fee calculation boundaries
- Guest checkout vs. required account rules
- Order cancellation rules and deadlines

Agents must not invent, assume, or approximate these rules. If unclear, stop and request owner clarification.

---

## Out of Scope

This document does not cover:

- Payment provider integration code
- Tax calculation algorithms or jurisdiction rules
- Shipping rate calculation or carrier integration
- Legal compliance for consumer protection, returns, or refunds
- Real product data, real order data, or real customer data
- Application source code, migrations, or database schemas

---

## Guardrails

- [ ] Cart and order are never treated as the same entity.
- [ ] Order items always preserve price and product snapshots at time of order.
- [ ] Product, SKU, and variant boundaries are explicit and not merged without owner approval.
- [ ] Payment state is not silently collapsed into order state.
- [ ] Ecommerce business rules are not hidden inside UI components.
- [ ] No payment provider integration code is created by this pack.
- [ ] No legal, tax, or regulated ecommerce advice is provided.
- [ ] Owner decisions are required for any business rule ambiguity.

---

## Related Core Files

- [../../../core/docs/00-document-priority.md](../../../core/docs/00-document-priority.md) — Document authority and conflict resolution
- [../../../core/docs/07-data-model.md](../../../core/docs/07-data-model.md) — Entity, field, and relationship definitions
- [../../../core/docs/09-api-design.md](../../../core/docs/09-api-design.md) — API contracts and endpoint design
- [../../../core/docs/10-security-model.md](../../../core/docs/10-security-model.md) — Authentication, authorization, and audit requirements
- [../../../core/docs/15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Agent behavior constraints and stop conditions

## Related Pack Files

- [catalog-and-product-guidelines.md](catalog-and-product-guidelines.md) — Product catalog, SKU, variant, and availability rules
- [cart-and-checkout-guidelines.md](cart-and-checkout-guidelines.md) — Cart and checkout state, recalculation, and payment initiation
- [order-lifecycle-guidelines.md](order-lifecycle-guidelines.md) — Order states, fulfillment states, cancellation, and audit
- [ecommerce-payment-refund-boundary-guidelines.md](ecommerce-payment-refund-boundary-guidelines.md) — Payment state, refunds, webhooks
- [promotion-and-discount-guidelines.md](promotion-and-discount-guidelines.md) — Promotions, discounts, coupons, and stacking rules
- [ecommerce-reporting-guidelines.md](ecommerce-reporting-guidelines.md) — Reporting source-of-truth and reconciliation
- [ecommerce-qa-checklist.md](ecommerce-qa-checklist.md) — Pre-release QA checklist

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation | Factory |
