# 11 — Development Roadmap

> Organizes delivery of the Invoice Tracker into phases and batches with clear tracking and validation.

---

## Purpose

Break down the MVP into ordered phases and actionable batches. Each batch is a discrete unit of work that can be assigned to the Coding Agent, validated, reviewed, and tracked.

## Status

`Approved`

---

## Roadmap Overview

| Phase | Name | Description | Batches | Status |
|-------|------|-------------|---------|--------|
| Phase 0 | Documentation & Setup | Establish documentation hierarchy and guardrails | 1 | `Complete` |
| Phase 1 | Foundation & Auth | Initial environment configuration, auth session setup | 2 | `Pending` |
| Phase 2 | Clients | Clients database schema and Client forms CRUD | 2 | `Pending` |
| Phase 3 | Invoices & Items | Invoices schema, items lists, and dynamic invoice form | 2 | `Pending` |
| Phase 4 | Payments & Calculations | Payments logging and dynamic status calculations | 2 | `Pending` |
| Phase 5 | Dashboard | Aggregated totals cards and landing interface | 1 | `Pending` |
| Phase 6 | QA & Release | Final testing checklists and deployment prep | 1 | `Pending` |

---

## Phase Details

### Phase 0: Documentation & Setup
**Status:** `Complete`  
**Objective:** Confirm specification clarity and governance rules.  
**Dependencies:** None.  
**Estimated Batches:** 1  

- **P0-B1 — Setup Documentation and Project Specs:** Create full spec docs for Invoice Tracker application.

---

### Phase 1: Project Foundation & Auth
**Status:** `Pending`  
**Objective:** Setup empty Next.js codebase, vanilla layout, and authentication framework.  
**Dependencies:** Phase 0 complete.  
**Estimated Batches:** 2

- **P1-B1 — Framework Initialization & Layout:** Initialize Next.js, structure global CSS and basic layouts.
- **P1-B2 — Simple Session Authentication:** Implement auth logins, session state middleware, and login view.

---

### Phase 2: Clients Management
**Status:** `Pending`  
**Objective:** Support Client entry, search lists, and detail logs.  
**Dependencies:** Phase 1 complete.  
**Estimated Batches:** 2

- **P2-B1 — Client Database Model & List Page:** Create clients schema, migration, seed, and list display with search input.
- **P2-B2 — Client Form (Create/Edit):** Create Client Form page with basic validator (non-empty name, email syntax check) and CRUD handlers.

---

### Phase 3: Invoices & Invoicing Items
**Status:** `Pending`  
**Objective:** Build dynamic itemized billing interface.  
**Dependencies:** Phase 2 complete.  
**Estimated Batches:** 2

- **P3-B1 — Invoices & Items Schema & List view:** Implement invoices database schemas, foreign keys, and invoices table lists.
- **P3-B2 — Invoices Form UI & Save Handlers:** Interactive invoice form where items are added dynamically inline.

---

### Phase 4: Payments & Calculations
**Status:** `Pending`  
**Objective:** Log payments and automatically derive status codes.  
**Dependencies:** Phase 3 complete.  
**Estimated Batches:** 2

- **P4-B1 — Payments Logging:** Implement payments schema and Record Payment modal dialog.
- **P4-B2 — Dynamic Calculations & Status Derivations:** Backend logic and database constraints mapping outstanding invoice status.

---

### Phase 5: Dashboard Overview
**Status:** `Pending`  
**Objective:** Display overall cash flow stats.  
**Dependencies:** Phase 4 complete.  
**Estimated Batches:** 1

- **P5-B1 — Financial Cards Overview:** Implement database query aggregations for dashboard totals.

---

### Phase 6: QA & Release
**Status:** `Pending`  
**Objective:** Verify application sanity and prepare for release.  
**Dependencies:** Phase 5 complete.  
**Estimated Batches:** 1

- **P6-B1 — E2E Verification & Release Checklist:** Confirm test coverage passes and run release prep scripts.

---

## Batch Template & Validation Example

For illustrative purposes, here is the detailed layout of batch **P1-B1**:

### Batch P1-B1: Framework Initialization & Layout
- **Status:** `Pending`
- **Objective:** Create baseline folder structure, style configurations, and base layouts.
- **Scope:**
  - Create global workspace config and Next.js structure.
  - Setup core vanilla layout (Sidebar navigation and Main wrapper).
  - Setup color and styling variables in `styles/globals.css`.
- **Validation Commands:**
  ```bash
  npm run lint
  npm run build
  ```
- **Manual Verification:**
  - Open `/` on local server. Verify sidebar renders correctly and adjusts dynamically to mobile viewport widths.

---

## Baseline-Aware Validation Expectations

- **New Projects:** Must pass validation check (0 errors, 0 warnings).
- **Existing Projects:** No new errors or warnings beyond established baseline. Baseline metrics must not worsen without approval.

---

## Report Format

All completed batches must be logged using the canonical implementation report format detailed in `15-ai-agent-operating-rules.md`.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation for Invoice Tracker | Product Owner |
