# [2026-07-28] User Management — Remove, Suspend & Delete actions

> Standalone ideation doc. Index entry lives in `ideation/README.md`.
> Exploratory — nothing here is committed to `context/atom-context.md`
> yet. Section refs (§) point at `context/atom-context.md`; cross-doc
> refs point at the named ideation file. This feature does **not** exist
> in `atom-context.md` yet — greenfield ideation, not spec reconciliation.

**Status:** Under review — a **2026-07-29, 3rd pass (D21)** established
that **Home > UM is not Master-Admin-exclusive as a page** — Industry and
Institution each get their own Home > UM (cross-Project rollup of their
own network, **view-only**, no new action), alongside their existing
Project > UM (Remove, unchanged). See §A.0a. Earlier passes: reworked in a
**second 2026-07-28 pass** that
(1) formalizes **two distinct UM surfaces** (Home > UM vs Project > UM,
§A.0); (2) **reverses D5** — Master Admin *does* get Remove in Project >
UM; (3) **retracts D6** — there is no "Suspend an Institution/Industry";
Suspend is **Individual-only, everywhere**; (4) **replaces D13's**
§C-checklist gating of Suspend with **one** simpler rule — *any active
project tie blocks Suspend* (§C.2); (5) adds a brand-new **Delete** action
(Industry / Institution / Individual-soft, Home > UM, Master-Admin-only,
§C.3 / §D). The old D6 workspace-freeze mechanics and the D11b "Deactivate
Tenant" action are **superseded** (see D6, D11). A **third 2026-07-28 pass**
then resolved three of the remaining open items: **D14** (child-handling —
now a **hard block** until children are reassigned/removed, B6), **D15**
(urgent-security override — **no override; stick to the defined cases**),
and the **D12** appeal-path sub-question (**no appeal mechanism in v1**,
accepted gap). A **2026-07-29 pass** confirmed Industry's action set is
final (**D19** — Remove only; a Project-scoped Restrict/Pause action was
considered and rejected for v1). A **second 2026-07-29 pass** resolved the
last open item — **D20**, the soft-deleted Individual's post-state: **30-day
reactivation grace window**, login blocked with permanent-framing copy
(same UX pattern as Suspend's notice, but "deleted" not "suspended"), and a
**Deleted filter/tab** on Home > UM so Master Admin retains visibility for
audit + reactivation during the window. **Nothing open remains — this doc
is now fully resolved.** Nothing committed to `atom-context.md`.

---

## A. Scope & the two surfaces this attaches to

The **User Management** area (part of the §8.3 Organisation Management
module — "user management, role management") surfaces **Industry**,
**Institution**, and **Individual** entities. This doc adds **three row
actions** across **two distinct UM surfaces** (§A.0):

1. **Remove** — remove an Institution/Individual from a **Project**
   (Project > UM). Project-scoped participation management.
2. **Suspend** — lock an **Individual** out of ATOM **platform-wide** for
   a Master-Admin-set duration (cannot log in). Home > UM only.
3. **Delete** — remove an entity (Industry / Institution / Individual)
   from ATOM **entirely** (Individual = *soft* delete). Home > UM only.

Remove, Suspend, and Delete are **different axes** (D1): Remove is
Project-scoped participation; Suspend is account/login governance; Delete
is tenant/account destruction. They must not be collapsed into one
"deactivate" control.

### A.0 The two User Management surfaces (NEW — foundational)

ATOM has **two** UM surfaces, not one. Which surface an action lives on is
now the primary organizing fact of this whole doc.

| Surface | Population it lists | Nature of its actions |
|---|---|---|
| **Home > UM** | **Platform-wide** — ALL Industries / Institutions / Individuals on ATOM (this is exactly the §A.1 "Master Admin" row, now formalized as *this specific page*). | **Account / tenant governance:** **Suspend**, **Delete**. |
| **Project > UM** | **Scoped to a single Project** — the Institutions / Individuals participating in *that* Project (this is exactly what the §A.1 Industry/Institution viewer rows always described; their lists were always implicitly Project-scoped). | **Participation management:** **Remove** only. |

**Why the split exists (the self-consistency problem it fixes).** Both
Suspend and Delete carry block conditions phrased as "has an active
project tie" (§C.2, §C.3). But **merely being listed on a Project's own
UM page already proves a tie to that Project** — so if Suspend/Delete lived
on Project > UM they would be permanently blocked/dead for every row there.
Putting Suspend and Delete **only on the platform-wide Home > UM page**
— where an entity can legitimately have *zero* active project ties — is
what makes those block conditions reachable. Project > UM therefore stays
**pure: Remove only**, for every actor. Symmetrically, **Remove is
inherently Project-scoped** and has no meaning platform-wide, so it never
appears on Home > UM.

### A.0a Home > UM is a recurring pattern, not a Master-Admin-only page
(NEW — 2026-07-29, D21)

Every workspace type gets **both** a Home > UM and a Project > UM — not
just Master Admin. What differs per workspace is **scope and whether the
surface carries actions**:

| Workspace | Home > UM population | Home > UM actions | Project > UM population | Project > UM actions |
|---|---|---|---|---|
| **Master Admin** | **Platform-wide** — ALL Industries/Institutions/Individuals on ATOM | **Suspend, Delete** (D2, D16–D18) | Single Project's participants | **Remove**, whole-Project (D5) |
| **Industry** | **Cross-Project rollup** — every Institution/Individual across **all of this Industry's own Projects**, deduplicated | **View-only** — no actions | Single Project's participants (this Industry's own Project) | **Remove**, whole-Project (D3/D19 — only action, unchanged) |
| **Institution** | **Cross-Project rollup** — every child Institution/Individual across **every Project this Institution joined and added those children into**, deduplicated | **View-only** — no actions | Single Project's own-branch children (this Institution's branch in that Project) | **Remove**, own-branch (D4 — only action, unchanged) |

**Why Home > UM is view-only for Industry and Institution (unlike Master
Admin's).** Suspend and Delete are **platform-level governance actions**
(D2, D16–D18) — deliberately Master-Admin-exclusive regardless of surface.
Giving Industry or Institution a Home > UM does **not** hand them a lighter
version of those actions; it only gives them **visibility** across their
own network without having to open each Project individually. This keeps
D19's "Industry: Remove only, no lighter Restrict/Pause action" conclusion
intact, and extends the same discipline to Institution: **no new action
surface was created by adding Home > UM to these two workspaces** — Remove
stays the sole lever, and it stays Project-scoped only (§A.0's split still
holds: Remove never appears on any Home > UM, including these two).

**Why the population differs from Master Admin's platform-wide list.**
Per §5.5, the Institution-under-Institution (and Industry's
Institution-in-Project) relationship is established **per-Project** — there
is no standing, platform-wide hierarchy table. So Industry's and
Institution's Home > UM cannot be a single flat list the way Master
Admin's is (Master Admin's has no "owner" to scope by); it is necessarily
a **rollup computed across each workspace's own Projects** — a union of
what that workspace already sees on every one of its individual Project >
UM pages, presented in one place. Nothing new is computed that isn't
already visible by clicking through every Project; Home > UM just spares
the click-through.

### A.1 List-visibility restated (as given by the user) + canon reconciliation

The rows a viewer sees on **Project > UM** are scoped to **one Project at
a time** by list-visibility. **Home > UM** shows everything the viewer is
entitled to see with no Project filter applied — platform-wide for Master
Admin (§A.0), a cross-Project rollup for Industry/Institution (§A.0a, D21).
The table below described Industry's/Institution's lists before D21
formalized the Home/Project split for them; read "Project(s)" (plural) in
those two rows as **what Home > UM now makes explicit as one rollup** —
the single-Project row (**Project > UM**) is the same population filtered
down to just one Project.

| Viewer | Industry list | Institution list | Individual list |
|---|---|---|---|
| **Master Admin (Home > UM)** | ALL Industries (assign module, view profile, see projects) | ALL Institutions on ATOM (view profile) | ALL Individuals on ATOM (view profile) |
| **Industry (Home > UM, rollup — D21)** | — | Institutions across **all** of this Industry's Projects | Individuals across **all** of this Industry's Projects |
| **Industry (Project > UM, single Project)** | — | Institutions in **this** Project | Individuals in **this** Project |
| **Institution (Home > UM, rollup — D21)** | — | Child Institutions across **every** Project this Institution joined and added them into | Child Individuals across **every** such Project |
| **Institution (Project > UM, single Project)** | — | Institutions under this Institution's branch in **this** Project | Individuals under this Institution's branch in **this** Project |

**Reconciliation findings against canon (flagging, not blocking):**

- **F1 (minor).** Industry/Institution visibility scoping is consistent
  with §10 isolation — Industry sees only its own Projects (Project
  isolation); Institution sees only its own sub-population (Functional
  Area isolation / participation lineage). Any Remove action must respect
  these *same* isolation scopes: you can only act on rows your Project > UM
  list surfaces, **and** RBAC/PBAC/ABAC must gate the *action* independently
  of mere visibility (§10, §16 — never flatten the three). Visibility ≠
  authority.
- **F2 (significant).** "Institutions **under** this Institution" and the
  user's note ("an Institution can request to join another Industry's
  project **under another Institution**, only if that target Institution
  is already part of the project") assume a **parent/child Institution
  relationship** that `atom-context.md` only *partially* defines. §5.5
  lists `Institution → Institution → Industry` as a participation path.
  **Per the 2026-07-28 review, this is a *current, v1-shippable* structure
  capped at exactly 2 levels:** an Institution may have Institutions/
  Individuals under it in a Project, but those child Institutions have
  **no further children of their own** (no grandchildren in v1). Only
  *deeper* nesting is Phase 2 (Flow Builder §K sub-canvas recursion). The
  2-level cap is what makes the child-handling problem bounded — there is
  no unbounded cascade, only one level of children to handle when a parent
  is **removed** (D4, D14, B6). *(Note: child-handling is now a Remove-only
  concern — Suspend no longer applies to Institutions at all; see D6.)*
- **F3 (minor).** The Industry list is empty for Industry/Institution
  viewers, and only Master Admin manages Industries — consistent with §4
  (Industry created/governed by Master Admin only) and §7.7.4 (Industry
  is never a participant). Industry-level governance is therefore a
  Master-Admin-only, Home > UM concern: an Industry can be **Deleted**
  (§C.3, §D) but never Removed (not a Project participant) or Suspended
  (not a login-bearing account — D6).

---

## B. The canonical Surface × Row × Action matrix (headline deliverable)

This table **replaces** the old actor×action matrix. Read it as: *on this
surface, this row type exposes these actions.*

| Surface | Row | Available actions |
|---|---|---|
| **Home > UM** | **Industry** | **Delete** (Master Admin only) |
| **Home > UM** | **Institution** | **Delete** (Master Admin only) |
| **Home > UM** | **Individual** | **Suspend** (Master Admin only) · **Delete / soft** (Master Admin only) |
| **Project > UM** | **Institution** | **Remove** — Master Admin (whole project) · Industry (whole project) · Institution (own branch) |
| **Project > UM** | **Individual** | **Remove** — same three actors, same scoping |

**Note (D21):** Industry's and Institution's own Home > UM rollups (§A.0a)
add **no rows to this table** — they are view-only surfaces, so every
action in this matrix is still exactly the set that existed before D21.
D21 only changed *where visibility lives*, not *what actions exist*.

**Invariants baked into this matrix (all confirmed 2026-07-28, 2nd pass):**

- **No Suspend or Delete anywhere in Project > UM** — they would be
  self-defeatingly blocked there (§A.0).
- **No Remove anywhere in Home > UM** — Remove is inherently
  Project-scoped and has no platform-wide meaning (§A.0).
- **Suspend is Individual-only, always, everywhere** (D6). Institutions
  and Industries are never suspendable.
- **Delete is Master-Admin-only** and Home > UM only; no other actor was
  granted it.
- **Remove's three actors, one scoping rule:** Master Admin removes across
  the **whole project** (D5, reversed); Industry removes across the
  **whole project** (D3); Institution removes only within its **own
  branch/sub-population** (D4). Every Remove is dependency-gated by the
  **same §C.1 checklist**, regardless of actor.

### B.1 Per-cell notes

- **Home > UM · Industry · Delete** — removes the Industry from ATOM
  entirely; it disappears from the linked Individual's workspace switcher.
  Strictest block of the three (owns *any* Project, ever — §C.3, D16).
- **Home > UM · Institution · Delete** — removes the Institution from ATOM
  entirely; disappears from the linked Individual(s') workspace switcher.
  Block is active-only (§C.3, D17).
- **Home > UM · Individual · Suspend** — platform-wide login lock, finite
  or **Indefinite** duration (D7). Gated by the single active-tie rule
  (§C.2, D13-corrected), **not** the §C.1 checklist. Cannot suspend the
  last/another Master Admin (B10).
- **Home > UM · Individual · Delete (soft)** — soft delete preserving the
  audit trail (B14 principle); block is active-only EITHER-logic (§C.3,
  D18). Post-delete account state is an **open gap** (§D).
- **Project > UM · Institution/Individual · Remove** — see §C.1 for the
  full dependency-gating checklist that applies to all three Remove actors.

### B.2 What is *not* here (superseded — do not reintroduce)

- **"Suspend an Institution" / "Suspend an Industry"** — **gone (D6
  retracted).** Institution's only lever anywhere is **Remove** (Project >
  UM). Industry gets **neither** Remove nor Suspend — only **Delete**
  (Home > UM).
- **"Deactivate Tenant" (old D11b)** — **superseded (D11).** The tenant-
  governance need it addressed is now served by **Delete Industry** (Home >
  UM), plus the Suspend/Delete active-tie rules that account for an
  Individual's ownership of an Industry with active Projects.
- **"Master Admin emergency Remove override" as a rejected concept (old
  D5)** — **gone.** Master Admin Remove is now a *first-class, ordinary*
  capability (D5 reversed), dependency-gated exactly like everyone else's
  Remove — not an emergency override.
- **"Industry Project-scoped Restrict/Pause" action — considered and
  rejected (D19).** Industry's action set stays **Remove-only**; no
  lighter, reversible, Project-scoped bench/pause action was added.

---

## C. Blocking-condition rules per action

Disposition vocabulary borrows Flow Builder §H (**managed teardown** vs
**hard block**) and the backlog "Delete Role safety" pattern
(block-until-reassignment + impact-count confirm).

### C.1 Remove blocking-condition checklist (Project > UM)

These conditions gate **Remove** for **all three Remove actors** (Master
Admin whole-project, Industry whole-project, Institution own-branch).
**They no longer gate Suspend** — Suspend now has its own rule (§C.2).

| # | Condition | Ref | Disposition |
|---|---|---|---|
| **B1** | Target holds **FA Head / FA Owner** of an FA | Flow Builder §K; backlog *Assign FA Owner*; §7.7.3 | **Hard block** until FA ownership reassigned or vacated. |
| **B2** | Target (Institution) holds an **FA grant with active execution** | §7.7.3, §3.1, §7.4 | **Managed teardown** — reassign/release the FA first; never silently orphan it. |
| **B3** | Target has **open Tasks** (To Do / In Progress / Reopen) | §7.5, §3.1 | **Allow + require reassignment/cancel** first (managed teardown). Open Tasks also sit in the §7.5 progress denominator — orphaning corrupts the number. |
| **B4** | Target (Institution) holds a **QuotaGrant with live Entries/nominations** | Flow Builder §C/§E/§H | **Managed teardown** — release/reassign nominations first. *Phase 2* (QuotaGrant uncommitted). |
| **B5** | Target is the **sole Module Admin** for a Module in the Project | Sub-FA §D6/O1 | **Hard block** until Module Admin reassigned. *Phase 2.* |
| **B6** | Target (Institution) has **child Institutions / Individuals** under it (v1: exactly one level, F2) | §5.5, F2 | **Hard block** (D14, resolved) until every child Institution/Individual is **reassigned to a different parent Institution or Removed first** — same disposition family as B1/B7. Not a cascade, not an orphan. Bounded (2-level cap → only one level of children). |
| **B7** | Target is **sole holder of a sensitive permission / last Project admin** | backlog *Delete Role safety*, §10 | **Hard block** — classic last-admin lockout; reuse Delete-Role machinery. |
| **B8** | Target has a **pending join request/invitation in flight** | §7.7 Requests & Invitations | **Resolve atomically** — Remove cancels/withdraws the pending request + audit. |
| **B9** | Target joined via **Command** (non-rejectable) | §7.2 | **Allow.** Non-rejectable binds the *recipient*, not the *issuer*; the Industry may rescind. Flag copy: Remove ≠ "participant rejected." |
| **B13** | **Self-remove / voluntary leave** | §7.7.4 | **N/A for Industry** (never a participant). Participant-initiated "leave Project" is a *separate* out-of-scope flow — flag. |
| **B14** | **Athlete data retention** on Remove (sports-tech lens) | §9 Pro Membership, §8.2 AMS, §10 ABAC | Remove must **not delete** the Individual's cross-Project AMS history. Remove ends *participation*, not the *account's data*. (This same retention principle is why Delete-Individual is *soft*, §C.3/D18.) |

**Phase note:** B1, B2, B3, B6, B7, B8, B9, B13, B14 rest on Phase-1
primitives (B6 bounded by the v1 2-level cap, F2) and ship in a v1 Remove.
Only **B4** (QuotaGrant) and **B5** (Module Admin) depend on Phase-2
concepts and layer in as those land.

### C.2 Suspend gating — ONE rule (Home > UM, Individual)

**This replaces the old §C-checklist gating of Suspend (D13-corrected).**
The granular conditions B1 (FA Head), B3 (open Tasks), B4 (QuotaGrant), B5
(Module Admin), B7 (sole/last admin) **remain exactly as above for
Remove**, but **no longer separately apply to Suspend** — because the rule
below **subsumes** all of them: none of those structural roles is even
reachable without an active project tie in the first place.

> **Block Suspend if the Individual has ANY active project tie, anywhere:**
> - **(a)** their **own direct** Project participation (as participant /
>   User-Type / Role holder), **OR**
> - **(b)** they **own an Industry** (§5.3 linked account) that has **≥1
>   active Project**, **OR**
> - **(c)** they **hold an Institution workspace** that has joined **≥1
>   active Project**.

- **"Active" = current/ongoing only.** A **fully completed / archived**
  project history does **NOT** block Suspend (deliberate user choice,
  resolved 2026-07-28 — this is intentionally *looser* than Delete-Industry's
  "any project ever" standard, §C.3/D16).
- **Master Admin must drive the target to zero active ties first** — via
  **Remove**, in each relevant **Project > UM** — before Suspend becomes
  available on Home > UM.
- **B10 (governance guard, retained, non-project-tie).** Master Admin
  **cannot suspend the last/another Master Admin** (§4) — the platform
  must never be suspendable into zero admins. This is a standalone
  invariant, *not* a project-tie condition, so the active-tie rule doesn't
  cover it; it stands alongside the rule.
- **Superseded:** old **B11** (Suspend + owns Industry tenant → route to
  "Deactivate Tenant") is folded into clause **(b)** above; **B12**
  (structural-role gating of Suspend, the old D13 mechanism) is dropped —
  replaced wholesale by this rule.

### C.3 Delete blocking conditions (Home > UM, Master Admin only)

Three entity-specific block standards. Full definitions/flows in §D.

| Delete target | Block condition | "Active" scope | Decision |
|---|---|---|---|
| **Industry** | Owns **any Project, ever** (current **or** previous) | **All history** — strictest of the three, to protect historical records tied to the tenant | **D16** |
| **Institution** | Has joined **any active Project** | **Active-only** — an Institution with only fully-archived history **CAN** be deleted (deliberate asymmetry vs. Industry) | **D17** |
| **Individual** (**soft**) | **EITHER** of: (a) own direct **active** Project participation, (b) owns an Industry with an **active** Project, (c) holds an Institution workspace with an **active** Project | **Active-only, EITHER-logic** — any single one blocks; **not** require-all-three | **D18** |

---

## D. Delete action — definitions, flows, decisions

**D16 — Delete Industry. Home > UM, Master-Admin-only.** Removes the
Industry from ATOM **entirely**; it disappears from the linked Individual's
workspace switcher. **Block: owns any Project, ever — current or previous**
(the strictest of the three standards, deliberately, to protect historical
project records tied to the tenant). *Flow:* Master Admin selects the
Industry row on Home > UM → **Delete** → system checks project-ownership
history → if any Project exists (active or archived), **hard block** with
the offending count/reference; if clean, confirm modal → Industry removed
from ATOM, workspace-switcher entry disappears for the linked Individual,
audit written (§10).

**D17 — Delete Institution. Home > UM, Master-Admin-only.** Removes the
Institution from ATOM **entirely**; disappears from the linked
Individual(s') workspace switcher (an Institution workspace can be held by
several Individuals). **Block: has joined any *active* Project** (resolved
2026-07-28) — **narrower than Industry**: an Institution whose project
history is entirely fully-archived **can** be deleted. *Flow:* Master
Admin selects the Institution row on Home > UM → **Delete** → system checks
for active project participation → if any **active** Project tie, **hard
block** (resolve via Remove in each Project > UM first); if only archived
history or none, confirm modal → Institution removed, switcher entries
disappear for all holding Individuals, audit written.

**D18 — Delete Individual. Home > UM, Master-Admin-only. SOFT delete.**
Preserves the underlying data / audit trail — does **not** hard-erase —
consistent with the existing **B14** athlete-data-retention principle
already in the doc (§9 Pro Membership depends on cross-Project history).
**Block (EITHER-logic, active-only, resolved 2026-07-28):** blocked if
**ANY** of — **(a)** the Individual has their own direct **active** Project
participation, **(b)** owns an Industry with an **active** Project, **(c)**
holds an Institution workspace with an **active** Project. Any single one
blocks; it is **not** require-all-three. *Flow:* Master Admin selects the
Individual row on Home > UM → **Delete** → system evaluates (a)/(b)/(c) →
if any is true, **hard block** (drive to zero active ties via Remove /
by resolving the owned Industry / held Institution first); if all clear,
confirm modal → **soft** delete applied, audit trail retained.

**D20 — Soft-deleted Individual post-state. RESOLVED (2026-07-29, 2nd
pass, user-fixed).** Closes the last open gap from D18.

- **Reactivation: time-limited grace window.** Master Admin can restore a
  soft-deleted Individual within **30 days** of the Delete action. After 30
  days, the account moves to a **permanent/hard state** — no in-app restore
  path remains. (Underlying data/audit trail is still retained per B14 —
  "soft" continues to mean data-preserving — but *account recoverability*
  itself expires; the 30-day window is what makes Delete genuinely distinct
  from Indefinite Suspend rather than a synonym for it.)
- **Login: blocked, permanent framing.** Same UX mechanism as Suspend's
  login-block notice (D7/D12) — the account cannot log in — but the copy
  reads as a deletion, not a suspension (no duration/expiry shown, no
  "reactivation pending" framing pre-restore). This distinguishes it from
  Suspend's "your account is suspended until X" notice at the UI-copy level
  even though the underlying block mechanism is shared.
- **Visibility: Deleted filter/tab on Home > UM.** Soft-deleted Individuals
  do **not** appear in Home > UM's default/active list, but Master Admin
  can find them via a dedicated **Deleted** filter/tab — this is what makes
  the 30-day reactivation window actually usable (Master Admin needs a way
  to *find* the row to restore it) and doubles as an audit surface. Past 30
  days, the row's disposition in that filter (still visible read-only vs.
  fully gone from the UI) is a follow-on detail, not blocking for v1.
