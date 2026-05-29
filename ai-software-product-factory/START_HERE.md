# START HERE — AI Software Product Factory

## 1. What This Guide Is For

This guide provides the practical operating path for using the AI Software Product Factory to start a product from:
- A raw idea
- A client request
- An internal business need
- An unfinished existing project

It serves as a step-by-step roadmap for the human product owner to guide the AI team through definition, scope, architecture, planning, implementation, and review.

---

## 2. The Core Rule

Every project using this factory must adhere to the Core Rule of AI Software Governance:
- **Human Owner Decides:** The human product owner makes all final product, scope, security, architectural, and roadmap decisions. AI agents can only propose or recommend changes.
- **Docs are the Source of Truth:** Code, tests, and conversation history are secondary. The project documents (in the `docs/` folder) are the definitive source of truth.
- **No Agent Improv:** AI agents must propose or execute code strictly within the approved scope. They are not allowed to anticipate future needs or independently expand boundaries.
- **No Vibe Coding:** Coding starts *only* after the product brief, MVP scope, technical architecture, and development roadmap are sufficiently defined and documented.

---

## 3. Choose Your Starting Situation

Depending on where you are starting, select one of the following recommended paths to establish your factory environment.

### Path A — I only have a raw idea

Use this path when you have a general concept but no detailed requirements.

1. **Initialize Project:** Create your project folders and copy the core templates (see Section 4).
2. **Draft Product Brief:** Fill out a rough version of [01-product-brief.md](core/docs/01-product-brief.md) with your high-level thoughts.
3. **Orient the Session:** Run [00-session-starter-prompt.md](core/prompts/00-session-starter-prompt.md) to initialize the AI's understanding.
4. **Refine Brief:** Run [01-product-manager-prompt.md](core/prompts/01-product-manager-prompt.md) to challenge and detail the brief.
5. **Explore Validation:** Run [02-business-analyst-prompt.md](core/prompts/02-business-analyst-prompt.md) if you need market research, competitor analysis, or user validation.
6. **Finalize Scope:** Use the Product Manager to establish and lock down [03-mvp-scope.md](core/docs/03-mvp-scope.md).
7. **Complete Technical Specs:** Move systematically through the UX/UI, Architecture, Data Model, Security Model, and Development Roadmap.

### Path B — I have a client request or business requirement

Use this path when you are handed a specific, structured list of requirements from a client or stakeholder.

1. **Map to Product Brief:** Translate the client request directly into [01-product-brief.md](core/docs/01-product-brief.md).
2. **Identify Unknowns:** Highlight missing assumptions, risks, and client dependencies.
3. **Use Business Analyst:** Paste the brief and run [02-business-analyst-prompt.md](core/prompts/02-business-analyst-prompt.md) to detail user personas, business rules, and validate critical constraints.
4. **Scope the MVP:** Extract the core "Must Have" features and define the boundaries in [03-mvp-scope.md](core/docs/03-mvp-scope.md).
5. **Define Layouts & Flows:** Define roles in [04-user-roles.md](core/docs/04-user-roles.md), flows in [05-user-flows.md](core/docs/05-user-flows.md), and UI layouts in [06-pages-spec.md](core/docs/06-pages-spec.md).
6. **Plan Roadmap:** Lay out the architecture and development roadmap to prepare for coding.

### Path C — I have an existing unfinished project

Use this path when inheriting or reviving a codebase that is partially implemented.

1. **Initialize Project docs:** Copy the factory template files (see Section 4) directly into your existing project.
2. **Establish the Baseline:** Audit the current state of the code. Fill out [16-context-snapshot.md](core/docs/16-context-snapshot.md) to document the current status, active modules, and existing validation rules/warnings.
3. **Fill Product Brief:** Write [01-product-brief.md](core/docs/01-product-brief.md) representing the existing product direction and targets.
4. **Audit Scope & Roadmap:** Compare existing features against [03-mvp-scope.md](core/docs/03-mvp-scope.md) and establish a clear delta sequence in [11-development-roadmap.md](core/docs/11-development-roadmap.md).
5. **Architectural Review:** Run [10-review-agent-prompt.md](core/prompts/10-review-agent-prompt.md) or [04-software-architect-prompt.md](core/prompts/04-software-architect-prompt.md) and [07-tech-lead-roadmap-prompt.md](core/prompts/07-tech-lead-roadmap-prompt.md) to audit code structure and dependencies before instructing the Coding Agent.

