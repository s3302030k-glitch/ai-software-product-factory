# Contributing to the AI Software Product Factory

> Guidelines for safely modifying, extending, and maintaining this repository.

---

## Purpose

This document explains how the repository owner and future contributors should add, modify, or update the AI Software Product Factory. It preserves the repository's documentation-only boundary and ensures that all changes are consistent, traceable, and safe.

If you are using the factory to build a product (rather than to improve the factory itself), see [HOW_TO_USE_THIS_FACTORY.md](HOW_TO_USE_THIS_FACTORY.md) instead.

---

## Contribution Principles

1. **Documentation only.** This repository contains no application source code, runtime files, or dependencies. All contributions must be Markdown files.
2. **Templates over specifics.** Core templates must remain generic and reusable across all products, not tailored to any one project.
3. **Supplements, not replacements.** Extension packs add domain-specific guidance; they never replace or override core templates.
4. **Privacy by default.** No real customer data, business data, credentials, or private information may appear anywhere in this repository.
5. **Relative links only.** All internal links must be relative. No absolute file paths, `file:///` links, or local machine paths.
6. **Human approval for governance changes.** Changes to `core/docs/00-document-priority.md` or `core/docs/15-ai-agent-operating-rules.md` require explicit owner review and a decision log entry.
7. **Traceable changes.** Significant structural changes should be noted in the relevant `Change Log` sections and in `RELEASE_NOTES.md` at release time.

---

## What Belongs in This Repository

| Category | Examples |
|----------|---------|
| Core document templates | `core/docs/01-product-brief.md`, `core/docs/07-data-model.md` |
| Core role prompt templates | `core/prompts/08-coding-agent-prompt.md` |
| Extension pack docs and prompts | `extensions/supabase-pack/docs/rls-policy-guidelines.md` |
| Reference example documentation | `examples/small-crud-app/docs/01-product-brief.md` |
| Root orientation and status docs | `README.md`, `START_HERE.md`, `FACTORY_STATUS.md`, `RELEASE_NOTES.md` |
| Root guide files | `HOW_TO_USE_THIS_FACTORY.md`, `GLOSSARY.md`, `CONTRIBUTING.md`, `TROUBLESHOOTING.md` |

---

## What Does Not Belong in This Repository

| Category | Examples | Why |
|----------|---------|-----|
| Application source code | `.ts`, `.js`, `.py`, `.go` files | This is a documentation kit, not an application |
| Runtime configuration | `package.json`, `tsconfig.json`, `.env`, `Dockerfile` | No runtime setup belongs here |
| Database migrations | SQL files, Prisma migration files | Migrations belong in product workspaces |
| Dependency lockfiles | `package-lock.json`, `yarn.lock` | No dependencies are installed here |
| Real business data | Customer records, invoices, payment tokens | Privacy boundary |
| Real credentials | API keys, passwords, tokens, secrets | Security boundary |
| Local machine paths | `C:/Users/...`, `d:/aitemp/...`, `/mnt/data/...` | Machine-specific paths break portability |
| Product-specific filled docs | A filled `01-product-brief.md` for a real product | Products have their own workspaces |

> [!CAUTION]
> Never commit runtime files, credentials, or real data to this repository. These violate the non-runnable boundary and the privacy rules that make the factory safe for public use.

---

## Documentation-Only Boundary

The factory repository must remain 100% Markdown. Before committing any change, verify:

```bash
# All files must have .md extension
Get-ChildItem -Recurse -File | Where-Object { $_.Extension -ne '.md' }
# This command must return no results
```

If a non-Markdown file appears, it must not be committed.

---

## How to Add a New Core Template

A new core template belongs in `core/docs/` when it applies to **all products** built with the factory, regardless of domain.

**Steps:**

1. Identify the next available number in the `core/docs/` sequence (currently `00`–`17`).
2. Create the file as `core/docs/NN-descriptive-name.md`.
3. Follow the structure of existing templates:
   - `# NN — Title`
   - `> One-line description`
   - `## Purpose`
   - `## Status` (with standard status options)
   - Template content sections
   - `## Scope`
   - `## Out of Scope`
   - `## Guardrails`
   - `## Related Files`
   - `## Change Log`
4. Add the new file to `README.md`'s folder structure diagram.
5. Update `core/docs/00-document-priority.md` to include the new document in the authority table if it carries governance authority.
6. Update `core/prompts/00-how-to-use-prompts.md` if a new corresponding role prompt is also added.
7. Update `FACTORY_STATUS.md` and `RELEASE_NOTES.md` at release time.

