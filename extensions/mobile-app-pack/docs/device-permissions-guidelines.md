# 10A — Device Permissions Guidelines

> Establishes rules for requesting, handling, and gracefully recovering from native mobile device permission requests (camera, location, contacts, notification, etc.).

---

## Purpose

Provide a standard framework for device permission integration. This prevents app-store rejections due to aggressive permissions, limits security risks, and ensures that AI agents build user-friendly fallback experiences when permission is denied.

## Status

`Active` — Must be referenced by UX designers and frontend developers when integrating native hardware features.

---

## Device Permission Principles

1. **Principle of Least Privilege**: Request only the permissions absolutely necessary for the current task. Do not request permissions "just in case" or for future features.
2. **Contextual & Just-in-Time (JIT)**: Trigger permission prompts in response to a user action (e.g., tapping a camera icon to take a profile photo), rather than on the first app launch.
3. **Double-Prompt Strategy**: Use an in-app modal (soft prompt) explaining *why* the permission is needed before triggering the system-level (hard) OS prompt.
4. **No Artificial Gates**: Do not prevent the user from accessing the entire app if they deny a non-essential permission (e.g. denying camera access must not block viewing list dashboards).

---

## Permission Matrices

### Camera Permission
- **Purpose**: Upload profile picture, scan barcodes/QR codes, capture document receipts.
- **Timing**: Trigger only when the user taps the camera action button.

### Photo/Media Permission
- **Purpose**: Select existing photos from the device library.
- **Timing**: Use system photo pickers where possible (which do not require full photo library permission access).

### Location Permission
- **Purpose**: Geotagging check-ins, locating nearby warehouses, tracking delivery routes.
- **Timing**: Trigger only when initiating location-based operations. Always prefer "While Using App" over "Always Allow".

### Contacts Permission
- **Purpose**: Sharing referral links or inviting team members.
- **Timing**: Soft prompt explaining the benefit. *Requires explicit human owner approval before implementation.*

### Microphone Permission
- **Purpose**: Audio recordings, voice search.
- **Timing**: Active only during microphone recording flows.

### Bluetooth / NFC / Sensor Permissions
- **Purpose**: Local warehouse hardware scanning, physical check-ins.
- **Timing**: Trigger only during scan setups.

### File Storage Permission
- **Purpose**: Saving report PDFs or downloading invoice receipts (see [print-reporting-pack](../../print-reporting-pack/README.md)).
- **Timing**: On click of the "Save to Device" action.

---

## Permission Request Timing

- **First Launch Exclusion**: Do not request device location, camera, contacts, or storage permissions during the initial onboarding flow unless the app's core value proposition is entirely location-based (e.g., navigation).
- **Soft Prompt Requirement**: An in-app explanation screen must display first. If the user taps "Not Now", save this preference locally and do not trigger the OS prompt, preserving the ability to ask again later.

---

## Permission Denial Handling

- If the OS permission is denied:
  1. Record this state locally.
  2. Renders a clear fallback UI explaining that the feature is locked.
  3. Provide a direct link button to open the device settings page (`App Settings`) so the user can easily toggle the permission.
- **Never Loop**: Do not loop the permission request or spam the user with alert dialogues when they attempt to use the feature.

---

## Privacy Copy and Consent Notes

- The text strings explaining permission purposes in configuration files (e.g. `Info.plist` on iOS, `AndroidManifest.xml` on Android) must be clear, honest, and specific.
- AI agents must not write or invent legal or regulatory consent policy copy. All consent language and permission descriptions must be reviewed and approved by the human owner.

---

## Out of Scope

- Configuring native compiler permission strings in `Info.plist` or `AndroidManifest.xml` files.
- Implementing low-level platform code for permission checks (e.g., writing Swift/Kotlin native bridges).

---

## Guardrails

- [ ] Every permission request must have a documented fallback UX flow for when access is denied.
- [ ] No contacts, background location, or Bluetooth scanning permissions are allowed without explicit human owner approval.
- [ ] System permission dialogs must never be triggered on the app's first launch screen.
- [ ] Agents must flag any request that seeks access to sensitive user data (e.g., contacts, calendar) and request verification.

---

## QA Checklist

- **Soft Prompt Flow**: Verified that soft prompt displays before OS prompt and explaining the request.
- **Denied Fallback**: Denied OS permission; verified that fallback UI displays and provides a link to device settings.
- **Approved Access**: Approved permission; verified that camera/location operates immediately without app restart.
- **No Block Page**: Verified that denying location or camera does not lock the user out of other dashboard views.

---

## Related Core Files

- [04-user-roles.md](../../../core/docs/04-user-roles.md) — Permission matrices.
- [10-security-model.md](../../../core/docs/10-security-model.md) — Security boundaries.
- [print-reporting-pack](../../print-reporting-pack/README.md) — Exporting boundaries.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation of device permissions guidelines | Antigravity |
