# Prompt — Mode of Registration (Open/Closed) toggle

Add a **project-wide Mode of Registration (Open/Closed) toggle** to the
top of the Registration Setup node, and wire its effects across Quota
Setup and Registration & Nomination. Do not touch Hierarchy, Sports
Setup, or the Registration Dashboard. The rest of Registration Setup's
own canvas (Sport rail, User Type/Sub-Type panel, Form Builder drawer)
is unchanged — this adds one control, not a re-architecture.

**1. Registration Setup:** add an Open/Closed segmented control at the
top of the canvas, above the Sports rail. One value for the whole
Project — not per Sport, not per leaf. Default Closed.

**2. Quota Setup — when Open:** every entry shows **Open Quota** instead
of Level 1/Level 2 — a new **Max Entries** cap (hard registration
ceiling) plus a **Selection target** reusing the existing Level 1
Max/Min/Reserve (or Team template) shape. No per-Institution split, no
Level 2. When Closed, renders exactly as today.

**3. Registration & Nomination — when Open:** the method picker gains a
third option, **Self-Register** (Individual registers directly, no
Institution step, hard-blocked at Max Entries), alongside Invite and
Manual which stay available. Nomination is replaced by **Selection** —
same drag-and-drop Main/Reserve mechanic as Nomination, run by Industry
or a permitted Institution on the flat applicant pool instead of
per-contingent. When Closed, renders exactly as today.

**Seed data:** one project, toggle set to Open by default, Cricket →
Athlete with Max Entries 100 / Selection target 50 (Max 40 / Reserve
10), a few seeded Self-Register applicants, and a partially-worked
Selection pass.

**Non-goals:** no per-Sport/per-User-Type override of the toggle; no
discovery surface or new sign-up flow for Self-Register; no early-close
before Max Entries; no lottery-based Selection; no changes to
Eligibility gating.
