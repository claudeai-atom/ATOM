# ATOM Flow Builder — Source of Truth (as of 2026-09-11)

## 0. What this document is

A single consolidated reference to everything decided about ATOM's Flow
Builder module across all ideation sessions to date. **Purpose: let other
product managers ideate their own modules against Flow Builder's actual
design** without re-reading a dozen chronological ideation docs or
re-deriving decisions from scratch.

**Status caveat — read this first:** Flow Builder is **entirely
pre-canon**. Nothing in this document has been committed to
`context/atom-context.md` or `context/backlog.md` yet — that commit pass
is explicitly gated on a separate go-ahead from Abhijeet (the ATOM PM)
and has not happened. Treat everything below as the current working
design, not shipped product. Section 8 lists what's still genuinely open
— don't assume those pieces are locked just because they're absent from
the "open items" section of whatever sub-area you're reading.

**Maintenance:** this file is a snapshot synthesis, not a live index.
When Flow Builder decisions change, the source ideation docs (§9) are
updated first; this file should be regenerated/updated to match, not
edited independently of them.

---

## 1. What Flow Builder is

Flow Builder is the ATOM module an Industry (e.g. a sports federation
running a National Games) uses to configure a Project's participation
structure, sports/registration rules, quota, and the registration/
nomination lifecycle. It is not a generic no-code workflow engine — a
maximalist "USI Flow Builder" vision (`flow_builder_prd.pdf`) was
reviewed and explicitly **not** adopted for v1; what's below is the
actual, phase-appropriate scope.

Flow Builder has **two top-level tabs**:

```text
Flow Builder
├── Hierarchy Setup                    (finalized separately, §2)
└── Registration & Quota Setup         (four fixed nodes, §3)
```

---

## 2. Hierarchy Setup tab

Finalized ahead of the Registration & Quota Setup tab; captured here for
completeness since other modules will care about it.

- **Purpose:** an Industry-authored canvas representing a Project's
  participation structure — which Institutions, FAs (Functional Areas),
  and Individuals are attached to the Project and how.
- **Node types:** FA, Institution, Individual. **FA is a first-class
  canvas node** (an FA Head plus linked Institutions) — the FA grant is
  represented directly, not implied. **FA can parent both Institution and
  Individual nodes**; Institution → Institution nesting is capped
  separately (below). An Institution or Individual node can never parent
  an FA node — FA is always the parent in that relationship.
- **Sub-canvas nesting — capped at 1 level.** An Institution node may
  open exactly one sub-canvas of its own immediate children; those
  children cannot open a further sub-canvas of their own. Deeper
  real-world lineages (e.g. National Federation → State → District) are
  **not** modeled via recursive nesting — they're represented as a
  **flat list of Institutions tagged by tier** (a `tier` attribute:
  National / State / District / etc.), not further tree depth.
- **Authoring scope (v1):** the sub-canvas is **Industry-authored** only.
  An Institution operating its *own* sub-canvas (in its own workspace) is
  explicitly **deferred**, not v1 scope — though Flow Builder's own RBAC
  (§4) already anticipates Institution-level roles, so this may land as
  part of Flow Builder's RBAC work rather than a separate later phase.
- **Role placement:** an Individual's Role can live in the invite modal,
  not necessarily on the node face — a UI-completeness detail, not a
  structural gap.
- **Forced/derived constraints (not open choices):** the canvas must
  represent **apply-path** joiners (people who joined by applying, not
  just those invited) as well as invitees; it must support **Command**
  (a non-rejectable assignment mechanism) as a representable action.

---

## 3. Registration & Quota Setup tab

### 3.1 Structure — four fixed nodes, forced order

```text
Sports Setup
      ↓
Registration Setup
      ↓
Quota Setup
      ↓
Registration & Nomination
```

Nodes are **system-defined** — Industry cannot add, delete, or reorder
them. The order is forced by data dependency (Sports Setup and
Registration Setup are independent, parallel inputs — §3.1a below — but
Quota Setup consumes both, and Registration & Nomination consumes Quota
Setup), not an arbitrary choice.

### 3.1a Sports Setup and Registration Setup are parallel, independent inputs

A load-bearing structural fact, confirmed explicitly after an earlier
mistaken assumption: **Sports Setup's tree has no linkage at all to
Registration Setup's tree.** They only converge downstream, at Quota
Setup (which crosses Discipline × User Type). Do not assume a change to
one requires a change to the other — check whether the change actually
touches the Discipline×User-Type intersection first.

```text
Sports Setup:        Sport → Discipline
Registration Setup:  User Type → Sub-Type → Joining Form
                              (no Sport/Discipline linkage)
                                        ↓
                                  Quota Setup
                    (the first node that crosses both trees)
```

