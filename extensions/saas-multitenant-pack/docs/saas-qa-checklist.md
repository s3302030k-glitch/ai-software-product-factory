# SaaS QA Checklist

> Comprehensive pre-release QA matrix and testing checklists to validate SaaS multi-tenancy, data isolation, membership states, role permissions, plan boundaries, and billing bridges.

---

## Purpose

Provide QA engineers and automated testing agents with a structured validation protocol. This checklist ensures zero cross-tenant leaks, verifies correct membership/permission bounds, checks subscription and feature limit enforcement, and certifies release readiness.

## Status

`Active` — Completion of this checklist is mandatory before signing off on any production releases containing multi-tenant, billing, or authorization changes.

---

## 1. Tenant Isolation QA Checklist

| Test ID | Scenario Description | Expected Result | Pass/Fail |
|---------|----------------------|-----------------|-----------|
| ISO-01 | Access Tenant B resource from Tenant A | API must return `403 Forbidden` or `404 Not Found`. | |
| ISO-02 | Direct ID enumeration in URL (e.g. `org/A/project/99`) | If project 99 belongs to Tenant B, UI must render a standard not-found state. | |
| ISO-03 | Direct database scoping verification | Query database using raw connection; verify every record contains a valid `organization_id` foreign key. | |
| ISO-04 | Cache key prefix validation | Inspect Redis or memcached keys; confirm all cached tenant records are prefixed with `{tenant_id}:`. | |
| ISO-05 | Client-side trust check | Attempt to change `organization_id` payload values in browser memory; verify server blocks action. | |

---

## 2. Organization & Membership QA Checklist

> [!IMPORTANT]
> **Must test users who belong to multiple organizations.**
> Multi-org users represent the highest risk for state confusion and scope leakage.

| Test ID | Scenario Description | Expected Result | Pass/Fail |
|---------|----------------------|-----------------|-----------|
| ORG-01 | Send invitation to new email | Invitation token is generated securely; email sent matching record details. | |
| ORG-02 | Accept invite on correct email | User completes sign-in; membership is provisioned under `Active` state. | |
| ORG-03 | Accept invite on wrong authenticated user | Verify invitation acceptance is blocked or warns user about mismatched accounts. | |
| ORG-04 | Remove member from organization | User's membership is marked as removed or deleted; user is immediately booted from active session. | |
| ORG-05 | Remove user with multiple memberships | User loses access *only* to the removed organization; they remain active in their other organizations. | |
| ORG-06 | Verify user account preservation | Confirm removing a member does NOT delete their global user account or personal credentials. | |

---

## 3. Roles & Permissions QA Checklist

| Test ID | Scenario Description | Expected Result | Pass/Fail |
|---------|----------------------|-----------------|-----------|
| PER-01 | Direct API call from restricted role | Viewer attempts `POST /projects` endpoint; server returns `403 Forbidden` immediately. | |
| PER-02 | Hiding verification vs API block | Verify that if a button is hidden in UI for `Member`, the underlying API endpoint is also protected server-side. | |
| PER-03 | Administrative continuity rule | Attempt to remove the last Owner or downgrade their role; system blocks action and requires ownership transfer. | |
| PER-04 | Role change propagation | Downgrade user from `Admin` to `Member`; verify that their active session permissions are immediately revoked. | |
| PER-05 | Cross-tenant role isolation | Verify that a user who is an Owner in Org A has strictly Viewer capabilities in Org B when switching contexts. | |

---

## 4. Subscription & Plan QA Checklist

| Test ID | Scenario Description | Expected Result | Pass/Fail |
|---------|----------------------|-----------------|-----------|
| SUB-01 | Seat Limit Cap validation | In a 5-seat plan, attempt to add or invite a 6th member; system blocks creation and prompts upgrade. | |
| SUB-02 | Usage Quota Cap validation | In a plan permitting 10 projects, attempt to create the 11th project; system blocks creation. | |
| SUB-03 | Plan Downgrade - excess data | Downgrade tenant to a tier below current active projects; verify data remains read-only and no silent deletes occur. | |
| SUB-04 | Free Trial Expiry | Set `trial_ends_at` to a past timestamp; verify tenant is automatically downgraded or redirected to billing. | |
| SUB-05 | Subscription transition statuses | Verify application behaves appropriately for `active`, `past_due`, `unpaid`, and `cancelled` states. | |

---

## 5. Billing Boundary QA Checklist

