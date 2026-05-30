# 11 — Development Roadmap: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document establishes the staged timeline and batching checklist for delivering the Team Subscription Manager specifications.

> [!IMPORTANT]
> **ROADMAP FOR SPECIFICATIONS ONLY**: This roadmap tracks the delivery of documentation reference assets, not active application code. No runnable modules or code bases are initialized during these phases.

---

## Roadmap Summary Matrix

| Stage | Focus Area | Primary Deliverables | Dependencies |
|-------|------------|----------------------|--------------|
| **Stage 0** | Governance & Setup | Priority rules, workspace configuration | None |
| **Stage 1** | Org & Membership | Tenant structure, switching, invitations | Stage 0 |
| **Stage 2** | Roles & Permissions | RBAC permissions mapping, access bounds | Stage 1 |
| **Stage 3** | Plan & Subscriptions | Pricing definitions, seat limit thresholds | Stage 2 |
| **Stage 4** | Billing & Invoices | Invoice placeholders list, printable views | Stage 3 |
| **Stage 5** | Dashboard & Logs | Utilization cards, activity feeds, metrics | Stage 4 |
| **Stage 6** | Preferences | Notification toggles, account views | Stage 5 |
| **Stage 7** | Security & Hardening | RLS logic checks, support admin limits | Stage 6 |
| **Stage 8** | Release Readiness | Checklists, bug reporting templates | Stage 7 |

---

## Stage Specifications

### Stage 0: Governance and Docs Setup
- **Objective**: Standardize documentation structure and priority guidelines.
- **Acceptance Criteria**:
  - `README.md` and basic priorities are created.
  - Workspace directories (`docs/` and `prompts/`) are initialized.

### Stage 1: Org / Membership Foundation
- **Objective**: Define tenant models, invitations, and membership switching layouts.
- **Acceptance Criteria**:
  - `01-product-brief.md`, `02-target-users.md`, and `03-mvp-scope.md` are drafted and approved.
  - Basic membership lifecycle flows are mapped.

### Stage 2: Roles / Permissions
- **Objective**: Map RBAC permissions and access rules.
- **Acceptance Criteria**:
  - Complete `04-user-roles.md` role matrix.
  - Document system vs. organization authorization boundaries.

### Stage 3: Plans / Subscription Placeholders
- **Objective**: Define subscription plans and seat limits.
- **Acceptance Criteria**:
  - Subscription states (Active, Past Due) are documented.
  - Seat limit checking logic is specified.

### Stage 4: Billing / Invoice Placeholder Surfaces
- **Objective**: Design billing layout placeholders.
- **Acceptance Criteria**:
  - Invoice metadata schemas and printable invoice layouts are specified.
  - Stripe exclusions are explicitly stated.

### Stage 5: Dashboard / Reports / Activity Log
- **Objective**: Define reporting metrics and logs.
- **Acceptance Criteria**:
  - Dashboard panels (active member counts, limit charts) are detailed.
  - Activity log data structures are finalized.

### Stage 6: Notifications and Preferences
- **Objective**: Detail notification controls and profile settings.
- **Acceptance Criteria**:
  - Checkbox layout preferences are mapped in page specs.
  - User settings structures are detailed.

### Stage 7: Security / QA Hardening
- **Objective**: Specify RLS rules and testing procedures.
- **Acceptance Criteria**:
  - RLS policies and support admin impersonation restrictions are documented.
  - QA test cases are defined in `12-qa-test-plan.md`.

### Stage 8: Release Documentation
- **Objective**: Complete operational checklists and release guidelines.
- **Acceptance Criteria**:
  - Release checklists and bug report templates are completed.
  - Prompt verification agents are prepared.

---

## Related Files

- [03-mvp-scope.md](03-mvp-scope.md) — Source of prioritized features.
- [13-release-checklist.md](13-release-checklist.md) — Gatekeeper rules before staging tags.
- [17-batch-request-template.md](17-batch-request-template.md) — Implementation batch forms.
