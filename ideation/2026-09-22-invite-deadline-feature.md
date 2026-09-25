# Invite Deadline — feature ideation

**Date:** 2026-09-22
**Author:** ATOM PM (for Abhijeet)
**Status:** Scoped — decisions locked (O1–O12 resolved 2026-09-22), ready
for backlog/spec. Not yet committed to `atom-context.md` or `backlog.md`;
see the "Canon updates needed" callout in §1 for the docs work this
surfaces.

---

## Purpose

Ideation pass for a small addition to ATOM's invite flow: an **"Invite
Deadline"** field on invites sent by Industry / Institution (and,
per the ask, Master Admin) roles, letting the sender set an expiry after
which the invite can no longer be accepted. Two candidate input shapes
were named — an absolute date picker, or a relative "expires in N days"
number — and a decision between them is wanted.

This doc grounds the idea in ATOM's actual invite model, runs a
full-flow impact audit (not just the three surfaces flagged), gives a
firm calendar-vs-days recommendation, and records the now-settled scope
(§5 Decisions).

---

## 1. Current state (what ATOM's invite model actually is today)

Grounding this against `atom-context.md` before proposing anything —
because the feature description's framing doesn't fully match canon, and
those mismatches are themselves findings.

**Invite types that actually exist (§7.7, §4, §6, §7.9.8):**

1. **Industry → Individual** (§7.7.2): Industry picks a **User Type** +
   **Role**, marks the invitee **New** (unregistered) or **Existing**,
   sends to the invitee's **email**; invitee accepts/rejects. A **New**
   invitee must **register on ATOM first (§5.1)**, then accept.
2. **Industry → Institution** (§7.7.3): same two-way structure, but the
   grant is a **Functional Area**, and it surfaces in the **Institution
   workspace**, not the Individual one.
3. **Institution → member** (§4, §6 — "invites members"): an Institution
   invites members. Canon names the capability but doesn't detail its
   form — a real gap this feature would have to fill in.
4. **Flow Builder Registration "Invite" method** (§7.9.8): a contingent
   Institution registers users into its Level 2 allocation via **Invite**
   (explicitly "reuses ATOM's existing invite/approve mechanism"). This
   invite consumes a **quota slot** against `Σ (Q + R)`.

**Not an invite (do not conflate):**

- **Command** (§7.7.5) is force-join, **cannot be rejected**, no
  accept/reject step — a deadline on it is meaningless. **But Command
  already owns the word "deadline"** for a completely different concept:
  the `P-COMMAND-OBLIGATION` **form-completion** deadline after which a
  Details-Pending member auto-locks. Reusing "deadline" for invite-expiry
  invites (pun intended) real confusion. **Naming finding — see §4.**
- **Apply / Join Request** (§7.7.1) is the participant-initiated
  direction; it's the Industry that decides. Symmetry question in §5.

**States & surfaces today:**

- Invite states are implied but **never enumerated** in canon: Pending →
  Accepted / Rejected. **There is no "Expired" state and no expiry
  concept anywhere in the invite flow.**
- The canonical status surface is the **"Requests & Invitations page"**
  (§15) — the invitee's/Institution's view of applications and invites.
  The feature description calls it the "Join Requests and Invites page —
  Invitation tab"; **that exact tabbed structure is not in canon.** Treat
  §15's Requests & Invitations page as the real target and design the
  tab within it, rather than assuming a page that doesn't exist yet.
- **Notifications** are a base-platform capability (§8.1); the
  Communication module (§8.3) adds email/SMS/WhatsApp. **No invite
  reminder/expiry notification is currently specified.**

**Two precedents that matter for this feature:**

- **ATOM already runs server-side time-triggers.** The Command obligation
  deadline (§7.7.5) auto-locks a member "on deadline breach" with no
  human in the loop — so a scheduled evaluator already has to exist.
  Invite auto-expiry can ride the same mechanism rather than inventing a
  new one.
- **Every existing ATOM date field is an absolute date, not a
  duration.** The Eligibility Age **cutoff/reference date** is set "per
  entry" (§7.9.4); the Command obligation deadline is a fixed date. There
  is **no "N days" relative-duration precedent anywhere in canon.** This
  is load-bearing for the §3 recommendation.

