# 11 — Development Roadmap

> Organizes delivery into phases and batches with clear tracking and validation.

---

## Purpose

Break down the MVP into ordered phases and actionable batches. Each batch is a discrete unit of work that can be assigned to the Coding Agent, validated, reviewed, and tracked.

## Status

`Draft` | `In Progress` | `Complete`

---

## Phase Status Values

| Status | Meaning |
|--------|---------|
| `Not Started` | Phase has not begun |
| `In Progress` | At least one batch in this phase is active |
| `Blocked` | Phase cannot proceed (dependency or issue) |
| `Complete` | All batches in phase pass validation |
| `Skipped` | Phase was determined unnecessary |

## Batch Status Values

| Status | Meaning |
|--------|---------|
| `Pending` | Not yet started |
| `In Progress` | Currently being implemented |
| `In Review` | Implementation complete, under review |
| `Changes Requested` | Review found issues, needs rework |
| `Complete` | Implemented, validated, and reviewed |
| `Blocked` | Cannot proceed due to dependency or issue |
| `Skipped` | Determined unnecessary |

---

## Roadmap Overview

_High-level view of all phases._

| Phase | Name | Description | Batches | Status |
|-------|------|-------------|---------|--------|
| 1 | _Foundation_ | _Project setup, auth, base layout_ | _3_ | `Not Started` |
| 2 | _Core Features_ | _Primary CRUD flows_ | _4_ | `Not Started` |
| 3 | _Business Logic_ | _Calculations, rules, workflows_ | _3_ | `Not Started` |
| 4 | _Polish_ | _UX improvements, error handling_ | _2_ | `Not Started` |
| 5 | _QA & Release_ | _Testing, fixes, deployment_ | _2_ | `Not Started` |

---

## Phase Template

_Copy this template for each phase._

---

### Phase [N]: [Phase Name]

**Status:** `Not Started` | `In Progress` | `Complete`
**Objective:** _What this phase accomplishes_
**Dependencies:** _What must be complete before this phase starts_
**Estimated Batches:** _N_

#### Batches

| Batch | Title | Status | Notes |
|-------|-------|--------|-------|
| P[N]-B1 | _Batch title_ | `Pending` | |
| P[N]-B2 | _Batch title_ | `Pending` | |
| P[N]-B3 | _Batch title_ | `Pending` | |

#### Phase Completion Criteria

- [ ] All batches are `Complete`
- [ ] All validation commands satisfy baseline-aware rules (no new errors, and no new warnings beyond baseline)
- [ ] No open critical/blocking issues
- [ ] Phase reviewed by human product owner

---

## Batch Template

_Copy this template for each batch. For the full batch request document, use `17-batch-request-template.md`._

---

### Batch [PX-BY]: [Batch Title]

**Status:** `Pending`
**Phase:** _Phase N_
**Objective:** _What this batch delivers_
**Dependencies:** _Previous batches that must be complete_

#### Scope

_What is included in this batch:_

- [ ] _Task 1_
- [ ] _Task 2_
- [ ] _Task 3_

#### Out of Scope

_What is explicitly NOT included:_

- _Item 1_
- _Item 2_

#### Files Likely Involved

- `path/to/file1`
- `path/to/file2`

#### Validation Commands

```bash
# Commands to verify this batch works correctly
[command 1]
[command 2]
```

#### Manual Verification

- [ ] _Verify step 1_
- [ ] _Verify step 2_

#### Completion Criteria

- [ ] All scope items implemented
- [ ] All validation commands satisfy baseline-aware rules (no new errors; no new warnings beyond baseline; baseline must not worsen unless explicitly approved)
- [ ] No regressions in existing functionality
- [ ] Implementation report submitted

---

## Validation Rules & Commands

_Standard validation commands to run after any batch._

```bash
# Type checking (if applicable)
[e.g., npx tsc --noEmit]

# Linting
[e.g., npm run lint]

# Unit tests
[e.g., npm test]

# Build check
[e.g., npm run build]

# Security audit
[e.g., npm audit]
```

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

---

## Report Format

_After each batch, the Coding Agent must submit a report in the canonical implementation report format defined in `15-ai-agent-operating-rules.md`:_

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

- This document defines **the delivery plan and tracking system**.
- It determines the order of work, not the content of work (see feature docs).

## Out of Scope

- Feature specifications (see `06-pages-spec.md`)
- Technical implementation details
- QA test cases (see `12-qa-test-plan.md`)

## Guardrails

- [ ] Batches must be small enough to complete in a single AI session
- [ ] Each batch must have clear validation commands
- [ ] Phase dependencies must be respected — no skipping ahead
- [ ] AI agents must not self-assign batches — humans approve batch starts

## Related Files

- `03-mvp-scope.md` — Features being delivered across these phases
- `15-ai-agent-operating-rules.md` — Rules agents follow during batch execution
- `17-batch-request-template.md` — Detailed template for requesting batch work
- `12-qa-test-plan.md` — Testing requirements for completed batches

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
