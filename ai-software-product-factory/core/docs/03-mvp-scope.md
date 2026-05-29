# 03 — MVP Scope

> Defines what is in and out of the first release using MoSCoW prioritization.

---

## Purpose

Clearly define the boundaries of version 1. This document prevents scope creep by providing a definitive list of what will and will not be built. Every feature, page, and capability must be traceable to this document.

## Status

`Draft` | `In Review` | `Approved` | `Locked`

> Once locked, changes require a decision log entry and human approval.

---

## Must Have

_Features that are essential for the product to function. Without these, there is no product._

| # | Feature | Related Flow | Related Pages | Notes |
|---|---------|-------------|---------------|-------|
| 1 | _e.g., User registration and login_ | _Auth flow_ | _Login, Register_ | |
| 2 | _e.g., Create/edit/delete [entity]_ | _CRUD flow_ | _List, Form_ | |
| 3 | | | | |

---

## Should Have

_Features that are important but the product can launch without them. Plan to add in a fast follow-up._

| # | Feature | Related Flow | Related Pages | Notes |
|---|---------|-------------|---------------|-------|
| 1 | _e.g., Search and filtering_ | _Search flow_ | _List page_ | |
| 2 | _e.g., Email notifications_ | _Notification flow_ | _Settings_ | |
| 3 | | | | |

---

## Could Have

_Nice-to-have features. Build only if time and budget allow._

| # | Feature | Related Flow | Related Pages | Notes |
|---|---------|-------------|---------------|-------|
| 1 | _e.g., Dashboard with charts_ | _Dashboard flow_ | _Dashboard_ | |
| 2 | _e.g., Export to CSV_ | _Export flow_ | _List page_ | |
| 3 | | | | |

---

## Won't Have Now

_Explicitly out of scope for version 1. Listing these prevents ambiguity and scope creep._

| # | Feature | Reason | Planned For |
|---|---------|--------|-------------|
| 1 | _e.g., Mobile native app_ | _Web-first strategy_ | _v2_ |
| 2 | _e.g., Multi-language support_ | _Single market launch_ | _v2_ |
| 3 | _e.g., Third-party integrations_ | _Stabilize core first_ | _v2+_ |
| 4 | | | |

---

## MVP Completion Criteria

_The MVP is considered complete when ALL of the following are true:_

- [ ] All "Must Have" features are implemented and functional
- [ ] All "Must Have" features pass QA validation
- [ ] No critical or blocking bugs remain open
- [ ] Security model is implemented as specified in `10-security-model.md`
- [ ] All user roles can complete their primary flows
- [ ] Release checklist (`13-release-checklist.md`) passes
- [ ] Human product owner has signed off

### Acceptance Verification

| Check | Method | Pass Criteria |
|-------|--------|---------------|
| All Must Have features work | Manual QA walkthrough | 100% pass |
| No critical bugs | Bug tracker review | 0 critical |
| Security baseline met | Security checklist review | All items pass |
| Performance acceptable | Manual testing / Lighthouse | No blocking issues |
| Data integrity | Test data validation | No corruption or loss |

---

## Scope

- This document defines the **feature boundary** for version 1.
- Features listed here must align with the product brief.
- Each feature should be traceable to a user flow and page spec.

## Out of Scope

- Detailed specifications (see `06-pages-spec.md`)
- Technical implementation (see `08-architecture.md`)
- Timeline and delivery order (see `11-development-roadmap.md`)

## Guardrails

- [ ] No AI agent may implement features not listed in "Must Have" or "Should Have" without explicit approval
- [ ] "Won't Have Now" items must be actively rejected if they appear in batch requests
- [ ] Adding new "Must Have" items requires human approval and a decision log entry
- [ ] Moving items between categories requires human approval

## Related Files

- `01-product-brief.md` — Product definition this scope derives from
- `05-user-flows.md` — Detailed flows for each feature
- `06-pages-spec.md` — Page specifications for each feature
- `11-development-roadmap.md` — Delivery schedule for scoped features
- `14-decision-log.md` — Record of scope changes

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
