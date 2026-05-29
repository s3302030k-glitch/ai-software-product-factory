# How to Use Prompts

> A guide to using the AI agent role prompts in this system.

---

## What Are These Prompts?

Each file in this `prompts/` folder defines a specific AI agent role. When you need a particular type of work done — product management, architecture, coding, QA — you use the corresponding prompt to configure an AI model to act as that specialist.

These prompts are **model-agnostic**. They work with any capable AI:
- Claude (Anthropic)
- GPT-4 / GPT-4o (OpenAI)
- Gemini (Google)
- Any AI coding assistant (Cursor, Copilot, Windsurf, etc.)

---

## How to Use a Prompt

### Step 1: Start the Session

Always begin by pasting the **Session Starter Prompt** (`00-session-starter-prompt.md`). This orients the AI to the project and its rules.

### Step 2: Choose a Role

Select the prompt that matches the work you need done:

| Prompt | Role | Use When You Need... |
|--------|------|---------------------|
| `01` | Product Manager | Product brief review, feature prioritization |
| `02` | Business Analyst | Discovery, requirements, user personas |
| `03` | UX/UI Designer | Page layouts, user flows, design decisions |
| `04` | Software Architect | Technology choices, project structure |
| `05` | Database Engineer | Data model, schema, relationships |
| `06` | Security Engineer | Auth, authorization, data protection |
| `07` | Tech Lead | Roadmap, phases, batch planning |
| `08` | Coding Agent | Implementation of approved batches |
| `09` | QA Agent | Testing, validation, bug finding |
| `10` | Review Agent | Code review, compliance checking |
| `11` | Bugfix Agent | Diagnosing and fixing reported bugs |
| `12` | Refactor Agent | Code improvement without behavior change |

### Step 3: Provide Required Inputs

Each prompt lists its **Required Inputs**. Provide these by:
- Pasting document contents directly
- Pointing to file paths in your project
- Providing specific context the prompt asks for

### Step 4: Let the Agent Work

The agent will work within its defined scope and guardrails. It should:
- Read the required documents
- Perform its responsibilities
- Produce output in the specified format
- Stop when it hits a stop condition

### Step 5: Review and Iterate

Review the agent's output. If changes are needed:
- Provide specific feedback
- Reference the relevant documents
- Ask the agent to revise

---

## Prompt Workflow Order

For a new project, use prompts roughly in this order:

```
1. Product Manager      → Define the product
2. Business Analyst     → Research and validate
3. Product Manager      → Finalize MVP scope
4. UX/UI Designer       → Design user flows and pages
5. Software Architect   → Define architecture
6. Database Engineer    → Design data model
7. Security Engineer    → Define security model
8. Tech Lead            → Create roadmap and batches
9. Coding Agent         → Implement batches (repeat)
10. QA Agent            → Test each batch (repeat)
11. Review Agent        → Review each batch (repeat)
12. Bugfix Agent        → Fix issues (as needed)
13. Refactor Agent      → Improve code (as needed)
```

---

## Tips for Effective Use

1. **One role per session** — Don't mix roles. Start a new session for each role.
2. **Provide full documents** — Don't summarize. Paste complete docs so the agent has full context.
3. **Trust the guardrails** — If the agent says "this is out of scope," that's the system working correctly.
4. **Use stop conditions** — If the agent stops and asks, answer before it continues.
5. **Keep docs updated** — The prompts are only as good as the documents they reference.
6. **Session starter first** — Always start with the session starter prompt, even if the AI "remembers" from before.

---

## Common Mistakes

| Mistake | Why It's a Problem | Fix |
|---------|-------------------|-----|
| Skipping the session starter | Agent lacks project context | Always paste it first |
| Combining roles | Agent scope becomes unclear | One role per session |
| Not providing required reading | Agent guesses instead of knowing | Paste all required docs |
| Ignoring stop conditions | Agent may go off track | Address stops before continuing |
| Changing scope mid-session | Scope creep | Start a new batch request |
| Not reviewing output | Bad output compounds | Review every output |
