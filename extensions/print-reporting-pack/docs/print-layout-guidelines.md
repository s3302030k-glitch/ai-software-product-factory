# Print Layout Guidelines

> Rules and layout principles for designing pages optimized for printing and PDF layout previews.

This document supplements the core product specifications and page layouts. It does not replace them.

---

## Purpose

This document provides design standards, CSS specifications, and formatting rules for creating print-friendly layouts. It ensures that pages rendered for print or PDF output maintain high legibility, proper margins, and deterministic page breaks across different platforms, browsers, and devices.

## Status

`Active` — These guidelines must be applied whenever print stylesheets or print-friendly page previews are implemented.

---

## When to Use

Apply these guidelines when:
- Creating print stylesheets (`@media print`) for web pages.
- Designing screens that have a "Print Page" action button.
- Building frontend previews of documents that will eventually be converted into PDFs.
- Styling tabular layouts, invoices, or receipts that users are expected to print or save.

---

## Print Layout Principles

1. **Do Not Assume Screen Layout Equals Print Layout**: Interfaces designed for screens (with interactive scrollbars, menus, and sidebars) fail when printed. Print layouts require stripping interactive elements, hiding navigation, and adapting to static page dimensions.
2. **Deterministic Content Breaking**: You must control where page breaks occur. Uncontrolled page breaks split lines of text, chop images in half, or leave orphaned headers at the bottom of a page.
3. **Ink and Color Optimization**: Print styles should use high-contrast text on light backgrounds. Avoid dark background themes, heavy color fills, or low-contrast text.

---

## Page Size and Orientation

- **Default Standard**: Support both **A4** (210mm x 297mm) and **US Letter** (8.5in x 11in) dimensions.
- **Orientation**: Default to portrait unless a landscape grid is explicitly requested (e.g., for wide spreadsheets, financial ledger views, or horizontal charts).
- **CSS Setup**: Use `@page` selectors to define sizes where appropriate:
  ```css
  @page {
    size: A4 portrait;
    margin: 15mm 15mm 20mm 15mm;
  }
  ```

---

## Margins and Safe Areas

- **Minimum Margin**: Maintain a minimum safe margin of `15mm` (approx. `0.6in`) on all edges to account for physical printer margins and binding.
- **Header/Footer Clearance**: Provide at least `10mm` of clear space between the main content body and the page headers/footers to prevent overlap.
- **Safe Area**: Do not place critical content, barcodes, signature lines, or transaction numbers outside the printable safe area.

---

## Header and Footer Rules

- **First Page Behavior**: The first page of an official document (e.g., an invoice or contract) typically features a prominent logo and header block. The standard page header should only appear on page 2 and onwards.
- **Content Requirements**: Standard headers on multi-page documents should show the document title, subtitle, and date of issue.
- **Footer Requirements**: Standard footers should display the confidentiality notice, page number, and document revision/version code.
- **CSS Page Identifiers**: Use CSS counters or container elements positioned absolutely to replicate page header/footer behavior when printing from browsers.

---

## Page Numbering Rules

- **Deliberate Numbering**: Do not rely on default browser header/footer margins for page numbering. Disable browser defaults in your user instructions and render numbers in the document body.
- **Format**: Use the "Page X of Y" format (e.g., `Page 1 of 3`) rather than just `1` to confirm that pages are not missing from the physical printout.
- **Dynamic Calculation**: For CSS-based print layouts, use CSS counters:
  ```css
  .page-number::after {
    content: "Page " counter(page) " of " counter(pages);
  }
  ```

---

## Table Pagination Rules

- **Repeated Headers**: When a table spans multiple pages, table headers (`<thead>`) must repeat at the top of each page for readability.
- **No Split Rows**: Prevent table rows from splitting across pages. If a row cannot fit on the current page, it must push entirely to the next page.
  ```css
  tr {
    page-break-inside: avoid;
    break-inside: avoid;
  }
  ```
- **Max Rows Per Page**: Define a logical maximum height or row limit for tables to avoid orphan headers or trailing totals rows sitting alone on a final blank page.