**Master Admin as inviter — confirmed canon gap (RESOLVED, O2).** The
feature lists Master Admin as a sender. Canon today gives MA **no in-app
invite path**: Industry creation is out-of-band (§5.3, "does not open an
in-app form") and MA's role is governance/module assignment. Abhijeet has
confirmed this is a **real gap, not a mismatch to drop**: MA's invite path
is **the same as Industry's** (same form, same mechanics per §7.7.2 /
§7.7.3). The only difference is a **superset entry point** in front of the
Industry form — MA must first **select an Industry**, then **select a
Project of that Industry**, after which the rest of the invite form (User
Type / Role / FA / New-or-Existing / email) proceeds identically to the
Industry-initiated flow. This is **not a separate flow**. §5.3 currently
states MA has no in-app invite form; that is now known to be incomplete
and must be corrected — captured in the "Canon updates needed" callout
below.

---

### Canon updates needed (surfaced by this feature — not yet applied)

This ideation is not touching `atom-context.md`, but for whoever specs it
next, the feature forces these canon corrections/additions:

1. **§5.3 (Master Admin) + §7.7** — document MA's invite path as the
   Industry invite form fronted by an Industry→Project selector (O2
   above). §5.3's "no in-app form" statement is stale for invites.
2. **§7.7 invite state machine** — enumerate invite states explicitly
   (Pending → Accepted / Rejected) and add the new terminal **Expired**
   state (currently states are only implied; see §4.4).
3. **§4 / §6 Institution→member invite** — canon names the capability but
   never details the send form; this feature requires it be pinned down.
4. **§15 Requests & Invitations page** — the invite-status surface needs
   the expiry column / Expired filter defined (see §4.2).

## 2. Proposed change

Add an optional **expiry** to an invite at send time. Canonically store an
**absolute UTC timestamp** (`expires_at`); allow the sender to enter it
either as a date or as a relative duration that resolves to that
timestamp (see §3). On expiry, the invite moves to a new terminal state
**Expired** and can no longer be accepted. Null `expires_at` = never
expires (the backward-compatible default for every pre-existing invite).

**v1 scope (O1, RESOLVED):** all four invite types carry the field —
Industry→Individual (§7.7.2), Industry→Institution (§7.7.3),
Institution→member (§4/§6), and the Flow Builder Registration Invite
(§7.9.8). Master Admin is included via the Industry-form-plus-selector
path (O2). No subset / phased-by-type rollout.

---

## 3. Calendar vs. "N days" — recommendation

**Recommendation: absolute date picker is the canonical input and the
stored value; offer relative durations only as quick-pick shortcuts that
immediately resolve to (and display) an absolute date.** Store `expires_at`
as a UTC timestamp regardless of how it was entered.

| Axis | Absolute date picker | Relative "N days" |
|---|---|---|
| **ATOM precedent** | **Matches** the only existing date-field pattern (Eligibility cutoff §7.9.4, Command deadline §7.7.5) — all absolute | No relative-duration field exists anywhere in canon; introduces a new idiom |
| **Clarity to invitee** | "Expires 30 Sep 2026" is unambiguous on the invite email + Requests & Invitations page | "Expires in 7 days" is ambiguous once received/forwarded ("7 days from when?") — must be resolved to a date to display anyway |
| **Timezone** | One clean rule: store UTC, render in viewer's locale, default cut at **end-of-day in the Project/Industry timezone** | Duration hides the timezone problem at entry but it resurfaces at display/enforcement identically |
| **Data-entry friction** | One extra tap; heavier for "just give them a week" | Faster for the common "a week/two weeks" case |
| **Mobile UX** | Native date pickers are well-solved | Numeric stepper is trivially mobile-friendly |
| **Bulk/templated invites** | Absolute date on a batch can go stale if the batch is reused later | "N days from send" travels well across reused templates |

The hybrid captures the relative input's speed (quick-picks: 7 / 14 / 30
days) without paying its ambiguity or precedent costs — because the chip
resolves to a shown absolute date before send. This is the same
"capture-then-resolve" shape ATOM already uses for Age (DOB + cutoff →
evaluated per Discipline, §7.9.2/§7.9.4).

