# Glossary

> Plain-English definitions for terms used throughout the AI Software Product Factory.

---

## Purpose

This glossary helps new users, contributors, and AI agents understand the vocabulary used across templates, prompts, and extension packs. Each entry explains the term, why it matters, and where to find it in the repository.

---

## Terms

---

### AI Software Product Factory

**Definition:** This repository. A reusable documentation and prompt system that structures AI-assisted software product development. It provides templates, role prompts, extension packs, and reference examples for guiding AI agents through the full product development lifecycle.

**Why it matters:** Without a structured system, AI agents tend to drift, invent rules, and work without consistent context. The factory gives every agent the same source of truth to work from.

**Where to look:** [README.md](README.md), [START_HERE.md](START_HERE.md), [HOW_TO_USE_THIS_FACTORY.md](HOW_TO_USE_THIS_FACTORY.md)

---

### Documentation-First

**Definition:** A development philosophy where all product decisions — scope, data model, security, API contracts, user flows — are documented and approved before any code is written. AI agents implement only what the documents specify.

**Why it matters:** Documentation-first prevents scope creep, keeps AI agents within defined boundaries, and creates a traceable record of all decisions.

**Where to look:** [core/docs/15-ai-agent-operating-rules.md](core/docs/15-ai-agent-operating-rules.md), [HOW_TO_USE_THIS_FACTORY.md](HOW_TO_USE_THIS_FACTORY.md)

---

### Core Docs

**Definition:** The 18 numbered document templates in `core/docs/`. These cover the full product lifecycle: product brief, discovery, MVP scope, user roles, user flows, page specs, data model, architecture, API design, security model, roadmap, QA plan, release checklist, decision log, agent operating rules, context snapshot, and batch request template.

**Why it matters:** Core docs are the foundation for every product built with this factory. They define what AI agents read and follow.

**Where to look:** [core/docs/](core/docs/)

---

### Core Prompts

**Definition:** The 14 role prompt files in `core/prompts/`. Each prompt configures an AI model to act as a specific specialist: Product Manager, Business Analyst, UX/UI Designer, Software Architect, Database Engineer, Security Engineer, Tech Lead, Coding Agent, QA Agent, Review Agent, Bugfix Agent, and Refactor Agent.

**Why it matters:** Role prompts give agents clear responsibilities, required reading lists, output formats, guardrails, and stop conditions so they behave predictably.

**Where to look:** [core/prompts/](core/prompts/), [core/prompts/00-how-to-use-prompts.md](core/prompts/00-how-to-use-prompts.md)

---

### Extension Pack

**Definition:** An optional add-on folder in `extensions/` that provides domain-specific documentation guidelines, QA checklists, and specialized role prompts for a particular product area (e.g., Supabase, ecommerce, ERP operations, mobile apps).

**Why it matters:** Extension packs supplement the core system for complex domains without cluttering the base templates. They supplement, never replace, core docs.

**Where to look:** [extensions/](extensions/), each pack's `README.md`

---

### Reference Example

**Definition:** A fully completed documentation set in the `examples/` folder, showing what a real product's docs look like when all core templates (and relevant extension pack notes) have been filled in. Examples are documentation references only — they are not runnable applications.

**Why it matters:** Examples give users a concrete model to compare against when filling in their own product's documents.

**Where to look:** [examples/small-crud-app/](examples/small-crud-app/), [examples/medium-saas-app/](examples/medium-saas-app/), [examples/complex-erp-app/](examples/complex-erp-app/)

---

### Product Workspace

**Definition:** The separate repository or folder where a user copies the factory's core templates (and any relevant extension packs) and fills them in for their specific product. The factory repository itself is not the product workspace.

**Why it matters:** Keeping the product workspace separate from the factory repository ensures the factory stays clean and reusable for future projects.

**Where to look:** [HOW_TO_USE_THIS_FACTORY.md](HOW_TO_USE_THIS_FACTORY.md) — "What To Copy Into Your Product Project" section

---

### Source of Truth

**Definition:** The documents that an AI agent must treat as the authoritative, final version of any fact, rule, or specification. If two documents conflict, the one with higher authority is the source of truth for that topic.

**Why it matters:** Without a designated source of truth, agents can use conversation history, guesses, or lower-authority summaries to make decisions — leading to incorrect implementations.

**Where to look:** [core/docs/00-document-priority.md](core/docs/00-document-priority.md)

---

### Document Authority

**Definition:** The ranked order that determines which document wins when two documents conflict. `15-ai-agent-operating-rules.md` has the highest authority; `16-context-snapshot.md` has the lowest.

**Why it matters:** Clear authority prevents agents from using convenient but incorrect documents when ambiguity arises.

**Where to look:** [core/docs/00-document-priority.md](core/docs/00-document-priority.md) — "Authority Priority" table

---

### Execution Priority

