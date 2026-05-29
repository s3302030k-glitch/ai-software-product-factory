# 08A — Supabase Architecture

> Defines the boundaries, integration model, client/server access patterns, environment variables, and guardrails when using Supabase in a product.

---

## Purpose

This document defines the tech stack architectural patterns and boundaries when integrating Supabase as the backend-as-a-service (BaaS) platform. It supplements the core architecture template in [08-architecture.md](../../../core/docs/08-architecture.md).

## Status

`Active` — Must be referenced during the design and execution phases for products using Supabase.

---

## When to Use

Use this architecture definition when the product incorporates Supabase for any of the following services:
- Postgres Database & Realtime
- GoTrue Auth (Supabase Auth)
- Row Level Security (RLS)
- Supabase Storage
- Supabase Edge Functions (Deno serverless runtime)

---

## Supabase Role in Architecture

Supabase acts as the primary data persistence, authentication, and serverless logic layer. 

```
┌─────────────────────────────────────────────────────────────┐
│                    Client / Frontend                        │
│          (Web App, Mobile App, etc. — UNTRUSTED)            │
└──────────────┬───────────────────┬───────────────────┬──────┘
               │ Auth Client       │ REST / Realtime   │ Upload / Download
               ▼                   ▼                   ▼
┌─────────────────────────────────────────────────────────────┐
│                       SUPABASE PLATFORM                     │
│  ┌───────────────┐ ┌───────────────────┐ ┌───────────────┐  │
│  │ Supabase Auth │ │ Postgres Database │ │ Storage       │  │
│  │ (GoTrue)      │ │ & RLS Policies    │ │ Buckets       │  │
│  └──────┬────────┘ └─────────▲─────────┘ └───────▲───────┘  │
│         │                    │                   │          │
│         │ JWT Claims         │ Direct Access     │          │
│         └────────────────────┼───────────────────┼──────────┘
│                              │                              │
│                    Invokes Edge Functions                   │
│                              │                              │
│                    ┌─────────▼─────────┐                    │
│                    │  Edge Functions   │                    │
│                    └───────────────────┘                    │
└─────────────────────────────────────────────────────────────┘
```

---

## Boundaries

### Database Boundary
- Postgres is the single source of truth for structured application state.
- Client applications connect to the database via PostgREST (auto-generated REST API) or Realtime subscriptions.
- All client-facing tables **must** have Row Level Security (RLS) enabled.
- Direct raw client-side write access to postgres system schemas (e.g., `auth`, `storage`) is blocked.

### Auth Boundary
- Supabase Auth manages user registration, email verification, passwords, login sessions, and JWT token issuance.
- The issued JWT contains user claims (like user ID and email) and is automatically attached to client requests to Postgres or Storage.
- Custom user profiles and application-specific permissions must be stored in custom public schema tables (e.g., a `profiles` or `users` table linked to `auth.users.id` via foreign key).

### Storage Boundary
- Supabase Storage holds files, images, videos, and large binaries.
- Client uploads and downloads are authorized through storage-specific RLS policies operating on the `storage.buckets` and `storage.objects` tables.
- Media assets must be segmented into distinct logical buckets depending on security needs.

### Edge Functions Boundary
- Supabase Edge Functions are serverless, typescript-based Deno functions.
- Used for third-party integrations, webhook handling, and operations that cannot be safely executed client-side due to secret leakage risks or complexity.
- Edge functions run isolated from direct database RLS unless client authentication headers are forwarded.

---

## Client/Server Access Model

1. **Untrusted Client-Side Requests**: The frontend interacts directly with Postgres and Storage using the Supabase client initialized with the **anon key**. This client is restricted entirely by the Postgres and Storage RLS policies.
2. **Secure Server-Side Requests**: Server environments (e.g., Next.js server actions, Edge Functions, background job servers) may interact with Supabase using the **service role key** to bypass RLS. This key should be used selectively for administrative tasks.

---

## Service Role Key Rules

> [!CAUTION]
> The Supabase **service_role key** bypasses all RLS policies. Misuse can lead to total data exposure.
>
> 1. **NEVER** expose the `service_role` key to frontend, mobile client, or browser code.
> 2. **NEVER** write the `service_role` key to client-accessible environment variables.
> 3. Limit server-side use to administrative tasks, sync jobs, or backend validation logic.
> 4. Ensure all code using `service_role` manually validates caller authorization before executing data actions.

---

## Environment Variable Rules

The following variables must be configured in environment files:

| Variable Name | Environment | Target | Access Level | Description |
|---|---|---|---|---|
| `NEXT_PUBLIC_SUPABASE_URL` | Local / Prod | Frontend & Backend | Public | The Supabase project API endpoint URL. |
| `NEXT_PUBLIC_SUPABASE_ANON_KEY` | Local / Prod | Frontend & Backend | Public | Safe public key for client-side operations (RLS enforced). |
| `SUPABASE_SERVICE_ROLE_KEY` | Local / Prod | Backend Only | Secret | Administrative key (bypasses RLS). Keep secure! |
| `SUPABASE_JWT_SECRET` | Local / Prod | Backend Only | Secret | Used to sign and verify custom JWTs. |

---

## Local / Dev / Staging / Prod Notes

- **Local Development**: The project team must use the Supabase CLI to run local emulators (Docker-based) for Postgres, Auth, and Storage. Local database schemas should be updated using migrations.
- **Staging / Production**: Changes must be deployed via automated CI/CD pipelines running Supabase CLI migration commands.
- No direct schema editing in the online Supabase dashboard is allowed for production environments.

---

## Out of Scope
- Custom OAuth provider setup guides (depends on third-party consoles).
- Direct Postgres database replication configs.
- Setup instructions for custom Postgres extensions beyond standard Supabase extensions.

---

## Guardrails

- [ ] **NO CLIENT-SIDE TRUST**: Client-side code must not be trusted for authorization. All permissions must be enforced at the database (RLS) or server level.
- [ ] **RLS ENABLED BY DEFAULT**: RLS must be enabled for all user-facing tables unless explicitly justified in a design document approved by the human owner.
- [ ] **SERVICE ROLE ISOLATION**: The `service_role` key must never be bundled into client builds.
- [ ] **OWNER APPROVAL**: Any design modification that bypasses RLS or changes authentication configuration requires owner approval.

---

## Related Core Files

- [08-architecture.md](../../../core/docs/08-architecture.md) — Core technology architecture definitions.
- [10-security-model.md](../../../core/docs/10-security-model.md) — Core security model definitions.
- [07-data-model.md](../../../core/docs/07-data-model.md) — Core data model definition.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial architecture documentation for Supabase Pack | Antigravity |
