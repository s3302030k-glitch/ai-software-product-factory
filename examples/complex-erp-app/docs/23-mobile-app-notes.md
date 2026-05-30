# 23 — Mobile App Notes

> How the Mobile App Pack may apply as future/optional scope for the Integrated Operations ERP.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> See the [example README](../README.md) for full context.
> Extension pack reference: [mobile-app-pack README](../../../extensions/mobile-app-pack/README.md)

---

## Pack Application Summary

The [Mobile App Pack](../../../extensions/mobile-app-pack/README.md) is documented here as **future/optional scope only**. No mobile app framework, no React Native, no Expo, no Flutter, and no PWA implementation exists in this documentation reference. Mobile warehouse flows are explicitly out of MVP scope (see [03-mvp-scope.md](03-mvp-scope.md) and [14-decision-log.md](14-decision-log.md) Decision 8).

> [!IMPORTANT]
> **No mobile implementation exists in this documentation reference.** This document records future scope notes only so that if mobile flows are added in a future version, the Mobile App Pack guidelines can be applied from the start.

---

## Warehouse Receiving Mobile Flow (Future Scope)

If mobile receiving is implemented in a future version:

- **Actor:** Inventory Clerk (mobile device in warehouse)
- **Flow concept:** Clerk opens a mobile app, selects an open PO, scans/enters SKU, enters quantity received, submits.
- **Mobile App Pack rules that would apply:**
  - Touch target size minimum (44×44pt) for all interactive elements.
  - Offline-capable receiving: clerk can record receipt even without network; submission queued and sent on reconnect.
  - Idempotency key on each receiving submission to prevent duplicate StockMovement records after reconnect.
  - Offline sync queue must be visible to the user (pending submissions shown with status).
- **Integration:** Receiving records sync to the same `ReceivingRecord` and `StockMovement` entities — no separate mobile data model.

---

## Stock Count Mobile Flow (Future Scope)

If mobile stock count is implemented in a future version:

- **Actor:** Inventory Clerk (mobile device in warehouse zone)
- **Flow concept:** Clerk selects a warehouse zone, enters counted quantities per SKU, submits count for review. Discrepancies trigger a StockAdjustment request.
- **Mobile App Pack rules that would apply:**
  - Large numeric input fields suitable for gloved hands.
  - Count session resumable: if app is interrupted, partially entered counts are saved locally.
  - Discrepancy flagging: items where counted quantity differs from current balance by more than a threshold are highlighted.
  - Count submission creates a StockAdjustment request — not a direct balance edit (maintains stock source-of-truth rule).

---

## Photo Attachment Permission Notes (Future Scope)

If photo attachments are added to receiving or adjustment records in a future version:

- **Camera permission:** Must be requested contextually (when user initiates photo capture) — not at app launch.
- **Denial fallback:** If camera permission is denied, the user can still complete the record without a photo. Photo is optional.
- **Storage:** Photos stored in a private storage bucket — not publicly accessible.
- **Mobile App Pack rule:** No photo that may capture PII (faces of warehouse staff) should be uploaded without explicit review of privacy policy applicability.
- **File size:** Images must be compressed client-side before upload to avoid large payload issues on mobile networks.

---

## Offline / Reconnect Risk (Future Scope)

If offline support is added in a future version:

- **Offline-first vs online-first:** Warehouse receiving and stock count are candidates for offline-first operation. Purchase requests and approvals should remain online-only (approval decisions must be server-confirmed).
- **Sync queue:** All offline mutations (receiving records, stock counts) are queued locally with idempotency keys. On reconnect, the queue is flushed in order.
- **Conflict resolution:** If the server rejects a queued item (e.g., PO was closed while offline), the item is flagged for manual resolution — not silently discarded.
- **Duplicate write prevention:** Each mutation carries a client-generated `idempotency_key`. The server rejects duplicate submissions with the same key.

> [!WARNING]
> Offline sync with approval-gated operations (purchase request approval, stock adjustment approval) is particularly risky. Approval decisions made offline could be based on stale state. The Mobile App Pack requires that approval actions always verify the current server state before committing.

---

## Push Notification Future Scope

If push notifications are added in a future version:

- **Approval notifications:** Notify approvers when a new request is pending in their inbox.
- **Stock alert notifications:** Notify Warehouse Manager when a SKU balance falls below a threshold.
- **Permission timing:** Push notification permission must be requested after the user has seen value from the app — not at first launch.
- **Lock-screen privacy:** Notification content must not expose financial amounts, supplier names, or customer names in the lock-screen preview. Only generic text (e.g., "You have a pending approval request").
- **Token lifecycle:** Push tokens must be refreshed on re-authentication and revoked on sign-out.

---

## No Mobile Implementation in This Example

To be absolutely clear:

- No mobile app source code exists in this documentation reference.
- No React Native, Expo, Flutter, Capacitor, or PWA setup exists.
- No app store accounts, app IDs, push notification tokens, or mobile build configs exist.
- No device permission handling code exists.
- The web application is responsive (tablet/desktop) but is not a mobile-native app.

All mobile flows described in this document are future planning notes only.

---

## Related Files

- [03-mvp-scope.md](03-mvp-scope.md) — Mobile explicitly in Future Scope section
- [08-architecture.md](08-architecture.md) — Mobile future concept in architecture
- [14-decision-log.md](14-decision-log.md) — Decision 8 (mobile is future/optional)
- [../../../extensions/mobile-app-pack/README.md](../../../extensions/mobile-app-pack/README.md) — Source pack
