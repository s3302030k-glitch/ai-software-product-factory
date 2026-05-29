# 15 — AI Agent Operating Rules

> Mandatory rules and constraints that all AI agents must follow during development of the Invoice Tracker.

---

## Purpose

Establish clear, enforceable boundaries for AI agent behavior. These rules prevent scope creep, unauthorized changes, security violations, and uncontrolled modifications to the codebase.

## Status

`Active` — These rules apply at all times during development.

---

## Fundamental Rule

> **The coding agent is NOT the product owner.**
>
> AI agents propose. Humans decide. Agents execute within approved scope. They do not make product decisions, architectural choices, or scope expansions independently.

---

## Required Startup Reading

Every AI session must begin by reading these documents in order:

1. `16-context-snapshot.md` — Orientation only (never treated as source of truth/authority)
2. `00-document-priority.md` — Authority and conflict resolution rules
3. `15-ai-agent-operating-rules.md` — Mandatory agent behavior constraints (this document)
4. `01-product-brief.md` — Product context, target users, and goals for Invoice Tracker
5. `03-mvp-scope.md` — MVP scope boundaries (MoSCoW items)
6. `11-development-roadmap.md` — Phase, batch sequence, and tracking/dependencies
7. The specific approved batch request (representing the work order)
8. Any task-specific documents listed in the batch request's Required Reading section

**If an agent cannot access the required documents, it must stop and report the issue.**

---

## Scope Rules

### DO
- ✅ Work **only** on the approved batch.
- ✅ Implement **exactly** what the batch request specifies.
- ✅ Follow the architecture defined in `08-architecture.md` (Next.js, vanilla styling).
- ✅ Follow the data model defined in `07-data-model.md` (USD only).
- ✅ Follow the API contracts defined in `09-api-design.md`.
- ✅ Reference page specs in `06-pages-spec.md` for UI work.

### DO NOT
- ❌ Add features not in the batch scope (e.g. email alerts, PDF exports).
- ❌ "Improve" or refactor code outside the batch scope.
- ❌ Add new dependencies without explicit approval.
- ❌ Create new pages or routes not in the specs.
- ❌ Add new API endpoints not in the API design doc.
- ❌ Add new database entities or fields not in the data model.
- ❌ Anticipate future requirements — build only what is asked.

---

## Migration Rules

1. **Do not create database migrations** unless the batch request explicitly says to.
2. **Do not modify existing migrations** — ever.
3. **Do not run destructive migrations** (DROP TABLE, DROP COLUMN) without human approval.
4. **Migration naming**: Use timestamp prefix format `YYYYMMDDHHMMSS_description`.
5. **All migrations must be reversible** — include both up and down operations.

---

## Security Rules

1. **Do not modify authentication** unless the batch explicitly scopes it.
2. **Do not modify authorization / role permissions** unless the batch explicitly scopes it.
3. **Do not change data scoping rules** (shared single-tenant layout).
4. **Do not expose new API endpoints** without verifying session validation middleware is active.
5. **Do not log sensitive data** (passwords, session cookies, connection strings).
6. **Do not disable security features** for convenience.
7. **Do not store secrets in code** — use environment variables.

---

## Business Calculation Rules

1. **Do not modify business calculations** unless the batch explicitly scopes it.
2. **Do not change data validation rules** unless the batch explicitly scopes it.
3. **Do not change entity relationships** unless the batch explicitly scopes it.
4. **Financial calculations** must follow the exact formulas specified in `07-data-model.md`:
   - `invoice_total = sum(rate * quantity)`
   - `paid_amount = sum(payment_amount)`
   - `balance_due = invoice_total - paid_amount`
   - Payments cannot exceed balance due.
   - Status is derived dynamically.
5. **Rounding rules**: Currency rounding must be done to exactly two decimal places.
6. **When in doubt about a calculation, stop and ask** — do not guess.

---

## Validation Rules & Baseline

After every batch, run all checks (`npm run build`, `npm run lint`, `npm test`).

### Baseline-Aware Validation Expectations
- **For new projects:** Zero errors required, zero warnings preferred.
- **For existing projects:** No new errors allowed. No new warnings beyond the documented baseline are allowed. A batch must not worsen the baseline unless explicitly approved.
- **Reporting:** If a baseline exists, the agent must report:
  1. The existing baseline.
  2. Whether this batch introduced new errors.
  3. Whether this batch introduced new warnings.
  4. Whether the baseline worsened.

---

## Required Implementation Report Format

Every batch must conclude with a report in this exact format:

```markdown
# Implementation Report: [Batch ID]

## 1. Commands Run

| Command | Purpose | Result |
|---|---|---|
| `[command]` | [purpose] | passed/failed/not available |

## 2. Files Changed

| File | Change Type | Summary |
|---|---|---|
| `[path]` | Added/Modified/Removed | [summary] |

## 3. Changes Made

- [change 1]
- [change 2]

## 4. Guardrails Confirmed

- Scope stayed within the approved batch.
- No out-of-scope features were added.
- No migrations were added unless explicitly scoped.
- No auth/security/permission rules were changed unless explicitly scoped.
- No business calculations were changed unless explicitly scoped.
- No unrelated UI behavior changed.
- No dependencies were added unless explicitly scoped.
- No broad architecture changes were made unless explicitly scoped.

## 5. Validation Results

| Check | Result | Notes |
|---|---|---|
| Build | passed/failed/not available | [notes] |
| Typecheck | passed/failed/not available | [notes] |
| Lint | passed/failed/not available | [notes] |
| Tests | passed/failed/not available | [notes] |
| Diff check | passed/failed/not available | [notes] |
| Manual verification | passed/failed/not performed/not required | [notes] |

## 6. Baseline Notes

- Existing baseline:
- New errors introduced: Yes/No
- New warnings introduced: Yes/No
- Baseline worsened: Yes/No
- Notes:

## 7. Risks / Follow-Ups

- [risk or follow-up]

## 8. Final Status

Ready for review / Blocked / Needs owner decision / Needs manual verification

## 9. Final Git Status

TEXT BLOCK:
[git status --short]
[git diff --stat]
```

---

## Stop Conditions

The agent must STOP immediately and report if any of the following occur:

1. **Scope ambiguity** — The batch request is unclear about what to implement.
2. **Document conflict** — Two core documents contradict each other.
3. **Missing specification** — A required page, flow, or entity is not documented.
4. **Security concern** — The requested change could introduce a security vulnerability.
5. **Data integrity risk** — The change could corrupt or lose existing data.
6. **Dependency missing** — The batch depends on another batch that is not complete.
7. **Validation failure** — Build, lint, or tests fail and cannot be fixed within scope.
8. **Architecture violation** — The requested change would violate architectural principles.
9. **Scope expansion** — The batch would require changes outside its defined scope.
10. **Business logic uncertainty** — The agent is unsure about a calculation, rule, or formula.

### Stop Behavior

When a stop condition is triggered:
1. **Stop** all work immediately.
2. **Do not attempt to resolve** the issue independently.
3. **Report** the stop condition with exact details of the issue.
4. **Wait** for human guidance.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial version for Invoice Tracker | Product Owner |
