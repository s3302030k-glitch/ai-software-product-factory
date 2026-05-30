# 08 — Architecture

> Conceptual architecture for the Integrated Operations ERP.
>
> **Example:** Integrated Operations ERP — Completed & Fully Filled Documentation Reference.
> This is not a runnable application. No source code, no package files, no build configs.
> See the [example README](../README.md) for full context.

> [!WARNING]
> This document defines a **documentation-only conceptual architecture**. No runnable code, framework, or deployment configuration is part of this documentation reference.

---

## Architecture Philosophy

This ERP system is designed as a modular, multi-layer web application with a clear separation of concerns between:
- **Presentation layer** (frontend UI, pages, forms)
- **Application layer** (business logic, approval workflows, stock movement rules)
- **Data layer** (persistent storage, RLS, immutable audit log)
- **Integration boundary** (external system placeholders — not implemented)

All stock, approval, and financial business logic resides in the **application layer**, not in frontend views. The frontend is for display and input only.

---

## Conceptual Layer Diagram

```
┌─────────────────────────────────────────────────────────┐
│                  Frontend Surfaces                       │
│   (Web App: Dashboard, Pages, Forms, Reports, Audit)     │
└───────────────────────┬─────────────────────────────────┘
                        │ HTTP / REST (conceptual)
┌───────────────────────▼─────────────────────────────────┐
│               Application / API Layer                    │
│   (Auth, Role Enforcement, Business Logic, Approval       │
│    Workflow Engine, Stock Movement Rules, Report Engine)   │
└───────────────────────┬─────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────┐
│               Data / Storage Layer                       │
│   (Database: Entities, RLS Concept, Audit Log,           │
│    File Storage: PDF/CSV placeholders)                   │
└───────────────────────┬─────────────────────────────────┘
                        │
┌───────────────────────▼─────────────────────────────────┐
│            External Integration Boundary                 │
│   (Supplier systems, logistics carriers, payment          │
│    providers — all OUT OF SCOPE for this reference)      │
└─────────────────────────────────────────────────────────┘
```

---

## Frontend Surfaces

**Type:** Web application (SPA or MPA — to be decided in implementation phase)

**Key surfaces:**
- Sign In page
- Dashboard (role-scoped widgets)
- Operational pages (22 pages — see [06-pages-spec.md](06-pages-spec.md))
- Approval Inbox
- Reports and export interface
- Audit Log viewer
- Settings / Admin panel

**RTL/i18n Readiness:**
- Text direction attribute applied at the app root level.
- All UI strings use translation key references — no hardcoded strings.
- Layout uses CSS logical properties for RTL mirror readiness.
- Full translation implementation is out of MVP scope — see [22-rtl-i18n-notes.md](22-rtl-i18n-notes.md).

**Mobile Readiness:**
- Web application is responsive for tablet/desktop.
- Native mobile app is **not implemented** in this documentation reference.
- Optional future mobile warehouse flows are documented in [23-mobile-app-notes.md](23-mobile-app-notes.md).

---

## Backend / API Surface

**Type:** Server-side API (REST conceptual — framework TBD in implementation phase)

**Responsibilities:**
- Authentication and session management
- Role-based authorization enforcement
- Operational scoping enforcement (department/warehouse filters)
- Business logic: stock movement creation, balance derivation, approval routing
- Approval workflow engine: routing, escalation, self-approval blocking
- Report generation and export
- Audit event recording

**Business Logic Placement Rule:**
All stock quantity rules, approval routing logic, and financial placeholder calculations must be implemented in the **application layer** — never in frontend components or database triggers alone.

---

## Database / Storage Concept

**Type:** Relational database (conceptual — specific engine TBD in implementation phase)

**Key concepts:**
- All entities defined in [07-data-model.md](07-data-model.md)
- Row Level Security (RLS) concept applied per operational scope (see [21-supabase-notes.md](21-supabase-notes.md) for Supabase-specific RLS notes)
- Immutable records: StockMovement and AuditEvent are append-only
- Soft delete: Supplier, Customer, Product, SKU, Warehouse, Zone use `status` / `is_archived` flags
- File storage: PDF and CSV export files stored in a scoped storage bucket (concept only — no real bucket config)

