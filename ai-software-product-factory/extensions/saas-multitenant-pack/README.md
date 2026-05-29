# Extension Pack: SaaS Multi-Tenant

> Adds multi-tenancy architecture patterns, tenant isolation, subscription management, and onboarding documentation.

---

## When to Use This Pack

Use this extension pack when your product:

- Serves **multiple organizations or tenants** from a single codebase
- Requires **data isolation** between tenants (each tenant sees only their data)
- Has **subscription/billing** tiers with different feature sets
- Needs **tenant onboarding** (signup, provisioning, configuration)
- Requires **tenant administration** (tenant-level settings, user management)

---

## What This Pack Will Add (When Built)

### Additional Documents

| Document | Purpose |
|----------|---------|
| `multi-tenant-architecture.md` | Tenant isolation strategy (shared DB vs. schema vs. DB per tenant) |
| `tenant-data-model.md` | Tenant entity, tenant-user relationships, scoping patterns |
| `subscription-tiers-spec.md` | Feature gates, usage limits, plan definitions |
| `tenant-onboarding-flow.md` | Signup, provisioning, initial setup, welcome flow |
| `tenant-admin-spec.md` | Tenant-level settings, branding, user management |
| `tenant-isolation-checklist.md` | Verification that no data leaks between tenants |

### Additional Prompts

| Prompt | Purpose |
|--------|---------|
| `saas-architect-prompt.md` | AI agent role for designing and implementing multi-tenant patterns |

### Additional Guardrails

- Every database query must include tenant scoping
- Tenant ID must be derived from session, never from user input
- Cross-tenant data access must be impossible (even for internal tools without explicit design)
- Subscription tier checks must happen server-side
- Tenant deletion must handle all related data (cascade or archive)
- Feature flags must be tied to subscription tier

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| Data leaks between tenants | Tenant isolation checklist and query-level scoping |
| Wrong tenant sees wrong data | Tenant ID from session, never user input |
| Feature access without subscription | Server-side tier checking |
| Tenant onboarding is broken | Onboarding flow spec with all edge cases |
| Orphaned data after tenant deletion | Cascade/archive rules |

---

## Example Project Types

- B2B SaaS platforms (project management, CRM, helpdesk)
- White-label platforms
- Managed service dashboards
- Multi-organization collaboration tools
- Marketplace platforms with seller accounts

---

## Status

`Placeholder` — This extension pack contains only this README. Full content will be added in a future version.
