# ATOM Flow Builder — Flow & Screens Spec

**Purpose of this file:** a screen-by-screen, mockup-ready description of the
Flow Builder module — what's on each screen, what's in the left menu vs. the
canvas, what the components are, and how a user moves through the flow. This
is derived from the resolved design decisions in
`ideation/2026-07-20-flow-builder-ideation-v2.md` (the full reasoning/decision
log — read that if you want the *why*; this file is the *what it looks like*).

Written for handing to a mockup/UI generation tool. Where a screen isn't
fully designed yet, it's marked **[NOT YET DESIGNED]** — don't invent detail
for those, just block them out.

---

## 1. What Flow Builder Is

Flow Builder is an **Industry-workspace configuration tool**. Before a
Project can accept registrations, the Industry uses Flow Builder to define:
who can join and how (Hierarchy), which sports/events are active, what
"types" of participant exist and what data they submit, how many of each
type can join per event/institution (quota), and how those slots get filled
with named individuals (nomination).

It has **two tabs**, opened from the Project's admin area:

1. **Hierarchy**
2. **Registration & Quota Setup**

---

## 2. Tab 1: Hierarchy

**Purpose:** build the Project's participation structure — which
Institutions and Individuals are involved, and how they're organized.

### Layout
A single **visual canvas** (node-based, drag-and-drop), with a **Node
Library** panel to add new nodes.

### Node Library (left panel)
Three node types available to drag onto the canvas:
- **Functional Area (FA)**
- **Institution**
- **Individual**

### Node types on the canvas

**FA node**
- Fields: name, description, **FA Head** (assignee), **Notification
  Recipients** (checkboxes: FA Head / Assigned Institution Admins /
  Assigned Industry Users / Assigned Individuals / Project Managers).
