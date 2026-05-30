# AI Software Product Factory

> A reusable documentation and prompt system that simulates a full software product team using AI agents.

Project status: see [FACTORY_STATUS.md](FACTORY_STATUS.md).

New here? Start with [START_HERE.md](START_HERE.md).

---

## What to Read First

To familiarize yourself with the governance, rules, and workspace setup:

1. **[START_HERE.md](START_HERE.md):** The primary user guide and workspace setup tutorial.
2. **[00-document-priority.md](core/docs/00-document-priority.md):** The governance document defining document authority.
3. **[15-ai-agent-operating-rules.md](core/docs/15-ai-agent-operating-rules.md):** Strict behavior constraints and stop conditions for AI agents.
4. **[01-product-brief.md](core/docs/01-product-brief.md):** The template for high-level product definitions.

---

## Recommended First Steps

If you are new to the AI Software Product Factory:

1. **Read the Guide:** Open [START_HERE.md](START_HERE.md) to understand the operating rules and start paths.
2. **Review the Completed Example:** Read the [small-crud-app README](examples/small-crud-app/README.md) to see a fully populated Invoice Tracker.
3. **Draft your Product Brief:** Create your workspace, copy the template folders, and edit `docs/01-product-brief.md`.

---

## Workspace Components Explained

The factory is organized into four main areas:

- **Core Documentation Templates (`core/docs/`):** Standardized, blank templates (e.g., MVP scope, data model, security) that form your project's source of truth. Copy these into your workspace and edit.
- **Core Role Prompts (`core/prompts/`):** Role-specific prompts that configure general AI models into dedicated team roles (e.g., Product Manager, Developer, QA).
- **Examples (`examples/`):** Reference implementations demonstrating how to scale and fill in templates for different product sizes.
- **Extension Packs (`extensions/`):** Optional, domain-specific add-ons (e.g., Supabase RLS, multi-tenant billing) that supplement the core documentation.

---

## Current Status of Examples and Extensions

To guide your reference, here is the implementation status of folders in this repository:

- **[examples/small-crud-app](examples/small-crud-app/README.md) (Invoice Tracker):** **Completed & Fully Filled Documentation Reference**. Contains fully populated templates and a mock batch sequence.
- **[examples/medium-saas-app](examples/medium-saas-app/README.md) (Team Subscription Manager):** **Completed & Fully Filled Documentation Reference**. Contains fully populated templates and AI agent prompts.
- **[examples/complex-erp-app](examples/complex-erp-app/README.md) (Integrated Operations ERP):** **Completed & Fully Filled Documentation Reference**. Contains fully populated templates and AI agent prompts.
- **Implemented Extension Packs:**
  - **[supabase-pack](extensions/supabase-pack/README.md)**
  - **[rtl-i18n-pack](extensions/rtl-i18n-pack/README.md)**
  - **[financial-business-logic-pack](extensions/financial-business-logic-pack/README.md)**
  - **[print-reporting-pack](extensions/print-reporting-pack/README.md)**
  - **[erp-operations-pack](extensions/erp-operations-pack/README.md)**
  - **[saas-multitenant-pack](extensions/saas-multitenant-pack/README.md)**
  - **[ecommerce-pack](extensions/ecommerce-pack/README.md)**
  - **[mobile-app-pack](extensions/mobile-app-pack/README.md)**
- **Placeholder / Planned Extension Packs:**
  - None at this stage

---

## What Is This?

The **AI Software Product Factory** is a structured template that lets you build software products by orchestrating AI agents into specialized roles — Product Manager, Architect, Developer, QA, and more. Instead of a single prompt or ad-hoc conversations, this system provides a complete document-driven workflow that mirrors how real product teams operate.

Every decision is documented. Every agent has a defined scope. Every change follows a traceable batch process from idea through release.

---

## Who Is This For?

- **Solo developers** who want structured AI assistance across all product disciplines
- **Small teams** using AI agents to augment their capacity
- **Technical founders** building MVPs with AI-assisted development
- **Agencies** delivering client projects with repeatable AI workflows
- **Anyone** who wants to move beyond "vibe coding" to disciplined AI-assisted product development

---

## How It Simulates a Software Product Team

Each AI agent is given a **role prompt** that defines:

- What they are responsible for
- What documents they must read before working
- What they are allowed to do (and what they must not touch)
- What output format they must follow
- When they must stop and ask for human review

The agents work within a **document-driven system** where:

1. Product decisions are captured in structured docs
2. Work is broken into traceable batches
3. Every agent reads the same source of truth
4. Scope is enforced through guardrails and operating rules
5. Changes are reviewed before acceptance

