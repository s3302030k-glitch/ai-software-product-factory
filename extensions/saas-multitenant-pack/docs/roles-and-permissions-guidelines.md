# Roles and Permissions Guidelines

> Defines standards for designing SaaS roles, system vs. tenant authorization, permission enforcement rules, super-admin boundaries, and role-change audit logging.

---

## Purpose

Prevent unauthorized privilege escalation, vertical and horizontal data bypasses, and accidental administrative exposures by establishing clean patterns for managing system-level and organization-level permissions.

## Status

`Active` — Mandatory for all software architects, backend developers, and security engineers. Compliance must be verified during code reviews.

---

## SaaS Permission Principles

1. **Authentication != Authorization**: Authentication verifies *who the user is*; authorization determines *what the user can do* in a specific tenant context.
2. **Server-Side Enforcement**: **UI hiding is not security.** Hiding a menu item, button, or link does not prevent a user from calling the API endpoint directly. Permissions must be explicitly evaluated server-side on every request.
3. **Least Privilege**: Users must be assigned the minimal set of capabilities required to perform their functions.
4. **Contextual Scoping**: Every permission check must be evaluated within the active `organization_id` context. Global un-scoped permissions must be blocked.

---

## Role Model Options

Applications can use one of three main authorization architectures:

- **Role-Based Access Control (RBAC)**:
  - Users are assigned predefined roles (e.g., Owner, Admin, Member, Viewer).
  - Each role is hardcoded to a static set of capabilities.
  - Recommended for simple to medium-complexity B2B SaaS.
- **Resource-Based / Fine-Grained Permissions (PBAC)**:
  - Users are assigned granular permissions (e.g., `projects:create`, `invoices:view`, `settings:update`).
  - Roles are simply collections of these permissions.
  - Recommended for complex corporate enterprise applications.
- **Attribute-Based Access Control (ABAC)**:
  - Access is evaluated at runtime based on attributes (e.g., "User is the creator of the project AND project is not closed").

---

## System Roles vs. Organization Roles

SaaS applications must distinguish between two separate permission scopes:

- **Organization Roles (Tenant-Scoped)**:
  - Assigned to a user via the `memberships` table for a specific organization.
  - Controls access strictly within that organization's boundaries.
  - Examples: `OrgOwner`, `OrgAdmin`, `OrgMember`, `OrgViewer`.
- **System Roles (Global-Scoped)**:
  - Assigned directly to the global `users` table.
  - Controls access to global administration dashboards, tenant directory management, and system configuration.
  - Examples: `SystemSuperAdmin`, `SystemSupportStaff`, `StandardUser`.
  - *Caution*: System roles must never be stored in the same field or table as Organization roles.

---

## Tenant-Scoped Permissions

Every permission evaluation inside a tenant context must check the membership record:
1. Verify the authenticated user has an `active` membership record for the requested `organization_id`.
2. Extract the user's role for that specific organization.
3. Evaluate if that role/membership permits the requested operation.

---

## Feature-Level Permissions

- **Billing-Aligned Gating**: Feature permissions can be combined with subscription plan capabilities (e.g., "Role permits creating projects AND plan has not exceeded project limit").
- **Decoupled Architecture**: Keep authorization checks (can the user perform this?) separate from plan limit checks (has the organization exceeded this limit?).

---

## Admin / Super-Admin Boundaries

- **Super-Admin Defined**: A global `SystemSuperAdmin` has write/read access to all tables across all tenants for maintenance, support, and emergency troubleshooting.
- **Auditing Constraint**: **Super-admin access must be explicit and audited.**
- **No Background Backdoors**: Support personnel must never have silent, un-logged access to tenant data. If a super-admin accesses or impersonates a tenant, an immutable audit log must be written:
  ```json
  {
    "event": "superadmin.impersonate.start",
    "actor_id": "super_admin_user_uuid",
    "target_org_id": "tenant_org_uuid",
    "timestamp": "2026-05-29T22:45:00Z",
    "reason": "Ticket #102934"
  }
  ```

---

## Permission Source of Truth

- **Database-Driven**: The database (e.g., `memberships` table roles) is the absolute source of truth for authorization.
- **Session Caching**: While roles can be cached inside JWT claims to avoid repetitive database lookups, the backend must invalidate or force-refresh tokens immediately upon role changes or membership suspensions to prevent stale privilege exposures.

---

## Server-Side Authorization Rules

- **Middleware Gatekeepers**: Implement robust, reusable route decorators or middlewares that guard endpoints:
  ```typescript
  // Example of explicit role checking in controller
  @Route('POST', '/projects')
  @RequirePermission('projects:create')
  async createProject(req: Request) {
     const orgId = req.context.validatedOrgId;
     // business logic here
  }
  ```
- **Check-Before-Write**: Before committing any write or update operation, the transaction must verify the user's current membership status is `Active`.

---

## UI Permission Display Rules

- **Decorative Hiding**: The frontend should hide or disable navigation items, buttons, and forms that the user's role cannot access to ensure a clean user experience.
- **Client-Side Limitations**: Client-side routing guards (e.g., React Router guards) are UX elements only. They prevent casual navigation but do not serve as security controls.

---

## Permission Audit Logging

> [!WARNING]
> **Role changes require an audit trail.**
> Changes to administrative or owner status represent high security risks and must be traceably recorded.

Every authorization-altering event must write to an immutable audit table containing:
- `timestamp`
- `actor_user_id` (who made the change)
- `target_user_id` (whose role was changed)
- `organization_id`
- `previous_role`
- `new_role`

---

## Out of Scope

- Concrete RBAC database seed SQL scripts.
- Third-party auth provider policy configurations (e.g., Auth0, Firebase Rules).

---

## Guardrails

- [ ] **NO SILENT PROMOTIONS**: All changes to organization roles must pass through verified, authorized admin endpoints.
- [ ] **EXPLICIT SUPER-ADMIN IMPERSONATION**: Block super-admin read/write operations on tenant data unless impersonation context is active and logged.
- [ ] **DECOUPLED LOGIC**: Ensure authentication state does not implicitly grant system-level administrative permissions.

---

## QA Checklist

- [ ] Verify that a `Viewer` cannot perform write operations (create, update, delete) even when making raw API calls.
- [ ] Verify that a role change from `Admin` to `Member` immediately revokes administrative privileges in the active session.
- [ ] Audit the super-admin access logs and confirm that any cross-tenant data query generates a traceable log entry.
- [ ] Test form submission on an endpoint while authenticated under a suspended membership; verify it is blocked server-side.

---

## Related Core Files

- [04-user-roles.md](../../../core/docs/04-user-roles.md) — Standard user roles and scoping definitions.
- [10-security-model.md](../../../core/docs/10-security-model.md) — Security boundaries and data encryption.
- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — Testing models and verification procedures.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation of Roles and Permissions Guidelines | Antigravity |
