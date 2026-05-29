# 14 — Decision Log

> Records all significant architectural, product, and process decisions with context and rationale.

---

## Purpose

Maintain an append-only log of decisions so that any team member or AI agent can understand **why** things are the way they are. Prevents re-litigating past decisions and provides context for future changes.

## Status

`Active` — Continuously updated throughout the project.

---

## Decision Entry Template

_Copy this template for each new decision._

---

### DEC-XXX: [Decision Title]

**Date:** _YYYY-MM-DD_
**Status:** `Proposed` | `Accepted` | `Superseded` | `Deprecated`
**Decided By:** _Name / Role_
**Impact:** _Low / Medium / High_

#### Context

_What is the situation? What problem or question prompted this decision?_

```
[Describe the context in 2-4 sentences.]
```

#### Options Considered

| Option | Pros | Cons |
|--------|------|------|
| _Option A_ | _Advantages_ | _Disadvantages_ |
| _Option B_ | _Advantages_ | _Disadvantages_ |
| _Option C_ | _Advantages_ | _Disadvantages_ |

#### Decision

_What was decided and why?_

```
[State the decision clearly. Explain the primary rationale.]
```

#### Consequences

_What are the implications of this decision?_

- **Positive:** _Benefits and opportunities_
- **Negative:** _Trade-offs and limitations_
- **Risks:** _What could go wrong_

#### Related Documents

- _List any documents affected by this decision_

---

## Example Decision

---

### DEC-001: Use AI Software Product Factory Template

**Date:** _YYYY-MM-DD_
**Status:** `Accepted`
**Decided By:** _Project Lead_
**Impact:** High

#### Context

We are starting a new software product and need a structured approach to documentation and AI-assisted development. Without a systematic process, there is risk of scope creep, inconsistent implementations, and undocumented decisions.

#### Options Considered

| Option | Pros | Cons |
|--------|------|------|
| No documentation — ad-hoc AI prompting | Fast start, no overhead | No traceability, scope creep, inconsistency |
| Minimal docs — README + basic spec | Low overhead | Missing guardrails, agent drift |
| Full factory template | Complete coverage, enforced guardrails, traceable | Higher initial setup time |

#### Decision

Adopt the full AI Software Product Factory template. The upfront investment in documentation pays off through reduced rework, consistent AI agent behavior, and traceable decision-making.

#### Consequences

- **Positive:** All agents work from the same source of truth. Scope is enforceable. Decisions are traceable.
- **Negative:** Initial setup requires time to fill in all templates. May feel heavy for very small projects.
- **Risks:** If templates are not maintained, they become stale and misleading.

#### Related Documents

- `00-document-priority.md` — Establishes document hierarchy
- `15-ai-agent-operating-rules.md` — Agent behavior rules

---

## Decision Index

| ID | Title | Date | Status | Impact |
|----|-------|------|--------|--------|
| DEC-001 | Use AI Software Product Factory Template | _YYYY-MM-DD_ | `Accepted` | High |

---

## Scope

- This document records **decisions and their rationale**.
- It is append-only — decisions should not be deleted, only superseded.

## Out of Scope

- Implementation details (those belong in code or specs)
- Meeting notes or general discussions

## Guardrails

- [ ] Decisions that affect scope, architecture, security, or data model must be logged
- [ ] Superseded decisions must reference the replacing decision
- [ ] AI agents must not make decisions independently — they must flag decisions for human review

## Related Files

- `00-document-priority.md` — This log captures overrides between documents
- All core docs — Any document can be affected by a logged decision

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation with DEC-001 example | _Name_ |
