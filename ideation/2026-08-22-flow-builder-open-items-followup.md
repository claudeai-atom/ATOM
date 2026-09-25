# Flow Builder — Post-Canvas Open Items Follow-up (parked queue)

## Purpose

After all four Registration & Quota Setup nodes reached canvas-level design
(`atom-flow-builder-registration-nomination.md`, 2026-08-21/22), a review
turned up items still open for v1. Abhijeet asked to work through them
**one at a time**, and to **park this thread with full context** rather
than resolve it in one pass. This doc is the running record — append to
it as each item resolves; don't lose the unresolved ones between sessions.

**Queue, in the order being worked:**
1. ~~Registration Status Dashboard layout~~ — **RESOLVED** (below).
2. Quota Setup Sub-Type nested-ceiling question — **IN PROGRESS**, options
   presented, awaiting Abhijeet's pick.
3. ~~Quota Setup custom-quota-fields affordance (D24 carry-forward)~~ —
   **RESOLVED/MOOT** (below).
4. Exact-match registration's brittleness against withdrawals/replacements
   — **NOT STARTED, explicitly deferred by Abhijeet 2026-09-14** ("let's
   take this later") — the only item still open.

Not in this queue but still pending regardless: the §O "downstream doc
impact" commit into `context/atom-context.md` / `context/backlog.md` —
explicitly gated on a separate go-ahead from Abhijeet, not part of this
item-by-item pass.

---

## Item 1 — Registration Status Dashboard layout — RESOLVED 2026-08-22

**Update (2026-09-10):** terminology re-pointed from Event to Discipline
throughout, matching the Sports Setup/Quota Setup/Registration & Nomination
rename (Sport → Discipline, no Category/Event level). Shape unchanged —
this is a rename, not a redesign.

**The gap it closes:** the original D8/§J dashboard concept (v2 doc) was a
*count/progress* view — filterable Quota/Reserve/Entries by
State×Sport×Discipline×Gender, reading OverallQuota/QuotaGrant/Entry data. Once
Registration & Nomination started producing real named individuals (Sport-
level pools, then Main/Reserve zones), that count-only shape stopped being
enough — Abhijeet's ask was a **roster of actual people**, not just fill
numbers, and needed to work for Admins, Industry, *and* contingent
Institutions (coaches included, any User Type).

**Decision:** a single **flat, filterable table** — not a Sport-first left
rail (the convention used by Sports Setup / Registration Setup / Quota
Setup), and not a Kanban/status board.

**Why not the Sport-rail convention:** the ask is explicitly cross-sport
("individuals across multiple sports and Disciplines") — a rail-scoped
view would fragment a multi-sport individual's record across separate
per-sport panels instead of showing them in one place.

**Shape:**
- One row = one individual × one Discipline they're filled against (a
  multi-Discipline individual, e.g. a swimmer in 100m + 200m, appears as
  two rows — Main/Reserve status is tracked per Discipline, not per
  person; a Team registrant's row reflects their role-slot §2.7 status
  the same way).
- Columns: Name, User Type/Sub-Type, Sport, Discipline, Institution,
  Status (Main / Reserve).
