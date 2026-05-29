# 06A — RTL UI Guidelines

> Outlines guidelines, CSS mirroring practices, typography standards, and direction rules for products requiring Right-to-Left (RTL) user interfaces.

---

## Purpose

Establish clear rules for designing, implementing, and maintaining user interfaces that support RTL text flow. This ensures that users of RTL languages (like Persian, Arabic, Hebrew, and Urdu) experience an interface that is visually correct, intuitive, and consistent.

## Status

`Active` — Must be followed by all designers, developers, and AI review agents before and during UI implementation.

---

## When to Use

Apply these guidelines when building or updating user interfaces that:
1. Render content in RTL languages (Persian, Arabic, Hebrew, Urdu, etc.).
2. Require dynamic switching between Left-to-Right (LTR) and Right-to-Left (RTL) views.
3. Feature mixed direction text (e.g., Arabic paragraphs containing English URLs, SKU numbers, or code snippets).

---

## RTL Layout Principles

The default flow direction of the Web is Left-to-Right. In RTL, the flow is mirrored horizontally:
- Content flows from right to left.
- Timeflows, progression indicators, and slider inputs move from right to left.
- Layout columns, grids, and tables order from right to left.

---

## Direction Rules

1. **HTML Attribute**: The global direction must be set on the root element using `dir="rtl"` or `<html dir="rtl" lang="ar">`.
2. **Dynamic Switching**: Ensure the application dynamically toggles the `dir` attribute on the `<html>` or `<body>` element when the language changes.
3. **No Ad-Hoc Styling Only**: UI direction must not be handled by ad-hoc CSS overrides only (e.g. applying `direction: rtl` randomly in separate stylesheets). The `dir` attribute must drive layout behavior.

---

## Page Layout Rules

1. **Grid and Flexbox**: Use CSS Logical Properties instead of physical coordinates.
   - Use `margin-inline-start` instead of `margin-left`.
   - Use `padding-inline-end` instead of `padding-right`.
   - Use `border-start-start-radius` instead of `border-top-left-radius`.
2. **Mirroring Layouts**: Grid structures must follow the direction. For example, a 3-column grid is rendered from right to left: Column 1 is on the far right, Column 2 in the center, Column 3 on the far left.
3. **Header and Footer**: Top navigation links, brand logos, and utility menus must be mirrored. The logo should sit on the right and navigation links align to the left of the logo.

---

## Component Layout Rules

1. **Sidebars and Drawers**: Sidebars that anchor on the left in LTR (such as main navigation menus) must anchor on the right in RTL. Side overlays (drawers, modal alerts) must slide in from the opposite side.
2. **Tabs**: Tabs must align starting from the right. The active tab indicator must slide correctly from right to left.
3. **Accordions**: Expand/collapse chevrons must sit on the opposite side of the text label (typically the far left in RTL).

---

## Forms and Input Rules

1. **Alignment**: Label text and placeholder text must align to the right. Input fields, textareas, and select menus must align text input to the right.
2. **Label Placement**: Place label text above or to the right of the field, never to the left.
3. **Helper and Error Text**: Inline help messages, validation warnings, and character counts must align to the right (or align with the start edge of the input).
4. **Interactive Controls**: Radio buttons and checkboxes must sit to the right of their accompanying text label.

---

## Tables and Data Grids

1. **Ordering**: Columns are read from right to left.
2. **Text Alignment**:
   - Align text columns to the right.
   - Align numerical data columns to the left (consistent with general number reading).
   - Action columns (edit, delete, view buttons) must sit on the far left of the table row.
3. **Paging Controls**: Next and Previous page arrows must be mirrored.

---

## Navigation and Sidebar Rules

1. **Navigation Items**: Navigation lists should align text to the right.
2. **Sidebar Collapse/Expand**: The collapse chevron must point inward. For a right-docked sidebar, the collapse chevron points to the right (`>`) and expand points to the left (`<`).
3. **Breadcrumbs**: Breadcrumbs flow from right to left. Use mirrored separator symbols (e.g., `‹` or `/`).

---

## Icons and Directional Symbols

