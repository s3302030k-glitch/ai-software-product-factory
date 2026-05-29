# 12A — RTL & i18n QA Checklist

> Comprehensive checklist, testing strategy, and bug reporting format for verifying Right-to-Left layouts, content internationalization, and locale formatting.

---

## Purpose

Provide QA engineers and automated validation tools with a structured inspection plan to verify localization correctness before releasing any batch or production updates.

## Status

`Active` — Must be completed for every UI-touching batch and prior to major product releases.

---

## Testing Strategy & Requirements

To sign-off on a localized release, the QA agent must fulfill these verification requirements:
1. **Test at Least One RTL Locale**: Select an active RTL language (e.g. Persian/Arabic/Hebrew) to test layout mirroring.
2. **Test at Least One LTR Locale**: If the product is multilingual, test standard LTR (e.g. English) to ensure no LTR regressions were introduced.
3. **Verify Mixed-Direction Content**: Test mixed strings, specifically Persian/Arabic text combined with English IDs, emails, URLs, code snippets, or numbers.
4. **Inspect Length Extremes**: Test with both very long translated strings (to check for line wrapping/layout breaks) and short strings.
5. **Verify All UI States**: Test translation and layout behavior across empty, loading, error, and success states.

---

## RTL Visual QA Checklist

- [ ] **Document Direction**: Check that the `dir="rtl"` attribute is set on the `<html>` or `<body>` tag.
- [ ] **Page Mirroring**: Verify that the global page structure is mirrored (e.g. left side items have swapped positions with right side items).
- [ ] **Alignment**: Ensure that text elements align to the right (except numeric-only tables or LTR strings).
- [ ] **Logical CSS**: Confirm that margins, paddings, and border-radii mirror correctly (no fixed `margin-left` pushing elements off-screen).
- [ ] **No Visual Overlap**: Check that labels and text do not overlap adjacent input boxes, icons, or borders.
- [ ] **Text Clipping**: Ensure that Arabic/Persian letter descenders (tail segments) are not cropped due to insufficient line-height.

---

## i18n Content QA Checklist

- [ ] **Zero Hardcoded Strings**: Scan pages for any raw English text that remains un-translated when switched to another locale.
- [ ] **Correct Key Lookup**: Verify that keys render their value strings instead of raw path keys (e.g. page should never display `auth.login.title` directly).
- [ ] **Placeholder Replacement**: Ensure variables (e.g. `{userName}`) compile and populate correctly; they must not display raw brackets.
- [ ] **No Sentence Fragmentation**: Check that sentences read naturally in translation (no grammar errors caused by concatenating strings).
- [ ] **Static Assets**: Verify that images or illustrations containing text display their localized counterparts.

---

## Locale Formatting QA Checklist

- [ ] **Numbers**: Check that numbers use the local decimal and thousands grouping separators.
- [ ] **Currencies**: Verify that currency values format correctly and place symbols in their correct locale positions.
- [ ] **Date/Time Display**: Confirm dates match regional norms (e.g., DD/MM/YYYY vs MM/DD/YYYY vs YYYY/MM/DD).
- [ ] **Calendars**: Verify solar Hijri calendar outputs dates matching current Gregorian periods if Persian locale is active.
- [ ] **UTC Conversion**: Verify that timezone adjustments convert database UTC values to the user's localized zone on the client.

---

## Forms QA

- [ ] Labels sit above or to the right of inputs.
- [ ] Placeholder text aligns to the right inside inputs.
- [ ] Validation errors align under or inline with input boxes.
- [ ] Checkbox and radio buttons render to the right of their descriptive text.
- [ ] Input typing inputs characters from right to left correctly.

---

## Tables/Data Grids QA

- [ ] Column headers sort from right to left.
- [ ] Text content aligns to the right.
- [ ] Numeric content aligns to the left (standard number reading convention).
- [ ] Table actions column sits on the far left.
- [ ] Column sizing is wide enough to handle translated strings without breaking layouts.

---

## Navigation QA

- [ ] Main header logo sits on the right; navigation links align to the left.
- [ ] Main navigation sidebar is docked on the right side.
- [ ] Collapsing chevrons point in the correct visual direction (collapse points right, expand points left).
- [ ] Breadcrumbs flow from right to left with mirrored separators.

---

## Export/Print/Report QA

- [ ] PDF report layouts mirror correctly when generated under an RTL locale.
- [ ] CSV file export supports decimal markers (e.g., semicolons used as column separators when commas are decimal markers).
- [ ] Export files explicitly state their generation locale.

---

## Mixed-Direction Text QA

- [ ] Embedded emails (`support@example.com`) are readable LTR, and the `@` symbol is not displaced.
- [ ] URLs (`https://example.com/invoices/1`) render LTR and do not break.
- [ ] SKUs, UUIDs, and technical system codes remain readable from left to right.
- [ ] Wrapped lists containing numbers and RTL text format correct markers.

---

## Browser/Device QA

- [ ] Render layouts correctly in Chrome, Firefox, Safari, and Edge.
- [ ] Test responsive layouts on mobile devices (RTL layouts have higher risk of horizontal overflow).
- [ ] Ensure touch targets remain usable when layout mirroring is applied.

---

## Regression Checklist

Verify that adding RTL support has not broken LTR rendering:
- [ ] User can switch from RTL to LTR, and the visual page mirrors back correctly.
- [ ] Standard user flows (registration, login, checkout) function on both locales.
- [ ] No database calculations are affected by switching currency displays.

---

## Bug Report Format (RTL/i18n)

When reporting bugs found during localization testing, use this format:

```markdown
## Localization Bug Report

### Bug ID
BUG-LOC-XXX

### Title
[e.g., Right-docked navigation sidebar overlaps page content under Persian locale]

### Severity
`Critical` | `Major` | `Minor` | `Cosmetic`

### Affected Locale(s)
[e.g., fa-IR, ar-SA]

### Steps to Reproduce
1. Log in to the application.
2. Toggle language to "Persian".
3. Navigate to the Invoices list page.
4. Observe the navigation sidebar alignment.

### Expected Behavior
The navigation sidebar should dock on the far right and push content to the left.

### Actual Behavior
The sidebar docks on the right but floats over the main content grid, covering the invoice IDs column.

### Screenshots / Recordings
[Attach absolute path to artifact image or detail visual mockup]

### Technical Details
- OS: Windows 11
- Browser: Safari / Chrome 120
- CSS Class involved: `.sidebar-container` using physical `right: 0` instead of logical property layout placement.
```

---

## Release Readiness Checklist

- [ ] 100% of core pages have been manually checked in at least one RTL and one LTR locale.
- [ ] Zero raw un-translated strings are detected in UI build artifacts.
- [ ] All forms have been tested with inputs containing mixed LTR/RTL text.
- [ ] Automated build, linting, and formatting checks pass.
- [ ] Human owner has signed off on the localization review.

---

## Related Core Files

- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — Core QA test procedures.
- [06A — RTL UI Guidelines](rtl-ui-guidelines.md) — Interface design principles.
- [01A — i18n Content Guidelines](i18n-content-guidelines.md) — Copywriting rules.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created RTL and i18n QA checklist | Antigravity |
