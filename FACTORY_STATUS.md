# AI Software Product Factory — Status Report

## 1. Current Release Summary

* **Current latest release:** v2.1.0
* **Repository status:** public/template-ready
* **Main purpose:** reusable AI-assisted software product factory kit
* **Current maturity:** Core Factory + 8 implemented extension packs + 3 completed documentation reference examples + public usability guides

The completed examples are:
* small-crud-app
* medium-saas-app
* complex-erp-app

The public usability guides are:
* [GLOSSARY.md](GLOSSARY.md)
* [CONTRIBUTING.md](CONTRIBUTING.md)
* [TROUBLESHOOTING.md](TROUBLESHOOTING.md)
* [HOW_TO_USE_THIS_FACTORY.md](HOW_TO_USE_THIS_FACTORY.md)
* [RELEASE_NOTES.md](RELEASE_NOTES.md)

---

## 2. Version Timeline

| Version | Commit Purpose | Main Deliverable | Status |
| --- | --- | --- | --- |
| v1.0.0 | Core Factory baseline | Core docs, prompts, governance, START_HERE, Invoice Tracker example | Released |
| v1.1.0 | Supabase Extension Pack | Supabase docs/prompts for RLS, migrations, auth, storage, edge functions | Released |
| v1.2.0 | RTL/i18n Extension Pack | RTL UI, localization, formatting, translation keys, localization QA | Released |
| v1.3.0 | Financial Business Logic Pack | Money, currency, calculations, payment/settlement, audit, units, financial QA | Released |
| v1.4.0 | Print Reporting Pack | Print/PDF/export/reporting docs and prompts | Released |
| v1.5.0 | ERP Operations Pack | ERP operations, inventory, warehouse, workflow, audit, operational reporting docs/prompts | Released |
| v1.6.0 | SaaS Multitenant Pack | SaaS organizations, tenants, memberships, permissions, subscriptions, billing boundary, tenant isolation docs/prompts | Released |
| v1.7.0 | Ecommerce Pack | Ecommerce catalog, product/SKU/variant, cart, checkout, orders, payments, refunds, promotions, reporting docs/prompts | Released |
| v1.8.0 | Mobile App Pack | Mobile product scope, UX/navigation, offline sync, device permissions, push notifications, mobile security/privacy, app-store release, mobile QA docs/prompts | Released |
| v1.9.0 | Medium SaaS App Reference Example | Completed documentation reference for Team Subscription Manager, a fictional B2B SaaS product demonstrating multitenancy, Supabase concepts, financial placeholders, print/reporting, and RTL/i18n readiness | Released |
| v2.0.0 | Complex ERP App Reference Example | Completed documentation reference for Integrated Operations ERP, a fictional complex multi-module ERP operations product demonstrating ERP Operations, Financial, Print/Reporting, Supabase, RTL/i18n, and Mobile App concepts where relevant | Released |
| v2.1.0 | Public Usability Polish Guides | Added glossary, contribution guide, troubleshooting guide, improved README navigation, example numbering note, and API style adaptation guidance | Released |

---

## 3. What the Factory Contains

### Core Factory

* [core/docs/](core/docs/)
* [core/prompts/](core/prompts/)
* Governance model
* Document priority (defined in [core/docs/00-document-priority.md](core/docs/00-document-priority.md))
* AI agent operating rules (defined in [core/docs/15-ai-agent-operating-rules.md](core/docs/15-ai-agent-operating-rules.md))
* Batch request template (defined in [core/docs/17-batch-request-template.md](core/docs/17-batch-request-template.md))
* QA/review/report formats
* [START_HERE.md](START_HERE.md)

### Completed Examples

* [examples/small-crud-app/](examples/small-crud-app/) (Invoice Tracker)
  * Completed documentation reference, not runnable application source code.
  * See the [examples/small-crud-app/README.md](examples/small-crud-app/README.md) for details.
* [examples/medium-saas-app/](examples/medium-saas-app/) (Team Subscription Manager)
  * Completed documentation reference, not runnable application source code.
  * See the [examples/medium-saas-app/README.md](examples/medium-saas-app/README.md) for details.
* [examples/complex-erp-app/](examples/complex-erp-app/) (Integrated Operations ERP)
  * Completed documentation reference, not runnable application source code.
  * See the [examples/complex-erp-app/README.md](examples/complex-erp-app/README.md) for details.

### Implemented Extension Packs

* [supabase-pack](extensions/supabase-pack/README.md)
* [rtl-i18n-pack](extensions/rtl-i18n-pack/README.md)
* [financial-business-logic-pack](extensions/financial-business-logic-pack/README.md)
* [print-reporting-pack](extensions/print-reporting-pack/README.md)
* [erp-operations-pack](extensions/erp-operations-pack/README.md)
* [saas-multitenant-pack](extensions/saas-multitenant-pack/README.md)
* [ecommerce-pack](extensions/ecommerce-pack/README.md)
* [mobile-app-pack](extensions/mobile-app-pack/README.md)

### Placeholder / Future Examples

* None at this stage.

### Placeholder / Future Extension Packs

* None at this stage.

---

## 4. Extension Pack Matrix

