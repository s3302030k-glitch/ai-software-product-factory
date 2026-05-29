# Extension Pack: SaaS Multi-Tenant

> Adds multi-tenancy architecture patterns, tenant isolation, membership management, roles and permissions, subscription/billing boundary guidelines, checklists, and role prompts.

This is a fully implemented extension pack, supplementing the core factory templates.

---

## Purpose and Scope

This pack **supplements** the core factory documents; it does **not** replace them. It is designed for software products that include SaaS organizations, tenants, workspaces, team memberships, role-based access, subscription plans, feature limits, billing boundaries, seat management, tenant switching, tenant-scoped data, or multi-organization users.

It is useful for building B2B SaaS products, internal business platforms, agency/client portals, team collaboration tools, subscription products, dashboards, and admin panels.

This pack is strictly template-based and generic. It does not include product-specific or private business information. It does not include real customer data, tenant data, payment data, credentials, project IDs, subscription IDs, invoice data, bank data, tax IDs, or company-specific SaaS records.

> [!WARNING]
> **NO LEGAL, TAX, ACCOUNTING, OR REGULATED COMPLIANCE ADVICE**: This extension pack does not provide legal, tax, accounting, payment compliance (PCI-DSS), privacy compliance (GDPR/CCPA/HIPAA), or regulated SaaS advice. All templates, guidelines, and prompts must be audited and approved by the product owner's security, legal, financial, and tax professionals before deployment.

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| **Tenant Data Leakage** | Mandates database-level and query-level isolation scoping, blocking cross-tenant visibility. |
| **Incorrect Organization/Member Scoping** | Enforces clear separation between user accounts and tenant memberships. |
| **Role/Permission Boundary Mistakes** | Details server-side authorization enforcement and separation of authentication from authorization. |
| **Plan Gating Mistakes** | Enforces server-side feature access checks and trial/expiration rules. |
| **Subscription State Confusion** | Standardizes handling of active, past_due, cancelled, and expired states. |
| **Billing vs. Permission Coupling** | Separates financial status flags from access control checks to avoid silent logic overrides. |
| **Seat/Usage Limit Mistakes** | Defines explicit checks for user counts and resource usage quotas. |
| **Tenant Switching Bugs** | Sets requirements for resetting cached states and active context in UI and API layers. |
| **Cross-Tenant Reporting/Export Leaks** | Establishes explicit scoping rules for exports, queues, and background jobs. |
| **Hidden SaaS Rules in UI Components** | Mandates backend-enforced business rule engines rather than client-side hiding. |

---

## Pack Components

### Documentation Guidelines (`docs/`)

- [SaaS Domain Model Guidelines](docs/saas-domain-model-guidelines.md) — Core modeling principles, user vs. member, multi-organization membership patterns, tenant-scoped vs. global entities, and lifecycle states.
- [Tenant Isolation Guidelines](docs/tenant-isolation-guidelines.md) — Principles of tenant isolation, scoping patterns for DB/API/UI, tenant switching, cross-tenant admin controls, and background contexts.
- [Organization and Membership Guidelines](docs/organization-and-membership-guidelines.md) — Invitation flows, role lifecycles, default organizations, transfer of ownership, and membership audit requirements.
- [Roles and Permissions Guidelines](docs/roles-and-permissions-guidelines.md) — System vs. tenant roles, feature-level permissions, admin/super-admin boundaries, and server-side authorization rules.
- [Subscription and Plan Guidelines](docs/subscription-and-plan-guidelines.md) — Gating mechanics, usage and seat limits, trials, grace periods, and mapping provider states to app states.
- [SaaS Billing Boundary Guidelines](docs/saas-billing-boundary-guidelines.md) — Decoupled billing boundaries, customer/subscription IDs handling, webhook idempotency, and failed payment policies.
- [SaaS QA Checklist](docs/saas-qa-checklist.md) — Comprehensive pre-release QA matrix covering multitenancy, memberships, plans, billing boundaries, and regressions.

### AI Agent Role Prompts (`prompts/`)

- [SaaS Domain Architect](prompts/saas-domain-architect-prompt.md) — Role for designing and reviewing SaaS/multi-tenant domain structures, memberships, and entities.
- [Tenant Isolation Review Agent](prompts/tenant-isolation-review-agent-prompt.md) — Role for auditing scoping, query variables, background job contexts, webhooks, and exports.
- [SaaS Permissions Review Agent](prompts/saas-permissions-review-agent-prompt.md) — Role for auditing role separation, server-side checks, continuity, and change logs.
- [Subscription and Plan Review Agent](prompts/subscription-plan-review-agent-prompt.md) — Role for auditing limit gating, grace periods, state mappings, and seat tracking.
- [SaaS QA Agent](prompts/saas-qa-agent-prompt.md) — Role for executing the pre-release checklists and testing multi-org/tenant scenarios.

---

## Recommended Usage

Follow these steps to integrate this extension pack into your product project:

1. **Initialize Core Kit First**: Copy the core factory documents (`core/docs/`) and prompt templates (`core/prompts/`) into your product project workspace.
2. **Apply SaaS Multi-Tenant Pack**: Copy this pack's folders (`docs/` and `prompts/`) into your project *only* if SaaS, multi-tenancy, memberships, subscriptions, or plans are part of the product.
3. **Add to Product Documentation**: Merge the relevant guidelines from `docs/` directly into your active product documentation.
4. **Use Prompts in Dev & QA Cycles**: Assign specialized prompts to your AI agents to guide domain architect reviews, security isolation audits, and QA validations before code merges.
5. **Enforce Governance Gatekeeping**: Never approve tenant isolation, permission boundaries, subscription behavior, or billing-adjacent logic changes without explicit human owner approval.

For workspace setup instructions and core governance rules, link back to [START_HERE.md](../../START_HERE.md).
