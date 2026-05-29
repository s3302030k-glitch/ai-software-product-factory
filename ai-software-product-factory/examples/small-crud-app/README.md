# Example: Small CRUD App — Invoice Tracker

> A complete reference implementation of the AI Software Product Factory documentation templates filled out for a realistic, simple CRUD product.

---

## What This Example Demonstrates

This example demonstrates how the AI Software Product Factory applies to a small, straightforward CRUD application with:
- **5 entities** (User, Client, Invoice, InvoiceItem, Payment)
- **2 roles** (Owner, Staff)
- **10 MVP pages** (Login, Dashboard, Clients List, Client Detail, Create/Edit Client, Invoices List, Invoice Detail, Create/Edit Invoice, Record Payment, Settings)
- **Basic CRUD flows** with derived business logic calculations (no multi-currency or automated workflows)

It serves as a model showing how to scope down documentation, omit irrelevant complexity, and keep instructions clear for a solo developer or AI coding agent.

---

## What Is the Product?

**Invoice Tracker** is a web-based utility for freelancers and small business owners to track clients, manage invoices, list billing items, and manually record customer payments.

### Who It Is For
- **Freelancers** who need a lightweight tool to log hours/items and know who owes them money.
- **Small business owners** who manage a small roster of clients and want a straightforward invoicing dashboard without complex ERP setups.

### Core Entities
- **User:** The owner/staff member accessing the tool.
- **Client:** The customer profile containing billing details.
- **Invoice:** The bill sent to a client containing a status and dates.
- **InvoiceItem:** Individual line items with rate, quantity, and description.
- **Payment:** A record of a cash, check, or bank transfer transaction applied against an invoice.

---

## Which Docs Are Filled

All 18 core documentation templates under the `docs/` folder are fully populated with realistic context for the Invoice Tracker:

1. **[00-document-priority.md](docs/00-document-priority.md):** Customized governance model.
2. **[01-product-brief.md](docs/01-product-brief.md):** Defines target users, success criteria, and product goals.
3. **[02-discovery.md](docs/02-discovery.md):** Details user personas, risks, and competitor alternatives.
4. **[03-mvp-scope.md](docs/03-mvp-scope.md):** Prioritizes MVP features using the MoSCoW framework.
5. **[04-user-roles.md](docs/04-user-roles.md):** Sets up Owner and Staff roles and permissions.
6. **[05-user-flows.md](docs/05-user-flows.md):** Charts the step-by-step paths for 7 core actions.
7. **[06-pages-spec.md](docs/06-pages-spec.md):** Specifies fields, tables, actions, and validations for 10 pages.
8. **[07-data-model.md](docs/07-data-model.md):** Contains entity definitions and business validation rules.
9. **[08-architecture.md](docs/08-architecture.md):** Specifies a Next.js, PostgreSQL, and simple auth stack.
10. **[09-api-design.md](docs/09-api-design.md):** Maps REST endpoints and JSON payloads.
11. **[10-security-model.md](docs/10-security-model.md):** Details authentication, authorization, and data scoping.
12. **[11-development-roadmap.md](docs/11-development-roadmap.md):** Outlines 6 phases and 11 small, testable execution batches.
13. **[12-qa-test-plan.md](docs/12-qa-test-plan.md):** Provides QA checklist and bug reporting formats.
14. **[13-release-checklist.md](docs/13-release-checklist.md):** Outlines migration, environment, and rollback checks.
15. **[14-decision-log.md](docs/14-decision-log.md):** Tracks 5 core architectural and scoping decisions.
16. **[15-ai-agent-operating-rules.md](docs/15-ai-agent-operating-rules.md):** Enforces coding constraints and calculations.
17. **[16-context-snapshot.md](docs/16-context-snapshot.md):** Simulates initial project state snapshot at Phase 0.
18. **[17-batch-request-template.md](docs/17-batch-request-template.md):** Shows a filled template for the initial batch.

---

## Intentionally Out of Scope

To keep this example small and easy to reference, the following features are strictly out of scope:
- **Online payment processing:** No Stripe/PayPal integration.
- **Tax automation:** Taxes are calculated or entered manually.
- **Multi-currency:** All calculations are assumed in a fixed default currency (USD).
- **Multi-tenant organizations:** Only single-tenant access is supported.
- **PDF generation:** Invoices are printed or saved to PDF via standard browser print commands.
- **Email sending:** No SMTP or SendGrid setup.
- **Recurring invoices:** All invoices are created manually.
- **Inventory/Accounting ledger:** No double-entry accounting.
- **Mobile app:** Web-only responsive application.

---

## How to Read and Reference This Example

1. **Understand Governance first:** Read `docs/00-document-priority.md` and `docs/15-ai-agent-operating-rules.md`. These teach you how the AI system establishes authority.
2. **Review the Product Definition:** Look at the `Product Brief` and `MVP Scope` to see how high-level product assumptions map to a minimal scope.
3. **Trace a Flow:** Look at `docs/05-user-flows.md` and see how it maps to `docs/06-pages-spec.md` (UI), `docs/07-data-model.md` (database), and `docs/09-api-design.md` (backend API).
4. **Follow the Roadmap:** Look at `docs/11-development-roadmap.md` to see how to slice the development of this app into small, testable chunks (batches) that can be easily fed to an AI agent.
