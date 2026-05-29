# 01A — i18n Content Guidelines

> Outlines conventions, source language rules, translation key guardrails, and workflow constraints for multilingual products.

---

## Purpose

Establish standard procedures and strategies for multi-language copy management, translation formatting, placeholder integration, and quality control. This document ensures that user-facing copy remains consistent, culturally appropriate, and technically correct across all supported languages.

## Status

`Active` — Must be followed by product managers, copywriters, and developers when managing UI strings, errors, or translations.

---

## Content Strategy

Multilingual products require a structured content plan to prevent language duplication, broken layouts due to text expansion, or awkward grammar.
1. **Source of Truth**: All translation files must be synchronized from a centralized key-value repository or localisation platform.
2. **Translation over Transliteration**: Translations must preserve the functional meaning and tone of the original copy, not simply provide literal dictionary mappings.
3. **Layout Considerations**: Expect and design for text expansion. English labels can expand by up to 30% to 50% when translated into European or Semitic languages.

---

## Source Language Rules

To facilitate clean and accurate translations, the source text (typically English) must adhere to these writing rules:
- **Keep it Simple**: Use simple sentence structures. Avoid idioms, metaphors, slang, or regional humor.
- **Avoid Ambiguity**: Avoid words that can function as both nouns and verbs without context (e.g. use "Please register a new account" instead of just "Register").
- **Consistent Terminology**: Always refer to the same concept using the same term (e.g. do not alternate between "Vendor", "Supplier", and "Provider" in UI labels).

---

## Translation Workflow

1. **Extraction**: Identify new UI copy during design and page specification phases.
2. **Key Assignment**: Assign keys according to the naming guidelines in [translation-key-guidelines.md](translation-key-guidelines.md).
3. **Translation Execution**: Populate translation catalogs (e.g., `en.json`, `ar.json`, `fa.json`).
4. **Validation**: Validate using translation checkers and localized build checks.

---

## Copy Ownership

- **Product Owner / Lead Copywriter**: Owns the glossary, approved source text, and final localized content approvals.
- **AI Agents**: Can translate placeholder catalogs or draft suggestions, but final validation of copy tone and accuracy must have human oversight.

---

## Tone and Terminology Rules

1. **Tone Profile**: Define the standard voice of the application (e.g. professional, friendly, neutral). Ensure that localized translations mirror this tone profile.
2. **Glossary Management**: Establish a bilingual or multilingual glossary of core business objects (e.g., "Invoice", "Client", "Payment Status"). All translators and AI copy agents must adhere strictly to the glossary terms.

---

## Avoiding Hardcoded Strings

> [!IMPORTANT]
> User-facing strings must NOT be hardcoded inside frontend codebase files (components, views, pages, or client-side utilities) when internationalization is in scope.
>
> - Every user-visible string must be fetched via the i18n translation function (e.g., `t('namespace.key')`).
> - Static images containing embedded text are strictly prohibited. Use HTML text overlay or serve localized image assets depending on the active locale.

---

## Placeholder and Variable Rules

When inserting dynamic variables into translated text:
1. **Name and Document Variables**: Variables in translated strings must be explicitly named, never positional (e.g. use `{userName}` or `{{userName}}` instead of `%s` or `{0}`).
2. **No Concatenation**: Do not build sentences by concatenating translated substrings in code (e.g., `t('key_prefix') + variable + t('key_suffix')`). Sentence structure and word order vary drastically across languages.
   - Correct: `t('notification.welcome_user', { name: userName })` where the translation string is:
     - English: `"Welcome back, {name}!"`
     - Persian: `"{name}، خوش آمدید!"`
3. **Punctuation Isolation**: Keep formatting characters (e.g., colons, exclamation marks) inside the translation strings to allow correct placement relative to text direction.

---

## Pluralization Rules

1. **Do Not Hardcode Plural Suffixes**: Never append an `"s"` character or rely on English-only plural rules in code.
2. **Use i18n Plural Engine**: Utilize the pluralization features of the translation framework (e.g. i18next plural keys).
3. **Complex Plurals**: Be aware that languages have varying numbers of plural forms:
   - **English**: 2 categories (one, other)
   - **French**: 2 categories (one, other)
   - **Arabic**: 6 categories (zero, one, two, few, many, other)
   - **Russian**: 4 categories (one, few, many, other)
   - Ensure the translation keys support the plural schema of each active language.

---

## Gender/Formality Notes

Some languages (e.g. Arabic, French, German) change verb conjugations, adjectives, or greetings depending on the gender or formality level of the user:
- If personalization is required, include keys that dynamically load based on user profile settings (e.g., `greeting.male`, `greeting.female` or `greeting.formal`, `greeting.informal`).
- Otherwise, use gender-neutral and polite, universally acceptable phrasing in translations.

---

## Error Message Translation Rules

1. **Actionable and Safe**: Error messages must remain clear, polite, translatable, and secure (do not expose stack traces or raw database exceptions).
2. **Error Translation Keys**: Map application error codes to translation keys under a dedicated `errors` namespace:
   - System error: `errors.auth.invalid_credentials`
   - UI maps this to: `"The email or password you entered is incorrect. Please try again."`
3. **No Raw API Message Rendering**: Client-side components must catch API errors and lookup translations using error keys instead of displaying unformatted backend text directly.

---

## Empty/Loading/Success/Error States

Ensure every page state defined in [06-pages-spec.md](../../../core/docs/06-pages-spec.md) has fully translated copy:
- **Loading State**: `"Loading invoices..."`, `"Please wait..."`
- **Empty State**: `"No invoices found. Click 'New Invoice' to create your first one."`
- **Success State**: `"Invoice created successfully!"`
- **Error State**: `"Failed to load invoices. Please check your internet connection."`

---

## Review Workflow

1. **Local Review**: Developer validates translation file changes locally.
2. **Copy Audit**: Copy review agent runs checks on tone, key names, and variable placement.
3. **QA Verification**: localization QA validates the strings in context.

---

## Out of Scope

- CSS, styling, page direction, or layout mirroring (see [rtl-ui-guidelines.md](rtl-ui-guidelines.md)).
- Numbers, currencies, timezones, and calendars (see [locale-formatting-guidelines.md](locale-formatting-guidelines.md)).
- Backend database schema definitions for translatable product listings.

---

## Guardrails

- [ ] Zero user-facing strings are hardcoded in application UI component source files.
- [ ] No positional placeholders (e.g., %s, %d) are used in translation files; use named variables instead.
- [ ] Concatenation of translated fragments to form sentences is strictly banned.
- [ ] All error messages are translated via a structured, secure key mapping system.

---

## Related Core Files

- [01-product-brief.md](../../../core/docs/01-product-brief.md) — Core product goals.
- [06-pages-spec.md](../../../core/docs/06-pages-spec.md) — UI layouts requiring copy specifications.
- [15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — General operating constraints.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created i18n content strategy and translation rules | Antigravity |
