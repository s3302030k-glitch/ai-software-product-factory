# 19 — Supabase Notes: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document details the conceptual database, auth, and storage rules for the Team Subscription Manager, in alignment with the **[Supabase Pack](../../../extensions/supabase-pack/README.md)**.

> [!WARNING]
> **NO REAL DATABASE MIGRATIONS OR PROJECT KEYS**: This outlines a conceptual integration blueprint. No SQL files, live Supabase projects, project IDs, credentials, or production environments are generated.

---

## 1. Authentication Integration
- **User Identity Storage**: User profiles are linked to the system's global identity database (`auth.users`) via a shared identifier (`user_id`).
- **Token Payloads**: Authentication flows return a secure JWT containing the user's ID. When communicating with the backend, authorization layers extract this ID (`auth.uid()`) to verify memberships.

---

## 2. Row Level Security (RLS) policies
- **RLS Enforcement**: Enable RLS on all tenant tables (e.g. `workspaces`, `invoice_placeholders`, `activity_logs`).
- **Scoping Rule**: Every row read/write query must match the active user's authenticated organization ID membership:
  ```sql
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
- **Security Definer Functions**: Database functions that run with elevated privileges (`SECURITY DEFINER`) must specify search path constraints to prevent search-path injection attacks.

---

## 3. Storage boundaries
- **Invoice PDF Bucket**: Private bucket scoped to `invoices`. Storage policies permit reads only if the requester's user ID matches the tenant organization ID associated with the invoice file path.
- **Upload Policies**: Organization Owners and Billing Managers possess write access to their organization's storage subfolders. Users holding Member or Viewer roles are blocked.

---

## 4. Edge Functions and RPC Caution
- **Token Verification**: Any custom serverless functions (Deno Edge Functions) must manually validate the Bearer JWT before extracting payloads.
- **Service Role Key Warning**: The global system backend key (Service Role Key) bypasses RLS rules. This key must never be used in client applications, UI components, or general API routines. It is restricted to system background setup actions.

---

## 5. Environment Variables & Credentials
- All project keys, API connection details, and database passwords must be referenced via mock environment variables (e.g., `SUPABASE_ANON_KEY`, `DATABASE_URL`). 
- No production secrets or live project credentials may be added to documentation or templates.

---

## Related Files

- [07-data-model.md](07-data-model.md) — Database models schemas.
- [10-security-model.md](10-security-model.md) — Authentication and isolation rules.
