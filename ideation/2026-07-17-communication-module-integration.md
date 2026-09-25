# [2026-07-17] Communication Module Integration into ATOM

> Standalone ideation doc (too large for a README inline entry). Index
> entry lives in `ideation/README.md`. This is exploratory — nothing here
> is committed to `atom-context.md` yet. Section refs (§) point at
> `context/atom-context.md`.

**Status:** Decisions D1–D9 resolved, G1/G2/G4 resolved (G3 deferred) in the
2026-07-20 sign-off session (§I), then the eligibility model and IA were
revised further the same day (§K, the current authoritative flow). Two
things are explicitly **pending Business/Legal approval, not yet final**:
the CMS monetization split (§J), and the dropped minor-safeguarding
messaging restriction (§K, flagged risk). 2026-07-25 addendum (§L):
Grievance Management System's nav placement and CMS integration decided
— GMS stays under the Project menu, not CMS. §L's escalation-matrix-owner
open question is now **resolved** by the Sub-FA / Module-Admin model
(`2026-07-25-sub-fa-access.md`): the Grievance Module's own Module Admin
owns the FA/Sub-FA escalation matrix. Not yet propagated into
`atom-context.md` (§F).

---

## A. What's being integrated

An externally-built **Communication Module** (a §8.2 Project Module,
§8.3: "notifications, email, SMS, WhatsApp, forums, announcements,
chat"; independently developed then integrated per §8.4 pattern). It
ships three surfaces:

1. **Dashboard** — analytics cards (Total Forums, Active Chats, …) + a
   "Top 10 Chats & Forums" list.
2. **Forums** — *Manage Forums* (joined forums, open a forum, create
   posts with polls/images) and *Explore Forum* (browse existing
   forums; Individuals or Institutions can join).
3. **Messages** — WhatsApp-style 1:1 and group chat. **No media
   attachments, no reply-to threading** in the current build (known
   limitations).

It lands against an ATOM fact the user surfaced: **Inbox is already a
Home-level left-nav item**, accessible outside any Project context.

---

## B. The core architectural reframe

The apparent contradiction — "Inbox is Home-level" vs "you may only
talk to people who share a Project with you" — dissolves once we
separate two things ATOM already keeps separate elsewhere (§3.2's
three-layer split):

| Concern | Answer for Communication |
|---|---|
| **Surface / entry point** (where you access it) | Home-level, project-agnostic — one Inbox that aggregates everything |
| **Eligibility / who you can reach** (access model) | Derived from project relationships, enforced by RBAC/PBAC/ABAC (§10) |

Inbox is a **project-agnostic aggregator populated by project-derived
eligibility edges** — the phone/WhatsApp model: the app is global, the
contact list is earned through real relationships. There is no need for
Inbox itself to be "inside" a Project; it just needs its *contents* to
be gated by project-derived eligibility. **The two were never actually
in conflict — they answer different questions.**

### B.1 The workspace-isolation catch (important)

"Home level" is ambiguous, and one reading breaks §10. The left nav is
always rendered *inside the active workspace* (the Role Switcher picks
it, §2, §6). So "Home level" must mean **the top of the current
workspace's nav, above its Project list — not above the workspace
boundary.**

If Inbox were a single cross-workspace inbox spanning all a user's hats
(Individual + Institution admin + Industry), a message received as an
Institution admin would surface in the Individual context — a direct
**§10 workspace-isolation violation ("no data leakage between
workspaces")**. So the recommended rule: **Inbox is Home-level *within*
a workspace, one Inbox per workspace context, not one global inbox per
human.** My Individual Inbox and my Institution's Inbox are different
Inboxes reached via the Role Switcher. (This is decision D1.)

---

## C. Proposed integration flow

### C.1 Mapping the three surfaces onto ATOM

**Messages (DM / group chat)** — the workspace-scoped Inbox.
- Participants are **actors**: Individual, Institution (as a shared
  team inbox — see C.4), and Industry (owner, governance/announcement
  role). Master Admin is platform-level and out of peer chat.
- A DM/group is an object that lives above Projects but whose
  membership was *authorized* by a project-derived edge (C.2).

**Forums** — community/broadcast spaces owned at a scope in the
hierarchy (§3.1), **not** free-floating global boards in v1:
- A forum is **owned by an Industry** (optionally bound to a specific
  Project). *Explore Forum* surfaces only forums the actor is
  **eligible** to see (its Industries/Projects), preserving tenant
  isolation. A truly public, cross-tenant community forum is a much
  bigger product bet (crosses Industry isolation, §10) and should be a
  **separate later decision**, not smuggled into v1 (decision D5).
- Forums already support polls/images (unlike Messages) — note this
  media asymmetry (§E).

**Dashboard** — analytics cards + "Top 10" are an **analytics surface**,
and Analytics is Phase 2 (§14). Recommendation: **defer the analytics
Dashboard to Phase 2**, or scope it to Industry/Master Admin only. For
v1 the Inbox landing is a simple personal "recent chats + joined
forums" view, not the ranked-analytics Dashboard (decision D7).

### C.2 The eligibility edge (who can reach whom)

An edge authorizing communication is created when two actors **share an
active Project** — but *not* the naive "anyone in a 5,000-person
Project can DM anyone." That is a spam/safeguarding hazard (youth
athletes, federation duty-of-care). Per §16 ("never flatten to a single
check"), eligibility layers:

- **RBAC** — the capability to message/forum at all (`Communication:
  Message`, `Communication: Announce`, `Communication: Create/Moderate
  Forum`). Not granted to participant User Types like Athletes by
  default (mirrors the §10 budget caution).
- **PBAC** — policy, e.g. "Athletes cannot initiate DMs to other
  Athletes"; "Industry may broadcast to its participants."
- **ABAC** — attributes: same **Functional Area / Sub FA** (your working
  unit — the natural team-chat boundary, §7.4); minor-athlete flag
  restricting who may initiate (§D4).
- **Isolation** — Project + FA isolation (§10) still scope *which*
  edges exist at all.

Recommended v1 default eligibility set (decision D6):
1. **Same FA/Sub FA** co-members (team chat).
2. **Industry ↔ its own participants** (governance + announcements;
   Industry is never a *peer* but is a legitimate messager, cf.
   §7.7.4).
3. **Admin/Command-initiated** channels (an Industry can open a
   channel; aligns with the Command instrument, §7.2).
- Deferred: "same Industry without a shared Project," "mutual
  connection / follow," cross-Institution same-FA free-for-all.

### C.3 Preconditions — start a DM vs join a forum

- **Start a DM:** an **active shared eligibility edge at time of first
  contact** (both are current participants of a common **non-archived**
  Project *and* satisfy the C.2 intra-project rule). **Checked once, at
  thread creation — not continuously** (this is what makes C.5
  persistence coherent).
- **Join a forum:** the forum is **eligible/visible** to the actor
  (surfaced by Explore) and its **owning context is active**.
  Individuals and Institutions may join; Industry owns/creates.

### C.4 Institution vs Individual asymmetry

- An **Institution is a workspace**, potentially multi-member. Its
  Inbox is therefore a **shared team mailbox**: which members may read/
  send on the Institution's behalf is RBAC-gated within the Institution
  workspace. An **Individual's** Inbox is **personal**. This asymmetry
  must be explicit in the model — an Institution "sending a DM" is the
  entity speaking, operated by an authorized member.
- **Forums:** an Institution joins *as the entity*; posting on its
  behalf is RBAC-gated to members.
- **Industry:** not a chat peer (§7.7.4) but the owner — gets
  **Announcements** (broadcast) and Industry↔participant messaging, not
  symmetric peer DMs.

### C.5 Project-end persistence — my position (the flagship call)

§7.1 lifecycle ends at Completed → Cancelled/Archived. What happens to
channels created by a now-ended Project?

**Recommended position (argued, not a menu):**

- **DMs persist.** Eligibility is checked once at first contact (C.3);
  the thread then **survives the Project that created it** and stays
  usable. *Why:* (a) real relationships don't evaporate when a
  tournament ends; (b) destroying history is hostile and kills the
  audit trail (§10); (c) it aligns with ATOM's own committed direction
  — the Pro Membership thesis (§9) is explicitly about value that
  **outlives individual Projects** ("cross-Project performance
  history"). Revoking chat on archive fights the roadmap.
- **Forums follow their owning context.** A **Project-bound forum goes
  read-only/archived** when its Project is Archived (history retained,
  new posts disabled) — the forum was a facet of that time-bound event.
  An **Industry-level forum** (not bound to a single Project) persists
  normally.
- **Governance guardrail (non-negotiable).** The owning **Industry** and
  **Master Admin** retain the ability to **revoke/freeze** channels and
  forums under their tenancy (abuse, safeguarding), every action logged
  to **Audit (§10)**. Persistence is a default, not an inalienable
  right.

Why not "revoke everything on project-end"? It's simpler but wrong: it
throws away legitimate relationships and history, contradicts the §9
cross-Project thesis, and provides no real safety benefit that the
governance guardrail + safeguarding rule (D4) don't provide more
surgically.

Why not "everything persists forever, no guardrail"? Safeguarding.
Sports platforms carry duty-of-care for minor athletes; an indefinite
coach↔minor channel created by a one-off trial, with the Industry
having lost oversight, is a liability. Hence the D4 stricter default.

**This is a genuine product judgment call, not derivable from existing
docs — it needs explicit sign-off (D3).**

**DECIDED (2026-07-20):** adopted as stated, with the licensing/scope
model reframed under D9/§I — see §I for the full split (persistence
rules above are unchanged; what changed is *which module tier* Chat vs
Forums sit in).

### C.6 The billing/base-platform tension (also flagship)

§8.1 lists **Notifications** as *base platform*; §8.2 lists
**Communication** as an *assignable Project Module* — and module
assignment is a **billing event** (§9) gated by Master Admin +
dependencies (§11). But a **Home-level Inbox for everyone** implies
chat is base-platform, available regardless of what any Industry
licensed. These can't both be true unqualified.

Options (decision D9):
- **(a) Split it:** the DM/Inbox *shell* is base-platform presence
  (like Notifications); **Forums + Announcements + richer Communication
  are the paid §8.2 module**, and **eligibility edges only exist for
  Projects whose Industry has licensed Communication.** Recommended —
  keeps the Home-level Inbox real while preserving §9 billing.
- **(b) All-module:** Inbox only appears/populates when at least one of
  your Industries has Communication enabled. Cleaner billing, but then
  Inbox isn't truly universal at Home level.

I lean **(a)**. Either way this must be decided before build, because it
changes what "Home-level" even means.

---

## D. Decisions needing your explicit sign-off

**D1 — Inbox scope.** Home-level **within each workspace** (one Inbox
per workspace context, Role-Switcher-separated), *not* one cross-
workspace inbox per human — to preserve §10 workspace isolation.
*Recommend: confirm.* **DECIDED (2026-07-20): confirmed as stated.**

**D2 — Eligibility narrowing.** Intra-project eligibility is **not**
"anyone in the Project" but **same FA/Sub FA + Industry↔participant +
admin-initiated** (C.2), layered through RBAC/PBAC/ABAC.
*Recommend: confirm.* **DECIDED (2026-07-20): confirmed as stated.**
Same-FA/Sub-FA is the default *ceiling*, not a blanket grant: RBAC gates
whether a Role has `Communication: Message` at all (participant User
Types don't get it by default), PBAC lets an Industry dial the ceiling
down further (e.g. disable Athlete↔Athlete peer DMs for a youth
league — feeds D4), and ABAC (same FA/Sub FA, minor-flag) is the actual
match rule within whatever RBAC/PBAC allow.

**D3 — Project-end persistence (flagship).** DMs persist (eligibility
checked once at first contact); Project-bound Forums go read-only on
archive; Industry-level Forums persist; Industry/Master Admin retain
revoke/freeze with Audit. *Recommend: adopt as stated — needs your
call.*

**D4 — Safeguarding stricter default for minors.** Channels involving a
minor-athlete User Type get a tighter rule (e.g. auto-freeze on
project-end unless explicitly retained; restricted initiation).
*Recommend: yes — confirm scope.* **DECIDED (2026-07-20): confirmed as
stated.**

**D5 — Forum scope.** v1 forums are **Industry/Project-owned and
tenant-scoped**; **no cross-tenant public community forum in v1**
(separate later bet). *Recommend: confirm v1 exclusion.* **DECIDED
(2026-07-20): confirmed as stated.**

**D6 — Eligibility sources in v1.** Shared-Project (via C.2) +
Industry↔participant + admin/Command-initiated. Defer same-Industry-
without-shared-Project, mutual-connection/follow, cross-Institution
same-FA. *Recommend: confirm the v1 set.* **DECIDED (2026-07-20):
confirmed as stated** (same rule set as D2).

**D7 — Dashboard.** Defer the analytics Dashboard ("Top 10", cards) to
**Phase 2 analytics** (or scope to Industry/Master Admin); v1 Inbox
landing is a simple personal recents view. *Recommend: defer.*
**DECIDED (2026-07-20): defer to Phase 2, as stated.**

**D8 — Known module gaps = fast-follows, not blockers.** Ship v1
without media attachments and without reply-threading in Messages; both
go to backlog. *Recommend: accept (see §E).* **DECIDED (2026-07-20):
accepted as stated.**

**D9 — Base-platform vs paid-module split.** ~~Adopt option (a):
DM/Inbox shell is base-platform; Forums/Announcements are the paid §8.2
Communication module; eligibility edges exist only for Communication-
licensed Industries' Projects.~~ *Recommend: confirm (a) vs (b).*
**SUPERSEDED (2026-07-20) — see §I.** The user's actual call is a
sharper version of option (a): **Chat/DM/Inbox is common to ATOM, free,
not a separately-purchasable module at all** (stronger than "base
platform presence" — there's no licensing gate on it whatsoever).
**Forums + Blogs become a new paid module, "CMS"** (Content/Community
Management), bought the same way an Industry buys AMS/TMS today — not
a renamed §8.2 "Communication" module, a distinct one. Eligibility
edges for Chat exist regardless of any module license (origination is
still Project/FA-scoped per D2/H.1, just not billing-gated). Where
**Announcements** lands (base Chat tier or paid CMS tier) was **not**
decided this session — flagged as a new open question in §G.

---

## E. The two known Communication gaps — assessment

- **No media in Messages** and **no reply-threading in Messages** are
  limitations of *Messages only* — **Forums already support polls/
  images.**
- **Neither is a v1 integration blocker.** Text-only 1:1/group chat is
  genuinely useful; ATOM's differentiators here are *who can talk to
  whom* and *governance*, not attachment richness.
- **Both are legitimate fast-follows** (→ backlog):
  - **Media in Messages** ranks higher for sports — coaches sharing
    technique photos/video clips is a core use case, and the **Grievance
    module already assumes media attachments (§8.3)**, so a media-
    capable Communication substrate has downstream pull. Also resolves
    the **Messages-vs-Forums media asymmetry** (Forums have images,
    Messages don't).
  - **Reply-threading** matters most for **group chat** clarity; low
    priority for 1:1.

---

## F. Downstream doc impact (when this is committed — not editing now)

- **§7.8 Module-Surfaced Services** — does Communication generate a
  participant-facing "Service" (Forums/Announcements) the way AMS→
  Training Programs? Likely yes; needs a line.
- **§8.3** — expand the Communication entry with the ATOM eligibility/
  persistence model.
- **§10** — new isolation nuance: eligibility edges + the workspace-
  scoped Inbox rule (D1); Audit for revoke/freeze.
- **§11** — Communication as a dependency-gated module (D9); does
  anything depend on it (Grievance media)?
- **§14 Roadmap** — v1 Communication (Phase 1/1.5?), analytics Dashboard
  + media + threading later.
- **§15 Glossary** — Inbox, Forum, Eligibility Edge, Announcement.
- **RBAC matrix backlog items (2026-07-09)** — Communication is exactly
  the §8.2 module those items say the matrix must cover; wire in
  `Communication:*` permissions there.

---

## H. RBAC for Comms actions, and how it resolves D3 + D9 (2026-07-18 addendum)

**The question that forced this:** should Comms actions (message, create
forum, moderate, announce) be RBAC-gated at all — and if RBAC is
Project-scoped (§7.7.2: "Role — maps to permissions on the ATOM
Project"), doesn't that mean permission dies when the Project ends,
which contradicts a "global" Inbox? Answer below. This is not a new
option to choose from — it's the resolution mechanism, and it makes D3
and D9 corollaries rather than separate judgment calls.

### H.1 The fix: RBAC gates the *act of creating*, not the *lifetime of
what was created*

The paradox only exists if you assume one permission check governs a
thread/forum from birth to death. Split it into two distinct moments,
which the existing docs already model this way elsewhere (Budget §10:
"RBAC alone must never be the whole check" — capability vs. scope are
separate layers; Progress §12: an object can be visible with **no**
live RBAC check at all, piggybacking on page access):

- **Origination (RBAC-gated, Project-scoped, checked once):** "Can I,
  under my current Role in this active Project, start a new DM thread /
  create a forum / post an announcement?" This is exactly a §7.7.2
  Project Role permission (`Communication: Message`,
  `Communication: Create Forum`, `Communication: Moderate Forum`,
  `Communication: Announce`) — scoped to, and only exercisable from, a
  **live** Project context. This is what C.3 already said ("checked
  once, at thread creation — not continuously") — the fix is simply to
  apply the *same* checked-once rule to the RBAC capability check, not
  just the eligibility-edge check. They're the same moment.
- **Persistence (not RBAC — object state, D3's rules):** once a thread/
  forum/post exists, it is an independent object carrying its own
  history. Reading it and replying to it are **not** re-authorized
  against the live Project Role on every message — if they were, the
  Project archiving would silently break a conversation mid-thread,
  which is bad UX and re-introduces exactly the contradiction you're
  pointing at. Continuing an existing thread is exempt from re-checking
  origination RBAC, same logic as continuing to own a phone number
  after changing jobs.

**Consequence:** a Project archiving doesn't need to "revoke" anything.
It simply removes the live surface (the active Project workspace) from
which *new* origination actions could be taken. Nothing was creatable
there anymore anyway — there's no live Role context to originate from.
Existing threads persist in the Home-level/workspace Inbox (D1)
untouched, because the Inbox was never the place permissions were
granted; it's just where already-created objects are read. This is
also why D9's split holds up structurally: the Inbox *shell*
(view/reply to what already exists) can be base-platform, while
*origination* (new threads, new forums, announcements) stays gated
behind the paid module's RBAC inside a live Project — the module
license is checked at creation time as part of the same one-time gate,
not on every open of the Inbox.

### H.2 Where each Comms permission should be scoped (Industry template
vs Project instance)

Not all four actions belong at the same scope — and this Comms design
is a concrete forcing function for the still-open **2026-07-09 backlog
item** ("Define role-scoping level for RBAC roles: Project vs Industry
template"). Recommend resolving that item as **Industry-level role
templates inherited by Projects** (its own recommendation) partly
*because* of Comms:

| Permission | Natural owner | Recommended scope |
|---|---|---|
| `Communication: Message` (start a DM) | Whoever the Project Role belongs to | **Project-instance** — DM eligibility is inherently derived from a specific shared Project/FA relationship (§C.2), so this stays per-Project even if roles move to Industry templates. |
| `Communication: Create Forum` | Industry (forums are Industry/Project-owned, §C "Forums") | ~~Industry-level.~~ **SUPERSEDED (2026-07-20, resolves G2): default-on for all Institutions**, not delegable-only. This flips the polarity from an allow-list (Industry grants it per Institution Role) to an opt-out model — the Industry needs a way to *revoke* Create Forum from a specific troublesome Institution rather than a way to grant it. Implies a PBAC-level "forum creation disabled" flag per Institution, not just an RBAC capability toggle. |
| `Communication: Moderate Forum` | Forum owner or delegate | **Industry-level**, delegable to a specific Institution/Individual per forum. |
| `Communication: Announce` | Industry only (never Institution/Individual — §C.4, Industry is not a peer) | **Industry-level.** |

If the Industry-template backlog item stays unresolved, all four
default to per-Project (current §7.7.2 model) — workable, just
duplicative for repeat participants (recurring volunteers/athletes
across Event Projects), which is the same pain the 2026-07-09 item
already names.

### H.3 One open sub-question this raises

**Does the RBAC check for "reply to an existing thread" ever need to
re-fire?** Recommend: no, with one exception — a **global** capability
revocation (the actor is banned/suspended platform-wide) should still
freeze reply capability even on old threads. **Note (2026-07-20):** the
original second trigger here — "the Communication module is un-licensed
entirely for the Industry" — no longer applies to Chat/DM threads after
D9's supersession (§I), since Chat has no license to revoke; it still
applies to CMS-side objects (Forums), where un-licensing CMS should
freeze forum posting/moderation the same way. That's a
platform-level kill switch, not a per-Project RBAC re-check, so it
doesn't reopen the paradox — it's the same Industry/Master Admin
governance-freeze lever already proposed in D3.

**Net effect on D3/D9:** both become easier, not harder, to decide —
D3's "DMs persist" and D9's "Inbox shell is base, origination is
module-gated" are now the direct consequence of the origination/
persistence split in H.1, not two independent bets. Recommend deciding
D3 and D9 together, adopting H.1 as the mechanism; H.2's scope table is
a secondary decision that can piggyback on the existing 2026-07-09
backlog item once you resolve it either way.

---

## G. Open questions I could not resolve from the docs

1. ~~**Group chat membership rules**~~ **RESOLVED (2026-07-20).** Groups
   relax the *membership* boundary from FA to **Project-level** (DMs
   stay FA-scoped per D2) — any current, active participant of the same
   Project is a valid group member regardless of FA, provided they hold
   `Communication: Message`. **Creation is open to anyone with that same
   Message capability** (no elevated capability required — user
   overrode the initial "gated creation" proposal). Adding a member
   re-checks the same Project-membership rule on the person being
   added. Cross-*Project* groups remain disallowed — this only relaxes
   FA isolation, not Project isolation.
2. ~~**Forum creation rights**~~ **RESOLVED (2026-07-20) — see §H.2.**
   Institutions can create forums, **default-on for all Institutions**
   (not Industry-delegated, not Industry-only). Superseded the original
   §H.2 recommendation.
3. **Announcement reach** — Project-wide vs FA-scoped; can an Institution
   announce to its FA members? **Explicitly deferred (2026-07-20)** —
   user flagged this as out of scope for now, not a "confirm the
   default" answer. Still open.
4. ~~**Does the Home-level Inbox exist for the Industry workspace too**~~
   **RESOLVED (2026-07-20): yes**, at minimum for Announcements and
   Industry↔participant messaging (§C.4).
5. **NEW (2026-07-20) — Which module tier does Announce belong to?**
   D9's supersession (Chat free / CMS paid = Forums+Blogs) never placed
   Announcements on either side. Original §8.3 module text bundles
   "announcements" with "notifications, email, SMS, WhatsApp, forums,
   chat" under one "Communication" module — that bundling no longer
   matches the Chat-vs-CMS split and needs an explicit call before §F
   is propagated into `atom-context.md`.

---

## I. Session decisions log (2026-07-20)

All nine §D decisions and three of four §G open questions were resolved
in this session. Two resolutions **supersede** text written earlier in
this doc rather than just confirming it — both are marked inline at
their original location, and repeated here for a single point of
reference:

**1. Module split reframed (supersedes D9, and the "Communication
module" framing used throughout §A–§H).**
- **Chat / DM / Inbox is common to ATOM** — free, base-platform, *not*
  a licensable module at all. No Industry billing gate on it.
- **A new paid module, "CMS"** (Content/Community Management) covers
  **Forums + Blogs**, purchased the same way an Industry buys AMS/TMS
  today (§8.4 pattern). This is a distinct module from "Communication,"
  not a rename — Blogs is new scope the original three source-module
  surfaces (Dashboard/Forums/Messages) never included.
- Origination scoping (Project/FA-derived eligibility, checked once)
  and persistence rules (D3) are **unchanged** by this — what changed
  is which tier each surface's *billing* sits in, not how eligibility
  or persistence work.
- **Left open:** where Announcements sits (base or CMS) — G3/G5 above.

**2. `Create Forum` flips to default-on (supersedes the §H.2 table
recommendation, resolves G2).**
- Every Institution gets `Create Forum` by default, no per-Institution
  Industry grant required.
- Implication not in the original doc: this needs a **revoke**
  mechanism (PBAC-level "forum creation disabled" flag per Institution)
  since the model is now opt-out, not opt-in — flag for the eventual
  RBAC-matrix backlog item (§F).

**3. Full decision ledger:**

| # | Topic | Outcome |
|---|---|---|
| D1 | Inbox scope | Confirmed as stated (per-workspace) |
| D2 | Eligibility narrowing | Confirmed as stated, RBAC/PBAC/ABAC layering explained |
| D3 | Project-end persistence | Adopted as stated (licensing model reframed under #1 above, persistence itself unchanged) |
| D4 | Safeguarding default for minors | Confirmed as stated |
| D5 | Forum scope (no cross-tenant v1) | Confirmed as stated |
| D6 | Eligibility sources v1 set | Confirmed as stated (= D2) |
| D7 | Dashboard | Deferred to Phase 2, as stated |
| D8 | Known module gaps | Accepted as fast-follows, as stated |
| D9 | Base-platform vs paid split | **Superseded** — see #1 above |
| G1 | Group chat membership | Resolved — Project-scoped membership, open creation |
| G2 | Forum creation rights | Resolved — default-on for all Institutions |
| G3 | Announcement reach | **Deferred**, not decided |
| G4 | Industry-workspace Inbox | Resolved — yes |
| G5 (new) | Announce's module tier | **Open** — raised by this session, not yet decided |

**Not yet done:** propagating any of this into `atom-context.md` (§F
lists the downstream sections — §7.8, §8.3, §10, §11, §14, §15, plus
the 2026-07-09 RBAC matrix backlog item). This doc is the record of
what was decided; committing it to the canonical doc is a separate
step, and G3/G5 should probably be closed first since they affect the
same §8.3 module text §F would edit.

---

## J. Scope gate + final integration flow (2026-07-20, second session)

**Business approval gate.** The CMS monetization split from §I item 1
(Chat free / Forums+Blogs paid) is **not decided — it's a pitch to the
Business team**, pending their sign-off before it's locked into billing
or the §11 dependency system. Product/technical design does **not**
block on that approval: eligibility, origination, and persistence logic
is identical regardless of which side of a paid gate Forum ends up on,
so it's built as a **license-agnostic flag check on top**, not baked
into the eligibility model. Engineering should not hardcode "Forum
requires a license" or "Forum is free" anywhere in the eligibility/
origination logic — that switch stays external.

**Current build scope: Chat + Forum only.** Blogs is **paused
entirely** — not merely "pending its billing tier" — until CMS approval
happens and Blogs scope is separately confirmed. Dashboard stays
deferred to Phase 2 (D7, unaffected by this).

### J.1 Final integration flow

**1. Base presence (every workspace, licensing-independent).**
One Home-level Inbox per workspace (Individual / Institution / Industry
— D1, G4), reached via the Role Switcher. It aggregates the DM/Group
threads the user is party to, plus their joined Forums.

**2. Eligibility-edge engine (governs both Chat and Forum access).**
Layered, never flattened (§10):
- **RBAC** — Role must carry `Communication: Message`. Off by default
  for plain participant User Types; Industry grants it per Role.
- **PBAC** — Industry can narrow further (e.g. disable Athlete↔Athlete
  peer DMs for a youth program) — feeds D4.
- **ABAC** — same FA/Sub-FA is the default DM match; minor-flag
  triggers the D4 stricter default.
- **Isolation** — Project + FA isolation bound which edges can
  structurally exist at all.
- v1 eligibility sources (D2/D6): same FA/Sub-FA co-membership,
  Industry↔its-own-participants, admin/Command-initiated.

**3. Starting a DM.** Precondition: an active eligibility edge at time
of first contact, both current participants of a non-archived Project.
Checked **once**, at thread creation — not on every message (H.1).
After creation the thread lives at the workspace Inbox level,
independent of the originating Project's lifecycle going forward.

**4. Group chat (G1).** Membership relaxes from FA-level to
**Project-level**: any current active participant of the same Project,
any FA, with `Communication: Message`, is a valid member. **Creation is
open to anyone with that same capability** — no elevated capability
required. Adding a member re-checks the same Project-membership rule.
Cross-Project groups stay disallowed.

**5. Institution / Individual / Industry asymmetry (C.4).** Individual
Inbox is personal. Institution Inbox is a shared team mailbox —
who may read/send "as the Institution" is RBAC-gated within that
Institution's workspace. Industry Inbox exists (G4) for Announcements
and Industry↔participant messaging; Industry is never a DM peer.

**6. Forums.** Owned by Industry (default, optionally Project-bound) or
created by an Institution — **`Create Forum` is default-on for every
Institution** (G2, supersedes the original §H.2 recommendation), an
opt-out model that still needs a revoke lever (PBAC flag per
Institution) before it's safe to ship — **flagged as a build
dependency, not yet designed.** *Explore Forum* surfaces only forums
the actor is eligible to see (own Industries/Projects — tenant
isolation, D5: no cross-tenant public forum in v1). Forums already
support polls/images.

**7. Project-end persistence (D3, unchanged by the billing reframe).**
DMs and Project-scoped Groups persist past the originating Project's
archive (workspace-level object, not Project-level — H.1). Project-
bound Forums go read-only/archived (history retained, no new posts).
Industry-level Forums persist normally. **Overriding lever, always
available:** Industry/Master Admin can revoke/freeze any channel or
forum under their tenancy at any time, logged to Audit. Minor-involved
channels get the D4 stricter default (e.g. auto-freeze on project-end
unless explicitly retained).

**8. Common/free tier, pending the Business gate above.** Chat/DM/
Inbox — points 1–5 and 7 — is the proposed always-on, license-free
base-platform layer. Forum (point 6) ships now on the same technical
footing but its licensing boundary is the open Business question.
Media attachments and reply-threading in Messages stay backlog
fast-follows (D8); Announcements' tier placement stays open (G3/G5).

### J.2 Direct answers

**1. Who can connect/message in the Messages page?**
Two actors can **DM** each other only if both (a) hold
`Communication: Message` on their current Role, and (b) satisfy one of:
same FA/Sub-FA co-membership in an active Project, an
Industry↔participant relationship, or an admin/Command-opened channel.
Checked once, at first contact. For **Groups**, membership widens to
"any current participant of the same Project, any FA" with the same
capability — creation is open to anyone holding it, no extra gate.
Institutions message "as the entity" through RBAC-authorized members;
Industry is not a peer, only a governance/broadcast participant. Minors
get the stricter D4 default on initiation.

**2. Who can create and join Forums?**
**Create:** the owning Industry by default, and — per G2 — every
Institution by default (opt-out, not opt-in; the revoke lever isn't
built yet). **Join:** any Individual or Institution the forum is
visible to via Explore Forum, scoped to their own Industries/Projects —
no cross-tenant public forums in v1 (D5). A forum can be Project-bound
or exist at Industry level.

**3. What happens after a Project ends?**
DMs and Project-scoped Groups are untouched — they live at the
workspace Inbox, not the Project. Project-bound Forums go read-only/
archived (history kept, posting disabled). Industry-level Forums are
unaffected. At any point, Industry/Master Admin can revoke or freeze
regardless of these defaults, logged to Audit. Minor-involved channels
get the stricter D4 default (may auto-freeze unless explicitly
retained).

**4. What's in the common (free) CMS tier, and how does it work?**
Proposed common tier = Chat/DM/Inbox only: 1:1 DM, Project-scoped Group
chat, one Inbox per workspace, the RBAC/PBAC/ABAC eligibility engine
gating who can originate a thread, persistence of history past
Project-end, and the Industry/Master Admin governance override. It
excludes (for now): Forum's final licensing side (Business-pending),
Blogs (paused entirely), Dashboard (Phase 2), media/reply-threading in
Messages (backlog), and Announcements' tier (open). Mechanically it
works via the **origination/persistence split (H.1)**: starting a
thread is RBAC/PBAC/ABAC-gated and Project/FA-scoped, checked once;
continuing to read/reply is an object-state property, never re-gated
against a live Project Role — that split is what lets an always-free
Chat coexist with Project-derived eligibility without threads breaking
when a Project archives.

**Superseded the same day — see §K.** §J.1 point 2 (the layered
RBAC/PBAC/ABAC eligibility engine, same-FA default) and §J.2 answer 1
(who can message) no longer reflect the model — the user replaced the
FA-scoped, RBAC-gated design with a flat Industry-wide rule for
Individual↔Individual messaging. Everything else in §J (Forums, Project-
end persistence, the Business approval gate, Blogs pause) still stands.

---

## K. Eligibility model V2 + navigation (2026-07-20, third revision) —
**current authoritative flow**

### K.1 What changed from §J

**Messaging eligibility drops FA-scoping and the Industry-configured
RBAC gate entirely.** New rule: **any Individual who currently has at
least one active (non-archived) Project under a given Industry can
message, or start a group with, any other Individual who currently has
at least one active Project under that same Industry.** This is not
Industry-admin-configurable — it is not a Role permission the Industry
grants or withholds; it is a platform-default available to every
Individual by virtue of active Industry membership. This **replaces**
D2/D6's same-FA/Sub-FA default and the RBAC-capability gate described in
§J.1 point 2.

- **"Active" is required for *establishing* eligibility, not for
  staying connected.** An Individual counts as "belonging to" an
  Industry only while they have ≥1 non-archived Project under it. Once
  a DM/Group thread exists, it **persists even after the Project(s)
  that made both parties eligible archive** — same checked-once-at-
  creation logic as before (H.1), just with a broader trigger condition.
- **Groups follow the same broadened scope** (supersedes G1's
  Project-level membership rule): any Individual currently active under
  the Industry can be added to a group, not just co-participants of one
  specific Project.
- **Industry↔participant and admin/Command-initiated channels (C.2
  rules 2–3) are unaffected** — those describe the Industry acting as
  an entity, a separate case from Individual↔Individual peer messaging.
- **Institution's messaging behavior is explicitly NOT addressed by
  this revision** — the user's instruction was scoped to Individuals.
  Whether Institution-to-Individual or Institution-to-Institution peer
  messaging also broadens to Industry-wide, or stays under the original
  narrower model, is now an **open question (G6, below)**.

**⚠️ Flagged risk — minor safeguarding, needs Legal/Business sign-off.**
D4 (minor-athlete stricter default: restricted initiation, auto-freeze)
is **dropped for messaging** at the user's explicit instruction — a
minor-athlete Individual is now reachable by any other Individual
active under the same Industry, with no extra restriction. The FA-
scoping this session removed was the main structural guardrail against
stranger-contact in large Projects (the "5,000-person marathon" case in
D2's rationale); with both that and D4 gone, the **only remaining
safety net is the reactive Industry/Master Admin revoke/freeze +
Audit lever (§C.5)** — after-the-fact, not preventive. Given this is a
sports platform with minor athletes as a named User Type, recommend
this ships alongside the CMS pitch as an explicit item for Legal/
Business sign-off, not silently — flagging it, not blocking it.

**Forums — reconfirmed, no change from §J.1 point 6:** created by
**Institution or Industry only** (never by an Individual); **Individual
can join** any forum visible to them (Explore Forum, tenant-scoped,
D5); **Project-bound Forums go view-only when their Project ends**
(Industry-level Forums unaffected). All three explicitly reconfirmed by
the user this turn.

### K.2 Navigation/IA — Forums live inside the same global Inbox as
Messages, not under the Project menu

**Decision:** the Home-level Inbox nav entry (D1/G4 — one per
workspace) is a **single surface containing both Messages and Forums**,
with a **Project filter/switcher inside that surface**. Forums are
**not** a separate item nested under each Project's own menu.

**Why:** a Forum's *ownership* can be Project-bound, but that doesn't
mean its *navigation* should be. Nesting Forums under the Project menu
while Messages lives at Home-level would split one mental model
("communication") across two different places in the nav — confusing,
since a user with forums across five Projects would have to hunt
through five separate Project menus instead of seeing them in one
place. Putting both under the same global Inbox, with a Project filter
to narrow either section down, keeps the aggregator model (§B) intact
for Forums too, not just Messages.

**Concretely:**
- Home-level nav: one **Inbox** entry per workspace (unchanged from
  D1/G4).
- Inside Inbox: two sections — **Messages** (DMs + Groups) and
  **Forums** (joined + Explore).
- A **Project filter/switcher control** sits inside the Inbox surface,
  applicable to both sections — e.g. narrowing Forums to "only this
  Project's forums," or Messages to "only threads that originated from
  this Project" (informational only, since Message eligibility itself
  is no longer Project-derived — see K.1).

### K.3 New open question

**G6 — Does Individual↔Individual Industry-wide messaging (K.1) extend
to Institution as a sender?** Not addressed this session. Institution's
shared-mailbox behavior (§C.4) predates this revision and hasn't been
reconciled with it. Needs a decision before build.

### K.4 Direct answers (supersedes §J.2 answer 1; 2–3 unchanged; 4 updated)

**1. Who can connect/message in the Messages page?** Any Individual
with at least one active Project under a given Industry can message, or
start a group with, any other Individual who also currently has at
least one active Project under that same Industry — no FA-scoping, no
Industry-configurable capability gate. Once a thread exists it persists
regardless of either party's Projects later archiving. Separately, the
Industry (as an entity) can still reach its participants directly, and
admins can still open channels directly (C.2 rules 2–3) — those are
unaffected. Institution-as-sender is unresolved (G6).

**2. Who can create and join Forums?** Unchanged from §J.2: **Create**
— Institution or Industry only, never an Individual. **Join** — any
Individual (or Institution) the forum is visible to via Explore Forum,
tenant-scoped (no cross-tenant forums in v1).

**3. What happens after a Project ends?** Unchanged from §J.2 for
Forums: Project-bound Forums go **view-only**; Industry-level Forums
are unaffected. For Messages, this answer actually gets **simpler**
under K.1: since eligibility no longer derives from one specific
Project, a thread has no single "owning" Project to end — it persists
by default regardless, with the Industry/Master Admin revoke/freeze
lever as the only override.

**4. What's in the common (free) CMS tier, and how does it work?**
Unchanged in kind from §J.2 (Chat/DM/Inbox free, Forum's licensing side
pending Business approval, Blogs paused, Dashboard Phase 2) — but the
mechanism for Chat changes: it's no longer "Project/FA-derived
eligibility, checked once" (H.1's original framing) but **"Industry-
active-membership-derived eligibility, checked at first contact,
independent of which Project made you active."** Forums keep the
original H.1 mechanism (Project/Industry-owned, origination-vs-
persistence split) unchanged. Both now live in one navigable surface
(K.2) regardless of which billing tier either ends up on.

---

## L. Grievance Management System — nav placement + CMS integration
(2026-07-25)

**What GMS is (§8.2/§8.3 baseline):** Grievance is its own §8.2 Project
Module — "FA-based ownership, escalation matrix, TAT, media attachments,
notifications" (§8.3). It's raised against a specific FA/Sub-FA; the
Industry (or any user holding the relevant permission) configures that
FA/Sub-FA's escalation matrix; an Individual raising a grievance against
the FA/Sub-FA is what starts escalation running against that matrix.

**Decision — GMS lives under the Project menu (with FA/Task Board), not
the CMS/Inbox nav item.** Rationale: GMS is FA/Sub-FA-scoped by
construction, structurally identical to Task Board's placement in the
Project → FA → Sub-FA → Task Board chain (§3.1). CMS only moved to
Home-level because Chat's eligibility became Industry-wide and
Project-independent (§K.1), and Forums were folded into the same surface
purely for nav consistency (§K.2) even though Forum *ownership* stayed
Project/Industry-scoped underneath. Neither rationale applies to GMS —
nothing about it is cross-Project — so it doesn't inherit CMS's Home-level
treatment.

**Decision — no cross-Project Grievance aggregation.** Explicitly
rejected an Inbox-style "My Grievances" rollup at Home/workspace level.
A grievance is read where it was raised: inside its originating Project's
page, scoped to the FA/Sub-FA it was raised against. This mirrors Forums'
existing archive behavior (§C.5/§J.1: Project-bound Forums go read-only
but stay accessible **in place**, not relocated to an aggregator) rather
than Chat's behavior (which did become a cross-context aggregator once
eligibility went Industry-wide, §K.1) — GMS follows the Forums precedent,
not the Chat one.

**Decision — grievance visibility survives Project archive.** An
Individual can open a Completed/Archived Project's page and still see
their grievance history there, read-only-by-virtue-of-the-Project-being-
archived (not a separate GMS-specific archival rule) — consistent with
how Project-bound Forums persist post-archive (§C.5). No new "GMS
persistence" mechanism needed; it inherits whatever the Project page's
own post-archive access model already is.

**"Integrated with CMS" means shared rails, not shared nav.** GMS
consumes Communication's underlying infrastructure — escalation
notifications, and the media-attachment capability §E already flagged
Grievance as needing ("the Grievance module already assumes media
attachments... a media-capable Communication substrate has downstream
pull") — without moving into CMS's menu or surface. This is the same
origination/persistence-style split established in H.1: a module can
consume another module's infrastructure while keeping its own
origination scope (here, FA/Sub-FA-bound, Project-menu-housed) and its
own nav location.

**Open questions (not resolved this session):**
- Mechanically, does an escalation open an actual CMS thread (e.g. a
  Grievance-scoped group chat between the raiser, the FA owner, and the
  escalation chain), or is "integration" limited to firing notifications
  through CMS's notification rail? Affects whether GMS needs its own
  message-thread object or just calls into existing notification
  plumbing.
- ~~Who besides the Industry can configure an FA/Sub-FA's escalation
  matrix — is "any user with relevant permission" an RBAC capability
  scoped like `Communication: Message` (Project-instance, per §H.2's
  table), or Industry-only?~~ **RESOLVED (2026-07-25) by the Sub-FA /
  Module-Admin model — see `2026-07-25-sub-fa-access.md` (D6).** It is
  the **Grievance Module's own Module Admin** (per-Project-per-Module,
  Industry-granted, Institution-or-Individual holder) who configures and
  owns the FA/Sub-FA escalation matrix and assigns the escalation chain —
  subject to the same **parent-FA-population guardrail** (D5): the Module
  Admin can only place users already in the parent FA's granted
  population onto that FA/Sub-FA's escalation chain, never an unrelated
  user. Not an ATOM-core RBAC permission and not Industry-only — it's the
  Module's own admin axis, consistent with GMS being a §8.2 Module that
  owns its own Sub-FA-level assignment (§8.4 pattern).
- Does GMS's `media attachments` requirement (§8.3) become the forcing
  function that finally prioritizes the media-in-Messages backlog item
  (§E), or does GMS attach media independently of the Messages/Forums
  media model?

**Downstream doc impact when this is committed (not editing now, same
caveat as §F):** §8.3's Grievance entry should note the FA/Sub-FA
escalation trigger explicitly (currently just lists capabilities, not the
raise → escalate mechanism); §7.8 Module-Surfaced Services should note
whether Grievance generates a participant-facing Service the way AMS/TMS
do.