- **Interaction with D9 (orthogonality):** reactivating within the window
  restores the account to **zero active project ties** (exactly the state
  it was deleted from, per D18's block condition) — reactivation does not
  retroactively restore Project participation. Any return to active
  participation happens through the ordinary join/Assign flow (§7.7)
  afterward, same as it would for a reactivated-from-Suspend Individual
  (D8c parallel).

---

## E. Decisions

### E.1 Settled / user-fixed

**D1 — Remove, Suspend, and Delete are independent axes.** Remove =
Project-scoped participation removal (Project > UM). Suspend = platform/
account login lock (Home > UM). Delete = entity destruction (Home > UM).
Orthogonal (see D9). *(Definitions user-given; independence framing is
this doc's.)*

**D2 — Suspend authority: Master Admin only. FIXED.** Industry and
Institution cannot suspend anyone. Platform-wide login lock, duration set
by Master Admin at suspend time. Suspend targets **only Individuals** (D6).

**D3 — Industry Remove: own Project only, whole-Project, dependency-gated.
FIXED.** Industry can Remove an Institution/Individual from its own Project
— the **whole Project's** population — gated by §C.1. Contrast the narrower
Institution Remove (D4).

**D4 — Institution Remove: branch-scoped. FIXED.** An Institution may
Remove Institutions/Individuals **under it in the Project** (its own
sub-population/branch), same scoping as its §A.1 Project > UM visibility —
**narrower than Industry's** whole-Project Remove. Dependency-gated per
§C.1 (including B6 child-handling, D14). v1 capability, bounded by the
2-level cap (F2).

**D5 — Master Admin Remove: YES, whole-Project, first-class. REVERSED
(2026-07-28, 2nd pass).** *Previously rejected* ("no emergency override
Remove"). **Now settled the other way:** Master Admin can Remove an
Institution or Individual from a Project via **Project > UM**, at
**whole-Project** scope (like Industry). The same §C.1 dependency-gating
checklist applies to Master Admin's Remove as to everyone else's — it is
**not** an emergency/override capability and carries **no** special
"past-a-block" power; it is an ordinary, dependency-gated Remove. The old
"rejected, route emergencies through Suspend/Deactivate Tenant" framing is
**gone**.

**D6 — "Suspend an Institution / an Industry" does not exist. RETRACTED
(2026-07-28, 2nd pass).** Suspend is **Individual-only, always,
everywhere.** *Rationale (user's, confirmed):* only **Individuals** log in
(root identity, §2); Institution and Industry are **workspaces**, not
login-bearing accounts, so "suspend" (a login block) does not apply to
them structurally. The old D6 **workspace-freeze mechanics** (the 6-step
Role-Switcher-blocking flow) are **removed — that concept is gone, not
deprioritized.** Consequences: an **Institution's** only lever anywhere is
**Remove** (Project > UM); an **Industry** gets **neither** Remove nor
Suspend — only **Delete** (Home > UM). Matrix updated accordingly (§B).

**D7 — Suspend duration: finite OR Indefinite. FIXED.** At suspend time,
Master Admin selects a **finite duration** or **"Until I change it" /
Indefinite**. Indefinite has **no auto-expiry** — ends only via manual
reactivation (see D8a). A separate permanent Ban, if ever wanted, stays a
distinct future action.

**D11 — Self/last-admin guard; tenant scope superseded.**
  - **(a) RESOLVED.** Master Admin **cannot suspend another Master Admin**
    or **the last** one (B10) — no platform-admin lockout/coup. Industry is
    never in its own participant list (§7.7.4) → no Industry self-Remove. A
    participant-initiated **leave-Project** is a separate future flow (B13).
  - **(b) SUPERSEDED (2026-07-28, 2nd pass).** The old **"Deactivate
    Tenant"** action is **withdrawn.** Its purpose — tenant-level Industry
    governance — is now served by **Delete Industry** (Home > UM, D16), and
    the Suspend/Delete active-tie rules already account for an Individual
    who owns an Industry with active Projects (§C.2 clause b, §C.3). There
    is no separate freeze-the-tenant action in this model.

**D19 — Industry's action set is final: Remove only. RESOLVED (2026-07-29,
user-fixed).** Confirmed Industry has exactly one lever on the Institutions/
Individuals in its Project — **Remove** (§C.1-gated, whole-Project scope,
D3) — and nothing else: no Suspend (D2, Individual-only/Master-Admin-only),
no Delete (Master-Admin-only, D16–D18). A lighter, reversible, Project-
scoped "Restrict/Pause" action (bench a participant within just this
Project, short of full Remove) was explicitly considered and **rejected for
v1** — Remove stays the only lever, to keep the model as simple as what's
already built. If this need resurfaces (e.g. benching a participant during
a dispute without ending their Project tie), it is a **v2 candidate**, not
in scope now.

### E.2 Proposed (recommendation — confirm)

**D8 — Suspend expiry / reactivation.** (a) **Auto-restore at expiry** for
**finite** durations (backend-scheduled, no manual step); **Indefinite**
does **not** auto-restore (D7) and needs a manual lift. (b) **Manual early
lift** allowed for finite suspensions. (c) **Structural roles persist**
through suspension and **resume** on reactivation — Suspend freezes access,
it does not strip FA ownership / quota / roles. (d) **Audit mandatory**
(§10): who, when, duration (or Indefinite), reason, every lift/reactivation.

**D9 — Remove / Suspend / Delete interaction: orthogonal.**
  - A **suspended** Individual **can still be Removed** from a Project (by
    Master Admin, Industry, or the owning Institution) — Remove works
    regardless of suspend state.
  - **Suspend and Delete both require zero active ties** (§C.2, §C.3), so
    in practice **Remove is the precondition** for both — you Remove a
    target out of its active Projects (Project > UM) before you can Suspend
    or Delete it (Home > UM). This is a deliberate one-way dependency, not
    a symmetry.
  - Removing from all *active* Projects does not by itself Suspend or
    Delete — those remain explicit Master Admin actions.

**D10 — Blocking sets are now per-action, not one shared checklist.**
**Remove** → §C.1 checklist (hard block / managed teardown per condition),
reusing Flow Builder §H + Delete-Role-safety patterns. **Suspend** → the
single active-tie rule (§C.2) + B10. **Delete** → the three entity-specific
standards (§C.3). A destructive action that would orphan/strand FA
ownership, Tasks, quota, Module Admin, or child entities must be
impossible.

**D12 — Notification & appeal.**
  - **Remove:** notify the removed party (§8.1) and reflect it as a
    **status on the §7.7 Requests & Invitations page** (e.g. `Removed`).
  - **Suspend:** notify + **force logout**; next login shows a suspension
    notice with duration/expiry (or "Indefinite"). Needs its own
    account-status surface, not the Project-scoped Requests page.
  - **Delete:** account/tenant-level; notify the linked Individual(s) and
    (for Industry/Institution Delete) write audit (§10). Copy differs from
    Suspend — Delete is terminal (Industry/Institution) or soft-terminal
    (Individual), not a timed lock.
  - **Reason field required** on all three (feeds audit §10 + notification).
  - **Appeal: RESOLVED (2026-07-28, 3rd pass) — no appeal mechanism in
    v1.** No appeal/dispute path is planned currently — **neither** the
    previously-recommended out-of-band-to-Master-Admin route **nor** an
    in-app appeal queue. Remove/Suspend/Delete are final for v1 with no
    built-in reversal-request flow. **Flagged as an accepted gap/risk**
    (same convention as Flow Builder D3's no-edit-after-registration): the
    design is intentionally deferred, not an oversight — an appeal workflow
    can be added later without disturbing these actions.

**D13 — Suspend gating: SIMPLIFIED. CORRECTED (2026-07-28, 2nd pass).**
*Previously* this decision said "the whole §C checklist gates Suspend too."
**That is now superseded.** Suspend is gated by **one** rule — *any active
project tie blocks Suspend* (§C.2) — which **subsumes** the granular §C.1
structural conditions (none is reachable without an active tie). The §C.1
checklist **still gates Remove** exactly as before; it simply no longer
*separately* gates Suspend. B12's old Suspend disposition is dropped.

### E.3 Resolved (3rd 2026-07-28 pass + 2026-07-29 passes) — nothing open remains

**D14 — Child-handling when a parent Institution is *removed*. RESOLVED
(2026-07-28, 3rd pass, user-fixed) — HARD BLOCK.** A **Remove-flow** question
only (Suspend no longer applies to Institutions, D6). Given the v1 2-level
structure (F2 — no grandchildren), when Master Admin (D5), Industry (D3), or
the parent's own Institution (D4) **removes** a parent Institution that still
has child Institutions/Individuals under it in that Project, the Remove is
**hard-blocked**. It **cannot proceed** until every child is either
**reassigned to a different parent Institution** in the Project **or Removed
itself first**. This is the **same disposition family as B1 (FA Head) and B7
(last admin)** — a straightforward hard block, **not** a cascade-remove and
**not** an orphan-as-direct-participant. (The rejected orphan option would
have broken reporting/quota lineage; cascade was declined in favour of the
simpler, safer block.) Reflected in B6 (§C.1). The 2-level cap keeps this
bounded — only one level of children can ever be in the way.

**D15 — Urgent-security override past a block. RESOLVED (2026-07-28, 3rd
pass, user-fixed) — NO OVERRIDE.** Master Admin gets **no** way to force a
Suspend past the active-tie rule (§C.2) **or** to force a Remove past a
hard-block condition (§C.1), **even for urgent security situations.** User's
words: *"stick to the cases we have defined."* A genuinely compromised/abusive
account must still be driven to zero active ties (via Remove) before it can be
Suspended — there is **no break-glass exception path in v1.** This is a
deliberate simplicity/safety choice: no override machinery to build, misuse,
or audit-around. (If a future need emerges, a scoped force-suspend with
mandatory post-hoc cleanup remains the natural design — but it is explicitly
**not** in v1.)

**D20 — Soft-delete post-state — RESOLVED (2026-07-29, 2nd pass).** 30-day
reactivation grace window, Suspend-style login block with permanent framing,
Deleted filter/tab on Home > UM for visibility. See full definition in §D.

**D21 — Home > UM extended to Industry and Institution, view-only.
RESOLVED (2026-07-29, 3rd pass, user-fixed).** Home > UM is **not**
Master-Admin-exclusive as a *page* — Industry and Institution each get
their own Home > UM too. What stays Master-Admin-exclusive is the
**Suspend/Delete actions**, not the surface itself. Industry's Home > UM =
cross-Project rollup of Institutions/Individuals across all of *its own*
Projects; Institution's Home > UM = cross-Project rollup of child
Institutions/Individuals across every Project it joined and added those
children into. **Both are view-only** — no new action was created; Remove
remains each workspace's sole lever and stays exclusively on Project > UM
(§A.0's surface split is unchanged, just extended to explain what Home >
UM *is* for non-Master-Admin workspaces rather than pretending it doesn't
exist for them). See §A.0a for the full three-way table.

---

## F. Feasibility & phasing

- **Suspend (Master Admin, Individual login lock + duration + audit):
  Feasible now.** All primitives are Phase 1 — Authentication, Audit,
  Notifications, Master Admin (§8.1, §14). Gating is now the simple
  active-tie rule (§C.2), which only needs project-participation lookups
  ATOM already has; it is *cheaper* to build than the old §C-checklist
  gating it replaces.
- **Remove (all three actors, dependency-gated): Feasible now for the
  Phase-1 blocker subset** (B1 FA Head, B2 FA grant, B3 Tasks, B6 one-level
  children, B7 last-admin, B8 pending requests, B9 Command, B14 retention).
  **Master Admin whole-project Remove (D5, reversed)** reuses exactly the
  Industry Remove path at whole-project scope — **no new machinery**, just
  an added authorized actor. Only B4 (QuotaGrant) and B5 (Module Admin)
  layer in later.
- **Institution Remove (D4): Feasible now** for the v1 branch-scoped,
  2-level structure (F2). Deeper nesting is Phase 2 (Flow Builder §K).
- **Delete Industry / Institution (D16/D17): Feasible now.** Block checks
  are project-ownership / project-participation lookups (history-wide for
  Industry, active-only for Institution) plus workspace-switcher removal
  and audit — all Phase-1 primitives.
- **Delete Individual / soft (D18/D20): Feasible now, end-to-end.** The
  active-tie EITHER-logic block, the 30-day reactivation window (needs a
  scheduled/background expiry job — same primitive class as finite-Suspend
  auto-restore, D8a), the Suspend-style login-block notice, and the Deleted
  filter/tab on Home > UM are all Phase-1 primitives (Auth, Notifications,
  Audit, scheduled jobs). No remaining design gap blocks shipping this
  action.

---

## G. Backlog overlap check

- **[2026-07-09] "Delete Role safety: reassignment flow + immutable system
  roles"** — **strongest tie-in.** Remove-with-dependencies (§C.1) is the
  same structural pattern: block a destructive action until reassignment,
  show an impact-count confirmation, never orphan dependents / lock out the
  last admin (B1, B7). Remove should **reuse** this machinery.
- **[2026-07-09] "'Assign User' must respect the Project join/approval
  gate"** — Remove is the **inverse of Assign**; both must respect the §7.7
  Industry-decision gate.
- **[2026-07-09] "Granular permissions / promote 'Assign FA Owner' to a
  first-class permission"** — the **right to Remove** (and to **Delete** /
  **Suspend**) should each be a first-class sensitive RBAC permission, not
  merely implied by being the Industry or Master Admin. B1 is the direct
  dependency of the FA-Owner assignment this item elevates.
- **[2026-07-09] "Define role-scoping level for RBAC roles (Project vs
  Industry template)"** — the Remove permission needs a scope decision
  (Project-scoped vs Industry-template).
- **Sub-FA ideation §D6 (Module Admin)** — B5 sole-Module-Admin blocker
  (Remove); ties to that doc's O1 cardinality question.
- **Flow Builder v2 §C/§E (QuotaGrant), §H (managed teardown), §K (FA
  Head / sub-canvas)** — B4 and the managed-teardown vocabulary come from
  here; *deeper* (grandchild+) nesting for B6/D14 is Flow Builder §K
  recursion (Phase 2), while the v1 one-level B6 case is native to this
  feature (F2).
- **Delete is net-new-new** — no existing backlog item covers Delete
  (Industry/Institution/Individual-soft); no tie beyond the shared
  Delete-Role-safety/last-admin pattern above. **No existing backlog item
  covers Remove/Suspend directly either.** Recommend a single backlog entry
  covering Remove + Suspend + Delete once D5/D6/D13/D16–D18 are confirmed
  and the soft-delete post-state gap is closed.

---

## H. Related context sections

§2 (root identity / login is Individual-level — the basis for D6), §3.1
(hierarchy; no level skipping), §4 (Master Admin governance), §5.3
(out-of-band Master Admin process), §5.5 (participation paths / Institution-
under-Institution), §6 (workspace isolation), §7.2 (Command non-rejectable),
§7.4 (FA execution), §7.5 (Task-based progress / denominator), §7.7 (join
flow + Requests & Invitations), §7.7.3 (Institution FA grant), §7.7.4
(Industry never joins), §8.1 (Auth / Notifications / Audit base platform),
§8.2/§8.3 (AMS, Organisation Management / user management), §9 (Pro
Membership data dependency — basis for B14 + soft-delete), §10 (RBAC/PBAC/
ABAC + three isolation levels + Audit), §11 (dependency ordering), §16
(never flatten RBAC/PBAC/ABAC). Cross-doc: `2026-07-25-sub-fa-access.md`
(§D6 Module Admin), `2026-07-20-flow-builder-ideation-v2.md` (§C/§E/§H/§K).
