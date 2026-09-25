# [2026-09-24] Roster Management — Project-level shared roster capability

> Standalone ideation doc. Index entry belongs in `ideation/README.md`.
> Exploratory — **nothing here is committed** to `context/atom-context.md`
> or `context/backlog.md`. Section refs (§) point at
> `context/atom-context.md`. Recommendations for what *should* change in
> those two files are collected in §H (do not edit them without the
> user's decision).

**Status (updated 2026-09-25): All nine open questions (§G, Q1–Q9)
decided.** Ready to apply to `context/atom-context.md` / `backlog.md`
per the §H touch-list — still requires the user's explicit go-ahead to
edit those files, since this doc itself commits nothing on its own.
Original framing below, left intact for the reasoning trail: the request
is coherent and fits ATOM's existing base-platform precedents (Budget
§8.5, Progress §7.5); it collided with two already-documented facts
(§A), both resolved (Q1, Q2). Direct answers to the original five
questions are in §C, updated in place by the 2026-09-25 addenda where a
later decision superseded the original draft (notably §C.2, FA-scoping
removed).

---

## A. Two blocking framing conflicts (read first)

The request is described as *"rosters currently get created inside
individual modules … lift roster management up into the Project-level
core/parent."* That framing is at odds with two things already in canon.
Neither kills the idea — but the doc will contradict itself if we don't
resolve them explicitly rather than silently.

### A.1 "Roster" is **already a §8.2 Project Module**, not a per-module feature (significant)

§8.2 lists the Project Modules as: *Athlete Management · **Roster** ·
Training · Credential · Accreditation · Communication · Grievance · …*
So in current canon, **Roster is its own independently-assignable module**
(§8.4 provenance pattern), *separate* from Athlete Management — it is not,
today, "a feature that lives inside Athlete MS." It has **no §8.3 Module
Detail entry** (§8.3 details Org Management, Athlete Management,
Communication, Grievance, Certification, Credential & Accreditation — not
Roster), so it is a **named-but-undefined module**.

The request therefore isn't "invent rosters at Project level." It is
really one of:

- **(A1-a) Reclassify Roster** from a §8.2 assignable module → a
  **base-platform, Project-level core capability** (the same move already
  made for Budget Tracking §8.5 and Progress §7.5, both of which were
  pulled into base platform rather than shipped as §8.2 modules). This is
  what the request text actually asks for ("lift into the core/parent")
  and is my recommended reading.
- **(A1-b) Keep Roster a §8.2 module** but make it a **foundational shared
  module** that other modules (AMS, Training, TMS) declare a **§11
  dependency** on — i.e. AMS can't be used until Roster is active. This
  preserves the billing model (§9: module assignment is a billing event)
  but means an Industry that hasn't licensed Roster can't get AMS rosters,
  which is probably the wrong coupling for something as basic as "a group
  of athletes."

**DECIDED (2026-09-25): A1-a (base-platform).** Confirmed by the user.
Roster is plumbing that multiple modules need (AMS training, TMS squads,
Credential/Accreditation batches). It becomes a base-platform capability,
bundled into standard Industry billing by default — no separate SKU, no
§11 purchase gate for AMS — mirroring Budget's and Progress's treatment.
Requires editing §8.2 (remove "Roster" from the module list) and adding a
new §8.6 entry alongside §8.5 Budget Tracking when the doc is applied to
atom-context.md (still batched with the other Q1–Q9 decisions per §H).

### A.2 The Sub-FA / Module-Admin decision (2026-07-25) said the opposite for grouping work (significant)

`ideation/2026-07-25-sub-fa-access.md` **D4** settled a directly-relevant
principle: *"'Access and work on a Sub-FA' is each Module's own
responsibility (§8.2/§8.4), **not an ATOM-core RBAC feature at any
phase**."* The reasoning (§B of that doc): ATOM core grants the FA and its
population; **each Module decides how to group/place those users** for its
own surface. A "roster" is precisely a fine-grained grouping of the FA's
granted population — the category D4 pushed *out* of core and *into* the
modules.

So lifting Roster into core is, on its face, a **reversal of D4's
principle** for one specific object. Two honest ways to read it:

- **It's a genuine, defensible exception.** D4 was about grouping that is
  *module-specific* (which users work which Sub-FA in *the Grievance
  module's* escalation chain, or *Task Management's* boards). A roster is
  the opposite: a grouping meant to be **shared across modules** (AMS *and*
  TMS *and* Credential all consume the same squad). Shared-across-modules
  infra is exactly what belongs in core (that is the whole rationale for
  the request). Under this reading, Roster-in-core and D4 coexist:
  **cross-module shared grouping → core; single-module internal placement
  → still the module's job (D4 unchanged).**
- **It's a contradiction that needs an explicit override.** If we say
  "grouping lives in core after all," we owe an explanation for why
  Sub-FA placement doesn't, or D4 starts to look arbitrary.

**Recommendation:** adopt the first reading and **state it as an explicit
boundary rule**: *Roster = a durable, cross-module-shareable membership
set, owned by ATOM core. Sub-FA placement / escalation chains / board
assignment = single-module internal wiring, owned by the module (D4,
unchanged).* This keeps both decisions internally consistent, but the user
should confirm it rather than have it slipped in (see §G-Q2). The D5
**parent-FA-population guardrail** from that same doc is *reused wholesale*
below (§C.2) — it's the cleanest existing answer to "who can I add."

### A.3 Roster already overlaps Flow Builder's Team / Nomination outputs (significant)

Flow Builder already produces roster-shaped objects that this feature must
be reconciled against, or ATOM will have two parallel "list of people on a
squad" concepts:

- **Team instances** at Registration (§7.9.8): a contingent creates up to
  `Q` team instances, each filled slot-by-slot against a fixed role-slot
  template (Batsman ×5, Bowler ×4, …). That *is* a roster.
