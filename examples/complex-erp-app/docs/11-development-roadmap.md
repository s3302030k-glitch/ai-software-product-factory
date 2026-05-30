# 11 — Development Roadmap

> Staged documentation roadmap for the Integrated Operations ERP.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This is not a runnable application. This roadmap documents the documentation stages, not code delivery.
> See the [example README](../README.md) for full context.

> [!IMPORTANT]
> This roadmap describes **documentation and design stages** — not software implementation sprints. Each stage produces completed documentation artifacts, not runnable code.

---

## Roadmap Structure

The documentation is organized into 11 stages (Stage 0 through Stage 10), ordered by dependency. Each stage builds on the outputs of the previous stage.

---

## Stage 0 — Governance and Docs Setup

**Objective:** Establish the documentation governance framework and file structure.

**Deliverables:**
- [x] Example README created with full table of contents
- [x] AI agent operating rules adapted for ERP context
- [x] Product brief drafted and approved
- [x] Decision log initialized

**Dependencies:** None — this is the foundation stage.

**Acceptance Criteria:**
- README links to all planned docs and prompts.
- AI agent operating rules clearly state: no source code, no real data, no accounting/payment integration.
- Product brief is approved and locked.

**Not-Code Reminder:** Stage 0 produces documentation files only. No source code, no package files, no migrations.

---

## Stage 1 — Master Data Placeholders

**Objective:** Define and document all master data entities and their management flows.

**Deliverables:**
- [x] Target users document (02-target-users.md)
- [x] MVP scope document (03-mvp-scope.md)
- [x] User roles document (04-user-roles.md)
- [x] Supplier/Customer placeholder entity definitions in data model
- [x] Product/SKU entity definitions in data model
- [x] Warehouse/Zone entity definitions in data model
- [x] Master data pages in pages spec (Supplier List, Customer List, Product Catalog, Warehouse List, Zones)
- [x] Master data creation flows in user flows

**Dependencies:** Stage 0 complete.

**Acceptance Criteria:**
- All 9 roles are defined with allowed/restricted actions.
- Supplier, Customer, Product, SKU, Warehouse, Zone entities are documented.
- No real supplier, customer, or product data is included.
- Master data pages include empty/loading/error states.

---

## Stage 2 — Warehouse / Stock Foundation

**Objective:** Document the warehouse structure and inventory stock model as the operational foundation.

**Deliverables:**
- [x] StockBalance entity (derived — not directly editable)
- [x] StockMovement entity (immutable)
- [x] Stock overview page spec
- [x] Stock movements page spec
- [x] Stock balance source-of-truth rule documented
- [x] Architecture document (conceptual layers)
- [x] ERP operations notes (18-erp-operations-notes.md)

**Dependencies:** Stage 1 complete.

**Acceptance Criteria:**
- StockBalance is clearly documented as derived from StockMovements.
- StockMovement is documented as immutable.
- No direct balance mutation endpoint exists.
- Architecture doc distinguishes presentation, application, and data layers.

---

## Stage 3 — Purchase Workflow Placeholders

**Objective:** Document the procurement workflow from purchase request through purchase order placeholder.

**Deliverables:**
- [x] PurchaseRequest entity definition
- [x] PurchaseOrderPlaceholder entity definition
- [x] ApprovalRequest entity definition
- [x] Purchase request creation and approval flows
- [x] Purchase requests page spec
- [x] Purchase orders page spec
- [x] Purchasing Manager role actions documented

**Dependencies:** Stage 1, Stage 2.

**Acceptance Criteria:**
- Approval workflow is documented end-to-end with self-approval block.
- Purchase order amounts are documented as placeholder display values only.
- No real procurement pricing, tax, or supplier payment is included.

---

## Stage 4 — Receiving and Stock Movements

**Objective:** Document the receiving workflow and stock movement recording process.

**Deliverables:**
- [x] ReceivingRecord entity definition
- [x] StockAdjustment entity definition
- [x] Receiving flow (goods receipt from supplier into warehouse)
- [x] Stock movement / transfer flow
- [x] Stock adjustment flow (submit → approve → create movement)
- [x] Receiving page spec
- [x] Stock adjustments page spec

**Dependencies:** Stage 3.

**Acceptance Criteria:**
- Receiving correctly links to PO placeholder and creates StockMovement records.
- Adjustment flow enforces approval before StockMovement creation.
- Self-approval is blocked in the adjustment approval flow.
- All stock changes are traceable to a StockMovement record.

---

## Stage 5 — Sales / Dispatch Placeholders

**Objective:** Document the sales order and dispatch workflow placeholder.

**Deliverables:**
- [x] SalesOrderPlaceholder entity definition
- [x] DispatchPlaceholder entity definition
- [x] Sales order creation flow
- [x] Dispatch preparation flow (creates StockMovement dispatch_out records)
- [x] Sales orders page spec
- [x] Dispatch / shipments page spec

**Dependencies:** Stage 4.

