# 07G — Payment and Settlement Guidelines

> Defines conventions for managing transaction lifecycles, partial payments, refunds, reversals, external gateway integrations, and bank reconciliation.

---

## Purpose

Maintain clear boundaries between payment collection (receiving cash) and invoice settlement (matching cash to outstanding debts). This document ensures allocations, partial payments, and reversals are recorded traceably, preventing silent balances discrepancies.

## Status

`Active` — Must be followed by system designers, backend developers, and business analysts when working on billing pipelines or ledger states.

---

## Payment vs Settlement Distinction

- **Payment**: The movement of funds from an external party to the company (e.g. Stripe charge, wire transfer). It represents cash received.
- **Settlement**: The accounting allocation that associates a payment with one or more obligations (e.g. applying a $100 payment to clear Invoice #101).
- **Rule**: A payment received is **not** always a final settlement. A payment can clear multiple invoices, or sit as unallocated credit on a customer account.

---

## Payment Lifecycle

Payments must follow a strict, uni-directional state path:
- **Pending / Authorized**: Gateway has authorized transaction, but funds are not yet captured or cleared.
- **Cleared / Captured**: Cash has been successfully received.
- **Failed**: Gateway rejected the transaction.
- **Refunded / Reversed**: Funds returned to the payer.

```
                  ┌───────────┐
                  │  Pending  │
                  └─────┬─────┘
            ┌───────────┴───────────┐
            ▼                       ▼
      ┌───────────┐           ┌───────────┐
      │  Failed   │           │  Cleared  │
      └───────────┘           └─────┬─────┘
                                    ▼ (Refund)
                              ┌───────────┐
                              │ Refunded  │
                              └───────────┘
```

---

## Settlement Lifecycle

Settlement records track allocations:
- **Unallocated**: Payment cleared but not linked to any invoice.
- **Partially Allocated**: Payment linked to invoices, but a balance remains.
- **Fully Allocated / Settled**: The invoice balance is reduced to zero.

---

## Partial Payment Handling

When a payment is less than the invoice total:
- Calculate the remaining balance due:
  $$\text{Balance Due} = \text{Invoice Total} - \sum(\text{Allocated Payments})$$
- The invoice status transitions to `Partially Paid`.
- The remaining balance must be explicitly tracked in the system and displayed to the customer.

---

## Overpayment Handling

When a payment exceeds the invoice total:
- Allocate funds up to the invoice balance.
- Store the excess amount as customer credit (`customer_credits` or `unallocated_payment_balance`).
- Do not automatically apply the excess to other invoices unless business rules explicitly allow automated allocation.

---

## Underpayment Handling

If a payment is short by small fractions (e.g., due to exchange rates or wire fees):
- **Do not automatically close the invoice** unless within a human-owner approved write-off tolerance (e.g., less than $1.00).
- Otherwise, track the remaining balance as due or request manual adjustment.

---

## Reversal, Refund, and Correction Patterns

If a payment is refunded or reversed:
- **Do not delete** the original payment or invoice records.
- Create a reverse transaction entry (e.g., negative amount payment) to offset the ledger.
- Reopen the linked invoices by updating their `Balance Due` and changing status back to `Unpaid` or `Partially Paid`.

---

## Payment Allocation Rules

- Allocating a payment to an invoice creates a new `PaymentAllocation` join record storing the allocation amount, timestamp, and actor.
- Modifying or deleting an allocation requires an audit trail (reason, timestamp, actor).

---

## State Transition Rules

Settlement-related state changes (e.g., flagging an account as delinquent or suspended) must be driven by owner-approved business rules. Coding agents must not implement automated account locks without locked design approvals.

---

## Idempotency Notes

- All payment actions (charges, refunds, allocations) must support idempotency keys (e.g. `Idempotency-Key` headers) to prevent duplicate processing on network retries.
- Verify that backend endpoints refuse to process the same payment request ID twice.

---

## External Payment Provider Notes

- Do not assume payment gateway webhooks arrive in chronological order.
- Always check the database transaction status before updating payment states based on webhooks.
- Log raw webhook payloads in a database request/response log for debugging and audits.

---

## Reconciliation Notes

Reconciliation routines must run periodically to verify:
- Bank statement values = logged payments.
- Total invoice allocations = total payments minus unallocated credits.
- Mismatches must trigger immediate alerts to administrators.

---

## Out of Scope

- Specific payment gateway API SDK configurations (Stripe JS, PayPal SDK).
- Bank integration formats (MT940, BAI2 protocols).

---

## Guardrails

- [ ] **SEPARATE CASH FROM DEBT**: Cash receipt (payment) must be modeled as a distinct entity from its application (allocation).
- [ ] **NO HISTORY ERASURE**: Reversals and refunds must be recorded as adjusting records, never by deleting the original ledger entries.
- [ ] **IDEMPOTENT GATEWAY**: Ensure all payment gateway calls use idempotency keys.
- [ ] **EXPLICIT ALLOCATION AUDIT**: Changes to how payments are allocated against invoices must be logged in the audit log.

---

## QA Checklist

- [ ] Verify that a partial payment correctly updates the invoice status to `Partially Paid` and recalculates balance due.
- [ ] Test overpayment: verify excess is stored as customer credit.
- [ ] Test gateway retry: verify duplicate payment requests are blocked using idempotency keys.
- [ ] Test webhook ordering: verify out-of-order webhooks do not revert cleared payments to pending.
- [ ] Verify refunding a payment correctly reopens the unpaid balance on the associated invoice.

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Database schema definitions.
- [10-security-model.md](../../../core/docs/10-security-model.md) — Security and audit logging patterns.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created payment and settlement tracking guidelines | Antigravity |
