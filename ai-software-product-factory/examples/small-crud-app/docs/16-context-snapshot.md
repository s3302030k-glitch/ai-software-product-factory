# 16 — Context Snapshot

> An orientation summary of the current project state. **This is NOT the source of truth.**

---

## ⚠️ Important

This document exists to help AI agents quickly understand the current state of the project. It is a **convenience summary** that is derived from other documents.

**Rules:**
- This document **must never override** any other document.
- If this snapshot conflicts with a core doc, the core doc is correct.
- Read this first for orientation, then read specific documents for authoritative detail.
- See `00-document-priority.md` for the full priority hierarchy.

---

## Product Summary

| Attribute | Value |
|-----------|-------|
| **Product Name** | Invoice Tracker |
| **Product Type** | Web Application (Next.js SPA / App Router) |
| **Target Users** | Owner, Staff |
| **Core Purpose** | A simple utility to track clients, multi-item invoices, manual payments, and dynamic outstanding status. |
| **Status** | `Planning` / Setup |

---

## Current Phase

| Attribute | Value |
|-----------|-------|
| **Phase** | Phase 1: Project Foundation and Auth |
| **Phase Status** | `Not Started` |
| **Started** | 2026-05-29 |
| **Estimated Completion** | 2026-06-05 |

---

## Last Completed Batch

| Attribute | Value |
|-----------|-------|
| **Batch ID** | P0-B1 |
| **Title** | Setup Documentation and Project Specs |
| **Completed On** | 2026-05-29 |
| **Review Status** | `Accepted` |
| **Key Changes** | Generated 18 Invoice Tracker core specification docs under `/docs`. |

---

## Next Batch

| Attribute | Value |
|-----------|-------|
| **Batch ID** | P1-B1 |
| **Title** | Framework Initialization & Layout |
| **Objective** | Initialize Next.js app, configure global vanilla CSS variables, and layout routing. |
| **Status** | `Ready` |
| **Blocked By** | Nothing |

---

## Tech Stack Summary

| Layer | Technology |
|-------|-----------|
| Frontend | Next.js 14 (App Router) |
| Styling | Vanilla CSS modules |
| Backend | Next.js API Routes (Node.js) |
| Database | PostgreSQL with Prisma ORM |
| Auth | NextAuth.js (Email/Password session cookies) |
| Hosting | Vercel (frontend/serverless) & Railway (Postgres) |

---

## Active Guardrails

- **Single-Tenant Shared Scoping:** No multi-tenant partitions; Owner and Staff access the same organization tables.
- **Manual Log Constraint:** No Stripe or bank synchronizations (manual recording only).
- **Single-Currency Validation:** All financial records fixed strictly to USD ($).
- **Dynamic Status Derivation:** Status must not be written statically (must compute dynamically at DB/query layer).

---

## Open Issues

None.

---

## Recent Decisions

| ID | Decision | Date | Impact |
|----|----------|------|--------|
| DEC-001 | Use Small MVP Scope | 2026-05-29 | High |
| DEC-002 | Fixed Single Currency (USD) for MVP | 2026-05-29 | Medium |
| DEC-003 | Payments Are Manually Recorded | 2026-05-29 | Medium |
| DEC-004 | Invoice Status Is Dynamically Derived | 2026-05-29 | High |
| DEC-005 | PDF Generation and Email Sending Out of Scope | 2026-05-29 | High |

---

## Known Technical Debt

None (initial project setup stage).

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-29 | Initial creation for Invoice Tracker | Product Owner |
