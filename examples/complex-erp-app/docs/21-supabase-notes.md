# 21 — Supabase Notes

> How the Supabase Pack conceptually applies to the Integrated Operations ERP documentation reference.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> See the [example README](../README.md) for full context.
> Extension pack reference: [supabase-pack README](../../../extensions/supabase-pack/README.md)

---

## Pack Application Summary

The [Supabase Pack](../../../extensions/supabase-pack/README.md) provides conceptual patterns for authentication, Row Level Security (RLS), storage, and Edge Functions. This documentation reference uses Supabase as the **conceptual backend platform** — no real Supabase project is configured, no real project IDs exist, and no migrations are included.

> [!WARNING]
> This documentation reference contains **no real Supabase project IDs, no real API keys, no real anon keys, no real service role keys, no real database URLs, no migrations, and no real RLS policies**. All Supabase references are conceptual documentation only.

---

## Auth

The Auth and Session Guidelines from the Supabase Pack apply conceptually:

- Authentication is handled via Supabase Auth (conceptual).
- Users are identified by a `auth.uid()` value in the conceptual RLS model.
- On sign-in, a session JWT is issued. All API requests include this JWT in the Authorization header.
- The Supabase service role key is **never** exposed to the client — it is used only in secure server-side contexts (e.g., admin operations, seed scripts in real implementation).
- Client applications use the `anon` key with RLS-protected table access.
- User profile data (display name, roles, warehouse assignments) is stored in a separate `user_profiles` table — not in `auth.users` directly.

**Client-Side Trust Rule:** Client-side role checks are for UI convenience only. All authorization enforcement happens via RLS policies (at the database level) and server-side API checks.

---

## RLS Concept

The Row Level Security Guidelines from the Supabase Pack provide the conceptual model:

**Conceptual RLS principles for this ERP:**

1. **Operational scope enforcement via RLS:**
   - `stock_movements` table: `SELECT` policy restricts to rows where `warehouse_id IN (user's assigned warehouses)`.
   - `purchase_requests` table: `SELECT` policy restricts to rows where `department_id IN (user's assigned departments)`.
   - `invoices` table: `SELECT` policy restricts to Finance Officer and Operations Director roles only.

2. **Approval self-check:**
   - `approval_requests` table: `UPDATE` (approve/reject) policy checks that `auth.uid() != requester_user_id`.

3. **Immutable records:**
   - `stock_movements` table: no `UPDATE` or `DELETE` policy — records are insert-only.
   - `audit_events` table: no `UPDATE` or `DELETE` policy — append-only.

4. **Admin bypass:**
   - Platform Admin operations use the service role in secure server-side contexts only — never in client-facing code.

> [!WARNING]
> No actual RLS SQL policies are written in this documentation reference. The above are **conceptual descriptions** for planning purposes. Real RLS policies must be written, tested, and reviewed by a qualified database engineer before production deployment.

---

## Storage Boundaries

The Storage Guidelines from the Supabase Pack apply conceptually:

- **PDF export bucket (conceptual):** Private bucket for generated PO PDFs and Invoice PDFs. Access restricted to Finance Officer and Operations Director via signed URL generation on the server side.
- **Report export bucket (conceptual):** Private bucket for CSV exports. Access restricted to the requesting user's session for a defined time window.
- **No public buckets:** No document in this ERP is publicly accessible without authentication.
- **Bucket naming convention placeholder:** `erp-exports-{environment}` (e.g., `erp-exports-production`).

> [!WARNING]
> No real storage bucket is configured. No real bucket names, no real access policies, and no real file paths are included in this documentation reference.

---

## Edge Functions / RPC Caution

The Edge Functions Guidelines from the Supabase Pack apply:

- If approval workflow logic, stock movement creation, or report generation is implemented as Supabase Edge Functions or RPC functions:
  - Each function must verify the user's JWT before performing any action.
  - Function must not trust client-supplied role claims — roles must be read from the database.
  - Sensitive operations (stock adjustments, approval decisions) must be wrapped in database transactions.
  - Edge Functions must not log PII, session tokens, or financial amounts in plain text.
- No Edge Functions are implemented in this documentation reference.

---

## Environment Variable Caution

> [!CAUTION]
> In any real Supabase implementation derived from this reference:
> - `SUPABASE_SERVICE_ROLE_KEY` must never appear in client-side code, environment files committed to version control, or public repositories.
> - `SUPABASE_ANON_KEY` is safe for client-side use only when RLS is properly configured.
> - All secret keys must be stored in a secure secrets manager (e.g., environment variables in a secure CI/CD system).
> - No keys, project IDs, or database URLs are included in this documentation reference.

---

## No Real Supabase Project IDs

This documentation reference contains no:
- Supabase Project URL (e.g., `https://[project-id].supabase.co`)
- Supabase Project ID
- Supabase anon key
- Supabase service role key
- Supabase database connection string
- Real migration files

---

## Related Files

- [08-architecture.md](08-architecture.md) — Conceptual backend/auth architecture
- [10-security-model.md](10-security-model.md) — RLS concept in security model
- [../../../extensions/supabase-pack/README.md](../../../extensions/supabase-pack/README.md) — Source pack
