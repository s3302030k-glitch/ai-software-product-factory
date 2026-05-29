# 17 — Batch Request Template

> The standard format for requesting a unit of work from the Coding Agent, with a filled sample request for Batch P1-B1 of the Invoice Tracker.

---

## Purpose

Define a single, traceable unit of work that the Coding Agent can execute within its operating rules. Every batch request provides the agent with everything it needs to work safely and completely.

## Status

`Template / Reference`

---

## Sample Batch Request: P1-B1

### Batch ID
`P1-B1`

### Title
`Initialize framework foundation and documentation structure`

### Objective
Initialize Next.js application setup, configure Vanilla CSS variables for layout and themes, and arrange the main sidebar routing frame.

---

### Required Reading

Documents the agent MUST read before starting this batch:

| Priority | Document | Reason |
|----------|----------|--------|
| 1 | `15-ai-agent-operating-rules.md` | Always required |
| 2 | `16-context-snapshot.md` | Current project state |
| 3 | `08-architecture.md` | Follow Next.js structure and Vanilla CSS constraints |
| 4 | `00-document-priority.md` | Authority guidelines |

---

### Scope

Exactly what is included in this batch:
- [ ] Initialize configuration files and setup workspace folders matching `08-architecture.md`.
- [ ] Setup standard layout shell at `src/app/layout.js` containing the app header and sidebar navigation elements.
- [ ] Create `src/styles/globals.css` defining Vanilla CSS variables for dark mode and responsive viewport adjustments.
- [ ] Implement empty router path skeletons for `/dashboard`, `/clients`, `/invoices`, and `/settings` returning sample placeholder cards to ensure sidebar navigation links load without errors.

---

### Out of Scope

- Authentication middleware and endpoints (Batch P1-B2).
- Client CRUD or API endpoint handlers (Phase 2).
- Database migration initialization (Phase 2).
- Inline invoice item calculations (Phase 3).
- PDF print layout rules (Phase 6).

---

### Files Likely Involved

| File / Directory | Action | Notes |
|-----------------|--------|-------|
| `src/app/layout.js` | Create | Root routing frame with navigation links |
| `src/styles/globals.css` | Create | Global theme typography and layout definitions |
| `src/app/dashboard/page.js` | Create | Stub dashboard card view |
| `src/app/clients/page.js` | Create | Stub clients table placeholder |
| `src/app/invoices/page.js` | Create | Stub invoices table placeholder |
| `src/app/settings/page.js` | Create | Stub settings view |

---

### Validation Rules & Commands

Commands to verify that changes do not break the baseline:

```bash
# Verify ESLint configurations and file layout correctness
npm run lint

# Compile and build Next.js application
npm run build
```

#### Baseline-Aware Validation Expectations
- **Baseline:** Empty workspace initialization, 0 errors, 0 warnings.
- **Goal:** Maintain 0 errors and 0 warnings after initialization.

---

### Manual Verification Steps

1. [ ] Launch local Next.js development server.
2. [ ] Navigate to `/dashboard`. Verify sidebar renders with link items for Dashboard, Clients, Invoices, Settings.
3. [ ] Click sidebar links. Confirm page router transitions to `/clients`, `/invoices`, and `/settings` without full page reloads.
4. [ ] Toggle responsive preview in Chrome DevTools to a mobile width (375px). Confirm navigation collapses into a responsive bottom bar or modal drawer.

---

### Special Instructions

- **No Tailwind CSS:** Stick strictly to Vanilla CSS custom variables and CSS modules. Do not install any utility-class libraries.
- **Session Auth Bypass:** For this batch, skip session checks so pages can be viewed for layout verification.

---

### Required Report Format

The agent must submit a report in the canonical implementation report format defined in `15-ai-agent-operating-rules.md`.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation for Invoice Tracker | Product Owner |
