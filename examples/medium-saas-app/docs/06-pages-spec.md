# 06 — Pages Specification: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document specifies the page layouts, UI components, states, input validations, and security conditions for all 17 screens in the Team Subscription Manager application.

---

## Page Layout Blueprint

The application uses a standard split layout:
- **Sidebar Navigation**: Desktop collapsible menu showing Dashboard, Workspaces, Members, Billing, Reports, Activity Log, and Settings.
- **Top Header**: Organization switcher, active locale selector, notifications panel trigger, and profile avatar menu.
- **RTL Support**: Layout direction switches between LTR and RTL dynamically via `dir="rtl"` in the root HTML, with css logical properties (e.g., `margin-inline-start`) mirroring layout positions.

---

## Pages Specification

### 1. Landing / Sign In
- **Purpose**: Authenticate users and route them to their default organization workspace.
- **Allowed Roles**: Public / Anonymous.
- **Key Components**: Email input, password input, Login button, Sign Up redirect link.
- **States**: Default, Submitting, Error.
- **Validations**: Email must be structured correctly; password cannot be empty.
- **Empty/Loading/Error States**: Shows invalid email format alerts; network load indicator on button; incorrect credential toasts.
- **Security Notes**: Password input is masked; brute-force rate-limiting triggers on five failed login attempts.

### 2. Onboarding: Create Organization
- **Purpose**: Guide new sign-ups to create their initial tenant organization.
- **Allowed Roles**: Authenticated users without an existing organization membership.
- **Key Components**: Organization name input, default currency dropdown, "Create Organization" button.
- **States**: Loading, Active input.
- **Validations**: Organization name must be between 2 and 64 characters.
- **Empty/Loading/Error States**: Inline text error if field is blank; button loading spinner during DB insertion.
- **Security Notes**: Establishes the new user as Owner of the newly created organization.

### 3. Organization Switcher (Panel)
- **Purpose**: Switch context between different tenant organizations the user belongs to.
- **Allowed Roles**: All roles.
- **Key Components**: Header dropdown menu, lists of organizations, "Create New Org" button link.
- **States**: Expanded, Collapsed.
- **Validations**: None.
- **Empty/Loading/Error States**: If user only has 1 organization, the dropdown is disabled.
- **Security Notes**: Switching organization triggers clear-down of browser caches and context variables to prevent tenant data spillover.

### 4. Dashboard
- **Purpose**: Centralized summary panel of active seat metrics, subscription statuses, and alerts.
- **Allowed Roles**: Owner, Workspace Admin, Billing Manager, Member, Read-only Viewer.
- **Key Components**: Active Seat Card, Available Seats Card, Subscription Status Card, Recent Activity List, Seat Utilization Gauge.
- **States**: Normal, Seat Limit warning state (90% capacity).
- **Validations**: None.
- **Empty/Loading/Error States**: Shows blank cards with onboarding tips for new organizations.
- **Security Notes**: Content is scoped to the active tenant ID.

### 5. Members List
- **Purpose**: Manage active organization users, modify roles, and reclaim seats.
- **Allowed Roles**: Owner, Workspace Admin (Write); Billing Manager, Member, Read-only Viewer (Read-only).
- **Key Components**: Search input, Members Table (Name, Email, Role, Joined Date), "Edit Role" button, "Remove Member" button.
- **States**: List mode, Edit role modal, Remove verification modal.
- **Validations**: Can't remove the last Organization Owner.
- **Empty/Loading/Error States**: Spinner during fetch; search error text if no matches.
- **Security Notes**: Access checks are validated on backend API endpoints.

### 6. Invitations (Panel / Tab)
- **Purpose**: Send and manage organization membership invitations.
- **Allowed Roles**: Owner, Workspace Admin (Write).
- **Key Components**: "Send Invite" button, Email input field, Role dropdown, Pending Invites Table, "Revoke Invite" buttons.
- **States**: Normal, Adding invite.
- **Validations**: Email must be formatted; seat limit check must verify available slots before enabling submit.
- **Empty/Loading/Error States**: Displays "No pending invitations" placeholder if empty.
- **Security Notes**: Tokens must be hashed before storage and are valid for 72 hours.

### 7. Roles & Permissions (Read-only Spec View)
- **Purpose**: Reference view explaining what each role is permitted to do in the organization.
- **Allowed Roles**: All roles (Read-only).
- **Key Components**: Permissions grid table (Role vs Action checklist).
- **States**: Read-only grid.
- **Validations**: None.
- **Empty/Loading/Error States**: None.
- **Security Notes**: Read-only. Does not allow modification of permissions.

### 8. Billing Overview
- **Purpose**: General panel summarizing active billing status, next payment date, and current cards.
- **Allowed Roles**: Owner, Billing Manager (Read/Write).
- **Key Components**: Subscription summary card, Credit Card info mock block, Billing Address form, "Change Plan" link.
- **States**: Normal, Edit billing address.
- **Validations**: Billing email must be structured. Credit card details are not collected or validated (mock display only).
- **Empty/Loading/Error States**: Spinner during data fetching.
- **Security Notes**: Accessible only to Owner and Billing Manager.

