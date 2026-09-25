# [2026-07-20] Flow Builder — full flow + gap audit (Phase 2)

> Standalone ideation doc (too large for a README inline entry). Index
> entry lives in `ideation/README.md`. This is exploratory — nothing
> here is committed to `atom-context.md` or `backlog.md` yet. Section
> refs (§) point at `context/atom-context.md`. Source draft:
> `ideation/atom-flow-builder.md` (first-pass overview, NOT finalized).

**Status:** Ideation — pending user sign-off on decisions D1–D12. The
load-bearing calls are **D1** (is the Hierarchy tab a new hierarchy
primitive or a view over §7.7?), **D2/D9** (how Sports/quota reconcile
with the §3.1 Functional Area hierarchy), and **D5** (config edits after
registrations exist).

---

## A. What the draft proposes (one-paragraph recap)

A two-tab Flow Builder module for the Industry workspace. **Tab 1
Hierarchy** — a drag-and-drop canvas where the Industry places
Institution/Individual nodes, connects them into a reporting tree, and
sends invitations (states: Draft/Pending/Accepted/Rejected/Expired),
with a per-Institution sub-canvas for nested hierarchy. **Tab 2
Validation & Quota Setup** — a 3-node sequential workflow: **Sports**
(Sport → Event → Discipline) → **Registration Configuration** (User
Types + Sub Types + Form Builder, standard-vs-custom fields) → **Quota
Setup** (project-level quota + per-Institution allocation with
min/max/participation-type). The draft defers nomination, approval-
workflow builder, qualification/eligibility engines, automation, AI,
bulk import/export, quota transfer, and version history.

The core value proposition — a generic, industry-agnostic project
configuration engine that other modules consume — is sound and fits
ATOM's modular thesis (§1, §8.4). The problems below are about
**reconciliation with already-finalized architecture**, not the idea
itself.

---

## B. Reconciliation with §7.7 — the Hierarchy tab is the biggest issue

The draft's Hierarchy tab does not contradict the *spirit* of §7.7, but
as written it **silently drops three things §7.7 makes mandatory** and
**invents a hierarchy primitive that runs parallel to §3.1**. Each is
flagged below with severity.

### B.1 It drops the grant axes §7.7 requires — **BLOCKING**

- **Individual nodes carry no Role.** §7.7.2 is explicit: inviting an
  Individual requires **two distinct axes — User Type *and* Role** —
  and "Role … is what actually governs what the invitee can do once
  inside." The draft's Individual Node shows only `User Type`. Role is
  absent. A Hierarchy tab that issues invites without a Role either (a)
  bypasses the permission model, or (b) forces a second, separate
  invite step outside Flow Builder — defeating the "single source of
  truth" claim (draft line 31).
