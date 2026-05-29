# 07F — Calculation and Rounding Guidelines

> Defines rules for business calculation ownership, mathematical formulas documentation, rounding strategies, decimal precision, and manual override audit logs.

---

## Purpose

Prevent discrepancies in billing, interest accruals, discounts, and reporting. This document ensures all business calculations are mathematically consistent, documented, auditable, and trace-compliant.

## Status

`Active` — Must be followed by software architects, developers, and QA agents when designing or reviewing calculation code.

---

## Calculation Ownership

- **Human Owner**: Owns the formulas, pricing sheets, tax rules, and calculation definitions.
- **AI Agents**: Can implement calculation functions matching approved formulas and write unit tests, but are strictly forbidden from altering formulas or rounding behaviors without owner approval.

---

## Calculation Formula Documentation

Every critical calculation must be defined in the project specification files (e.g. data model or page specs) with a clear formula expression:
- Use standard algebraic notation.
- Define all variable terms (inputs, constants, outputs).
- Example:
  $$\text{Invoice Total} = \sum (\text{Line Item Amount}) + \text{Tax Total} - \text{Discount Total}$$

---

## Rounding Strategy

- **Default Mode**: Use **Round Half Up** (e.g., standard symmetric rounding where `0.5` rounds to `1`) or **Round Half Even** (Banker's Rounding, which reduces cumulative rounding bias by rounding `0.5` to the nearest even number).
- **Consistence**: The rounding mode must be uniform across the entire codebase. Do not use Round Half Up in the database and Banker's Rounding in frontend JS code.
- **Explicit Rounding Functions**: Never rely on default language round functions (e.g. JS `Math.round`) which have floating-point quirks. Use dedicated big-decimal libraries (e.g., `decimal.js`, `bignumber.js`) or database rounding features.

---

## Rounding Timing

> [!IMPORTANT]
> **DO NOT ROUND TOO EARLY**: Rounding intermediate steps in a calculation cascade introduces significant compounding errors.
>
> - Maintain maximum precision (e.g., 4 to 6 decimal places) for all intermediate steps (unit price multiplies, discount allocations, interest calculations).
> - Apply the final rounding to 2 decimal places (or the target currency's scale) only at the invoice total, payment charge, or final posted record boundary.

---

## Precision and Scale

- **Precision**: Total number of significant digits (e.g., 18).
- **Scale**: Number of digits to the right of the decimal point (e.g., 4 for intermediate calculations, 2 for final amounts).
- Ensure all datatypes in code and database schemas specify precision and scale (e.g., do not use `NUMERIC` without arguments in Postgres, as it defaults to varying scale).

---

## Order of Operations

To ensure repeatability, follow a strict order of operations:
1. Validate inputs (no nulls, check positive/negative boundaries).
2. Perform multiplications and divisions at high precision.
3. Sum line totals.
4. Calculate tax and discount percentages.
5. Apply final rounding at the aggregate level.

---

## Recalculation Rules

- Once a financial transaction is posted (e.g., an invoice), its values must be locked. No automatic recalculation routines (e.g., when tax rates change in system configuration) are allowed to retroactively modify the posted invoice values.
- Re-run calculations only during explicit amendment workflows approved by the owner.

---

## Manual Override Rules

If an administrator or user manually overrides a calculated value (e.g., manually adjusting a line price or tax rate):
- The override must be recorded in an audit trail.
- Log the **original calculated value**, the **overridden value**, the **actor ID**, the **timestamp**, and the **reason for override**.

---

## Derived Value Persistence Rules

- Always persist the results of critical calculations in the database. 
- Do not rely on runtime calculations during report generation, as historical inputs or tax formulas might change over time, resulting in reports that diverge from historical invoices.

---

## Formula Change Approval

- Modifying any formula or rounding threshold requires a corresponding architectural decision log and human owner approval.
- Coding agents must stop work if a task requires altering an existing business formula.

---

## Regression Testing

- Every formula must be covered by unit tests.
- Tests must cover:
  - Exact division results (e.g., split amounts).
  - High-volume rounding scenarios (to check for cumulative errors).
  - Boundary cases (zero inputs, extremely large values, negative returns).

---

## Common Mistakes

- **JS Math.round Quirks**: Relying on standard float arithmetic, causing `$0.10 + $0.20` to mismatch.
- **Rounding Intermediate Line Items**: Rounding each unit price before multiplying by quantity.
- **Dynamic Calculation in Reports**: Generating financial reports by recalculating totals on-the-fly, leading to mismatches when tax rules or pricing terms change.

---

## Stop Conditions

> [!CAUTION]
> Stop and report to the human owner if:
> 1. You are asked to implement calculations using standard floating-point datatypes.
> 2. You discover rounding mismatches between the database calculations and frontend display layouts.
> 3. You are asked to change rounding rules or calculation formulas without an approved architectural decision log.
> 4. You find intermediate rounding happening in multi-stage calculations.

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Core database definitions.
- [15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Fundamental calculation and rounding constraints.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created calculation formulas and rounding guidelines | Antigravity |
