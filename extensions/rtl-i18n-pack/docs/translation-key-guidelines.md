# 07C — Translation Key Guidelines

> Defines naming standards, namespaces, grouping structures, and lifecycle guidelines for translation keys in multilingual applications.

---

## Purpose

Establish clean, semantic, and stable conventions for writing, organizing, and maintaining translation keys. This keeps codebase UI components decoupled from specific translation copy strings and ensures translations are modular and easily maintainable.

## Status

`Active` — Must be followed by frontend developers and business analysts when drafting page specs or writing component logic.

---

## Translation Key Naming Conventions

1. **Semantic and Stable**: Translation keys should describe *what the string represents*, not what it says in English. This ensures keys do not change when copy is revised.
   - Correct: `login.button.submit`
   - Incorrect: `login.button.click_here_to_login_now`
2. **Casing**: Use `snake_case` or `camelCase` consistently for key names. Do not mix casing styles in the same file.
3. **Hierarchy**: Use dot-separated path format to denote layout levels (e.g. `namespace.page.section.element`).

---

## Namespace Conventions

Divide translations into logical namespaces (separate JSON files) to prevent the application from loading unnecessary strings.

| Namespace | Contents | Example Key |
|-----------|----------|-------------|
| `common` | Shared layout elements, buttons, generic actions | `common.buttons.save` |
| `auth` | Login, signup, password recovery flow strings | `auth.login.label_email` |
| `errors` | System validations, API messages, and constraints | `errors.validation.field_required` |
| `pages` | Page-specific content and dashboard layouts | `pages.dashboard.card_revenue_title` |

---

## Page-Level Keys

- Group page-specific translations inside the `pages` namespace under a folder or key matching the page route name.
- Example for `/invoices/:id`: `pages.invoices.details.section_summary_title`

---

## Component-Level Keys

- Group reusable components' translations in a dedicated `components` namespace.
- Example for a shared calendar component: `components.calendar.month_january`

---

## Shared/Common Keys

- Use the `common` namespace for labels that appear across multiple pages.
- Standard buttons: `common.buttons.cancel`, `common.buttons.delete`, `common.buttons.confirm`
- Standard statuses: `common.status.active`, `common.status.pending`, `common.status.inactive`
- Avoid duplicating common words under separate page namespaces.

---

## Error and Validation Message Keys

- Keep form validation rules and error messages modular.
- Example validation: `errors.validation.email_invalid`, `errors.validation.password_too_short`
- Example server error: `errors.server.timeout`, `errors.server.unauthorized`

---

## Dynamic Variable Naming

When using dynamic attributes in translation strings, define them with descriptive names inside the key definitions:
- Correct: `pages.welcome.title_welcome_user: "Welcome back, {userName}!"`
- Incorrect: `pages.welcome.title_welcome_user: "Welcome back, {var}!"` or `"Welcome back, {0}!"`

---

## Deprecated Key Handling

1. **Do Not Delete Instantly**: When a translation key is no longer used, do not delete it immediately. It might still be referenced by cached client applications.
2. **Deprecation Step**: Move the key to a deprecation block or append a comment indicating deprecation, or schedule key cleanup during release updates.
3. **Verification**: Search the codebase to ensure no components reference the key before removal.

---

## Missing Translation Handling

1. **Fallback Translation**: If a key is missing in the target locale (e.g., Persian `fa`), the application must fallback gracefully to the source language string (e.g., English `en`).
2. **No Crash Guardrail**: The application must never crash when a translation key is missing. It should display the raw key name (or the fallback text) and log a warning in development mode.
3. **Auditing**: Run missing translation key validation scripts prior to compiling production releases.

---

## Review Checklist

Before creating or editing translation keys:
- [ ] Confirm the key is semantic and stable (does not hardcode the English copy inside the key name).
- [ ] Verify the key is placed under the correct namespace.
- [ ] Ensure that dynamic variables are properly named and documented in comments if necessary.
- [ ] Confirm no private customer, system, or project business data is exposed in key names.

---

## Common Mistakes

- **English Sentences as Key Names**: Using `"Don't forget to save your changes"` as the key itself instead of `common.notifications.unsaved_changes`.
- **Exposing Private Business Information**: Key names revealing hidden features or code names (e.g., `pages.checkout.project_secret_promotion`).
- **Renaming Keys without Code Updates**: Deleting or renaming translation keys without updating all references in components, leading to broken placeholder text in production.

---

## Stop Conditions

> [!CAUTION]
> Stop and report to the human owner if:
> 1. You are requested to delete or rename keys that are actively referenced in the core codebase without a matching refactor task.
> 2. You find translation keys containing plain-text credentials, developer names, or private client details.
> 3. You are asked to construct dynamic sentences through text concatenation in code instead of utilizing correct variable interpolation keys.

---

## Related Core Files

- [07-data-model.md](../../../core/docs/07-data-model.md) — Standard database data shapes.
- [09-api-design.md](../../../core/docs/09-api-design.md) — API contract errors definition.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created translation key management standards | Antigravity |
