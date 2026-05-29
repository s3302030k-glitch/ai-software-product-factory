# 10A — Row Level Security (RLS) Guidelines

> Establishes the principles, design patterns, checklists, and guardrails for implementing Row Level Security in Postgres databases.

---

## Purpose

This document provides guidelines for designing, implementing, and reviewing Postgres Row Level Security (RLS) policies within Supabase projects. It supplements the core security model in [10-security-model.md](../../../core/docs/10-security-model.md).

## Status

`Active` — Mandatory reference for RLS policies design and review.

---

## RLS Principles

1. **Deny by Default**: Always enable RLS on every table. If no policy is defined, access is denied to all operations for authenticated and anonymous users.
2. **Never Trust Client Inputs for Security**: Do not rely on client-side filters (e.g., `.eq('user_id', userId)`) as a security mechanism. The database must enforce isolation.
3. **Use Helpers for Context**: Rely on built-in Supabase functions like `auth.uid()` or `auth.jwt()` to establish caller identity.
4. **Performance-Aware Policies**: RLS policies are evaluated on every row. Avoid heavy queries or nested joins directly inside policy expressions without indexes or cache functions.

---

## Tables That Usually Require RLS

All user-facing tables require RLS. Example structures:
- **User Profiles**: e.g., `profiles` containing user-specific data.
- **Tenant Objects**: e.g., `organizations`, `projects`, `teams`.
- **Core Entities**: e.g., `tasks`, `invoices`, `transactions` created by users.
- **Logs / Audit Trails**: Read-only tables populated via triggers or backend functions.

---

## Role-Based Access Patterns

### User Ownership Pattern
Restrict access to rows where a foreign key matches the authenticated user's ID.
```sql
-- Read/Write policy example:
create policy "Users can modify their own items"
on public.items
for all
to authenticated
using (auth.uid() = user_id)
with check (auth.uid() = user_id);
```

### Team / Organization / Tenant Scoping Pattern
Verify if the authenticated user is associated with the tenant owner of the row.
```sql
-- Read policy example:
create policy "Team members can view team data"
on public.team_data
for select
to authenticated
using (
  exists (
    select 1 from public.team_members
    where team_members.team_id = team_data.team_id
      and team_members.user_id = auth.uid()
  )
);
```

### Admin Access Pattern
Bypass standard checks for users marked with administrative roles in their profile or metadata.
```sql
-- Admin check policy example:
create policy "Admins have full access"
on public.items
for all
to authenticated
using (
  (auth.jwt() ->> 'role') = 'admin'
  -- Or checking a custom profiles table:
  -- exists (select 1 from public.profiles where id = auth.uid() and role = 'admin')
);
```

---

## Operations Checklist

### Read (SELECT) Policy Checklist
- [ ] Confirmed that SELECT does not expose rows belonging to other users/tenants.
- [ ] Checked for indexes on columns used in the `using` clause (e.g., `user_id`, `tenant_id`).
- [ ] Verified that public tables do not leak PII or administrative data.

### Insert (INSERT) Policy Checklist
- [ ] Verified that `with check` restricts inserts to data that the user actually owns (e.g., `auth.uid() = user_id`).
- [ ] Blocked client-side insertion of administrative flags (e.g., `is_admin`, `role`).
- [ ] Confirmed default database values (like `created_by default auth.uid()`) are utilized.

### Update (UPDATE) Policy Checklist
- [ ] Confirmed the user has the authority to edit the existing row (defined by `using`).
- [ ] Confirmed the new values conform to ownership rules (defined by `with check`).
- [ ] Blocked updates to immutable tracking columns (e.g., `created_at`, `owner_id`).

### Delete (DELETE) Policy Checklist
- [ ] Ensured that delete permissions are tightly restricted (often restricted to owners or admins).
- [ ] Confirmed that `using` clause targets the specific item owner.

---

## RPC and Security Definer Risk Notes

Database functions in Postgres run with two potential privilege contexts:
1. **SECURITY INVOKER (Default)**: The function runs with the permissions of the calling user. RLS is respected.
2. **SECURITY DEFINER**: The function runs with the permissions of the user who *created* it (usually `postgres` or `supabase_admin`). RLS is bypassed.

> [!WARNING]
> `SECURITY DEFINER` functions bypass RLS policies and can access all rows.
>
> 1. Only use `SECURITY DEFINER` when performing cross-user updates, trigger-based profiles creation, or operations that require system-level privileges.
> 2. **ALWAYS** explicitly set a secure `search_path` when creating `SECURITY DEFINER` functions to prevent search path injection attacks.
>    *Example: `alter function my_definer_func() set search_path = public;`*
> 3. Never trust arguments supplied to `SECURITY DEFINER` functions without strict validation against `auth.uid()`.

---

## Testing Checklist

Before deploying any RLS policy to staging or production:
- [ ] **Anonymous test**: Run SELECT/INSERT/UPDATE/DELETE requests without auth headers; confirm all are rejected (unless public read is explicitly approved).
- [ ] **Standard user test**: Authenticate as User A and verify that User B's private rows are inaccessible.
- [ ] **Cross-tenant test**: Verify that a user authenticated in Tenant X cannot read, modify, or insert rows into Tenant Y.
- [ ] **Admin test**: Confirm that admin-only routes/tables deny standard users but allow admin users.

---

## Common Mistakes

- **Forgetting `alter table ... enable row level security;`**: Merely defining policies has no effect if RLS is not enabled on the table.
- **Using `security definer` on custom RPCs casually**: Bypasses RLS safety, opening the database to SQL injection or logical authorization errors.
- **Recursive Policies**: Defining a policy that queries the same table it is protecting, causing an infinite loop.
- **Leaking the Service Role Key**: Bypassing client-side controls entirely.

---

## Stop Conditions

> [!CAUTION]
> Stop and report to the human owner if:
> 1. You are asked to write a policy that grants public write/delete access to any table.
> 2. You detect a table containing user data where RLS is disabled.
> 3. A `SECURITY DEFINER` function is requested without an explicitly defined schema search path.
> 4. You detect recursive triggers or policies causing query timeouts.

---

## Related Files

- [08A — Supabase Architecture](supabase-architecture.md) — Architectural system design.
- [10-security-model.md](../../../core/docs/10-security-model.md) — Core security model document.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created RLS policy guidelines for Supabase pack | Antigravity |
