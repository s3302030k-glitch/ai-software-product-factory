# 13 — Release Checklist

> Pre-release verification checklist for the Integrated Operations ERP documentation reference.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This checklist verifies documentation completeness — not a software deployment.
> See the [example README](../README.md) for full context.

---

## Release: v2.0.0 — complex-erp-app Documentation Reference

**Product Concept:** Integrated Operations ERP
**Release Type:** Completed & Fully Filled Documentation Reference
**Release Date Placeholder:** 2026-05-30
**Owner Approval Status:** Pending final owner sign-off

---

## Section 1 — Documentation Completeness

| File | Status | Notes |
|------|--------|-------|
| examples/complex-erp-app/README.md | ✅ Complete | States non-runnable, lists all docs/prompts |
| docs/01-product-brief.md | ✅ Complete | Product name, summary, goals, risks |
| docs/02-target-users.md | ✅ Complete | All 9 personas documented |
| docs/03-mvp-scope.md | ✅ Complete | MoSCoW with explicit exclusions |
| docs/04-user-roles.md | ✅ Complete | Full 9-role matrix |
| docs/05-user-flows.md | ✅ Complete | 16 operational flows |
| docs/06-pages-spec.md | ✅ Complete | 22 pages with states, validations, security |
| docs/07-data-model.md | ✅ Complete | 23 entities with relationships |
| docs/08-architecture.md | ✅ Complete | Conceptual layers, no code |
| docs/09-api-design.md | ✅ Complete | 12 API groups, conceptual only |
| docs/10-security-model.md | ✅ Complete | Auth, authorization, RLS concept |
| docs/11-development-roadmap.md | ✅ Complete | 11 stages, all marked complete |
| docs/12-qa-test-plan.md | ✅ Complete | 17 QA areas |
| docs/13-release-checklist.md | ✅ Complete | This file |
| docs/14-decision-log.md | ✅ Complete | 9 key decisions |
| docs/15-ai-agent-operating-rules.md | ✅ Complete | ERP-adapted agent rules |
| docs/16-bug-report-template.md | ✅ Complete | ERP-specific bug template |
| docs/17-batch-request-template.md | ✅ Complete | ERP batch request template |
| docs/18-erp-operations-notes.md | ✅ Complete | ERP Operations Pack notes |
| docs/19-financial-business-logic-notes.md | ✅ Complete | Financial placeholder notes |
| docs/20-print-reporting-notes.md | ✅ Complete | Print/export placeholder notes |
| docs/21-supabase-notes.md | ✅ Complete | Supabase conceptual notes |
| docs/22-rtl-i18n-notes.md | ✅ Complete | RTL/i18n readiness notes |
| docs/23-mobile-app-notes.md | ✅ Complete | Mobile future scope notes |
| prompts/session-starter-prompt.md | ✅ Complete | |
| prompts/product-architect-prompt.md | ✅ Complete | |
| prompts/erp-domain-review-agent-prompt.md | ✅ Complete | |
| prompts/inventory-workflow-review-agent-prompt.md | ✅ Complete | |
| prompts/financial-logic-review-agent-prompt.md | ✅ Complete | |
| prompts/print-reporting-review-agent-prompt.md | ✅ Complete | |
| prompts/security-review-agent-prompt.md | ✅ Complete | |
| prompts/mobile-operations-review-agent-prompt.md | ✅ Complete | |
| prompts/qa-agent-prompt.md | ✅ Complete | |

---

## Section 2 — Link Hygiene

| Check | Status | Notes |
|-------|--------|-------|
| README links to all 23 docs | ✅ Pass | Relative links only |
| README links to all 9 prompts | ✅ Pass | Relative links only |
| Docs link to related docs using relative paths | ✅ Pass | ../docs/[file].md format |
| Docs link to extension pack READMEs correctly | ✅ Pass | ../../../extensions/[pack]/README.md |
| Prompts link to docs correctly | ✅ Pass | ../docs/[file].md format |
| No file:/// links in any file | ✅ Pass | Text search confirmed |
| No local machine paths (d:/aitemp, C:/Users) | ✅ Pass | Text search confirmed |
| No /mnt/data or other system paths | ✅ Pass | Text search confirmed |

---

## Section 3 — No Real Data Verification

| Check | Status | Notes |
|-------|--------|-------|
| No real customer names or contact data | ✅ Pass | All placeholder names |
| No real supplier names or contact data | ✅ Pass | All placeholder names |
| No real payment data or amounts | ✅ Pass | Placeholder display values only |
| No real invoice data | ✅ Pass | Placeholder documents only |
| No real shipment or logistics records | ✅ Pass | Dispatch placeholders only |
| No real inventory datasets | ✅ Pass | Entity definitions only |
| No real bank account, IBAN, SWIFT data | ✅ Pass | Text search confirmed |
| No tax IDs, VAT numbers, or tax rates | ✅ Pass | Text search confirmed |
| No email addresses (gmail, company emails) | ✅ Pass | Only example.internal format |
| No API keys, secrets, or passwords | ✅ Pass | Text search confirmed |
| No real Supabase project IDs | ✅ Pass | Text search confirmed |

