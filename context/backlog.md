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

---

### [2026-07-17] Project Progress Tracking v1 — backend-computed Task completion %

**Problem:** A Project has no at-a-glance completion signal. The Industry (and
participants) can't tell how far along a Project is without inspecting Task Boards.

**Proposal:** A backend-computed, read-only percentage per Project — never manually
set:
`Progress % = (Completed Tasks / Total Tasks) × 100`, counting **Task Board Tasks**
(§3.1; some UI copy says "Tickets" — Task is canonical per §13). Folds into the base
platform (like Budget Tracking), not a §8.2 module.

**Rules (finalized 2026-07-17):**
- **Authoritative Task status set:** To Do · In Progress · Reopen · Completed ·
  Cancelled. Progress is computed from a Task's *current* status.
  - Completed Tasks = count in status **Completed**.
  - Total Tasks = all Tasks **except Cancelled** (= To Do + In Progress + Reopen +
    Completed).
  - **Reopen counts toward Total, not Completed** — an open bucket; moving
    Completed → Reopen drops the %. Falls out of "count by current status."
- New Project = **0%** (zero Tasks; `0/0` defined as 0%, never NaN/100%).
- **Rounded to nearest integer, no decimals.**
- Only Tasks counted (no sub-task entity in the hierarchy).
- Computed **flat** across all of a Project's Tasks (all FAs), not rolled up from FA.

**Surfaces (4):** (1) Project card on the Industry Projects list; (2) Project Overview
page; (3) Task Board page; (4) Industry Overview (Home) "ongoing projects" section —
one tracker per project.

**Supersedes:** the retired Phase 1 §7.5 FA-based model (`Completed FAs / Total FAs`).

**Access control (decision):** **No dedicated RBAC permission.** Unlike Budget (money +
manageable → explicit `Budget: View`/`Manage`, §8.5/§10), Progress is a read-only
derived value shown inside pages the viewer already reaches. Visibility piggybacks on
page/role access: Projects list + Industry Overview are Industry-only surfaces; on Task
Board / Project Overview it renders for Industry and Institution but **not on the
Individual participant surface** (UI-surface rule, not a permission). §10 Project + FA
isolation still implicitly scope which project's Tasks feed the number.

**Considered and deferred (NOT in v1.5 scope):** FA-level progress rollup (Completed
Tasks in FA / Total in FA). Would restore symmetry with FA-level Budget dashboards and
fit the FA-scoped Task Board page, but explicitly deferred per user 2026-07-17. Revisit
alongside Institution FA-scoped Roles in Phase 2 (§7.7.3).

**Related context sections:** §3.1, §7.1, §7.4, §7.5 (rewritten), §7.8, §10, §12,
§14 (Phase 1.5), §15

**Feasibility:** Feasible now. No AMS/TMS dependency. Task status model (To Do / In
Progress / Reopen / Completed / Cancelled) already exists in this ATOM instance — the
earlier "needs a Task completion status" prerequisite is resolved.

**Status:** Committed to Phase 1.5 (per user decisions 2026-07-14 / 2026-07-17)

**Notes:** Overturns the documented §7.5 Phase 1 FA-based progress decision — flagged
to and requested by the user. Persisted to atom-context.md §7.5, §12, §14, §15 on
2026-07-17.

---

### [2026-09-17] Command mode mechanics — force-join model (RESOLVED, Phase 1)

**Problem:** "Command" existed only as a label (§7.2 "cannot be rejected", §14 Phase 1,
§15 glossary) with **no defined mechanics** — the earlier analysis found that gating
Command behind a form would silently recreate a reject loophole, and nothing specified
what state a commanded member lands in, how the outstanding form is enforced, who may
issue a Command, or how the org escapes a non-compliant commanded member.