**Default expiry time-of-day:** end of the selected day, evaluated in a
single declared timezone (propose the Industry/Project timezone, not the
invitee's — the sender owns the deadline). Note this in copy so "expires
30 Sep" isn't read as midnight-start.

---

## 4. Impact analysis by surface

Severity: **blocking** = must be resolved before build; **significant** =
real design work; **minor** = copy/config.

### 4.1 Invite form — the field itself — *significant*
- New optional **Expires on** field (O8, RESOLVED — this is the label, not
  "Deadline"/"Invite Deadline", to stay clear of Command's obligation
  deadline §7.7.5 and Nomination's window §7.9.8). Quick-pick chips + date
  picker per §3.
- **New/unregistered invitee (§7.7.2 "New") — NO guard (O7, RESOLVED).**
  Decision is to treat New invitees identically to Existing: **no minimum
  lead time, no warning**. A New invitee must still register (§5.1) then
  accept, so a short deadline can be mathematically impossible to fulfill
  — Abhijeet has **accepted this as a known risk** rather than building a
  guard. Carried in §6 Risks, not enforced here.
- **Master Admin (O2, RESOLVED):** same form, preceded by
  Industry-then-Project selectors (§1).
- **Institution FA invite (§7.7.3)** and **Institution→member (§4/§6)**:
  field is identical; confirm it appears in the Institution-workspace send
  form too, not only the Industry one — all four types are in scope (O1).

### 4.2 Requests & Invitations page / Invitation tab (§15) — *significant*
- Display `expires_at` + a live **countdown / "expires in Xd"** on each
  invite row (compute for display even though stored absolute).
- **Sort** by soonest-expiring; **filter** by status incl. new
  **Expired**.
- **On expiry, the row does not vanish** — it flips to a greyed
  **Expired** state with the accept action removed. No "request again" /
  "resend" affordance (O9, RESOLVED — there is no resend/revive in v1; a
  sender who wants to re-offer simply creates a brand-new invite).
  Silently disappearing rows would destroy the invitee's audit trail of
  what they were offered.
- Mirror on the **sender's** management view (the Industry/Institution
  side that lists sent invites), so the sender sees what lapsed.

### 4.3 Notifications (§8.1 / §8.3) — *minor→significant* (O6, RESOLVED)
- **24h-before-expiry reminder — reuses EXISTING infrastructure.** Per
  Abhijeet, the 24h-before-deadline reminder logic already exists on the
  platform (the Command-obligation-deadline precedent, §7.7.5). This is
  therefore **not new notification design work** — just wire the
  invite-expiry event into the existing 24h-before mechanism. (Reminder is
  to the **invitee**; richer email/SMS/WhatsApp if the Communication
  module §8.3 is assigned.)
- **Expiry-moment notification — still a design note (not confirmed
  existing).** Propose firing at expiry to **both** parties — invitee
  ("this invite has expired") and **sender** ("your invite to X expired,
  not accepted"), since the sender needs the signal (and, for §7.9.8, the
  slot auto-releases per §4.6). Unlike the 24h reminder, this piece was
  **not** stated to already exist — keep it as a proposal to confirm at
  spec time.
- Consistent with §7.7.5's precedent of notifying the affected party when
  a deadline passes.

### 4.4 Acceptance flow / state machine — *blocking*
- Add terminal state **Expired** to the invite lifecycle (Pending →
  Accepted / Rejected / **Expired**). This is a genuine state-machine
  change, so it must be defined in canon (§7.7) not left implicit.
- **Guard the accept action (confirmed, O5):** an invitee opening a stale
  link/email must get a clean "this invite has expired" screen, **not** a
  join. Rule is locked: **accept succeeds only if `now < expires_at`
  evaluated at the moment of the accept action** (server timestamp wins).
  This is also what makes edit-after-send safe — a sender shortening a
  deadline cannot retroactively invalidate an accept already in flight
  (see §4.5).
- **Auto-expiry mechanism**: reuse the Command-deadline scheduler concept
  (§7.7.5) rather than a new job. A lapsed-but-not-yet-swept invite must
  still read as Expired via the `now < expires_at` guard even before the
  sweep runs.

### 4.5 Edit-after-send (no resend) — *significant* (O5 + O9, RESOLVED)
- **No resend/revive in v1 (O9).** There is no action that reopens an
  expired invite or re-fires a pending one. If a sender wants to re-offer
  (before or after expiry) they **create a brand-new, independent
  invite**; the old one stays in its terminal state for audit. This
  removes an entire mechanism from scope — no "resend" state transition,
  no link re-issuance logic.
- **Edit deadline while Pending (O5).** The sender **can extend OR
  shorten** a still-Pending invite's `expires_at`. Permissioned — **RBAC**
  for who can edit (default: same role that can send that invite type).
  The **guard against abuse is the accept-time check** (§4.4): shortening
  cannot retroactively kill an accept already in flight, because accept
  only requires `now < expires_at` at the accept instant. Cannot edit a
  **terminal** (Expired / Accepted / Rejected) invite.

### 4.6 Flow Builder Registration "Invite" method + QUOTA (§7.9.8) — *significant* (O10, RESOLVED YES)
- This is the highest-impact surface and the one the feature description
  didn't call out. A Registration Invite **consumes a quota slot** against
  `Σ (Q + R)`. **Decision is locked: an expired Registration Invite
  automatically releases its reserved slot back to the `Σ(Q+R)` pool.** No
  silent quota stranding.
- **Concrete dependency resolved — flag this as a product win.** Automatic
  expiry-release is a clean, hands-off way to reclaim an unclaimed slot,
  which **closes / partially closes the exact-match withdrawal-and-
  replacement brittleness** raised as **Item 4 of
  `2026-08-22-flow-builder-open-items-followup.md`** (backlog). Whoever
  updates that backlog item should cross-reference this decision: an
  invite that is never accepted now self-heals the pool instead of
  requiring manual replacement.
- Note the terminology overlap: Nomination separately has "**No deadline
  system exists yet**" (§7.9.8) for its publish flow — that's a different
  deadline again. Three distinct "deadline" concepts now (invite-expiry,
  Command-obligation, nomination-window); keep them named apart.

### 4.7 Audit (§10) — *minor*
- Log deadline **set**, **edited** (old→new), and the **auto-expiry**
  transition, consistent with §7.7.5's "every state transition is
  audited" pattern.

### 4.8 Admin-configurable default — *dropped from v1* (O4, RESOLVED)
- **No org-level policy in v1.** No Industry/Project **default** expiry and
  no **enforced max** deadline. The sender picks `expires_at` **per invite
  only**, or leaves it blank (= never expires, O3). The org-default /
  enforced-max idea is removed from v1 scope; it can return as a later
  ideation if consistency-at-scale becomes a real pain point.

### 4.9 Bulk invites — *minor→significant*
- The Registration Invite method can be many-at-once. A single
  batch-level `expires_at` (absolute) or "N days from send" (relative)
  applied to all — the **relative** form travels better for reused batches
  (see §3 table), reinforcing the hybrid.

### 4.10 Backward compatibility / migration — *blocking, but simple*
- Every invite created before this ships has **no `expires_at`**. Rule:
  **null = never expires.** No backfill, no retroactive expiry of
  in-flight invites. Clean and non-breaking.

### 4.11 Access control (§10) — *significant*
- **RBAC**: who can *set/edit* an invite deadline (default: the same role
  that can send that invite type).
- **ABAC narrow-only invariant** (§10, and the pattern in
  `2026-07-31-abac-pbac-access-control.md`): editing must not become a way
  to widen/retroactively-narrow beyond what the invite already granted.
- **Isolation**: expiry state respects Workspace / Project / FA isolation
  — an Institution sees expiry only on its own branch's invites.

---

## 5. Decisions (O1–O12, resolved 2026-09-22)

All twelve questions are settled by Abhijeet. O-numbers are kept as stable
references so older discussion still cross-references cleanly.

- **O1 — RESOLVED: all four invite types in v1.** Industry→Individual
  (§7.7.2), Industry→Institution (§7.7.3), Institution→member (§4/§6), and
  the Flow Builder Registration Invite (§7.9.8). No subset / phased rollout.
- **O2 — RESOLVED: MA uses the Industry invite path (confirmed canon
  gap).** Not a mismatch to drop. MA gets the **same** invite form and
  mechanics as Industry (§7.7.2 / §7.7.3), fronted by an **Industry
  selector then Project selector**; everything after that is identical. Not
  a separate flow. §5.3's "no in-app form" line is stale and must be
  corrected (see §1 "Canon updates needed").
- **O3 — RESOLVED: null `expires_at` = never expires.** Confirmed as-is;
  backward-compatible default for pre-existing invites.
- **O4 — RESOLVED: no org policy in v1.** No org-level default and no
  enforced-max deadline. Per-invite choice only (or blank). Dropped from v1
  scope (see §4.8).
- **O5 — RESOLVED: editable while Pending, extend OR shorten.** Guard: the
  accept only succeeds if `now < expires_at` at the accept instant, so
  shortening can't retroactively invalidate an in-flight accept (see §4.4 /
  §4.5). Cannot edit a terminal invite.
