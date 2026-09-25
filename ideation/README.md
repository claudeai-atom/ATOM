# ATOM Ideation

One entry per idea/proposal. Append new entries below the template;
don't overwrite prior ones.

Ideas that get finalized as product decisions should be merged into
`../context/atom-context.md` (not left only here) — this folder is for
raw and in-progress thinking, not the source of truth.

---

## Template

### [YYYY-MM-DD] Idea title

**Problem it solves:**

**Proposal:**

**Related context sections:** (e.g. §7.7 Project Joining Flow)

**Status:** Raw / Under review / Accepted / Rejected

**Notes:**

---

### [2026-07-31] ABAC + PBAC on top of ATOM's RBAC

**Problem it solves:** Can ATOM add ABAC (Attribute-Based) and PBAC
(Policy-Based) access control on top of its current RBAC — and how?
Headline finding: **RBAC/PBAC/ABAC are already committed doctrine
(§8.1, §10, §16) but entirely undefined** — no policy schema, no
attribute list, no PDP concept, zero examples. ATOM also **already does
ABAC informally, per-feature**: §10's budget FA-scoping and the Sub-FA
D5 guardrail are attribute predicates hand-written into each decision.
The task is to *specify* the model, not decide whether to adopt it.

**Proposal:** See `2026-07-31-abac-pbac-access-control.md`. Grounds the
three layers in real ATOM entities (workspace, `granted_fas`,
`project.lifecycle_state`, `task.status`, Grievance TAT, quota
reclamation) with concrete policies that mostly **re-express existing
decisions** (P-BUDGET = §10 budget rule; P-SUBFA = Sub-FA D5; P-PROGRESS
= §7.5 UI-hack). Recommends: **don't build a big-bang policy engine** —
(A, doc-only now) name the attribute model + policy register + the
**narrow-only invariant** (ABAC/PBAC may only tighten an RBAC grant,
never widen — the key to non-breaking migration); (B, 1.5→2) pilot the
ABAC overlay on **Budget Tracking §8.5** then **Sub-FA/Module Admin D5**
(both already ABAC in disguise); (C, Phase 2) add PBAC environment/time
policies via a central PDP as AMS/Grievance/Flow-Builder land, with an
Audit decision-trace for "why was I denied"; (D) defer any policy-editor
UI. The five 2026-07-09 RBAC-granularity backlog items stay RBAC — not
folded in.

**Related context sections:** §2, §3.2, §6, §7.1, §7.2, §7.5, §7.6,
§7.7.2, §7.7.3, §7.8, §8.1, §8.3, §8.4, §8.5, §9, §10, §12, §15, §16

**Status:** Raw / Under review — user asked to save the full analysis to
revisit later (2026-07-31). No decisions locked. Four open questions
(O1 narrow-only-invariant-as-hard-rule, O2 central-PDP-vs-per-module,
O3 deny-reason surfacing, O4 where-policies-live). Nothing committed to
`atom-context.md` or `backlog.md`.