---

## Signature, Stamp, and Approval Block Rules

- **No Orphan Signatures**: Signature blocks, stamps, and authorization lines must never appear on a page by themselves without accompanying content.
- **Keep Together**: Force signature blocks to stay grouped with at least the final 3-4 rows of a table or the preceding content block.
  ```css
  .signature-section {
    page-break-inside: avoid;
    break-inside: avoid;
  }
  ```

---

## Image and Logo Handling

- **Vector Formats**: Use SVG logos or high-resolution images (at least 300 DPI) for printed graphics to prevent pixelation.
- **Print Hiding**: Hide decorative graphics, backgrounds, illustrations, badges, and icons that do not add informational value to the printed record.
- **Alt Text Rendering**: When printing articles or content-heavy pages, render image alt text or URL references inline if they are vital to the context.

---

## Typography and Readability

- **Print-friendly Fonts**: Use clean, highly readable serif or sans-serif typography (e.g., Georgia, Times New Roman, or Arial/Inter) that maintains contrast at small sizes (e.g., 9pt - 10pt for tables).
- **Absolute Sizes**: Use absolute point sizes (`pt`) or pixels (`px`) in print stylesheets rather than relative viewport sizes (`vw`, `vh`) or elastic rems designed for screen scaling.
- **Line Height**: Maintain a line height of `1.3` to `1.4` for printed body copy to ensure readability.

---

## RTL and Multilingual Print Layout Notes

- **Direction Mirroring**: When printing in right-to-left (RTL) languages (Arabic, Hebrew, Persian, etc.), the entire layout grid, columns, margins, logos, and header hierarchies must mirror correctly.
- **Alignment Checks**: Text-align properties must switch from `left` to `right` and absolute elements positioned on `right` must mirror to `left`. Use CSS logical properties (`margin-inline-start`, `text-align: start`) where supported.
- **Font Compatibility**: Ensure the print stylesheet applies fonts that fully support Persian/Arabic glyphs and prevent character clipping on lines.
- **Page Counters**: Ensure localized character sets or standard numerals are used consistently for page numbers.

---

## Browser Print vs. Generated PDF Notes

- **Browser Print (`ctrl+p`)**: Relies on browser-specific engines (Blink, WebKit, Gecko) and user print settings. Ideal for user-driven quick prints, but less predictable. Requires CSS rules that hide navigation bars, sidebar menus, buttons, and popups.
- **Programmatic PDF Generation**: Generated on the backend (using headless Chrome, Puppeteer, WeasyPrint, or PDF libraries). Highly predictable, layout-controlled, and immutable. Essential for official legal, tax, or business documentation.
- **QA Expectation**: Official documents must not rely on ad-hoc browser print behavior without comprehensive QA validation and verification controls.

---

## Out of Scope

- Implementing specific HTML/CSS print stylesheets or PDF rendering code.
- Configuring specific PDF generation library servers or headless browser clusters.
- Defining product-specific invoice headers or localized copy.

---

## Guardrails

- [ ] Strip all interactive UI components (buttons, dropdowns, navigation menus, tooltips) from the print view.
- [ ] Tables must repeat headers (`<thead>`) on every printed page.
- [ ] Prevent orphan headings (`h1`, `h2`, `h3`) at the bottom of a page by forcing them to keep with the next element.
- [ ] Never place signature or stamp blocks on a page without preceding context.
- [ ] Test layout directionality in both LTR and RTL orientations when both directions are supported by the product brief.

---

## Related Files

- [06 — Pages Spec](../../../core/docs/06-pages-spec.md) — Base layout rules for screen interfaces.
- [15 — AI Agent Operating Rules](../../../core/docs/15-ai-agent-operating-rules.md) — Agent execution boundaries and instructions.
- [pdf-report-guidelines.md](pdf-report-guidelines.md) — Sibling guide for programmatic PDF generation.
- [invoice-contract-document-guidelines.md](invoice-contract-document-guidelines.md) — Sibling rules for invoices and official records.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial guidelines implementation | AI Software Product Factory |
