# 02 — Target Users: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document details the target users and personas for the Team Subscription Manager SaaS platform. It establishes the context for why each role interacts with the app, their functional boundaries, and their pain points.

---

## Target User Personas

### 1. Organization Owner
- **Role Purpose**: The primary account holder who registered the Organization. Represents the legal and financial owner of the subscription.
- **Goals**: 
  - Manage subscription tiers and billing settings.
  - Approve high-level organization adjustments and workspace structures.
  - Track seat utilization vs. plan costs.
- **Permissions Summary**: Full read/write access to all organization records, workspace setups, and billing configurations. Ownership transfer rights.
- **Key Pages**: Billing Overview, Plans, Members List, Settings.
- **Pain Points**: Fear of unexpected billing charges or seat limit overages; difficulty in transferring ownership to other stakeholders if they leave the company.
- **Out-of-Scope Abilities**: Cannot view database credentials or direct system infrastructure logs; cannot access records of other tenants on the SaaS platform.

### 2. Workspace Admin
- **Role Purpose**: A team member delegated to manage day-to-day work areas (Workspaces) within the organization.
- **Goals**:
  - Add or remove team members within assigned workspaces.
  - Configure workspace settings and project categories.
  - Monitor member activity logs for security and compliance.
- **Permissions Summary**: Read/write access to workspaces, member invites, and roles (except Owner/Platform Admin roles).
- **Key Pages**: Members List, Invitations, Activity Log.
- **Pain Points**: Friction when inviting new members due to hitting plan seat limits without warnings; lack of visibility into subscription limits.
- **Out-of-Scope Abilities**: Cannot change subscription plans, view invoice PDF details, update credit card credentials, or delete the main Organization.

### 3. Billing Manager
- **Role Purpose**: A financial or administrative user responsible for payments, invoices, and subscription compliance.
- **Goals**:
  - Access, view, and download invoice history records.
  - Update plan placeholder subscription details.
  - Reconcile invoice amounts with workspace seat usage summaries.
- **Permissions Summary**: Read/write access to plans, invoice lists, payment status mock inputs, and seat usage history. Read-only access to workspaces.
- **Key Pages**: Billing Overview, Invoice List, Plans, Reports.
- **Pain Points**: Mismatches between live workspace active seat counts and the invoices sent; difficulty downloading structured CSV/PDF history files.
- **Out-of-Scope Abilities**: Cannot invite team members, change user roles (e.g. elevate a member to Admin), or delete audit/activity logs.

### 4. Team Member
- **Role Purpose**: A standard collaborative user working within one or more workspaces.
- **Goals**:
  - Collaborate on workspace items and configure personal profile settings.
  - View team directories to find colleagues.
- **Permissions Summary**: Read-only access to team directory. Read/write access to their own account settings and personal notification preferences.
- **Key Pages**: Dashboard, Account Settings, Notification Preferences.
- **Pain Points**: Receiving too many notifications; difficulty switching between multiple organizations they belong to.
- **Out-of-Scope Abilities**: Cannot view invoices, cannot invite new members (unless permitted by organization policy placeholders), cannot configure roles, plans, or view activity logs of other members.

### 5. Read-only Viewer
- **Role Purpose**: A stakeholder, auditor, or external collaborator who needs visibility without editing privileges.
- **Goals**:
  - Review dashboard reports and workspace directories.
  - Audit membership activity.
- **Permissions Summary**: Read-only access to workspaces, member listings, and activity logs. No write access.
- **Key Pages**: Dashboard, Members List, Reports.
- **Pain Points**: Accidental data changes by other users; need for simple printable summaries.
- **Out-of-Scope Abilities**: Cannot make any modifications, cannot send invites, cannot view billing or invoice details.

### 6. Platform Support Admin
- **Role Purpose**: A system-level administrator employed by the SaaS provider to troubleshoot tenant issues.
- **Goals**:
  - Assist tenants with login problems, invite failures, or billing state mismatches.
  - Investigate bug reports on behalf of organizations.
- **Permissions Summary**: Global read-only access to tenant metrics and configuration (requires audit-logged impersonation token). Write access is strictly limited and audited.
- **Key Pages**: Platform Support Console.
- **Pain Points**: Lack of audit trails when debugging tenant issues; risk of accidentally viewing sensitive tenant-specific information.
- **Out-of-Scope Abilities**: Cannot view unmasked user passwords; cannot download invoice PDFs containing billing details without explicit tenant owner approval tokens; cannot bypass RLS without system log generation.

---

## Related Files

- [01-product-brief.md](01-product-brief.md) — Context of why the product exists.
- [04-user-roles.md](04-user-roles.md) — Exact permissions matrix mapping roles to actions.
- [10-security-model.md](10-security-model.md) — Tenant isolation and Support Admin access boundaries.
