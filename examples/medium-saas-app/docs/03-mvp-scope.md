# 03 — MVP Scope: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document establishes the MVP boundaries for the Team Subscription Manager. It applies the MoSCoW prioritization model to enforce strict guidelines on what must be built, what is deferred, and what is strictly out of scope.

---

## MoSCoW Prioritization Matrix

### 1. Must Have (In-Scope for MVP)
- **Multi-Tenant Structure**:
  - Organization profiles containing multiple workspaces.
  - Multi-tenant data segregation enforced at the database level.
- **Team Membership & Invites**:
  - Invitation flows via token-based links.
  - Organization switching for users belonging to multiple tenants.
- **Role & Permission Management**:
  - User role assignment (Owner, Workspace Admin, Billing Manager, Team Member, Read-only Viewer).
  - Backend authorization checking on all API calls.
- **Seat Limits & Feature Gating**:
  - Hard seat limits enforced on active team memberships based on plan selection.
  - Subscription status dashboards (Active, Past Due, Cancelled, Expired).
- **Billing History & Invoice Placeholders**:
  - List of invoice placeholders with dynamic dates, payment statuses, and currency symbols.
  - Printable invoice layout details representing downloadable PDF placeholders.
- **Reporting & Logging**:
  - Centralized Admin Dashboard displaying active seat counts, remaining seats, and active invites.
  - Immutable Member Activity Log recording membership additions, role updates, and plan changes.
  - Basic Reports showing seat usage snapshots over time.
- **Notification Preferences**:
  - User preference checkboxes for email and in-app notifications.

### 2. Should Have (Targeted for Post-MVP)
- **Seat Limit Warning Notifications**:
  - In-app alerts when an organization reaches 90% of its seat capacity.
- **Support Admin Impersonation Logs**:
  - Strict logging of Support Admin access to tenant screens for debugging purposes.
- **Dynamic Chart Exports**:
  - Exporting basic seat reports as CSV/Excel files using locale-aware formatting.

### 3. Could Have (Future Enhancements)
- **Google Workspace Directory Sync**:
  - Automatic synchronization of workspace members from Google Workspace / Okta profiles.
- **Automated Seat Reclamation Recommendations**:
  - AI-driven suggestions to remove users who have not logged in for over 60 days.

### 4. Won't Have / Out of Scope (MVP Restrictions)
- **Real Payment Gateway Integration**:
  - No direct Stripe, PayPal, or merchant api integrations. All plan changes are mocked, and checkout screens use placeholders.
- **Real Tax Calculations**:
  - No integration with VAT/tax engines. Tax rates are mocked as simple, static inputs.
- **Real Bank/Accounting Workflows**:
  - No ledger exports to QuickBooks or Xero; no bank statement reconciliation.
- **Mobile Native Application**:
  - No Android/iOS Swift or Kotlin builds. Responsive web-app view only.
- **Ecommerce Checkout Cart**:
  - No retail shop, coupon code engines, or shopping cart structures.
- **Production Billing Automation**:
  - No automated invoice generation based on cron jobs. All invoices are stored as static placeholders for reference validation.

---

## MVP Feature Specifications Summary

| Feature Area | Description | Constraint / Limit |
|--------------|-------------|--------------------|
| Organizations | A user can create or join multiple Organizations. | Scoped isolation |
| Seat Limits | Enforce max users based on selected plan (e.g., Free: 3 seats, Team: 15 seats, Enterprise: Unlimited). | Enforced at invite accept |
| Invoice Detail | Shows mock invoice amount, payment date, and items list. | Static layout only |
| Activity Log | Records actor, timestamp, event, and IP placeholder. | Immutable |
| Notifications | Scopes preferences to (Invitations, Billing, Security, Reports). | Stored in JSON |

---

## Related Files

- [01-product-brief.md](01-product-brief.md) — High-level goals.
- [06-pages-spec.md](06-pages-spec.md) — Detailed UI design for in-scope pages.
- [18-saas-multitenant-notes.md](18-saas-multitenant-notes.md) — Multi-tenant implementation guidelines.
