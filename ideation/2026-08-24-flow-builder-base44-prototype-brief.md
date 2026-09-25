# Flow Builder — Prototype Build Brief

---

## 1. What to build

A clickable prototype of ATOM's **Flow Builder** — a project-configuration
tool with two tabs: **Hierarchy** and **Registration & Quota Setup** (a
5-step node sequence). This is a UI/interaction prototype, not a
production app — mock data, no real auth.

## 2. Visual theme — reuse the screenshot's colors/typography, not its layout

Take **colors, fonts, buttons, pills, and card styling** from the
attached screenshot, but Flow Builder's layout is different from the
Project Details page shown there — see §3.

- Brand: "Khelo Tech" logo, top-left.
- Left icon rail: dark purple, icon-only nav (Home, Project, More;
  Settings pinned at the bottom) — keep this, it's app-level chrome.
- Top bar: global search "Search anything... Ctrl+K", user avatar + name
  + role in the top-right (hardcode "Master Admin / ATOM_ADMIN_001").
- Buttons: rounded, solid purple primary, white/outline secondary.
- Status pills, rounded cards, light gray page background — same style
  language as the screenshot.
- **Do not reuse:** the pink → purple gradient project hero/banner card
  (with the project thumbnail, P-0001 code, "Edit Details" button), and
  the white secondary sidebar listing Overview / Project Details /
  Functional Area / Task Board / User Management / Modules / Budget.
  Neither appears anywhere in Flow Builder — see §3.

## 3. App shell / navigation

- Keep the outer dark-purple icon rail (Home/Project/More/Settings) and
  the top bar (search + user) exactly as in the screenshot.
- **Remove** the white secondary Project sidebar entirely (no Overview,
  Project Details, Functional Area, Task Board, User Management,
  Modules, Budget list) and **remove** the pink/purple project hero card.
  Flow Builder's content area is a full-width canvas.
- In their place, put a small **"← Back to Project"** control at the
  top-left of the content area (button or breadcrumb-style link). This
  is the only way back to the Project Details screen from Flow Builder —
  it doesn't need to actually navigate anywhere else in the prototype,
  just be present and clickable.
- Below that: two tabs, **Hierarchy** | **Registration & Quota Setup**.
  Both tabs are full-bleed, pannable **canvases** (Figma-style — nodes,
  connectors/arrows, a docked node palette, and a right-side
  configuration panel that opens on node click), not stepper pages. See
  §4.1 and §4.2 for how each canvas behaves.

## 4. Screen specs

### 4.1 Hierarchy tab — Figma-style canvas + right config panel

**Layout:** a docked **left node palette** (narrow, fixed) + a large
pannable/zoomable **center canvas** + a **right configuration panel**
that slides in when a node is selected. This mirrors Figma's
layers/canvas/inspector pattern.

- **Left palette:** 3 **draggable** node-type chips — **Functional
  Area, Institution, Individual**. Nothing else in this panel.
- **Fixed Project root node:** the canvas always contains one
  non-deletable root node representing the current project (e.g.
  "National Games 2027"), pre-placed near the top/center on load. It
  can't be created, deleted, or dragged from the palette — it's just
  always there as the top of the tree.
- **Drag-to-create:** dragging a chip from the palette onto the canvas
  drops a new, minimal node card there (icon + name placeholder + status
  dot — full detail lives in the right panel, not on the card).
- **Drag-to-connect (core interaction — must work):** dropping an FA
  chip near/onto the Project node auto-connects it (Project → FA arrow).
  Dropping an Institution or Individual chip/card near/onto an FA node
  auto-connects it (FA → Institution or FA → Individual arrow). Dropping
  a card on empty canvas space leaves it unconnected; dragging an
  already-placed Institution/Individual card onto a different FA
  re-parents it (old arrow removed, new one drawn).
- **Click-to-configure (core interaction — must work):** clicking any
  node on the canvas — whether just dropped from the palette or an
  existing node — opens the **right panel** with a form scoped to that
  node type:
  - **Project node** → read-only summary (name, code) — not editable
    here.
  - **FA node** → FA creation/edit form: name, description, FA Head
    field, Notification Recipients (5 checkboxes: FA Head / Assigned
    Institution Admins / Assigned Industry Users / Assigned Individuals
    / Project Managers). A "Create" / "Save" button in the panel writes
    the values back onto the canvas card (name + status update
    immediately).
  - **Institution node** → invitation/details form: Institution name,
    "Assign Existing Institution" (dropdown of seeded institutions) or
    "Invite Institution" (name + email fields), Status ("Onboarded"),
    and an "Open sub-canvas" button.
  - **Individual node** → invitation/details form: name, email, role,
    "Invite" button.
- Institution → Institution nesting is **not** done by dragging one onto
  another on the same canvas — only one level deep, via "Open
  sub-canvas" (from the Institution node's right panel). FA is always
  the parent in any FA edge; Institution/Individual nodes never accept
  a node dropped onto them.
- "Open sub-canvas" drills into a nested canvas (breadcrumb
  `Hierarchy > [Institution Name]`) — same left palette, same
  drag-to-create/drag-to-connect/click-to-configure/right-panel pattern,
  scoped to that Institution's children — but Institution nodes placed
  here have **no "Open sub-canvas" option in their right panel** (max 1
  level deep, enforced by omitting that control at this depth).
- Connectors are simple arrows, auto-drawn/updated on every connect,
  re-parent, or detach — Project → FA → Institution/Individual.