- **Institution nodes carry no Functional Area.** §7.7.3: an Institution
  is granted a **Functional Area**, not a generic membership. The
  Institution Node lists Name, Type, Invitation Status, Member Count,
  Assigned Quota — but **not which FA it is granted**. FA assignment is
  a hard §11 dependency ("Cannot assign a Functional Area before
  Institution approval"; "Cannot create Tasks before FA assignment").
  Omitting FA from the node means the hierarchy the draft builds cannot
  actually authorize any Institution to execute work.

**Scenario:** Industry builds a 40-node hierarchy in Flow Builder, sends
invites, everyone accepts — and no Institution can be assigned a Task
because no FA was ever granted, and no Individual has a Role. The
hierarchy is decorative. This is why B.1 is blocking, not cosmetic.

### B.2 It is invite-only and omits the Apply and Command paths — **SIGNIFICANT**

- §7.7 says there are **exactly two ways** to join: **apply** or **get
  invited**. The Hierarchy tab models only *invite* (Industry places a
  node, sends an invitation). There is no representation of **apply-path
  joiners** — an Individual/Institution who self-registered into an
  Open project (§7.2) and was accepted. Do they appear on the canvas?
  If not, the canvas is not a true picture of who is in the Project.
- **Command is missing.** §7.2/§12: Command is a **platform
  differentiator that cannot be rejected**. The draft's node states
  include `Rejected` for everything, which structurally contradicts
  Command. A canvas that can only Invite (rejectable) silently drops
  Command.

### B.3 The canvas hierarchy is a *parallel* primitive to §3.1 — **SIGNIFICANT**

The draft's example tree is `Industry → National Federation → State
Association → District Association → Athlete`. That is a
**participation/reporting** hierarchy of Institutions and Individuals.
ATOM's canonical §3.1 hierarchy is `Industry → Project → Functional Area
→ Sub FA → Task Board → Task`, with Institutions **plugging into** FAs
(§3.1: "Institution is not a rung of the … ladder — it's a separate
lineage that plugs into it"). The draft never says how its tree maps
onto FAs.

Good news: nested Institutions are **already legal** — §5.5 lists
`Institution → Institution → Industry` as a participation path. So a
multi-tier Institution tree is not itself a violation. The unresolved
question (D2) is whether the Flow Builder tree is (a) *the same thing*
as FA assignment visualized, or (b) a genuinely separate axis layered
on top of the FA hierarchy. It cannot be left ambiguous, because Quota
Setup (Tab 2) allocates to Institutions and the whole §3.1/§7.4 model
scopes Institution authority to **FAs**.

### B.4 The sub-canvas crosses a workspace boundary — **SIGNIFICANT**

The draft: "Clicking an Institution node opens a dedicated sub-canvas
[where] the Institution can invite child Institutions and Individuals."
Who operates it? Two readings, both problematic as written:

- **Industry operates it** (builds sub-institutions' children itself):
  contradicts §5.2/§6 — Institutions invite their own members from
  their **own workspace**, and workspaces are isolated (§10). The
  Industry authoring an Institution's internal roster is a workspace-
  isolation smell.
- **The Institution operates it** (in its own workspace): then Flow
  Builder is *not* an Industry-only tool — it spans workspaces, and the
  Institution needs the **FA-scoped Roles that are themselves Phase 2**
  (§7.7.3: "Later phases: Institutions will also get Roles scoped within
  their assigned FA(s)"). You cannot delegate sub-hierarchy authority
  before that primitive exists.

Recommendation lands in D3: **defer the sub-canvas**; ship Industry-
authored single-tier hierarchy first.

---

## C. Missing flow pieces

### C.1 Relationship to the existing project-setup flow (§7.3 / §5.4) — **overlap, must reconcile**

§7.3's workflow is already: `Create Project → Configure Project →
Configure Functional Areas → Configure User Types → Configure Dynamic
Forms → Open Registration → Execution → Closure`. Flow Builder's **Node
2 (Registration Configuration = User Types + Form Builder) is the same
thing as §7.3's "Configure User Types" + "Configure Dynamic Forms"
steps**, and §7.6 already says these are per-Project. So Flow Builder is
**not additive here — it repackages existing Phase 1 steps into a node
UI.** That is fine, but two things must be stated:

1. Flow Builder **replaces** those two §7.3 steps rather than running
   beside them (otherwise there are two places to define User Types /
   forms — a §12 "dynamic forms are project-specific" consistency hazard
   and a data-duplication bug).
2. **§7.3's "Configure Functional Areas" step is absent from Flow
   Builder entirely.** FA config is THE operational unit (§7.4) and a
   hard dependency (§11). Where does it live — inside Flow Builder (a
   missing 4th concern) or upstream of it? This must be answered before
   Quota Setup (which allocates to Institutions whose authority is
   FA-scoped) can be coherent. See D2/D9.

### C.2 Config edits after registrations exist — **SIGNIFICANT gap, not just "versioning"**

The draft defers "Version History and Rollback." But the *policy*
question is separate from the *feature*: what happens when the Industry
edits a Published node **after participants have registered against the
old config**?

- Remove an Event from Sports that athletes already registered for →
  orphaned registrations.
- Add a now-required field to a registration form → already-submitted
  registrations are retroactively invalid.
- Lower a Quota `Maximum` below the count already filled → over-subscribed
  state.

ATOM already has the right pattern for this: **§8.5 Budget Tracking
makes reducing an allocation below what's already committed a hard
block.** Recommend mirroring it (D5): once a node is Published *and
registration has opened*, config becomes **edit-restricted** — additive
changes allowed via re-publish; **destructive changes hard-blocked while
dependent registrations exist.** Full version history stays out of
scope; this guardrail is *in* scope because without it the module can
silently corrupt live registrations.

### C.3 Ordering: can the three nodes run out of order / in parallel? — gap

The draft asserts a sequence but doesn't state whether it's enforced.
The **data dependencies force most of it**: the Registration form
references Sport/Discipline (draft's Athlete form has `Sport`,
`Discipline` fields) and Quota validates against both configured Events
*and* configured User Types (draft "Validation Rules"). So:

- **Sports must Complete first** (both downstream nodes reference it).
- **Registration depends on Sports** (form fields reference
  Sport/Discipline). Recommend: Registration after Sports.
- **Quota depends on both** and is **strictly last**.

Effective enforced order: `Sports → Registration → Quota`. Recommend
blocking a node's config until its prerequisite node is Completed (D4).

### C.4 Can you invite into the hierarchy before Quota is Published? — gap

The draft says "The hierarchy is completely independent of quota
configuration" (line 48). Taken literally that means an Institution can
be invited and accepted before it has any quota. Then its members try to
register **against no quota**. Recommend decoupling two states (D6):
*inviting/accepting into the hierarchy* is allowed early (relationship
establishment), but *registering members that consume quota* is blocked
until Quota Setup is Published for the relevant Event. "Independent" is
true for authoring; it must not be true for enforcement.

### C.5 Who can use Flow Builder? — **gap, RBAC unaddressed**

The draft never states permissions. Flow Builder is an Industry-
workspace configuration tool, so it needs new **Industry-scoped RBAC
permissions** (e.g. `Flow Builder: Configure Hierarchy`, `Flow Builder:
Configure Sports/Registration/Quota`). Per §10 and the §7.7.2/§8.5
caution, it must **never be granted to participant User Types (Athletes,
etc.) by default.** This is also a live tie-in to the two open
**2026-07-09 backlog items** (RBAC matrix must cover modules; role-
scoping level Project vs Industry template) — Flow Builder is exactly
the kind of Industry-level authoring capability that wants Industry-
template scoping, not per-Project duplication. See D7.

---

## D. Cross-module consumption — several consumers don't exist yet

The draft wires each config output to a list of consuming modules. Sanity
check against the §8 catalog and §14 roadmap:

| Consumer named in draft | Exists in ATOM today? | Verdict |
|---|---|---|
| Registration / Join Requests / Invitations | **Yes** — Phase 1 (§14) | In scope, real |
| Individual Profile | Yes (§2) — but see D8 caveat | In scope, with a caveat |
| Athlete Management (AMS) | §8.2/§8.4 — assignable module, MVP exists but **only if the Industry licensed it** | **Conditional** — not premature, but must be a subscribe-if-present consumer, never assumed |
| Accreditation / Credential | §8.2 assignable module | **Conditional** — same as AMS |
| **Schedule / Scheduling** | **No standalone Schedule module exists** in §8.1 or §8.2. "Scheduling" is named as a *problem* (§1) and AMS has "training schedules," but there is no Schedule module in the catalog or roadmap | **Premature** — don't build this consumer |
| **Reports** | **No Reports module in §8.** Analytics is Phase 2 (§14) and not yet a named module | **Premature** — defer |
| **Institution Dashboard** | The draft's *own* Tab 1 "Future Enhancement"; not a current module/entity | **Premature** — forward-looking, don't build the consumer |

**Recommendation (D10):** Flow Builder should **emit versioned config
artifacts** (Sports config, Registration config, Quota config) via a
publish event, and let consumers subscribe **if and when they exist and
are licensed** — rather than hard-wiring named consumers. This keeps the
"foundational config engine" thesis intact while honoring §11 (a module
can't consume before it's active) and §8.4 (modules are independently
developed and integrated). Do **not** build Schedule / Reports /
Institution Dashboard integrations in the Phase 2 first cut.

---

## E. Data-model implications (prose sketch)

New or extended entities implied, and how they relate to existing
`atom-context.md` entities:

- **Sport → Event → Discipline/Category** (new, 3-level). Likely a
  **global Sport master library** (draft's searchable "Sports Library")
  plus a **project-scoped `SportConfig`** recording which sports/events/
  disciplines the Industry enabled for *this* Project. Relates to
  **Project** (owns the config). Introduces the Sport taxonomy ATOM
  currently has no entity for.
- **UserType (existing, §15) + `SubType` (new) + selection state.** §15
  already defines User Type as a per-Project classification. The draft
  adds **Sub Types** (standard + custom) and a **"Master Library" to
  select from.** Mild tension with §7.6 ("Industry defines User Types
  per Project"): reconcile as a **global master palette that seeds
  project-scoped instances** (D11) — the instance stays project-specific
  (§7.6/§12 preserved), the palette just provides reuse.
- **RegistrationForm + FieldDefinition** (this *is* §7.6's Dynamic Form,
  now keyed by UserType/SubType). A `FieldDefinition` carries a
  **standard-vs-custom** flag:
  - **Custom fields** → stored only on the `ProjectRegistration`. Clean,
    matches §7.6 "project-specific, never global."
  - **Standard fields** → the draft says these **map to Individual
    Profile and pre-fill in future projects.** This implies a **global
    Individual profile-attribute schema that ATOM does not currently
    describe**, and mildly cuts against §7.6/§12 ("forms are project-
    specific, never global"). It also foreshadows the §9 Pro Membership
    "cross-Project value" thesis. Flag as a real new data structure —
    see D8 (recommend deferring the pre-fill).
- **Quota + InstitutionAllocation** (new). `Quota` keyed by
  `(Event, UserType, participation constraints)` at Project level;
  `InstitutionAllocation` distributes it per Institution. Relates to
  **Event** (Sports config) and **Institution** (§7.7.3). **The
  unresolved relationship (D9):** §7.7.3/§7.4 scope an Institution's
  authority to its granted **Functional Area**, but the draft allocates
  quota to an Institution **flat, with no FA**. Is quota
  per-Institution, or per-Institution-per-FA? This must be decided or
  Quota Setup and FA ownership describe two different Institution
  scopes.
- **Role (existing, §7.7.2) — must be re-attached** to the Individual
  node/registration path (B.1). Not a new entity; a dropped one.
- **Functional Area (existing, §3.1/§7.4) — the missing spine.** Flow
  Builder as drafted never references FAs, yet FAs are the operational
  unit and the §11 dependency gate. The data model is not coherent until
  FA is placed relative to the Sports/Quota/Institution structures (D2).

**Net:** the deepest data-model risk is not any single new table — it's
that Flow Builder introduces a **Sport/Quota/Institution participation
structure that runs parallel to the §3.1 Functional Area structure and
never joins to it.** D2 and D9 exist to force that join.

---

## F. Phasing recommendation (Phase 2 first cut vs defer further)

**Ship in the Phase 2 first cut:**

- **Node 1 — Sports** (Sport/Event/Discipline). Self-contained,
  foundational, no external dependency.
- **Node 2 — Registration Configuration** (User Types + Sub Types + Form
  Builder, standard + custom fields **as project-scoped only**). This is
  largely §7.3/§7.6 Phase-1 capability repackaged — low new risk.
- **Node 3 — Quota Setup** (project-level quota + Institution allocation,
  min/max/participation-type only). Core to the quota value prop.
- **Tab 1 — Hierarchy**, but **reconciled per D1**: a
  visualization + authoring surface *over the existing §7.7 apply/invite
  + User Type+Role / FA-grant model*, single-tier, Industry-operated —
  **not** a new hierarchy primitive.

**Defer further (beyond the draft's own "Out of Scope V2"), with the
dependency that forces each deferral:**

- **Institution sub-canvas / nested Institution-operated hierarchy
  building** — depends on §7.7.3 **Institution FA-scoped Roles (also
  Phase 2, not yet built)** and touches §10 workspace isolation. Ship
  Industry-authored single tier first. *(D3)*
- **Standard-field → Individual Profile cross-project pre-fill** —
  depends on a **global Individual profile-attribute schema that does
  not exist** and mildly conflicts with §7.6. Ship standard + custom as
  project-scoped now; design the profile schema separately (it also
  underpins §9 Pro Membership). *(D8)*
- **Extensible quota constraints** (Age/Weight/Nationality/State/
  Experience/Qualification/Disability) — ship the **min/max/
  participation-type** trio only; the pluggable constraint engine is a
  larger bet adjacent to the qualification/eligibility engines the draft
  already defers.
- **Schedule / Reports / Institution Dashboard consumers** — the modules
  don't exist (§D). Emit config; don't build these integrations.

---

## G. Decisions needing explicit sign-off

**D1 — Is the Hierarchy tab a new primitive or a view over §7.7?**
*Recommend:* a **visualization + authoring surface over the existing
§7.7 model**, not a new hierarchy primitive. It must (a) carry the
**Role** axis on Individual nodes (§7.7.2), (b) carry the granted
**Functional Area** on Institution nodes (§7.7.3), (c) render
**apply-path** joiners, not just invitees, and (d) support **Command**
as well as Invitation. *Why:* anything less re-implements join mechanics
in a second place and drops finalized §7.7 semantics (B.1–B.2).

**D2 — How does Flow Builder reconcile with the §3.1 FA hierarchy?**
*Recommend:* keep **FA as the unchanged execution/ownership spine**;
treat **Sports as a separate "what is contested" taxonomy**; and make
the **granted FA an explicit field on every Institution node**. The
Flow Builder tree is a participation view *layered on* the FA model, not
a replacement for it. *Why:* Quota (Tab 2) allocates to Institutions
whose authority is FA-scoped (§7.4/§7.7.3); the two hierarchies must
join somewhere. **Load-bearing.**

**D3 — Who operates the Institution sub-canvas — and ship it when?**
*Recommend:* **defer the sub-canvas** from the first cut; ship Industry-
authored single-tier hierarchy. When built, it must run in the
**Institution's own workspace** (§6/§10) and depends on §7.7.3 Phase 2
Institution FA-scoped Roles. *Why:* B.4 — either reading of the draft
violates workspace isolation or presupposes an unbuilt primitive.

**D4 — Enforced node ordering?** *Recommend:* enforce `Sports →
Registration → Quota`; block a node until its prerequisite is Completed;
Quota strictly last. *Why:* the data dependencies already require it
(C.3).

**D5 — Config edits after registrations exist?** *Recommend:* on Publish
+ registration-open, config becomes **edit-restricted** — additive
changes allowed via re-publish; **destructive changes hard-blocked while
dependent registrations exist** (mirrors the §8.5 over-allocation hard-
block pattern). Full version history stays out of scope; this guardrail
is *in* scope. *Why:* C.2 — otherwise the module can silently invalidate
live registrations. **Significant.**

**D6 — Invite into hierarchy before Quota is Published?** *Recommend:*
allow **inviting/accepting** into the hierarchy early, but **block
member registration that consumes quota** until Quota Setup is Published
for the relevant Event. *Why:* the draft's "hierarchy is independent of
quota" is true for authoring, unsafe for enforcement (C.4).

**D7 — RBAC for Flow Builder.** *Recommend:* new **Industry-workspace**
permissions (per-tab or per-node), **never** granted to participant User
Types by default (§10, §7.7.2). Resolve alongside the open 2026-07-09
backlog items (RBAC matrix coverage; Industry-template role scoping).
*Why:* C.5 — the draft is silent and Flow Builder is a high-privilege
authoring surface.

**D8 — Standard-field → Individual Profile pre-fill: build now or
defer?** *Recommend:* **defer** the cross-project pre-fill; ship standard
+ custom fields as **project-scoped** now. *Why:* it needs a global
profile-attribute schema that doesn't exist and cuts against §7.6/§12;
better designed with §9 Pro Membership's cross-Project data model.

**D9 — Quota ↔ FA ↔ Institution scope.** *Recommend:* scope quota
allocation to the **Institution's granted Functional Area** (align with
§7.7.3/§7.4), not a flat per-Institution number. *Why:* E — otherwise
Quota Setup and FA ownership describe two different Institution scopes.
Depends on D2. **Load-bearing.**

**D10 — Which consumers are in Phase 2 scope?** *Recommend:* Flow
Builder **emits versioned config artifacts**; real consumers are the
Phase-1 Registration/Invitation/Join-Requests flow plus **conditionally**
licensed §8.2 modules (AMS, Accreditation). **Do not** build Schedule /
Reports / Institution Dashboard — they don't exist (§D, §11, §8.4).

**D11 — Master Library vs project-scoped for Sports & User Types.**
*Recommend:* a **global master palette that seeds project-scoped
instances** — reuse without breaking §7.6/§12 "project-specific, never
global." *Why:* reconciles the draft's "Master Library" with §7.6.

**D12 — Command in the Hierarchy canvas.** *Recommend:* **yes** —
represent Command as a node state that **cannot enter `Rejected`**
(§7.2/§12). *Why:* omitting it silently drops a documented platform
differentiator (B.2).

---

## H. Overlap check against `backlog.md` (no duplication, two tie-ins)

- No existing backlog item covers Sports config, registration forms, or
  quota — **no duplication**; Flow Builder is net-new scope.
- **Tie-in 1 (D7):** the two **2026-07-09 RBAC items** ("matrix must
  cover §8.2 modules"; "define role-scoping level Project vs Industry
  template") are prerequisites for clean Flow Builder permissions.
- **Tie-in 2 (D5):** reuses the **§8.5 Budget Tracking** finalized
  "reduce-below-committed = hard block" pattern — precedent, not
  conflict.
- **Adjacent (D9/§7.5):** Project Progress Tracking (Phase 1.5) counts
  Tasks under FAs; Flow Builder's quota-per-FA question (D9) touches the
  same FA-scoping that §7.5's deferred FA-rollup and §7.7.3 Phase 2
  Institution Roles all orbit. Worth deciding these together.

---

## I. Downstream doc impact (when committed — not editing now)

- **§3.1 / §7.4** — state how the Flow Builder participation tree relates
  to the FA spine (D2).
- **§7.3 / §5.4** — Flow Builder Node 2 *replaces* the "Configure User
  Types / Dynamic Forms" steps; note where FA config sits (C.1).
- **§7.7** — Hierarchy tab as the authoring surface over apply/invite;
  Role + FA + Command carried through (D1, D12).
- **§8 / §8.2** — add Flow Builder as a module (base-platform vs
  assignable?) and its emitted config artifacts (D10).
- **§10** — new `Flow Builder:*` RBAC permissions; sub-canvas workspace-
  isolation rule (D3, D7).
- **§11** — ordering constraints Sports→Registration→Quota; quota-before-
  registration gate (D4, D6).
- **§12** — config-edit-after-registration hard-block rule (D5); quota
  parent-sets-child allocation echoing §8.5.
- **§14 Roadmap** — Flow Builder first cut in Phase 2; sub-canvas /
  profile pre-fill / extensible-quota-constraints deferred further.
- **§15 Glossary** — Sport/Event/Discipline, Sub Type, Registration
  Form, Standard vs Custom Field, Quota, Institution Allocation.
