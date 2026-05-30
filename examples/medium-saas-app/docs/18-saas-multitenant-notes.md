# 18 — SaaS Multi-Tenant Notes: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document details the multi-tenant architectural rules, membership controls, and tenant isolation policies applied to the Team Subscription Manager, in alignment with the **[SaaS Multi-Tenant Pack](../../../extensions/saas-multitenant-pack/README.md)**.

---

## 1. Organization vs. User Identity
- **User**: Represents a global entity in the authentication database (e.g. `auth.users`). Possesses a singular profile.
- **Organization**: Represents the tenant entity. Each organization has its own subscription scope, workspace collections, active member lists, and billing records.
- **Membership**: Modeled via `OrganizationMembership` (association entity). This links a User to an Organization, scoping their active context and role permissions.

---

## 2. Tenant Isolation & Switching
- **Isolation Boundaries**: All SQL queries, API endpoints, and UI dashboard renders must filter content utilizing the active tenant key `organization_id`.
- **Tenant Switching**: When a user switches context via the organization switcher dropdown, the frontend must reset active workspace states and reload all dashboard variables with the new header `X-Organization-ID` values.

---

## 3. Subscription & Plan Limits
- **Plan Types**:
  - **Free**: Max 3 seats, 1 workspace.
  - **Pro**: Max 15 seats, unlimited workspaces.
  - **Enterprise**: Unlimited seats, unlimited workspaces.
- **Seat Limit Enforcement**:
  - The seat limit counts active memberships in `OrganizationMembership` where `deleted_at IS NULL` plus pending invitations in `Invitation` where `status = 'pending'`.
  - When a Workspace Admin attempts to invite a user, the backend checks:
    `Active Members + Pending Invites < Plan Seat Limit`.
  - If the check fails, the invitation is blocked and the API returns `422 Unprocessable Entity`.

---

## 4. Cross-Tenant Support Admin Access
- Platform Support Admins have global views for debugging but are blocked from direct, unlogged access.
- Impersonating a tenant's workspace requires:
  1. An active customer support ticket ID.
  2. Temporary token generation which is valid for 2 hours only.
  3. Generating an immutable system event log (`support.impersonation.started`).

---

## 5. Owner Decisions and Governance
- **Ownership Transfer**: If the Organization Owner leaves, they must nominate an active member to receive Owner permissions. The system updates the target member's role to Owner and downgrades the original owner to Workspace Admin.
- **Organization Deletion**: Deletion of the organization soft-deletes the organization record and all associated memberships, workspaces, and dashboards.

---

## Related Files

- [04-user-roles.md](04-user-roles.md) — Allowed roles definitions.
- [07-data-model.md](07-data-model.md) — Organization membership entities.
- [10-security-model.md](10-security-model.md) — Security access controls.
