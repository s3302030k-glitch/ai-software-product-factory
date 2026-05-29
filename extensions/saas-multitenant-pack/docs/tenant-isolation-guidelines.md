# Tenant Isolation Guidelines

> Defines standards, patterns, and guardrails for ensuring absolute data isolation between different tenants at the database, API, and UI boundaries.

---

## Purpose

Prevent data leaks between tenants (cross-tenant contamination) by establishing strict engineering requirements for querying, caching, updating, and exporting tenant-scoped resources.

## Status

`Active` — Mandatory for all backend developers, database engineers, and API designers. Compliance must be verified during review and QA gates.

---

## Tenant Isolation Principles

1. **Defense in Depth**: Isolation must be enforced at multiple layers: database-level Row Level Security (RLS), backend query filters, and API authorization checks.
2. **Server-Side Enforcement**: UI hiding is NOT security. Tenant isolation must be enforced server-side or database-side, never only in the UI.
3. **Explicit Query Context**: Every tenant-scoped database query must have explicit tenant context. Do not rely on implicit default filters unless combined with compiler/linter checks.
4. **Zero-Trust Cache**: Cached records must be partitioned by tenant. A cache key must always incorporate the tenant prefix.

---

## Tenant Key Strategy

- **Immutable ID**: Use an immutable globally unique identifier (`UUIDv4`) as the organization/tenant key (`organization_id` or `tenant_id`). Avoid serial integers (`1`, `2`, `3`) or modifiable slugs as primary database scoping keys, as integers are guessable and easily targeted by ID-enumeration attacks.
- **Tenant Verification**: The tenant ID must be derived from the user's active session (e.g., verified JWT claims or session storage on the server), never trusted directly from client-side URL parameters or request bodies for write/read actions.

---

## Database Scoping Patterns

Three main strategies exist for multi-tenant databases. For most product factory builds, **Logical Isolation** is the default:

1. **Logical Isolation (Shared Database, Shared Schema)**:
   - All tenants share the same tables.
   - Every tenant-scoped table *must* have an `organization_id` column.
   - Databases supporting Row Level Security (e.g., PostgreSQL RLS) should enable RLS policies on all tenant-scoped tables to enforce filters at the engine level. See [Supabase Pack](../../supabase-pack/README.md) for database-level RLS policy details.
2. **Logical Isolation (Shared Database, Separate Schemas)**:
   - Each tenant has an independent database schema.
   - Connections must set the search path dynamically to the tenant's schema based on session initialization.
3. **Physical Isolation (Separate Databases)**:
   - Each tenant has an independent database server or cluster.
   - Requires a router/middleware to dynamically resolve connection strings.

---

## API Scoping Patterns

- **Context Middleware**: Backend routes must pass through a scoping middleware that extracts the user's active session, validates that the user is a member of the requested organization, and attaches the validated `organization_id` to the request context.
- **Scoping Injection**: Handlers and controllers must query the database using the injected `organization_id` from the context:
  ```typescript
  // Bad: relying on client-supplied tenant ID
  const projects = await db.projects.find({ id: req.params.id, org_id: req.body.org_id });

  // Good: query strictly scoped using context extracted from session
  const projects = await db.projects.find({ id: req.params.id, org_id: req.context.validatedOrgId });
  ```

---

## UI Scoping Patterns

- **UI Isolation**: The UI must display data fetched strictly from scoped API endpoints.
- **Safe Rendering**: If a resource is not found or is forbidden due to tenant mismatch, the UI must render a standard 404 "Resource Not Found" or 403 "Access Denied" state, rather than leaking metadata of that resource.

---

## Reporting/Export Scoping

> [!WARNING]
> **Exports and reports must never leak another tenant’s data.**
> Reports, CSV exports, PDF files, and Excel downloads involve bulk-querying. These functions must strictly apply the active `organization_id` constraint to all subqueries, joins, and temporary views.