**Notes:** Not a greenfield "should we adopt it" question — §10 already
mandates all three "operate together" and §16 says "never flatten." The
genuine RBAC-alone break is documented by §10 itself ("RBAC alone must
never be the whole check"). Strongest existing tie-ins: Budget Tracking
(§8.5) and the Sub-FA Module Admin model (`2026-07-25-sub-fa-access.md`
D5/D6), both proposed as the ABAC pilots.

---

### [2026-08-10] Flow Builder — workflow automation & universality (PM review)

**Problem it solves:** Abhijeet wants to fold two additions into the
in-progress Flow Builder design — (1) some concept of **workflow
automation** (a phase-appropriate slice of the maximalist
`flow_builder_prd.pdf` "USI Flow Builder" vision, whose full scope he has
already rejected for v1), and (2) **universality** — making the builder
usable for "anything," not hardcoded to national-sports-event concepts
(quota/nomination/contingent).

**Proposal:** See
`2026-08-10-flow-builder-workflow-automation-universality-review.md`.
**Point 1 (automation):** don't add a generic runtime "Automation node"
(that reopens D2's §7.7 boundary and pulls in the PRD node zoo). Instead
formalize the **three time/state triggers already decided** (nomination
deadline D27, deadline-gated reclamation D6e, Min deadline-block D28) into
one thin "Nomination-window automation" primitive, and add **deadline-
reminder notifications** riding Phase-1 Notifications (§14) + the
Hierarchy tab's existing Notification Recipients (§B). Nomination is *not*
already a workflow (D16 makes it manual, immediate, no approval). Note
§14 Phase 2 already lists "Automation / Advanced workflows" — this is a
define-the-slice gap, not adopt-or-not. **Point 2 (universality):**
reframe, don't rebuild. Much universality is already locked (D1/D4/D5/D17
= "ATOM ships the mechanism, Industry supplies vocabulary"); Registration
Config + Quota Setup (the D17–D28 range Abhijeet named) is the *already-
generic* part — relabel it and make `per Project` quota granularity the
non-sports path (free, do anyway). The real coupling he under-scoped is
**Sports Setup (D15/§P.2)** — backend-fixed taxonomy, "no free-text
fields anywhere," node #1 that **gates the whole sequence**, so a
non-sports project can't advance. That is an unresolved contradiction
with the stated "avoid hardcoded National Games workflows" principle.
Full "configure anything" engine = scope creep (no non-sports customer in
canon). Make one deliberate call: is Sports Setup mandatory?

**Related context sections:** §7.7 (join/nomination boundary), §8.2/§8.3
(Communication module), §11 (module dependency ordering), §14 (Roadmap —
Automation already listed), §15 (Glossary). D-numbers: D1, D2, D4, D5,
D6e, D13, D14, D15, D16, D17, D21, D22, D23, D24, D27, D28; §B, §I, §M.3,
§P.1–P.6 of `2026-07-20-flow-builder-ideation-v2.md`.

**Status:** Raw / Under review. No decisions locked. Two decisions
requested of Abhijeet: (P1) accept the deadline-trigger + reminder slice
vs. rename-only vs. nothing; (P2) is Sports Setup mandatory (make it
optional/non-gating) or is v1 explicitly sports-shaped.

**Notes:** Flow Builder is still entirely pre-canon (zero mentions in
`atom-context.md`/`backlog.md`), so both additions are cheap to frame
correctly *before* commit. `flow_builder_prd.pdf` could not be text-
extracted here (Flate-compressed, no pdf lib) — review works from the
task-supplied PRD summary; recommendation doesn't hinge on PRD detail.

---

### [2026-08-18] Flow Builder — Sports Setup & Registration Setup finalized (canvas-level design)

**Problem it solves:** Detailed canvas/UI design for the first two of the
five Registration & Quota Setup nodes, one level below the D14–D28
decisions in `2026-07-20-flow-builder-ideation-v2.md` §P.

**Proposal:** See `atom-flow-builder-sports-registration-setup.md`.
Locks the canvas structure for **Sports Setup** (Sport → Category →
Event tree, drag-to-canvas, Pending/Configured/Published, permission-based
Configure/Publish with Industry approval fallback) and **Registration
Setup**, renamed from "Registration Configuration" (User Type → Sub-Type →
Joining Form, redirect to existing Form Builder, same permission model).

**Reverses D15 and D17.** Sports Setup is now the **free-text authoring**
canvas (Industry creates Sport/Category/Event by name; Event Type gains a
third value, "Double") and Registration Setup is now **enable-only** over
a system-defined User Type list (only Sub-Type stays free-text). This is
the exact inverse of D15 (backend-fixed taxonomy, no free-text) and D17
("Industry-authored, NOT enable-only — the inverse of Sports Setup").
Confirmed as an intentional pivot by Abhijeet (not an oversight) —
D15/D17 in the v2 doc are annotated superseded, pointing here. D18–D21
(Sub-Type shape, leaf-only forms, standard palette, Publish semantics)
are unaffected. The 5-node sequence and Registration Dashboard (D14) are
unaffected — still 5 nodes; this doc just hasn't reached Dashboard yet.

**Related context sections:** §P.2 (D15), §P.4 (D17–D21) of
`2026-07-20-flow-builder-ideation-v2.md`.

**Status:** Accepted for Sports Setup + Registration Setup. Quota Setup
and Registration & Nomination canvas-level design remain (session moves
there next); D22–D28's Quota Setup content (§P.5) should be re-checked
against the reversed D15/D17 model since it leaned on the old shape (e.g.
Event Type as backend-inherited-readonly no longer holds).

**Notes:** Nothing committed to `atom-context.md`/`backlog.md` yet —
still pre-canon, per the 2026-08-10 entry above.

---

### [2026-08-21] Flow Builder — Quota Setup Level 1 & Level 2 finalized

**Problem it solves:** Canvas-level design for the Quota Setup node (third
of four fixed nodes), one level below the D22–D28 decisions in
`2026-07-20-flow-builder-ideation-v2.md` §P.5.

**Proposal:** See `atom-flow-builder-quota-setup.md`. Quota Setup splits
into **Level 1 (Overall Quota)** — an assigned Institution (or Industry
directly) sets Max/Min/Reserve-shaped ceilings per Sport Event × User Type,
fields varying by Event Type (Individual/Double/Team), entries created via
a guided flow since the applicable User Type isn't knowable in advance —
and **Level 2 (Contingent Allocation)** — per-sport-delegated federations
distribute each Level 1 ceiling across explicitly-added Institutions
(Q/R, hard-blocked at the ceiling), publishing per sport once `Σ Q ≥ Min`
on every entry (**no override**), with the node auto-rolling up to
Published only once every sport publishes. **No Level 3** — registration/
nomination activity lives entirely in the separate, final Registration &
Nomination node, which gates on Level 2's full rollup, not per-sport.
Also locks a **multi-role view model**: Master Admin/Industry see and can
edit every sport at both levels regardless of delegation; an assigned
Institution sees only its assigned scope (sport(s) at Level 1, one sport
at Level 2) — same canvas, filtered left rail per role.

**Refines D22–D28** (see the superseded-block added at §P.5 in the v2
doc): D24→Level 1, D25→Level 2 (same shapes, mechanics refined); D26's
ceiling-only-Publish is tightened at the node level; **D28's inferred
Min-enforcement mechanism is superseded** by a concrete, explicitly-
confirmed one (hard Level-2-Publish precondition, no waiver); D23's
User-Type-tree left rail is superseded by a Sport-first rail. **Two open
carry-forwards, not resolved:** D22's Sub-Type nested-ceiling mode (no
worked example used a User Type with Sub-Types) and D24's custom-quota-
fields affordance.

**Related context sections:** §P.5 (D22–D28) of
`2026-07-20-flow-builder-ideation-v2.md`.

**Status:** Accepted for Quota Setup Level 1 + Level 2. Registration &
Nomination (the last of the four fixed nodes) remains for subsequent
ideation.

**Notes:** Nothing committed to `atom-context.md`/`backlog.md` yet — still
pre-canon.

---

### [2026-08-21/22] Flow Builder — Registration & Nomination finalized

**Problem it solves:** Canvas/mechanics design for the fourth and last
fixed node, Registration & Nomination — both halves. Corrects an earlier
mistaken assumption made mid-session (that this node was just the old
Nomination node + the old Registration Status dashboard folded together)
— "Registration" is a distinct, previously-undocumented contingent-facing
flow that Abhijeet had to explain from scratch, and "Nomination" itself
turned out to need a full self-service redesign, not just a filter-bar
tweak.

**Proposal:** See `atom-flow-builder-registration-nomination.md`.
**Registration:** a contingent Institution registers users against its
Quota Setup Level 2 allocation — Sport-level pooled registration for
Event-split individual quota (e.g. 20+10 athletes → one 30-person Athletics
pool, later split by Nomination), or direct Event-level registration
(final, no Nomination) for Team entries and Sport-granularity-only entries
like Coaches. Registration cap is exactly `Σ (Q + R)` per contingent per
sport (revised up from an initial `Σ Q`-only pass once Nomination's Reserve
mechanic was defined). Team roster: Min–Max range. Two methods: Manual
(form-fill + emailed password-reset link) and Invite (reuses ATOM's
*existing* invite/approve mechanism — resolves an apparent conflict with
the v2 doc's "self-service deferred to §7.7" language, since that deferral
was about nomination, not registration). Multi-sport reuse of the same
person under the same User Type goes through "Search Existing User," no
re-registration; mixed User Types on one person in one Project (e.g. a
Cricket "Batsman" also joining Football) is explicitly deferred to v2.
**Nomination:** drag-and-drop — registered individuals (left panel) onto
event boxes (right panel) with two zones each, Main (`Q`) and Reserve
(`R`), both filled with real named individuals and hard-blocked at their
ceiling. Multi-event nomination is allowed and uncapped in v1 (a
per-individual cap is planned for Flow Builder Phase 2). Status is draft
until the contingent explicitly Publishes (drag-back-out allowed before
that); Publish requires every event's Main **and** Reserve zone to be
exactly full, no partial publish, no deadline enforcement in the system
yet. This **supersedes D16's** "instant Confirmed, no pending step" and
"Manual Registration at the nomination leaf" — D16 modeled Industry-manual
entry; this is the actual self-service contingent flow D16 had deferred to
§7.7, now designed here.

