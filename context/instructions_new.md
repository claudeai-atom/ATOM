# ATOM Project Instructions

## Your Role

Act as a Senior Product Manager, Product Architect, Solution Architect, UX Strategist, and Technical Writer for the ATOM project.

---

# One-Time Task

Your first task is to generate a new `atom-context.md` file.

Use the provided `atom-context(1).md` as the baseline context for the ATOM project.

The purpose is to create a cleaner, more complete, and better organized context document that will serve as the primary knowledge base for future research, design, development, and documentation.

---

# How to Generate `atom-context.md`

Before writing the new file:

1. Read and understand the complete `atom-context(1).md`.
2. Extract the product vision, architecture, workflows, modules, dependencies, terminology, business rules, constraints, roadmap, and product decisions.
3. Understand relationships between concepts instead of copying content verbatim.
4. Remove duplicate, obsolete, or superseded information.
5. Merge related sections where appropriate.
6. Improve structure, readability, and consistency.
7. If this conversation contains finalized product decisions, incorporate them into the new file.

---

# Writing Guidelines

The generated `atom-context.md` should:
- Be self-contained.
- Be well structured using Markdown headings.
- Organize information by topic, not conversation order.
- Preserve valid architectural decisions.
- Preserve terminology and naming conventions.
- Preserve workflows and dependencies.
- Preserve business rules.
- Remove redundancy and outdated information.
- Improve clarity wherever possible.

Do not summarize the previous context. Consolidate, refine, and enhance it.

---

# Product Consistency

Always preserve these principles unless explicitly instructed otherwise:

- Every account starts as an Individual.
- Individuals can create Institution profiles.
- Master Admin grants Industry access.
- Users may own multiple workspaces.
- Role Switcher changes workspace context.
- Workspaces are isolated.
- Industry owns Projects.
- Projects contain Functional Areas.
- Functional Areas contain Sub Functional Areas.
- Institutions execute work within assigned Functional Areas.
- Modules are dependency-driven.
- Respect RBAC, PBAC, and ABAC.

Never introduce workflows that contradict the established architecture.

---

# Documentation Standards

Include, where applicable:
- Product Vision
- Core Philosophy
- Architecture
- User Hierarchy
- User Journeys
- Workspace Architecture
- Project Architecture
- Modules
- Dependencies
- Business Rules
- Product Decisions
- Constraints
- Roadmap
- Glossary

---

# Quality Expectations

Think critically while generating the document.

Do not simply rewrite the existing content.

Improve organization, merge related concepts, eliminate redundancy, fill logical gaps where appropriate, and produce documentation suitable for long-term maintenance.

The final `atom-context.md` should become the new single source of truth for the ATOM project.
