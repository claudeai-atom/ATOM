# [2026-08-10] Flow Builder — workflow automation & universality (PM review)

> Standalone ideation doc. Reviews two additions Abhijeet wants to fold
> into the in-progress Flow Builder design:
> (1) some concept of **workflow automation** (a phase-1-appropriate
> slice of the maximalist `flow_builder_prd.pdf` "USI Flow Builder"
> vision), and (2) **universality** — making the builder usable for
> "anything," not hardcoded to national-sports-event concepts.
> Section refs (§) point at `context/atom-context.md`; D-numbers point at
> `ideation/2026-07-20-flow-builder-ideation-v2.md` (the canonical
> in-progress Flow Builder doc). Exploratory — nothing committed to
> `atom-context.md`/`backlog.md`.

**Status:** Raw / Under review. No decisions locked.

**Terminology note up front — "phase 1" vs product Phase 2.** Abhijeet's
"phase 1 flow builder" means the **Flow Builder module's own first cut**
("Flow Builder v1" throughout the v2 doc). That first cut is itself
phased into **product Phase 2** (§14 Roadmap; v2 doc §L, title). This
review uses **"Flow Builder v1"** to avoid the collision and flags where
product-Phase-2/Later-Phase capabilities are being assumed.

**Source-access caveat:** `flow_builder_prd.pdf` could not be text-
extracted in this environment (Flate-compressed streams; no
poppler/pdf lib available). This review works from the detailed PRD
summary supplied with the task (governance-aware orchestration: vertical
Hierarchy Engine + horizontal Workflow Engine; RBAC/ABAC/PBAC/VQE; a
node zoo of Action/Approval/Validation/Communication/AI/Integration/
Logic nodes; workflow templates for athlete onboarding, tournament
approvals, reimbursements, grievance; AI governance; audit/compliance;
multi-tenant; 7-phase roadmap ending in a "national sports governance
operating system"). If any PRD specifics below are misattributed,
correct against the source — the recommendation does not hinge on PRD
detail, since Abhijeet has already rejected the full PRD scope for v1.

---

## 0. Framing findings that apply to both points

- **F0.1 — Flow Builder is still entirely pre-canon.** A grep of
  `atom-context.md` and `backlog.md` returns **zero** occurrences of
  "Flow Builder," "workflow automation," "orchestration," "generic," or
  "universal." §O of the v2 doc confirms committal is deferred. **Good
  news:** both proposed additions are cheap to get *right* now, because
  nothing in canon has to be unwound to change the framing before commit.

- **F0.2 — "Automation" and "Advanced workflows" are already Phase-2
  roadmap intent.** §14 Phase 2 literally lists *"Advanced workflows ·
  Analytics · **Automation** · Performance dashboards · AI assistance."*
  So workflow automation is not a foreign concept to sneak in — it is
  **already promised**, just undefined (the same shape of gap the ABAC/
  PBAC review found for §8.1/§10/§16). This is a define-the-slice
  problem, not an adopt-or-not problem.

- **F0.3 — "node" already means two different things, and the PRD adds a
  third.** In Flow Builder the word "node" is overloaded:
  (i) **Hierarchy-tab nodes** = FA/Institution/Individual graph nodes
  (§B, §K);
  (ii) **Registration & Quota Setup nodes** = the five fixed
  **configuration surfaces** with a Pending→Published status and a forced
  sequence (D14/§P.1) — these are *authoring pages*, not runtime steps.
  The PRD's Action/Approval/Validation/Communication nodes are (iii)
  **runtime execution nodes** in a process engine — a categorically
  different thing. The companion draft (`atom-flow-builder.md`) already
  seeds the confusion by calling Tab 2 "a **workflow** consisting of
  configuration nodes." **Recommendation regardless of the rest: do not
  call the Tab-2 config sequence a "workflow," and do not introduce a
  PRD-style runtime "node" into a tab whose "nodes" are config pages.**
  Mixing the two meanings will make every future scope conversation
  murkier.

---

## POINT 1 — Workflow automation: the smallest high-leverage slice

### 1.1 What the current design actually is (and isn't)

The Registration & Quota Setup tab is a **linear configuration wizard
with publish-gates**, not a workflow engine. Each of the five nodes
(D14/§P.1) is a config page that starts **Pending**, is **Published** by
the Industry, and Publishing unlocks the next node "as a literal UI gate,
not just a data dependency" (§P.1). That is a *hardcoded, non-
configurable* state machine — the Industry cannot author it, branch it,
or attach side-effects to a transition. Calling this "workflow
automation" today would be generous: it automates nothing; it gates
authoring order.

### 1.2 "Isn't Nomination already a workflow?" — No.

Nomination (D2, D16, §P.3) is a **manual, synchronous data-entry
surface**: an Industry admin drills Institution → QuotaGrant leaf →
Search/Manual-register, and the Entry lands as `Confirmed` **immediately,
with no approval step** (D16 explicitly rules out a pending/approval
stage). There is no routing, no trigger, no unattended action. It is the
*opposite* of automation — a human doing one insert at a time. So
Nomination does not already cover the intent, and "more nodes like
Nomination" would not be automation either.

### 1.3 Where automation *already leaks into the decided design* (the seam to pull)

The important finding: **v1 already contains three time/state-triggered
behaviors, each decided as a scattered one-off**, none named as
"automation":

1. **Nomination deadline** — an Industry-configured Nomination-node
   setting (D27, resolved 2026-07-31).
2. **Deadline-gated reclamation** of allocated-but-unused quota (D6e,
   D27) — a runtime action that only *opens* after the nomination
   deadline passes and caps each reduction at `Q − Confirmed`.
3. **Min-quota deadline block** (D28) — at the nomination deadline, any
   leaf still `Σ Q < Min` is flagged under-allocated and blocks
   nomination-close until granted-up or an Audit-logged waiver.

These are **event-driven side effects Flow Builder already owns and
already computes** — they are automation in all but name, currently
hardwired into three separate decisions. That is the cheapest, safest,
highest-leverage place to put a *thin, explicit* automation concept.

### 1.4 Recommendation — do NOT add a generic "Automation node"; formalize the deadline/state triggers that already exist, plus reminders

**The smallest high-leverage slice:** a single, closed **"Nomination-
window automation"** primitive on the Nomination node (natural home,
since it already owns the deadline, D27), consisting of:

