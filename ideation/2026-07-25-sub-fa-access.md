# [2026-07-25] Sub-Functional-Area (Sub-FA) Access Control

> Standalone ideation doc. Index entry lives in `ideation/README.md`.
> Exploratory — nothing here is committed to `context/atom-context.md`
> yet. Section refs (§) point at `context/atom-context.md`.

**Status:** Model finalized across the 2026-07-25 conversation. D1–D6
represent the settled design (not a menu of options); two small open
questions remain (O1–O2). Nothing committed to `atom-context.md` yet.

> **Supersession note:** this replaces the original 2026-07-25 draft of
> this doc ("pure inheritance," decisions D1–D4 with D4 = "defer Sub-FA-
> specific Institution assignment to Phase 2"). D1 survives; the D4
> framing was **wrong**, not merely early — Sub-FA-level access is not an
> ATOM-core RBAC feature at any phase. It lives one layer up, inside the
> Modules. See D4 below.

---

## A. The question, restated after the full walk-through

Original question: *now that the Industry can create Sub-FAs inside an
FA, who gets access to a Sub-FA?* The full model that resolves it has two
distinct halves that the first draft collapsed into one:

1. **ATOM core** answers *who is granted the FA at all* — and its answer
   is unchanged from today: assignment happens **only at the FA level**,
   and an FA grant **auto-cascades** to every Sub-FA under it. ATOM core
   has **no** per-Sub-FA grant step.
2. **Each Module** answers *what "access and work on a Sub-FA" means for
   its own surface, and which of the FA's already-granted users it puts
   on which Sub-FA.* This is deliberately **not** ATOM's job — it is the
   Module's, per the §8.4 pattern of independently-built systems
   integrated into ATOM.

The first draft's mistake was trying to answer half 2 inside ATOM core
(as a deferred "finer-grained Institution scoping" RBAC feature). That
was the wrong home for it. Sub-FA-level work belongs to the Modules.

---

## B. The finalized model

### B.1 ATOM core: FA-level grant, auto-cascade, nothing new (survives from draft)

- Institution/Individual assignment happens **only at the FA level**,
  exactly as §7.7.3 describes today (full-FA-access, all-or-nothing —
  "an Institution gets full access to its assigned FA; there is no finer-
  grained role within the FA yet").
- An FA grant **automatically cascades to every Sub-FA under it.** A
  Sub-FA nests *inside* its FA (§3.1: `… → FA → Sub FA → Task Board →
  Task`; §7.4), so full FA access structurally covers every Sub-FA —
  including Sub-FAs created *after* the Institution/Individual joined.
  **Creating a Sub-FA grants access to nothing new and needs no
  re-approval.**
- There is **no separate Sub-FA grant step in ATOM core**, and **no
  FA+Sub-FA multi-select at invite time.** (An FA+Sub-FA multi-select
  was floated earlier in this thread and explicitly rejected by the user
  in favor of the Module-owned model below.)
- Existing isolation is untouched: §10 names three isolation levels
  (Workspace / Project / Functional Area). Sub-FA sits *inside* FA, so
  **Functional Area isolation already contains it** — no fourth level.

### B.2 The purpose of Sub-FA: it is where the Modules do fine-grained work

This is the answer to the natural objection — *if Institution access is
just inherited wholesale, what is a Sub-FA even for?* A Sub-FA is a
**structural entity ATOM creates and exposes**, but the meaning of
"working on" one is **each Project Module's own responsibility** (§8.2:
Task Management, Grievance, AMS, etc.). ATOM hands each Module the FA,
its Sub-FAs, and the FA's granted population; the Module decides how to
scope its surface to the Sub-FA level and which of those users it places
where.

**Concrete resolution — Task Board scoping.** The open "Task Board per
Sub-FA?" question is a **Task Management Module scoping choice, not an
ATOM hierarchy change.** When an FA has Sub-FAs, the Task Management
Module scopes its Task Boards to the Sub-FA level; when an FA has none,
the Task Board sits directly on the FA. **Sub-FA is optional per FA.**

- **Consistency check (worth stating explicitly in any commit):** §7.5
  Progress Tracking already computes flat across "every FA / Sub FA /
  Task Board." It already anticipated Sub-FA-level Task Boards, so
  Module-scoped-per-Sub-FA Task Boards **do not break Progress
  Tracking** — the two are already aligned.

### B.3 The isolation guardrail (non-negotiable)

A Module may **only** assign, to a Sub-FA, users who are **already part
of the parent FA's granted population** — i.e. the Institution holding
the FA grant, plus Individuals already invited under it (§7.7.2/§7.7.3).
A Module can **never** pull in a user with no relationship to that FA.

Frame it precisely: **ATOM's FA grant is the outer boundary; the
Module's Sub-FA assignment is strictly a narrowing operation inside that
boundary.** This is what keeps §10 Functional-Area isolation intact even
though the fine-grained assignment logic itself lives outside ATOM core,
in the Module. Isolation is now enforced at **two layers instead of
one**: ATOM enforces the FA boundary; the Module enforces the Sub-FA
narrowing within it, and can never widen past what ATOM granted.

### B.4 Who performs the Sub-FA assignment inside a Module — the Module Admin flow

A **new RBAC axis**, orthogonal to both the per-Project Role (§7.7.2)
and the FA grant (§7.7.3). It is a **Module-scoped admin capability**,
granted by the Industry, layered **on top of** a user's existing Project
membership.

1. **Grant.** For each Module assigned to a Project, the **Industry**
   goes into that Module (inside the Project) and creates/grants a
   **Module Admin** role, assigning it to a user **already part of that
   Project.** Either workspace type is eligible — **Institution or
   Individual**, no restriction.
2. **Scope: per-Project-per-Module, explicitly NOT per-FA.** There is
   **one Module Admin per Module**, covering that Module across **every
   FA** in the Project — not one Module Admin per FA. (Asked directly,
   the user ruled out an FA-specific Module Admin: *"There will be no FA
   specific module admin. For each module, there will be one module
   admin."*)
3. **What the Module Admin does.** The Module Admin then adds/assigns
   further users as the Module requires — e.g. placing specific
   Individuals/Institutions onto specific Sub-FAs for that Module's work
   — **subject to the B.3 guardrail** (only from the parent FA's granted
   population). Being made Module Admin is itself just a special case of
   this same assignment mechanism, not a separate onboarding path.
4. **It does not grant Project membership.** The Module Admin candidate
   must **already** be part of the Project some other way first (via
   §7.7 project-joining); Module Admin is a capability layered on top,
   not an entry path into the Project.

---

## C. Decision ledger (finalized)

**D1 — FA-level-only grant, auto-cascade to Sub-FA. CONFIRMED.**
Institution/Individual assignment happens only at the FA level (§7.7.3,
unchanged). An FA grant auto-cascades to every current and future Sub-FA
under it. No separate Sub-FA grant step, no FA+Sub-FA multi-select at
invite time. *(Survives from the original draft.)*

**D2 — No new Individual Sub-FA permission in ATOM core. CONFIRMED.**
ATOM core adds no dedicated Sub-FA visibility/permission for Individuals.
An Individual's ATOM-core capability still comes from their Project Role
(§7.7.2); *where inside a Module* they work is set by the Module (D4/D6),
within the guardrail (D5). Follows the §7.5/§10 "don't add a permission
axis when an existing one already covers it" precedent.

**D3 — No new "Sub-FA isolation" level. CONFIRMED.** Sub-FA is covered
by existing Functional Area isolation (§10). No fourth isolation level.

**D4 — Sub-FA-level access is delegated to the Modules, not deferred to
a later ATOM-core phase. CONFIRMED (supersedes original D4).** "Access
and work on a Sub-FA" is each Module's own responsibility (§8.2/§8.4),
not an ATOM-core RBAC feature at any phase. The original D4 ("defer
Sub-FA-specific Institution assignment to Phase 2, bundled with §7.7.3
FA-scoped Institution Roles") is **withdrawn** — its premise (that this
is an ATOM-core scoping feature waiting for a phase) was wrong. It should
**not** be described as "deferred to Phase 2" anymore.

**D5 — Parent-FA-population guardrail. CONFIRMED (new).** A Module may
only assign, to a Sub-FA, users already in the parent FA's granted
population (the FA-granted Institution + Individuals invited under it). A
Module can never introduce a user unrelated to that FA. ATOM's FA grant
is the outer boundary; Module Sub-FA assignment is a narrowing operation
inside it — §10 FA isolation enforced at two layers.

**D6 — Module Admin role. CONFIRMED (new).** A new RBAC axis, orthogonal
to Project Role (§7.7.2) and FA grant (§7.7.3). **Scope:** per-Project-
per-Module — one Module Admin per Module, spanning all FAs in the
Project, explicitly not per-FA. **Granted by:** the Industry, from inside
that Module in that Project. **Eligible holder:** any Institution or
Individual already a member of the Project. **Powers:** assigns further
users to the Module's Sub-FA-level work, within the D5 guardrail. **Does
not** confer Project membership (candidate must already be in the
Project).

---

## D. Open questions (genuinely still open)

**O1 — Is "one Module Admin" a hard cap of exactly one holder, or one
role type?** The user said "for each module, there will be one module
admin." This is unambiguous that the Module Admin role is **not**
FA-scoped (D6). What it does *not* pin down is cardinality of *holders*:
must there be **exactly one** person/entity holding Module Admin per
Module (single named admin, a bus-factor risk), or is it **one role
type** that could be granted to more than one user (and can it be zero —
i.e. a Module with no admin assigned yet)? Recommend confirming: (a)
minimum — can a Module sit with **zero** Module Admins, or must the
Industry assign one when the Module is added to the Project? (b) maximum
— **exactly one** holder, or multiple allowed? A hard cap of one is
operationally fragile (single point of failure, no cover during
absence); "one role, one-or-more holders" is the safer default unless
there's a governance reason to force singularity.

**O2 — Does the D5 guardrail need a re-check when the FA population
shrinks?** If an Individual is removed from the parent FA's granted
population *after* a Module Admin has placed them on a Sub-FA, does the
Module assignment auto-revoke, or persist until the Module Admin removes
it? The narrowing-within-boundary framing (D5) implies it **should**
auto-revoke (you can't be narrowed into a set you've left), but the
mechanism — who enforces it, ATOM or the Module — isn't specified. Low
urgency (exploratory), but it's the one place the two-layer enforcement
could silently drift out of sync.

---

## E. Downstream doc impact (when committed — not editing now)

- **§7.4 / §7.7.3** — state that FA grant auto-cascades to all Sub-FAs
  (current and future); remove any implication of a separate Sub-FA
  grant. Drop the earlier "Sub-FA-specific assignment deferred to Phase
  2" line — it's delegated to Modules, not deferred (D4).
- **§8 / §8.2 / §8.4** — add that Module-level Sub-FA assignment is a
  Module responsibility, bounded by the D5 parent-FA-population
  guardrail; Task Board-per-Sub-FA is the Task Management Module's
  scoping choice (Sub-FA optional per FA).
- **§7.7 (RBAC)** — introduce the **Module Admin** axis (D6): per-
  Project-per-Module, Industry-granted, Institution-or-Individual
  holder, layered on existing Project membership.
- **§10** — one line: Functional Area isolation encompasses Sub-FAs; the
  guardrail (D5) is the second enforcement layer for Module-side
  assignment.
- **§7.5** — note explicitly that Progress Tracking's existing flat
  "every FA / Sub FA / Task Board" computation already accommodates
  Module-scoped Sub-FA Task Boards.
- **backlog.md** — no deferred-D4 item (withdrawn). If anything, a small
  item to specify O1/O2 before build.
- **Cross-doc** — the Grievance §L addendum in
  `2026-07-17-communication-module-integration.md` now resolves its open
  "who configures the escalation matrix" question against **this**
  Module Admin model (the Grievance Module's own Module Admin).
