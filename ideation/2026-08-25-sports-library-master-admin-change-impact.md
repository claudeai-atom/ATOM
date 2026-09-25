# Sports Library (Master Admin) — Change-Impact / Gap Analysis

**Date:** 2026-08-25
**Type:** Change-impact / gap analysis only. **No design resolved, no
open question answered, nothing committed to `atom-context.md` /
`backlog.md`.** This doc characterizes a new requirement against the
already-finalized Flow Builder design so Abhijeet can decide direction.

## 0. The new requirement (as received, condensed)

Master Admin portal gains a **Sports Library**:

- Master Admin authors **Sports**, each with **gender, category, type**
  mapped, plus a **number of team members** (playing members) for the
  sport.
- Worked example: `Cricket` is the sport; admin creates
  `Cricket Mens Senior` as an **"event discipline"** with **team members
  = 11**. "We will define the playing members of the sport from the
  admin panel."
- Master Admin then **enables** sports / event disciplines **to
  Industries (clients)**. E.g. Master Admin assigns 5 sports and 10
  disciplines to an Industry.
- In a project, the Industry/GTCC **enables** (a subset of) the sports
  the Master Admin gave them — "The Industry can enable all 5 or 3
  sports." This happens **in the Sports Setup node** of Registration &
  Quota Setup.
- The Industry then **allocates quota to the playing members by role**:
  e.g. of the 11 cricket playing members — 5 batsman, 4 bowler, 2
  all-rounder (as "usertype"), plus a **reserve** the Industry sets
  (e.g. 4 → 15-member squad). **Reserve is Industry-set, not Master
  Admin-set.** Master Admin only sets the **playing** count.
- This Master Admin page is **not in Flow Builder** — it lives in the
  Master Admin portal.

---

## 1. What is genuinely new (no equivalent anywhere in current design)

Severity tags: **blocking** = must be resolved before any of the four
nodes can be re-specced; **significant** = new surface/field but
localizable; **minor** = terminology/UX.

1. **A Master Admin "Sports Library" authoring surface — significant.**
   The Master Admin *portal* already exists in canon (§5.3 — "the Master
   Admin creates the Industry from the Master Admin portal"; §4 Master
   Admin row; §8/§4 module assignment). What is new is a **content-
   authoring catalog page inside it**. Today the Master Admin portal is
   described only as doing governance (Industry creation, module
   assignment, user governance — §4, §5.3, §8). No catalog/library
   authoring surface for domain content exists anywhere in canon or in
   the four finalized Flow Builder docs.

2. **"Event Discipline" as a named, Master-Admin-authored unit —
   blocking (structural, not just naming).** The worked example
   ("cricket mens senior as event discipline") collapses what the
   finalized Sports Setup treats as **two distinct tree levels**
   (Sport → Category, where Category carries Gender+Name — see finalized
   Sports Setup §7, §30) into **one master-admin-authored object** that
   simultaneously carries sport identity + gender + category + a member
   count. This is a different shape from the current 3-level
   Sport → Category → Event tree, not a synonym for any one node in it.
   See §4(b) for the mapping question.

3. **A Master-Admin-locked "playing members" count — blocking.** No
   field like this exists. The closest current field is Quota Setup
   **Level 1 → Team → "Max Team Members (per team)"** and
   **"Min Team Members (per team)"** (Quota Setup §2.3), which are
   **Industry/GTCC-editable at Level 1**, not Master-Admin-locked. A
   Master-Admin-owned, non-Industry-editable squad size is a new
   ownership boundary (see §2 conflict).

4. **Per-User-Type / per-role quota breakdown *within* a single team —
   blocking.** "5 quota for batsman, 4 for bowler, 2 for all-rounder"
   distributes the 11 playing slots **across roles inside one team**.
   The current Team-type quota model has **no field for this at all**.
   Quota Setup §2.3 Team only exposes Max Team / Max Team Members / Min
   Team Members / Reserve — the 11 is an opaque squad size, never broken
   down by role. Registration Setup's User Type/Sub-Type tree exists but
   was never connected to an intra-team composition constraint. This is
   the single largest genuinely-new concept in the requirement.

5. **Master-Admin-mapped "type" and "gender" on the sport itself —
   significant.** In current design, Gender lives on **Category**
   (Sports Setup §7/§30) and Type (Individual/Double/Team) lives on
   **Event** (Sports Setup §8/§30), both **Industry-authored** post-pivot.
   The requirement moves gender + type authorship up to Master Admin.