**Related context sections:** §7.7 (Project Joining Flow / invite-approve
mechanism), §P.3 (D16 Nomination, v2 doc — now superseded for the
contingent self-service case), §3.3 (Quota Setup Level 2 `Σ R ≤ Reserve`
enforcement), D17 (User Types are Project-scoped, not Sport-scoped).

**Status:** Accepted for both Registration and Nomination. **Still open:**
the Registration Status Dashboard's place in this node (an earlier
in-session proposal to fold it in as a second tab predates the real design
and isn't locked); the per-individual multi-event cap (Phase 2); the
mixed-User-Type-per-Individual limitation (v2); exact-match registration's
brittleness against withdrawals (v1-accepted risk, unsolved).

**Notes:** Nothing committed to `atom-context.md`/`backlog.md` yet — still
pre-canon. This closes out canvas-level design for all four Registration &
Quota Setup nodes (Sports Setup, Registration Setup, Quota Setup,
Registration & Nomination) — next step for the thread as a whole is the
§O "downstream doc impact" pass, gated on a separate go-ahead from
Abhijeet.

---

### [2026-08-22] Flow Builder — post-canvas open items, worked one at a time (parked queue)

**Problem it solves:** A review of the four finalized nodes turned up
several still-open items for v1. Abhijeet asked to resolve them **one at a
time**, parking the thread with full context between sessions rather than
solving them all at once.

**Proposal:** See `2026-08-22-flow-builder-open-items-followup.md` — the
running record. **Item 1 (Registration Status Dashboard layout) —
RESOLVED:** a flat, filterable table (one row per individual × event),
not the Sport-first rail the other three nodes use (the ask is explicitly
cross-sport). Role scoping is row-level: Master Admin/Industry see an
Institution column and unrestricted rows; a contingent sees the same table
with Institution implicit and rows pre-filtered to its own people, still
across all its sports. **Item 2 (Quota Setup Sub-Type nested-ceiling,
D22 carry-forward) — IN PROGRESS:** three options laid out (Sub-Type-only
entries / roll-up parent + children / Industry's per-User-Type choice),
awaiting Abhijeet's pick. **Item 3 (D24 custom-quota-fields affordance)**
and **Item 4 (exact-match registration brittleness on withdrawal/
replacement)** — not yet discussed.

**Related context sections:** builds on
`atom-flow-builder-quota-setup.md` and
`atom-flow-builder-registration-nomination.md`.

**Status:** Item 1 accepted. Items 2–4 parked, queued in order. Nothing
committed to `atom-context.md`/`backlog.md`.

**Notes:** Resume by working Item 2 next — present the three options,
record Abhijeet's pick in the doc, then move to Item 3.

---

### [2026-08-24] Flow Builder — Base44 prototype build brief

**Problem it solves:** Abhijeet wants a clickable prototype of Flow
Builder built on app.base44.com (free tier) to demo the design, matching
the visual theme of an earlier screenshot (`atom-project.png`).

**Proposal:** See `2026-08-24-flow-builder-base44-prototype-brief.md` —
a self-contained prompt document to paste into Base44 alongside the
screenshot. Covers the Hierarchy tab (3 node types, 1-level sub-canvas)
and all 5 Registration & Quota Setup nodes at prototype fidelity, with
mock/seed data throughout. Scoped deliberately small for the free tier:
explicit non-goals list (no real auth, no notifications, no Level-3
quota, no custom fields), and a priority-ordered interaction list led by
**working drag-and-drop in the Nomination screen** (Main/Reserve zone
capacity enforcement) as the must-have interactive centerpiece.

**Related context sections:** builds on
`atom-flow-builder-sports-registration-setup.md`,
`atom-flow-builder-quota-setup.md`,
`atom-flow-builder-registration-nomination.md`,
`2026-08-22-flow-builder-open-items-followup.md`.

**Status:** Delivered as a build brief for Abhijeet to feed into Base44.
Not a design decision — no changes to canvas specs, just a fidelity-
reduced restatement for prototyping.

**Notes:** Nothing committed to `atom-context.md`/`backlog.md` — this is
a prototyping artifact, not a spec change.

---

### [2026-08-25] Sports Library (Master Admin) — change-impact / gap analysis

**Problem it solves:** Abhijeet raised a new Sports requirement — a Master
Admin portal **Sports Library** (outside Flow Builder) where Master Admin
authors sports with gender/category/type + a locked **playing-members**
count ("Cricket Mens Senior as event discipline, 11 members"), enables
sports/disciplines to Industries, who then enable a subset per project in
the Sports Setup node and allocate quota **by role within a team**
(batsman/bowler/all-rounder) plus an Industry-set Reserve. Needed a
change-impact/gap analysis only — no design resolved, no commits (commit
pass still gated per the Flow Builder session).

**Proposal:** See
`2026-08-25-sports-library-master-admin-change-impact.md`. Structured as:
(1) genuinely-new concepts (Master Admin authoring surface; "Event
Discipline" as a Sport+Category-fused unit; Master-Admin-locked playing
count; **per-role intra-team quota — no current field supports it**);
(2) conflicts — chiefly that this **structurally reverts the 2026-08-18
Sports Setup free-text-authoring pivot** back toward enable-only, and that
a Master-Admin-locked squad size collides with Quota Setup Level 1's
Industry-editable Max Team Members (§2.3–2.4); (3) what's compatible
(two-tier enablement mirrors module assignment §8; Reserve-stays-Industry
matches Quota canon; Master Admin portal already hosts admin-only
authoring §5.3); (4) seven open gaps left unanswered for Abhijeet to
decide (ownership fork, Event-Discipline mapping, role-vs-Sub-Type,
playing-count-vs-Max-Team-Members, list-vs-create UX, RBAC/versioning,
reserve granularity); (5) size = **large, multi-node, one build-new
surface**.

**Related context sections:** §4, §5.3, §7.6, §8, §10 of
`atom-context.md`; finalized `atom-flow-builder-sports-registration-setup.md`
§3–12/§30; `atom-flow-builder-quota-setup.md` §2.3–2.4/§3.3; memory
2026-08-18 pivot.

**Status:** All open questions resolved as of 2026-09-09 (§6 Quota Setup
model 2026-09-08; §7 ownership/tree/seed-list/RBAC 2026-09-09). **§4a
ownership fork: FULL REVERT** — Master Admin owns the whole sports
taxonomy, Industry Sports Setup goes back to enable-only, which
**reverses the 2026-08-18 free-text-authoring pivot**. **§4b tree:**
2-tier Sport → Discipline only (no separate Category/Event level;
Gender/Type/playing-count all fold into Discipline as Master-Admin
attributes); Discipline is the new leaf, so Registration Setup forms and
Quota Setup quotas need to re-point from the old Event leaf to
Discipline. **§4e:** system-seeded, extendable list (same pattern as the
User Type library). **§4f:** RBAC/versioning explicitly deferred —
Master Admin gets a "used in project" notice on edit, propagation
behavior undefined, and out of scope because Flow Builder only designs
the Industry-facing enable-only side, not the Master Admin authoring
surface itself. Still nothing committed to `atom-context.md`/
`backlog.md` — commit pass remains gated on Abhijeet's go-ahead.

**Notes:** The requirement partially resurrects the pre-pivot D15 model;
this doc characterizes that precisely rather than assuming it's a
reversal or an oversight. **Sports Setup canvas re-design is now done**
— see the 2026-09-09 entry below.

---

### [2026-09-09] Flow Builder — Sports Setup v2 canvas LOCKED (Master Admin enable-only)

**Problem it solves:** With the Sports Library §4a/§4b/§4e/§4f fork
resolved (full revert — Master Admin owns the sports taxonomy, Industry
enable-only), Sports Setup's finalized canvas (2026-08-18 free-text
model) was stale and needed a concrete redesign.