- **O6 — RESOLVED: reuse existing 24h-before reminder infrastructure.**
  The 24h-before-expiry reminder already exists on the platform (Command
  deadline precedent, §7.7.5) — just wire the invite-expiry event into it,
  no new notification design. The **expiry-moment** notification to invitee
  + sender is **not** confirmed-existing; it stays a design note to confirm
  at spec (see §4.3).
- **O7 — RESOLVED: NO guard for New/unregistered invitees.** New invitees
  are treated identically to Existing — no minimum lead time, no warning.
  Accepted risk that a short deadline to a New invitee can be impossible to
  fulfill (carried in §6 Risks).
- **O8 — RESOLVED: label is "Expires on."** Not "Deadline" / "Invite
  Deadline" — avoids collision with Command's obligation deadline (§7.7.5)
  and Nomination's window (§7.9.8).
- **O9 — RESOLVED: no resend/revive in v1.** To re-offer, create a
  brand-new independent invite; the old one stays terminal for audit. Whole
  resend mechanism removed from scope (see §4.5).
- **O10 — RESOLVED: YES, expiry releases the quota slot.** An expired Flow
  Builder Registration Invite (§7.9.8) automatically returns its reserved
  slot to the `Σ(Q+R)` pool. This closes / partially closes Item 4
  (withdrawal-brittleness) of
  `2026-08-22-flow-builder-open-items-followup.md` — a concrete resolved
  dependency worth flagging (see §4.6).