6. **A two-tier enablement chain for sports — significant.**
   "Master Admin enables sports to Industry → Industry enables a subset
   in the project." A per-Industry entitlement list of sports/disciplines
   is a new data object. (Structurally analogous to existing module
   assignment — see §3.)

---

## 2. What conflicts with already-finalized decisions

### 2a. Direct reversal of the 2026-08-18 Sports Setup pivot — blocking

The 2026-08-18 pivot (memory log; ideation index [2026-08-18]) made
**Sports Setup a free-text authoring canvas**: "Industry creates
Sport/Category/Event **by name**" — the deliberate inverse of the old
D15 "enablement-only over a fully backend-fixed taxonomy." Confirmed as
intentional, not an oversight.

The new requirement is structurally the **pre-pivot D15 model**
resurfacing: a backend/admin-fixed catalog that the Industry only
*enables* against. **This does not automatically revert the pivot — the
two can partially coexist — but they cannot both be true as written.**
The unavoidable decision (surfaced, not answered, in §4a):

- **Full revert:** Master Admin owns the whole Sport→Category(→Event)
  taxonomy; Industry Sports Setup goes back to toggle-only. This throws
  away the finalized free-text Sports Setup canvas (finalized doc §3–12,
  §30).
- **Split ownership:** Master Admin owns Sport + discipline + playing-
  member count; Industry still free-authors *Event* (and possibly
  Category) underneath the enabled discipline. This keeps part of the
  finalized canvas but changes its top levels from authored to inherited-
  read-only — which is exactly the "Event Type as backend-inherited-
  readonly" assumption the pivot *removed* (memory 2026-08-18 flag), now
  partially coming back.

Either way, the finalized Sports Setup canvas (`atom-flow-builder-sports-
registration-setup.md` §3–12, §30) must be re-opened. This is the
highest-impact conflict.

### 2b. Playing-members count vs. Quota Setup Level 1 Team fields — blocking

