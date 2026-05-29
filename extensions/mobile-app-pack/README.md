# Extension Pack: Mobile App

> Adds mobile-first design patterns, native feature integration, offline support, and app store deployment documentation.

---

## When to Use This Pack

Use this extension pack when your product:

- Is a **native mobile app** (iOS, Android) or **hybrid app** (React Native, Flutter, Capacitor)
- Requires a **progressive web app (PWA)** with mobile-native features
- Needs **offline support** (works without network, syncs when online)
- Uses **device features** (camera, GPS, push notifications, biometrics)
- Will be distributed through **app stores** (Apple App Store, Google Play)

---

## What This Pack Will Add (When Built)

### Additional Documents

| Document | Purpose |
|----------|---------|
| `mobile-architecture.md` | Mobile framework choice, native vs. hybrid, code sharing strategy |
| `mobile-ux-guidelines.md` | Touch targets, gestures, navigation patterns, platform conventions |
| `offline-sync-spec.md` | Offline data storage, conflict resolution, sync strategy |
| `push-notification-spec.md` | Notification types, channels, permissions, payload format |
| `device-features-spec.md` | Camera, GPS, biometrics, file system access |
| `app-store-checklist.md` | Submission requirements, review guidelines, metadata |

### Additional Prompts

| Prompt | Purpose |
|--------|---------|
| `mobile-engineer-prompt.md` | AI agent role for implementing mobile-specific features |

### Additional Guardrails

- Touch targets must be at least 44x44 points (iOS) / 48x48 dp (Android)
- Offline functionality must handle conflict resolution gracefully
- Push notifications must have user opt-in and respect platform guidelines
- Mobile-specific permissions must follow the principle of least privilege
- App must handle interruptions (phone calls, multitasking, low memory)
- Platform-specific conventions must be followed (iOS HIG, Material Design)

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| App store rejection | Submission checklist with common rejection reasons |
| Poor mobile UX | Platform-specific UX guidelines |
| Data loss when offline | Offline sync spec with conflict resolution |
| Battery drain from background activity | Background processing guidelines |
| Permission request fatigue | Just-in-time permission strategy |

---

## Example Project Types

- Consumer mobile apps (social, fitness, finance)
- Field service applications (technicians, delivery)
- Mobile point-of-sale systems
- Healthcare patient apps
- Mobile inventory management
- Location-based services

---

## Status

> **Status: Placeholder / Planned Future Pack**
>
> This extension pack is currently a **placeholder**. The folder contains only this README. Full templates, prompts, and instructions will be added in a future version.
>
> **Core Governance Rule:** Extension packs are optional and exist to **supplement** core documents for specific product needs — they do **not** replace core documents.
>
> For workspace setup instructions and core rules, link back to [START_HERE.md](../../START_HERE.md).
