# ATOM Flow Builder — Quota Setup (Level 1 & Level 2)
## Finalized Ideation

## 1. Purpose

Quota Setup is the third of the four fixed nodes in the **Registration & Quota
Setup** tab:

```text
Sports Setup
      ↓
Registration Setup
      ↓
Quota Setup
      ↓
Registration & Nomination
```

Clicking **Quota Setup** opens a dedicated sub-canvas. Unlike the other three
nodes, Quota Setup is internally split into **three sequential stages**
as of 2026-09-10 (was two — see §10):

```text
Eligibility  (who's allowed in — age/gender/custom criteria, §10)
   ↓
Level 1  (Overall Quota — the national/event ceiling)
   ↓
Level 2  (Contingent Allocation — distributing that ceiling to Institutions)
```

(For Open-mode entries, §9, the equivalent shape is Eligibility → Open
Quota → Selection — same Eligibility stage, no Level 2.)

There is no Level 3 inside Quota Setup. What happens after quota is
allocated — an Institution registering and nominating its athletes against
its allocation — is the job of the separate, final **Registration &
Nomination** node, not a fourth tab here.

---

# 2. Level 1 — Overall Quota

## 2.1 Purpose

Level 1 defines the total ceiling for a Sport Discipline, per User Type/Sub-Type,
before any distribution to Institutions happens. This is where the
authoritative body (e.g. GTCC for a National Games) states: *"this many
slots exist for this Discipline, for this User Type."*

## 2.2 Who configures it

An Institution can be given **assigned access** to configure Level 1 (e.g.
GTCC). Industry (and Master Admin) can **also** configure and view Level 1
directly at any time — assigning an Institution is additive delegation, not
an exclusive handoff. See §6 (Permission & Views) below.

## 2.3 Fields — vary by Discipline Type

The Discipline's Type (Individual / Double / Team, a Master-Admin-owned
attribute from Sports Setup) determines which fields apply. Quota is
always scoped to one **Discipline** leaf (inherited from Sports Setup —
Sport → Discipline, no Category/Event level survives) and one **User
Type/Sub-Type**.

### Individual
- **Max** — total individual entries allowed.
- **Min** — floor for individual entries.
- **Reserve** — reserve individual slots.

### Double
- **Max Pairs** — total pairs allowed.
- **Min Pairs** — floor for pairs.
- **Reserve Pairs** — reserve pairs.
- A pair is always exactly 2 members; there is no separate members-per-pair
  field — pair size is a sport fact, not a quota decision.

### Team — updated 2026-09-10, see §8
- **Max Team** — total number of teams allowed.
- **Min Team** — floor for number of teams.
- **Playing breakdown, per User Type/Sub-Type** — Industry splits the
  Discipline's Master-Admin-fixed playing-member total (e.g. Cricket = 11)
  across User Types/Sub-Types (e.g. 5 Batsman / 4 Bowler / 2 All-Rounder,
  or left flat/generic under one Sub-Type). Must sum to **exactly** the
  MA-fixed number.
- **Max/Min Reserve (per team), per User Type/Sub-Type** — reserve slots
  per team, tagged the same way as the playing breakdown (not a separate
  national reserve pool — every team fielded by any Institution gets this
  many reserve slots per role).
- **No "Max/Min Team Members" field.** The playing-member total itself
  (11 for Cricket, 2 for a Doubles-shaped team sport, etc.) is fixed by
  Master Admin on the Discipline, in their portal — not authored in Flow
  Builder. Industry only breaks that fixed number down by role (above); it
  can no longer resize the squad.

## 2.4 Quota entries are created, not pre-populated

The system cannot know in advance which User Type(s) need quota for a given
Discipline — that is GTCC's real-time judgment call, and not every enabled
User Type needs Discipline-tied quota. So Level 1 is **not** a pre-filled matrix.
Instead:

- Each Sport's main panel shows a **list of quota entries already added**
  for that sport (read: created, not blank cells waiting to be filled).
- **"+ Add Quota"** opens a short guided flow: pick **User Type**, pick
  **Granularity** (Discipline / Sport / Project — same three-way
  choice already established for quota tracks), and if Discipline-level,
  pick the specific **Discipline**. The field set shown then adapts to
  that Discipline's Type (§2.3).
- Once created, an entry's numeric fields are **directly editable inline**,
  autosaving on blur — no further modal. There is no separate row-level or
  page-level Save button; every field commits the instant you leave it.

```text
SWIMMING                                              [+ Add Quota]
User Type    Scope                Max  Min  Reserve
Athlete      100m Freestyle (M)   [40] [10]  [4]
Athlete      200m Freestyle (M)   [40] [10]  [4]
Athlete      4x100m Relay (Team)  [12] [4-4]  [2]
Official     All Swimming (Sport) [ 8] [ 2]   [1]
Coach        All Swimming (Sport) [ 6] [ 2]   [0]
```

