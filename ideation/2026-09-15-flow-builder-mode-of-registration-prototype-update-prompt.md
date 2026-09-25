# Flow Builder Prototype — Update Prompt (Mode of Registration: Open/Closed)

Context: source of truth is `atom-flow-builder-sports-registration-setup.md`
§36 (2026-09-15 — Mode of Registration revised to a single project-wide
toggle, surfaced at the top of the Registration Setup node; supersedes
§33's original per-leaf placement), plus
`atom-flow-builder-registration-nomination.md` §2.8 (Self-Register method)
and `atom-flow-builder-quota-setup.md` §9 (Open Quota). **This is the one
piece of the Mode of Registration thread that never made it into the built
prototype** — there is currently no Open/Closed control anywhere in Flow
Builder, so Self-Register and Open Quota have nothing to gate on.

This pass touches **three** nodes, because the toggle is project-wide and
everything downstream reacts to it as a whole:

1. **Registration Setup** — add the Open/Closed toggle at the **top of
   the node's canvas**, above the existing 3-pane Sport rail / User Type
   panel / Form Builder drawer. One value for the whole Project — not
   per Sport, not per leaf. Everything else in this node is unchanged.
2. **Quota Setup** — when the Project is Open, every entry's quota
   becomes Open Quota (Max Entries + Selection target, no Level 2)
   instead of the normal Level 1/Level 2 split.
3. **Registration & Nomination** — when the Project is Open, every entry
   gains Self-Register as a third method (alongside Invite and Manual,
   which stay available), and Nomination becomes Selection.

Build all three together — a toggle with nothing reacting to it isn't
useful to demo, and Quota Setup / Registration & Nomination changing
without a visible switch to trigger them will look broken.

---

## Prompt to paste into AI Studio

Add a **project-wide Mode of Registration (Open/Closed) toggle** to the
top of the Registration Setup node, and wire its effects across Quota
Setup and Registration & Nomination. Do not touch Hierarchy, Sports
Setup, or the Registration Dashboard in this pass. The rest of
Registration Setup's own canvas (Sport rail, User Type/Sub-Type panel,
Form Builder drawer) is unchanged — this adds one control, not a
re-architecture.

### Why it's changing

Every project today is implicitly Closed — participants can only be
registered by an Institution (Invite or Manual), never themselves. Some
projects (open marathons, open-entry tournaments) need the opposite:
anyone can find the event and apply directly. This is a single,
project-wide identity, not something that varies sport-by-sport within
one project — either the whole project is Open or the whole project is
Closed. Switching it to Open changes two things everywhere in the
project at once: the quota model (an oversubscribed cap + a selection
cut, instead of top-down Institution allotment) and the registration
methods available (Self-Register added, alongside the existing two).

### 1. Registration Setup — add the toggle at the top of the node

Add a single **Open/Closed segmented control** at the top of the
Registration Setup canvas, above the Sports rail — scoped to the whole
Project, not per Sport, not per User Type, one switch for the whole
node:

```
REGISTRATION SETUP                     Mode of Registration: [Closed] [Open]

SPORTS
▸ Cricket
▸ Swimming
▸ Boxing
```

- **Default: Closed** — must not change behavior for any existing
  project or seed data.
- Everything below it — the Sports rail, the User Type/Sub-Type middle
  panel, the Form Builder drawer — is **unchanged**. This is one
  additional control at the top of the existing canvas, not a new pane
  and not a per-leaf setting.
- Changing it should be a simple, immediate flip in the prototype (no
  confirmation modal needed) — the point of this pass is to demo both
  states, not to model a real publish/lock workflow around switching it.

### 2. Quota Setup — Open Quota everywhere when the Project is Open

**When the Project is Closed:** every Discipline × User Type entry
renders exactly as today — Level 1 (Max/Min/Reserve) and Level 2
(per-Institution Q/R split). No change.

**When the Project is Open:** every entry instead shows an **Open Quota**
row — no Level 2 table at all (there's no Institution to distribute
across):

