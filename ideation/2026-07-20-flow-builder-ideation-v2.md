# [2026-07-20] Flow Builder — full flow + gap audit (Phase 2) — **v2**

> Standalone ideation doc. Supersedes
> `2026-07-20-flow-builder-ideation.md` (v1, kept as historical
> record). This is exploratory — nothing here is committed to
> `atom-context.md` or `backlog.md`. Section refs (§) point at
> `context/atom-context.md`. Inputs to v2: (a) the user's explicit
> decisions overriding v1 D2/D9; (b) a brutal peer review of v1; (c) a
> walkthrough of the user's click-through prototype
> (`flow-architect-24.lovable.app`, "37th National Games"), which is
> materially richer than the original `atom-flow-builder.md` draft and
> is treated here as the primary UX reference.

**Status:** **All 13 sign-off decisions (D1–D13) resolved/confirmed
2026-07-21** (see §M.0–M.2). Post-signoff, Information Architecture for
the Registration & Quota Setup tab (§P) continues node-by-node: **D14**
(2-tab, 5-fixed-node structure), **D15** (Sports Setup node), **D16**
(Nomination node), **D17–D21** (Registration Configuration node, resolved
2026-07-31), and **D22–D28** (Quota Setup node, resolved 2026-07-31) are
resolved. **Still parked/undesigned:** Registration Dashboard node
contents only (§P.6). Committing the agreed model into
`atom-context.md`/`backlog.md` (§O) is still not done — awaiting explicit
go-ahead.
>
> **⚠️ Superseded 2026-08-18 — D15 and D17's authoring models are
> reversed.** See
> `ideation/atom-flow-builder-sports-registration-setup.md`
> (confirmed intentional pivot by Abhijeet): **Sports Setup is now the
> free-text authoring canvas** (Industry creates Sport/Category/Event by
> name; Event Type is an Industry-picked field with a third value,
> "Double," added), and **Registration Setup (renamed from "Registration
> Configuration") is now enable-only** over a system-defined User Type
> list (only Sub-Type remains free-text/Industry-created). D18–D21
> (Sub-Type shape, leaf-only forms, standard field palette, Publish
> semantics) are unaffected — they describe the Sub-Type/form layer,
> which didn't change. The 5-node sequence and Registration Dashboard
> (D14) are unaffected — still 5 nodes; the new doc just hasn't reached
> Dashboard yet.

---

## A. What changed from v1 (orientation)

1. **Quota is decoupled from Functional Area — confirmed by the user and
   by the prototype end-to-end.** v1's D9 ("scope quota to the granted
   FA") is **withdrawn.** Quota is a second, independent grant an
   Institution's Project participation can carry alongside (or instead
   of) an FA grant — **no new "Contingent" entity needed** (§C, resolved
   2026-07-21).
2. **Nomination is designed in, not flagged as TBD.** The prototype's
   Stage 6 ("Sport Registrations") *is* nomination. §D makes the call.
3. **B.1 is downgraded** from BLOCKING to a spec-completeness gap — the
   prototype resolves most of it (FA is a first-class canvas node; Role
   lives in the invite modal). The genuine structural issue B.1 walked
   past is **tree-depth mismatch** (§K).
4. **v1 D5 ("hard-block destructive edits") is a category error** and is
   reframed as **managed teardown vs. hard block** (§H).
5. **The data model (v1 §E) only modeled Athlete-shaped quota.** The
   prototype has **three parallel quota tracks** (Athlete / Officials /
   SSV) and a **per-Event-per-State Quota + Reserve + Active** shape.
   §E rebuilds it.
6. **New domain decisions** the v1 12-decision set missed entirely:
   wildcard-vs-cap, multi-event athletes, waitlist promotion,
   DQ/withdrawal/replacement, unused-slot reclamation (§G).
7. **Sport-taxonomy self-contradiction** in the original draft (Node 1
   vs Node 3) is resolved into one canonical taxonomy (§F).
8. **D4/D11/D12 demoted** to derived constraints; **D2+D9 merged**; two
   missed backlog tie-ins added (§N).

---

## B. The prototype as ground truth (structure actually observed)

**Hierarchy tab** — a single canvas with a Node Library of **three peer
node types: Functional Area, Institution, Individual.**
- **FA node** (e.g. "Accommodation," "12 institutions linked"): has a
  description, an **FA Head** field, and **Notification Recipients**
  checkboxes (FA Head / Assigned Institution Admins / Assigned Industry
  Users / Assigned Individuals / Project Managers).