**Proposal (now specified, not a draft):** Command joins the target immediately and
unconditionally. Two states — **Active** (no obligation) and **Active — Details Pending**
(outstanding form). Details-Pending is enforced by a narrow-only ABAC policy
`P-COMMAND-OBLIGATION` that narrows only data-dependent actions (attendance,
participation, credential/accreditation) before the deadline and **auto-locks** the
member on deadline breach with **no escalation**; a §8.1 notification fires on lock.
No auto-assigned Tasks (protects the §7.5 progress denominator). Targets **existing**
ATOM users/orgs only (no "New" force-onboarding). Issuable by Industry/Institution by
default; Individual-type roles need a `Command: Issue` RBAC permission. Own management
surface parallel to Invitations/Join Requests; status mirrored on the affected party's
Requests & Invitations page (D12). Reversible via the existing Remove capability (B9).
Every issuance / state transition / Remove is audited (§10).

**Related context sections:** §7.2, §7.7 (intro), §7.7.5 (new), §7.7.2/§7.7.3 (grant
axes), §7.6 (Dynamic Form), §8.1 (Notifications), §8.3 (credential/accreditation),
§10 (ABAC + Audit), §12, §14 (Phase 1), §15. Cross-docs:
`ideation/2026-07-31-abac-pbac-access-control.md` (P-COMMAND / P-COMMAND-OBLIGATION
register), `ideation/2026-07-28-user-management-remove-suspend.md` (B9 Remove, D12
status page).

**Feasibility:** Feasible now — Phase 1 (Command is already Phase 1; all prerequisites,
including Remove and the ABAC narrow-only pattern, exist).

**Status:** Committed to Phase 1 (mechanics resolved per user decisions 2026-09-17;
persisted to atom-context.md §7.2, §7.7 intro, §7.7.5, §12, §14, §15 on 2026-09-17).

**Notes:** **Considered but deferred (NOT written into atom-context.md):**
delegation / org pre-fill of the Dynamic Form on the commanded individual's behalf. The
earlier analysis floated it as a "nice to have"; the user has not confirmed it, so it is
parked here only and is out of scope for the committed §7.7.5 model. Revisit if
Details-Pending friction proves high in practice.

---

### [2026-09-17] Custom Eligibility criterion enforcement mode

**Problem:** For Master-Admin-authored custom Eligibility criteria (e.g. Weight,
license/ID, qualifying time), it is undecided whether enforcement is hard-block,
informational-flag, or type-based default. Age/Gender enforcement is settled (automatic);
this gap is only about the extensible custom criteria.

**Proposal:** Pick one of: (a) always hard-block; (b) always informational-flag for a
human reviewer; (c) a per-criterion or type-based default set by Master Admin at
authoring time. Abhijeet parked this ("we will reiterate this tomorrow"), never resumed.

**Related context sections:** §7.9.4 (Eligibility)

**Feasibility:** Feasible now (decision only, no prerequisite)

**Status:** Parked / awaiting decision

**Notes:** §7.9.4 currently documents enforcement as "fully automatic" for Age/Gender;
the custom-criteria mode is the remaining open sub-question. Do not assume hard-block.

---

### [2026-09-17] Capture-field wiring: MA custom criterion → Joining Form field

**Problem:** §7.9.2 states the intended behavior — every Master-Admin-set eligibility
field for a Sport is auto-added and locked on that Sport's Joining Form. But **nothing
currently wires this automatically**: the mechanism that turns "Master Admin adds a custom
criterion on a Discipline" into "a matching capture field appears on the relevant Joining
Form(s)" is not designed.

**Proposal:** Design the generation/sync mechanism (when does the field appear, what
happens to existing forms/live projects, what field type is inferred from the criterion
type, etc.).

**Related context sections:** §7.9.1 (Sports Library), §7.9.2 (Registration Setup),
§7.9.4 (Eligibility)

**Feasibility:** Partly blocked — depends on the Master Admin Sports Library authoring
surface (out of Flow Builder scope) existing.

**Status:** Open / not designed

**Notes:** The design *intent* (auto-lock MA fields on the Sport form) is committed as
fact in §7.9.2; only the wiring mechanism is open.

---