---

## 4. Create a New Product Project

To initialize a new product project, create the following workspace folders. Do not copy the entire `core/` folder verbatim; copy only its contents into a standard layout.

Your resulting product project structure should look like this:

my-product/
  docs/
  prompts/
  src/ or app/
  README.md

Run the following commands in your shell to copy the templates:

mkdir -p my-product/docs my-product/prompts
cp ai-software-product-factory/core/docs/* my-product/docs/
cp ai-software-product-factory/core/prompts/* my-product/prompts/

Once copied, customize your new `README.md` to reference the copied [START_HERE.md](START_HERE.md).

---

## 5. Minimum Docs Before Coding

Before you authorize the [08-coding-agent-prompt.md](core/prompts/08-coding-agent-prompt.md) to edit a single line of code, you must ensure the following documentation is completed and locked:

1. **[00-document-priority.md](core/docs/00-document-priority.md):** Customized governance model.
2. **[01-product-brief.md](core/docs/01-product-brief.md):** Core product goals and target users.
3. **[03-mvp-scope.md](core/docs/03-mvp-scope.md):** Locked MVP features (Must Haves vs. Won't Haves).
4. **[06-pages-spec.md](core/docs/06-pages-spec.md):** Mandatory for any UI work (describing layouts and states).
5. **[07-data-model.md](core/docs/07-data-model.md):** Mandatory for any data storage work.
6. **[08-architecture.md](core/docs/08-architecture.md):** Selected stack and code organization rules.
7. **[10-security-model.md](core/docs/10-security-model.md):** Mandatory if permissions, authentication, or sensitive data are involved.
8. **[11-development-roadmap.md](core/docs/11-development-roadmap.md):** Current phase and batches defined.
9. **[15-ai-agent-operating-rules.md](core/docs/15-ai-agent-operating-rules.md):** Unmodified agent rules.
10. **[16-context-snapshot.md](core/docs/16-context-snapshot.md):** Current state snapshot.
11. **[17-batch-request-template.md](core/docs/17-batch-request-template.md):** Filled out and approved for the current batch.

**If these documents are incomplete or missing, DO NOT start implementation.** Stop and run the relevant analysis and design prompts first to flesh them out.

---

## 6. Recommended Prompt Sequence

Use the prompts in the following sequence when designing and building a product. You do not need to run every single prompt for small projects, but you must not skip the PM, Tech Lead, Coding, and Review stages.

1. **[00-session-starter-prompt.md](core/prompts/00-session-starter-prompt.md):** Run at the start of every session to establish basic context.
2. **[01-product-manager-prompt.md](core/prompts/01-product-manager-prompt.md):** Define product goals, problems, and success metrics.
3. **[02-business-analyst-prompt.md](core/prompts/02-business-analyst-prompt.md):** Validate assumptions, explore risks, and write personas.
4. **[03-ux-ui-designer-prompt.md](core/prompts/03-ux-ui-designer-prompt.md):** Define user journeys, pages, and interactive layouts.
5. **[04-software-architect-prompt.md](core/prompts/04-software-architect-prompt.md):** Select the tech stack, directory layouts, and design principles.
6. **[05-database-engineer-prompt.md](core/prompts/05-database-engineer-prompt.md):** Specify database tables, foreign keys, constraints, and indexes.
7. **[06-security-engineer-prompt.md](core/prompts/06-security-engineer-prompt.md):** Detail authentication models, authorization matrices, and audit events.
8. **[07-tech-lead-roadmap-prompt.md](core/prompts/07-tech-lead-roadmap-prompt.md):** Structure the delivery timeline into phases and implementable batches.
9. **[08-coding-agent-prompt.md](core/prompts/08-coding-agent-prompt.md):** Execute approved batches. (Only run after a batch request has been approved).
10. **[09-qa-agent-prompt.md](core/prompts/09-qa-agent-prompt.md):** Test batch functionality and verify validation commands.
11. **[10-review-agent-prompt.md](core/prompts/10-review-agent-prompt.md):** Uncompromising gatekeeper that reviews code changes against batch scope.
12. **[11-bugfix-agent-prompt.md](core/prompts/11-bugfix-agent-prompt.md):** Diagnose and resolve verified issues. (Never use to add new features).
13. **[12-refactor-agent-prompt.md](core/prompts/12-refactor-agent-prompt.md):** Clean up, simplify, or restructure code. (Must not change software behavior).

---

## 7. When It Is Safe to Start Coding

You may only begin implementation when the following readiness criteria are fully met:
- [ ] **Clear Product Brief:** High-level goals are locked down.
- [ ] **Approved MVP Scope:** MoSCoW prioritization is approved and locked.
- [ ] **Specifications Completed:** All flows, UI layouts, database tables, and API endpoints for the scope are documented.
- [ ] **Security Understood:** Auth limits and permissions rules are specified.
- [ ] **Batch Approved:** A small batch request is documented using the template.
- [ ] **Current Snapshot:** The context snapshot accurately matches the workspace.
- [ ] **Clean Working Tree:** `git status` shows no uncommitted code.

---

## 8. How to Write a Good Batch Request

A batch request is a single, isolated, reviewable unit of work. It is documented by filling out the template in [17-batch-request-template.md](core/docs/17-batch-request-template.md).

- **Be Small:** One batch should be small enough to complete in a single session.
- **Be Focused:** Do not combine database migrations, UI changes, and security configurations in a single batch unless they are directly coupled. Keep them separate.
- **Provide Verification:** Always define automated verification commands and manual click-through steps.

### Batch Title Examples

- **Good Batch Title:** `P2-B1 — Create Clients List Page UI only`
- **Bad Batch Title:** `Build the whole app`
- **Bad Batch Title:** `Implement invoices and dashboard and payments`
- **Bad Batch Title:** `Refactor everything`

---

## 9. How to Review Coding Agent Output

The Coding Agent is required to run automated checks and submit an implementation report before declaring a batch complete.

### Step 1: Request Rationale & Git Status
Always request the following from the agent:
1. **Implementation Report:** Following the canonical report format.
2. **Git status:** `git status --short` to see what files were created or edited.
3. **Diff statistics:** `git diff --stat` to review the scale of edits.
4. **Full Git Diff:** `git diff` to look at the exact line modifications.
5. **Validation results:** Outputs of automated tests, builders, and linters.
6. **Manual verification notes:** Steps confirming the UI and behavior are correct.

### Step 2: Use the Review Agent
Run [10-review-agent-prompt.md](core/prompts/10-review-agent-prompt.md) to inspect the work. Provide the Review Agent with:
- The approved batch request
- The Coding Agent's implementation report
- The full git diff
- Relevant specification docs (e.g., page specs, database specs)

---

## 10. Update Rules After Every Accepted Batch

Once a batch is approved and merged, you must update the workspace index:
1. **[11-development-roadmap.md](core/docs/11-development-roadmap.md):** Mark the batch status as `Complete`.
2. **[16-context-snapshot.md](core/docs/16-context-snapshot.md):** Update the "Last Completed Batch" and "Next Batch" attributes.
3. **[14-decision-log.md](core/docs/14-decision-log.md):** Record any new scoping or architectural decisions made during the batch.
4. **Relevant Specs:** Update any database schema, page specifications, or user flows if the batch changed or clarified the source of truth.

> [!IMPORTANT]
> The Context Snapshot is for orientation only. It is a derived summary and must never be treated as the authority. If a spec is updated, modify the source document first, then update the snapshot.

---

## 11. When to Use Extension Packs

Extension packs are optional sets of templates, rules, and prompts built for specific product needs. They supplement, but do not replace, the core documentation.

> [!NOTE]
> All extension packs in the `extensions/` directory are currently **placeholders/planned** for a future version. Their README files explain when and how to apply each pack, but do not yet contain filled-in templates or prompts.

Only copy extension templates if your product requires them:
- **`rtl-i18n-pack`:** For products requiring right-to-left languages or internationalization.
- **`financial-business-logic-pack`:** For apps involving double-entry ledgers, tax rules, or financial formulas.
- **`print-reporting-pack`:** For PDF generation, data export (CSV/Excel), and reports.
- **`supabase-pack`:** For applications built on Supabase utilizing Row Level Security (RLS) and Edge Functions.
- **`saas-multitenant-pack`:** For SaaS platforms requiring tenant isolation and subscription scoping.
- **`ecommerce-pack`:** For stores with catalogs, shopping carts, order tracking, and checkouts.
- **`mobile-app-pack`:** For mobile-first or native applications requiring offline storage.
- **`erp-operations-pack`:** For back-office inventory, complex workflows, and logistics.

---

## 12. How to Use Examples

Examples in the `examples/` directory demonstrate the factory templates in action.

- **[examples/small-crud-app/README.md](examples/small-crud-app/README.md) (Invoice Tracker):** **Completed & fully filled documentation reference**. A complete reference demonstrating clients, invoices, payments, roles, roadmap, and a sample batch request. Use this to understand how to fill out your documents for simple products.
- **`medium-saas-app` / `complex-erp-app`:** **Placeholder templates (planned/future)** demonstrating how core docs scale to support larger architectures.
- **Reference, don't Copy:** Use examples to learn how to detail your specs, but do not copy their architecture or data structures unless they fit your goals.

---

## 13. Common Mistakes to Avoid

- **Vibe Coding:** Giving instructions to a Coding Agent before locking the MVP scope or database model.
- **Agent Scope Creep:** Allowing the Coding Agent to add extra fields, buttons, or cleanup edits that were not explicitly scoped in the batch request.
- **Skipping Document Priority:** Allowing conflicting files to linger without checking [00-document-priority.md](core/docs/00-document-priority.md).
- **Snapshot as Truth:** Citing `16-context-snapshot.md` as the authority for how a page or table should behave.
- **Large Batches:** Requesting an entire phase or multiple core features in one batch, leading to context loss and errors.
- **Combining Concerns:** Writing a batch that modifies database schema, updates auth rules, and rewrites UI styles simultaneously.
- **Vibe Merging:** Accepting a batch based solely on the agent's report without inspecting the `git diff`.

---

## 14. Quick Checklist Before Giving Work to Coding Agent

Before starting execution, confirm all items are checked:
- [ ] Product Brief is clear and approved.
- [ ] MVP Scope is locked.
- [ ] Relevant UI/API/Database specs are written.
- [ ] Roadmap contains the active phase and batch.
- [ ] A specific batch request is filled out using the template.
- [ ] All out-of-scope boundaries are clearly listed.
- [ ] Validation commands (lint, build, test) are defined.
- [ ] Manual click-through verification steps are written.
- [ ] Git status is clean (`git status --short` is empty).
- [ ] Context snapshot is current.
- [ ] The human owner has explicitly approved the batch request.

---

## 15. Suggested First Real Test

To get familiar with the AI Software Product Factory workflow:
1. Review the Invoice Tracker example at [examples/small-crud-app/README.md](examples/small-crud-app/README.md).
2. Create a test workspace and copy templates.
3. Draft a simple CRUD product brief (e.g., a simple task board or recipe manager).
4. Run the [01-product-manager-prompt.md](core/prompts/01-product-manager-prompt.md) to define scope.
5. Use [07-tech-lead-roadmap-prompt.md](core/prompts/07-tech-lead-roadmap-prompt.md) to define Phase 1.
6. Write your first batch request using [17-batch-request-template.md](core/docs/17-batch-request-template.md).
7. Assign it to a coding agent, review the results using [10-review-agent-prompt.md](core/prompts/10-review-agent-prompt.md), and commit the changes!
