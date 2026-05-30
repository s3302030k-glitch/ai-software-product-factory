# 12 — QA Test Plan: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document specifies the Quality Assurance (QA) and testing plan for verifying the Team Subscription Manager specifications.

---

## QA Test Scenarios

### 1. Role and Access Control QA
- **Objective**: Verify that permission gates block unauthorized operations.
- **Test Steps**:
  1. Authenticate as a user holding the `Team Member` role.
  2. Attempt to request `GET /api/invoices` or access `/billing`.
  3. Verify that the response returns `403 Forbidden` and the UI routes the user to an Access Denied view.
  4. Authenticate as a `Billing Manager`.
  5. Attempt to invite a member. Verify that the request is blocked.

### 2. Tenant Isolation QA
- **Objective**: Confirm that user data does not spill across organization boundaries.
- **Test Steps**:
  1. Set up two mock organizations: Organization A and Organization B.
  2. Authenticate as a member of Organization A.
  3. Attempt to fetch workspaces using the ID of Organization B.
  4. Verify that the API returns `404 Not Found` or `403 Forbidden` (RLS policy check).

### 3. Invitation Flow QA
- **Objective**: Confirm that invite links expire and enforce security limits.
- **Test Steps**:
  1. Generate an invitation link.
  2. Force-expire the invitation token in the mock database.
  3. Attempt to navigate to the accept URL.
  4. Verify the page displays "Invitation link expired".
  5. Try to accept an invitation using an email different from the invitee. Verify that acceptance is blocked.

### 4. Subscription & Seat Limit QA
- **Objective**: Verify that membership limits are enforced on the backend.
- **Test Steps**:
  1. Select a plan with a seat limit of 3 (Free).
  2. Add 3 active members.
  3. Attempt to invite a 4th member.
  4. Verify that the "Invite" button is disabled and the API returns `422 Unprocessable Entity`.

### 5. Invoice Placeholder QA
- **Objective**: Ensure invoice lists and print layouts are correct.
- **Test Steps**:
  1. Navigate to the Invoice List as a Billing Manager.
  2. Verify that mock amounts (e.g. $15.00) are rounded and formatted correctly.
  3. Click "Download PDF".
  4. Verify that print headers hide navigation sidebar elements.

### 6. Dashboard & Report QA
- **Objective**: Reconcile live active seat counts against report displays.
- **Test Steps**:
  1. Count active members in the organization.
  2. Navigate to Dashboard.
  3. Verify that the "Active Seats" count matches the database total.
  4. Check that seat metrics are updated instantly when a member is removed.

### 7. Notification Preferences QA
- **Objective**: Verify user setting changes are saved.
- **Test Steps**:
  1. Navigate to Notification Preferences.
  2. Change email alerts toggle to false.
  3. Refresh page and confirm preference persists.

### 8. Audit Log QA
- **Objective**: Ensure security-relevant actions trigger audit entries.
- **Test Steps**:
  1. Perform a role change on a member.
  2. Navigate to the Activity Log.
  3. Verify that a record exists containing the actor, target, timestamp, and action description.

### 9. RTL and i18n QA
- **Objective**: Verify interface mirroring and locale formatting.
- **Test Steps**:
  1. Toggle interface language to a right-to-left language (e.g., Arabic/Hebrew).
  2. Verify that `dir="rtl"` is applied on the HTML root element.
  3. Inspect page layouts to ensure sidebars mirror positions correctly.
  4. Check that dates and currency symbols display according to selected locales.

### 10. Regression QA
- **Objective**: Ensure upgrades do not break core tenant boundaries.
- **Test Steps**:
  1. Run automated migration check simulations.
  2. Audit all endpoint queries to confirm `organization_id` filters are always present.

### 11. Release Readiness QA
- **Objective**: Confirm documentation completeness prior to release tags.
- **Test Steps**:
  1. Check that all relative links between specification files resolve successfully.
  2. Verify no absolute local paths or placeholder strings exist.

---

## Related Files

- [05-user-flows.md](05-user-flows.md) — User flows mapping to these tests.
- [13-release-checklist.md](13-release-checklist.md) — Checklists validating release readiness.
- [22-rtl-i18n-notes.md](22-rtl-i18n-notes.md) — Mirroring specs.
