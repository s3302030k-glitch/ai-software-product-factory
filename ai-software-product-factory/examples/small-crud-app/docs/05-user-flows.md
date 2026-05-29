# 05 — User Flows

> Step-by-step descriptions of how users interact with the Invoice Tracker to accomplish their goals.

---

## Purpose

Define every significant user interaction as a traceable flow with preconditions, main path, edge cases, error states, and rollback options. User flows bridge the gap between MVP scope and page specs.

## Status

`Approved`

---

## Flow Index

List of all flows defined in this document:

| Flow ID | Flow Name | Actor | Priority | Status |
|---------|-----------|-------|----------|--------|
| FLOW-001 | User Login | Owner / Staff | Must Have | `Complete` |
| FLOW-002 | Create Client | Owner / Staff | Must Have | `Complete` |
| FLOW-003 | Create Invoice (with items) | Owner / Staff | Must Have | `Complete` |
| FLOW-004 | Record Payment | Owner / Staff | Must Have | `Complete` |
| FLOW-005 | View Dashboard | Owner / Staff | Must Have | `Complete` |
| FLOW-006 | Mark Invoice as Sent | Owner / Staff | Must Have | `Complete` |
| FLOW-007 | Overdue Invoice Check | Owner / Staff | Must Have | `Complete` |

---

### Flow: User Login
**ID:** `FLOW-001`  
**Priority:** Must Have  
**Actor:** Owner / Staff  
**Goal:** Authenticate and access dashboard.  
**Trigger:** Navigates to `/login`.

#### Preconditions
- [x] User has a registered account.

#### Main Path
1. **User** enters email and password on `/login` and clicks "Login".
2. **System** hashes password, verifies against the database, creates session, and redirects user to `/dashboard`.

#### Edge Cases / Errors
- **Wrong password:** System shows "Invalid email or password" validation message. User re-enters credentials.
- **Server offline:** System shows "Server error, please try again later" toast.

#### Rollback / Undo Notes
- **Logout:** User can click "Logout" in header to invalidate session and redirect to `/login`.

---

### Flow: Create Client
**ID:** `FLOW-002`  
**Priority:** Must Have  
**Actor:** Owner / Staff  
**Goal:** Create a client record for invoicing.  
**Trigger:** Clicks "New Client" button on `/clients`.

#### Preconditions
- [x] User is logged in.

#### Main Path
1. **User** fills in Client Form (Name, Billing Address, Email) and clicks "Save Client".
2. **System** validates inputs, inserts record into database, and redirects to `/clients` showing a "Client created successfully" toast.

#### Edge Cases / Errors
- **Empty client name:** System shows "Client name is required" validation under the input field.
- **Duplicate name:** System allows it (to support clients with identical names), but encourages adding unique business identifier (e.g. Acme Corp vs Acme LLC).

#### Rollback / Undo Notes
- **Deletion:** Owner can delete the client via Client Detail screen (un-reversible hard delete). Staff cannot delete.

---

### Flow: Create Invoice
**ID:** `FLOW-003`  
**Priority:** Must Have  
**Actor:** Owner / Staff  
**Goal:** Create a drafted invoice with billing line items.  
**Trigger:** Clicks "New Invoice" button on `/invoices`.

#### Preconditions
- [x] User is logged in.
- [x] At least one Client exists in the system.

#### Main Path
1. **User** selects Client, sets Issue Date and Due Date, enters one or more line items (Description, Unit Rate, Quantity).
2. **System** calculates line totals, aggregates invoice sum, validates fields.
3. **User** clicks "Save Invoice".
4. **System** inserts Invoice and InvoiceItems in database, sets status to `Draft`, and redirects to `/invoices` with success message.

#### Edge Cases / Errors
- **No line items entered:** System shows error "At least one line item is required" on submit.
- **Due Date before Issue Date:** System highlights Due Date input with "Due Date cannot be before Issue Date" message.

#### Rollback / Undo Notes
- **Edit/Delete:** Invoice can be edited or deleted (Owner only) while in `Draft` state.