This applies to **any enabled User Type**, not just Athlete — an Institution
with Level 1 access can set quota for Athlete, Coach, Official, or any other
enabled User Type, for any enabled Sport Discipline, choosing the combination in
real time. Registration/joining for a given User Type only opens once that
User Type has **both** a published Joining Form (Registration Setup) **and**
quota set here.

## 2.5 Canvas layout

- **Left rail:** Sports (from Sports Setup), each showing its quota-entry
  count.
- **Main panel:** the selected Sport's quota entries, as above.

## 2.6 Publish gate

Level 1 becomes publishable once **every enabled Discipline leaf**
(from Sports Setup) has **at least one** quota entry, for at least one User
Type. It does not require every enabled User Type to have an entry on every
Discipline — only that no Discipline is left with zero entries. A
Discipline with only Sport-level or Project-level entries covering its
sport (and no Discipline-level entry of its own) does **not** satisfy this
gate; an enabled Discipline with nobody competing in it is treated as a
real gap, not a valid state.

---

# 3. Level 2 — Contingent Allocation

## 3.1 Purpose

Level 2 distributes each Level 1 ceiling across Institutions ("contingents")
— e.g. the Athletics Federation splitting the national 200m ceiling of 50
across India: 10, China: 5, USA: 20, etc.

## 3.2 Who configures it

**Permission is granted per Sport**, to one Institution (e.g. Athletics
Federation for Athletics, Swimming Federation for Swimming). Real-world
federations own a whole sport, not individual Disciplines, so delegation
is scoped at the sport level; the actual allocation work inside that
permission still happens **per Discipline**, since that's where the Level
1 ceilings live. Industry/Master Admin can configure and view Level 2 for
**every** sport, same additive-delegation model as Level 1.

## 3.3 Institutions are added, not pre-populated

Contingent Institutions are **not** automatically listed. The configuring
Institution explicitly picks which existing Institutions (sourced from the
Hierarchy tab / Institutions with ProjectParticipation) to allocate to, via
an **"+ Add Institution"** picker, scoped to **one Level 1 entry at a time**
— adding India to the 200m entry does not add India to 100m or to the
Officials entry; each Discipline's allocation is independent, matching how
the ceilings themselves are independent per Discipline.

**Individual / Double** — per Institution, `Q` (allocated) and `R`
(reserve):

```text
Athlete — 200m (M)                                  [+ Add Institution]
Max:  Total 50 · Allocated 35 · Unallocated 15
Reserve: Total 4 · Allocated 3 · Unallocated 1

Institution          Q      R      Active
India                [10]   [1]     [x]
China                [ 5]   [0]     [x]
USA                  [20]   [2]     [x]
```

Enforcement (hard block): `Σ Q ≤ Max`, `Σ R ≤ Reserve`.

**Team — updated 2026-09-10, locked (§8).** Per Institution, **`Q` only =
number of teams allotted** — no `R` column, and no per-Institution role
breakdown either. The playing breakdown (e.g. 5 Batsman/4 Bowler/2
All-Rounder out of an MA-fixed 11) and the role-tagged Reserve are a fixed
**template** set once at Level 1 on the Discipline entry — identical for
every team, regardless of which Institution fields it. Level 2 only
decides **how many teams**, never how any one team is composed:

```text
Cricket — Men's Senior (Team)                        [+ Add Institution]
Max Team: 16 · Min Team: 8
Playing breakdown (per team): Batsman 5 · Bowler 4 · All-Rounder 2
Reserve (per team): Batsman 1 · Bowler 1 · All-Rounder 0

Institution          Q (teams)   Active
India                [3]          [x]
Australia             [2]          [x]
England                [1]          [x]
```

Enforcement (hard block): `Σ Q ≤ Max Team`.

## 3.4 Canvas layout

- **Left rail:** Sports (filtered by role — see §6).
- **Main panel:** the selected Sport's Level 1 quota entries; selecting one
  expands its per-Institution allocation table.

## 3.5 Publish gate — per sport, with an automatic node-level rollup

- Each sport publishes **independently**, once **every quota entry under
  that sport has `Σ Q ≥ Min`** (the floor, not the full ceiling) — across
  every Discipline and every User Type in that sport.
- **No override or waiver.** If Min cannot be met, that sport simply cannot
  publish. There is no Industry force-publish escape hatch in v1 — a
  Discipline's games cannot be considered ready to start until its Min has
  genuinely been allocated to real Institutions.
- **Level 2 as a whole has no manual Publish action.** Its status is a pure
  rollup: it automatically flips to Published the moment **every** enabled
  sport has individually published.
- **Registration & Nomination (the last node) does not unlock per sport.**
  It waits for Level 2's full rollup — i.e. every sport published — before
  any Institution can begin registering or nominating athletes, even if
  some sports (e.g. Swimming) finished long before others (e.g. Athletics).

---

# 4. Permission & Views (both levels)

