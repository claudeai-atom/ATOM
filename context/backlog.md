# ATOM Backlog

Not-yet-phased feature/change proposals for ATOM. Once something is
formally committed to a phase, reflect it in `atom-context.md` §14
Roadmap as well — this file and that section should stay in sync.

Maintained by the `atom-product-manager` subagent (and anyone else
working on ATOM). Append new entries below the template; don't
overwrite prior ones.

---

## Template

### [YYYY-MM-DD] Item title

**Problem:** what gap or opportunity this addresses

**Proposal:** what it would concretely involve

**Related context sections:** (e.g. §7.7 Project Joining Flow)

**Feasibility:** Feasible now / blocked on X / later-phase

**Status:** Proposed / Under review / Backlog / Committed to Phase N / Rejected

**Notes:**

---

### [2026-07-09] Define role-scoping level for RBAC roles (Project vs Industry template)

**Problem:** The Roles screen is nested under a single Project ("11th Asian
Aquatics Championship") with no scope control, yet 7.7.2 says a Role "maps to
permissions on the ATOM Project" (Project-scoped) while seed data ("Viewer —
Read-only access across the industry") implies Industry-wide scope. Unresolved:
are roles authored per-Project (heavy duplication across every Event Project) or
authored once at Industry level and inherited/cloned into Projects?

**Proposal:** Decide and document a role-scoping model. Recommended: Industry-level
role library/templates inherited by Projects, with optional per-Project override;
surface the scope explicitly on the list and Add Role screens.

**Related context sections:** 3.1, 7.7.2, 10, 13 (naming: "industry" misuse)

**Feasibility:** Feasible now (architectural decision, no module prerequisite)

**Status:** Proposed

**Notes:** The "Clone" row action implies duplication pain the current model doesn't
solve. Also fix "across the industry" copy — wrong scope or a 13 naming violation.

---

### [2026-07-09] RBAC matrix must cover 8.2 product modules and be dynamic to enabled modules

**Problem:** The Add Role matrix rows are platform-admin capabilities (Functional
Area Management, Project Configuration, Task Management, Project Role Management,
Institution Management, Individual Management), NOT the 8.2 Project Modules (Athlete
Management, Roster, Training, Grievance, Accreditation, Certification, Payments,
etc.). A role cannot be scoped to "manage Athletes but not Accreditation." Since
modules are the core value and the Industry billing unit (9), RBAC that can't express
module-level access is a structural gap. The list is also static.

**Proposal:** Drive matrix rows from modules enabled for the Industry/Project (8, 9).
Reconcile admin-capability rows vs product-module rows. Define what "Full Access to all
modules" (Super Admin seed desc) binds to.

**Related context sections:** 8, 8.2, 8.4, 9, 10

**Feasibility:** Feasible now for the model; per-module granularity lands as AMS/TMS
integrate (8.4).

**Status:** Proposed

**Notes:**

---

### [2026-07-09] Delete Role safety: reassignment flow + immutable system roles

**Problem:** Delete is exposed in the row menu with no confirmation modal and no
reassignment path, on roles with up to 60 users assigned (Viewer). Deleting orphans
every assigned user's permissions. Seed roles (Super Admin etc.) appear deletable/
editable — deleting or nerfing Super Admin can lock an Industry out of its own Project.

**Proposal:** Block deletion of roles with assigned users until reassignment; confirm
modal showing impact count; mark seed/system roles immutable and distinguish them from
custom roles.

**Related context sections:** 10 (audit), 12, 14 (Phase 1 Audit)

**Feasibility:** Feasible now

**Status:** Proposed

**Notes:**

---

### [2026-07-09] "Assign User" must respect the Project join/approval gate

**Problem:** The Roles list exposes "Assign User" independent of the 7.7 apply/invite
flow. If it can grant a Project role to a user who never applied, was invited, or was
approved, it bypasses 7.7 join mechanics and the 11 dependency that participation
resolves through an Industry decision.

**Proposal:** Constrain "Assign User" to already-joined/approved participants (re-assign
a Role, never admit a new participant). Document its relationship to invite-time Role
(7.7.2).

**Related context sections:** 7.7, 7.7.2, 11

**Feasibility:** Feasible now

**Status:** Proposed

**Notes:**

---

### [2026-07-09] Granular permissions depth for the "More permissions" pattern

**Problem:** The "More permissions" popover exposes a single checkbox ("Assign
Functional Area Owner"). ATOM modules need finer granularity than View/Create/Edit/
Delete + one extra — Grievance (escalation, TAT), Accreditation (zone/venue access),
Certification (bulk generation, DigiLocker verify), Payments/Expenses (approve vs
initiate). A singleton cannot express these, and burying the high-privilege "Assign
Functional Area Owner" (tied to 7.7.3 / 11 FA assignment) under-weights it.

**Proposal:** Per-module granular permission sets in an expandable panel, not a one-item
popover; promote "Assign Functional Area Owner" to a first-class sensitive permission.

**Related context sections:** 7.7.3, 8.3, 10, 11

**Feasibility:** Feasible now for the framework; module-specific actions land per
integration (8.4).

**Status:** Proposed

**Notes:**

---

### [2026-07-14] Basic Budget Tracking v1 — Industry → Project → Functional Area allocation

**Problem:** Industry has no way to plan/track its **own internal operating budget**
for running Projects. (This is distinct from ATOM's B2B billing of the Industry, §9 —
it is the Industry's own program money, not what it pays ATOM.)

**Proposal:** A lightweight, single-currency, **allocation-only** tracker following the
existing hierarchy (§3.1) one level at a time. "FA" here = **Functional Area** (§15),
NOT "Field Agent". **v1 tracks allocation only — no spend logging** (spend = v2, see
next entry). Folds into ATOM **base platform**, not a separate assignable module.

Five pages:
1. **Industry Overall Budget** — Industry enters `total_budget` (+ single currency,
   optional label). One record per Industry.
2. **Overall Budget Dashboard (Industry)** — Total · Allocated (Σ Project allocations) ·
   Unallocated. Per-Project row: allocated, % of total. Covers Default + all Event
   Projects (§7.1). No spend columns in v1.
3. **Project Budget Allocation** — Industry allocates a slice of overall budget to a
   Project.
4. **Project → Functional Area Budget Allocation** — Industry allocates a Project's
   budget down to its configured FAs. v1 stops at FA (no Sub-FA/Task budgets).
5. **Project Budget Dashboard** — Project allocation · Allocated to FAs (Σ) ·
   Unallocated. Per-FA row: allocated, %. No spend columns in v1.

**Business rules (finalized):**
- **Over-allocation = hard block.** Σ Project allocations ≤ Industry total; Σ FA
  allocations ≤ Project allocation. This is the only guardrail needed in an
  allocation-only v1 (no over-spend state can exist without spend).
- Editing Industry total below the amount already allocated to Projects = hard block.
- Editing a Project's allocation below the amount already allocated to its FAs =
  hard block (same rule, one level down).
- Allocation is a **parent-sets-child** act: Industry sets Project and FA allocations;
  the FA-owning Institution does not set its own allocation.
- **Currency:** single, Industry-level. No multi-currency.

**Access control:** New RBAC permissions (platform-admin capability rows, e.g.
`Budget: View` / `Budget: Manage`) — folded into base platform, so NOT a §8.2 module
matrix row. Dashboards/budget functionality **may be visible to Institution/Individual
per the RBAC permission granted**. Constraint: this must NOT flatten RBAC/PBAC/ABAC
(§10, §16) — RBAC grants the *capability*, while Project isolation and Functional Area
isolation (§10) still scope *which* budgets a viewer sees (an Institution sees its own
FA slice, not siblings). Caution: don't hand budget-view to participant User Types
(e.g. Athletes) via a Project Role by default (§7.7.2).

**Related context sections:** §3.1, §7.1, §7.4, §8.1 (base platform target), §9
(revenue-neutral; not a billing SKU), §10, §11, §14 (new Phase 1.5), §15 (FA =
Functional Area)

**Feasibility:** Feasible now. No AMS/TMS dependency; all prerequisites (Industry,
Project, FA config, RBAC/PBAC/ABAC, Audit) are Phase 1.

**Status:** Committed to Phase 1.5 (per user decision 2026-07-14)

**Notes:** Folds into ATOM core (not a separate module) — user decision 2026-07-14.
Persisting to atom-context.md (§8.1, §9, §10, §14 Phase 1.5, §15) is deferred until
the user confirms satisfaction with the flow.

---

### [2026-07-14] Budget spend tracking — v2 flow (deferred from v1)

**Problem:** v1 tracks allocation only. To know how much of an allocated budget is
actually consumed, ATOM needs a spend-logging layer. Explicitly deferred to v2 per
user decision 2026-07-14.

**Proposal (v2 flow outline, to be detailed later):**
- Record spend against a **Functional Area** (`fa_id`, amount, date, note, source);
  roll spend up to Project and Industry.
- Dashboards (v1 Pages 2 & 5) gain **Spent** and **Remaining** columns plus an
  **over-budget flag** (spent > allocated is allowed + flagged; real money can't be
  blocked).
- **Decisions to make in v2:** (a) spend source — manual entry vs auto-derive from the
  Expenses/Payments modules (§8.2/§8.3), noting auto-derive introduces a §11 module
  dependency; (b) who logs spend — Industry-side vs the FA-owning Institution (adds an
  Institution-workspace surface + self-reported-spend trust boundary); (c) confirm
  over-spend = allow + flag.

**Related context sections:** §8.2/§8.3 (Expenses, Payments), §10, §11, §14

**Feasibility:** Later phase (v2). Design flow now optional; not building in Phase 1.5.

**Status:** Proposed (v2)

**Notes:** Keep separate from the v1 allocation tracker so v1 scope stays clean.
