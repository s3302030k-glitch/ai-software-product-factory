# 10B — Auth and Session Guidelines

> Standardizes authentication strategies, session token management, user identity structures, and authorization controls.

---

## Purpose

This document defines the conventions and policies for user authentication and authorization when utilizing Supabase Auth (GoTrue). It supplements the core security model in [10-security-model.md](../../../core/docs/10-security-model.md).

## Status

`Active` — Must be implemented during the authentication and session integration phase.

---

## Authentication vs. Authorization

> [!IMPORTANT]
> **Authentication (AuthN)** and **Authorization (AuthZ)** are fundamentally different.
> - **Authentication** verifies *who* the user is (handled by Supabase Auth, yielding a session JWT).
> - **Authorization** verifies *what* the user is permitted to do.
>
> A logged-in user is **not** automatically authorized to read or modify data. Permissions must be server-enforced via RLS policies or backend API checks.

---

## Auth Model Options

The project may implement one or more of the following standard models:
1. **Email and Password**: Standard login using verified email addresses.
2. **OAuth Providers**: Third-party logins (e.g., Google, GitHub).
3. **Magic Links**: Passwordless email verification tokens.

---

## User Identity Assumptions

- Every authenticated user maps to a unique record in the Postgres system table `auth.users`, identified by a UUID.
- The `auth.uid()` helper in Postgres retrieves the calling user's UUID from the request's JWT.
- User metadata (like names, avatars, or external accounts) stored directly in `auth.users.raw_user_meta_data` should not be used as the definitive source for application-level business permissions.

---

## Session Handling

- The client application stores the Supabase session token (JWT) and refresh token in local storage or cookies (for Server-Side Rendering).
- The client-side Supabase client automatically refreshes expiring tokens in the background.
- For server-side rendering (SSR), session tokens must be forwarded securely via HTTP-only cookies.

---

## Role Claims / Profile Table Pattern

To manage application roles (e.g., User, Manager, Admin, Owner):
1. **Public Profiles Table**: Create a `public.profiles` table linked via foreign key to `auth.users.id`.
2. **Sync Trigger**: Create a Postgres database function and trigger that automatically inserts a row into `public.profiles` whenever a new user is inserted into `auth.users`.
3. **Roles column**: Add a `role` enum or text column to `public.profiles` to specify authorization levels.

```sql
-- Sync profile trigger example:
create function public.handle_new_user()
returns trigger as $$
begin
  insert into public.profiles (id, full_name, role)
  values (new.id, new.raw_user_meta_data->>'full_name', 'member');
  return new;
end;
$$ language plpgsql security definer set search_path = public;

create trigger on_auth_user_created
  after insert on auth.users
  for each row execute procedure public.handle_new_user();
```

---

## Permission Source of Truth

- **Database level**: The `public.profiles` table or custom JWT claims are the primary source of truth for authorization checks.
- **Client level**: The UI must display elements conditionally based on the user's role, but this is only for UX optimization and **never** represents a security check.

---

## Password and Session Security Notes

- Password complexity rules (length, character sets) are configured within the Supabase Auth project dashboard.
- Sessions should have reasonable lifetimes (default JWT expiry is typically 1 hour).
- Always enforce email verification before allowing users to log in or interact with protected tables.

---

## Admin User Handling

- Administrative roles must not be self-assignable.
- Trigger logic must default new profiles to the lowest privilege role.
- Changes to admin permissions must be updated directly by database admins or via an approved admin panel that checks for `admin` or `owner` scopes.

---

## Invite and Onboarding Notes

- User invitations can be sent via Supabase's invite API (`inviteUserByEmail`).
- Onboarding states (e.g., team creation, profile completion) should be tracked on the user's profile table row.

---

## Out of Scope
- Custom password hashing algorithms.
- Custom session storage engine integrations (Supabase GoTrue handles token management).
- Single Sign-On (SSO) SAML configuration details.

---

## Guardrails

- [ ] **NO PUBLIC ROLE WRITE**: The `role` column in the `profiles` table must not be updateable by the profile owner themselves via RLS policies.
- [ ] **SERVER-ENFORCED PERMISSIONS**: Every API route, server action, or Postgres RPC must validate user permission tokens before returning data.
- [ ] **TRIGGER SAFETY**: Auth profile sync triggers must run under `security definer` with an explicit `search_path` set to prevent search path pollution.

---

## QA Checklist

- [ ] Verify that registering a new user successfully triggers creation of a corresponding public profile row.
- [ ] Verify that standard users cannot elevate their role to `admin` via API payloads.
- [ ] Verify that logging out clears session cookies and tokens, rejecting subsequent authenticated queries.
- [ ] Verify that accessing protected API endpoints with an expired JWT results in an HTTP 401 Unauthorized status.

---

## Related Files

- [10-security-model.md](../../../core/docs/10-security-model.md) — Core security model document.
- [04-user-roles.md](../../../core/docs/04-user-roles.md) — Core user roles and permissions mapping.
- [rls-policy-guidelines.md](rls-policy-guidelines.md) — Row Level Security details.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created auth and session guidelines | Antigravity |