### 3.2 Common conventions across all four nodes

- **Status model, per node:** **Pending** (not configured) →
  **Configured** (valid, not published) → **Published** (live, available
  to downstream nodes/functionality).
- **Permission/approval model (repeats per node):** Configure and Publish
  are **separate RBAC permissions**. A user/Institution with Publish
  permission configures and publishes directly, no approval needed. A
  user/Institution **without** Publish permission configures, then
  submits for Industry review (Approve → Published, Reject → back to
  Configured). This exact pattern appears in Sports Setup, Registration
  Setup, and (in a role-scoped form) Quota Setup.
- **Canvas convention:** most nodes use a **left rail of Sports** (or
  equivalent), filtered by the viewer's role/assignment, with a main
  panel showing the selected scope's detail — the same screen rendering
  different views by role, not separate screens per role. (Exception: the
  Registration Status Dashboard, §3.6, deliberately breaks this
  convention — see why there.)
- **Multi-role view model (repeats at Quota Setup and implicitly
  elsewhere):** Master Admin/Industry get **unrestricted** access to
  everything, in addition to whatever Institution is separately assigned
  — delegating to an Institution is always **additive**, never exclusive
  or a full handoff.

---

### 3.3 Node 1 — Sports Setup

**Current model (v2, locked 2026-09-09) — Master Admin owns the sports
taxonomy; Industry is enable-only.** This reverses an earlier
free-text-authoring pivot (2026-08-18) that is now superseded.