**Definition:** The order in which an agent should read and apply documents during an active batch. Distinct from standing authority: during execution, the batch request and operating rules take precedence for scoping what work to do.

**Why it matters:** Execution priority ensures agents focus on the specific approved work order while still respecting the broader governance rules.

**Where to look:** [core/docs/00-document-priority.md](core/docs/00-document-priority.md) — "Execution Priority" section

---

### Batch

**Definition:** A single, small, self-contained unit of approved work assigned to a Coding Agent. Each batch has a defined scope (files in scope, files out of scope), validation commands, and a required implementation report.

**Why it matters:** Batches keep work reviewable and traceable. Small batches reduce the risk of large, uncontrolled changes.

**Where to look:** [core/docs/17-batch-request-template.md](core/docs/17-batch-request-template.md), [core/docs/11-development-roadmap.md](core/docs/11-development-roadmap.md)

---

### Phase

**Definition:** A grouping of related batches in the development roadmap. Phases represent major milestones (e.g., Phase 1: Foundation, Phase 2: Core Features, Phase 3: Polish).

**Why it matters:** Phases help sequence work and identify dependencies between batches. A batch in Phase 2 cannot start until Phase 1 batches it depends on are complete.

**Where to look:** [core/docs/11-development-roadmap.md](core/docs/11-development-roadmap.md)

---

### Baseline-Aware Validation

**Definition:** A validation approach that accounts for pre-existing errors or warnings in a project. Rather than requiring zero errors unconditionally, it requires that a batch introduces no *new* errors or warnings beyond the documented baseline.

**Why it matters:** Real-world projects often inherit technical debt. Baseline-aware validation prevents agents from being blocked by pre-existing issues while still preventing regressions.

**Where to look:** [core/docs/15-ai-agent-operating-rules.md](core/docs/15-ai-agent-operating-rules.md) — "Baseline-Aware Validation Expectations" section, [core/docs/12-qa-test-plan.md](core/docs/12-qa-test-plan.md)

---

### Stop Condition

**Definition:** A specific situation that requires an AI agent to immediately halt all work, document what it found, and wait for human guidance before continuing. Examples: document conflicts, scope ambiguity, security concerns, missing specifications.

**Why it matters:** Stop conditions prevent agents from guessing or improvising when they encounter situations they are not authorized to resolve independently.

**Where to look:** [core/docs/15-ai-agent-operating-rules.md](core/docs/15-ai-agent-operating-rules.md) — "Stop Conditions" section, each role prompt's "Stop Conditions" section

---

### Guardrail

**Definition:** A rule or constraint that limits what an AI agent is permitted to do, even if it is technically capable of doing more. Guardrails are listed as checkboxes at the end of each document and as explicit restrictions in each role prompt.

**Why it matters:** Guardrails enforce the documentation-first philosophy by preventing agents from taking actions outside their authorized scope.

**Where to look:** Every document's "Guardrails" section, [core/docs/15-ai-agent-operating-rules.md](core/docs/15-ai-agent-operating-rules.md)

---

### Scope Boundary

**Definition:** The explicit list of what is included in (and excluded from) a batch, document, or extension pack. In batch requests, scope boundaries appear as "Scope" (what to build) and "Out of Scope" (what to leave alone).

**Why it matters:** Clear scope boundaries prevent agents from adding unasked-for features, modifying files they shouldn't touch, or expanding work beyond what the product owner approved.

**Where to look:** [core/docs/17-batch-request-template.md](core/docs/17-batch-request-template.md) — "Scope" and "Out of Scope" sections

---

### Files in Scope

**Definition:** The specific files that a Coding Agent is authorized to create or modify during a batch. Any file not listed as "in scope" must not be touched.

**Why it matters:** Explicit file lists prevent accidental modification of auth, security, migration, or other sensitive files during routine implementation batches.

**Where to look:** [core/docs/17-batch-request-template.md](core/docs/17-batch-request-template.md) — "Files Likely Involved" section

---

### Files out of Scope

**Definition:** Files explicitly excluded from a batch. Listing out-of-scope files is just as important as listing in-scope files — it removes ambiguity about what the agent must not touch.

**Why it matters:** Without an explicit out-of-scope list, agents may assume that related files are also authorized for modification.

**Where to look:** [core/docs/17-batch-request-template.md](core/docs/17-batch-request-template.md) — "Out of Scope" section

---

### Human Approval Gate

**Definition:** A decision point where the human product owner must explicitly approve before the agent continues. Common gates: approving scope, accepting a batch implementation, approving a database migration, or approving a security change.

**Why it matters:** Human approval gates are the enforcement mechanism for the principle that "agents propose, humans decide." Without them, agents make product decisions autonomously.

**Where to look:** [core/docs/15-ai-agent-operating-rules.md](core/docs/15-ai-agent-operating-rules.md), [core/docs/13-release-checklist.md](core/docs/13-release-checklist.md)

---

### Decision Log

