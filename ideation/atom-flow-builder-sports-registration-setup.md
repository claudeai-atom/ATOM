# ATOM Flow Builder — Registration & Quota Setup
## Finalized Ideation: Sports Setup & Registration Setup

## 1. Purpose

The ATOM Flow Builder contains two top-level tabs:

1. **Hierarchy Setup**
2. **Registration & Quota Setup**

The **Hierarchy Setup** flow is already finalized and is outside the scope of this document.

The **Registration & Quota Setup** tab contains four fixed configuration nodes:

```text
Sports Setup
      ↓
Registration Setup
      ↓
Quota Setup
      ↓
Registration & Nomination
```

Each node has three states:

- **Pending**
- **Configured**
- **Published**

This document captures the finalized ideation for **Sports Setup** and **Registration Setup**. Quota Setup and Registration & Nomination are intentionally not defined here.

---

# 2. Common Design Principles

## 2.1 Fixed Nodes

The four nodes in Registration & Quota Setup are system-defined. Users cannot add, delete, or rearrange them.

```text
┌──────────────────────┐
│ 1. Sports Setup      │
│ Status: Pending      │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ 2. Registration Setup│
│ Status: Pending      │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ 3. Quota Setup       │
│ Status: Pending      │
└──────────┬───────────┘
           ↓
┌──────────────────────┐
│ 4. Registration &    │
│    Nomination        │
│ Status: Pending      │
└──────────────────────┘
```

## 2.2 Status Model

### Pending
Configuration has not yet been completed.

### Configured
Configuration has been created and passes applicable validation but has not been published.

### Published
Configuration is published and available to downstream ATOM functionality.

---

# 3. Sports Setup

**⚠ SUPERSEDED (2026-09-09) — see §32.** Sections 3–13 and the Sports
Setup half of §30 below describe the **free-text-authoring** model
(2026-08-18 pivot: Industry names Sport/Category/Event). A new Master
Admin "Sports Library" requirement (`ideation/2026-08-25-sports-library-
master-admin-change-impact.md`) reverses that pivot: Master Admin now
owns the whole sports taxonomy (Sport → Discipline, 2 levels), and
Industry Sports Setup is **enable-only** again. Sections 3–13/30 are kept
below for historical record (they were a real, deliberate decision, not
an error) but are **no longer current behavior**. §32 is authoritative
for Sports Setup as of 2026-09-09.

## 3.1 Purpose

Sports Setup defines the complete sports structure enabled for a particular project.

The authorized user configures:

```text
Sport
  ↓
Category
  ↓
Event
```

Gender is defined as part of the Category.

Event Type is defined at the Event level.

The final configuration is represented as a visual tree on the canvas.

---

# 4. Sports Setup Canvas

Clicking **Sports Setup** opens a dedicated sub-canvas inside Flow Builder.

The workspace contains:

- Left node/library panel
- Central canvas
- Right configuration panel
- Canvas controls
- Save/Publish actions as applicable

The configuration is represented visually rather than as a single conventional form.

---

# 5. Sports Setup — Left Panel

The left panel contains the Sport node/library.

```text
SPORTS

┌─────────────────────┐
│ Sport               │
│ Drag to Canvas      │
└─────────────────────┘
```

The user drags the Sport node to the canvas.

Multiple Sport nodes can be added.

---

# 6. Adding a Sport

When a Sport node is dropped onto the canvas, configure:

- **Sport Name**
- **Sport Logo**

Example:

```text
┌──────────────────────────┐
│ Swimming                 │
│                          │
│ + Add Category           │
└──────────────────────────┘
```

The Sport becomes the parent of its Categories.

---

# 7. Adding a Category

A Category is created under a Sport.

Fields:

- **Gender**
- **Category Name**

Example:

```text
Swimming
├── Men - Senior
└── Women - Senior
```

Multiple Categories can exist under a Sport.

Example:

```text
Swimming
├── Men - Senior
├── Women - Senior
├── Men - U-18
└── Women - U-18
```

---

# 8. Adding an Event

Events are created under a specific Category.

Fields:

- **Event Name**
- **Type**
  - Individual
  - Double
  - Team
- **Category**

The Category is derived from the parent Category node.

Example:

```text
Swimming
└── Men - Senior
    ├── 100m Freestyle
    │    Type: Individual
    ├── 200m Freestyle
    │    Type: Individual
    └── 4x100m Relay
         Type: Team
```

An Event cannot exist independently of a Category, and a Category cannot exist independently of a Sport.

Therefore:

```text
Sport → Category → Event
```

---

# 9. Multiple Sports

The Industry can configure multiple Sports in the same project.

Example:

```text
                    SPORTS SETUP

        ┌──────────────┐
        │  Swimming    │
        └──────┬───────┘
               │
        ┌──────┴──────┐
        │             │
   Men - Senior   Women - Senior
        │             │
    ┌───┴───┐       ┌─┴────┐
   100m    200m     100m   200m


        ┌──────────────┐
        │  Athletics   │
        └──────┬───────┘
               │
        ┌──────┴──────┐
        │             │
   Men - Senior   Women - Senior
        │             │
    ┌───┴───┐       ┌─┴────┐
   100m    200m     100m   200m
```

