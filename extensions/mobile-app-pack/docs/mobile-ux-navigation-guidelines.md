# 06A — Mobile UX & Navigation Guidelines

> Details navigation models, stack behaviors, form inputs, touch targets, keyboard handling, empty states, and accessibility rules for mobile screen layouts.

---

## Purpose

Define the standards for mobile-specific user experience and interface layout. This prevents desktop patterns from being directly copied into mobile apps, ensuring that interfaces remain usable, intuitive, and responsive to mobile constraints.

## Status

`Active` — Must be followed by all UX designers, developers, and review agents before laying out screen specs.

---

## Mobile UX Principles

1. **Thumb-Friendly Zones**: Place critical buttons and primary actions within natural reach of the user's thumb (lower-to-middle portion of the screen).
2. **Minimize Typing**: Use selectors, toggle buttons, calendars, and auto-completions to minimize raw text input on mobile keyboards.
3. **Graceful Degradation**: Screen layouts must reflow gracefully on smaller screens down to 320px width without layout overlap.
4. **Instant Response**: Interactive states must respond immediately (within 100ms) with visual highlights (hover, active press, ripples) to prevent double-tap issues.

---

## Navigation Patterns

Select the primary navigation layout in [06-pages-spec.md](../../../core/docs/06-pages-spec.md):
- **Tab Bar (Bottom)**: Use for primary, top-level app sections (limit to 3-5 tabs). Bottom tabs are highly visible and thumb-reachable.
- **Navigation Drawer / Hamburger Menu (Side)**: Use for secondary actions, settings, or multi-tenant switches (avoid hiding core user flows here).
- **Stack Navigation (Push/Pop)**: Detail standard navigation transitions. Pushing a new screen onto the stack adds a back button; popping returns to the previous screen.

---

## Back Behavior

- **Explicit Back Button**: Every pushed screen must include an explicit visual back button in the top-navigation bar (e.g., `<` or `Back`).
- **Physical Gesture / Android Back**: The app must handle native back gestures or the physical back button correctly. It must pop the stack, close active modals, or exit sheets.
- **Loss of Unsaved Data Check**: If a form has unsaved modifications, tapping the back button or swiping back must trigger a confirmation dialog before discarding inputs.

---

## Modal and Sheet Behavior

- **Modals (Full Screen)**: Use for focused flows that require the user's complete attention (e.g., initiating a checkout or editing a document). Modals must have a clear "Close" or "X" button.
- **Bottom Sheets (Partial)**: Use for quick choices, filters, or context menus. Users must be able to dismiss bottom sheets by tapping outside the sheet or swiping down.
- **Stacking Modals**: Avoid opening modals on top of other modals. Keep sheets and overlays single-layered.

---

## Small-Screen Layout Rules

- **Hide Secondary Data**: Hide less critical columns in tables or use responsive list-card patterns instead of horizontal scroll grids.
- **Dynamic Font Scaling**: Ensure text remains readable under system-wide accessibility scaling.
- **Sticky Actions**: Renders primary CTAs (e.g., "Submit Order", "Save") as sticky elements at the bottom or top of the viewport for easy access.

---

## Touch Target and Gesture Notes

- **Minimum Size**: All interactive elements (buttons, links, check-boxes, input fields) must have a touch target of at least **44x44 CSS points / pixels** on iOS and **48x48 dp** on Android.
- **Spacing**: Ensure adequate padding between interactive targets to prevent accidental taps.
- **Swipe Actions**: Double-action gestures (like swipe-to-delete) must have a secondary confirmation or clear undo banner.

---

## Form and Keyboard Behavior

- **Input Types**: Assign correct HTML/native input types (e.g., `type="email"`, `type="number"`, `type="tel"`) to invoke the appropriate mobile keyboard layout.
- **Auto-Capitalize / Auto-Correct**: Disable auto-capitalization and auto-correction on inputs that accept usernames, codes, SKU names, or emails.
- **Avoid Hiding Fields**: Form container scrolling must ensure that active input fields are scrolled into view when the mobile virtual keyboard appears.
- **Submit Actions**: Provide action keys on the keyboard (e.g., `Return`, `Go`, `Search`) mapped to form submissions.

---

## Loading and Empty States

- **Loading Spinners / Skeletons**: Avoid frozen screens. Renders skeleton screens for primary dashboard components or loading spinners during actions.
- **Empty States**: Every empty list screen (e.g., "No Invoices") must contain:
  1. A clear explanatory icon or graphic.
  2. A title and brief description.
  3. A primary call-to-action button (e.g., "Create First Invoice").

---

## Error and Retry States

- **In-App Alerts**: Errors must not freeze the app. Renders localized error banners with clear messages.
- **Offline / Network Block**: If an action fails due to network loss, show a specific offline indicator with a "Retry" button.
- **Validation Messages**: Place error strings immediately below the invalid input field, in clear contrast.

---

## RTL & Mobile Localization Notes

- Mobile layouts must mirror horizontally when rendering in Right-to-Left (RTL) locales.
- Toggles, back arrows, list icons, and navigation paths must mirror correctly.
- Cursive fonts for Arabic, Persian, or Hebrew must be configured with appropriate `line-height` spacing to prevent cut-offs.
- Align RTL components with [RTL UI Guidelines](../../rtl-i18n-pack/docs/rtl-ui-guidelines.md) if localization is enabled.

---

## Accessibility Notes

- **Screen Reader Support**: Ensure elements use descriptive semantic attributes (`aria-label`, native button roles) and that screen reader focus flow aligns with visual grid flow.
- **Contrast**: Maintain a contrast ratio of at least 4.5:1 for text.
- **Haptic Feedback**: Trigger mild haptic feedback for success, warnings, or errors when using compiled native shells.

---

## Out of Scope

- Designing custom graphics, vector icons, or branding assets.
- Writing framework-specific animations or gesture-library code (e.g. React Native Reanimated setup).

---

## Guardrails

- [ ] Interactive elements must maintain the minimum 44px/48dp touch target boundaries.
- [ ] Virtual keyboards must not overlap or hide the active input field.
- [ ] Modals, drawers, and sheets must be dismissible via physical back events or gestures.
- [ ] No desktop-style double-click patterns; only tap, long-press, and swipe actions are permitted.

---

## QA Checklist

- **Touch targets**: Verified that all clickable elements are at least 44x44 pixels.
- **Keyboard invocation**: Confirmed correct keyboard layout displays for email/number/text fields.
- **Back navigation**: Verified that tapping back on all pushed pages pop the stack correctly.
- **Keyboard scrolling**: Confirmed active input field is visible when keyboard is active.
- **Empty/Error States**: Confirmed presence of empty state graphics and action paths on list pages.

---

## Related Core Files

- [05-user-flows.md](../../../core/docs/05-user-flows.md) — Step-by-step user journeys.
- [06-pages-spec.md](../../../core/docs/06-pages-spec.md) — Page-level details and visual specs.
- [rtl-ui-guidelines.md](../../rtl-i18n-pack/docs/rtl-ui-guidelines.md) — Sibling localization guidelines.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation of mobile UX guidelines | Antigravity |
