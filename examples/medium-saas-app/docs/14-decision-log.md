# 14 — Decision Log: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document records the architectural and scoping decisions made during the design of the Team Subscription Manager.

---

## Decisions Log

### DEC-001: Documentation-Only Reference Example
- **Date Placeholder**: 2026-05-30
- **Decision**: Develop this example strictly as a documentation and template reference without creating runnable code, package configs, or database schemas.
- **Rationale**: To demonstrate how the factory specifications scale to medium SaaS platforms without introducing code-level stack dependencies.
- **Alternatives Considered**: Create a full Next.js/PostgreSQL codebase. Rejected due to complexity, maintenance overhead, and stack bias.
- **Owner Approval Status**: Approved by Product Owner.

### DEC-002: No External Payment Provider Integration
- **Date Placeholder**: 2026-05-30
- **Decision**: Exclude real Stripe, PayPal, or card processing code. Billing actions are represented via mocked state changes.
- **Rationale**: Real merchant integrations require private developer tokens, sandbox accounts, webhook security verification, and create maintenance risks.
- **Alternatives Considered**: Build a mock billing provider service wrapper. Rejected to maintain a clean focus on documentation templates.
- **Owner Approval Status**: Approved by Product Owner.

### DEC-003: Subscriptions and Invoices as Placeholders
- **Date Placeholder**: 2026-05-30
- **Decision**: Model plans, seats, and historical billing transactions as static placeholders.
- **Rationale**: Simplifies verification of page layouts and data calculations without the need for active background cron-job workers.
- **Alternatives Considered**: Simulate database polling workers. Rejected to keep data scoping simple.
- **Owner Approval Status**: Approved by Product Owner.

### DEC-004: Organization Membership as the Tenant Isolation Boundary
- **Date Placeholder**: 2026-05-30
- **Decision**: Establish the `OrganizationMembership` relationship as the single validation point for access control and tenant isolation.
- **Rationale**: Decoupling users from direct organization scopes allows users to belong to multiple companies and switch tenants cleanly.
- **Alternatives Considered**: Single organization per user ID. Rejected because B2B users require multi-org access (e.g. agencies or external contractors).
- **Owner Approval Status**: Approved by Product Owner.

### DEC-005: Role Model is Organization-Scoped
- **Date Placeholder**: 2026-05-30
- **Decision**: Store user roles within the scope of specific organizations.
- **Rationale**: A user must be able to act as an Owner in one company but remain a Read-only Viewer in another.
- **Alternatives Considered**: Global user roles. Rejected because it breaks B2B multi-tenant logic.
- **Owner Approval Status**: Approved by Product Owner.

### DEC-006: Platform Support Admin Access is Audited and Limited
- **Date Placeholder**: 2026-05-30
- **Decision**: Isolate platform administrators from tenant data by default. Impersonation requires a registered ticket ID and triggers immutable event records.
- **Rationale**: Protects customer privacy and complies with standard security audit boundaries.
- **Alternatives Considered**: Give support staff direct DB read access. Rejected due to high risk of data leakage.
- **Owner Approval Status**: Approved by Product Owner.

### DEC-007: Exclude Native Mobile Application from MVP
- **Date Placeholder**: 2026-05-30
- **Decision**: Build the MVP specification for web platforms only, deferring mobile native components to post-launch roadmap phases.
- **Rationale**: Reduces UI/UX spec scope and keeps initial delivery focused on core multi-tenancy and seat gating rules.
- **Alternatives Considered**: Include React Native templates. Rejected to limit documentation complexity.
- **Owner Approval Status**: Approved by Product Owner.

### DEC-008: RTL and Internationalization (i18n) Readiness Included
- **Date Placeholder**: 2026-05-30
- **Decision**: Mandate CSS logical property layouts and translation key name spacing in the core specifications.
- **Rationale**: SMB teams are globally distributed and require localized interfaces and print reports.
- **Alternatives Considered**: English LTR-only MVP. Rejected because internationalization is a core architectural requirement for modern B2B SaaS.
- **Owner Approval Status**: Approved by Product Owner.

---

## Related Files

- [01-product-brief.md](01-product-brief.md) — Connects decisions to goals.
- [08-architecture.md](08-architecture.md) — Architectural implementation details.