- **Isolation during Bulk Jobs**: Bulk processing must run inside isolated sandbox functions that pass the tenant ID explicitly down to the reporting engine.
- **Private Storage**: Generated export files must be saved in storage paths partitioned by the tenant ID (e.g., `buckets/exports/{organization_id}/{export_file_uuid}.csv`) with access policies configured to require membership verification.

---

## Tenant Switching Rules

Users who belong to multiple organizations must be able to switch contexts securely:
- **Cache Invalidation**: Tenant switching must completely reset scoped caches (e.g., browser localStorage/sessionState, React/Vue global state, Apollo/Query client cache).
- **Session Swapping**: The switching endpoint must issue a new session token (JWT or cookie) that reflects the new active `organization_id` and has passed organization membership checks.
- **Redirect Isolation**: After a tenant switch, redirect the user to the dashboard homepage to ensure all components remount and fetch fresh, isolated data.

---

## Cross-Tenant Admin Access Rules

> [!IMPORTANT]
> **Cross-tenant admin access must be explicit, audited, and owner-approved.**
> "Backdoor" access for support or super-admin personnel must never be built implicitly.

- **Impersonation Logs**: If support staff need to access a tenant dashboard, it must be performed through an official Impersonation Mode that creates an immutable, timestamped audit log detailing:
  - Who requested access.
  - Which tenant was accessed.
  - The explicit reason/ticket number.
  - Exactly what changes were made.

---

## Background Jobs and Tenant Context

- **Context Serialization**: Background workers (e.g., BullMQ, Celery, Sidekiq) must serialize the active `organization_id` inside the job payload.
- **Worker Execution Scoping**: When executing the job, the background worker must initialize its database connections or scoping context using the payload's `organization_id`. It must not run under global un-scoped permissions unless performing aggregate system-level maintenance.

---

## Webhooks and Tenant Context

- **Webhook Validation**: Webhook receivers (e.g., receiving payment notifications from Stripe) must dynamically look up the destination tenant using the external reference IDs (e.g., matching the metadata field `stripe_customer_id` or a custom reference object mapped in the DB).
- **Safe Context Extraction**: Do not trigger webhook operations without first verifying that the derived tenant exists and is active.

---

## RLS/Security Policy Notes

For systems built on database engines supporting Row Level Security:
- Enable RLS for all tenant-scoped tables.
- Define a tenant enforcement policy:
  ```sql
  ALTER TABLE projects ENABLE ROW LEVEL SECURITY;
  CREATE POLICY tenant_isolation_policy ON projects
    USING (organization_id = auth.jwt() ->> 'org_id');
  ```
- Ensure all database queries set the session parameter appropriately. Refer to [Supabase Pack](../../supabase-pack/README.md) for full authorization mapping details.

---

## Out of Scope

- Specific configuration scripts for load balancers (Nginx, AWS ALB) or reverse proxies.
- Physical server orchestration or Docker networking rules.

---

## Guardrails

- [ ] **NO CLIENT-SIDE TRUTH**: Never trust the tenant ID supplied in request payloads or URL parameters without server-side validation against session records.
- [ ] **EXPLICIT CACHE SCOPING**: Cache keys must always be prefixed with `{tenant_id}:`.
- [ ] **SECURE EXPORTS**: Export tasks must be strictly scoped to the active tenant's context.

---

## QA Checklist

- [ ] Verify that query filters in all API controllers include an explicit `organization_id` check.
- [ ] Attempt to query an item belonging to Tenant B while authenticated as Tenant A; verify the system returns a 404 or 403.
- [ ] Test tenant switching and confirm browser storage, state management, and localized caches are fully cleared.
- [ ] Run a test bulk export and verify no data from other organizations appears in the CSV/PDF.
- [ ] Verify support impersonation logs are created successfully when access is simulated.

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Core database schemas.
- [10-security-model.md](../../../core/docs/10-security-model.md) — Security boundaries and policies.
- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — QA verification procedures.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation of Tenant Isolation Guidelines | Antigravity |
