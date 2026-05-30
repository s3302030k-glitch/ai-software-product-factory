# Order Lifecycle Guidelines

> Order states, payment state vs. fulfillment state, order item snapshots, partial fulfillment, cancellation, returns, refunds, and audit trail requirements.

This document supplements the core factory documents. It does not replace them.

---

## Purpose

Define the rules for modeling, governing, and auditing the complete lifecycle of an ecommerce order — from creation through fulfillment, cancellation, and return. This prevents the most common order lifecycle mistakes: collapsing order, payment, and fulfillment states into one field, silently mutating order items after creation, allowing refunds to erase original history, and skipping audit trail requirements.

## Status

`Active` — Applies to all products that include orders, order items, fulfillment, cancellation, or refunds.

---

## Order Lifecycle Principles

1. **Order state, payment state, and fulfillment state are independent.** An order can be paid but not yet fulfilled. An order can be partially fulfilled. A payment can be refunded without the order being returned. Each state dimension must be modeled separately unless the owner explicitly approves a simplified model.
2. **Order records are immutable after confirmation.** Once an order is created, its items, prices, and applied discounts must not change. Corrections are handled through separate adjustment, refund, or return records — not by editing the original order.
3. **Cancelled orders preserve all history.** Cancellation must mark the order as cancelled; it must not delete the order, its items, its payment records, or its audit events.
4. **Partial fulfillment must be supported or explicitly out of scope.** If the product ships items in multiple batches, the fulfillment model must handle partial fulfillment. If not needed, this must be explicitly documented by the owner.

---

## Order States

Order state tracks the administrative and fulfillment status of an order. The product owner must define and approve the exact state list and valid transitions.

Typical order states:

| State | Meaning |
|-------|---------|
| `pending` | Order confirmed; awaiting payment or initial processing |
| `processing` | Payment received; order is being prepared |
| `partially_fulfilled` | Some items have been shipped or delivered |
| `fulfilled` | All items have been shipped or delivered |
| `cancelled` | Order has been cancelled; all history preserved |
| `returned` | Customer has returned items after fulfillment |
| `closed` | Order is fully resolved (fulfilled, returned, or refunded and closed) |

**Rules:**
- Valid transitions must be defined and documented. For example: `pending → processing`, `processing → fulfilled`, `fulfilled → returned`.
- Agents must not invent additional states without owner approval.
- State changes must be recorded in the audit trail with actor and timestamp.

---

## Payment State vs. Fulfillment State

Payment state and fulfillment state must be modeled as independent dimensions of an order:

| Dimension | Tracks |
|-----------|--------|
| **Payment state** | Whether the customer has paid, payment is pending, failed, or refunded. Managed in coordination with the payment provider. |
| **Fulfillment state** | Whether the physical or digital goods have been shipped, delivered, or partially delivered. |
| **Order state** | The overall administrative status of the order (see above). |

**Rules:**
- Paid orders that are not yet fulfilled must correctly reflect both states (e.g., payment = `paid`, fulfillment = `unfulfilled`).
- Fulfillment must not be marked complete based on payment state alone.
- Payment state changes must not automatically override fulfillment state.
- See [ecommerce-payment-refund-boundary-guidelines.md](ecommerce-payment-refund-boundary-guidelines.md) for payment state model details.

---

## Order Item Snapshots

When an order is created, each order item must capture a point-in-time snapshot:

| Field | Snapshot Rule |
|-------|---------------|
| Product name | As it was at order creation |
| SKU identifier | As it was at order creation |
| Variant description | As it was at order creation |
| Unit price | As it was at order creation |
| Quantity | As confirmed at order creation |
| Discount applied | Discount amount captured at order creation |
| Line item subtotal | Calculated at order creation |
| Tax / shipping apportioned | If applicable, as calculated at order creation |

**Rules:**
- After an order is created, no order item field may be changed except through explicit adjustment, refund, or return records.
- If a product is later archived, renamed, or repriced, the original order item must still display the original snapshot values.
- Agents must not build order item logic that fetches live catalog prices for display in historical order views.

---

## Partial Fulfillment

- Partial fulfillment occurs when some items in an order are shipped or delivered before others.
- Each shipment should be associated with a set of order items and quantities.
- An order item may have a fulfillment quantity (shipped) separate from its ordered quantity.
- The product owner must define whether partial fulfillment triggers partial invoicing or partial billing.
- If partial fulfillment is not needed, this must be documented as explicitly out of scope.

---

## Cancellation Rules

- **Who can cancel**: The product owner must define which roles (customer, admin, system) may cancel an order and under what conditions.
- **When cancellation is permitted**: Cancellation windows (e.g., before shipment, within 24 hours) must be defined and owner-approved.
- **Effect on inventory**: Cancellation must release any reserved or allocated inventory.
- **Effect on payment**: If payment was already captured, cancellation triggers a refund flow. See [ecommerce-payment-refund-boundary-guidelines.md](ecommerce-payment-refund-boundary-guidelines.md).
- **Effect on records**: Cancellation marks the order as cancelled. It does not delete the order, items, or payment records.

---