| Test ID | Scenario Description | Expected Result | Pass/Fail |
|---------|----------------------|-----------------|-----------|
| BIL-01 | Webhook Signature Verification | Send mock webhook without a valid signature header; server must reject with `401 Unauthorized`. | |
| BIL-02 | Webhook Idempotency Check | Re-send identical webhook `event_id` payload; confirm system skips second processing without errors. | |
| BIL-03 | Billing Portal Redirection | Click "Manage Billing"; verify secure session link is generated server-side and does not leak private API keys. | |
| BIL-04 | Failed payment banner | Simulate `past_due` webhook; verify a payment warning banner displays in the UI. | |
| BIL-05 | Sensitive ID isolation | Verify that Stripe/Paddle Customer and Subscription IDs are never sent in standard public front-end payloads. | |

---

## 6. Tenant Switching QA Checklist

> [!WARNING]
> **Tenant switching must reset scoped caches and UI state.**
> Stale data rendering after a switch represents a critical security risk.

| Test ID | Scenario Description | Expected Result | Pass/Fail |
|---------|----------------------|-----------------|-----------|
| SWT-01 | Clear Local Cache | Trigger organization switch; confirm React/Vue state, local storage keys, and API caches are purged. | |
| SWT-02 | Session JWT Regeneration | Confirm a new JWT is issued containing the correct active `org_id` and verified user claims. | |
| SWT-03 | Redirect homepage remount | Confirm user is redirected to dashboard homepage, forcing all components to remount. | |
| SWT-04 | Multi-Tab switching test | Switch organizations in Tab A; verify Tab B is notified or behaves gracefully without mixing data scopes. | |

---

## 7. Reporting & Export QA Checklist

| Test ID | Scenario Description | Expected Result | Pass/Fail |
|---------|----------------------|-----------------|-----------|
| EXP-01 | Tenant CSV Export Scoping | Generate projects CSV export; open file and confirm zero records from other organizations are included. | |
| EXP-02 | PDF print scoping | Verify report generation does not leak metrics or headers of other tenant accounts. | |
| EXP-03 | Private Export Storage | Confirm CSV/PDF generated files are stored in `organization_id` restricted subdirectories. | |

---

## 8. Super-Admin QA Checklist

| Test ID | Scenario Description | Expected Result | Pass/Fail |
|---------|----------------------|-----------------|-----------|
| SAD-01 | Impersonation Activation | Support staff activates impersonation; verify system logs an explicit `superadmin.impersonate` event. | |
| SAD-02 | Impersonation Write audit | Super-admin performs a write action while impersonating; confirm log includes super-admin user ID and timestamp. | |
| SAD-03 | Silent Access Block | Verify super-admins cannot access tenant-scoped resources without activating official Impersonation Mode. | |

---

## 9. Regression Checklist

- [ ] Confirm core CRUD workflows (e.g. creating/editing items) still operate seamlessly for single-tenant users.
- [ ] Confirm page rendering speeds are not impacted by the added context middlewares or tenant scoping queries.
- [ ] Confirm no global tables (e.g. default system lists) are broken or locked due to dynamic context filters.

---

## Bug Report Format

When a QA verification check fails, file a bug report in this format:

```markdown
### [Bug Title] - [SaaS Pack Failure Category]

**Test ID:** [e.g. ISO-01]
**Severity:** [High/Medium/Low]

**Preconditions:**
1. User authenticated as Tenant A (User UUID: `...`)
2. Target resource belongs to Tenant B (Resource UUID: `...`)

**Steps to Reproduce:**
1. Make raw HTTP GET request to `api/v1/projects/[Tenant B Resource UUID]`
2. Inspect response headers and body

**Expected Behavior:**
Response status `403 Forbidden` or `404 Not Found`.

**Actual Behavior:**
Response status `200 OK` and returned full project JSON payload.

**Log Traces:**
[Attach logs showing lacking tenant context variables]
```

---

## Release Readiness Checklist

- [ ] **100% Isolation Pass**: All `Tenant Isolation` and `Tenant Switching` tests are passed.
- [ ] **Idempotency Checked**: Webhook idempotent logger is verified active in production schema.
- [ ] **Super-Admin Audit Confirmed**: Super-admin log audit trails are active and verified.
- [ ] **Documentation Sync**: Product documentation matches these guidelines.
- [ ] **Human Owner Sign-Off**: Product owner has manually reviewed and signed off on this release candidate.

---

## Related Core Files

- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — Core QA rules and strategies.
- [13-release-checklist.md](../../../core/docs/13-release-checklist.md) — Base go-live verification steps.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation of SaaS QA Checklist | Antigravity |
