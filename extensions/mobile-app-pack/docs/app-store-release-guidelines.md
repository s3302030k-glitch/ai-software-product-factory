# 13A — App Store Release Guidelines

> Outlines build versioning, privacy declarations, screenshot assets, phased rollouts, TestFlight/Internal testing protocols, and release checklists for Apple App Store and Google Play submissions.

---

## Purpose

Establish standard protocols for deploying mobile app binaries to official app stores. This document manages the risks of review rejections, unscheduled rollbacks, missing privacy label declarations, and lack of release traceability.

## Status

`Active` — Must be followed by release coordinators, product owners, and developers prior to initiating store submissions.

---

## App Store Release Principles

1. **No Legal or Compliance Advice**: This guideline does not provide legal, privacy, or app-store compliance advice. All store terms, privacy policies, and developer agreements must be vetted by the product owner's legal team.
2. **Review Mandatory**: All store metadata, app descriptions, and privacy label answers require explicit human owner and legal approval before submission.
3. **Privacy of Release Assets**: Screenshots, promotional videos, and app preview assets must not contain real customer data, real names, emails, transaction values, or private credentials. Use mock profiles only.
4. **Monitoring & Rollback Plan**: Every release must include active crash monitoring (e.g. Sentry/Crashlytics) and a documented rollback or hotfix procedure.

---

## Store Metadata Checklist

Before submitting the app to review, verify that the following store metadata are populated and approved:
- [ ] **App Name & Subtitle**: Matches branding, contains no trademarked names of other products.
- [ ] **App Description**: Explains features clearly; lists correct user roles (matching [04-user-roles.md](../../../core/docs/04-user-roles.md)).
- [ ] **Support & Marketing URLs**: Functional, hosted pages containing contact details and documentation links.
- [ ] **Privacy Policy URL**: Legally approved privacy policy hosted publicly.
- [ ] **Review Login Credentials**: Active mock test account (with preloaded sample data) for the app reviewer. Must bypass multi-factor authentication (MFA) or provide a fixed bypass token to prevent login failure during review.

---

## Versioning and Build Numbers

To ensure release traceability, follow a structured versioning format:
- **Marketing Version (Semantic Versioning)**: `MAJOR.MINOR.PATCH` (e.g., `1.0.0`). Set by the product manager, incremented for releases.
- **Build Version (Internal)**: A monotonic increasing integer (e.g., `104` or `2026053001` based on date). Must be unique for every uploaded binary.
- **Traceability**: Every uploaded build version must map directly to a specific Git commit hash in the codebase history.

---

## Privacy Labels and Permission Declarations

When filling out the App Store "App Privacy" labels (Apple Nutrition Labels) or Google Play "Data Safety" forms:
- Declare all data types collected by the app (e.g., email addresses for auth, location data for route mapping, photos for profile upload).
- Specify whether the data is used for tracking purposes or linked to the user's identity.
- Declared items must align exactly with [10-security-model.md](../../../core/docs/10-security-model.md).

---

## Screenshots and Preview Assets

- Ensure screenshots render on target screen dimensions (e.g., 6.7" and 6.5" displays for iOS).
- **Mock Data Scrubber**: Verify all visible text, charts, and lists in the screenshots use placeholder data. Never use screenshots capturing real customer workspaces, active projects, or personal names.

---

## TestFlight / Internal Testing Notes

- Every release candidate must pass through an internal alpha/beta testing phase:
  - **Internal Testing**: Share builds instantly with developers and QA via TestFlight (iOS) or Google Play Console Internal Sharing.
  - **External Beta**: Share builds with external beta users after passing automated test runs.
- Collect crash logs and validation feedback during the testing window (typically 3 to 7 days) before promoting to production.

---

## Rollout and Phased Release Notes

- **Phased Rollouts**: Use phased releases (iOS) or staged rollouts (Android) to distribute the update to users gradually:
  - Day 1: 1% of users.
  - Day 2: 2% of users.
  - Day 3: 5% of users.
  - Day 5: 10% of users.
  - Day 7: 20% of users.
  - Day 10: 50% of users.
  - Day 14: 100% of users.
- **Pause Capability**: If crash rates spike or critical regressions are discovered, pause the rollout immediately and prepare a hotfix build.

---

## Crash Monitoring and Rollback Notes

- Establish a baseline crash-free user rate (e.g. 99.9% crash-free sessions).
- If the crash-free session rate drops below 99.5% during a phased release, halt the rollout.
- **Rollback Limitation**: App stores do not support reverting to a previous version once an update is installed. Rollback is achieved by building and submitting a new hotfix binary with an incremented version/build number (e.g., `1.0.1`).

---

## Review Rejection Risk Notes

Be prepared to mitigate common app store rejection reasons:
- **Incomplete Profile**: Reviewer cannot log in because credentials failed or require SMS/MFA which is not accessible to the reviewer.
- **Performance**: App crashes on launch or freezes on screens due to failed network calls.
- **Missing Guidelines**: Inadequate explanation of native features (e.g. requesting location permission without explanatory copy in the permission prompt).
- **Subscription Gates**: Gated content without clear subscription pricing, terms, or restore buttons.

---

## Release Approval Checklist

Prior to clicking the final "Submit for Review" button, the release coordinator must verify:
- [ ] Automated build verification runs have passed.
- [ ] No new crash logs have been reported in the TestFlight test window.
- [ ] All screenshot assets use mocked/scrubbed placeholders.
- [ ] The reviewer test credentials have been tested and verified.
- [ ] The human product owner has explicitly approved the release version.

---

## Out of Scope

- Setting up App Store Connect API keys, provisioning profiles, or developer portal certificates.
- Generating binary builds using fastlane or custom CI/CD scripts.

---

## Guardrails

- [ ] Release screenshots must not contain real customer, project, or credential data.
- [ ] Review accounts must bypass MFA/SMS verification.
- [ ] Releases must not be promoted directly to 100% of users without a phased rollout stage.
- [ ] Marketing descriptions and support links must be checked for broken URLs.

---

## QA Checklist

- **Review Login Check**: Logged into the app using the exact credentials provided to the app store reviewer; verified access works.
- **URL Verification**: Tapped all support, contact, and privacy URLs; verified pages load correctly without 404 errors.
- **Traceability Check**: Confirmed that the marketing version and build number map to a tagged commit in the Git repository history.
- **Crash Rate Verification**: Checked TestFlight monitoring; confirmed crash-free rate is above the approved threshold.

---

## Related Core Files

- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — Core test protocols.
- [13-release-checklist.md](../../../core/docs/13-release-checklist.md) — Baseline release steps.
- [15-ai-agent-operating-rules.md](../../../core/docs/15-ai-agent-operating-rules.md) — Behavior constraints.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation of app store release guidelines | Antigravity |
