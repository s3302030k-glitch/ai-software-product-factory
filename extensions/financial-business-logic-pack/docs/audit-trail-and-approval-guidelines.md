# 10A — Audit Trail and Approval Guidelines

> Defines guidelines for tracking changes to financial records, logging actor actions, structuring approval workflows, and preserving historical integrity.

---

## Purpose

Ensure compliance with auditing requirements (e.g., SOC 2, SOX) and secure the application against data tampering, unauthorised overrides, or accidental deletion of financial records.

## Status

`Active` — Must be followed by backend engineers, database developers, and security analysts when configuring audit systems or workflows.

---

## Audit Trail Principles

- **Immutability**: Audit logs must be append-only. Once written, a log record must be unalterable.
- **Traceability**: Every write operation to financial data must be linkable to a specific human user or system process.
- **Completeness**: Logs must contain enough detail to reconstruct the exact state change.

---

## What Must Be Audited

All additions, edits, or status changes to:
- Prices, pricing sheets, tax configurations.
- Subscriptions, payment terms, discounts.
- Quantities, expected measurements, delivered items.
- Invoices, credit notes, invoice adjustments.
- Payments, settlements, bank transfers, allocations.
- User permission levels and role assignments.

---

## Actor/Timestamp/Reason Pattern

Every audit log record must contain:
1. **Actor ID**: The unique identifier of the user (or system agent) executing the change.
2. **Timestamp**: High-precision UTC timestamp (e.g. Postgres `TIMESTAMPTZ` set to `now()`).
3. **Reason**: A text field explaining why the change was made (mandatory for sensitive manual adjustments).
4. **Action type**: `CREATE`, `UPDATE`, `VOID`, `OVERRIDE`.

---

## Before/After Value Pattern (Diffs)

For any modifications (`UPDATE`/`OVERRIDE` actions), the log must record:
- **Before State**: The previous values of modified fields.
- **After State**: The new values of modified fields.
- **Format**: Structured format (e.g., JSON diffs) is recommended for ease of comparison and automated auditing.

---

## Approval Workflow Principles

- **Separation of Duties**: The user proposing a sensitive change (e.g., raising a credit limit or discounting an invoice by 50%) should not be the user approving it (e.g., Dual Control / Four-Eyes Principle).
- **Explicit Transitions**: Approval states must be tracked explicitly (e.g., `Submitted_for_Approval`, `Approved`, `Rejected`).
- **Pre-lock**: The object must be locked against execution or billing while in the pending approval state.

---

## Sensitive Change Categories

The following events require manual approval workflows before taking effect:
- Invoice discounts exceeding a defined percentage (e.g. >10%).
- Manual overrides of calculated tax rates.
- Refunds exceeding specific cash thresholds.
- Retroactive pricing changes.
- Increasing client credit terms.

---

## Financial Record Edit Rules

- Once an invoice is `Posted` or a payment is `Cleared`, its amounts, currencies, and line items must not be edited.
- Corrective modifications must be applied via credits/debits, reversals, or adjustments, which are then audited like standard transactions.

---

## Soft Delete vs Hard Delete

> [!CAUTION]
> **NO HARD DELETIONS**: Financially meaningful records (invoices, payments, transactions, audit logs) must never be permanently deleted (`DELETE` statements) from the database, unless explicitly approved by the owner under strict legal compliance rules.
>
> - Use soft-deletion (`is_deleted` flags or `deleted_at` timestamps) for draft records.
> - For posted records, use standard state cancellation or voiding routines.
> - Preserving history is critical to maintain audit balances.

---

## Admin Override Rules

- Administrators must not have the ability to bypass the audit logging system.
- Database service roles (e.g., the bypass key) must still log actions through database-level triggers or centralized API endpoints.
- All admin overrides require a documented reason.

---

## Out of Scope

- Integrating with SIEM security products (e.g., Splunk, Datadog security monitoring).
- Specific data archiving or cold-storage strategies.

---

## Guardrails

- [ ] **APPEND-ONLY LOGS**: Ensure audit table schema is restricted to `INSERT` only (no `UPDATE` or `DELETE` permissions allowed for standard users).
- [ ] **NO HARD DELETE**: Do not implement `DELETE` database operations for posted financial models.
- [ ] **MANDATORY DIFF**: Ensure all edits to posted records write the exact before/after field changes.
- [ ] **LOCKED APPROVALS**: Ensure sensitive workflows cannot be finalized without approval records.

---

## QA Checklist

- [ ] Verify that audit logs cannot be updated or deleted by standard API users.
- [ ] Test editing a record: verify the audit table receives an entry with actor, timestamp, reason, and before/after values.
- [ ] Validate that attempting to hard-delete an invoice returns an API error or is blocked at the database level.
- [ ] Test the four-eyes approval: verify that a standard user cannot approve their own discount requests.

---

## Related Core Files

- [10-security-model.md](../../../core/docs/10-security-model.md) — Core security model definitions.
- [15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Security and audit constraints.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created audit trail and approval workflows guidelines | Antigravity |
