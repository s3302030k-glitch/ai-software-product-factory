# 10C — Mobile Security & Privacy Guidelines

> Defines rules for token storage, biometric authentication gates, logging/crash-reporting privacy, deep link security, and session revocation.

---

## Purpose

Establish the security and privacy rules for mobile client applications. This document prevents unauthorized access to user accounts, leaking of customer secrets or payment details in logs, and data contamination across multi-tenant workspaces.

## Status

`Active` — Must be followed by all security engineers, architects, developers, and QA leads.

---

## Mobile Security Principles

1. **No Insecure Local Storage**: Authenticated access tokens, refresh tokens, and passwords must never be stored in insecure local storage (such as HTML5 localStorage, unencrypted shared preferences, or plain text files).
2. **Crash & Log Privacy**: Client-side application logs, error reports, and crash dumps must be scrubbed of credentials, tokens, payment card details, and customer messages.
3. **No Auth Bypass in Deep Links**: Deep links must not act as a bypass for authorization. Tapping a URL must trigger authentication checks before routing.
4. **Instant Cache Purging**: Changing workspaces or logging out must immediately purge local active memory caches.

---

## Mobile Auth and Session Rules

- **Refresh Token Lifecycle**: Refresh tokens stored on mobile devices must have a bounded lifespan. Ensure the server database allows remote invalidation of refresh tokens.
- **Session Duration**: Mobile sessions are typically longer than desktop web sessions. Implement periodic background checks to confirm that the account has not been deactivated or suspended on the server.

---

## Token Storage Rules

- **Enclave/Keychain Storage**: Tokens must be saved in secure, hardware-backed keychains or key vaults:
  - **iOS**: Apple Keychain Services.
  - **Android**: Android Keystore system.
- If using cross-platform wrappers (e.g., Capacitor, React Native), use secure storage libraries that bind directly to these native platform APIs.

---

## Biometric Auth Notes

- **UX Layer Only**: Biometric authentication (FaceID, TouchID, Android Biometric Prompt) is a client-side UX convenience gate. It must never act as a replacement for server-side cryptography or token exchange.
- **Biometric Keys**: Biometrics must unlock access to a locally stored cryptographic key, which then retrieves the session token from the Keychain.
- **Biometric Toggle**: Toggling biometrics must require the user to verify their primary account password.

---

## Device Loss and Session Revocation

- Provide an option in the web and mobile settings to "Log Out of All Devices" or "Revoke Device Session".
- Tapping this option must immediately:
  1. Revoke the specific device token on the server.
  2. Cause the mobile app to fail the next API verification check.
  3. Prompt the user to log in again and clear the local storage.

---

## Secure Local Storage

- Local SQLite caches or JSON caches must use disk-level encryption (e.g. SQLCipher) if they store any data classified as private (PII, customer names, address records).
- Encryption keys must be generated dynamically on device startup and protected behind the OS keychain.

---

## Logging and Crash Reporting Privacy

- **Logging Guards**: Ensure that debug logging statements are compiled out of release builds.
- **Crash Reports Scrubbing**: Third-party crash trackers (e.g., Sentry, Firebase Crashlytics) must use interceptors to filter out:
  - `Authorization` headers.
  - Credit card numbers, expiration dates, CVVs (see [financial-business-logic-pack](../../financial-business-logic-pack/README.md)).
  - Passwords and auth tokens.
  - Personal identifiable information (PII) like email addresses, phone numbers, or physical addresses.

---

## Deep Link Security

- Deep links can launch actions (e.g. `myapp://invoices/:id/pay`).
- **No Direct Action Execution**: A deep link must never execute a state mutation (like processing a payment or deleting a record) directly without explicit, authenticated user approval and confirmation click within the app.
- **Domain Verification**: Use Universal Links (iOS) and App Links (Android) to verify that only official domains can launch the app.

---

## API Security

- **SSL Pinning**: Consider SSL pinning for API endpoints to prevent man-in-the-middle (MITM) attacks in high-security environments.
- **Device Attestation**: In high-risk financial apps, implement Google Play Integrity or Apple DeviceCheck to verify the app is running on a secure, non-rooted device.

---

## Tenant / Account Switching Security

- In SaaS multitenant environments (see [saas-multitenant-pack](../../saas-multitenant-pack/README.md)):
  - Switching tenants must completely reset all active caches, context states, and memory stores.
  - A user must never be able to access Tenant B API endpoints using an active session token scoped exclusively to Tenant A.

---

## Payment-Related Mobile Boundary Notes

- All payments processed inside mobile apps must align with [financial-business-logic-pack](../../financial-business-logic-pack/README.md) and [ecommerce-pack](../../ecommerce-pack/README.md).
- Mobile apps must use secure WebViews, hosted input fields, or official SDKs (e.g., Stripe SDK) to handle card numbers.
- **No Local Card Storage**: Do not save credit card numbers, CVVs, or cardholder names in device databases.

---

## Out of Scope

- Configuring SSL pinning certificates or third-party SDK dependencies on developer systems.
- Proposing native encryption algorithm code (e.g., configuring AES GCM in Swift).

---

## Guardrails

- [ ] Cleartext local storage of session tokens or refresh tokens is strictly prohibited.
- [ ] Debug logs must not render credentials or private client data in production builds.
- [ ] Universal links must not bypass authorization checks.
- [ ] Tenant switching must completely wipe active client caches and scopes.
- [ ] Payment-related inputs must be routed through secure provider SDKs; no local card details are to be stored on the device.

---

## QA Checklist

- **Token Storage Audit**: Verified that session tokens are stored in the secure enclave / Keychain and not in cleartext local storage.
- **Logging Scrub Check**: Inspected application log output; verified that no authorization headers, tokens, or PII are printed.
- **Deep Link Auth**: Attempted to open a protected deep link while logged out; verified that the app requires authentication before displaying content.
- **Tenant Wipe**: Switched tenants; verified that the active memory cache for the old tenant is completely purged and unreadable.

---

## Related Core Files

- [10-security-model.md](../../../core/docs/10-security-model.md) — Main security architecture.
- [saas-multitenant-pack](../../saas-multitenant-pack/README.md) — SaaS multi-tenant controls.
- [financial-business-logic-pack](../../financial-business-logic-pack/README.md) — Financial data boundaries.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation of mobile security and privacy guidelines | Antigravity |
