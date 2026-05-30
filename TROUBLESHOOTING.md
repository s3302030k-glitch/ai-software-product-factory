# Troubleshooting

> Common AI-agent workflow failures and how to resolve them.

---

## Purpose

This guide helps users diagnose and fix problems that arise when working with AI agents in the AI Software Product Factory workflow. Most failures fall into predictable patterns that can be resolved by returning to the documents as the source of truth.

---

## Before Troubleshooting: Confirm Your Documents Are the Source of Truth

AI agent failures almost always trace back to one root cause: **the agent was not reading the right documents, or was not treating them as authoritative.**

Before investigating any specific issue, verify:

1. The agent read `core/docs/15-ai-agent-operating-rules.md` at the start of the session.
2. The agent read `core/docs/00-document-priority.md` and understands the authority hierarchy.
3. The agent was given a filled batch request (`17-batch-request-template.md`) with explicit scope and out-of-scope sections.
4. The agent confirmed understanding via the Session Understanding Confirmation from `core/prompts/00-session-starter-prompt.md`.

If any of these were skipped, restart the session with the session starter prompt before continuing.

---

## Common Issues

---

### AI agent starts coding without reading docs

| | |
|--|--|
| **Symptom** | Agent immediately starts writing code or asks to begin implementing without confirming it has read the required documents. |
| **Likely Cause** | Session starter prompt was not used. Agent relied on conversation memory or previous session context instead of documents. |
| **What to Check** | Was `00-session-starter-prompt.md` pasted at the start of this session? Did the agent output the Session Understanding Confirmation block? |
| **Recommended Fix** | Stop the session. Restart with the session starter prompt. Paste the required documents or their paths explicitly. Do not allow the agent to begin work until it outputs the confirmation block. |

---

### AI agent changes files outside scope

| | |
|--|--|
| **Symptom** | The implementation report or git diff shows changes to files not listed in the batch request's "Files Likely Involved" section. |
| **Likely Cause** | The batch request did not include an explicit "Out of Scope" section, or the agent did not read it carefully. |
| **What to Check** | Review the batch request for a clear "Out of Scope" list. Check `git diff --stat` against the expected file list. |
| **Recommended Fix** | Revert out-of-scope changes with `git checkout -- <file>`. Add an explicit "Out of Scope" section to the batch request listing the files that must not be touched. Re-run the batch with the corrected request. |

---

### AI agent invents business rules

| | |
|--|--|
| **Symptom** | Agent makes up pricing rules, tax calculations, rounding logic, financial formulas, discount policies, or other business-specific decisions that were never documented. |
| **Likely Cause** | The relevant business rule was not documented before the batch started. The agent guessed rather than stopping. |
| **What to Check** | Does `docs/07-data-model.md`, `docs/03-mvp-scope.md`, or a relevant extension pack doc contain the rule? If not, it was never defined. |
| **Recommended Fix** | Stop the batch. Document the business rule in the appropriate document and get owner approval. Then re-issue the batch with the rule explicitly referenced. Never accept invented business logic. |

---

### AI agent ignores the out-of-scope section

| | |
|--|--|
| **Symptom** | Agent implements features, pages, or endpoints that were explicitly listed as out of scope for the batch. |
| **Likely Cause** | The out-of-scope section was present but not emphatic enough, or the agent was not instructed to stop if it encountered temptation to add more. |
| **What to Check** | Re-read the batch request's "Out of Scope" section. Confirm the section is explicit and uses clear language (e.g., "Do NOT implement X — this is deferred to Batch P2-B5"). |
| **Recommended Fix** | Revert out-of-scope additions. Restate the out-of-scope section using strong, explicit language. Remind the agent in the batch prompt: "Any work not listed in the Scope section is forbidden, even if it seems helpful." |

---

### AI agent adds package.json or runtime files to the factory repo

| | |
|--|--|
| **Symptom** | Files such as `package.json`, `tsconfig.json`, `.env`, `Dockerfile`, migration files, or source code appear in the factory repository. |
| **Likely Cause** | The agent was working inside the factory repository instead of a separate product workspace, or confused the factory repo with the product repo. |
| **What to Check** | Run `git status --short`. Look for any non-`.md` files. Run `Get-ChildItem -Recurse -File | Where-Object { $_.Extension -ne '.md' }`. |
| **Recommended Fix** | Do not commit. Run `git checkout -- .` to discard unstaged changes, or `git clean -fd` to remove untracked files. Redirect the agent to work in a separate product workspace. Review [HOW_TO_USE_THIS_FACTORY.md](HOW_TO_USE_THIS_FACTORY.md). |

---

### AI agent mixes example docs with real product docs

| | |
|--|--|
| **Symptom** | Agent copies content from `examples/` (e.g., Invoice Tracker entities, fictional users, placeholder amounts) into the user's real product documents. |
| **Likely Cause** | The agent was given example files as input documents and misunderstood them as templates to copy rather than references to learn from. |
| **What to Check** | Review the product docs for any mention of "Invoice Tracker," "Team Subscription Manager," "Integrated Operations ERP," or other fictional example product names and data. |
| **Recommended Fix** | Remove any example-specific content from product docs. Clarify to the agent: examples are references only — the product's own `01-product-brief.md` is the source of truth. Use core templates (not examples) as the base for product documents. |

