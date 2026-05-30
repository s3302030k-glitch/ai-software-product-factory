# 01 — Product Brief: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

---

## Product Name

```
Team Subscription Manager (TSM)
```

---

## Product Summary

Team Subscription Manager is a B2B SaaS platform that enables small-to-medium teams to manage their third-party software subscription plans, workspace seat limits, team permissions, notification settings, and invoice billing history from a single, unified admin portal. It helps team leaders track seat utilization across dynamic multi-tenant workspace environments and reconciles subscription tiers with actual active user seats.

---

## Problem Statement

Small-to-medium businesses frequently struggle to manage and track software subscription seats, costs, and access levels across multiple team members. The lack of visibility leads to seat waste (paying for inactive users), security risks (former employees retaining access due to stale roles), and billing confusion (untracked invoice histories). Teams need a centralized, lightweight multi-tenant portal to monitor seats, assign roles, review billing statements, and align software utilization with subscription plan limits.

---

## Target Customers

- **Small-to-Medium Businesses (SMBs)**: Managing 5 to 50 team members across multiple workspaces.
- **Team Leaders & Operations Managers**: Who need visibility into who has access to which roles and subscription plans.
- **Billing Managers / Accountants**: Who require regular access to invoice placeholders and historical seat usage statistics.

---

## Core Value Proposition

"Centralize team membership, control role-based access, and match your active seats to subscription plan boundaries from a single, multi-tenant workspace portal."

---

## Related Extension Packs Used

This example incorporates guidelines and structures from the following factory extension packs:
- **[SaaS Multi-Tenant Pack](../../../extensions/saas-multitenant-pack/README.md)**: Models tenants (Organizations) and User-to-Organization memberships, isolates tenant contexts, and controls seat-limit check boundaries.
- **[Supabase Pack](../../../extensions/supabase-pack/README.md)**: Defines conceptual schemas, Row Level Security (RLS) policies, and storage boundary concepts for user profiles and invoices.
- **[Financial Business Logic Pack](../../../extensions/financial-business-logic-pack/README.md)**: Establishes exact monetary scale constraints (integers representing subunit amounts, e.g., cents), and strict rounding rules for plans and invoices.
- **[Print & Reporting Pack](../../../extensions/print-reporting-pack/README.md)**: Standardizes PDF generation placeholders for invoices and structures data export logs for seat usage.
- **[RTL & i18n Pack](../../../extensions/rtl-i18n-pack/README.md)**: Integrates right-to-left layout and locale-aware formatting parameters for international teams.

---

## MVP Success Criteria

| Criteria | Measurement | Target |
|----------|-------------|--------|
| Multi-tenant Isolation | Security verification of PostgreSQL Row Level Security (RLS) rules | 100% data separation (zero leakage across tenants) |
| Seat Gating Accuracy | Functional validation of workspace invites blocking when seat limit is reached | 100% block rate on limit breaches |
| Invoice PDF Generation | Browser render and printable print styling verification | Clean page breaks, no cutoff text |
| Localization Readiness | RTL layout mirroring switch validation | Mirroring direction maps dynamically without overlapping elements |
| Operational Quality | Zero critical bugs in documentation workflows | 0 critical, < 2 minor issues |

---

## Non-Goals (MVP)

- **Real Payment Integrations**: No direct Stripe, PayPal, or merchant processor code. The MVP uses mocked subscription plans and placeholder payment statuses.
- **Real Tax Calculations**: No third-party tax API integration (e.g., Avalara). Tax fields on invoices are static placeholders.
- **Automated Billing/Invoicing Engine**: Invoices are generated as mock records at plan limits rather than processed by a background billing engine.
- **Mobile Native Application**: This is a web-only responsive application design. Native mobile app integration is out of scope.

---

## Assumptions

- Each user has a primary identity (User account) but can belong to multiple Organizations as a Member.
- Subscription limits (seats, organizations, workspaces) are enforced on the backend at the database query level.
- Rounding for financial placeholders follows round-half-to-even (Banker's rounding) where applicable.

---

## Risks and Mitigation

| Risk | Impact | Mitigation Strategy |
|------|--------|---------------------|
| Cross-tenant data leakage | High | Apply strict PostgreSQL Row Level Security (RLS) scoped to `organization_id` on all tables. |
| Inconsistent currency displays in exports | Medium | Store all monetary values as integers (subunits) and format in the UI based on locale formatting guidelines. |
| PDF invoice breaks on long lists | Low | Design PDF invoice templates with explicit CSS page-break print media styles. |

---

## Related Files

- [02-target-users.md](02-target-users.md) — Personas and detailed user permissions.
- [03-mvp-scope.md](03-mvp-scope.md) — Detailed feature boundaries and MoSCoW scoping.
- [07-data-model.md](07-data-model.md) — Database schema blueprints and data structures.
- [14-decision-log.md](14-decision-log.md) — Architectural decisions behind Team Subscription Manager.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation from template for v1.9.0 release | AI Product Architect |
