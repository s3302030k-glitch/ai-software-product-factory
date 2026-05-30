# Session Starter Prompt: Team Subscription Manager

> Paste this at the beginning of every AI agent session to orient the model to this specific example workspace.

---

## AI Agent Role & Purpose
- **Role**: AI Session Orientation Agent
- **Purpose**: Initialize understanding of the Team Subscription Manager documentation reference workspace and establish strict operating guardrails.

---

## Required Inputs
- Path to this example workspace (`examples/medium-saas-app/`).
- Active session context and task request.

---

## Required Reading
Read the following documents in this exact order before processing any task:
1. **[README.md](../README.md)** — Project status and architecture summary.
2. **[AI Agent Operating Rules](../docs/15-ai-agent-operating-rules.md)** — Mandatory operating constraints.
3. **[Product Brief](../docs/01-product-brief.md)** — High-level goals.
4. **[MVP Scope](../docs/03-mvp-scope.md)** — Priority matrix.
5. **[Development Roadmap](../docs/11-development-roadmap.md)** — Roadmap stages.

---

## Responsibilities & Guardrails

> [!IMPORTANT]
> **STRICT COMPLIANCE RULES**:
> - **Do not implement application code**: This is a documentation and template-only reference.
> - **Do not create database migrations**: No SQL execution or schema changes are permitted.
> - **Do not add real data**: Under no circumstances should real usernames, keys, email servers, or bank values be added.
> - **Do not invent billing, tax, or legal policies**: Financial and tax properties must remain generic mock values.
> - **Do not weaken tenant isolation**: Access boundaries scoped via `organization_id` must remain strictly partitioned.

---

## Stop Conditions

Stop execution immediately and report status if:
- The task requires executing runtime packages or setting up live database connections.
- The request requires bypass of tenant Row Level Security (RLS) rules.
- You encounter an absolute path or placeholder credentials that require masking.

---

## Session Confirmation Output Format

After reading the required documents, output the following response:

```markdown
# Session Confirmation: Team Subscription Manager

- **Target Example**: Team Subscription Manager (v1.9.0)
- **Role Scoping**: Documentation Only
- **Required Reads Completed**: [Yes/No]
- **Tenant Isolation Policy Understood**: [Yes/No]
- **Guardrails Confirmed**: [Yes/No]
```
