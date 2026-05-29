# 12 — QA Test Plan

> Defines the testing strategy, validation commands, checklists, and bug reporting format.

---

## Purpose

Establish a repeatable, comprehensive testing process that ensures every batch and release meets quality standards. This plan covers automated validation, manual QA walkthroughs, regression testing, and bug management.

## Status

`Draft` | `In Progress` | `Active`

---

## Validation Commands

_Standard automated checks to run after every batch._

### Build & Type Checking

```bash
# Build the project
[e.g., npm run build]

# Type checking (TypeScript)
[e.g., npx tsc --noEmit]

# Linting
[e.g., npm run lint]
```

### Automated Tests

```bash
# Unit tests
[e.g., npm test]

# Integration tests
[e.g., npm run test:integration]

# End-to-end tests
[e.g., npm run test:e2e]
```

### Security Checks

```bash
# Dependency audit
[e.g., npm audit]

# Known vulnerability scan
[e.g., npx audit-ci --moderate]
```

### Baseline-Aware Validation Criteria

All checks must satisfy baseline-aware rules:

| Check | Required | Blocking | Notes |
|-------|----------|----------|-------|
| Build succeeds | Yes | Yes | |
| Type check passes | Yes | Yes | No new errors allowed |
| Lint passes | Yes | Yes | No new warnings/errors beyond baseline |
| Unit tests pass | Yes | Yes | |
| Security audit | Yes | Only critical/high | |

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

## Manual QA Checklist

_Run through this checklist for every batch that touches UI or user-facing functionality._

### Authentication

- [ ] Login with valid credentials works
- [ ] Login with invalid credentials shows error
- [ ] Logout clears session
- [ ] Protected pages redirect to login when unauthenticated
- [ ] Session expiry is handled gracefully

### Authorization

- [ ] Each role can only access permitted pages
- [ ] Each role can only perform permitted actions
- [ ] Direct URL access to unauthorized pages is blocked
- [ ] Data scoping is enforced (users see only their data)

### CRUD Operations

- [ ] Create: Form submits, record appears in list
- [ ] Read: List page shows correct data, detail page loads
- [ ] Update: Edit form pre-fills, changes persist
- [ ] Delete: Confirmation dialog, record removed from list
- [ ] Validation: Required fields enforced, error messages shown

### UI / UX

- [ ] Loading states display while data loads
- [ ] Empty states display when no data exists
- [ ] Error states display when operations fail
- [ ] Forms clear or redirect after successful submission
- [ ] Navigation works correctly
- [ ] Responsive design works on mobile/tablet/desktop
- [ ] No broken images, icons, or assets

### Edge Cases

- [ ] Very long text inputs handled (truncation or scroll)
- [ ] Special characters in inputs handled
- [ ] Rapid repeated submissions handled (no duplicates)
- [ ] Browser back/forward navigation doesn't break state
- [ ] Multiple browser tabs don't cause conflicts

---

## Regression Checklist

_After any change, verify that previously working features still work._

### Core Flows

- [ ] User can register (if applicable)
- [ ] User can log in
- [ ] User can complete primary flow
- [ ] User can log out
- [ ] Navigation and routing work

### Data Integrity

- [ ] Existing data is not corrupted
- [ ] Relationships between entities are intact
- [ ] Soft-deleted records are hidden but not lost
- [ ] Timestamps are updating correctly

### Permissions

- [ ] Role-based access still works
- [ ] Data scoping still works
- [ ] No new unauthorized access paths

### Performance

- [ ] Page load times have not degraded
- [ ] No new console errors
- [ ] No memory leaks in long-running pages

---

## QA Report Format

_Every QA validation run must produce a report in this exact format:_

```markdown
# QA Report: [Batch ID or Release ID]

## 1. Scope Tested

- [scope]

## 2. Commands / Checks Run

| Check | Result | Notes |
|---|---|---|
| [check] | passed/failed/not available | [notes] |

## 3. Manual Tests

| Test | Result | Notes |
|---|---|---|
| [test] | passed/failed/not performed/not required | [notes] |

## 4. Regression Checks

| Area | Result | Notes |
|---|---|---|
| [area] | passed/failed/not performed/not required | [notes] |

## 5. Baseline Notes

- Existing baseline:
- New errors introduced: Yes/No
- New warnings introduced: Yes/No
- Baseline worsened: Yes/No
- Notes:

## 6. Bugs Found

| Severity | Issue | Evidence | Recommendation |
|---|---|---|---|
| Critical/Major/Minor/Nit | [issue] | [evidence] | [recommendation] |

## 7. Recommendation

Pass / Pass with notes / Needs fixes / Blocked
```

---

## Bug Report Format

_Use this format when reporting bugs found during QA._

```markdown
## Bug Report

### Bug ID
BUG-XXX

### Title
[Short, descriptive title]

### Severity
`Critical` | `Major` | `Minor` | `Cosmetic`

### Found In
- Batch: [Batch ID]
- Page/Feature: [Where it occurs]
- Environment: [Dev / Staging / Production]

### Steps to Reproduce
1. [Step 1]
2. [Step 2]
3. [Step 3]

### Expected Behavior
[What should happen]

### Actual Behavior
[What actually happens]

### Screenshots / Recordings
[Attach if available]

### Browser / Device
- Browser: [e.g., Chrome 120]
- OS: [e.g., Windows 11]
- Screen size: [e.g., 1920x1080]

### Additional Context
[Any other relevant information]

### Suggested Fix (if known)
[Optional — point to likely cause]
```

### Severity Definitions

| Severity | Definition | Response Time |
|----------|-----------|---------------|
| **Critical** | Product is unusable, data loss, security breach | Immediate fix required |
| **Major** | Core feature broken, no workaround | Fix before release |
| **Minor** | Feature works but with issues, workaround exists | Fix when possible |
| **Cosmetic** | Visual or text issue, no functional impact | Fix in polish phase |

---

## Test Coverage Goals

| Category | Coverage Target | Notes |
|----------|----------------|-------|
| Unit tests | _e.g., 80% of business logic_ | _Focus on calculations and rules_ |
| Integration tests | _e.g., All API endpoints_ | _Happy path + error cases_ |
| E2E tests | _e.g., All Must Have flows_ | _Critical user journeys_ |
| Manual QA | _100% of Must Have features_ | _Before every release_ |

---

## Scope

- This document defines **how to test the product**.
- It applies to all phases and batches.

## Out of Scope

- Writing specific test cases (done per batch)
- Performance testing methodology
- Load testing

## Guardrails

- [ ] All validation commands satisfy baseline-aware rules (no new errors, and no new warnings beyond baseline) before a batch is considered complete
- [ ] Critical and major bugs block release
- [ ] QA checklist must be completed for every UI-touching batch
- [ ] Regression checklist must be run before every release

## Related Files

- `11-development-roadmap.md` — Batches that need testing
- `13-release-checklist.md` — Release process that includes QA sign-off
- `15-ai-agent-operating-rules.md` — Agent rules for running validation

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
