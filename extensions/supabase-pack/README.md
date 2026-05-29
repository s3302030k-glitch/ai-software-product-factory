# Extension Pack: Supabase

> Adds Supabase-specific architecture patterns, Row Level Security (RLS), Edge Functions, database migrations, and storage documentation.

This is a fully implemented extension pack, supplementing the core factory templates.

---

## Purpose and Scope

This pack **supplements** the core factory documents; it does **not** replace them. It is designed for software products that use Supabase for database management, authentication, RLS, file storage, and/or edge functions.

This pack includes reusable document templates and role prompts. It is strictly template-based and does not contain product-specific details, private business information, real credentials, real project IDs, or real migrations.

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| **Unsafe RLS policies** | Establishes strict principles for Row Level Security design and reviews. |
| **Security definer exposure** | Audits database functions to prevent privilege escalation. |
| **Over-broad service role usage** | RESTRICTS service role key usage to secure backend environments. |
| **Unreviewed migrations** | Provides structured checklists for database schema changes. |
| **Storage bucket policy mistakes** | Differentiates public/private buckets and maps access paths. |
| **Edge function authorization mistakes** | Mandates token verification and permission checks in serverless endpoints. |
| **Client-side trust mistakes** | Establishes the rule that client filters are not security barriers. |

---

## Pack Components

### Documentation Guidelines (`docs/`)

- [Supabase Architecture](docs/supabase-architecture.md) — Tech stack mapping, boundaries, and client-server access model rules.
- [Row Level Security (RLS) Guidelines](docs/rls-policy-guidelines.md) — RLS policy structure, roles, schemas, and security definer safety.
- [Database Migration Guidelines](docs/database-migration-guidelines.md) — Safe schema modification guidelines, naming, rollback strategies, and approval gates.
- [Auth and Session Guidelines](docs/auth-and-session-guidelines.md) — User identification, profile patterns, permission boundaries, and server-side verification.
- [Storage Guidelines](docs/storage-guidelines.md) — Bucket definitions, public/private scoping, path conventions, and file upload/access policies.
- [Edge Functions Guidelines](docs/edge-functions-guidelines.md) — Serverless logic scoping, authentication validation, error logging, and secrets management.
- [Supabase QA Checklist](docs/supabase-qa-checklist.md) — Comprehensive pre-release QA matrix covering migrations, auth, RLS, and functions.

### AI Agent Role Prompts (`prompts/`)

- [Supabase Architect](prompts/supabase-architect-prompt.md) — Role for high-level architecture design and boundary definitions.
- [RLS Policy Review Agent](prompts/rls-policy-review-agent-prompt.md) — Auditor for verifying the safety of proposed database RLS rules.
- [Supabase Migration Review Agent](prompts/migration-review-agent-prompt.md) — Auditor for validating migration SQL safety and backward compatibility.
- [Edge Function Review Agent](prompts/edge-function-review-agent-prompt.md) — Auditor for verifying security and error handling in Deno edge functions.

---

## Recommended Usage

Follow these steps to integrate this extension pack into your product project:

1. **Initialize Core Kit:** Copy the core factory documents (`core/docs/`) and prompt templates (`core/prompts/`) into your product project first.
2. **Apply Supabase Pack:** Copy the contents of this folder (`docs/` and `prompts/`) into your project *only* if Supabase is part of the system architecture.
3. **Integrate Documentation:** Merge the guidelines from `docs/` into your active product documentation.
4. **Use Prompt Templates:** Assign the specialized Supabase prompts to your AI agents to guide architectural design, RLS creation, and code reviews before implementation.
5. **Enforce Governance:** Never apply migrations or modify RLS policies directly in production without human owner approval.

For workspace setup instructions and core governance rules, link back to [START_HERE.md](../../START_HERE.md).
