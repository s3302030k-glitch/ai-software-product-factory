# 08B — Edge Functions Guidelines

> Outlines implementation rules, security procedures, input validation, and operational patterns for serverless Edge Functions.

---

## Purpose

This document provides conventions, guidelines, and security requirements for writing and deploying Supabase Edge Functions. It supplements the core technology architecture in [08-architecture.md](../../../core/docs/08-architecture.md).

## Status

`Active` — Mandatory reference for edge function development and reviews.

---

## When to Use Edge Functions

Edge Functions are serverless TypeScript endpoints running on Deno. Use them for:
- Integrations with third-party payment gateways (e.g., Stripe webhooks).
- Operations requiring secret API keys that cannot be exposed to the client.
- Orchestrating complex multi-step database mutations that are too heavy for Postgres RPCs.
- Custom authentication flows or generating secure PDF certificates.

---

## When NOT to Use Edge Functions

- Do **not** use Edge Functions for standard CRUD database queries. Direct client queries through PostgREST (enforced by RLS) are faster and more cost-effective.
- Do **not** use them as a simple relay to bypass database RLS policies.
- Do **not** use them for long-running batch computing or processing large files (Deno deploy has execution timeouts and memory limits).

---

## Authorization Requirements

Edge Functions are exposed to the public internet by default. Every client-facing function must check the caller's identity:

1. **Verify the Auth Header**: Read the `Authorization` header containing the user's JWT.
2. **Initialize Client with JWT**: Pass the JWT token to the Supabase JS client inside the function to ensure the database query respects that user's RLS policies.
3. **Handle Unauthorized Access**: Return an HTTP `401 Unauthorized` response immediately if the JWT is missing or invalid.

```typescript
// Authentication check example:
import { createClient } from 'https://esm.sh/@supabase/supabase-js@2'

Deno.serve(async (req) => {
  const authHeader = req.headers.get('Authorization')
  if (!authHeader) {
    return new Response(JSON.stringify({ error: 'Missing auth header' }), { status: 401 })
  }

  const supabaseClient = createClient(
    Deno.env.get('SUPABASE_URL') ?? '',
    Deno.env.get('SUPABASE_ANON_KEY') ?? '',
    { global: { headers: { Authorization: authHeader } } }
  )

  // Get user profile (authenticates JWT)
  const { data: { user }, error } = await supabaseClient.auth.getUser()
  if (error || !user) {
    return new Response(JSON.stringify({ error: 'Invalid token' }), { status: 401 })
  }

  // User is authenticated. Enforce permissions...
})
```

---

## Service Role Usage Rules

> [!WARNING]
> Initializing the database client inside an Edge Function with the `SUPABASE_SERVICE_ROLE_KEY` bypasses RLS rules.
>
> 1. Use the `service_role` client only when performing system tasks (like creating user logs, system notifications, or Stripe billing synchronization).
> 2. Document any use of the `service_role` key in the code comments, explaining why RLS bypass is required.
> 3. Verify user privileges manually before performing any write operations with the `service_role` client.

---

## Input Validation

- Trust no client input. Parse and validate the incoming request body (using libraries like `zod` or manual property checks).
- Reject payloads containing unexpected parameters, incorrect types, or invalid formats with `400 Bad Request`.

---

## Error Handling

- Wrap database operations and third-party API calls in `try...catch` blocks.
- Ensure that detailed internal database error messages or raw system logs are **never** returned directly to the client. Send user-friendly error messages (e.g., "Transaction failed. Please try again.") and log the specific technical details internally.
- Set appropriate HTTP status codes (e.g., `400` for validation issues, `401` for auth issues, `403` for permission issues, `500` for server failures).

---

## Secrets Management

- **NEVER** hardcode credentials, API keys, or database URLs in the function source code.
- Retrieve configuration keys from environment variables using `Deno.env.get('MY_SECRET_KEY')`.
- Secrets must be set on the Supabase dashboard or via the CLI (`supabase secrets set KEY=VALUE`).
- **NEVER log secrets, tokens, or raw credentials to the console logs.**

---

## Logging Rules

- Log significant execution milestones, external API latencies, and request counts.
- Redact PII (Personal Identifiable Information) and authorization tokens from all console output logs.

---

## CORS Notes

- Handle preflight requests (`OPTIONS` method) by returning appropriate headers:
  - `Access-Control-Allow-Origin` (set to specific origins or validated request origin).
  - `Access-Control-Allow-Headers` (include `Authorization`, `Content-Type`, `apikey`).
  - `Access-Control-Allow-Methods` (e.g., `POST, GET, OPTIONS`).

---

## Testing Checklist

Before deploying:
- [ ] Run the Edge Function locally using `supabase start` and `supabase functions serve`.
- [ ] Send request payloads with missing `Authorization` headers; verify rejection (401).
- [ ] Send request payloads with invalid JWT tokens; verify rejection (401).
- [ ] Send request payloads containing invalid JSON properties; verify rejection (400).
- [ ] Test the function under typical timeout constraints to ensure performance is acceptable.

---

## Common Mistakes

- **Bypassing RLS by Default**: Initializing the Supabase database client with `service_role` for simple operations.
- **Leaking Secrets**: Accidentally committing `.env` configuration files or logging variables to the Deno dashboard console.
- **Missing CORS Handlers**: Forgetting to process `OPTIONS` preflight requests, causing client-side browser errors.
- **Broad CORS allowance**: Setting `Access-Control-Allow-Origin: *` for functions that process sensitive operations.

---

## Stop Conditions

> [!CAUTION]
> Stop and report to the human owner if:
> 1. You are asked to write an Edge Function that accepts sensitive database mutations from users without checking their authentication token.
> 2. You find a function exposing external API secrets in cleartext within the repository code.
> 3. An Edge Function requires `service_role` usage to bypass RLS, but the request does not perform manual authorization verification.

---

## Related Files

- [08-architecture.md](../../../core/docs/08-architecture.md) — Core technology architecture definitions.
- [supabase-architecture.md](supabase-architecture.md) — System boundaries.
- [rls-policy-guidelines.md](rls-policy-guidelines.md) — Database RLS guidelines.

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Created edge functions guidelines | Antigravity |
