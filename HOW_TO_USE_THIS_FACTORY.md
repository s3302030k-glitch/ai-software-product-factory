# How To Use This Factory

> Practical guide for the AI Software Product Factory — v2.0.0

---

## Purpose

This repository is a **documentation-first software product factory kit**. It gives you a complete, structured system for designing software products, preparing AI-agent prompts, and controlling implementation workflows before any code is written.

**What it is:**

- A reusable set of document templates for defining software products (brief, scope, data model, architecture, security, roadmap, and more).
- A library of AI agent role prompts (Product Manager, Architect, Database Engineer, Security Engineer, Coding Agent, QA, Review, Bugfix, and more).
- Eight domain extension packs for specialized product areas (Supabase, RTL/i18n, Financial Logic, Print/Reporting, ERP Operations, SaaS Multi-Tenant, Ecommerce, Mobile App).
- Three completed documentation reference examples (small CRUD, medium SaaS, complex ERP) showing what a fully filled product specification looks like.

**What it is not:**

- It is not a runnable application.
- It contains no runtime framework, source code, `package.json`, database migrations, or installed dependencies.
- The examples are documentation references only — they do not run as software.

---

## Who Should Use This

This kit is designed for:

- **Solo founders** who want to design products properly before handing work to AI coding agents.
- **Product owners** who need a structured way to define scope, roles, and data models.
- **AI-assisted developers** who want guardrails and controlled batch workflows instead of unstructured vibe coding.
- **Agencies** delivering client projects with repeatable, document-driven AI workflows.
- **Teams building MVPs** who need a lightweight process without a full engineering department.
- **Anyone** who wants structured AI coding-agent workflows instead of ad-hoc prompting.

---

## Quick Start

Follow these seven steps to begin using the factory for your product:

1. **Read [README.md](README.md).** Understand the overall structure, components, and workflow.
2. **Read [START_HERE.md](START_HERE.md).** Follow the operating path that matches your starting situation (raw idea, client request, or existing project).
3. **Review [FACTORY_STATUS.md](FACTORY_STATUS.md).** Confirm which extension packs and examples are available and what each version delivered.
4. **Open [RELEASE_NOTES.md](RELEASE_NOTES.md).** Understand the complete v2.0.0 contents and the full release timeline.
5. **Pick the closest reference example** for your product type:
   - [`examples/small-crud-app`](examples/small-crud-app/README.md) — for simple CRUD tools, admin utilities, or tracker apps.
   - [`examples/medium-saas-app`](examples/medium-saas-app/README.md) — for SaaS products with teams, roles, and subscription plans.
   - [`examples/complex-erp-app`](examples/complex-erp-app/README.md) — for ERP, operations, inventory, or warehouse systems.