- Filter bar: Sport, Discipline, User Type, Status, plus name/ID search.
- **Role scoping is row-level, not menu-level** (a deliberate contrast with
  Quota Setup Level 2, where a federation-delegate sees *all* institutions
  in its one assigned sport because it's allocating between them). Here:
  - **Master Admin / Industry:** Institution column visible, unrestricted
    rows across every sport and every institution.
  - **Contingent (Institution):** same table, Institution column dropped
    (implicitly always "them"), rows pre-filtered to only their own
    registered/nominated individuals — but still spanning *all* sports
    they've registered in, not scoped to one sport at a time.
- Read-only, downstream of Registration + Nomination data — no editing
  here, no node depends on it Publishing (same non-gating principle as the
  original D8).

**Still to decide when this gets built out further (not blocking, just
noted):** export/print affordance, and whether Reserve rows for
not-yet-triggered reserves need a visual distinction from "active" Main
rows — neither was raised in this pass.

---

## Item 2 — Quota Setup Sub-Type nested-ceiling question — IN PROGRESS

**Background (D22 carry-forward):** Registration Setup allows a User Type
to optionally have one level of Sub-Type (e.g. `Athlete → Senior /
Junior`), and forms live at the leaf only (D18 — a User Type with
Sub-Types has no form of its own). Every worked example in the finalized
Quota Setup doc used a flat User Type with no Sub-Type breakdown, so it
was never decided what Level 1 quota looks like when a User Type *does*
have Sub-Types.

**Three options put to Abhijeet (none chosen yet):**

- **(A) Sub-Type-only entries.** Separate Max/Min/Reserve per Sub-Type
  (`Athlete-Senior`, `Athlete-Junior`) on the same event, no parent-level
  number. Unambiguous consumption (always at the leaf), but no single
  "total Athletes" ceiling without summing children yourself.
- **(B) Roll-up parent + Sub-Type children.** A parent `Athlete` entry
  that's a pure roll-up (Σ Sub-Type Max ≤ parent Max), mirroring the
  Budget Tracking Total/Allocated/Unallocated convention already used
  elsewhere in Quota Setup. Gives both a total and a breakdown, but adds a
  second enforcement layer (parent vs. child ceilings) untested by any
  worked example so far.
- **(C) Industry's choice per User Type.** Same "not forced, Industry
  decides" pattern already used for whether quota attaches at
  parent-or-Sub-Type level (the double-counting-risk call from
  Registration Setup, D20). One User Type could stay flat, another could
  be Sub-Type-split, decided case by case.

**Status:** parked — awaiting Abhijeet's pick (A / B / C / other) next
time this is picked back up.

---

## Item 3 — Quota Setup custom-quota-fields affordance (D24 carry-forward) — RESOLVED/MOOT 2026-09-14

**Background:** D24 (2026-07-31 pass) added custom quota fields as
in-scope — Max/Min/Max Team/Reserve are "standard" fields, and Industry
can add custom quota fields per track, same label+type pattern as
Registration Setup's Form Builder, **display-only in v1** (no enforcement
semantics were ever defined).

**Resolution:** superseded by the 2026-09-12 Eligibility-ownership move
(`atom-flow-builder-quota-setup.md` §10.8). Master Admin now authors all
per-Discipline criteria (Age, Gender, custom ones like Weight) at Sport/
Discipline creation; Quota Setup's Eligibility stage is a pure read-only
echo. The real-world need D24/Item-3 was chasing is served one layer up,
by Master Admin — Industry has no custom-field authoring of its own at
Quota Setup, and per Abhijeet's 2026-09-14 confirmation, what Industry
does at the Quota Setup node is already fully defined post-MA-change, no
further affordance needed. Closed, not reopened unless a genuinely new
Industry-facing (not MA-facing) need surfaces.

---

## Item 4 — Exact-match registration's brittleness against withdrawals/replacements — NOT STARTED

**Background:** Registration & Nomination's design accepted, as a known
v1 risk, that registration is an exact-match process (a contingent
registers exactly up to `Σ (Q + R)`) with no defined behavior yet for
withdrawals or replacements after the fact — e.g. a registered/nominated
athlete drops out and needs to be swapped. This was flagged but explicitly
left unsolved when the node was finalized. **Update (2026-09-10):** Team's
new role-slot registration model (§2.7 of
`atom-flow-builder-registration-nomination.md`) inherits the identical
risk per slot — still unsolved, now explicitly covering Team too, not just
Individual.

**Status:** not yet discussed in this pass — worth a deliberate design
conversation (what happens on withdrawal: does the slot reopen for
re-registration? re-nomination? is there a deadline cutoff after which
swaps are blocked?) rather than leaving it as silent accepted risk
indefinitely.

---

## Resume instructions

Work through items 2, 3, 4 **one at a time**, same pattern as Item 1: lay
out the real options/trade-offs, let Abhijeet decide, record the decision
here (or in the relevant canvas doc) before moving to the next. Do not
batch-resolve or assume an answer on Abhijeet's behalf. Once all four are
resolved, fold the outcomes into `atom-flow-builder-quota-setup.md` /
`atom-flow-builder-registration-nomination.md` as appropriate, and note it
in `ideation/README.md`.
