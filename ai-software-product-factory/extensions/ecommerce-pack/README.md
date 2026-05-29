# Extension Pack: E-Commerce

> Adds product catalog, shopping cart, checkout, payment processing, and order management documentation.

---

## When to Use This Pack

Use this extension pack when your product:

- Has a **product catalog** (physical or digital goods, services)
- Includes a **shopping cart** and **checkout flow**
- Processes **payments** (one-time, recurring, or both)
- Manages **orders** (creation, fulfillment, returns, refunds)
- Tracks **inventory** or stock levels
- Needs **pricing rules** (discounts, coupons, tiers, taxes)

---

## What This Pack Will Add (When Built)

### Additional Documents

| Document | Purpose |
|----------|---------|
| `product-catalog-spec.md` | Product types, variants, attributes, categories, images |
| `cart-checkout-flow.md` | Add to cart, cart management, checkout steps, guest checkout |
| `payment-integration-spec.md` | Payment provider integration, webhooks, error handling |
| `order-management-spec.md` | Order lifecycle, statuses, fulfillment, returns, refunds |
| `pricing-rules-spec.md` | Discounts, coupons, tax calculation, currency handling |
| `inventory-spec.md` | Stock tracking, low-stock alerts, reservation during checkout |

### Additional Prompts

| Prompt | Purpose |
|--------|---------|
| `ecommerce-engineer-prompt.md` | AI agent role for implementing e-commerce features safely |

### Additional Guardrails

- Price calculations must use integer/decimal types (never floating point)
- Payment processing must handle all failure cases (declined, timeout, duplicate)
- Order status transitions must follow a defined state machine
- Inventory must be reserved during checkout to prevent overselling
- Tax calculations must be jurisdiction-aware
- Refund logic must be audited and logged

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| Price calculation errors | Integer math and explicit rounding rules |
| Payment failures lose orders | Payment error handling and recovery spec |
| Overselling out-of-stock items | Inventory reservation during checkout |
| Tax compliance issues | Jurisdiction-aware tax calculation spec |
| Refund disputes | Audit logging and state machine for order lifecycle |

---

## Example Project Types

- Online stores (physical goods)
- Digital product marketplaces
- Subscription box services
- Service booking platforms
- Ticket sales platforms
- B2B wholesale ordering systems

---

## Status

> **Status: Placeholder / Planned Future Pack**
>
> This extension pack is currently a **placeholder**. The folder contains only this README. Full templates, prompts, and instructions will be added in a future version.
>
> **Core Governance Rule:** Extension packs are optional and exist to **supplement** core documents for specific product needs — they do **not** replace core documents.
>
> For workspace setup instructions and core rules, link back to [START_HERE.md](../../START_HERE.md).
