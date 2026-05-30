# Ecommerce Payment and Refund Boundary Guidelines

> Payment provider boundary, payment state model, payment intent and session ID handling, webhook idempotency, refund model, partial refunds, chargebacks, and sensitive data handling.

This document supplements the core factory documents. It does not replace them.

---

## Purpose

Define the boundary between the application's payment domain and external payment providers, and establish clear rules for payment state, refunds, webhooks, idempotency, and sensitive data handling. This prevents the most critical payment boundary mistakes: storing raw card data, non-idempotent webhook handling, silent payment state overrides, and losing payment history on refund.

## Status

`Active` — Applies to all products that process payments, issue refunds, or receive payment provider webhooks.

---

## Payment Boundary Principles

1. **This pack does not provide payment provider integration code.** Payment provider selection, SDK integration, and API calls to Stripe, PayPal, Paddle, or any other provider are out of scope for this pack. The pack defines the boundary model only.
2. **The application must not store raw card data.** PCI-DSS compliance requires that raw card numbers, CVVs, and magnetic stripe data must never be stored in the application database. Payment tokenization is the responsibility of the payment provider.
3. **Payment state is derived from provider webhooks and API responses.** The application's payment state record is updated based on confirmed events from the payment provider — not from client-side assumptions.
4. **Payment state and order state are independent.** Payment state changes must not automatically override order state or fulfillment state without documented transition rules.

---

## Payment Provider vs. Application Domain

| Concern | Owned By |
|---------|----------|
| Raw card data collection | Payment provider (never the application) |
| Tokenization and card vaulting | Payment provider |
| Payment processing and authorization | Payment provider |
| Fraud detection and risk scoring | Payment provider (optionally supplemented by application) |
| Payment state record in the database | Application |
| Order state record in the database | Application |
| Refund initiation request | Application (via provider API) |
| Refund processing and settlement | Payment provider |
| Webhook event handling | Application |

---

## Payment State Model

The application must maintain a payment state record per order (or per payment attempt if multiple attempts are allowed).

Typical payment states (product owner must approve the exact state list):

| State | Meaning |
|-------|---------|
| `pending` | Payment initiated; awaiting provider confirmation |
| `authorized` | Payment authorized but not yet captured |
| `captured` | Payment successfully collected |
| `failed` | Payment attempt failed |
| `cancelled` | Payment was cancelled before capture |
| `refunded` | Full refund issued |
| `partially_refunded` | Partial refund issued; original payment partially returned |
| `disputed` | Chargeback or dispute filed by the customer |

**Rules:**
- Payment state must transition only on confirmed events (webhook or API response).
- Transitions must follow a documented state machine approved by the owner.
- Multiple payment attempts (e.g., after a failed first attempt) must each be recorded, not overwrite the previous attempt.

---

## Payment Intent and Session ID Handling

- A payment intent ID (or equivalent — varies by provider) is a unique identifier for a payment attempt issued by the payment provider.
- The application must store the payment intent ID on the payment record to enable idempotency checks and provider reconciliation.
- Payment session IDs (e.g., Stripe Checkout session IDs) must be stored and associated with the order.
- Payment intent IDs must not be exposed to unauthorized users or logged in plain text alongside sensitive data.

---

## Webhook Handling Rules

1. **Webhooks must be idempotent.** The same webhook event may be delivered more than once by the payment provider. Processing a duplicate webhook must not create a duplicate state transition, a duplicate order, or a duplicate refund.
2. **Webhook authenticity must be verified.** Each webhook payload must be verified using the provider's signature mechanism before processing.
3. **Webhooks must be mapped to the correct order, customer, and tenant.** In multitenant products, webhooks must be routed to the correct tenant context. See [saas-multitenant-pack](../../saas-multitenant-pack/README.md) for tenant scoping rules.
4. **Webhook processing must be atomic.** State transitions triggered by webhooks must be wrapped in database transactions to prevent partial updates on failure.
5. **Failed webhook processing must be logged and retried.** A failed webhook handler must not silently discard the event.

---

## Idempotency

- Payment initiation must be idempotent: retrying the same payment request must not result in a double charge.
- Order creation from a checkout session must be idempotent: retrying checkout confirmation must not create duplicate orders.
- Refund initiation must be idempotent: retrying a refund request must not result in a double refund.
- Idempotency keys (checkout session ID, payment intent ID, or application-generated idempotency token) must be stored and checked before processing any state-changing operation.

---

## Refund Model

A refund is a separate record linked to the original order and payment:

| Field | Description |
|-------|-------------|
| `order_id` | Reference to the original order |
| `payment_id` | Reference to the original payment record |
| `refund_amount` | Amount being refunded |
| `refund_reason` | Reason for the refund |
| `initiated_by` | Actor (admin user ID, system job, or customer self-service) |
| `provider_refund_id` | Provider's refund transaction ID |
| `status` | `pending`, `completed`, `failed` |
| `created_at` | Timestamp when the refund was initiated |

