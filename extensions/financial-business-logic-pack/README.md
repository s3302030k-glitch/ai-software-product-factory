# Extension Pack: Financial Business Logic

> Adds financial calculation guardrails, audit trail requirements, currency representation models, and compliance-ready documentation templates.

This is a fully implemented extension pack, supplementing the core factory templates.

---

## Purpose and Scope

This pack **supplements** the core factory documents; it does **not** replace them. It is designed for software products that handle money, payments, currencies, pricing, quantities, units, settlements, invoices, commissions, financial reports, approval workflows, or audit trails.

It is useful for building SaaS billing systems, invoicing applications, ERP modules, trading and operations platforms, marketplaces, finance dashboards, and business workflow software.

This pack is strictly template-based and generic. It does not contain product-specific details, private business information, real credentials, real project IDs, real contracts, or real migrations.

> [!WARNING]
> **NO LEGAL OR FINANCIAL ADVICE**: This extension pack does not provide legal, tax, accounting, investment, or regulated financial advice. All templates and prompts must be audited and approved by the product owner's accounting, legal, and tax professionals before deployment.

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| **Incorrect Money Calculations** | Enforces exact calculation formulas and decimal precision boundaries. |
| **Floating Point Errors** | Bans binary floating point representation (`float`/`double`) for monetary data. |
| **Inconsistent Rounding** | Mandates explicit rounding modes and documents exact rounding timing. |
| **Currency Conversion Mistakes** | Establishes ISO 4217 currency tracking and rate timestamping rules. |
| **Unit Conversion Mistakes** | Outlines base unit storage and explicit unit conversion validation checklists. |
| **Payment/Settlement State Mistakes** | Defines explicit lifecycle states, separating payments from final settlements. |
| **Editing Financial Records Without Audit** | Restricts records modification and mandates actor/timestamp/reason logging. |
| **Changing Quantities Without Approval** | Requires trace history and human owner approval for quantity/price adjustments. |
| **Report/Export Mismatch** | Imposes reconciliation validation checklists between UI totals and database logs. |
| **Hidden Business Logic in UI** | Mandates core logic placement in isolated backend functions, not frontend views. |

---

## Pack Components

### Documentation Guidelines (`docs/`)

- [Financial Domain Model Guidelines](docs/financial-domain-model-guidelines.md) — Core principles of financial modeling, lifecycles, and adjustment patterns.
- [Money and Currency Guidelines](docs/money-currency-guidelines.md) — Numeric storage standards, ISO currency formats, and exchange rate rules.
- [Calculation and Rounding Guidelines](docs/calculation-and-rounding-guidelines.md) — Calculation formulas ownership, rounding modes, and overrides tracking.
- [Payment and Settlement Guidelines](docs/payment-settlement-guidelines.md) — Payment versus settlement state-tracking, refunds, and allocation policies.
- [Audit Trail and Approval Guidelines](docs/audit-trail-and-approval-guidelines.md) — Immutable logging patterns, edit history tracking, and approval workflows.
- [Units and Quantity Guidelines](docs/units-and-quantity-guidelines.md) — Base unit constraints, measurement definitions, and expected vs. actual quantity.
- [Financial Reporting QA Checklist](docs/financial-reporting-qa-checklist.md) — Standard test matrices, edge-cases coverage, and reporting validation checklist.

### AI Agent Role Prompts (`prompts/`)

- [Financial Domain Architect](prompts/financial-domain-architect-prompt.md) — Role for auditing and structuring domain models before implementation.
- [Calculation Review Agent](prompts/calculation-review-agent-prompt.md) — Role for verifying formulas, precision, rounding, and report calculations.
- [Payment and Settlement Review Agent](prompts/payment-settlement-review-agent-prompt.md) — Role for reviewing transactional lifecycles, balance allocations, and reconciliation.
- [Financial QA Agent](prompts/financial-qa-agent-prompt.md) — Role for executing automated and manual checklists prior to release.

---

## Recommended Usage

Follow these steps to integrate this extension pack into your product project:

1. **Initialize Core Kit First**: Copy the core factory documents (`core/docs/`) and prompt templates (`core/prompts/`) into your product project workspace.
2. **Apply Financial Pack**: Copy this pack's folders (`docs/` and `prompts/`) into your project *only* if financial or business logic (money, billing, quantities) is part of the product.
3. **Add to Product Documentation**: Merge the relevant files from `docs/` into your active product documentation.
4. **Use Prompts in Dev & QA Cycles**: Assign specialized prompts to your AI agents to guide architectural design, calculation reviews, and QA validations before code merge.
5. **Enforce Governance Gatekeeping**: Never approve financial calculations, payment flow states, quantity changes, or settlement rules without explicit human owner review and sign-off.

For workspace setup instructions and core governance rules, link back to [START_HERE.md](../../START_HERE.md).