- **Triggers (closed set, not user-authored logic):** the
  **nomination-deadline** already in D27, plus optionally a small number
  of relative offsets (e.g. "N days before deadline"). No arbitrary
  event graph, no Logic nodes.
- **Actions (bounded to Phase-1 primitives only):**
  - **(a) Send a notification** to a scoped recipient set — reusing the
    Hierarchy tab's already-designed **Notification Recipients** concept
    (§B: FA Head / Assigned Institution Admins / Assigned Industry Users
    / Individuals / Project Managers) and Phase-1 **Notifications** (§14
    Phase 1, which already ships Notifications). Canonical use:
    *"3 days before the nomination deadline, notify every quota-holder
    with unfilled slots"* and *"at deadline, notify quota-holders below
    Min."*
  - **(b) Flip Flow-Builder-internal state** that is *already decided* —
    open reclamation mode (D6e/D27), raise the under-allocated flag
    (D28), close nomination on a leaf. No new state; just making the
    existing hardcoded transitions the visible output of one automation
    surface instead of three buried rules.

**What this deliberately excludes** (this is the whole point):

- **No Approval / Validation / Integration / AI / Logic nodes** (the PRD
  zoo). Those are runtime orchestration and belong to product Phase 2+
  ("Advanced workflows · Automation · AI assistance," §14).
- **No approval-workflow authoring on the join/nomination path.** Adding
  that reopens the module boundary that **D2** deliberately closed:
  self-service nomination and the accept/reject gate live **downstream in
  the §7.7 Registration/Join path**, not in Flow Builder. An "Approval
  node" here would duplicate §7.7 and violate D2.
- **No dependency on the Communication module** (§8.2/§8.3) as a runtime
  consumer. That module doesn't exist in v1; wiring a "Communication
  node" to it violates §11 ("cannot use a dependent module before
  prerequisite modules are active") and D13 ("don't hard-wire nonexistent
  consumers"). The notify action must ride **Phase-1 Notifications**
  only, or be emitted as a config artifact per D13 — not presuppose the
  Communication module.

### 1.5 Why this is "automation," not "more of the same"

- It is **unattended and event/time-triggered** (the deadline fires;
  nobody clicks) — categorically unlike Nomination's manual inserts
  (1.2) and unlike the publish-gate wizard's human-driven transitions
  (1.1).
- It **unifies three already-decided scattered behaviors** (D6e, D27,
  D28) under one legible concept instead of three special cases — net
  simplification, not net scope.
- It **demos as automation** (reminders + auto-open reclamation + auto-
  flag) with **zero new module dependencies** and **zero reopening of
  D2's boundary** — the two ways this could go wrong.

