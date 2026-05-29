# 07H — Units and Quantity Guidelines

> Defines standards for modeling physical quantities, measurement units, precision, conversions, and adjustments across contracts, orders, and receipt logs.

---

## Purpose

Prevent financial and physical discrepancies caused by rounding errors during unit conversions (e.g., converting grams to kilograms, counts to weights) and maintain clear separation of quantities throughout the fulfillment lifecycle.

## Status

`Active` — Must be followed by developers, architects, and business analysts designing stock keeping, shipping, or contract pricing schemas.

---

## Unit Modeling Principles

- **Base Unit Storage**: Store all quantities in the database using a single, immutable base unit (the source of truth).
- **Explicit Conversion**: Conversions for client display or reporting must happen at the boundaries.
- **Traceability**: Changes to expectation vs. actual physical measurements must be tracked.

---

## Base Unit vs Display Unit

- **Base Unit (Storage)**: The database field must use a fixed base unit (e.g., grams for mass, millimeters for length, items for count). This is the value used in all core math and pricing calculations.
- **Display Unit (Presentation)**: The UI converts the base value to the user's preferred unit (e.g. converting 1,500,000 grams to 1.5 metric tonnes). Display conversions must never modify the database source of truth.

---

## Quantity Precision

Quantities must use fixed-point decimals (`NUMERIC`/`DECIMAL`) rather than standard binary floats to prevent rounding leaks:
- Standard precision recommendation: `NUMERIC(18, 6)` or `NUMERIC(15, 3)` depending on the granular needs.
- If fractional items are impossible (e.g., selling integer units like laptops), enforce integer database types (`BIGINT` or `INTEGER`) at the schema constraint level.

---

## Unit Conversion Rules

Conversions must use explicit, tested multiplier constants:
- Convert up/down by multiplying/dividing the base unit by the exact conversion factor.
- **Precision Preservation**: Carry conversion calculations out to 6 or more decimal places before final display rounding.

---

## Weight, Volume, and Count Examples

- **Weight (Mass)**: Base unit `grams (g)`. 
  - To display as `kilograms (kg)`: Divide base by `1,000`.
  - To display as `tonnes (t)`: Divide base by `1,000,000`.
- **Volume**: Base unit `milliliters (ml)`. 
  - To display as `liters (l)`: Divide base by `1,000`.
- **Count**: Base unit `each (ea)`. Fractional units are blocked unless splitting cases is explicitly supported.

### kg/tonne Example Pattern

If a contract specifies a price per metric tonne, but the weight scales measure in kilograms (kg):
1. Database stores the scale measurement in base unit kilograms: `measured_weight_kg = 5420`.
2. Pricing formula pulls the contract price per tonne: `price_per_tonne = 150.00`.
3. Calculation converts kilograms to tonnes at high precision:
   $$\text{Weight in Tonnes} = \frac{5420}{1000} = 5.420000\text{ tonnes}$$
4. Calculate subtotal:
   $$\text{Subtotal} = 5.420000 \times 150.00 = 813.00$$
5. Round subtotal to 2 decimal places: `$813.00`.

---

## Contract, Expected, Measured, and Received Quantity Distinction

Do not treat all quantities as the same column or field. Maintain a clear lifecycle history:
- **Contract Quantity**: The total quantity committed in the legal agreement.
- **Expected Quantity**: The amount planned for a specific delivery or shipment.
- **Measured Quantity**: The weight or count recorded by logistics equipment or warehouse scanners.
- **Received Quantity**: The final amount accepted at the delivery dock after damage inspections.
- **Adjusted Quantity**: Any corrections made due to shrinkage, errors, or billing adjustments.

Merging these into a single `quantity` field obscures shrinkage and prevents reconciliation audit checks.

---

## Quantity Correction and Adjustment Pattern

If a quantity needs adjustment after operational progress starts (e.g., shipping is checked-in):
- Do not edit the original expected quantity.
- Create an adjusting entry (e.g. `QuantityCorrection` record storing diff quantity, actor, timestamp, and reason).
- Trace adjustments explicitly to reconcile invoices against shipping logs.

---

## Out of Scope

- Specific physical unit libraries or third-party conversion APIs.
- Inventory valuation formulas (FIFO, LIFO, Weighted Average cost models).

---

## Guardrails

- [ ] **BASE STORAGE**: Store operational quantities in a documented base unit.
- [ ] **NO FLOAT QUANTITY**: Do not use floating-point types (`float`, `double`, `real`) for quantity fields.
- [ ] **EXPLICIT CONVERSION LOGIC**: Display units must not change source-of-truth quantities.
- [ ] **SEPARATE LIFECYCLE FIELDS**: Never use a single mutable field to track expected, measured, received, and adjusted quantities.

---

## QA Checklist

- [ ] Verify that quantity columns in database use `NUMERIC` or `INTEGER` fields.
- [ ] Test converting kilograms to tonnes and verify mathematical precision is carried to 6 decimal places before rounding.
- [ ] Verify that contract quantity remains unchanged when receiving cargo logs.
- [ ] Verify that quantity adjustments write correction logs (actor, reason, diff).

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Database schema definitions.
- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — QA test strategy.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created units and quantity modeling guidelines | Antigravity |
