# 08 — Architecture

> Defines the technology stack, project structure, and architectural principles for the Invoice Tracker.

---

## Purpose

Provide a clear, enforceable architecture reference that all agents must follow. This document ensures consistency across the codebase and prevents ad-hoc technology decisions.

## Status

`Approved`

---

## Technology Stack

| Layer | Technology | Version | Notes |
|-------|-----------|---------|-------|
| **Frontend Framework** | Next.js (App Router, React) | 14.x | App router handles routing and pages |
| **CSS / Styling** | Vanilla CSS | — | Modular CSS or global layout files |
| **State Management** | React Context & Hooks | — | Simple app-wide states |
| **Backend** | Next.js API Routes | 14.x | Standard Node.js handlers |
| **Database ORM** | Prisma / PostgreSQL | 5.x / 15.x | RDBMS for relational integrity |
| **Authentication** | NextAuth.js or Iron Session | 4.x | Session validation (email/password) |
| **Hosting** | Vercel / Railway / Render | — | Serverless frontend, hosted Postgres |

---

## Project Structure

Standard single-repository layout:

```
invoice-tracker/
├── prisma/
│   ├── schema.prisma         # Database schema
│   └── migrations/           # Database migration files
├── src/
│   ├── app/                  # Next.js Pages & API routes
│   │   ├── (auth)/login      # Login view
│   │   ├── (dashboard)/      # Dashboard, Clients, Invoices pages
│   │   ├── api/              # API route handlers (/api/clients, etc.)
│   │   ├── layout.js         # Navigation wrappers
│   │   └── page.js           # Root redirect to login/dashboard
│   ├── components/           # UI elements
│   │   ├── ui/               # Buttons, Inputs, Dialogs
│   │   ├── layout/           # Sidebar, Navbar
│   │   └── forms/            # ClientForm, InvoiceForm, PaymentModal
│   ├── lib/                  # Utilities (db client, session configs)
│   ├── types/                # TypeScript/JSDoc types
│   └── styles/               # CSS modules and root styles
├── tests/                    # Unit and integration tests
├── docs/                     # Product Factory documentation files
├── package.json
└── README.md
```

---

## Frontend Principles

1. **Component Reuse:** Place generic UI elements (Buttons, Input textfields, dropdown select boxes, Modals) under `components/ui` to maintain design consistency.
2. **State Scoping:** Keep state local to page components. Use simple React context for globally shared details (e.g. current authenticated session).
3. **No External CSS Frameworks:** Stick to Vanilla CSS. Use native CSS variables for theme declarations (colors, border-radii, spacing units).
4. **Validation Synchronicity:** Run form validations inline on field blur/change using simple JavaScript, mirroring backend rules.

---

## Backend/API Principles

1. **Next.js Server Actions / Handlers:** Use route handlers (e.g., `/api/clients/route.js`) to process AJAX calls from the client.
2. **Boundary Validation:** Always validate incoming payloads at the route boundary before processing. Return standard 400 Bad Request with field validation errors.
3. **Session Enforcement:** Verify user auth session at the top of every authenticated API handler.
4. **No UI in backend:** Backend must return clean JSON data payloads only.

---

## Database Principles

1. **Relational Constraints:** Foreign Keys must be declared and enforced in PostgreSQL (e.g. deleting a client with invoices is restricted).
2. **Schema Control:** All changes must go through Prisma migrations (`prisma migrate dev`). Never run manual SQL schema alterations.
3. **Timestamps:** Every table must contain `created_at` and `updated_at` columns automatically set.
4. **No Soft Deletes:** For this small MVP, we will use database hard deletes (restricted by relations) to avoid query complexity.

---

## Integration Principles

1. **No External Integrations:** Online payments (Stripe/PayPal), external email templates, and automated tax systems are strictly out of scope.
2. **Local Environment Variables:** DB connection strings and session secret keys must be read from `.env` files, never committed to VCS.

---

## Scalability Notes

Due to the small, single-tenant target scale, no complex scaling features are implemented.

| Concern | Current Approach | Scale Trigger | Future Approach |
|---------|-----------------|---------------|-----------------|
| Database load | Single DB instance | > 100 concurrent clients | Read replica |
| File Storage | Not applicable | Image uploads needed | Cloud storage (S3) |

---

## Scope

- This document defines **technology choices and structural conventions**.
- All coding agents must follow the principles and structure defined here.

## Out of Scope

- Architectural setup of external payment integrations.
- Multi-tenancy tenant-isolation layers.

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial version for Invoice Tracker | Product Owner |
