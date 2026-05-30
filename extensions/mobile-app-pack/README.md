# Extension Pack: Mobile App

> Adds guidelines, checklists, and role prompts for mobile-first design patterns, native device permission handling, offline usage and synchronization, push notifications, deep links, mobile-specific security, and app store deployment.

This is a fully implemented extension pack, supplementing the core factory templates.

---

## Purpose and Scope

This pack **supplements** the core factory documents; it does **not** replace them. It is designed for software products that include mobile apps (iOS, Android, PWA, hybrid, or native), responsive mobile-first experiences, native device permissions, offline usage, sync behavior, push notifications, deep links, app store release readiness, mobile auth, mobile privacy, mobile QA, or device-specific UX.

It is useful for building mobile-first SaaS apps, customer portals, ecommerce apps, field operations apps, warehouse/ERP mobile flows, notification-heavy products, and offline-capable applications.

This pack is strictly template-based and generic. It does not include product-specific or private business information. It does not include real customer data, device data, push tokens, app IDs, project IDs, store credentials, payment data, bank data, tax IDs, or company-specific mobile records.

> [!WARNING]
> **NO LEGAL, PRIVACY COMPLIANCE, APP-STORE LEGAL, PAYMENT COMPLIANCE, MEDICAL-DEVICE, SAFETY-CRITICAL, OR REGULATED MOBILE ADVICE**: This extension pack does not provide legal, privacy compliance, app-store legal, payment compliance (PCI-DSS), medical-device, safety-critical, or regulated mobile advice. All templates, guidelines, and prompts must be audited and approved by the product owner's security, legal, financial, and operational professionals before deployment.

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| **Mobile Scope Creep** | Establishes explicit boundaries for MVP features, platform targets, and device capability assumptions. |
| **Desktop-First UX Copied into Mobile** | Enforces touch targets, gestures, mobile form design, and mobile navigation layouts. |
| **Broken Navigation / Back Behavior** | Sets guidelines for back buttons, physical gestures, modal sheets, and stack flow safety. |
| **Unsafe Offline Sync** | Distinguishes offline-first vs. online-first with caching, and defines sync queue boundaries. |
| **Duplicate Writes after Reconnect** | Imposes client-side idempotency keys and request deduplication gates. |
| **Device Permission Overreach** | Mandates contextual, just-in-time requests and proper fallback UX when permission is denied. |
| **Push Notification Abuse** | Establishes notification preference categories, transactional vs. marketing rules, and quiet hours. |
| **Deep Link Security Problems** | Enforces server-side validation of routing paths and prevents auth-bypass via URLs. |
| **Weak Mobile Auth/Session Handling** | Mandates secure storage of tokens, biometric boundaries, and remote session revocation rules. |
| **Privacy Leakage through Logs/Notifications** | Restricts PII, secrets, or financial details in client logs, crash dumps, and lock-screen alerts. |
| **App-Store Readiness Gaps** | Standardizes submission checklists, build versioning, privacy labels, and phased rollouts. |
| **Mobile QA Coverage Gaps** | Provides a comprehensive QA checklist for mobile devices, networks, states, and platform-specific behaviors. |

---

## Pack Components

### Documentation Guidelines (`docs/`)

