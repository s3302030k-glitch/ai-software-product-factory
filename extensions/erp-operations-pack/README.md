# Extension Pack: ERP & Operations

> Adds enterprise resource planning patterns, operational workflows, inventory management, and multi-module coordination guidelines and prompts.

This is a fully implemented extension pack, supplementing the core factory templates.

---

## Purpose and Scope

This pack **supplements** the core factory documents; it does **not** replace them. It is designed for software products that include ERP-style operations, inventory, warehouse operations, receiving, shipping, production steps, approvals, internal workflows, operational audit trails, operational reporting, or multi-step business processes.

It is useful for building ERP systems, warehouse systems, inventory apps, procurement workflows, sales operations, logistics platforms, factory/production planning modules, and operational dashboards.

This pack is strictly template-based and generic. It does not include product-specific or private business information. It does not include real warehouse data, real addresses, real inventory lists, real supplier/customer data, real contracts, credentials, project IDs, or company-specific operational records.

> [!WARNING]
> **NO LEGAL, TAX, ACCOUNTING, OR REGULATED OPERATIONAL ADVICE**: This extension pack does not provide legal, tax, accounting, safety, customs, logistics, or regulated operational advice. All templates and prompts must be audited and approved by the product owner's operations, legal, safety, and tax professionals before deployment.

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| **Inventory Mismatch** | Establishes on-hand vs. available vs. reserved vs. allocated stock rules. |
| **Unclear Stock Movement History** | Mandates update via stock movement records rather than silent direct edits. |
| **Receiving/Shipping Status Confusion** | Separates physical layout, logical stock status, and document status. |
| **Workflow State Mistakes** | Defines explicit state machines and transition paths. |
| **Approval Bypass** | Blocks state progression without verified actor credentials and audit trails. |
| **Quantity Mismatch** | Reconciles planned vs. received vs. shipped vs. adjusted values. |
| **Warehouse Location/Zone Confusion** | Enforces traceable location movements and physical-logical separation. |
| **Soft Delete and Cancellation Ambiguity** | Defines rules for reversing records while preserving historical logs. |
| **Operational Report Mismatch** | Requires audit-trail reconciliation verification checking. |
| **Hidden Operational Rules in UI** | Mandates core logic placement in isolated backend functions, not frontend views. |

---

## Pack Components

### Documentation Guidelines (`docs/`)

- [ERP Domain Model Guidelines](docs/erp-domain-model-guidelines.md) — Core principles of ERP modeling, lifecycle states, planning vs. execution separation, and correction patterns.
- [Inventory and Stock Guidelines](docs/inventory-and-stock-guidelines.md) — Stock movement records, reservation/allocation rules, and unit/quantity handling.
- [Warehouse Operations Guidelines](docs/warehouse-operations-guidelines.md) — Facility layout modeling, receiving, picking, shipping, and transfers.
- [Workflow and Approval Guidelines](docs/workflow-and-approval-guidelines.md) — State machine design, four-eyes approval patterns, and UI state visibility.
- [Operational Audit Trail Guidelines](docs/operational-audit-trail-guidelines.md) — Immutable logging patterns, edit history tracking, and admin override rules.
- [Operational Reporting Guidelines](docs/operational-reporting-guidelines.md) — Source-of-truth alignment, live vs. snapshot values, and timezone boundaries.
- [ERP Operations QA Checklist](docs/erp-operations-qa-checklist.md) — Comprehensive pre-release QA matrix covering domain, stock, warehouse, workflows, and audits.

### AI Agent Role Prompts (`prompts/`)

- [ERP Domain Architect](prompts/erp-domain-architect-prompt.md) — AI agent role for designing ERP/operations domain models and state machines.
- [Inventory Workflow Review Agent](prompts/inventory-workflow-review-agent-prompt.md) — AI agent role for auditing stock movement logs, adjustments, and cancellations.
- [Warehouse Operations Review Agent](prompts/warehouse-operations-review-agent-prompt.md) — AI agent role for auditing receiving, shipping, putaway, picking, and location traces.
- [ERP Operations QA Agent](prompts/erp-qa-agent-prompt.md) — AI agent role for validating releases against ERP guidelines and running checklists.

---

## Recommended Usage

Follow these steps to integrate this extension pack into your product project:

1. **Initialize Core Kit First**: Copy the core factory documents (`core/docs/`) and prompt templates (`core/prompts/`) into your product project workspace.
2. **Apply ERP Operations Pack**: Copy this pack's folders (`docs/` and `prompts/`) into your project *only* if ERP, inventory, warehouse, or multi-step operational workflows are part of the product.
3. **Add to Product Documentation**: Merge the relevant guidelines from `docs/` directly into your active product documentation.
4. **Use Prompts in Dev & QA Cycles**: Assign specialized prompts to your AI agents to guide domain design, stock transaction reviews, and QA validations before code merges.
5. **Enforce Governance Gatekeeping**: Never approve inventory, stock, shipment, receiving, production, or workflow logic changes without explicit human owner approval.

For workspace setup instructions and core governance rules, link back to [START_HERE.md](../../START_HERE.md).
