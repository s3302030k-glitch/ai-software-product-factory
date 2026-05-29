# 02 — Discovery

> Research, assumptions, and validation that inform the product brief and MVP scope.

---

## Purpose

Capture everything learned during the discovery phase — assumptions that need testing, constraints that limit options, competitor analysis, user personas, and risks. This document feeds into the product brief and MVP scope.

## Status

`Draft` | `In Progress` | `Complete`

---

## Assumptions

_What do we believe to be true but have not yet validated?_

| # | Assumption | Risk if Wrong | Validation Method | Status |
|---|-----------|---------------|-------------------|--------|
| 1 | _e.g., Users prefer self-service over assisted onboarding_ | _Low adoption_ | _User interviews_ | `Unvalidated` / `Validated` / `Invalidated` |
| 2 | | | | |
| 3 | | | | |

---

## Validation Plan

_How will we test our assumptions before and during development?_

| # | What to Validate | Method | Timeline | Owner | Status |
|---|-----------------|--------|----------|-------|--------|
| 1 | _e.g., Core user flow is intuitive_ | _Prototype testing with 5 users_ | _Before dev_ | _Name_ | `Planned` / `Done` |
| 2 | | | | | |

---

## Constraints

_What limits our options? Consider technical, business, regulatory, timeline, and budget constraints._

### Technical Constraints

- _e.g., Must run on shared hosting (no Docker)_
- _e.g., Must support IE11 (or not)_

### Business Constraints

- _e.g., Budget limited to $X/month for infrastructure_
- _e.g., Must launch before Q3_

### Regulatory Constraints

- _e.g., Must comply with GDPR_
- _e.g., Financial data requires specific retention policies_

### Team Constraints

- _e.g., Solo developer — no dedicated QA_
- _e.g., AI agents handle development; human does review only_

---

## Competitors

_Who else solves this problem? What can we learn from them?_

| Competitor | Strengths | Weaknesses | Key Differentiator | Pricing |
|-----------|-----------|------------|-------------------|---------|
| _Name_ | _What they do well_ | _Where they fall short_ | _What makes them unique_ | _Free / Paid / Freemium_ |
| | | | | |

### Competitive Insights

- _What features do all competitors share? (table stakes)_
- _What gap exists that no competitor addresses?_
- _What UX patterns are users already familiar with?_

---

## User Personas

_Detailed profiles of the people who will use this product._

### Persona 1: [Name / Title]

| Attribute | Detail |
|-----------|--------|
| **Role** | _e.g., Small business owner_ |
| **Age Range** | _e.g., 30-50_ |
| **Technical Skill** | _Low / Medium / High_ |
| **Primary Goal** | _What they want to accomplish_ |
| **Frustration** | _What currently annoys them_ |
| **Usage Context** | _Desktop / Mobile / Both; Frequency_ |
| **Quote** | _"I just want to..."_ |

### Persona 2: [Name / Title]

| Attribute | Detail |
|-----------|--------|
| **Role** | |
| **Age Range** | |
| **Technical Skill** | |
| **Primary Goal** | |
| **Frustration** | |
| **Usage Context** | |
| **Quote** | |

---

## Risks

_What could go wrong? What threats should we plan for?_

| # | Risk | Likelihood | Impact | Mitigation | Owner |
|---|------|-----------|--------|------------|-------|
| 1 | _e.g., Scope creep delays MVP_ | High | High | _Strict MVP scope doc, batch limits_ | _Name_ |
| 2 | _e.g., Chosen tech stack becomes unsupported_ | Low | High | _Use mainstream, well-supported tools_ | _Name_ |
| 3 | _e.g., AI agent introduces security vulnerability_ | Medium | High | _Security review on every batch_ | _Name_ |
| 4 | | | | | |

---

## Scope

- This document captures **research and analysis**, not decisions.
- Decisions derived from discovery should be recorded in `01-product-brief.md` or `14-decision-log.md`.

## Out of Scope

- Final product decisions (see `01-product-brief.md`)
- Feature prioritization (see `03-mvp-scope.md`)
- Technical architecture choices (see `08-architecture.md`)

## Guardrails

- [ ] Assumptions should be validated before building dependent features
- [ ] High-impact risks must have mitigation plans before development starts
- [ ] Personas should be referenced when designing user flows

## Related Files

- `01-product-brief.md` — Product definition informed by this research
- `03-mvp-scope.md` — Feature prioritization based on discovery findings
- `04-user-roles.md` — Roles derived from personas defined here
- `14-decision-log.md` — Decisions made based on discovery insights

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
