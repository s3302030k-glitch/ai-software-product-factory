# 01 — Product Brief

> The single source of truth for what the Invoice Tracker is, why it exists, and who it serves.

---

## Purpose

Define the product at the highest level. Every other document derives from this one. If the product brief doesn't say it, it's not part of the product.

## Status

`Approved`

---

## Product Name

```
Invoice Tracker
```

## Description

A simple web app for freelancers or small businesses to track clients, invoices, invoice items, and payments.

---

## Problem Statement

Freelancers and small business owners waste significant time tracking manual billing, matching payments, and calculating outstanding balances using generic spreadsheets or overly complex enterprise software. They need a simple, single-tenant, focused tool to manage their clients, create multi-item invoices, log payments manually, and quickly see their collection status.

---

## Target Users

Who are the primary users of this product?

| User Type | Description | Key Need |
|-----------|-------------|----------|
| Owner | Freelancer or small business founder. Has full control. | Needs dashboard totals, client lists, invoice status, and settings. |
| Staff | Employee or contractor who helps manage invoicing. | Needs to create invoices and clients, and log payments without settings access. |

---

## Current Alternatives

- **Spreadsheet templates (Excel/Google Sheets):** Free but error-prone, requiring manual cell formulas for status calculations.
- **Complex Enterprise Platforms (QuickBooks, FreshBooks):** Over-featured, expensive, and overwhelming for simple workflow needs.
- **Pen and Paper:** Highly inefficient, lacks automatic calculations, and is difficult to search or audit.

---

## Product Goal

Simplify tracking clients, invoice items, invoices, and payments in one clear, high-speed interface with automatic status tracking.

---

## Business Goal

Provide a high-efficiency utility that accelerates cash flow monitoring, reduces billing errors, and saves administrative time.

---

## Product Type

- [x] Web Application (SPA / Next.js)
- [ ] Web Application (MPA / SSR)
- [ ] Mobile App (Native)
- [ ] Mobile App (Hybrid / PWA)
- [ ] API / Backend Service
- [ ] Desktop Application
- [ ] CLI Tool
- [ ] Other: ___

---

## Product Size

`Small`

---

## First Version Summary

The MVP is a simple web-based Invoice Tracker. Users can log in, view a simple dashboard with financial totals, manage clients (CRUD), create invoices with multiple line items, record client payments, and track the status of invoices as they progress through **Draft**, **Sent**, **Partially Paid**, **Paid**, and **Overdue**. 

### Not In First Version
- Online payment processing (Stripe, PayPal)
- Tax automation/calculators
- Multi-currency support (USD only)
- Multi-tenant organization accounts (Single-tenant app)
- Native PDF generation (rely on browser print functionality)
- Direct email sending (copy sent status manually or email from personal inbox)
- Recurring invoice templates
- Inventory integration or accounting ledger sheets

---

## Success Criteria

How will you know version 1 is successful?

| Criteria | Measurement | Target |
|----------|-------------|--------|
| User can complete core flows | Manual QA of client, invoice, item, and payment CRUD | 100% pass |
| Calculation Accuracy | Verification of invoice totals, payments, and balance due | 100% correct calculations |
| Zero Critical Bugs | Bug count | 0 critical, < 2 minor |
| Page Load Performance | Chrome Lighthouse Score | > 90 |

---

## Open Questions

All fundamental MVP scope questions are resolved.

| # | Question | Impact | Status |
|---|----------|--------|--------|
| 1 | Should currency symbol be customizable? | Affects UI representation | `Resolved` — Fixed to default (USD) for MVP. |

---

## Scope

- This document defines **what** the product is and **why** it exists.
- It does **not** define how to build it (see `08-architecture.md`).
- It does **not** define the feature list (see `03-mvp-scope.md`).

## Out of Scope

- Technical implementation details
- UI/UX specifications
- Data model definitions
- Timeline or roadmap

## Guardrails

- [x] Product brief must be approved before development begins
- [x] Changes to the product brief require human approval and a decision log entry
- [x] No AI agent may modify this document without explicit authorization

## Related Files

- `02-discovery.md` — Research that informs this brief
- `03-mvp-scope.md` — Feature prioritization derived from this brief
- `04-user-roles.md` — Detailed role definitions for target users listed here
- `14-decision-log.md` — Decisions that affect or modify the brief

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial version for Invoice Tracker | Product Owner |