Three roles can access Quota Setup: **Master Admin**, **Industry**, and
**Institution**. Individual is out of scope for now.

- **Master Admin / Industry:** unrestricted. At Level 1, the left rail shows
  **every** Sport, editable directly regardless of any Institution
  assignment. At Level 2, the left rail shows **every** Sport's full
  allocation table across all Institutions — not just the sport(s) any one
  Institution was assigned. Delegating configuration to an Institution never
  removes Industry's own ability to configure or view.
- **Institution:** scoped to its assignment. At Level 1, an assigned
  Institution's left rail shows only the sport(s) it's been given access to.
  At Level 2, an assigned Institution (federation) sees only its **one**
  assigned sport, and within it allocates that sport's events to other
  Institutions.

The canvas, fields, and interactions are otherwise identical across roles —
only the left-rail Sport list (and therefore what's editable) changes by
role. This is the same screen rendering multiple views, not separate
screens per role.

---

# 5. Relationship to prior Quota Setup canon

This Level 1 / Level 2 split refines, rather than discards, the Quota Setup
data model already established (OverallQuota, QuotaGrant, granularity
choice, the Budget-Tracking-style Total/Allocated/Unallocated convention).
Specifically:

- **Level 1 = OverallQuota authoring.** Same fields in spirit (Max/Min/
  Reserve/Max Team), now split further by Discipline Type (Individual/Double/
  Team each have a different field set) and created via a guided flow
  instead of a pre-rendered grid, since the User Type applicable to an event
  isn't known in advance.
- **Level 2 = Contingent Allocation / QuotaGrant authoring.** Same Q/R-per-
  Institution shape, same `Σ Q ≤ Max` / `Σ R ≤ Reserve` hard-block
  enforcement, now with Institutions added explicitly per event rather than
  listed automatically.
- **Ceiling-only Publish is no longer sufficient for the node as a whole.**
  Level 1 alone can still publish without any grants existing (consistent
  with the old allowance), but the full Quota Setup node does not unlock
  Nomination until Level 2 also rolls up to Published — which requires real
  Min-satisfying grants to exist. This is a deliberate tightening.
- **Min enforcement now has a concrete mechanism**, replacing the earlier
  inferred ratchet-plus-deadline-block approach: Min is a **hard
  precondition for a sport's Level 2 Publish** (`Σ Q ≥ Min` on every entry
  in that sport), not a runtime ratchet during the nomination window.
- **Open item, partially resolved 2026-09-08 (see §8):** whether a User
  Type with Sub-Types (e.g. Athlete → Senior/Junior) needs one Level 1
  entry per Sub-Type or a roll-up parent/child ceiling. **Resolved for
  Team** — Industry breaks the MA-fixed playing total down per User
  Type/Sub-Type (§2.3, §8). **Still open for Individual/Double/Project-
  or Sport-granularity entries** — no worked example there used a User
  Type with Sub-Types.
- **Open item carried forward:** custom quota fields (beyond the standard
  Max/Min/Reserve/Team fields) were part of the earlier model; whether
  Level 1 still supports Industry-defined custom fields per entry was not
  revisited in this design pass.

---

# 6. Final Locked Scope

## Level 1 — Overall Quota
- Structure: quota entry = (Sport Discipline **or** Sport **or** Project) ×
  User Type/Sub-Type, with Max/Min/Reserve fields shaped by Discipline Type.
- Entries created via guided "+ Add Quota" flow; edited inline with
  autosave.
- Any enabled User Type can receive quota on any enabled Sport Discipline —
  GTCC's real-time choice, not a fixed matrix.
- Publish gate: every enabled Discipline leaf has ≥1 quota entry.
- **Team entries (§8):** no squad-size field — the playing-member total is
  Master-Admin-fixed on the Discipline. Industry only sets Max/Min Team
  count, the User Type/Sub-Type breakdown of that fixed total (must sum
  exactly), and Max/Min Reserve per team, User Type/Sub-Type-tagged.

## Level 2 — Contingent Allocation
- Structure: per Level 1 entry, a list of Institutions each with Q
  (allocated) and R (reserve), added explicitly, not pre-populated.
- Team-type entries: Q only, no R — a team's role breakdown and reserve
  shape are already fixed per-team at Level 1, so every team any
  Institution fields carries them automatically.
- Hard-blocked at `Σ Q ≤ Max`, `Σ R ≤ Reserve`.
- Publish: per sport, gated on `Σ Q ≥ Min` on every entry in that sport, no
  override. Level 2 node-level status auto-rolls up once every sport
  publishes.
- Registration & Nomination (next node) gates on Level 2's full rollup, not
  per-sport.

## Permission & Views
- Roles: Master Admin, Industry, Institution (Individual out of scope).
- Master Admin/Industry: full, unrestricted access to every sport at both
  levels, in addition to whatever Institution is assigned.
- Institution: scoped to its assignment — Level 1 to its assigned sport(s),
  Level 2 to its one assigned sport.