- Shows a count of linked Institutions/Individuals (e.g. "12 institutions
  linked").
- **Can parent both Institution and Individual nodes.** (FA is always the
  parent in that relationship — an Institution/Individual node can never
  parent an FA node.)

**Institution node**
- Fields: ID, "Assign Existing Institution" or "Invite Institution" action,
  Status badge (e.g. "Onboarded", "Invited", "Pending").
- **"Open sub-canvas"** button → opens a nested canvas scoped to that
  Institution, breadcrumbed (e.g. "Hierarchy > ABC Hotels Group"), where its
  own child Institutions/Individuals are added.
- **Sub-canvas nesting is capped at exactly 1 level.** A child inside a
  sub-canvas cannot itself open a further sub-canvas. Anything deeper (a
  3rd+ tier of institutional lineage) is represented as a **flat list of
  Institutions tagged with a `tier` attribute** (e.g. National / State /
  District), not further nesting.

**Individual node**
- Represents a single participant.
- **Role** (Project permission mapping) is assigned in the **invite modal**,
  not shown on the node face.
- **User Type** (see Registration Configuration, §4.2) is also assigned at
  invite/apply time, not authored here.

### Invite modal (opens from an Institution or Individual node)
- For an **Individual**: select **User Type** (+ Sub-Type if applicable) and
  **Role** — two distinct axes shown as separate fields.
- For an **Institution**: select a **Functional Area** to grant, and/or
  whether it also receives a **Quota Grant** (configured later, in Quota
  Setup) — an Institution can hold an FA grant only, a quota grant only,
  both, or neither.
- The canvas must also represent **apply-path** joiners (Individuals/
  Institutions who applied rather than were invited), not just invitees, and
  must support **Command** — a non-rejectable registration mode — as a
  distinct action alongside Invite.

---

## 3. Tab 2: Registration & Quota Setup

**Purpose:** the second tab holds **five fixed nodes**, opened in a forced
sequence. Each node is its own dedicated page/canvas (not an inline panel).

### The five nodes, in order
```
Sports Setup → Registration Configuration → Quota Setup → Nomination → Registration Dashboard
```

### Node mechanics (applies to all five)
- Every node starts in status **Pending**.
- Clicking a node opens its dedicated page.
- Once configured, the Industry clicks **Publish**, flipping status to
  **Published**.
- **Publishing a node is a literal UI gate** — it unlocks the *next* node in
  the sequence. A Pending node blocks everything after it in the chain.
- Show each node's status as a badge on the tab's overview screen (a
  horizontal stepper or node-map: Pending nodes greyed/locked, Published
  nodes checked/green, the current unlockable node highlighted).

---

## 4.1 Sports Setup node

**Purpose:** enable which Sports/Disciplines/Events this Project uses. **Not
an authoring screen** — the Industry only *toggles* things on, against a
taxonomy that already exists in ATOM's backend. No free text anywhere on
this screen.

### Left rail
- List of **enabled Sports**, each showing an enabled-event count, e.g.:
  ```
  Athletics — 8/14 events enabled
  Swimming — 3/10 events enabled
  Boxing — 0 events enabled
  ```
- **"+ Enable Sport"** button → opens a picker over the full backend Sport
  master list (Athletics, Swimming, Boxing, …). Multi-select. No "create new
  Sport" option in v1.

### Main panel (a Sport selected in the rail)
- A **read-only** Discipline/Event tree for that Sport.
- Each **Event** row expands to show:
  - Its **Category variants** as chips (Men / Women / Mixed) — each with its
    own enable toggle.
  - A read-only **Event Type** badge (Individual / Team).
- A **"enable all in this Discipline"** bulk-action shortcut per Discipline
  group.
- Every label on this screen is backend-sourced — no editable text fields.

### Node status
- Pending until at least one Event×Category combination is enabled.
- Publishing unlocks Registration Configuration.

---

## 4.2 Registration Configuration node

**Purpose:** define what "types" of Individual can participate (User Types
and Sub-Types) and what form each fills out. **This is a free-text authoring
screen** — the direct opposite of Sports Setup.

### Left rail
- List of authored **User Types**, each showing:
  - a Sub-Type count (e.g. "2 sub-types"),
  - a form-status chip (e.g. "Coach — no sub-types · form: 6 fields").
- **"+ Add User Type"** → a modal with a single free-text Name field.

### Main panel — depends on what's selected in the rail

**If the selected User Type HAS Sub-Types:**
- Shows a **Sub-Types manager**: list of its Sub-Types + "+ Add Sub-Type"
  (free-text name).
- **No Form Builder appears at the User Type level** — forms only ever live
  at the leaf. (If a User Type has Sub-Types, only its Sub-Types get forms —
  the parent itself has none.)

**If the selected node is a leaf (a Sub-Type, or a User Type with zero
Sub-Types):**
- Shows the **Form Builder**:
  - A **fixed standard-field palette** (locked for v1): **Name, Date of
    Birth, Gender, Contact Number, ID/Aadhaar Number, Photo.** These can be
    dropped onto the form.
  - **"+ Custom Field"** — Industry-authored fields: label, type (`text` /
    `number` / `date` / `select` / `file`), required toggle.
  - Fields listed as a simple vertical form-builder list (drag to reorder is
    a reasonable mockup assumption, not a confirmed decision).
- Validation options on each field are **minimal in v1**: a "Required"
  toggle and the type constraint itself. No regex, no min/max, no
  uniqueness/dedup options shown.

### Node status
- Pending until every leaf (every Sub-Type, and every Sub-Type-less User
  Type) has a non-empty form.
- Publishing unlocks Quota Setup — the set of published User
  Types/Sub-Types becomes the row-source for Quota Setup's left rail.

---

## 4.3 Quota Setup node

**Purpose:** set per-event/sport quota ceilings and allocate slices of that
ceiling to specific Institutions.

### Left rail — Quota Track list
- Auto-populated from the **published** User Types/Sub-Types from
  Registration Configuration (not editable here — read-only list, Sub-Types
  indented one level under their parent).
- Each row has:
  - a **"has a QuotaTrack" toggle** (quota is opt-in per type),
  - when ON: a **granularity selector** (`per Event × Category` / `per
    Sport` / `per Project`),
  - a **completeness chip** (e.g. "Max set on 6/8 leaves", "ceiling: 90/100
    allocated").
- Toggling a parent User Type ON *while* its Sub-Types are also ON puts that
  type into **"both" mode** (see below) — the UI should visually distinguish
  three configurations a User Type can be in:
  1. **Parent-pool** — quota lives on the parent only.
  2. **Per-Sub-Type** — quota lives on each Sub-Type, parent has none.
  3. **Both** — parent is a roll-up ceiling over its Sub-Types' own caps.
  (Only one of these three per User Type — no mixing within one type.)

### Main panel — two sub-tabs, per selected track

**Sub-tab A: "Overall Quota"** (the project-wide ceiling)
- A grid: one row per **granularity-leaf** (e.g. one row per enabled
  Event×Category).
- Standard columns (Industry-editable): **Max, Min, Max Team, Reserve.**
- One read-only column: **Event Type** (Individual/Team), inherited from
  Sports Setup.
- **"+ Custom Field"** — Industry can add extra reference columns beyond the
  four standard ones (same builder pattern as Registration Configuration's
  Form Builder). These render as additional grid columns; treat as
  display/reference only, not enforced.
- If the selected track's User Type is in **"both" mode**, this grid instead
  shows a **Total / Allocated / Unallocated** summary row (Total = parent
  Max, Allocated = Σ of its Sub-Types' Max, Unallocated = remaining
  headroom) above the per-leaf detail.

**Sub-tab B: "Contingent Allocation"** (granting to Institutions)
- Per selected granularity-leaf, a header showing two tri-column summaries:
  ```
  Total: 40   Allocated: 32   Unallocated: 8
  Reserve — Total: 5   Allocated: 3   Unallocated: 2
  ```
- Below it, a list of Institutions (pulled from the Hierarchy tab) eligible
  to receive a grant for this leaf, each row:
  **Institution name · Q (quota) · R (reserve) · Active/Off toggle.**
- **"+ Grant to Institution"** to add a new row.
- Grants are validated live against the ceiling — cannot exceed it.

### Node status
- Pending until: at least one track is enabled, and every enabled track's
  Overall Quota is fully set (every leaf has a Max).
- **No grant has to exist to Publish** — the Industry can publish ceilings
  only and grant to Institutions later. (This means Nomination's rail can
  legitimately be empty right after this node Publishes — that's an
  accepted, valid state, not an error.)
- Publishing unlocks Nomination.

---

## 4.4 Nomination node

**Purpose:** a thin, Industry-side surface for manually filling quota slots
with named individuals, and seeing fill progress. (The full self-service
flow where an Institution's own admin nominates is a separate, downstream
Registration-module concern — out of Flow Builder.)

### Navigation model
This node is **drill-down, not a broad filterable list** (unlike the
Dashboard node). No global filter bar.

**Left rail:** Institutions holding a QuotaGrant ("contingents"), each with
an aggregate fill indicator:
```
Delhi Athletics Assoc. — 34/50 slots filled
Maharashtra Athletics  — 21/40 slots filled
```

**Selecting an Institution** opens its QuotaGrant leaves — one row per
(QuotaTrack × Event × Category) it was actually granted:
```
100m — Men       Q: 12   Confirmed: 9    Reserve: 1
4x100m Relay      Q: 4    Confirmed: 4    Reserve: 0
```

**Selecting a leaf** opens the nomination surface for that specific slot:
- **Search Existing User** (by name / ID) — select and confirm.
- **Manual Registration** — a form to register a new athlete directly.
- A running count against the cap: nomination is **blocked** once
  Confirmed = Q (no auto-waitlist; Reserve is a separate, Industry-set
  ceiling, not auto-filled by overflow).
- Manual nomination by the Industry lands the entry as status **Confirmed**
  immediately — no separate approval step.

### Node status
- Pending until at least one Entry is Confirmed.
- Publish is available as a completion marker but doesn't gate anything
  downstream (Nomination is the last node before the read-only Dashboard).

---

## 4.5 Registration Dashboard node — **[NOT YET DESIGNED]**

**Purpose (known, from earlier decisions — screen layout not yet designed):**
a cross-cutting, filterable read-only view of registration status — built
*inside* Flow Builder (not a separate Reports module), reading live data
from the other four nodes.

**What's confirmed:**
- Lives inside Flow Builder as this 5th node.
- **Is** filterable by Sport / Event / State (unlike Nomination, which is
  drill-down only) — this is the one node in the tab that needs a broad
  filter bar.
- Rows are expected to look like: State × Sport × Event × Gender, with
  Quota, Reserve, and Total-Entries progress (this pattern is carried from
  the original prototype's "Registration Status" screen, but the final
  layout hasn't been walked through/confirmed for v2).
- Reads Quota Setup's OverallQuota/QuotaGrant and Nomination's Entry data —
  it doesn't own any data itself.

**Do not invent canvas/left-rail detail for this node** — block it out as a
filterable table/dashboard placeholder only, pending its design pass.

---

## 5. Cross-cutting design conventions (use consistently across mockups)

- **Pending/Published badge:** every one of the five Tab-2 nodes carries
  this status; render consistently (grey/locked vs. green/checked).
- **Read-only vs. authored contrast:** Sports Setup is 100% read-only labels
  (backend-sourced, toggle-only) — no free text anywhere. Registration
  Configuration is the opposite — free-text names and a form builder. Keep
  this visual contrast sharp (e.g. read-only = plain text/badges;
  authorable = input fields, "+" buttons).
- **Total / Allocated / Unallocated tri-column:** reused in Quota Setup for
  both the Overall Quota roll-up and Contingent Allocation grants — the same
  three-number pattern ATOM already uses in its Budget Tracking feature.
  Keep the visual treatment identical across both places for consistency.
- **Standard-field-palette + "+ Custom Field" pattern:** appears twice
  (Registration Configuration's Form Builder, Quota Setup's Overall Quota
  grid) — reuse the same builder-affordance visual pattern in both places.
- **Left rail = navigation/selection, never free authoring** except in
  Registration Configuration (User Types) and Sports Setup's Sport picker —
  everywhere else (Quota Setup's track list, Nomination's Institution list),
  the left rail is populated from an upstream node, not typed into directly.