```
CRICKET — Mens T20 · Athlete           (Registration Setup Mode: Open)

  Max Entries (registration cap)        [100]
  Selection target
    Individual/Double: Max / Min / Reserve   [50] [—] [10]
    Team: Max Team + role breakdown + Reserve  [reuses existing Team template UI]
```

- **Max Entries** is the one new field — a hard registration cap,
  deliberately set above the Selection target (oversubscription is the
  point). Don't validate `Max Entries == Selection total`; only warn
  softly if `Max Entries < Selection total`.
- **Selection target** reuses the existing Level 1 field shapes verbatim
  (Individual: Max/Min/Reserve; Team: Max Team + playing breakdown +
  role-tagged Reserve) — don't build new field types, just render Level
  1's existing component under the Open Quota label.
- Flipping the Project's Mode toggle should swap every entry in Quota
  Setup between these two shapes live (or at minimum, on next visit to
  the node) — this is what makes the toggle's effect visible end to end.

### 3. Registration & Nomination — Self-Register + Selection everywhere when Open

**When the Project is Closed:** unchanged — Invite and Manual, both
Institution-initiated, then per-contingent Nomination (existing
drag-and-drop).

**When the Project is Open:** every entry's registration-method picker
gains a third option, and Nomination is replaced by Selection:

```
REGISTER — Cricket · Athlete           (Registration Setup Mode: Open)

  ○ Invite           Institution invites, Individual self-fills
  ○ Manual / Command Institution registers on Individual's behalf
  ○ Self-Register    Individual finds this project and registers directly
```

- Invite and Manual keep working exactly as today — Open **adds**
  Self-Register, it doesn't remove the other two.
- **Self-Register** skips the "pick an Institution" step — no
  contingent involved. Hard-blocks once the entry's **Max Entries** cap
  (from Quota Setup) is reached; show a simple "Registration full" state
  once blocked.
- **Selection** replaces per-contingent Nomination for Open entries.
  Reuse the existing Nomination drag-and-drop UI exactly (left panel of
  not-yet-placed people, right panel of Main/Reserve zones per
  Discipline, hard-block at each zone's ceiling, draft state until an
  explicit Publish, Publish only enabled once every zone is exactly
  full) — the only difference is the source pool is the single flat
  Self-Register list (plus anyone Invited/Manually registered — they all
  land in the same applicant pool), not one contingent's roster, and
  there's no per-Institution split. For a Team-shaped Open Discipline,
  the right-side boxes are the fixed role-slot template from Quota Setup
  Level 1.
- **Who can run Selection:** Industry always; an Institution only if
  explicitly granted permission (reuse whatever permission-picker
  pattern already exists for Level 1/Level 2 delegation).

### Seed data

Seed one project with the toggle set to **Open** by default so the full
chain is visible on load — Cricket → Athlete with Max Entries 100 and a
Selection target of 50 (Max 40 / Reserve 10), a handful of seeded
Self-Register applicants, and a partially-worked Selection pass (some
names in Main, one in Reserve, rest unplaced) so the drag-and-drop and
Publish-gate states are all visible. Flipping the Registration Setup
toggle back to Closed on that same seeded project should revert Quota
Setup and Registration & Nomination to their existing Closed rendering
— that live flip is the demo, not two separate projects.

### Explicit non-goals for this update

- Don't touch Sports Setup or the Registration Dashboard. In
  Registration Setup, don't touch anything below the new toggle — the
  Sports rail, User Type/Sub-Type panel, and Form Builder drawer stay
  exactly as currently built.
- Don't build per-Sport or per-User-Type overrides of the Mode toggle —
  it is strictly one switch per Project, even though it's surfaced
  inside the Registration Setup node.
- Don't build the discovery surface (how an Individual finds an Open
  project) — assume a hardcoded/seeded link to the Self-Register form.
- Don't build a new account-creation flow for Self-Register — reuse
  whatever sign-up/login stub already exists.
- Don't build early-close before Max Entries is hit — capacity is the
  only stop condition.
- Don't build lottery-based Selection — drag-and-drop manual Selection
  only.
- Don't re-litigate Eligibility gating — it already applies unchanged to
  every registration method, Open or Closed.
