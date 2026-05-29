# SaaS Billing Boundary Guidelines

> Defines standards for designing the boundary between third-party billing providers (e.g., Stripe, Paddle) and the internal application domain model, webhook handling, and idempotency rules.

---

## Purpose

Maintain clear logical separation between the external billing system and the internal application database. This ensures billing provider issues do not corrupt the local data model, payment failures do not silently break permissions, and sensitive customer accounts are securely mapped.

## Status

`Active` — Mandatory for all software architects, backend developers, and payment engineers building or reviewing subscription structures.

---

## Billing Boundary Principles

1. **Explicit Separation**: Keep financial data and billing transaction operations in the billing provider's system. Do not duplicate transaction logs or invoice line details in the application database unless required for local reporting.
2. **Webhook Idempotency**: All webhook events received from the billing provider must be handled idempotently to prevent double-processing or incorrect subscription states.
3. **Audit Trail Traceability**: Every critical synchronization event (e.g., subscription created, invoice payment succeeded) must be traceably logged.
4. **No Sensitive ID Exposure**: Do not expose sensitive billing IDs (e.g., Customer keys, Checkout hashes, Subscription tokens) to the client UI or in public API payloads.

---

## Billing Provider vs. Application Domain

- **Billing System Scope**: Owns payment collection, credit cards, billing cycles, invoice PDF generation, refund math, tax calculation, and payment compliance (PCI-DSS).
- **Application System Scope**: Owns the mapping of the subscription tier to localized plan entitlements, seat counts, active users, feature access flags, and workspace data.
- **Bridge Fields**: Only store the minimal necessary bridging keys in the application's local `organizations` or `subscriptions` table:
  - `billing_customer_id` (e.g., `cus_...` for Stripe)
  - `billing_subscription_id` (e.g., `sub_...` for Stripe)
  - `subscription_status` (internal enum state)
  - `plan_id` (reference code matching a local plan template)

---

## Customer ID / Subscription ID Handling

- **Immutable Bridge**: The association between `organization_id` and the external billing customer ID must be immutable once created.
- **Secure Lookups**: When a customer clicks "Manage Billing," generate a secure, server-side Billing Portal Checkout Session link via the billing provider's API. Do not expose client keys or allow direct modification of billing keys via public API endpoints.

---

## Invoice and Payment State Notes

- **Read-Only Invoices**: Keep invoices read-only inside the app. For custom exports or reporting adjacent to invoices, refer to the [Print Reporting Pack](../../print-reporting-pack/README.md) to manage invoices and exported transaction files.
- **Local Ledger Boundaries**: Local tables should store only high-level status flags (e.g., `invoice_paid`, `invoice_failed`) and a timestamp, pointing to the billing platform as the absolute source of truth for full financial ledgers. For currency math, rounding, and audit rules, see [Financial Business Logic Pack](../../financial-business-logic-pack/README.md).

---

## Webhook Handling Rules

Webhooks are asynchronous events sent by the billing provider to notify the application of billing events:
1. **Verification**: Always verify webhook signatures securely using the provider's signing key before processing the payload.
2. **Queueing Strategy**: Handlers should immediately place incoming validated webhook events into a message queue (e.g., Redis queue) and return a `200 OK` status to the provider. Do not run heavy database operations synchronously inside the webhook request lifecycle, as this risks timeout retries from the provider.

---

## Idempotency

To prevent race conditions, double upgrades, or duplicate charges:
- **Idempotency Key Tracking**: Store all processed webhook event IDs in an `idempotent_events` database table:
  - Fields: `event_id` (Primary Key), `provider` (string), `processed_at` (timestamp).
- **Check-Before-Process**: Before executing a webhook event, query `idempotent_events` for `event_id`. If it already exists, discard the event immediately and return success (idempotent skip).

---

## Failed Payment Handling

- **Status Downside**: If a payment fails (e.g., `invoice.payment_failed`), do not immediately suspend all user access. 
- **Graceful Reminders**: Map the state to `PastDue` and trigger an automated system email/banner notifying the administrators.
- **Automated Suspension**: Only downgrade the entitlement state to `Suspended` after the billing provider marks the subscription as `unpaid` or `canceled` (following the retries and grace periods).

---

## Refund / Credit / Cancellation Boundary

- **Initiation Rule**: Refunds, billing adjustments, and direct credits must be initiated in the billing provider's dashboard or via secure, restricted admin panel APIs.
- **Entitlement Revocation**: When a refund results in subscription cancellation, the webhook receiver must sync the cancel event and immediately execute the plan gating downgrades locally.

---

## Permission and Billing Separation

> [!WARNING]
> **Billing state must not silently override permissions without documented rules.**
> Do not let billing state directly manipulate individual user permissions.

- **Status Separation**: Subscription statuses represent *organizational capabilities*. Individual user access is governed strictly by the `memberships` table.
- **Logic Mapping**: If a subscription is suspended, the application blocks the *entire organization context*, but must leave the individual user's roles, passwords, and other tenant memberships untouched.

---

## Reporting/Export Considerations

- **Revenue Data**: Do not query the local database to generate financial revenue charts or MRR metrics, as local records may mismatch active provider adjustments. 
- **Authoritative Metrics**: Revenue reports should be pulled directly from the billing platform APIs or synced to an authoritative data warehouse.

---

## Out of Scope

- **This pack does not provide payment provider integration code** (e.g., no code for Stripe SDK initialization).
- **This pack does not define legal, tax, accounting, refund, or payment compliance policy.** All such policies must be established by the product owner.

---

## Guardrails

- [ ] **SECURE WEBHOOKS**: Every billing webhook endpoint must verify signatures using the provider's secret keys.
- [ ] **IDEMPOTENT HANDLERS**: Webhook receivers must log and block duplicate event IDs.
- [ ] **NO HARDCODED SENSITIVE KEYS**: Sensitive customer credentials, gateway secret keys, or live credentials must never be committed to repository code or templates.

---

## QA Checklist

- [ ] Verify webhook verification fails and returns `401 Unauthorized` if signature headers are invalid or missing.
- [ ] Send duplicate webhook payloads containing the same `event_id`; confirm that the system processes it exactly once.
- [ ] Simulate an `invoice.payment_failed` webhook; verify that the database status changes to `past_due` and a banner appears.
- [ ] Verify that a `customer.subscription.deleted` webhook immediately downgrades organization features.
- [ ] Confirm that billing portal redirection links are created securely and do not leak internal secret keys in client logs.

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Base entity tracking.
- [09-api-design.md](../../../core/docs/09-api-design.md) — Webhook endpoints design.
- [10-security-model.md](../../../core/docs/10-security-model.md) — Secret keys storage guidelines.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation of SaaS Billing Boundary Guidelines | Antigravity |
