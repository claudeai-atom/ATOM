# Flow Builder Prototype — Update Prompt (Sports Setup & Quota Setup only)

Context: source of truth is `atom-flow-builder-sports-registration-setup.md`
§32 (Sports Setup) and `atom-flow-builder-quota-setup.md` §2–§10 (Quota
Setup), current as of 2026-09-14. Original prototype brief:
`2026-08-24-flow-builder-base44-prototype-brief.md` §4.4/§4.6 (now
superseded for these two nodes only).

---

## Prompt to paste into AI Studio

You previously built a Flow Builder prototype for me. Since then, a new
**Master Admin Sports Library** was introduced upstream of Flow Builder —
it changes how two of the five nodes work: **Sports Setup** and **Quota
Setup**. Update only these two nodes. Do not touch Hierarchy, Registration
Setup, Registration & Nomination, or the Registration Dashboard — they are
unaffected and should stay exactly as built.

### Why it's changing

Previously, Industry free-text authored its own Sport → Category → Event
tree inside Sports Setup. Now, a separate Master Admin portal (outside
Flow Builder, not part of this app) owns a global **Sport → Discipline**
catalog and assigns a subset of it to each Industry. Master Admin fixes,
per Discipline: Gender, Type (Individual/Double/Team), playing-member
count (e.g. 11 for Cricket), and — new — eligibility criteria (Age
range+cutoff date, Gender, and extensible custom criteria like Weight).
Industry no longer creates or names anything in Sports Setup — it only
**enables** what Master Admin assigned. This also removes the old
Category/Event levels entirely: the tree is now 2 levels, Sport →
Discipline, and Discipline is the new leaf that Quota Setup attaches to
(replacing the old Event leaf).

### 1. Sports Setup node — rebuild as enable-only over a fixed catalog

**Remove entirely:** the "+ Add Sport" / "+ Add Category" / "+ Add Event"
free-text authoring flow, the 3-level nested list, and the Event Type
dropdown on events.

**Build instead:**

- **Left panel, "My Sports Library"** — a read-only, searchable list of
  Disciplines already assigned to this Industry by Master Admin, grouped
  by Sport (collapsible headers), each card showing its Master-Admin-owned
  attributes baked in (not editable): playing-member count and Type. Show
  a small "X of Y assigned enabled" counter per Sport group.

  ```
  MY SPORTS LIBRARY                    🔍 [search disciplines...]

  ▾ Cricket                    (2 of 3 assigned disciplines enabled)
      ┌─────────────────────────┐
      │ Cricket Mens Senior      │  ← drag to canvas
      │ 11 playing · Team        │
      └─────────────────────────┘
      ┌─────────────────────────┐
      │ Cricket Womens Senior    │
      │ 11 playing · Team        │
      └─────────────────────────┘

  ▾ Swimming                   (0 of 4 assigned disciplines enabled)
      ┌─────────────────────────┐
      │ Swimming Mens Senior     │
      │ 1 playing · Individual   │
      └─────────────────────────┘
  ```

- **Central canvas** — same tree visual language as before, but flattened
  to 2 levels and read-only content: Sport as an auto-grouping header
  (not configurable), Discipline as the only draggable/interactive card.
  Dropping a Discipline card onto the canvas = **Enable**, not author.
  Removing a card from the canvas = **Disable** (show a simple warning
  modal if the prototype has any Quota/Registration data seeded against
  it — no real logic needed, just the warning).

- **Right configuration panel** — opens on clicking a Discipline card, all
  fields read-only except one action:

  ```
  Cricket Mens Senior

  Sport:            Cricket           (read-only, from Master Admin)
  Gender:           Men               (read-only)
  Type:             Team              (read-only)
  Playing Members:  11                (read-only)

  Status:           🟢 Enabled
  [ Disable ]

  Assigned to this Industry by Master Admin — cannot be edited here.
  ```

- **Status pill + Publish button** — unchanged from before (Pending /
  Configured / Published).