### [2026-09-17] Exact-match registration/nomination brittleness (withdrawals/replacements)

**Problem:** Registration and Nomination are exact-match (`Σ (Q+R)` per contingent per
sport; every zone/role-slot must fill exactly). No behavior is defined for withdrawals or
replacements after registration/nomination: does a withdrawn slot reopen? is there a swap
deadline? This affects both Closed and Open, and both Individual and Team (role-slots).

**Proposal:** Design a withdrawal/replacement/swap flow (slot reopen, re-nomination,
optional deadline cutoff).

**Related context sections:** §7.9.7 (Open Quota), §7.9.8 (Registration & Nomination)

**Feasibility:** Feasible now (design), but explicitly deferred by Abhijeet ("let's take
this later").

**Status:** Deferred (accepted v1 risk)

**Notes:** Flagged inline in §7.9.8 as an accepted v1 risk. Item 4 in the 2026-08-22
open-items follow-up.

---

### [2026-09-17] Mixed User Types on one person in one Project

**Problem:** ATOM enforces one User Type per Individual per Project. Real cases want one
person to hold two User Types in one Project (e.g. Batsman for Cricket AND Player for
Football). Also includes the narrower sport-affinity gap: nothing stops a sport-specific
User Type (e.g. "Batsman") being reused under the wrong sport, since User Types have no
sport-affinity concept.

**Proposal:** Relax the one-User-Type-per-Individual-per-Project rule and/or add
sport-affinity to User Types. Out of scope for Flow Builder v1 to decide unilaterally.

**Related context sections:** §7.6, §7.9.8 (Registration & Nomination), §7.7

**Feasibility:** Later-phase (v2) — touches a core ATOM identity rule.

**Status:** Deferred to v2

**Notes:** Reflected in §14 Phase 2 roadmap.

---

### [2026-09-17] Per-individual multi-Discipline nomination cap

**Problem:** In v1, one individual can be nominated to unlimited Disciplines (uncapped).
Some Industries will want a configurable cap (e.g. "max 3 Disciplines per person").

**Proposal:** Add an Industry-configurable per-individual multi-Discipline cap at
Nomination.

**Related context sections:** §7.9.8 (Nomination)

**Feasibility:** Flow Builder Phase 2 (explicitly deferred from v1).

**Status:** Committed to Flow Builder Phase 2

**Notes:** Reflected in §14 Phase 2 roadmap.

---

### [2026-09-17] Per-Sport Publish gating at Registration Setup

**Problem:** Registration Setup Publish is currently node-wide. Per-Sport Publish gating
(publish one Sport's forms independently, like Quota Setup Level 2's per-sport publish)
was considered but not designed.

**Proposal:** Decide whether Registration Setup should gate Publish per Sport, and if so
design it.

**Related context sections:** §7.9.2 (Registration Setup)

**Feasibility:** Feasible now (design decision)

**Status:** Deferred / not designed

**Notes:** Node-wide Publish is the committed v1 behavior (§7.9.2).

---

### [2026-09-17] Level 2 Min-enforcement mechanism — explicit sign-off

**Problem:** §7.9.6 documents Min enforcement as a hard precondition on a sport's Level 2
Publish (`Σ Q ≥ Min` on every entry, no override). This evolved from an earlier
agent-inferred ratchet-plus-deadline mechanism (D28) and was never explicitly signed off
by Abhijeet in its current publish-gate form.

**Proposal:** Get explicit confirmation that the publish-gate `Σ Q ≥ Min` mechanism (no
runtime ratchet, no deadline) is the intended Min-enforcement model.

**Related context sections:** §7.9.6 (Level 2)

**Feasibility:** Feasible now (confirmation only)

**Status:** Pending explicit confirmation

**Notes:** Flagged inline in §7.9.6. Same treatment requested for D28-derived mechanisms.

---

### [2026-09-17] Master Admin Sports Library authoring surface + edit-after-enablement

**Problem:** Master Admin needs a portal surface to author Sports/Disciplines, their
attributes, Eligibility criteria, and per-Industry entitlement. Additionally, RBAC and
versioning behavior when Master Admin edits a Discipline already live in Projects is
undefined (propagate immediately vs. version/freeze).

**Proposal:** Build the Master Admin Sports Library authoring surface (separate from Flow
Builder) and decide the edit-after-enablement / versioning model.

**Related context sections:** §7.9.1 (Sports Setup / Sports Library scope boundary), §10

**Feasibility:** Separate build — explicitly out of Flow Builder's own design scope.

**Status:** Out of Flow Builder scope / separate build item

**Notes:** Abhijeet: "we are only creating Flow Builder for Industry which will be used in
Project." Recorded here so the dependency isn't lost.

---

### [2026-09-17] Self-Register open sub-items (Open mode)

**Problem:** Several Self-Register aspects are undesigned: (a) the discovery surface (how
an Individual finds an Open leaf — public browse, search, direct link); (b) whether
Self-Register reuses ATOM's existing sign-up or needs a new account-creation flow;
(c) early-close before Max Entries (deadline vs. pure capacity cutoff); (d) lottery-based
Selection as an alternative to manual Selection for oversubscribed events.

**Proposal:** Design the discovery surface and account-creation path first (blocking for
Self-Register to function); early-close and lottery are optional future enhancements.

**Related context sections:** §7.9.3 (Mode of Registration), §7.9.7 (Open Quota), §7.9.8

**Feasibility:** (a)/(b) needed for Open mode to ship; (c)/(d) later options.

**Status:** Open / not designed

**Notes:** From the Open Quota and Registration & Nomination source docs.

---

### [2026-09-17] Industry-facing custom quota fields (D24 / Item-3) — CLOSED, moot

**Problem:** D24 (2026-07-31) had proposed Industry-defined custom quota fields
(display-only) at Quota Setup.

**Proposal:** N/A — no longer needed.

**Related context sections:** §7.9.4 (Eligibility)

**Feasibility:** N/A

**Status:** Rejected / Resolved-as-moot (2026-09-14)

**Notes:** The underlying need is now served one layer up: custom Eligibility criteria are
Master-Admin-authored on the Discipline (§7.9.1, §7.9.4). Industry has no custom-field
authoring at Quota Setup, and per Abhijeet's 2026-09-14 confirmation what Industry does at
Quota Setup is fully defined without it. Reopen only if a genuinely new Industry-facing
(not MA-facing) need surfaces.

---

### [2026-09-24] Roster Management — Project-level shared roster capability

**Problem:** Rosters are currently built per-module (e.g. Athlete MS: a coach creates a
roster of athletes, then runs training sessions against it). §8.2 also lists "Roster" as
one of the assignable Project Modules, but it has no §8.3 detail entry — named but never
actually specified or built. Left as-is, every module that needs a squad of people (AMS,
TMS, Credential, Accreditation) builds and maintains its own divergent copy.

**Proposal:** Lift Roster into a **base-platform** capability (like Budget Tracking, §8.5)
— bundled into standard Industry billing, not a separately-assignable SKU — with a new
**Roster Management** page (Project-level, between Task Board and User Management) plus a
companion **My Rosters** page (Individual workspace). Modules reference a roster by
`roster_id` instead of copying members.

Two pages, three internal views (Roster Management: List / Create-Edit / Detail; My
Rosters: single read-only list). Full flow, page breakdown, and a per-role (Industry /
Institution / Individual) functionality matrix are in the ideation doc §I.

**Business rules (finalized):**
- **Creation:** Industry (whole-Project, any FA) and FA-scoped Institution (own FA
  population only) by default, mirroring `Command: Issue`'s default-on/off split
  (§7.7.5); an Individual only via an explicit `Roster: Create` Role grant.
- **Membership: Individuals only**, never Institutions — a roster is a squad of real
  people for an activity; an Institution participates as creator/owner or via its own
  Individuals' Project membership, not as a roster member.
- **Adding a member** is bounded by the D5 parent-FA-population guardrail
  (`2026-07-25-sub-fa-access.md`) regardless of the roster having no FA tag — a creator
  can only ever see and pick from their own already-granted population.
- **No Functional-Area tag on the roster itself** — it's a pure Project-level object.
  Sport/Discipline is the sole optional tag and the sole filter on the list/picker.
- **Visibility (list/view, distinct from the add-guardrail):** creator; the Industry,
  always; the roster's own members; and the creator's own Institution, for every roster
  any of its own members create (oversight of its own branch — never crosses into a
  sibling Institution's branch). **Known v1 gap, accepted, not resolved:** an Institution
  cannot see a roster the Industry created, even one containing its own athletes —
  visibility never flows Industry → the Institutions whose people are on it.
- **Co-member visibility** within a roster: default-private, per-roster creator opt-in;
  **no minor-athlete-specific override in v1** (decided independently of the Communication
  module's own minor-safeguarding question, which is a different risk shape — active
  contact vs. passive list visibility — and itself still pending Legal/Business sign-off,
  `2026-07-17-communication-module-integration.md`).
- **Membership removal:** a Project member removed from the Project (§7.7.5) is
  auto-removed from every roster, audited.
- **Orphan handling:** ownership escalates up the existing oversight chain — Individual
  creator → their Institution if still present → Industry — never deleted, never
  silently archived (would strand every consuming module's `roster_id`); transfer is
  audited and the new owner notified.
- **Rostering is never an admission path** — every member must already have joined the
  Project via §7.7.
- **RBAC:** `Roster: View / Create / Edit / Manage Members / Delete`, mirroring the
  Budget/Command permission pattern; view-as-member piggybacks on membership (no
  permission needed, same as Project Progress, §7.5).
- **Module consumption:** a module may still layer its own internal sub-grouping on top
  of a roster reference for purposes no other module needs (e.g. AMS splitting a roster
  into morning/evening training batches) — that stays the module's own job, the same
  principle as Sub-FA placement (D4, `2026-07-25-sub-fa-access.md`).

**Phase 2 (explicit follow-on, gated on the "[2026-09-17] Exact-match
registration/nomination brittleness" item above being resolved first):** Publishing a
Nomination or completing a Team instance auto-creates a core Roster, tagged with that
Discipline, **snapshotted at Publish** (not live-synced — deferred until a
reopen/swap/deadline mechanism exists), with Main/Reserve or role-slot status carried as
a per-member attribute. Also Phase 2: AMS/TMS refactor to consume core rosters instead of
their own module-internal ones (gated on AMS/TMS integration work, §8.4).

**New canonical term:** "Roster" gets a §15 glossary entry, explicitly distinct from
Flow Builder's "Team" (role-slot structure), Nomination's "Main/Reserve zone", and
"Contingent" (the registering Institution itself, not a group of people).

**Related context sections:** §7.4, §7.5, §7.7.3, §7.7.5, §7.8, §7.9.8, §8.1, §8.2, §8.4,
§8.5, §9, §10, §11, §12, §13, §15; `2026-07-25-sub-fa-access.md` (D4/D5/D6),
`2026-07-17-communication-module-integration.md` (D4, correction re: precedent — see
ideation doc §Q4), `2026-07-31-abac-pbac-access-control.md` (new policy
`P-ROSTER-VISIBILITY` candidate).

**Feasibility:** Core = feasible now, no AMS/TMS dependency (Phase 1.5, same prerequisites
as Budget Tracking). Flow-Builder materialisation + AMS/TMS consumption = Phase 2, gated
on the withdrawal-brittleness item above and on AMS/TMS integration work (§8.4).

**Status:** Committed to Phase 1.5 (core) / Phase 2 (Flow Builder materialisation +
AMS/TMS consumption), per user decision 2026-09-25.

**Notes:** Full reasoning, nine resolved open questions (Q1–Q9), and the consolidated
flow/page/role-matrix synthesis are in `ideation/2026-09-24-roster-management.md`.
