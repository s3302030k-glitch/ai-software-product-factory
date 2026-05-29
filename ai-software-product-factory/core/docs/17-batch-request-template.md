# 17 — Batch Request Template

> The standard format for requesting a unit of work from the Coding Agent.

---

## Purpose

Define a single, traceable unit of work that the Coding Agent can execute within its operating rules. Every batch request provides the agent with everything it needs to work safely and completely.

## Status

`Template` — Copy and fill in for each batch request.

---

## Batch Request

### Batch ID

```
[e.g., P2-B4 — Phase 2, Batch 4]
```

### Title

```
[Clear, descriptive title of what this batch delivers]
```

### Objective

_What does this batch accomplish? What will be true when it is complete?_

```
[2-3 sentences describing the goal and outcome.]
```

---

### Required Reading

_Documents the agent MUST read before starting this batch._

| Priority | Document | Reason |
|----------|----------|--------|
| 1 | `15-ai-agent-operating-rules.md` | Always required |
| 2 | `16-context-snapshot.md` | Current project state |
| 3 | _[Specific doc]_ | _[Why this doc is needed]_ |
| 4 | _[Specific doc]_ | _[Why this doc is needed]_ |

---

### Scope

_Exactly what is included in this batch. Be specific._

- [ ] _[Task 1 — e.g., Create the user list page at `/users`]_
- [ ] _[Task 2 — e.g., Implement GET /api/v1/users endpoint]_
- [ ] _[Task 3 — e.g., Add client-side search and filter to user list]_
- [ ] _[Task 4 — e.g., Write unit tests for user list API]_

### Out of Scope

_Explicitly what is NOT included. Prevents scope creep._

- _[e.g., User edit/create forms — those are in Batch P2-B5]_
- _[e.g., Role management — that is in Phase 3]_
- _[e.g., Pagination — deferred to polish phase]_
- _[e.g., Any database migrations — use existing schema]_

---

### Files Likely Involved

_Guide the agent to the right files. Not exhaustive — the agent may need others._

| File / Directory | Action | Notes |
|-----------------|--------|-------|
| `src/app/users/page.tsx` | Create | New page component |
| `src/app/api/users/route.ts` | Create | New API endpoint |
| `src/components/features/user-list.tsx` | Create | List component |
| `src/lib/queries/users.ts` | Create | Database queries |
| `tests/api/users.test.ts` | Create | Unit tests |

---

### Validation Rules & Commands

_Commands the agent must run after implementation to verify correctness._

```bash
# Build check
[e.g., npm run build]

# Type check
[e.g., npx tsc --noEmit]

# Lint
[e.g., npm run lint]

# Run tests
[e.g., npm test]

# Run specific tests for this batch
[e.g., npm test -- --grep "users"]
```

#### Baseline-Aware Validation Expectations

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

---

### Manual Verification Steps

_Steps for manual testing after automated checks pass._

1. [ ] _[e.g., Navigate to /users — page loads with list of users]_
2. [ ] _[e.g., Search for a user by name — results filter correctly]_
3. [ ] _[e.g., Filter by status — only matching users shown]_
4. [ ] _[e.g., With no users — empty state is displayed]_
5. [ ] _[e.g., With slow network — loading state is displayed]_
6. [ ] _[e.g., As non-admin user — only own team's users visible]_
7. [ ] _[e.g., Direct URL access as unauthorized user — redirect to dashboard]_

---

### Special Instructions (if any)

_Any batch-specific exceptions to the operating rules, or additional context._

```
[e.g., "This batch requires creating a new database migration for the 'users' table.
This is explicitly approved. Use format: YYYYMMDDHHMMSS_create_users_table."]

[e.g., "This batch modifies authorization for the /users endpoint.
This is explicitly approved. Follow the permission matrix in 04-user-roles.md."]

[Leave empty if no special instructions.]
```

---

### Required Report Format

_The agent must submit a report in the canonical implementation report format defined in `15-ai-agent-operating-rules.md`:_

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

## Scope

- This is a **template** — copy and fill in for each batch request.
- Each batch request is a self-contained work order.

## Out of Scope

- Defining the roadmap (see `11-development-roadmap.md`)
- Defining agent behavior rules (see `15-ai-agent-operating-rules.md`)

## Guardrails

- [ ] Batch scope must not exceed what `03-mvp-scope.md` allows
- [ ] Batch must not require the agent to violate operating rules (unless explicitly excepted)
- [ ] Every batch must have validation commands — no "implement and hope"
- [ ] Batch requests must be approved by the human product owner before assignment

## Related Files

- `11-development-roadmap.md` — Where batches are tracked
- `15-ai-agent-operating-rules.md` — Rules agents follow during execution
- `12-qa-test-plan.md` — QA standards for completed batches

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