```
┌─────────────────────────────────────────────────────────────┐
│                    HUMAN PRODUCT OWNER                       │
│              (You — makes all final decisions)               │
└──────────────────────────┬──────────────────────────────────┘
                           │
          ┌────────────────┼────────────────┐
          ▼                ▼                ▼
   ┌─────────────┐ ┌─────────────┐ ┌──────────────┐
   │   Product    │ │  Business   │ │   UX / UI    │
   │   Manager   │ │   Analyst   │ │   Designer   │
   └──────┬──────┘ └──────┬──────┘ └──────┬───────┘
          │               │               │
          ▼               ▼               ▼
   ┌─────────────────────────────────────────────┐
   │         Core Documentation System            │
   │  (Product Brief → Discovery → MVP Scope →   │
   │   Flows → Specs → Data Model → API → ...)   │
   └──────────────────────┬──────────────────────┘
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
   ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
   │  Software   │ │  Database   │ │  Security   │
   │  Architect  │ │  Engineer   │ │  Engineer   │
   └──────┬──────┘ └──────┬──────┘ └──────┬──────┘
          │               │               │
          ▼               ▼               ▼
   ┌─────────────────────────────────────────────┐
   │           Development Roadmap                │
   │      (Phases → Batches → Tasks)              │
   └──────────────────────┬──────────────────────┘
                          │
          ┌───────────────┼───────────────┐
          ▼               ▼               ▼
   ┌─────────────┐ ┌─────────────┐ ┌─────────────┐
   │   Coding    │ │  QA Agent   │ │  Review     │
   │   Agent     │ │             │ │  Agent      │
   └─────────────┘ └─────────────┘ └─────────────┘
          │               │               │
          ▼               ▼               ▼
   ┌─────────────┐ ┌─────────────┐
   │  Bugfix     │ │  Refactor   │
   │  Agent      │ │  Agent      │
   └─────────────┘ └─────────────┘
```

---

## Core Workflow: From Idea to Release

```
1. DEFINE     →  Product Brief, Discovery, MVP Scope
2. DESIGN     →  User Roles, User Flows, Page Specs
3. MODEL      →  Data Model, Architecture, API Design
4. SECURE     →  Security Model
5. PLAN       →  Development Roadmap (Phases & Batches)
6. BUILD      →  Coding Agent executes batches
7. TEST       →  QA Agent validates each batch
8. REVIEW     →  Review Agent assesses compliance
9. FIX        →  Bugfix Agent resolves issues
10. REFACTOR  →  Refactor Agent improves code quality
11. RELEASE   →  Release Checklist, Post-release monitoring
```

Each step feeds into the next. Documents reference each other. Agents read the outputs of earlier agents before beginning their work.

---

## Folder Structure

### In the Factory Template Repository

```
ai-software-product-factory/
│
├── README.md                          # This file
│
├── core/
│   ├── docs/                          # Reusable document templates
│   │   ├── 00-document-priority.md    # Governance: authority & conflict rules
│   │   ├── 01-product-brief.md        # Product definition
│   │   ├── 02-discovery.md            # Research & validation
│   │   ├── 03-mvp-scope.md            # MoSCoW prioritization
│   │   ├── 04-user-roles.md           # Roles & permissions
│   │   ├── 05-user-flows.md           # Step-by-step user journeys
│   │   ├── 06-pages-spec.md           # Page-level specifications
│   │   ├── 07-data-model.md           # Entities, fields, relationships
│   │   ├── 08-architecture.md         # Tech stack & principles
│   │   ├── 09-api-design.md           # API endpoints & contracts
│   │   ├── 10-security-model.md       # Auth, authorization, audit
│   │   ├── 11-development-roadmap.md  # Phases, batches, tracking
│   │   ├── 12-qa-test-plan.md         # Testing strategy & checklists
│   │   ├── 13-release-checklist.md    # Go-live verification
│   │   ├── 14-decision-log.md         # Architectural decision records
│   │   ├── 15-ai-agent-operating-rules.md  # Agent behavior constraints
│   │   ├── 16-context-snapshot.md     # Orientation summary (not authority)
│   │   └── 17-batch-request-template.md    # Work unit template
│   │
│   └── prompts/                       # AI agent role definitions
│       ├── 00-how-to-use-prompts.md   # Guide to using prompts
│       ├── 00-session-starter-prompt.md # Session initialization
│       ├── 01-product-manager-prompt.md
│       ├── 02-business-analyst-prompt.md
│       ├── 03-ux-ui-designer-prompt.md
│       ├── 04-software-architect-prompt.md
│       ├── 05-database-engineer-prompt.md
│       ├── 06-security-engineer-prompt.md
│       ├── 07-tech-lead-roadmap-prompt.md
│       ├── 08-coding-agent-prompt.md
│       ├── 09-qa-agent-prompt.md
│       ├── 10-review-agent-prompt.md
│       ├── 11-bugfix-agent-prompt.md
│       └── 12-refactor-agent-prompt.md
│
├── extensions/                        # Optional capability packs
│   ├── rtl-i18n-pack/
│   ├── financial-business-logic-pack/
│   ├── print-reporting-pack/
│   ├── saas-multitenant-pack/
│   ├── ecommerce-pack/
│   ├── mobile-app-pack/
│   ├── supabase-pack/
│   └── erp-operations-pack/
│
└── examples/                          # Reference implementations
    ├── small-crud-app/
    ├── medium-saas-app/
    └── complex-erp-app/
```

### In Your Product Project (After Copying)

