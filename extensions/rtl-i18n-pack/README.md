# Extension Pack: RTL & Internationalization (i18n)

> Adds right-to-left (RTL) language support, multilingual content management, translation key guidelines, locale-aware formatting, and dedicated localization QA checks.

This is a fully implemented extension pack, supplementing the core factory templates.

---

## Purpose and Scope

This pack **supplements** the core factory documents; it does **not** replace them. It is designed for software products that require:
- **Right-to-Left (RTL) UI support** for languages such as Persian, Arabic, Hebrew, and Urdu.
- **Multilingual (i18n) content workflows** for consistent interface labels across different target languages.
- **Locale-aware formatting rules** for dates, times, calendars, numbers, and currencies.
- **Translation key management** to ensure keys remain clean, semantic, and decoupled from raw strings.
- **Localization QA processes** to prevent layout breakage, untranslated strings, or formatting regressions.

### Left-to-Right (LTR) Multilingual Projects
This pack is also valuable for standard LTR-only multilingual projects. The locale formatting, copy review guidelines, translation key structures, and copy review prompts remain highly relevant for languages like Turkish, Spanish, French, or German, even though they are left-to-right.

This pack is strictly template-based and generic. It does not contain product-specific details, private business information, real translations, real customer data, real credentials, or real project IDs.

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| **Broken RTL Layouts** | Establishes mirroring rules using CSS logical properties and HTML attributes. |
| **Inconsistent Copy/Tone** | Outlines source copy rules, variable documentation, and copy review workflows. |
| **Silent Layout Overflow** | Mandates testing of expanded translated strings and long localized numbers. |
| **Hardcoded UI Strings** | Imposes strict guardrails prohibiting hardcoded text in UI components. |
| **Calculation Currency Mixing** | Separates raw stored numerical data from display-level localized currency rendering. |
| **Bad Text Concatenation** | Defines strict formatting rules for variable replacement to prevent grammar breaks. |
| **Unintentional Icon Mirroring** | Differentiates non-directional (fixed) and directional (mirrored) icons. |

---

## Pack Components

### Documentation Guidelines (`docs/`)

- [RTL UI Guidelines](docs/rtl-ui-guidelines.md) — Mirroring principles, typography notes for Persian/Arabic/Hebrew, directional icons, and mixed direction text.
- [i18n Content Guidelines](docs/i18n-content-guidelines.md) — Multi-language strategies, pluralization, variables, error translations, and copywriting guardrails.
- [Locale Formatting Guidelines](docs/locale-formatting-guidelines.md) — Localized dates, calendars, numbers, currencies, and display formatting boundaries.
- [Translation Key Guidelines](docs/translation-key-guidelines.md) — Key naming conventions, namespaces, page/component scoping, and key removal workflows.
- [RTL & i18n QA Checklist](docs/rtl-qa-checklist.md) — Comprehensive QA checklist covering RTL layouts, content validation, forms, tables, and device rendering.

### AI Agent Role Prompts (`prompts/`)

- [RTL UI Review Agent](prompts/rtl-ui-review-agent-prompt.md) — Role prompt to audit layouts, mirroring, and mixed LTR/RTL text direction safety.
- [i18n Copy Review Agent](prompts/i18n-copy-review-agent-prompt.md) — Role prompt to audit translation keys, placeholder naming, plural rules, and hardcoded UI copy.
- [Localization QA Agent](prompts/localization-qa-agent-prompt.md) — Role prompt to conduct pre-release validation of localized formatting, translation fallbacks, and layouts.

---

## Recommended Usage

Follow these steps to integrate this extension pack into your product project:

1. **Initialize Core Kit First:** Copy the core factory documents (`core/docs/`) and prompt templates (`core/prompts/`) into your product project workspace.
2. **Apply RTL & i18n Pack:** Copy this pack's folders (`docs/` and `prompts/`) into your project *only* if RTL, multilingual, or localization support is part of the product.
3. **Add to Product Documentation:** Merge relevant files from `docs/` into your active product documentation.
4. **Use Prompts in Dev & QA Cycles:** Run specialized prompts during design/code reviews (prior to UI implementation) and during QA validation steps to check compliance.
5. **Never Rely on Visual Guessing:** Always define direction, copy, formatting, and translation rules explicitly in project documents before implementation starts.

For workspace setup instructions and core governance rules, link back to [START_HERE.md](../../START_HERE.md).