- **Nomination Main/Reserve zones** (§7.9.8): "filled with **real named
  individuals**" per Discipline. That is also a roster (the selected
  squad + reserves for a Discipline).
- §7.7.5 even uses the word literally: a commanded member is *"counted in
  **roster**/quota like any other member."*

So "roster" is **already an informal, load-bearing word** in canon. If
Roster Management becomes a formal core object, the spec must say whether a
Flow Builder Team / a published Nomination **is** a Roster-Management
roster (auto-created from the quota flow), **feeds** one, or is **parallel
and separate**. Leaving this unstated will produce two divergent squad
lists for the same tournament (see §G-Q3). This also implies a **§13
naming** cleanup — "roster" needs a single canonical definition.

---

## B. Where the page sits (structural check)

The requested placement — a **Roster Management** page in the Project,
between **Task Board** and **User Management** — is structurally clean and
does not break the §3.1 hierarchy or §7 Project architecture:

- It is an **Industry-side / Project-management surface** (the Project's
  own tab bar is the Industry's organizing container — §7.8 is explicit
  that "a Project is an Industry-side management container, not something a
  participant conceptually attends"). Good: this page belongs on the
  Industry/Institution management side, alongside User Management and Task
  Board, **not** on the participant surface. The participant's view of
  their rosters is a *different* surface (§C.4).
- Sitting **next to User Management** is the right adjacency: User
  Management answers *who is in the Project* (Institution / Individual
  membership, §7.7); Roster Management answers *how those already-joined
  members are grouped*. Roster consumes User Management's output, exactly
  as the Sub-FA doc framed grouping as a **narrowing of an
  already-granted population** (D5).
- **Dependency ordering (§11) is respected:** you can only roster people
  who have joined (§7.7), who joined a Project that exists under an
  Industry. No lower level bypasses its parent. A roster is never a
  join/admission path — it cannot pull a non-participant into the Project
  (contrast the "Assign User must respect the join gate" backlog item,
  2026-07-09). This must be stated as a hard rule (§C.2, §G-Q?).

The one caveat: the tab list in the request (Overview, Project Details,
Functional Area, Task Board, User Management, Module) is **PRD-level detail
not enumerated in `atom-context.md`** — the context doc describes §7
architecture, not a literal tab order. So inserting the tab is a PRD change;
the architectural commitments are the ones in this doc.

---

## C. Direct answers to the five questions

### C.1 — Who can create a roster? (role-gated, not "all of an entity type")

Grounded in: Institution authority is **FA-scoped**, not Project-wide
(§7.4, §12); the **Command-issuance governance** model (§7.7.5) is the
existing precedent for "who may perform an org-action"; and the Module
Admin model (D6, 2026-07-25) allows either Institution or Individual
holders.

**Answer — mirror `Command: Issue` (§7.7.5) exactly:**

- **Industry** — yes, by default. Whole-Project scope; can create rosters
  spanning any FA.
- **Institution** — yes, by default, but **scoped to its assigned FA(s)**
  (§7.4, §10 FA isolation). Phase 1 Institutions have *full* access within
  an assigned FA and no finer role yet (§7.7.3), so an Institution
  workspace gets roster-create within its FA branch with no extra grant.
- **Individual** — **not by default.** An Individual-type actor (e.g. the
  Coach in the AMS example) can create a roster **only if their Project
  Role carries an explicit `Roster: Create` / `Roster: Manage`
  permission** — precisely the way `Command: Issue` is off-by-default for
  Individual-type roles (§7.7.5). This matters: the whole motivating
  use-case ("a coach creates a roster") is an *Individual*, so this
  permission must exist and be grantable via the §7.7.2 Role axis.

So the answer to "all Institutions? all Individuals?" is: **all Industries
and (FA-scoped) Institutions by default; Individuals only via a granted
Role permission.** Not open to everyone; not limited to one entity type.

### C.2-addendum (2026-09-25) — Roster is Project-level, not FA-tagged

**Superseding decision:** a Roster carries **no stored Functional Area
link**. It is a **Project-level object** — "Project has Rosters," full
stop — not "FA has Rosters." This replaces the FA-scoping framing
originally drafted in §C.2/§D.1/§E.4 below (left in place for the
reasoning trail, but read through this addendum).

This does **not** reopen FA isolation. The two things an FA tag would
have done are now split apart and handled separately:

1. **Who can be *added* to a roster** — unaffected, still governed
   entirely by the D5 guardrail (§C.2 below): the creator can only ever
   *see* and pick from their own FA-granted population, so an Institution
   still structurally cannot add a user outside its FA, tag or no tag.
   Enforcement lives in "who the creator can see," not in a label on the
   roster record.
2. **Who can *view/list* a roster** — needed a new rule, since there's no
   `roster.fa_id` to filter by. **Decided (2026-09-25):** a roster is
   visible to: (a) its creator; (b) the Industry, always (whole-Project
   scope, unchanged); (c) the roster's own members (§C.3/§C.4, "My
   Rosters"); and (d) **the Institution under whose FA-granted population
   the creator sits, for every roster created by any of its own members**
   — i.e. an Institution sees every roster its own coaches/Individuals
   create, even ones it didn't create itself and isn't a member of.
   This is pure oversight-of-your-own-branch, symmetric with the D5
   guardrail (an Institution's oversight never reaches outside its own
   FA population) — it does **not** let an Institution see a sibling
   Institution's rosters, and visibility does not flow the other
   direction (an Individual does not automatically see everything their
   Institution creates — only what they're a member of).
3. **Practical filtering** replaces FA-filtering with the **Sport/
   Discipline tag** (§G-Q8, now decided yes) as the **sole** filter/
   scoping dimension on the roster list and on a module's roster picker
   (e.g. AMS's coach sees "cricket squads" vs "swimming squads," not an
   FA-based split).

Net effect: §D.1's "Filters: by FA" / "Institution sees pre-filtered to
its FA branch" and §E.4's "rosters are FA-scoped by default" are
**superseded** — replace with the rule above wherever this doc is later
applied to `atom-context.md`.

### C.2 — Which users can a creator add? (reuse the D5 guardrail verbatim)

The cleanest answer already exists — it's the **parent-FA-population
guardrail (D5, 2026-07-25):** *a module may only assign, to a grouping,
users already in the parent FA's granted population; it can never pull in a
user unrelated to that FA.* Apply it directly:

- **An Institution** can add only participants who are **in the Project and
  within the Institution's FA scope** (its own contingent / FA-granted
  population, §7.7.3). It **cannot** pull Individuals from a **sibling
  Institution's branch or another FA** — that is a §10 Functional-Area
  isolation violation, the same wall the Sub-FA doc protected.
- **The Industry** (whole-Project scope) can add **any participant in the
  Project**, across FAs.
- **Members are Individuals (participant User Types), not Institutions.
  DECIDED (2026-09-25) — see §G-Q7**, re-grounded after Q2-addendum
  removed FA-scoping from rosters (the original "muddies the FA-scoping
  model" reason is void). Standing reason: a roster is a squad of real
  people for an activity (training, competition, credentialing);
  Institutions participate as **creator/owner** and via **their
  Individuals' own Project membership**, not as roster members
  themselves.
- **Hard rule (must be stated):** adding to a roster is **never** an
  admission path. Every member must **already** have joined the Project via
  §7.7 (apply / invite / command). Roster membership is layered *on top of*
  Project membership, exactly as Module Admin "does not confer Project
  membership" (D6). This mirrors the open 2026-07-09 backlog concern that
  "Assign User must respect the join/approval gate."

RBAC grants the *capability* to add; **FA/Project isolation (§10) scopes
the candidate pool** — never flattened into a single check (§10, §16), same
discipline as Budget visibility (§10).

### C.3 — Multi-roster membership: can the Individual see their rosters / co-members?

An Individual may be on many rosters at once (across FAs, across
Institutions if the Industry placed them, across modules). Two sub-answers:

- **Can they see the list of rosters they belong to?** **Recommend YES.**
  This is the Individual's own membership data, not Industry-management
  data. Withholding it is user-hostile and there's no isolation reason to
  hide *your own* memberships from you. (Contrast Progress §7.5, which is
  *Industry-management* data deliberately not shown to participants — that
  rationale doesn't apply to "which teams am I on.")
- **Can they see the *other members* of a roster they're on?**
  **Recommend DEFAULT NO in Phase 1, opt-in per roster.** Co-member
  visibility leaks the creating Institution's/coach's roster composition to
  a participant, and — this being a sports platform with **minor athletes**
  as a named User Type — exposing a squad list to every squad member is a
  real **duty-of-care / athlete-data-privacy** concern (the same class of
  risk the Communication doc's D4 minor-safeguarding default addressed).
  Make it a **per-roster setting** the creator can flip on ("members can see
  each other" — natural for a competing team that obviously knows its own
  line-up, off for a coach's private shortlist). This is a textbook
  **narrow-only ABAC policy** (`P-ROSTER-VISIBILITY`) in the sense of the
  2026-07-31 ABAC doc: default-deny co-member visibility, opt-in widening
  by the owner, never widening past the FA population.

### C.4 — Do we need a "My Rosters" page for Individuals?

**Yes, if C.3's "see my own rosters" is adopted — but it is NOT a tab on
the Project's tab bar.** The Project tab bar is the Industry-side
management container (§7.8, §B above). The Individual's view is a
**workspace-level surface in the Individual workspace**, analogous to how
the Communication doc placed Inbox as *"Home-level within the workspace"*
(that doc's D1) rather than inside a Project. Recommended shape:

- A read-only **"My Rosters"** view in the **Individual workspace**, listing
  the rosters the user is a member of, **Project-scoped** (grouped by
  Project), showing roster name, Project, FA/Sport, and their own status
  (e.g. Main vs Reserve if the roster came from Nomination). Co-members
  shown only where §C.3's opt-in is on.
- **DECIDED (2026-09-25, §G-Q5): ship in Phase 1.5, converge with §7.8
  whenever it lands.** §7.8 has no committed timeline, so "My Rosters"
  is not gated on it. §7.8 says the participant-facing surface will
  eventually move from raw Project → module-generated Service (AMS →
  Training Programs, TMS → Tournaments); when that ships, folding "My
  Rosters" into the relevant Service ("your Training Program squad") is
  a presentation-layer relocation of the same data, not a re-decision.

### C.5 — RBAC actions and how they map onto the existing model

Follow the **Budget** (`Budget: View` / `Budget: Manage`, §8.5/§10) and
**Command** (`Command: Issue`, §7.7.5) precedents. Proposed permission set:

| Permission | Who holds it | Notes |
|---|---|---|
| `Roster: View` | Industry (all); Institution (rosters it/its own members created); Individual only if granted, plus any roster they're a member of regardless of grant | Scope per the 2026-09-25 addendum (creator + Industry + members + creator's own Institution), not FA-tag matching. |
| `Roster: Create` | Industry, Institution by default; Individual only if granted | Mirrors `Command: Issue` default-on/off split (§7.7.5). |
| `Roster: Edit` | same as Create | Rename, re-scope, toggle co-member-visibility (§C.3). |
| `Roster: Manage Members` | same as Create | Add/remove, **bounded by the D5 FA-population guardrail** (§C.2). |
| `Roster: Delete` | Industry always; Institution for its own FA rosters | Delete/deactivate rules — §D edge cases. |
| *view-as-member* | **no permission** | Piggybacks on **membership**, not RBAC — exactly the Progress §7.5 "don't add a permission axis when membership already covers it" precedent. An Individual sees rosters they're on with no RBAC check. |

**Mapping onto the Institution/Individual/User-Management RBAC model:**

- **Institution grants are FA-based and full-access in Phase 1** (§7.7.3),
  so an Institution automatically gets the full roster action set **within
  its assigned FA** — no new per-action grant needed until FA-scoped
  Institution Roles land in Phase 2 (§7.7.3). Roster is another capability
  that will want that Phase-2 finer scoping.
- **Individual grants are User Type + Role** (§7.7.2); the roster
  permissions attach to the **Role** axis, off by default for participant
  User Types (never hand `Roster: Manage` to an Athlete Role by default —
  same caution as Budget in §10 and Communication in that doc).
- **Never flatten (§10, §16):** RBAC = capability; §10 Project/FA isolation
  = which rosters; ABAC `P-ROSTER-VISIBILITY` = co-member reveal. Three
  layers, as mandated.
- These roster rows are also **exactly** the kind of module/capability the
  open 2026-07-09 backlog items ("RBAC matrix must cover modules"; "granular
  permissions depth") say the matrix must express — Roster should be wired
  in when that matrix work happens.

---

## D. The page proposal + how AMS consumes it

### D.1 The Roster Management page (Industry/Institution side)

Contents:

- **Roster list** for the Project — columns: Name, Sport/Discipline tag
  (§G-Q8, sole scoping tag — see 2026-09-25 addendum, no FA column),
  Member count, Creator (Industry / Institution / Individual role),
  Created, Status (Active / Archived), Co-member-visibility flag.
- **Filters:** by Sport/Discipline, by creator, by status. Row-level
  scoped per the 2026-09-25 visibility rule (not FA-tag matching):
  Industry sees all rosters; an Institution sees rosters it created plus
  every roster created by its own members; an Individual sees rosters
  they created (if granted) plus every roster they're a member of.
- **Create / Edit roster:** name, FA scope, optional Sport/Discipline tag,
  co-member-visibility toggle, member picker (candidate pool bounded by
  D5 — §C.2).
- **Member management:** add/remove from the FA-scoped candidate pool;
  removal is audited.
- **Roster detail:** member list with per-member status where relevant
  (Main/Reserve, if sourced from Nomination), created/modified history.

### D.2 How a module (AMS) references a core roster instead of building its own

Target model: **a roster is a core object with a stable identity
(`roster_id`); modules hold a *reference*, never a copy.**

- When a coach in **AMS** goes to run a training session, AMS presents a
  **roster picker** populated from **Project-level rosters visible to that
  coach** (scoped by the coach's FA population + their `Roster: View`).
  They select an existing core roster rather than re-entering names.
- AMS stores `roster_id` on the training session, not a duplicated member
  list. Membership changes made on the core Roster Management page (add a
  new athlete, remove one who left) **propagate to every consuming module**
  automatically — which is the entire point of lifting it to core, and the
  fix for "each module maintains its own divergent roster."
- If a coach needs a training-specific sub-grouping that is *not* meaningful
  to other modules (e.g. "morning batch vs evening batch" purely inside
  AMS), that stays **module-internal placement** and remains AMS's job per
  **D4** — it does *not* have to become a core roster. This is the concrete
  boundary line from §A.2: shared squad → core roster; module-only internal
  split → module. AMS can layer its own sub-grouping *on top of* a core
  roster reference.

### D.3 What changes for AMS's existing roster feature

- AMS's in-module roster **CRUD is deprecated** in favour of consuming the
  core Roster API (create/list/add-member/remove). AMS keeps only the
  *reference + any module-only sub-grouping*.
- **Migration:** existing AMS-internal rosters must be migrated to core
  roster records (one-time), with AMS sessions re-pointed from local lists
  to `roster_id`s.
- **This is real integration cost against an independently-developed
  system (§8.4).** AMS/TMS are built externally and integrated; changing
  where AMS's roster lives is an AMS-side refactor, not a pure ATOM-core
  change. So the *core capability* can ship on ATOM's own timeline, but the
  *AMS-consumes-core* piece is **gated on AMS integration work** (§G-Q,
  §F phasing). Don't assume AMS can drop its roster the day the core page
  ships.

---

## E. Edge cases & gap analysis

1. **Member leaves the Institution / Project.** On removal from the Project
   (§7.7.5 Remove / user-management B9), the person should be
   **auto-removed from all rosters**, audited — the self-healing pattern
   already used for quota-slot release on invite expiry (§7.7.6). The
   roster itself **persists** (minus that member). *Gap:* if the roster
   came from a Nomination Main zone (§7.9.8) and loses a member, does a
   Reserve auto-promote? That reopens the **exact-match withdrawal
   brittleness** already logged as an open backlog item (2026-09-17 /
   `2026-08-22-flow-builder-open-items-followup.md` Item 4). Roster
   Management should **not** silently invent a promotion rule that node
   doesn't have — flag the dependency.

2. **The creating Institution/Individual is itself removed from the
   Project. DECIDED (2026-09-25) — see §G-Q6.** Command is reversible via
   Remove (§7.7.5, B9), and any Institution can be removed — so a roster
   can be **orphaned**. Resolved as an **escalation chain**, not a flat
   rule: Individual creator → their Institution if still present
   (upgrades its existing view-only oversight, Q2-addendum, to full
   ownership) → Industry (whole-Project scope) if no Institution remains.
   Never silently delete (would strand every consuming module's
   `roster_id` reference and destroy audit history, §10) or cascade-
   delete on a membership change; the transfer is Audit-logged and the
   new owner is notified (§8.1).

3. **Cross-Project rosters.** **Project isolation (§10)** says a roster is
   **Project-scoped in v1** — an Individual's roster membership does **not**
   span Projects; the same human on two Projects has two independent roster
   memberships. Do **not** conflate this with §9 Pro Membership's
   cross-Project *performance-history aggregation* — that is read-only
   analytics layered on top, a different concept from a live editable
   membership set. Cross-Project rosters would be a much larger isolation
   bet (like the cross-tenant-forum question the Communication doc deferred,
   D5) — explicitly out of scope for v1.

4. **Roster ↔ Functional Area relationship. SUPERSEDED (2026-09-25) — see
   the C.2-addendum.** Rosters carry **no FA tag at all**; they are
   Project-level objects. FA isolation still governs *who can be added*
   (D5 guardrail, unaffected) but does not scope roster *visibility* —
   that's the new creator/Industry/members/creator's-Institution rule.
   §G-Q2b is resolved as moot (no per-roster FA link to span). Sub-FA
   needs no separate roster scope, as originally noted (D3, 2026-07-25).

5. **Sport / Discipline tagging.** AMS training and TMS competition are
   sport-specific, and the taxonomy already exists (Sport → Discipline,
   §7.9.1). Recommend an **optional Discipline/Sport tag** on a roster so a
   module can filter "cricket squads" vs "swimming squads." This also
   creates the natural bridge to §A.3 (a Nomination for a Discipline → a
   roster tagged with that Discipline). See §G-Q8.

6. **Duplicate / conflicting membership across modules.** Many-to-many is
   expected and fine (one athlete on a training roster *and* a competition
   squad). The **whole value proposition** is **one canonical roster
   identity** that AMS and TMS both reference — the failure mode to prevent
   is modules **copying** a roster and diverging. Enforce
   reference-not-copy (§D.2). A person appearing on two *different* rosters
   is normal; the same *logical* roster existing twice (once per module) is
   the bug this feature exists to kill.

7. **Reconciliation with Flow Builder Team / Nomination (from §A.3).
   DECIDED (2026-09-25) — see §G-Q3.** Phased auto-materialisation:
   parallel in Phase 1.5 (manual roster creation only), auto-materialise
   on Publish/team-completion in Phase 2, snapshotted (not live-synced),
   gated on the exact-match withdrawal/reserve-promotion backlog item
   being resolved first.

8. **Command interaction / counting.** §7.7.5 says a commanded member is
   "counted in roster/quota like any other member." If Roster becomes a
   formal object, confirm whether that sentence refers to **this** roster
   object or was loose wording for "membership count." A commanded member
   is a full member (§7.7.5), so they are a **valid roster candidate**; a
   *Details-Pending* commanded member (§7.7.5) can be *on* a roster but
   `P-COMMAND-OBLIGATION` still narrows their data-dependent actions
   (attendance, participation) — so **rostering someone does not clear the
   obligation lock.** Roster membership and obligation state are orthogonal;
   state that explicitly so no one treats "add to roster" as a compliance
   shortcut.

9. **Audit (§10).** Every roster create / edit / add-member / remove-member
   / delete / ownership-transfer / visibility-toggle is **written to the
   Audit log**, consistent with the every-transition-audited pattern in
   §7.7.5 and the Budget/Command precedents.

10. **ABAC/PBAC ties (2026-07-31).** Co-member visibility (§C.3) is a
    natural **narrow-only ABAC policy** `P-ROSTER-VISIBILITY` — it belongs
    in that doc's policy register, and it is another candidate for the ABAC
    pilot list alongside `P-BUDGET`/`P-SUBFA`. It re-expresses an isolation
    intuition as a policy; it does not need a big-bang engine.

11. **Naming (§13).** "Roster" is currently used **informally** (§7.7.5,
    §8.2) and **overlaps** "Team," "squad," "contingent," Nomination
    "Main/Reserve." Formalising Roster as a core object requires a **single
    canonical §13/§15 definition** and a ruling on how it relates to those
    neighbours, or the vocabulary fractures.

12. **Roster is not a join gate (restated as a rule).** Belongs in §12
    business rules: *rostering never admits a participant; every roster
    member must already have joined via §7.7.* Prevents the §11-dependency
    and 2026-07-09 "Assign User bypasses the join gate" failure mode.

---

## F. Feasibility & phasing assessment

**Feasibility of the core capability: Feasible now.** All hard
prerequisites exist in Phase 1: Project + FA config (§7.4), participant
membership (§7.7), the RBAC/PBAC/ABAC substrate (§10), Audit (§10),
workspace/Project/FA isolation (§10). The **base-platform, parent-scoped,
RBAC-with-isolation** pattern is already proven twice — Budget (§8.5) and
Progress (§7.5), both Phase 1.5.

**But two things gate the *full* feature:**

- The **§8.2-vs-base-platform reclassification** (§A.1) and the **D4
  boundary confirmation** (§A.2) are decisions, not build work — they must
  land first, or the doc self-contradicts.
- The **AMS-consumes-core** piece (§D.2/D.3) is gated on **AMS integration
  work** (§8.4, independently-developed system). The core page can ship
  without AMS refactored, but the payoff (modules stop building their own
  rosters) only arrives when AMS/TMS re-point.

**Recommended phasing:** treat the **core Roster Management page +
RBAC + FA-scoped membership + My-Rosters + Audit** as a **Phase 1.5
base-platform capability**, siblings to Budget and Progress. Treat
**Flow-Builder-Team/Nomination materialisation (§E.7)** and the
**AMS/TMS consumption refactor** as **Phase 2**, sequenced with the §7.8
Service-view shift and the §7.7.3 FA-scoped Institution Roles (which roster
permissions will want anyway).

---

## G. Open questions requiring a user decision (before spec)

- **Q1 — Reclassify or keep as a module? DECIDED (2026-09-25): Option A —
  base-platform.** Roster Management is reclassified as a base-platform,
  Project-level capability, structured identically to Budget Tracking
  (§8.5/§8.1): removed from the §8.2 module list, given its own §8.6 entry,
  and **bundled into ATOM's standard Industry billing by default** — not a
  separately-assignable SKU, not gated behind purchasing AMS/Training/any
  other module. This also resolves the visibility wrinkle raised alongside
  Q1: the page is visible to **every** Industry regardless of which §8.2
  modules they've enabled, exactly as Budget Tracking is (§9). Rationale
  (confirmed against the actual text): §8.2 names "Roster" as a module but
  §8.3 has no detail entry for it — it was never actually specified or
  built as a standalone module, so reclassifying costs no real existing
  revenue line, only a theoretical future one.
- **Q2 — D4 boundary. DECIDED (2026-09-25): confirmed.** Rule: "grouping
  that must mean the same thing to more than one Module (§8.4) → core
  Roster object; grouping with meaning only inside one Module's own
  workflow → stays Module-owned (D4 unchanged)." Structural parallel to
  D4 itself: ATOM owns the object + base membership; a Module (e.g. AMS
  splitting a roster into "morning/evening batch") may still layer its
  own internal sub-grouping on top of a core roster reference — that
  stays the Module's job, same as Sub-FA placement does today.
  **(Q2b) DECIDED (2026-09-25): moot, resolved by elimination.**
  Rosters are **not mapped/tagged to a Functional Area at all** — see the
  2026-09-25 addendum below. With no stored FA link on a roster, "can one
  roster span multiple FAs" has no object to apply to; an Institution
  holding several FAs simply sees the union of those FAs' populations
  when picking members and can mix them freely in one roster.
- **Q3 — Flow Builder reconciliation. DECIDED (2026-09-25): phased
  auto-materialisation.** Target design is §E.7 option (a) — Publishing a
  Nomination or completing a Team instance **auto-creates a core
  Roster** — but split across two phases so this feature doesn't silently
  inherit the open withdrawal/reserve-promotion brittleness item as a
  hidden dependency:
  - **Phase 1.5 (ships with core Roster):** Flow Builder and Roster stay
    **parallel**. Nomination/Team data remains authoritative for its own
    purposes (Registration Status Dashboard §7.9.9, quota, TMS entries).
    A coach wanting an AMS training roster creates one manually via
    Roster Management — free to pick the same nominated athletes, just no
    automatic link yet.
  - **Phase 2 (explicit follow-on, gated on resolving the exact-match
    withdrawal/reserve-promotion backlog item, §7.9.8 "accepted v1
    risk"):** Publish (Nomination) / team-instance completion
    **auto-creates a core Roster**, tagged with that Discipline (feeds
    directly off the Q8 Sport/Discipline tag), **snapshotted at Publish**
    (not live-synced — live-sync is explicitly deferred until the
    reopen/swap/deadline mechanism exists, since a live link would let an
    in-flight edit silently change a roster AMS/TMS is already running
    against). Requires the Roster object to carry a **per-member status
    field** (Main/Reserve, or role-slot e.g. "Batsman Reserve") beyond the
    flat membership list originally sketched in §D.1 — a real scope
    addition, not just a data copy.
- **Q4 — Co-member visibility default. DECIDED (2026-09-25): Option 1 —
  no minor-specific override, for now.** Default-private + per-roster
  creator opt-in (§C.3) applies uniformly, minors included; no forced
  override for minor-involved rosters. **Correction to the earlier
  framing:** the "Communication D4" precedent cited when this question
  was first raised is **not** a settled endorsed pattern — D4 was
  confirmed, then **explicitly dropped by the user** for messaging
  (minor-athlete Individuals made reachable with no extra restriction),
  flagged in that doc as an **unresolved risk pending Legal/Business
  sign-off**, not a clean precedent to imitate. Decided independently
  here on the merits: roster co-member visibility is passive information
  exposure (seeing a squad list), not an active contact/initiation
  channel like messaging, so the two aren't the same risk shape. Revisit
  if/when the messaging D4 question gets Legal sign-off, in case that
  changes the platform's overall minor-safeguarding posture.
- **Q5 — "My Rosters" now vs later. DECIDED (2026-09-25): ship now,
  converge later.** A thin, read-only "My Rosters" view ships in the
  Individual workspace **in Phase 1.5**, alongside core Roster Management
  — **not** gated on §7.8. Reason: §7.8 is explicitly labeled
  "Later-Phase" with **no committed timeline** (unlike Roster's own
  Phase 1.5/Phase 2 split), so tying this to §7.8 risked leaving
  Individuals unable to see their own roster memberships — already
  decided as self-data they should see, §C.3 — for an indefinite period.
  Scope in Phase 1.5 is naturally smaller since Q3 keeps Flow-Builder
  squads parallel (not materialised) until Phase 2, so "My Rosters"
  initially shows only manually-created rosters and grows automatically
  once Q3 Phase 2 lands — no rework needed. **When §7.8 ships**, folding
  "My Rosters" into the relevant Service view (e.g. "your Training
  Program squad") is a **presentation-layer relocation, not a
  re-decision** — same underlying data/API, different surface.
- **Q6 — Orphan handling. DECIDED (2026-09-25): escalation chain, not a
  flat rule.** Ownership transfers to the **nearest still-present owner
  up the existing oversight hierarchy**, composing with the Q2-addendum
  visibility rule rather than adding a separate mechanism:
  1. **Individual creator removed, their Institution still in the
     Project** → ownership transfers to that **Institution** (upgrades
     its existing view-only oversight, Q2-addendum rule d, to full
     `Roster: Edit / Manage Members / Delete`) — not new exposure, just
     promotion of access it already had.
  2. **Institution creator removed** (or the Individual's Institution is
     *also* being removed in the same cascade — don't hand ownership to
     an Institution that's on its way out) → ownership transfers to the
     **Industry**.
  3. **Individual creator with no Institution in their chain**
     (directly Industry-granted `Roster: Create`) → straight to
     Industry, same as case 2.
  **Unchanged from the original draft:** never delete (would strand
  every consuming module's `roster_id` reference and destroy audit
  history) and never silently archive (roster stays Active so
  references keep resolving). The transfer is Audit-logged and the new
  owner gets a Notification (§8.1) that the roster was reassigned to
  them — not a silent ownership change.
- **Q7 — Members: Individuals only, or also Institutions? DECIDED
  (2026-09-25): Individuals only, confirmed.** Re-grounded independently
  of the original §C.2 justification, which partly leaned on "muddies
  the FA-scoping model" — now void since the Q2-addendum removed FA
  scoping from rosters entirely. Standing reason instead: a roster
  represents real people doing an activity (training/competing/
  credentialing); an Institution doesn't itself train, compete, or hold
  a credential. Its relationship to a roster is already fully captured
  as **creator/owner** and via **its Individuals' own Project
  membership** (§7.7) — no module (AMS/TMS/Credential/Accreditation)
  needs "this roster includes Institution X" as a first-class fact. A
  future composite squad drawing individuals from two Institutions needs
  no new mechanism — an Industry-created roster (whole-Project
  visibility) can already contain individuals from both.
- **Q8 — Sport/Discipline tag. DECIDED (2026-09-25): yes, and it is now
  the sole filter/scoping dimension on the roster list**, since rosters
  carry no FA tag (Q2b addendum) — see the 2026-09-25 addendum below.
- **Q9 — Naming (§13). DECIDED (2026-09-25): confirmed.** Canonical
  **new §15 Glossary entry** — *"Roster: a durable, Project-level,
  cross-module-shareable group of Individual members (§Q7), not
  Functional-Area-scoped (§Q2-addendum), optionally tagged with a
  Sport/Discipline (§Q8), created/owned by an Industry, FA-scoped
  Institution, or permitted Individual, and referenced by modules via
  `roster_id` rather than copied."* Relationship to neighbouring terms,
  each staying **distinct** (no renaming/merging):
  - **Team (§7.9.8)** — stays its own term (role-slot-filled Registration
    structure); related to Roster only via the Q3 Phase 2 materialisation
    link, not synonymous before that.
  - **Nomination Main/Reserve zone (§7.9.8)** — same treatment as Team;
    materialises into a Roster in Phase 2 (Q3), Main/Reserve carried over
    as the member's status field, not called "Roster" until then.
  - **Contingent (§7.9.8)** — stays its own term; means the **Institution
    itself acting in its Registration capacity** (an actor/role label),
    not a group of people — no overlap with Roster; flagged explicitly so
    future spec writing doesn't conflate the two.
  - **"Squad"** — not an actual canon term, just informal prose (used
    loosely in this doc's own reasoning too); recommend avoiding it in
    spec/UI copy going forward now that "Roster" is canonical.
  Also add `Roster` / `Roster Management` to the §13 "use these terms
  exactly" list.

---

## H. Recommended changes to canonical files

**Updated 2026-09-25: all nine decisions (Q1–Q9) are now final.** This
section still requires the user's explicit go-ahead before either file is
actually edited — this doc commits nothing on its own — but the content
below is no longer a placeholder; it reflects the finalized model.

**`context/backlog.md`** — recommended entry, superseding the earlier
placeholder text (FA-scoping references removed, decisions folded in):

> **[2026-09-24] Roster Management — Project-level shared roster capability.**
> Problem: rosters are built per-module (AMS etc.), duplicated and divergent;
> §8.2 also lists "Roster" as an undefined module. Proposal: lift roster into
> a **base-platform** capability (Q1), bundled into standard Industry billing
> like Budget Tracking — a Project-level page between Task Board and User
> Management, **not Functional-Area-tagged** (Q2-addendum/Q2b), that modules
> reference by `roster_id` instead of copying. Members are **Individuals
> only** (Q7); adding a member is still gated by the D5 parent-FA-population
> guardrail regardless of the roster having no FA tag. Visibility: creator +
> Industry + roster members + the creator's own Institution (Q2-addendum).
> RBAC `Roster: View/Create/Edit/Manage Members/Delete` mirroring
> Budget/Command; view-as-member piggybacks on membership. Co-member
> visibility: default-private, per-roster creator opt-in, no minor-specific
> override for now (Q4). Sport/Discipline tag is the sole filter (Q8). "My
> Rosters" ships in the Individual workspace at the same time as core Roster
> (Q5), converging into the §7.8 Service view later as a presentation-layer
> move, not gated on it. Orphan handling: ownership escalates
> Individual-creator → their Institution (if still present) → Industry (Q6),
> audited + notified, never deleted/silently archived. Flow Builder
> reconciliation (Q3): Nomination/Team stay parallel in Phase 1.5, then
> auto-materialise into core Rosters in Phase 2 (snapshotted, not live-
> synced), gated on the exact-match withdrawal/reserve-promotion backlog
> item being resolved first. New canonical glossary term "Roster" (Q9),
> distinct from Team/Nomination-zone/Contingent. Related: §7.4, §7.5,
> §7.7.3, §7.7.5, §7.8, §7.9.8, §8.1, §8.2, §8.4, §8.5, §9, §10, §11, §12,
> §13, §15; `2026-07-25-sub-fa-access.md` (D4/D5/D6), `2026-07-17-
> communication-module-integration.md` (D4, correction re: precedent),
> `2026-07-31-abac-pbac-access-control.md`. Feasibility: core = feasible
> now (Phase 1.5); Flow-Builder materialisation + AMS/TMS consumption =
> Phase 2 (gated on the withdrawal-brittleness item and on AMS integration
> §8.4). **Status: Decided — ready to apply to `atom-context.md`, pending
> user go-ahead.** Full reasoning in `ideation/2026-09-24-roster-management.md`.

**`context/atom-context.md`** touch-list (unchanged in shape, now
unblocked): **§8.1** (add Roster Management alongside Budget Tracking as a
base-platform capability), **§8.2** (remove "Roster" from the module
list), a **new §8.6** describing the capability per this doc's §D, **§10**
(RBAC actions + the co-member-visibility ABAC policy + Audit; visibility
scoping per the creator/Industry/members/Institution rule, not FA-tag
matching), **§7.9.8** (note the Phase 2 Nomination/Team → Roster
materialisation path and its gating on the withdrawal-brittleness item),
**§7.7.5** (clarify the "counted in roster" wording is loose usage,
distinct from the formal Roster object), **§12** (business rules: roster
≠ join gate; Project-scoped, not FA-scoped; Individuals-only membership),
**§13** (add `Roster` / `Roster Management` to the canonical-terms list),
**§15** (new glossary entry per Q9), and **§14** (Phase 1.5 core / Phase 2
materialisation placement).

---

## I. Consolidated flow, page count & role matrix (2026-09-25 synthesis)

Pulls Q1–Q9 into one end-to-end reference, ahead of applying §H to
`atom-context.md`.

### I.1 Pages — 2 total, one with 3 internal views

1. **Roster Management** — Project-level page, Industry/Institution-side
   tab bar, between Task Board and User Management. Three views inside
   it (not separate nav entries): **List** (filterable by Sport/
   Discipline only, §Q8 — no FA filter, §Q2-addendum), **Create/Edit**
   (name, optional Sport/Discipline tag, co-member-visibility toggle,
   member picker), **Detail** (member list, per-member status once
   Phase 2 lands, §Q3, history).
2. **My Rosters** — Individual-workspace-level page (not a Project tab),
   ships Phase 1.5 alongside page 1 (§Q5). Read-only list of rosters the
   Individual belongs to, with their own status; converges into the
   §7.8 Service view later as a relocation, not a rebuild.

Module-side screens (e.g. AMS's roster picker) belong to the consuming
module's own UI, not this feature's page count — they call the core
Roster API / hold a `roster_id`.

### I.2 End-to-end flow

1. Industry / FA-scoped Institution / specifically-granted Individual
   opens Roster Management and creates a roster (name, optional Sport/
   Discipline tag, member picker).
2. Member picker only shows people the creator can already see —
   Industry sees the whole Project, an Institution/granted Individual
   sees only their own FA-granted population (D5 guardrail, §C.2) —
   enforced by visibility, not a stored FA tag on the roster.
3. Roster saved. Ongoing visibility (list/view) follows the
   creator + Industry + creator's-own-Institution + members rule
   (§Q2-addendum), independent of who can be added.
4. A module (e.g. AMS) presents a roster picker scoped to what the
   current user can see; picks an existing roster instead of
   re-entering names. Module stores `roster_id`, not a copy —
   membership edits on the core page propagate everywhere (§D.2).
5. Members removed from the Project are auto-removed from every roster
   they're on, audited (§E.1). If a roster's creator is later removed,
   ownership escalates up the chain — Individual → their Institution if
   still present → Industry (§Q6) — never deleted.
6. Each member sees the roster in their own My Rosters page; sees
   co-members only if the creator opted in for that roster (§Q4,
   default off, no minor-specific override for now).
7. **(Phase 2)** Publishing a Nomination or completing a Team instance
   auto-creates a matching core Roster, tagged with its Discipline,
   snapshotted at Publish — gated on the withdrawal/reserve-promotion
   backlog item being resolved first (§Q3).

### I.3 Functionality by role

| Capability | Industry | Institution | Individual (default) | Individual (granted `Roster:*`) |
|---|---|---|---|---|
| Create roster | Yes, whole-Project, any FA | Yes by default, own FA population only | No | Yes, within own visibility |
| Edit / rename / toggle co-member visibility | Yes, any roster | Yes, rosters it owns | No | Yes, rosters it owns |
| Manage members (add/remove) | Yes, any roster, any Project member | Yes, own rosters, own FA population only | No | Yes, own rosters, own visibility only |
| Delete | Yes, any roster | Yes, own rosters | No | Yes, own rosters |
| View roster list | All rosters in the Project | Rosters it created + every roster its own members created | Rosters it's a member of only (My Rosters) | Same, plus rosters it created |
| See co-members on a roster | Yes (full visibility) | Yes, on rosters it can see | Only if creator opted in for that roster | Same as default |
| Appears in "My Rosters" | N/A (not a roster member, §Q7) | N/A (Institutions aren't members, §Q7) | Yes, for every roster they're on | Yes |
| Orphan-fallback ownership (§Q6) | Final fallback — absorbs when no Institution remains | Absorbs a departed Individual member's rosters | N/A | N/A |
| Receives audit/notification on ownership transfer | Yes, when it becomes new owner | Yes, when it becomes new owner | No | No |

### I.4 Known v1 gap (flagged, not decided — carried forward, not blocking)

Under the current visibility rule, an **Institution cannot see a roster
the Industry created**, even one containing the Institution's own
athletes — visibility flows creator → Industry/own-branch, never
Industry → the Institutions whose people are on it. Real coordination
gap (e.g. an Industry-built combined multi-FA squad is invisible to the
FAs it draws from) that Q1–Q9 doesn't resolve. Accepted as a known v1
limitation per user instruction (2026-09-25); revisit as a follow-up
decision if it becomes a real pain point.