---

## Section 4 — Role Matrix Reviewed

| Check | Status |
|-------|--------|
| All 9 roles defined | ✅ Pass |
| Approval authority separated from data-entry authority | ✅ Pass |
| Self-approval block documented for all approval flows | ✅ Pass |
| Finance access restricted to Finance Officer + Ops Director | ✅ Pass |
| Read-only Auditor has no write access | ✅ Pass |
| Platform Admin is system role, not operational approver | ✅ Pass |

---

## Section 5 — Operational Authorization Reviewed

| Check | Status |
|-------|--------|
| Warehouse scope enforcement documented | ✅ Pass |
| Department scope enforcement documented | ✅ Pass |
| Client-side checks noted as UI convenience only | ✅ Pass |
| Server-side enforcement documented in security model | ✅ Pass |
| RLS concept documented in Supabase notes | ✅ Pass |

---

## Section 6 — Stock Quantity Source-of-Truth Reviewed

| Check | Status |
|-------|--------|
| StockBalance is derived from StockMovements | ✅ Pass |
| No direct balance mutation endpoint exists | ✅ Pass |
| StockMovement records are immutable | ✅ Pass |
| Corrections flow through StockAdjustment → approval → StockMovement | ✅ Pass |
| Source-of-truth rule documented in data model | ✅ Pass |
| Source-of-truth rule documented in ERP operations notes | ✅ Pass |

---

## Section 7 — Finance / Payment Boundary Reviewed

| Check | Status |
|-------|--------|
| Invoice amounts are display placeholder values only | ✅ Pass |
| Payment records are placeholder only — no real payment processing | ✅ Pass |
| No bank, IBAN, SWIFT, payment provider integration described | ✅ Pass |
| No tax calculation or accounting claim | ✅ Pass |
| Financial logic notes clearly state no tax/legal/accounting advice | ✅ Pass |

---

## Section 8 — Invoice Placeholder Reviewed

| Check | Status |
|-------|--------|
| InvoicePlaceholder entity documented | ✅ Pass |
| Invoice amounts labeled as placeholder display values | ✅ Pass |
| Invoice PDF layout documented as placeholder | ✅ Pass |
| No real legal invoice claim | ✅ Pass |

---

## Section 9 — Reports / Exports Reviewed

| Check | Status |
|-------|--------|
| Reports page spec'd | ✅ Pass |
| Report data sourced from authoritative data layer | ✅ Pass |
| CSV and PDF export documented as placeholders | ✅ Pass |
| Report export events logged as AuditEvents | ✅ Pass |

---

## Section 10 — Print Documents Reviewed

| Check | Status |
|-------|--------|
| PO PDF placeholder documented | ✅ Pass |
| Invoice PDF placeholder documented | ✅ Pass |
| No real PDF library configured | ✅ Pass |
| Print/reporting notes (20-print-reporting-notes.md) present | ✅ Pass |
| No legal or accounting claim in print documents | ✅ Pass |

---

## Section 11 — QA Checklist Reviewed

| Check | Status |
|-------|--------|
| QA test plan (12-qa-test-plan.md) present | ✅ Pass |
| All 17 QA areas present | ✅ Pass |
| All QA checks pass (documentation review) | ✅ Pass |

---

## Section 12 — Owner Approval Gates

| Gate | Status | Owner Sign-off |
|------|--------|----------------|
| Product brief approved | ✅ Pass | [Owner placeholder] |
| MVP scope approved | ✅ Pass | [Owner placeholder] |
| Role matrix approved | ✅ Pass | [Owner placeholder] |
| Stock source-of-truth rules approved | ✅ Pass | [Owner placeholder] |
| Finance placeholder boundary approved | ✅ Pass | [Owner placeholder] |
| No real data confirmation | ✅ Pass | [Owner placeholder] |
| Documentation reference released as v2.0.0 | ⬜ Pending | [Owner placeholder] |

---

## Section 13 — Release Notes Placeholder

**v2.0.0 — Integrated Operations ERP Documentation Reference**

This release adds the `complex-erp-app` completed documentation reference example to the AI Software Product Factory. It demonstrates how to fill factory templates for a complex ERP-style product using:
- Core factory docs (01–17)
- ERP Operations Pack
- Financial Business Logic Pack
- Print & Reporting Pack
- Supabase Pack
- RTL/i18n Pack
- Mobile App Pack (future scope documentation)

This is not a runnable application. All entities, names, and values are fictional placeholders.

---

## Related Files

- [12-qa-test-plan.md](12-qa-test-plan.md) — Full QA test plan
- [14-decision-log.md](14-decision-log.md) — Key decisions affecting this release
- [15-ai-agent-operating-rules.md](15-ai-agent-operating-rules.md) — Agent constraints
