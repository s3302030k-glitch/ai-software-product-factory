# 08 — Architecture

> Defines the technology stack, project structure, and architectural principles.

---

## Purpose

Provide a clear, enforceable architecture reference that all agents must follow. This document ensures consistency across the codebase and prevents ad-hoc technology decisions.

## Status

`Draft` | `In Review` | `Approved` | `Locked`

---

## Technology Stack

| Layer | Technology | Version | Notes |
|-------|-----------|---------|-------|
| **Frontend Framework** | _e.g., Next.js / React / Vue_ | _e.g., 14.x_ | |
| **CSS / Styling** | _e.g., Tailwind CSS / CSS Modules_ | _e.g., 3.x_ | |
| **State Management** | _e.g., Zustand / Redux / Context API_ | | |
| **Backend** | _e.g., Node.js / Supabase / Django_ | | |
| **Database** | _e.g., PostgreSQL / Supabase_ | | |
| **Authentication** | _e.g., Supabase Auth / NextAuth / Clerk_ | | |
| **Hosting** | _e.g., Vercel / AWS / Railway_ | | |
| **File Storage** | _e.g., S3 / Supabase Storage_ | | |
| **Email** | _e.g., Resend / SendGrid / Postmark_ | | |
| **Payments** | _e.g., Stripe / Paddle_ | | _If applicable_ |
| **Monitoring** | _e.g., Sentry / LogRocket_ | | |
| **CI/CD** | _e.g., GitHub Actions / Vercel_ | | |

---

## Project Structure

_Define the directory layout for the codebase._

```
project-root/
├── src/
│   ├── app/                  # Routes / pages
│   │   ├── (auth)/           # Auth-related pages
│   │   ├── (dashboard)/      # Dashboard pages
│   │   └── api/              # API routes
│   ├── components/           # Reusable UI components
│   │   ├── ui/               # Base components (Button, Input, etc.)
│   │   ├── layout/           # Layout components (Sidebar, Header)
│   │   └── features/         # Feature-specific components
│   ├── lib/                  # Utility functions, clients, helpers
│   ├── hooks/                # Custom React hooks
│   ├── types/                # TypeScript type definitions
│   ├── styles/               # Global styles
│   └── constants/            # App-wide constants
├── public/                   # Static assets
├── prisma/ or supabase/      # Database schema / migrations
├── tests/                    # Test files
└── docs/                     # Project documentation (from factory)
```

_Adapt this structure to your chosen framework._

---

## Frontend Principles

1. **Component-based architecture** — Build small, reusable components
2. **Separation of concerns** — UI components should not contain business logic
3. **Type safety** — Use TypeScript for all frontend code (if applicable)
4. **Responsive design** — Mobile-first, works on all screen sizes
5. **Accessibility** — Follow WCAG 2.1 AA guidelines
6. **Loading states** — Every data-dependent view must have loading, empty, and error states
7. **No hardcoded strings** — Use constants or i18n keys for user-facing text
8. **Form validation** — Client-side validation mirrors server-side rules

---

## Backend Principles

1. **API-first** — Frontend communicates with backend exclusively through defined APIs
2. **Validation at the boundary** — All inputs are validated at the API layer
3. **Business logic in services** — Controllers/handlers are thin; logic lives in service functions
4. **Error handling** — Consistent error response format across all endpoints
5. **Logging** — Structured logging for all operations (no `console.log` in production)
6. **No direct database access from frontend** — All queries go through the API/service layer
7. **Idempotent operations** — Where possible, API operations should be safe to retry

---

## Database Principles

1. **Migrations for all changes** — No manual schema modifications
2. **Naming conventions** — `snake_case` for tables and columns
3. **Soft deletes by default** — Use `deleted_at` unless explicitly decided otherwise
4. **Timestamps on all tables** — `created_at`, `updated_at` on every table
5. **Foreign key constraints** — Enforce referential integrity at the database level
6. **Indexes for query performance** — Index foreign keys and frequently filtered columns
7. **No business logic in database** — Avoid stored procedures and triggers unless architecturally justified

---

## Authentication Principles

1. **Use established auth libraries** — Do not implement custom auth
2. **Session management** — Use secure, httpOnly cookies or JWT with refresh tokens
3. **Password policies** — Enforce minimum complexity requirements
4. **Rate limiting** — Protect auth endpoints against brute force
5. **Secure by default** — All routes require authentication unless explicitly public

---

## Integration Principles

1. **Third-party services** — Wrap in adapter/client classes for testability and replaceability
2. **API keys** — Store in environment variables, never in code
3. **Webhooks** — Validate signatures, process idempotently
4. **External API calls** — Implement retry logic with exponential backoff
5. **Feature flags** — Use feature flags for gradual rollout of integrations

---

## Scalability Notes

_Document any scalability considerations for the current architecture._

| Concern | Current Approach | Scale Trigger | Future Approach |
|---------|-----------------|---------------|-----------------|
| _Database load_ | _Single DB instance_ | _> 10k concurrent users_ | _Read replicas_ |
| _File storage_ | _Local / single bucket_ | _> 1TB_ | _CDN + tiered storage_ |
| _Background jobs_ | _Inline processing_ | _> 5s processing time_ | _Queue-based workers_ |
| _API rate limits_ | _None / basic_ | _Public API launch_ | _Redis-based rate limiting_ |

---

## Scope

- This document defines **technology choices and structural conventions**.
- All coding agents must follow the principles and structure defined here.

## Out of Scope

- Feature implementation details
- Deployment procedures (see `13-release-checklist.md`)
- Data model specifics (see `07-data-model.md`)

## Guardrails

- [ ] No new dependencies may be added without architectural justification
- [ ] Project structure must follow the defined layout
- [ ] AI agents must not introduce new architectural patterns without approval
- [ ] All technology choices must be documented in this file before use

## Related Files

- `07-data-model.md` — Database schema that implements these principles
- `09-api-design.md` — API contracts that follow these principles
- `10-security-model.md` — Security requirements that architecture must support
- `11-development-roadmap.md` — Delivery plan built on this architecture

## Change Log

| Date | Change | Author |
|------|--------|--------|
| _YYYY-MM-DD_ | Initial creation from template | _Name_ |
