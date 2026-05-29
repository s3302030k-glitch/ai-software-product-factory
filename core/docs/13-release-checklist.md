# 13 — Release Checklist

> Pre-release, migration, environment, QA sign-off, rollback, and post-release verification steps.

---

## Purpose

Ensure every release is safe, verified, and reversible. This checklist must be completed before any deployment to production (or staging for QA).

## Status

`Template` — Copy and fill in for each release.

---

## Release Information

| Attribute | Value |
|-----------|-------|
| **Release Version** | _e.g., v1.0.0_ |
| **Release Date** | _YYYY-MM-DD_ |
| **Release Owner** | _Name_ |
| **Batches Included** | _e.g., P1-B1 through P3-B4_ |
| **Target Environment** | _Staging / Production_ |

---

## Pre-Release Checks

### Code Quality

- [ ] All validation commands pass (build, lint, type check, tests)
- [ ] No unresolved merge conflicts
- [ ] All batch implementation reports are complete
- [ ] All batch reviews are accepted (no "Changes Requested" batches)
- [ ] Code follows project architecture (`08-architecture.md`)

### Feature Completeness

- [ ] All "Must Have" features from `03-mvp-scope.md` are implemented
- [ ] All Must Have user flows from `05-user-flows.md` are working
- [ ] All pages match specs in `06-pages-spec.md`
- [ ] All API endpoints match contracts in `09-api-design.md`

### Documentation

- [ ] `16-context-snapshot.md` is up to date
- [ ] `14-decision-log.md` captures all decisions made during development
- [ ] `11-development-roadmap.md` reflects actual completion status
- [ ] README or user-facing docs are updated (if applicable)

---

## Migration Checks

- [ ] All database migrations have been created and tested
- [ ] Migrations run successfully on a fresh database
- [ ] Migrations run successfully on a copy of production data
- [ ] Migrations are reversible (rollback tested)
- [ ] No data loss during migration
- [ ] Migration order is correct (no dependency issues)
- [ ] Seed data is appropriate for the target environment

### Migration Commands

```bash
# Run migrations
[e.g., npx prisma migrate deploy]

# Verify migration status
[e.g., npx prisma migrate status]

# Rollback command (if needed)
[e.g., npx prisma migrate rollback]
```

---

## Environment Checks

### Environment Variables

- [ ] All required environment variables are set
- [ ] No development/test values in production config
- [ ] API keys and secrets are production values
- [ ] Database connection string points to correct database
- [ ] Email service is configured for production

### Infrastructure

- [ ] Server/hosting is provisioned and configured
- [ ] Domain/DNS is configured
- [ ] SSL certificate is valid and active
- [ ] CDN is configured (if applicable)
- [ ] File storage is configured
- [ ] Monitoring/alerting is active

### Third-Party Services

- [ ] Authentication provider is configured for production
- [ ] Payment provider is in live mode (if applicable)
- [ ] Email service is verified and in production mode
- [ ] Analytics tracking is enabled
- [ ] Error reporting service is connected

---

## QA Sign-Off

### Automated QA

- [ ] All unit tests pass
- [ ] All integration tests pass
- [ ] All E2E tests pass (if applicable)
- [ ] Security audit passes (no critical vulnerabilities)

### Manual QA

- [ ] Full QA checklist from `12-qa-test-plan.md` completed
- [ ] Regression checklist passed
- [ ] All critical/major bugs resolved
- [ ] Edge cases tested
- [ ] Mobile/responsive testing completed

### Sign-Off

| Reviewer | Role | Status | Date |
|----------|------|--------|------|
| _Name_ | _QA_ | `Approved` / `Blocked` | _YYYY-MM-DD_ |
| _Name_ | _Product Owner_ | `Approved` / `Blocked` | _YYYY-MM-DD_ |
| _Name_ | _Security_ | `Approved` / `Blocked` | _YYYY-MM-DD_ |

---

## Rollback Plan

### Rollback Trigger Conditions

_When should we rollback?_

- [ ] Critical bug affecting > 10% of users
- [ ] Data corruption detected
- [ ] Security vulnerability discovered
- [ ] Authentication/authorization failure
- [ ] Performance degradation > 50%

### Rollback Steps

1. **Identify** — Confirm the issue and decide to rollback
2. **Communicate** — Notify team of rollback decision
3. **Revert deployment** — Roll back to previous version
   ```bash
   [e.g., git revert to previous tag]
   [e.g., Redeploy previous container image]
   ```
4. **Rollback database** — If migrations were applied
   ```bash
   [e.g., npx prisma migrate rollback]
   ```
5. **Verify** — Confirm previous version is working
6. **Investigate** — Document what went wrong
7. **Plan fix** — Create a batch to address the root cause

### Rollback Time Target

| Step | Target Time |
|------|-------------|
| Decision to rollback | < 15 minutes from detection |
| Deployment rollback | < 30 minutes |
| Database rollback | < 30 minutes |
| Full rollback complete | < 1 hour |

---

## Post-Release Monitoring

### Immediate (First 1 hour)

- [ ] Application is accessible
- [ ] Login flow works
- [ ] Core user flows complete successfully
- [ ] No error spikes in monitoring
- [ ] No performance degradation
- [ ] Database connections stable

### Short-Term (First 24 hours)

- [ ] Error rates remain at baseline
- [ ] No user-reported issues
- [ ] Background jobs running normally
- [ ] Email delivery working
- [ ] Resource usage (CPU, memory, disk) stable

### Medium-Term (First 7 days)

- [ ] User engagement metrics normal
- [ ] No delayed-onset bugs
- [ ] Performance trends stable
- [ ] No data integrity issues discovered

---

## Scope

- This document is a **checklist template** for each release.
- Copy and fill in for every deployment.

## Out of Scope

- Feature specifications
- Test case writing
- Infrastructure provisioning procedures

## Guardrails

- [ ] No release without QA sign-off
- [ ] No release without a rollback plan
- [ ] All critical/major bugs must be resolved before release
- [ ] Post-release monitoring must be assigned to a person

## Related Files

- `11-development-roadmap.md` — Phases and batches included in this release
- `12-qa-test-plan.md` — QA checklist and testing strategy
- `10-security-model.md` — Security checklist referenced during sign-off

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
