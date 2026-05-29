# 07E — Money and Currency Guidelines

> Defines standards for representing, storing, and calculating monetary values and handling multi-currency assets in databases and APIs.

---

## Purpose

Prevent financial errors caused by floating-point arithmetic (rounding anomalies) and establish clean, trace-ready patterns for tracking multiple currencies, exchange rate sources, and locale-based financial displays.

## Status

`Active` — Mandatory for all database engineers, backend developers, and software architects designing tables or calculations involving money.

---

## Money Representation Principles

Binary floating-point types (such as `float`, `double`, `real`, or `double precision` in databases) cannot accurately represent base-10 decimals. Performing arithmetic on them introduces rounding errors (e.g., `0.1 + 0.2 = 0.30000000000000004`). 

Monetary representation must follow one of these two patterns:
1. **Minor-Unit Storage (Integers)**: Store all amounts in the currency's smallest fractional unit (e.g. cents for USD/EUR, yen for JPY, fil for AED). $100.50 is stored as the integer `10050`.
2. **High-Precision Decimals (Arbitrary Precision)**: Store amounts using fixed-point decimal types (e.g. `NUMERIC` or `DECIMAL` in databases) with explicitly defined scale and precision (e.g., `NUMERIC(20, 4)` to allow 4 decimal places for internal calculations).

---

## Minor-Unit Storage Pattern

For simple applications without high-fraction pricing, the minor-unit storage pattern is recommended:
- **Database type**: `BIGINT` or `INTEGER`.
- **API representation**: Integers representing the minor unit.
- **Conversion rule**: Convert to major units *only* at the rendering or output boundary (e.g., divide by `100` for display).
- **Caution**: Ensure the database columns and variables are clearly named to indicate minor units (e.g., `amount_cents` or `balance_minor_units`).

---

## Decimal Precision Pattern

For complex billing, interest calculations, or fractional quantities:
- **Database type**: `NUMERIC(precision, scale)`. Recommend `NUMERIC(18, 4)` or `NUMERIC(20, 6)`.
- **Rounding timing**: Keep 4-6 decimal places for intermediate math, rounding to the currency's standard minor-unit scale (typically 2 decimal places) at the posting or billing stage.

---

## Currency Code Rules

Every monetary field in the database must be associated with a currency code:
- Currency codes must use the standard ISO 4217 three-letter code (e.g., `USD`, `EUR`, `GBP`, `JPY`).
- Store the currency code in an explicit column alongside the amount.
- Never hardcode the currency in application code.

---

## Single-Currency vs Multi-Currency Products

- **Single-Currency Products**: The system default currency must be defined in project configuration (e.g., environment variables). Even in single-currency products, database columns must explicitly store or default to the currency code to future-proof the application.
- **Multi-Currency Products**:
  - Keep calculations in the transaction's source currency.
  - Convert to the reporting currency (e.g., base currency) only using explicit exchange rates.
  - Store the exchange rate used, the source of that rate, and the timestamp of conversion on the record.

---

## Exchange Rate Source Rules

When converting currencies:
- Exchange rates must be fetched from an approved, authoritative source (e.g., Open Exchange Rates, European Central Bank API).
- The name or identifier of the source service must be logged on the conversion transaction.

---

## Exchange Rate Timestamp Rules

Exchange rates fluctuate constantly. Conversions must not use stale cached values:
- The rate must correspond to a specific historical timestamp (typically the invoice date, transaction date, or the time the conversion was authorized).
- The database record must store the exchange rate timestamp alongside the rate value.

---

## Display Formatting vs Calculation Rules

- **Display Formatting**: Format currency using locale-aware display APIs (e.g., JavaScript `Intl.NumberFormat`). This handles currency symbols (e.g. `$`, `€`, `£`), symbol positioning (prefix vs suffix), and thousands/decimal separators.
- **Calculation Logic**: Display formatters must NEVER modify stored numbers or interfere with calculation logic.

---

## Refund, Discount, Tax, and Fee Considerations

- **Discounts**: Must be calculated against subtotals using decimal arithmetic, then rounded according to the rounding guidelines.
- **Taxes/Fees**: Store taxes and fees as separate line items and columns, not merged into the base unit price.
- **Refunds**: A refund must match the currency and rate of the original payment. Do not convert the refund amount back and forth across currencies using current rates, as this introduces exchange risk losses or gains.

---

## Out of Scope

- Integrating with specific payment gateways (Stripe, Adyen, etc.).
- Legal definitions of tax brackets.
- Recommending currency hedging strategies or investment advice.

---

## Guardrails

- [ ] **NO FLOAT FOR MONEY**: Do not use binary floating-point types (`float`, `double`, `real`) for monetary database columns or runtime calculations.
- [ ] **EXPLICIT CURRENCY**: Every money value must be stored alongside its ISO 4217 currency code.
- [ ] **EXCHANGE RATE TRACKING**: All currency conversions must record the exchange rate, its source, and the conversion timestamp.
- [ ] **NO TAX/LEGAL ADVICE**: This pack does not provide tax, legal, or accounting advice.

---

## QA Checklist

- [ ] Verify that monetary columns in the database schema use `BIGINT` or `NUMERIC`/`DECIMAL`.
- [ ] Test calculation arithmetic for rounding anomalies (e.g., adding line items and checking totals).
- [ ] Verify currency codes match ISO 4217 standard (uppercase, 3 letters).
- [ ] Test multi-currency billing displays under different user locales (US, French, German).
- [ ] Validate that refunds use the original transaction's exchange rate.

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Core database definitions.
- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — Test planning and QA checks.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created currency handling and numeric representation rules | Antigravity |
