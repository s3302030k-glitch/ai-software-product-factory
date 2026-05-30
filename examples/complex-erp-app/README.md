# Example: Complex ERP App — Integrated Operations ERP

> **Status: Completed & Fully Filled Documentation Reference**

This is a complete reference implementation of the AI Software Product Factory documentation templates and extension packs filled out for a complex, multi-module ERP-style business operations product. It is fully completed and intended to be used as a learning and reference tool.

> [!IMPORTANT]
> **This is a Documentation Reference, Not a Runnable Application:** This directory contains no application source code, no `package.json`, no dependencies, no database migrations, and no real data. It is a pure specification reference showing how to detail a complex ERP-style product before any coding starts.

> [!WARNING]
> No real customer data, supplier data, tenant data, payment data, invoice data, bank data, tax IDs, credentials, inventory records, shipment records, warehouse records, or private business information is included. All entities, names, and values are fictional placeholders to serve as a structural blueprint only.

---

## What the Product Is

**Integrated Operations ERP** is a fictional ERP-style business operations system used by a multi-department trading and warehouse company to manage its core operational domains in a unified platform.

### Core Domains Documented

- **Organizations & Departments**: Multi-department structure with operational scoping.
- **Supplier & Customer Placeholders**: Master data records for placeholder suppliers and customers.
- **Products / SKUs / Items**: Product catalog with SKU-level tracking.
- **Warehouses & Zones**: Warehouse facility definitions, zone layouts, and location tracking.
- **Stock Balances & Movements**: Inventory levels derived from stock movement records as the source of truth.
- **Receiving**: Goods receipt workflow from supplier shipments into warehouse stock.
- **Stock Adjustments & Transfers**: Approved quantity corrections and cross-location transfers.
- **Purchase Requests & Purchase Orders**: Procurement workflow placeholders with approval gates.
- **Sales Orders & Dispatch Placeholders**: Sales fulfillment workflow placeholders.
- **Invoice & Payment Placeholders**: Financial document and payment record placeholders.
- **Approval Workflows**: Multi-level approval authority separated from data-entry authority.
- **Operational Dashboard & Reports**: KPI summaries and operational report exports.
- **Audit Trail**: Immutable event log for all state changes.
- **Print / Export Placeholders**: Purchase order PDFs, invoice PDFs, and stock movement exports.
- **RTL / i18n Readiness**: Layout mirroring and translation key structure.
- **Optional Mobile Warehouse Flows**: Documented as future scope only; not implemented in this example.

---

## Extension Packs Demonstrated

This example demonstrates integration of six active AI Software Product Factory extension packs:

1. **[ERP Operations Pack](../../extensions/erp-operations-pack/README.md)** — Master data, warehouse/zones, inventory, stock movements, receiving, dispatch placeholders, approvals, operational reporting, and audit trail.
2. **[Financial Business Logic Pack](../../extensions/financial-business-logic-pack/README.md)** — Invoice/payment placeholder amounts, money formatting, rounding concepts, currency display, and auditability boundaries.
3. **[Print & Reporting Pack](../../extensions/print-reporting-pack/README.md)** — Purchase order PDF placeholder, invoice PDF placeholder, stock movement report export, and operational report placeholders.
4. **[Supabase Pack](../../extensions/supabase-pack/README.md)** — Auth concepts, RLS conceptual policy model, storage boundaries, and no real Supabase project IDs.
5. **[RTL & i18n Pack](../../extensions/rtl-i18n-pack/README.md)** — Text direction readiness, language switching, date/number/currency formatting, and layout mirroring guidelines.
6. **[Mobile App Pack](../../extensions/mobile-app-pack/README.md)** — Optional/future mobile warehouse flows including receiving and stock count; documented as future scope only, not implemented.

*Note: The [SaaS Multitenant Pack](../../extensions/saas-multitenant-pack/README.md) and [Ecommerce Pack](../../extensions/ecommerce-pack/README.md) are listed as optional future extensions and are not implemented inside this example.*

---

## Relative Links to Reference Docs

### Core Specification Docs (`docs/`)

1. **[01-product-brief.md](docs/01-product-brief.md):** Product name, summary, problem statement, value proposition, success criteria, and risks.
2. **[02-target-users.md](docs/02-target-users.md):** Detailed user personas, goals, permissions, pain points, and key pages for each role.
3. **[03-mvp-scope.md](docs/03-mvp-scope.md):** MoSCoW prioritization of all ERP modules with explicit exclusions and future scope.
4. **[04-user-roles.md](docs/04-user-roles.md):** Role matrix with allowed/restricted actions, approval authority, and audit events.
5. **[05-user-flows.md](docs/05-user-flows.md):** Step-by-step user journeys for 16 core ERP operational flows.
6. **[06-pages-spec.md](docs/06-pages-spec.md):** Comprehensive specs for all 22 application pages with states, validations, and security notes.
7. **[07-data-model.md](docs/07-data-model.md):** Logical data entities, relationships, lifecycles, and stock source-of-truth rules.
8. **[08-architecture.md](docs/08-architecture.md):** Conceptual layers, surfaces, consistency rules, and integration boundaries.
9. **[09-api-design.md](docs/09-api-design.md):** Conceptual API groups, endpoint placeholders, auth requirements, and error cases.
10. **[10-security-model.md](docs/10-security-model.md):** Operational authorization, warehouse access, finance access, approval authority, and RLS concept.
11. **[11-development-roadmap.md](docs/11-development-roadmap.md):** 11-stage documentation roadmap with dependencies and acceptance criteria.
12. **[12-qa-test-plan.md](docs/12-qa-test-plan.md):** Comprehensive QA test plan covering all ERP domains and release readiness.
13. **[13-release-checklist.md](docs/13-release-checklist.md):** Documentation completeness checklist with owner approval gates.
14. **[14-decision-log.md](docs/14-decision-log.md):** Key design decisions with rationale, alternatives, and approval status.
15. **[15-ai-agent-operating-rules.md](docs/15-ai-agent-operating-rules.md):** Agent behavior constraints adapted for this ERP documentation example.
16. **[16-bug-report-template.md](docs/16-bug-report-template.md):** Bug report format with ERP-specific context fields.
17. **[17-batch-request-template.md](docs/17-batch-request-template.md):** Batch request template for future edits to this documentation example.