The canvas represents the complete sports configuration for the project.

---

# 10. Sports Setup — Node Interaction

## Sport Node

Selecting a Sport opens the right configuration panel.

The panel displays:

- Sport Name
- Sport Logo
- Category count
- Event count
- Add Category
- Other applicable actions

## Category Node

Selecting a Category opens the right configuration panel.

The panel displays:

- Gender
- Category Name
- Parent Sport
- Event count
- Add Event
- Other applicable actions

## Event Node

Selecting an Event opens the right configuration panel.

The panel displays:

- Event Name
- Event Type
- Parent Sport
- Parent Category
- Applicable actions

---

# 11. Sports Setup — Rules

### Rule 1 — Event requires Category
An Event cannot be created without a Category.

### Rule 2 — Category requires Sport
A Category cannot be created without a Sport.

### Rule 3 — Event inherits Category context
When an Event is created from a Category, its Category is automatically associated with that parent.

### Rule 4 — Multiple Categories
A Sport can have multiple Categories.

### Rule 5 — Multiple Events
A Category can have multiple Events.

### Rule 6 — Multiple Sports
A project can have multiple Sports.

---

# 12. Sports Setup — Completion & Publishing

### Pending
No meaningful Sports configuration has been completed.

### Configured
Sports, Categories and Events have been configured and pass validation.

### Published
The Sports configuration has been published and becomes available to downstream setup.

The published Sports Setup becomes the source configuration for subsequent features such as Registration Setup and Quota Setup.

---

# 13. Sports Setup — Permission & Approval Model

The Industry controls what Institutions can do through ATOM RBAC.

Approval is **not mandatory by default**.

Configuration and publishing are separate permissions.

Possible permissions include:

- View Sports Configuration
- Create/Edit Sports Configuration
- Publish Sports Configuration

## Institution with Publish Permission

```text
Configure
   ↓
Validate
   ↓
Publish
```

No Industry approval is required.

## Institution without Publish Permission

```text
Configure
   ↓
Configured
   ↓
Pending Approval
   ↓
Industry Review
```

Industry can:

- Approve → Published
- Reject → Configured

Approval is therefore permission-based rather than mandatory for every configuration.

---

# 14. Registration Setup

## 14.1 Purpose

Registration Setup defines:

1. Who can join the project.
2. Which User Types are enabled.
3. Which Subtypes are available under each User Type.
4. Which Joining Form must be completed for each registration entity.

The configuration structure is:

```text
User Type
    ↓
Subtype
    ↓
Joining Form
```

A User Type may also exist without a Subtype:

```text
User Type
    ↓
Joining Form
```

---

# 15. Registration Setup — User Type Library

Clicking Registration Setup opens a dedicated sub-canvas.

The left panel contains predefined User Types.

Example:

```text
USER TYPES

┌─────────────────────┐
│ Athlete             │
└─────────────────────┘

┌─────────────────────┐
│ Coach               │
└─────────────────────┘

┌─────────────────────┐
│ Official            │
└─────────────────────┘

┌─────────────────────┐
│ Support Staff       │
└─────────────────────┘

┌─────────────────────┐
│ Volunteer           │
└─────────────────────┘
```

The actual User Type list is system-defined.

---

# 16. User Type Creation Rule

The Industry/authorized Institution cannot create a completely new User Type.

They can only:

- Enable an existing User Type by adding it to the canvas.
- Add Subtypes under the enabled User Type.

This keeps User Types consistent across ATOM.

---

# 17. Adding a User Type

The user drags a User Type from the left panel and drops it onto the canvas.

Example:

```text
┌──────────────────────────┐
│ Athlete                  │
│                          │
│ + Add Subtype            │
│                          │
│ Joining Form: Pending    │
└──────────────────────────┘
```

Multiple User Types can be added to the same project.

---

# 18. Adding a Subtype

Clicking **+ Add Subtype** opens the subtype configuration interface through the right panel or modal.

Two options are available.

## Option 1 — Select Existing Subtype

Select from predefined Subtypes applicable to the User Type.

```text
Select Existing Subtype

[ Senior ▼ ]
```

## Option 2 — Create New Subtype

Create a project-specific Subtype using:

- **Subtype Name**

Example:

```text
Create New Subtype

Subtype Name
[ Junior ]

[Cancel] [Add Subtype]
```

---

# 19. Registration Tree

Added Subtypes are linked to their parent User Type.

Example:

```text
Athlete
├── Senior
├── Junior
└── U-18

Coach
├── Head Coach
└── Assistant Coach

Official
├── Technical Official
└── Match Official
```

Multiple User Types and Subtypes can exist on the same canvas.

