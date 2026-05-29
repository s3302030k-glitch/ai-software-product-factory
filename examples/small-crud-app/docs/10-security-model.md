# 10 — Security Model

> Defines authentication, authorization, data protection, audit logging recommendations, and security review requirements for the Invoice Tracker.

---

## Purpose

Establish the security framework that all development must follow. This document is the single authority on how users are authenticated, how access is controlled, how data is protected, and how security compliance is verified.

## Status

`Approved`

---

## Authentication

### Authentication Method

| Attribute | Value |
|-----------|-------|
| **Method** | Email / password login |
| **Provider** | NextAuth.js or Iron Session |
| **Session Type** | Encrypted HTTP-only, secure, SameSite session cookies |
| **Token Expiry** | Session expires in 12 hours of inactivity |
| **MFA** | Not supported (Out of scope for MVP) |

### Authentication Rules

1. All passwords must meet minimum complexity: at least 8 characters.
2. Failed login attempts must be rate-limited at the API level (max 5 failures per email address per 15 minutes).
3. Session cookies must be flagged as `httpOnly: true`, `secure: true` (in production), and `sameSite: "lax"`.
4. Auth secrets must be configured via environment variables.

---

## Authorization

### Authorization Model

| Attribute | Value |
|-----------|-------|
| **Model** | Role-Based Access Control (RBAC) |
| **Implementation** | Server-side middleware checks + API route handler assertions |
| **Enforcement Point** | Next.js API boundary handlers |

### Authorization Rules

1. Authorization must be checked **server-side** on every request. Client-side hiding of buttons is for UX only.
2. Default policy: **deny all** unless the session is verified.
3. Operations that mutate data structure (deletes) or view sensitive panels (Settings) must explicitly verify that the session user's role is `owner`. Staff attempts must return **403 Forbidden**.

---

## Sensitive Data

Detailed handling of sensitive data elements:

| Data Type | Classification | Storage | Transmission | Access |
|-----------|---------------|---------|-------------|--------|
| Passwords | Critical | Hashed (bcrypt, 10+ salt rounds) | HTTPS only | Never exposed or readable |
| DB Connection String | Critical | System Environment Variable | HTTPS only | Backend server process only |
| Client billing details | PII / Sensitive | Plain text in DB | HTTPS only | Owner and Staff roles |
| Invoice financial amounts | Sensitive | Plain text in DB | HTTPS only | Owner and Staff roles |
| Session secret keys | Critical | System Environment Variable | HTTPS only | System process only |

---

## Audit Log Recommendations

To ensure accountability in the shared single-tenant environment, the following actions should log standard JSON entries to stdout/server logs (which can be compiled by logging services):

| Event | Logged Fields | Rationale |
|-------|---------------|-----------|
| User Login | `timestamp`, `email`, `result (success/fail)`, `ip` | Track unauthorized login attempts |
| Settings Change | `timestamp`, `userId`, `changedFields` | Tracking profile modifications |
| Client Deleted | `timestamp`, `userId`, `clientId`, `clientName` | Trace accidental or malicious data loss |
| Invoice Deleted | `timestamp`, `userId`, `invoiceId`, `invoiceNumber` | Trace invoice removal |
| Permission Denied | `timestamp`, `userId`, `route`, `attemptedAction` | Flag potential privilege escalation |

---

## Security Review Checklist

### Authentication
- [ ] NextAuth/session configs have `httpOnly` and `secure` cookie flags enabled.
- [ ] Passwords hashed using bcrypt before inserting into database.
- [ ] Auth endpoints rate-limited.
- [ ] Session invalidates correctly on logout request.

### Authorization
- [ ] All routes matching `/api/*` except `/api/auth/login` require session validation.
- [ ] Delete endpoint handlers for clients and invoices assert `session.user.role === 'owner'`.
- [ ] Settings patch requests assert `session.user.role === 'owner'`.

### Data Protection
- [ ] No raw password text or sensitive API secrets printed in application logs.
- [ ] All database queries utilize Prisma/ORM query builders (built-in SQL injection defense).
- [ ] Inputs are sanitized and HTML-escaped by React during client-side rendering (XSS mitigation).
- [ ] HTTPS redirect active on target cloud host.

---

## Scope

- This document defines **security requirements and constraints**.
- All code must comply with these rules.

## Out of Scope

- Multi-tenant Row-Level Security (RLS) policies.
- Third-party SSO/OAuth integrations.
- Database cell-level column encryption.

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial version for Invoice Tracker | Product Owner |