**Proposal:** See `atom-flow-builder-sports-registration-setup.md` §32
(new, supersedes §3–13/§30's Sports Setup half). **Structure flattens to
Sport → Discipline** (2 levels, both Master-Admin-authored; no Category/
Event survives — Gender, Type, playing-member count all live on
Discipline). **Left panel** becomes "My Sports Library" — scoped to only
the Sports/Disciplines Master Admin assigned to *this* Industry (not the
full system catalog), grouped by Sport, searchable, each card showing
read-only MA-owned attributes. **Canvas interaction**: dragging a
Discipline card onto the canvas = Enable (not author) — no "+Add
Category"/"+Add Event" affordance survives. Removing a node = Disable
(warns if downstream data exists — ties to the still-open exact-match-
brittleness item). **Right panel**: all Discipline fields read-only
except the Enable/Disable toggle. **Status model and permission/approval
model unchanged in shape** from the original Sports Setup (Pending/
Configured/Published; Configure vs. Publish as separate RBAC
permissions, Industry-review fallback). **Rejected:** a draggable
"Project" node on the canvas — Project stays a context/breadcrumb like
every other Flow Builder canvas, not a new node type.

**Related context sections:** `2026-08-25-sports-library-master-admin-
change-impact.md` §7 (the ownership/tree resolution this canvas
implements); supersedes `atom-flow-builder-sports-registration-setup.md`
§3–13/§30 (Sports Setup half only — Registration Setup §14–30 unaffected
for now).

**Status:** Locked for Sports Setup's canvas UI. **Registration Setup —
confirmed no changes needed** (2026-09-09): its tree (User Type →
Subtype → Joining Form) has no Sport/Category/Event linkage at all,
confirmed by Abhijeet and consistent with §29's parallel-inputs model —
Sports Setup and Registration Setup only converge downstream at Quota
Setup. **Quota Setup still pending** — it's the only node that actually
crosses Sport/Discipline × User Type and needs to re-point its leaf from
Event to Discipline; Abhijeet will walk that change next. Nothing
committed to `atom-context.md`/`backlog.md` yet.

**Notes:** Quota Setup's canvas doc hasn't been touched yet — do not
assume any shape for it until Abhijeet specifies it.

---

### [2026-09-10] Flow Builder — Quota Setup re-pointed to Discipline; Team model confirmed

**Problem it solves:** Quota Setup was the last node still pointing at the
old Sport → Category → Event shape; needed to re-point to the new
Sport → Discipline tree from the 2026-09-09 Sports Setup lock, and confirm
whether the Team-specific quota model (resolved 2026-09-08, never written
into the Quota Setup doc itself) still holds.

**Proposal:** See `atom-flow-builder-quota-setup.md` §8 (new). **Individual
and Double are a pure rename** — "Event"/"Event × Category" → "Discipline"
throughout, no field-shape change. **Team is confirmed, not re-opened**:
Max/Min Team (count), a User Type/Sub-Type breakdown of the Discipline's
Master-Admin-fixed playing total (summing exactly), and Max/Min Reserve
per team also User Type/Sub-Type-tagged; no Team Member sizing field
(squad size lives on Master Admin's Discipline, not Flow Builder). One
field — the playing breakdown — was missing from Abhijeet's initial
restatement and got flagged: dropping it would leave the playing XI's role
composition totally unconstrained while Reserve stayed role-tagged,
backwards from real roster logic. Confirmed: keep it. Level 2 unaffected
(Team still Q-only, no R, breakdown/reserve shape travels per-team from
Level 1).

**Related context sections:** `2026-08-25-sports-library-master-admin-
change-impact.md` §6 (the original Team resolution this confirms); Sports
Setup §32 in `atom-flow-builder-sports-registration-setup.md` (the
Discipline tree this re-points to).

**Status:** Accepted, including Level 2. Same-day follow-up confirmed
Level 2's Team allocation: per Institution, `Q` only = number of teams
(`Σ Q ≤ Max Team`), no `R` column and no per-Institution role breakdown —
the Level 1 playing breakdown and role-tagged Reserve are a fixed
template applied identically to every team any Institution fields; Level
2 only decides team count, matching the pattern already locked for
Individual/Double (unchanged, just Discipline-scoped).

**Notes:** Nothing committed to `atom-context.md`/`backlog.md` yet — still
gated on a separate go-ahead from Abhijeet.

---

### [2026-09-10] Flow Builder — Registration & Nomination and Dashboard re-pointed; Team registration redesigned

**Problem it solves:** Abhijeet assumed Registration & Nomination and the
Registration Status Dashboard needed no changes from the Sport → Discipline
rollout. That was only half right — both needed a terminology rename
(Event → Discipline, same as Quota Setup), and Registration's Team
mechanic had a real gap: its locked "fill a Min–Max roster range" design
was built on Quota Setup's old Max/Min Team Members fields, which the
2026-09-08/10 Team model removed entirely (replaced by an MA-fixed total +
Industry-authored role breakdown + role-tagged Reserve).

**Proposal:** See `atom-flow-builder-registration-nomination.md` §2.7 (new)
and `2026-08-22-flow-builder-open-items-followup.md` Item 1 (updated).
**Terminology:** "event box(es)" → "Discipline box(es)" throughout
Nomination; Dashboard's Event column/filter → Discipline. **Team
registration redesigned (§2.7):** the contingent creates up to `Q` team
instances ("+ Add Team"); each inherits the same fixed role-slot template
from Quota Setup Level 1 (e.g. Batsman Playing ×5/Reserve ×1, Bowler
Playing ×4/Reserve ×1, All-Rounder Playing ×2) and is filled slot-by-slot,
hard-blocked per role — not freely fillable within a range. Team still
skips Nomination entirely (Nomination is now explicitly Individual-only);
this exact-fill model inherits the same unresolved withdrawal/replacement
brittleness already flagged for Individual (Item 4 of the open-items doc,
now explicitly covering Team too).

**Related context sections:** `atom-flow-builder-quota-setup.md` §8 (the
Team model this re-points to); `2026-08-22-flow-builder-open-items-
followup.md` Items 1 and 4.

**Status:** Accepted. This closes out the 2026-09-09 Sports Library
rollout in full — Sports Setup, Registration Setup, Quota Setup (both
levels), Registration & Nomination, and the Dashboard are all now
consistent with the Sport → Discipline model.

**Notes:** Nothing committed to `atom-context.md`/`backlog.md` yet — still
gated on a separate go-ahead from Abhijeet.

---

### [2026-09-10] Flow Builder — Mode of Registration (Open/Closed) + Self-Register method

**Problem it solves:** Abhijeet's first genuinely new Flow Builder
requirement since the Sports Library rollout, not a re-pointing exercise.
Registration previously had exactly two methods (Manual, Invite), both
always Institution-initiated — no concept of an Individual registering
themselves existed anywhere. New ask: a per-registration-entity **Mode**
— **Open** (Invite + Manual + a new **Self-Register**, where the
Individual finds and submits the Joining Form themselves, no Institution
action) vs **Closed** (Invite + Manual only — exactly today's locked
model).

**Proposal:** See `atom-flow-builder-sports-registration-setup.md` §33
(new) and `atom-flow-builder-registration-nomination.md` §2.8 (new).
**Placement, Abhijeet's explicit pick over the agent's Quota-Setup
recommendation:** Registration Setup, at the same leaf as the Joining
Form (User Type, or each Subtype) — Project-wide per User Type/Sub-Type,
**no per-Discipline override** (a real granularity trade-off accepted
knowingly). Default **Closed**, so nothing already designed changes
behavior unless Industry opts in. **The harder question — does
Self-Register consume an Institution's Quota Setup allocation
(`Σ Q+R`), or bypass quota entirely** — is **explicitly parked**:
Abhijeet noted real-world Open events typically skip quota and gate on
**Eligibility** (age/gender/qualifying standard) instead, a concept Flow
Builder doesn't have yet, and asked to design Eligibility as its own next
pass before resolving this. Also flagged, not designed: how an Individual
discovers an Open leaf, whether Self-Register needs its own account-
creation flow, and whether it can apply to a Team leaf at all (every
worked example so far is Individual-shaped).

**Related context sections:** `atom-flow-builder-registration-
nomination.md` §2.5 (Manual/Invite, unchanged), §2.7 (Team role-slot
model — Self-Register/Team interaction unresolved).

**Status:** Placement and default accepted. Quota-interaction question
resolved same day — see the 2026-09-10 Open Quota entry below.

**Notes:** Nothing committed to `atom-context.md`/`backlog.md` yet — still
gated on a separate go-ahead from Abhijeet.

---

### [2026-09-10] Flow Builder — Open Quota (market-researched)

**Problem it solves:** Resolves the quota-interaction question parked in
the Mode of Registration entry above. Abhijeet initially heard Open events
skip quota entirely (gate on Eligibility instead), then followed up having
learned the opposite is standard — real open events (his worked example:
Cricket Mens T20, 100 Individuals can register, 50 play) do cap both
registration and the actual playing/selected count, as two different
numbers, with selection deciding who moves from one to the other.

**Proposal:** See `atom-flow-builder-quota-setup.md` §9 (new). Agent ran
market research (marathon registration-cap-plus-waitlist models; open
cricket/corporate-tournament entry-cap-then-trials models) confirming the
pattern: oversubscribe against a **Max Entries** cap, then run a
**Selection** cut down to the real field, holding the remainder as
Reserve/waitlist. Maps cleanly onto pieces ATOM already has: **Max
Entries is the one new field**; the **Selection target reuses Level 1's
existing Max/Min/Reserve shape** (by Discipline Type); **Selection itself
reuses Nomination's existing Main/Reserve drag-and-drop mechanic**,
performed by Industry or a permitted Institution directly on the single
flat self-registered pool (**no Level 2** — nothing to distribute across
Institutions, since there isn't one). Two forks Abhijeet resolved
(both agent-recommended): excess registrants become a **Reserve/waitlist
tier**, not a hard cut; Selection is done by **Industry or a permitted
Institution**, same additive-delegation pattern as everywhere else.
**Team-shaped Open entries** (individuals self-register, Selection
distributes them into role slots across teams, reusing §2.7's template)
is proposed but **not yet explicitly confirmed**.

**Related context sections:** `atom-flow-builder-sports-registration-
setup.md` §33.6 (updated, resolved); `atom-flow-builder-registration-
nomination.md` §2.8 (updated, resolved), §2.7 (Team role-slot template
reused as the Team Selection target), §3 (Nomination mechanic reused for
Selection).

**Status:** Accepted. §9.5's Team-under-Open resolution subsequently
**confirmed** by Abhijeet same day. Three items still not designed:
early-close-before-cap, Selection method (manual assumed, lottery flagged
as a possible future alternative), and Eligibility — see the entry below.

**Notes:** Nothing committed to `atom-context.md`/`backlog.md` yet — still
gated on a separate go-ahead from Abhijeet.

---

### [2026-09-10] Flow Builder — Eligibility (market-researched, closes a long-parked item)

**Problem it solves:** Abhijeet proposed capturing eligibility (e.g. Age
14–25) as a validated Form Builder field on the Joining Form. Asked
whether that matches industry standard before building it that way.

**Proposal:** See `atom-flow-builder-quota-setup.md` §10 (new). Market
research (FIBA/USA Water Polo/Athletics Under-23/Little League/USSSA age-
category rules; Golf Genius tournament software) found **two standard
deviations** from the proposal: (1) age must be derived from **Date of
Birth against a configurable cutoff/reference date** (which varies by
sport — Dec 31, Aug 31, May 1 are all real examples), never validated as
a raw Age number; (2) eligibility is a **separate rules layer evaluated
against captured data**, not a constraint baked into the field's own
validation — exactly the split found industry-wide between "form
validation" (data correctness) and "eligibility rules" (qualification
logic). Architecturally this matters because Registration Setup's
Joining Form is Discipline-agnostic (§33.5) while age-category windows
are inherently Discipline-specific — so **Eligibility now lives at Quota
Setup, Discipline × User Type granularity**, as a new **third stage**
alongside Level 1/Level 2 (and Open Quota/Selection for Open entries).
Abhijeet asked for it to be **prominent, not buried** — resolved as its
own canvas stage with a visible summary badge on every quota entry, not
an inline field. **Scope, per Abhijeet's pick:** Age (DOB + cutoff date +
min/max) + Gender (mostly an echo of Sports Setup's already-locked
Discipline attribute) + **extensible custom criteria** (license/ID,
qualifying rank, residency, etc.) — the custom-criteria mechanism
**closes the long-parked D24/Item-3 "Quota Setup custom fields" item**
(open since 2026-07-31), finally giving it real enforcement semantics
(hard-block vs. informational-flag, not yet chosen which is v1 default).

**Related context sections:** `atom-flow-builder-sports-registration-
setup.md` §33.5, §33.8 (Registration Setup stays pure data capture);
`atom-flow-builder-quota-setup.md` §9 (Open Quota, shares the Eligibility
stage); `2026-08-22-flow-builder-open-items-followup.md` Item 3 (the
closed custom-fields item).

**Status:** Placement (Quota Setup, prominent third stage) and scope
(Age + Gender + extensible custom criteria) accepted. **Confirmed
2026-09-11:** enforcement scope (gates all registration methods, not
just Open/Self-Register), cutoff-date default (none — Industry sets per
entry), Gender field shape (read-only echo, no override). **Still open:**
custom-criteria enforcement mode, and interaction with Team's role-slot
model.

**Notes:** Nothing committed to `atom-context.md`/`backlog.md` yet — still
gated on a separate go-ahead from Abhijeet.

---

### [2026-09-11] Flow Builder — consolidated source-of-truth doc (for other PMs)

**Problem it solves:** Abhijeet asked for a single reference file
covering every page/feature/decision made across the whole Flow Builder
thread, so other product managers can ideate their own modules against
Flow Builder's actual design without reading a dozen chronological docs.

**Proposal:** See `atom-flow-builder-source-of-truth.md` — not a new
design decision, a synthesis. Organized by node (Hierarchy Setup; Sports
Setup; Registration Setup; Quota Setup incl. Eligibility/Level 1/Level
2/Open Quota/Selection; Registration & Nomination incl. the Dashboard),
plus cross-cutting RBAC/architectural rules, a roadmap section, and a
consolidated "still open, don't assume locked" list pulled from every
node. Points back to the per-topic docs for full reasoning/worked
examples rather than duplicating them.

**Related context sections:** synthesizes all prior entries in this
index.

**Status:** Delivered. Also fixed a stale cross-reference found while
writing it — `atom-flow-builder-registration-nomination.md` §2.8 still
said Eligibility's enforcement scope was unconfirmed after it had
already been confirmed same-day.

**Notes:** This is a snapshot, not a live index — re-sync it after
future decisions land, don't treat it as authoritative once the
underlying docs move ahead of it. Nothing committed to
`atom-context.md`/`backlog.md` — still pre-canon.

---

### [2026-09-12] Flow Builder — Eligibility ownership moves to Master Admin; Registration Setup becomes Sport-scoped

**Problem it solves:** Resuming the parked Eligibility thread (custom-
criteria enforcement mode, §10.6/§10.7), Abhijeet redirected the whole
question: for v1 there's no Industry-authored custom criteria at all.
Instead, Master Admin — when creating a Sport and its Disciplines in the
Sports Library — attaches **all** eligibility criteria directly (Age,
Gender, and extensible custom ones, e.g. **Weight** for a combat-sport
Discipline). This surfaced a real, previously-undesigned gap: Nomination
needs to auto-verify eligibility, but nothing connected MA's per-
Discipline criteria to a capture field on the Individual's Joining Form
— and Registration Setup's form tree has no Sport linkage at all
(confirmed "no changes" as recently as 2026-09-09).

**Proposal:** See `atom-flow-builder-quota-setup.md` §10.8 (new) and
`atom-flow-builder-sports-registration-setup.md` §34 (new,
supersedes §33.8). **Age ownership flips**: MA sets it at Discipline
authoring (like Gender already worked), reversing the 2026-09-11
"Industry sets Age/cutoff per Quota Setup entry" confirmation. **Quota
Setup's Eligibility stage becomes a pure read-only echo** — no more
"+Add Eligibility Rule" flow; Industry authors nothing. **Registration
Setup's Joining Form becomes Sport-scoped** (new tree: Sport → User
Type → Sub-Type → Joining Form, one form per Sport×UserType/SubType,
not just UserType/SubType) — reverses the 2026-09-09 "no changes"
call. Every MA-set criterion for that Sport is auto-locked onto the
form; the same Sub-Type used under two Sports now gets two separate
forms. Enforcement is **inferred as hard-block/auto-verify** at
Nomination, from Abhijeet's own framing — not yet an explicitly
confirmed line, flagged for sign-off like D28 was. D24/Item-3
(Industry custom-quota-fields) reverts to unresolved as an
Industry-facing item — the underlying need is now met by Master Admin
instead.

**Related context sections:** `2026-08-25-sports-library-master-admin-
change-impact.md` §4f (deferred MA versioning/RBAC — same class of
question, not reopened); the 2026-09-10 Eligibility entry above (Age/
Gender/custom-criteria placement, now partially superseded).

**Status:** Accepted: Age-ownership flip, Quota Setup as pure echo,
Registration Setup Sport-scoped forms, Sport (not Discipline)
granularity for the form. **Flagged, not explicitly confirmed:**
hard-block/auto-verify enforcement (inferred). **Still open, not
re-discussed:** Team role-slot interaction with Eligibility; MA
Discipline-edit versioning/notice behavior for live Projects.

**Notes:** `atom-flow-builder-source-of-truth.md` now needs a re-sync
pass — it predates this update and still describes the old
Industry-authored-custom-criteria/Discipline-agnostic-Registration-
Setup shape. Nothing committed to `atom-context.md`/`backlog.md` yet —
still gated on a separate go-ahead from Abhijeet.

---

### [2026-09-14] Flow Builder — Eligibility fully resolved; prototype update prompt for Sports Setup & Quota Setup

**Problem it solves:** Two things closed in one session. First, the last
three Eligibility open items (enforcement, timing, Team role-slot scope)
got confirmed, closing §10 of the Quota Setup doc entirely. Second,
Abhijeet's existing Base44 prototype (2026-08-24 brief) pre-dates the
Master Admin Sports Library rollout, so Sports Setup and Quota Setup in
the live prototype are stale against the now-finalized design — needed a
scoped update prompt for just those two nodes.

**Proposal:** Eligibility resolutions recorded in
`atom-flow-builder-quota-setup.md` §10.6. Enforcement is fully automatic
(no Industry toggle). Timing: checked at Registration for Team/direct-
Discipline entries (already Discipline-scoped), at Nomination for
Sport-pooled Individual entries (Discipline isn't picked until then) —
reconciles with, doesn't replace, the 2026-09-11 "gates every
registration method" confirmation. Team role-slot scope: one Eligibility
rule per Discipline×User Type/Sub-Type entry, uniform across every slot
of that role (Playing and Reserve alike), no per-slot variation. Also
closed Item 3 of the open-items-followup queue (custom-quota-fields) as
moot — the need is served by Master Admin, Industry's Quota Setup shape
needs nothing further. Item 4 (withdrawal brittleness) explicitly
deferred by Abhijeet, now the only open item left.
Prototype update prompt: see
`2026-09-14-flow-builder-sports-quota-prototype-update-prompt.md` — a
self-contained AI Studio prompt scoped to only Sports Setup (rebuild as
enable-only over the MA-assigned "My Sports Library" catalog, 2-level
Sport→Discipline, per §32) and Quota Setup (insert the new read-only
Eligibility stage, repoint Level 1's leaf from Event to Discipline, and
replace the old Team fields with the new Max Team/Min Team + playing
breakdown + role-tagged Reserve shape, per §2.3/§8/§10). Explicitly
excludes Hierarchy, Registration Setup, Registration & Nomination, and
the Dashboard (unaffected), and excludes Mode of Registration/Open
Quota (a separate, unrelated feature thread not yet in any prototype).

**Related context sections:** `atom-flow-builder-quota-setup.md` §2–§10;
`atom-flow-builder-sports-registration-setup.md` §32;
`2026-08-24-flow-builder-base44-prototype-brief.md` §4.4/§4.6
(superseded for these two nodes only); `2026-08-22-flow-builder-open-
items-followup.md`.

**Status:** Eligibility (§10) fully accepted, no open items remain
there. Prototype prompt delivered, not yet run against AI Studio.

**Notes:** Nothing committed to `atom-context.md`/`backlog.md` yet —
still gated on a separate go-ahead from Abhijeet. Only open Flow Builder
Phase 1 item remaining: Item 4 (exact-match registration brittleness on
withdrawal/replacement), deferred.

---

### [2026-09-14] Flow Builder — Registration Setup canvas redesign for the Sport axis

**Problem it solves:** §34 (2026-09-12) added a Sport axis to Registration
Setup's data model, but the actual built prototype/canvas (screenshot
`registration-setup-node.png`) still has no Sport concept — flat User
Type list, no way to map User Types/Sub-Types to a Sport, no Sport-scoped
Form Builder. Abhijeet proposed a free-form drag-to-canvas UI and asked
for a UX opinion before building it.

**Proposal:** See §35 (new) of
`atom-flow-builder-sports-registration-setup.md`. **Rejected** the
free-form drag/connect canvas — Sport → User Type → Sub-Type → Form is a
strict non-reorderable tree, not a graph being actively connected, and a
canvas would introduce a third interaction idiom alongside Hierarchy's
and Sports Setup's for no functional gain. **Adopted instead:** a 3-pane
layout reusing Quota Setup's already-built Sport-rail pattern (§2.5) —
far-left Sports rail auto-populated from Sports Setup's enabled
Disciplines (no manual add step), middle panel reusing the existing
User Type/Sub-Type checkbox UI now scoped per-Sport, and an on-demand
**expandable right-side Form Builder drawer** (keeping this part of
Abhijeet's original idea) that adds a new "Locked — set by Master Admin"
section for that Sport's eligibility fields above Industry's normal
palette.

**Related context sections:** §34 (the data-model trigger this redesigns
the UI for); `atom-flow-builder-quota-setup.md` §2.5 (the rail pattern
reused).

**Status:** Accepted — both the rail+panel layout and the auto-populated
rail were confirmed by Abhijeet over the alternatives offered.

**Notes:** Two follow-ups flagged, not yet designed (§35.3): visual
treatment for locked vs. Industry-owned fields in the Form Builder
drawer, and whether Publish gates per-Sport (like Quota Setup Level 2)
or once for the whole node. Nothing committed to `atom-context.md`/
`backlog.md` yet.

---

### [2026-09-14] Flow Builder — Registration Setup prototype update prompt

**Problem it solves:** Companion to the §35 canvas-redesign decision
above — a self-contained AI Studio prompt to update the live prototype's
Registration Setup node, matching the format already delivered for
Sports Setup + Quota Setup.

**Proposal:** See
`2026-09-14-flow-builder-registration-setup-prototype-update-prompt.md`.
Scoped to only Registration Setup: rebuild the current 2-pane layout
(flat User Type list + inline Form Fields table, per
`registration-setup-node.png`) into the 3-pane rail/panel/drawer shape
from §35 — auto-populated Sports rail, per-Sport-scoped User Type/
Sub-Type middle panel (reusing the existing checkbox UI), and an
expandable Form Builder drawer that adds a new "Locked — set by Master
Admin" section (DOB/Gender/Weight/etc., per Sport) above the existing
field-toggle table. Seeds Cricket/Swimming/Boxing so Boxing's differing
locked criteria (Weight) is visible.

**Related context sections:** §34–§35 of
`atom-flow-builder-sports-registration-setup.md`;
`2026-09-14-flow-builder-sports-quota-prototype-update-prompt.md`
(sibling prompt for the other two changed nodes).

**Status:** Delivered, not yet run against AI Studio.

**Notes:** Explicitly excludes per-Sport Publish gating and the Master
Admin authoring screen (both flagged open in §35.3, not resolved here) —
mocked/deferred rather than designed. Nothing committed to
`atom-context.md`/`backlog.md` yet.

---

### [2026-09-15] Flow Builder — Mode of Registration revised to a single project-wide toggle

**Problem it solves:** §33's original placement (2026-09-10) set Mode of
Registration per-leaf — the same leaf as the Joining Form, i.e. per Sport
× User Type/Sub-Type after §34's Sport axis landed. Walking the Closed
vs. Open flow end-to-end with Abhijeet surfaced that this was never the
model he wanted: Open/Closed is a **project-level identity** ("either the
project is open or closed"), not something that varies leaf-by-leaf
within one project. He also confirmed a standing ambiguity from §33.3:
on an Open project, Invite and Manual stay available alongside the new
Self-Register — Open adds the public path, it doesn't remove
Institution-initiated registration.

**Proposal:** See §36 (new) of
`atom-flow-builder-sports-registration-setup.md`, and the corresponding
update to §3.4 of `atom-flow-builder-source-of-truth.md`. Mode of
Registration becomes one Open/Closed switch for the whole Project.
Closed: every leaf behaves exactly as today (Level 1/2 quota, Institution
Invite/Manual, then Nominate) — unchanged. Open: every leaf's quota
becomes Open Quota (Max Entries + Selection target, no Level 2, §9 of
`atom-flow-builder-quota-setup.md`) and every leaf's Registration &
Nomination screen gains Self-Register alongside Invite/Manual, with
Nomination becoming Selection (Industry or a permitted Institution,
flat applicant pool). This **removes** the per-leaf toggle from the
Registration Setup canvas entirely — that node goes back to being pure
Sport → User Type → Sub-Type → Joining Form (§34/§35), no Mode control
in it. The toggle's new home is **proposed, not confirmed**: Project
level, alongside Hierarchy or as a persistent setting visible across all
five Registration & Quota Setup nodes.

**Related context sections:** `atom-flow-builder-sports-registration-
setup.md` §33 (superseded placement), §36 (this revision);
`atom-flow-builder-quota-setup.md` §9 (Open Quota, now project-wide
when Open); `atom-flow-builder-registration-nomination.md` §2.8, §3
(Self-Register/Selection, now project-wide when Open);
`2026-09-15-flow-builder-mode-of-registration-prototype-update-prompt.md`
(updated to match).

**Status:** Granularity (project-wide, not per-leaf), method coexistence
(all three methods available when Open), and UI placement all confirmed
by Abhijeet. Placement resolved same day (§36.3): the toggle surfaces at
the **top of the Registration Setup node's canvas** — still a single
Project-wide value, just physically living in that node rather than on
Hierarchy or a cross-node setting. Rest of Registration Setup (§34/§35's
Sport → User Type → Sub-Type → Joining Form tree) is unchanged.

**Notes:** Nothing committed to `atom-context.md`/`backlog.md` yet —
same gating as the rest of this thread. This is still the one piece of
the 2026-09-10 design thread never built into any prototype.

---

### [2026-09-22] Invite Deadline — add an expiry to sent invites

**Problem it solves:** Small feature ask — add an "Invite Deadline"
(expiry) field to invites sent by Industry/Institution (and, per the ask,
Master Admin), with a calendar-vs-"N days" input decision to make.

**Proposal:** See `2026-09-22-invite-deadline-feature.md`. Grounds the ask
in ATOM's real invite model (§7.7 Industry→Individual/Institution, §4/§6
Institution→member, §7.9.8 Flow Builder Registration Invite) and corrects
framing mismatches: canon has **no "Expired" state, no invite expiry, no
invite reminder notification**; the status surface is the **Requests &
Invitations page (§15)**, not an "Invitation tab"; and **Master Admin has
no in-app invite path (§5.3)** — flagged, not invented. **Input-shape
recommendation: absolute date picker is canonical + stored** (matches the
only existing ATOM date-field precedent — Eligibility cutoff §7.9.4,
Command deadline §7.7.5 — and is unambiguous to the invitee), with
relative quick-picks (7/14/30d) that resolve to a shown absolute date;
store UTC, cut at end-of-day in the Project/Industry timezone. Full-flow
impact audit across 11 surfaces incl. the **blocking** ones the ask
missed: new **Expired** state-machine step (§7.7); **New/unregistered
invitee** short-deadline guard (§7.7.2 + §5.1); and **quota release on
expiry** for the Flow Builder Registration Invite (§7.9.8) — which, if
adopted, **partially closes the open withdrawal-brittleness backlog item**
(Item 4, `2026-08-22-flow-builder-open-items-followup.md`). Reuses the
Command-deadline scheduler (§7.7.5) rather than a new time-trigger. 12
open questions listed (scope, MA path, org default-vs-max, edit-after-send
+ ABAC narrow-only, notification recipients, naming collision with
Command/Nomination "deadline", resend semantics, timezone, Join-Request
symmetry).

**Related context sections:** §4, §5.1, §5.3, §6, §7.7 (all), §7.9.8,
§8.1, §8.3, §10, §11, §15.

**Status:** Raw / Under review — exploratory ideation only. Nothing
committed to `atom-context.md`/`backlog.md`.

**Notes:** Feature forces two undocumented substrates to be pinned down in
§7.7 if it proceeds — the invite **state enumeration** and the
**Institution→member invite form** — neither is drop-in.

---

### [2026-09-24] Roster Management — Project-level shared roster capability

**Problem it solves:** Rosters are currently built inside individual
modules (e.g. Athlete MS), so each module maintains its own divergent
roster concept. Request: lift roster management into the Project core so
modules consume one shared capability. New page requested between Task
Board and User Management.

**Proposal:** See `2026-09-24-roster-management.md`. Recommends
reclassifying Roster from a §8.2 module (where it's currently listed but
undefined) into a **base-platform, Project-level capability** (like Budget
§8.5 / Progress §7.5), consumed by modules via a stable `roster_id`
reference, never copied. Creation role-gated per the `Command: Issue`
precedent (Industry/Institution default; Individual only via granted
`Roster: Create`). Membership bounded by the **D5 parent-FA-population
guardrail** (2026-07-25 Sub-FA doc); rosters FA-scoped, Project-scoped
(no cross-Project in v1). RBAC `Roster: View/Create/Edit/Manage
Members/Delete`; view-as-member piggybacks on membership (Progress §7.5
precedent); co-member visibility a narrow-only ABAC policy
`P-ROSTER-VISIBILITY`, default-private + minor-safeguarding. Flags three
framing conflicts: Roster already a §8.2 module; the 2026-07-25 **D4**
decision that grouping belongs in Modules not core; and overlap with Flow
Builder Team/Nomination outputs (§7.9.8). Nine open questions (Q1–Q9) for
the user; recommends a backlog entry but no atom-context.md edits until
decisions land.

**Related context sections:** §7.4, §7.5, §7.7.2, §7.7.3, §7.7.5, §7.8,
§7.9.8, §8.2, §8.4, §8.5, §9, §10, §11, §12, §13, §15;
`2026-07-25-sub-fa-access.md` (D4/D5/D6),
`2026-07-31-abac-pbac-access-control.md`,
`2026-07-17-communication-module-integration.md` (D1 workspace-level surface).

**Status:** Raw / Under review — exploratory ideation only. Nothing
committed to `atom-context.md`/`backlog.md`.

**Notes:** Core capability is feasible now (Phase 1.5 candidate); the
AMS/TMS consumption refactor and Flow-Builder-Team/Nomination
materialisation are Phase 2, gated on AMS integration (§8.4).