The visual model intentionally follows the Sports Setup tree pattern.

---

# 20. User Type Without Subtype

A User Type does not have to contain a Subtype.

For example:

```text
Coach
   ↓
Joining Form
```

Whereas a User Type with Subtypes follows:

```text
Athlete
   ↓
Senior
   ↓
Joining Form
```

The system must support both configurations.

---

# 21. Joining Form Requirement

Every enabled registration entity must have a Joining Form.

A registration entity is either:

1. A User Type with no Subtypes, or
2. An enabled Subtype under a User Type.

Example:

```text
Athlete
├── Senior      → Joining Form
├── Junior      → Joining Form
└── U-18        → Joining Form

Coach           → Joining Form
```

If Subtypes exist, each enabled Subtype gets its own Joining Form.

If no Subtype exists, the User Type itself gets the Joining Form.

---

# 22. Joining Form Status

Each enabled registration entity maintains an independent form status.

## Pending

No Joining Form configured.

```text
🔘 Pending

[+ Add Form]
```

## Configured

A form exists and is configured but not published.

```text
🟡 Configured

[Edit Form]
```

## Published

The form is published and available for project joining.

```text
🟢 Published

[View Form] [Edit Form]
```

---

# 23. Form Builder Integration

ATOM already has an existing Form Builder with field configuration and validations.

Registration Setup will **not recreate Form Builder**.

Clicking **+ Add Form** redirects the user to the existing ATOM Form Builder.

```text
Registration Setup
       ↓
Athlete
       ↓
Senior
       ↓
+ Add Form
       ↓
Existing Form Builder
```

The existing Form Builder handles:

- Adding fields
- Adding questions
- Field types
- Required/optional configuration
- Validations
- Reordering
- Save Draft
- Publish

The form is associated with the User Type/Subtype from which the user launched Form Builder.

---

# 24. Form Context

When launching Form Builder, the context should identify the registration entity.

Example:

```text
Project:
37th National Games

User Type:
Athlete

Subtype:
Senior

Form:
Senior Athlete Joining Form
```

This ensures the form is associated with:

```text
Project
   ↓
Athlete
   ↓
Senior
```

---

# 25. Registration Setup — Example

A completed Registration Setup canvas could look like:

```text
                         REGISTRATION SETUP

       ┌────────────────┐            ┌────────────────┐
       │    Athlete     │            │     Coach      │
       └───────┬────────┘            └───────┬────────┘
               │                             │
       ┌───────┼────────┐                    │
       │       │        │                    │
    Senior   Junior    U-18             Joining Form
       │       │        │
      🟢      🟢       🟡
      Form    Form     Form
```

Another example:

```text
Official
├── Technical Official
│      └── Joining Form 🟢 Published
│
└── Match Official
       └── Joining Form 🟡 Configured
```

---

# 26. Registration Setup — Publication Validation

A User Type cannot be fully Published if an enabled Subtype does not have a Published Joining Form.

Example:

```text
Athlete
├── Senior     🟢 Published
├── Junior     🟢 Published
└── U-18       🟡 Configured
```

In this state:

```text
Athlete = Configured
```

It cannot become Published until the U-18 form is also Published.

Once all enabled registration entities have published forms:

```text
Athlete
├── Senior     🟢
├── Junior     🟢
└── U-18       🟢

Athlete = Published
```

---

# 27. Registration Setup — Permission & Approval Model

The same permission-based publishing model used in Sports Setup applies here.

Possible permissions include:

- View Registration Configuration
- Create/Edit Registration Configuration
- Publish Registration Configuration

## Institution with Publish Permission

```text
Configure
   ↓
Create/Configure Forms
   ↓
Validate
   ↓
Publish
```

No Industry approval is required.

## Institution without Publish Permission

```text
Configure
   ↓
Configured
   ↓
Pending Approval
   ↓
Industry Review
      ↙     ↘
   Approve   Reject
      ↓        ↓
 Published  Configured
```

Approval is therefore permission-based rather than mandatory.

---

# 28. Project Joining Flow

Once Registration Setup and the required forms are published, the configuration is used during project joining.

```text
User wants to join Project
          ↓
Selects User Type
          ↓
Selects Subtype, if applicable
          ↓
System identifies associated Joining Form
          ↓
User fills Joining Form
          ↓
Form validation
          ↓
Submit Join Request
          ↓
Industry / Authorized Reviewer
          ↓
Approve / Reject
          ↓
Project Access
```

Example:

```text
User Type: Athlete
Subtype: Senior

        ↓

Senior Athlete Joining Form

        ↓

Submit

        ↓

Industry Review

        ↓

Approved
```

The joining request review/approval process is not configured inside the Registration Setup canvas. Registration Setup only defines the registration structure and associated forms.

---

# 29. Relationship Between Sports & Registration Setup

## Sports Setup

```text
Sport
 ↓
Category
 ↓
Event
```

Answers:

> **What sports/events are available in this project?**