- [Mobile Product Scope Guidelines](docs/mobile-product-scope-guidelines.md) — Platform scope, mobile-first vs. responsive, MVP boundaries, offline boundaries, and owner approval rules.
- [Mobile UX & Navigation Guidelines](docs/mobile-ux-navigation-guidelines.md) — Gestures, touch targets, back button handling, modal sheets, keyboard handling, empty states, and RTL/i18n layout rules.
- [Offline Sync Guidelines](docs/offline-sync-guidelines.md) — Local cache, sync queues, conflict resolution, retry/backoff, reconnect behavior, and SaaS tenant scoping.
- [Device Permissions Guidelines](docs/device-permissions-guidelines.md) — Permission request timing, denial flows, camera/location/contacts access, and privacy copy rules.
- [Push Notification Guidelines](docs/push-notification-guidelines.md) — Permission timing, category preferences, transactional vs. marketing rules, deep links, token lifecycle, and lock-screen privacy.
- [Mobile Security & Privacy Guidelines](docs/mobile-security-privacy-guidelines.md) — Secure token storage, biometrics, session revocation, logging privacy, deep link validation, API security, and tenant switching.
- [App Store Release Guidelines](docs/app-store-release-guidelines.md) — Store metadata checklist, versioning, privacy labels, TestFlight, phased rollouts, crash monitoring, and rollback strategies.
- [Mobile QA Checklist](docs/mobile-qa-checklist.md) — Comprehensive QA checklist covering layouts, platforms, offline transitions, permission states, push notifications, security, and RTL/i18n.

### AI Agent Role Prompts (`prompts/`)

- [Mobile Product Architect](prompts/mobile-product-architect-prompt.md) — Role prompt to review or design mobile scope, platforms, navigation, offline sync, permissions, and release readiness.
- [Mobile UX Review Agent](prompts/mobile-ux-review-agent-prompt.md) — Role prompt to audit layouts, gestures, touch targets, keyboard handling, RTL layouts, and accessibility.
- [Offline Sync Review Agent](prompts/offline-sync-review-agent-prompt.md) — Role prompt to audit local caching, write queues, conflict resolution, and SaaS multitenant context.
- [Mobile Security Review Agent](prompts/mobile-security-review-agent-prompt.md) — Role prompt to audit auth tokens, biometrics, logging privacy, deep link security, and payment boundaries.
- [Mobile QA Agent](prompts/mobile-qa-agent-prompt.md) — Role prompt to run QA checklists, test device/platform coverage, and audit regression scenarios.

---

## Recommended Usage

Follow these steps to integrate this extension pack into your product project:

1. **Initialize Core Kit First**: Copy the core factory documents (`core/docs/`) and prompt templates (`core/prompts/`) into your product project workspace before applying this pack.
2. **Apply Mobile App Pack Selectively**: Copy this pack's folders (`docs/` and `prompts/`) into your project *only* if mobile app behavior is part of the product.
3. **Add Relevant Mobile Docs to Product Docs**: Merge the relevant mobile guidelines from `docs/` into your active product documentation.
4. **Use Mobile Prompts Before Implementation and During Review/QA**: Assign specialized prompts to your AI agents to guide mobile architecture design, UX audits, security reviews, and QA validations before code merges.
5. **Enforce Governance Gatekeeping**: Never approve offline sync models, push notifications, device permissions, mobile auth, privacy-sensitive flows, app-store release, or payment-related mobile flows without explicit human owner approval.

For workspace setup instructions and core governance rules, see [START_HERE.md](../../START_HERE.md).

---

## Related Extension Packs

This mobile app pack is designed to complement the following sibling packs when applicable:

- [rtl-i18n-pack](../rtl-i18n-pack/README.md) — For right-to-left UI layouts, translation keys, and locale formatting considerations on mobile screens.
- [supabase-pack](../supabase-pack/README.md) — For mobile authentication, offline database mapping, remote storage bucket policies, and Deno edge functions.
- [saas-multitenant-pack](../saas-multitenant-pack/README.md) — For tenant/account switching, workspace scopes, and team membership synchronization on mobile devices.
- [ecommerce-pack](../ecommerce-pack/README.md) — For mobile shopping carts, checkout flows, guest profiles, order tracking, and inventory reservation.
- [print-reporting-pack](../print-reporting-pack/README.md) — For export/share flows, PDF print view boundaries, and device-native share sheets.
- [financial-business-logic-pack](../financial-business-logic-pack/README.md) — For money rendering, currency selectors, payments, refunds, and transactional audit trails on mobile.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Pack implemented: README, 8 docs, 5 prompts created | Antigravity |