> [!IMPORTANT]
> Changes to `00-document-priority.md` require explicit owner approval and a `14-decision-log.md` entry in any product using the factory.

---

## How to Add a New Extension Pack

An extension pack belongs in `extensions/` when it addresses a specific product domain (not universal) and supplements the core templates.

**Steps:**

1. Create a new folder: `extensions/my-new-pack/`.
2. Add `README.md` with:
   - Title: `# Extension Pack: [Name]`
   - One-line tagline
   - `## Purpose and Scope` (what products need this pack)
   - `## Risks This Pack Helps Manage` (table: Risk | How This Pack Helps)
   - `## Pack Components` (listing `docs/` files and `prompts/` files)
   - `## Recommended Usage` (5-step integration guide)
   - Link back to `[START_HERE.md](../../START_HERE.md)`
3. Create `docs/` subfolder with domain-specific guideline documents.
4. Create `prompts/` subfolder with domain-specific role prompts.
5. Each prompt must follow the standard structure: Role Definition, Required Inputs, Required Reading, Responsibilities, Output Format, Guardrails, Stop Conditions.
6. Add appropriate legal/compliance disclaimers if the pack touches finance, health, legal, or regulated domains.
7. Add the pack to:
   - `README.md` — extension pack table
   - `HOW_TO_USE_THIS_FACTORY.md` — available extension packs table
   - `FACTORY_STATUS.md` — version matrix
   - `RELEASE_NOTES.md` — at release time
8. Tag the release after validation.

**Privacy rules for extension packs:**
- No real customer data, real project IDs, real credentials, or real business information.
- No product-specific logic. Keep all guidelines generic.

---

## How to Add a New Reference Example

A reference example belongs in `examples/` when it demonstrates the factory applied to a realistic but entirely fictional product.

**Steps:**

1. Create a new folder: `examples/my-product-name/`.
2. Add `README.md` with:
   - Status: `Completed & Fully Filled Documentation Reference`
   - Non-runnable disclaimer (see existing examples)
   - What the product is (fictional)
   - Which extension packs are demonstrated
   - Links to all docs and prompts in the example
   - Recommended reading order
   - Privacy and data guardrail warning
3. Create `docs/` subfolder with all core templates filled in for the fictional product.
4. Optionally create extension pack integration notes (e.g., `18-pack-notes.md`).
5. Optionally create `prompts/` subfolder with product-specific session-starter and review prompts.
6. Ensure every document has a clear header stating it is a documentation reference only.
7. Run a text search confirming no real data, credentials, or machine paths are present.
8. Add the example to `README.md`, `FACTORY_STATUS.md`, and `RELEASE_NOTES.md`.

**Privacy rules for examples:**
- All product names, user names, companies, addresses, amounts, and identifiers must be entirely fictional.
- Do not base examples on real client work, real projects, or real business situations.
- Examples must include explicit `[!WARNING]` privacy disclaimers.

---

## How to Update Root and Status Docs

Root-level files (`README.md`, `START_HERE.md`, `FACTORY_STATUS.md`, `RELEASE_NOTES.md`, `HOW_TO_USE_THIS_FACTORY.md`) are the primary user-facing navigation layer.

**Rules:**
- Only update these files to reflect actual repository changes.
- Do not add content that does not correspond to an existing file or feature.
- Keep links relative and verify they point to real files.
- Do not restructure unrelated sections when making targeted updates.
- `FACTORY_STATUS.md` must reflect the actual state of each pack and example.

---

## How to Handle Release Tags

Tags mark stable, verified states of the repository.

**Tag naming:** Semantic versioning — `v1.0.0`, `v1.1.0`, `v2.0.0`, etc.

**Tag rules:**
- Use annotated tags: `git tag -a vX.Y.Z -m "Release vX.Y.Z: [short description]"`
- Never delete or move existing tags.
- Tags must only be applied after validation passes (see Required Checks below).
- Minor version bumps (`v1.1.0` → `v1.2.0`) for new extension packs or reference examples.
- Major version bumps (`v1.x.x` → `v2.0.0`) for significant structural changes or full system milestones.
- Patch version bumps (`v1.1.0` → `v1.1.1`) for small corrections, link fixes, or typo corrections.
- After tagging, push tags: `git push origin vX.Y.Z` or `git push origin --tags`.

---

