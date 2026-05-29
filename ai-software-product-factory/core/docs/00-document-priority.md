# 00 — Document Priority

> Defines the authority hierarchy, execution priority, conflict resolution rules, and governance for all project documents.

---

## Purpose

Establish a clear authority order so that when two documents contain conflicting information, any reader (human or AI agent) knows which document takes precedence. This document also defines how agents should prioritize documents during batch execution.

## Status

`Governance` — This is a meta/governance document. See [Editing This Governance Document](#editing-this-governance-document) for change rules.

---

## File Numbering

File numbers (`00-`, `01-`, `02-`, etc.) exist for **reading order and grouping only**. They do not determine authority. Some files have special roles:

- `00-document-priority.md` — Governance/meta document. Defines authority and conflict rules.
- `15-ai-agent-operating-rules.md` — Defines mandatory AI agent behavior constraints.
- `16-context-snapshot.md` — Read early for orientation but is never a source of truth.

Authority is defined exclusively by the priority tables below.

---

## A. Authority Priority

This table defines the **standing authority** of each document across the entire project. When two documents conflict, the higher-ranked document wins.

| Priority | Document | Authority Role |
|----------|----------|---------------|
| 1 | `15-ai-agent-operating-rules.md` | Agent behavior constraints. Overrides all other docs for agent conduct. |
| 2 | `00-document-priority.md` | Governance and conflict resolution rules (this document). |
| 3 | `01-product-brief.md` | Defines the product, target users, and goals. |
| 4 | `03-mvp-scope.md` | Defines what is in/out of the first release. |
| 5 | `11-development-roadmap.md` | Defines delivery phases, batch sequence, and dependencies. |
| 6 | `10-security-model.md` | Defines security constraints, auth, and data protection. |
| 7 | `07-data-model.md` | Defines data structures, relationships, and integrity rules. |
| 8 | `08-architecture.md` | Defines technology stack and structural conventions. |
| 9 | `09-api-design.md` | Defines API contracts and endpoint specifications. |
| 10 | `04-user-roles.md` | Defines roles, permissions, and data scoping. |
| 11 | `05-user-flows.md` | Defines step-by-step user interactions. |
| 12 | `06-pages-spec.md` | Defines page-level detail and UI behavior. |
| 13 | `02-discovery.md` | Research, assumptions, and analysis (see note below). |
| 14 | `12-qa-test-plan.md` | Testing strategy and checklists. |
| 15 | `13-release-checklist.md` | Release verification steps. |
| 16 | `14-decision-log.md` | Historical decisions (for context and rationale). |
| 17 | `16-context-snapshot.md` | Orientation summary only (see note below). |
| 18 | `17-batch-request-template.md` | Defines the shape of a batch request (see note below). |

### Key Clarifications

**`02-discovery.md`** is a research and input document. It informs decisions but it does **not** override the approved Product Brief, MVP Scope, Development Roadmap, Security Model, or any approved batch scope. If discovery findings contradict an approved document, the conflict must be escalated to the human product owner — the agent must not resolve it by applying discovery findings over approved docs.

**`16-context-snapshot.md`** is an orientation summary only. It must **never** override any source document. If the snapshot conflicts with any other document, the other document is correct. The snapshot exists to help agents get oriented quickly — it is always a derived document.

**`17-batch-request-template.md`** defines the standard shape and format of a batch request. An actual approved batch request (filled in from this template) can **narrow** implementation scope within the boundaries set by MVP Scope, the Roadmap, and Operating Rules. It cannot expand scope beyond those boundaries.

---

## B. Execution Priority (During an Approved Batch)

When an AI agent is executing a specific approved batch, documents should be consulted in this priority order:

| Priority | Document | Role During Execution |
|----------|----------|----------------------|
| 1 | `15-ai-agent-operating-rules.md` | Mandatory constraints — always active |
| 2 | The approved filled batch request (based on `17-batch-request-template.md`) | Defines the specific scope of this work unit |
| 3 | `16-context-snapshot.md` | Orientation only — read for context, never as authority |
| 4 | `03-mvp-scope.md` | Scope boundary check |
| 5 | `11-development-roadmap.md` | Phase and dependency verification |
| 6 | Relevant specs listed in the batch request | Task-specific detail (pages, flows, data model, API, etc.) |
| 7 | `02-discovery.md` | Background context only, when needed |

### Execution Priority Rules

- A batch request can **narrow** scope (e.g., implement only 3 of 5 page fields) but cannot **expand** beyond what MVP Scope, the Roadmap, Security Model, or Operating Rules allow.
- If the approved batch request conflicts with a higher-authority document (Operating Rules, MVP Scope, Security Model), the agent must **stop and report the conflict**. The agent must not resolve it independently.
- Context Snapshot is read early for orientation but is **never treated as source of truth** during execution.
- Discovery is consulted only as background context — it does not drive implementation decisions during a batch.

---

## Context Snapshot Is for Orientation Only

`16-context-snapshot.md` exists to help AI agents quickly understand the current state of the project. It is a **convenience summary**, not a source of truth.

### Rules

- The context snapshot **must never override** any other document.
- If the context snapshot says something different from a core doc, the core doc is correct.
- The context snapshot should be updated frequently, but it is always a **derived document** — it summarizes other documents, it does not define anything new.
- AI agents should read the context snapshot first for orientation, then read the specific documents they need for their task.

---

## Conflict Handling Rules

### Rule 1: Higher Authority Wins

If `01-product-brief.md` says the product is B2B and `06-pages-spec.md` includes a consumer-facing signup page, the product brief wins. The pages spec must be corrected.

### Rule 2: Specific Overrides General

If a higher-authority document is silent on a topic, the lower-authority document's guidance stands. Conflicts only exist when two documents actively state contradictory things.

### Rule 3: Decision Log Captures Overrides

If a lower-authority document intentionally deviates from a higher-authority one (e.g., an architectural decision that modifies an MVP scope item), it must be recorded in `14-decision-log.md` with rationale.

### Rule 4: Agents Must Flag Conflicts

If an AI agent detects a conflict between documents, it must:

1. **Stop** the current task
2. **Report** the conflict with exact references to both documents
3. **Wait** for human resolution
4. **Not** resolve the conflict independently

### Rule 5: Batch Requests Inherit and Narrow Scope

An approved batch request cannot expand scope beyond what `03-mvp-scope.md` and `11-development-roadmap.md` allow. If a batch request asks for work outside MVP scope, the agent must reject it. A batch request may narrow scope within those boundaries.

---

## Required Reading Order for New Sessions

When starting a new AI session, documents should be read in this order:

1. `16-context-snapshot.md` — Quick orientation (not authority)
2. `00-document-priority.md` — Authority and conflict rules
3. `15-ai-agent-operating-rules.md` — Agent behavior constraints
4. `01-product-brief.md` — Understand the product
5. `03-mvp-scope.md` — Understand current scope
6. `11-development-roadmap.md` — Understand delivery plan and current phase
7. Task-specific documents as needed

---

## Editing This Governance Document

`00-document-priority.md` is a governance/meta document with special change rules:

1. **Who may change it:** Only the project owner, product lead, or an explicitly approved governance/documentation batch.
2. **Coding agents must not change it** during normal feature, bugfix, refactor, QA, or release batches.
3. **Every change must be recorded** in `14-decision-log.md` with:
   - What was changed
   - Short reason for the change
   - Impact summary (what behavior or priority shifts result)
4. **No silent edits:** If this file is found modified without a corresponding decision log entry, the modification should be treated as unauthorized and reverted.

---

## Guardrails

- [ ] No agent may resolve document conflicts independently
- [ ] Context snapshot must never be cited as the authority for a decision
- [ ] All intentional overrides must be logged in the decision log
- [ ] Batch requests cannot exceed MVP scope or roadmap boundaries
- [ ] This governance document may only be changed through approved governance batches
- [ ] Discovery findings do not override approved product, scope, or security documents

## Related Files

- `14-decision-log.md` — Where overrides, conflict resolutions, and governance changes are recorded
- `15-ai-agent-operating-rules.md` — Agent behavior constraints (highest authority)
- `16-context-snapshot.md` — Orientation summary (lowest authority)
- `17-batch-request-template.md` — Batch request format

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
| _YYYY-MM-DD_ | Governance hardening: separated authority vs. execution priority, added governance editing rules, clarified discovery/snapshot/batch roles | _Name_ |
