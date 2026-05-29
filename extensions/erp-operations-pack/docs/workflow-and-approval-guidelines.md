# Workflow and Approval Guidelines

> Defines rules for state machine design, authorization boundaries, multi-level approvals, and workflow visibility constraints.

---

## Purpose

This document provides principles and templates for designing structured state machines and approval flows. It ensures that business processes proceed through verified transitions, prevent unauthorized overrides, and provide full visibility into the current progress of actions. It supplements the user roles in [04-user-roles.md](../../../core/docs/04-user-roles.md) and security rules in [10-security-model.md](../../../core/docs/10-security-model.md).

## Status

`Active` — Must be followed when implementing status changes, transaction approvals, and role boundaries.

---

## Workflow Principles

### 1. Explicit State Machines
All workflow-driven documents (e.g., Purchase Requisitions, Quality Inspections, Shipments) must have their states defined explicitly.
- **Valid Transitions Only**: The system must enforce that a state can only transition via defined paths (e.g., a record cannot jump from `Draft` directly to `Approved` without undergoing the `Pending Approval` step).
- **Transition Logs**: Every state change must write a log record capturing the actor, original state, target state, timestamp, and reason (if transitioned manually or rejected).

### 2. No Code-Level Improvised Policies
AI agents and developers must not invent approval thresholds or business policies. All thresholds (e.g., orders over $5,000 require manager approval) must be defined in the data model or system configuration, as approved by the product owner.

---

## State Machine Design Template

When documenting a state machine, specify the transitions using the following format:

| Source State | Target State | Triggering Event / Action | Required Role | Conditions / Guardrails |
|---|---|---|---|---|
| `Draft` | `Pending Approval` | Submit for Review | Submitter | Record must pass validation checks. |
| `Pending Approval` | `Approved` | Approve | Approver (different from Submitter)| Submitter ID != Approver ID (Four-Eyes). |
| `Pending Approval` | `Rejected` | Reject | Approver | Rejection reason must be provided. |
| `Approved` | `Completed` | Process Fulfill | Execution Agent | Downstream steps must be completed. |

---

## Approval Workflow Design

### 1. The Four-Eyes Principle (Dual Authorization)
For high-risk operations (e.g., adjusting stock, issuing refunds, approving high-value orders):
- The user who created/submitted the record **cannot** be the user who approves it.
- **Rule**: Enforce this constraint at the database layer (e.g., trigger/constraint checking `creator_id != approver_id`) or application service layer, not just in UI code.

### 2. Approval Bypass Block
Bypassing approved workflow paths is forbidden unless an admin override rule is explicitly documented and configured.
- Any bypass must write an audit record with `Bypass: true`, the administrator's credentials, and a mandatory justification text.

### 3. Rejection and Escalation
- **Rejections**: Must return the record to `Draft` or a designated `Rejected` state. Rejection requires a mandatory text reason explaining why it was sent back.
- **Escalation**: If an approval is pending beyond an SLA period, the workflow must support routing to an escalated approver role or sending a reminder, without modifying the core state machine rules.

---

## Reopen, Reversal, and Correction Workflows

Once a workflow reaches a final state (e.g., `Completed`, `Approved`), it cannot be deleted or edited directly.
- **Reopening**: Returning a completed document to an active state (e.g., `Draft`) requires:
  - Explanatory reason log.
  - Resetting downstream records that depended on this approval.
  - Recording the action in the workflow history trail.
- **Reversals**: If a processed transaction must be undone, a compensating document must follow the same approval workflow (e.g., a credit note reversing an invoice).

---

## UI State Visibility Requirements

The user interface must clearly display the status of the workflow to help users understand what can happen next.
- **Visual Progress Tracker**: Show the current state, who currently holds action authority, and the path to completion.
- **Dynamic Action Buttons**: Action buttons (e.g., "Approve", "Submit") must be visible and active **only** if:
  - The current state allows the transition.
  - The logged-in user possesses the required role/permissions.
  - The dual-control (four-eyes) constraint is satisfied.
- **State Explanation**: If an action is blocked, display a brief info message (e.g., "Approval pending: awaiting manager review").

---

## Out of Scope

- Email/SMS notification template design and configurations.
- Integration with third-party external BPMN workflow engines.
- Active Directory / Single Sign-On role synchronization logic.

---

## Guardrails

- [ ] **NO BYPASS WITHOUT AUDIT**: All administrative bypasses of workflows must log an override event.
- [ ] **FOUR-EYES ENFORCED**: High-risk state changes must validate that the approver is different from the creator.
- [ ] **STATE IMMUTABILITY**: Final states must block further updates to the record's primary fields.
- [ ] **UI ACTION SEGREGATION**: The frontend must disable state transition actions for unauthorized roles.

---

## QA Checklist

- [ ] Verify that a user cannot approve an order they created.
- [ ] Attempt to bypass an approval state using a direct API request and confirm the request is rejected.
- [ ] Verify that rejecting a document requires entering a reason and transitions the status correctly.
- [ ] Check that completed workflow documents block direct edits to their fields (e.g., quantities, values, dates).

---

## Related Core Files

- [04-user-roles.md](../../../core/docs/04-user-roles.md) — Standard user roles definitions.
- [10-security-model.md](../../../core/docs/10-security-model.md) — RLS and API security rules.
- [erp-domain-model-guidelines.md](erp-domain-model-guidelines.md) — Lifecycle states definitions.
- [README.md](../README.md) — ERP Extension Pack README.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial workflow and approval guidelines for ERP Operations Pack | Antigravity |