## Return and Exchange Notes

- A return is a post-fulfillment process where the customer sends items back.
- A return request is a separate record linked to the original order; it does not modify the original order.
- The product owner must define the return eligibility window and conditions.
- Exchange (return one item and receive another) involves both a return and a new order or adjustment; the flow must be owner-defined.
- Agents must not invent return eligibility rules or exchange flows without owner approval.

---

## Refund References

Refunds are closely tied to order lifecycle but governed by separate rules. See [ecommerce-payment-refund-boundary-guidelines.md](ecommerce-payment-refund-boundary-guidelines.md) for:

- Refund model
- Partial refund handling
- Chargeback and dispute notes
- Refund and original payment history preservation

---

## Shipment and Fulfillment References

If the product includes physical shipment:

- Each shipment record is linked to an order and a set of order items.
- Shipment tracking information (carrier, tracking number, status) is associated with the shipment record.
- For ERP-style warehouse and fulfillment operations, see [erp-operations-pack](../../erp-operations-pack/README.md).

---

## Customer Notification Notes

- Customers typically expect notifications at key order lifecycle events (order confirmed, shipped, delivered, cancelled, refunded).
- Notification trigger rules (which events send notifications, which channels) must be owner-defined.
- Notification content must not expose other customers' data or internal system identifiers.
- Agents must not implement notification logic without owner-approved trigger rules and templates.

---

## Audit Trail Requirements

Every order state change, payment state change, and fulfillment state change must be recorded in an audit log including:

| Field | Requirement |
|-------|-------------|
| Entity type | Order, order item, shipment, return |
| Entity ID | Identifier of the changed record |
| Field changed | Which field or state changed |
| Previous value | The value before the change |
| New value | The value after the change |
| Actor | User ID, system job, or webhook source that made the change |
| Timestamp | UTC timestamp of the change |
| Reason | Optional reason or note (required for cancellations and refunds) |

**Rules:**
- Audit events must not be editable or deletable.
- Cancellation and refund events must include a reason field.
- See [financial-business-logic-pack](../../financial-business-logic-pack/README.md) for financial audit trail alignment.

---

## Out of Scope

This document does not cover:

- Payment provider integration code
- Shipping carrier integration or tracking APIs
- Legal compliance for returns, consumer protection, or refund obligations
- Tax recalculation on refunds or partial returns
- Real order data, customer data, or payment records
- Application source code or database migrations

---

## Guardrails

- [ ] Order state, payment state, and fulfillment state are modeled as separate dimensions.
- [ ] Cancelled orders preserve all history; no deletion of order items or payment records.
- [ ] Partial fulfillment is supported or explicitly out of scope with owner approval.
- [ ] Refunds do not erase original payment or order records.
- [ ] Order documents and reports align with print-reporting-pack where applicable.
- [ ] Audit trail captures all state changes with actor, timestamp, and reason.
- [ ] No real order, customer, or payment data is included in this document.
- [ ] No legal, consumer protection, or refund compliance advice is provided.

---

## QA Checklist

- [ ] Are order state, payment state, and fulfillment state independent fields?
- [ ] Do order items preserve their snapshot values even if the catalog changes?
- [ ] Does archiving or deleting a product not affect historical order items?
- [ ] Is cancellation non-destructive (order marked cancelled, not deleted)?
- [ ] Does cancellation release reserved inventory?
- [ ] Is partial fulfillment supported or explicitly out of scope?
- [ ] Are return requests separate records, not modifications to the original order?
- [ ] Is the audit trail capturing all state changes with actor and timestamp?
- [ ] Does the audit trail include reasons for cancellations and refunds?
- [ ] Are order documents (invoices, receipts) aligned with print-reporting-pack?
- [ ] Are all order state transitions validated server-side?
- [ ] Are customer notifications triggered by owner-defined events only?

---

## Related Core Files

- [../../../core/docs/05-user-flows.md](../../../core/docs/05-user-flows.md) — Order management user flows
- [../../../core/docs/07-data-model.md](../../../core/docs/07-data-model.md) — Order entity definitions
- [../../../core/docs/09-api-design.md](../../../core/docs/09-api-design.md) — Order API contracts
- [../../../core/docs/10-security-model.md](../../../core/docs/10-security-model.md) — Order access control and audit requirements
- [../../../core/docs/12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — QA testing strategy
- [../../../core/docs/15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Agent behavior constraints

## Related Pack Files

- [ecommerce-domain-model-guidelines.md](ecommerce-domain-model-guidelines.md) — Order vs. cart distinction, entity principles, lifecycle state overview
- [cart-and-checkout-guidelines.md](cart-and-checkout-guidelines.md) — Order creation from checkout
- [ecommerce-payment-refund-boundary-guidelines.md](ecommerce-payment-refund-boundary-guidelines.md) — Payment state, refunds, and webhooks
- [promotion-and-discount-guidelines.md](promotion-and-discount-guidelines.md) — Discount snapshot at order creation
- [ecommerce-reporting-guidelines.md](ecommerce-reporting-guidelines.md) — Order reporting and reconciliation

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation | Factory |