- Same canvas renders different views by filtering the left rail per role.

---

# 7. Overall Flow (updated)

```text
                    FLOW BUILDER
                         │
          ┌──────────────┴──────────────┐
          │                             │
   Hierarchy Setup           Registration & Quota Setup
                                        │
                    ┌───────────┬───────┴───────┬──────────────────────┐
                    │           │               │                      │
             Sports Setup  Registration    Quota Setup          Registration
                            Setup               │                & Nomination
                                    ┌─────────┬──┴──────────┐
                                    │         │             │
                                Eligibility Level 1       Level 2
                                (§10)     (Overall      (Contingent
                                           Quota)        Allocation)
                                   (for Open entries, §9: Eligibility →
                                    Open Quota → Selection, no Level 2)
```

**Current finalized scope:** Sports Setup + Registration Setup + Quota
Setup (Level 1 & Level 2).

Registration & Nomination remains for subsequent ideation.

---

# 8. Update (2026-09-10) — re-pointed to Discipline; Team model confirmed

**Problem it solves:** Sports Setup's canvas flattened to a Master-Admin-
owned **Sport → Discipline** tree (no Category/Event level; Gender, Type,
and a fixed playing-member count all live on Discipline) — see the
2026-09-09 Sports Setup lock. Quota Setup was the one node still pointing
at the old Sport → Category → Event → leaf shape and needed to re-point.

**Terminology, throughout this doc:** every prior reference to "Event" or
"Event × Category" as the quota leaf now means **Discipline**. "Event
Type" (Individual/Double/Team) is now **Discipline Type**, a Master-Admin-
owned attribute rather than something Sports Setup authored. This is a
rename, not a structural change, for **Individual** and **Double** — both
field sets (§2.3) are unchanged in shape.

**Team is functionally unchanged from the 2026-09-08 resolution** (recorded
in `2026-08-25-sports-library-master-admin-change-impact.md` §6, never
previously written into this doc) — today's discussion confirmed it, it
did not re-open it:

- **Max Team / Min Team** — count of teams, unchanged.
- **Playing breakdown, per User Type/Sub-Type** — Industry splits the
  Discipline's Master-Admin-fixed playing-member total (e.g. Cricket = 11)
  by role (e.g. 5 Batsman/4 Bowler/2 All-Rounder), summing to **exactly**
  that total. **This was the one field Abhijeet's initial 2026-09-10
  restatement of the Team model omitted** — flagged and confirmed it
  should stay (see below), not be dropped.
- **Max/Min Reserve per team, per User Type/Sub-Type** — same role-tagging
  as the playing breakdown, so a reserve slot has a role to register
  against.
- **No Max/Min Team Members field** — squad size is Master-Admin-fixed on
  the Discipline, in their portal, not authored in Flow Builder.

**Why the playing-breakdown field was flagged:** dropping it would have
left the playing squad's *role composition* completely unconstrained
(only the flat MA-fixed total would bound it) while Reserve stayed
role-tagged — backwards from how real team-sport rosters work (e.g. a
cricket XI needs a realistic batsman/bowler split, not just 11 of
anything). Abhijeet confirmed: **keep it, add it back alongside
Reserve** — both playing and Reserve are User Type/Sub-Type-tagged;
only the raw headcount (11, 2, etc.) moves to Master Admin.