## Required Checks Before Opening or Accepting a Change

Before committing or merging any change, verify all of the following:

### Non-Runnable Boundary Check
- [ ] No `.js`, `.ts`, `.py`, `.sql`, `.json`, `.yaml`, `.toml`, `.lock`, or other non-Markdown files added
- [ ] No `package.json`, `Dockerfile`, migration files, or framework config files

### Privacy and Security Check
- [ ] No real customer names, emails, addresses, or identifiers
- [ ] No real payment data, bank data, tax IDs, or financial records
- [ ] No real credentials, API keys, tokens, or passwords
- [ ] No local machine paths (`C:/Users/`, `d:/aitemp/`, `/mnt/data/`, etc.)
- [ ] No `file:///` absolute links

### Link Hygiene Check
- [ ] All internal links are relative
- [ ] All linked files actually exist in the repository
- [ ] No broken or dangling links

### Structural Check
- [ ] New core templates follow the standard section structure
- [ ] New extension packs have a compliant `README.md`
- [ ] New examples have non-runnable disclaimers and privacy warnings
- [ ] Root navigation files updated if new files were added

### Git Hygiene Check
- [ ] `git status --short` shows only intended changes
- [ ] `git diff --check` passes (no whitespace errors)
- [ ] Commit message follows the conventional format (see below)

---

## Privacy and Data Safety Rules

| Rule | Details |
|------|---------|
| No real data | All names, amounts, IDs, emails, and addresses must be fictional |
| No credentials | No API keys, tokens, passwords, or secrets of any kind |
| No machine paths | No local or CI/CD machine-specific paths |
| No real business logic | Templates must remain generic, not product-specific |
| No real migrations | No SQL or ORM migration files belong in this repository |
| Placeholder only | Use `_e.g., My Product_`, `UUID-placeholder`, `_YYYY-MM-DD_` patterns |

---

## Style Guidelines

- **Markdown only.** No HTML blocks, no inline scripts.
- **Relative links.** Always use relative paths (e.g., `[README](README.md)`, not absolute URLs or `file:///` paths).
- **Table format.** Use standard Markdown tables with pipe characters.
- **Heading hierarchy.** Each file has one `# H1` title. Use `##`, `###` for subsections.
- **Guardrails as checkboxes.** List guardrail items as `- [ ] Rule` in the Guardrails section.
- **Change log at the end.** Every template and major file should have a `## Change Log` table.
- **Consistent status labels.** Use `Draft`, `In Progress`, `Active`, `Complete`, `Template`, `Governance` for Status fields.
- **GitHub Alerts for emphasis.** Use `> [!NOTE]`, `> [!WARNING]`, `> [!IMPORTANT]`, `> [!CAUTION]`, `> [!TIP]` for critical callouts, not ad-hoc bold text.

---

## Commit Message Guidelines

All commits must follow conventional commit format:

```
<type>(<scope>): <short description>

[optional body]
```

**Types:**

| Type | When to use |
|------|------------|
| `docs` | Adding or updating documentation files |
| `fix` | Fixing a broken link, typo, or incorrect content |
| `refactor` | Restructuring content without changing meaning |
| `chore` | Updating metadata files (FACTORY_STATUS, RELEASE_NOTES) |

**Examples:**

```
docs: add financial-business-logic extension pack
docs(examples): add complex-erp-app completed documentation reference
fix: correct broken link in supabase-pack README
chore: update factory status for v1.5.0
docs: add v2.1.0 public usability polish guides
```

**Rules:**
- Keep the subject line under 72 characters.
- Use lowercase for type and scope.
- Do not end the subject line with a period.
- Reference the relevant version or pack in the subject when applicable.

---

## Review Checklist

Before accepting any contribution, confirm:

- [ ] Non-runnable boundary respected (no source code or runtime files)
- [ ] Privacy rules followed (no real data, credentials, or machine paths)
- [ ] Link hygiene passed (relative links only, no broken links)
- [ ] Style guidelines followed (Markdown, tables, headings)
- [ ] Commit message follows conventional format
- [ ] Root navigation files updated (if new files added)
- [ ] FACTORY_STATUS.md updated (if a new pack or example was added)
- [ ] RELEASE_NOTES.md updated (at release time)
- [ ] Release tag applied correctly (annotated, pushed)

---

## Change Log

| Date | Change | Author |
|------|--------|--------|
| 2026-05-30 | Initial creation as part of v2.1.0 public usability polish | Factory |