---

## Auth Concept

**Type:** Session-based or JWT token-based authentication (specific mechanism TBD in implementation phase)

**Key rules:**
- All API endpoints require a valid authenticated session.
- User identity is verified server-side on every request.
- Client-side role checks are for UI convenience only — never for security enforcement.
- Sensitive session tokens are not stored in unsecured client storage.
- See [21-supabase-notes.md](21-supabase-notes.md) for Supabase Auth conceptual notes.
- See [10-security-model.md](10-security-model.md) for full auth/authorization rules.

---

## Operational Authorization Concept

- Every request is checked against the user's role and operational scope.
- Role is determined by the user's RoleAssignment records.
- Operational scope (department, warehouse) is enforced by filtering all queries to the user's assigned scope.
- Approval authority is a separate permission from data-entry authority — a user cannot approve their own submissions.

---

## Inventory / Stock Consistency Concept

- **StockBalance is derived** — never directly written. All changes flow through StockMovement records.
- StockMovement records are created atomically with the business event (receiving, adjustment, dispatch).
- Negative stock balances are allowed to be flagged as warnings but do not block all operations — documented behavior for resolution.
- Adjustments require a separate approval before a StockMovement is created.
- See [18-erp-operations-notes.md](18-erp-operations-notes.md) for full ERP Operations Pack rules.

---

## Approval Workflow Concept

- All approval-gated actions (purchase request, stock adjustment) create an `ApprovalRequest` record on submission.
- ApprovalRequest is routed to the designated approver for the user's department/warehouse scope.
- Self-approval is blocked at the API level — not just at the UI level.
- Escalation is triggered when a request value exceeds the approver's authorization limit.
- All approval decisions are recorded as immutable AuditEvents.

---

## Reporting / Export Concept

- Reports are generated server-side from the authoritative data layer.
- UI display values and exported values must match — no client-side recalculation.
- Export formats: CSV (tabular data) and PDF placeholder (layout spec — implementation TBD).
- Report generation events are logged as AuditEvents.
- See [20-print-reporting-notes.md](20-print-reporting-notes.md) for full Print & Reporting Pack notes.

---

## PDF Placeholder Concept

- Purchase Order PDF and Invoice PDF are documented as placeholder layouts.
- No real PDF generation library is configured in this documentation reference.
- Layout specs are defined conceptually for future implementation.
- PDFs must clearly show: document type, placeholder reference number, party names (placeholder), line items, total placeholder, and a "DOCUMENTATION REFERENCE ONLY" watermark note.
- See [20-print-reporting-notes.md](20-print-reporting-notes.md) for details.

---

## Mobile Warehouse Future Concept

- Mobile warehouse flows (receiving, stock count) are documented as **optional future scope**.
- No mobile app framework, no React Native, no Expo, no Flutter setup in this reference.
- If implemented in future, the [Mobile App Pack](../../../extensions/mobile-app-pack/README.md) guidelines apply.
- See [23-mobile-app-notes.md](23-mobile-app-notes.md) for future scope notes.

---

## Integration Boundaries

| Integration | Status | Notes |
|-------------|--------|-------|
| Supplier systems | Out of scope | Placeholder records only |
| Logistics carriers | Out of scope | Dispatch placeholder only |
| Payment providers | Out of scope | Payment placeholder only |
| Bank APIs | Out of scope | No bank connection of any kind |
| Tax engine | Out of scope | No tax calculation |
| Barcode scanners | Out of scope | No hardware integration |
| Email / SMTP | Out of scope | Notification placeholder only |
| Mobile push | Out of scope | Future scope only |

---

## Architecture Decisions

See [14-decision-log.md](14-decision-log.md) for formal records of key architectural decisions.

---

## Related Files

- [07-data-model.md](07-data-model.md) — Entity definitions
- [09-api-design.md](09-api-design.md) — API group definitions
- [10-security-model.md](10-security-model.md) — Auth and authorization model
- [21-supabase-notes.md](21-supabase-notes.md) — Supabase-specific architecture notes
- [14-decision-log.md](14-decision-log.md) — Architecture decisions
