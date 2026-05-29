# 02 — Discovery

> Research, assumptions, and validation that inform the product brief and MVP scope for Invoice Tracker.

---

## Purpose

Capture everything learned during the discovery phase — assumptions that need testing, constraints that limit options, competitor analysis, user personas, and risks. This document feeds into the product brief and MVP scope.

## Status

`Complete`

---

## Assumptions

What do we believe to be true but have not yet validated?

| # | Assumption | Risk if Wrong | Validation Method | Status |
|---|-----------|---------------|-------------------|--------|
| 1 | Users prefer entering line items and recording payments manually over automated bank feeds. | Low adoption due to friction | User interviews with 5 freelancers | `Validated` — Users prioritize control and simple setup over complex integrations. |
| 2 | A responsive web app is sufficient, with no native mobile app needed. | Users drop out on mobile | Web usage analytics and interviews | `Validated` — 90% of invoicing is done at a desk. |
| 3 | Single-tenant (one user/business context) is acceptable for MVP. | Scalability limitations | User profile survey | `Validated` — Targets solo freelancers or small family-run shops. |

---

## Validation Plan

How we test our assumptions before and during development:

| # | What to Validate | Method | Timeline | Owner | Status |
|---|-----------------|--------|----------|-------|--------|
| 1 | Core workflow (client -> invoice -> payment -> status transition) is intuitive. | Interactive wireframe testing with 3 target users | Before development | PM | `Done` |

---

## Constraints

What limits our options?

### Technical Constraints

- **Single Default Currency:** Application only supports USD in the database and UI to keep data models and business calculations simple.
- **Web Only:** Must run on modern desktop and responsive mobile browsers (Next.js App Router).
- **RDBMS:** Relational database (PostgreSQL) is required to ensure data integrity between Clients, Invoices, and Payments.

### Business Constraints

- **No payment gateway integration:** No Stripe, PayPal, or Plaid to prevent regulatory, financial, and API maintenance overhead.
- **Development Schedule:** Must be deliverable within 11 short batches to maintain a rapid feedback loop.

### Regulatory Constraints

- **Data Privacy:** Simple data protection standards (HTTPS, encrypted password hashing, session management) are sufficient; no HIPAA or strict PCI-compliance is needed as no cards are processed.

### Team Constraints

- **Solo Execution:** The app will be built by an AI coding agent and verified by a human reviewer.

---

## Competitors

Who else solves this problem? What can we learn from them?

| Competitor | Strengths | Weaknesses | Key Differentiator | Pricing |
|-----------|-----------|------------|-------------------|---------|
| Google Sheets | Free, infinite flexibility. | No database relations, status requires manual script/formula updating, high user error. | Standard layout, zero onboarding. | Free |
| Wave Accounting | Free invoice builder. | Slow load times, forces merchant account signup, complex ledger options. | Zero subscription cost for basic invoicing. | Free/Ad-supported |
| QuickBooks | Deep accounting features, automatic matching. | Extremely expensive, steep learning curve, cluttered interface. | Industry standard for tax accountants. | Paid subscription |

### Competitive Insights

- **Table Stakes:** Client lists, invoice line items (rate, quantity, total), status tracking (Unpaid vs Paid), manual entry.
- **Identified Gap:** Small business owners want a dashboard that is faster than a spreadsheet but lacks the enterprise bloating of accounting platforms.
- **UX Familiarity:** Clean lists, data-entry forms with auto-calculating totals, and status badges (green for Paid, red for Overdue).

---

## User Personas

Detailed profiles of the people who will use this product.

### Persona 1: Sarah — Freelance Graphic Designer

| Attribute | Detail |
|-----------|--------|
| **Role** | Freelance Owner |
| **Age Range** | 25-35 |
| **Technical Skill** | High (UX/Design), Medium (Development) |
| **Primary Goal** | Send professional-looking invoices quickly and easily see who is late on payment. |
| **Frustration** | Forgetting who has paid and having to search through bank deposits and email chains. |
| **Usage Context** | Primarily desktop web browser, occasional checking of status on phone. |
| **Quote** | *"I want to focus on my designs, not spend Sunday afternoon figuring out who owes me what."* |

### Persona 2: Tom — Consulting Agency Owner

| Attribute | Detail |
|-----------|--------|
| **Role** | Agency Owner (retains Staff assistant) |
| **Age Range** | 40-50 |
| **Technical Skill** | Low-Medium |
| **Primary Goal** | Let his assistant draft invoices while Tom reviews, marks them as sent, and logs bank check deposits. |
| **Frustration** | Complicated software where he accidentally changes ledger settings or logs payments in the wrong fiscal category. |
| **Usage Context** | Desktop web browser, daily checks. |
| **Quote** | *"I want my assistant to prepare the invoices, and I just click 'sent' and enter the payment when the check clears."* |

---

## Risks

What could go wrong? What threats should we plan for?

| # | Risk | Likelihood | Impact | Mitigation | Owner |
|---|------|-----------|--------|------------|-------|
| 1 | Feature creep: wanting automated emails or Stripe. | High | Medium | Enforce strict MVP scope in `03-mvp-scope.md`. | PM |
| 2 | Multi-currency needs by foreign clients. | Medium | Low | Clearly state single-currency constraint in the onboarding UI. | PM |
| 3 | AI Agent alters formula rules during batch work. | High | High | Tailor strict operating rules in `15-ai-agent-operating-rules.md` and enforce QA verification. | Tech Lead |

---

## Scope

- This document captures **research and analysis**, not decisions.
- Decisions derived from discovery are recorded in `01-product-brief.md` or `14-decision-log.md`.

## Out of Scope

- Final product decisions (see `01-product-brief.md`)
- Feature prioritization (see `03-mvp-scope.md`)
- Technical architecture choices (see `08-architecture.md`)

## Guardrails

- [x] Assumptions must be validated before building dependent features
- [x] High-impact risks must have mitigation plans before development starts
- [x] Personas should be referenced when designing user flows

## Related Files

- `01-product-brief.md` — Product definition informed by this research
- `03-mvp-scope.md` — Feature prioritization based on discovery findings
- `04-user-roles.md` — Roles derived from personas defined here
- `14-decision-log.md` — Decisions made based on discovery insights

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation for Invoice Tracker | Product Owner |