---

### AI agent treats examples as runnable applications

| | |
|--|--|
| **Symptom** | Agent tries to run, deploy, or install dependencies for files in `examples/`. |
| **Likely Cause** | Agent was not informed that examples are documentation references with no source code. |
| **What to Check** | Does the example folder contain any `package.json`, SQL migrations, source code, or runtime files? (It should not — if it does, those files must not be there.) |
| **Recommended Fix** | Remind the agent: "Examples contain documentation only. There is no code to run. Do not attempt to install, build, or deploy anything from the examples folder." Reference the non-runnable disclaimer in the example's README. |

---

### AI agent weakens security or permissions

| | |
|--|--|
| **Symptom** | Agent removes authentication checks, relaxes authorization rules, disables security middleware, or broadens data access beyond what `10-security-model.md` specifies. |
| **Likely Cause** | The agent encountered a failing test or convenience issue and took the path of least resistance by removing the security control. |
| **What to Check** | Review `git diff` for any changes to auth middleware, role checks, RLS policies, CORS settings, or data scoping queries. Compare against `docs/10-security-model.md`. |
| **Recommended Fix** | Immediately revert the security change. Escalate the underlying issue to the human product owner. The security model must never be weakened without explicit owner approval and a decision log entry. See `core/docs/15-ai-agent-operating-rules.md` Security Rules. |

---

### AI agent skips validation commands

| | |
|--|--|
| **Symptom** | The implementation report shows validation steps as "not run" or the agent marks the batch complete without running build, lint, type check, or tests. |
| **Likely Cause** | The batch request did not include explicit validation commands, or the agent skipped them to save time. |
| **What to Check** | Review the implementation report's "Validation Results" table. Check whether build, typecheck, lint, and test commands were actually run. |
| **Recommended Fix** | Do not accept the batch. Require the agent to run all validation commands listed in the batch request and in `core/docs/12-qa-test-plan.md`. Validation is not optional — a batch without passing validation is not complete. |

---

### AI agent produces too-large a batch

| | |
|--|--|
| **Symptom** | The batch request or the agent's proposed work covers many features, many files, or multiple phases at once, making it impossible to review clearly. |
| **Likely Cause** | The roadmap was not broken into small enough batches. The agent was given too broad an objective. |
| **What to Check** | Count the files and features in the batch. A well-sized batch typically touches 3–8 files and delivers one clearly testable capability. |
| **Recommended Fix** | Split the batch into smaller units. Use `docs/11-development-roadmap.md` to restructure the phase into finer-grained batches. Each batch should be completable and reviewable in a single session. The agent should report if a batch is too large rather than silently reducing scope. |

---

### AI agent cannot decide between conflicting documents

| | |
|--|--|
| **Symptom** | Agent reports that two documents say contradictory things and it is unsure which to follow. |
| **Likely Cause** | Documents have drifted out of sync (e.g., `07-data-model.md` and `09-api-design.md` describe the same entity differently), or an older decision was not propagated. |
| **What to Check** | Identify which documents conflict. Check `core/docs/00-document-priority.md` to determine which document has higher authority for this topic. |
| **Recommended Fix** | The higher-authority document wins. Update the lower-authority document to align. Log the resolution in `14-decision-log.md`. The agent must stop and report the conflict — it must not resolve it independently. |

---

### AI agent uses conversation memory instead of documents

| | |
|--|--|
| **Symptom** | Agent references things said in earlier messages, earlier sessions, or previous conversations instead of the current documents. It may say "as we discussed earlier" or "based on what you told me before." |
| **Likely Cause** | The session starter prompt was not used, or the agent's context window includes prior conversations that override the current documents. |
| **What to Check** | Did the agent output the Session Understanding Confirmation from `00-session-starter-prompt.md`? Are the required documents explicitly provided in the current session? |
| **Recommended Fix** | Start a fresh session. Paste the session starter prompt. Paste or reference the required documents explicitly. Instruct the agent: "Ignore all prior conversations. The documents provided in this session are the only source of truth." |

---

### AI agent adds real or private data

| | |
|--|--|
| **Symptom** | Real company names, real customer names, real email addresses, real financial amounts, real credentials, or real identifiers appear in product documents or factory files. |
| **Likely Cause** | The user provided real data in their prompt, or the agent drew from external sources without being restricted to placeholder data. |
| **What to Check** | Search documents for real names, real email domains, real amounts, real addresses, or any data that is not clearly fictional or labeled as a placeholder. |
| **Recommended Fix** | Remove all real data immediately. Replace with fictional equivalents or `_placeholder_` values. Remind the agent: "All data in this document must be fictional. Use placeholder values only. Never use real customer, business, payment, or personal data." |

---

### Extension packs conflict or overlap

