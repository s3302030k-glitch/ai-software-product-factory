# 01 — Product Brief

> The single source of truth for what the product is, why it exists, and who it serves.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This is not a runnable application. See the [example README](../README.md) for full context.

---

## Status

`Approved` — Documentation reference locked for this example.

---

## Product Name

```
Integrated Operations ERP
```

---

## Product Summary

Integrated Operations ERP is a web-based internal business operations platform for a multi-department trading and warehouse company. It centralizes procurement, inventory, warehouse operations, sales fulfillment, and financial document management into a single unified system with structured approval workflows, an immutable audit trail, and operational reporting. The system is designed to eliminate spreadsheet-based operations, reduce inter-department miscommunication, and provide real-time stock visibility across multiple warehouses.

---

## Problem Statement

Mid-sized trading and warehouse companies operating across multiple departments (procurement, warehouse, sales, finance) typically run their operations across disconnected spreadsheets, email approvals, and standalone tools. This leads to inventory mismatches, delayed purchase approvals, untracked stock movements, inconsistent financial documents, and no unified audit trail for compliance. Staff spend significant time reconciling records across systems, while managers lack real-time visibility into operational status.

---

## Target Users

| User Type | Description | Key Need |
|-----------|-------------|----------|
| Operations Director | Senior manager overseeing all departments | Real-time cross-department dashboard |
| Warehouse Manager | Manages physical warehouse operations | Stock accuracy, receiving, zone tracking |
| Inventory Clerk | Executes stock movements and adjustments | Accurate movement recording tools |
| Purchasing Manager | Manages procurement workflow | Purchase request and order tracking |
| Sales Coordinator | Creates and tracks sales orders | Sales order and dispatch status |
| Finance Officer | Manages invoice and payment placeholders | Finance document accuracy |
| Approver / Dept. Manager | Approves requests across departments | Approval inbox with context |
| Read-only Auditor | Reviews records without editing | Full read access to audit logs |
| Platform Admin | Manages users, roles, and system config | Admin panel and user management |

Full persona details are in [02-target-users.md](02-target-users.md).

---

## Product Goal

Provide a multi-department trading and warehouse company with a unified ERP-style operations platform that connects procurement, inventory, warehouse, sales, and finance workflows — replacing disconnected spreadsheets with traceable, approval-gated, and auditable processes.

---

## Business Goal

Reduce operational errors, eliminate spreadsheet-driven reconciliation, and provide management with real-time operational visibility — enabling the company to scale its operations without proportionally increasing administrative overhead.

---

## Product Type

- [x] Web Application (SPA or MPA — to be decided in architecture phase)
- [ ] Mobile App (documented as optional future scope only)
- [ ] API-only

---

## First Version Summary

Version 1 of Integrated Operations ERP provides:
- Supplier and customer placeholder master data management.
- Product/SKU catalog with warehouse and zone definitions.
- Stock movement recording as the source of truth for inventory balances.
- Goods receiving workflow from supplier placeholder into warehouse stock.
- Purchase request creation and approval workflow placeholder.
- Sales order and dispatch workflow placeholder.
- Invoice and payment placeholder document management.
- Multi-role approval inbox with audit trail.
- Operational dashboard with basic KPIs.
- Basic report and export placeholders.
- RTL/i18n readiness.

Version 1 does **not** include: real accounting ledger, real tax calculation, real bank or payment provider integration, production-grade WMS automation, barcode hardware, real logistics carrier integration, production planning, or mobile app implementation.

---

## MVP Success Criteria

| Criteria | Measurement | Target |
|----------|-------------|--------|
| All core ERP flows documented | Manual review of all docs | 100% complete |
| Role matrix complete and consistent | Role matrix audit | All 9 roles defined |
| Stock quantity source-of-truth rule enforced | Data model + QA review | StockMovement is sole source |
| Approval workflow documented end-to-end | Flow + page spec review | All approval paths documented |
| No real data included | Text search audit | Zero real records |
| All extension pack notes complete | Docs 18–23 review | All 6 notes written |
| All prompts complete | Prompts folder review | All 9 prompts written |
| Link hygiene passed | Relative link audit | No broken or absolute links |

---

## Non-Goals

- Real accounting ledger or double-entry bookkeeping.
- Real tax calculation engine.
- Real bank or payment provider integration (Stripe, PayPal, bank APIs).
- Production-grade warehouse automation (barcode scanners, conveyors, RFID).
- Real logistics carrier or shipping API integration.
- Production planning or manufacturing module.
- Mobile app implementation (documented as optional future scope only).
- Real HR or payroll modules.
- Real customer portal or supplier portal.
- Real e-commerce storefront.

---

## Assumptions

- The company operates from multiple warehouses with designated zones.
- All users authenticate through a managed identity system.
- Financial documents (invoices, payments) are placeholder records only — not real financial instruments.
- Approval authority is clearly separated from data-entry authority.
- Stock quantity is always derived from stock movement records, never directly edited.
- RTL/i18n readiness is documented but full translation is not in MVP scope.
- Mobile warehouse flows are documented as future/optional only.

---

## Risks

| Risk | Likelihood | Mitigation |
|------|------------|------------|
| Stock quantity source-of-truth drift | Medium | Enforce StockMovement as sole source in data model and QA |
| Approval authority bypass | Medium | Document strict approval gates in roles and flows |
| Finance document confusion with real invoices | Low | Clearly label as placeholders throughout |
| RTL/i18n scope creep | Low | Scope to readiness only; full translations are out of MVP |
| Mobile scope creep | Low | Explicitly mark as future/optional in all relevant docs |
| Real data accidentally included | Low | Text search guardrails at every release check |

---

## Related Extension Packs

| Pack | How It Applies |
|------|----------------|
| [ERP Operations Pack](../../../extensions/erp-operations-pack/README.md) | Core ERP domain model, warehouse, inventory, approvals, audit trail |
| [Financial Business Logic Pack](../../../extensions/financial-business-logic-pack/README.md) | Invoice/payment placeholder amounts, money handling, rounding |
| [Print & Reporting Pack](../../../extensions/print-reporting-pack/README.md) | PO PDF, invoice PDF, stock movement report, export placeholders |
| [Supabase Pack](../../../extensions/supabase-pack/README.md) | Auth concept, RLS concept, storage boundaries |
| [RTL & i18n Pack](../../../extensions/rtl-i18n-pack/README.md) | Text direction readiness, locale formatting |
| [Mobile App Pack](../../../extensions/mobile-app-pack/README.md) | Optional future mobile warehouse flows |

---

## Out of Scope

- Technical implementation details (see `08-architecture.md`)
- Feature prioritization detail (see `03-mvp-scope.md`)
- UI/UX specifications (see `06-pages-spec.md`)
- Data model definitions (see `07-data-model.md`)
- Timeline or roadmap (see `11-development-roadmap.md`)

---

## Guardrails

- [ ] Product brief is locked for this documentation reference.
- [ ] No AI agent may alter product scope without owner approval and a decision log entry.
- [ ] No real data, credentials, or private business information may be added.
- [ ] Finance documents remain placeholders only — no real accounting, tax, or payment claims.
- [ ] Mobile app flows remain future/optional scope only.

---

## Related Files

- [02-target-users.md](02-target-users.md) — Persona details
- [03-mvp-scope.md](03-mvp-scope.md) — Feature prioritization
- [04-user-roles.md](04-user-roles.md) — Role matrix
- [14-decision-log.md](14-decision-log.md) — Key decisions
- [15-ai-agent-operating-rules.md](15-ai-agent-operating-rules.md) — Agent constraints

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation as completed documentation reference | Factory Example |