## Registration Setup

```text
User Type
 ↓
Subtype
 ↓
Joining Form
```

Answers:

> **Who can join this project and what information must they provide?**

These configurations become inputs for downstream Quota Setup.

```text
Sports Setup
Sport
 ↓
Category
 ↓
Event
                                    → Quota Setup
            /
           /
Registration Setup
User Type
 ↓
Subtype
```

Quota Setup can therefore consume the already configured Sports and Registration structures rather than redefining them.

---

# 30. Final Locked Scope

## Sports Setup

**Structure:**

```text
Sport → Category → Event
```

### Sport
- Sport Name
- Sport Logo

### Category
- Gender
- Category Name

### Event
- Event Name
- Type: Individual / Double / Team
- Category: derived from parent Category

### Behavior
- Sports are added through the left node library.
- Multiple Sports can be configured.
- Multiple Categories can exist under each Sport.
- Multiple Events can exist under each Category.
- The complete configuration is displayed as a tree.
- Status: Pending / Configured / Published.
- Published Sports become available to downstream setup.

## Registration Setup

**Structure:**

```text
User Type → Subtype → Joining Form
```

or, when no Subtype exists:

```text
User Type → Joining Form
```

### User Type
- Predefined by ATOM.
- Cannot be created by Industry/Institution.
- Enabled by dragging to the canvas.

### Subtype
- Select from predefined Subtypes.
- Create a new project-specific Subtype.
- Multiple Subtypes can exist under a User Type.

### Joining Form
- Required for every enabled registration entity.
- Uses the existing ATOM Form Builder.
- Add Form redirects to Form Builder.
- Form status: Pending / Configured / Published.
- If Subtypes exist, every enabled Subtype requires a published form.
- If no Subtype exists, the User Type requires the Joining Form.