### 1.6 Honest alternative — add nothing, just rename

A defensible minimalist position: the deadline behaviors (D6e/D27/D28)
**are** ATOM's v1 automation story; don't even add reminders — just
*document* those three as "Flow Builder v1's workflow-automation
surface" and move on. This is the lowest-risk option and I'd accept it if
capacity is tight. The reason I still recommend adding **deadline-
reminder notifications** on top (1.4a) is that it is the one genuinely
new, genuinely useful automation that (i) sports operators expect
(chasing contingents to fill quota before a deadline is a real, painful,
manual job today), and (ii) costs almost nothing because the recipient
model and Notifications both already exist. Everything beyond that
reminder is Phase-2 and should be refused by name.

### 1.7 Registration Dashboard node (the one undesigned node, §P.6)

The still-undesigned Registration Dashboard node is the right place to
**surface** automation state (what fired, what's pending, which leaves
are under-allocated), **not** to author it. Worth noting so the automation
primitive and the Dashboard node get designed as read/write counterparts,
not overlapping surfaces.

---

## POINT 2 — Universality: cheap reframe vs. costly rework

### 2.1 A large amount of universality is ALREADY built — this is the key finding

Abhijeet frames universality as a thing still to be won. Much of it is
**already locked in**, via a consistent "ATOM ships the mechanism, the
Industry supplies the vocabulary" doctrine:

- **D1** — no "Contingent" entity; quota is a **generic grant on any
  participating Institution**; "Contingent" is only Industry-facing
  shorthand.
- **D4** — no GTCC/NSF/SSV primitives; **plain Institution nodes the
  Industry labels itself.** "A club league never sees 'GTCC' anywhere"
  (§I).
- **D5** — quota tracks are **fully generic per User Type/Sub-Type**, not
  a fixed Athlete/Officials/SSV set; those three were "artifacts of the
  prototype's own initial ideation."
- **D17** — User Types/Sub-Types are **Industry-authored free-text**,
  with **no backend master** (the explicit inverse of Sports Setup).

