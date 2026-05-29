# 10 — Security Model

> Defines authentication, authorization, data protection, audit logging, and security review requirements.

---

## Purpose

Establish the security framework that all development must follow. This document is the single authority on how users are authenticated, how access is controlled, how data is protected, and how security compliance is verified.

## Status

`Draft` | `In Review` | `Approved` | `Locked`

---

## Authentication

### Authentication Method

| Attribute | Value |
|-----------|-------|
| **Method** | _e.g., Email/password, OAuth, Magic link, SSO_ |
| **Provider** | _e.g., Supabase Auth, NextAuth, Clerk, Custom_ |
| **Session Type** | _e.g., JWT, HTTP-only cookie, Session token_ |
| **Token Expiry** | _e.g., Access: 15 min, Refresh: 7 days_ |
| **MFA** | _Required / Optional / Not supported_ |

### Authentication Rules

1. All passwords must meet minimum complexity: _e.g., 8+ chars, 1 uppercase, 1 number_
2. Failed login attempts must be rate-limited: _e.g., 5 attempts per 15 minutes_
3. Password reset tokens must expire: _e.g., 1 hour_
4. Sessions must be invalidated on password change
5. Auth tokens must not be stored in localStorage (use httpOnly cookies or secure alternatives)

### Authentication Flows

| Flow | Method | Notes |
|------|--------|-------|
| Login | _Email + password_ | _Rate limited_ |
| Registration | _Email + password + verification_ | _Email confirmation required_ |
| Password Reset | _Email link_ | _Token expires in 1 hour_ |
| Logout | _Session invalidation_ | _Clear all tokens_ |
| Token Refresh | _Refresh token rotation_ | _Old refresh tokens invalidated_ |

---

## Authorization

### Authorization Model

| Attribute | Value |
|-----------|-------|
| **Model** | _e.g., RBAC (Role-Based Access Control)_ |
| **Implementation** | _e.g., Middleware, RLS (Row Level Security), Policy-based_ |
| **Enforcement Point** | _Server-side (API layer and/or database)_ |

### Authorization Rules

1. Authorization must be enforced **server-side** — never rely on client-side checks alone
2. Every API endpoint must check both authentication and authorization
3. Default policy: **deny all** unless explicitly permitted
4. Role checks must happen before any data access or mutation
5. Authorization failures must be logged

---

## Roles

_Reference `04-user-roles.md` for complete role definitions. Summary here for security context._

| Role | Auth Level | Notes |
|------|-----------|-------|
| _Admin_ | Full access | _Can manage all data and users_ |
| _Manager_ | Scoped access | _Can manage team/department data_ |
| _User_ | Own data | _Can only access own records_ |
| _Guest_ | Public data | _Read-only access to public content_ |

---

## Data Scoping

_How is data isolated between users and organizations?_

| Scope Level | Implementation | Applies To |
|------------|----------------|------------|
| _User-level_ | _Filter by `user_id`_ | _Personal records_ |
| _Team-level_ | _Filter by `team_id`_ | _Shared team resources_ |
| _Tenant-level_ | _Filter by `tenant_id` (if multi-tenant)_ | _All tenant data_ |
| _Global_ | _No filter (admin only)_ | _System configuration_ |

### Data Scoping Rules

1. Scoping must be applied at the **query level** (not filtered after retrieval)
2. Direct URL access must not bypass data scoping
3. API responses must never include data outside the user's scope
4. Scoping must be verified in QA for every feature

---

## Sensitive Data

_How is sensitive data handled?_

| Data Type | Classification | Storage | Transmission | Access |
|-----------|---------------|---------|-------------|--------|
| Passwords | Critical | Hashed (bcrypt/argon2) | HTTPS only | Never exposed |
| API Keys | Critical | Encrypted at rest | HTTPS only | Admin only |
| Email addresses | PII | Plain text | HTTPS only | Role-based |
| Financial data | Sensitive | Encrypted at rest | HTTPS only | Authorized roles |
| Session tokens | Critical | HTTP-only cookies | HTTPS only | System only |

### Sensitive Data Rules

1. **Never log sensitive data** — No passwords, tokens, or PII in logs
2. **Never expose in API responses** — Strip sensitive fields from all responses
3. **Encrypt at rest** — Sensitive data must be encrypted in the database
4. **Encrypt in transit** — HTTPS required for all communication
5. **Minimize collection** — Only collect data that is necessary
6. **Retention policy** — Define how long data is kept and when it is purged

---

## Audit Logs

_What actions should be tracked for security and compliance?_

| Event | Logged Data | Retention |
|-------|-----------|-----------|
| Login (success/fail) | User ID, IP, timestamp, result | _e.g., 90 days_ |
| Password change | User ID, timestamp | _e.g., 1 year_ |
| Role change | User ID, old role, new role, changed by | _e.g., 1 year_ |
| Data deletion | User ID, entity type, entity ID | _e.g., 1 year_ |
| Permission denied | User ID, resource, action attempted | _e.g., 90 days_ |
| Admin actions | Admin ID, action, target, timestamp | _e.g., 1 year_ |

### Audit Log Rules

1. Audit logs must be **append-only** — no editing or deletion
2. Audit logs must include the actor (who), action (what), target (on what), and timestamp (when)
3. Audit logs must be accessible to admin users (read-only)
4. Audit logs must not contain sensitive data values (log the event, not the data)

---

## Security Review Checklist

_Run this checklist before every release and after every security-related change._

### Authentication

- [ ] All auth endpoints are rate-limited
- [ ] Password hashing uses bcrypt or argon2 with appropriate cost factor
- [ ] Session tokens are stored securely (httpOnly cookies)
- [ ] Token expiry is configured and enforced
- [ ] Password reset flow is secure (expiring tokens, email verification)

### Authorization

- [ ] Every API endpoint checks authentication
- [ ] Every API endpoint checks authorization (role/scope)
- [ ] Default policy is deny-all
- [ ] Data scoping is enforced at query level
- [ ] Direct URL manipulation cannot bypass authorization

### Data Protection

- [ ] No sensitive data in logs
- [ ] No sensitive data in API responses
- [ ] HTTPS enforced on all endpoints
- [ ] File uploads are validated (type, size, content)
- [ ] SQL injection prevention (parameterized queries)
- [ ] XSS prevention (output encoding, CSP headers)
- [ ] CSRF protection enabled

### Infrastructure

- [ ] Environment variables used for secrets (not in code)
- [ ] Dependency vulnerabilities checked (npm audit / similar)
- [ ] Error messages do not expose internal details
- [ ] CORS configured to allow only expected origins

---

## Scope

- This document defines **security requirements and constraints**.
- All code must comply with these rules.

## Out of Scope

- Detailed implementation of auth provider (handled in code)
- Infrastructure hardening (server config, firewalls)
- Compliance framework details (GDPR, SOC2 — document in extension packs)

## Guardrails

- [ ] No AI agent may modify authentication or authorization without explicit approval
- [ ] Security checklist must pass before any release
- [ ] Audit logging must be implemented for all specified events
- [ ] Data scoping violations are treated as critical bugs

## Related Files

- `04-user-roles.md` — Role definitions used in authorization
- `07-data-model.md` — Data that security rules protect
- `08-architecture.md` — Architecture that implements security
- `09-api-design.md` — API endpoints where security is enforced
- `13-release-checklist.md` — Release process includes security sign-off

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
