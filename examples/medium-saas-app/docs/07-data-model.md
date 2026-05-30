# 07 — Data Model: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document specifies the logical data entities, attributes, relationships, status lifecycles, tenant scoping rules, and audit requirements for the Team Subscription Manager.

> [!WARNING]
> **NO REAL DATABASE MIGRATIONS**: This document outlines conceptual logical models for reference and documentation validation purposes only. No SQL files, schemas, database tables, or migrations are created.

---

## Data Architecture Rules

### 1. Source-of-Truth Rules
- **Organizations** are the absolute tenant boundaries.
- Every entity (except `User` and global `Plan` records) MUST contain a foreign key reference to `organization_id` to establish tenant isolation.
- Active seat counts are calculated dynamically from the active records in the `OrganizationMembership` table.

### 2. Derived vs. Stored Fields
- **Stored Fields**: Raw attributes written to the database (e.g., base plan price, seat limit count).
- **Derived Fields**: Computed dynamically on query requests (e.g., active user counts, remaining seat capacity).
- **Decoupled Financial Values**: Price values are stored as positive integers representing the smallest currency subunit (cents, e.g. `$15.00` is stored as `1500`) to prevent floating-point errors.

### 3. Soft Delete / Archive Policy
- Memberships, Organizations, and Workspaces are soft-deleted by updating a `deleted_at` timestamp.
- Audit records (`AuditEvent` and `ActivityLog`) are immutable and cannot be deleted or archived by tenant users.

---

## Logical Entities

### 1. User
- **Purpose**: Global user profile and auth credentials link.
- **Key Fields**: `user_id` (UUID), `email` (string), `full_name` (string), `created_at` (timestamp).
- **Relationships**: One-to-Many with `OrganizationMembership`, One-to-One with `NotificationPreference`.
- **Lifecycle/Status**: Active, Disabled.
- **Tenant Scoping**: Global (cross-tenant identity).
- **Audit Requirements**: Track signup and password change dates.

### 2. Organization
- **Purpose**: Represents the primary billing tenant.
- **Key Fields**: `organization_id` (UUID), `name` (string), `created_at` (timestamp), `deleted_at` (timestamp).
- **Relationships**: One-to-Many with `Workspace`, `OrganizationMembership`, `Subscription`, `InvoicePlaceholder`.
- **Lifecycle/Status**: Active, Suspended, Archived.
- **Tenant Scoping**: Self (the tenant root).
- **Audit Requirements**: Log creation, status changes, and deletion events.

### 3. OrganizationMembership
- **Purpose**: Maps a User to an Organization with a specific role.
- **Key Fields**: `membership_id` (UUID), `organization_id` (UUID), `user_id` (UUID), `role_id` (UUID), `joined_at` (timestamp).
- **Relationships**: Belongs to `User`, Belongs to `Organization`, Belongs to `Role`.
- **Lifecycle/Status**: Active, Suspended, Removed.
- **Tenant Scoping**: Scoped via `organization_id`.
- **Audit Requirements**: Log joins, role elevations, and removals.

### 4. Invitation
- **Purpose**: Token-based invitation records.
- **Key Fields**: `invitation_id` (UUID), `organization_id` (UUID), `email` (string), `role_id` (UUID), `token` (string), `expires_at` (timestamp), `accepted_at` (timestamp).
- **Relationships**: Belongs to `Organization`, Belongs to `Role`.
- **Lifecycle/Status**: Pending, Accepted, Expired, Revoked.
- **Tenant Scoping**: Scoped via `organization_id`.
- **Audit Requirements**: Log invite creations, acceptances, and revocations.

### 5. Role
- **Purpose**: Roles defining access permissions in the organization.
- **Key Fields**: `role_id` (UUID), `name` (string, e.g., "Workspace Admin"), `code` (string, e.g., `workspace_admin`), `is_system` (boolean).
- **Relationships**: One-to-Many with `OrganizationMembership`, Many-to-Many with `Permission`.
- **Lifecycle/Status**: Static / Immutable.
- **Tenant Scoping**: Global lookup or custom tenant-scoped (if custom roles are created).
- **Audit Requirements**: None.

### 6. Permission
- **Purpose**: Granular feature flags for access checking.
- **Key Fields**: `permission_id` (UUID), `code` (string, e.g., `invoice:read`), `description` (string).
- **Relationships**: Many-to-Many with `Role`.
- **Lifecycle/Status**: Static.
- **Tenant Scoping**: Global.
- **Audit Requirements**: None.

