# [2026-07-31] ABAC + PBAC on top of ATOM's RBAC

> Standalone ideation doc. Index entry lives in `ideation/README.md`.
> Exploratory — nothing here is committed to `context/atom-context.md`
> yet. Section refs (§) point at `context/atom-context.md`.

**Status:** Raw / Under review. User asked to save the full analysis to
revisit later (2026-07-31). No decisions locked; no files other than this
doc + the README index touched.

**Question that started this:** "Can ATOM add ABAC (Attribute-Based) and
PBAC (Policy-Based) access control on top of its current RBAC, and how?"

---

## A. Headline finding — this is a gap to close, not a new thing to adopt

**ABAC and PBAC are already committed doctrine in `atom-context.md`, but
they are entirely undefined.**

- **§8.1** lists `RBAC · PBAC · ABAC` as base-platform capabilities.
- **§10**: "RBAC (Role-Based), PBAC (Policy-Based), ABAC (Attribute-Based)
  access control operate together — every feature proposal must respect
  all three, not just role checks," and "Never flatten permissions — do
  not propose a shortcut that collapses RBAC/PBAC/ABAC into a simpler
  single check."
- **§16 AI Guidance**: "Never flatten RBAC/PBAC/ABAC," "Every proposal
  must respect PBAC and ABAC, not just role checks."
- **§3.2** maps the Access Control layer to "RBAC, PBAC, ABAC."
- **§15 Glossary**: defines all three one-liner each, "applied together,
  never singly, to every permission check."

So "is this possible?" is stronger than possible — it's **mandated**.
What's missing is that **nowhere in the document is a single concrete
policy or attribute defined.** "PBAC" appears with zero example policies;
"ABAC" with zero example attributes. The real work is *specifying* them,
not deciding whether to adopt them.

**Second key finding: ATOM already does ABAC informally, per-feature,
without a shared model.** The three isolation levels in §10 (Workspace /
Project / Functional Area isolation) are attribute predicates in all but
name. "An Institution sees its own assigned FA's allocation, not sibling
FAs'" (§10 budget rule) is `resource.fa_id ∈ user.granted_fas` — that's
ABAC. Same with the Sub-FA D5 guardrail. These are hand-written into each
feature decision instead of expressed against one model.

---

## B. What the RBAC model actually looks like today (grounded)

Concrete access model that exists today is **pure RBAC**, and it lives
mostly in `backlog.md` (2026-07-09 items), not the context body:

- Roles + a permission matrix. Seed roles: **Super Admin, Viewer**.
- Matrix rows are **platform-admin capabilities** — Functional Area
  Management, Project Configuration, Task Management, Project Role
  Management, Institution Management, Individual Management.
- A "More permissions" popover with one high-privilege checkbox,
  "Assign Functional Area Owner."
