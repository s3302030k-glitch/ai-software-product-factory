# 12A — Mobile QA Checklist

> Outlines the pre-release testing matrix, platform coverage, offline transitions, permission fallbacks, push alerts, deep links, security audits, and regression checklists for mobile applications.

---

## Purpose

Define the comprehensive testing protocols for mobile product validation. This checklist ensures that features are validated across different device layouts, network states, and system permission choices, preventing regressions or crashes on launch.

## Status

`Active` — Must be executed by QA engineers and validated by AI agents before tagging any release.

---

## Required Testing Environment

- Testing must occur on at least:
  1. **One Small Screen Device Profile** (e.g., iPhone SE or Android equivalent, width <= 375px).
  2. **One Large Screen Device Profile** (e.g., iPhone Pro Max or Android equivalent, width >= 428px).
- Verify support on tablet profiles (iPad or Android tablet) if tablet support is scoped.

---

## A. Mobile UX & Navigation QA Checklist

- [ ] **Touch Target Size**: All clickable actions are at least 44x44 pixels (iOS) or 48x48 dp (Android).
- [ ] **Back Navigation**: Tapping the back button or swiping back pops the screen stack correctly without blank displays.
- [ ] **App Resume Behavior**: Closing the app to the background and resuming preserves the user's active page context.
- [ ] **Virtual Keyboard Ingress**: Focus on inputs; verify the keyboard does not overlap, block, or hide the active field.
- [ ] **Unsaved Work Gate**: Modifying a form and swiping back triggers a confirmation alert before discarding changes.
- [ ] **States Verification**: Confirmed loading skeletons, empty lists, and error states display correctly.

---

## B. Platform & Device QA Checklist

- [ ] **iOS Layout**: Navigation headers, safe area margins (notch, home indicator) align properly on iOS devices.
- [ ] **Android Layout**: Custom hardware back-button presses pop stacks and sheets; system status bars do not overlap header text.
- [ ] **PWA Launcher**: Installing as a home screen app launches in standalone mode, hiding the browser URL bar.

---

## C. Offline & Sync QA Checklist (If Offline is in Scope)

- [ ] **Disconnect Check**: Turn off wifi/cellular during a data mutation; verify data is queued locally and displays "Pending Sync".
- [ ] **Reconnect Sync**: Turn on cellular/wifi; verify queued changes are transmitted in FIFO order.
- [ ] **Idempotency Check**: Verify that interrupted connection sync retries do not create duplicate database records.
- [ ] **Conflict Resolution Alert**: Modify a record on the web and the same record offline on mobile; verify the resolution policy merges changes correctly.
- [ ] **Tenant Scoping on Sync**: Verify updates queued for Tenant A are not synced under Tenant B context if the user switches workspace.

---

## D. Device Permission QA Checklist

- [ ] **First Launch Check**: Verify no hardware permission prompts trigger immediately on the first onboarding screen.
- [ ] **Soft Prompt Verification**: Verify that the soft prompt explanation displays before triggering the system OS dialog.
- [ ] **Denied Fallback UX**: Deny permission (camera/location); verify that the fallback UI is rendered and contains a settings link.
- [ ] **Graceful Recovery**: Approve permission via device settings after denial; confirm the app resumes and enables the feature instantly.

---

## E. Push Notification QA Checklist

- [ ] **Token Registration**: Log in; verify FCM/APNs token is registered correctly in the backend database.
- [ ] **Token Cleanup**: Log out; verify the token is deleted from the backend server database.
- [ ] **Lock Screen Privacy**: Send an alert; verify that lock-screen titles and texts do not leak sensitive PII or amounts.
- [ ] **Preference Scoping**: Verify toggling transactional or marketing preferences respects user selections and server databases.

---

## F. Deep Link QA Checklist

- [ ] **Logged-out Deep Link**: Click a link while logged out; verify the app redirects to the login screen and then forwards to the target link screen.
- [ ] **Bypass Auth Check**: Verify that deep links targeting protected screens (e.g. `myapp://settings`) do not bypass authorization policies.
- [ ] **Cross-Tenant Guard**: Click a link for Tenant B while logged into Tenant A; verify the app prompts a workspace switch.

---

## G. Mobile Security & Privacy QA Checklist

- [ ] **Secure Storage Check**: Verify credentials/tokens are stored in Key Vaults/Keychains and not cleartext localStorage.
- [ ] **Logging Audit**: Run the app in production configuration; inspect device console log outputs and confirm no tokens, headers, or PII are printed.
- [ ] **Crash Scrubbing**: Inspect crash logs; verify that authorization headers and payment card details are filtered.

---

## H. RTL / i18n Mobile QA Checklist (If Product Supports RTL)

- [ ] **Mirror Check**: Verify that when RTL is enabled, the layout mirrors completely (tab bar, back arrows, list flows).
- [ ] **Cursive Font Padding**: Confirm Arabic/Persian/Hebrew characters are fully readable with no line overlaps or truncated descenders.
- [ ] **Logical CSS Verification**: Inspect CSS; verify logical properties (`margin-inline-start`) are used instead of physical coordinates.

---

## I. Performance & Crash QA Checklist

- [ ] **Memory Monitoring**: App does not crash after multiple photo capture runs or offline sync loops.
- [ ] **Battery/Background Drain**: Verify background fetch loops do not drain the device battery or cause OS termination.
- [ ] **API Latency Handling**: Verify app remains responsive on slow 3G networks.

---

## Bug Report Format

When logging a mobile-specific bug, the reporter must format the report as follows:
```markdown
### Mobile Bug Report: [Title]
- **Device / Model**: [e.g., iPhone 13]
- **OS Version**: [e.g., iOS 17.2]
- **App Version & Build**: [e.g., v1.0.0 (Build 104)]
- **Network State**: [e.g., WiFi, 3G, Offline]
- **Steps to Reproduce**:
  1. [Step 1]
  2. [Step 2]
- **Expected Behavior**: [Expected outcome]
- **Actual Behavior**: [Error, visual overlap, crash]
- **Logs / Screenshots**: [Scrubbed screenshots / console traces]
```

---

## Release Readiness Checklist

Prior to final store build, verify:
- [ ] 100% of "Must Have" features have passed manual device QA.
- [ ] Crash-free session rate is above 99.9%.
- [ ] Reviewer credentials are verified.
- [ ] Human owner has approved release.

---

## Related Core Files

- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — Core testing specs.
- [rtl-qa-checklist.md](../../rtl-i18n-pack/docs/rtl-qa-checklist.md) — Localization QA steps.
- [15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Validation criteria.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation of mobile QA checklist | Antigravity |
