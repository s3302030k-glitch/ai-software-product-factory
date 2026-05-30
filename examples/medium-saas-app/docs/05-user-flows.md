# 05 — User Flows: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document details the step-by-step user journeys and flows for the Team Subscription Manager SaaS platform.

---

## User Flows Index

### 1. Sign Up and Create Organization
- **Actor**: Anonymous visitor
- **Preconditions**: Visitor must have a valid email address.
- **Steps**:
  1. Visitor inputs email and password on the Sign Up page.
  2. Submits the form, triggering a mock verification email.
  3. Enters the dashboard onboarding view to name their first Organization (e.g., "Acme Corp").
  4. Clicks "Create", initializing database records with the visitor as the `Organization Owner`.
- **Success State**: User is authenticated and redirected to the new Organization's Dashboard.
- **Failure/Edge Cases**:
  - Email already exists: System displays "Account already exists" error and redirects to Sign In.
- **Audit Events**: `auth.signup`, `org.created`

### 2. Invite Team Member
- **Actor**: Workspace Admin or Organization Owner
- **Preconditions**: Must be logged in; organization must have unused seats available in the current plan limit.
- **Steps**:
  1. Actor navigates to the "Members" page and clicks "Invite Member".
  2. Inputs the invitee's email and selects a role (e.g., Team Member).
  3. Clicks "Send Invite".
  4. System generates an invite token, records it in the database, and increments the "Pending Invites" seat reservation count.
- **Success State**: Invitation appears in the "Pending Invites" list, and a mock invitation token link is created.
- **Failure/Edge Cases**:
  - Seat limit reached: Invite button is disabled, and system displays a "Seat Limit Exceeded" alert.
- **Audit Events**: `member.invite_created`

### 3. Accept Invitation
- **Actor**: Invited user (existing or new)
- **Preconditions**: Invitee must access the registration URL containing the invite token.
- **Steps**:
  1. User clicks the invite link token.
  2. System validates the token expiration and matches it to the invited email.
  3. User enters account password details (if new) or logs in.
  4. System adds the user to the organization membership record and clears the invite token.
- **Success State**: User is redirected to the dashboard of the inviting Organization.
- **Failure/Edge Cases**:
  - Token expired/invalid: Displays "Invitation link expired or invalid" screen.
- **Audit Events**: `member.invite_accepted`

### 4. Change Member Role
- **Actor**: Organization Owner or Workspace Admin
- **Preconditions**: Actor must be active within the organization; target user must be an active member.
- **Steps**:
  1. Actor navigates to the "Members" list.
  2. Locates the target member, clicks "Edit Role".
  3. Selects a new role from the dropdown (e.g., elevated from Member to Admin).
  4. Clicks "Save".
- **Success State**: Target member's permissions update instantly; new role is displayed.
- **Failure/Edge Cases**:
  - Workspace Admin trying to update Owner: Action is blocked in the UI and rejected by the API layer.
- **Audit Events**: `member.role_updated`

### 5. Remove Member
- **Actor**: Organization Owner or Workspace Admin
- **Preconditions**: Active member lookup.
- **Steps**:
  1. Actor navigates to "Members".
  2. Clicks "Remove Member" next to the target user.
  3. System prompts with a confirmation dialog: "Are you sure you want to reclaim this seat?".
  4. Confirms, removing the user from the `OrganizationMembership` table and freeing up a seat.
- **Success State**: User is removed, active seat count decrements.
- **Failure/Edge Cases**:
  - Attempting to remove the last Owner: System blocks removal with "Organizations must have at least one Owner".
- **Audit Events**: `member.removed`

### 6. Switch Organization
- **Actor**: Any User belonging to multiple organizations
- **Preconditions**: Logged in.
- **Steps**:
  1. User clicks the Organization Switcher dropdown in the navigation header.
  2. Dropdown shows a list of organizations the user belongs to.
  3. User selects "Beta Corp".
  4. System resets local state, changes active organization context headers, and refreshes dashboard queries.
- **Success State**: UI loads the workspaces and billing details of "Beta Corp".
- **Failure/Edge Cases**:
  - Network timeout: Reverts to original organization and shows warning alert.
