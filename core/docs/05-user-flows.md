# 05 — User Flows

> Step-by-step descriptions of how users interact with the product to accomplish their goals.

---

## Purpose

Define every significant user interaction as a traceable flow with preconditions, main path, edge cases, error states, and rollback options. User flows bridge the gap between MVP scope (what) and page specs (how).

## Status

`Draft` | `In Progress` | `Complete`

---

## Flow Template

_Copy this template for each user flow._

---

### Flow: [Flow Name]

**ID:** `FLOW-XXX`
**Priority:** Must Have | Should Have | Could Have
**Actor:** [Role name from `04-user-roles.md`]
**Goal:** _What the user is trying to accomplish_
**Trigger:** _What initiates this flow (e.g., clicks button, navigates to page, receives notification)_

#### Preconditions

_What must be true before this flow can begin?_

- [ ] User is authenticated
- [ ] User has role: [role]
- [ ] [Entity] exists / does not exist
- [ ] _Other preconditions..._

#### Main Path (Happy Path)

| Step | Actor | Action | System Response | Page / Component |
|------|-------|--------|----------------|-----------------|
| 1 | User | _What user does_ | _What system does_ | _Where this happens_ |
| 2 | System | _Automatic action_ | _Result_ | |
| 3 | User | _Next action_ | _System response_ | |
| 4 | | | | |

#### Postconditions

_What is true after the flow completes successfully?_

- [ ] [Entity] is created/updated/deleted
- [ ] User sees confirmation
- [ ] _Other outcomes..._

#### Edge Cases

| # | Scenario | Expected Behavior |
|---|---------|-------------------|
| 1 | _e.g., User submits form with empty required fields_ | _Inline validation errors shown_ |
| 2 | _e.g., User navigates away mid-flow_ | _Draft is auto-saved / data is discarded_ |
| 3 | _e.g., Concurrent edit by another user_ | _Last-write-wins / conflict warning shown_ |

#### Error States

| # | Error Condition | User Sees | System Does | Recovery |
|---|----------------|-----------|-------------|----------|
| 1 | _e.g., Network failure during save_ | _Error toast: "Save failed, please retry"_ | _Log error, do not persist_ | _Retry button_ |
| 2 | _e.g., Server validation rejects data_ | _Inline error on affected fields_ | _Return 422 with field errors_ | _Fix and resubmit_ |
| 3 | _e.g., Unauthorized action_ | _403 page or redirect_ | _Log unauthorized attempt_ | _Contact admin_ |

#### Rollback / Undo Notes

_Can this action be undone? What happens if the user wants to reverse the outcome?_

| Action | Reversible? | Method | Time Limit |
|--------|-------------|--------|------------|
| _e.g., Create record_ | Yes | _Delete button_ | _No limit_ |
| _e.g., Delete record_ | Yes / No | _Soft delete (30 days) / Hard delete_ | _30 days_ |
| _e.g., Submit for approval_ | No | _N/A — contact admin_ | _N/A_ |

#### Analytics Events (if relevant)

_What events should be tracked for analytics or audit purposes?_

| Event Name | Trigger | Properties |
|-----------|---------|------------|
| `flow_started` | _User begins flow_ | `{ user_id, timestamp }` |
| `flow_completed` | _Flow succeeds_ | `{ user_id, entity_id, duration }` |
| `flow_error` | _Error occurs_ | `{ user_id, error_type, step }` |

---

## Flow Index

_List all flows defined in this document for quick navigation._

| Flow ID | Flow Name | Actor | Priority | Status |
|---------|-----------|-------|----------|--------|
| FLOW-001 | _e.g., User Registration_ | _Guest_ | Must Have | `Draft` |
| FLOW-002 | _e.g., Create [Entity]_ | _User_ | Must Have | `Draft` |
| FLOW-003 | | | | |

---

## Scope

- This document defines **how users interact with the product step by step**.
- Each flow must map to at least one feature in `03-mvp-scope.md`.

## Out of Scope

- Page layout and visual design (see `06-pages-spec.md`)
- API contracts (see `09-api-design.md`)
- Data model details (see `07-data-model.md`)

## Guardrails

- [ ] Every "Must Have" feature must have at least one flow defined
- [ ] Every flow must specify error states — no "happy path only" flows
- [ ] Flows must reference roles from `04-user-roles.md`
- [ ] AI agents must implement flows exactly as specified — no additions

## Related Files

- `03-mvp-scope.md` — Features that flows implement
- `04-user-roles.md` — Roles referenced as actors in flows
- `06-pages-spec.md` — Page-level detail for each step
- `09-api-design.md` — API calls triggered by flows

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
