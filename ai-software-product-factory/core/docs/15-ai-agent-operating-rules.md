# 15 — AI Agent Operating Rules

> Mandatory rules and constraints that all AI agents must follow during development.

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

_Every AI agent session must begin by reading these documents in order:_

1. `16-context-snapshot.md` — Orientation only (never treated as source of truth/authority)
2. `00-document-priority.md` — Authority and conflict resolution rules
3. `15-ai-agent-operating-rules.md` — Mandatory agent behavior constraints (this document)
4. `01-product-brief.md` — Product context, target users, and goals
5. `03-mvp-scope.md` — MVP scope boundaries (MoSCoW items)
6. `11-development-roadmap.md` — Phase, batch sequence, and tracking/dependencies
7. The specific approved batch request (representing the work order)
8. Any task-specific documents listed in the batch request's Required Reading section

**If an agent cannot access the required documents, it must stop and report the issue.**

### Clarifications on Reading and Conflict Resolution
- **Context Snapshot** is for orientation only and has no authority.
- If any conflict between documents appears during reading or execution, the agent must refer to the standing authority defined in `00-document-priority.md`.
- If the conflict affects the current batch or task, the agent must **STOP** immediately and report the conflict to the human product owner.

---

## Scope Rules

### DO

- ✅ Work **only** on the approved batch
- ✅ Implement **exactly** what the batch request specifies
- ✅ Follow the architecture defined in `08-architecture.md`
- ✅ Follow the data model defined in `07-data-model.md`
- ✅ Follow the API contracts defined in `09-api-design.md`
- ✅ Reference page specs in `06-pages-spec.md` for UI work

### DO NOT

- ❌ Add features not in the batch scope
- ❌ "Improve" or refactor code outside the batch scope
- ❌ Add new dependencies without explicit approval
- ❌ Create new pages or routes not in the specs
- ❌ Add new API endpoints not in the API design doc
- ❌ Add new database entities or fields not in the data model
- ❌ Anticipate future requirements — build only what is asked

### When In Doubt

If the batch request is ambiguous or incomplete:

1. **Stop** the current task
2. **Document** the ambiguity
3. **Ask** for clarification
4. **Do not** guess or make assumptions

---

## Migration Rules

1. **Do not create database migrations** unless the batch request explicitly says to
2. **Do not modify existing migrations** — ever
3. **Do not run destructive migrations** (DROP TABLE, DROP COLUMN) without human approval
4. **Migration naming**: Use timestamp prefix format `YYYYMMDDHHMMSS_description`
5. **All migrations must be reversible** — include both up and down operations
6. **Separate schema migrations from data migrations**

---

## Security Rules

1. **Do not modify authentication** unless the batch explicitly scopes it
2. **Do not modify authorization / role permissions** unless the batch explicitly scopes it
3. **Do not change data scoping rules** unless the batch explicitly scopes it
4. **Do not expose new API endpoints** without verifying authorization is configured
5. **Do not log sensitive data** (passwords, tokens, PII)
6. **Do not disable security features** for convenience (e.g., turning off CORS, removing CSRF)
7. **Do not store secrets in code** — use environment variables
8. **Follow `10-security-model.md`** at all times

---

## UI Rules

1. **Follow the page spec** in `06-pages-spec.md` — do not invent UI
2. **Implement all states**: loading, empty, error, and populated
3. **Follow the existing design system** — do not introduce new patterns
4. **Ensure responsive design** — mobile, tablet, desktop
5. **Do not add new pages or routes** unless specified in the batch
6. **Form validation** must match the validation rules in the page spec
7. **Permissions on UI** must match `04-user-roles.md`

---

## Data & Calculation Rules

1. **Do not modify business calculations** unless the batch explicitly scopes it
2. **Do not change data validation rules** unless the batch explicitly scopes it
3. **Do not change entity relationships** unless the batch explicitly scopes it
4. **Financial calculations** must follow the exact formulas specified in docs
5. **Rounding rules** must follow the specified policy (do not improvise)
6. **When in doubt about a calculation, stop and ask** — do not guess

---

## Batch Size Rules

1. **One batch at a time** — do not combine batches
2. **Complete batches fully** — do not partially implement
3. **If a batch is too large**, report it and suggest splitting — do not silently reduce scope
4. **Dependencies first** — if the batch depends on another batch, verify it is complete before starting
5. **No forward references** — do not implement code that depends on a future batch

---

## Validation Rules

_After every batch, run ALL of the following:_

1. **Build check** — The project must build without errors
2. **Type check** — No new type errors (if using TypeScript)
3. **Lint check** — No new lint errors or warnings beyond the baseline
4. **Test check** — All existing tests pass, new tests pass
5. **Manual verification** — Complete the manual verification steps from the batch request

### Baseline-Aware Validation Expectations

For new projects:
- zero errors are required
- zero warnings are preferred

For existing projects:
- no new errors are allowed
- no new warnings beyond the documented baseline are allowed
- existing baseline warnings/errors must be reported separately
- a batch must not worsen the baseline unless explicitly approved

If a project has a known validation baseline, the agent must report:
- the existing baseline
- whether this batch introduced new errors
- whether this batch introduced new warnings
- whether the baseline worsened

### Validation Is Not Optional

- If any validation step fails, the batch is **not complete**
- Report the failure in the implementation report
- Do not mark the batch as complete with failing validations

---

## Required Implementation Report Format

_Every batch must conclude with a report in this exact format:_

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

_The agent must STOP immediately and report if any of the following occur:_

1. **Scope ambiguity** — The batch request is unclear about what to implement
2. **Document conflict** — Two core documents contradict each other
3. **Missing specification** — A required page, flow, or entity is not documented
4. **Security concern** — The requested change could introduce a security vulnerability
5. **Data integrity risk** — The change could corrupt or lose existing data
6. **Dependency missing** — The batch depends on another batch that is not complete
7. **Validation failure** — Build, type check, lint, or tests fail and cannot be fixed within scope
8. **Architecture violation** — The requested change would violate architectural principles
9. **Scope expansion** — The batch would require changes outside its defined scope
10. **Business logic uncertainty** — The agent is unsure about a calculation, rule, or formula

### Stop Behavior

When a stop condition is triggered:

1. **Stop** all work immediately
2. **Do not attempt to resolve** the issue independently
3. **Report** the stop condition with:
   - Which condition was triggered
   - Exact details of the issue
   - What documents are in conflict (if applicable)
   - What work was completed before stopping
4. **Wait** for human guidance

---

## Scope

- This document has the highest authority for AI agent behavior constraints.
- It overrides conflicting instructions in batch requests or lower-authority docs for agent conduct.
- Authority and conflict rules are defined in `00-document-priority.md`.

## Out of Scope

- Agent role definitions (see individual prompts in `core/prompts/`)
- Product decisions (see `01-product-brief.md`)
- Technical architecture (see `08-architecture.md`)

## Guardrails

- [ ] These rules override any conflicting instructions in batch requests
- [ ] Agents cannot self-exempt from these rules
- [ ] Humans can temporarily relax specific rules via explicit batch instructions, but must document the exception

## Related Files

- `00-document-priority.md` — This document's authority level
- `16-context-snapshot.md` — Agent orientation document
- `17-batch-request-template.md` — Template for requesting agent work
- `11-development-roadmap.md` — Batch tracking and report format

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
