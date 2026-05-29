# Extension Pack: Supabase

> Adds Supabase-specific architecture patterns, Row Level Security (RLS), Edge Functions, and storage documentation.

---

## When to Use This Pack

Use this extension pack when your product:

- Uses **Supabase** as the backend platform
- Relies on **Supabase Auth** for authentication
- Uses **Row Level Security (RLS)** for data access control
- Implements server-side logic with **Supabase Edge Functions**
- Uses **Supabase Storage** for file uploads
- Leverages **Supabase Realtime** for live updates

---

## What This Pack Will Add (When Built)

### Additional Documents

| Document | Purpose |
|----------|---------|
| `supabase-architecture.md` | Supabase-specific project structure, client setup, environment config |
| `rls-policy-spec.md` | Row Level Security policies for every table, with test cases |
| `edge-functions-spec.md` | Edge Function definitions, triggers, input/output contracts |
| `supabase-storage-spec.md` | Bucket structure, access policies, file type restrictions |
| `supabase-auth-config.md` | Auth providers, email templates, redirect URLs, session config |
| `supabase-migration-guide.md` | Migration workflow, seed data, local development setup |
| `supabase-realtime-spec.md` | Realtime subscriptions, channels, presence features |

### Additional Prompts

| Prompt | Purpose |
|--------|---------|
| `supabase-engineer-prompt.md` | AI agent role specialized in Supabase patterns and best practices |

### Additional Guardrails

- Every table must have RLS policies — no exceptions
- RLS policies must be tested for every role
- Edge Functions must validate all inputs
- Service role key must never be exposed to the client
- Storage buckets must have access policies
- Migrations must use Supabase CLI format
- Local development must use Supabase CLI with local containers

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| Missing RLS exposes all data | Mandatory RLS spec for every table |
| Service key exposed in client code | Explicit rules about key usage |
| Edge Function errors are silent | Input validation and error handling spec |
| Storage bucket open to public | Storage access policy spec |
| Migration conflicts | Migration workflow and naming conventions |

---

## Example Project Types

- Any web application using Supabase as backend
- Serverless applications with Supabase
- Real-time collaborative applications
- Rapid MVP development projects
- JAMstack applications with Supabase backend

---

## Status

> **Status: Placeholder / Planned Future Pack**
>
> This extension pack is currently a **placeholder**. The folder contains only this README. Full templates, prompts, and instructions will be added in a future version.
>
> **Core Governance Rule:** Extension packs are optional and exist to **supplement** core documents for specific product needs — they do **not** replace core documents.
>
> For workspace setup instructions and core rules, link back to [START_HERE.md](../../START_HERE.md).
