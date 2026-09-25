# ATOM Flow Builder — Registration & Nomination
## Finalized Ideation (Registration + Nomination)

## 1. Purpose

Registration & Nomination is the fourth and last of the four fixed nodes:

```text
Sports Setup
      ↓
Registration Setup
      ↓
Quota Setup
      ↓
Registration & Nomination
```

It unlocks once Quota Setup's Level 2 fully rolls up to Published (every
sport published — see `atom-flow-builder-quota-setup.md` §3.5). Inside this
node, a contingent Institution (one holding a Level 2 QuotaGrant) does two
things in sequence: **Registration** (onboard users against its allocation)
and **Nomination** (assign registered users into specific Discipline slots).
Only Registration is locked in this pass.

---

## 2. Registration — contingent onboards users against its Level 2 allocation

### 2.1 Who does this

The contingent Institution opens Registration & Nomination, sees which
sport(s)/allocations it holds from Quota Setup Level 2, and registers users
against them.

### 2.2 Granularity depends on whether the underlying quota was Discipline-split

- **Individual-type quota allocated at Discipline level** (e.g. Athlete on
  100m, Athlete on 200m — separate Level 1 entries) → Registration pools
  at **Sport** granularity: the contingent registers the *combined* count
  under the sport, not per Discipline. The Discipline-level split happens
  later, in Nomination.
- **Quota allocated directly at Sport granularity, with no Discipline
  split** (e.g. Coach entries for Football — a Sport-level Level 1 entry,
  no per-Discipline breakdown) → Registration **is** the final step. No
  Nomination follows — there is nothing left to split.
- **Team-type quota** → Registration happens directly at **Discipline**
  granularity, **per team** (up to the Institution's allotted `Q` team
  count from Quota Setup Level 2) — see §2.7 for the role-slot model
  (updated 2026-09-10). No Nomination follows, for the same reason —
  Registration already landed at final granularity.

**General rule:** Nomination exists only to resolve a Sport-level pool into
its Discipline-level allocations. Wherever Registration already lands at
final (Discipline) granularity — Team, and Sport-granularity-only entries
— Nomination is skipped entirely.

### 2.3 Worked example (India, Athletics contingent)

Level 2 allocation to India (from Quota Setup):
- 100m: Q 20, R 5
- 200m: Q 10
- Cricket (Team): Q 3 teams. Level 1 template per team — Playing: 5
  Batsman / 4 Bowler / 2 All-Rounder; Reserve: 1 Batsman / 1 Bowler / 0
  All-Rounder.
- Football (Coach, Sport-level entry): Q 4

**Registration:**
- 30 athletes registered under "Athletics" (Sport pool = 20 + 10)
- 3 team instances created onto Cricket (Team 1/2/3, up to India's
  allotted `Q` of 3), each filled slot-by-slot to its fixed template — 5
  Batsman/4 Bowler/2 All-Rounder starters + 1 Batsman/1 Bowler reserve,
  ×3 (39 cricketers total) — see §2.7
- 4 coaches registered directly onto Football (Sport-level — final, no
  Nomination follows)

**Nomination** (separate, later step — not designed yet in this pass): of
the 30 registered athletes, 20 get nominated to 100m and 10 to 200m.

### 2.4 Registration cap (v1) — REVISED, see §3.4

For a Sport-level individual pool, the registered count must equal
**exactly** `Σ (Q + R)` across that contingent's Discipline-level entries in
that sport — not `Σ Q` alone. (India/Athletics: `(20+5) + (10+R₂₀₀) `, not
just 30.) **This supersedes the first pass at this section**, which had
capped Registration at exactly `Σ Q` — that was wrong once Nomination's
Reserve mechanic (§3.3) was defined: Reserve slots get filled with real,
named individuals during Nomination, not just tracked as a number, so the
registered pool must be large enough to cover both Main and Reserve
placements.