- Seed the canvas with the Project root + 2-3 FA nodes + 4-5
  Institution/Individual nodes already connected, so the tree is visible
  on load, not empty.

### 4.2 Registration & Quota Setup tab — fixed 5-node canvas

- The main canvas for this tab always shows **5 fixed nodes, laid out in
  the middle of the canvas, connected in sequence by arrows**: **Sports
  Setup → Registration Setup → Quota Setup → Registration & Nomination →
  Registration Dashboard**. These 5 nodes can't be added to, renamed, or
  removed — they're permanent, unlike Hierarchy's freeform nodes.
- Each node card shows its name and a status pill (**Pending /
  Published**).
- **Clicking a node opens that node's own dedicated setup canvas**
  (a separate full screen, specs in §4.4–§4.8 below), with a "← Back to
  Registration & Quota Setup" control to return to this 5-node overview.
  Publishing inside a node's canvas updates that node's status pill back
  on this overview canvas.

### 4.4 Node 1 — Sports Setup
- Free-text authoring: "+ Add Sport" → "+ Add Category" → "+ Add Event"
  (3-level nested list).
- Each Event has an Event Type dropdown: **Individual / Team / Double**.
- Status pill (Pending/Configured/Published) + a "Publish" button.
- Seed 2 sports × 2 events each.

### 4.5 Node 2 — Registration Setup
- Left: system-defined User Type list with enable checkboxes (e.g.
  Athlete, Coach, Official, Volunteer).
- An enabled User Type can add a free-text Sub-Type (e.g. Athlete →
  Senior / Junior).
- Clicking a leaf (whichever of User Type / Sub-Type has no children)
  opens a Form Builder panel: toggle fields from the standard palette
  (Name, DOB, Gender, Contact Number, ID/Aadhaar Number, Photo) on/off,
  with a "required" checkbox per field.
- Publish button + status pill.

### 4.6 Node 3 — Quota Setup
Two sub-tabs: **Level 1 (Overall Quota)**, **Level 2 (Contingent
Allocation)**.
- **Level 1:** "+ Add Quota" guided flow — User Type → granularity
  (Sport/Event) → Event → then Max/Min/Reserve/Max Team fields. List/table
  of created entries.
- **Level 2:** Sport-first left rail; selecting a sport shows its events,
  each with a table of Institutions ("+ Add Institution") and per-row Q/R
  inputs, plus a running total vs. the Level 1 ceiling (e.g.
  "Σ Q: 18 / Max 20").
- Status pill per sport (Level 2) + a node-level rollup pill.

### 4.7 Node 4 — Registration & Nomination
Two sub-tabs: **Registration**, **Nomination**.
- **Registration:** table of registered individuals per sport; "+ Add"
  opens Manual (a short form) or Invite (an email input) as simple
  modals.
- **Nomination — the interactive centerpiece; drag-and-drop must actually
  work:**
  - Left panel: scrollable, **draggable** individual cards (name, User
    Type).
  - Right panel: Event boxes, each split into two drop zones, **Main
    (Q)** and **Reserve (R)**, each showing "x / capacity" and rejecting
    a drop once full.
  - Dragging a card into a zone moves it there (removed from the left
    list); dragging a placed card back to the left panel un-nominates
    them.
  - "Publish" button, disabled until every event's Main and Reserve zones
    are exactly full — show which zones are incomplete when disabled.
  - Seed ~10 individuals and 2-3 events with small capacities (e.g. Q=3,
    R=2) so filling them is actually demoable.

### 4.8 Node 5 — Registration Dashboard
- Flat table with filters (Sport, Event, Institution) at top. Columns:
  Name, User Type, Sport, Event, Institution, Status (Main/Reserve pill).
- Seed 10-15 rows.

## 5. Interactions that must actually work (priority order)

1. **Hierarchy canvas (4.1)** — drag-to-create from the palette,
   drag-to-connect (Institution/Individual onto an FA, FA onto the
   Project root), and click-to-open the right config panel with the
   FA/Institution/Individual form. Must-have.
2. **Drag-and-drop in Nomination (4.7)** — must-have, same priority as
   above.
3. Tab navigation (Hierarchy ↔ Registration & Quota Setup) and clicking
   a fixed node on the 5-node overview (4.2) to open its own canvas.
4. Sub-canvas drill-in/out in Hierarchy.
5. Add/enable forms in Sports Setup, Registration Setup, Quota Setup
   (simple forms/modals are fine, no need for real validation).
6. Filters on the Dashboard table.

Everything else — role switching, notifications, autosave — skip or fake
with static UI.

## 6. Data — mock only

Static seed data for: 2 sports, 4-6 events, 4-5 User Types, 3-4
Institutions, ~10-15 Individuals. No real backend logic, no auth, no
persistence requirements beyond Base44's defaults.

## 7. Explicit non-goals (don't build)

- **No project hero/gradient card and no white Project secondary
  sidebar (Overview/Project Details/Functional Area/Task Board/User
  Management/Modules/Budget)** — explicitly excluded from every Flow
  Builder screen, replaced by the "← Back to Project" control (§3).
- No real authentication/roles — hardcode "Master Admin" as in the
  screenshot.
- No notification delivery or email sending.
- No multi-Industry/tenant switching.
- No Level-3 quota, no per-individual multi-event cap enforcement.
- No custom quota-field builder — standard palette only.
- No manual freeform connector-dragging in Hierarchy — connect-on-drop
  (§4.1) is enough; don't build Figma-style drag-a-line-between-handles.
