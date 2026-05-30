# 12 — QA Test Plan

> Quality assurance test plan for the Integrated Operations ERP documentation reference.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This is not a runnable application. QA here validates documentation completeness and consistency.
> See the [example README](../README.md) for full context.

---

## QA Scope

This QA plan validates the documentation reference — not a running application. QA checks cover:
- Documentation completeness and correctness
- Internal link hygiene
- Role/access consistency
- Stock source-of-truth rules
- Finance placeholder boundaries
- Approval workflow completeness
- Privacy / data guardrails
- Extension pack coverage

---

## QA Area 1 — Role / Access QA

| Check | Expected Result |
|-------|----------------|
| All 9 roles are defined in 04-user-roles.md | Pass |
| Each role has allowed actions, restricted actions, audit events | Pass |
| Platform Admin is distinguished from operational roles | Pass |
| Approval authority is separated from data-entry authority | Pass |
| Self-approval block is documented in roles and flows | Pass |
| Finance access is restricted in role matrix | Pass |
| Read-only Auditor has no write actions | Pass |
| Role matrix matches page-level access in 06-pages-spec.md | Pass |

---

## QA Area 2 — Supplier / Customer Placeholder QA

| Check | Expected Result |
|-------|----------------|
| SupplierPlaceholder entity defined in data model | Pass |
| CustomerPlaceholder entity defined in data model | Pass |
| No real supplier names, addresses, or tax IDs | Pass |
| No real customer names, contact data, or PII | Pass |
| Supplier create/archive flows documented | Pass |
| Customer create/archive flows documented | Pass |
| Supplier List and Customer List pages spec'd | Pass |
| Role access on Supplier/Customer pages matches role matrix | Pass |

---

## QA Area 3 — Product / SKU QA

| Check | Expected Result |
|-------|----------------|
| Product entity defined in data model | Pass |
| SKU entity defined with unique code constraint | Pass |
| Product/SKU catalog page spec'd | Pass |
| Create product and add SKU flows documented | Pass |
| SKU uniqueness validation documented | Pass |
| Unit of measure field documented | Pass |

---

## QA Area 4 — Warehouse / Zone QA

| Check | Expected Result |
|-------|----------------|
| Warehouse entity defined | Pass |
| WarehouseZone entity defined with zone_type field | Pass |
| Zone types (receiving, storage, dispatch) documented | Pass |
| Warehouse and Zone pages spec'd | Pass |
| Warehouse scope enforcement documented in security model | Pass |
| No real warehouse addresses or geographic data | Pass |

---

## QA Area 5 — Stock Movement QA

| Check | Expected Result |
|-------|----------------|
| StockMovement entity is documented as immutable | Pass |
| All movement types documented (receiving_in, transfer, adjustment, dispatch_out) | Pass |
| No direct balance mutation endpoint exists | Pass |
| Source zone balance check documented (insufficient stock → error) | Pass |
| Stock Movements page spec'd with immutable detail view | Pass |
| Movement creation flow documented with audit event | Pass |

---

## QA Area 6 — Receiving QA

| Check | Expected Result |
|-------|----------------|
| ReceivingRecord entity defined | Pass |
| Receiving links to PurchaseOrderPlaceholder | Pass |
| Receiving zone must be zone_type = receiving (documented) | Pass |
| Receiving creates StockMovement records | Pass |
| Receiving page spec'd | Pass |
| Receiving flow documented with over-receipt edge case | Pass |
| Warehouse scope enforced for receiving | Pass |

---

## QA Area 7 — Adjustment QA

| Check | Expected Result |
|-------|----------------|
| StockAdjustment entity defined | Pass |
| Adjustment requires reason field | Pass |
| Adjustment requires approval before StockMovement is created | Pass |
| Self-approval block documented | Pass |
| Approved adjustment creates StockMovement of correct type | Pass |
| Rejected adjustment does not affect stock balance | Pass |
| Stock Adjustments page spec'd with approve/reject actions | Pass |

---

## QA Area 8 — Purchase Workflow QA

| Check | Expected Result |
|-------|----------------|
| PurchaseRequest entity defined with approval lifecycle | Pass |
| PurchaseOrderPlaceholder entity defined | Pass |
| Purchase request → approval → PO flow documented | Pass |
| Self-approval blocked in approval flow | Pass |
| Escalation to Operations Director documented | Pass |
| Purchase Requests and Purchase Orders pages spec'd | Pass |
| PO amounts documented as display placeholders only | Pass |

---

## QA Area 9 — Sales / Dispatch QA