### Mode of Registration — new, 2026-09-10, see §33
- Same leaf as the Joining Form (User Type if no Subtype, else each
  Subtype). Two values: **Open** (Invite + Manual + Self-Register) /
  **Closed** (Invite + Manual only — today's model, unchanged). Default
  **Closed**.
- Project-wide per User Type/Sub-Type, no per-Discipline override.
- Self-Register's quota interaction is explicitly parked, pending a
  dedicated Eligibility design pass (§33.6).

### Permission Model
- Industry controls Institution permissions through RBAC.
- Create/Edit and Publish are separate capabilities.
- Users with Publish permission do not require Industry approval.
- Users without Publish permission can configure and submit for Industry approval.

---

# 31. Overall Flow

```text
                    FLOW BUILDER
                         │
          ┌──────────────┴──────────────┐
          │                             │
   Hierarchy Setup           Registration & Quota Setup
                                        │
                         ┌──────────────┼──────────────┐
                         │              │              │
                  Sports Setup   Registration Setup   ...
                         │              │
                         │              │
                    Sport →         User Type →
                    Category →      Subtype →
                    Event           Joining Form
                         │              │
                         └──────┬───────┘
                                ↓
                           Quota Setup
                                ↓
                     Registration & Nomination
```

**Current finalized scope:** Sports Setup + Registration Setup.

Quota Setup and Registration & Nomination remain for subsequent ideation.

---

# 32. Sports Setup v2 — LOCKED (2026-09-09)

**Supersedes §3–13 and the Sports Setup half of §30.** Reason: the
Master Admin Sports Library requirement (see
`2026-08-25-sports-library-master-admin-change-impact.md` §7) reverses
the 2026-08-18 free-text-authoring pivot. Master Admin now owns the
entire sports taxonomy; Industry Sports Setup is enable-only. This
section is the authoritative Sports Setup design going forward.

## 32.1 Purpose

Sports Setup now defines **which of the Disciplines assigned to this
Industry by Master Admin are enabled for this project**. It no longer
authors Sport/Category/Event names, gender, type, or squad size — all of
that is fixed upstream in the Master Admin Sports Library.

## 32.2 Structure

```text
Sport            (Master-Admin-authored grouping, read-only)
  ↓
Discipline       (Master-Admin-authored leaf — e.g. "Cricket Mens Senior")
```

No Category or Event level exists anymore. Discipline carries Gender,
Type (Individual/Double/Team), and playing-member count as fixed,
read-only attributes (per the §6 Quota Setup resolution and §7
ownership resolution in the Sports Library doc). Discipline is the new
leaf that Registration Setup and Quota Setup attach to (previously
Event — see the "Downstream impact" flag in §32.6).

## 32.3 Left Panel — "My Sports Library"

Not the full system catalog — scoped to only the Sports/Disciplines
**Master Admin has assigned to this Industry** (the two-tier
entitlement chain from the Sports Library requirement).

```text
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

▸ Athletics                  (collapsed)
```

- Grouped by Sport (auto, read-only grouping — convenience only, not
  authored by Industry).
- Each card shows Master-Admin-owned attributes baked in (playing count,
  Type) — not editable here.
- Search bar is load-bearing at scale (worked example: 5 sports, 10
  disciplines assigned to one Industry), unlike the old free-text model
  where the list only grew as Industry typed.
- Small per-Sport assigned/enabled counter for at-a-glance status.

## 32.4 Central Canvas

Same tree visual language as the superseded model, flattened to 2
levels, read-only node content:

```text
                    SPORTS SETUP

        ┌──────────────┐
        │   Cricket    │   ← auto-grouping header, not configurable
        └──────┬───────┘
               │
    ┌──────────┴──────────┐
    │                      │
┌───────────────┐   ┌────────────────┐
│ Cricket Mens   │   │ Cricket Womens │
│ Senior         │   │ Senior         │
│ 🟢 Enabled     │   │ 🟢 Enabled     │
└────────────────┘   └────────────────┘
```

- Dropping a Discipline card onto the canvas = **Enable**, not author.
- No "+Add Category" / "+Add Event" affordance anywhere — removed
  entirely, there is nothing left for Industry to name or create.
- Removing a Discipline from canvas = **Disable**. If Registration/Quota
  data already exists against it, surface a warning (exact behavior
  ties into the still-open exact-match-registration-brittleness item —
  flagged, not solved here).

## 32.5 Right Configuration Panel

All fields read-only except the enable/disable action:

```text
Cricket Mens Senior

Sport:            Cricket           (read-only, from Master Admin)
Gender:           Men               (read-only)
Type:             Team              (read-only)
Playing Members:  11                (read-only)

Status:           🟢 Enabled
[ Disable ]

Assigned to this Industry by Master Admin — cannot be edited here.
```

## 32.6 Status Model & Permissions — unchanged in shape

- **Pending** — nothing enabled yet.
- **Configured** — ≥1 Discipline enabled, passes validation.
- **Published** — enabled set locked in, available to Registration Setup
  / Quota Setup.
- Permission/approval model is **unchanged from §13** — Configure/Publish
  remain separate RBAC permissions; Institutions without Publish
  permission submit for Industry review (Approve → Published,
  Reject → Configured).

**Downstream impact flag, corrected 2026-09-09:** Registration Setup's
own tree (`User Type → Subtype → Joining Form`, §14–30) has **no
Sport/Category/Event linkage at all** — confirmed by Abhijeet and borne
out by §29, which shows Sports Setup and Registration Setup as parallel,
independent inputs that only converge downstream at Quota Setup.
**Registration Setup needs no changes** from the Sports Setup v2 switch.
Only **Quota Setup** actually crosses Sport/Discipline × User Type
(Quota Setup §2.3's "per Sport Event × User Type" ceilings) and needs to
re-point its leaf from Event to Discipline. (An earlier draft of this
flag incorrectly named Registration Setup too — corrected here.)

**Explicitly rejected in this pass:** a "Project" node on the Sports
Setup canvas. Every Flow Builder canvas is already scoped to one Project
by context/breadcrumb (same convention as Registration Setup §24) —
adding a draggable Project node would be new and inconsistent with the
other three nodes. Revisit only if a concrete multi-project scenario
surfaces.

---

# 33. Registration Setup — Mode of Registration (new, 2026-09-10)

## 33.1 Purpose

A new per-leaf setting controlling whether an Individual can register
**themselves**, or can only be added by an Institution/Admin acting on
their behalf. Previously Registration & Nomination had exactly two
methods (Invite, Manual) and both were always Institution-initiated —
self-registration did not exist as a concept anywhere in Flow Builder.

## 33.2 Where it lives — same leaf as the Joining Form

Mode of Registration is set at the **same leaf** the Joining Form already
lives at (§21) — the User Type itself if it has no Subtype, otherwise
each enabled Subtype individually. One Mode value per leaf, matching the
existing "forms live at the leaf only" rule (D18) — no inheritance from a
User Type down to its Subtypes.

## 33.3 Two values

**Open** — all three registration methods available downstream in
Registration & Nomination:
1. **Invite** — Institution invites, Individual self-fills, Institution
   approves (existing, unchanged).
2. **Manual / Command** — Institution registers the Individual on their
   behalf (existing, unchanged).
3. **Self-Register (new)** — the Individual finds this leaf's Joining
   Form themselves and registers directly; no Institution action
   required. See `atom-flow-builder-registration-nomination.md` §2.8 for
   the mechanic.

**Closed** — only Invite and Manual/Command. **This is exactly today's
already-locked Registration model — Closed requires no new build**, it's
a label for existing behavior, not a new state.

## 33.4 Default — Closed

New leaves default to **Closed**, preserving current behavior for every
already-designed Registration Setup entry; Industry opts into Open
explicitly. (Reasonable default, not explicitly confirmed by Abhijeet —
flag if a different default is wanted.)

## 33.5 Granularity — Project-wide per User Type/Sub-Type, not per Discipline

Because Registration Setup's tree has no Sport/Discipline linkage at all
(§32.6), Mode of Registration is scoped by **User Type/Sub-Type only** —
the same User Type is Open or Closed across every Discipline it's enabled
on in a project. There is **no per-Discipline override** (e.g. "Athlete"
can't be Open for a public 5K Fun Run but Closed for Cricket trials) —
this was a real granularity trade-off against putting Mode in Quota Setup
instead (Discipline×User Type granularity), and Abhijeet chose Registration
Setup's coarser, simpler placement explicitly.

## 33.6 Quota interaction — RESOLVED 2026-09-10, see Quota Setup §9

Initially parked: whether Self-Register interacts with quota at all, or
whether Open events skip quota entirely and gate only on Eligibility.
Market research (marathon entry-cap-plus-waitlist, open-tournament
entry-cap-then-trials patterns) plus Abhijeet's follow-up correction
settled it — **Open events do cap registration**, via a new **Open
Quota** concept (`atom-flow-builder-quota-setup.md` §9): a **Max Entries**
cap Self-Register hard-blocks against, oversubscribed above a **Selection**
target that reuses Quota Setup Level 1's existing Max/Min/Reserve shape
and Nomination's existing Main/Reserve drag-and-drop mechanic, performed
by Industry or a permitted Institution on the flat self-registered pool
(no Level 2 — there's no Institution to distribute across). **Eligibility
is a related but separate concept** — a different question (who's
allowed to enter) from this one (how many can enter/be selected) — now
also resolved, see §33.8 and Quota Setup §10.

## 33.7 Also open, not yet designed

- Where/how Self-Register actually surfaces to an Individual (a public
  browse/listing page of Open leaves? search? a direct link?) — not
  designed in this pass.
- Whether Self-Register needs its own account-creation flow or reuses
  ATOM's existing sign-up.

## 33.8 Relationship to Eligibility — SUPERSEDED 2026-09-12, see §34

Eligibility (age window, gender, custom criteria) is **not** configured
here — it lives at Quota Setup §10, at Discipline × User Type
granularity, because age-category windows genuinely vary by Discipline
while this node's leaf is deliberately Discipline-agnostic (§33.5).
Registration Setup's role is narrower than it might look: the standard
field palette (DOB, Gender, etc.) **captures** the data Eligibility later
evaluates — this node stays pure data capture, Quota Setup owns the
per-Discipline business rule.

**Superseded by §34 below.** This section's premise — that Registration
Setup's leaf is Discipline-agnostic and Eligibility criteria are
Industry-authored at Quota Setup — no longer holds. Eligibility criteria
are now Master-Admin-authored at Sport/Discipline creation (not just
Age/Gender — extensible, e.g. Weight), and Registration Setup's Joining
Form must capture whatever MA has defined. Kept here, marked superseded,
for the historical record of why the original decoupling was chosen.

---

# 34. Registration Setup — Sport-Scoped Joining Form for Eligibility Capture (new, 2026-09-12)

## 34.1 Trigger

Abhijeet: Master Admin, when creating a Sport and its Disciplines in the
Sports Library, can attach eligibility criteria to them — not just Age
and Gender (§10.3 of `atom-flow-builder-quota-setup.md`), but arbitrary
MA-defined categories, e.g. **Weight** for a combat-sport Discipline.
Whatever MA attaches must be **captured on the Individual's Joining
Form**, or Nomination has nothing to auto-verify against. This is a real
gap: Registration Setup's Joining Form (§14–24) is built per User
Type/Sub-Type only, with **no Sport linkage at all** (§29, §32.6, §33.5)
— it cannot know which MA-defined fields a given Sport requires.

## 34.2 Resolution — the Joining Form becomes Sport-scoped

**Reverses §29 / §32.6 / the 2026-09-09 "Registration Setup: CONFIRMED NO
CHANGES" call.** Registration Setup's tree gains a **Sport axis above
User Type**:

```text
Before (§19, §29):                 After (2026-09-12):
User Type                          Sport
 ↓                                  ↓
Subtype                            User Type
 ↓                                  ↓
Joining Form                       Subtype
                                     ↓
                                    Joining Form
```

**Granularity is Sport, not Discipline** (explicit choice — the
alternative, one form per Discipline, was considered and rejected as
unnecessary machinery). One Joining Form exists per **Sport × User
Type/Sub-Type** combination. The same User Type/Sub-Type used under two
different Sports (e.g. "Athlete" under both Swimming and Boxing) now gets
**two separate forms** — a real increase in authoring surface versus the
old single-form-per-Sub-Type model, accepted as the cost of this design.

**Why Sport, not Discipline, works even though criteria are set per
Discipline:** the form captures the **union** of every criterion any of
that Sport's enabled Disciplines has defined (e.g. Boxing's form gets a
Weight field because *some* Boxing Discipline uses Weight, even if
different weight-class Disciplines under Boxing set different Min/Max
bands). The captured value is evaluated **per Discipline** downstream —
this is exactly the same "capture once, evaluate per entry" pattern
already locked for Age (§10.5 of the Quota Setup doc), just generalized
from Age-only to every MA-set criterion. A Discipline never needs its own
form; it only needs its own threshold, which lives at Quota Setup §10,
not here.

## 34.3 Form content — MA-set fields are locked, Industry's palette stays underneath

When Industry opens Form Builder (§23) for a Sport × User Type/Sub-Type
leaf, every MA-defined eligibility field for that Sport (DOB for Age,
Gender, Weight, any future custom criterion) is **auto-added and
locked** — non-removable, cannot be unchecked from the form, same
"locked" treatment as Gender's read-only echo elsewhere. Industry's
existing standard palette (Name, Contact Number, ID/Aadhaar, Photo) and
"+Custom Field" (§D19) remain fully available underneath, for anything
that isn't an eligibility criterion.

## 34.4 Downstream consequences, flagged not fully re-discussed

- **Quota Setup §10 (Eligibility) simplifies to a pure read-only
  echo** — Industry no longer authors anything there; see
  `atom-flow-builder-quota-setup.md` §10, updated 2026-09-12.
- **Nomination auto-verifies, inferred as hard-block for every MA-set
  criterion** — Abhijeet's own framing ("the system will automatically
  verify if the nominated user is eligible or not") reads as a gate, not
  an informational flag. This closes §10.6's old "custom criteria
  enforcement mode" open item by removing Industry's choice entirely
  (MA's criteria are always auto-verified) — but this is an **inferred**
  reading, not a line Abhijeet stated explicitly as "always hard block."
  Flag for sign-off next time it resurfaces, same treatment as D28 was
  given.
- **Multi-sport reuse (§2.7/§2.8 of `atom-flow-builder-registration-
  nomination.md`, "Search Existing User, no re-registration")** still
  works Sport-by-Sport as designed, and is now actually *more* internally
  consistent — pooling was already Sport-scoped, forms weren't; now both
  are. But a person switching from one Sport's pool to a second Sport's
  pool now fills a genuinely different form (with that Sport's own locked
  fields), not the same Sub-Type form reused verbatim — a consequence
  worth flagging, not re-decided here.
- **Weight-class-as-separate-Disciplines-under-one-Sport** (Abhijeet's
  own example) is the concrete case validating §34.2's union-capture
  design: one "Weight" field captured once on the Boxing Sport form,
  each weight-class Discipline's own Min/Max Weight band checked against
  it at Quota Setup/Nomination time.

---

# 35. Registration Setup — canvas/UI redesign for the Sport axis (2026-09-14)

## 35.1 Trigger

§34 added a Sport axis to the data model (Sport → User Type → Sub-Type →
Joining Form) but the node's existing built canvas (screenshot
`registration-setup-node.png`, matches the old §14–30 shape) has no Sport
concept at all — flat User Type list on the left, Form Fields table
filling the main area. Abhijeet proposed a free-form drag-to-canvas UI
(click a Sport in a left palette to drop its node on a canvas, map User
Types/Sub-Types onto it, "+ Add Form" button on the canvas opening an
expandable right-side Form Builder window) and asked for a UX opinion.

## 35.2 Resolution — rail + panel, matching Quota Setup's existing pattern; canvas proposal rejected

**Rejected the free-form canvas.** Sport → User Type → Sub-Type → Form is
a strict, non-reorderable 3-level tree — nothing is being connected or
re-parented the way Hierarchy's FA/Institution edges or Sports Setup's
enable/disable drag are. A drag-and-connect canvas would add panning/
zooming/arrow overhead with no functional benefit, and would give the
Registration & Quota Setup tab three different interaction idioms for
two variations of the same "rail + scoped panel" problem — Quota Setup
already solved the identical shape (crossing Sport × User Type) with a
Sport rail + main panel (§2.5), not a canvas.

**Adopted instead — 3-pane layout, reusing Quota Setup's rail pattern:**

- **Far-left rail: Sports.** Auto-populated from every Sport with ≥1
  enabled Discipline in Sports Setup — identical source and behavior to
  Quota Setup's rail (§2.5). No manual "click to add" step; if Sports
  Setup enabled it, it's already here. Selecting a Sport scopes the
  middle panel.
- **Middle panel: User Types/Sub-Types for the selected Sport.** Reuses
  the existing built UI (`registration-setup-node.png` — enable
  checkboxes, "+ Add Sub-Type") almost unchanged, now scoped per-Sport:
  enabling "Athlete" under Cricket and enabling "Athlete" under Swimming
  are independent states, each producing its own leaf — matches §34.2's
  "same Sub-Type under two Sports = two separate forms" rule. Each
  leaf (whichever of User Type/Sub-Type has no children) shows a
  "Configure Form" affordance instead of the form table appearing inline.
- **Right panel: Form Builder, on-demand and expandable.** Clicking a
  leaf's "Configure Form" opens a right-side drawer containing the
  existing field-toggle-table UI (Standard Field Attribute / Active in
  Form / Required Mandatory, from the built screenshot), plus a new
  **"Locked — set by Master Admin" section** listing that Sport's
  eligibility fields (DOB for Age, Gender, Weight, any custom criterion,
  per §34.3) above Industry's normal standard palette + custom fields.
  The drawer has an expand control to go full-width/full-screen — the
  one piece of Abhijeet's original proposal kept as-is, since it directly
  solves the cramped-space problem the new 3rd axis creates.

**Both decisions confirmed by Abhijeet (asked via AskUserQuestion, picked
the recommended option both times):** rail+panel over the canvas, and
auto-populated Sports rail over an explicit per-Sport add step.

## 35.3 Not yet designed

- Exact visual treatment distinguishing "Locked (MA-set)" fields from
  Industry's own fields in the Form Builder drawer (icon, section
  divider, color — not specified, just "visually distinct").
- Whether the existing "Field Config" / "Live Form Preview" toggles and
  "Publish Registration Schema" button (top bar in the screenshot) move,
  stay, or need Sport-scoping of their own (e.g. does Publish gate per
  Sport like Quota Setup Level 2, or once for the whole node like
  before) — not discussed, flag before finalizing the canvas rebuild.

---

# 36. Mode of Registration — granularity revised to a single project-wide toggle (2026-09-15)

## 36.1 Trigger

Walking through the Closed vs Open flow end-to-end with Abhijeet surfaced
that §33's original per-leaf placement was more granular than he actually
wants. His framing: Closed means Industry sets quota top-down and
Institutions register/invite into it — that flow is unchanged. Open means
participants can apply directly and Industry/a permitted Institution
selects from the applicant pool — but this is a **project-level identity**
("either the project is open or closed"), not a per-Sport or per-User-Type
switch. Setting "Athlete" Open under Cricket but Closed under Swimming
was never a real scenario he wanted; §33.5's granularity trade-off solved
a finer problem than the one he actually has.

## 36.2 Resolution — supersedes §33.2 and §33.5

**Mode of Registration becomes a single Open/Closed toggle for the whole
Project**, not a setting on the Registration Setup canvas at all. Every
registration entity in the project (every Sport × User Type/Sub-Type
leaf) follows the same Mode:

- **Closed** (default) — every leaf behaves exactly as today: Industry
  sets Level 1/Level 2 quota, Institutions register (Manual) or invite
  (Invite) into their allotment, then Nominate. No change from current
  behavior anywhere.
- **Open** — every leaf's quota becomes **Open Quota** (§9 of
  `atom-flow-builder-quota-setup.md` — Max Entries + Selection target,
  no Level 2), and every leaf's Registration & Nomination screen gains
  **Self-Register** as a third method alongside the still-available
  Invite and Manual (confirmed 2026-09-15 — Open does not remove
  Institution-initiated registration, it adds the public path on top).
  The Nomination step becomes **Selection**, performed by Industry or a
  permitted Institution on the flat pool of applicants (§9.4), same as
  before per-leaf but now uniformly across the whole project.

**What this drops from §33.5:** the per-User-Type/Sub-Type granularity
trade-off (and the finer per-Discipline alternative it was weighed
against) is no longer relevant — there is no granularity below Project
level anymore. Simpler than either option originally on the table.

## 36.3 Where the toggle lives — RESOLVED 2026-09-15: top of the Registration Setup node

**Confirmed by Abhijeet**, resolving the placement question left open
earlier the same day: the toggle stays a single Project-wide switch (one
value, no per-leaf granularity — §36.2 unchanged), but it's surfaced at
the **top of the Registration Setup node's canvas** — a control above
the 3-pane Sport rail / User Type panel / Form Builder drawer, not
per-leaf and not on Hierarchy. Rest of the node (§34/§35's tree and
canvas) is unaffected — this is purely one additional control at the
top of the same screen, not a new pane or a re-architecture.

## 36.4 Downstream sections this affects

- Registration Setup (§33, §35): the per-leaf Open/Closed toggle
  described in §33.2/§33.3 is **removed** from the canvas — Registration
  Setup goes back to being purely the Sport → User Type → Sub-Type →
  Joining Form tree from §34/§35, with no Mode control anywhere in it.
- Quota Setup (§9): "Open leaves" throughout that section now reads as
  "every leaf, when the Project is Open" — the Open Quota shape
  (Max Entries + Selection target, no Level 2) applies project-wide, not
  per-entry.
- Registration & Nomination (§2.8, §3): same substitution — Self-Register
  and Selection are either available everywhere in the project (Open) or
  nowhere (Closed), not mixed within one project.
- `atom-flow-builder-source-of-truth.md` §3.4 updated in place to point
  here.

**Still nothing committed to `atom-context.md`/`backlog.md`** — same
gating as the rest of this thread.
