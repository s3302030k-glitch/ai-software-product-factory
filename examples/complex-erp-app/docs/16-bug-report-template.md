# 16 — Bug Report Template

> Bug report template for the Integrated Operations ERP documentation reference.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> Use this template to report documentation errors, inconsistencies, or missing content.
> See the [example README](../README.md) for full context.

---

## How to Use This Template

Copy this template when reporting a documentation issue (incorrect content, broken link, missing section, inconsistency between docs). Fill in all applicable fields.

Do **not** use this template to request new features or scope changes — use [17-batch-request-template.md](17-batch-request-template.md) for those.

---

## Bug Report Template

```
## Bug Report — Integrated Operations ERP Documentation Reference

### Summary
[One sentence describing the documentation issue]

### Area
[ ] Product Brief / Target Users / MVP Scope
[ ] User Roles / Permissions
[ ] User Flows
[ ] Pages Specification
[ ] Data Model
[ ] Architecture
[ ] API Design
[ ] Security Model
[ ] Development Roadmap
[ ] QA Test Plan
[ ] Release Checklist
[ ] Decision Log
[ ] AI Agent Operating Rules
[ ] Bug / Batch Templates
[ ] ERP Operations Notes
[ ] Financial Logic Notes
[ ] Print / Reporting Notes
[ ] Supabase Notes
[ ] RTL / i18n Notes
[ ] Mobile App Notes
[ ] Prompts
[ ] README / Navigation

### Role Context
[Which role(s) are affected by this documentation issue?]
e.g., Warehouse Manager, Finance Officer, Approval workflow

### Department / Warehouse Context
[Which department or warehouse scope is relevant, if applicable]
e.g., "Procurement Department context", "All warehouses", "Finance section only"

### Steps to Reproduce (Documentation Error)
1. [Open file X]
2. [Find section Y]
3. [Observe incorrect/missing content Z]

### Expected Documentation Content
[What should the documentation say?]

### Actual Documentation Content
[What does it currently say?]

### Severity
[ ] Critical — Blocks QA or release (incorrect source-of-truth rule, broken approval workflow, real data found)
[ ] Major — Significant inconsistency across documents
[ ] Minor — Small error, typo, or missing detail
[ ] Cosmetic — Formatting or link issue

### Stock / Finance / Security Impact
[ ] Stock source-of-truth rule affected
[ ] Finance placeholder boundary affected
[ ] Approval workflow affected
[ ] Security / authorization rule affected
[ ] Operational scoping affected
[ ] No operational impact (documentation clarity only)

### Screenshots / Log Placeholders
[Paste relevant excerpt from the doc, or note line numbers]
File: docs/[filename].md, Line ~[line number]
Excerpt: "[quote the problematic text]"

### Suspected Documents
[List all docs that may need to be updated to resolve this bug]
- docs/[file].md
- docs/[file].md

### Acceptance Criteria for Fix
[What does a correct fix look like?]
- [ ] [Specific doc updated with correct content]
- [ ] [Related docs updated for consistency]
- [ ] [Links verified after fix]
- [ ] [No real data introduced in fix]
- [ ] [No source code introduced in fix]
- [ ] [Owner review: Yes / No]
```

---

## Example Bug Report (Filled)

```
## Bug Report — Integrated Operations ERP Documentation Reference

### Summary
Stock adjustment approval flow in 05-user-flows.md does not mention that approved adjustments
create a StockMovement record, which is inconsistent with the data model.

### Area
[x] User Flows
[x] Data Model

### Role Context
Warehouse Manager, Inventory Clerk

### Department / Warehouse Context
All warehouses

### Steps to Reproduce
1. Open docs/05-user-flows.md
2. Find Flow 9 — Record Stock Adjustment
3. Step 9 does not mention StockMovement creation after approval

### Expected Documentation Content
Step 9 should state: "If approved: StockMovement record of type 'adjustment' is created."

### Actual Documentation Content
Step 9 only mentions adjustment status changes to "Approved" without referencing StockMovement creation.

### Severity
[x] Major — Significant inconsistency across documents

### Stock / Finance / Security Impact
[x] Stock source-of-truth rule affected

### Suspected Documents
- docs/05-user-flows.md (Flow 9, Step 9)
- docs/07-data-model.md (StockAdjustment section — verify cross-reference)

### Acceptance Criteria for Fix
- [x] Flow 9 Step 9 updated to reference StockMovement creation
- [x] Data model cross-reference verified
- [x] No real data introduced
- [x] Owner review: No (minor correction within approved scope)
```

---

## Related Files

- [17-batch-request-template.md](17-batch-request-template.md) — For requesting new content or scope changes
- [12-qa-test-plan.md](12-qa-test-plan.md) — QA checklist that identifies documentation gaps
- [15-ai-agent-operating-rules.md](15-ai-agent-operating-rules.md) — Rules governing what fixes are permitted