Quota Setup §2.3 Team currently lets the Level-1 configurer
(GTCC/Industry/Master Admin) **set** Max Team Members and Min Team
Members per team, editable inline with autosave (§2.4). The requirement
makes **playing members a Master-Admin-locked fact** ("We will define
the playing members of the sport from the admin panel"; "Reserve for
sport will be set by Industry not from master admin").

Conflict: is the Master-Admin playing count now a **hard ceiling that
Level 1 must inherit read-only** (replacing/constraining Max Team
Members), or a **separate field** that coexists with an Industry-editable
Max Team Members? If it inherits, Quota Setup §2.3's "directly editable
inline" behavior for that field breaks. If it coexists, ATOM now has two
squad-size numbers (Master Admin "playing members" vs. Level-1 "Max Team
Members") whose relationship is undefined. Not resolvable here — flagged
in §4d.

### 2c. Gender/Type ownership vs. finalized Sports Setup fields — significant

Finalized Sports Setup §30 locks **Gender on Category** and **Type on
Event**, both Industry-authored. The requirement relocates both to Master
Admin. If accepted, §30's "Final Locked Scope" for Sports Setup is no
longer accurate and Sports Setup §7/§8 field ownership flips.

### 2d. Tension with atom-context.md §7.6 (already latent) — significant

`atom-context.md` §7.6 still says "**Industry defines User Types,
Registration Forms, and Validation Rules per Project**." The 2026-08-18
Registration Setup pivot already made User Types a **system-defined
library** (Industry enable-only), so §7.6 is *already* stale versus the
finalized design (both are pre-canon/uncommitted). The new requirement
compounds this: if batsman/bowler/all-rounder are User-Type-like and
Master-Admin-scoped-per-sport, that pulls **even more** authorship up out
of the Industry than §7.6 describes. Whatever direction is chosen, §7.6
will need reconciling when the commit pass eventually happens — this
requirement widens an existing gap rather than creating it fresh.

---

## 3. What is compatible / already anticipated

1. **Two-tier enablement pattern has precedent — compatible.**
   "Master Admin enables to Industry, Industry enables in project"
   mirrors ATOM's existing **module assignment** model: Master Admin
   assigns modules to an Industry (§4, §8, §8.2 "assignable by Master
   Admin, subject to dependency"), and the Industry then applies them per
   Project. The same "grant at platform tier, apply at project tier"
   delegation shape already exists — the sports entitlement list is a new
   instance of a known pattern, not a new pattern. Also echoes the Quota
   Setup Level 1→Level 2 additive-delegation model (Quota Setup §4).

2. **Reserve staying Industry-set — compatible.** "Reserve for sport
   will be set by Industry not from master admin" matches the existing
   canon exactly: Reserve is an Industry/GTCC-configurable field at Quota
   Setup Level 1 (§2.3 Team "Reserve (per team)") and Level 2 (§3.3).
   Master-Admin-fixed-playing + Industry-set-reserve is a clean split
   along the existing ownership line for Reserve.

3. **Master Admin portal as the host surface — compatible.** Canon
   already places Master-Admin-only authoring (Industry creation) in the
   Master Admin portal outside any project/Flow Builder context (§5.3).
   Putting the Sports Library there — explicitly "not in Flow Builder" —
   is consistent with where Master-Admin governance already lives.

4. **Sports Setup node remains the Industry's enable-point — compatible
   in role, not in mechanic.** The requirement keeps the *Sports Setup
   node* as where the Industry chooses sports for a project. The node's
   **place in the 4-node sequence is unaffected**; only its internal
   behavior (author vs. enable) is in question (§2a).

5. **"Event Discipline" may be partly terminological — compatible-ish.**
   "Discipline" is a real sports-domain layer (e.g. Athletics discipline
   = Track/Field/Road) and could map to an existing tree level. But the
   worked example collapses Sport+Category into one unit, so it is **not
   purely terminological** — see §4b. Treat the terminology overlap as a
   convenience, not a resolution.

---

## 4. Open gaps / questions that MUST be resolved before design (not answered here)

- **(a) Ownership boundary — does the Master Admin taxonomy *replace* or
  *sit above* Industry free-text Sports Setup authoring?** Full revert to
  enable-only, or split (Master Admin owns Sport/discipline/playing-count;
  Industry free-authors Event/Category beneath)? This is the pivotal
  fork; §2a cannot proceed without it.

- **(b) Where does "Event Discipline" map onto Sport / Category / Event?**
  Is `Cricket Mens Senior` = Sport(`Cricket`) + Category(`Mens Senior`)
  fused into one Master-Admin object? Does "Event" (the current 3rd
  level, e.g. `100m Freestyle`) still exist under a discipline, and if so
  is it Master-Admin- or Industry-authored? Does "discipline" introduce a
  **new** level, making the tree 4-deep?

- **(c) RESOLVED 2026-09-08 — see §6.** Batsman/bowler/all-rounder are
  **Sub-Types**, using Registration Setup's existing User Type → Sub-Type
  model unchanged. No new role layer.

- **(d) RESOLVED 2026-09-08 — see §6.** Master-Admin playing-member count
  is a **single flat number per sport** (e.g. Cricket = 11), owned
  entirely by Master Admin, with **no Industry-editable counterpart** at
  Level 1. Max/Min Team Members (per team) are **removed** from Quota
  Setup — Industry no longer sets squad size at all, only the Sub-Type
  breakdown that must sum to exactly the Master-Admin number.

- **(e) What does "a list or they can create as well" mean for Master
  Admin UX?** Is there a pre-seeded system sport list to reuse/extend
  (Registration Setup's system-defined **User Type library** — finalized
  §15 — proves ATOM already ships backend-fixed lists elsewhere, a
  candidate precedent/home), or is Sports Library wholly greenfield? Does
  the per-role list (batsman/bowler…) live in the same library and is it
  per-sport?

- **(f) RBAC / multi-tenancy of a Master-Admin-only portal page outside
  Flow Builder.** Who can read/write the Sports Library? Is the per-
  Industry sports entitlement a new grant object governed like module
  assignment (§8, §10 access control), and does editing a sport already
  enabled to live projects propagate or freeze? (Catalog-versioning /
  "edit after enablement" is unspecified and mirrors an unsolved pattern
  — cf. the exact-match-registration brittleness already flagged open.)

- **(g) RESOLVED 2026-09-08 — see §6.** Reserve is per-team, Sub-Type-
  tagged for Team quota (mirrors Main), set by Industry at Level 1. Flat/
  untagged for Individual (already Sub-Type-scoped per entry). Doubles
  Reserve stays flat too, for the same reason as Individual.

---

## 6. RESOLVED — Quota Setup Level 1 Team/Doubles/Individual model
(2026-09-08, supersedes §1.3/§1.4/§2b/§4c/§4d/§4g above for the items
listed; §4a/§4b/§4e/§4f remain fully open)

**Division of authorship (the core mechanic):**
- **Master Admin** sets exactly **one flat number per sport**: total
  playing members (Cricket = 11; a Doubles sport = fixed 2, formalizing
  what was already an implicit sport fact). **No role/Sub-Type breakdown
  authored by Master Admin at all** — confirmed explicitly after an
  earlier misreading in this thread assumed Master Admin authored the
  role split too.
- **Industry**, at **Quota Setup Level 1**, does the Sub-Type breakdown —
  freely splits the fixed total across Sub-Types (e.g. 5 batsman/4
  bowler/2 all-rounder, or "11 generic Cricketer" with zero breakdown),
  via the same "+Add Quota" flow Individual-type entries already use,
  pooled against the Master-Admin ceiling.
- **Batsman/Bowler/All-rounder = Sub-Types**, under Registration Setup's
  existing User Type → Sub-Type model (finalized §14–20). No new role
  layer, no schema change needed there. This resolves former open item
  §4c.
- This closes the **parked "Item 2" open item** from the Flow Builder
  session (Sub-Type nested-ceiling, open since 2026-08-22) — Industry
  picks the per-Sub-Type breakdown, the option-C shape from that queue.

**Team — Quota Setup Level 1 fields, final:**
- Max Teams, Min Teams (counts of teams — new fields; not squad size)
- Sub-Type-wise quota of the fixed playing-member total. **Must sum to
  exactly** the Master-Admin number (hard `Σ = 11`, not `≤`) — no
  partial/generic-leftover slots.
- Max Reserve, Min Reserve **per team**, also **Sub-Type-tagged**, same
  shape as Main quota — reversed from an earlier tentative "flat,
  untagged, resolved at Nomination" position once Abhijeet clarified that
  without a Sub-Type tag, a reserve slot has nothing to register against.
- **Max Team Members (per team) / Min Team Members (per team) — removed
  entirely.** Superseded by the Master-Admin fixed number; Industry no
  longer sets squad size in any form.
- Level 2 (Contingent Allocation) — **unchanged**: Q = number of teams
  allocated to a contingent, still no Reserve column at Level 2 (reserve
  is already fixed per team at Level 1). One contingent normally gets one
  team's worth of quota but can be allocated more (e.g. 2).

**Doubles — Quota Setup Level 1 fields, final:**
- Master Admin fixes playing members = 2.
- Max Pair, Min Pair, a **single Sub-Type per pair entry** — mixed-
  Sub-Type pairs (e.g. Mixed Doubles) are **explicitly out of scope**;
  pairs are always same-Sub-Type, so no per-pair role breakdown field
  exists or is needed.
- Max/Min Reserve, **flat/untagged** — no Sub-Type breakdown needed,
  because (like Individual) a Doubles entry is already scoped to one
  Sub-Type at the entry level, so there's nothing ambiguous for a reserve
  slot to register against.

**Individual — Quota Setup Level 1 fields, final: unchanged from current
canon.** Max, Min, Reserve as a single flat number per User Type/Sub-Type
entry. Confirmed to stay flat (not upgraded to Max+Min, not Sub-Type-
tagged) precisely because each Individual entry is already scoped to one
User Type/Sub-Type — the same reasoning that keeps Doubles Reserve flat.

**Still not addressed by this resolution — remains open:** §4a (does
Master Admin's Sport/discipline catalog replace or sit above Industry's
free-text Sports Setup authoring — the core 2026-08-18-pivot conflict),
§4b (Event Discipline → Sport/Category/Event tree mapping), §4e (system-
seeded list vs. greenfield Master Admin UX), §4f (RBAC / catalog-
versioning on the Master Admin portal page). None of these were discussed
in the Quota Setup resolution pass above — do not assume they're
answered.

## 7. RESOLVED — §4a/§4b/§4e/§4f (2026-09-09)

**§4a — Ownership fork: FULL REVERT.** Master Admin owns the whole sports
taxonomy; Industry Sports Setup goes back to enable/toggle-only. This
**reverses the 2026-08-18 free-text-authoring pivot** — Sports Setup's
finalized canvas doc (`atom-flow-builder-sports-registration-setup.md`
§3–12/§30) is no longer current for authoring behavior and needs a
canvas-level re-design pass (not done in this session — see "Next up"
below). The pivot itself stays on record as a real, intentional decision
that was later superseded by this new requirement, not erased.

**§4b — Tree mapping: 2-tier, Sport → Discipline, Discipline is the
leaf.** Per Abhijeet directly: "The master admin will create sports and
their discipline. That's it. The category and all will be mapped to
sports only. The Industry will then enable those discipline of sports in
their project which was assigned to it by MA." Concretely:
- **Master Admin authors two levels only: Sport, then Discipline**
  (e.g. `Cricket` → `Cricket Mens Senior`). No separate Category or Event
  level exists anymore — Gender (formerly on Category) and Type
  (Individual/Double/Team, formerly on Event, Industry-authored
  post-pivot) both fold into Discipline as Master-Admin-owned attributes,
  alongside the already-resolved playing-member count (§6).
- **Discipline is the leaf** — there is no finer Event subdivision below
  it (e.g. no separate "100m Freestyle" under a Swimming discipline in
  v1). This closes what §4b flagged as possibly sport-dependent (team
  sports vs. multi-event individual sports) — Abhijeet chose one uniform
  shape, Discipline-as-leaf, for all sports.
- **Industry's role is enable-only**, at the Discipline level: toggling
  which of the disciplines Master Admin assigned to them get turned on
  per project (the Sports Setup node). No Industry authoring of any tree
  level remains.
- **Downstream consequence, not yet separately confirmed but follows
  directly:** Registration Setup's forms and Quota Setup's quotas, which
  currently attach at the old Event leaf (D18–D21 leaf-only rule), must
  now attach at Discipline instead — Discipline is the new form/quota
  leaf. Flag this for the Sports Setup re-design pass.

**§4e — Seed list: system-seeded, extendable.** Matches the precedent
already set by Registration Setup's system-defined User Type library
(finalized §15) — ATOM ships a starter catalog of common Sports/
Disciplines that Master Admin can extend/edit, not wholly greenfield
authoring from zero.

**§4f — RBAC/catalog-versioning: explicitly out of scope for Flow
Builder.** Per Abhijeet: editing a Discipline already enabled to a live
project surfaces a "used in project" notice to Master Admin, but whether
the edit propagates immediately or is versioned/frozen is **deliberately
left undefined for now** — "we don't need to worry about that. We are
only creating Flow Builder for Industry which will be used in Project."
**Scoping clarification that applies beyond just §4f:** the Master Admin
Sports Library authoring surface itself (creation/editing of Sports and
Disciplines, entitlement-to-Industry mechanics) is **outside Flow
Builder's own design scope** — Flow Builder only needs to design the
Industry-facing consumption side (the Sports Setup node's enable-only
behavior). The Master Admin portal surface is a separate build item,
referenced here for impact but not designed by this thread.

**All four §4 open items are now resolved.** Nothing here has been
committed to `atom-context.md`/`backlog.md` yet — still gated on
Abhijeet's separate go-ahead, per the rest of this doc and the Flow
Builder session as a whole.

**Next up:** Sports Setup's finalized canvas
(`atom-flow-builder-sports-registration-setup.md` §3–12/§30) needs a
re-design pass to reflect enable-only-over-Master-Admin-catalog instead
of Industry free-text authoring, and Registration Setup/Quota Setup's
"leaf" references need re-pointing from Event to Discipline. Not done in
this session — flag for the next Flow Builder canvas-design pass.

## 5. Rough size-of-change signal

| Affected area | Size | Why |
|---|---|---|
| **New Master Admin portal surface (Sports Library authoring + per-Industry enablement)** | **Large / build-new** | No authoring/catalog surface exists in the Master Admin portal today (only governance actions). New authoring UI + new entitlement data model + new RBAC scope. Partially reuses the module-assignment delegation pattern (§8) as a template, so not wholly greenfield conceptually. |
| **Sports Setup node** | **Large** | Depending on §4a, either fully reverts from the finalized free-text canvas back toward enable-only, or splits into inherited-top-levels + authored-lower-levels. Finalized doc §3–12/§30 must be re-opened either way. |
| **Registration Setup node** | **Medium** | Only affected if batsman/bowler/all-rounder become Sub-Types or a new role layer (§4c). If they are a separate quota-only concept, Registration Setup may be untouched; if Sub-Types, §14–20 and the joining-form-per-entity model are impacted. |
| **Quota Setup node** | **Medium–Large (model now RESOLVED — see §6)** | Level 1 Team field set changes concretely: Max/Min Team Members removed, Max/Min Teams added, Sub-Type-wise breakdown of the Master-Admin fixed total added (exact-sum constraint), Reserve becomes Sub-Type-tagged. Doubles/Individual mostly unchanged. Level 2 untouched. Remaining work is spec/doc drafting, not open design. |
| **atom-context.md reconciliation** | **Medium (deferred)** | §7.6 already stale re: system-defined User Types; this widens it. No commit now (gated), but the eventual §O pass grows. |

**Headline:** This is a **large, multi-node change with one new
build-from-scratch surface** (Master Admin Sports Library), and it
**re-opens the single most recently pivoted decision** (2026-08-18 Sports
Setup free-text authoring). It is not additive around the edges — it
touches the ownership model at the top of the whole Registration & Quota
Setup chain, plus introduces a genuinely new capability (per-role
intra-team quota) that no current field supports.
