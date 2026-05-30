# 09 — API Design: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document specifies the conceptual REST API design for the Team Subscription Manager.

> [!IMPORTANT]
> **NO API IMPLEMENTATION OR BILLING GATEWAY**: This represents a conceptual API design spec. No runnable endpoints, server route mappings, or live third-party integrations (e.g. Stripe checkout) exist.

---

## Global API Rules

- **Authentication**: All endpoints (except public authentication routes) require a bearer JWT in the `Authorization` header.
- **Tenant Header**: All scoped requests must send the active organization ID in the `X-Organization-ID` header.
- **Idempotency**: All write requests that modify billing states or memberships must include an `Idempotency-Key` header to prevent duplicate creations.

---

## Conceptual API Groups

### 1. Auth & Session API
- **Purpose**: Authenticate users and retrieve active session contexts.
- **Example Endpoint Names**:
  - `POST /api/auth/login` (Public)
  - `GET /api/auth/session` (Authenticated)
- **Request / Response Concept**:
  - Request: `{email, password}`
  - Response: `{token, user: {user_id, email, full_name}}`
- **Auth Requirements**: Public (Login), Bearer Token (Session).
- **Tenant Scoping**: Global context.
- **Error Cases**: `401 Unauthorized` for bad credentials.

### 2. Organization API
- **Purpose**: Manage organization profiles and workspaces.
- **Example Endpoint Names**:
  - `POST /api/organizations`
  - `GET /api/organizations/:id`
- **Request / Response Concept**:
  - Request: `{name, default_currency}`
  - Response: `{organization_id, name, created_at}`
- **Auth Requirements**: Authenticated user.
- **Tenant Scoping**: Isolated to active organization memberships.
- **Error Cases**: `403 Forbidden` if user is not associated with organization `id`.

### 3. Membership & Invitations API
- **Purpose**: Manage active members and pending invitations.
- **Example Endpoint Names**:
  - `GET /api/members`
  - `POST /api/invitations`
  - `POST /api/invitations/:token/accept` (Public)
- **Request / Response Concept**:
  - Invite Request: `{email, role_code}`
  - Invite Response: `{invitation_id, email, token, expires_at}`
- **Auth Requirements**: Owner or Workspace Admin permissions. Accept invitation is public but requires a valid token payload.
- **Tenant Scoping**: Scoped via `X-Organization-ID`.
- **Error Cases**: `422 Unprocessable Entity` if invitation email is already a member, or if seat limit has been reached.
- **Idempotency**: Required for `/api/invitations` to prevent duplicate emails.

### 4. Roles & Permissions API
- **Purpose**: Read static role definitions and permissions matrix.
- **Example Endpoint Names**:
  - `GET /api/roles`
  - `GET /api/permissions`
- **Request / Response Concept**:
  - Response: `[{role_id, code, name, permissions: [...]}]`
- **Auth Requirements**: Logged in user.
- **Tenant Scoping**: Global lookup.
- **Error Cases**: None.

### 5. Plans & Subscriptions API
- **Purpose**: View pricing tiers and request mock plan updates.
- **Example Endpoint Names**:
  - `GET /api/plans`
  - `POST /api/subscription/change-plan`
- **Request / Response Concept**:
  - Request: `{plan_code}`
  - Response: `{subscription_id, plan_code, status, seat_limit}`
- **Auth Requirements**: Owner role only.
- **Tenant Scoping**: Scoped via `X-Organization-ID`.
- **Error Cases**: `409 Conflict` if attempting to downgrade to a plan whose seat limit is less than current active members.

### 6. Invoice Placeholders API
- **Purpose**: Access historical mock billing records.
- **Example Endpoint Names**:
  - `GET /api/invoices`
  - `GET /api/invoices/:id`
- **Request / Response Concept**:
  - Response: `[{invoice_id, invoice_number, amount_cents, status, due_date}]`
- **Auth Requirements**: Owner or Billing Manager roles.
- **Tenant Scoping**: Scoped via `X-Organization-ID`.
- **Error Cases**: `403 Forbidden` if accessed by non-billing roles.

### 7. Reports API
- **Purpose**: Retrieve historical seat snapshots and metrics.
- **Example Endpoint Names**:
  - `GET /api/reports/seat-usage`
- **Request / Response Concept**:
  - Query Params: `?start_date=YYYY-MM-DD&end_date=YYYY-MM-DD`
  - Response: `[{date, active_seats, pending_invites, limit}]`
- **Auth Requirements**: Owner, Workspace Admin, Billing Manager.
- **Tenant Scoping**: Scoped via `X-Organization-ID`.
- **Error Cases**: `400 Bad Request` if date range exceeds 12 months.

### 8. Activity & Audit Log API
- **Purpose**: Query tenant security history.
- **Example Endpoint Names**:
  - `GET /api/audit-log`
- **Request / Response Concept**:
  - Response: `[{log_id, timestamp, actor: {...}, action, description}]`
- **Auth Requirements**: Owner, Workspace Admin, Billing Manager.
- **Tenant Scoping**: Scoped via `X-Organization-ID`.
- **Error Cases**: None.

### 9. Notification Preferences API
- **Purpose**: Manage personal alert triggers.
- **Example Endpoint Names**:
  - `GET /api/users/preferences`
  - `PUT /api/users/preferences`
- **Request / Response Concept**:
  - Request: `{scopes: {invites: true, billing: false}}`
  - Response: `{preference_id, scopes: {...}}`
- **Auth Requirements**: Authenticated user.
- **Tenant Scoping**: Scoped via User JWT and active `organization_id`.
- **Error Cases**: None.

---

## Related Files

- [05-user-flows.md](05-user-flows.md) — User flows executing these requests.
- [07-data-model.md](07-data-model.md) — Maps backend storage representation.
- [10-security-model.md](10-security-model.md) — Details authentication boundaries.
