# 10 — Security Model: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document specifies the security controls, tenant isolation guidelines, authorization checks, and audit logging definitions for the Team Subscription Manager.

> [!WARNING]
> **NO PRODUCTION SECURITY CLAIM**: This represents a documentation reference. No production security controls, encryption configurations, or live RLS policies are deployed.

---

## Authentication vs. Authorization

- **Authentication (AuthN)**: Relies on cryptographic verification of global JWTs issued by the Identity Provider (e.g. Supabase Auth). The token encodes the user's primary identity ID (`user_id`).
- **Authorization (AuthZ)**: Performed downstream at the application and database layers. Access controls determine actions based on active membership records (`OrganizationMembership`) associated with the requested `organization_id`.

---

## Tenant Isolation & RLS

Data segregation is enforced at the database query layer. When using Supabase/PostgreSQL, all tenant-scoped tables must implement PostgreSQL **Row Level Security (RLS)**.

### Conceptual RLS Policies

```sql
-- 1. Enable RLS on Tenant-Scoped tables
ALTER TABLE workspaces ENABLE ROW LEVEL SECURITY;
ALTER TABLE invoice_placeholders ENABLE ROW LEVEL SECURITY;

-- 2. Create isolation policy verifying active memberships
CREATE POLICY tenant_isolation_policy ON workspaces
    FOR ALL
    USING (
        organization_id IN (
            SELECT organization_id 
            FROM organization_memberships 
            WHERE user_id = auth.uid()
        )
    );
```

Under these rules, client-side queries cannot bypass tenant partitioning because database-level parameters restrict operations to the user's confirmed membership boundaries.

---

## Access Scoping & Role Boundaries

### 1. Billing Section Access
- Access to the `/api/invoices`, `/api/subscription/*`, and `/billing` UI routes is strictly restricted.
- Only users holding the **Organization Owner** or **Billing Manager** roles within the active organization will pass the backend validation middleware checks.

### 2. Platform Support Admin Restrictions
- To prevent abuse of customer data by platform employees, Platform Support Admins are isolated from standard tenant UI layouts.
- They cannot view raw tenant tables or invoice lists unless they invoke the Platform Support Console with a registered, active Support Ticket ID.
- Impersonation tokens expire automatically after 2 hours and trigger immutable system-level audit records.

### 3. Invitation Security
- Invitation URLs use securely generated tokens (cryptographically random UUIDv4 or hashes).
- Tokens are one-time use only.
- The signup/invite acceptance flow must verify that the accepting user's email matches the exact email address specified in the invitation record.

---

## Audit Logging & Sensitive Data Handling

- **Immutable Audit Logging**: Actions that alter user lists, plan states, or security preferences are copied to `AuditEvent` and `ActivityLog` tables. These tables only support `INSERT` actions.
- **Passwords**: Never processed or stored in cleartext by the application. Handled entirely by the core Auth Provider.
- **PII Masking**: Platform logs and Support Admin screens must mask user emails and billing details.

---

## Related Files

- [04-user-roles.md](04-user-roles.md) — Allowed action permissions matrix.
- [07-data-model.md](07-data-model.md) — Audit event table schemas.
- [19-supabase-notes.md](19-supabase-notes.md) — Supabase RLS specifications.
