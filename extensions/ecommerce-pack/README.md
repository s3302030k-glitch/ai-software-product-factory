# Extension Pack: E-Commerce

> Adds ecommerce domain guidelines, catalog and product rules, cart and checkout state, order lifecycle, payment and refund boundaries, promotion and discount rules, ecommerce reporting, QA checklists, and role prompts.

This is a fully implemented extension pack, supplementing the core factory templates.

---

## Purpose and Scope

This pack **supplements** the core factory documents; it does **not** replace them. It is designed for software products that include product catalogs, product variants, prices, shopping carts, checkout flows, orders, order items, payment states, refunds, returns, promotions, discounts, inventory reservation, shipping and fulfillment, customer accounts, ecommerce reports, or marketplace-style transactions.

It is useful for building online stores, B2B order portals, marketplace MVPs, product catalog systems, checkout-enabled SaaS products, invoice and order workflows, and ecommerce admin panels.

This pack is strictly template-based and generic. It does not include product-specific or private business information. It does not include real customer data, product catalogs, payment data, credentials, project IDs, order IDs, invoice data, bank data, tax IDs, or company-specific ecommerce records.

> [!WARNING]
> **NO LEGAL, TAX, ACCOUNTING, PAYMENT COMPLIANCE, CONSUMER PROTECTION, CUSTOMS, SHIPPING COMPLIANCE, OR REGULATED ECOMMERCE ADVICE**: This extension pack does not provide legal, tax, accounting, payment compliance (PCI-DSS), consumer protection, customs, shipping compliance, or regulated ecommerce advice. All templates, guidelines, and prompts must be audited and approved by the product owner's legal, financial, tax, compliance, and operational professionals before deployment.

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|---------------------|
| **Cart Total Mismatch** | Mandates server-side total recalculation at checkout; client-side totals are treated as estimates only. |
| **Price Calculation Mistakes** | Establishes price snapshot rules at order creation and enforces source-of-truth separation. |
| **Discount Stacking Errors** | Requires explicit discount stacking rules, transactional usage limits, and server-side application. |
| **Checkout State Confusion** | Defines a documented checkout state machine with explicit valid transitions. |
| **Order/Payment State Mismatch** | Separates order state, payment state, and fulfillment state as independent lifecycle dimensions. |
| **Refund/Return Ambiguity** | Defines refund and return models explicitly, preserving original order and payment history. |
| **Inventory Reservation Mismatch** | Requires explicit inventory reservation and release rules approved by the product owner. |
| **Variant/SKU Confusion** | Mandates distinct product, SKU, and variant boundaries; merging requires owner approval. |
| **Tax/Shipping/Fee Boundary Mistakes** | Keeps tax, shipping, and fee rules explicit and owner-approved rather than invented by agents. |
| **Customer Data Exposure** | Enforces customer privacy scoping in reports, exports, and cross-tenant scenarios. |
| **Hidden Ecommerce Logic in UI** | Mandates that price calculations, discount rules, and order logic live in backend services, not UI components. |
| **Export/Report Mismatch with Source Orders** | Requires reconciliation between UI totals, export totals, and PDF totals against source order records. |

---

## Pack Components

### Documentation Guidelines (`docs/`)

