# 09 — API Design

> Defines all API endpoints, request/response contracts, validation, error handling, and authorization rules for the Invoice Tracker.

---

## Purpose

Provide a complete API reference that frontend code, backend handlers, and AI agents use to build and consume the API. Every endpoint, its shape, validation, errors, and auth rules are specified here.

## Status

`Approved`

---

## API Conventions

- **Base URL:** `/api`
- **Format:** JSON payloads
- **Authentication:** Cookie-based session validation
- **Naming:** CamelCase for JSON object fields, plural nouns for paths (e.g. `/api/clients`)

### Standard Response Envelope
```json
// Success
{
  "data": { ... }
}

// Error (e.g. 400 Bad Request / 422 Unprocessable)
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Field errors detected",
    "details": [
      { "field": "amount", "message": "Amount must be greater than 0" }
    ]
  }
}
```

---

## Endpoint Index

| Method | Path | Description | Roles | Priority |
|--------|------|-------------|-------|----------|
| GET | `/api/auth/session` | Get current user session | Owner, Staff | Must Have |
| POST | `/api/auth/login` | Log in user and establish session | Guest | Must Have |
| POST | `/api/auth/logout` | Log out and destroy session | Owner, Staff | Must Have |
| GET | `/api/clients` | List clients (optional `?search=`) | Owner, Staff | Must Have |
| POST | `/api/clients` | Create a new client | Owner, Staff | Must Have |
| GET | `/api/clients/:id` | Get client details | Owner, Staff | Must Have |
| PUT | `/api/clients/:id` | Update client profile | Owner, Staff | Must Have |
| DELETE | `/api/clients/:id` | Delete a client (Restricted if has invoices) | Owner | Must Have |
| GET | `/api/invoices` | List invoices (optional `?status=&search=`) | Owner, Staff | Must Have |
| POST | `/api/invoices` | Create invoice (includes line items nested) | Owner, Staff | Must Have |
| GET | `/api/invoices/:id` | Get invoice details (items, payments nested) | Owner, Staff | Must Have |
| PUT | `/api/invoices/:id` | Update invoice (includes items nested) | Owner, Staff | Must Have |
| DELETE | `/api/invoices/:id` | Delete invoice | Owner | Must Have |
| POST | `/api/invoices/:id/payments` | Record a payment | Owner, Staff | Must Have |
| GET | `/api/dashboard/summary` | Fetch dashboard totals | Owner, Staff | Must Have |

---

## Endpoint Specifications

### 1. `POST /api/auth/login`
- **Description:** Establish session.
- **Auth Required:** No
- **Request Body:**
  ```json
  { "email": "user@example.com", "password": "password123" }
  ```
- **Response (200 OK):**
  ```json
  { "data": { "id": "uuid", "email": "user@example.com", "role": "owner" } }
  ```

---

### 2. `GET /api/clients`
- **Description:** Retrieve client list.
- **Auth Required:** Yes (Owner, Staff)
- **Response (200 OK):**
  ```json
  {
    "data": [
      {
        "id": "uuid",
        "name": "Acme Corp",
        "email": "billing@acme.com",
        "createdAt": "2026-05-29T18:30:00Z"
      }
    ]
  }
  ```

---

### 3. `POST /api/clients`
- **Description:** Create a client.
- **Auth Required:** Yes (Owner, Staff)
- **Request Body:**
  ```json
  {
    "name": "Acme Corp",
    "email": "billing@acme.com",
    "address": "123 Main St, New York, NY 10001"
  }
  ```
- **Response (201 Created):**
  ```json
  {
    "data": {
      "id": "uuid",
      "name": "Acme Corp",
      "email": "billing@acme.com",
      "address": "123 Main St"
    }
  }
  ```

---

### 4. `DELETE /api/clients/:id`
- **Description:** Delete client.
- **Auth Required:** Yes (Owner only)
- **Response (204 No Content):** Empty.
- **Conflict (409):** If client has associated invoices. Response: `{"error": {"code": "RESTRICTED_RELATION", "message": "Cannot delete client with existing invoices."}}`

---

### 5. `POST /api/invoices`
- **Description:** Create an invoice with items.
- **Auth Required:** Yes (Owner, Staff)
- **Request Body:**
  ```json
  {
    "clientId": "uuid",
    "issueDate": "2026-05-29",
    "dueDate": "2026-06-29",
    "items": [
      { "description": "Web Development", "unitRate": 150.00, "quantity": 10 }
    ]
  }
  ```
- **Response (201 Created):**
  ```json
  {
    "data": {
      "id": "invoice-uuid",
      "invoiceNumber": "INV-0001",
      "clientId": "client-uuid",
      "status": "Draft",
      "invoiceTotal": 1500.00,
      "paidAmount": 0.00,
      "balanceDue": 1500.00
    }
  }
  ```

---

### 6. `POST /api/invoices/:id/payments`
- **Description:** Log a manual payment.
- **Auth Required:** Yes (Owner, Staff)
- **Request Body:**
  ```json
  {
    "amount": 500.00,
    "paidDate": "2026-05-29",
    "paymentMethod": "bank_transfer"
  }
  ```
- **Validation:** `amount` must be > 0 and <= remaining balance on the invoice.
- **Response (201 Created):**
  ```json
  {
    "data": {
      "id": "payment-uuid",
      "invoiceId": "invoice-uuid",
      "amount": 500.00,
      "paidDate": "2026-05-29",
      "paymentMethod": "bank_transfer"
    }
  }
  ```

---

### 7. `GET /api/dashboard/summary`
- **Description:** Aggregates dashboard card figures.
- **Auth Required:** Yes (Owner, Staff)
- **Response (200 OK):**
  ```json
  {
    "data": {
      "totalBilled": 25000.00,
      "totalPaid": 18000.00,
      "totalOutstanding": 7000.00,
      "totalOverdue": 3000.00
    }
  }
  ```

---

## Authorization & Error Handling Rules
- All authenticated APIs must verify session existence. If invalid, return **401 Unauthorized**.
- Staff role trying to delete records (Clients or Invoices) or query settings must be blocked at route handler level with **403 Forbidden**.
- Bad payload structures or missing validation checks must return **400 Bad Request** or **422 Unprocessable Entity**.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation for Invoice Tracker | Product Owner |
