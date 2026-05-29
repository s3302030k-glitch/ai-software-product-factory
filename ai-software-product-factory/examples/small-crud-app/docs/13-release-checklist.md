# 13 — Release Checklist

> Pre-release, migration, environment, QA sign-off, rollback, and post-release verification steps for Invoice Tracker.

---

## Purpose

Ensure every release is safe, verified, and reversible. This checklist must be completed before any deployment to production.

## Status

`Template` — Copy and fill in for each release version.

---

## Release Details

| Attribute | Value |
|-----------|-------|
| **Release Version** | v1.0.0-MVP |
| **Release Owner** | Product Owner |
| **Batches Included** | P1-B1 through P6-B1 |
| **Target Environment** | Staging / Production |

---

## Pre-Release Checks

### Code Quality
- [ ] All validation commands pass (`npm run build`, `npm run lint`, `npm test`).
- [ ] No unresolved merge conflicts in the main branch.
- [ ] Implementation reports for all 11 roadmap batches are submitted and approved.

### Feature Completeness
- [ ] All 7 "Must Have" features are fully implemented (Auth, Client CRUD, Invoice CRUD, item entry, payment logging, derived status, dashboard totals).
- [ ] All 10 MVP pages specified in `06-pages-spec.md` are accessible.
- [ ] API endpoints conform strictly to `09-api-design.md` response shapes.

### Documentation
- [ ] `16-context-snapshot.md` is updated to reflect Phase 6 completion.
- [ ] All architectural choices are documented in `14-decision-log.md`.

---

## Migration Checks

- [ ] Prisma schema is synced with PostgreSQL database.
- [ ] Database migration files created (`prisma/migrations`).
- [ ] Rollback strategy verified (database snapshot rollback).

### Migration Commands
```bash
# Run migrations on staging/production
npx prisma migrate deploy

# Verify migration status
npx prisma migrate status
```

---

## Environment Checks

### Environment Variables
- [ ] `DATABASE_URL` is set to the production PostgreSQL cluster.
- [ ] `NEXTAUTH_SECRET` is set to a secure, randomly generated string.
- [ ] `NODE_ENV` is set to `production`.

### Infrastructure
- [ ] Hosting environment (Vercel/Railway) is provisioned and bound to domain name.
- [ ] SSL certificate is active (HTTPS enforced).

---

## QA Sign-Off

### Verification Checklist
- [ ] Automated linting and tests pass.
- [ ] Manual QA walkthrough of the 7 core flows completed on staging.
- [ ] Regression checks passed (no dashboard calculation discrepancies).

### Sign-Off
- **QA Tester:** Approved
- **Product Owner:** Approved

---

## Rollback Plan

### Rollback Trigger Conditions
- [ ] Crash loop on Vercel deployment.
- [ ] Database connection failures.
- [ ] Broken calculation formulas affecting dashboard numbers or balance due.
- [ ] Authentication failure (users cannot log in).

### Rollback Steps
1. **Redeploy previous deployment:** Revert to the last successful git commit tag on Vercel/Railway.
2. **Restore database:** If a destructive migration was run, restore the database to the snapshot backup taken immediately before migration.
3. **Verify:** Confirm the previous build is live and users can authenticate.

---

## Post-Release Monitoring

### Immediate (First 1 hour)
- [ ] Open the live application.
- [ ] Log in as Owner and verify the dashboard reads $0.00 (or seeded totals).
- [ ] Create a test client, draft an invoice, add line items, mark as sent, and record a payment. Verify status badge updates from Draft -> Sent -> Partially Paid.
- [ ] Delete the test data.
- [ ] Monitor Vercel console for 500 error logs.

---

## Known MVP Limitations (Documented)

- **Browser Print:** No custom PDF generation. Users must print or save as PDF using Chrome/Safari's print prompt.
- **Fixed Currency:** Fixed to USD ($) format.
- **Manual Log:** No card reader or merchant processing (manual record only).
- **Single-Tenant:** Accessible only by a single organization.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation for Invoice Tracker | Product Owner |