- **O11 — RESOLVED: end-of-day in the Project/Industry timezone.** Not the
  invitee's timezone. Store UTC, cut at end-of-day in the declared
  Project/Industry timezone.
- **O12 — Deferred (explicitly out of scope, not decided).** Whether Join
  Requests / Applications (§7.7.1) should carry a symmetric expiry the
  Industry must act within is **parked as a separate future ideation** —
  neither adopted nor rejected here.
- **O13 — RESOLVED: calendar min-date is today, not tomorrow.** The
  "Expires on" date picker allows same-day selection (only genuinely past
  dates are disabled). This was flagged during spec because disabling
  "today" would impose a de facto 1-day minimum lead time on every invite —
  in tension with **O7**'s explicit no-guard decision. Resolved in favor of
  staying literally consistent with O7: today is selectable, resolving to
  end-of-day expiry per O11 (so a same-day invite is tight but not
  blocked).

---

## 6. Risks & gaps

- **Naming collision (MITIGATED, O8).** Three "deadline" concepts coexist
  — Command obligation (§7.7.5), Nomination window (§7.9.8), and invite
  expiry. Resolved by **not** using "Deadline" for this field: the label is
  **"Expires on."** Keep the distinct copy consistent across form,
  notifications, and the Requests & Invitations page.
- **Broken short-deadline invites to New users (ACCEPTED risk, O7).** By
  decision there is **no guard**: an Industry/MA can send e.g. a 1-day
  invite to an unregistered person who cannot possibly register (§5.1) then
  accept in time. Abhijeet has knowingly accepted this rather than build a
  minimum-lead-time check. Worth a light copy nudge at spec time (show the
  invitee's New/unregistered status next to the date field) but **not** a
  blocker.
- **Silent quota stranding — RESOLVED, not a risk (O10).** Expired
  Registration invites **auto-release** their `Σ(Q+R)` slot, so there is no
  stranding path. This flips from a risk into a product win (see §4.6).
- **Undocumented invite substrate.** Canon never enumerates invite states
  or the Institution→member invite form; this feature forces both to be
  pinned down in §7.7 — a positive side effect, but real work, not a
  drop-in field.
- **Requests & Invitations page structure (§15)** is thinly specified;
  the "Invitation tab" the ask assumes needs to be designed, not just
  extended.

---

## 7. Related context sections

§4, §5.1, §5.3, §6, §7.7 (7.7.1 / 7.7.2 / 7.7.3 / 7.7.5), §7.9.8, §8.1,
§8.3, §10, §11, §15. Related ideation:
`2026-08-22-flow-builder-open-items-followup.md` (Item 4 withdrawal
brittleness), `2026-07-31-abac-pbac-access-control.md` (narrow-only
invariant for edit-after-send).
