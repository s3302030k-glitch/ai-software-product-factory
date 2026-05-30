# 15 — AI Agent Operating Rules: Team Subscription Manager

> **Status: Completed & Fully Filled Documentation Reference**

This document specifies the mandatory operating rules, boundary constraints, and verification formats for any AI agent interacting with the Team Subscription Manager example.

---

## Operating Guardrails

### 1. No Code Execution or File Creation
- AI agents are prohibited from generating application source code, initializing frameworks, running package installers (`npm`, `pip`), or creating database migrations.
- This is a documentation-only environment. All work must be restricted to markdown files and prompt templates.

### 2. Privacy & Data Boundaries
- Do not append real customer records, billing accounts, credit card tokens, secret keys, password values, or tax identifiers.
- Always use the generic placeholder values defined in this documentation (e.g., "Acme Corp").

### 3. Policy & Compliance Restrictions
- Do not invent billing formulas, tax calculation policies, or local legal disclaimers. 
- All pricing and invoice calculations in documentation must use simple, generic placeholders and positive integers representing cents.

### 4. Tenant Isolation Safeguard
- Never propose changes that bypass database Row Level Security (RLS) or merge organization roles with system-level roles (e.g., merging a Platform Support Admin with an Organization Owner).
- Always maintain explicit tenant scoping via `organization_id` on all entities.

---

## Stop Conditions

An AI agent MUST immediately stop work, report status to the Human Product Owner, and wait for instructions if any of the following occur:
- An instruction requests the creation of SQL migrations or database connections.
- A task asks for the integration of live payment gateways (e.g. Stripe checkout scripts).
- A conflict arises between a requested change and the tenant isolation security rules.
- A path references a local machine path or absolute URL.

---

## Required Implementation Report Format

At the end of any work cycle, the agent must output a final report using the following structure:

```markdown
# Implementation Report

## 1. Summary of Changes
- Detailed list of files created or updated.

## 2. Guardrails Confirmed
- [ ] Confirmed no runnable code was created.
- [ ] Confirmed no real credentials or data were added.
- [ ] Confirmed tenant isolation remained intact.

## 3. Link Audit Results
- Statement verifying that all links use relative paths and resolve successfully.
```

---

## Related Files

- [01-product-brief.md](01-product-brief.md) — Core constraints reference.
- [10-security-model.md](10-security-model.md) — Security boundaries.
