# 03 — MVP Scope

> Defines what is in and out of the first release of Invoice Tracker using MoSCoW prioritization.

---

## Purpose

Clearly define the boundaries of version 1 (MVP). This document prevents scope creep by providing a definitive list of what will and will not be built. Every feature, page, and capability must be traceable to this document.

## Status

`Approved`

---

## Must Have

Features that are essential for the product to function. Without these, there is no product.

| # | Feature | Related Flow | Related Pages | Notes |
|---|---------|-------------|---------------|-------|
| 1 | Auth/Login | Login | Login | Simple email/password login and session validation. |
| 2 | Client CRUD | Create client | Clients List, Client Detail, Create/Edit Client | Manage clients with basic billing details. |
| 3 | Invoice CRUD | Create invoice | Invoices List, Invoice Detail, Create/Edit Invoice | Create, update, list, and view detailed invoices. |
| 4 | Invoice item entry | Create invoice | Create/Edit Invoice | Enter items (description, rate, quantity) inline inside the invoice form. |
| 5 | Payment recording | Record payment | Record Payment | Manually record payments applied against an invoice. |
| 6 | Invoice status tracking | Mark invoice as sent, Overdue check | Invoice Detail, Invoices List | Derived status based on sent state, due date, invoice total, and payments. |
| 7 | Simple dashboard totals | View dashboard | Dashboard | View total billed, total paid, total outstanding, and overdue totals. |

---

## Should Have

Features that are important but the product can launch without them. Plan to add in a fast follow-up.

| # | Feature | Related Flow | Related Pages | Notes |
|---|---------|-------------|---------------|-------|
| 1 | Search and filtering for clients/invoices | View lists | Clients List, Invoices List | Simple text search by name or invoice number. |
| 2 | Simple Settings page | Settings | Settings | Basic settings (user password update, default business name). |

---

## Could Have

Nice-to-have features. Build only if time and budget allow.

| # | Feature | Related Flow | Related Pages | Notes |
|---|---------|-------------|---------------|-------|
| 1 | Export Invoice list to CSV | View lists | Invoices List | Simple client-side CSV export of visible rows. |

---

## Won't Have Now

Explicitly out of scope for version 1. Listing these prevents ambiguity and scope creep.

| # | Feature | Reason | Planned For |
|---|---------|--------|-------------|
| 1 | Online payment processing (Stripe/PayPal) | Relies on manual payment logging to avoid integrations. | v2 |
| 2 | Automated tax calculations | Requires external tax engines/logic. Done manually. | v2 |
| 3 | Multi-currency support | Fixed to a single currency (USD) to simplify database/UI. | v2 |
| 4 | Multi-tenant organization accounts | App is single-tenant. Only a single organization is tracked. | v2 |
| 5 | Native PDF generation | Users print or "Save to PDF" using their browser's print dialog. | v2 |
| 6 | Direct email sending | Invoices are marked "Sent" manually. Sent via personal inbox. | v2 |
| 7 | Recurring invoices | Invoices must be drafted and created individually. | v2 |
| 8 | Inventory integration or Accounting ledger | App is a lightweight invoicing tool, not an ERP. | v2 |

---

## MVP Completion Criteria

The MVP is considered complete when ALL of the following are true:
- All "Must Have" features are implemented and functional.
- All "Must Have" features pass QA validation.
- No critical or blocking bugs remain open.
- Security model is implemented as specified in `10-security-model.md`.
- All user roles (Owner, Staff) can complete their primary flows.
- Release checklist (`13-release-checklist.md`) passes.
- Human product owner has signed off.

### Acceptance Verification

| Check | Method | Pass Criteria |
|-------|--------|---------------|
| All Must Have features work | Manual QA walkthrough | 100% pass |
| Calculation Accuracy | Check formulas: total = sum(items), outstanding = total - paid | 100% correct calculations |
| No critical bugs | Bug tracker review | 0 critical |
| Security baseline met | Security checklist review | All items pass |

---

## Scope

- This document defines the **feature boundary** for version 1.
- Features listed here must align with the product brief.
- Each feature should be traceable to a user flow and page spec.

## Out of Scope

- Detailed specifications (see `06-pages-spec.md`)
- Technical implementation (see `08-architecture.md`)
- Timeline and delivery order (see `11-development-roadmap.md`)

## Guardrails

- [x] No AI agent may implement features not listed in "Must Have" or "Should Have" without explicit approval
- [x] "Won't Have Now" items must be actively rejected if they appear in batch requests
- [x] Adding new "Must Have" items requires human approval and a decision log entry
- [x] Moving items between categories requires human approval

## Related Files

- `01-product-brief.md` — Product definition this scope derives from
- `05-user-flows.md` — Detailed flows for each feature
- `06-pages-spec.md` — Page specifications for each feature
- `11-development-roadmap.md` — Delivery schedule for scoped features
- `14-decision-log.md` — Record of scope changes

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial version for Invoice Tracker | Product Owner |
