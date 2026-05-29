# 07 — Data Model

> Defines all entities, fields, relationships, business rules, and data integrity constraints for the Invoice Tracker.

---

## Purpose

Provide a complete, implementation-ready data model that the Database Engineer and Coding Agent use to build the database schema. Every entity, field, relationship, and business rule is specified here.

## Status

`Approved`

---

## Entity Index

| Entity | Table Name | Priority | Status |
|--------|-----------|----------|--------|
| User | `users` | Must Have | `Complete` |
| Client | `clients` | Must Have | `Complete` |
| Invoice | `invoices` | Must Have | `Complete` |
| InvoiceItem | `invoice_items` | Must Have | `Complete` |
| Payment | `payments` | Must Have | `Complete` |

---

### 1. Entity: User
- **Table Name:** `users`
- **Description:** Application access profile (Owner or Staff).

#### Fields
| Field | Column Name | Type | Nullable | Default | Unique | Notes |
|-------|------------|------|----------|---------|--------|-------|
| ID | `id` | `uuid` | No | Auto | Yes | Primary key |
| Email | `email` | `varchar(150)` | No | — | Yes | Login identity |
| Password Hash | `password_hash` | `varchar(255)` | No | — | No | bcrypt encrypted |
| Role | `role` | `enum('owner','staff')` | No | `'staff'` | No | Access control |
| Created At | `created_at` | `timestamp` | No | `now()` | No | |
| Updated At | `updated_at` | `timestamp` | No | `now()` | No | |

---

### 2. Entity: Client
- **Table Name:** `clients`
- **Description:** Customer business profile.

#### Fields
| Field | Column Name | Type | Nullable | Default | Unique | Notes |
|-------|------------|------|----------|---------|--------|-------|
| ID | `id` | `uuid` | No | Auto | Yes | Primary key |
| Name | `name` | `varchar(100)` | No | — | Yes | Client business name |
| Email | `email` | `varchar(150)` | No | — | No | Billing address |
| Address | `text` | `text` | No | — | No | Mailing address |
| Created At | `created_at` | `timestamp` | No | `now()` | No | |
| Updated At | `updated_at` | `timestamp` | No | `now()` | No | |

#### Relationships
- **Invoices:** One-to-Many with `invoices` (Foreign Key `client_id` on `invoices`). On delete Restrict (cannot delete client if they have active invoices).

---

### 3. Entity: Invoice
- **Table Name:** `invoices`
- **Description:** Billing statement containing metadata and dates.

#### Fields
| Field | Column Name | Type | Nullable | Default | Unique | Notes |
|-------|------------|------|----------|---------|--------|-------|
| ID | `id` | `uuid` | No | Auto | Yes | Primary Key |
| Invoice Number | `invoice_number` | `varchar(50)` | No | — | Yes | Auto-incrementing code (e.g. `INV-0001`) |
| Client ID | `client_id` | `uuid` | No | — | No | FK → `clients.id` |
| Issue Date | `issue_date` | `date` | No | `current_date` | No | Date billing created |
| Due Date | `due_date` | `date` | No | — | No | Date payment required |
| Sent At | `sent_at` | `timestamp` | Yes | `null` | No | Timestamp if invoice marked as Sent |
| Created At | `created_at` | `timestamp` | No | `now()` | No | |
| Updated At | `updated_at` | `timestamp` | No | `now()` | No | |

#### Relationships
- **Client:** Many-to-One with `clients` (Foreign Key `client_id` -> `clients.id`, On Delete Restrict).
- **Items:** One-to-Many with `invoice_items` (On Delete Cascade).
- **Payments:** One-to-Many with `payments` (On Delete Cascade).

---

### 4. Entity: InvoiceItem
- **Table Name:** `invoice_items`
- **Description:** Individual line item detail for an invoice.

#### Fields
| Field | Column Name | Type | Nullable | Default | Unique | Notes |
|-------|------------|------|----------|---------|--------|-------|
| ID | `id` | `uuid` | No | Auto | Yes | Primary Key |
| Invoice ID | `invoice_id` | `uuid` | No | — | No | FK → `invoices.id` |
| Description | `description` | `text` | No | — | No | Task/item details |
| Unit Rate | `unit_rate` | `numeric(12,2)` | No | — | No | Item rate (USD) |
| Quantity | `quantity` | `integer` | No | `1` | No | Amount purchased |
| Created At | `created_at` | `timestamp` | No | `now()` | No | |
| Updated At | `updated_at` | `timestamp` | No | `now()` | No | |

