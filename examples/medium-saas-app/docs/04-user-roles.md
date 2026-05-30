# 04 — User Roles: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document specifies the authentication vs. authorization model and details the role permissions matrix for the Team Subscription Manager SaaS platform.

---

## Authentication vs. Authorization

- **Authentication (AuthN)**: Handled globally at the platform level (e.g. via Supabase Auth). Verifies *who* the user is, issuing a secure JWT containing the user's global `user_id`.
- **Authorization (AuthZ)**: Handled at the organization level (tenant boundary). Verifies *what* the user is allowed to do within a specific Organization based on their active membership role.

---

## System Roles vs. Organization Roles

- **System Roles (Global)**: Broad access scopes mapping to the infrastructure layer (e.g. Platform Support Admin). These exist independently of tenant organizations.
- **Organization Roles (Tenant-Scoped)**: Access levels mapping to specific organization records (Owner, Workspace Admin, Billing Manager, Member, Viewer). A single user can have different organization roles across different tenants (e.g., Owner in Organization A, but Read-only Viewer in Organization B).

---

## Roles and Permissions Matrix

| Actions / Resources | Platform Support Admin | Organization Owner | Workspace Admin | Billing Manager | Team Member | Read-only Viewer |
|---------------------|------------------------|--------------------|-----------------|-----------------|-------------|------------------|
| Change Plan / Billing | Read-only (Audited) | Read/Write | Blocked | Read/Write | Blocked | Blocked |
| View Invoice PDFs | Read-only (Audited) | Read | Blocked | Read/Write | Blocked | Blocked |
| Invite Members | Blocked | Read/Write | Read/Write | Blocked | Blocked | Blocked |
| Update Member Roles | Blocked | Read/Write | Read/Write | Blocked | Blocked | Blocked |
| Transfer Ownership | Blocked | Read/Write | Blocked | Blocked | Blocked | Blocked |
| Access Support Console | Read/Write | Blocked | Blocked | Blocked | Blocked | Blocked |
| Delete Workspace | Blocked | Read/Write | Read/Write | Blocked | Blocked | Blocked |
| View Activity Log | Read-only (Audited) | Read | Read | Read-only | Read-only | Read-only |

---

## Role Definitions

### 1. Platform Support Admin (System Role)
- **Role Purpose**: Troubleshoot customer-facing issues across the entire platform.
- **Allowed Actions**: View metadata for organizations, view billing states (masked), and access the Support Console. Impersonate user roles with a temporary, audited token.
- **Restricted Actions**: Cannot modify tenant data directly (unless authorized by transient system tokens); cannot view raw passwords.
- **Owner Approval Requirements**: Requires an active support ticket and system-generated access approval token.
- **Audit Events**: `support.impersonation.started`, `support.tenant_metadata.viewed`.

### 2. Organization Owner (Organization Role)
- **Role Purpose**: Legal and financial controller of the organization.
- **Allowed Actions**: Change plans, select seat capacity, assign/change any user roles, transfer ownership, delete the organization.
- **Restricted Actions**: Cannot bypass global platform settings or access other tenant records.
- **Owner Approval Requirements**: Self-approving; changes to subscriptions trigger secondary verification emails.
- **Audit Events**: `org.ownership.transferred`, `org.subscription.updated`, `org.member.deleted`.

### 3. Workspace Admin (Organization Role)
- **Role Purpose**: Day-to-day manager of workspace team settings.
- **Allowed Actions**: Create workspaces, invite new members, assign Member or Viewer roles, remove team members from workspaces.
- **Restricted Actions**: Cannot view invoices, cannot change plans, cannot change Owner role.
- **Owner Approval Requirements**: None for membership actions; role elevation to Admin requires Owner approval.
- **Audit Events**: `workspace.created`, `member.invited`, `member.role_updated`.

### 4. Billing Manager (Organization Role)
- **Role Purpose**: Administrative manager of financials.
- **Allowed Actions**: View billing invoices, edit mock payment details, update billing email, run seat reports.
- **Restricted Actions**: Cannot create workspaces or modify team memberships.
- **Owner Approval Requirements**: Changing billing configurations triggers email alerts to the Owner.
- **Audit Events**: `billing.invoice_downloaded`, `billing.payment_method_updated`.

### 5. Team Member (Organization Role)
- **Role Purpose**: Standard collaborative member.
- **Allowed Actions**: Edit profile, update personal notification preferences, view teammate directory.
- **Restricted Actions**: Cannot change workspace structures, billing roles, or access billing overview.
- **Owner Approval Requirements**: Self-managed profile updates.
- **Audit Events**: `user.profile.updated`, `user.preferences.updated`.

### 6. Read-only Viewer (Organization Role)
- **Role Purpose**: External stakeholder or read-only observer.
- **Allowed Actions**: View dashboard, read reports, view list of workspaces and team directories.
- **Restricted Actions**: All write actions are blocked.
- **Owner Approval Requirements**: None.
- **Audit Events**: `reports.viewed`.

---

## Related Files

- [02-target-users.md](02-target-users.md) — Persona descriptions and pain points.
- [10-security-model.md](10-security-model.md) — Enforcement mechanics of authorization boundaries.
- [18-saas-multitenant-notes.md](18-saas-multitenant-notes.md) — Multi-tenant role scoping.