| | |
|--|--|
| **Symptom** | Two extension packs give contradictory guidance (e.g., the financial pack and the ecommerce pack describe money handling differently), and it is unclear which to follow. |
| **Likely Cause** | Extension packs were designed for different contexts. Overlap is expected — both packs may be partially applicable. |
| **What to Check** | Read both packs' guidance on the topic. Check whether the ecommerce pack's README lists the financial pack as a related/complementary pack. Usually, the more specific pack (e.g., ecommerce-specific guidance) takes precedence for ecommerce scenarios. |
| **Recommended Fix** | The product owner decides which guidance applies. Document the decision in `14-decision-log.md`. Extension packs are advisory — the core docs and the product owner's decision are authoritative. |

---

### User does not know which example to start from

| | |
|--|--|
| **Symptom** | User is unsure whether their product is more like `small-crud-app`, `medium-saas-app`, or `complex-erp-app`. |
| **Likely Cause** | Product scope is still unclear, or the user has not compared their product against the examples' feature sets. |
| **What to Check** | Read `HOW_TO_USE_THIS_FACTORY.md` — "Choosing the Right Example" table. Count the product's estimated entities, roles, and features. |
| **Recommended Fix** | Use this decision guide: simple single-tenant tools with 2–5 entities → `small-crud-app`; SaaS products with organizations, subscriptions, and memberships → `medium-saas-app`; ERP or operations systems with inventory, approvals, and workflows → `complex-erp-app`. When in doubt, start with the simpler example and add complexity as needed. |

---

### User does not know which extension pack to choose

| | |
|--|--|
| **Symptom** | User is unsure which extension packs are relevant to their product. |
| **Likely Cause** | User has not mapped their product's features against the available packs. |
| **What to Check** | Read each pack's `README.md` — specifically the "Risks This Pack Helps Manage" and "Purpose and Scope" sections. Compare against your product's feature list. |
| **Recommended Fix** | Add only packs that match your product's actual domain needs. Start with zero packs. Add a pack only when you encounter a domain problem it solves (e.g., add `financial-business-logic-pack` only when you start designing money handling). See [HOW_TO_USE_THIS_FACTORY.md](HOW_TO_USE_THIS_FACTORY.md) for path-based recommendations. |

---

### User wants to move from documentation to actual coding

| | |
|--|--|
| **Symptom** | User has completed the factory documentation phase and wants to start writing real application code, but is unsure how to proceed. |
| **Likely Cause** | The factory produces documentation, not code. The transition to coding requires a separate product workspace and a coding agent. |
| **What to Check** | Is `docs/11-development-roadmap.md` complete with phases and batches? Is `docs/17-batch-request-template.md` filled in for the first batch? Is `docs/16-context-snapshot.md` up to date? |
| **Recommended Fix** | Follow these steps: (1) Create a separate product repository (not inside the factory repo). (2) Copy `core/docs/` and `core/prompts/` into `my-product/docs/` and `my-product/prompts/`. (3) Fill in the first batch request using `17-batch-request-template.md`. (4) Start a new AI session with `00-session-starter-prompt.md` and `08-coding-agent-prompt.md`. (5) Provide the batch request and required reading documents. (6) Let the coding agent implement the first batch. |

---

## Escalation Checklist Before Continuing

If you have tried the recommended fix and the problem persists, run through this checklist before escalating:

- [ ] Did the agent use the session starter prompt and output the Session Understanding Confirmation?
- [ ] Did the agent read all required documents listed in the batch request?
- [ ] Is the batch request filled in completely (Scope, Out of Scope, Files Likely Involved, Validation Commands)?
- [ ] Is the conflict or ambiguity documented clearly?
- [ ] Have you checked `core/docs/00-document-priority.md` for which document wins?
- [ ] Have you reviewed `git diff` to confirm exactly what changed?
- [ ] Have you reverted any unauthorized changes?

---

## When to Stop and Ask the Product Owner

Stop the agent session and escalate to the human product owner when:

- Two approved documents conflict and neither is clearly lower-authority.
- The batch would require changing security, authentication, or authorization rules.
- The batch would require a database migration that was not explicitly planned.
- A business rule (formula, rounding mode, pricing policy) is undefined or ambiguous.
- The agent has produced output that cannot be reviewed confidently.
- A security concern or data integrity risk was identified.
- The agent's implementation report claims "passed" but the actual behavior seems wrong.
- Scope needs to be expanded beyond the approved batch.

The product owner's decision, documented in `14-decision-log.md`, is the resolution for all of these situations.

---

## Related Files

- [core/docs/15-ai-agent-operating-rules.md](core/docs/15-ai-agent-operating-rules.md) — Agent behavior rules and stop conditions
- [core/docs/00-document-priority.md](core/docs/00-document-priority.md) — Document authority hierarchy
- [core/prompts/00-session-starter-prompt.md](core/prompts/00-session-starter-prompt.md) — Session initialization prompt
- [core/docs/17-batch-request-template.md](core/docs/17-batch-request-template.md) — Batch request format
- [HOW_TO_USE_THIS_FACTORY.md](HOW_TO_USE_THIS_FACTORY.md) — Full usage guide
- [GLOSSARY.md](GLOSSARY.md) — Term definitions

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation as part of v2.1.0 public usability polish | Factory |