#### Relationships
- **Invoice:** Many-to-One with `invoices` (Foreign Key `invoice_id` -> `invoices.id`, On Delete Cascade).

---

### 5. Entity: Payment
- **Table Name:** `payments`
- **Description:** Transaction log of client payment against an invoice.

#### Fields
| Field | Column Name | Type | Nullable | Default | Unique | Notes |
|-------|------------|------|----------|---------|--------|-------|
| ID | `id` | `uuid` | No | Auto | Yes | Primary Key |
| Invoice ID | `invoice_id` | `uuid` | No | — | No | FK → `invoices.id` |
| Amount | `amount` | `numeric(12,2)` | No | — | No | Payment value (USD) |
| Paid Date | `paid_date` | `date` | No | `current_date` | No | Date payment logged |
| Payment Method | `payment_method` | `enum('cash','check','bank_transfer')` | No | `'bank_transfer'` | No | |
| Created At | `created_at` | `timestamp` | No | `now()` | No | |
| Updated At | `updated_at` | `timestamp` | No | `now()` | No | |

#### Relationships
- **Invoice:** Many-to-One with `invoices` (Foreign Key `invoice_id` -> `invoices.id`, On Delete Cascade).

---

## Business Calculations & Status Derivation Rules

The following core business logic rules must be consistently applied:

### 1. Invoice Total
- **Formula:** `invoice_total = sum(item.unit_rate * item.quantity)` for all items associated with the invoice.
- **Enforcement:** Calculated dynamically in queries or pre-calculated and cached on row write.

### 2. Paid Amount
- **Formula:** `paid_amount = sum(payment.amount)` for all payments linked to the invoice. If no payments exist, `paid_amount = 0.00`.

### 3. Balance Due
- **Formula:** `balance_due = invoice_total - paid_amount`.
- **Constraint:** Overpayments are NOT allowed for MVP. Payments that make `paid_amount > invoice_total` must be rejected at database/application layer.

### 4. Invoice Status Derivation
Invoice status is dynamically derived using the following logic hierarchy:
- If `sent_at` is `null`, status is **`Draft`**.
- If `sent_at` is not null:
  - If `paid_amount == 0.00`:
    - If current date > `due_date`, status is **`Overdue`**.
    - Else, status is **`Sent`**.
  - If `paid_amount > 0.00` and `paid_amount < invoice_total`:
    - If current date > `due_date`, status is **`Overdue`**.
    - Else, status is **`Partially Paid`**.
  - If `paid_amount == invoice_total`, status is **`Paid`**.

---

## Validation Rules

| Entity | Field | Rule | Error Message |
|--------|-------|------|---------------|
| Client | `name` | Required, 1-100 characters, unique in DB. | "Client name is required and must be unique." |
| Invoice | `due_date` | Must be greater than or equal to `issue_date`. | "Due Date cannot be earlier than Issue Date." |
| InvoiceItem | `unit_rate` | Must be a positive decimal value > 0. | "Unit rate must be greater than 0." |
| InvoiceItem | `quantity` | Must be an integer >= 1. | "Quantity must be at least 1." |
| Payment | `amount` | Must be > 0 and <= remaining balance due. | "Payment amount must be greater than 0 and cannot exceed the balance due." |

---

## Indexing Notes

| Table | Index Name | Columns | Reason |
|-------|------------|---------|--------|
| `users` | `idx_users_email` | `email` (Unique) | Quick lookup during authentication |
| `invoices` | `idx_invoices_client` | `client_id` | Foreign Key lookups |
| `invoices` | `idx_invoices_due` | `due_date` | Date range status checks |
| `invoice_items` | `idx_items_invoice` | `invoice_id` | Fetching invoice line items |
| `payments` | `idx_payments_invoice` | `invoice_id` | Fetching invoice payments |

---

## Scope

- This document defines **data structures, relationships, and integrity rules**.
- It is the single authority on what data exists and how it relates.

## Out of Scope

- API contracts (see `09-api-design.md`)
- Page display logic (see `06-pages-spec.md`)
- Migrations framework execution details

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation for Invoice Tracker | Product Owner |
