# Catalog and Product Guidelines

> Product catalog principles, product vs. SKU vs. variant model, categories, attributes, pricing boundaries, availability states, inventory linkage, media, search, and archive rules.

This document supplements the core factory documents. It does not replace them.

---

## Purpose

Define the rules for modeling, managing, and governing a product catalog in ecommerce products. This prevents common catalog mistakes: merging product, SKU, and variant entities prematurely, treating price display as the source of truth for calculations, silently hiding archived products from historical orders, and including unsafe media path references.

## Status

`Active` — Applies to all products that include a product catalog, product variants, SKUs, pricing, or product availability.

---

## Product Catalog Principles

1. **Catalog serves customers, not just admins.** The product catalog must be designed for both customer-facing display (storefront) and admin management. These are different views of the same data; they must not diverge into different data sources.
2. **Price display is not the price calculation source.** The price shown in a UI component is for display only. The authoritative price for checkout and order calculations is computed server-side from the stored price field on the SKU or product record.
3. **Catalog changes do not alter historical orders.** Editing a product's name, price, description, or status must not retroactively change any existing order item records.
4. **Archived products are not deleted.** An archived product must remain accessible for historical order views, reports, and exports. Deletion is a separate, owner-approved action.

---

## Product vs. SKU vs. Variant Model

### Product

- The top-level catalog record representing a thing being sold.
- Has a name, description, category, status, and optional set of attributes.
- A product with multiple attributes (e.g., color, size) generates multiple variants.
- A product with no variants still has exactly one implicit SKU.

### SKU (Stock Keeping Unit)

- A unique, sellable unit. Each distinct purchasable configuration has its own SKU.
- Carries its own price, stock quantity, availability, and fulfillment properties.
- Is the entity that appears on an order item.
- Must not be merged with the product record unless the product owner explicitly approves a simplified single-SKU model.

### Variant

- A specific combination of attribute values (e.g., color = Blue, size = Large) that corresponds to one SKU.
- Variant options are defined at the product level. Variant combinations produce SKUs.
- Variant attribute definitions (e.g., "Color", "Size") are distinct from their values (e.g., "Blue", "Large").

**Rules:**
- Products, SKUs, and variants must not be modeled as a single entity unless the owner explicitly approves and documents a simplified model.
- Each SKU must have an independent price, stock status, and availability state.
- Agents must not merge these entities based on assumptions about product simplicity.

---

## Product Categories and Collections

- **Categories**: Hierarchical groupings of products (e.g., Clothing > Tops > T-Shirts). A product may belong to one primary category.
- **Collections**: Curated, often non-hierarchical groupings (e.g., "Summer Sale", "New Arrivals"). A product may belong to multiple collections.
- Category and collection membership is a property of the product record, not the SKU.
- Archived categories must not be silently reused as active categories.

---

## Product Attributes and Options

- Attributes define the dimensions along which variants are created (e.g., "Color", "Size", "Material").
- Options are the valid values for each attribute (e.g., Color options: Red, Blue, Green).
- The product owner must define which attributes are required and which are optional for each product type.
- Attributes used for variant generation must be distinguished from informational attributes (e.g., "Weight", "Country of Origin") that do not generate variants.

---

## Pricing Model Boundaries

- **Base price**: The standard selling price for a SKU. Stored on the SKU record.
- **Sale price / compare-at price**: An optional display price showing a marked-down rate. This is a display field; it must not be used for discount calculations unless explicitly modeled.
- **Tiered / volume pricing**: If applicable, tiered pricing rules must be defined explicitly by the owner and applied server-side.
- **Price display**: UI price display is for presentation only. Checkout and order creation must recalculate price server-side from the authoritative SKU price record.
- **Currency**: The currency for each price must be explicit. Multi-currency support requires owner approval and alignment with [financial-business-logic-pack](../../financial-business-logic-pack/README.md).

**Rules:**
- Agents must not invent pricing tiers, tax-inclusive display logic, or currency conversion rules.
- Price display formatting (currency symbol, decimal places) must not alter the stored numeric value.

---

## Product Availability and Active/Inactive States

| State | Meaning |
|-------|---------|
| `active` | Product is visible and available for purchase |
| `inactive` / `draft` | Product exists but is not visible on storefront or purchasable |
| `out_of_stock` | Product is active but has no available inventory |
| `archived` | Product is discontinued; not visible on storefront but preserved in history |
| `deleted` | Product record is permanently removed; only with explicit owner approval |

**Rules:**
- Archived products must remain readable for historical order views and reports.
- Inactive/draft products must not appear in customer-facing search or catalog unless in a preview mode.
- State transitions must follow an owner-approved state machine.
- Deleting a product must be a deliberate, confirmed owner action — not the result of a soft-delete default.

