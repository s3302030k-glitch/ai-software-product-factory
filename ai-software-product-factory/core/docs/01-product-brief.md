# 01 — Product Brief

> The single source of truth for what the product is, why it exists, and who it serves.

---

## Purpose

Define the product at the highest level. Every other document derives from this one. If the product brief doesn't say it, it's not part of the product.

## Status

`Draft` | `In Review` | `Approved` | `Locked`

> Update status as the brief progresses through review cycles.

---

## Product Name

_What is the product called?_

```
[Product Name]
```

---

## Problem Statement

_What problem does this product solve? Who has this problem? Why does it matter?_

```
[Describe the problem in 2-4 sentences. Be specific about the pain point.]
```

---

## Target Users

_Who are the primary users of this product?_

| User Type | Description | Key Need |
|-----------|-------------|----------|
| _e.g., Admin_ | _Who they are_ | _What they need most_ |
| _e.g., End User_ | _Who they are_ | _What they need most_ |

---

## Product Goal

_What does the product do for its users?_

```
[One sentence describing the core value proposition.]
```

---

## Business Goal

_What does the product do for the business?_

```
[One sentence describing the business outcome — revenue, efficiency, market entry, etc.]
```

---

## Product Type

_What kind of product is this?_

- [ ] Web Application (SPA)
- [ ] Web Application (MPA / SSR)
- [ ] Mobile App (Native)
- [ ] Mobile App (Hybrid / PWA)
- [ ] API / Backend Service
- [ ] Desktop Application
- [ ] CLI Tool
- [ ] Other: ___

---

## First Version Summary

_In 3-5 sentences, describe what version 1 of this product will look like. What can a user do? What is intentionally excluded?_

```
[Describe the MVP in plain language.]
```

---

## Success Criteria

_How will you know version 1 is successful?_

| Criteria | Measurement | Target |
|----------|-------------|--------|
| _e.g., User can complete core flow_ | _Manual QA pass_ | _100% pass_ |
| _e.g., Page load time_ | _Lighthouse score_ | _> 80_ |
| _e.g., Zero critical bugs_ | _Bug count_ | _0 critical, < 3 major_ |

---

## Open Questions

_What is still unresolved? What decisions need to be made before development begins?_

| # | Question | Impact | Status |
|---|----------|--------|--------|
| 1 | _e.g., Which payment provider?_ | _Affects checkout flow_ | `Open` / `Resolved` |
| 2 | | | |

---

## Scope

- This document defines **what** the product is and **why** it exists.
- It does **not** define how to build it (see `08-architecture.md`).
- It does **not** define the feature list (see `03-mvp-scope.md`).

## Out of Scope

- Technical implementation details
- UI/UX specifications
- Data model definitions
- Timeline or roadmap

## Guardrails

- [ ] Product brief must be approved before development begins
- [ ] Changes to the product brief require human approval and a decision log entry
- [ ] No AI agent may modify this document without explicit authorization

## Related Files

- `02-discovery.md` — Research that informs this brief
- `03-mvp-scope.md` — Feature prioritization derived from this brief
- `04-user-roles.md` — Detailed role definitions for target users listed here
- `14-decision-log.md` — Decisions that affect or modify the brief

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
