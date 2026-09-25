# Flow Builder Prototype — Update Prompt (Registration Setup node only)

Context: source of truth is `atom-flow-builder-sports-registration-setup.md`
§34 (data model — Sport-scoped Joining Form) and §35 (canvas/UI
redesign), current as of 2026-09-14. Current built state is
`registration-setup-node.png` — flat User Type list, no Sport concept,
form fields shown inline in the main panel. Companion prompt already
delivered for the other two changed nodes:
`2026-09-14-flow-builder-sports-quota-prototype-update-prompt.md`
(Sports Setup + Quota Setup).

---

## Prompt to paste into AI Studio

Update only the **Registration Setup** node (Node 2 of the 5-step
Registration & Quota Setup sequence). Do not touch Hierarchy, Sports
Setup, Quota Setup, Registration & Nomination, or the Registration
Dashboard in this pass — they're covered separately or unaffected.

### Why it's changing

Registration Setup currently has no concept of Sport at all — it's a
flat User Type → Sub-Type → Joining Form tree, and the Joining Form is
identical no matter which Sport an Individual is registering for. That's
no longer correct: Master Admin now attaches eligibility criteria (Age,
Gender, Weight, other custom criteria) per Sport in the Sports Library,
and those criteria must be captured on the Joining Form — but different
Sports can have different criteria (e.g. Boxing needs Weight, Swimming
doesn't). So the Joining Form must become **Sport-scoped**: one form per
**Sport × User Type/Sub-Type** combination, not just per User
Type/Sub-Type. The same User Type/Sub-Type used under two different
Sports now needs two separate forms (e.g. "Athlete" under both Swimming
and Boxing).

### Rebuild as a 3-pane layout (not a canvas)

Replace the current 2-pane layout (User Types list | Form Fields table)
with three panes:

**1. Far-left rail — Sports.** Auto-populated from every Sport that has
at least one enabled Discipline in Sports Setup (same source and
behavior as Quota Setup's Sport rail — no manual "add" step, every
relevant Sport is just already there). Clicking a Sport scopes the
middle panel to it.

```
SPORTS
▸ Cricket
▸ Swimming
▸ Boxing
```

**2. Middle panel — User Types/Sub-Types for the selected Sport.** Reuse
the existing built UI almost exactly (enable checkboxes, "+ Add
Sub-Type", the card style from the current screenshot), but scope it
per-Sport: enabling "Athlete" under Cricket and enabling "Athlete" under
Swimming are **independent states** — each produces its own leaf and its
own form, not a shared one. Each leaf (a User Type with no Sub-Types, or
a Sub-Type) shows a **"Configure Form"** button instead of the form
table appearing inline in this panel.

```
USER TYPES — Cricket                    Enable roles & configure forms

☑ Athlete                    8 fields
  Sub-Types              + Add Sub-Type
  Senior National Athlete        [Configure Form]
  Junior National Athlete (U-20) [Configure Form]

☑ Coach & Support Staff      6 fields
  Sub-Types              + Add Sub-Type
  Head Coach                     [Configure Form]
  Assistant / Skills Coach       [Configure Form]
```

**3. Right panel — Form Builder, on-demand, expandable.** Clicking
"Configure Form" on a leaf slides in a right-side drawer (not a full
inline panel) containing the existing field-toggle-table UI from the
current screenshot (Standard Field Attribute / Active in Form / Required
Mandatory columns, "+ Add Custom Field" button, `text`/`date`/`select`
type badges), unchanged in mechanics — but add one new section **above**
it:

```
FORM BUILDER — Cricket × Athlete                           [Expand ⤢] [×]

🔒 LOCKED — Set by Master Admin (cannot be removed)
  Date of Birth (DOB)     — for Age eligibility
  Gender                  — read-only echo
  Weight                  — combat-sport custom criterion

STANDARD FIELD ATTRIBUTE      ACTIVE IN FORM    REQUIRED MANDATORY
[existing table from the current screenshot, unchanged]

[+ Add Custom Field]
```

- The drawer opens at a default width (similar to a standard side panel)
  with an **expand control** (`⤢` icon or similar) that widens it to
  full-screen/full-width for easier editing, and a close control to
  collapse it back. This is the same drawer for every leaf — reopening
  a different leaf's "Configure Form" swaps its content, doesn't stack
  multiple drawers.
- Fields in the "Locked" section are visually distinct from the
  standard/custom fields below (e.g. a lock icon, muted/non-interactive
  toggle, different background tint) and cannot be toggled off or marked
  non-mandatory — they're always active and always required.
- Keep the existing "Field Config" / "Live Form Preview" toggle buttons
  and the "Publish Registration Schema" button from the current top bar;
  wire "Publish" to the whole node for now (don't build per-Sport publish
  gating in this pass).

### Seed data

Seed 2–3 Sports (e.g. Cricket, Swimming, Boxing) in the rail. Under
Cricket, enable Athlete and Coach & Support Staff (matching the existing
screenshot's data) with their existing Sub-Types. Under Boxing, seed at
least one leaf whose locked section shows a Weight field (to demonstrate
a Sport with different MA-set criteria than Cricket/Swimming, which
should only show DOB + Gender locked). Reuse the existing standard field
list (Full Legal Name, DOB, Gender, Contact Number, ID/Aadhaar, Photo)
for the non-locked part of every form.

### Explicit non-goals for this update

- Don't touch Hierarchy, Sports Setup, Quota Setup, Registration &
  Nomination, or the Registration Dashboard nodes in this pass.
- Don't build a free-form drag/connect canvas — this is a fixed 3-pane
  rail/panel/drawer layout, not a Figma-style canvas like Hierarchy or
  Sports Setup.
- Don't build per-Sport Publish gating — Publish stays node-wide for now.
- Don't build the Master Admin Sports Library authoring screen — mock
  the locked-field data per Sport as static seed data, as if Master
  Admin already defined it.
- No new validation logic on the locked fields beyond marking them
  always-active/always-mandatory in the UI.