---

### Flow: Record Payment
**ID:** `FLOW-004`  
**Priority:** Must Have  
**Actor:** Owner / Staff  
**Goal:** Manually record a partial or full payment against a sent invoice.  
**Trigger:** Clicks "Record Payment" button on `/invoices/[id]`.

#### Preconditions
- [x] User is logged in.
- [x] Invoice exists and status is `Sent` or `Partially Paid`.

#### Main Path
1. **User** enters Payment Amount, Date, and payment method (Cash, Check, Bank Transfer) in the record payment modal.
2. **System** validates that amount is positive and does not exceed outstanding balance.
3. **User** clicks "Save Payment".
4. **System** creates Payment record, updates Invoice paid amount, derives new Invoice status (`Paid` or `Partially Paid`), and updates the Invoice Detail view.

#### Edge Cases / Errors
- **Overpayment:** User enters amount > balance due. System displays validation message "Payment cannot exceed remaining invoice balance" unless overridden. (Fixed constraint: no overpayments allowed in MVP).

#### Rollback / Undo Notes
- **Delete payment:** Owner (only) can view payments list on `/invoices/[id]` and click "Delete" (trash can) next to a payment. The system deletes the payment, recalculates paid amount, and shifts status back (e.g. from `Paid` to `Sent` or `Partially Paid`).

---

### Flow: View Dashboard
**ID:** `FLOW-005`  
**Priority:** Must Have  
**Actor:** Owner / Staff  
**Goal:** Monitor overall business billing health.  
**Trigger:** Logs in or clicks "Dashboard" in sidebar.

#### Preconditions
- [x] User is logged in.

#### Main Path
1. **User** navigates to `/dashboard`.
2. **System** queries db to aggregate:
   - Total Billed (sum of all invoices except `Draft`)
   - Total Paid (sum of all payment records)
   - Total Outstanding (Total Billed minus Total Paid)
   - Total Overdue (sum of balance due for `Sent` or `Partially Paid` invoices where due date < current date)
3. **System** renders these values in 4 clear card grids.

#### Edge Cases / Errors
- **No data:** System shows $0.00 for all totals and an empty state illustration.

---

### Flow: Mark Invoice as Sent
**ID:** `FLOW-006`  
**Priority:** Must Have  
**Actor:** Owner / Staff  
**Goal:** Mark a drafted invoice as sent to the client.  
**Trigger:** Clicks "Mark as Sent" on `/invoices/[id]`.

#### Preconditions
- [x] User is logged in.
- [x] Invoice exists and status is `Draft`.

#### Main Path
1. **User** views draft invoice on `/invoices/[id]` and clicks "Mark as Sent".
2. **System** updates invoice status to `Sent` and record `sent_at` timestamp. Renders new status badge.

#### Edge Cases / Errors
- **Already Sent:** Button is disabled.

---

### Flow: Overdue Invoice Check
**ID:** `FLOW-007`  
**Priority:** Must Have  
**Actor:** System (Automated check / Derived status)  
**Goal:** Flag unpaid invoices whose due date has passed.  
**Trigger:** Evaluated automatically upon view.

#### Preconditions
- [x] Invoices exist in database.

#### Main Path
1. **User** requests a page containing Invoice status (Dashboard, Invoices List, Invoice Detail).
2. **System** evaluates: if status is `Sent` or `Partially Paid` and `due_date` is before current date, system displays status badge as `Overdue` in UI. (Dynamic status derivation).

---

## Scope

- This document defines **how users interact with the product step by step**.
- Each flow must map to at least one feature in `03-mvp-scope.md`.

## Out of Scope

- Page layout and visual design (see `06-pages-spec.md`)
- API contracts (see `09-api-design.md`)
- Data model details (see `07-data-model.md`)

## Guardrails

- [x] Every "Must Have" feature must have at least one flow defined
- [x] Every flow must specify error states — no "happy path only" flows
- [x] Flows must reference roles from `04-user-roles.md`

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial version for Invoice Tracker | Product Owner |
