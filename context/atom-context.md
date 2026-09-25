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

**Master Admin as inviter.** Separately from Industry *creation* (which
remains the out-of-band process above), the Master Admin **does** have an
in-app path to **send participant invites**, using the same invite form
and mechanics as an Industry (§7.7.2 / §7.7.3, including the **Expires
on** deadline, §7.7.6). The only difference is a superset entry point: MA
first selects an **Industry**, then a **Project of that Industry**, after
which the rest of the form (User Type / Role or Functional Area,
New-or-Existing, email, Expires on) proceeds identically to the
Industry-initiated flow. This is **not** a separate flow — it is the
Industry invite form fronted by an Industry→Project selector.

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

**Institution → member invites** (the "invite members" capability above,
also named in §4) use the same invite lifecycle and the same **Expires
on** field as Industry invites (§7.7.6). This feature pins down only the
expiry behavior on that send form; it does not add other new form fields.

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
| Open | Join Request (Apply), Invitation, Command |
| Closed | Invitation, Command |

"Join Request (Apply)" is the same path documented as **Apply** in
[§7.7.1](#771-individual--way-1-apply) and listed as **Join Requests** in
the [§14 Roadmap](#14-roadmap) — one participant-initiated path under
three names; treat them as synonymous.

**Command is a platform differentiator: a Command cannot be rejected.**
It is a stronger instrument than an Invitation, used where the Industry
needs to compel participation rather than solicit it. "Cannot be
rejected" means the target joins **immediately and unconditionally** —
the join is never gated behind acceptance or form completion. See
[§7.7.5](#775-command--force-join-no-participant-decision) for the full
mechanics (membership states, the `P-COMMAND-OBLIGATION` policy,
deadline/lock behavior, issuer governance, and reversibility).

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

### 7.5 Progress Tracking (current decision — Phase 1.5)

Project progress is a **backend-computed, read-only percentage** — it is
**never manually set** — and it is **not** derived from configuration
completeness. The model is Task-completion-based:

```
Progress % = (Completed Tasks / Total Tasks) × 100
```

The unit counted is the **Task** — the leaf work item created and managed
on a Task Board (§3.1). (Some UI/spec copy calls these "Tickets"; Task is
the canonical term, §13.)

- **Authoritative Task status set.** A Task is always in exactly one of
  five statuses: **To Do · In Progress · Reopen · Completed · Cancelled.**
  Progress is computed purely from a Task's *current* status:
  - **Completed Tasks** = count of Tasks in status **Completed**.
  - **Total Tasks** = all Tasks **excluding** status **Cancelled** (i.e.
    To Do + In Progress + Reopen + Completed). Cancelled work is neither
    numerator nor denominator, so cancelling a Task cannot inflate
    progress and a fully-cancelled Board does not strand progress below
    100%.
  - **Reopen** counts toward Total but **not** toward Completed — it is
    an open bucket. This falls out naturally from "count by current
    status": moving a Task from Completed → Reopen removes it from the
    numerator while it stays in the denominator, so progress drops. No
    special-casing is needed.
- **Rounding.** The percentage is **rounded to the nearest integer, no
  decimal places** (e.g. `47%`).
- **Initial state.** A newly-created Project has zero Tasks, so
  Progress = **0%**. `0 / 0` is defined as **0%** — never NaN/undefined,
  and never 100%.
- **Live recompute.** Adding Tasks lowers the percentage until they are
  Completed (expected, not a bug); reopening a Completed Task lowers it;
  cancelling or deleting Tasks recomputes against the remaining
  non-Cancelled set. Only Tasks are counted — the hierarchy has no
  sub-task entity below a Task.
- **Flat computation.** Progress is computed across all of a Project's
  Tasks (spanning every FA / Sub FA / Task Board), not rolled up from
  FA-level completion.

**Surfaces (Phase 1.5):** (1) each Project card on the Industry's
Projects list; (2) the Project Overview page; (3) the Task Board page;
(4) the Industry Overview (Home) "ongoing projects" section — one
progress tracker per project.

**This supersedes the Phase 1 FA-based model** (`Completed Functional
Areas / Total Functional Areas`), which is retired. **FA-level progress
rollup is explicitly out of v1.5 scope** (considered and deferred).

**Access control (decision).** Project Progress does **not** get its own
RBAC permission in v1.5. Unlike Budget Tracking — which is money and is
*manageable*, and therefore earned explicit `Budget: View` / `Budget:
Manage` permissions (§8.5, §10) — Progress is a **read-only derived
display value** that always renders *inside* a page the viewer already
has route access to. Its visibility therefore **piggybacks on existing
page/role access**, not a new permission:
  - The Projects list card and the Industry Overview "ongoing projects"
    section are **Industry-workspace surfaces**; only the Industry sees
    them. Implicit — no permission needed.
  - On the **Task Board** and **Project Overview**, whoever can already
    open the page sees the number. Per the v1.5 decision, progress is
    rendered for the **Industry** and the **Institution** (which owns FA
    execution and works on the Task Board, §7.4), and is **not rendered
    on the Individual participant surface**. This is a UI-surface rule,
    not an RBAC permission — an Individual is simply not shown the
    progress element, rather than being blocked by a permission check.
  - §10 Project isolation and Functional Area isolation still implicitly
    scope *which* project's Tasks feed a viewer's number. Adding a
    dedicated `Progress: View` permission would add matrix noise for
    near-zero security value; revisit only if per-role progress toggling
    is wanted when Institution FA-scoped Roles land in Phase 2 (§7.7.3).

### 7.6 User Types & Dynamic Forms

- **User Types are drawn from a system-defined library** — the Industry
  *enables* them per Project (via Flow Builder's Registration Setup,
  §7.9.2), it does not author brand-new User Types. **Sub-Types**,
  **Joining Forms**, and validation rules are **project-specific**, never
  global.
- Different Projects may require entirely different onboarding fields for
  what is nominally the same User Type.
- In Flow Builder, Joining Forms are scoped to **Sport × User
  Type/Sub-Type** (§7.9.2); Master-Admin eligibility fields auto-lock on
  the form; validation is minimal-only for v1 (required + type
  constraint).

### 7.7 Project Joining Flow

A participant ends up in a Project one of three ways — **apply**, **get
invited**, or **be commanded** (§7.7.5). The first two are *solicited*
paths that resolve through a decision before participation is finalized:
the Industry accepts/rejects an application, or the invitee
accepts/rejects an invitation. **Command has no participant-side decision
at all** — it joins the target immediately and unconditionally
(§7.7.5), so it does not resolve through any accept/reject step. The
mechanics differ for Individual vs. Institution, and Industry can never
be the one joining.

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

The Industry may also set an optional **Expires on** deadline on the
invite, after which it can no longer be accepted (§7.7.6).

#### 7.7.3 Institution — Same Two Ways, Different Grant

Institution joins a Project through the identical two paths (apply, or
Industry invites), with two differences from Individual:

- Requests and invitations surface in the **Institution workspace**,
  not the Individual workspace.
- Instead of selecting a User Type + Role, the Industry selects a
  **Functional Area (FA)** to grant the Institution.

The optional **Expires on** deadline (§7.7.6) applies to the Institution
invite too, on the Institution-workspace send form.

**Phase 1:** an Institution gets **full access** to its assigned FA —
there is no finer-grained role within the FA yet. **Later phases:**
Institutions will also get Roles scoped within their assigned FA(s),
mirroring the Role concept Individuals already have.

#### 7.7.4 Industry Cannot Join

Industry only ever **creates** Projects and **invites** participants
into them. **Industry can never join a Project as a participant** — it
has no apply/join path of its own; it is structurally always the
owning/inviting party, never the invitee.

#### 7.7.5 Command — Force-Join (No Participant Decision)

Command is the third way into a Project (§7.2) and the only one with no
participant-side accept/reject step. Where Apply resolves through an
Industry decision and Invite through the invitee's decision, **Command
joins the target immediately and unconditionally** — "cannot be
rejected" (§7.2, §12) means exactly this: the join is never gated behind
any acceptance or form completion. Gating the join behind a form would
recreate a silent-reject loophole, and is therefore explicitly
disallowed.

**Scope.** Command may target both **Individuals** and **Institutions**:
an Industry can force-join an Individual, or force-attach an
Institution's Functional Area grant, with no accept/reject step. The
grant axes are identical to the invite paths — User Type + Role for an
Individual (§7.7.2), a Functional Area grant for an Institution
(§7.7.3) — minus the participant decision.

**Target restriction.** Command may only target an Individual or
Institution **already registered/present on ATOM**. There is no path to
Command an unregistered ("New", §7.7.2) person or org onto the platform
— that is an explicit non-goal for v1. (Invite still supports New via
email; Command does not.)

**Membership states.** A commanded member is a real, counted member from
the moment of Command — counted toward membership/quota totals like any
other member. (This is loose usage of "roster" as in "headcount," not a
reference to the formal Roster Management object, §8.6.)
Two states:

- **Active** — no outstanding data obligation; a full member
  immediately.
- **Active — Details Pending** — the same real, counted membership, but
  with an outstanding data/form obligation flagged (the Project's Dynamic
  Form for that User Type/FA, §7.6, was not completed at Command time).

**Obligation enforcement (ABAC, narrow-only).** A Details-Pending member
is governed by policy **`P-COMMAND-OBLIGATION`** (ABAC, §10), which may
only *narrow* an existing grant, never widen it (narrow-only invariant):

- **Before the deadline:** the policy narrows *only* the specific actions
  that depend on the missing data — e.g. cannot be marked
  present/attendance, cannot compete/participate in a scheduled activity,
  cannot be issued a credential or accreditation (§8.3). Everything else
  about normal membership stays intact — the member can still view the
  Project/Service, see schedules, etc.
- **No auto-assigned Tasks.** A Details-Pending member is **not**
  auto-assigned Tasks, so a stalled obligation cannot corrupt the §7.5
  progress denominator with a stuck Task.
- **On deadline breach:** the same policy automatically tightens its own
  narrowing to a **full lock** — the member can take no action in the
  Project until the form is completed. This is purely automated (the
  policy narrowing further over time), with **no escalation to any other
  party** — no parent Institution, no issuing Industry, no
  human-in-the-loop step. The affected party is notified when the deadline
  passes and the lock engages (§8.1 Notifications).
- Completing the form clears the obligation: the member returns to
  **Active** and `P-COMMAND-OBLIGATION` stops narrowing.

**Governance — who can issue Command.**

- **Industry** and **Institution** roles hold Command-issue capability by
  default — no extra permission grant needed.
- An **Individual-type** actor (e.g. an elevated staff/coach role inside
  an org) may issue Command only with an explicit RBAC permission
  (`Command: Issue`) — off by default for Individual-type roles.

**Org-side visibility.** Command has its own management surface, parallel
to the Invitations and Join Requests pages (§7.7, §15): it lists every
commanded member/institution, their state (Active vs Details Pending),
and how long they have been pending. Status is also reflected on the
affected party's own **Requests & Invitations** page (§15), consistent
with that page's role as the general status surface (user-management
decision D12).

**Reversibility.** Command is not irreversible for the org. A commanded
member who never complies can be **Removed** by the org through the
existing Remove capability (user-management decision B9): non-rejectability
binds the *recipient*, not the *issuer*, so the Industry (whole-Project)
or the owning Institution (own-branch) may rescind. No new mechanism is
needed — this already works today.

**Audit (§10).** Every Command issuance, every state transition (Active →
Details Pending → locked → completed), and every Remove of a commanded
member is written to the Audit log.

#### 7.7.6 Invite Lifecycle & Expiry (Expires on)

All four solicited invite types share one explicit lifecycle:
Industry→Individual (§7.7.2), Industry→Institution (§7.7.3),
Institution→member (§4/§6), and the Flow Builder Registration Invite
(§7.9.8) — plus Master Admin invites, which are the Industry invite form
fronted by an Industry→Project selector (§5.3). Earlier canon only
implied invite states; they are now enumerated here. (Command, §7.7.5, is
**not** an invite and has no lifecycle here — it cannot be rejected and
carries no accept/reject step, so expiry is meaningless for it.)

**Invite states.** An invite is always in exactly one of four states:

- **Pending** — sent, awaiting the invitee's decision.
- **Accepted** — invitee accepted; participation finalized (terminal).
- **Rejected** — invitee declined (terminal).
- **Expired** — the deadline passed before acceptance (terminal; new with
  this feature).

**"Expires on" field.** Every invite send form carries an optional
**Expires on** field. The label is deliberately **not** "Deadline" —
that word is reserved for Command's `P-COMMAND-OBLIGATION` obligation
deadline (§7.7.5) — nor "window" (Nomination, §7.9.8). Canonically the
value is stored as an absolute UTC timestamp (`expires_at`); the sender
enters it as an absolute date, optionally via quick-pick shortcuts
(7 / 14 / 30 days) that resolve to and display an absolute date before
send (matching ATOM's only existing date-field pattern — Eligibility
cutoff §7.9.4, Command deadline §7.7.5 — all absolute, never relative
durations). **A blank Expires on = never expires** — the
backward-compatible default, and the state of every invite created before
this feature (no backfill, no retroactive expiry of in-flight invites).

**Field copy & calendar UI.** Placeholder text: "Select a date" (or
"Select a date — leave blank for no expiry" to surface the null-default
inline). The date picker's minimum selectable date is **today** — only
genuinely past dates are disabled — resolving to an end-of-day expiry per
the Timezone rule below; this stays literally consistent with the
New-invitee no-guard decision below rather than quietly imposing a
1-day-minimum lead time on every invite via the calendar widget. An info
("i") tooltip beside the field reads: *"Sets the last date this invite
can be accepted. After this date, it automatically expires and can no
longer be used. Leave blank for an invite that never expires. Time cuts
off at end of day in your Project/Industry timezone."* When the invitee
is New/unregistered (§7.7.2), a second tooltip line nudges the sender:
*"This invitee isn't registered yet — they'll need to sign up before
they can accept, so make sure the deadline gives them enough time."*

**Timezone.** `expires_at` cuts at **end-of-day in the Project/Industry
timezone** — the sender owns the deadline, not the invitee's timezone.
Stored UTC, rendered in the viewer's locale.

**Accept-time guard.** Accept succeeds **only if `now < expires_at`**,
evaluated against the server timestamp at the moment of the accept
action. An invitee opening a stale link/email after expiry gets a clean
"this invite has expired" screen, never a join — even before the
scheduled sweep flips the row to Expired. This rides the same
server-side time-trigger that auto-locks a Details-Pending commanded
member (§7.7.5); no new scheduler is introduced.

**New invitees — no guard (deliberate).** A **New** (unregistered)
invitee (§7.7.2) is treated identically to an Existing one: no minimum
lead time, no warning. Because a New invitee must register (§5.1) before
accepting, a short deadline can be mathematically impossible to fulfil —
this is a **knowingly accepted v1 risk**, not an omission, and there is
no minimum-lead-time check.

**Edit while Pending; no resend.** The sender may **extend or shorten** a
still-Pending invite's Expires on (RBAC: the same role that can send that
invite type, §10). Shortening is safe precisely because of the
accept-time guard — it cannot retroactively invalidate an accept already
in flight. A **terminal** invite (Accepted / Rejected / Expired) cannot
be edited. **There is no resend/revive in v1:** to re-offer, the sender
creates a brand-new, independent invite; the old one stays in its
terminal state for audit.

**Status surface.** On the Requests & Invitations page (§15) — the
invitee's/Institution's status view — each invite row shows `expires_at`
and a live countdown ("expires in Xd"), rows **sort** by soonest-expiring
and **filter** by status including **Expired**. An expired row **does not
disappear** — it greys out and drops its accept action, preserving the
invitee's record of what was offered. The sender's own sent-invite
management view mirrors this so the sender sees what lapsed.

**Notifications.** A **24h-before-expiry reminder** fires to the invitee,
reusing the existing 24h-before-deadline notification mechanism (the same
one behind the Command obligation deadline, §7.7.5; §8.1, with richer
email/SMS/WhatsApp when the Communication module §8.3 is assigned). At the
**expiry moment**, a notification fires to **both** the invitee ("this
invite has expired") and the sender ("your invite to X expired, not
accepted") — the sender needs the signal, and for §7.9.8 the quota slot
auto-releases.

**Audit (§10).** Setting an Expires on, editing it (old→new), and the
auto-expiry transition are all written to the Audit log, consistent with
§7.7.5's every-transition-audited pattern.

**Out of v1 scope.** There is **no** org-level default or enforced-max
deadline policy — the sender picks `expires_at` per invite only, or
leaves it blank. There is **no** symmetric expiry on Apply / Join
Requests (§7.7.1) — whether an application should carry a deadline the
Industry must act within is explicitly deferred to a future ideation,
neither adopted nor rejected here.

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

### 7.9 Flow Builder — Registration & Quota Setup

Flow Builder is the Industry's Project-configuration tool. It has two
top-level tabs: **Hierarchy Setup** (Functional Areas / Institutions —
covered by §3.1, §7.4) and **Registration & Quota Setup**. This section
documents the latter, which contains **four fixed, system-defined nodes**
run in order (users cannot add, delete, or reorder them):

```
Sports Setup → Registration Setup → Quota Setup → Registration & Nomination
```

Each node has three states: **Pending → Configured → Published**. A
node's published output is the input to the next. Configure and Publish
are separate RBAC permissions; an Institution without Publish permission
submits its configuration for Industry review (Approve → Published,
Reject → Configured) — approval is permission-based, not mandatory.

Terminology: throughout Flow Builder the competition leaf is the
**Discipline** (§7.9.1). Earlier drafts used "Event"; "Discipline" is
now canonical here.

#### 7.9.1 Sports Setup & the Master-Admin Sports Library

The **sports taxonomy is owned centrally by Master Admin**, two tiers:

```
Sport → Discipline   (Discipline is the leaf; no Category or Event level)
```

A **Discipline** (e.g. "Cricket Mens Senior") carries Master-Admin-owned
attributes:

- **Gender** — Men / Women / Mixed.
- **Type** — Individual / Double / Team.
- **Playing-member count** — a fixed number (Cricket = 11, any Doubles =
  2). Squad size is Master Admin's; the Industry never sets it.
- **Eligibility criteria** — extensible, attached per Discipline (§7.9.4).

Master Admin maintains a **system-seeded, extendable catalog** and
**assigns a subset to each Industry** ("My Sports Library"), mirroring
the module-assignment delegation pattern (§8, §11).

**Sports Setup (the Industry-facing node) is enable-only.** It shows a
two-tier catalog scoped to only what Master Admin assigned to that
Industry, grouped by Sport, searchable. Enabling a Discipline for a
Project is a **toggle** — there is no create/edit/name affordance on the
Industry side. Canvas: a "My Sports Library" left panel + a read-only
2-level tree (dropping a Discipline = Enable, removing = Disable) + a
read-only right config panel.

**Scope boundary (explicit, not a gap):** the Master Admin Sports
Library *authoring* surface (creating/editing Sports & Disciplines,
entitlement-to-Industry mechanics), and RBAC/versioning behavior when
Master Admin edits a Discipline already live in Projects, are **out of
scope for Flow Builder** — Flow Builder designs only the Industry-facing
enable-only consumption side.

This supersedes an earlier (2026-08-18) intermediate "free-text
authoring" design in which the Industry named Sports/Categories/Events
itself; the final state is enable-only over a Master-Admin-owned catalog.

#### 7.9.2 Registration Setup

Registration Setup defines who can join and what they must submit. Its
tree carries a **Sport axis**:

```
Sport → User Type → Sub-Type → Joining Form
```

(A User Type with no Sub-Type attaches its Joining Form directly.)

- **User Types** are drawn from a **system-defined library** — the
  Industry enables them (drag/toggle), it cannot author brand-new User
  Types. **Sub-Types** are project-specific (select existing or create).
- **Granularity is Sport, not Discipline.** One Joining Form exists per
  **Sport × User Type/Sub-Type**. The same Sub-Type reused under two
  Sports (e.g. "Athlete" under Swimming and Boxing) needs **two separate
  forms** — accepted cost.
- The form captures the **union** of every Eligibility criterion any of
  that Sport's Disciplines defines (e.g. one "Weight" field on a Boxing
  form even though weight-class Disciplines set different bands).
  Evaluation happens **per Discipline** downstream at Quota Setup
  Eligibility (§7.9.4) — "capture once, evaluate per Discipline."
- **Master-Admin-set eligibility fields are auto-added and locked** on
  the form (non-removable). Underneath the locked section sit the
  Industry's standard field palette (Name, DOB, Gender, Contact Number,
  ID/Aadhaar Number, Photo — locked set for v1) plus Industry custom
  fields, for non-eligibility data.
