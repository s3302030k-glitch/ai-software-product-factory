# 14 — Decision Log

> Key design decisions for the Integrated Operations ERP documentation reference.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This is not a runnable application. See the [example README](../README.md) for full context.

---

## Decision Log Format

Each entry records: a date placeholder, the decision made, the rationale, alternatives considered, and owner approval status.

---

## Decision 1 — Documentation-Only Reference (No Runnable Application)

**Date:** 2026-05-30
**Decision:** This example is a completed documentation reference only. No application source code, no package.json, no database migrations, and no runnable application will be created.
**Rationale:** The purpose of this example is to demonstrate how to fill the AI Software Product Factory templates for a complex ERP product. Creating runnable code would shift the scope from documentation reference to application development, which is outside the factory's intent.
**Alternatives Considered:**
- Create a partially runnable stub application — rejected; adds complexity, dependency management, and maintenance burden without benefiting the documentation reference goal.
- Create API stubs with mock data — rejected; risks being mistaken for real implementation.
**Owner Approval Status:** ✅ Approved

---

## Decision 2 — No Real Accounting Ledger

**Date:** 2026-05-30
**Decision:** The Integrated Operations ERP will not include a real accounting ledger, general ledger (GL), chart of accounts, double-entry bookkeeping, or trial balance. Invoice and payment records are placeholder display values only.
**Rationale:** Real accounting requires legal, tax, and audit compliance that is outside the scope of this documentation reference. Implementing a real ledger would require professional accounting, tax, and compliance review before any production use — which is beyond the factory's purpose.
**Alternatives Considered:**
- Include a conceptual GL with placeholder journal entries — rejected; risks misleading readers into thinking the ERP handles real accounting.
**Owner Approval Status:** ✅ Approved

---

## Decision 3 — No Real Bank or Payment Integration

**Date:** 2026-05-30
**Decision:** No bank API, SWIFT/IBAN processing, direct debit, or real payment provider integration (Stripe, PayPal, Adyen, etc.) will be documented or implemented.
**Rationale:** Real payment integration requires PCI-DSS compliance, legal agreements with payment providers, and security review that is entirely outside this documentation reference's scope. Including real payment details would introduce genuine compliance and privacy risks.
**Alternatives Considered:**
- Document a conceptual payment API — rejected; the Financial Business Logic Pack already covers payment placeholder concepts adequately.
**Owner Approval Status:** ✅ Approved

---

## Decision 4 — Stock Balance is Derived from Stock Movements (Source-of-Truth Rule)

**Date:** 2026-05-30
**Decision:** `StockBalance.current_balance` is always derived by summing all `StockMovement` records for a given SKU/zone combination. It is never directly edited. Any correction requires a `StockAdjustment` → approval → new `StockMovement`.
**Rationale:** Direct balance edits without movement records break auditability and create inventory mismatches. A movement-based source of truth ensures every balance change is traceable to an actor, a timestamp, and a reason.
**Alternatives Considered:**
- Allow direct balance edits with audit logging — rejected; movement records provide superior traceability and are standard ERP practice.
- Use periodic stock counts as the source of truth — rejected; too complex for MVP scope.
**Owner Approval Status:** ✅ Approved

---

## Decision 5 — Approval Authority is Separated from Data-Entry Authority

**Date:** 2026-05-30
**Decision:** The role of Approver/Department Manager is explicitly separated from data-entry roles (Inventory Clerk, Purchasing Manager, Sales Coordinator). A user who submits a request cannot approve it, regardless of their role combination.
**Rationale:** Combining data-entry and approval authority creates internal control risk (fraud, errors without review). Separation ensures a four-eyes principle for all approval-gated operations.
**Alternatives Considered:**
- Allow managers to approve their own low-value requests — rejected; any threshold introduces ambiguity and control bypass risk.
**Owner Approval Status:** ✅ Approved

---

## Decision 6 — Finance Documents Are Placeholders Only

**Date:** 2026-05-30
**Decision:** All InvoicePlaceholder and PaymentPlaceholder records contain display values only. No real accounting, tax calculation, or legal financial instrument is implied.
**Rationale:** Making finance documents real would require tax law compliance, accounting standards compliance, and professional financial review. The documentation reference demonstrates document structure only — not real financial operations.
**Alternatives Considered:**
- Include sample tax calculations with disclaimers — rejected; any tax calculation, even with disclaimers, risks misleading readers.
**Owner Approval Status:** ✅ Approved

---

## Decision 7 — No Production Warehouse Automation in MVP

**Date:** 2026-05-30
**Decision:** Barcode scanner hardware, RFID integration, conveyor systems, pick-and-pack automation, and carrier API integrations are explicitly out of scope for this documentation reference.
**Rationale:** Production-grade WMS automation requires hardware specifications, vendor partnerships, and operational safety reviews. The documentation reference demonstrates the software documentation layer, not hardware integration.
**Alternatives Considered:**
- Document a conceptual barcode scanning flow — rejected; would imply hardware integration that is genuinely out of scope.
**Owner Approval Status:** ✅ Approved

---

## Decision 8 — Mobile Warehouse Flows Are Future / Optional Scope

**Date:** 2026-05-30
**Decision:** Mobile app warehouse flows (receiving on mobile, stock count on mobile) are documented in [23-mobile-app-notes.md](23-mobile-app-notes.md) as optional future scope only. No mobile app framework, no React Native, no Expo setup is included.
**Rationale:** Mobile implementation requires a separate technology stack, device permission management, offline sync design, and app store readiness — all substantial additional scope beyond demonstrating ERP documentation patterns.
**Alternatives Considered:**
- Include a basic mobile receiving flow as a PWA concept — rejected; even a PWA concept risks being interpreted as implementation scope.
**Owner Approval Status:** ✅ Approved

---

## Decision 9 — RTL / i18n Readiness Is Documented, Full Translation Is Out of Scope

**Date:** 2026-05-30
**Decision:** The documentation reference includes RTL/i18n readiness concepts (text direction, translation key structure, locale formatting) as documented in [22-rtl-i18n-notes.md](22-rtl-i18n-notes.md). Full translation files, language switching implementation, and locale-specific testing are out of MVP scope.
**Rationale:** RTL/i18n readiness documentation demonstrates the architectural approach without requiring actual translation content, which would need professional translators, locale-specific review, and ongoing maintenance.
**Alternatives Considered:**
- Include a sample translation key file — rejected; translation files are not documentation templates and would be mistaken for actual translations.
**Owner Approval Status:** ✅ Approved

---

## Related Files

- [01-product-brief.md](01-product-brief.md) — Product decisions that flow from these entries
- [03-mvp-scope.md](03-mvp-scope.md) — Scope decisions
- [15-ai-agent-operating-rules.md](15-ai-agent-operating-rules.md) — Agent constraints derived from these decisions