- **Audit Events**: `user.org_switched`

### 7. Select Plan Placeholder
- **Actor**: Organization Owner
- **Preconditions**: Plan upgrade/downgrade mockup review.
- **Steps**:
  1. Owner navigates to "Plans".
  2. Reviews plans list (Free, Pro, Enterprise).
  3. Clicks "Select Plan" under "Pro Plan".
  4. System displays a plan confirmation modal showing seat pricing and terms.
  5. Owner clicks "Confirm".
  6. System updates mock subscription fields and updates plan limits.
- **Success State**: Subscription plan upgrades to Pro; seat limit extends to 15.
- **Failure/Edge Cases**:
  - Downgrading to a plan with fewer seats than currently active: System warns "You must remove members before downgrading to this plan".
- **Audit Events**: `org.subscription.plan_changed`

### 8. View Subscription Status
- **Actor**: Organization Owner or Billing Manager
- **Preconditions**: Active billing access.
- **Steps**:
  1. Actor navigates to "Billing Overview".
  2. System queries the database for plan details and seat limits.
  3. Displays active plan name, renewal date, current seat usage, billing status (e.g. Active).
- **Success State**: Actor reviews the status details.
- **Failure/Edge Cases**:
  - Subscription status past due: Displays warnings in yellow headers across the application.
- **Audit Events**: `billing.status.viewed`

### 9. View Invoice List Placeholder
- **Actor**: Organization Owner or Billing Manager
- **Preconditions**: Active billing access.
- **Steps**:
  1. Actor navigates to "Invoice List".
  2. System loads placeholder invoice records.
  3. Displays table columns: Invoice Number, Date, Amount (e.g. $15.00), Status (Paid/Unpaid).
- **Success State**: Billing manager can review all historical invoice placeholders.
- **Failure/Edge Cases**:
  - No invoices exist: Displays clean empty state.
- **Audit Events**: `billing.invoices.viewed`

### 10. Download Invoice PDF Placeholder
- **Actor**: Organization Owner or Billing Manager
- **Preconditions**: Navigated to an invoice detail page.
- **Steps**:
  1. Actor clicks "Download PDF".
  2. System renders a printable invoice template using CSS print media directives.
  3. Opens the system print/PDF download dialog.
- **Success State**: User saves the clean PDF representation of the invoice.
- **Failure/Edge Cases**:
  - Browser block popups: Alerts user to print using manual browser key combination.
- **Audit Events**: `billing.invoice.printed`

### 11. View Admin Dashboard
- **Actor**: Workspace Admin, Owner, or Billing Manager
- **Preconditions**: Logged in.
- **Steps**:
  1. User navigates to dashboard home.
  2. System fetches seat limits, active members count, pending invites count, and recent activity logs.
  3. Renders summary cards and charts.
- **Success State**: User sees correct metrics.
- **Failure/Edge Cases**:
  - Fresh account: Renders empty dashboard with onboarding tutorial tooltips.
- **Audit Events**: `dashboard.viewed`

### 12. Review Activity Log
- **Actor**: Organization Owner or Workspace Admin
- **Preconditions**: Logged in.
- **Steps**:
  1. User navigates to the "Activity Log" page.
  2. System fetches audit trail events for the current organization ID.
  3. Lists events in descending chronological order.
- **Success State**: User audits security events.
- **Failure/Edge Cases**:
  - Long list: Paginated list loads next 50 events on scroll.
- **Audit Events**: `activity_log.reviewed`

### 13. Update Notification Preferences
- **Actor**: Any User
- **Preconditions**: Logged in.
- **Steps**:
  1. User navigates to "Notification Preferences".
  2. Toggles checkboxes for email alerts and in-app updates.
  3. Clicks "Save Settings".
  4. System updates user metadata in JSON preferences columns.
- **Success State**: Saves preferences and displays success toast.
- **Failure/Edge Cases**:
  - Validation error: None.
- **Audit Events**: `user.preferences.updated`

---

## Related Files

- [02-target-users.md](02-target-users.md) — Persona descriptions.
- [06-pages-spec.md](06-pages-spec.md) — UI layouts that support these flows.
- [09-api-design.md](09-api-design.md) — API endpoints called during steps.
