# Extension Pack: Financial Business Logic

> Adds financial calculation guardrails, audit trail requirements, and compliance-ready documentation templates.

---

## When to Use This Pack

Use this extension pack when your product:

- Handles **monetary values** (prices, balances, transactions, invoices)
- Performs **financial calculations** (tax, discounts, interest, amortization)
- Requires **audit trails** for financial transactions
- Must comply with **accounting standards** or financial regulations
- Needs **precision guarantees** (no floating-point errors in money)

---

## What This Pack Will Add (When Built)

### Additional Documents

| Document | Purpose |
|----------|---------|
| `financial-calculations-spec.md` | Exact formulas, rounding rules, precision requirements |
| `audit-trail-spec.md` | What events to log, retention policies, immutability rules |
| `currency-handling-guide.md` | Multi-currency support, exchange rates, conversion rules |
| `financial-reporting-spec.md` | Report formats, period handling, reconciliation rules |
| `compliance-checklist.md` | Regulatory requirements checklist per jurisdiction |

### Additional Prompts

| Prompt | Purpose |
|--------|---------|
| `financial-logic-agent-prompt.md` | AI agent role specialized in financial calculation implementation and validation |

### Additional Guardrails

- All monetary values must use integer/decimal types (never floating point)
- All calculations must follow documented formulas exactly
- Rounding rules must be explicitly defined and consistently applied
- All financial transactions must produce audit log entries
- Financial reports must reconcile to the source data
- No financial calculation may be modified without explicit approval and testing

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| Floating-point rounding errors in money | Enforces integer/decimal types and explicit rounding |
| Audit compliance failures | Defines audit trail spec with immutable logging |
| Incorrect tax or discount calculations | Requires exact formula documentation and validation |
| Financial data tampering | Audit trail and data integrity rules |
| Regulatory non-compliance | Compliance checklist per jurisdiction |

---

## Example Project Types

- Invoicing and billing systems
- Accounting software
- Point-of-sale (POS) systems
- Loan management platforms
- Payroll systems
- Financial dashboards and reporting tools
- Insurance claim processing

---

## Status

`Placeholder` — This extension pack contains only this README. Full content will be added in a future version.