> [!WARNING]
> Do not blindly mirror all icons. Inappropriate mirroring creates confusion.

1. **Do Not Mirror Non-Directional Icons**: Icons representing physical objects, abstract tools, or state indicators must remain fixed:
   - Search (magnifying glass)
   - Lock/Key
   - Gear (settings)
   - Trash bin
   - Calendar
   - Print
   - Camera
   - Edit (pencil)
   - Info/Help circles
2. **Mirror Directional Icons**: Icons that convey motion, sequence, or direction must be mirrored horizontally:
   - Back and Forward arrows (e.g. `arrow-left` becomes `arrow-right`)
   - Play, Rewind, Fast-Forward controls
   - Speech bubbles with line alignment
   - Undo/Redo symbols
   - Progress sliders and progress bars (filling from right to left)
3. **Intentional Review**: Review all icons in the layout to ensure they correspond to semantic expectations.

---

## Mixed LTR/RTL Content Rules

1. **Technical Strings**: Left-to-Right (LTR) technical strings such as database IDs, SKUs, emails, URLs, code snippets, phone numbers, and international dates must remain LTR and align left where appropriate.
2. **Inline Markup**: Wrap inline LTR strings within RTL text inside a `<bdi>` (Bi-directional Isolation) element or use `dir="ltr"` inline to prevent punctuation from wrapping incorrectly.
3. **Bidirectional Text Flow**:
   - Correct: `Please email us at <bdi dir="ltr">support@example.com</bdi> for help.`
   - Incorrect: Hardcoding raw email inside a Farsi paragraph without isolation, which causes the `@` or `.` symbols to render on the wrong side.

---

## Persian/Arabic/Hebrew Typography Notes

1. **Font Families**: Browser default sans-serif fonts may not look correct or readable in RTL scripts. Ensure high-quality web fonts are defined:
   - **Arabic**: Amiri (serif), Cairo (sans-serif), Tajawal.
   - **Persian**: Vazirmatn, IRANSans, Shabnam.
   - **Hebrew**: Rubik, Heebo, Assistant.
2. **Font Size**: RTL characters are often more detailed than Latin characters. Increase the baseline font-size slightly (typically by `1px` or `2px`) and increase `line-height` (to `1.6` or `1.7`) to prevent scripts from overlapping or becoming illegible.
3. **Text Justification**: Avoid full text justification (`text-align: justify`) as it can stretch cursive connections in Arabic/Persian scripts awkwardly. Use `text-align: right` instead.

---

## Accessibility Notes

1. **Focus Order**: Tabbing (`Tab` key navigation) must match the mirrored visual reading order: top-right to bottom-left. Ensure that HTML source order matches the visual order to prevent jarring keyboard navigation.
2. **Screen Readers**: Ensure language codes on elements (e.g. `lang="fa"`, `lang="he"`) are correct so screen readers use the appropriate text-to-speech voice and pronunciation engine.
3. **Aria Labels**: All mirrored interactive components must preserve their semantic labels.

---

## Out of Scope

- Translation management workflows (see [i18n-content-guidelines.md](i18n-content-guidelines.md)).
- Database schemas for multi-lingual tables (see [locale-formatting-guidelines.md](locale-formatting-guidelines.md)).
- Build systems, webpack configs, or compiler plugins.

---

## Guardrails

- [ ] Every page and component must use CSS logical properties instead of directional properties (e.g. margin-inline-start instead of margin-left).
- [ ] No non-directional icons are mirrored.
- [ ] Technical strings (emails, URLs, code snippets, SKUs, phone numbers) must use `<bdi>` or `dir="ltr"` when embedded inside RTL blocks.
- [ ] RTL layout behavior must be tested manually across major browsers.
- [ ] Never rely on ad-hoc CSS overrides alone to handle text direction.

---

## Related Core Files

- [06-pages-spec.md](../../../core/docs/06-pages-spec.md) — Core page specification template.
- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — Core QA rules and testing checklists.
- [15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — General operating constraints.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created RTL UI guidelines and layout mirroring rules | Antigravity |
