# Extension Pack: RTL & Internationalization (i18n)

> Adds right-to-left language support, multi-language content management, and locale-aware formatting.

---

## When to Use This Pack

Use this extension pack when your product:

- Needs to support **right-to-left (RTL) languages** such as Arabic, Hebrew, Farsi, or Urdu
- Requires **multi-language content** (UI strings, user-generated content, or both)
- Targets users in **multiple countries or locales** with different date, number, and currency formats
- Must comply with **localization standards** for specific markets

---

## What This Pack Will Add (When Built)

### Additional Documents

| Document | Purpose |
|----------|---------|
| `i18n-strategy.md` | Translation workflow, locale management, fallback rules |
| `rtl-layout-guide.md` | RTL-specific CSS patterns, mirroring rules, bidirectional text handling |
| `locale-format-spec.md` | Date, number, currency, and calendar formatting per locale |
| `translation-glossary.md` | Consistent terminology across languages |

### Additional Prompts

| Prompt | Purpose |
|--------|---------|
| `i18n-engineer-prompt.md` | AI agent role for implementing and testing i18n/RTL support |

### Additional Guardrails

- No hardcoded strings in UI components
- All text must go through the i18n system
- RTL layout must be tested for every page
- Date and number formatting must use locale-aware functions
- Text direction must be set at the document/component level

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| Layout breaks in RTL languages | RTL layout guide with specific CSS patterns |
| Inconsistent translations | Translation glossary and workflow |
| Hardcoded date/number formats | Locale-aware formatting specifications |
| Missing translations crash the app | Fallback rules and error handling |
| Text expansion breaks layouts | Design guidelines for variable-length strings |

---

## Example Project Types

- Government portals serving multilingual populations
- E-commerce platforms targeting Middle East / North Africa markets
- SaaS products with international user bases
- Content platforms with multilingual content
- Financial applications used across multiple countries

---

## Status

> **Status: Placeholder / Planned Future Pack**
>
> This extension pack is currently a **placeholder**. The folder contains only this README. Full templates, prompts, and instructions will be added in a future version.
>
> **Core Governance Rule:** Extension packs are optional and exist to **supplement** core documents for specific product needs — they do **not** replace core documents.
>
> For workspace setup instructions and core rules, link back to [START_HERE.md](../../START_HERE.md).
