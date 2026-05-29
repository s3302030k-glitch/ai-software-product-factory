# 14 — Decision Log

> Records all significant architectural, product, and process decisions with context and rationale for the Invoice Tracker.

---

## Purpose

Maintain an append-only log of decisions so that any team member or AI agent can understand **why** things are the way they are. Prevents re-litigating past decisions and provides context for future changes.

## Status

`Active`

---

## Decision Index

| ID | Title | Date | Status | Impact |
|----|-------|------|--------|--------|
| DEC-001 | Use Small MVP Scope | 2026-05-29 | `Accepted` | High |
| DEC-002 | Fixed Single Currency (USD) for MVP | 2026-05-29 | `Accepted` | Medium |
| DEC-003 | Payments Are Manually Recorded | 2026-05-29 | `Accepted` | Medium |
| DEC-004 | Invoice Status Is Dynamically Derived | 2026-05-29 | `Accepted` | High |
| DEC-005 | PDF Generation and Email Sending Out of Scope | 2026-05-29 | `Accepted` | High |

---

### DEC-001: Use Small MVP Scope
- **Date:** 2026-05-29 | **Status:** `Accepted` | **Decided By:** Product Owner | **Impact:** High
- **Context:** We need a small, highly focused tool for freelancers to track outstanding bills without incurring high operational or codebase complexity.
- **Options Considered:**
  - *Option A:* Build a full multi-tenant bookkeeping SaaS. (High cost, long timeline).
  - *Option B:* Build a single-tenant Invoice Tracker with Client/Invoice/Payment CRUD only. (Low complexity, highly focused).
- **Decision:** Build Option B. Enforce a single-tenant setup tracking basic clients, billing items, and manual cash flow records.
- **Consequences:**
  - *Positive:* Fast development timeline, low code maintenance.
  - *Negative:* Not saleable immediately as a multi-user SaaS.

---

### DEC-002: Fixed Single Currency (USD) for MVP
- **Date:** 2026-05-29 | **Status:** `Accepted` | **Decided By:** Architect | **Impact:** Medium
- **Context:** Freelancers frequently bill international clients. Standardizing multi-currency database columns adds exchange rate sync overhead.
- **Options Considered:**
  - *Option A:* Store numeric values and link each invoice to a currency code, converting values via an API.
  - *Option B:* Strict single-currency schema (USD).
- **Decision:** Option B. Default all UI references to USD (`$`) and enforce numeric currency fields to a single base currency without conversions.
- **Consequences:**
  - *Positive:* Simplifies invoice total and payment aggregations in SQL queries.
  - *Negative:* Restricts product utility for non-US users.

---

### DEC-003: Payments Are Manually Recorded
- **Date:** 2026-05-29 | **Status:** `Accepted` | **Decided By:** Product Owner | **Impact:** Medium
- **Context:** Collecting money via bank transfers or cards requires payment gateways which charge fees and require KYC setup.
- **Options Considered:**
  - *Option A:* Integrate Stripe or PayPal API for automated invoice checkouts.
  - *Option B:* Users collect money offline (checks, bank wires, cash) and manually input the receipt amounts.
- **Decision:** Option B. The system acts as a ledger/tracker rather than a processor.
- **Consequences:**
  - *Positive:* Saves significant regulatory overhead and API complexity.
  - *Negative:* Higher friction for the end user to mark things paid.

---

### DEC-004: Invoice Status Is Dynamically Derived
- **Date:** 2026-05-29 | **Status:** `Accepted` | **Decided By:** Database Engineer | **Impact:** High
- **Context:** Keeping invoice status (Draft, Sent, Partially Paid, Paid, Overdue) synchronized with payments and current dates can lead to race conditions if status is stored statically in a DB column.
- **Options Considered:**
  - *Option A:* Store `status` as a static database enum column and update it using triggers or application state listeners.
  - *Option B:* Deriving status dynamically in the query or API layer by comparing due dates, total items, and payments.
- **Decision:** Option B. Maintain single sources of truth (`sent_at`, `due_date`, sums of items/payments) and compute status badge dynamically.
- **Consequences:**
  - *Positive:* Zero data inconsistencies. Status naturally transitions to Overdue when date rolls over.
  - *Negative:* Slightly higher query processing workload.

---

### DEC-005: PDF Generation and Email Sending Out of Scope
- **Date:** 2026-05-29 | **Status:** `Accepted` | **Decided By:** Product Owner | **Impact:** High
- **Context:** Sending emails directly from the app requires mail hosts (SMTP, Mailgun) and domain validation. PDF rendering engines require server-side Chromium or canvas layout tools.
- **Options Considered:**
  - *Option A:* Build server-side PDF conversion and automated SMTP email dispatch.
  - *Option B:* Rely on standard web browser print styling (CSS `@media print`) to allow "Print/Save to PDF" and let users mark invoices "Sent" manually.
- **Decision:** Option B. Use standard CSS print styles and let the user handle transmission externally.
- **Consequences:**
  - *Positive:* Zero external service dependencies.
  - *Negative:* Users must manually download browser PDFs and attach them to emails.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial version for Invoice Tracker | Product Owner |