**Definition:** The append-only record of significant architectural, product, and process decisions, documented in `14-decision-log.md`. Each entry includes context, options considered, the decision made, and its consequences.

**Why it matters:** The decision log prevents re-litigating past decisions and gives any agent or team member the rationale behind current system design.

**Where to look:** [core/docs/14-decision-log.md](core/docs/14-decision-log.md)

---

### Context Snapshot

**Definition:** The document `16-context-snapshot.md` — a convenience summary of the current project state (current phase, last completed batch, tech stack summary, open issues). It is orientation-only and has the lowest document authority.

**Why it matters:** The context snapshot helps agents orient quickly at the start of a session without reading every document. However, it must never override any other document.

**Where to look:** [core/docs/16-context-snapshot.md](core/docs/16-context-snapshot.md), [core/docs/00-document-priority.md](core/docs/00-document-priority.md)

---

### Non-Runnable Boundary

**Definition:** The rule that this factory repository contains no application source code, no `package.json`, no database migrations, no framework configurations, no Docker files, and no runtime dependencies. It is documentation only.

**Why it matters:** The non-runnable boundary keeps the factory clean and universally applicable. It ensures the templates are not polluted with product-specific or runtime-specific files.

**Where to look:** [HOW_TO_USE_THIS_FACTORY.md](HOW_TO_USE_THIS_FACTORY.md), each extension pack's `README.md`

---

### Placeholder Data

**Definition:** Fictional, invented data used in document templates and reference examples to illustrate structure without revealing or using real information. Examples: `_e.g., My Product_`, `UUID-placeholder`, `_YYYY-MM-DD_`.

**Why it matters:** Placeholder data makes templates understandable without risking accidental inclusion of private, customer, financial, or sensitive information.

**Where to look:** Every template in [core/docs/](core/docs/), every reference example in [examples/](examples/)

---

### Fictional Example

**Definition:** A reference example built around an invented product (e.g., "Invoice Tracker," "Team Subscription Manager," "Integrated Operations ERP") with fabricated entities, fictional users, and made-up data. None of the data in examples is real.

**Why it matters:** Fictional examples demonstrate how to fill in templates without leaking real business, customer, or financial information.

**Where to look:** [examples/small-crud-app/README.md](examples/small-crud-app/README.md), [examples/medium-saas-app/README.md](examples/medium-saas-app/README.md), [examples/complex-erp-app/README.md](examples/complex-erp-app/README.md)

---

### Review Agent

**Definition:** The AI agent role (prompt `10-review-agent-prompt.md`) responsible for reviewing a completed batch implementation against the approved batch request, guardrails, security rules, and business logic correctness. It is the last gate before a batch is accepted.

**Why it matters:** The Review Agent provides an independent check that the Coding Agent stayed within scope, followed all rules, and produced correct output.

**Where to look:** [core/prompts/10-review-agent-prompt.md](core/prompts/10-review-agent-prompt.md)

---

### QA Agent

**Definition:** The AI agent role (prompt `09-qa-agent-prompt.md`) responsible for testing completed batch implementations against documented specifications, verifying happy paths, edge cases, error states, and authorization rules.

**Why it matters:** The QA Agent verifies that what was built actually works as documented — not just that the code was written.

**Where to look:** [core/prompts/09-qa-agent-prompt.md](core/prompts/09-qa-agent-prompt.md), [core/docs/12-qa-test-plan.md](core/docs/12-qa-test-plan.md)

---

### Coding Agent

**Definition:** The AI agent role (prompt `08-coding-agent-prompt.md`) responsible for implementing exactly what an approved batch request specifies — nothing more, nothing less. It is disciplined, precise, and transparent, and always submits an implementation report.

**Why it matters:** The Coding Agent is the primary implementation worker. Its strict scope discipline prevents uncontrolled changes.

**Where to look:** [core/prompts/08-coding-agent-prompt.md](core/prompts/08-coding-agent-prompt.md)

---

### Extension Notes

**Definition:** Additional documents (e.g., `18-saas-multitenant-notes.md`, `19-supabase-notes.md`) that a reference example adds to explain how a specific extension pack applies to that product. These are example-specific and supplement the core docs.

**Why it matters:** Extension notes show users how to integrate multiple extension packs into a real product specification, without modifying the core templates.

**Where to look:** [examples/medium-saas-app/docs/](examples/medium-saas-app/docs/), [examples/complex-erp-app/docs/](examples/complex-erp-app/docs/)

---

### Release Checklist

**Definition:** The document `13-release-checklist.md` — a comprehensive pre-release verification checklist covering code quality, feature completeness, migrations, environment variables, QA sign-off, rollback plan, and post-release monitoring.

**Why it matters:** The release checklist ensures no release goes to production without a systematic human review of all critical concerns.

**Where to look:** [core/docs/13-release-checklist.md](core/docs/13-release-checklist.md)

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation as part of v2.1.0 public usability polish | Factory |
