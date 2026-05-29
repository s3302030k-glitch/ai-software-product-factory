# 06 — Pages Spec

> Detailed specification for every page in the Invoice Tracker application, including fields, actions, states, and permissions.

---

## Purpose

Define exactly what appears on each page, how it behaves, what data it shows, and who can access it. This document is the implementation blueprint for frontend development.

## Status

`Approved`

---

## Page Index

| Page Name | Route | Role Access | Priority | Status |
|-----------|-------|-------------|----------|--------|
| Login | `/login` | Guest | Must Have | `Complete` |
| Dashboard | `/dashboard` | Owner, Staff | Must Have | `Complete` |
| Clients List | `/clients` | Owner, Staff | Must Have | `Complete` |
| Client Detail | `/clients/[id]` | Owner, Staff | Must Have | `Complete` |
| Create/Edit Client | `/clients/form` | Owner, Staff | Must Have | `Complete` |
| Invoices List | `/invoices` | Owner, Staff | Must Have | `Complete` |
| Invoice Detail | `/invoices/[id]` | Owner, Staff | Must Have | `Complete` |
| Create/Edit Invoice | `/invoices/form` | Owner, Staff | Must Have | `Complete` |
| Record Payment | `/invoices/[id]/payment` | Owner, Staff | Must Have | `Complete` |
| Settings | `/settings` | Owner | Should Have | `Complete` |

---

### 1. Login Page
- **Route:** `/login` | **Layout:** Auth (centered card, no sidebar) | **Access:** Guest
- **Purpose:** Securely log in user.

#### Fields & Validations
- `Email` (text, required, valid email format).
- `Password` (password, required, minimum 8 characters).

#### Actions
- **Sign In:** Submits form, validates session, redirects to `/dashboard`.

#### Loading / Error States
- Loading: Disable button, show spinner.
- Error: Inline text "Invalid email or password" below header.

---

### 2. Dashboard Page
- **Route:** `/dashboard` | **Layout:** Sidebar Navigation | **Access:** Owner, Staff
- **Purpose:** Overall cash flow visualization.

#### Main Components & Data
- **Summary Cards (4):** 
  - *Total Billed:* Sum of all sent/paid invoices.
  - *Total Paid:* Sum of all logged payment amounts.
  - *Outstanding Balance:* Total Billed - Total Paid.
  - *Overdue Balance:* Total Outstanding of invoices past due date.
- **Recent Invoices Table:** Displays 5 most recently updated invoices.

#### Actions
- **Quick Actions:** Buttons to "Create Invoice", "Create Client".

---

### 3. Clients List Page
- **Route:** `/clients` | **Layout:** Sidebar Navigation | **Access:** Owner, Staff
- **Purpose:** Browse and search client profiles.

#### Table Columns
- **Name:** Link to `/clients/[id]`.
- **Email:** Plain text.
- **Invoice Count:** Number of invoices created for this client.
- **Total Billed:** Derived sum of client's invoices.
- **Actions:** View link, Edit link, Delete icon (Owner only).

#### Actions & Filters
- **Search:** Text filter matching name.
- **New Client:** Clicks to `/clients/form` (Create mode).
- **Delete Client:** (Owner only) Triggers confirmation modal: "Are you sure? This will delete all client data."

#### Empty State
- Show message "No clients found. Click 'New Client' to get started."

---

### 4. Client Detail Page
- **Route:** `/clients/[id]` | **Layout:** Sidebar Navigation | **Access:** Owner, Staff
- **Purpose:** Profile overview and billing history.