| Check | Expected Result |
|-------|----------------|
| SalesOrderPlaceholder entity defined | Pass |
| DispatchPlaceholder entity defined | Pass |
| Sales order creation flow documented | Pass |
| Dispatch creates StockMovement (dispatch_out) | Pass |
| Sales Orders and Dispatch pages spec'd | Pass |
| No real logistics carrier or shipping API documented | Pass |

---

## QA Area 10 — Invoice / Payment Placeholder QA

| Check | Expected Result |
|-------|----------------|
| InvoicePlaceholder entity defined | Pass |
| PaymentPlaceholder entity defined | Pass |
| Invoice creation flow documented | Pass |
| Payment recording flow documented | Pass |
| Invoice and Payment pages spec'd | Pass |
| Finance amounts clearly labeled as placeholders | Pass |
| No real bank, IBAN, SWIFT, or payment provider data | Pass |
| No tax calculation or accounting claim | Pass |

---

## QA Area 11 — Approval Workflow QA

| Check | Expected Result |
|-------|----------------|
| ApprovalRequest entity defined | Pass |
| Approval inbox page spec'd | Pass |
| All approval flows documented (purchase request, stock adjustment) | Pass |
| Self-approval block enforced at API level (documented) | Pass |
| Escalation path to Operations Director documented | Pass |
| All approval decisions logged as AuditEvents | Pass |
| Reject reason required field documented | Pass |

---

## QA Area 12 — Dashboard / Report QA

| Check | Expected Result |
|-------|----------------|
| Dashboard page spec'd with role-scoped widgets | Pass |
| Reports page spec'd with filter and export | Pass |
| ReportDefinition entity defined | Pass |
| Report data source is authoritative data layer (not UI) | Pass |
| Finance widgets hidden from non-Finance roles | Pass |
| Export generates CSV and PDF placeholder | Pass |
| Report export logged as AuditEvent | Pass |

---

## QA Area 13 — Print / Export QA

| Check | Expected Result |
|-------|----------------|
| Print/export notes document (20-print-reporting-notes.md) present | Pass |
| PO PDF placeholder documented | Pass |
| Invoice PDF placeholder documented | Pass |
| Stock movement report export documented | Pass |
| No real PDF library configured | Pass |
| No legal invoice or accounting claim in print notes | Pass |

---

## QA Area 14 — Audit Log QA

| Check | Expected Result |
|-------|----------------|
| AuditEvent entity defined as immutable | Pass |
| Audit log page spec'd (read-only) | Pass |
| No delete or edit endpoint for AuditEvents | Pass |
| Audit log accessible only to Read-only Auditor and Ops Director | Pass |
| Before/after snapshots documented (sensitive data redaction noted) | Pass |

---

## QA Area 15 — RTL / i18n QA

| Check | Expected Result |
|-------|----------------|
| RTL/i18n notes document (22-rtl-i18n-notes.md) present | Pass |
| Text direction readiness documented | Pass |
| Translation key structure documented | Pass |
| Date, number, currency display formatting documented | Pass |
| Layout mirroring concept documented | Pass |
| Full translation implementation correctly out-of-scope | Pass |

---

## QA Area 16 — Regression QA

| Check | Expected Result |
|-------|----------------|
| Stock source-of-truth rule is consistent across data model, flows, and pages | Pass |
| Approval authority separation is consistent across roles, flows, and API | Pass |
| Finance boundaries (no real accounting) consistent across all docs | Pass |
| Mobile scope (future only) consistent across all docs | Pass |
| All relative links in docs resolve to existing files | Pass |

---

## QA Area 17 — Release Readiness QA

| Check | Expected Result |
|-------|----------------|
| All 23 docs files present and non-empty | Pass |
| All 9 prompts files present and non-empty | Pass |
| README links to all docs and prompts | Pass |
| No file:/// links in any doc | Pass |
| No local machine paths (d:/aitemp, C:/Users, /mnt/data) | Pass |
| No real data (real customer, supplier, payment, invoice, bank, tax ID) | Pass |
| No credentials (api_key, secret, password, project_id) | Pass |
| No source code, no package.json, no migrations | Pass |
| README states: Completed & Fully Filled Documentation Reference | Pass |
| Example is not described as a runnable application | Pass |

---

## Related Files

- [13-release-checklist.md](13-release-checklist.md) — Pre-release sign-off checklist
- [15-ai-agent-operating-rules.md](15-ai-agent-operating-rules.md) — Agent constraints during QA
- [04-user-roles.md](04-user-roles.md) — Role matrix for access QA
- [07-data-model.md](07-data-model.md) — Entity definitions for data QA
