# 07A — Offline Sync Guidelines

> Outlines local storage caching, mutation queues, conflict resolution rules, backoff schedules, and security boundaries for disconnected mobile device operations.

---

## Purpose

Establish clear patterns for managing local data structures when a mobile device is offline. This document prevents data corruption, duplicate database writes, session leaks, and incorrect tenant access during network reconnect phases.

## Status

`Active` — Must be followed by all software architects, database engineers, and security reviewers when offline capabilities are scoped.

---

## Offline/Sync Principles

1. **Explicit Owner Authorization**: Offline data mutations (write capabilities) must be explicitly authorized by the human product owner; do not assume "offline support" covers write queues by default.
2. **Idempotency is Mandatory**: All queued writes must include client-generated UUIDs/idempotency keys to prevent duplicate records upon network reconnection.
3. **Scoped Tenant Isolation**: Every queued offline mutation must serialize and carry the active tenant/organization context to prevent data leaks across workspaces (essential when integrated with `saas-multitenant-pack`).
4. **Secure Local Storage Boundaries**: Sensitive keys, session tokens, or personal identifiers must not be cached locally without cryptographic encryption.

---

## Offline-First vs. Online-First with Cache

Before designing, check the project's approved sync model:
- **Online-First with Read Cache**: The app expects a network connection to make changes. It caches data for read-only offline viewing. If a network request fails, mutations are blocked and error alerts are shown.
- **Offline-First with Write Queue**: The app operates fully offline. It records mutations in a local database queue and resolves them in the background when the connection is restored.

---

## Local Cache Model

- Cache structure must mirror the entities defined in [07-data-model.md](../../../core/docs/07-data-model.md).
- Use local SQLite, Deno KV, or indexed collections depending on the mobile shell (as specified in `docs/08-architecture.md`).
- Define cache expiry limits (e.g. discard cached records older than 24 hours) to prevent reading stale, out-of-date state.

---

## Sync Queue Model

When mutation is queued:
1. **Serialization**: Convert the payload into a structured JSON string containing:
   - `id`: Unique client transaction UUID.
   - `tenant_id`: Active tenant identifier.
   - `user_id`: Authenticated user identifier.
   - `action`: E.g., `CREATE_INVOICE`, `UPDATE_CLIENT`.
   - `payload`: Transaction values.
   - `timestamp`: Epoch when the user executed the change.
2. **First-In, First-Out (FIFO)**: Process queue items in chronological order.
3. **Execution Gate**: Queue processing must halt immediately if a dependency item fails, avoiding out-of-order schema errors.

---

## Conflict Resolution

Establish and document the conflict resolution policy approved by the owner:
- **Client Wins (Last Write Wins)**: The server blindly accepts the client payload, overwriting server state.
- **Server Wins**: The server rejects the client change if the record was modified by another source since the client last fetched it.
- **Merge/Resolution Rules**: Flag conflicts for manual review by the user.

*Agents must request the conflict resolution rule from the owner and not invent custom merge policies.*

---

## Duplicate Write Prevention

- Every write payload must include an `idempotency_key` (UUID v4) generated at the moment of the user transaction.
- The server API must log processed idempotency keys and return cached responses for duplicate transactions (see [09-api-design.md](../../../core/docs/09-api-design.md)).

---

## Retry and Backoff Behavior

- When a sync request fails due to temporary errors (e.g., HTTP 503, connection timeout):
  1. Retry using **Exponential Backoff** with jitter (e.g., try after 2s, 4s, 8s, 16s up to a max wait of 60s).
  2. Limit total consecutive retries before pausing and alerting the user.
- Do not retry client validation errors (HTTP 400, 422, 403); flag these items as "needs repair" and halt queue processing.

---

## Reconnect Behavior

- Monitor network connectivity using system event listeners.
- On reconnect:
  1. Validate the active authentication token.
  2. Verify that the user's tenant membership is still active.
  3. Resume the sync queue processing in the background.
  4. Perform a read-only fetch to pull the latest server updates.

---

## Data Freshness Indicators

- The UI must display clear indicators to let users know the state of their data:
  - `Synced`: Data matches the server.
  - `Offline / Pending Sync`: (E.g., "5 updates waiting to sync").
  - `Sync Error`: Highlighted items that failed to process.
  - `Last Synced`: Time of the last successful background fetch.

---

## Offline Permissions and Tenant/Account Scoping

- In multitenant SaaS contexts (see [saas-multitenant-pack](../../saas-multitenant-pack/README.md)), local database tables must enforce tenant isolation by partitioning caches by `tenant_id`.
- Tapping "Switch Tenant" must immediately:
  1. Pause any running sync queues for the old tenant.
  2. Flush the UI cache to prevent visual data bleed.
  3. Load the new tenant cache.
- Tapping "Log Out" must completely delete all local tenant caches and active write queues.

---

## Sensitive Local Storage Notes

- **Never cache raw payment details**, credit card numbers, CVVs, passwords, or PII keys in cleartext.
- Tokens (Refresh/Access) must live in device-encrypted storage (e.g. Keychain on iOS, Keystore on Android).
- Local DB files must use encryption (e.g. SQLCipher) if stored data is classified as sensitive by [10-security-model.md](../../../core/docs/10-security-model.md).

---

## Out of Scope

- Implementing third-party sync libraries (e.g., WatermelonDB, RxDB, Supabase offline-first libraries).
- Configuring native database file systems or compilation flags.

---

## Guardrails

- [ ] Write queue actions must fail-safe: if the user logs out, all un-synced data must be explicitly handled (either warned or securely cleared; never left in cleartext).
- [ ] No background sync processing is allowed if the authentication token has expired.
- [ ] Every queued write must carry a client-generated UUID for idempotency.
- [ ] All synchronization events must be logged to a local security audit trail.

---

## QA Checklist

- **Disconnect Flow**: Disconnected network during a transaction; verified that action is queued and offline indicator displays.
- **Reconnect Sync**: Restored network; verified queue processes in order and indicator updates to "Synced".
- **Idempotency Check**: Simulated double-send of sync request; verified server only creates a single database entry.
- **Tenant Isolation**: Logged in as Tenant A, queued updates, switched to Tenant B; verified Tenant A's updates do not execute under Tenant B's context.

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Data entity schema boundaries.
- [09-api-design.md](../../../core/docs/09-api-design.md) — Server endpoints and idempotency rules.
- [saas-multitenant-pack](../../saas-multitenant-pack/README.md) — Multitenancy details.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation of offline sync guidelines | Antigravity |
