# 10C — Storage Guidelines

> Defines bucket designs, public vs. private access configurations, folder path conventions, file metadata modeling, and storage security policies.

---

## Purpose

This document provides conventions and security standards for utilizing Supabase Storage. It outlines how files should be organized, validated, and protected. It supplements the core security model in [10-security-model.md](../../../core/docs/10-security-model.md).

## Status

`Active` — Mandatory reference for file uploads and media access controls design.

---

## Bucket Design

File assets must be organized into logical buckets. Do not mix unrelated files with different security scopes in a single bucket. Example buckets:
- `avatars`: Publicly readable user profile images.
- `invoices`: Highly secure, private financial PDF documents.
- `reports`: Confidential analytical reports scoped to specific teams.

---

## Public vs. Private Buckets

- **Public Buckets**: Files are publicly accessible via a permanent URL without token authentication.
  - *Constraint*: Public buckets **must** be explicitly justified in the architecture design. Only use for non-sensitive public assets (e.g., product images, avatars).
- **Private Buckets**: Files require authentication and authorization to access. Direct URLs return 401/403 errors. Access is granted via temporary signed URLs or verified client sessions.

---

## Path Naming Conventions

Files inside a bucket must be scoped using a logical path structure:
- **User scope**: `{bucket_name}/{user_id}/{filename}` (e.g., `avatars/usr_12345/profile.png`).
- **Tenant/Team scope**: `{bucket_name}/{tenant_id}/{entity_id}/{filename}` (e.g., `invoices/org_98765/inv_abcde/invoice.pdf`).

Using directory separators in filenames establishes a virtual folder hierarchy inside Supabase Storage.

---

## File Ownership and Metadata Modeling

- Storage files represent application resources. The relationship between the application data and the storage objects must be modeled in Postgres.
- Do not store business metadata (like description, category, size validations) purely on the client side. Model them in a public schema table (e.g., `public.attachments` linked to storage paths).
- Check that deletion of a database record triggers a deletion of the corresponding file in storage (using edge functions or database triggers executing storage API cleanups).

---

## Upload Validation

Client-side uploads should be validated before invoking the Supabase client:
1. **File Type Verification**: Restrict MIME types (e.g., only allow `image/jpeg` or `application/pdf`).
2. **File Size Limits**: Enforce maximum limits (e.g., max 5MB for avatars) to prevent denial-of-service / excessive storage charges.

*Note: These constraints must also be configured in the Supabase Storage settings dashboard.*

---

## Download / Access Rules

- Public assets are fetched using `supabase.storage.from(bucket).getPublicUrl(path)`.
- Private assets are retrieved via `supabase.storage.from(bucket).createSignedUrl(path, expiresInSeconds)`.
- The signed URL lifespan should be kept short (e.g., 60 to 300 seconds).

---

## Storage RLS Policy Checklist

Storage permissions are enforced by Postgres RLS policies operating on the `storage.buckets` and `storage.objects` tables.

- [ ] Confirm RLS is enabled on `storage.buckets` and `storage.objects`.
- [ ] Verify that SELECT policies on private buckets restrict reads to authorized owners:
  ```sql
  -- Example SELECT policy for owner-scoped private files:
  create policy "Users can view their own uploads"
  on storage.objects
  for select
  to authenticated
  using (
    bucket_id = 'private-documents'
    and (storage.foldername(name))[1] = auth.uid()::text
  );
  ```
- [ ] Verify that INSERT policies enforce path ownership (preventing User A from uploading to User B's folder).
- [ ] Verify that UPDATE policies prevent users from overwriting or changing metadata of other users' files.
- [ ] Verify that DELETE policies restrict deletes to owners or administrators.

---

## Signed URL Notes

- Signed URLs provide temporary read access via a query parameter token.
- Secure servers must generate signed URLs using the `service_role` key only *after* validating that the requesting user's session is authorized to view that specific file path.
- **NEVER** expose the backend code generating signed URLs to anonymous API calls.

---

## Common Mistakes

- **Leaving Buckets Public by Default**: Storing confidential files (like tax records, resumes) in a public bucket.
- **Using broad storage RLS**: Writing policies like `using (auth.uid() is not null)` which allows *any* logged-in user in the system to view, update, or delete files uploaded by anyone else.
- **Leaking Signed URLs**: Logging signed URLs to public analytics trackers or server logs.
- **Failing to clean up orphans**: Deleting rows from the database but leaving the binary files in Supabase Storage indefinitely.

---

## Stop Conditions

> [!CAUTION]
> Stop and report to the human owner if:
> 1. You are asked to mark a bucket containing sensitive user or financial data as "Public".
> 2. You detect storage RLS policies that allow any authenticated user to write files into paths outside their own user or tenant folders.
> 3. You are asked to implement file upload/download endpoints that bypass token authorization entirely.

---

## Related Files

- [10-security-model.md](../../../core/docs/10-security-model.md) — Core security model.
- [rls-policy-guidelines.md](rls-policy-guidelines.md) — Row Level Security details.
- [supabase-architecture.md](supabase-architecture.md) — Systems architecture boundaries.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created storage guidelines | Antigravity |
