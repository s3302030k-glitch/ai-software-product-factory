# 16 — Context Snapshot

> An orientation summary of the current project state. **This is NOT the source of truth.**

---

## ⚠️ Important

This document exists to help AI agents quickly understand the current state of the project. It is a **convenience summary** that is derived from other documents.

**Rules:**
- This document **must never override** any other document
- If this snapshot conflicts with a core doc, the core doc is correct
- Read this first for orientation, then read specific documents for authoritative detail
- See `00-document-priority.md` for the full priority hierarchy

---

## Product Summary

| Attribute | Value |
|-----------|-------|
| **Product Name** | _[From `01-product-brief.md`]_ |
| **Product Type** | _[e.g., Web Application]_ |
| **Target Users** | _[Brief list of user types]_ |
| **Core Purpose** | _[One sentence]_ |
| **Status** | `Planning` / `In Development` / `QA` / `Released` |

---

## Current Phase

| Attribute | Value |
|-----------|-------|
| **Phase** | _[e.g., Phase 2: Core Features]_ |
| **Phase Status** | _[e.g., In Progress]_ |
| **Started** | _YYYY-MM-DD_ |
| **Estimated Completion** | _YYYY-MM-DD_ |

---

## Last Completed Batch

| Attribute | Value |
|-----------|-------|
| **Batch ID** | _[e.g., P2-B3]_ |
| **Title** | _[Batch title]_ |
| **Completed On** | _YYYY-MM-DD_ |
| **Review Status** | _Accepted / Changes Requested_ |
| **Key Changes** | _[Brief summary of what was built]_ |

---

## Next Batch

| Attribute | Value |
|-----------|-------|
| **Batch ID** | _[e.g., P2-B4]_ |
| **Title** | _[Batch title]_ |
| **Objective** | _[What this batch will deliver]_ |
| **Status** | _Pending / Ready_ |
| **Blocked By** | _[Nothing / Batch X / Decision Y]_ |

---

## Tech Stack Summary

_Quick reference — see `08-architecture.md` for full details._

| Layer | Technology |
|-------|-----------|
| Frontend | _[e.g., Next.js 14 + TypeScript]_ |
| Styling | _[e.g., Tailwind CSS 3]_ |
| Backend | _[e.g., Supabase]_ |
| Database | _[e.g., PostgreSQL via Supabase]_ |
| Auth | _[e.g., Supabase Auth]_ |
| Hosting | _[e.g., Vercel]_ |

---

## Active Guardrails

_Key constraints currently in effect._

- _[e.g., No migrations without explicit batch approval]_
- _[e.g., All new endpoints require authorization check]_
- _[e.g., Data scoping must be enforced at query level]_
- _[e.g., No new dependencies without approval]_

---

## Open Issues

_Unresolved bugs, blockers, or questions._

| # | Issue | Severity | Status | Assigned To |
|---|-------|----------|--------|-------------|
| 1 | _[Issue description]_ | _Critical / Major / Minor_ | _Open / In Progress_ | _Name_ |
| 2 | | | | |

---

## Recent Decisions

_Last 5 decisions from `14-decision-log.md`._

| ID | Decision | Date | Impact |
|----|----------|------|--------|
| _DEC-XXX_ | _[Brief title]_ | _YYYY-MM-DD_ | _Low / Medium / High_ |

---

## Known Technical Debt

_Items that need attention but are not blocking current work._

| # | Item | Impact | Planned For |
|---|------|--------|-------------|
| 1 | _[e.g., No unit tests for auth flows]_ | _Risk of regressions_ | _Phase 4_ |
| 2 | _[e.g., Error handling uses generic messages]_ | _Poor UX on errors_ | _Phase 4_ |
| 3 | | | |

---

## When This Must Be Updated

This snapshot must be updated:

1. **After every completed batch** — Update last completed batch and next batch
2. **After every phase change** — Update current phase
3. **After every significant decision** — Update recent decisions
4. **After every new issue** — Update open issues
5. **At the start of every work session** — Verify accuracy
6. **After every release** — Update product status

### Who Updates It

- The **human product owner** is responsible for keeping this document accurate
- AI agents may **suggest updates** but must not modify this document without approval
- The Review Agent should flag if the snapshot appears stale

---

## Scope

- This document is an **orientation aid**, not a source of truth.
- It summarizes information from other documents.

## Out of Scope

- Defining anything new — only summarizing what exists elsewhere
- Replacing any core document

## Guardrails

- [ ] Must never be cited as the authority for any decision or specification
- [ ] Must be updated after every batch
- [ ] Conflicts with other docs must be resolved by updating this snapshot, not the other doc

## Related Files

- All core documents — this snapshot summarizes them
- `00-document-priority.md` — Defines this document's authority level (lowest)

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