---

## Inventory and Stock Relationship

- Inventory (stock levels, reservation status) is associated with a SKU, not a product.
- If the product uses ERP-style stock management, inventory data should align with [erp-operations-pack](../../erp-operations-pack/README.md).
- Inventory availability affects product display (e.g., "Out of Stock" badge) but is not the source of truth for price.
- Inventory reservation behavior during checkout must be explicitly defined. See [cart-and-checkout-guidelines.md](cart-and-checkout-guidelines.md).

---

## Image and Media Metadata

- Product images and media are associated with the product or a specific SKU/variant.
- Media metadata must include: URL (or storage path), alt text, display order, and whether it is the primary image.
- Media must not include local filesystem paths (e.g., `C:\Users\...`, `/home/...`) in any stored or exported record.
- Media must not include private internal URLs that expose infrastructure details.
- Image storage and access policy must follow the product's defined storage guidelines (e.g., [supabase-pack](../../supabase-pack/README.md) if Supabase Storage is used).

---

## Search, Filter, and Sort Notes

- Product search, filtering, and sorting are presentation-layer concerns that must be backed by indexed or query-optimized data.
- Filterable attributes (e.g., color, size, price range, category) must be explicitly defined by the owner.
- Search behavior (keyword match fields, ranking logic) must be documented, not invented by agents.
- Archived or inactive products must be excluded from customer-facing search results by default.
- Admin search may include all statuses; this must be a separate query path or filter parameter.

---

## Soft Delete and Archive Rules

- **Soft delete** means marking a record as inactive without removing it from the database.
- **Archive** means marking a product as discontinued; it is no longer available for new orders but must remain in historical records.
- **Hard delete** means permanent removal. This requires explicit owner approval and a check that the product is not referenced in any existing order.
- Agents must not implement hard delete as a default. Soft delete or archive must be the default.
- Reports and exports must correctly exclude or include archived/deleted products based on the report's defined scope.

---

## Out of Scope

This document does not cover:

- Real product data, product catalogs, or product images
- Tax calculation rules or jurisdiction-specific pricing
- Shipping weight, dimensions, or carrier integration
- Legal compliance for product descriptions or regulated goods
- Payment processing or order fulfillment logic
- Application source code or database migrations

---

## Guardrails

- [ ] Products, SKUs, and variants are not merged into a single entity without owner approval.
- [ ] Price display is never the source of truth for price calculations.
- [ ] Archived products remain visible in historical order views.
- [ ] No local filesystem or private URL paths appear in media metadata.
- [ ] Inventory relationship aligns with erp-operations-pack when stock management is involved.
- [ ] No real product data, prices, or catalogs are included.
- [ ] Agents do not invent pricing tiers, tax rules, or product taxonomy without owner approval.

---

## QA Checklist

- [ ] Do products with multiple variants each have distinct SKUs?
- [ ] Does each SKU carry its own price and stock status?
- [ ] Is the product/SKU/variant boundary documented and owner-approved?
- [ ] Are inactive and draft products hidden from the storefront?
- [ ] Are archived products still visible in historical order views?
- [ ] Do price changes in the catalog leave existing order items unchanged?
- [ ] Is the base price stored on the SKU and not derived from a display component?
- [ ] Are product image paths safe (no local paths, no private infrastructure URLs)?
- [ ] Is category and collection membership stored on the product record?
- [ ] Does admin search include all statuses while customer search excludes inactive/archived?
- [ ] Is hard delete blocked without explicit owner approval?

---

## Related Core Files

- [../../../core/docs/07-data-model.md](../../../core/docs/07-data-model.md) — Entity, field, and relationship definitions
- [../../../core/docs/06-pages-spec.md](../../../core/docs/06-pages-spec.md) — Page-level specifications for product catalog pages
- [../../../core/docs/09-api-design.md](../../../core/docs/09-api-design.md) — API contracts for catalog endpoints
- [../../../core/docs/10-security-model.md](../../../core/docs/10-security-model.md) — Access control for admin vs. customer catalog access
- [../../../core/docs/15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Agent behavior constraints

## Related Pack Files

- [ecommerce-domain-model-guidelines.md](ecommerce-domain-model-guidelines.md) — Core ecommerce entity distinctions and modeling principles
- [cart-and-checkout-guidelines.md](cart-and-checkout-guidelines.md) — Inventory reservation timing and checkout rules
- [order-lifecycle-guidelines.md](order-lifecycle-guidelines.md) — Order item snapshots and archived product references
- [ecommerce-reporting-guidelines.md](ecommerce-reporting-guidelines.md) — Product and catalog reporting rules

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation | Factory |
