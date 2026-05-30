# 13 — Release Checklist: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document specifies the operational checklist, verification gates, and approvals required before publishing changes to the Team Subscription Manager reference documentation.

---

## Pre-Release Checklist

### 1. Documentation Completeness
- [ ] Verify that all 22 reference documentation files exist.
- [ ] Confirm all templates are fully populated (no empty brackets or unresolved prompts).
- [ ] Ensure the non-runnable nature of the example is explicitly highlighted in the README.

### 2. Link Hygiene
- [ ] Audit all relative file links in the README.md and documentation folders.
- [ ] Confirm no absolute machine paths (e.g. `C:/Users/...` or `d:/aitemp/...`) exist.
- [ ] Verify no `file:///` protocols are used in documentation links.

### 3. Privacy & Data Integrity
- [ ] Scan all documents for real usernames, tenant records, credentials, bank account numbers, or tax IDs.
- [ ] Ensure that only fictional placeholder names (e.g., "Acme Corp", "Beta Corp") are used.
- [ ] Confirm no private business information is present.

### 4. Specification Reviews
- [ ] **Role Matrix Reviewed**: Verify the role matrix correctly details allowed/restricted actions for all 6 roles.
- [ ] **Tenant Isolation Reviewed**: Ensure RLS policies and organization boundaries are correctly specified.
- [ ] **Billing Boundaries Reviewed**: Confirm Stripe is excluded, and billing states are decoupled from permission rules.
- [ ] **Invoice Placeholder Reviewed**: Verify precision pricing rules (subunit integers) and layout guidelines.
- [ ] **Reports Reviewed**: Confirm live seat reconciliation and CSV export logic are correct.
- [ ] **QA Checklist Reviewed**: Ensure all 11 QA test scenarios are present and defined.

### 5. Owner Approval Gates
- [ ] Obtain explicit Human Product Owner approval before tagging changes.
- [ ] Document approvals and review comments in the decision log.

---

## Release Notes (v1.9.0 Placeholder)

```
Release: Team Subscription Manager Documentation Reference (v1.9.0)
Date: YYYY-MM-DD
Status: Approved

Summary:
Initial release of the medium SaaS application documentation reference. Details a multi-tenant B2B subscription portal integrating SaaS multitenancy, Supabase architecture concepts, exact rounding rules, print-friendly invoice page layouts, and RTL-ready UI mirroring guidelines.
```

---

## Related Files

- [11-development-roadmap.md](11-development-roadmap.md) — Preceding stage releases.
- [14-decision-log.md](14-decision-log.md) — Documented approvals.