#### Main Components
- **Header:** Client Name, Email, Phone, Address.
- **Summary:** Lifetime Billed, Lifetime Paid, Remaining Balance.
- **Invoice History Table:** List of client invoices (Invoice #, Date, Due Date, Total, Status).

#### Actions
- **Edit Client:** Navigates to edit form.
- **Create Invoice:** Navigates to `/invoices/form` pre-selected with this Client.

---

### 5. Create/Edit Client Page (Client Form)
- **Route:** `/clients/form` (optional query query `?id=[id]`) | **Layout:** Sidebar | **Access:** Owner, Staff
- **Purpose:** Form to enter client profile details.

#### Fields & Validations
| Field | Type | Required | Validation | Default | Notes |
|-------|------|----------|------------|---------|-------|
| Name | `text` | Yes | Max 100 characters | Empty | Unique |
| Contact Email | `email` | Yes | Valid email format | Empty | Billing communications |
| Billing Address | `textarea` | Yes | Max 250 characters | Empty | Linebreaks preserved |

#### Actions
- **Save:** Submits form, validates, saves to DB, redirects to `/clients`.
- **Cancel:** Discards and navigates back to list.

---

### 6. Invoices List Page
- **Route:** `/invoices` | **Layout:** Sidebar | **Access:** Owner, Staff
- **Purpose:** Browse, filter, and search invoices.

#### Table Columns
- **Invoice Number:** e.g., `INV-0001` (Link to `/invoices/[id]`).
- **Client:** Client name.
- **Issue Date:** Formatted date `YYYY-MM-DD`.
- **Due Date:** Formatted date `YYYY-MM-DD`.
- **Total Amount:** Derived invoice sum.
- **Balance Due:** Invoice total - Paid.
- **Status:** Badge (Draft/Sent/Partially Paid/Paid/Overdue).

#### Filters & Actions
- **Status Filter:** Select (All, Draft, Sent, Partially Paid, Paid, Overdue).
- **Search:** Text matching Invoice Number or Client Name.
- **New Invoice:** Navigates to `/invoices/form`.

---

### 7. Invoice Detail Page
- **Route:** `/invoices/[id]` | **Layout:** Sidebar | **Access:** Owner, Staff
- **Purpose:** Detailed view of invoice items, status, and associated payments.

#### Main Components
- **Status Header:** Displays status badge.
- **Meta Block:** Invoice Number, Issue Date, Due Date, Client Billing Details.
- **Line Items Table:** Description, Rate, Quantity, Total.
- **Summary Block:** Subtotal, Paid Amount, Balance Due.
- **Payments Log:** Lists date, amount, and payment method for payments logged.

#### Actions
- **Mark as Sent:** (Visible in `Draft` state) Transitions status to `Sent`.
- **Record Payment:** (Visible if status is `Sent` or `Partially Paid`) Opens payment input.
- **Delete:** (Owner only) Danger confirmation modal to delete.

---

### 8. Create/Edit Invoice Page (Invoice Form)
- **Route:** `/invoices/form` | **Layout:** Sidebar | **Access:** Owner, Staff
- **Purpose:** Compile client invoice details and line items.

#### Fields & Validations
- `Client ID` (select dropdown, required).
- `Issue Date` (date, required, default: today).
- `Due Date` (date, required, must be >= Issue Date).
- `Line Items` (dynamic array list, must have >= 1 item):
  - `Description` (text, required).
  - `Rate` (decimal, required, > 0).
  - `Quantity` (integer, required, > 0).

#### Actions
- **Add Item:** Appends a blank item row in the UI.
- **Remove Item:** Deletes a row.
- **Save Invoice:** Persists record.
- **Cancel:** Returns to `/invoices`.

---

### 9. Record Payment Page (Modal/Form)
- **Route:** `/invoices/[id]/payment` (implemented as a modal overlay) | **Access:** Owner, Staff
- **Purpose:** Add payment record to outstanding invoices.

#### Fields & Validations
| Field | Type | Required | Validation | Default |
|-------|------|----------|------------|---------|
| Amount | `decimal` | Yes | > 0, <= Remaining Balance | Remaining Balance |
| Paid Date | `date` | Yes | No future dates | Today |
| Method | `select` | Yes | Cash, Check, Bank Transfer | Bank Transfer |

#### Actions
- **Save Payment:** Deducts amount, triggers status derivation, closes modal.

---

### 10. Settings Page
- **Route:** `/settings` | **Layout:** Sidebar | **Access:** Owner (Staff gets 403)
- **Purpose:** App settings management.

#### Fields & Validations
- `Business Name` (text, required, default default profile).
- `Current Password` (password, required to update profile).
- `New Password` (password, optional, min 8 chars).

---

## Permissions Table

Detailed page operation authorization check:

| Page / Route | Guest | Staff | Owner |
|--------------|-------|-------|-------|
| `/login` | View | Redirect | Redirect |
| `/dashboard` | Redirect | View | View |
| `/clients` | Redirect | View / Create / Edit | View / Create / Edit / Delete |
| `/invoices` | Redirect | View / Create / Edit / Mark Sent | View / Create / Edit / Mark Sent / Delete |
| Record Payment | Redirect | Create | Create / Delete |
| `/settings` | Redirect | 403 Forbidden | View / Edit |

---

## Out of Scope (UI/UX)
- Printable PDF customization screen.
- Charting libraries for dashboard trends (numbers only for MVP).
- File upload for client logos.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation for Invoice Tracker | Product Owner |
