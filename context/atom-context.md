# ATOM Context (Primary Architectural Source of Truth)

> Companion to the PRD. The PRD is the functional specification (what
> screens/features do). This document is the architectural and product
> philosophy layer — the decisions, constraints, and mental model that
> the PRD must remain consistent with. When the two appear to conflict,
> treat the PRD as functional truth and this document as architectural
> truth, and flag the conflict rather than silently picking one.

---

## 1. Product Vision

ATOM (Unified Sports Ecosystem) is a **modular sports operating system**,
not merely an event-management platform.

It connects every stakeholder in sports into one ecosystem:

- Individuals
- Institutions
- Industries
- Master Administration

It is built to serve Clubs, Academies, Universities, Schools,
Federations, Governments, and Event Organisers, and to solve
their recurring operational problems:

- Athlete management
- Day-to-day operations
- Registrations
- Scheduling
- Communication
- Assessments

The platform supports both continuous, day-to-day sports operations
(via a standing "Default Project") and large, time-bound multi-sport
events (via "Event Projects") through the same set of reusable
modules.

**Modules are independently-developed systems that integrate into
ATOM**, not features built inside the ATOM core — e.g. AMS (Athlete
Management System), TMS (Tournament Management System). Each is built
and versioned on its own and then plugged into a Project. See [§8
Modules](#8-modules) for the catalog and [§9 Business Model &
Monetization](#9-business-model--monetization) for how module choice
drives Industry billing.

---

## 2. Core Philosophy

**Every account starts as an Individual.** This is the root identity
of the system — Institutions and Industry access are things an
Individual acquires, not separate account types someone signs up as.

Identity lifecycle:

```
Individual
  → completes profile
  → Default (Individual) Workspace is created
  → may create an Institution profile
  → Master Admin may grant Industry access
  → user may end up owning multiple workspaces
  → Role Switcher moves between workspace contexts
```

Key invariants that follow from this:

- **Workspace context determines permissions.** What a user can do is
  a function of which workspace they are currently acting in, not a
  global property of their account.
- **No data leakage between workspaces.** Workspaces are strictly
  isolated from one another (see [§10 Security & Access
  Control](#10-security--access-control)).
- A single human can simultaneously be, e.g., an Individual applying
  to events, an admin of an Institution, and a member of an Industry
  team — each hat is a separate workspace context reached via the
  Role Switcher.

---

## 3. Architecture Overview

### 3.1 Primary Hierarchy

The system nests strictly top-down; nothing skips a level:

```
Master Admin
  └─ Industry
       └─ Project
            └─ Functional Area (FA)
                 └─ Sub Functional Area (Sub FA)
                      └─ Task Board
                           └─ Task
                                └─ executed by Individuals / Institutions
```

- **Industry** is the tenant-like top-level entity that owns Projects.
- **Institution** is a distinct entity type: it is created by an
  Individual, exists independently, and *participates inside* Industry
  Projects (typically by being assigned ownership of one or more
  Functional Areas). Institution is not a rung of the Master
  Admin→Industry ladder — it's a separate lineage that plugs into it.
- **Functional Area (FA)** is the operational unit of a Project:
  Institutions own and execute work at the FA (or Sub FA) level, not
  at the whole-Project level.

### 3.2 How the Hierarchy, Workspaces, and Access Control Relate

Three architectural layers work together and should not be conflated:

| Layer | Answers | Governed by |
|---|---|---|
| Primary Hierarchy (§3.1) | Who owns what, structurally | Master Admin → Industry → Project → FA → Sub FA |
| Onboarding (§5) / Workspace Architecture (§6) | How a workspace is acquired, and which context a user is acting in right now | Registration form, Role Switcher, Master Admin linking, workspace isolation |
| Access Control (§10) | What a user is allowed to do in that context | RBAC, PBAC, ABAC |

---

## 4. User Hierarchy & Identity Model

| Entity | Created by | Key capability |
|---|---|---|
| **Individual** | Self-registration (form) | Root identity; discovers Industries/Institutions; applies to Projects |
| **Institution** | An Individual, self-service via "Create Institution Profile" (form, instant) | Joins Projects; manages assigned FAs; invites members; creates Tasks |
| **Industry** | Master Admin only, after an out-of-band request from an Individual (no in-app form) | Creates and configures Projects; assigns Institutions and permissions |
| **Master Admin** | Platform-level | Global governance: module assignment, user governance, Industry governance |

See [§5 Onboarding & User Journey](#5-onboarding--user-journey) for the exact mechanics of each.

---

## 5. Onboarding & User Journey

Every onboarding path starts from the same root: registering as an
Individual. Institution and Industry are not separate sign-up types —
they are workspaces an already-registered Individual acquires, each
through a different mechanism.

### 5.1 Individual Onboarding

1. Register on ATOM by filling the registration form.
2. On successful registration, the **Individual workspace** is created
   automatically and added to the Role Switcher.
3. This Individual workspace is the **default** workspace shown when
   the user opens ATOM.
4. The user is now considered onboarded onto ATOM.
5. From the Individual workspace, the user can explore and apply to
   Industry Projects (see [§5.5 Participation
   Paths](#55-participation-paths)).

No approval gate exists at this step — registering *is* onboarding for
an Individual.

### 5.2 Institution Onboarding

1. Starting point: the user is already registered and onboarded as an
   Individual (§5.1).
2. The user opens the **Role Switcher** and selects **"Create
   Institution Profile."**
3. This opens the **Create Institution** form.
4. On submission, a new **Institution workspace** is added to the Role
   Switcher alongside the existing Individual workspace — the user
   now has two workspaces: Individual and Institution.
5. From the Institution workspace, the Institution can explore and
   apply to Industry Projects.

Institution workspace creation is **self-service and immediate** —
filling the form is sufficient; there is no separate approval step to
create the workspace itself. (Approval still applies later, at the
point an Institution is assigned to a Functional Area within a
specific Project — see [§11 Dependencies](#11-dependencies).)

### 5.3 Industry Onboarding

1. Starting point: the user is already registered as an Individual.
2. The user opens the Role Switcher and selects **"Create Industry."**
3. Unlike Institution, this does **not** open an in-app form. Instead,
   the user must contact the **Master Admin directly** (email or call)
   with their details and the modules they require.
4. The Master Admin creates the Industry from the Master Admin portal
   and **links it to the Individual account** that made the request.
5. Once linked, the **Industry workspace** becomes visible in that
   user's Role Switcher.
6. From the Industry workspace, the user can create and configure
   Projects.

Industry provisioning has **no self-service path** — it is always a
manual, out-of-band process performed by the Master Admin. This is a
deliberate gate: Industry is the tenant-owning entity, so its creation
is administratively controlled rather than instant like Individual and
Institution.

### 5.4 Post-Onboarding: Industry Project Journey

Once an Industry workspace exists, the journey continues into Project
execution:

1. Industry creates a Project.
2. Industry configures Functional Areas.
3. Industry configures User Types & Dynamic Forms.
4. Industry opens registration for the Project.
5. Industry invites users or issues a Command (see [§7.2 Registration
   Modes](#72-registration-modes)).
6. Institutions and Individuals apply to the Project, or the Industry
   invites them directly — see [§7.7 Project Joining
   Flow](#77-project-joining-flow) for the full mechanics of both
   paths, including User Type/Role (Individual) vs. Functional Area
   grants (Institution).
7. Industry approves applicants (apply path) or the invitee
   accepts/rejects (invite path).
8. Institution manages execution within its assigned FA(s); Individual
   operates under its assigned User Type/Role.
9. Tasks are executed.
10. Project is archived.

### 5.5 Participation Paths

An Individual or Institution can reach an Industry Project via any of
these paths; the Industry approves at every step regardless of path:

- Individual → Industry
- Individual → Institution → Industry
- Institution → Industry
- Institution → Institution → Industry

---

## 6. Workspace Architecture

Workspaces are the unit of context-switching and isolation. A single
user may own several; the Role Switcher moves between them.

| Workspace | Purpose |
|---|---|
| **Individual** | Personal profile; discover Industries/Institutions; apply to Projects |
| **Institution** | Institution profile; join Projects; manage assigned FAs; invite members; create Tasks |
| **Industry** | Create/configure Projects; assign Institutions; assign permissions; manage Project lifecycle |
| **Master Admin** | Global governance; module assignment; user governance; Industry governance |

**Workspaces are isolated** — no data leakage across workspace
boundaries, even for the same underlying user.

---

## 7. Project Architecture

### 7.1 Project Types & Lifecycle

- **Default Project** — internal operations; exactly one per Industry.
- **Event Project** — unlimited per Industry; time-bound (tournaments,
  camps, trials, etc.).

Lifecycle (both types): `Draft → Upcoming → Ongoing → Completed →
Cancelled/Archived`

### 7.2 Registration Modes

| Project setting | Modes available |
|---|---|
| Open | Self-registration, Invitation, Command |
| Closed | Invitation, Command |

**Command is a platform differentiator: a Command cannot be
rejected.** It is a stronger instrument than an Invitation, used where
the Industry needs to compel participation rather than solicit it.

### 7.3 Project Management Workflow

```
Industry creates Project
  → Configure Project
  → Configure Functional Areas
  → Configure User Types
  → Configure Dynamic Forms
  → Open Registration
  → Execution
  → Closure
```

### 7.4 Functional Areas

Functional Areas are configured per-Project and subdivide into Sub
Functional Areas, which contain Task Boards, which contain Tasks
(full nesting shown in [§3.1](#31-primary-hierarchy)). **Institutions
own execution inside their assigned FAs** — an Institution's authority
is scoped to the FA(s) it has been assigned, not the whole Project.

### 7.5 Progress Tracking (current decision — Phase 1)

Project progress is **not** derived from configuration completeness.
Instead:

```
Progress = Completed Functional Areas / Total Functional Areas
```

This is the Phase 1 implementation; more granular progress models are
out of scope until Phase 1 is validated.

### 7.6 User Types & Dynamic Forms

- Industry defines User Types, Registration Forms, and Validation
  Rules **per Project** — forms are project-specific, not global.
- Different Projects may require entirely different onboarding fields
  for what is nominally the same User Type.

### 7.7 Project Joining Flow

There are exactly two ways a participant ends up in a Project —
**apply**, or **get invited** — and both always resolve through an
Industry decision before participation is finalized. The mechanics
differ for Individual vs. Institution, and Industry can never be the
one joining.

#### 7.7.1 Individual — Way 1: Apply

1. From the Individual workspace, explore Industries and their
   Projects.
2. Review a Project and apply to it.
3. Applying requires selecting a **User Type** (Athlete, Coach, Support
   Staff, etc. — defined per-Project, see [§7.6](#76-user-types--dynamic-forms)).
4. Track the application's status on the **Requests & Invitations**
   page.
5. The Industry reviews the application and accepts or rejects it at
   its discretion.

#### 7.7.2 Individual — Way 2: Industry Invites

1. On the Project, the Industry clicks **Invite** for an Individual.
2. The Industry selects a **User Type** and a **Role** for the invite.
   These are two distinct axes:
   - **User Type** — functional classification (Athlete, Coach,
     Support Staff, etc.), same concept as in the Apply path.
   - **Role** — maps to permissions on the ATOM Project; this is what
     actually governs what the invitee can do once inside.
3. The Industry marks the invitee as **New** (not yet registered on
   ATOM) or **Existing**.
4. The invitation is sent to the invitee's email.
5. **New**: the individual registers on ATOM first (§5.1), then
   reviews and accepts/rejects the invite. **Existing**: the
   individual reviews and accepts/rejects the invite directly.

#### 7.7.3 Institution — Same Two Ways, Different Grant

Institution joins a Project through the identical two paths (apply, or
Industry invites), with two differences from Individual:

- Requests and invitations surface in the **Institution workspace**,
  not the Individual workspace.
- Instead of selecting a User Type + Role, the Industry selects a
  **Functional Area (FA)** to grant the Institution.

**Phase 1:** an Institution gets **full access** to its assigned FA —
there is no finer-grained role within the FA yet. **Later phases:**
Institutions will also get Roles scoped within their assigned FA(s),
mirroring the Role concept Individuals already have.

#### 7.7.4 Industry Cannot Join

Industry only ever **creates** Projects and **invites** participants
into them. **Industry can never join a Project as a participant** — it
has no apply/join path of its own; it is structurally always the
owning/inviting party, never the invitee.

### 7.8 Module-Surfaced Services (Later-Phase Participant View)

**Current/Phase 1 behavior:** a joined Individual or Institution sees
the **Project** itself as the thing they've joined.

**Planned later-phase change:** showing the raw Project to
Individuals/Institutions is considered the wrong mental model, because
a Project is an Industry-side management container, not something a
participant conceptually "attends." Instead, participants will see the
**Services** that the Project's assigned modules generate:

- A Project with **AMS** assigned generates **Training Programs** —
  the participant sees "Training Programs," not "Project X."
- A Project with **TMS** assigned generates **Tournaments** — the
  participant sees "Tournament Y," not the Project that contains it.

In other words, the Project remains the Industry's organizing unit
(hierarchy unchanged, §3.1), but the **participant-facing surface
shifts from Project → Module-generated Service**. A single Project can
surface multiple Services simultaneously if it has multiple modules
assigned (e.g. Training Programs from AMS *and* a Tournament from TMS
on the same Project). This is a UI/visibility change, not a hierarchy
change — Functional Area ownership, Task execution, and approvals
still work exactly as described in §7.7.

---

## 8. Modules

Modules are **assignable by Master Admin, subject to dependency
validation** (see [§11 Dependencies](#11-dependencies)) — a module
cannot be turned on for an Industry until its prerequisites are
already active.

### 8.1 Base Platform

Authentication · Workspace · Account · RBAC · PBAC · ABAC ·
Notifications · Audit · Settings · Organisation Management · Project
Management · Budget Tracking

**Budget Tracking** is a base-platform capability (Phase 1.5), **not** a
separately-assignable §8.2 module. It lets an Industry plan and track
**its own internal operating budget** for running Projects — distinct
from ATOM's B2B billing of the Industry (§9). See §8.5 for scope.

### 8.5 Budget Tracking (base-platform capability — Phase 1.5)

A lightweight, single-currency, **allocation-only** tracker that follows
the primary hierarchy (§3.1) one level at a time:
`Industry overall budget → Project allocation → Functional Area
allocation`. "FA" here means **Functional Area** (§15), not "Field
Agent."

**v1 scope — allocation only (no spend logging):**

1. **Industry Overall Budget** — Industry enters its `total_budget`
   (single Industry-level currency, optional label). Exactly one overall
   budget record per Industry.
2. **Overall Budget Dashboard (Industry)** — Total · Allocated
   (Σ Project allocations) · Unallocated. Per-Project row: allocated,
   % of total. Covers the Default Project and all Event Projects (§7.1).
3. **Project Budget Allocation** — Industry allocates a slice of the
   overall budget to an individual Project.
4. **Project → Functional Area Budget Allocation** — Industry allocates
   a Project's budget down to its configured Functional Areas. v1 stops
   at FA (no Sub-FA / Task Board / Task budgets).
5. **Project Budget Dashboard** — Project allocation · Allocated to FAs
   (Σ) · Unallocated. Per-FA row: allocated, %.

**Business rules:**

- **Over-allocation is a hard block**, at both levels: Σ Project
  allocations ≤ Industry total, and Σ FA allocations ≤ that Project's
  allocation. This is the only guardrail an allocation-only v1 needs.
- Reducing the Industry total below the amount already allocated to
  Projects is a hard block.
- Reducing a Project's allocation below the amount already allocated to
  its Functional Areas is also a hard block (same rule, one level down).
- Allocation is always a **parent-sets-child** action: the Industry sets
  Project and FA allocations; the FA-owning Institution does not set its
  own allocation.
- **Single currency**, set at the Industry level — no multi-currency.

**Spend tracking is out of scope for v1** — v1 records allocation only.
Logging actual spend against Functional Areas (with roll-up, Spent /
Remaining columns, and over-budget flagging) is a **v2** capability; its
open decisions (manual entry vs. auto-derive from the Expenses/Payments
modules per §8.2/§8.3; who logs spend; over-spend = allow + flag) are
deferred, not decided here.

Also out of scope for v1: approval/sign-off workflows, multi-currency,
invoicing/reconciliation, purchase orders, receipts/attachments,
forecasting/variance analytics, Sub-FA/Task-level budgets, and threshold
push-notifications.

### 8.2 Project Modules

Athlete Management · Roster · Training · Credential · Accreditation ·
Communication · Grievance · Certification · Membership · Inventory ·
Expenses · Contract · Payments · Task Management

### 8.3 Module Detail

**Organisation Management** — Industry onboarding, Institution
onboarding, CRM, relationship management, profiles, hierarchy, access,
user management, role management.

**Athlete Management** (current MVP) — athlete profiles, coach
management, batch management, training schedules, manual assessments.
*Future:* performance analytics, health, nutrition, injury management.

**Communication** — notifications, email, SMS, WhatsApp, forums,
announcements, chat.

**Grievance** — FA-based ownership, escalation matrix, TAT
(turnaround time), media attachments, notifications.

**Certification** — templates, coordinate mapping, bulk generation,
DigiLocker integration, verification.

**Credential & Accreditation** — credential generation, accreditation
cards, venue access, zone permissions.

### 8.4 Module Provenance

Some modules are built as **standalone systems under independent
development**, with their own acronyms, and then integrated into
ATOM rather than authored as native ATOM features:

- **AMS** — Athlete Management System (integrates as the Athlete
  Management module).
- **TMS** — Tournament Management System (integrates as the
  Tournament/event-execution module).

More such systems are expected to follow the same pattern: developed
independently, then integrated. When a module is assigned to a
Project, it is also what generates the participant-facing Services
described in [§7.8](#78-module-surfaced-services-later-phase-participant-view)
(e.g. AMS → Training Programs, TMS → Tournaments).

---

## 9. Business Model & Monetization

**Today: Industry is the paying client.** Billing is B2B — the
Industry is invoiced based on which modules it has selected/enabled
for its account (§8). Individuals and Institutions are not billed
under the current model.

**Do not confuse ATOM billing with the Industry's own budget.** The
Budget Tracking capability (§8.5) is the Industry planning and tracking
**its own operating budget** for running Projects; it is a base-platform
feature, available to every Industry, **revenue-neutral** (not a billing
SKU) and entirely separate from what the Industry pays ATOM for module
access.

**Later phase: Individual monetization.** A Pro Membership tier is
planned for Individuals, gated behind a purchase, that unlocks
cross-Project value an Individual can't get otherwise — the flagship
example is viewing their own **performance history tracked via AMS
across past Projects**. This is additive to the Industry billing
model, not a replacement for it: Industry billing funds module access
at the tenant level, Individual billing (later) funds premium
personal-analytics features layered on top.

Implications to keep in mind for any future design:

- Don't assume all monetization is B2B — the roadmap already commits
  to a B2C layer for Individuals.
- AMS is the first module named as a source of the Individual-facing
  premium feature (historical performance analytics) — expect other
  modules to eventually follow the same "aggregate an Individual's
  history across Projects" pattern.
- Module assignment (§8) is simultaneously a *feature* decision and a
  *billing* decision for the Industry — don't design module
  assignment UX/logic without accounting for its billing
  consequences.

---

## 10. Security & Access Control

- **RBAC** (Role-Based), **PBAC** (Policy-Based), **ABAC**
  (Attribute-Based) access control operate together — every feature
  proposal must respect all three, not just role checks.
- Isolation is enforced at three nested levels: **Workspace
  isolation**, **Project isolation**, **Functional Area isolation**.
- **Audit logs** capture actions across the platform.
- Never flatten permissions — do not propose a shortcut that collapses
  RBAC/PBAC/ABAC into a simpler single check.
- **Budget Tracking (§8.5) visibility** follows this rule too: new RBAC
  permissions (e.g. `Budget: View` / `Budget: Manage`) grant the
  *capability* to see/manage budgets and may be assigned to Institution
  or Individual roles — but Project isolation and Functional Area
  isolation still scope *which* budgets a viewer sees (an Institution
  sees its own assigned FA's allocation, not sibling FAs'). RBAC alone
  must never be the whole check. Do not grant budget visibility to
  participant User Types (e.g. Athletes) via a Project Role by default
  (§7.7.2).

---

## 11. Dependencies

Hard ordering constraints — none of these may be reordered or
short-circuited by a feature design:

- Cannot create an Industry without Master Admin — there is no
  self-service form; the Master Admin creates the Industry and links
  it to the requesting Individual.
- Cannot create a Project without an Industry.
- Cannot participate in the platform without an Individual profile —
  Institution and Industry are workspaces an Individual acquires, not
  independent sign-ups.
- Cannot assign a Functional Area before Institution approval. (Note:
  this approval gate applies to FA assignment within a Project, not to
  creating the Institution workspace itself, which is instant and
  self-service — see [§5.2](#52-institution-onboarding).)
- Cannot create Tasks before FA assignment.
- Cannot use a dependent module before its prerequisite modules are
  active.

---

## 12. Business Rules & Product Decisions

- Individual is always the root identity.
- The Workspace Switcher (Role Switcher) is central to the UX — not an
  edge-case control.
- Industry owns Projects; Institution owns execution within assigned
  FAs.
- Functional Area is the operational unit of a Project.
- Dynamic forms are project-specific, never global.
- Modules are dependency-driven and assigned by Master Admin.
- Exactly one Default Project per Industry; unlimited Event Projects.
- Command registration is supported and cannot be rejected.
- Project progress is based on completed Functional Areas (§7.5), not
  configuration completeness.
- Individual and Institution workspaces are created through in-app
  self-service forms with no approval gate; the Industry workspace is
  never self-service — it always requires a manual Master Admin
  action triggered by an out-of-band request (§5.3).
- A participant joins a Project either by applying (then Industry
  accepts/rejects) or by being invited by the Industry (then the
  participant accepts/rejects) — see [§7.7](#77-project-joining-flow).
- Individual grants are User Type + Role based; Institution grants are
  Functional-Area based and full-access in Phase 1 (no FA-scoped roles
  until a later phase).
- **Industry can never join a Project as a participant** — it is
  exclusively the Project-creating and inviting party.
- Modules (AMS, TMS, etc.) are independently-developed systems
  integrated into ATOM, not native ATOM features (§8.4).
- Industry is the current paying client; billing is driven by module
  selection. Individual monetization (Pro Membership) is a planned,
  additive later-phase layer, not a replacement (§9).
- In later phases, participants see module-generated Services
  (Training Programs, Tournaments, etc.), not the raw Project (§7.8).
- Budget Tracking (§8.5) is a base-platform capability (Phase 1.5), not
  a separately-assignable module; it tracks the Industry's own operating
  budget and is revenue-neutral (§9). v1 is **allocation-only** (no
  spend); over-allocation is a hard block; allocation is always
  parent-sets-child (Industry → Project → Functional Area); single
  currency. Spend tracking is v2.

---

## 13. Constraints & Naming Standards

Use these terms exactly; do not substitute synonyms in product
documentation, UI copy, or code without explicit instruction:

`Industry` · `Institution` · `Individual` · `Functional Area` ·
`Sub Functional Area` · `Workspace` · `Role Switcher`

**Do not replace "Industry" with "Organisation"** unless explicitly
instructed — this substitution has been explicitly rejected as a
naming direction.

---

## 14. Roadmap

### Phase 1 (current scope)

Core workspaces · Organisation Management · Project Management ·
Functional Areas · User Types · Dynamic Forms · Invitations · Join
Requests · Command · Tasks · Notifications · Audit · Module assignment

### Phase 1.5

Budget Tracking v1 (§8.5) — base-platform, allocation-only budget
tracker: Industry overall budget → Project allocation → Functional Area
allocation, with allocation dashboards at the Industry and Project
levels. No spend logging in this phase.

### Phase 2

Advanced workflows · Analytics · Automation · Performance dashboards ·
AI assistance · Advanced assessments · Institution Roles scoped within
assigned Functional Areas (§7.7.3) · Participant-facing shift from
Project view to module-generated Service view (§7.8) · Budget Tracking
v2 — spend logging against Functional Areas with roll-up and
Spent/Remaining/over-budget dashboards (§8.5)

### Later Phase (beyond Phase 2, not yet sequenced)

Individual Pro Membership — paid tier unlocking AMS-tracked
cross-Project performance history for Individuals (§9)

---

## 15. Glossary

| Term | Definition |
|---|---|
| **Individual** | The root account type every user starts as. |
| **Institution** | An entity created by an Individual that participates in Industry Projects and executes work within assigned Functional Areas. |
| **Industry** | The top-level tenant entity, granted by Master Admin, that owns and configures Projects. |
| **Master Admin** | Platform-level governance role: grants Industry access, assigns modules, governs users. |
| **Workspace** | An isolated context (Individual / Institution / Industry / Master Admin) that determines a user's current permissions. |
| **Role Switcher** | The UX mechanism for moving between a user's workspaces. |
| **Project** | A unit of work owned by an Industry; either the single per-Industry Default Project or one of many time-bound Event Projects. |
| **Functional Area (FA)** | A subdivision of a Project; the operational unit that Institutions are assigned to execute. |
| **Sub Functional Area** | A subdivision of a Functional Area, containing Task Boards and Tasks. |
| **Command** | A registration mode stronger than Invitation; cannot be rejected by the recipient. |
| **RBAC / PBAC / ABAC** | Role-, Policy-, and Attribute-Based Access Control — applied together, never singly, to every permission check. |
| **User Type** | A per-Project functional classification for an Individual applicant/invitee (e.g. Athlete, Coach, Support Staff). Defined by the Industry per Project (§7.6). |
| **Role (Project)** | The permission-mapping axis assigned to an Individual alongside User Type when invited to a Project — distinct from User Type, which is purely classificatory. |
| **Requests & Invitations page** | The page where an Individual (or Institution) tracks the status of Projects they've applied to and invitations they've received. |
| **AMS** | Athlete Management System — an independently-developed system that integrates into ATOM as the Athlete Management module; generates Training Programs as its participant-facing Service (§7.8). |
| **TMS** | Tournament Management System — an independently-developed system that integrates into ATOM as the Tournament module; generates Tournaments as its participant-facing Service (§7.8). |
| **Service (module-surfaced)** | A participant-facing deliverable generated by a module assigned to a Project (e.g. a Training Program from AMS, a Tournament from TMS). In later phases this — not the raw Project — is what Individuals/Institutions see (§7.8). |
| **Pro Membership** | A planned paid tier for Individuals (later phase) unlocking cross-Project value such as AMS-tracked performance history — distinct from Industry's module-based billing (§9). |
| **Budget Tracking** | A base-platform capability (Phase 1.5, §8.5) for an Industry to plan/track its own operating budget by allocating from an overall budget down to Projects and then to Functional Areas. v1 is allocation-only (no spend); revenue-neutral, distinct from ATOM's billing of the Industry (§9). |

---

## 16. AI Guidance

When working on ATOM:

- Treat the PRD as functional truth; treat this document as
  architectural truth.
- Preserve terminology exactly as defined in [§15
  Glossary](#15-glossary).
- Preserve the workspace architecture and its isolation guarantees.
- Validate against [§11 Dependencies](#11-dependencies) before
  proposing any new feature or workflow.
- Keep the Industry → Project → Functional Area hierarchy intact —
  never propose a workflow that lets a lower level bypass its parent.
- Never flatten RBAC/PBAC/ABAC into a simplified permission model.
- Every proposal must respect PBAC and ABAC, not just role checks.
