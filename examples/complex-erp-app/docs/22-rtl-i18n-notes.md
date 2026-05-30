# 22 — RTL / i18n Notes

> How the RTL & i18n Pack applies to the Integrated Operations ERP documentation reference.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> See the [example README](../README.md) for full context.
> Extension pack reference: [rtl-i18n-pack README](../../../extensions/rtl-i18n-pack/README.md)

---

## Pack Application Summary

The [RTL & i18n Pack](../../../extensions/rtl-i18n-pack/README.md) governs text direction readiness, translation key structure, locale-aware formatting, and layout mirroring concepts. RTL/i18n in this example is documented as **readiness only** — full translation files, language switching implementation, and locale-specific QA are out of MVP scope (see [03-mvp-scope.md](03-mvp-scope.md) and [14-decision-log.md](14-decision-log.md) Decision 9).

---

## Text Direction

The RTL UI Guidelines from the pack apply:

- The application root element must carry a `dir` attribute (`ltr` or `rtl`) that reflects the active language direction.
- Default direction: `ltr` (left-to-right for English).
- When RTL language support is activated (future scope), the root `dir` attribute switches to `rtl` for languages such as Arabic, Persian, Hebrew, and Urdu.
- All CSS layout uses **logical properties** (e.g., `margin-inline-start` instead of `margin-left`) to support automatic RTL mirroring without duplicate CSS rules.
- Hardcoded `left`/`right` CSS properties are prohibited in new UI components.

---

## Language Switching

- Language switching is documented as **future scope** for this MVP.
- The translation key structure (see below) is defined now so that future language switching can be implemented without restructuring the codebase.
- A `locale` preference field is noted on the User entity for future use.

---

## Date / Number / Currency Formatting

The Locale Formatting Guidelines from the pack apply:

**Date formatting:**
- Dates are stored as ISO 8601 UTC values.
- Display format is locale-dependent: `DD/MM/YYYY` for most regions, `MM/DD/YYYY` for US locale.
- Full implementation of locale-specific date formatting is future scope.

**Number formatting:**
- Large numbers use locale-appropriate thousands separators.
- Decimal separator: `.` for English/US; `,` for many European locales.
- CSV exports use `,` as the column delimiter but this clashes with European number formats — locale-aware CSV delimiter handling is documented in [20-print-reporting-notes.md](20-print-reporting-notes.md) as a future implementation concern.

**Currency display:**
- Currency amounts include a currency display label (e.g., `"1,250.00 USD"`).
- RTL layout: currency symbol placement mirrors with the text direction.
- No real currency conversion or exchange rate fetching is implemented.

---

## Layout Mirroring

For RTL language support (future scope):

| UI Element | LTR | RTL |
|------------|-----|-----|
| Navigation sidebar | Left side | Right side |
| Form labels | Left-aligned | Right-aligned |
| Table columns | Left-to-right | Right-to-left |
| Action buttons (primary) | Right side | Left side |
| Currency amount | Left-aligned number | Right-aligned number |
| Icon directions (arrows, chevrons) | Mirror horizontally | Mirror horizontally |
| Non-directional icons (settings, user) | No mirror | No mirror |

**Pack Rule Applied:** Directional icons (forward/back arrows, pagination chevrons, breadcrumb separators) must mirror in RTL layouts. Non-directional icons (gear, user avatar, bell) must not mirror.

---

## Translation Keys

The Translation Key Guidelines from the pack define the structure:

**Key namespace convention (conceptual):**
```
{page}.{component}.{element}
```

**Examples:**
```
dashboard.widgets.pending_approvals.label
purchase_requests.form.submit_button
stock_movements.table.column.quantity
invoice_list.status.partially_paid
approval_inbox.reject_reason.placeholder
```

**Rules:**
- No hardcoded UI strings in components — all strings reference a translation key.
- Key names are in English snake_case.
- Keys are grouped by page/feature namespace to avoid global key collision.
- Removed keys must be tracked — keys cannot be silently deleted without removing their references.

**Full translation file implementation is out of MVP scope.** Key structure is defined now to enable future implementation without restructuring.

---

## QA Notes

The RTL & i18n QA Checklist from the pack applies (future scope):

- [ ] All UI strings use translation keys (no hardcoded text)
- [ ] RTL layout mirror verified for all 22 pages
- [ ] Date format switches correctly per locale
- [ ] Currency amount displays correctly per locale
- [ ] CSV export delimiter adapts to locale
- [ ] Directional icons mirror in RTL; non-directional icons do not
- [ ] Form inputs work correctly in RTL direction
- [ ] Table sort indicators mirror in RTL

These QA checks are future scope — RTL/i18n readiness is documented but full QA is not in MVP scope for this documentation reference.

---

## Related Files

- [06-pages-spec.md](06-pages-spec.md) — Pages that must support RTL layout
- [20-print-reporting-notes.md](20-print-reporting-notes.md) — CSV delimiter locale notes
- [08-architecture.md](08-architecture.md) — RTL readiness noted in frontend surface
- [14-decision-log.md](14-decision-log.md) — Decision 9 (RTL readiness vs full translation)
- [../../../extensions/rtl-i18n-pack/README.md](../../../extensions/rtl-i18n-pack/README.md) — Source pack