```
my-project/
├── docs/                              # Copied from core/docs/
│   ├── 00-document-priority.md
│   ├── 01-product-brief.md
│   ├── ...
│   └── 17-batch-request-template.md
│
└── prompts/                           # Copied from core/prompts/
    ├── 00-how-to-use-prompts.md
    ├── 00-session-starter-prompt.md
    ├── ...
    └── 12-refactor-agent-prompt.md
```

---

## How to Use Core Docs

The factory template repository keeps reusable templates in `core/docs/` and `core/prompts/`. When starting a real product project, copy them into your project:

```bash
# Copy document templates into your project
cp core/docs/*  my-project/docs/

# Copy prompt templates into your project
cp core/prompts/*  my-project/prompts/
```

Inside your product project, you will have:
- `docs/` — all document templates (product brief, MVP scope, data model, etc.)
- `prompts/` — all AI agent role prompts

Then:

1. **Start** with `docs/01-product-brief.md` — fill in your product idea
2. **Work through** each document in numeric order
3. **Skip** documents that don't apply (e.g., `docs/09-api-design.md` if you're using a BaaS)
4. **Reference** `docs/00-document-priority.md` when documents conflict
5. **Update** `docs/16-context-snapshot.md` at the end of each working session

### Document Authority

File numbers (`00-`, `01-`, `02-`, etc.) exist for **reading order and grouping only**. They do not determine authority.

Authority is defined exclusively by `docs/00-document-priority.md`, which specifies:

- **`15-ai-agent-operating-rules.md`** — highest authority; defines agent behavior constraints
- **`00-document-priority.md`** — governance document; defines authority and conflict rules
- **`01-product-brief.md`** — defines what the product is
- **`03-mvp-scope.md`** — defines what is in/out of version 1
- **`16-context-snapshot.md`** — orientation only; never overrides other docs

See `docs/00-document-priority.md` for the full authority hierarchy and conflict resolution rules.

---

## How to Use Role Prompts

1. **Read** `prompts/00-how-to-use-prompts.md` for the complete guide
2. **Start** each AI session with `prompts/00-session-starter-prompt.md`
3. **Choose** the role prompt matching the work you need done
4. **Provide** the required inputs listed in each prompt
5. **Review** the agent's output against the guardrails

### Prompt Workflow

```
Session Start → Role Selection → Required Reading → Work → Output → Review
```

Each prompt is self-contained. You can use them with any AI model (Claude, GPT, Gemini, etc.) or any AI coding tool.

---

## How Extension Packs Work

Extension packs add domain-specific documents, prompts, and guardrails to the core system. They are **optional** and should be added only when relevant.

### Using an Extension Pack

1. Browse the `extensions/` folder in the factory template repository
2. Read the pack's `README.md` to determine if it applies
3. When the pack is fully built, copy its doc templates into your project's `docs/` folder and its prompt templates into your project's `prompts/` folder
4. Extension docs supplement — never replace — core docs

### Available Packs

| Pack | Use Case |
|------|----------|
| `rtl-i18n-pack` | Right-to-left languages, internationalization |
| `financial-business-logic-pack` | Financial calculations, audit trails, compliance |
| `print-reporting-pack` | PDF generation, print layouts, report templates |
| `saas-multitenant-pack` | Multi-tenant architecture, tenant isolation |
| `ecommerce-pack` | Product catalogs, carts, payments, orders |
| `mobile-app-pack` | Mobile-first design, native features, offline support |
| `supabase-pack` | Supabase-specific patterns, RLS, Edge Functions |
| `erp-operations-pack` | ERP modules, workflows, inventory, operations |

---

## How Examples Should Be Used

Examples in the `examples/` folder demonstrate the factory applied to different scales of product:

| Example | Complexity | Demonstrates |
|---------|-----------|--------------|
| `small-crud-app` | Low | Basic CRUD with 2-3 entities, simple roles |
| `medium-saas-app` | Medium | Multi-tenant SaaS with subscriptions, dashboards |
| `complex-erp-app` | High | Full ERP with modules, workflows, reporting |

Each example will contain filled-in versions of core docs showing how the templates scale from simple to complex products. Use them as reference when filling in your own project docs.

---

## Quick Start

```bash
# 1. Create your project and copy the templates
mkdir -p my-project/docs my-project/prompts
cp ai-software-product-factory/core/docs/*    my-project/docs/
cp ai-software-product-factory/core/prompts/*  my-project/prompts/

# 2. Fill in the product brief
# Edit my-project/docs/01-product-brief.md

# 3. Start an AI session with the session starter prompt
# Paste contents of my-project/prompts/00-session-starter-prompt.md into your AI tool

# 4. Use the Product Manager prompt to refine your brief
# Paste contents of my-project/prompts/01-product-manager-prompt.md

# 5. Work through each document and role in order
```

---

## Principles

1. **Documents are the source of truth** — not conversation history
2. **Agents have boundaries** — they work within their defined scope
3. **Batches are the unit of work** — small, traceable, reviewable
4. **Humans make decisions** — agents propose, humans approve
5. **Everything is traceable** — decisions, changes, and rationale are logged

---

## License

This template is provided as-is for use in your own projects. Customize it freely to match your workflow.