**Rules:**
- A refund must not modify or delete the original order record or payment record.
- A refund must not mark the original order items as deleted or unavailable.
- Refund records must be immutable after creation; corrections require a separate adjustment record.
- The product owner must define refund eligibility rules (window, eligible order states).

---

## Partial Refund Handling

- A partial refund applies to a subset of the order total (e.g., refunding one item out of three).
- The remaining amount still owed or retained must be recalculated and documented.
- The application must track the total refunded amount per order to prevent over-refunding.
- Partial refunds must update the payment state to `partially_refunded`, not `refunded`.
- Multiple partial refunds on the same order are allowed if the sum does not exceed the original payment amount.

---

## Chargeback and Dispute Notes

- A chargeback (dispute) is initiated by the customer's bank, not by the application.
- The application must record the dispute event when notified by the payment provider webhook.
- The dispute must be associated with the original order and payment record.
- The product owner must define the response process for disputes.
- Agents must not implement automated dispute responses without owner approval.
- Chargebacks must not silently cancel the order or delete order history.

---

## Payment vs. Order State Separation

Payment state changes must not automatically override order state unless documented transition rules are defined:

| Payment Event | Effect on Order State |
|---------------|-----------------------|
| Payment captured | Order moves from `pending` to `processing` (if owner-defined rule) |
| Payment failed | Order remains `pending`; customer is notified |
| Refund completed | Order state change (e.g., to `refunded`) must be explicit and owner-approved |
| Dispute filed | Order state may be flagged as `disputed`; must not be silently cancelled |

All cross-state effects must be explicitly documented. Agents must not invent implicit state coupling.

---

## Sensitive Payment Data Handling

- Raw card numbers, CVVs, and expiry dates must never be stored in the application database.
- Payment provider tokens, customer IDs, and subscription IDs from the provider may be stored but must be treated as sensitive identifiers.
- Payment-related fields must be excluded from general application logs.
- Payment data must not appear in error messages, debug output, or user-facing error pages.
- Access to payment records must be restricted to authorized roles as defined in the security model.
- See [../../../core/docs/10-security-model.md](../../../core/docs/10-security-model.md) for authorization requirements.

---

## Out of Scope

This document does not cover:

- Payment provider SDK integration code (Stripe, PayPal, Paddle, LemonSqueezy, or any other)
- PCI-DSS compliance implementation
- Tax calculation on refunds
- Legal compliance for refund obligations, chargebacks, or consumer protection
- Real payment data, real transaction IDs, or real customer payment records
- Application source code or database migrations

---

## Guardrails

- [ ] No payment provider integration code is created by this pack.
- [ ] Raw card data is never stored in the application database.
- [ ] Webhooks are idempotent and signature-verified before processing.
- [ ] Webhooks are mapped to the correct order, customer, and tenant.
- [ ] Payment state does not silently override order or fulfillment state.
- [ ] Refunds and disputes preserve original payment and order history.
- [ ] No real payment, transaction, or customer payment data is included.
- [ ] No PCI-DSS, legal, or consumer protection advice is provided.

---

## QA Checklist

- [ ] Is the payment state record separate from the order state record?
- [ ] Are payment state transitions only triggered by confirmed provider events?
- [ ] Is webhook authenticity verified before processing?
- [ ] Are duplicate webhooks handled idempotently?
- [ ] Are webhooks routed to the correct order, customer, and tenant?
- [ ] Is payment initiation idempotent (no double charge on retry)?
- [ ] Is order creation from checkout idempotent (no duplicate orders)?
- [ ] Does the application store the payment intent ID and session ID?
- [ ] Is raw card data absent from the application database?
- [ ] Is payment data excluded from general application logs?
- [ ] Are refunds stored as separate records, not modifications to the original order?
- [ ] Does a partial refund update payment state to `partially_refunded`?
- [ ] Is over-refunding prevented (sum of refunds ≤ original payment amount)?
- [ ] Are chargebacks recorded without silently cancelling the order?
- [ ] Are failed webhook events logged and retried?

---

## Related Core Files

- [../../../core/docs/07-data-model.md](../../../core/docs/07-data-model.md) — Payment and refund entity definitions
- [../../../core/docs/09-api-design.md](../../../core/docs/09-api-design.md) — Payment initiation and webhook API contracts
- [../../../core/docs/10-security-model.md](../../../core/docs/10-security-model.md) — Sensitive data handling and access control
- [../../../core/docs/15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Agent behavior constraints

## Related Pack Files

- [ecommerce-domain-model-guidelines.md](ecommerce-domain-model-guidelines.md) — Payment lifecycle references and entity distinctions
- [cart-and-checkout-guidelines.md](cart-and-checkout-guidelines.md) — Payment initiation boundary at checkout
- [order-lifecycle-guidelines.md](order-lifecycle-guidelines.md) — Order state vs. payment state, refund references
- [ecommerce-reporting-guidelines.md](ecommerce-reporting-guidelines.md) — Payment and refund reporting

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation | Factory |