**Level 2 for Team — confirmed, locked (second pass, same day).** Per
Institution, Level 2 allots **`Q` only = number of teams**, hard-blocked
at `Σ Q ≤ Max Team` (see §3.3's Cricket worked example). No `R` column,
and **no per-Institution role breakdown** — the playing breakdown and
role-tagged Reserve set at Level 1 are a fixed template, identical for
every team any Institution fields. Level 2 never re-decides team
composition, only team **count**. Once an Institution has a `Q`
allotment, it can start registering (feeds the existing `Σ (Q + R)`
per-contingent cap in Registration & Nomination — for Team, that's
`Q` teams × the fixed per-team shape, not a separately-allotted `R`).
Individual/Double Level 2 is unchanged from the original 2026-08-21
design (§3.3) — `Q`/`R` per Institution, same `Σ Q ≤ Max`/`Σ R ≤ Reserve`
enforcement, just Discipline-scoped instead of Event-scoped.

**Also resolves part of the §5 Sub-Type-nested-ceiling open item:** the
Team case is now answered (Industry-picks-the-breakdown, matching the
already-adopted pattern). Individual/Double/Project-or-Sport-granularity
entries with Sub-Typed User Types remain open — no worked example has
exercised that case yet.

**Related:** `2026-08-25-sports-library-master-admin-change-impact.md` §6
(the original Team model resolution, 2026-09-08); Sports Setup §32 in
`atom-flow-builder-sports-registration-setup.md` (the Discipline tree this
re-points to).

**Still nothing committed to `atom-context.md`/`backlog.md`** — gated on
a separate go-ahead, per the rest of this thread.

---

# 9. Open Quota (new, 2026-09-10)

## 9.1 Why this exists

Registration Setup §33 (`atom-flow-builder-sports-registration-setup.md`)
added a per-leaf **Mode of Registration** — Open leaves get a third
registration method, **Self-Register** (§2.8 of
`atom-flow-builder-registration-nomination.md`), with no Institution
involved. When that was designed, whether Self-Register needed any quota
concept at all was explicitly parked — Abhijeet had heard Open events
skip quota entirely and gate on Eligibility instead. Market research
(marathon registration, open-tournament entry models) plus Abhijeet's own
follow-up correction settled it: **Open events do cap registration, just
differently from Closed** — the standard pattern is an **oversubscribed
entry cap, followed by a selection cut** (e.g. Big Sur Marathon-style
capacity + waitlist; open-trial cricket tournaments capping applicants
above the playing squad size). Eligibility (age/gender/qualifying
standard) remains a **separate, still-undesigned** gating layer — a
different question (who's allowed to enter) from this one (how many can
enter, how many get selected).

## 9.2 Structural difference from Closed: no Level 2

Open Quota is a **single flat pool** — there is no per-Institution
distribution step, because there's no Institution: registrants come
straight from Self-Register, not from a contingent. Level 2 (§3) does not
apply to Open entries at all.

## 9.3 Two numbers, not one — the core new concept

Closed's Registration is an **exact-match** target: a contingent
registers precisely `Σ (Q + R)`, no more, no less (§2.4/§3.4 of the
Registration & Nomination doc). Open is deliberately **not** exact-match
— oversubscription is the point, same as a marathon accepting far more
entries than the qualifying field, or a cricket tournament running trials
above squad size:

- **Max Entries (new field)** — the Registration Cap (Abhijeet's "100").
  Self-Register hard-blocks once hit. This is the one genuinely new
  number in the whole model.
- **Selection target — reuses the existing Level 1 field shape exactly**,
  by Discipline Type (Abhijeet's "50" plus however much Reserve/waitlist
  is set):
  - Individual: Max / Min / Reserve (§2.3).
  - Double: Max Pairs / Min Pairs / Reserve Pairs (§2.3).
  - Team: Max Team + User Type/Sub-Type playing breakdown + role-tagged
    Reserve (§8) — see §9.5 below for how Individual entries feed a
    Team's role slots under Open.
- **`Max Entries ≥ Selection target total` is expected**, not enforced
  equal — the slack between them is the entire reason this exists.

## 9.4 Selection reuses Nomination's mechanic, doesn't reinvent it

Once Self-Register has filled the pool (up to `Max Entries`), **Selection**
is the Open equivalent of Nomination (§3) — **same drag-and-drop
mechanic, same Main/Reserve zones, same hard-block-at-ceiling behavior,
same draft-until-Publish lifecycle with exact-fill required to Publish**.
The only real difference: it's performed **directly on the single flat
self-registered pool**, not per-contingent.

- **Who performs Selection:** Industry, or a **permitted Institution** —
  same additive-delegation pattern already used for Level 1/Level 2
  access elsewhere (§4). Industry can always do it; delegating to an
  Institution is additive, not exclusive.
- **Reserve/waitlist semantics carry over unchanged:** individuals
  selected into the Reserve zone are real, named people (not just a
  number), promotable if a Main-zone selectee withdraws — same
  mechanic already locked for Closed Nomination, same unresolved
  withdrawal-brittleness flag (Item 4, carried forward, now also
  covering Open).

## 9.5 Team under Open — individuals self-register, Selection distributes them into role slots — CONFIRMED 2026-09-10

Abhijeet's own worked example ("Cricket — Mens T20... 100 Individuals can
register with maybe certain User Type/Sub-Type... 50 can play") describes
Individual-shaped entry into a **Team**-type Discipline — self-registered
Batsmen/Bowlers/All-Rounders, not self-registered whole teams. **Confirmed
by Abhijeet:** the pool of self-registered Individuals (tagged by
Sub-Type) is the entry side; Selection distributes them across up to
`Max Team` team instances, filling each team's fixed role-slot template
(§2.7) from the shared pool — the same role-slot mechanic already built
for Closed Team registration, just sourced from Open's flat pool instead
of an Institution's own roster.

## 9.6 Sequencing (proposed, mirrors the Closed flow's shape)

1. Industry/permitted Institution sets `Max Entries` + the Selection
   target on the Open entry ("Open Quota" configuration) and Publishes —
   mirrors Level 1's Publish gate.
2. Self-Register opens; Individuals register until `Max Entries` is hit
   (hard block).
3. Industry/permitted Institution runs Selection — drags from the flat
   pool into Main/Reserve (or Team role-slots) — same exact-fill Publish
   gate as Nomination.

**Open items, not resolved in this pass:**
- Whether registration can be manually closed **before** `Max Entries` is
  reached (a deadline rather than a pure capacity cutoff) — not
  discussed.
- Selection method is assumed **manual** (drag/select by Industry or
  Institution, matching "decided by the Industry or Institution who has
  permission") — research surfaced lottery-based selection as a common
  alternative for oversubscribed events; not adopted here, flagged as a
  possible future option, not v1 scope.
- §9.5's Team-under-Open resolution needs explicit confirmation.

## Sources consulted

- [Open Registration Marathons 2026: Easy-Entry Races](https://www.marathonfocus.com/hubs/open-registration-marathons)
- [Enabling a Waiting List — RunSignup](https://help.runsignup.com/support/solutions/articles/17000062844-enabling-a-waiting-list)
- [FAQs About 2027 Registration — Big Sur International Marathon](https://www.bigsurmarathon.org/faqs-about-registration/)
- [Corporate Cricket Championship — SponsorMyEvent](https://www.sponsormyevent.com/corporate-cricket-championship-bengaluru-2)

---

# 10. Eligibility (new, 2026-09-10) — prominent third stage of Quota Setup

## 10.1 Why this exists, and why it isn't a Registration Setup field

Abhijeet proposed capturing eligibility (e.g. Age 14–25) as a validated
field on the Joining Form, via Form Builder. Market research (age-category
rules across FIBA, USA Water Polo, Athletics' Under-23, Little League,
USSSA) plus event-platform research surfaced two standard deviations:

1. **Age is derived from Date of Birth against a configurable cutoff/
   reference date, never entered or validated as a raw number.** The
   cutoff date itself varies by sport (Dec 31 of competition year for
   FIBA/Water Polo/Athletics; Aug 31 for Little League; May 1 for
   USSSA) — a fixed "Age" field can't reproduce any of these.
2. **Eligibility is a separate rules layer evaluated against captured
   data, not a constraint baked into the field itself** — the standard
   distinction found across event-platform sources ("form validation
   handles data format; eligibility rules handle qualification
   criteria"), concretely shaped in tournament software as a per-program
   calculation date + min/max, decoupled from the underlying field.

This matters architecturally because Registration Setup's Joining Form
lives at the User Type/Sub-Type leaf, which is **Discipline-agnostic**
(§33.5, explicitly no per-Discipline override). Age-category eligibility
is inherently **Discipline-specific** (a U-17 football tryout and a
Senior cricket squad need different windows on the same "Athlete"
Sub-Type) — baking it into the form field would silently break the
moment two Disciplines needed different windows. **Eligibility therefore
lives at Quota Setup, at Discipline × User Type granularity — the same
place Level 1/Open Quota already live** — not at Registration Setup.
Registration Setup's Joining Form stays pure data capture (DOB, Gender,
etc.); Quota Setup's Eligibility stage is where that captured data gets
evaluated against a per-Discipline rule.

## 10.2 Placement, prominence — Abhijeet's explicit ask

Abhijeet accepted Quota Setup as the home, but asked for it to be **"quite
prominent,"** not buried as one more inline field on the "+Add Quota"
flow. Resolution:

- **Eligibility is its own labeled stage**, same visual weight as Level 1
  and Level 2 — see the updated §1 diagram (Eligibility → Level 1 →
  Level 2, or Eligibility → Open Quota → Selection for Open entries).
- ~~Own guided creation flow, "+ Add Eligibility Rule," mirroring "+ Add
  Quota" (§2.4)~~ — **SUPERSEDED 2026-09-12, see §10.8.** Industry no
  longer authors the rule here; it's a read-only echo of what Master
  Admin set on the Discipline. No "+ Add Eligibility Rule" flow exists —
  there's nothing left for Industry to add.
- **Visible on the entry itself, not hidden behind a click.** Every
  Level 1 / Open Quota entry shows an Eligibility summary line alongside
  its Max/Min/Reserve numbers, e.g.:

```text
SWIMMING                                              [+ Add Quota]
User Type    Scope                Max  Min  Reserve   Eligibility
Athlete      100m Freestyle (M)   [40] [10]  [4]      Age 14–18 (as of Dec 31) · Male
Athlete      200m Freestyle (M)   [40] [10]  [4]      Age 14–18 (as of Dec 31) · Male
```

## 10.3 Fields

**Age**
- Never a raw field — always Date of Birth (already in Registration
  Setup's standard field palette) evaluated as of a **Cutoff/Reference
  Date** set per entry. **CONFIRMED (2026-09-11): no Project-level
  default** — Industry configures it explicitly on every entry, since
  the correct cutoff genuinely varies by sport (see §10.1) and a
  multi-sport Project would need different cutoffs per Discipline
  anyway, making a single default as often wrong as right.
- **Min / Max Age** as of that cutoff date, either bound optional (blank
  = no floor/ceiling) — matches the tournament-software pattern found in
  research.

**Gender**
- Discipline already carries a Master-Admin-owned Gender attribute
  (§32) — structurally enforced already for a single-gender Discipline.
  **CONFIRMED (2026-09-11): read-only echo, no override.** Eligibility
  just displays Discipline's existing Gender attribute (Men/Women/Mixed)
  for visibility. Reasoning: Gender is already fully resolved at
  Discipline granularity elsewhere in Flow Builder — Master Admin
  creates separate gender-variant Disciplines/Events when needed (e.g.
  "100m Men," "100m Women," "4x100m Relay Mixed" are three distinct
  enabled entries with their own toggles), so "Mixed" already means
  genuinely mixed-gender competition, not "either gender, pick per
  entry." An override would duplicate a distinction the Discipline
  catalog already expresses one level up.

**Custom criteria (extensible) — SUPERSEDED 2026-09-12, see §10.8.**
This subsection originally proposed Industry-defined label + type
criteria at Quota Setup (license/ID, qualifying time/rank, residency,
etc.), folding in the long-parked D24/Item-3 custom-quota-fields item.
**That's no longer the shape.** Custom criteria (e.g. Weight) are
**Master-Admin-authored at Sport/Discipline creation**, not
Industry-authored here — see §10.8. D24/Item-3 goes back to unresolved
as a *Quota-Setup-level* item (Industry still has no custom-field
capability of its own at Quota Setup), but the underlying need it was
trying to serve is now met one layer up, by Master Admin.

## 10.4 Enforcement scope — CONFIRMED (2026-09-11)

Eligibility gates **every** registration method (Manual, Invite,
Self-Register), not just Self-Register/Open entries — eligibility is a
property of the person-vs-category match, not of how they entered, so a
contingent Institution manually registering someone outside the age
window is blocked exactly like a self-registering Individual would be.
Abhijeet confirmed the agent's proposed default. No longer flagged.

## 10.5 Data flow

Registration Setup's Joining Form captures DOB/Gender/custom-field values
**once**, Discipline-agnostically. Quota Setup's Eligibility stage reads
those captured values and evaluates them **per Discipline × User Type
entry** — exactly the decoupling the research validated (Golf Genius:
DOB captured once, evaluated against a per-program calculation date and
range). Registration Setup stays pure data capture; Quota Setup owns the
per-Discipline business rule.

## 10.6 Open items — status as of 2026-09-14

- ~~Custom criteria enforcement: hard-block vs. informational-flag not
  decided.~~ **CONFIRMED 2026-09-14** — enforcement is fully automatic:
  the system checks eligibility against whatever Master Admin defined on
  the Discipline, no Industry-facing toggle, no manual review step.
  Abhijeet confirmed directly (no longer just inferred from framing).
  **Timing CONFIRMED 2026-09-14:** the check fires at whichever step
  first ties the person to a specific Discipline — reconciles cleanly
  with §10.4's "gates every registration method" rather than replacing
  it. **Team/Sport-granularity-only entries** (no Nomination step,
  Registration is already Discipline-scoped — §2.7): checked at
  Registration. **Event-split Individual entries** (Registration is a
  Sport-level pool, §2.8/registration-nomination.md, Discipline not
  picked until Nomination): checked at Nomination, when the person is
  assigned to a specific Discipline's Main/Reserve zone.
- ~~New gap surfaced 2026-09-11: how does a custom Eligibility criterion
  get a capture field on the Joining Form?~~ **RESOLVED 2026-09-12** —
  Registration Setup's Joining Form is now Sport-scoped and auto-locks
  every MA-set criterion for that Sport. See
  `atom-flow-builder-sports-registration-setup.md` §34.
- ~~Interaction with Team's role-slot model (§2.7/§9.5): does Eligibility
  apply once per Discipline×User Type entry or could it vary per
  role/slot?~~ **CONFIRMED 2026-09-14** — one rule per Discipline×User
  Type/Sub-Type entry, applies uniformly to every slot of that role
  (Playing and Reserve alike). No per-slot-number variation — e.g. all 5
  Batsman Playing slots + both Batsman Reserve slots share the same Age/
  Gender/custom-criteria rule. Matches real-world roster rules (a weight
  class or age band applies to the role, not a specific slot index).

**All three items in this section are now resolved. No open items
remain in §10 as of 2026-09-14.**

## 10.7 Worked example — how the pieces connect end-to-end (2026-09-11, explanatory, not a new decision)

Walked through for Abhijeet's benefit before deciding §10.6's
enforcement-mode item; captures no new decision, just makes the existing
design concrete.

**Registration Setup (a different node, happens once, Discipline-
agnostic):** an Athlete fills the Joining Form — DOB = 12 Mar 2011,
Gender = Male, plus custom fields the Industry added, e.g. "State
Swimming Association ID" and "Personal Best Time (100m Freestyle)".

**Quota Setup → Eligibility (Industry configures per entry, ahead of
time):** on the "100m Freestyle (M)" quota entry:
```
Age:     14–18, as of Dec 31 (competition year)
Gender:  Male                              (read-only echo)
Custom:  State Swimming Association ID     [text]   → Informational flag
Custom:  Personal Best Time ≤ 58.00s       [number] → Hard block
```

**Registration attempt (Manual/Invite/Self-Register — all gated
identically, §10.4):** the system reads the Athlete's already-captured
values and evaluates them **against this specific Discipline entry's
rule**: age as of Dec 31 = 15 → passes; Gender Male → matches; Personal
Best Time 61.2s → **fails** the hard-block rule → registration rejected
outright, regardless of method; Association ID → captured and shown,
**not** blocking, flagged for a human reviewer to verify before
Selection/Nomination.

**Why per-Discipline, not once per person:** the same Athlete attempting
a *different* Discipline (e.g. "200m Freestyle (M)") hits a **different**
Eligibility entry — possibly a different age window, qualifying time, or
custom-criteria set. Nothing is evaluated once globally for the person;
it's re-evaluated per Discipline×User Type entry they attempt to enter.

**Status: SUPERSEDED 2026-09-12 — see §10.8.** The worked example above
still illustrates the *mechanics* correctly (capture once, evaluate per
Discipline entry), but its ownership model is wrong in one respect: it
shows Industry configuring the Eligibility entry directly. Under §10.8,
Industry configures nothing — the entry is inherited read-only from
Master Admin's Discipline definition. Custom criteria in the example
("Personal Best Time," "Association ID") are now MA-set, not
Industry-set, and (per §10.6's updated line) inferred hard-block rather
than a per-criterion choice.

## 10.8 Ownership moved to Master Admin — all criteria, not just Age/Gender (2026-09-12)

**Trigger:** Abhijeet clarified that custom Eligibility criteria aren't
dropped for v1 (as first stated) — they're relocated. Master Admin, when
creating a Sport and its Disciplines in the Sports Library, can attach
**any** eligibility criterion to a Discipline: Age and Gender as already
designed (§10.3), plus extensible custom ones, concretely **Weight** for
a combat-sport Discipline. This generalizes the Age-ownership shift
already confirmed 2026-09-12 (Age moved from "Industry sets per Quota
Setup entry" to "MA sets at Discipline authoring, Quota Setup echoes
it") to every criterion, not just Age.

**Resulting model:**
- **Master Admin authors, at Sport/Discipline creation:** Age (Min/Max +
  cutoff date), Gender (already the case), and any custom criterion
  (label + type + threshold, e.g. Weight Min/Max), scoped **per
  Discipline** — this is why a Sport can have Disciplines with different
  thresholds (different weight-class Disciplines under the same combat
  Sport, or Junior vs Senior age bands under the same Sport).
- **Quota Setup's Eligibility stage (§10.1–§10.5) becomes a pure
  read-only echo** of whatever MA set on that Discipline — no Industry
  authoring, no "+ Add Eligibility Rule" flow (§10.2, superseded).
  Industry's only role is visibility (the summary badge, §10.2).
- **Registration Setup's Joining Form captures whatever MA has defined**,
  via the new Sport-scoped form design —
  `atom-flow-builder-sports-registration-setup.md` §34. This is the
  resolution to the capture-field-linkage gap flagged 2026-09-11 (§10.6).
- **Enforcement is inferred as hard-block/auto-verify** for every
  MA-set criterion — Abhijeet: "the system will automatically verify if
  the nominated user is eligible or not." No per-criterion Industry
  choice exists anymore (there's no Industry authoring left to attach a
  choice to). **Flagged for explicit sign-off**, not yet a line Abhijeet
  confirmed directly — see §10.6.
- **D24/Item-3 (custom-quota-fields, display-only, no enforcement
  semantics) reverts to unresolved as an Industry-facing Quota Setup
  item** — see §10.3's updated note. The need it described is now served
  one layer up, by Master Admin, not by Industry at Quota Setup.

**Explicitly NOT re-discussed in this pass, carried forward as still
open:** the Team role-slot interaction (§10.6's last bullet), and whether
MA's per-Discipline criteria editing after a Discipline is already live
in a Project needs any versioning/notice behavior (mirrors the deferred
§4f Sports Library RBAC/versioning question from 2026-09-08/09 — same
class of problem, not reopened here).

## Sources consulted (Eligibility)

- [Age Eligibility FAQs — USA Water Polo](https://usawaterpolo.org/sports/2018/11/28/membership-faq-age-eligibility-faqs-html.aspx)
- [Youth Sports Age Cutoff Dates by Sport (2026 Guide)](https://sportssteps.com/youth-sports-age-cutoff-dates-explained/)
- [Under-23 athletics — Wikipedia](https://en.wikipedia.org/wiki/Under-23_athletics)
- [Membership Program Player Eligibility Criteria — Golf Genius](https://docs.golfgenius.com/en/articles/11408947-membership-program-player-eligibility-criteria)