- **Validation is minimal-only for v1** — required + type constraint only
  (no regex / min-max / uniqueness). *Accepted v1 risk:* Nomination's
  "Search Existing User" cannot rely on form-enforced dedup.

Canvas: a **3-pane** rail + panel + drawer — Sports rail (auto-populated
from Sports Setup, no manual add step) + per-Sport User Type/Sub-Type
panel + an on-demand, expandable Form Builder drawer with a distinct
"Locked — set by Master Admin" section for that Sport's eligibility
fields. (A free-form drag-to-connect canvas was considered and
rejected.) Publish is **node-wide**; per-Sport Publish gating was
considered and deferred (backlog).

#### 7.9.3 Mode of Registration (Open vs Closed)

**Mode of Registration** is a **single Project-wide toggle** surfaced at
the top of the Registration Setup node — one value for the whole
Project, **not** per Sport, per User Type, or per Discipline. Default
**Closed**.

- **Closed** (default; unchanged from today's model): registration is
  Institution-initiated only — **Invite** + **Manual**. Industry sets
  quota top-down (Eligibility → Level 1 → Level 2, §7.9.4–7.9.6);
  Institutions register/invite into their allotment, then Nominate.
- **Open**: adds **Self-Register** (an Individual submits the Joining
  Form directly, no Institution action or approval) on top of the
  still-available Invite + Manual. Every leaf's quota becomes **Open
  Quota** (§7.9.7) and Nomination becomes **Selection** (§7.9.8).

An earlier design placed this per-leaf; it is now a single Project-wide
switch.

#### 7.9.4 Quota Setup — Eligibility (stage 1)

Quota Setup runs in **three sequential stages** of equal visual weight:

```
Closed:  Eligibility → Level 1 (Overall Quota) → Level 2 (Contingent Allocation)
Open:    Eligibility → Open Quota → Selection
```

There is no Level 3.

**Eligibility** is **fully Master-Admin-owned** (authored on the
Discipline, §7.9.1). On the Industry-facing Quota Setup screen it is a
**pure read-only echo** — the Industry authors nothing here; there is no
"+Add Eligibility Rule" flow. Every Level 1 / Open Quota entry row shows
an Eligibility **summary badge** (visible, not hidden behind a click).

Criteria:

- **Age** — never a raw validated field; derived from **DOB + a
  configurable cutoff/reference date**. The Industry sets the
  cutoff/reference date **per entry**, with **no Project-level default**
  (the correct cutoff genuinely varies by Discipline). Min/Max age each
  optional.
- **Gender** — read-only echo of the Discipline's own Gender attribute
  (Men/Women/Mixed); no per-User-Type override.
- **Custom criteria** — extensible (license/ID, qualifying rank/time,
  residency, Weight, etc.), Master-Admin-authored on the Discipline,
  same label+type pattern as Form Builder.

Enforcement:

- **Scope** — gates **all** registration methods (Manual, Invite, and
  Self-Register), not just Open/Self-Register.
- **Mechanism** — **fully automatic**: the system checks against whatever
  Master Admin defined; no Industry toggle, no manual review path.
- **Timing** — fires at whichever step first ties the person to a
  specific Discipline: at **Registration** for Team and
  Sport-granularity-only entries (already Discipline-scoped, no
  Nomination); at **Nomination** for Event-split Individual entries
  (Registration is only a Sport-level pool; Discipline isn't picked until
  Nomination).
- **Team role-slot scope** — one rule per Discipline × User Type/Sub-Type
  entry, applied uniformly to every slot of that role (all Playing and
  Reserve slots share it); no per-slot variation.

*Open items:* the enforcement **mode** for custom criteria (hard-block
vs informational-flag) is not yet decided; and the **wiring** that turns
"Master Admin adds a custom criterion" into "a matching capture field
appears on the relevant Joining Form(s)" is not yet designed — §7.9.2
states the intended auto-lock behavior, but the generation mechanism is
open (both in backlog).

#### 7.9.5 Quota Setup — Level 1 (Overall Quota)

Level 1 sets the total ceiling for a **Discipline × User Type/Sub-Type**
(the authoritative body's ceiling, e.g. GTCC for a National Games).
Entries are created via a guided **"+Add Quota"** flow (User Type →
granularity → Discipline) then edited **inline with autosave**. Fields
vary by Discipline Type:

- **Individual** — Max / Min / Reserve (flat, per entry). Unchanged in
  shape from prior canon, now keyed to Discipline.
- **Double** — Max Pairs / Min Pairs / Reserve Pairs; a single Sub-Type
  per pair (mixed-Sub-Type pairs out of scope); pair size fixed at 2 by
  Master Admin; Reserve flat/untagged.
- **Team** — Max Team + Min Team (team counts) + a **Sub-Type breakdown
  of the Master-Admin-fixed playing total that must sum exactly** (e.g.
  5 Batsman / 4 Bowler / 2 All-Rounder = 11) + Max/Min Reserve per team,
  also Sub-Type-tagged. **No squad-size / "Team Members" field exists in
  the Industry flow** — the playing total is Master Admin's.

Who configures: Industry/Master Admin always; an Institution (e.g. GTCC)
can be given **additive** delegated access (not an exclusive handoff).
Publish gate: every enabled Discipline leaf has ≥1 quota entry.

#### 7.9.6 Quota Setup — Level 2 (Contingent Allocation)

Level 2 distributes each Level 1 ceiling across contingent Institutions.
Delegation is **per Sport** (federations own a whole sport), though the
allocation work happens per Discipline. Institutions are added
explicitly per entry ("+Add Institution"), not pre-populated.

- **Individual / Double** — per Institution `Q` (allocated) and `R`
  (reserve); hard-blocked at `Σ Q ≤ Max`, `Σ R ≤ Reserve`.
- **Team** — `Q` only = number of teams per Institution, hard-blocked at
  `Σ Q ≤ Max Team`. **No R column and no per-Institution role breakdown**
  — Level 1's playing breakdown + role-tagged Reserve is a fixed template
  that travels automatically to every team any Institution fields. Level
  2 decides team **count** only.

Publish: per sport, gated on `Σ Q ≥ Min` on every entry in that sport (no
override/waiver); the node auto-rolls up to Published once every sport
publishes. Registration & Nomination (§7.9.8) unlocks only on that full
rollup, not per sport. *The Level 2 Min-enforcement mechanism is
inferred / pending explicit confirmation* — see backlog.

#### 7.9.7 Open Quota (Open-mode Projects)

When the Project is **Open** (§7.9.3), every entry uses **Open Quota**
instead of Level 1/Level 2:

- **Max Entries (Registration Cap)** — the one genuinely new field;
  Self-Register hard-blocks against it. The pool is deliberately allowed
  to be **oversubscribed** above the Selection target (the core
  structural difference from Closed's exact-match `Σ (Q+R)`).
- **Selection target** — reuses Level 1's Max/Min/Reserve shape by
  Discipline Type; no new field shape. `Max Entries ≥ Selection target`
  is expected, not enforced equal.
- **No Level 2** — there is no Institution to distribute across; the pool
  is flat.
- **Selection** replaces Nomination: run once on the flat self-registered
  pool by Industry or a permitted Institution (additive delegation),
  reusing Nomination's Main/Reserve drag-and-drop, hard-block-at-ceiling,
  and draft-until-exact-fill-then-Publish lifecycle. Excess above target
  becomes a Reserve/waitlist tier (not a hard cut) — inherits the same
  withdrawal-brittleness gap as Closed (backlog).
- **Team under Open** — individuals self-register into a Team-type
  Discipline (tagged by Sub-Type); Selection distributes them across up
  to Max Team teams' Level 1 role-slot template. Not a team-count-based
  cap.

#### 7.9.8 Registration & Nomination

The final node unlocks on Quota Setup's full Level 2 rollup. A contingent
Institution does two things in sequence.

**Registration** — the contingent onboards users against its Level 2
allocation:

- **Granularity:** a **Sport-level pool** for Event-split Individual
  quota (e.g. a 30-person Athletics pool later split by Nomination);
  **direct Discipline-level** registration where no Nomination follows —
  Team (role-slot filling, below) and Sport-granularity-only entries
  (e.g. Coaches).
- **Cap:** `Σ (Q + R)` per contingent per sport (covers Main + Reserve
  pool capacity).
- **Methods:** **Manual** (contingent fills the leaf form + user gets an
  emailed password-reset link) and **Invite** (reuses ATOM's existing
  invite/approve mechanism). **Self-Register** is added only when the
  Project is Open (§7.9.3). An **Invite** carries the **Expires on**
  deadline (§7.7.6) and consumes a slot against `Σ (Q + R)`; **an expired
  Registration Invite automatically releases its reserved slot back to the
  pool** — no manual reclaim, no silent quota stranding.
- **Team mechanic (role-slot):** the contingent creates up to `Q` team
  instances ("+Add Team"), each filled slot-by-slot against the fixed
  Level 1 role/reserve template (e.g. Batsman Playing ×5 / Reserve ×1),
  hard-blocked per role. **Team never goes through Nomination.**
- **Multi-sport reuse:** the same person/User Type is added to a second
  sport's pool via "Search Existing User" — no re-registration. (An
  Individual keeps exactly one User Type per Project — existing ATOM
  rule.) **Mixed User Types on one person in one Project is out of scope
  for v1** (deferred to v2, backlog).

**Nomination** — Individual-only (Team never reaches it):

- Drag-and-drop: registered individuals (left panel, only from
  Event-split/Discipline-split User Types) onto **Discipline boxes**
  (right panel).
- Each Discipline box has **two zones, Main (Q) and Reserve (R)**, both
  filled with **real named individuals** and hard-blocked at ceiling.
- **Multi-Discipline nomination allowed, uncapped in v1** (a
  per-individual cap is deferred to Flow Builder Phase 2, backlog).
- Status is **draft until the contingent Publishes** (drag-back-out
  allowed before that); **Publish requires every Discipline's Main AND
  Reserve zone to be exactly full** — no partial publish. **No deadline
  system exists yet.**

*Accepted v1 risk:* exact-match registration/nomination (Closed and
Open, Individual and Team) is brittle against withdrawals/replacements —
no reopen/swap/deadline mechanism defined (backlog). *Partially
mitigated:* invite expiry now self-heals the unclaimed-slot case — a
Registration Invite that is never accepted auto-releases its `Σ (Q + R)`
slot on expiry (§7.7.6), so that path no longer requires a manual
replacement.

*Phase 2, gated on resolving the above:* Publishing a Nomination or
completing a Team instance auto-creates a matching Roster Management
(§8.6) object, tagged with the Discipline, snapshotted at Publish (not
live-synced), with Main/Reserve or role-slot status carried as a
per-member attribute — letting AMS/TMS/Credential/Accreditation
reference the same squad instead of each re-deriving it. Deliberately
not attempted in v1: a live link would let an in-flight withdrawal/
reserve-promotion silently change a roster another module is already
running against, which is exactly the unresolved case above.

#### 7.9.9 Registration Status Dashboard

A **flat, filterable table** (deliberately not the Sport-first rail the
other nodes use, because its job is cross-sport listing): one row per
**individual × Discipline** — columns Name, User Type/Sub-Type, Sport,
Discipline, Institution, Status (Main/Reserve); filters on Sport,
Discipline, User Type, Status, plus name/ID search. Read-only; no node
gates on it.

**Role scoping is row-level, not menu-level:** Master Admin / Industry
see the Institution column and unrestricted rows; a contingent
Institution sees the same table with the Institution column dropped and
rows pre-filtered to its own people — still spanning **all** its sports
at once, not one sport at a time.

---

## 8. Modules

Modules are **assignable by Master Admin, subject to dependency
validation** (see [§11 Dependencies](#11-dependencies)) — a module
cannot be turned on for an Industry until its prerequisites are
already active.

### 8.1 Base Platform

Authentication · Workspace · Account · RBAC · PBAC · ABAC ·
Notifications · Audit · Settings · Organisation Management · Project
Management · Budget Tracking · Roster Management

**Budget Tracking** is a base-platform capability (Phase 1.5), **not** a
separately-assignable §8.2 module. It lets an Industry plan and track
**its own internal operating budget** for running Projects — distinct
from ATOM's B2B billing of the Industry (§9). See §8.5 for scope.

**Roster Management** is a base-platform capability (Phase 1.5), **not**
a separately-assignable §8.2 module — bundled into standard Industry
billing like Budget Tracking, not a billing SKU. It gives modules (AMS,
TMS, Credential, Accreditation) a shared, Project-level squad of
Individual members to reference by `roster_id` instead of each building
its own. See §8.6 for scope.

**Invite expiry notifications** reuse this base Notifications capability:
a **24h-before-expiry reminder** to the invitee — the *same*
24h-before-deadline mechanism already used for the Command obligation
deadline (§7.7.5) — plus an **expiry-moment** notification to both the
invitee and the sender (§7.7.6). Richer email/SMS/WhatsApp delivery
applies when the Communication module (§8.3) is assigned.

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

### 8.6 Roster Management (base-platform capability — Phase 1.5)

A durable, **Project-level**, cross-module-shareable grouping of
Individual members that modules (AMS, TMS, Credential, Accreditation,
Training) reference by `roster_id` instead of building their own.
Structurally parallel to Budget Tracking (§8.5): base-platform, not a
separately-assignable §8.2 module, bundled into standard Industry
billing (§9).

**Pages:**

1. **Roster Management** — Project-level page, Industry/Institution-side,
   sitting between Task Board and User Management. Three views: **List**
   (filterable by Sport/Discipline only — the sole scoping tag; rosters
   carry **no Functional Area tag**), **Create/Edit** (name, optional
   Sport/Discipline tag, co-member-visibility toggle, member picker), and
   **Detail** (member list, per-member status once Phase 2 lands, below;
   history).
2. **My Rosters** — Individual-workspace-level page (not a Project tab),
   ships in the same phase. Read-only list of rosters the Individual
   belongs to, with their own status. Converges into the §7.8
   module-surfaced Service view once that ships, as a presentation-layer
   relocation of the same data — not gated on §7.8, which has no
   committed timeline.

**v1 scope:**

1. **Creation:** Industry (whole-Project, any FA) and FA-scoped
   Institution (own FA-granted population only) by default, mirroring
   `Command: Issue`'s default-on/off split (§7.7.5). An Individual only
   via an explicit `Roster: Create` Role grant (§7.7.2).
2. **Membership: Individuals only**, never Institutions. A roster is a
   squad of real people for an activity (training, competition,
   credentialing); an Institution participates as creator/owner or via
   its own Individuals' Project membership, not as a roster member
   itself.
3. **Adding a member** is bounded by the D5 parent-FA-population
   guardrail (`2026-07-25-sub-fa-access.md`) regardless of the roster
   carrying no FA tag: a creator can only ever see and pick from their
   own already-granted population. This is enforced by *who the creator
   can see*, not by a stored field on the roster.
4. **No Functional-Area scoping on the roster object.** Rosters are
   Project-level, not FA-tagged; Sport/Discipline is the sole optional
   tag and the sole filter on the list/picker.
5. **Visibility (list/view — distinct from the add-guardrail in #3):** a
   roster is visible to its creator; the Industry, always; the roster's
   own members; and **the creator's own Institution, for every roster
   any of its own members create** (oversight of its own branch,
   symmetric with the D5 guardrail — never crosses into a sibling
   Institution's branch, and does not flow the other direction: an
   Institution's own roster is not automatically visible to Industry-
   created rosters it happens to be relevant to — see the known v1 gap
   below).
6. **Co-member visibility** within a roster: default-private, per-roster
   creator opt-in. No minor-athlete-specific override in v1 — a
   narrow-only ABAC policy `P-ROSTER-VISIBILITY` (§10).
7. **Membership removal:** a Project member removed from the Project
   (§7.7.5) is auto-removed from every roster they're on, audited.
8. **Orphan handling:** if a roster's creator is removed from the
   Project, ownership escalates up the existing oversight chain —
   Individual creator → their Institution if still present → Industry —
   never deleted, never silently archived (would strand every consuming
   module's `roster_id`); the transfer is audited and the new owner
   notified (Notifications, §8.1).
9. **Rostering is never an admission path** — every member must already
   have joined the Project via §7.7.
10. **RBAC:** `Roster: View / Create / Edit / Manage Members / Delete`,
    mirroring the Budget/Command permission pattern (§10). Viewing as a
    member piggybacks on membership itself — no permission required,
    same precedent as Project Progress (§7.5).

**Module consumption:** a module presents a roster picker scoped to
what the current user can see, and stores `roster_id` rather than
copying members; membership edits on the core page propagate to every
consuming module automatically. A module may still layer its own
internal sub-grouping on top of a roster reference for purposes no
other module needs (e.g. AMS splitting a roster into morning/evening
training batches) — that stays the module's own responsibility, the
same principle as Sub-FA placement (D4, `2026-07-25-sub-fa-access.md`).

**Known v1 gap, accepted, not resolved:** an Institution cannot see a
roster the Industry created, even one containing its own athletes —
visibility flows creator → Industry/own-branch, never Industry → the
Institutions whose people are on it. Revisit as a follow-up decision if
it becomes a real pain point.

**Phase 2 — Flow Builder reconciliation (gated on resolving the
exact-match withdrawal/reserve-promotion brittleness — backlog item
"[2026-09-17] Exact-match registration/nomination brittleness"):**
publishing a Nomination or completing a Team instance (§7.9.8)
auto-creates a core Roster, tagged with that Discipline, **snapshotted
at Publish** (not live-synced — deferred until a reopen/swap/deadline
mechanism exists, since a live link would let an in-flight edit
silently change a roster AMS/TMS is already running against), with
Main/Reserve or role-slot status carried as a per-member attribute.
Also Phase 2: AMS/TMS refactor to consume core rosters instead of their
own module-internal ones (gated on AMS/TMS integration, §8.4).

**Out of scope for v1:** Institution-as-roster-member; Functional-Area
tagging/scoping of rosters; cross-Project roster membership; live-synced
Flow Builder materialisation; a forced minor-safeguarding override on
co-member visibility (the Communication module's own, separate minor-
safeguarding question remains pending Legal/Business sign-off,
`2026-07-17-communication-module-integration.md` — not the same risk
shape as roster co-member visibility, and not treated as binding
precedent here).

### 8.2 Project Modules

Athlete Management · Training · Credential · Accreditation ·
Communication · Grievance · Certification · Membership · Inventory ·
Expenses · Contract · Payments · Task Management

**Roster** was previously listed here but is reclassified as a
base-platform capability (§8.1/§8.6, Phase 1.5) — it had no §8.3 detail
entry and was never actually built as a standalone module.

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
- **Roster Management (§8.6) visibility** is deliberately **not** the
  same shape as Budget's — a roster carries no Functional Area tag, so
  FA isolation cannot be the scoping mechanism. Instead: **RBAC**
  (`Roster: View/Create/Edit/Manage Members/Delete`) grants the
  *capability*; **who can be added** to a roster is bounded by the D5
  parent-FA-population guardrail (`2026-07-25-sub-fa-access.md`) —
  enforced by the creator's own visibility, not a stored field; **who
  can view/list** a roster is a separate rule — creator, Industry
  always, the roster's own members, and the creator's own Institution
  for rosters any of its members created; and **co-member visibility
  within a roster** is the narrow-only ABAC policy `P-ROSTER-VISIBILITY`
  — default-deny, opt-in per roster by the creator, never widening past
  the roster's own membership. Four distinct layers, never flattened
  into one check. Do not grant `Roster: Create/Edit/Manage Members` to
  participant User Types (e.g. Athletes) via a Project Role by default
  (§7.7.2).
- **Invite expiry ("Expires on", §7.7.6)** respects all three controls:
  **RBAC** gates who may set/edit an invite's deadline (default: the same
  role that can send that invite type). The **accept-time guard**
  (`now < expires_at`, evaluated server-side at the accept instant) is the
  mechanism that makes shortening-a-deadline-after-send safe — it must
  never be flattened away, since it is what prevents a shortened deadline
  from retroactively invalidating an accept in flight. Editing a deadline
  must stay within the **ABAC narrow-only invariant** — it may not widen,
  or retroactively narrow, beyond what the invite already granted.
  **Workspace / Project / Functional Area isolation** scope expiry
  visibility exactly as they scope invites generally (an Institution sees
  expiry only on its own branch's invites). Setting, editing (old→new),
  and auto-expiry of a deadline are all **audited**.

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
- Command registration is supported and **cannot be rejected** — it joins
  the target (Individual or Institution) **immediately and
  unconditionally**, never gated behind form completion (§7.7.5). A
  commanded member is either **Active** (no obligation) or **Active —
  Details Pending** (outstanding form); the Details-Pending state is
  enforced by the narrow-only ABAC policy `P-COMMAND-OBLIGATION`, which
  narrows only the data-dependent actions and, on deadline breach,
  auto-locks the member with **no escalation** to any other party. Command
  targets existing ATOM users/orgs only (no "New" force-onboarding);
  issuable by Industry/Institution by default and by Individual-type roles
  only with a `Command: Issue` RBAC permission; reversible by the org via
  Remove; and fully audited (§7.7.5, §10).
- Project progress is a backend-computed, read-only percentage —
  `(Completed Tasks / Total Tasks) × 100`, counting Task Board Tasks
  (§7.5); never manually set, not based on configuration completeness.
  Completed = status Completed; Total = all Tasks except status Cancelled
  (Reopen counts toward Total, not Completed). `0/0` = 0% (new project);
  rounded to the nearest integer. Progress has no dedicated RBAC
  permission — it piggybacks on page/role access and is not shown on the
  Individual participant surface (§7.5, §10). Replaces the retired
  Phase 1 FA-based progress model (§7.5).
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
- Roster Management (§8.6) is a base-platform capability (Phase 1.5),
  not a separately-assignable module; bundled into standard Industry
  billing (§9). A roster is **Project-scoped, not Functional-Area-
  scoped**, and its members are **Individuals only**, never
  Institutions. Rostering is **never an admission path** — every member
  must already have joined the Project via §7.7. Flow Builder Nomination/
  Team materialisation into core Rosters is Phase 2, gated on resolving
  the exact-match withdrawal/reserve-promotion brittleness (§7.9.8).
- **Flow Builder — Registration & Quota Setup** has four fixed,
  system-defined nodes: Sports Setup → Registration Setup → Quota Setup →
  Registration & Nomination, each Pending → Configured → Published
  (§7.9).
- The **sports taxonomy (Sport → Discipline, Discipline the leaf) is
  Master-Admin-owned**; Discipline carries Gender, Type, a fixed
  playing-member count, and Eligibility criteria. Industry Sports Setup
  is **enable-only** (§7.9.1). "Discipline" replaces "Event" throughout
  this area.
- Registration Setup's Joining Form is scoped to **Sport × User
  Type/Sub-Type**; Master-Admin eligibility fields auto-lock on the form
  (§7.9.2).
- **Mode of Registration** is a single **Project-wide Open/Closed
  toggle** (default Closed) at the top of Registration Setup. Closed =
  Invite + Manual (Institution-initiated); Open adds **Self-Register**
  and switches quota to Open Quota and Nomination to Selection (§7.9.3).
- **Quota Setup** runs Eligibility → Level 1 → Level 2 (Closed) or
  Eligibility → Open Quota → Selection (Open); no Level 3. **Eligibility
  is Master-Admin-owned and read-only for Industry**, gating every
  registration method automatically (§7.9.4).
- **Team squad size is Master-Admin-fixed on the Discipline**; the
  Industry sets team counts and a Sub-Type breakdown that must sum
  exactly, never squad size (§7.9.5).
- **Nomination is Individual-only** (Team fills role-slots directly at
  Registration); Main and Reserve zones both hold real named
  individuals; Publish requires exact fill, no partial publish (§7.9.8).
- **Invite expiry ("Expires on", §7.7.6).** Every invite —
  Industry→Individual, Industry→Institution, Institution→member, the Flow
  Builder Registration Invite, and Master Admin invites — carries an
  optional **Expires on** deadline stored as an absolute UTC `expires_at`,
  cut at end-of-day in the Project/Industry timezone; **blank = never
  expires** (backward-compatible default). Invite states are enumerated:
  Pending → Accepted / Rejected / **Expired**. Accept succeeds only if
  `now < expires_at` (accept-time guard). The sender may extend/shorten
  while Pending (RBAC-gated, ABAC narrow-only); terminal invites are not
  editable; **there is no resend/revive** — re-offering means a new
  invite. An expired Flow Builder Registration Invite **auto-releases its
  `Σ (Q + R)` slot** (§7.9.8). No org-level default/max policy and no
  symmetric expiry on Join Requests in v1 (§7.7.6).

---

## 13. Constraints & Naming Standards

Use these terms exactly; do not substitute synonyms in product
documentation, UI copy, or code without explicit instruction:

`Industry` · `Institution` · `Individual` · `Functional Area` ·
`Sub Functional Area` · `Workspace` · `Role Switcher` · `Roster` ·
`Roster Management`

**Do not replace "Industry" with "Organisation"** unless explicitly
instructed — this substitution has been explicitly rejected as a
naming direction.

---

## 14. Roadmap

### Phase 1 (current scope)

Core workspaces · Organisation Management · Project Management ·
Functional Areas · User Types · Dynamic Forms · Invitations · Join
Requests · Command (force-join mechanics + `P-COMMAND-OBLIGATION`, §7.7.5) ·
Tasks · Notifications · Audit · Module assignment

**Flow Builder — Registration & Quota Setup v1 (§7.9).** Sports Setup
(enable-only over the Master-Admin Sports Library), Registration Setup
(Sport-scoped Joining Forms + Project-wide Mode of Registration), Quota
Setup (Eligibility → Level 1 → Level 2, plus Open Quota for Open
Projects), and Registration & Nomination (contingent Registration,
Individual-only Nomination, Registration Status Dashboard). *Placed in
Phase 1 as the source ideation consistently calls it "v1" but never named
a phase number — confirm.* The Master Admin Sports Library authoring
surface itself is a separate build, out of Flow Builder scope (§7.9.1).

### Phase 1.5

Budget Tracking v1 (§8.5) — base-platform, allocation-only budget
tracker: Industry overall budget → Project allocation → Functional Area
allocation, with allocation dashboards at the Industry and Project
levels. No spend logging in this phase.

Project Progress Tracking (§7.5) — backend-computed, read-only
`(Completed Tasks / Total Tasks) × 100` per Project, counting Task Board
Tasks (Completed = status Completed; Total = all Tasks except Cancelled;
Reopen counts toward Total only). New Project = 0%; rounded to nearest
integer. Surfaces on the Projects list card, Project Overview, Task
Board, and the Industry Overview "ongoing projects" section. No dedicated
RBAC permission (piggybacks on page access; not shown to Individual
participants). Replaces the retired Phase 1 FA-based progress model.
FA-level rollup is out of scope for v1.5.

Roster Management v1 (§8.6) — base-platform, Project-level shared roster
capability: Roster Management page (List / Create-Edit / Detail views,
between Task Board and User Management) plus the Individual-workspace
My Rosters page. Individuals-only membership; not Functional-Area-scoped
(Sport/Discipline tag is the sole filter); member-add bounded by the D5
guardrail; visibility = creator + Industry + own members + creator's own
Institution; co-member visibility default-private with per-roster
creator opt-in; orphan ownership escalates Individual → Institution →
Industry. Modules reference a roster by `roster_id`. Flow Builder
materialisation and AMS/TMS consumption are Phase 2 (below).

### Phase 2

Advanced workflows · Analytics · Automation · Performance dashboards ·
AI assistance · Advanced assessments · Institution Roles scoped within
assigned Functional Areas (§7.7.3) · Participant-facing shift from
Project view to module-generated Service view (§7.8) · Budget Tracking
v2 — spend logging against Functional Areas with roll-up and
Spent/Remaining/over-budget dashboards (§8.5) · Flow Builder Phase 2 —
configurable per-individual multi-Discipline nomination cap (§7.9.8;
uncapped in v1) · Mixed User Types on one person in one Project (v2;
out of scope for Flow Builder v1, §7.9.8) · Roster Management Phase 2
(§8.6) — Flow Builder Nomination/Team auto-materialisation into core
Rosters (snapshotted at Publish, Discipline-tagged, Main/Reserve or
role-slot as a per-member attribute), gated on resolving the exact-match
registration/nomination brittleness (§7.9.8); AMS/TMS refactor to
consume core Rosters instead of module-internal ones (gated on AMS/TMS
integration, §8.4)

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
| **Command** | A registration mode stronger than Invitation; joins the target (Individual or Institution) immediately and unconditionally — cannot be rejected. A commanded member is *Active* or *Active — Details Pending* (outstanding form), the latter enforced by the narrow-only ABAC policy `P-COMMAND-OBLIGATION` (narrows data-dependent actions; auto-locks on deadline breach, no escalation). Existing ATOM users/orgs only; reversible via Remove; fully audited. See §7.7.5. |
| **RBAC / PBAC / ABAC** | Role-, Policy-, and Attribute-Based Access Control — applied together, never singly, to every permission check. |
| **User Type** | A per-Project functional classification for an Individual applicant/invitee (e.g. Athlete, Coach, Support Staff). Defined by the Industry per Project (§7.6). |
| **Role (Project)** | The permission-mapping axis assigned to an Individual alongside User Type when invited to a Project — distinct from User Type, which is purely classificatory. |
| **Requests & Invitations page** | The page where an Individual (or Institution) tracks the status of Projects they've applied to and invitations they've received. Invite rows show `expires_at` + a live countdown, sort by soonest-expiring, filter by status including **Expired**, and keep expired rows visible but greyed and non-actionable (§7.7.6). |
| **Expires on** | The optional per-invite deadline field (§7.7.6) after which an invite can no longer be accepted, moving it to the terminal **Expired** state. Stored as an absolute UTC `expires_at`, cut at end-of-day in the Project/Industry timezone; blank = never expires. Applies to all invite types (Industry→Individual, Industry→Institution, Institution→member, Flow Builder Registration Invite) and to Master Admin invites. Deliberately not called "Deadline" (reserved for Command, §7.7.5) or "window" (Nomination, §7.9.8). |
| **AMS** | Athlete Management System — an independently-developed system that integrates into ATOM as the Athlete Management module; generates Training Programs as its participant-facing Service (§7.8). |
| **TMS** | Tournament Management System — an independently-developed system that integrates into ATOM as the Tournament module; generates Tournaments as its participant-facing Service (§7.8). |
| **Service (module-surfaced)** | A participant-facing deliverable generated by a module assigned to a Project (e.g. a Training Program from AMS, a Tournament from TMS). In later phases this — not the raw Project — is what Individuals/Institutions see (§7.8). |
| **Pro Membership** | A planned paid tier for Individuals (later phase) unlocking cross-Project value such as AMS-tracked performance history — distinct from Industry's module-based billing (§9). |
| **Budget Tracking** | A base-platform capability (Phase 1.5, §8.5) for an Industry to plan/track its own operating budget by allocating from an overall budget down to Projects and then to Functional Areas. v1 is allocation-only (no spend); revenue-neutral, distinct from ATOM's billing of the Industry (§9). |
| **Project Progress** | A backend-computed, read-only percentage per Project, `(Completed Tasks / Total Tasks) × 100`, counting Task Board Tasks (Phase 1.5, §7.5). Completed = status Completed; Total = all Tasks except status Cancelled (Reopen counts toward Total, not Completed). New Project = 0%; rounded to nearest integer; never manually set. Some UI copy calls Tasks "Tickets" — Task is canonical (§13). Replaces the retired Phase 1 FA-based progress model. |
| **Roster** | A durable, Project-level, cross-module-shareable group of Individual members — not Functional-Area-scoped, optionally tagged with a Sport/Discipline — created/owned by an Industry, FA-scoped Institution, or permitted Individual, and referenced by modules via `roster_id` rather than copied (base-platform, Phase 1.5, §8.6). Distinct from a Flow Builder **Team** instance (role-slot structure, §7.9.8) or **Nomination** Main/Reserve zone, which may materialise into a Roster in Phase 2 but aren't Rosters themselves until then; also distinct from **Contingent**, which names the registering Institution, not a group of people. |
| **Roster Management** | The Project-level page (between Task Board and User Management) where Rosters are created, edited, and managed; paired with the Individual-workspace **My Rosters** page (§8.6). |
| **Flow Builder** | The Industry's Project-configuration tool, with two tabs: Hierarchy Setup and Registration & Quota Setup. The latter has four fixed nodes (Sports Setup → Registration Setup → Quota Setup → Registration & Nomination), each Pending/Configured/Published (§7.9). |
| **Discipline** | The leaf of the Master-Admin-owned sports taxonomy (Sport → Discipline; e.g. "Cricket Mens Senior"). Carries Gender, Type (Individual/Double/Team), a fixed playing-member count, and Eligibility criteria. Replaces the older "Event" term throughout Flow Builder (§7.9.1). |
| **Sports Library** | The Master-Admin-owned catalog of Sports and Disciplines (system-seeded, extendable); a subset is assigned per Industry ("My Sports Library"). Its authoring surface is out of Flow Builder scope (§7.9.1). |
| **Sports Setup** | The enable-only Flow Builder node where an Industry toggles which assigned Disciplines are active for a Project — no authoring (§7.9.1). |
| **Registration Setup** | The Flow Builder node defining who can join and what they submit, as a Sport → User Type → Sub-Type → Joining Form tree; Joining Forms are Sport-scoped and auto-lock Master-Admin eligibility fields (§7.9.2). |
| **Mode of Registration** | A single Project-wide Open/Closed toggle (default Closed) at the top of Registration Setup. Closed = Invite + Manual only; Open adds Self-Register and switches quota to Open Quota, Nomination to Selection (§7.9.3). |
| **Quota Setup** | The Flow Builder node running Eligibility → Level 1 (Overall Quota) → Level 2 (Contingent Allocation) for Closed, or Eligibility → Open Quota → Selection for Open. No Level 3 (§7.9.4–7.9.7). |
| **Eligibility (Quota Setup)** | Master-Admin-owned Age/Gender/custom criteria attached to a Discipline; a read-only echo on the Industry-facing screen. Gates every registration method automatically (§7.9.4). |
| **Open Quota** | The quota shape used when a Project is Open: a Max Entries registration cap (Self-Register hard-blocks) that is deliberately oversubscribed above a Selection target; no Level 2 (§7.9.7). |
| **Self-Register** | An Open-mode registration method where an Individual submits the Joining Form directly, with no Institution action or approval (§7.9.3, §7.9.8). |
| **Registration & Nomination** | The final Flow Builder node: a contingent Institution registers users against its Level 2 allocation, then (Individual-only) nominates them into Discipline Main/Reserve zones; Team fills role-slots at Registration and skips Nomination (§7.9.8). |
| **Selection** | The Open-mode equivalent of Nomination: run once on the flat self-registered pool by Industry or a permitted Institution, reusing Nomination's Main/Reserve mechanic; no per-Institution split (§7.9.7). |

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
