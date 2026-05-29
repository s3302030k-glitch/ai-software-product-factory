# 12B — Financial Reporting QA Checklist

> Defines QA test checklists, regression strategies, edge-case matrices, and release readiness gates for business and financial logic products.

---

## Purpose

Provide QA engineers and automated validation agents with a rigorous checklist to confirm financial calculations are correct, audit logs are intact, reports reconcile to database transactions, and permissions prevent unauthorized modifications.

## Status

`Active` — Must be completed and signed off prior to tagging any production releases that modify financial features.

---

## Calculation QA Checklist

- [ ] **Formula Match**: Verify code calculations match the formulas documented in the specifications.
- [ ] **Rounding Mode**: Confirm calculations use the approved rounding method (e.g. Banker's rounding) across all environments.
- [ ] **Rounding Accumulation**: Run aggregate calculations over high-volume datasets to check for cumulative rounding deviations.
- [ ] **Boundary Tests**: Verify formulas handle:
  - Zero value inputs.
  - Negative values (returns, adjustments).
  - Extremely large numeric inputs (overflow prevention).

---

## Money/Currency QA Checklist

- [ ] **No Float Check**: Audit the database schema and variables to confirm zero binary floats (`float`/`double`) are used for money.
- [ ] **ISO Format validation**: Check that currency code inputs are validated against ISO 4217 lists.
- [ ] **Exchange Rate Source**: Confirm conversions pull from authorized sources and log timestamps.
- [ ] **Exchange Rate Decay**: Test converting currencies using stale exchange rates (verify the system rejects cached values older than the allowed tolerance).
- [ ] **Locale Display separation**: Verify currency symbols, decimal dividers, and thousands separators render correctly without changing stored values.

---

## Payment/Settlement QA Checklist

- [ ] **State Transition Audit**: Trace payments through `Pending` -> `Cleared` -> `Refunded` to verify states transition correctly.
- [ ] **Idempotency Gate**: Test sending identical charge payloads twice; verify the second call is blocked and does not charge the customer twice.
- [ ] **Balance Due Calculation**: Verify that partial payments reduce `Balance Due` correctly.
- [ ] **Credit Memo Allocation**: Confirm applying credit memos reduces invoice balances traceably.
- [ ] **Refund Restoration**: Verify refunding a payment restores the invoice balance correctly.

---

## Quantity/Unit QA Checklist

- [ ] **Base Unit Isolation**: Verify that quantities written to the database are converted and saved strictly in base units.
- [ ] **Display Unit Conversion**: Check that display toggles (e.g., kg to metric tonnes) show correct values.
- [ ] **Separate Quantity columns**: Confirm that contract quantity, expected quantity, measured quantity, and received quantity are saved in separate columns and not combined.
- [ ] **Adjustment logs**: Verify that quantity changes write adjustment records with actor, timestamp, and reason.

---

## Audit Trail QA Checklist

- [ ] **Append-Only verification**: Verify standard database roles cannot run `UPDATE` or `DELETE` statements on audit trail tables.
- [ ] **Diff log completeness**: Confirm that modifications to prices, quantities, and permissions log before/after values.
- [ ] **Actor capture**: Verify audit logs record the correct user ID (e.g., when administrative overrides are performed).
- [ ] **Override tracing**: Check that override logs capture actor, reason, timestamp, and the original calculation results.

---

## Report/Export QA Checklist

> [!IMPORTANT]
> **REPORT RECONCILIATION**: Financial reports must reconcile to the source transactions.
>
> - **Reconciliation Gate**: Verify report totals match the sum of historical transaction records.
> - **UI vs. Export Match**: Export totals (CSV, PDF, Excel) must match the totals displayed on the web interface dashboard.
> - **Tabular Formatting**: CSV exports under locales with comma decimal markers must use semicolon `;` separators to prevent layout breakage.

---

## Role/Permission QA Checklist

- [ ] **Bypass prevention**: Confirm frontend permission locks are backed by server-side checks.
- [ ] **Four-Eyes verification**: Verify a user cannot approve their own credit extensions or discount requests.
- [ ] **Service Role Key isolation**: Verify the Supabase service role key is not bundled in frontend JS files.

---

## Regression Checklist

Prior to release, re-test the following common financial regressions:
- Changing tax configurations must not recalculate historic posted invoices.
- System timezone shifts must not alter payment cleared dates.
- Refunding a payment must not delete the payment record.

---

## Bug Report Format

Use this format when filing financial or calculation bugs:

```markdown
# Financial Bug Report: [Short Title]

## 1. Context & Impact
- **Severity**: [CRITICAL / HIGH / MEDIUM]
- **Affected Module**: [e.g. Billing, Subscriptions, CSV Export]
- **Discrepancy Amount**: [e.g., Mismatch of $0.03, or $12,000.00]

## 2. Steps to Reproduce
1. [Step 1]
2. [Step 2]
3. [Step 3]

## 3. Expected vs. Actual Behavior
- **Expected Formula**: [Describe formula and expected values]
- **Expected Output**: [e.g., Total: $100.50]
- **Actual Output**: [e.g., Total: $100.47]

## 4. Trace Log / Database State
- **Invoice UUID**: [UUID]
- **Audit Log Entry ID**: [ID]
- **Before/After state JSON**:
```

---

## Release Readiness Checklist

- [ ] All calculation unit tests pass.
- [ ] Rounding accuracy is verified over a test dataset.
- [ ] Report reconciliation checks pass.
- [ ] No hard deletes are allowed for financial schema.
- [ ] The human owner has signed-off on the release.

---

## Related Core Files

- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — Core test structures.
- [13-release-checklist.md](../../../core/docs/13-release-checklist.md) — Release readiness patterns.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created financial logic and reporting QA checklist | Antigravity |
