# 07D — Financial Domain Model Guidelines

> Defines principles, entity boundaries, lifecycles, and correction patterns for products containing core financial and business data structures.

---

## Purpose

Establish clear rules for modeling financial records, transactions, and contracts. This document ensures data structures are designed to be audit-compliant, robust, and clean, preventing common architecture bugs like mixing customer intent with payments or burying business logic in user interface views.

## Status

`Active` — Must be referenced during design, data modeling, and code review phases for any financial capabilities.

---

## When to Use

Use this guideline when designing:
- Business contracts, sales orders, purchase orders, or subscription objects.
- Invoicing and billing sub-systems.
- Ledger systems, transaction records, and double-entry accounting models.
- Any entity that records a legal or financial obligation between parties.

---

## Financial Domain Modeling Principles

1. **Immutability First**: Historically recorded transactions should not be modified. If errors occur, correct them with adjustment entries, not by editing existing rows.
2. **Explicit Lifecycles**: Every financial entity must have a clear state transition path. No entity should exist in an undefined or implicit state.
3. **Decouple View from Logic**: Business rules (pricing, interest rates, tax calculations) must reside in core domain services or backend database schemas. They must never be implemented inside UI components.

---

## Source-of-Truth Rules

- The database is the single source of truth for transaction state and balances.
- Calculated totals must be computed on the server side using the database-stored fields as inputs.
- Client applications must never send a pre-calculated total to the server to be written as the final truth. The server must re-calculate and validate all totals.

---

## Entity Separation Principles

To prevent data model tangling, maintain a strict division between:
- **Agreements / Commitments**: Contracts, subscriptions, quotes, and pricing sheets.
- **Transactional Intents**: Invoices, billing statements, and purchase orders.
- **Settlement Actions**: Payments, bank transfers, credit applications, and cash receipts.

Never merge these roles into a single database table. For example, do not track whether an invoice is paid by simply updating a status column without creating a corresponding payment allocation record.

```
┌────────────────────────┐         ┌────────────────────────┐         ┌────────────────────────┐
│     COMMITMENTS        │         │  TRANSACTION INTENTS   │         │   SETTLEMENT ACTIONS   │
│  (e.g., Subscription)  ├────────►│    (e.g., Invoice)     ├────────►│  (e.g., Payment/Alloc) │
│                        │         │                        │         │                        │
│ Defines expected terms │         │ Details obligation to  │         │ Records actual cash    │
│ and schedules          │         │ pay on a specific date │         │ movement & application │
└────────────────────────┘         └────────────────────────┘         └────────────────────────┘
```

---

## Invoice, Order, Contract, Payment, and Settlement Distinction

| Entity Category | Core Role | Mutability Rules | State Trigger |
|---|---|---|---|
| **Contract** | Defines long-term terms and conditions. | Mutable only via signed amendments. | Activation / Expiration |
| **Order** | Records a customer request and price agreements. | Immutable after confirmation. | Approval / Fulfillment |
| **Invoice** | Records a formal demand for payment. | Immutable once posted. | Posting / Settlement / Voiding |
| **Payment** | Records cash receipt from external gateway/bank. | Immutable once cleared. | Authorization / Settlement / Reversal |
| **Settlement** | Allocates payments against outstanding invoices. | Immutable once applied. | Reconciliation / Correction |

---

## Mutable vs Immutable Financial Records

- **Mutable Records (Draft States)**: Objects during preparation (e.g., Draft Invoices, Pending Orders) can be modified.
- **Immutable Records (Posted States)**: Once a record is finalized (e.g., Posted Invoice, Cleared Payment), it must become write-locked. No database `UPDATE` statement should be allowed to modify the financial values (amounts, dates, quantities) of these records.

---

## Draft, Posted, Voided, and Cancelled States

Every financial record must transition through an explicit state machine:
1. **Draft**: Modifications allowed. Calculations are preliminary.
2. **Posted / Issued**: The record is locked. It represents a legal obligation.
3. **Voided**: Used to neutralize an incorrectly posted record. Requires a documented reason.
4. **Cancelled**: Used for intents (orders/contracts) that were approved but terminated before invoicing or delivery.

```
           ┌──────────┐
           │  Draft   │
           └────┬─────┘
                │
                ▼ (Post Action)
           ┌──────────┐
           │  Posted  │
           └────┬─────┘
                ├──────────────────────┐
                ▼ (Void Action)        ▼ (Settle Action)
           ┌──────────┐          ┌───────────┐
           │  Voided  │          │  Settled  │
           └──────────┘          └───────────┘
```

---

## Adjustment and Correction Patterns

If a posted record (e.g., an invoice for $100) is incorrect:
- **Do not edit** the original record's rows.
- **Credit Memo / Debit Memo Pattern**: Issue a separate matching record that adjusts the balance. A credit memo of $20 reduces the active balance to $80.
- **Reversal and Re-issue Pattern**: Void the incorrect invoice entirely and post a new corrected invoice with a new unique identifier, linking back to the original for audit traceability.

---

## Derived Values vs Stored Values

- **Stored Values**: Raw transaction inputs (unit price, quantity, tax rate) must be saved in the database.
- **Derived Values**: Totals (subtotal, tax amount, grand total) should generally be stored on the record once posted. This preserves the calculation result against future formula or rate changes.
- **Documentation**: All derived values must have their calculation formulas documented in the database schema annotations and the project specification files.

---

## Business Rule Ownership

- **Human Owner**: Owns the rules defining prices, interest rates, tax regions, and payment states.
- **AI Agents**: Can design database schemas and write calculation helpers based on approved guidelines, but must flag any proposed alterations to pricing policies or state transition rules for human owner approval.

---

## Out of Scope

- Database-specific migration SQL scripts (see [database-migration-guidelines.md](../../supabase-pack/docs/database-migration-guidelines.md) if using Supabase).
- Specific code libraries for interest rates or tax APIs.
- Legal, tax, accounting, or regulated financial advice.

---

## Guardrails

- [ ] **NO UI LOGIC**: Do not hide financial calculations or business rules inside UI code.
- [ ] **SEPARATE INTENT**: Keep intent records (invoices) separate from settlement records (payments).
- [ ] **LOCKED POSTING**: Ensure once an entity status transitions to `Posted`, the amounts and line items cannot be modified by standard database queries.
- [ ] **OWNER SIGN-OFF**: Any modification to financial state transition paths or business logic ownership rules requires human owner sign-off.

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Core database schema guidelines.
- [10-security-model.md](../../../core/docs/10-security-model.md) — Security boundaries and data logging rules.
- [15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Agent constraints and behavior.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created financial domain modeling and lifecycle guidelines | Antigravity |