### 7. Plan
- **Purpose**: Available subscription plans and their limits.
- **Key Fields**: `plan_id` (UUID), `name` (string, e.g. "Pro Plan"), `seat_limit` (integer), `price_amount` (integer in cents), `currency_code` (string, e.g., "USD").
- **Relationships**: One-to-Many with `Subscription`.
- **Lifecycle/Status**: Active, Retired.
- **Tenant Scoping**: Global.
- **Audit Requirements**: Log changes to plan price or limits.

### 8. Subscription
- **Purpose**: Links an Organization to an active Plan.
- **Key Fields**: `subscription_id` (UUID), `organization_id` (UUID), `plan_id` (UUID), `status` (string), `current_period_start` (timestamp), `current_period_end` (timestamp).
- **Relationships**: Belongs to `Organization`, Belongs to `Plan`.
- **Lifecycle/Status**: Active, Past Due, Cancelled, Expired.
- **Tenant Scoping**: Scoped via `organization_id`.
- **Audit Requirements**: Log plan switches and status changes.

### 9. SeatUsageSnapshot
- **Purpose**: Historical log of seat capacity used.
- **Key Fields**: `snapshot_id` (UUID), `organization_id` (UUID), `snapshot_date` (date), `active_members_count` (integer), `pending_invites_count` (integer), `max_seats` (integer).
- **Relationships**: Belongs to `Organization`.
- **Lifecycle/Status**: Immutable snapshot.
- **Tenant Scoping**: Scoped via `organization_id`.
- **Audit Requirements**: Saved automatically daily via cron-level script simulation.

### 10. InvoicePlaceholder
- **Purpose**: Mock billing records.
- **Key Fields**: `invoice_id` (UUID), `organization_id` (UUID), `invoice_number` (string), `amount_subunits` (integer in cents), `tax_subunits` (integer in cents), `status` (string, e.g., "Paid"), `due_date` (date).
- **Relationships**: Belongs to `Organization`.
- **Lifecycle/Status**: Draft, Open, Paid, Uncollectible.
- **Tenant Scoping**: Scoped via `organization_id`.
- **Audit Requirements**: Log invoice downloads.

### 11. ActivityLog
- **Purpose**: User-facing audit trail of membership changes.
- **Key Fields**: `log_id` (UUID), `organization_id` (UUID), `actor_user_id` (UUID), `action` (string), `description` (text), `created_at` (timestamp).
- **Relationships**: Belongs to `Organization`, Belongs to `User` (actor).
- **Lifecycle/Status**: Immutable.
- **Tenant Scoping**: Scoped via `organization_id`.
- **Audit Requirements**: Append-only permissions.

### 12. NotificationPreference
- **Purpose**: Stores user preferences.
- **Key Fields**: `preference_id` (UUID), `user_id` (UUID), `organization_id` (UUID), `scopes` (JSONB, e.g. `{invites: true, billing: false}`).
- **Relationships**: Belongs to `User`, Belongs to `Organization`.
- **Lifecycle/Status**: Active.
- **Tenant Scoping**: Scoped via `organization_id` and `user_id`.
- **Audit Requirements**: None.

### 13. ReportDefinition
- **Purpose**: Stored structures for generating seat reports.
- **Key Fields**: `report_id` (UUID), `organization_id` (UUID), `title` (string), `parameters` (JSONB), `created_at` (timestamp).
- **Relationships**: Belongs to `Organization`.
- **Lifecycle/Status**: Active.
- **Tenant Scoping**: Scoped via `organization_id`.
- **Audit Requirements**: Log creation and configuration changes.

### 14. AuditEvent
- **Purpose**: System-level immutable security log.
- **Key Fields**: `event_id` (UUID), `organization_id` (UUID), `user_id` (UUID), `event_type` (string), `ip_address` (string), `payload` (JSONB), `created_at` (timestamp).
- **Relationships**: Belongs to `Organization`, Belongs to `User`.
- **Lifecycle/Status**: Immutable (System-locked).
- **Tenant Scoping**: Scoped via `organization_id`.
- **Audit Requirements**: Append-only. Bypasses standard soft-delete rules.

---

## Related Files

- [03-mvp-scope.md](03-mvp-scope.md) — Maps features to entities.
- [09-api-design.md](09-api-design.md) — Details payloads representing these entities.
- [20-financial-business-logic-notes.md](20-financial-business-logic-notes.md) — Outlines precision constraints.
