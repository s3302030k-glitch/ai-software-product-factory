# 17 — Batch Request Template

> Template for requesting documentation edits to the Integrated Operations ERP reference.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> Use this template to request additions or changes to the documentation. Not for bug fixes.
> See the [example README](../README.md) for full context.

---

## How to Use This Template

Copy this template for each batch of requested documentation changes. Every batch must be small, focused, and independently reviewable. Do not combine unrelated changes into a single batch.

---

## Batch Request Template

```
## Batch Request — Integrated Operations ERP Documentation Reference

### Batch ID
[e.g., ERP-DOCS-B01]

### Objective
[One sentence describing what this batch achieves]

### Scope
[Which doc area does this batch target?]
e.g., "Add stock count placeholder flow to 05-user-flows.md and a matching entity to 07-data-model.md"

### Files In Scope
- examples/complex-erp-app/docs/[file].md — [what changes]
- examples/complex-erp-app/docs/[file].md — [what changes]
- examples/complex-erp-app/README.md — [if links need updating]

### Files Out of Scope (Must Not Be Modified)
- core/ — all core factory docs and prompts
- extensions/ — all extension packs
- examples/small-crud-app/
- examples/medium-saas-app/
- Root README.md, START_HERE.md, FACTORY_STATUS.md
- Any application source code, package files, or migrations

### Guardrails
- [ ] No source code will be created
- [ ] No real data will be added (no real customers, suppliers, payments, invoices, bank data, tax IDs)
- [ ] No accounting/payment/bank integration will be documented
- [ ] No tax/legal/accounting advice will be added
- [ ] Stock source-of-truth rules will not be changed without a new decision log entry
- [ ] Approval workflow will not be weakened
- [ ] Operational authorization will not be weakened
- [ ] No files outside examples/complex-erp-app/ will be modified

### Required Reading Before Starting
[List all docs that must be read before this batch begins]
- examples/complex-erp-app/README.md
- docs/15-ai-agent-operating-rules.md
- docs/14-decision-log.md
- docs/[relevant-files].md

### Exact Changes Requested

#### File: docs/[filename].md
Section: [section name or line range]
Change: [describe exactly what to add, modify, or remove]
New content: [paste the new content or describe it precisely]

#### File: docs/[filename].md
Section: [section name or line range]
Change: [describe exactly what to add, modify, or remove]
New content: [paste the new content or describe it precisely]

### Validation Checklist (Agent Must Confirm After Completing Batch)
- [ ] All files in scope modified correctly
- [ ] No files out of scope touched
- [ ] No source code created
- [ ] No real data added
- [ ] No accounting/payment/bank integration added
- [ ] No tax/legal/accounting advice added
- [ ] Stock source-of-truth rules unchanged
- [ ] Approval workflow not weakened
- [ ] Operational authorization not weakened
- [ ] README links updated if new files were added
- [ ] No file:/// links or machine paths in any edited file
- [ ] Relative links verified

### Final Report Format (Agent Must Provide)
## Batch Completion Report — [Batch ID]

### Files Modified or Created
| File | Change Type | Summary |

### Validation Results
| Check | Result |

### Consistency Notes
[Any cross-doc consistency issues found and resolved]

### Owner Review Required
[Yes / No — reason]

### Remaining Issues
[None / list any]
```

---

## Example Batch Request (Filled)

```
## Batch Request — Integrated Operations ERP Documentation Reference

### Batch ID
ERP-DOCS-B01

### Objective
Add a stock count placeholder flow to the user flows and a StockCountRecord placeholder
entity to the data model to document the optional cycle count workflow.

### Files In Scope
- examples/complex-erp-app/docs/05-user-flows.md — Add Flow 17: Stock Count Placeholder
- examples/complex-erp-app/docs/07-data-model.md — Add StockCountRecord placeholder entity
- examples/complex-erp-app/docs/03-mvp-scope.md — Move stock count from Could Have to Should Have

### Files Out of Scope
- core/ — all core factory docs
- extensions/ — all extension packs
- examples/small-crud-app/ or examples/medium-saas-app/
- Root README, START_HERE, FACTORY_STATUS

### Guardrails
- [x] No source code
- [x] No real data
- [x] No accounting/payment/bank integration
- [x] Stock source-of-truth rules unchanged (count creates adjustment, not direct edit)

### Required Reading
- docs/15-ai-agent-operating-rules.md
- docs/07-data-model.md (StockBalance and StockMovement sections)
- docs/14-decision-log.md (Decision 4 — stock source-of-truth)

### Exact Changes
#### File: docs/05-user-flows.md
Add a new Flow 17 at the end of the flow index and flow list.
Flow: Stock Count Placeholder — actor: Inventory Clerk, steps: initiate count, record counted
quantity per SKU/zone, submit for review, discrepancy triggers StockAdjustment request.

#### File: docs/07-data-model.md
Add a StockCountRecord entity after StockAdjustment.
Fields: id, warehouse_zone_id, sku_id, counted_quantity, initiated_by_user_id, status
(In Progress / Submitted), created_at.
Note: discrepancies create StockAdjustment records — not direct balance edits.

### Validation Checklist
- [ ] Flow 17 added to 05-user-flows.md
- [ ] StockCountRecord entity added to 07-data-model.md
- [ ] No direct balance edits introduced
- [ ] No real data added
- [ ] No source code created

### Owner Review Required
No — this is a documentation clarification within approved scope.
```

---

## Related Files

- [16-bug-report-template.md](16-bug-report-template.md) — For reporting documentation errors
- [15-ai-agent-operating-rules.md](15-ai-agent-operating-rules.md) — Rules that constrain all batches
- [14-decision-log.md](14-decision-log.md) — Decisions that cannot be changed without owner approval
