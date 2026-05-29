# 07B — Locale Formatting Guidelines

> Outlines conventions, data storage limits, and rendering guidelines for dates, numbers, currencies, timezones, and calendars.

---

## Purpose

Establish standards for formatting dates, times, calendars, numbers, currencies, and other localized metrics. This document ensures that display presentation changes depending on the user's locale without modifying underlying data structures or business logic calculations.

## Status

`Active` — Must be followed by all database engineers, backend developers, and frontend developers when coding formatting logic.

---

## Locale Formatting Principles

1. **Separation of Concerns**: Keep display formatting strictly separated from data storage.
2. **Never Store Localized Strings as Truth**: Do not store formatted date/time strings or localized numbers (e.g. `"12.500,50 TL"`) as source-of-truth values in database tables. Always store raw values (integers, decimals, UTC datetimes) and format them at the presentation layer.
3. **Consistency**: Use built-in system localization API functions (e.g., JavaScript `Intl` API) instead of manual formatting implementations.

---

## Date and Time Formatting

1. **Storage Format**: Database columns storing timestamps must use timezone-aware types (e.g. `TIMESTAMP WITH TIME ZONE` in PostgreSQL) and store values normalized to UTC.
2. **Display Format**: Render dates and times in the user's preferred format using locale-aware functions:
   - English (US): `MM/DD/YYYY` (e.g., `05/29/2026`)
   - French (FR): `DD/MM/YYYY` (e.g., `29/05/2026`)
   - Persian (IR): `YYYY/MM/DD` (e.g., `1405/03/08` using Solar Hijri)
3. **Relative Time**: Relative dates (e.g. `"2 hours ago"`) must use translated lookup strings or standard APIs like `Intl.RelativeTimeFormat`.

---

## Calendar Considerations

1. **Gregorian Calendar**: Default calendar for most locales.
2. **Solar Hijri (Persian Calendar)**: Mandatory for products targeting Iran and Afghanistan. Conversion must be performed at the UI layer using locale-aware calendar parameters (e.g. `fa-IR-u-ca-persian`).
3. **Islamic Calendar (Hijri)**: Relevant for specific religious or administrative calculations in Arab countries. Define calendar configurations in page specifications if active.

---

## Number Formatting

1. **Separators**: Decimal points and thousand grouping symbols vary:
   - English: `1,250,500.75` (comma thousands, dot decimal)
   - German: `1.250.500,75` (dot thousands, comma decimal)
   - Persian/Arabic: `۱٬۲۵۰٬۵۰۰٫۷۵` (using Eastern Arabic numerals and local separators)
2. **Numeral Representation**: Specify in configuration whether to render numerals using Western Arabic digits (`0, 1, 2...`) or Eastern Arabic/Farsi digits (`۰, ۱, ۲...` / `٠, ١, ٢...`).

---

## Currency Formatting

1. **Calculation vs. Display**: Business logic, invoices, and totals calculations must always be calculated using precise numeric datetimes (e.g., decimal values with standard scale). Currency display formatting must NEVER change the underlying values or calculations.
2. **Placement**: The position of the currency symbol changes by locale:
   - US: `$150.00`
   - Europe: `150,00 €`
   - Turkey: `₺150,00` or `150,00 TL`
3. **ISO Codes**: When symbols are ambiguous (e.g. `$`), prefix with or use ISO currency codes (e.g., `USD 150.00`, `CAD 150.00`).

---

## Percent and Quantity Formatting

- Percent signs change placement and form:
  - English: `25%`
  - Arabic: `٢٥٪` (mirrored percent sign, right-aligned)
- Quantities (counts of physical items) must format using local number rules (e.g. `1,000 items` vs `1.000 items` vs `۱٬۰۰۰ item`).

---

## Unit Formatting

- Ensure units of measurement (e.g. kilograms, kilometers, miles, pounds) are formatted using locale rules.
- Maintain conversions between imperial and metric systems if the product brief requires country-specific unit switching.

---

## Time Zone Handling

1. **UTC Storage**: All system servers, APIs, and databases must run and record events in UTC time.
2. **User Locale Translation**: Convert UTC timestamps into the user's localized timezone at the edge of the user interface (using the browser's timezone setting or user profile configurations).
3. **DST Safety**: Never assume a fixed offset (e.g., always `+03:30`). Timezone translations must account for local Daylight Saving Time rules.

---

## Sorting and Collation Notes

1. **Database Collation**: Ensure database queries sorting text strings use the correct language collations (e.g. French accents sorting, Farsi characters placing correctly in alphabetic lists).
2. **Client-Side Sorting**: Use string locale compare (`String.prototype.localeCompare`) for frontend sorting.

---

## Input Parsing vs Display Formatting

1. **Loose Input Parsing**: Allow users to enter numbers and dates using their local formatting habits (e.g., using commas or dots as decimal markers).
2. **Standardization**: Parse localized inputs into standardized, clean values (e.g. standard floats or UTC date objects) before dispatching API request payloads.
3. **Input Validation**: Validation rules must support locale variations (e.g. phone number lengths, zip code formats, localized date boundaries).

---

## Export/Print/Report Formatting Notes

> [!IMPORTANT]
> When exporting data (CSV, Excel) or generating printouts (PDF reports, invoices):
> - **Define Locale Behavior Explicitly**: Define whether the export uses the current UI language formatting, or falls back to a standardized system locale (e.g. English/USD for international invoicing).
> - **CSV Separators**: Standard LTR spreadsheets use commas `,` as separators. In countries where commas are used as decimal markers, CSV exports must use semicolons `;` or tabs to prevent column breaks.

---

## Out of Scope

- Core data structures and entity listings (see [07-data-model.md](../../../core/docs/07-data-model.md)).
- Dynamic content translations and copywriting rules (see [i18n-content-guidelines.md](i18n-content-guidelines.md)).

---

## Guardrails

- [ ] Localized formatting must be performed exclusively at the UI/rendering level.
- [ ] No localized number/date strings are stored as raw values in the database.
- [ ] Currency display changes never alter mathematical calculation logic.
- [ ] Export files (CSV, PDF) define their output locale explicitly in the code.

---

## QA Checklist

- [ ] Verify dates are shown in the format matching the selected language (e.g. US date formats vs European date formats).
- [ ] Verify numbers use correct thousands and decimal separator symbols.
- [ ] Verify calendar toggle shows Solar Hijri when Persian locale is active.
- [ ] Verify currency symbol placement matches local rules (e.g., symbol prefix vs suffix).
- [ ] Verify timezones are rendered based on user's browser setting, keeping storage in UTC.
- [ ] Verify CSV export outputs columns correctly under locales with comma decimal markers.

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Core database schema rules.
- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — QA testing procedures.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created locale formatting and timezone guidelines | Antigravity |