6. **Copy core docs and prompts into a separate product repository.** Do not edit the factory templates in place (see the [What To Copy](#what-to-copy-into-your-product-project) section below).
7. **Add relevant extension packs only when needed.** Add packs that match your product domain — do not copy all of them.

---

## Choosing the Right Example

| If your product is...                          | Start with...       | Why                                                                    |
| ---------------------------------------------- | ------------------- | ---------------------------------------------------------------------- |
| Simple CRUD tool, admin panel, or tracker      | `small-crud-app`    | Shows minimal docs for simple roles, entities, and pages               |
| SaaS with teams, roles, and billing boundaries | `medium-saas-app`   | Shows organization, membership, roles, subscriptions, and reports      |
| ERP, warehouse, or operations workflow         | `complex-erp-app`   | Shows inventory, approvals, finance placeholders, reports, audit trail |

Read the example's `README.md` first. Then use it as a reference when filling in your own product's documents. Do not copy an example's data structures directly unless they match your product's goals.

---

## What To Copy Into Your Product Project

Create a **separate** repository or folder for your product. Do not build your product inside this factory repository.

Your product project should have this structure:

```
my-product/
├── docs/
└── prompts/
```

Copy the core factory templates into your product project:

```bash
mkdir -p my-product/docs my-product/prompts
cp core/docs/* my-product/docs/
cp core/prompts/* my-product/prompts/
```

Then optionally copy relevant extension pack files:

```bash
# Example: adding the Supabase pack if your product uses Supabase
cp extensions/supabase-pack/docs/* my-product/docs/
cp extensions/supabase-pack/prompts/* my-product/prompts/
```

Once copied, work entirely inside `my-product/`. Fill in each document in order, starting with `docs/01-product-brief.md`.

> Do not modify the factory's `core/` templates directly for a single product unless you are intentionally updating the factory itself for all future projects.

---

## How To Add Extension Packs

Extension packs add domain-specific guidelines, audit checklists, and specialized review prompts. Each pack supplements the core templates — it never replaces them.

**Before adding a pack:**

1. Read the pack's `README.md` to confirm it applies to your product.
2. Add only packs that match your product's actual domain needs.
3. Copy the pack's `docs/` and `prompts/` into your product's `docs/` and `prompts/` folders.
4. Use pack prompts during design reviews, code reviews, and QA cycles.

### Available Extension Packs

| Pack | When To Use |
|------|-------------|
| [`supabase-pack`](extensions/supabase-pack/README.md) | Your product uses Supabase for database, auth, Row Level Security (RLS), storage, or edge functions. |
| [`rtl-i18n-pack`](extensions/rtl-i18n-pack/README.md) | Your product needs right-to-left (RTL) language support or multilingual/locale-aware formatting. |
| [`financial-business-logic-pack`](extensions/financial-business-logic-pack/README.md) | Your product handles money, payments, pricing, invoices, currencies, rounding, or audit trails. |
| [`print-reporting-pack`](extensions/print-reporting-pack/README.md) | Your product needs PDF generation, printable layouts, data exports (CSV/Excel), or reconciled reports. |
| [`erp-operations-pack`](extensions/erp-operations-pack/README.md) | Your product includes inventory, warehouse management, receiving, approvals, or operational workflows. |
| [`saas-multitenant-pack`](extensions/saas-multitenant-pack/README.md) | Your product has SaaS organizations, workspaces, memberships, subscription plans, or tenant isolation. |
| [`ecommerce-pack`](extensions/ecommerce-pack/README.md) | Your product includes product catalogs, carts, checkout flows, orders, payments, refunds, or promotions. |
| [`mobile-app-pack`](extensions/mobile-app-pack/README.md) | Your product includes mobile apps, offline support, push notifications, device permissions, or app-store release. |

---

## Recommended Workflow With AI Agents

Use the following sequence when designing and building a product with this factory:

1. **Fill the product brief.** Edit `docs/01-product-brief.md` with your product concept, target users, and problem statement.
2. **Use the Product Manager prompt.** Run `prompts/01-product-manager-prompt.md` to refine and challenge the brief.
3. **Fill target users and MVP scope.** Complete `docs/02-discovery.md` and lock `docs/03-mvp-scope.md` using MoSCoW prioritization.
4. **Use the Architect prompt.** Run `prompts/04-software-architect-prompt.md` to select the tech stack and design principles.
5. **Fill data model, architecture, API, and security.** Complete `docs/07-data-model.md`, `docs/08-architecture.md`, `docs/09-api-design.md`, and `docs/10-security-model.md`.
6. **Use security and review prompts.** Run `prompts/06-security-engineer-prompt.md` to audit the security model before coding starts.
7. **Build the roadmap and break it into batches.** Use `prompts/07-tech-lead-roadmap-prompt.md` and `docs/11-development-roadmap.md`. Fill out `docs/17-batch-request-template.md` for the first batch.
8. **Give one batch at a time to the coding assistant.** Use `prompts/08-coding-agent-prompt.md`. Each batch should be small enough to complete in one session.
9. **Review output against docs.** Use `prompts/10-review-agent-prompt.md` to inspect the implementation report and git diff against the approved batch request.
10. **Update the decision log and context snapshot.** After each accepted batch, update `docs/14-decision-log.md` and `docs/16-context-snapshot.md`.

**Key principles:**

- Humans approve all final decisions — agents propose, owners approve.
- Keep batches small. One batch = one focused, reviewable unit of work.
- Documents are the source of truth. Code and conversation history are secondary.
- Do not let AI agents invent business rules, legal rules, tax rules, or payment policies. These must be defined in your documents first and approved by the product owner.

---

## How To Use Prompts

Each prompt file is self-contained. You can use any of them with any AI model (Claude, GPT, Gemini, etc.) or any AI coding tool.

**Session start:**

Always begin each AI session by pasting the contents of `prompts/00-session-starter-prompt.md` into your AI tool. This establishes the context and rules for the session.

**Choosing a role prompt:**

Select the role prompt that matches the work you need done. Paste the required docs or file paths alongside the prompt when starting a session.

**Require final reports:**

Each prompt is designed to produce a structured output or implementation report. Always require the agent to produce the report before accepting the batch.

**Stop on guardrails:**

If an agent produces output that conflicts with your approved scope or triggers a guardrail condition, stop the session, review, and resolve the conflict before continuing.

### Available Role Prompts

| Prompt | When To Use |
|--------|-------------|
| `00-session-starter-prompt.md` | Start of every AI session |
| `01-product-manager-prompt.md` | Defining and refining product goals, problems, and scope |
| `02-business-analyst-prompt.md` | Validating assumptions, exploring risks, writing personas |
| `03-ux-ui-designer-prompt.md` | Designing user journeys, pages, and interactive layouts |
| `04-software-architect-prompt.md` | Selecting tech stack, directory layouts, and design principles |
| `05-database-engineer-prompt.md` | Specifying database tables, foreign keys, constraints, and indexes |
| `06-security-engineer-prompt.md` | Detailing authentication models, authorization matrices, and audit events |
| `07-tech-lead-roadmap-prompt.md` | Structuring delivery timeline into phases and implementable batches |
| `08-coding-agent-prompt.md` | Executing approved batches (only after batch request is approved) |
| `09-qa-agent-prompt.md` | Testing batch functionality and verifying validation commands |
| `10-review-agent-prompt.md` | Reviewing code changes against batch scope and guardrails |
| `11-bugfix-agent-prompt.md` | Diagnosing and resolving verified issues (never for new features) |
| `12-refactor-agent-prompt.md` | Cleaning up or restructuring code without changing behavior |

---

## What Not To Do

- **Do not build runtime application code inside this repository.** The factory is a template kit, not a product workspace.
- **Do not add `package.json` here.** No dependencies, build tools, or runtime setup belong in this repository.
- **Do not add database migrations here.** Migrations belong in your separate product repository.
- **Do not store real credentials.** No API keys, tokens, passwords, or secrets of any kind.
- **Do not paste real customer, payment, bank, tax, or invoice data** into any document in this factory or your product docs.
- **Do not modify core templates directly for one product** unless you are intentionally updating the factory for all future projects.
- **Do not treat examples as runnable applications.** They contain no source code and cannot be deployed.
- **Do not let AI agents change product scope without owner approval.** Scope changes must go through the product owner, not the coding agent.

---

## Common Product Starting Paths

### Small CRUD Product Path

Best for: simple admin tools, trackers, internal dashboards, and single-tenant utilities.

Use:

- [`examples/small-crud-app`](examples/small-crud-app/README.md) as reference
- Core docs and prompts only
- Optionally: [`supabase-pack`](extensions/supabase-pack/README.md) if the backend uses Supabase

---

### SaaS Product Path

Best for: B2B/B2C SaaS platforms with organizations, team members, roles, and subscription plans.

Use:

- [`examples/medium-saas-app`](examples/medium-saas-app/README.md) as reference
- [`saas-multitenant-pack`](extensions/saas-multitenant-pack/README.md) — for tenant isolation and subscriptions
- [`supabase-pack`](extensions/supabase-pack/README.md) — if the product uses Supabase
- [`financial-business-logic-pack`](extensions/financial-business-logic-pack/README.md) — for billing and payment placeholders
- [`print-reporting-pack`](extensions/print-reporting-pack/README.md) — for invoice PDFs and report exports
- [`rtl-i18n-pack`](extensions/rtl-i18n-pack/README.md) — if the product needs multilingual or RTL support

---

### ERP / Operations Product Path

Best for: ERP systems, warehouse management, inventory, procurement workflows, and operations dashboards.

Use:

- [`examples/complex-erp-app`](examples/complex-erp-app/README.md) as reference
- [`erp-operations-pack`](extensions/erp-operations-pack/README.md) — for inventory, warehouse, approvals, and workflows
- [`financial-business-logic-pack`](extensions/financial-business-logic-pack/README.md) — for financial calculations and audit trails
- [`print-reporting-pack`](extensions/print-reporting-pack/README.md) — for PO PDFs, invoice PDFs, and operational reports
- [`supabase-pack`](extensions/supabase-pack/README.md) — if the product uses Supabase
- [`rtl-i18n-pack`](extensions/rtl-i18n-pack/README.md) — if multilingual or RTL support is needed
- [`mobile-app-pack`](extensions/mobile-app-pack/README.md) — if warehouse or field operations mobile flows are planned

---

### Ecommerce Product Path

Best for: online stores, B2B ordering portals, marketplace MVPs, and checkout-enabled platforms.

Use:

- [`ecommerce-pack`](extensions/ecommerce-pack/README.md) — for catalog, cart, checkout, orders, payments, and refunds
- [`financial-business-logic-pack`](extensions/financial-business-logic-pack/README.md) — for pricing, rounding, and payment boundaries
- [`print-reporting-pack`](extensions/print-reporting-pack/README.md) — for order invoices, receipts, and export reports
- [`mobile-app-pack`](extensions/mobile-app-pack/README.md) — if mobile commerce flows are planned
- [`saas-multitenant-pack`](extensions/saas-multitenant-pack/README.md) — only if the product has marketplace/vendor workspaces or multi-tenant access

---

## Validation Checklist Before Coding

Before authorizing any coding agent to write a single line of code, confirm all of the following:

- [ ] Product brief completed and approved by owner
- [ ] MVP scope locked using MoSCoW prioritization and approved
- [ ] User roles defined and permissions reviewed
- [ ] Pages spec completed for all in-scope pages
- [ ] Data model reviewed and approved
- [ ] Security model reviewed and approved
- [ ] Relevant extension pack notes reviewed (if applicable)
- [ ] Development roadmap broken into small, focused batches
- [ ] Current batch request document filled out using the template
- [ ] Files in scope and out of scope clearly listed in the batch request
- [ ] Owner approval gates explicitly confirmed before coding starts
- [ ] No real, private, or sensitive data present in any document

---

## Final Notes

- This kit is complete through **v2.0.0**: Core Factory + 8 extension packs + 3 completed reference examples.
- Future additions — new extension packs, translated guides, or tooling integrations — are optional and not required to use the factory.
- The best use of this repository is to **copy the templates into a separate product workspace** and run controlled AI-agent batches from there, keeping the factory kit clean and reusable for future products.
- See [RELEASE_NOTES.md](RELEASE_NOTES.md) for the complete v2.0.0 audit summary and non-runnable boundary confirmation.
- See [FACTORY_STATUS.md](FACTORY_STATUS.md) for the current extension pack matrix and version timeline.