### Extension Pack Integration Notes (`docs/`)

18. **[18-erp-operations-notes.md](docs/18-erp-operations-notes.md):** How the ERP Operations Pack applies to master data, inventory, warehouse, approvals, and reporting.
19. **[19-financial-business-logic-notes.md](docs/19-financial-business-logic-notes.md):** How the Financial Business Logic Pack applies to invoice/payment placeholders, money, and rounding.
20. **[20-print-reporting-notes.md](docs/20-print-reporting-notes.md):** How the Print & Reporting Pack applies to PO PDFs, invoice PDFs, and stock exports.
21. **[21-supabase-notes.md](docs/21-supabase-notes.md):** How the Supabase Pack conceptually applies to auth, RLS, and storage boundaries.
22. **[22-rtl-i18n-notes.md](docs/22-rtl-i18n-notes.md):** How the RTL/i18n Pack applies to text direction, formatting, and layout mirroring.
23. **[23-mobile-app-notes.md](docs/23-mobile-app-notes.md):** How the Mobile App Pack may apply as future/optional mobile warehouse flows.

### AI Agent Review Prompts (`prompts/`)

- **[session-starter-prompt.md](prompts/session-starter-prompt.md):** Sets context for any new coding-agent session working on this example.
- **[product-architect-prompt.md](prompts/product-architect-prompt.md):** Reviews ERP product architecture, module boundaries, and documentation completeness.
- **[erp-domain-review-agent-prompt.md](prompts/erp-domain-review-agent-prompt.md):** Audits master data, warehouse, inventory, receiving, dispatch, approvals, and reports.
- **[inventory-workflow-review-agent-prompt.md](prompts/inventory-workflow-review-agent-prompt.md):** Audits stock source-of-truth, movements, receiving, adjustments, transfers, and derived balances.
- **[financial-logic-review-agent-prompt.md](prompts/financial-logic-review-agent-prompt.md):** Audits invoice/payment placeholders, money formatting, rounding, and no tax/accounting claims.
- **[print-reporting-review-agent-prompt.md](prompts/print-reporting-review-agent-prompt.md):** Audits PO PDF placeholder, invoice PDF, stock movement report, and operational exports.
- **[security-review-agent-prompt.md](prompts/security-review-agent-prompt.md):** Audits operational authorization, warehouse access, finance access, approval authority, and RLS.
- **[mobile-operations-review-agent-prompt.md](prompts/mobile-operations-review-agent-prompt.md):** Reviews optional/future mobile warehouse flows, offline risk, and no mobile implementation.
- **[qa-agent-prompt.md](prompts/qa-agent-prompt.md):** Validates the full example against the QA plan and release checklist.

---

## Recommended Reading Order

For the best understanding of how this complex ERP specification fits together:

1. **Product Concept & Users**: Read [01-product-brief.md](docs/01-product-brief.md) and [02-target-users.md](docs/02-target-users.md).
2. **Feature Boundaries & Roles**: Read [03-mvp-scope.md](docs/03-mvp-scope.md) and [04-user-roles.md](docs/04-user-roles.md).
3. **User Interaction & Pages**: Follow the flows in [05-user-flows.md](docs/05-user-flows.md) and examine the corresponding views in [06-pages-spec.md](docs/06-pages-spec.md).
4. **Data & API Structures**: Inspect [07-data-model.md](docs/07-data-model.md) and [09-api-design.md](docs/09-api-design.md).
5. **Security & Authorization**: Read [10-security-model.md](docs/10-security-model.md).
6. **Architecture & Integration**: Read [08-architecture.md](docs/08-architecture.md) and the extension pack notes ([18–23](docs/)).
7. **Implementation Strategy**: Review [11-development-roadmap.md](docs/11-development-roadmap.md) and [12-qa-test-plan.md](docs/12-qa-test-plan.md).
8. **Governance**: Read [14-decision-log.md](docs/14-decision-log.md), [15-ai-agent-operating-rules.md](docs/15-ai-agent-operating-rules.md), and [13-release-checklist.md](docs/13-release-checklist.md).
9. **Prompts**: Review the `prompts/` folder to understand how to direct AI agents on this product.

---

## Privacy and Data Guardrail

> [!WARNING]
> No real customer data, supplier data, payment tokens, invoice values, banking credentials, tax IDs, inventory records, shipment records, warehouse records, project identifiers, credentials, or private business secrets are included in this example. All references are entirely fictional placeholders to serve as a structural blueprint only.
