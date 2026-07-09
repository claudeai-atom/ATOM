# ATOM Context Pack (Companion Context)

> This document is intended to be shared with Claude alongside the
> latest PRD. The PRD remains the detailed functional specification.
> This file provides the architectural decisions, product philosophy,
> workflows, constraints, and product context that evolved through
> design discussions.

## Product Vision

ATOM (Unified Sports Ecosystem) is a modular sports operating system,
not merely an event management platform.

Its purpose is to connect every stakeholder in sports into one
ecosystem:

-   Individuals
-   Institutions
-   Industries
-   Master Administration

The platform supports both day-to-day sports operations and large
multi-sport events through reusable modules.

------------------------------------------------------------------------

# Core Philosophy

Every account starts as an **Individual**.

Lifecycle:

Individual → Complete profile → Default Workspace created → Can create
Institution profile → Master Admin may grant Industry access → User may
own multiple workspaces → Switch contexts using Role Switcher

Workspace context determines permissions.

No data leakage between workspaces.

------------------------------------------------------------------------

# Primary Hierarchy

Master Admin

↓

Industry

↓

Projects

↓

Functional Areas

↓

Sub Functional Areas

↓

Tasks

↓

Individuals / Institutions

Institution is created by an Individual and later participates inside
Industry projects.

------------------------------------------------------------------------

# Primary User Journey

1.  Register as Individual.
2.  Verify account.
3.  Complete Individual profile.
4.  Default workspace created.
5.  User may create Institution profile.
6.  Institution awaits approval.
7.  Master Admin grants Industry workspace where applicable.
8.  Industry creates projects.
9.  Configure Functional Areas.
10. Configure User Types & Dynamic Forms.
11. Open registration.
12. Invite or Command users.
13. Institutions apply.
14. Individuals apply.
15. Industry approves.
16. Assign FA/Sub FA.
17. Institution manages execution.
18. Tasks executed.
19. Project archived.

------------------------------------------------------------------------

# Workspace Model

Individual Workspace - Personal profile - Discover industries - Discover
institutions - Apply to projects

Institution Workspace - Institution profile - Join projects - Manage
assigned FAs - Invite members - Create tasks

Industry Workspace - Create projects - Configure projects - Assign
institutions - Assign permissions - Manage lifecycle

Master Admin - Global governance - Module assignment - User governance -
Industry governance

------------------------------------------------------------------------

# Project Types

Default Project - Internal operations - One per Industry

Event Project - Unlimited - Time-bound - Sports events - Tournaments -
Camps - Trials

Lifecycle

Draft Upcoming Ongoing Completed Cancelled Archived

------------------------------------------------------------------------

# Registration Modes

Supported product decisions

Open - Self registration - Invitation - Command

Closed - Invitation - Command

Command is a platform differentiator.

A Command cannot be rejected.

------------------------------------------------------------------------

# Participation Paths

Individual → Industry

Individual → Institution → Industry

Institution → Industry

Institution → Institution → Industry

Industry controls approvals.

------------------------------------------------------------------------

# Functional Areas

Project

↓

Functional Area

↓

Sub Functional Area

↓

Task Board

↓

Tasks

Institutions own execution inside assigned FAs.

------------------------------------------------------------------------

# Progress Tracking (Latest Decision)

Project progress should NOT depend on configuration fields.

Instead:

Progress = Completed Functional Areas / Total Functional Areas

Phase 1 implementation.

------------------------------------------------------------------------

# Core Modules

Base Platform

-   Authentication
-   Workspace
-   Account
-   RBAC
-   PBAC
-   ABAC
-   Notifications
-   Audit
-   Settings
-   Organisation Management
-   Project Management

Project Modules

-   Athlete Management
-   Roster
-   Training
-   Credential
-   Accreditation
-   Communication
-   Grievance
-   Certification
-   Membership
-   Inventory
-   Expenses
-   Contract
-   Payments
-   Task Management