- [Ecommerce Domain Model Guidelines](docs/ecommerce-domain-model-guidelines.md) — Core ecommerce modeling principles, entity distinctions, lifecycle states, price snapshot rules, and business rule ownership.
- [Catalog and Product Guidelines](docs/catalog-and-product-guidelines.md) — Product vs. SKU vs. variant model, categories, attributes, pricing boundaries, availability states, and soft delete rules.
- [Cart and Checkout Guidelines](docs/cart-and-checkout-guidelines.md) — Cart principles, guest vs. authenticated cart, checkout state machine, server-side recalculation, discount timing, and payment initiation.
- [Order Lifecycle Guidelines](docs/order-lifecycle-guidelines.md) — Order states, payment state vs. fulfillment state, order item snapshots, partial fulfillment, cancellation, and audit trail.
- [Promotion and Discount Guidelines](docs/promotion-and-discount-guidelines.md) — Coupon model, automatic discounts, eligibility, stacking rules, usage limits, and abuse prevention.
- [Ecommerce Payment and Refund Boundary Guidelines](docs/ecommerce-payment-refund-boundary-guidelines.md) — Payment provider boundary, payment state model, webhook idempotency, refund model, and sensitive data handling.
- [Ecommerce Reporting Guidelines](docs/ecommerce-reporting-guidelines.md) — Source-of-truth rules, sales/order/refund/promotion reports, reconciliation, timezone boundaries, and privacy scoping.
- [Ecommerce QA Checklist](docs/ecommerce-qa-checklist.md) — Comprehensive pre-release QA matrix covering catalog, cart, checkout, orders, payments, refunds, promotions, inventory, and reporting.

### AI Agent Role Prompts (`prompts/`)

- [Ecommerce Domain Architect](prompts/ecommerce-domain-architect-prompt.md) — Role for reviewing or designing the ecommerce domain model before implementation.
- [Catalog and Product Review Agent](prompts/catalog-product-review-agent-prompt.md) — Role for auditing product catalog, SKUs, variants, availability, media, and archive behavior.
- [Cart and Checkout Review Agent](prompts/cart-checkout-review-agent-prompt.md) — Role for reviewing cart behavior, checkout state, server-side recalculation, discount timing, and idempotency.
- [Order and Payment Review Agent](prompts/order-payment-review-agent-prompt.md) — Role for reviewing order lifecycle, payment and refund boundaries, fulfillment states, webhooks, and audit trail.
- [Promotion and Pricing Review Agent](prompts/promotion-pricing-review-agent-prompt.md) — Role for reviewing promotion rules, coupon behavior, discount stacking, usage limits, and rounding.
- [Ecommerce QA Agent](prompts/ecommerce-qa-agent-prompt.md) — Role for validating a product or release against the full ecommerce QA checklist.

---

## Recommended Usage

Follow these steps to integrate this extension pack into your product project:

1. **Initialize Core Kit First**: Copy the core factory documents (`core/docs/`) and prompt templates (`core/prompts/`) into your product project workspace before applying this pack.
2. **Apply Ecommerce Pack Selectively**: Copy this pack's folders (`docs/` and `prompts/`) into your project *only* if ecommerce, order, or checkout behavior is part of the product.
3. **Add Relevant Ecommerce Docs to Product Docs**: Merge the relevant ecommerce guidelines from `docs/` into your active product documentation. Not every guideline will apply to every product.
4. **Use Ecommerce Prompts Before Implementation and During Review/QA**: Assign specialized prompts to your AI agents to guide ecommerce domain design, catalog/checkout reviews, payment boundary reviews, and QA validations before code merges.
5. **Enforce Governance Gatekeeping**: Never approve price calculations, checkout behavior, payment and refund boundaries, inventory reservation, tax and shipping behavior, or official order documents without explicit human owner review and sign-off.

For workspace setup instructions and core governance rules, see [START_HERE.md](../../START_HERE.md).

---

## Related Extension Packs

This ecommerce pack is designed to complement the following sibling packs when applicable:

- [financial-business-logic-pack](../financial-business-logic-pack/README.md) — For money, rounding, discount calculations, payment and settlement, and audit trail considerations.
- [erp-operations-pack](../erp-operations-pack/README.md) — For inventory, stock movement, warehouse, shipment, and operational workflow considerations.
- [print-reporting-pack](../print-reporting-pack/README.md) — For order invoices, receipts, export documents, and ecommerce reports.
- [saas-multitenant-pack](../saas-multitenant-pack/README.md) — For tenant-scoped ecommerce and subscription or membership access boundaries.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Pack implemented: README, 8 docs, 6 prompts created | Factory |