- **Structure:** `Sport → Discipline`. Two levels only — no separate
  Category or Event level survives. A Discipline (e.g. "Cricket Mens
  Senior") carries **Gender**, **Type** (Individual / Double / Team), and
  a fixed **playing-member count** (e.g. 11 for Cricket) as
  Master-Admin-owned, read-only attributes.
- **Who authors what:** Master Admin (in a separate portal, outside Flow
  Builder's design scope) creates the entire Sport/Discipline catalog and
  assigns a subset of it to each Industry (two-tier entitlement chain:
  Master Admin → Industry → Project). Industry's Sports Setup canvas only
  **enables/disables** Disciplines already assigned to it — no
  "+Add Category"/"+Add Event" authoring affordance exists anymore.
- **Left panel — "My Sports Library":** scoped to only the
  Sports/Disciplines Master Admin assigned to *this* Industry (not the
  full system catalog), grouped by Sport, searchable, each card showing
  the read-only MA-owned attributes (playing count, Type).
- **Canvas:** dragging a Discipline card onto the canvas = **Enable**
  (not author). Removing it = **Disable** (warns if downstream
  Registration/Quota data already exists — exact behavior tied to the
  still-open exact-match-brittleness item, §8).
- **Right panel:** all Discipline fields read-only except the
  Enable/Disable toggle.
- **Status/permission model:** unchanged in shape from §3.2's general
  pattern — Pending (nothing enabled) / Configured (≥1 enabled) /
  Published (enabled set locked in, available downstream).
- **Explicitly rejected:** a draggable "Project" node on the canvas —
  Project stays a context/breadcrumb like every other Flow Builder
  canvas, not a node type.
- **Out of scope for Flow Builder's design:** the Master Admin authoring
  surface itself (RBAC/versioning for Master Admin editing a Discipline
  already live in projects is explicitly deferred/undefined) — Flow
  Builder only designs the Industry-facing enable-only consumption side.

---

### 3.4 Node 2 — Registration Setup

**Purpose:** defines who can join the project, which User Types/Sub-Types
are enabled, and what Joining Form each must complete. Renamed from
"Registration Configuration."

- **Structure:** `User Type → [Sub-Type] → Joining Form`. A User Type may
  have zero Sub-Types (form lives on the User Type itself) or one or more
  Sub-Types (form lives on each enabled Sub-Type — **no inheritance**, a
  User Type with Sub-Types has no form of its own).
- **User Type authoring:** User Types come from a **system-defined
  library** (Athlete, Coach, Official, Support Staff, Volunteer, etc.) —
  Industry **cannot** create a wholly new User Type, only enable an
  existing one and add Sub-Types under it.
- **Sub-Type authoring:** Industry can either select a predefined
  Sub-Type or create a new **project-specific** Sub-Type (free-text name)
  under an enabled User Type.
- **Joining Form:** every enabled registration entity (a Sub-Type-less
  User Type, or each enabled Sub-Type) must have a Joining Form. Flow
  Builder does **not** rebuild Form Builder — "+Add Form" redirects to
  ATOM's existing Form Builder, pre-scoped to the launching User
  Type/Sub-Type. Form status is independently Pending/Configured/
  Published per entity; a User Type can't fully Publish until every
  enabled Sub-Type's form is Published too.
- **No Sport/Discipline linkage** — see §3.1a. Registration Setup is
  Project-wide and Discipline-agnostic by design.
- **Mode of Registration (new, 2026-09-10; granularity revised
  2026-09-15)** — a setting controlling *how* someone can register:
  - **Closed** (default) — Invite and Manual only. This is exactly
    today's pre-existing model; Closed requires no new build.
  - **Open** — Invite + Manual, **plus Self-Register**: the Individual
    finds the leaf's Joining Form themselves and submits it directly, no
    Institution action on either side. All three methods coexist on an
    Open project (Abhijeet confirmed, 2026-09-15) — Institutions can
    still Invite/Manually register people even while Self-Register is
    also open to the public.
  - **Granularity — REVISED 2026-09-15: single project-wide toggle, not
    per-leaf.** The whole Project is Open or Closed, one switch — not set
    per Sport, per User Type/Sub-Type, or per leaf. This **supersedes**
    the original 2026-09-10 placement (a per-leaf setting living at the
    same leaf as the Joining Form, scoped Project-wide per User
    Type/Sub-Type with no per-Discipline override). Abhijeet's own
    framing: "either the project is open or closed" — every registration
    entity in the project follows the same Mode. See
    `atom-flow-builder-sports-registration-setup.md` §36 for the full
    revision and exact UI placement (still to be pinned down — proposed
    at Project/Hierarchy level, not inside the Registration Setup canvas).
  - Self-Register's quota interaction is **Open Quota** (§3.5.4), and its
    gating rule is **Eligibility** (§3.5.1) — both live in Quota Setup,
    not here. Registration Setup stays pure data capture (DOB, Gender,
    etc.); it does not itself evaluate any rule.
  - Still undesigned: how an Individual discovers an Open leaf (browse
    page? search? direct link?); whether Self-Register needs its own
    account-creation flow or reuses ATOM's existing sign-up.

---

### 3.5 Node 3 — Quota Setup

Internally split into **three sequential stages** (as of 2026-09-10 —
was two before Eligibility was added):

```text
Eligibility  (who's allowed in — §3.5.1)
   ↓
Level 1      (Overall Quota — the national/event ceiling, §3.5.2)
   ↓
Level 2      (Contingent Allocation — distributing to Institutions, §3.5.3)
```

For **Open**-mode entries, the equivalent shape is:

```text
Eligibility → Open Quota → Selection      (§3.5.4 — no Level 2, no Institution)
```

There is **no Level 3** — what happens after quota is allocated
(Institutions actually registering/nominating athletes) is the separate,
final **Registration & Nomination** node (§3.6), not a fourth Quota Setup
tab.

Quota is always scoped to one **Discipline** leaf × one **User
Type/Sub-Type**.

#### 3.5.1 Eligibility (added 2026-09-10)

**Why it's here, not on the Joining Form:** the original proposal was to
capture eligibility (e.g. Age 14–25) as a validated Form Builder field.
Market research (FIBA/USA Water Polo/Athletics Under-23/Little
League/USSSA age-category rules; Golf Genius tournament software) found
this doesn't match industry standard, in two ways: (1) age must be
derived from **Date of Birth evaluated against a configurable
cutoff/reference date** (the cutoff varies by sport — Dec 31, Aug 31, May
1 are all real examples — a raw validated Age field can't reproduce any
of them); (2) eligibility is a **separate rules layer evaluated against
already-captured data**, not a constraint baked into the field's own
validation. Architecturally, this also matters because Registration
Setup's Joining Form is Discipline-agnostic while age-category windows
are inherently Discipline-specific — so Eligibility has to live at Quota
Setup (Discipline × User Type granularity), not Registration Setup.

**Placement/prominence:** Abhijeet asked for it to be prominent, not
buried. Resolved as its **own labeled stage**, same visual weight as
Level 1/Level 2, with its own guided **"+ Add Eligibility Rule"** flow
(User Type → Granularity → rule), and a **visible summary badge on every
Level 1/Open Quota entry row** — not hidden behind a click:

```text
SWIMMING                                              [+ Add Quota]
User Type    Scope                Max  Min  Reserve   Eligibility
Athlete      100m Freestyle (M)   [40] [10]  [4]      Age 14–18 (as of Dec 31) · Male
```

**Fields:**
- **Age** — never a raw field; always DOB evaluated as of a **Cutoff/
  Reference Date** set explicitly per entry. **Confirmed: no
  Project-level default** — Industry sets the cutoff on every entry
  individually, since the correct cutoff genuinely varies by sport, and a
  multi-sport Project would need different cutoffs per Discipline anyway.
  Min/Max Age are each independently optional (blank = no floor/ceiling).
- **Gender** — **Confirmed: read-only echo** of Discipline's existing
  Master-Admin-owned Gender attribute (Men/Women/Mixed), no override.
  Gender is already fully resolved at Discipline granularity elsewhere
  (Master Admin creates separate gender-variant Disciplines/Events when
  needed, e.g. "100m Men"/"100m Women"/"4x100m Relay Mixed" as three
  distinct toggleable entries) — an Eligibility-level override would
  duplicate a distinction the catalog already expresses.
- **Custom criteria (extensible)** — Industry-defined label + type
  criteria (license/ID number, qualifying time/rank, residency/state,
  amateur/professional status), same label+type pattern as Form Builder.
  This closes the long-parked "Quota Setup custom fields, display-only,
  no enforcement semantics" item (originally D24, 2026-07-31) by finally
  giving custom criteria real enforcement semantics: **hard block** or
  **informational/manual-verify flag**, chosen per criterion. **Which
  mode is the v1 default is not yet decided** — see §8.
- **Enforcement scope — Confirmed 2026-09-11: applies to all
  registration methods** (Manual, Invite, Self-Register alike), not just
  Self-Register/Open — eligibility is a property of the person-vs-
  category match, not of how someone entered, so a contingent Institution
  manually registering someone outside the age window is blocked exactly
  like a self-registering Individual would be.
- **Data flow:** Registration Setup's Joining Form captures DOB/Gender/
  custom-field values **once**, Discipline-agnostically; Quota Setup's
  Eligibility stage reads those values and evaluates them **per
  Discipline × User Type entry**.

#### 3.5.2 Level 1 — Overall Quota

The authoritative body (e.g. a national federation) states how many
slots exist for a Discipline, per User Type/Sub-Type, before any
distribution to Institutions.

- **Who configures it:** an assigned Institution, or Industry/Master
  Admin directly at any time (additive delegation, §3.2).
- **Fields vary by Discipline Type** (Individual / Double / Team — a
  Master-Admin-owned Discipline attribute):
  - **Individual:** Max, Min, Reserve (individual entries).
  - **Double:** Max Pairs, Min Pairs, Reserve Pairs. No separate
    members-per-pair field — a pair is always exactly 2, a sport fact,
    not a quota decision.
  - **Team:** **Max/Min Team** (count of teams) + a **playing
    breakdown per User Type/Sub-Type** that splits the Discipline's
    Master-Admin-**fixed** playing-member total (e.g. Cricket = 11) by
    role (e.g. 5 Batsman / 4 Bowler / 2 All-Rounder) — must sum to
    **exactly** that fixed total — + **Max/Min Reserve per team**,
    also User Type/Sub-Type-tagged (so a reserve slot has a role to
    register against). **No "Team Members" sizing field** — squad size
    is fixed by Master Admin on the Discipline itself, not authored in
    Flow Builder.
- **Entries are created, not pre-populated** — the system can't know in
  advance which User Type(s) need quota for a given Discipline (a
  real-time judgment call). A guided **"+ Add Quota"** flow: pick User
  Type → pick Granularity (Discipline / Sport / Project) → if
  Discipline-level, pick the Discipline. Fields then adapt to that
  Discipline's Type. Once created, fields are directly editable inline
  with autosave — no modal, no separate Save button.
- **Publish gate:** every enabled Discipline leaf (from Sports Setup) has
  **at least one** quota entry for at least one User Type. An enabled
  Discipline with zero entries is a real gap, not a valid state — even if
  Sport-level or Project-level entries cover that sport generally.

#### 3.5.3 Level 2 — Contingent Allocation

Distributes each Level 1 ceiling across Institutions ("contingents").

- **Who configures it:** permission is granted **per Sport** (real-world
  federations own a whole sport, not individual Disciplines), but the
  allocation work itself happens per Discipline, since that's where
  Level 1 ceilings live. Industry/Master Admin can configure/view every
  sport regardless.
- **Institutions are added explicitly**, not pre-populated — an
  **"+ Add Institution"** picker, scoped to **one Level 1 entry at a
  time** (adding India to the 200m entry doesn't add it to 100m).
- **Individual/Double:** per Institution, `Q` (allocated) and `R`
  (reserve). Hard-blocked: `Σ Q ≤ Max`, `Σ R ≤ Reserve`.
- **Team:** per Institution, **`Q` only = number of teams allotted** — no
  `R` column, no per-Institution role breakdown. The playing breakdown
  and role-tagged Reserve are a **fixed template**, set once at Level 1,
  identical for every team any Institution fields. Level 2 only ever
  decides team **count**. Hard-blocked: `Σ Q ≤ Max Team`.
- **Publish gate — per sport, with automatic node-level rollup:** each
  sport publishes independently once **every** quota entry under it has
  `Σ Q ≥ Min` (the floor, not full ceiling), across every Discipline and
  User Type in that sport. **No override/waiver** — a sport can stay
  permanently blocked if Institutions won't commit enough; deliberate.
  Level 2 as a whole has **no manual Publish** — it auto-rolls up to
  Published only once **every** enabled sport has individually published.
  Registration & Nomination gates on that **full rollup**, not per-sport
  (a sport finishing early doesn't let its contingents start early).

#### 3.5.4 Open Quota + Selection (added 2026-09-10)

For leaves marked **Open** in Registration Setup (§3.4). Resolves the
question of whether Self-Register interacts with quota at all — market
research (marathon entry-cap-plus-waitlist models; open-tournament
entry-cap-then-trials models) plus Abhijeet's own correction settled it:
**Open events do cap registration**, just via a different, deliberately
oversubscribed pattern than Closed.

- **No Level 2** — Open Quota is a single flat pool; there's no
  Institution to distribute across.
- **Two numbers, not one:**
  - **Max Entries (the one genuinely new field)** — the Registration
    Cap. Self-Register hard-blocks against it once hit.
  - **Selection target** — reuses Level 1's existing field shape exactly,
    by Discipline Type (Individual: Max/Min/Reserve; Double: Max/Min/
    Reserve Pairs; Team: Max Team + playing breakdown + role-tagged
    Reserve). `Max Entries ≥ Selection target total` is expected, not
    enforced-equal — the slack is the entire point (oversubscription,
    like a marathon accepting far more entries than the qualifying
    field).
- **Selection reuses Nomination's mechanic wholesale** (§3.6.2) — same
  drag-and-drop, same Main/Reserve zones, same hard-block-at-ceiling, same
  draft-until-Publish lifecycle — just performed **directly on the flat
  self-registered pool**, by Industry or a permitted Institution (same
  additive-delegation pattern as Level 1/Level 2), not per-contingent.
  Reserve/waitlist semantics carry over unchanged (real named people,
  promotable on a Main-zone withdrawal).
- **Team under Open — confirmed:** individuals self-register (tagged by
  Sub-Type); Selection distributes them across up to `Max Team` team
  instances, filling each team's fixed role-slot template (§3.6.1) from
  the shared pool — same role-slot mechanic as Closed Team registration,
  just sourced from the Open pool instead of an Institution's own roster.
- **Sequencing:** (1) Industry/permitted Institution sets Max Entries +
  Selection target and Publishes; (2) Self-Register opens until Max
  Entries hits (hard block); (3) Industry/permitted Institution runs
  Selection to exact-fill, same Publish gate as Nomination.
- **Not resolved:** whether registration can be manually closed before
  Max Entries is reached (deadline vs. pure capacity cutoff); Selection
  method is assumed manual (lottery flagged as a documented market
  alternative, not adopted).

---

### 3.6 Node 4 — Registration & Nomination

Unlocks once Quota Setup's Level 2 fully rolls up to Published (every
sport published). A contingent Institution (holding a Level 2
QuotaGrant) does two things in sequence: **Registration** (onboard users
against its allocation) and **Nomination** (assign registered users into
specific Discipline slots).

#### 3.6.1 Registration

- **Granularity depends on whether the underlying quota was
  Discipline-split:**
  - Individual-type quota allocated **at Discipline level** (e.g.
    separate Athlete entries for 100m and 200m) → Registration pools at
    **Sport** granularity (register the combined count under the sport;
    the Discipline-level split happens later, in Nomination).
  - Quota allocated directly **at Sport granularity** with no
    Discipline split (e.g. Coach entries for Football) → Registration
    **is** the final step, no Nomination follows.
  - **Team-type quota** → Registration happens directly at **Discipline**
    granularity, per team (see role-slot model below); no Nomination
    follows either.
  - **General rule:** Nomination exists only to resolve a Sport-level
    pool into Discipline-level allocations. Wherever Registration already
    lands at final granularity (Team, Sport-only entries), Nomination is
    skipped entirely.
- **Registration cap (Individual sport-pool):** the registered count must
  equal **exactly `Σ (Q + R)`** across that contingent's Discipline-level
  entries in that sport — not `Σ Q` alone, since Reserve slots get filled
  with real named individuals during Nomination, not just tracked as a
  number. **Flagged v1 risk, unsolved:** this exact-match target is
  brittle against withdrawals/replacements before Nomination locks in —
  no adjustment mechanism is defined (see §8).
- **Team registration — role-slot model:** the contingent creates team
  instances via **"+ Add Team"**, up to its Level 2 `Q` allotment. Each
  instance inherits the **same fixed role-slot template** from Level 1
  (Playing + Reserve slots, per User Type/Sub-Type — e.g. Batsman Playing
  ×5/Reserve ×1). Every slot hard-blocks once filled and is
  role-specific (a Batsman can't fill a Bowler slot). No separate
  Nomination step, for the same reason as above — already final
  granularity. Inherits the same exact-fill/withdrawal-brittleness risk,
  per slot.
- **Registration methods, gated by Mode of Registration (§3.4):**
  - **Manual** — the contingent fills the leaf's form on the user's
    behalf; the user gets an emailed password-reset link (contingent
    never sets a password for them).
  - **Invite** — the contingent invites, the user self-fills and
    accepts, the contingent approves. Reuses ATOM's **existing**
    invite/approve mechanism as-is, not a new build.
  - **Self-Register** (Open leaves only) — the Individual finds the form
    and submits it directly, no Institution action either side. Hard-
    blocks against Open Quota's Max Entries (§3.5.4) instead of picking
    an Institution.
- **Multi-sport reuse:** an Individual keeps exactly one User Type per
  Project (existing ATOM rule). Since User Types are Project-scoped, not
  Sport-scoped, the same person under the same User Type can be added to
  a **second sport's** pool via "Search Existing User" — no second
  form-fill, no second account. **Explicit v1 risk, accepted, not
  solved:** no concept of sport-affinity on a User Type exists (e.g.
  nothing stops a "Batsman" quota entry under Football). **Explicitly out
  of scope for v1:** the same Individual holding two *different* User
  Types in one Project (e.g. Batsman for Cricket *and* Player for
  Football) — deferred to v2, would require relaxing ATOM's
  one-User-Type-per-Individual-per-Project rule.

#### 3.6.2 Nomination — Individual only

Team never reaches Nomination (already final at Registration, §3.6.1).

- **Mechanic:** two panels. Left panel lists the contingent's registered,
  not-yet-fully-nominated individuals for the selected sport. Right panel
  lists **Discipline boxes** the sport's Level 2 allocation covers. The
  contingent **drags an individual onto a Discipline box** to nominate
  them. Only Discipline-split User Types appear (Sport-granularity-only
  User Types like Coach, and Team entries, never reach this screen).
- **Multi-Discipline nomination:** allowed, **uncapped in v1** (e.g. the
  same athlete nominated to both 100m and 200m). A configurable
  per-individual cap is planned for **Flow Builder Phase 2**, not v1.
- **Two zones per Discipline box: Main (`Q`) and Reserve (`R`)** — both
  filled with **real named individuals** (Reserve is not just a number at
  this stage), both hard-blocked at their ceiling.
- **Status model:** draft until the contingent explicitly **Publishes**
  (drag-back-out allowed before that). **Publish gate — exact fill, no
  partial publish:** every Discipline box's Main **and** Reserve zone
  must be exactly full for every Discipline in that sport. No Min-based
  partial-publish allowance (unlike Level 2). No deadline enforcement
  exists in the system currently — Publish is a manual action.

#### 3.6.3 Registration Status Dashboard

Resolved separately (2026-08-22) — its exact place *within* this node
(second tab vs. separate node vs. dropped) is still **unconfirmed** (see
§8), but its **shape** is locked:

- **A single flat, filterable table** — deliberately *not* the Sport-
  first left-rail convention every other node uses, because the ask is
  explicitly cross-sport (a multi-sport individual should appear in one
  place, not fragmented across per-sport panels).
- **One row = one individual × one Discipline** they're filled against
  (a multi-Discipline individual appears as multiple rows; a Team
  registrant's row reflects their role-slot status the same way).
- **Columns:** Name, User Type/Sub-Type, Sport, Discipline, Institution,
  Status (Main/Reserve). **Filters:** Sport, Discipline, User Type,
  Status, name/ID search.
- **Role scoping is row-level, not menu-level** (a deliberate contrast
  with Quota Setup Level 2's rail-based scoping): Master Admin/Industry
  see an Institution column and unrestricted rows across everything; a
  contingent Institution sees the same table with the Institution column
  dropped and rows pre-filtered to its own people, but still spanning
  *all* its sports (not one sport at a time).
- **Read-only**, downstream of Registration + Nomination data — no node
  depends on it Publishing.

---

## 4. RBAC / Permission model (Flow Builder-wide)

Two RBAC surfaces, not one:

1. **Industry-workspace roles:** **Flow Builder Editor** (full authoring
   — Hierarchy tab, Sports Setup, Registration Setup, Quota Setup,
   manual nomination/override, quota reclamation) and **Flow Builder
   Viewer** (read-only, including the Registration Status Dashboard).
2. **Institution-workspace roles:** **FA Executor** (operates its
   granted FA's Tasks, pre-existing/unchanged); **Quota-holder** (v1:
   view own QuotaGrant + fill-progress read-only; self-registration
   happens via the node's own Registration flow, not a separate
   mechanism; cannot see other Institutions' data or edit config);
   **Sub-canvas Operator** (defined, but capability-gated off until
   Hierarchy's Institution-operated-sub-canvas dependency ships — when
   enabled: edit own sub-canvas one level only, cannot edit structure
   above itself).

**Default-deny:** none of these roles are auto-granted by merely holding
an FAGrant or QuotaGrant — each requires an explicit RBAC assignment.
Only **three roles** (Master Admin, Industry, Institution) are in scope
for Quota Setup access specifically; **Individual is out of scope** for
Quota Setup permissions.

---

## 5. Cross-cutting architectural rules

Facts that hold across every node — worth knowing before designing
another module that touches the same data:

- **Quota is a grant on the Institution** — no separate "Contingent"
  entity exists in the data model. `ProjectParticipation` carries two
  independent optional grants: **FAGrant** (pre-existing) and
  **QuotaGrant** (new). "Contingent" is informal shorthand for
  "quota-granted Institution," not a system entity.
- **GTCC/NSF/SSV-style oversight bodies are plain Institutions,
  Industry-labeled** — no `OversightBody` entity exists. An Industry
  running a National Games just names ordinary Institution nodes that.
- **QuotaTrack is fully generic** — an optional grant Industry attaches
  to *any* User Type/Sub-Type it has configured, with its own
  granularity (Discipline / Sport / Project), not a fixed hardcoded set.
- **An Individual holds exactly one User Type per Project** — a
  pre-existing ATOM rule, not something Flow Builder can relax (see the
  multi-User-Type limitation in §3.6.1/§8).
- **No edit-after-registration protection in v1** — config edits (e.g.
  cancelling a Discipline, removing a User Type) are unprotected once
  live data exists against them; can silently corrupt nomination data.
  Accepted v1 risk, deferred to v2.
- **Config artifacts are versioned and emitted, not hard-wired** — Flow
  Builder emits versioned config artifacts that downstream modules
  (Schedule, Reports, Institution Dashboard) subscribe to *if* present
  and licensed — those modules are not hard-coded dependents.
- **Standard-field → Individual Profile pre-fill is deferred** — fields
  ship as **project-scoped** for now (a global profile schema doesn't
  exist yet and would conflict with other canon).
- **Reserve/waitlist semantics (shared across Level 2, Nomination, and
  Open Selection):** Reserve is an **ordered waitlist**, promoted
  **manually** in v1 (no auto-promotion). Wildcard/overflow draws from
  Reserve, never from Max.
- **Unused-slot reclamation is deadline-gated** — reclaiming an
  allocated-but-unused slot only opens after the relevant deadline, not
  ad hoc.

---

## 6. Roadmap / explicitly deferred items

Not v1 scope, but named and intentional (not forgotten):

- **Flow Builder Phase 2:** a per-individual cap on multi-Discipline
  Nomination (currently uncapped).
- **v2:** edit-after-registration protection/managed-teardown workflow;
  mixed User Types on one Individual within one Project; Institution-
  operated (own-workspace) sub-canvas.
- **Workflow automation / universality (2026-08-10 PM review, status:
  raw/under review, no decisions locked):** proposes formalizing the
  three already-decided time/state triggers (nomination deadline,
  deadline-gated reclamation, Min deadline-block) into one thin
  "Nomination-window automation" primitive plus deadline-reminder
  notifications, rather than a generic runtime Automation node. Also
  flags that Sports Setup's now-mandatory Master-Admin-fixed taxonomy is
  in tension with a stated "avoid hardcoded National-Games-only
  workflows" universality goal — an open, unresolved question, not
  answered by this document.

---

## 7. Open items — consolidated, not yet decided

Do not treat any of the following as locked. Grouped by node:

**Quota Setup — Eligibility:**
- Custom criteria enforcement mode default (hard-block vs.
  informational-flag) — not yet decided. In discussion 2026-09-11
  (three options on the table: per-criterion type-based toggle / all
  hard-block / all informational-flag; a worked example was recorded in
  `atom-flow-builder-quota-setup.md` §10.7) — explicitly parked
  mid-discussion, not resolved.
- **New, 2026-09-11:** how does a custom Eligibility criterion actually
  get a capture field added to the relevant Joining Form(s)? Eligibility
  only evaluates already-captured data (§3.5.1/§10.5) — nothing yet
  connects "Industry adds a custom Eligibility criterion at Quota Setup"
  to "a matching field must exist on the Joining Form." Not designed.
- Interaction with Team's role-slot model — assumed one Eligibility rule
  per Discipline×User Type entry (all Batsmen share one age window), not
  per role/slot — not explicitly discussed.

**Quota Setup — general:**
- **Sub-Type nested-ceiling model** (does a User Type *with* Sub-Types
  get one Level 1 entry per Sub-Type, or a roll-up parent+children
  ceiling?) — **resolved for Team** (Industry picks the breakdown);
  **still open for Individual/Double/Project-or-Sport-granularity
  entries** — no worked example has exercised that case.
- Open Quota: early-close-before-cap (deadline vs. pure capacity cutoff)
  not designed; Selection method assumed manual, lottery flagged as a
  possible future alternative, not adopted.

**Registration Setup:**
- Mode of Registration's **Closed default** is a reasonable assumption,
  not explicitly confirmed by Abhijeet.
- Self-Register discovery surface (how an Individual finds an Open leaf)
  and account-creation flow — not designed.

**Registration & Nomination:**
- **Exact-match registration's brittleness against withdrawals/
  replacements** — accepted v1 risk across Individual and Team; no
  defined behavior for what happens when a registered/nominated person
  withdraws (does the slot reopen? is there a swap deadline?). Flagged
  repeatedly, never designed.
- **Registration Status Dashboard's place within this node** — second
  tab, separate node, or something else — genuinely unconfirmed, not to
  be assumed from an earlier tentative exchange.
- Team-under-Open's role-slot Selection reuse (§3.5.4) — proposed and
  confirmed by Abhijeet, but worth re-verifying against this doc if it
  resurfaces, since it was a late addition.

**Hierarchy tab:**
- Exact role/permission granularity for an Institution operating its own
  sub-canvas — carried into RBAC (§4), not fully spelled out.

**Cross-cutting:**
- Sport-affinity on User Types (nothing stops a Cricket-specific User
  Type like "Batsman" from being used under an unrelated sport) —
  accepted v1 gap, not designed around.
- Automation/universality (§6) — under review, no decisions locked.

---

## 8. Source documents (for deeper detail)

This file is a synthesis. For full reasoning, worked examples, and
decision history, the authoritative per-topic docs are:

- `2026-07-20-flow-builder-ideation-v2.md` — foundational decisions
  (D1–D13): quota-as-grant data model, Hierarchy tab, RBAC, config
  versioning. **Its Sports Setup (D15), Registration Setup (D17), and
  early Quota Setup (D22–D28) sections are superseded** by the docs
  below — read those instead for current node design.
- `atom-flow-builder-sports-registration-setup.md` — Registration Setup
  full detail (§14–30) and Sports Setup v2 (§32, current) + Mode of
  Registration (§33).
- `atom-flow-builder-quota-setup.md` — Quota Setup Level 1/Level 2
  (§1–8), Open Quota (§9), Eligibility (§10) — all current.
- `atom-flow-builder-registration-nomination.md` — Registration +
  Nomination, both halves, current.
- `2026-08-22-flow-builder-open-items-followup.md` — the open-items
  queue (Registration Status Dashboard shape resolved here; Sub-Type
  nested-ceiling and withdrawal-brittleness still tracked here).
- `2026-08-25-sports-library-master-admin-change-impact.md` — the
  change-impact analysis that produced the Master-Admin-owned sports
  taxonomy pivot (background/history; superseded by the v2 canvas docs
  above for current shape).
- `2026-08-10-flow-builder-workflow-automation-universality-review.md` —
  automation/universality review, status raw/under review.
- `README.md` (this folder) — chronological index of every entry above,
  useful for tracing *when* and *why* something changed.

---

**Last synthesized:** 2026-09-11, reflecting Eligibility's enforcement
scope, cutoff-date default, and Gender field shape all being confirmed
same-day. The custom-criteria-enforcement-mode discussion (§7) was
parked mid-conversation the same day, pending resumption — see
`atom-flow-builder-quota-setup.md` §10.7 for the worked example already
produced. Re-sync this file after that resolves, or before handing it to
another PM if meaningful time has passed.