- **Seed data:** 2–3 Sports, 2 Disciplines each (mix of Individual/Team
  types), some enabled and some not-yet-enabled, matching the example
  above.

### 2. Quota Setup node — add an Eligibility stage, repoint Level 1 to Discipline, redesign Team fields

Quota Setup is now **three sequential stages**, not two: **Eligibility →
Level 1 → Level 2**. Show this as either a 3-step tab bar or a step
indicator at the top of the node's canvas.

**Eligibility stage (new, read-only, no authoring UI):**

For the currently selected Discipline × User Type entry, display whatever
Master Admin set on that Discipline — Age (as "Min–Max, as of [cutoff
date]"), Gender (echoed from the Discipline attribute), and any custom
criteria (e.g. "Weight: 60–75kg"). There is no "+ Add Eligibility Rule"
button and no edit affordance anywhere in this stage — Industry only
views it. Also show a compact **Eligibility summary badge on every Level
1 quota entry row** (see table below), not hidden behind a click.

**Level 1 (Overall Quota) — repoint leaf, redesign Team fields:**

- The "+ Add Quota" guided flow becomes: pick **User Type** → pick
  **Granularity** (Discipline / Sport / Project) → if Discipline-level,
  pick the specific **Discipline** (was "Event"). Rename every remaining
  "Event" label in this node to "Discipline."
- **Individual fields unchanged:** Max, Min, Reserve.
- **Double fields unchanged:** Max Pairs, Min Pairs, Reserve Pairs (pair
  size fixed at 2, no separate field).
- **Team fields — replace entirely:**
  - Remove the old "Max/Min Team Members" field completely — squad size
    is no longer Industry-set, it's fixed read-only by Master Admin
    (shown for reference, e.g. "11 playing (fixed by Master Admin)").
  - Add: **Max Team**, **Min Team** (number of teams allowed).
  - Add: **Playing breakdown per User Type/Sub-Type** — Industry splits
    the fixed playing total across roles (e.g. 5 Batsman / 4 Bowler / 2
    All-Rounder for Cricket's 11). Show a running sum vs. the fixed total
    and block/flag if it doesn't match exactly.
  - Add: **Max/Min Reserve per team, per User Type/Sub-Type** — same
    role-tagging as the playing breakdown.
- **Quota entry table** gains an Eligibility column:

  ```
  SWIMMING                                              [+ Add Quota]
  User Type    Discipline            Max  Min  Reserve   Eligibility
  Athlete      100m Freestyle (M)   [40] [10]  [4]      Age 14–18 (as of Dec 31) · Male
  Athlete      200m Freestyle (M)   [40] [10]  [4]      Age 14–18 (as of Dec 31) · Male
  ```

**Level 2 (Contingent Allocation) — relabel only, shape unchanged:**

Same Sport-first left rail, same per-Institution Q/R table, same rollup
math. Just rename "Event" to "Discipline" throughout. Team-type entries
keep the existing Level 2 shape: **Q only** (number of teams per
Institution), no R column, no role breakdown — the role/reserve template
is fixed at Level 1 and travels automatically.

**Seed data:** update sample entries to use Discipline names (e.g.
"Cricket Mens Senior," "100m Freestyle (M)") instead of the old Event
names, and include at least one Team-type entry showing the new playing
breakdown fields plus its Eligibility badge.

### Explicit non-goals for this update

- Don't touch Hierarchy, Registration Setup, Registration & Nomination, or
  the Registration Dashboard nodes — out of scope, unaffected.
- Don't build Mode of Registration (Open/Closed) or the Open
  Quota/Selection flow — that's a separate, unrelated feature thread, not
  part of this update.
- Don't build Master Admin's own Sports Library authoring screen — this
  app only **consumes** it; mock "My Sports Library" as static seed data
  as if Master Admin already created and assigned it.
- No custom-eligibility-criteria builder — just display whatever custom
  criteria are seeded as read-only key:value pairs.