### 9. Plans
- **Purpose**: Showcase subscription plan tiers and trigger mock change flows.
- **Allowed Roles**: Owner (Write); Billing Manager (Read-only).
- **Key Components**: Plan Grid (Free, Pro, Enterprise), "Select Plan" buttons.
- **States**: Plan comparison, Plan change modal.
- **Validations**: Cannot downgrade plan if active member counts exceed the lower plan's seat capacity.
- **Empty/Loading/Error States**: Loading animations when confirming plan selection.
- **Security Notes**: Plan updates must verify billing role permissions at backend level.

### 10. Subscription Status
- **Purpose**: Detailed subscription status tracking for billing audits.
- **Allowed Roles**: Owner, Billing Manager (Read-only).
- **Key Components**: Status indicator badge, Current seat usage graph, Renewal date metadata block.
- **States**: Active (Green), Past Due (Yellow), Cancelled (Gray), Expired (Red).
- **Validations**: None.
- **Empty/Loading/Error States**: Standard loading skeletons.
- **Security Notes**: Fully scoped to active tenant organization ID.

### 11. Invoice List
- **Purpose**: Display history of mock invoices and PDF links.
- **Allowed Roles**: Owner, Billing Manager (Read-only).
- **Key Components**: Invoices Table (Invoice ID, Date, Amount, Payment Status), "Download PDF" links.
- **States**: Default list.
- **Validations**: None.
- **Empty/Loading/Error States**: Shows "No invoices available" text for Free accounts.
- **Security Notes**: Users with Member or Viewer roles receive a 403 Forbidden page on access attempts.

### 12. Invoice Detail / PDF Placeholder
- **Purpose**: Printable/Saveable clean representation of an invoice.
- **Allowed Roles**: Owner, Billing Manager.
- **Key Components**: Invoice metadata (ID, date, billing address), Seat breakdown rows, Subtotal, Tax placeholder, Total.
- **States**: Printable layout.
- **Validations**: None.
- **Empty/Loading/Error States**: 404 screen if invoice ID does not exist.
- **Security Notes**: Rendered template uses explicit CSS `@media print` rules to hide web headers and menus.

### 13. Activity Log
- **Purpose**: Audit trails of security and configuration changes in the tenant.
- **Allowed Roles**: Owner, Workspace Admin, Billing Manager (Read-only).
- **Key Components**: Chronological audit feed list, Filter dropdown by category, Date picker.
- **States**: Filtered list, scroll paging.
- **Validations**: Start date cannot be after end date.
- **Empty/Loading/Error States**: "No events found for this filter" empty state.
- **Security Notes**: Logs are immutable and cannot be deleted by users.

### 14. Reports
- **Purpose**: Basic visualizations of seat capacity usage over time.
- **Allowed Roles**: Owner, Workspace Admin, Billing Manager (Read-only).
- **Key Components**: Line chart placeholder (showing seat usage), "Export to CSV" button.
- **States**: Chart render, exporting state.
- **Validations**: Date ranges limited to maximum 12 months.
- **Empty/Loading/Error States**: Chart displays flat lines if no snapshot history exists.
- **Security Notes**: Export file is assembled completely within tenant-scoped database queries.

### 15. Notification Preferences
- **Purpose**: Allow users to control their alert scopes.
- **Allowed Roles**: All authenticated users.
- **Key Components**: Checkbox grid (Email, Push toggles for Invitations, Billing Alerts, Weekly Reports).
- **States**: Editing form.
- **Validations**: None.
- **Empty/Loading/Error States**: Success notification toaster on save.
- **Security Notes**: Users can only modify their own preference metadata.

### 16. Account Settings
- **Purpose**: Personal profile updates.
- **Allowed Roles**: All authenticated users.
- **Key Components**: Full Name input, Password Change inputs, language/locale dropdown.
- **States**: Edit details.
- **Validations**: Full name must be between 1 and 100 characters. Password strength must be checked.
- **Empty/Loading/Error States**: Inline validation indicators.
- **Security Notes**: Password changes require re-authenticating with the current password.

### 17. Platform Support Console
- **Purpose**: Allow Support Admins to resolve customer tickets.
- **Allowed Roles**: Platform Support Admin (System Role) only.
- **Key Components**: Tenant list, Active impersonation buttons, impersonation banner.
- **States**: Impersonation mode.
- **Validations**: Impersonation requires a valid Support Ticket ID entry.
- **Empty/Loading/Error States**: "Access Denied" page if accessed by non-Platform Support Admins.
- **Security Notes**: Every impersonation action is logged in global system audit tables.

---

## Related Files

- [05-user-flows.md](05-user-flows.md) — User journeys passing through these pages.
- [09-api-design.md](09-api-design.md) — Underpinning endpoint structures.
- [22-rtl-i18n-notes.md](22-rtl-i18n-notes.md) — RTL UI specs.
