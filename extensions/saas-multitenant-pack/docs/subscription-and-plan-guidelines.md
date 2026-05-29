# Subscription and Plan Guidelines

> Defines standards for designing subscription plans, feature gating mechanics, seat allocation, usage quotas, grace periods, and mapping billing states to internal authorization states.

---

## Purpose

Prevent revenue leakage, accidental over-allocation of resources, and customer lockouts by establishing strict, server-side rules for tracking, evaluating, and changing subscription plan status and usage quotas.

## Status

`Active` — Mandatory for all software architects, backend developers, database engineers, and product managers defining plans or feature limitations.

---

## Subscription Model Principles

1. **Server-Side Enforcement**: Plan gating must be enforced server-side where it protects access or limits. Client-side gating is strictly for UX purposes.
2. **Explicit Decoupling**: Separate subscription state evaluations (Is this subscription active?) from resource limit evaluations (Has this tenant used all their allocated seats?).
3. **Explicit State Mapping**: The relationship between the billing provider status and the internal application entitlement status must be mapped explicitly. Never make assumptions about billing states.
4. **Owner-Approved Rules**: All subscription state changes, downgrades, and grace periods require owner-approved business rules.

---

## Plan Model

Plans must be defined globally in configuration or in a global database table (read-only for tenants). Each plan object must specify:
- `plan_id` (e.g., `free`, `growth`, `enterprise`)
- `name`
- `max_seats` (integer, `-1` for unlimited)
- `max_resources` (quota objects, e.g., max monthly projects, max storage space)
- `enabled_features` (array of feature identifier strings)

---

## Feature Gating

- **Feature Gating Engine**: Create a centralized service (e.g., `PlanGatingService`) that takes the active `organization_id` and a `feature_key` and evaluates whether access is allowed.
  ```typescript
  // Bad: scattering direct DB plan checks throughout code
  if (org.plan === 'enterprise') { allowFeature(); }

  // Good: centralized gating check
  const hasAccess = await PlanGatingService.hasFeature(orgId, 'advanced_analytics');
  if (!hasAccess) { throw new ForbiddenException('Upgrade to access advanced analytics'); }
  ```

---

## Usage Limits

- **Dynamic Limits Verification**: For resources that have quantitative limits (e.g., "maximum 50 active items"), the system must check current usage before permitting creation:
  1. Retrieve the tenant's plan quota for the resource type.
  2. Run a database count query of active items for the `organization_id`.
  3. Block creation if the count equals or exceeds the quota.
- **Race Condition Prevention**: For high-volume transaction limits, use database-level locking or atomic counters to prevent exceeding limits through concurrent requests.

---

## Seat Limits

> [!WARNING]
> **Seat limits and usage limits must be thoroughly tested.**
> Over-allocating user seats represents a direct loss of SaaS revenue.

- **Seat Definition**: A "seat" represents an active, invited, or provisioned `memberships` record in the organization.
- **Enforcement Rules**:
  - Before an Admin can send a new invitation or add a member, the system must verify: `current_active_members + pending_invitations < max_seats`.
  - Block invitation creation with a clear "Seat limit reached" warning if the check fails.
  - Require the Owner to upgrade their plan or deactivate an existing member before adding a new one.

---

## Trial State

- **Trial Flags**: Organizations can have a temporary `trial_ends_at` timestamp.
- **Expiration Logic**: If `current_timestamp > trial_ends_at` and no paid subscription has been active, the organization's entitlement status must immediately change to `expired` or downgrade automatically to the standard `free` tier.
- **Extended Trials**: Trial extensions must be performed exclusively by administrative tools and logged in the audit trail.

---

## Subscription Lifecycle States

The following map defines standard subscription states and their corresponding internal access entitlements:

| Billing Provider Status | Internal State | App Access Entitlement | Notes |
|-------------------------|----------------|------------------------|-------|
| `trialing` | `Trialing` | Full Plan Access | Access allowed until trial ends. |
| `active` | `Active` | Full Plan Access | standard operating state. |
| `past_due` | `PastDue` | Warning Active | Payment failed; entry allowed but warning banner displayed. |
| `unpaid` | `Unpaid` | Suspended | All write access blocked; read-only access or lock screen displayed. |
| `canceled` | `Cancelled` | Access Permitted | Access active until end of billing period. |
| `incomplete` | `Incomplete` | Locked | Initial payment failed; access blocked. |

---

## Upgrade / Downgrade Behavior

Changing plans must follow documented business rule logic:
- **Upgrades**: Immediately apply new seat and feature limits.
- **Downgrades**:
  - *Data Preservation policy*: When a tenant downgrades to a tier with lower limits, **do not silently delete or corrupt user data** that exceeds the new limits.
  - *Feature Locking*: Set excess resources to a read-only or locked state. The user can view or export their excess data but cannot create new items until they either delete the excess items or upgrade their plan again.
  - *Seat Downgrade block*: If an organization has 10 active seats and attempts to downgrade to a 5-seat plan, block the downgrade action in the UI until they manually deactivate 5 members.

---

## Grace Periods

- **Failed Payment Grace Period**: For `past_due` states, provide an owner-approved grace period (e.g., 7 days or 14 days) to allow the customer to update payment details before their access is downgraded or suspended.
- **Grace Period Config**: The duration of the grace period must be defined in the system configurations, never hardcoded.

---

## Plan Change Audit

Every plan modification, upgrade, downgrade, or trial extension must write a permanent audit entry:
- `timestamp`
- `organization_id`
- `actor_user_id`
- `previous_plan_id`
- `new_plan_id`
- `change_source` (e.g., `user_initiated`, `billing_webhook`, `system_admin_override`)

---

## Billing Provider State vs. Internal App State

- **Independent Mapping**: Do not tightly couple code execution to external payment gateway response keys. Instead, use an abstraction layer that maps external states (e.g., Stripe subscription objects) into your local database status flags (`subscription_status`).
- **Sync Reliability**: If a webhook fails to deliver, the application must fallback to checking cached local database flags, which can be periodically refreshed via scheduled background cron synchronization jobs.

---

## Out of Scope

- **Do not invent pricing, tax, refund, or billing policy.** These are commercial and legal policies that must be supplied by the product owner.
- Implementing specific payment gateways (e.g., writing Stripe checkout session Javascript).

---

## Guardrails

- [ ] **NO CLIENT-SIDE LIMITS**: Seat and usage quotas must be validated server-side.
- [ ] **NO SILENT DELETES**: Downgrading plans must never delete or destroy excess tenant records.
- [ ] **EXPLICIT MAPPING**: Map every billing provider status code to a documented application state.

---

## QA Checklist

- [ ] Test adding members up to the plan's seat limit; verify that the 11th member/invite is blocked on a 10-seat plan.
- [ ] Test the grace period transition: simulate a `past_due` webhook and verify a warning banner is displayed while full access remains.
- [ ] Test the expiration transition: simulate an `unpaid` state and verify a lock screen is displayed blocking all write actions.
- [ ] Verify that a downgraded organization can still read but cannot write to features that exceed their new limits.
- [ ] Audit the database after a plan change and confirm a `plan.changed` log entry is recorded successfully.

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Database schemas.
- [09-api-design.md](../../../core/docs/09-api-design.md) — API contracts and response structures.
- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — Verification methods.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation of Subscription and Plan Guidelines | Antigravity |
