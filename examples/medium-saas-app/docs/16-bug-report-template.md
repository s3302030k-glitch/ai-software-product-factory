# 16 — Bug Report Template: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document provides the standard template to be used when logging bug reports within the Team Subscription Manager.

---

# Bug Report: [Short Summary of the Issue]

## 1. Context Metadata

- **Area**: `[e.g., Billing / Member Invites / Organization Switcher / RTL Layout]`
- **Active Role**: `[e.g., Workspace Admin / Organization Owner / Read-only Viewer]`
- **Organization Context**: `[Name/ID of the mock organization, e.g., Acme Corp]`
- **Severity**: `[Critical / Major / Minor]`
- **Tenant/Security Impact**: `[Yes/No - does this issue bypass Row Level Security or allow cross-tenant data leakage?]`

---

## 2. Steps to Reproduce

1. `[First Step, e.g., Login as Workspace Admin in Acme Corp]`
2. `[Second Step, e.g., Navigate to Members page]`
3. `[Third Step, e.g., Click Invite Member and input email]`
4. `[Fourth Step, e.g., Click Submit]`

---

## 3. Observed vs. Expected Behavior

### Expected Behavior
`[Clear description of what should have occurred, e.g., The invitation should be created and list as pending because Acme Corp has 2 available seats under the Pro plan.]`

### Actual Behavior
`[Clear description of what actually occurred, e.g., The system returns a 422 Unprocessable Entity error stating seat limit reached, even though the team active count is only 13 out of 15.]`

---

## 4. Diagnostic Attachments

- **Console Log Placeholders**:
  ```
  [Paste Console Errors Here]
  ```
- **Network Payload Placeholders**:
  ```
  Request: POST /api/invitations { email: "user@example.com", role: "member" }
  Response: 422 Unprocessable Entity { error: "LIMIT_EXCEEDED" }
  ```

---

## 5. Resolution Context

- **Suspected Documents**:
  - `[Link to suspected spec file, e.g., docs/03-mvp-scope.md or docs/07-data-model.md]`
- **Proposed Acceptance Criteria**:
  - `[Checklist for verifying the fix, e.g., Invite flow correctly reads seat usage and does not trigger error when count is below limit.]`