- Role assigned to Individuals at invite time (§7.7.2: "Role — maps to
  permissions on the ATOM Project; this is what actually governs what the
  invitee can do").
- Institutions in Phase 1 get **full FA access, no roles** (§7.7.3).
- **Module Admin** — a new RBAC axis (Sub-FA doc D6), per-Project-per-
  Module.

Scoping is done through three **isolation levels** in §10: **Workspace,
Project, Functional Area isolation.**

`backlog.md` has **no** ABAC/PBAC entry. The five 2026-07-09 RBAC items
(role scoping, module-driven matrix, delete safety, assign-user gate,
granular permissions) are all *RBAC granularity* work — NOT attribute/
policy work, and should stay in RBAC.

---

## C. What each layer concretely means for ATOM

### RBAC — "what capability does this role have?"
Coarse yes/no on an action (`Budget: Manage`, `Task Management`). Exists.

### ABAC — "on which instance, given who/what/when?"

**User attributes** (all already tracked by ATOM):
- `current_workspace` — Individual / Institution / Industry / Master
  Admin (§6; already the primary determinant per §2).
- `project_role` (§7.7.2), `user_type` — Athlete / Coach / Support Staff
  (§7.6).
- `granted_fas` — FA(s) an Institution/Individual holds (§7.7.3).
- `is_module_admin(module, project)` (Sub-FA doc D6).
- `home_industry`; `pro_membership` (later-phase B2C, §9).

**Resource attributes:**
- `resource.project_id`, `resource.fa_id`, `resource.sub_fa_id`.
- `resource.generating_module` — AMS Training Program vs TMS Tournament
  (§7.8).
- `project.lifecycle_state` — Draft / Upcoming / Ongoing / Completed /
  Archived (§7.1).
- `budget.fa_id` (§8.5); `task.status` — To Do / In Progress / Reopen /
  Completed / Cancelled (§7.5).

**Environment attributes:**
- `project.registration_state` — Open / Closed (§7.2).
- Season / active window (AMS training schedules, §8.3).
- Grievance TAT deadline elapsed? (§8.3).
- Quota reclamation deadline passed? (Flow Builder v2 D6).

### PBAC — "the combined rule, expressed once, reusable across modules"

Each policy below re-expresses a decision **already made** or an obvious
near-term need:

- **P-BUDGET** (re-expresses §10): a subject with `Budget: View` may read
  a budget record only where `budget.fa_id ∈ subject.granted_fas`, or
  where `subject.current_workspace = Industry` owning the Project.
- **P-SUBFA** (re-expresses Sub-FA D5): a Module Admin may assign a user
  to a Sub-FA only where that user ∈ `parent_fa.granted_population`.
- **P-PROGRESS** (re-expresses §7.5): the Progress element renders only
  where `subject.current_workspace ∈ {Industry, Institution}`; never on
  the Individual participant surface. (Currently a UI hack — really a
  policy.)
- **P-COACH-SEASON** (new, AMS): a `Coach` User Type may edit attendance/
  assessment only where `project.lifecycle_state = Ongoing` AND now ∈
  season window.
- **P-GRIEVANCE-ESCALATE** (new, §8.3): a grievance may be escalated only
  after its TAT deadline has elapsed.
- **P-COMMAND** (re-expresses §7.2 / §7.7.5): a Command registration
  bypasses the accept/reject decision — the target joins immediately and
  unconditionally. This is the *join-gate* policy only; it does **not**
  carry the post-join form obligation (that is P-COMMAND-OBLIGATION).
- **P-COMMAND-OBLIGATION** (committed, §7.7.5): governs a commanded member
  in the *Active — Details Pending* state (outstanding Dynamic Form).
  Narrow-only: before the deadline it narrows *only* the actions dependent
  on the missing data (attendance/presence, participation/competition,
  credential/accreditation issuance); on deadline breach it tightens its
  own narrowing to a **full project lock**. Purely automated, **no
  escalation** to any other party. Distinct from P-COMMAND — that decides
  the join, this governs the outstanding obligation afterward. This is the
  first policy in the register carried by a *committed* atom-context.md
  decision (§7.7.5) rather than a retroactive formalization.

Many of these are not new features — they are **existing decisions that
are secretly policies**, scattered today across §7.2, §7.5, §8.5, §10,
and the Sub-FA doc.

---

## D. Feasibility (honest)

### RBAC alone is insufficient — the context proves it in three places

- **Significant — §10 budget rule is un-expressible in RBAC.** §10 itself
  says `Budget: View`/`Manage` grant the *capability* but isolation still
  scopes *which* budgets are seen, and "RBAC alone must never be the whole
  check." Pure RBAC has no instance/attribute dimension → can't express
  "your own FA's slice, not siblings'." Clearest documented RBAC failure
  in the file.
- **Significant — Sub-FA D5 guardrail** ("assign only users already in the
  parent FA's granted population") is a narrowing-within-a-set constraint,
  inherently attribute-based. The Sub-FA doc already had to invent
  two-layer enforcement because RBAC couldn't carry it.
- **Minor→significant as modules land — time/state-bound rules** (Coach
  edits only in active season, Grievance escalation only after TAT, quota
  reclamation only before deadline) have no home in stateless RBAC.

**Where RBAC is genuinely enough (don't over-reach):** the five 2026-07-09
backlog items are RBAC-granularity problems — solve in RBAC, not ABAC.

### What ABAC/PBAC requires (product level, no code)

- **A shared attribute model** — documented, named User/Resource/
  Environment attributes above; most are already loaded to render a page,
  so sourcing is cheap.
- **A decision point (PDP) concept** — one place answering "may subject S
  do action A on resource R, now?" Load-bearing because of §8.4 (modules
  are independently developed): either each module evaluates policies
  itself (drift — the failure the Sub-FA two-layer model was dancing
  around) or ATOM exposes a central decision service modules call. The
  latter is the only option consistent with §10/§16.
- **A policy authoring surface** — in v1 this can be documented policy
  constants in the context file, NOT a UI. No policy-editor product needed
  to start.

### Build now or phase it?

Phase it — not because it's optional (§10/§16 make it mandatory) but
because the full PBAC engine is over-scoped for Phase 1.5 while the ABAC
attribute model is overdue.

### Risks

- **Complexity / combinatorial blowup (significant).** Mitigation: the
  **narrow-only invariant** — ABAC/PBAC may only *narrow* an RBAC grant,
  never widen it. This single rule keeps the system tractable and makes
  migration safe.
- **Debuggability of denials (significant).** "Why was I denied?" is the
  classic ABAC failure. §10 has Audit logs — extend them to record a
  **decision trace** (which policy denied, on which attribute) + a
  human-readable deny reason.
- **Performance/latency (minor for ATOM).** Most attributes are already
  loaded per page; real cost is central-PDP round-trips from independent
  modules (§8.4) — cache the subject's attribute bundle per request.
- **Migration from pure RBAC (significant if mishandled).** Existing seed
  roles (Super Admin, Viewer) and assigned Project Roles must not suddenly
  deny. The narrow-only invariant solves it: introduce ABAC/PBAC strictly
  as additional narrowing on top of existing grants. A role that works
  today keeps working; the overlay only removes access it shouldn't have
  had (e.g. sibling-FA budgets) — the intended fix, not a regression.
  Exactly how §10's budget rule already frames it.

---

## E. Proposed phased approach

- **Phase A — Name the model (doc-only, ~now).** Add an ABAC attribute
  vocabulary + a policy register to §10 (attributes in §C; policies
  P-BUDGET, P-SUBFA, P-PROGRESS as retroactive formalizations). Add the
  **narrow-only invariant** as a §12 business rule. Zero engineering —
  closes the §10/§16 "declared but undefined" gap.
- **Phase B — Pilot ABAC overlay on already-decided features (1.5→2).**
  (1) **Budget Tracking (§8.5)** — its access rule is already documented
  as RBAC-capability + isolation-scoping (§10), i.e. already ABAC waiting
  to be named. (2) **Sub-FA / Module Admin guardrail (D5)** — two-layer
  enforcement is already ABAC in disguise.
- **Phase C — Introduce PBAC evaluation (Phase 2, module-driven).** As
  AMS (season), Grievance (TAT), Flow Builder quota (reclamation) land,
  add environment/time policies (P-COACH-SEASON, P-GRIEVANCE-ESCALATE)
  through a central PDP. Add the Audit decision-trace here.
- **Phase D — (only if justified) policy authoring product.** A UI for
  Industry/Master Admin to compose policies. Don't commit until policy
  count demands it.

**First pilot candidates, ranked:** (1) Budget Tracking §8.5,
(2) Sub-FA Module Admin D5, (3) Progress visibility §7.5 (convert the
UI-hack rule into P-PROGRESS). All three are already-made decisions whose
rules are secretly ABAC/PBAC — piloting there is formalization, not new
risk.

---

## F. Where the documentation genuinely runs out

`atom-context.md` does not define what a "policy" or "attribute"
concretely *is* in ATOM anywhere. §8.1/§10/§15/§16 assert PBAC/ABAC exist
and must be respected, but there is no policy schema, no attribute list,
no PDP concept, no example. Everything in §C–§E is synthesis from ATOM's
entities and existing scoping decisions (workspace, FA grants, lifecycle
states, the §10 budget rule, the D5 guardrail) — grounded in real ATOM
structure, but the access-control specifics themselves are **net-new
documentation**, not read back from the file. Not inventing hierarchy;
filling a doctrinal gap the file openly leaves open.

---

## G. Recommendation (decisive)

**Yes — build it, and treat it as closing an existing gap, not adopting
something new.** ATOM already committed to RBAC+PBAC+ABAC operating
together (§10, §16) but never defined the latter two, and its own Budget
rule (§10) and Sub-FA guardrail (D5) are already ABAC hand-written
per-feature. Not a big-bang policy engine. Instead:

1. **Now (doc-only):** name the ABAC attribute model + a small policy
   register; adopt the **narrow-only invariant** (ABAC/PBAC may only
   tighten an RBAC grant, never widen) — makes migration non-breaking and
   reasoning tractable.
2. **Phase 1.5→2:** pilot on **Budget Tracking (§8.5)** first, then
   **Sub-FA/Module Admin (D5)** — both already-decided, both already ABAC
   in disguise.
3. **Phase 2, module-driven:** add PBAC environment/time policies as
   AMS/Grievance/Flow-Builder land, with an Audit decision-trace.
4. Defer any policy-authoring UI indefinitely.

Do **not** fold the five 2026-07-09 RBAC-granularity backlog items into
this — those stay RBAC.

---

## H. Open questions to resolve on revisit

- **O1 — Narrow-only invariant: hard rule?** It's load-bearing: it makes
  the RBAC→hybrid migration non-breaking, but means ABAC/PBAC can never
  *grant* access, only restrict it. If an attribute should ever *open*
  access (e.g. Pro Membership unlocks a feature, §9), that breaks the
  invariant and needs a deliberate exception. Decide the default.
- **O2 — Central PDP vs per-module evaluation.** §8.4 independent-module
  reality pushes toward a central decision service; confirm before any
  module integrates its own policy checks and drifts.
- **O3 — Deny-reason surfacing.** Confirm the Audit decision-trace (§10)
  is in scope from Phase B, not bolted on later.
- **O4 — Where policies live in v1.** Documented constants in
  `atom-context.md` §10 vs a config artifact. Recommend documented
  constants until count justifies a UI.

---

## I. Downstream doc impact (when/if committed — not editing now)

- **§10** — add the ABAC attribute vocabulary (§C), the policy register
  (P-BUDGET / P-SUBFA / P-PROGRESS to start), and the central-PDP concept;
  state the three isolation levels are the first ABAC predicates.
- **§12** — add the **narrow-only invariant** as a business rule.
- **§8.1 / §15** — expand the one-line PBAC/ABAC entries with the concrete
  attribute/policy model once settled.
- **§7.5** — note P-PROGRESS replaces the "UI-surface rule, not a
  permission" framing with a named policy.
- **§8.5 / §10 budget rule** — reframe as the first ABAC pilot (P-BUDGET).
- **backlog.md** — add a stub: "ABAC attribute model + policy register
  (formalize §10/§16 doctrine)"; keep the five 2026-07-09 RBAC items as
  RBAC, not folded in.
- **Cross-doc** — Sub-FA D5/D6 (`2026-07-25-sub-fa-access.md`) becomes the
  P-SUBFA pilot; its two-layer enforcement is the reference ABAC pattern.
