# 10B — Push Notification Guidelines

> Details rules for requesting push notification permissions, categorizing alerts, managing device tokens, routing deep links, and enforcing lock-screen privacy.

---

## Purpose

Define the standards for integrating push notifications. This prevents notification fatigue, ensures compliance with platform store guidelines, protects user privacy on lock screens, and prevents deep link security exploits.

## Status

`Active` — Must be followed by all backend developers, mobile frontend developers, and product managers.

---

## Push Notification Principles

1. **Explicit Opt-in**: Push notifications must be opt-in where platform or product rules require it. Users must be able to choose which notifications they receive.
2. **Lock-Screen Privacy**: Notification payloads must not leak sensitive personal, financial, or workspace data on the system lock screen.
3. **Server-Side Deep Link Security**: Deep links included in notification payloads must be validated server-side; links must not bypass authorization checks.
4. **Token Security**: Push tokens must be treated as sensitive data and scoped specifically to the user session that generated them.

---

## Notification Permission Timing

- Do not trigger the native system push notification permission prompt immediately on the first launch of the app.
- Trigger push permission requests contextually, such as:
  - After the user submits a form that initiates a workflow (e.g. "Notify me when this order ships").
  - On a dedicated notification preferences screen where the user explicitly toggles push alerts.
- Use a soft confirmation modal explaining the benefit of the notifications before triggering the native OS prompt.

---

## Notification Categories and Preferences

- Provide an in-app preferences screen allowing users to toggle specific categories:
  - **Transactional**: Invoice updates, payment alerts, membership approvals, operational check-ins.
  - **Marketing/Updates**: Special promotions, news, feature highlights. (Marketing alerts must be opt-in and disabled by default).
- Preferences must be stored on the server database to ensure consistency across multiple devices.

---

## Transactional vs. Marketing Notifications

- **Transactional**: Critical to the user’s operations. Can bypass marketing opt-outs but must still respect user settings if configured.
- **Marketing**: Strictly regulated. Require explicit human owner approval before campaigns are implemented. Must contain a clear opt-out path.

---

## Quiet Hours and Frequency Limits

- **Quiet Hours**: Allow users to set time windows during which non-urgent notifications are silenced (e.g., 10 PM to 7 AM local time).
- **Frequency Capping**: Implement limits on the server to prevent sending duplicate or excessive notifications to a single device (e.g. max 3 marketing notifications per week).

---

## Deep Link Handling

- Notification payloads can contain dynamic target paths (e.g., `invoice_id`).
- When the user taps the notification:
  1. Open the app and authenticate the session first.
  2. Validate the user has active permission to read the target entity.
  3. Route the user to the target screen.
- **No Auth Bypass**: A deep link must never bypass login, paywall, or tenant-isolation checks. If the user is unauthenticated, redirect them to the login screen first and preserve the redirect path.

---

## Token Lifecycle

- Push tokens (APNs for iOS, FCM for Android) are created by the device.
- Send the token to the server database mapped to:
  - `user_id`
  - `device_id` (unique hardware hash or client identifier)
  - `tenant_id` (current tenant context)
- **Token Invalidation**: Delete the push token record from the server database immediately when the user:
  1. Logs out of the app.
  2. Disables push notifications in their device settings (report token failure).
  3. Closes or deactivates their tenant membership.

---

## Multi-Device Behavior

- A single user account can have multiple active device tokens.
- The server notification engine must support sending notifications to all registered tokens for a user, or only to the active device.
- When an action is read on one device, clear the notification badge on all other devices if supported by the OS payload.

---

## Tenant / Account Scoping

- In multitenant SaaS contexts (see [saas-multitenant-pack](../../saas-multitenant-pack/README.md)), notifications must be partitioned by `tenant_id`.
- Tapping a notification for Tenant B while active on Tenant A must prompt the user to switch organizations before showing the target resource. Do not cross-contaminate data.

---

## Privacy and Content Safety

- **Payload Separation**: Do not include sensitive information (e.g., bank balances, transaction amounts, user passwords) in the text payload of the notification.
- **Lock Screen Safety**: Use generic titles on the lock screen (e.g., "New Invoice Received" instead of "Invoice #1234 for $5,000 from client XYZ"). Detail specific balances only *after* the user authenticates and opens the app.

---

## Out of Scope

- Setting up Apple Developer portals or Google Firebase credentials.
- Writing native Deno edge functions for APNs/FCM delivery (unless specified in migrations/architecture).

---

## Guardrails

- [ ] Push tokens must not be logged or exposed in cleartext.
- [ ] Notification lock-screen payloads must not contain private data (PII, balances, credentials).
- [ ] All deep-link routes must be validated server-side before showing content.
- [ ] Tapping a notification must never bypass the login or workspace switching gates.

---

## QA Checklist

- **Permission Timing**: Verified push request is not triggered at first launch; soft prompt displays first.
- **Logout Token Clean**: Verified token is deleted from backend database on logout.
- **Lock Screen Privacy**: Verified lock screen alert contains no private customer names or balances.
- **Deep Link Validation**: Tapped deep link while logged out; verified app redirects to login and then to the target screen.
- **Cross-Tenant Notification**: Tapped Tenant B alert while logged into Tenant A; verified app prompts workspace switch before displaying data.

---

## Related Core Files

- [05-user-flows.md](../../../core/docs/05-user-flows.md) — Notification tap flows.
- [09-api-design.md](../../../core/docs/09-api-design.md) — Token registration endpoints.
- [saas-multitenant-pack](../../saas-multitenant-pack/README.md) — Tenant switching bounds.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation of push notification guidelines | Antigravity |
