# 22 — RTL & i18n Notes: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document specifies RTL mirroring guidelines, localized date/number formatting rules, and translation key namespaces for the Team Subscription Manager, in alignment with the **[RTL & Internationalization (i18n) Pack](../../../extensions/rtl-i18n-pack/README.md)**.

---

## 1. Right-to-Left (RTL) Layout Mirroring
- **Dynamic Text Direction**: When switching to RTL languages (such as Persian, Hebrew, or Arabic), the UI must update the root element attribute to `dir="rtl"`.
- **CSS Logical Properties**: Sidebars, paddings, and alignment rules must use CSS logical properties instead of directional absolute properties:
  - Use `margin-inline-start` instead of `margin-left`.
  - Use `padding-inline-end` instead of `padding-right`.
  - Use `text-align: start` instead of `text-align: left`.
- **Directional Icons**: Icons representing progression direction (e.g. arrows, chevron buttons) must mirror directions dynamically under `dir="rtl"`. Global static icons (e.g., mail envelope icons, credit card icons) remain unmirrored.

---

## 2. Date, Number, and Currency Formatting
- **Browser Locale APIs**: All formatting must utilize standard locale APIs (`Intl.DateTimeFormat`, `Intl.NumberFormat`) rather than custom string formatting functions.
- **Rules**:
  - Dates must dynamically render using the selected user locale (e.g. `MM/DD/YYYY` for US, and `DD/MM/YYYY` for UK).
  - Currencies format raw integers (cents) to target locale display characters (e.g., `$15.00` for USD in LTR, and `15,00 €` for EUR in European locales).

---

## 3. Translation Key Namespaces
- **No Hardcoded Strings**: All text displayed in UI views must map to key identifiers stored in translation namespace JSON files.
- **Namespace Scoping**: Keys are structured by component namespace:
  - `billing.invoice.amount`
  - `members.invitations.pending`
  - `navigation.sidebar.dashboard`

---

## 4. Localization QA Verification
- **Text Wrap Checks**: Check interfaces to ensure translated strings (which are frequently 20-30% longer than English equivalents) do not trigger layout clipping.
- **Switching State**: Test toggling locales to confirm that cached layouts reload without breaking inputs or alignment.

---

## Related Files

- [06-pages-spec.md](06-pages-spec.md) — Pages and UI blueprints.
- [12-qa-test-plan.md](12-qa-test-plan.md) — Localization QA test checklists.