| Pack | Status | Use When | Key Risks Covered |
| ---- | ------ | -------- | ----------------- |
| [supabase-pack](extensions/supabase-pack/README.md) | Implemented | Product uses Supabase database/auth/RLS/storage/edge functions | Unsafe RLS, service role exposure, migration mistakes, storage policy errors, edge function auth mistakes |
| [rtl-i18n-pack](extensions/rtl-i18n-pack/README.md) | Implemented | Product needs RTL, multilingual content, locale formatting, translation keys, localization QA | Broken RTL layout, hardcoded strings, translation key drift, locale formatting errors, mixed-direction text issues |
| [financial-business-logic-pack](extensions/financial-business-logic-pack/README.md) | Implemented | Product has money, payments, currency, units, invoices, settlement, financial reports, audit approvals | Money calculation errors, rounding mistakes, payment/settlement confusion, missing audit trail, unit conversion errors |
| [print-reporting-pack](extensions/print-reporting-pack/README.md) | Implemented | Product needs PDF, print layouts, exports, reports, invoices, contracts | UI/export mismatch, print layout breakage, report totals inconsistency |
| [erp-operations-pack](extensions/erp-operations-pack/README.md) | Implemented | Use when: product has inventory, warehouse, receiving, shipping, workflows, approvals, operational audit trails, operational reporting, or ERP-style operations. | Key risks: inventory mismatch, stock movement ambiguity, warehouse location confusion, approval bypass, workflow state mistakes, operational report mismatch. |
| [saas-multitenant-pack](extensions/saas-multitenant-pack/README.md) | Implemented | Use when: product has SaaS organizations, tenants, workspaces, team memberships, roles, subscriptions, plans, tenant switching, billing boundaries, or tenant-scoped data. | Key risks: tenant data leakage, incorrect organization/member scoping, permission boundary mistakes, plan gating errors, subscription state confusion, billing/access coupling mistakes, cross-tenant export/report leaks. |
| [ecommerce-pack](extensions/ecommerce-pack/README.md) | Implemented | Use when: product has product catalogs, SKUs, variants, carts, checkout flows, orders, payment/refund states, promotions, discounts, inventory reservation, fulfillment, customer accounts, ecommerce reports, or marketplace-style transactions. | Key risks: cart total mismatch, price calculation mistakes, discount stacking errors, checkout state confusion, order/payment mismatch, refund ambiguity, inventory reservation mismatch, variant/SKU confusion, customer data exposure, report/export mismatch. |
| [mobile-app-pack](extensions/mobile-app-pack/README.md) | Implemented | Use when: product has mobile apps, responsive-mobile-first experiences, native device permissions, offline usage, sync behavior, push notifications, deep links, app-store readiness, mobile auth, mobile privacy, mobile QA, or device-specific UX. | Key risks: mobile scope creep, desktop-first UX copied into mobile, broken navigation/back behavior, unsafe offline sync, duplicate writes after reconnect, permission overreach, push notification privacy leaks, deep link security problems, weak token storage/session handling, app-store readiness gaps. |

---

## 5. Current Readiness Assessment

* **Core Factory:** Production-ready as documentation/template kit.
* **Extension framework:** Established.
* **Implemented packs:** 8
* **Completed documentation reference examples:** 3
* **Placeholder examples:** 0
* **Runtime application code:** not included by design
* **Public usability polish:** completed and released as v2.1.0
* **Current roadmap:** complete through v2.1.0
* **Security/secrets:** No credentials or private project data intended.

> [!NOTE]
> This repository is a software product factory template and workflow kit. It is not a finished software application.

---

## 6. How to Use This Repository Today

1. Start with [START_HERE.md](START_HERE.md).
2. Copy core docs/prompts into a new product workspace.
3. Fill Product Brief, MVP Scope, Pages Spec, Data Model, Architecture, Security, Roadmap.
4. Use role prompts for product/design/architecture/review.
5. Add extension packs only when needed.
6. Use batch request template before giving work to a coding agent.
7. Review coding agent output using implementation report, git diff, validation results, and review prompts.

---

## 7. Recommended Next Roadmap

* **Roadmap Complete:** All planned extension packs and documentation reference examples (Simple, Medium, Complex) have been successfully completed and released. No further core components are planned.

---

## 8. Governance and Safety Notes

* **Human owner** makes final product/scope/security/business decisions.
* **AI agents** propose, review, or execute within approved scope.
* **Document Priority** (defined in [core/docs/00-document-priority.md](core/docs/00-document-priority.md)) controls conflicts.
* **Context Snapshot** is orientation only.
* **Coding starts only after** enough docs and a batch request are approved.
* **Extension packs** supplement core docs, not replace them.
* **No pack** should be treated as legal, tax, accounting, investment, or regulated professional advice.

---

## 9. Current Repository Health

* **Working tree status:** Clean (`git status --short` is empty).
* **Branch status:** `main` is up to date with remote `origin/main`.
* **Latest tag:** `v2.1.0`
* **All tagged versions:**
  * `v1.0.0`
  * `v1.1.0`
  * `v1.2.0`
  * `v1.3.0`
  * `v1.4.0`
  * `v1.5.0`
  * `v1.6.0`
  * `v1.7.0`
  * `v1.8.0`
  * `v1.9.0`
  * `v2.0.0`
  * `v2.1.0`
* **Latest release tag commit:** `534de53` (`docs: add public usability polish guides`)
* **Latest repository status commit:** `534de53` (`docs: add public usability polish guides`)
