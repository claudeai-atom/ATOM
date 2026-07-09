# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this directory is

`C:\ATOM claude` is the working directory for the ATOM product project (a modular sports
operating system). It is not a software codebase — no source code, build system, or tests.
It contains:

- `context/` — the ATOM product knowledge base. `context/atom-context.md` is the canonical,
  continuously-updated architectural source of truth (product vision, hierarchy, onboarding,
  project-joining flow, modules, dependencies, business rules, roadmap, glossary). Read this
  file first for any ATOM work. `context/backlog.md` is the not-yet-phased feature/change
  backlog (kept in sync with `atom-context.md` §14 Roadmap). `context/atom-context(1).md` is the
  original baseline input (superseded, kept for reference); `context/instructions_new.md` is the
  one-time generation instructions that produced `atom-context.md`.
- `.claude/agents/atom-product-manager.md` — a project-scoped subagent that owns ATOM product
  ideation, change-request review, feasibility checks, full-flow auditing, and phase/backlog
  management. Prefer it over ad-hoc analysis for those tasks.
- `research/` — ATOM-related research notes and findings.
- `ideation/` — ATOM product ideation, brainstorming, and exploratory proposals.
- `memory/` — Claude Code's persistent auto-memory store (`MEMORY.md` index plus individual memory files). This is where cross-conversation context about the user, feedback, and project facts is kept. See the memory instructions in the global CLAUDE.md for how to read/write these.
- `graphify-out/` — cached output from a previous `/graphify` run over this directory (AST/detect/extract/semantic caches).

There are no build, lint, or test commands here. All ATOM tasks/research/ideation should be
organized under `context/`, `research/`, or `ideation/` respectively, rather than the root.
If asked to work on a different project, ask the user for that project's directory rather than
assuming it's this one.