**Still an explicit v1 flag, not solved:** an exact match (now against a
*larger* number) is still brittle against withdrawals/replacements before
Nomination locks in — arguably more so, since there's no headroom left
above the new target either. No mechanism defined yet for adjusting an
already-registered pool. Accepted as a known v1 limitation, to be
revisited.

### 2.6 Multi-sport participation reuses the same registration, doesn't repeat it

An Individual keeps **exactly one User Type per Project** — this is an
existing ATOM architectural rule, not something Flow Builder can relax.
Since User Types are Project-scoped, not Sport-scoped (D17 — Industry
defines User Types per Project), the same User Type (e.g. "Athlete") can
span multiple sports for the same person without breaking that rule: the
Individual fills the Joining Form **once**, at their first registration.
Adding them to a **second sport's** pool under the **same** User Type is
not a fresh registration — it's an **add-to-pool action** via "Search
Existing User" (the dedup-search concept D16 already had): find the
already-registered person, add them to the second sport's pool too. No
second form-fill, no second account.

**Explicit v1 risk, accepted, not solved:** this only works cleanly for
User Types that are genuinely sport-agnostic (Athlete, Coach, Official).
For a User Type whose real-world meaning is tied to one specific sport
(e.g. "Batsman" — Cricket-only), the system has **no concept of
sport-affinity on a User Type** — nothing stops an Industry from creating
a "Batsman" quota entry under Football, or from a Batsman-registered
individual being added to a football pool while still carrying the
"Batsman" label, which is semantically wrong. This is the same underlying
gap as the mixed-User-Type problem below (no per-sport scoping on User
Types), just a narrower instance of it. **Accepted v1 limitation** — not
designed around, flagged for a future pass alongside the mixed-User-Type
deferral.

**Explicitly out of scope for v1:** the same Individual holding **two
different User Types** within one Project (e.g. Batsman for Cricket *and*
Player for Football) — this would require relaxing ATOM's one-User-Type-
per-Individual-per-Project rule, which is out of bounds for this node to
decide unilaterally. Deferred to v2 as a known, explicit limitation, not
an oversight.

**Team roster size at registration — superseded 2026-09-10, see §2.7.**
There is no more Min/Max Team Members range; each team fills to an exact
role-slot template inherited from Quota Setup Level 1.

### 2.5 Registration methods

Two methods, both operating on the User Type/Sub-Type leaf's Joining Form
(from Registration Setup):

**Manual** — the contingent fills out the leaf's form on the user's behalf.
On submit, the registered user receives an email at their given address
with a password-reset link; the contingent never sets a password for them.

**Invite** — the contingent invites the user; the user fills out the form
themselves and accepts the invite; the contingent then approves the
invitation. This reuses ATOM's **existing** invite/approve mechanism as-is
— confirmed by Abhijeet as "basically what we have in ATOM right now," not
a new build.

**Reconciles an earlier apparent conflict:** the v2 ideation doc (§L, §P.3)
repeatedly deferred "self-service contingent nomination" to the downstream
§7.7 Registration/Join path, out of Flow Builder v1 scope. That deferral
was about a self-service **nomination** flow. This Invite method is
**registration**, surfaced as a UI entry point inside Flow Builder onto the
same existing §7.7 invite/approve machinery — not a new self-service
engine, so it does not reopen that deferral.

### 2.7 Team registration — role-slot model (new, 2026-09-10)

Quota Setup Level 1's new Team fields (playing breakdown + role-tagged
Reserve per team, §8 of `atom-flow-builder-quota-setup.md`) replace the
old free Min–Max roster range. Registration for Team now works like this:

- **Team instances, up to `Q`.** The contingent Institution creates
  individual team rosters via **"+ Add Team"**, up to its Level 2
  allotment (`Q`, e.g. India's 3 Cricket teams). Each instance inherits
  the **same fixed role-slot template** from the Level 1 entry — it is
  not re-authored per team.
- **Two zones per team, same hard-block spirit as Nomination's Main/
  Reserve (§3.3), but per role, not just per team.** Each team shows its
  Playing slots and Reserve slots broken out by User Type/Sub-Type (e.g.
  Batsman Playing ×5, Batsman Reserve ×1, Bowler Playing ×4, Bowler
  Reserve ×1, All-Rounder Playing ×2). The contingent registers (Manual
  or Invite, §2.5) a named individual of the matching Sub-Type into each
  slot. Every slot hard-blocks further registration once filled — a
  Batsman can't be placed into a Bowler slot, and a 6th Batsman can't be
  registered once the 5 Playing + 1 Reserve Batsman slots are full.
- **No separate Nomination step.** Because every slot is role-specific
  and filled directly, Team registration already lands at final
  granularity in one pass — this is why Team skips Nomination entirely
  (§3.1), same as before, just with a more constrained fill mechanic than
  the old free-range model.
- **Exact-fill expectation, same accepted v1 risk as §2.4's Individual
  cap.** A team's slots are expected to fill exactly to the Level 1
  template (no partial teams implied by canon yet) — this inherits the
  same unresolved brittleness-against-withdrawal flag as Individual
  registration (§2.4, Item 4 in the 2026-08-22 open-items doc): if a
  registered Batsman withdraws after their slot is filled, there's no
  defined swap/reopen mechanism. Not solved here, carried forward as the
  same known gap, now explicitly also covering Team.

### 2.8 Self-Register — third registration method, gated by Mode of Registration (new, 2026-09-10)

Registration Setup §33 (`atom-flow-builder-sports-registration-setup.md`)
adds a per-leaf **Mode of Registration**: Open or Closed. Closed is
exactly the model above (§2.5) — Manual and Invite only, nothing new.
**Open adds a third method:**

**Self-Register** — the Individual finds the Open leaf's Joining Form
themselves and submits it directly, with **no Institution action** on
either side (no invite to accept, no contingent filling it on their
behalf). This is a genuinely new entry point: everywhere else in this
node, a contingent Institution is the actor initiating registration;
here, the Individual is.

**Quota interaction — RESOLVED 2026-09-10, see
`atom-flow-builder-quota-setup.md` §9.** Self-Register does **not**
require picking an Institution. It hard-blocks against a new **Max
Entries** cap (Open Quota §9.3) instead — a deliberately oversubscribed
number, above the actual Selection target. After Self-Register fills the
pool, **Selection** (Industry or a permitted Institution, no per-
Institution split) cuts it down using the **same Main/Reserve drag-and-
drop mechanic as Nomination (§3)** — Open events effectively get their
own single-pool Nomination pass instead of Closed's per-contingent one.
Team-shaped Open entries (§9.5 of the Quota Setup doc) reuse §2.7's
role-slot template as the Selection target — **proposed, not yet
explicitly confirmed by Abhijeet.**

**Eligibility (new, 2026-09-10) — see `atom-flow-builder-quota-setup.md`
§10.** **Confirmed 2026-09-11:** every registration method here,
including Manual and Invite, is gated by the Discipline×User Type
entry's Eligibility rule (age window via DOB, gender, custom criteria) —
not just Self-Register — since eligibility is a property of the
person-vs-category match, not of entry method (§10.4).

**Still not yet designed, carried forward:**
- **Discovery surface.** How an Individual finds an Open leaf to begin
  with — public browse page, search, direct link — not designed.
- **Account creation.** Whether Self-Register reuses ATOM's existing
  sign-up flow or needs something new.
- **Early-close.** Whether registration can be manually closed before
  Max Entries is hit (deadline vs. pure capacity cutoff) — not discussed.
- **Selection method.** Assumed manual (drag/select), per Abhijeet's "the
  Industry or Institution who has permission" framing — lottery-based
  selection is a documented market alternative for oversubscribed events,
  not adopted, flagged as a possible future option only.

---

## 3. Nomination — drag-and-drop, contingent self-service, Individual only

**D16/§P.3 in `2026-07-20-flow-builder-ideation-v2.md` is superseded** by
this design for the contingent self-service case. D16 modeled *Industry*-
manual nomination (drill-down, Search Existing User / Manual Registration
at the leaf, lands as `Confirmed` immediately). This is a different actor
and a different mechanic — see §3.5 for exactly what carries over vs. what
changes.

### 3.1 The mechanic

Two panels. **Left panel** lists the contingent's **registered, not-yet-
fully-nominated individuals** for the selected sport. **Right panel** lists
**Discipline boxes** — the Disciplines that sport's Level 2 allocation
covers for this contingent. The contingent **drags an individual from the
left panel and drops them onto a Discipline box** to nominate them into
that Discipline.

Only User Types whose Level 1 quota was **Discipline-split** appear in the
left panel at all (Athlete in the running example). Sport-granularity-only
User Types (Coach, etc.) and Team entries never reach Nomination — they
were already finalized at Registration (§2.2/§2.7) — so they never appear
here.

### 3.2 Multi-Discipline nomination is allowed, uncapped in v1

The same individual **can** be dragged onto more than one Discipline box
(e.g. the same athlete nominated to both 100m and 200m) — real rosters
double up sprinters this way. **No per-individual cap on how many
Disciplines one person can be nominated to exists in v1.** This is
intentionally deferred: a configurable Industry-set cap (e.g. "max 3
Disciplines per person") is planned for **Flow Builder Phase 2**, not v1.

### 3.3 Each Discipline box has two zones: Main (Q) and Reserve (R)

Reserve is **not** just a numeric ceiling at this stage (unlike Level 1/
Level 2, where R is a cap) — during Nomination, the contingent drags
specific named individuals into a **Reserve zone** for the Discipline,
distinct from the **Main zone** (which fills toward `Q`). Both zones
hard-block further drops once they hit their respective ceiling (`Q` for
Main, `R` for Reserve) for that contingent on that Discipline — same
hard-block spirit as Level 2's `Σ Q ≤ Max` / `Σ R ≤ Reserve`, just
enforced with real people now instead of just numbers.

### 3.4 Registration must supply exactly enough people: `Σ (Q + R)`

Because both zones need real individuals, the contingent's registered pool
for that sport must total **exactly `Σ (Q + R)`** across its
Discipline-level entries — this is the number Registration (§2.4) now
targets exactly, revised up from the original `Σ Q`-only cap.

### 3.5 Status model: draft until the contingent Publishes (supersedes D16)

A drop is **provisional**, not immediately final — the contingent can drag
an individual back out of a Discipline box, or move them to a different
Discipline box, at any point **before Publishing**. Publish is a
deliberate, contingent-side action, and it is the point of no return:
**once published, nomination is final.**

**Publish gate — exact fill, no partial publish:** the contingent can only
Publish once **every** Discipline box for that sport has **both** zones
fully filled — Main at exactly `Q`, Reserve at exactly `R`, for every
Discipline. There is no Min-based partial-publish allowance here (unlike
Level 2's `Σ Q ≥ Min` gate) — Nomination requires the complete allocation
to be placed before it locks in.

**No deadline enforcement currently exists in the system** — Publish is a
manual action with no time-based auto-lock. (D27/D28's deadline/ratchet
mechanics from the pre-Level-1/2-split model are not wired to this action;
revisit if/when a deadline system is built.)

### 3.6 What D16 still contributes vs. what's superseded

- **Superseded:** "lands as `Confirmed` immediately, no pending step" —
  that was true for Industry-manual entry; contingent self-service
  nomination via drag-and-drop has a real draft/Publish lifecycle instead.
- **Superseded:** "Manual Registration" as a nomination-time option to
  create a brand-new user on the spot — nomination now **only** draws from
  the already-registered pool (confirmed explicitly this session). Any
  account creation happens at Registration (§2.5), not here.
- **Carries over, unchanged:** hard-capped, no-auto-waitlist principle
  (still true, just enforced per-zone now instead of one number); the
  general "thin, not the full self-service flow" framing no longer applies
  as-is, since this *is* now the full self-service contingent flow — D16's
  scope note describing it as deferred to §7.7 was about this exact
  mechanic, which has now been designed here instead.

---

## 4. Relationship to Registration Status Dashboard (D8/§J) — unconfirmed

Earlier in this session, before Registration's actual scope was explained,
the agent proposed folding the old Registration Status dashboard (§J/D8 —
cross-cutting, filterable Quota/Reserve/Entries by State×Sport×Discipline×
Gender) into this node as a second tab alongside Nomination, based on a
mistaken assumption about what "Registration & Nomination" meant. That
proposal was tentatively accepted via a quick-pick answer before the real
Registration flow (§2 above) was explained, so it should **not** be treated
as confirmed. The Dashboard's place in this node — third tab, separate
node, dropped, or something else — is genuinely open and should be revisited
explicitly, not assumed from that earlier exchange.

---

## 5. Final Locked Scope

### Registration
- **Granularity:** Sport-level pool for Discipline-split individual quota;
  direct Discipline-level registration for Team and for Sport-granularity-
  only entries (Coach-style).
- **Team roster — updated 2026-09-10 (§2.7):** no more Min–Max range. Up
  to `Q` team instances, each filled to an exact role-slot template
  (Playing + Reserve, per User Type/Sub-Type) inherited from Quota Setup
  Level 1 — hard-blocked per slot, not freely fillable.
- **Individual sport-pool registration count:** exactly `Σ (Q + R)` per
  contingent per sport (revised from an earlier `Σ Q`-only pass once
  Nomination's Reserve mechanic was defined — see §3.4). Flagged v1 risk:
  exact-match is brittle against withdrawals/replacements; no adjustment
  mechanism defined. **Team inherits the same exact-fill risk** per slot
  (§2.7).
- **Two-or-three registration methods, gated by Mode of Registration
  (new, 2026-09-10, §2.8):** Manual (form-fill by contingent + emailed
  password-reset link to the user) and Invite (existing ATOM invite/
  approve flow, reused not rebuilt) always available. **Self-Register**
  (Individual submits the Joining Form directly, no Institution action)
  additionally available only on leaves Registration Setup has marked
  **Open** — default **Closed**, i.e. today's two-method model, unless
  Industry opts in. Self-Register hard-blocks against a new **Max
  Entries** cap and feeds a **Selection** pass (Industry/permitted
  Institution, reuses Nomination's Main/Reserve mechanic on the flat
  pool) — see §2.8 and Quota Setup §9.
- **Multi-sport reuse:** same User Type, same person, added to a second
  sport's pool via "Search Existing User" — no second form-fill. Mixed
  User Types on one Individual within one Project (including the narrower
  sport-affinity case, e.g. "Batsman" showing up under Football) is
  explicitly out of scope for v1, deferred to v2.

### Nomination — Individual only (Team never reaches it, §2.7)
- **Mechanic:** drag-and-drop — registered individuals (left panel) onto
  Discipline boxes (right panel), scoped to Discipline-split User Types
  only.
- **Multi-Discipline nomination:** allowed, uncapped in v1; a
  per-individual cap is planned for Flow Builder Phase 2, not now.
- **Two zones per Discipline:** Main (fills to `Q`) and Reserve (fills to
  `R`), both populated with real individuals, both hard-blocked at their
  ceiling.
- **Status model:** draft until the contingent explicitly Publishes;
  drag-back-out allowed pre-Publish; Publish requires every Discipline's
  Main **and** Reserve zone to be exactly full (no partial/Min-based
  publish); no deadline enforcement exists in the system currently.
- **Supersedes D16's** "instant `Confirmed`, no pending step" and "Manual
  Registration at the nomination leaf" — both were specific to the old
  Industry-manual model; this is the actual self-service contingent flow
  D16 had deferred to §7.7, now designed here instead.

**Not yet done:** the Registration Status Dashboard's fate (§4) — still
unconfirmed — and, per the usual gating on this whole ideation thread,
nothing here is committed to `context/atom-context.md` /
`context/backlog.md` yet; that remains a separate, explicit go-ahead.