**Acceptance Criteria:**
- Dispatch correctly creates StockMovement records of type `dispatch_out`.
- Sales order amounts are documented as placeholder display values only.
- No real logistics carrier or shipping API integration is described.

---

## Stage 6 — Finance / Invoice / Payment Placeholders

**Objective:** Document the invoice and payment placeholder records and finance overview.

**Deliverables:**
- [x] InvoicePlaceholder entity definition
- [x] PaymentPlaceholder entity definition
- [x] Invoice creation flow
- [x] Payment recording flow
- [x] Invoice list page spec
- [x] Payment placeholder list page spec
- [x] Finance overview page spec
- [x] Financial business logic notes (19-financial-business-logic-notes.md)

**Dependencies:** Stage 5.

**Acceptance Criteria:**
- Invoice and payment records are clearly labeled as placeholders.
- No real bank, payment provider, tax, or accounting integration is described.
- Finance access is restricted to Finance Officer and Operations Director in role matrix.

---

## Stage 7 — Approvals and Audit Trail

**Objective:** Document the approval workflow engine and immutable audit trail.

**Deliverables:**
- [x] ApprovalRequest entity documented with self-approval block
- [x] Approval inbox page spec
- [x] Approval flows (purchase request, stock adjustment)
- [x] AuditEvent entity (immutable)
- [x] Audit log page spec
- [x] Audit log review flow

**Dependencies:** Stages 3, 4, 6.

**Acceptance Criteria:**
- Self-approval block is enforced at API level (not just UI).
- AuditEvent records are documented as immutable.
- Audit log is accessible only to Read-only Auditor and Operations Director.
- All state changes produce AuditEvent records.

---

## Stage 8 — Reports / Print / Export

**Objective:** Document the reporting, print, and export placeholder system.

**Deliverables:**
- [x] ReportDefinition entity
- [x] Reports page spec with filter and export actions
- [x] Export flow (CSV and PDF placeholder)
- [x] Print reporting notes (20-print-reporting-notes.md)
- [x] Dashboard page spec

**Dependencies:** Stage 7.

**Acceptance Criteria:**
- Report data is sourced from authoritative data layer — not from UI state.
- PDF and CSV exports are documented as placeholders — no real library is configured.
- Report export events are logged as AuditEvents.

---

## Stage 9 — Security / QA Hardening

**Objective:** Complete the security model, QA test plan, and release checklist documentation.

**Deliverables:**
- [x] Security model document (10-security-model.md)
- [x] Supabase notes (21-supabase-notes.md) — RLS concept
- [x] RTL/i18n notes (22-rtl-i18n-notes.md)
- [x] Mobile app notes (23-mobile-app-notes.md)
- [x] QA test plan (12-qa-test-plan.md)
- [x] Release checklist (13-release-checklist.md)
- [x] Bug report template (16-bug-report-template.md)
- [x] Batch request template (17-batch-request-template.md)

**Dependencies:** Stages 1–8.

**Acceptance Criteria:**
- Security model clearly separates authentication from authorization.
- RLS concept is documented (conceptual only — no real policies).
- QA plan covers all ERP domains and extension pack areas.
- Release checklist includes no-real-data verification.

---

## Stage 10 — Release Documentation

**Objective:** Finalize all prompts, run consistency checks, and confirm documentation reference is complete.

**Deliverables:**
- [x] All 9 prompts created in `prompts/` folder
- [x] Internal consistency check (README links, relative links, no broken paths)
- [x] Text search validation (no real data, no private paths, no credentials)
- [x] Decision log complete (14-decision-log.md)
- [x] Final report generated

**Dependencies:** Stages 0–9.

**Acceptance Criteria:**
- All docs and prompts are linked from README.
- No `file:///` links, no machine paths, no real data found in text search.
- README correctly states: Completed & Fully Filled Documentation Reference.
- No source code, no package files, no migrations in the example folder.

---

## Roadmap Completion Status

| Stage | Status |
|-------|--------|
| Stage 0 — Governance & Docs Setup | ✅ Complete |
| Stage 1 — Master Data Placeholders | ✅ Complete |
| Stage 2 — Warehouse / Stock Foundation | ✅ Complete |
| Stage 3 — Purchase Workflow Placeholders | ✅ Complete |
| Stage 4 — Receiving and Stock Movements | ✅ Complete |
| Stage 5 — Sales / Dispatch Placeholders | ✅ Complete |
| Stage 6 — Finance / Invoice / Payment Placeholders | ✅ Complete |
| Stage 7 — Approvals and Audit Trail | ✅ Complete |
| Stage 8 — Reports / Print / Export | ✅ Complete |
| Stage 9 — Security / QA Hardening | ✅ Complete |
| Stage 10 — Release Documentation | ✅ Complete |

---

## Related Files

- [13-release-checklist.md](13-release-checklist.md) — Final release verification
- [12-qa-test-plan.md](12-qa-test-plan.md) — QA coverage per stage
- [14-decision-log.md](14-decision-log.md) — Decisions made during each stage
