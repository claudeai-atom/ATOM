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
