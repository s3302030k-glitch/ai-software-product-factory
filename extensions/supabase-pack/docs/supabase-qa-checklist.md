# 12A — Supabase QA Checklist

> Comprehensive pre-release verification protocol for Row Level Security, Auth, Storage, and Edge Functions.

---

## Purpose

This document provides a structured quality assurance validation protocol to verify the security, functionality, and stability of Supabase integrations before releasing changes to production. It supplements the core QA template in [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md).

## Status

`Active` — Must be completed and documented by the QA Agent during the release phase.

---

## Pre-Release Supabase Checklist

Every release involving Supabase changes must go through the following verification checkpoints:
1. **Migration check**: All SQL migrations run successfully on a clean database emulator.
2. **RLS validation**: Every new table has RLS explicitly enabled, and access matches specs.
3. **Storage isolation**: Storage buckets deny unauthorized reads/writes.
4. **Edge Function isolation**: All client-accessible edge functions enforce token authorization.

---

## RLS Testing Matrix

Verify database table permissions against the following checklist:

| Target Table | Actor Role | Operation (SELECT/INSERT/UPDATE/DELETE) | Expected Result (Allow/Deny) | Actual Result (Passed/Failed) |
|---|---|---|---|---|
| `example_table` | Anonymous | SELECT | Deny | |
| `example_table` | Authenticated Owner | SELECT / INSERT | Allow | |
| `example_table` | Authenticated Tenant B | SELECT / UPDATE | Deny (Tenant A rows) | |
| `example_table` | Authenticated Owner | DELETE | Deny (unless specified) | |
| `example_table` | Administrator | SELECT / UPDATE / DELETE | Allow | |

---

## Auth Testing

- [ ] Verify magic link and email login redirect to approved frontend URLs (no wildcard or localhost URLs permitted in production).
- [ ] Confirm registration trigger successfully creates user profiles.
- [ ] Verify session tokens refresh automatically without user logging out.
- [ ] Verify that password reset links expire and can only be used once.

---

## Storage Testing

- [ ] Verify public bucket paths return files without authentication headers.
- [ ] Verify private bucket paths block unauthenticated HTTP requests (HTTP 401/403).
- [ ] Verify User A cannot read or download private files from User B's storage folder.
- [ ] Verify that uploaded files exceeding maximum size limits configured in dashboard are rejected.
- [ ] Verify file uploads with incorrect mime types are rejected.

---

## Edge Function Testing

- [ ] Verify calls without `Authorization` header return HTTP 401.
- [ ] Verify calls with invalid or expired JWT return HTTP 401.
- [ ] Verify preflight CORS requests (`OPTIONS`) return HTTP 200 with correct headers.
- [ ] Verify request payloads containing invalid JSON structures return HTTP 400.
- [ ] Verify that function errors do not leak database credentials or internal stack traces in response bodies.

---

## Migration Validation

- [ ] Confirm no existing migration files have been modified (hashes match).
- [ ] Execute `supabase db lint` to check for syntax and reference errors.
- [ ] Verify that testing seeds do not pollute staging/production migrations.
- [ ] Confirm that destructive operations (e.g. `DROP COLUMN`) have written owner approval.

---

## Environment Variable Checks

- [ ] Verify `SUPABASE_SERVICE_ROLE_KEY` is not present in the frontend bundle.
- [ ] Verify all production keys are loaded securely from the hosting environment (e.g., Vercel, Netlify) and not committed to git.
- [ ] Verify database connection strings use secure SSL protocols.

---

## Regression Checklist

- [ ] Existing core RLS policies still pass validation tests.
- [ ] Database triggers do not fail during standard user profile creation.
- [ ] Custom user metadata sync functions still populate profile tables.

---

## Bug Report Format

If any QA step fails, file a bug report containing:
```markdown
### Bug Description
[Clear description of what failed]

### Repro Steps
1. Navigate to ...
2. Click ...
3. Observe ...

### Environment
- Local / Staging
- User role tested: [Anonymous / Authenticated User / Tenant Admin / System Admin]

### Logs / Error Messages
- Database log error (if any):
- Console error log (if any):

### Security Severity
- Critical (Data leak, RLS bypass)
- Major (Auth failure, upload failure)
- Minor (UI alignment, slow responses)
```

---

## Release Readiness Checklist

- [ ] All automated migration and lint checks passed.
- [ ] RLS validation matrix completed with 100% passes.
- [ ] Service key isolation verified.
- [ ] Signed URL lifespans validated.
- [ ] Human owner has signed off on the release plan.

---

## Related Files

- [12-qa-test-plan.md](../../../core/docs/12-qa-test-plan.md) — Core QA guidelines.
- [rls-policy-guidelines.md](rls-policy-guidelines.md) — Row Level Security details.
- [edge-functions-guidelines.md](edge-functions-guidelines.md) — Serverless logic rules.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created Supabase QA Checklist | Antigravity |
