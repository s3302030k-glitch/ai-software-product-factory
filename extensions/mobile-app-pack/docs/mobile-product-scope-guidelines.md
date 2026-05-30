# 03A — Mobile Product Scope Guidelines

> Establishes the platform scope, MVP boundaries, offline sync support rules, push notification strategies, and app store release boundaries for mobile-related features.

---

## Purpose

Define the functional and platform limits of mobile implementations. This document prevents mobile scope creep and ensures that AI agents construct mobile interfaces and services strictly within approved platform scopes, capability matrices, and business boundaries.

## Status

`Active` — Must be referenced by all product managers, business analysts, and architects before detailing mobile features.

---

## When to Use

Apply these guidelines when:
1. The product specification contains an app meant for mobile screens (iOS, Android, responsive web, or PWA).
2. Implementing native features (biometrics, location tracking, camera/photos, offline data storage).
3. Defining release channels, subscription/payment boundaries, or background jobs for mobile systems.

---

## Mobile Product Scope Principles

1. **Mobile is Not Desktop**: Do not assume every desktop web feature belongs in the mobile MVP. Mobile screens have limited space, variable network latency, and distinct input patterns.
2. **Platform Explicitly Defined**: Agents must verify target platforms (iOS, Android, responsive mobile web, tablet, or PWA) from approved project documents before proposing layout designs.
3. **No Unapproved Framework Selection**: AI agents must not choose native or hybrid mobile frameworks (e.g. React Native, Flutter, Swift, Kotlin, Expo) or app store release strategies without explicit, written human owner approval.
4. **Scoping Before Implementation**: All mobile-native access paths (e.g., location, push, camera) must be explicitly mapped in active product specs before code is written.

---

## Mobile-First vs. Responsive Web vs. Native App

- **Responsive Web**: A single web codebase that reflows for mobile screen widths. It does not access native hardware APIs easily and operates in standard browser sandboxes.
- **PWA (Progressive Web App)**: Web-based app cached on device, allowing home-screen launching, offline caching, and simple push notifications, but with limited native capabilities on iOS.
- **Native/Hybrid App**: App compiled for App Stores (iOS App Store, Google Play Store) using React Native, Flutter, Swift, or Kotlin. Provides full native API access (background execution, deep biometrics, secure storage) but requires store review cycles.

Agents must consult `docs/08-architecture.md` to confirm which of these approaches is approved.

---

## Platform Scope

Select and document which platform targets are approved in `docs/03-mvp-scope.md`:
- **iOS Phones**: Target versions (e.g., iOS 16+)
- **Android Phones**: Target API levels (e.g., API Level 33+)
- **Tablets (iPad/Android)**: Specifying if the app runs in compatibility mode or has responsive master-detail structures
- **PWA / Responsive Mobile Web**: Supported browsers (Safari, Chrome)

---

## MVP Mobile Feature Boundary

- Only "Must Haves" listed in [03-mvp-scope.md](../../../core/docs/03-mvp-scope.md) are allowed.
- Any mobile-specific layouts must be detailed in [06-pages-spec.md](../../../core/docs/06-pages-spec.md).
- Do not add features based on general mobile assumptions (e.g., adding contact sharing because it is a mobile app, unless explicitly specified in the batch request).

---

## Device Capability Assumptions

Document what hardware capabilities the app is assumed to have access to:
- **Network**: Assume offline/disconnected states can occur at any time (see [Offline Sync Guidelines](offline-sync-guidelines.md)).
- **Hardware**: GPS, camera, local secure enclave (biometrics), accelerometer, local sandbox storage.
- **Limits**: Low memory, battery-saving mode, physical screen size restrictions, app termination by the OS.

---

## Mobile Account, Auth, and Session Scope

- Mobile authentication must follow the security guidelines in [Mobile Security & Privacy Guidelines](mobile-security-privacy-guidelines.md) and [10-security-model.md](../../../core/docs/10-security-model.md).
- Detail token lifecycles (long-lived refresh tokens vs short-lived access tokens) and whether biometric log-in is approved.
- Tenant/account switching must reset cache context immediately when multitenant SaaS is involved (see [saas-multitenant-pack](../../saas-multitenant-pack/README.md)).

---

## Offline / Online Scope Boundary

- **Offline Support is Not Default**: Do not assume offline-first support is required unless explicitly approved in the project scope.
- If offline is approved, it must follow [Offline Sync Guidelines](offline-sync-guidelines.md).
- Define whether the app operates as:
  1. *Online Only* (shows error block if network is lost)
  2. *Online-First with Local Read Caching* (renders cached items but blocks mutations)
  3. *Offline-First with Write Queue* (mutations queued locally, synced on reconnect)

---

## Push & Deep Link Scope Boundary

- Push notifications require explicit permission schemas (see [Push Notification Guidelines](push-notification-guidelines.md)).
- Deep links must be explicitly mapped (e.g., `myapp://invoices/:id` or `https://app.myapp.com/invoices/:id`).
- All deep-linked parameters must be verified server-side.

---

## App-Store Release Scope Boundary

- Release boundaries, TestFlight distributions, and store review timelines must align with [App Store Release Guidelines](app-store-release-guidelines.md).
- Standard compliance with Apple Human Interface Guidelines (HIG) and Android Material Design is required.

---

## Business Rule Ownership

- All core business logic and validation rules must be defined in backend services or server configurations.
- Mobile client-side rules are for UX convenience only (immediate feedback) and must never override backend policies.
- Mobile-specific business rules (e.g., geographical check-in limits, biometric gates) require human owner approval.

---

## Out of Scope

- Native framework installations or build commands.
- Specific database schema setups for SQL/NoSQL on device (unless detailed in data model).
- Custom mobile app compiler setup, asset resizing scripts, or store distribution keys.

---

## Guardrails

- [ ] AI agents must not implement mobile code without target platform details defined in project docs.
- [ ] Agents must not decide the native framework or app-store release channel.
- [ ] Offline write capability must be explicitly authorized; do not assume "offline support" covers offline database writes by default.
- [ ] All mobile-specific business logic must be backed up by server-side validation.
- [ ] Any native feature request (e.g. location, camera) must list a fallback UX behavior for when the user denies permission.

---

## Related Core Files

- [01-product-brief.md](../../../core/docs/01-product-brief.md) — Product context and target users.
- [03-mvp-scope.md](../../../core/docs/03-mvp-scope.md) — Scope boundary checks (Moscow checklist).
- [08-architecture.md](../../../core/docs/08-architecture.md) — Overall tech stack selection.
- [15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Mandatory behavior constraints.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation of mobile product scope guidelines | Antigravity |
