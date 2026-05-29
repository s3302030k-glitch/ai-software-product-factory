# 12 — QA Test Plan

> Defines the testing strategy, validation commands, checklists, and bug reporting format for the Invoice Tracker.

---

## Purpose

Establish a repeatable, comprehensive testing process that ensures every batch and release meets quality standards. This plan covers automated validation, manual QA walkthroughs, regression testing, and bug management.

## Status

`Approved`

---

## Validation Commands

Standard automated checks to run after every batch:

```bash
# Build verification
npm run build

# Code linting
npm run lint

# Run unit tests (especially business logic calculations)
npm test
```

### Baseline-Aware Validation Criteria

All checks must satisfy baseline-aware rules:

| Check | Required | Blocking | Notes |
|-------|----------|----------|-------|
| Build succeeds | Yes | Yes | Must produce valid bundle output |
| Lint passes | Yes | Yes | No new warnings or errors beyond current baseline |
| Unit tests pass | Yes | Yes | 100% test success rate required |

#### Baseline-Aware Validation Expectations
- **For new projects:** Zero errors required, zero warnings preferred.
- **For existing projects:** No new errors allowed. No new warnings beyond the documented baseline are allowed. A batch must not worsen the baseline unless explicitly approved.
- **Reporting:** If a baseline exists, the agent must report:
  1. The existing baseline.
  2. Whether this batch introduced new errors.
  3. Whether this batch introduced new warnings.
  4. Whether the baseline worsened.

---

## Manual QA Checklist

### Authentication & Authorization
- [ ] Direct access to `/dashboard`, `/clients`, or `/invoices` without logging in redirects to `/login`.
- [ ] Attempting to delete a client/invoice as Staff fails (button hidden in UI, API returns 403 Forbidden).
- [ ] Attempting to view `/settings` as Staff redirects to dashboard with 403 Forbidden alert.

### Client Management
- [ ] Create Client form validates that Name is present and Email has standard format.
- [ ] Saved client successfully shows up on `/clients` table list.
- [ ] Client details view shows associated invoice history (or empty state if new).

### Invoices & Item Entry
- [ ] Create Invoice page prevents submission with no line items.
- [ ] Due Date cannot be set earlier than Issue Date.
- [ ] Dynamic add/remove item buttons work correctly in DOM.
- [ ] Invoice Total aggregates accurately: `Sum of (Rate * Qty)`.
- [ ] Saved invoice defaults to status `Draft`.

### Payments & Status Transitions
- [ ] Logging a payment checks that payment amount > 0 and <= outstanding balance.
- [ ] Logged payments immediately recalculate `paid_amount` and `balance_due`.
- [ ] Invoice status transitions automatically:
  - Sent: Marked as sent.
  - Partially Paid: Payment < total.
  - Paid: Payments sum matches total.
  - Overdue: Sent/Partially Paid invoice whose due date has passed.

---

## Regression Checklist

### Core Sanity
- [ ] User session remains valid throughout CRUD operations.
- [ ] Navigation sidebar links click through correctly.
- [ ] No DB relational errors (e.g. deleting an invoice removes payments cascade, but client cannot be deleted if active invoices exist).

---

## QA Report Format

Every QA verification run must produce a report in the following format:

```markdown
# QA Report: [Batch ID or Release ID]

## 1. Scope Tested
- [List of features tested]

## 2. Automated Validation
| Check | Result | Notes |
|---|---|---|
| npm run build | passed/failed | |
| npm run lint | passed/failed | |
| npm test | passed/failed | |

## 3. Manual Tests
| Test | Result | Notes |
|---|---|---|
| User login | passed/failed | |
| Client CRUD | passed/failed | |
| Invoice CRUD | passed/failed | |
| Payments & status calculations | passed/failed | |

## 4. Baseline Notes
- Existing baseline:
- New errors introduced: Yes/No
- New warnings introduced: Yes/No
- Baseline worsened: Yes/No

## 5. Bugs Found
| Severity | Issue | Evidence | Recommendation |
|---|---|---|---|
| Critical/Major/Minor/Nit | [issue description] | [link or logs] | [fix recommendation] |

## 6. Recommendation
Pass / Pass with notes / Needs fixes / Blocked
```

---

## Bug Report Format

```markdown
## Bug Report: BUG-XXX

### Title
[Short, descriptive title]

### Severity
`Critical` | `Major` | `Minor` | `Cosmetic`

### Found In
- Batch: [Batch ID]
- Page/Feature: [Where it occurs]

### Steps to Reproduce
1. [Step 1]
2. [Step 2]

### Expected Behavior
[What should happen]

### Actual Behavior
[What actually happens]
```

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation for Invoice Tracker | Product Owner |