So the `atom-flow-builder.md` design principle ("Be generic and industry-
agnostic… Avoid hardcoded National Games workflows," lines 824–826) is
**not merely aspirational — it is substantively honored** across the
Hierarchy tab, Registration Configuration, and Quota tracks. For those
surfaces, universality is a **naming/reframe** job, not a rebuild.

### 2.2 The one place it is genuinely NOT universal — and Abhijeet under-scoped it

Abhijeet named **D17–D28** (Registration Configuration + Quota Setup) as
the at-risk locked work. That range is actually the *already-generic*
part (2.1). The real domain coupling sits **outside** the range he named:

- **Sports Setup node (D15/§P.2) is the single most sports-specific,
  most hardcoded thing in the entire design — and it is node #1 of 5, and
  it gates everything after it.** Its whole model is a **backend-fixed
  Sport→Discipline→Event→Category→Event-Type taxonomy**, enable-only,
  with **"no free-text fields anywhere"** (§P.2). It is Pending until
  **≥1 Event×Category leaf is enabled**, and Registration Configuration
  is **unlocked by Sports Setup Publishing** (D21/§P.4). **Consequence: a
  non-sports project literally cannot advance the flow** — there is no
  "Sport" to enable, so the forced sequence (D14/§P.1, and the §M.3
  node-ordering derived constraint) never unlocks node 2.
- **Quota leaf = Event × Category** (D7/D22/D24). Two of the three
  granularity options (D23: `per Event × Category / per Sport / per
  Project`) are sports nouns. **But `per Project` already exists as a
  generic fallback**, so this is a soft coupling, not a blocker.
- **"Max Team" / "Event Type (Individual/Team)"** (D24/§P.2) are sports-
  shaped but arguably domain-neutral (team-vs-individual participation
  exists outside sports).

The v2 doc itself already spotted this class of tension —
§B (lines 99–104) flags that the quota workflow is "governance-heavy and
National-Games-flavored… in direct tension with the draft's own design
principle 'avoid hardcoded National Games workflows.'" **D4 resolved the
GTCC/NSF/SSV half of that tension. The Sports Setup half was never
reconciled — D15 in fact made it *more* hardcoded** ("no free-text fields
anywhere"). So there is a **genuine, unresolved contradiction between the
stated design principle and locked D15/§P.2** — not a vague worry.

### 2.3 So: relabel, or different model? — Mixed, and the split matters

- **Registration Configuration + Quota Setup (D17–D28): relabeling /
  generalization — cheap, and mostly already done (2.1).** Make the canon
  language generic (User Type, Sub-Type, QuotaTrack, granularity — none
  sport-specific), ensure **`per Project` granularity is a first-class
  non-sports path**, and treat "Event × Category" as **one domain
  instance** of a generic quota leaf. This is consistent with D1/D4/D5/
  D17 and with the atom-flow-builder principle — **it should happen
  anyway**, independent of any universality ambition. Near-zero cost, no
  conflict with locked decisions.

- **Sports Setup + the Sports-first forced gate (D14/D15/§P.1/§P.2/D21):
  NOT relabeling.** True universality requires a real structural decision:
  **make Sports Setup optional and non-gating**, so the flow can run for a
  project with zero sports (Registration Configuration must be reachable
  without a published Sport). Optionally, provide a generic taxonomy/
  category the Quota leaf can key off when there is no Sport (or lean on
  `per Project`). This is a **bounded change to D14/D15/D21**, not a full
  rework — but it *is* more than a rename and it touches locked
  decisions, so it needs to be made deliberately, not assumed.

- **A truly domain-agnostic "configure anything" engine (the PRD's
  generic Workflow Engine + arbitrary node types + arbitrary taxonomies):
  SCOPE CREEP.** That is precisely the PRD vision Abhijeet has already
  rejected for v1. There is **no non-sports customer anywhere in canon** —
  §8 modules (Athlete Management, Accreditation, Credential, Grievance,
  Certification), §8.4 provenance (AMS, TMS), and the whole §14 roadmap
  are sports-tech. Building universality with zero named non-sports demand
  is **speculative generality** — the classic PM trap of paying today for
  optionality nobody has asked to buy.

### 2.4 Brutally honest verdict

**Reframe, don't rebuild — and make exactly one real decision.**

1. **Do the free reframe now** (2.3, bullet 1). It is not "chasing
   universality"; it is finishing the D1/D4/D5/D17 doctrine consistently
   and honoring a design principle the doc already claims. Zero conflict,
   should happen regardless.
2. **Decide Sports Setup's mandatoriness deliberately** (2.3, bullet 2).
   If ATOM has *any* concrete near-term non-sports project, make Sports
   Setup optional/non-gating — a bounded change. If not, **explicitly
   accept that Flow Builder v1 is sports-event-shaped around a generic
   core**, document Sports Setup as the one intentional domain coupling,
   and defer true multi-domain until a real non-sports customer exists.
   Either way, close the D15-vs-design-principle contradiction on the
   record (2.2) instead of leaving it latent.
3. **Refuse the PRD's generic engine** (2.3, bullet 3) by name. Universal-
   for-its-own-sake is scope creep against ATOM's actual, entirely
   sports-tech, Phase-1/1.5 maturity.

---

## Conflicts with locked decisions (consolidated)

- **Point 1, if done as a generic runtime node:** conflicts with **D14**
  (fixed 5-node structure) and F0.3's node-meaning discipline; an
  **Approval node on the join/nomination path conflicts with D2**
  (self-service + approval is downstream §7.7, not Flow Builder); a
  **Communication/Integration node conflicts with §11** (no dependent
  module before prerequisite active) and **D13** (don't hard-wire
  nonexistent consumers). The **recommended slice (1.4) does not
  conflict** — it formalizes D6e/D27/D28 and rides Phase-1 Notifications.
- **Point 2, if pursued as full universality:** conflicts with **D15/§P.2**
  (Sports Setup backend-fixed, "no free-text fields anywhere"),
  **D14/§P.1** (Sports-first forced gate) and the **§M.3** node-ordering
  derived constraint. The **recommended reframe (2.3 bullet 1) does not
  conflict** — it is consistent with D1/D4/D5/D17.

## If committed later — downstream doc impact (not editing now)

- **§14 Roadmap** — Point 1's slice is the concrete v1 down-payment on the
  already-listed "Automation / Advanced workflows"; the PRD's node zoo is
  the Phase-2+ expansion. Record the split so future scope talks inherit
  it.
- **v2 doc §P.3/§P.5** — Point 1 folds D6e/D27/D28 into one named
  "Nomination-window automation" primitive; reminder-notification action
  is net-new to spec.
- **v2 doc §P.2/D15 + §M.3** — Point 2's Sports-Setup-optional decision
  (if taken) changes the node-ordering/gating constraint; the reframe
  updates canon naming only.
- **§15 Glossary** — if the reframe lands, ensure QuotaTrack/granularity/
  Sub-Type are defined domain-neutrally; note Sport/Event/Category as
  one domain instantiation.
