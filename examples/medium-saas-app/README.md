# Example: Medium SaaS App — Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This is a complete reference implementation of the AI Software Product Factory documentation templates and extension packs filled out for a medium-complexity, multi-tenant B2B SaaS product. It is fully completed and intended to be used as a learning and reference tool.

> [!IMPORTANT]
> **This is a Documentation Reference, Not a Runnable Application:** This directory contains no application source code, no `package.json`, no dependencies, no database migrations, and no real data. It is a pure specification reference showing how to detail a medium-sized product before any coding starts.

---

## What the Product Is

**Team Subscription Manager** is a fictional B2B SaaS product that allows organizations and small teams to manage their software subscriptions, seats, workspace members, billing roles, and invoice history placeholders.

### Core Features Documented
- **Multi-tenancy & Workspaces**: Dynamic organization and workspace creation with isolated tenant boundaries.
- **Team Management**: User invitation flows, membership tracking, and tenant switching.
- **Role-Based Access Control (RBAC)**: Fine-grained permissions (Platform Admin, Org Owner, Workspace Admin, Billing Manager, Member, Viewer).
- **Seat & Feature Limits**: Active tracking of seat limits and feature gating based on selected plans.
- **Billing History Placeholders**: View plan pricing, subscription status, and download invoice PDF placeholders.
- **Activity Log & Basic Reports**: Audit trails of membership events and basic metrics dashboards.
- **Notification Preferences**: Customizable email and in-app alert scopes.
- **Multilingual/RTL UI readiness**: Language switching and mirrored layout guidelines.

---

## Extension Packs Demonstrated

This example details the integration of five active AI Software Product Factory extension packs:

1. **[SaaS Multi-Tenant Pack](../../extensions/saas-multitenant-pack/README.md)** — Tenant data isolation, seat limits, and subscription scoping.
2. **[Supabase Pack](../../extensions/supabase-pack/README.md)** — Row Level Security (RLS) concepts, Supabase auth schema mapping, and secure storage buckets.
3. **[Financial Business Logic Pack](../../extensions/financial-business-logic-pack/README.md)** — Precision money storage, billing boundary decoupling, and no float calculations.
4. **[Print & Reporting Pack](../../extensions/print-reporting-pack/README.md)** — Print CSS layout rules, invoice PDF layouts, and CSV export reconciliation.
5. **[RTL & i18n Pack](../../extensions/rtl-i18n-pack/README.md)** — RTL layout mirroring and translation key namespaces.

*Note: The [Mobile App Pack](../../extensions/mobile-app-pack/README.md) and [Ecommerce Pack](../../extensions/ecommerce-pack/README.md) are not implemented in this MVP, but are listed conceptually in the future product scope.*

---

## Relative Links to Reference Docs

### Core Specification Docs (`docs/`)
1. **[01-product-brief.md](docs/01-product-brief.md):** Defines target market, value proposition, and success criteria.
2. **[02-target-users.md](docs/02-target-users.md):** Detailed user personas and their specific friction points.
3. **[03-mvp-scope.md](docs/03-mvp-scope.md):** Locked MVP features using the MoSCoW prioritization framework.
4. **[04-user-roles.md](docs/04-user-roles.md):** Clear mapping of roles to permitted/restricted actions.
5. **[05-user-flows.md](docs/05-user-flows.md):** Detailed step-by-step user journeys for 13 core actions.
6. **[06-pages-spec.md](docs/06-pages-spec.md):** Comprehensive specs for the 17 pages and dashboard panels.
7. **[07-data-model.md](docs/07-data-model.md):** Logical data structures, relationships, and scoping keys.
8. **[08-architecture.md](docs/08-architecture.md):** Recommended conceptual stack layers and directories.
9. **[09-api-design.md](docs/09-api-design.md):** REST endpoints and JSON payloads for client-server communication.
10. **[10-security-model.md](docs/10-security-model.md):** Tenant isolation details, RLS rule summaries, and access logs.
11. **[11-development-roadmap.md](docs/11-development-roadmap.md):** Staged phases from foundation to final release.
12. **[12-qa-test-plan.md](docs/12-qa-test-plan.md):** QA test cases covering isolation, limits, and translation.
13. **[13-release-checklist.md](docs/13-release-checklist.md):** Operational checks for non-runnable reference verification.
14. **[14-decision-log.md](docs/14-decision-log.md):** Fictional key design decisions and rationale.
15. **[15-ai-agent-operating-rules.md](docs/15-ai-agent-operating-rules.md):** Rules restricting AI agent behavior to documentation only.
16. **[16-bug-report-template.md](docs/16-bug-report-template.md):** Bug reporting format categorized by role and impact.
17. **[17-batch-request-template.md](docs/17-batch-request-template.md):** Template for detailing individual execution batches.