Modules are assignable by Master Admin with dependency validation.

------------------------------------------------------------------------

# Organisation Management

Supports

Industry onboarding

Institution onboarding

CRM

Relationship management

Profiles

Hierarchy

Access

User management

Role management

------------------------------------------------------------------------

# Project Management

Industry creates Project

↓

Configure Project

↓

Functional Areas

↓

User Types

↓

Dynamic Forms

↓

Registration

↓

Execution

↓

Closure

------------------------------------------------------------------------

# User Types & Dynamic Forms

Industry defines

-   User Types
-   Registration Forms
-   Validation Rules

Forms are project-specific.

Different projects may require different onboarding fields.

------------------------------------------------------------------------

# Clubs & Academies

ATOM is intended to support

-   Clubs
-   Academies
-   Universities
-   Schools
-   Federations
-   Governments
-   Event organisers

Typical problems solved

-   Athlete management
-   Operations
-   Registrations
-   Scheduling
-   Communication
-   Assessments

------------------------------------------------------------------------

# Athlete Management

Current MVP

-   Athlete profiles
-   Coach management
-   Batch management
-   Training schedules
-   Manual assessments

Future

-   Performance analytics
-   Health
-   Nutrition
-   Injury management

------------------------------------------------------------------------

# Communication

Supports

-   Notifications
-   Email
-   SMS
-   WhatsApp
-   Forums
-   Announcements
-   Chat

------------------------------------------------------------------------

# Grievance

-   FA based ownership
-   Escalation Matrix
-   TAT
-   Media attachments
-   Notifications

------------------------------------------------------------------------

# Certification

-   Templates
-   Coordinate mapping
-   Bulk generation
-   DigiLocker
-   Verification

------------------------------------------------------------------------

# Credential & Accreditation

-   Credential generation
-   Accreditation cards
-   Venue access
-   Zone permissions

------------------------------------------------------------------------

# Security

RBAC

PBAC

ABAC

Workspace isolation

Project isolation

Functional Area isolation

Audit logs

------------------------------------------------------------------------

# Key Dependencies

Cannot create Industry without Master Admin.

Cannot create Project without Industry.

Cannot create Institution workspace before approval.

Cannot participate without Individual profile.

Cannot assign FA before Institution approval.

Cannot create Tasks before FA assignment.

Cannot use dependent modules before prerequisites.

------------------------------------------------------------------------

# Important Product Decisions

-   Individual is always the root identity.
-   Workspace switcher is central to UX.
-   Industry owns Projects.
-   Institution owns execution.
-   Functional Area is the operational unit.
-   Dynamic forms are project-specific.
-   Modules are dependency-driven.
-   One Default Project per Industry.
-   Multiple Event Projects allowed.
-   Command registration supported.
-   Progress based on completed Functional Areas.

------------------------------------------------------------------------

# Naming Standards

Use:

Industry

Institution

Individual

Functional Area

Sub Functional Area

Workspace

Role Switcher

Avoid replacing Industry with Organisation unless explicitly requested.

------------------------------------------------------------------------

# Phase 1 Scope

-   Core workspaces
-   Organisation Management
-   Project Management
-   Functional Areas
-   User Types
-   Dynamic Forms
-   Invitations
-   Join Requests
-   Command
-   Tasks
-   Notifications
-   Audit
-   Module assignment

------------------------------------------------------------------------

# Phase 2

-   Advanced workflows
-   Analytics
-   Automation
-   Performance dashboards
-   AI assistance
-   Advanced assessments

------------------------------------------------------------------------

# AI Guidance

When working on ATOM:

-   Treat PRD as functional truth.
-   Treat this document as architectural truth.
-   Preserve terminology.
-   Preserve workspace architecture.
-   Validate dependencies before proposing features.
-   Keep Industry → Project → FA hierarchy intact.
-   Never flatten permissions.
-   Every proposal must respect PBAC and ABAC.
