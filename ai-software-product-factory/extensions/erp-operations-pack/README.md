# Extension Pack: ERP & Operations

> Adds enterprise resource planning patterns, operational workflows, inventory management, and multi-module coordination documentation.

---

## When to Use This Pack

Use this extension pack when your product:

- Is an **ERP system** or has ERP-like modules (HR, inventory, procurement, manufacturing)
- Has **complex operational workflows** (approval chains, status machines, multi-step processes)
- Manages **inventory** (stock levels, warehouses, transfers, lot tracking)
- Requires **multi-module data flow** (e.g., sales order → inventory → shipping → invoicing)
- Has **role-based workflows** where different users handle different stages
- Needs **operational dashboards** with KPIs and metrics

---

## What This Pack Will Add (When Built)

### Additional Documents

| Document | Purpose |
|----------|---------|
| `erp-module-map.md` | Module definitions, boundaries, and inter-module data contracts |
| `workflow-engine-spec.md` | Status machines, approval chains, transition rules, notifications |
| `inventory-management-spec.md` | Stock tracking, warehouses, transfers, reservations, lot/serial tracking |
| `operational-dashboard-spec.md` | KPI definitions, calculation methods, refresh intervals |
| `inter-module-contracts.md` | How modules communicate, event-driven patterns, data consistency |
| `approval-workflow-spec.md` | Multi-level approval chains, escalation rules, delegation |

### Additional Prompts

| Prompt | Purpose |
|--------|---------|
| `erp-architect-prompt.md` | AI agent role for designing ERP modules and workflows |
| `workflow-engineer-prompt.md` | AI agent role for implementing status machines and approval chains |

### Additional Guardrails

- Module boundaries must be respected — no cross-module database queries
- Workflows must follow defined state machines — no ad-hoc status changes
- Inventory transactions must be atomic — no partial updates
- Inter-module communication must use defined contracts
- Dashboard calculations must match the underlying data exactly
- Approval workflows must enforce authorization at every step

---

## Risks This Pack Helps Manage

| Risk | How This Pack Helps |
|------|-------------------|
| Modules become tightly coupled | Module map and inter-module contracts |
| Workflow states get corrupted | State machine spec with valid transitions only |
| Inventory counts become inaccurate | Atomic transaction rules for inventory |
| Dashboard shows wrong metrics | KPI calculation spec with validation |
| Approval bypass | Authorization enforcement at every workflow step |
| Data inconsistency across modules | Event-driven patterns with consistency rules |

---

## Example Project Types

- Manufacturing ERP systems
- Warehouse management systems
- Human resource management systems (HRMS)
- Procurement and supply chain platforms
- Healthcare clinic management systems
- School/university administration systems
- Fleet management and logistics platforms

---

## Status

`Placeholder` — This extension pack contains only this README. Full content will be added in a future version.