### Extension Pack Integration Notes (`docs/`)
18. **[18-saas-multitenant-notes.md](docs/18-saas-multitenant-notes.md):** Multi-tenant rules, organization vs user scoping, and seat-limit checks.
19. **[19-supabase-notes.md](docs/19-supabase-notes.md):** Conceptual database schemas, RLS rules, and storage configurations.
20. **[20-financial-business-logic-notes.md](docs/20-financial-business-logic-notes.md):** Decoupled money representation, rounding, and audit rules.
21. **[21-print-reporting-notes.md](docs/21-print-reporting-notes.md):** PDF document layouts and reconciliation logic.
22. **[22-rtl-i18n-notes.md](docs/22-rtl-i18n-notes.md):** Right-to-Left CSS mirroring and localization setups.

### AI Agent Review Prompts (`prompts/`)
- **[session-starter-prompt.md](prompts/session-starter-prompt.md):** Sets context for any active coding-agent session.
- **[product-architect-prompt.md](prompts/product-architect-prompt.md):** Reviews conceptual architectural alignments.
- **[security-review-agent-prompt.md](prompts/security-review-agent-prompt.md):** Audits tenant isolation, role boundaries, and access control.
- **[saas-domain-review-agent-prompt.md](prompts/saas-domain-review-agent-prompt.md):** Audits team memberships, invites, and subscription plan limits.
- **[supabase-review-agent-prompt.md](prompts/supabase-review-agent-prompt.md):** Checks conceptual Supabase RLS and storage setup.
- **[financial-logic-review-agent-prompt.md](prompts/financial-logic-review-agent-prompt.md):** Checks monetary rounding precision and billing logic.
- **[print-reporting-review-agent-prompt.md](prompts/print-reporting-review-agent-prompt.md):** Verifies PDF layouts and export datasets.
- **[qa-agent-prompt.md](prompts/qa-agent-prompt.md):** Validates overall implementation against test plans and checklists.

---

## Recommended Reading Order

For the best understanding of how this medium-sized SaaS specification fits together, read the documents in the following order:

1. **Product Concept & Users**: Read [01-product-brief.md](docs/01-product-brief.md) and [02-target-users.md](docs/02-target-users.md).
2. **Feature Boundaries**: Read [03-mvp-scope.md](docs/03-mvp-scope.md) and [04-user-roles.md](docs/04-user-roles.md).
3. **User Interaction & Pages**: Follow the flows in [05-user-flows.md](docs/05-user-flows.md) and examine the corresponding views in [06-pages-spec.md](docs/06-pages-spec.md).
4. **Data & API Structures**: Inspect [07-data-model.md](docs/07-data-model.md) and [09-api-design.md](docs/09-api-design.md).
5. **Security & Integration**: Look at [10-security-model.md](docs/10-security-model.md) and the specialized integration notes ([18-saas-multitenant-notes.md](docs/18-saas-multitenant-notes.md) to [22-rtl-i18n-notes.md](docs/22-rtl-i18n-notes.md)).
6. **Implementation Strategy**: Review the roadmap in [11-development-roadmap.md](docs/11-development-roadmap.md) and the prompts in the `prompts/` folder.

---

## Privacy and Data Guardrail

> [!WARNING]
> No real customer data, tenant details, payment tokens, invoice values, banking credentials, project identifiers, SMTP settings, or private business secrets are included in this example. All references are entirely fictional placeholders to serve as a structural blueprint.