- **Institution node:** ID, "Assign Existing Institution," Status
  ("Onboarded"), "Invite Institution" action, and **"Open sub-canvas"**
  → a nested canvas scoped to that Institution (breadcrumb "Hierarchy >
  ABC Hotels Group") where its own child institutions/individuals are
  added. **The sub-canvas is real, not speculative.**

**Quota Workflow tab** — a **6-stage branched** sequence (NOT the linear
"Sports → Registration → Quota" v1 assumed):
1. **GTCC** (Games Technical Conduct Committee) — a governance-body
   config node (Institution, Primary Contact, Email, Status: Published).
2. **Quota Config 1** ("Overall quotas, officials, SSV") with sub-tabs:
   **Setup Overall Quota** (per Event+Gender: Max Quota, Min Quota, Event
   Type [Individual/Team], Max Team, **Reserve Quota** — the project-wide
   ceiling before any contingent split), **Overall Staff Quota for
   Officials** (a separate track), **SSV Quotas** (State Sports Volunteer
   per-sport number + enable/disable toggle — a third track), and
   **Registration Status** (a cross-cutting dashboard filterable by
   State/Sport/Gender, State×Sport×Event×Gender rows with Quota, Reserve,
   Total-Entries progress).
3. **NSF Admin** (National Sports Federation admin) — federation contact
   config, parallel to GTCC.
4. **Per-sport branches** — one node per sport (Swimming NSF / Athletics
   NSF / Boxing NSF) + "Add Sport NSF." Workflow branches here.
5. **Quota Config 2 · [Sport]** — "Per-sport NSF **state** allocation."
   Each Event card ("100m Freestyle · Men," "4x100m Relay · Mixed") lists
   per-**State** rows: state name, Active/Off pill, **Q** (quota) and
   **R** (reserve) fields, Toggle to activate/deactivate a state for that
   event. **This is the distribution layer, keyed to State — never to
   FA.**
6. **Sport Registrations** — "Athlete intake by state." Per sport, per
   state, a progress bar ("Maharashtra 3/5"); "Manage Registrations"
   opens a modal: **Search Existing User** (name / Aadhaar, "Select") or
   **Manual Registration** → "Register Athlete." **This is Nomination.**

Two structural facts drop out immediately: **(i)** quota's distribution
axis is the **State/contingent**, confirmed end-to-end, and **(ii)** the
Quota Workflow is **governance-heavy and National-Games-flavored**
(GTCC, NSF, SSV), which is in direct tension with the draft's own design
principle "avoid hardcoded National Games workflows / be generic" (draft
lines 824–826). That tension drives D4.

---

## C. Quota is a grant on the Institution — no new primitive needed (D1, merges v1 D2+D9) — **RESOLVED 2026-07-21**

**Decision (user-confirmed, revised from the initial proposal):** quota is
not a new entity's problem — it's a **grant that attaches to an
Institution's existing Project participation.** Any Institution already
participating in a Project (§3.1, §5.5) can be granted a **quota
allocation**: a cap, per User Type (and Event/Category where applicable),
on how many Individuals it may add or invite into the Project. It cannot
add more of that type than the quota allows. This is genuinely generic —
it is not specific to "a State in a National Games," it is a property any
Industry can attach to any participating Institution.

**What this replaces:** the original D1 proposal (a new "Contingent"
primitive/entity, with a `ProjectParticipation.capacity ∈ {Executor ·
Contingent · both}` enum) is **withdrawn**. There is no new entity. §5.5's
Institution and §7.7.3's FA-grant machinery are untouched.

**Revised model:** an Institution's `ProjectParticipation` record can
carry **two independent, optional grants**, which may coexist on the same
Institution:
- **FA grant** (existing, §7.7.3, unchanged) — Institution executes a
  Functional Area.
- **Quota grant** (new) — Institution is capped, per User Type ×
  Event/Category, on how many Individuals it may add.
An Institution can hold an FA grant only, a quota grant only, both, or
neither (plain participant).

**Terminology note:** the word **"Contingent"** is retained elsewhere in
this document (§D, §G, §H, §J, §K) as **informal domain shorthand** for
"the Institution holding a quota grant" — e.g. how a National Games
Industry would label it in its own UI. It is not a system-level entity or
field name. Any Industry can rename this in its own vocabulary (Team,
Chapter, Franchise, Roster). Where the doc says "per Contingent," read it
as "per quota-granted Institution."

**Consequence — kills the D1 sub-question.** There is no
first-class-entity-vs-free-label choice to make, because there was never
a new entity to create. The prototype's free-typed "State" string was a
mockup shortcut for *the Institution record already in the Hierarchy
tab*, not evidence of a missing primitive.

**Bonus simplification this unlocks:** the quota-granted Institution
already has a real workspace (it's an Institution, §5.5) — so "who logs
in to manage nomination for a quota-holder" (§D's open question) is
answered for free: **their existing Institution workspace**, scoped by
the quota grant. No new workspace concept needed.

**Cross-reference:** this genericity argument — don't hardcode a
National-Games-specific noun into the data model — is the same spirit as
D4's GTCC/NSF/SSV concern. Worth resolving D4 with the same lens.

---

## D. Nomination — what it is and whether it's in v1 scope (D2) — **RESOLVED 2026-07-21: adopt as recommended**

**What it is:** the act by which a contingent **fills its allocated
per-Event quota with specific named athletes** — the prototype's Stage 6
("Sport Registrations" → Manage Registrations → Search Existing User /
Manual Registration → Register Athlete), one athlete at a time, capped by
the allocated Q for that Event×Category×Contingent.

**Is quota-without-nomination half a feature?** Yes — the reviewer is
right. A published Quota is *a number nobody can fill*; the value only
materializes when a contingent nominates against it. Shipping Quota Setup
in a v1 that cannot be filled would demo as an empty spreadsheet.

**But does the *filling* belong inside Flow Builder, or downstream?**
This is the module-boundary call (§8/§11). Two candidate homes:
- **(a) Inside Flow Builder** (as the prototype shows) — Flow Builder
  owns both quota authoring *and* nomination-against-quota.
- **(b) Downstream** — Flow Builder publishes quota as a config artifact;
  a separate **Registration/Join module** does the actual nomination and
  merely *validates against* the published quota.

**Recommendation: split by role, lean (b) for the participant-facing
act, but include a minimal nomination surface in Flow Builder v1.**
Reasoning against §8/§11:
- Flow Builder is an **Industry-workspace authoring tool**; nomination is
  performed by a **contingent admin** in a *different* workspace. Baking
  the participant-facing nomination flow permanently inside an
  Industry-authoring module blurs a workspace boundary (§6/§10) and
  duplicates the join/registration mechanics §7.7 already owns.
- The cleanest architecture: **Flow Builder emits published quota as a
  versioned config artifact** (per §D10-equivalent), and **nomination is
  a Registration/Join capability that consumes it** — this respects §11
  ("a module can't consume a config before it's active") and keeps §7.7
  as the single home for "how a participant ends up in a Project."
- **However**, to avoid shipping an unfulfillable Quota in v1, Flow
  Builder v1 **should include a thin "Sport Registrations" review/manual
  surface** (Industry can nominate/override on a contingent's behalf, and
  see fill progress) — matching the prototype — *while the
  self-service contingent-admin nomination flow is the downstream
  Registration module's job.* In other words: **the quota + the
  fill-progress view + Industry-side manual nomination are in v1;
  contingent-self-service nomination rides on the §7.7 Registration path.**

**Net answer to decision #3 (is Nomination necessary for v1?):** **Yes,
in the minimal form above.** Quota ships with a way to be filled (at
least Industry-side manual + progress), because quota-without-any-fill is
not a shippable feature — but the full self-service contingent nomination
experience is a downstream Registration-module responsibility, not new
Flow-Builder scope. This keeps v1 honest without absorbing §7.7's job.

---

## E. Data model v2 — three quota tracks + per-Event-per-Contingent shape

Extends v1 §E. New/changed entities and relationships:

**Sport taxonomy (canonical, see §F):**
- **Sport** (Athletics, Swimming) — global master, project-selected.
- **Discipline** *(optional grouping within a Sport)* — Track, Field.
- **Event** (100m, 100m Freestyle, 4×100m Relay) — the schedulable unit.
- **Category attributes on the Event** — **Gender** (Men/Women/Mixed),
  later Weight/Age. These are **attributes of an Event, not a tree
  level.** The **quota leaf = Event × Category.**

**Participation / Quota grant (revised, §C):**
- **ProjectParticipation** — (Project, Institution) with two independent
  optional grants: **FAGrant** (§7.7.3, unchanged — Institution executes
  a Functional Area) and **QuotaGrant** (new — Institution is capped per
  User Type × Event/Category on how many Individuals it may add). An
  Institution may hold either grant, both, or neither. No new entity —
  "Contingent" (used elsewhere in this doc) is shorthand for "the
  Institution holding a QuotaGrant," not a distinct primitive (D1).

**Quota — fully generic per User Type/Sub-Type, not a fixed set of tracks
(D5, resolved 2026-07-21):** the prototype's Athlete / Officials / SSV
screens are **illustrative artifacts of the prototype's own initial
ideation, not a fixed track set ATOM must reproduce.** A **QuotaTrack** is
an optional grant an Industry can attach to **any** User Type or
Sub-Type it has configured in Registration Configuration — not limited to
three, not named by ATOM. Each QuotaTrack carries its own **granularity**
(per Event × Category / per Sport / per Project — Industry's choice per
track). Athlete/Officials/SSV, if an Industry wants them, are just three
QuotaTracks it configures itself with those labels — same mechanism as
D1's Institution-labeling and D4's GTCC/NSF-labeling: **ATOM ships the
generic mechanism, the Industry supplies the vocabulary.**

**New requirement this implies — a Quota Track management surface:** Flow
Builder needs a screen (not three hardcoded tabs) that lists **every User
Type / Sub-Type configured in Registration Configuration** and lets the
Industry, per type: toggle "has a quota" on/off, and if on, set the
granularity and the Max/Min/Reserve fields (§ OverallQuota below). This
replaces the prototype's fixed "Setup Overall Quota / Officials Staff
Quota / SSV Quotas" sub-tabs with one generic, User-Type-driven list.

**Quota allocation, two levels (matches prototype Stages 2 & 5):**
- **OverallQuota** (project ceiling) — per QuotaTrack leaf: **Max, Min,
  Max Team, Reserve** are Industry-entered. **Event Type
  (Individual/Team) is read-only here**, inherited from the backend Sport
  master (§P.2) — not an Industry input at Quota Setup. (prototype "Setup
  Overall Quota")
- **QuotaGrant** — per (QuotaTrack leaf × Institution): **Q (quota), R
  (reserve), Active/Off.** Σ QuotaGrant.Q ≤ OverallQuota.Max; Σ R ≤
  OverallQuota.Reserve (hard block — Budget §8.5 pattern, applied to
  *fungible numbers only*, see §H). (Renamed from "ContingentAllocation"
  — per §C it's keyed to the Institution, not a separate Contingent
  entity.)

**Nomination (new, §D):**
- **Entry** — (Institution [quota-granted], Event, Category, QuotaTrack,
  Individual, status ∈ {Nominated · Confirmed · Reserve/Waitlisted ·
  Withdrawn · DQ}). An Individual may hold **many Entries** (multi-event,
  §G). Each Entry consumes one slot of its (Event × Category ×
  Institution) QuotaGrant.

**Governance directory — withdrawn (D4, resolved 2026-07-21):** no
`OversightBody` entity. GTCC/NSF/SSV are plain Institution nodes, labeled
by the Industry in the Hierarchy tab — see §I.

**Registration form (unchanged from v1, one correction):** the draft's
singular `Sport`/`Discipline` fields on the athlete form are **wrong** —
an athlete's event participation is an **Entry (per-event), not a single
form field** (§G multi-event). Standard-vs-custom field split and the
deferred profile pre-fill (v1 D8) are unchanged.

---

## F. Sport taxonomy — resolving the draft's self-contradiction (D7) — **RESOLVED 2026-07-21: adopted as proposed**

The original draft contradicts itself: **Node 1** says Sport → Event →
Discipline with `Discipline = Men/Women` (Athletics → 100m → Men/Women).
**Node 3's** left-nav implies Sport → Discipline → Event → Gender
(Athletics → **Track** → 100m Men, gender derived). The prototype's quota
cards ("100m Freestyle · Men," "4×100m Relay · Mixed") and Registration
Status rows (State × Sport × **Event** × **Gender**) settle it.

**Canonical resolution:** `Sport → [Discipline (optional grouping)] →
Event`, with **Gender/Category as an attribute of the Event, not a tree
level.** Node 1's error was labeling **Men/Women as a "Discipline"** —
they are a **Category attribute**. "Track/Field" is the real (optional)
Discipline grouping. The quota/nomination **leaf is Event × Category**.
This reconciles Node 3's nav *and* the prototype in one model, and it is
what §E is built on.

---

## G. Quota semantics — five explicit decisions the v1 set missed (D6) — **RESOLVED 2026-07-21: D6a–d adopted as proposed, D6e deadline-gated**

**D6a — Wildcard vs hard cap (the draft contradicts itself).** The draft
lists "Wildcard Athlete" as a SubType **and** makes quota Max a hard
validation cap — so the draft's own wildcard entry would be rejected by
its own rule. **Resolution: use the prototype's Reserve (R) field.**
Wildcard/discretionary entries draw from the **Reserve pool**, which sits
**above** the regular Max allocation. Max is a hard cap on *regular*
entries; Reserve is a separately-capped discretionary pool. A wildcard is
a Reserve-funded exception, not a Max violation. No contradiction remains.

**D6b — Multi-event / multi-sport athletes.** Real athletes enter several
events; the draft's singular Sport/Discipline form field cannot express
it. **Resolution: quota is consumed per (Event × Category × Contingent)
Entry, independently.** One athlete entering 100m and 200m holds **two
Entries** and consumes **one slot in each** event's allocation. Nomination
is per-Entry, an Individual has N Entries (§E).

**D6c — Waitlist / Reserve promotion.** Undefined in the draft.
**Resolution:** Reserve (R) is an **ordered waitlist per (Event ×
Category × Contingent).** When a confirmed slot vacates (D6d), the next
Reserve Entry is eligible for promotion. **Recommend manual promotion by
the contingent admin** (mirrors nomination being a deliberate,
one-at-a-time act), Industry-visible and Audit-logged (§10). Auto-promote
is a fast-follow.

**D6d — DQ / withdrawal / replacement after quota fills.** Undefined, and
it collides with the edit policy (§H). **Resolution: participant-lifecycle
events are handled by nomination management (Stage 6), NOT by editing
quota config.** Withdraw/DQ sets the Entry status → the slot reopens →
Reserve promotion (D6c) or fresh nomination fills it. This is deliberately
kept *out* of the "edit quota numbers" path so a withdrawal never requires
touching the OverallQuota/QuotaGrant figures.

**D6e — Unused-slot reclamation — DECIDED: deadline-gated.** Distinguish
**allocated-but-unused** (a quota-holder got Q=3, filled 1) from
**never-allocated** (project ceiling not yet distributed). The Industry
can **reclaim allocated-but-unused quota** from a quota-holder back to the
unallocated pool (mirrors Budget §8.5's "Unallocated"), distinct from
reducing the OverallQuota ceiling. **Timing: reclamation is gated to open
only after a nomination deadline** (Industry-configured per Project/Event)
— quota-holders are never raced by the Industry reclaiming slots
mid-nomination-window.

---

## H. Edit-after-registration — deferred to v2, flagged as risk (D3) — **RESOLVED 2026-07-21: out of v1 scope**

**Decision (user-confirmed):** Flow Builder **v1 ships without an
edit-after-registration policy.** No hard-block on fungible number
reductions, no managed-teardown workflow for structural changes. Config
edits behave like any other config edit — nothing in v1 distinguishes
"this Event already has live Entries against it."

**Why this was deliberately punted, not missed:** the original "hard-block
everything" framing (mirroring §8.5) is a category error — you can't
forbid cancelling an event or merging a category just because athletes
registered, the real world does this routinely. Designing the *right*
policy is real scope v1 doesn't need to carry to ship a usable Quota +
Nomination flow.

**Flagged risk — carried forward to v2, not resolved:** without a policy,
v1 permits an Industry edit that **silently orphans or corrupts live
nomination data** — e.g. reducing Max below already-confirmed Entries,
deleting an Event with live registrations, removing a User Type mid-flow.
**This is an accepted, explicit v1 risk**, to be closed by the policy
design below in **v2**. Cheap interim mitigation worth considering for v1
(not a substitute, doesn't require sign-off now): a passive warning in the
UI ("N athletes are registered against this Event") before a destructive
structural edit, without blocking it.

**V2 design — preserved for later (not built now):** split config changes
into two classes:
- **Fungible allocation numbers** (OverallQuota Max/Reserve, QuotaGrant
  Q/R): **hard-block reducing below already-consumed** — the correct §8.5
  analog. You cannot set Max = 2 when 3 are confirmed.
- **Structural / participant-lifecycle changes** (cancel an Event, merge
  categories, remove a Sport or User Type after nominations exist): **NOT
  hard-blockable** → a **managed-teardown workflow**: mark the element for
  teardown → **notify affected quota-holders/registrants** → **reassign /
  waitlist-promote / release affected Entries** → **Audit** (§10). Same
  machinery as D6d, generalized from one Entry to a whole config element.

Full version history/rollback stays out of scope regardless of phase.

---

## I. GTCC / NSF Admin — generic Institutions, Industry-labeled (D4) — **RESOLVED 2026-07-21**

**Decision (user-confirmed):** GTCC, NSF Admin, and SSV are **not** a new
entity or a new "Oversight Body" primitive — they are **plain
Institutions** participating in the Project, the same primitive used
everywhere else in this doc (§C/D1). An Industry building a National
Games hierarchy simply **labels** the Institution nodes it creates "GTCC,"
"NSF — Swimming," etc. in its own Hierarchy tab. There is no system-level
`OversightBody` type, no dedicated config screen, and no hardcoded
National-Games vocabulary anywhere in the data model.

**What this replaces:** withdraws the proposed `OversightBody` entity
from §E. No new entity, no new stage — the "governance directory" is
just ordinary Institution nodes on the canvas.

**Consequence for genericity:** this resolves the D4 tension the same way
D1 resolved the Contingent tension — GTCC/NSF/SSV never existed as ATOM
concepts, they're an Industry's own naming of ordinary Institution nodes.
A club league never sees "GTCC" anywhere in the product; a National
Games Industry just names its own nodes that way.

**SSV's "per-sport enable/disable" behavior needs no special-casing
either:** an Institution the Industry labels "SSV" that's also a
quota-holder is just a QuotaTrack (§E) scoped to a Volunteer User Type —
the same QuotaGrant machinery as any other track.

---

## J. Registration Status dashboard — build it inside Flow Builder (D8) — **RESOLVED 2026-07-21: adopted as proposed**

v1 correctly flagged **Schedule / Reports / Institution Dashboard** as
premature consumers (no such modules exist, §8/§14). But the prototype's
**Registration Status** view (cross-cutting, filterable Quota / Reserve /
Entries by State×Sport×Event×Gender) is genuinely useful and is exactly
the kind of thing that would otherwise **wait on a nonexistent Reports
module.** **Recommendation: build Registration Status as a view *inside*
Flow Builder** (a Quota Config sub-tab, as the prototype has it), reading
Flow Builder's own OverallQuota/QuotaGrant/Entry data — **not**
as a dependency on a future Reports/Institution-Dashboard module. This
keeps v1 self-contained and honors the v1 D10 "emit artifacts, don't
hard-wire nonexistent consumers" principle: the dashboard reads local
data, it doesn't presuppose an external module.

---

## K. Hierarchy tab reconciliation — B.1 downgraded, depth is the real issue (D9, D10) — **D9 RESOLVED 2026-07-21**

**B.1 downgraded (was BLOCKING → now Significant spec-completeness).**
The prototype resolves most of v1's B.1 alarm:
- **FA is a first-class canvas node** with an FA Head and Institutions
  "linked" to it — so the FA grant (§7.7.3) *is* represented, as an
  FA node participants attach to, not a missing field.
- **Role** for an Individual can live in the **invite modal** (§7.7.2),
  not necessarily on the node face — a spec-completeness detail, not a
  structural break. B.1's "the hierarchy authorizes no one" scenario
  overstated it.

**The real structural issue B.1 walked past — tree depth.** The draft's
`NF → State → District → Athlete` is a **4+ tier institution lineage**,
built via recursive sub-canvases. ATOM's FA structure is only **2 levels
(FA → Sub-FA, §3.1)**. These are **different axes** — the participation
lineage (nested Institutions, §5.5) is *not* the FA execution structure —
so they need not "map onto" each other. But two things are genuinely
undefined and need a call:
- **Sub-canvas nesting depth — DECIDED: max 1 level.** An Institution node
  may open exactly **one** sub-canvas of its own children; those children
  cannot themselves open a further sub-canvas. This replaces the
  prototype's unbounded recursion. **Consequence:** multi-tier federation
  lineages deeper than one nesting level (e.g. the draft's own `NF → State
  → District → Athlete`, a 3-tier Institution lineage) are **not** modeled
  via recursive sub-canvases. **Resolution:** represent deeper tiers as a
  **flat list of Institutions tagged by tier** (e.g. an Institution record
  carries a `tier` attribute — National / State / District — rather than
  being nested inside another Institution's sub-canvas). One sub-canvas
  level is for immediate children only; anything deeper is flattened with
  a tier tag, not further nesting.
- **Legal node parent/child edges — DECIDED:** an **FA node can parent
  both Institution and Individual nodes** (FA → Institution assignment,
  FA → Individual assignment). Institution → Institution nesting is
  limited to the one sub-canvas level above. This rules out an Institution
  or Individual node parenting an FA node — FA is always the parent in
  that relationship.

**Sub-canvas (D10, reframes v1 D3) — CONFIRMED 2026-07-21.** The
prototype shows the **Industry** authoring the sub-canvas from within Flow
Builder (breadcrumb-scoped to an Institution). That is
**Industry-authored**, which *weakens* v1's workspace-isolation objection
— the Industry authoring a participation structure is fine.
**Confirmed:** **Industry-authored sub-canvas is in v1 scope**; what
stays **deferred** is **delegating sub-canvas operation to the
Institution acting in its own workspace.**

**User note carried into D11:** Flow Builder will define **Institution-
level roles**, not just Individual roles (matching D9's FA → Institution
parenting) — so the §7.7.3 Phase-2 dependency this item names may in
practice get built *as part of* Flow Builder's own RBAC work (D11),
rather than waiting on a separate later phase. What's still open either
way: the **specific role/permission granularity** for an Institution
acting in its own workspace (e.g. can it edit its own sub-canvas, manage
its own QuotaGrant nominations, but not touch OverallQuota) needs to be
spelled out precisely — carried forward as a requirement into D11, not
resolved here.

**Still valid from v1 (kept):** the canvas must also show **apply-path**
joiners, not just invitees (§7.7 = two ways), and must support **Command**
(non-rejectable, §7.2) — these are **derived constraints** now (§M), not
open decisions.

---

## L. Phasing v2 (revises v1 §F)

**Phase 2 first cut (ship):**
- **Sports/Taxonomy** (Sport → [Discipline] → Event × Category), per §F.
- **Registration Configuration** (User Types + Sub Types + Form Builder,
  project-scoped fields only) — replaces §7.3's "Configure User Types /
  Dynamic Forms" steps (must **replace, not duplicate**; and note §7.3's
  "Configure Functional Areas" step is **absent** from Flow Builder — FA
  config still lives in the Hierarchy tab's FA nodes, wire it explicitly).
- **Quota Setup — fully generic per User Type/Sub-Type** (a Quota Track
  management screen driven by Registration Configuration's User Types,
  not a fixed Athlete/Officials/SSV set, per D5), two levels (OverallQuota
  + QuotaGrant), with Max/Min/Reserve/Event-Type/Max-Team.
- **Nomination — minimal (§D):** published quota + fill-progress +
  **Industry-side manual nomination**; contingent self-service nomination
  rides on the downstream §7.7 Registration path.
- **Registration Status dashboard inside Flow Builder** (§J).
- **Hierarchy tab** — FA/Institution/Individual nodes; **Industry-authored
  sub-canvas** (§K); Role in invite modal; apply + Command represented.
- **GTCC/NSF/SSV** — nothing to build; Industries label ordinary
  Institution nodes in the Hierarchy tab (D4, resolved — no dedicated
  stage).

**Deferred further (with the dependency forcing each):**
- **Contingent self-service nomination flow** → belongs to the downstream
  Registration/Join module (§D, §7.7), not new Flow Builder scope.
- **Institution-operated sub-canvas** (delegated, in Institution's own
  workspace) → depends on §7.7.3 Phase-2 Institution FA-scoped Roles
  (§K).
- **Standard-field → Individual Profile cross-project pre-fill** → needs a
  global profile-attribute schema that doesn't exist; conflicts with §7.6
  (v1 D8, unchanged).
- **Extensible quota constraints** (Age/Weight/Nationality/…) → ship
  Max/Min/Reserve/Participation-type + Gender only.
- **Auto waitlist promotion** (D6c) and **version history/rollback** →
  fast-follows.
- **Edit-after-registration policy** (fungible hard-block + managed
  teardown, §H) → deferred to v2; **flagged as an accepted v1 risk**
  (destructive structural edits can silently corrupt live nomination
  data) — v2 design preserved in §H, plus a cheap passive-warning
  mitigation worth considering for v1.
- **Schedule / Reports / Institution-Dashboard external consumers** → the
  modules don't exist (§8/§11); emit config artifacts, build the local
  Registration Status view instead (§J).

---

## M. Decisions

### M.0 Resolved this session [2026-07-21]

**D1 — Quota is a grant on the Institution; no new "Contingent"
primitive.** Withdraws the original proposal to add a new entity.
Instead, `ProjectParticipation` carries two independent optional grants —
**FAGrant** (existing, §7.7.3, unchanged) and **QuotaGrant** (new: cap per
User Type × Event/Category on how many Individuals an Institution may
add). "Contingent" is retained elsewhere in this doc as informal
shorthand for "the quota-granted Institution," not a system entity.
Bonus: the quota-holder's management workspace is just its existing
Institution workspace — no new workspace concept needed. See §C.

**D4 — GTCC / NSF Admin / SSV are plain Institutions, Industry-labeled.**
No `OversightBody` entity, no new stage. An Industry running a National
Games project just names ordinary Institution nodes "GTCC"/"NSF"/"SSV" in
its own Hierarchy tab; ATOM has no concept of any of them. Resolves the
D4 genericity tension the same way D1 resolved it for "Contingent." See
§I.

**D2 — Nomination in v1 scope: adopt as recommended.** Flow Builder v1
ships a thin surface — published quota + fill-progress view +
Industry-side manual nomination/override. Full **self-service
nomination** (a quota-granted Institution's own admin adding its
athletes) stays a downstream Registration/Join-module responsibility
riding the §7.7 join/approval path, not new Flow Builder scope — kept
distinct from Flow Builder even though D1 resolves the "which workspace"
question, because the *actor* (participant Institution vs. Industry) and
the *module boundary* (§7.7 owns participant join) are the real reasons
for the split, not a technical blocker. See §D.

**D3 — No edit-after-registration policy in v1; deferred to v2 as a
flagged risk.** No hard-block, no managed-teardown workflow in v1 — config
edits are unprotected. **Accepted risk:** destructive structural edits
(cancel Event, remove User Type, etc.) can silently corrupt live
nomination data; closing this is v2 scope. The fungible-hard-block +
managed-teardown design is preserved in §H for v2. See §H, §L.

**D5 — Quota-track model is fully generic, not a fixed set.** The
prototype's Athlete/Officials/SSV screens were artifacts of its own
initial ideation, not a required track set. A **QuotaTrack** is an
optional grant an Industry attaches to **any** User Type/Sub-Type it has
configured, each with its own granularity (Event×Category / Sport /
Project). New requirement: Flow Builder needs a **Quota Track management
screen** driven by Registration Configuration's User Types — not three
hardcoded tabs. See §E.

**D6 — Quota semantics bundle: adopted.** (a) Wildcards draw from
**Reserve**, not Max; (b) quota consumed **per (Event×Category×
Institution) Entry**, athletes hold N Entries; (c) Reserve = ordered
waitlist, **manual** promotion in v1; (d) DQ/withdrawal handled by
**nomination management**, not by editing quota numbers; (e)
allocated-but-unused reclamation, **deadline-gated** (opens only after
the nomination deadline). See §G.

**D7 — Sport taxonomy canonical form: adopted.** `Sport → [Discipline] →
Event`, **Gender/Category = Event attribute, not a tree level**; quota
leaf = Event × Category. Resolves Node 1 vs Node 3 draft contradiction.
See §F.

**D8 — Registration Status dashboard lives inside Flow Builder: adopted.**
Reads local OverallQuota/QuotaGrant/Entry data directly, not a dependency
on a future Reports/Institution-Dashboard module. See §J.

**D9 — Hierarchy tab confirmed, plus two new decisions.** Confirmed:
authoring surface over §7.7, not a new primitive; FA is a canvas node
(grant represented); Role lives in the invite modal; B.1 downgraded to
spec-completeness. **Decided:** sub-canvas nesting capped at **1 level**,
deeper tiers use a **flat Institution list tagged by tier** instead of
recursive nesting; **FA node can parent both Institution and Individual**
nodes. See §K.

**D10 — Sub-canvas confirmed.** Industry-authored sub-canvas in v1 scope;
Institution-operated (own-workspace) sub-canvas deferred. **Note carried
into D11:** Flow Builder will define Institution-level roles (not just
Individual roles), so this dependency may get built as part of Flow
Builder's own RBAC (D11) rather than waiting on a separate phase — exact
role/permission granularity for an Institution's own workspace is still
open, carried into D11. See §K.

### M.1 — all spotlight decisions (D1–D8) resolved 2026-07-21. See §M.0.

**D11 — RBAC, expanded and signed off 2026-07-21.** Two RBAC surfaces,
not one:
1. **Industry-workspace roles** (original scope) — **Flow Builder
   Editor** (full authoring: Hierarchy tab, Quota Track config, Sport
   taxonomy, Registration Config, manual nomination/override, quota
   reclamation) and **Flow Builder Viewer** (read-only, incl. Registration
   Status dashboard).
2. **Institution-workspace roles** (new, follows from D1/D9/D10) —
   **FA Executor** (existing §7.7.3, unchanged: operates its granted FA's
   Tasks); **Quota-holder** (v1: view own QuotaGrant + fill-progress,
   read-only — self-nomination happens downstream via §7.7 per D2, not
   inside Flow Builder; cannot see other Institutions' data or edit any
   config); **Sub-canvas Operator** (defined now, **capability gated
   off** until D10's Institution-operated-sub-canvas dependency ships —
   when enabled: edit own sub-canvas, one level only per §K, cannot edit
   Hierarchy structure above itself).

**Default-deny, unchanged:** none of these roles are auto-granted by
holding an FAGrant/QuotaGrant — each requires an explicit RBAC
assignment. See §N for the backlog tie-in this adds.

**D12 — Confirmed 2026-07-21.** Standard-field → Individual Profile
pre-fill deferred; ship standard + custom fields as **project-scoped**
now (needs a nonexistent global profile schema; conflicts §7.6).

**D13 — Confirmed 2026-07-21.** Flow Builder emits versioned config
artifacts; downstream consumers subscribe if present + licensed
(§8.4/§11). Don't hard-wire Schedule/Reports/Institution-Dashboard.

### M.2 — all decisions in this tier (D9–D13) confirmed 2026-07-21.

### M.3 Derived constraints — NOT open decisions (demoted, per reviewer)

- **Node ordering** (Sports → Registration → Quota → Nomination) — forced
  by data dependencies, not a choice (was v1 D4).
- **Sports come from a backend-fixed master taxonomy, enablement-only**
  (revised 2026-07-21, was "master palette seeds project-scoped
  instances" — that phrasing implied an editable copy, which is wrong for
  Sports; see §P.2). Whether **User Types** (Registration Configuration
  node) follow the same enable-only pattern or allow Industry-authored
  custom types is **RESOLVED 2026-07-31: Industry-authored, NOT
  enable-only** (there is no backend User Type master to enable against —
  see D17/§P.4).
- **Command is non-rejectable and must be representable** on the canvas —
  forced by §7.2/§12 (was v1 D12).
- **Apply-path joiners must appear on the canvas** — forced by §7.7 (was
  v1 B.2).

---

## N. Backlog overlap check (three tie-ins added — two per reviewer, one from D11 expansion)

- No backlog item covers Sports/registration-forms/quota — **no
  duplication**; Flow Builder is net-new.
- **2026-07-09 "RBAC matrix must cover §8.2 modules / dynamic"** — D11.
- **2026-07-09 "Define role-scoping level (Project vs Industry
  template)"** — D11 (Flow Builder wants Industry-template scoping).
- **[ADDED] 2026-07-09 "'Assign User' must respect the Project
  join/approval gate"** — **directly relevant**: the Hierarchy tab's
  invite action is exactly the surface that item warns can bypass §7.7's
  apply/invite/approval gate. Flow Builder invites **must** route through
  §7.7, not admit participants sideways.
- **[ADDED] 2026-07-09 "Granular permissions / promote 'Assign FA Owner'
  to first-class permission"** — relevant to D11: the Hierarchy tab's FA
  node assigns FA ownership (the prototype's "FA Head"), which that item
  says should be a first-class sensitive permission (§7.7.3/§11).
- **[ADDED 2026-07-21] "Assign Quota-holder role" should be a first-class
  permission** — same treatment as "Assign FA Owner" above, but for D11's
  new Institution-workspace **Quota-holder** role (granting the QuotaGrant
  view + eventual Sub-canvas Operator capability is a sensitive act,
  same tier as granting FA ownership).
- **§8.5 Budget Tracking** "reduce-below-committed = hard block" — reused
  in D3 **for fungible numbers only** (precedent, not conflict).

---

## O. Downstream doc impact (when committed — not editing now)

- **§3.1 / §7.4 / §7.7.3** — add **QuotaGrant** as a second optional
  grant on `ProjectParticipation`, independent of the existing FA grant;
  FA ownership mechanics are unchanged (D1).
- **§7.3 / §5.4 / §7.6** — Flow Builder's Registration Configuration node
  (§P.4) is the authoring surface for User Types + Sub-Types + Forms, and
  **replaces** §7.3's two separate "Configure User Types" + "Configure
  Dynamic Forms" steps by **merging them into one node** (must replace,
  not duplicate); §7.6 should name this node as the "defines User
  Types/Forms" surface. Note FA config lives in the Hierarchy tab's FA
  nodes, not this tab.
- **§7.7** — Hierarchy invites route through the join/approval gate;
  Nomination-vs-Registration boundary (D2).
- **§8 / §8.2** — Flow Builder as a module + its emitted config artifacts;
  local Registration Status view (D8). No governance-directory feature
  (D4, resolved — GTCC/NSF/SSV are Industry-labeled Institutions).
- **§10 / §11** — new Flow Builder RBAC; sub-canvas isolation rule;
  Sports→Registration→Quota→Nomination ordering. Fungible hard-block +
  managed-teardown (D3) explicitly **not** in v1 — log as a known risk in
  §14 Roadmap, revisit for v2.
- **§12** — quota semantics (D6), taxonomy (D7), contingent allocation is
  parent-sets-child (echoing §8.5).
- **§14 Roadmap** — Flow Builder first cut in Phase 2; deferrals per §L.
- **§15 Glossary** — QuotaGrant, ProjectParticipation, QuotaTrack, Entry,
  Reserve, Sport/Discipline/Event/Category ("Contingent"
  noted as informal domain shorthand, not a glossary entity, per D1).
  **Add "Sub-Type"** — a net-new concept introduced by Registration
  Configuration (§P.4/D18), with no §15 entry today; define it as the
  optional one-level child of a User Type that carries the joining Form
  when present (leaf-only rule).

---

## P. Registration & Quota Setup tab — node structure (post-signoff additions, 2026-07-21)

All 13 sign-off decisions (D1–D13, §M.0–M.2) are closed. This section
captures Information Architecture decided **after** sign-off, in the same
session — numbered D14+ for traceability, same rigor as D1–D13.

### P.1 Tab structure (D14 — resolved 2026-07-21)

Flow Builder has **two tabs**:
1. **Hierarchy** — unchanged from §K/D9/D10 (FA/Institution/Individual
   nodes, 1-level sub-canvas, FA can parent Institution/Individual).
2. **Registration & Quota Setup** — holds **five fixed nodes**, in a
   forced sequence (matches the existing node-ordering derived constraint,
   §M.3): **Sports Setup → Registration Configuration → Quota Setup →
   Nomination → Registration Dashboard.** GTCC/NSF/SSV do **not** get a
   node (D4 — they're plain Hierarchy-tab Institutions).

**Node mechanics:** each of the five nodes starts **Pending**. Clicking a
node opens a **dedicated page/canvas** for that node's configuration
(not an inline panel). Once configured, the Industry **Publishes** the
node, flipping its status to **Published** — which is what unlocks the
next node in sequence (the ordering constraint is enforced as a literal
UI gate, not just a data dependency).

### P.2 Sports Setup node (D15 — resolved 2026-07-21; **superseded 2026-08-18**)

> **Superseded — see `atom-flow-builder-sports-registration-setup.md`.**
> Sports Setup is now a free-text **authoring** canvas (Sport Name, Sport
> Logo, Category Name, Event Name all Industry-entered; Event Type is an
> Industry-picked field, now with three values Individual/Double/Team).
> The backend-fixed-taxonomy/enable-only model below no longer applies.
> Confirmed as an intentional pivot, traded with D17 below.

**Not an authoring canvas — a two-step enablement flow over a
backend-fixed master taxonomy.** The full Sport → Discipline → Event
tree, **including each Event's Gender/Category variants and Event Type
(Individual/Team), is pre-mapped in the backend.** The Industry never
names, creates, or edits a Sport/Discipline/Event/Category/Event-Type —
it only **enables** the backend-defined combinations it wants active for
this Project. (v1 has no "request a new Sport" escalation path — out of
scope, flagged for later if it comes up.)

**Two-step flow:**
1. **Enable Sports** — multi-select picker over the backend's full Sport
   master list (Athletics, Swimming, Boxing…). No creation.
2. **Enable Discipline/Event per enabled Sport** — drill into a Sport to
   see its backend-mapped, **read-only** Discipline/Event tree; toggle ON
   only the specific Events (and their specific Gender/Category variants)
   this Project wants. Only toggled-ON Event×Category leaves become
   visible to Quota Setup and Nomination — nothing else exists for this
   Project.

**Canvas layout:**
- **Left rail:** enabled Sports with an enabled-event count (e.g.
  "Athletics — 8/14 events enabled"); "+ Enable Sport" opens the backend
  picker.
- **Main panel:** selected Sport's read-only Discipline/Event tree; each
  Event row expands to show its backend-fixed Category variants (Men /
  Women / Mixed chips) and a read-only Event Type badge
  (Individual/Team), each variant with its own enable toggle. Bulk
  "enable all in this Discipline" shortcut.
- **No free-text fields anywhere** — every label is backend-sourced.

**Node status:** Pending until ≥1 Event×Category leaf is enabled;
Publish unlocks Registration Configuration next.

**Correction this makes to §E:** OverallQuota's **Event Type
(Individual/Team)** field is **not** an Industry input at Quota Setup —
it's read-only, inherited from this node's backend Event data. **Max
Team** (the squad-size cap) remains Industry-entered at Quota Setup, since
it's a quota decision, not an intrinsic Sport fact.

### P.3 Nomination node (D16 — resolved 2026-07-22)

Two sub-questions parked at end of 2026-07-21 session are now resolved:
- **Industry-manual nomination lands as Entry status `Confirmed`
  immediately** — no separate pending/approval step. Consistent with the
  fact that this is an Industry admin acting directly (not a
  contingent-admin self-service submission that would need review).
- **Not Sport/Event/State-filterable like the Dashboard node** — the node
  is entered *through* quota context, not via a broad cross-cutting
  filter, so a Dashboard-style filter bar would be redundant (see
  navigation model below).

**What the node is:** the thin, Industry-side manual nomination + fill-
progress surface scoped in §D — *not* the full self-service
contingent-admin nomination flow (that's downstream §7.7 Registration
module territory, out of Flow Builder v1).

**Navigation model (why no broad filter):** entry is drill-down, not
search-first —
1. **Left rail:** Institutions holding a **QuotaGrant** (i.e. the
   "contingents"), each showing an aggregate fill indicator (e.g.
   "Delhi Athletics Assoc. — 34/50 slots filled"). **This rail can
   legitimately start empty** — per Quota Setup's ceiling-only Publish
   (§P.5/D26), Quota Setup may Publish with zero QuotaGrants and the
   Industry grants to Institutions later as they confirm participation.
   Nomination therefore does **not** assume Quota Setup has pre-seeded any
   contingents; an empty rail is a valid pending state, not an error.
2. **Selecting an Institution** opens its QuotaGrant leaves (QuotaTrack ×
   Event × Category, per §E), each showing **Q allocated / Confirmed
   count / R reserve** — this *is* the Sport/Event context, scoped
   naturally to what that Institution was actually granted, so a global
   filter adds nothing here (unlike the Dashboard, which surveys *all*
   Institutions at once and needs one).
3. **Selecting a QuotaGrant leaf** opens the nomination surface for that
   specific (Institution, Event, Category, QuotaTrack) slot: **Search
   Existing User** or **Manual Registration** (matches prototype Stage
   6), capped at that leaf's remaining `Q − Confirmed` count.

**Hard cap, no auto-waitlist in v1:** once `Confirmed = Q` for a leaf,
further nomination on that leaf is **blocked**, not shifted to
`Reserve/Waitlisted` — waitlist promotion logic is a self-service-flow
concern (§7.7) and stays out of this thin surface, consistent with §D's
scope line. `R` (reserve) is a separate Industry-set ceiling, not
auto-populated by overflow.

**Explicitly out of scope for this node in v1 (flag, don't design now):**
Entry status transitions to `Withdrawn`/`DQ`/`Reserve` after a Confirmed
nomination — this is post-registration editing, same family of concern
as **D3's "no edit-after-registration policy in v1"** (§H), so it
inherits that deferral/risk rather than getting new scope here.

**Node status:** Pending until ≥1 Entry is Confirmed; Publish is
available but not gating anything downstream (Nomination is the last
node before Dashboard, which just reads live data — no further node
depends on Nomination being "Published" the way earlier nodes gate each
other, so Publish here is a completion marker, not a functional gate).

### P.4 Registration Configuration node (D17–D21 — resolved 2026-07-31; **D17 superseded 2026-08-18, renamed "Registration Setup"**)

> **D17 superseded — see `atom-flow-builder-sports-registration-setup.md`.**
> Registration Setup (renamed from "Registration Configuration") is now
> **enable-only**: User Type comes from a system-defined list; the
> Industry cannot create a new User Type, only enable one and add
> Sub-Types under it (Sub-Type creation is still free-text — D18–D21
> below are unaffected). This is the inverse of D17's rationale and was
> confirmed as an intentional pivot, traded with D15 above.

Second of the five fixed nodes. Unlocked by Sports Setup Publishing;
gates Quota Setup. Resolves the enable-only-vs-authored question left open
in §M.3.

**D17 — Authoring model: Industry-authored, NOT enable-only (the inverse
of Sports Setup).** Sports Setup (§P.2/D15) is enable-only *because* its
full Sport→Discipline→Event→Category tree is pre-mapped in the backend;
there is **no equivalent backend master for User Types** anywhere in
canon. §7.6 states the Industry "**defines** User Types, Registration
Forms, and Validation Rules **per Project** — forms are project-specific,
not global"; §15's glossary reaffirms "**Defined by the Industry per
Project.**" §E/D5 already assume User Types the Industry "**configured**,"
and D6a's "Wildcard Athlete" Sub-Type is a bespoke, project-specific
classification that could never live in a fixed backend list. So this node
is an **authoring canvas**: the Industry creates User Types and Sub-Types
by **free-text name** (the explicit inverse of §P.2's "no free-text fields
anywhere") and builds a form per leaf. ATOM ships the mechanism + a fixed
standard-field palette (D19); the Industry supplies the vocabulary and the
custom fields — the same "generic mechanism, Industry supplies the
vocabulary" spirit as D1/D4/D5.

**D18 — User Type ↔ Sub-Type shape + leaf-only form rule.**
- **Sub-Type is a child of a User Type, exactly one nesting level** (User
  Type → Sub-Type; no sub-sub-types) — matching ATOM's shallow-tree
  discipline (FA → Sub-FA 2 levels §3.1; Hierarchy sub-canvas 1 level,
  D9/§K).
- **Sub-Types are optional** — a User Type may have zero Sub-Types.
- **Leaf-only form rule (Abhijeet, 2026-07-31, verbatim):** *"If a User
  Type has sub-type in it, then user type will not have any form. Its
  sub-type will have the forms. If no sub-type has been added for the user
  type, then that user type will have joining form."* **Forms live at the
  leaf only — never at both levels, no inheritance, no stacking.** If a
  User Type has ≥1 Sub-Type, the User Type itself carries **no** form and
  each Sub-Type carries its own; if a User Type has zero Sub-Types, the
  User Type *is* the leaf and carries the form directly. (This supersedes
  the layered/inherited model floated pre-signoff — there is **no**
  "parent fields shown locked, then append" behavior, and **no**
  override/remove question, because nothing is inherited.)
- Both User Type and Sub-Type remain **first-class configured entities**
  (each is quota-attachable, D20), not cosmetic labels.

**D19 — Form Builder: locked standard palette + minimal validation.**
Each **leaf's** form is built from:
- **Standard field palette — FIXED for v1 (locked constant):** **Name,
  DOB, Gender, Contact Number, ID/Aadhaar Number, Photo.** Recorded as a
  locked constant rather than its own D-number — consistent with how D12
  recorded the standard/custom split without enumerating the standard set;
  this pins the v1 contents.
- **Custom fields** — Industry-authored, **project-scoped** (per D12),
  added via "+ Custom Field."
- **Validation primitives — minimal only for v1:** a **required** toggle +
  a **basic type constraint** (text / number / date / select / file).
  **No regex/format, no min/max, no uniqueness/dedup in v1.**
- **v1 risk flag (same family as D3's deferred edit-after-registration
  risk):** because the Form Builder enforces **no uniqueness/dedup**,
  Nomination's "Search Existing User" (Aadhaar-style dedup, prototype
  Stage 6 / §P.3) **cannot rely on form-enforced uniqueness in v1.** Dedup
  during search/nomination is a **separate, unscoped concern** — not
  solved by this node. Accepted, explicit v1 risk.

**D20 — Quota attach point: Industry's choice per User Type (NOT forced
leaf-only like forms).** Unlike forms (D18, leaf-only), the Industry
chooses **per User Type** whether a QuotaTrack (§E/D5) sits on the
**parent User Type** or is pushed down to its **Sub-Types.** Quota
therefore attaches at either level, per the Industry's decision —
consistent with §E's "any User Type **or** Sub-Type."
- **Inherited-context flag for Quota Setup (RESOLVED 2026-07-31 in
  §P.5/D22):** because a parent and its child can *both* carry a
  QuotaTrack, there is a live **double-counting risk** (a Sub-Type's slot
  counted against both its own and its parent's quota). The prevention
  mechanism was deferred to Quota Setup and is now resolved there: quota is
  consumed at **leaves only** (D18's leaf-only forms), so a parent
  QuotaTrack over Sub-Types is necessarily a §8.5 **roll-up ceiling**
  (Σ child Max ≤ parent Max), never an independent second counter. See
  §P.5/D22.

**D21 — Publish semantics + role-stays-out confirmation.**
- **Pending → Published preconditions:** ≥1 User Type exists, and **every
  leaf** (every User Type with no Sub-Types, and every Sub-Type) resolves
  to a form (minimum: the standard palette; no leaf may publish formless).
  A User Type that has Sub-Types whose Sub-Types lack forms cannot
  publish.
- **What Publish unlocks (Quota Setup):** the published set of **leaves**
  (User Types / Sub-Types) is the **row-source** for Quota Setup's Quota
  Track management screen (§E: "lists every User Type / Sub-Type
  configured in Registration Configuration"). Until Published, Quota Setup
  has an empty list and stays gated — the literal UI gate of D14/§P.1 and
  the concrete data dependency behind the Sports→Registration→Quota
  ordering (§M.3).
- **Role axis stays OUT (confirmed):** this node authors **User Type +
  Sub-Type + Form only.** Role (Project) is a distinct axis (§15) assigned
  **per-Individual at invite/join time** in the invite modal (§7.7.2,
  D9/§K), not a per-Project configuration — so it is not configured here.
  (The separate D11 Flow Builder RBAC roles are workspace-permission
  roles, a different concept, also not configured here.)

**Resolved-by-silence (recorded, not re-asked):** (i) User Types are
**Individual-facing only** — Institutions receive FA/Quota grants, not
User Types (§7.7.3/§C); there is no "Institution User Type." (ii) User
Type / Sub-Type names are **unique per Project.**

**Canvas layout (P.2/P.3 depth):**
- **Left rail:** authored **User Types**, each showing a Sub-Type count +
  a form/quota status chip (e.g. "Athlete — 2 sub-types" or "Coach — no
  sub-types · form: 6 fields"). "**+ Add User Type**" (free-text name) is
  the authoring entry point — free-text is expected here, the explicit
  inverse of §P.2's read-only backend labels.
- **Main panel (User Type *with* Sub-Types selected):** its **Sub-Types
  manager** (list + "+ Add Sub-Type"); the User Type itself shows **no
  Form Builder** (leaf-only rule, D18).
- **Main panel (leaf selected — a Sub-Type, or a User Type with zero
  Sub-Types):** the **Form Builder** — the fixed standard-field palette to
  drop in, plus "+ Custom Field" (label; type ∈ {text / number / date /
  select / file}; required toggle).
- **No locked/inherited-field display anywhere** — nothing is inherited
  (D18).

**Node status:** Pending until ≥1 leaf has a form and the node is
Published; Publish unlocks Quota Setup.

### P.5 Quota Setup node (D22–D28 — resolved 2026-07-31; **refined 2026-08-21, split into Level 1 / Level 2**)

> **Refined — see `ideation/atom-flow-builder-quota-setup.md`.** Quota
> Setup's canvas is split into two sequential stages, **Level 1 (Overall
> Quota)** and **Level 2 (Contingent Allocation)** — not a rename, a real
> canvas restructure. Mapping to the decisions below: **D24→Level 1**
> (same fields in spirit, now shaped per Event Type Individual/Double/Team,
> created via a guided flow instead of a pre-rendered grid since the
> applicable User Type isn't known in advance); **D25→Level 2** (same Q/R-
> per-Institution shape and `Σ Q ≤ Max`/`Σ R ≤ Reserve` hard block, now
> Institutions are added explicitly per event rather than pre-populated).
> **D26 is tightened, not preserved as-is:** ceiling-only Publish still
> works for Level 1 alone, but the *node* no longer unlocks Nomination on
> that basis — Level 2 must also fully roll up (every sport published),
> which requires real Min-satisfying grants. **D28's inferred mechanism is
> superseded** by a concrete, explicitly-confirmed one: Min is now a hard
> precondition for a sport's Level 2 Publish (`Σ Q ≥ Min` per entry, **no
> override/waiver** — a deliberate v1 choice that a sport can stay
> permanently blocked if institutions won't commit enough). **D23's
> User-Type-tree left rail is superseded** by a Sport-first left rail at
> both levels (matches Sports/Registration Setup's own left-rail
> convention). **D22's Sub-Type nested-ceiling mode is an open carry-
> forward, not resolved:** every Level 1/2 worked example used a flat User
> Type with no Sub-Type breakdown — whether a User Type *with* Sub-Types
> still needs D22's parent-pool/per-Sub-Type/both roll-up inside Level 1
> was not revisited. D24's custom-quota-fields affordance is likewise an
> open carry-forward. D27 (reclamation/waitlist live in Nomination) is
> unaffected.

Third of the five fixed nodes (§P.1/D14). Unlocked by Registration
Configuration Publishing (D21); gates Nomination. Its data model is
already fixed in §E (OverallQuota, QuotaGrant, QuotaTrack, granularity);
this section is the canvas/authoring design and the resolution of the D20
double-counting flag inherited from §P.4. Throughout, this node mirrors
**§8.5 Budget Tracking**'s Total / Allocated / Unallocated,
parent-sets-child, hard-block-reduce-below-committed convention, for
product-wide consistency (§8.5, §12, §N precedent — applied to fungible
slot counts only, §H).

**D22 — Double-counting resolution: nested-ceiling, quota consumed at
leaves only (the keystone).** Because D18 (§P.4) puts forms — and
therefore Individual classification and nomination — at the **leaf** only,
quota is only ever *consumed* at a leaf. A QuotaTrack on a parent User
Type that *has* Sub-Types is therefore necessarily a **roll-up ceiling**
(Σ Sub-Type OverallQuota.Max ≤ parent OverallQuota.Max, **hard block**,
the §8.5 rule), never an independently-consumed counter — which
structurally eliminates the D20 double-count: an Entry increments exactly
one leaf counter, and a parent counter is *definitionally* Σ(children), so
"counting the same athlete at both levels" is one count rolled up, not two
consumptions. Three legal configs for a User Type *with* Sub-Types (D5/D20
"parent **or** pushed down"), **one mode per User Type — no mixing**
(D-mixed resolved 2026-07-31):
- **Parent-pool** — QuotaTrack on the parent only; parent Max is a single
  **shared pool**; QuotaGrants + leaf Entries draw from it (a grant = "N
  slots fillable by any Sub-Type"). Here the parent's Unallocated remainder
  **is** directly consumable.
- **Per-Sub-Type** — QuotaTrack on each Sub-Type; each has its own Max, no
  parent ceiling.
- **Both** — parent Max = roll-up **ceiling** over Sub-Type sub-caps
  (Σ child Max ≤ parent Max, hard block); QuotaGrants attach at the
  Sub-Type leaves only; the parent gets **no** grants of its own. Here the
  parent's Unallocated remainder is **not** directly grantable — D18's
  leaf-only rule forbids parent-level Entries, so it is un-sub-allocated
  *headroom*, released only by raising a child's sub-cap. (This is the one
  place the quota tree tightens §8.5, where a parent's unallocated budget
  is always notionally spendable; it falls straight out of D18.)

A User Type with **no** Sub-Types is itself the leaf: quota, OverallQuota,
QuotaGrants and Entries all sit on it directly — no nesting, trivially no
double-count. **Transitivity property:** Σ QuotaGrant.Q ≤ child Max (§E,
Institution axis) and Σ child Max ≤ parent Max (tree axis) together imply
Σ(all grants) ≤ parent Max, so no third roll-up check is needed — §8.5's
"each level guards only its own children" discipline holds.

**D23 — Quota Track management surface (the §E screen). Canvas:**
- **Left rail — the published User-Type tree from Registration
  Configuration (D21 row-source):** each User Type shown with its
  Sub-Types indented one level (D18's one-level shape). Every node carries
  a **"has a QuotaTrack" toggle**; when ON it shows (i) a **granularity
  selector** — `per Event × Category / per Sport / per Project` (§E,
  Industry's choice per track) — and (ii) a **completeness chip** (e.g.
  "Max set on 6/8 leaves", or "ceiling 90/100 allocated"). The rail *is*
  the "list every User Type / Sub-Type configured in Registration
  Configuration" surface §E requires — it is **populated, not authored,
  here** (no free-text; new types are made only in the Registration
  Configuration node, D17). An empty published set leaves this rail empty
  and the node stays gated (D14/D21).
- Toggling a **parent** ON while its Sub-Types are also toggled ON puts
  that User Type into **both** mode and switches its panel to the roll-up
  view (D24). Mode is chosen **once per User Type** (no per-Sub-Type
  mixing — resolved 2026-07-31).
- **Main panel — two sub-tabs** (mirroring the prototype's Quota Config
  Stage 2 vs Stage 5, §B): **Overall Quota** (D24) and **Contingent
  Allocation** (D25).

**D24 — OverallQuota authoring: standard + custom quota fields. Main-panel
sub-tab "Overall Quota":** a grid whose **rows are the selected track's
granularity-leaves** — one row per enabled Event × Category (from Sports
Setup, §P.2), or per enabled Sport, or a single Project-level row, per the
track's granularity.
- **Standard quota fields (fixed columns):** **Max, Min, Max Team,
  Reserve** (Industry-editable); **Event Type (Individual/Team)** shown
  **read-only**, inherited from Sports Setup backend data (§P.2 correction
  — *not* an input here).
- **Custom quota fields (new, resolved 2026-07-31 — the D19 pattern
  applied to quota):** beyond the four standard columns, the Industry can
  add **custom quota fields per track** via a **"+ Custom Field"**
  affordance in this sub-tab, exactly analogous to D19's Registration Form
  Builder. Each custom quota field is **label + type**, type ∈ the same
  primitive set as D19 — `{text / number / date / select / file}` — with a
  **required** toggle, **project-scoped per track** (per D12/D19). It
  renders as an additional column on the granularity-leaf grid. Use case:
  Sport/Discipline/Event-specific constraints or reference data the four
  standard fields don't capture.
- **Custom fields do NOT participate in the D22 roll-up / hard-block
  math:** only **Max / Min / Reserve** are structurally load-bearing for
  the §8.5 roll-up ceiling and the Σ QuotaGrant.Q ≤ Max block (D25). Custom
  quota fields are **display / reference (informational) in v1** — their
  enforcement semantics were not specified and are **flagged open** (§P.5
  open items), not invented here.
- For a **parent in "both" mode**, this grid renders as the §8.5
  tri-column — **Total (parent Max) · Allocated (Σ Sub-Type Max) ·
  Unallocated** — and hard-blocks Σ Sub-Type Max > parent Max
  (parent-sets-child, §8.5). Reducing a parent Max below Σ committed
  Sub-Type Max, or a Max below already-granted Q, is *not* protected in v1
  (inherits D3/§H's accepted edit-after-registration risk — surface a
  passive warning, don't block).

**D25 — QuotaGrant authoring (per-Institution — prototype Stage 5).
Main-panel sub-tab "Contingent Allocation":** for the selected track, per
granularity-leaf, a list of **Institutions holding a ProjectParticipation**
(sourced from the Hierarchy tab, §K — the same Institution nodes;
"Contingent" is D1 shorthand), each row: **Q · R · Active/Off** toggle. A
per-leaf header shows the §8.5 tri-column **Total (OverallQuota.Max) ·
Allocated (Σ Q) · Unallocated**, and a parallel **Reserve: Total
(OverallQuota.Reserve) · Allocated (Σ R) · Unallocated**. Enforcement
(§E): **Σ QuotaGrant.Q ≤ OverallQuota.Max** and **Σ R ≤
OverallQuota.Reserve**, hard block. **Grants attach at whichever node
carries the track:** parent-pool → grants at the parent (a grant = N slots
fillable by any Sub-Type Entry); per-Sub-Type / both → grants at each
Sub-Type leaf; a "both"-mode parent ceiling gets **no** grants (D22).
Granting a QuotaGrant is the sensitive "Assign Quota-holder"-adjacent act
flagged in §N; visibility scoping (an Institution sees only its own grant)
follows §10.

**D26 — Publish semantics + Nomination hand-off (ceiling-only Publish
allowed).** **Pending → Published preconditions:** (i) ≥1 QuotaTrack is
toggled ON; (ii) every toggled-ON track has OverallQuota.Max set on
**every** one of its granularity-leaves (no partial ceilings — mirrors
D21's "every leaf resolves to a form"); (iii) every "both"-mode parent
satisfies Σ child Max ≤ parent Max. **No QuotaGrant is required to Publish
— ceiling-only Publish is explicitly allowed** (resolved 2026-07-31,
overriding the earlier "≥1 grant" proposal): the Industry may Publish
OverallQuota ceilings and grant to Institutions later as they confirm
participation. **Accepted v1 consequence:** Nomination (§P.3) can
legitimately open with an **empty left rail** (no Institutions holding a
grant yet) — this is a valid pending state, not the "empty spreadsheet"
failure, because the Industry adds grants downstream without re-editing
Quota Setup config. **What Publish unlocks:** the published **QuotaGrants**
(as and when they exist) become **Nomination's row-source** — §P.3's left
rail (Institutions holding a QuotaGrant) and its per-leaf **Q / Confirmed /
R** display read directly from the QuotaGrant + OverallQuota set here. Same
literal UI gate as every other node (D14/§P.1): until Quota Setup
Publishes, Nomination is gated; the Sports → Registration → Quota →
Nomination ordering (§M.3) is enforced as data dependency + UI gate.

**D27 — Runtime boundary: allocation-authoring surface only, not the
nomination/promotion runtime.** Per §D's Flow-Builder-vs-downstream split
and §P.3: **Reserve/waitlist promotion (D6c) is an Entry-level act and
lives in the Nomination node, not here** — Quota Setup authors the *R
ceiling*, it does not populate or promote the waitlist.
**Nomination/Confirmed counts are read-only inputs here**, produced by
Nomination. The one runtime action that *does* touch this node's data —
**deadline-gated reclamation of allocated-but-unused quota (D6e)** —
reduces a QuotaGrant.Q back to the Unallocated pool, so it surfaces on the
Contingent Allocation surface, but **only in a post-deadline "reclaim"
mode** that (a) is disabled until the Industry-configured **nomination
deadline** (a **Nomination-node** setting — resolved 2026-07-31) passes,
and (b) caps each reduction at `Q − Confirmed` (reads Nomination's live
fill state; never touches OverallQuota — reclamation ≠ lowering the
ceiling, per D6e). Reserve promotion and Entry status changes
(Withdrawn/DQ, D6d) never happen here.

**D28 — Min-quota enforcement (INFERRED mechanism — flag for correction).**
Abhijeet ruled Min is **enforced, not display-only**, but did not dictate
the mechanism; the following is reasoned from D22/D26/D27 and is flagged as
**inferred**, to be corrected if it misreads his intent.
- **Semantics:** Min is the **allocation floor** per OverallQuota
  granularity-leaf — the mirror of Max's ceiling. Target: **Σ
  QuotaGrant.Q ≥ OverallQuota.Min**. Enforcement is on **allocation (Σ Q)**,
  not fill (Σ Confirmed): Min parallels Max (both quota-allocation numbers),
  and ATOM cannot force athletes to exist, so a fill-floor would be
  unenforceable — a Σ-Confirmed "under-filled" signal is at most a
  Dashboard indicator, out of this node's scope.
- **Why it can't gate Quota Setup Publish:** ceiling-only Publish (D26)
  starts every leaf at Σ Q = 0, so any Min > 0 would trivially fail a
  Publish gate. Min therefore bites **downstream of Publish**, at two
  points:
  1. **Ratchet block (during the nomination window):** once a leaf's Σ Q
     has first reached Min, the node hard-blocks any grant edit that would
     drop Σ Q back below Min — the §8.5 "can't reduce below committed"
     discipline, applied to the floor. (Before Min is first reached, Σ Q
     builds up freely from zero — no block.)
  2. **Deadline block (at the Nomination-node nomination deadline, D27):**
     any leaf still at Σ Q < Min is flagged **under-allocated** and blocks
     nomination-close/finalization for that leaf until the Industry either
     grants up to Min **or** records an explicit, **Audit-logged Min
     waiver** for that leaf (§10).
- **Interaction with reclamation (D27):** the ratchet block applies only
  *during* the nomination window; **post-deadline reclamation of unused
  quota is not blocked by Min** — once nomination is closed the floor's
  purpose (ensuring enough allocation to fill) has expired.

**Node status:** Pending until preconditions (D26) are met and the node is
Published; Publish unlocks Nomination.

**Open sub-questions parked at this node (flag, don't design now):**
- **Custom quota field enforcement (D24):** custom quota fields are
  display/reference in v1; whether any become hard constraints (e.g. a
  `select` field that gates nomination eligibility) is unspecified —
  parked, same "generic mechanism now, enforcement later" posture as D19.
- **Cross-granularity roll-up:** "both" mode assumes a parent ceiling
  track and its Sub-Type sub-cap tracks share the **same granularity** for
  Σ child Max ≤ parent Max to be well-defined (v1 default). Allowing mixed
  granularity with a defined aggregation rule is a later question.

### P.6 Still parked — not yet designed

- **Registration Dashboard node** screen/canvas details — behavior exists
  (§J/D8), UI/canvas layout not yet walked through.
