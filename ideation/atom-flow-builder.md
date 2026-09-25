# ATOM V2 – Flow Builder

## Overview

The Flow Builder is a core project configuration module in ATOM that enables Industries to visually configure how a project will operate before invitations, registrations, nominations, and other project activities begin.

In ATOM V2, the Flow Builder is no longer limited to hierarchy management and quota assignment for National Games. Instead, it becomes a generic project configuration engine that any Industry can use, regardless of domain.

The Flow Builder will consist of two major tabs:

1. Hierarchy
2. Validation & Quota Setup

Each tab serves a distinct purpose.

---

# Objectives

The Flow Builder should allow an Industry to:

- Define project hierarchy.
- Invite Institutions and Individuals.
- Configure Sports available in the project.
- Configure User Types participating in the project.
- Configure custom registration forms for each User Type.
- Configure project-level quota rules.
- Configure Institution-level quota allocation.
- Provide configurations that other ATOM modules consume.

The Flow Builder becomes the single source of truth for project configuration.

---

# Tab 1 – Hierarchy

## Purpose

The Hierarchy tab allows the Industry to build the project hierarchy visually.

This hierarchy defines:

- Institutions participating in the project.
- Individual nodes.
- Parent-child relationships.
- Invitation flow.

The hierarchy is completely independent of quota configuration.

---

## Main Canvas

The Hierarchy tab contains a visual drag-and-drop canvas.

Each node represents either:

- Institution
- Individual

Nodes can be connected to define reporting and participation hierarchy.

Example

Industry

↓

National Federation

↓

State Association

↓

District Association

↓

Athlete

---

## Institution Node

Institution node should display:

- Institution Name
- Institution Type
- Invitation Status
- Member Count
- Assigned Quota (future enhancement)
- Quick Actions

Actions

- Invite Institution
- Edit
- Delete
- View Details

---

## Individual Node

Displays

- Individual Name
- User Type
- Invitation Status

Actions

- Invite Individual
- Edit
- Remove

---

## Invitation Flow

Instead of immediately creating members inside the hierarchy, invitations are sent.

Invitation states

- Draft
- Pending
- Accepted
- Rejected
- Expired

The node should visually indicate invitation status.

Example

Pending Invitation

Accepted

Rejected

---

## Institution Sub Canvas

Clicking an Institution node opens a dedicated sub-canvas.

Inside this canvas the Institution can:

- Invite child Institutions
- Invite Individuals
- Build its own hierarchy

This creates hierarchical project ownership.

---

## Future Enhancements

- Quota allocation from hierarchy
- Institution Dashboard
- Hierarchy templates
- Bulk import

---

# Tab 2 – Validation & Quota Setup

## Purpose

This tab configures all validations required before participants can successfully join a project.

Unlike Hierarchy, this is not a drag-and-drop relationship builder.

Instead, it is a workflow consisting of configuration nodes.

Each node represents a configuration module.

---

## Main Workflow

The workflow contains sequential nodes.

Sports

↓

Registration Configuration

↓

Quota Setup

Each node has status.

States

- Pending
- Draft
- Published
- Completed

A node becomes Completed once successfully configured and published.

---

# Node 1 – Sports

## Purpose

Configure sports available inside the project.

Only configured sports become available for:

- Invitations
- Registrations
- Quota
- Scheduling
- Accreditation
- Athlete Management

---

## Page Layout

Left Navigation

Sports Library

Right Workspace

Sport Configuration

---

## Features

### Sports Library

Searchable list of sports.

Examples

- Athletics
- Boxing
- Swimming
- Hockey

Industry selects required sports.

---

### Event Configuration

Every Sport contains Events.

Example

Athletics

- 100m
- 200m
- Relay

Swimming

- 100m Freestyle
- 200m Butterfly

---

### Discipline Configuration

Every Event can contain Disciplines or Categories.

Example

Athletics

100m

- Men
- Women

Boxing

- 51kg Men
- 57kg Women

Swimming

100m Freestyle

- Men
- Women

---

## Publish

Once saved and published

Sports Node

changes to

Completed

---

## Output

The Sports configuration becomes available to:

- Registration Configuration
- Quota Setup
- Registration Module
- Invitation Module

---

# Node 2 – Registration Configuration

(Previously User Type & Form Setup)

## Purpose

Configure who can participate in the project and what information they must submit during registration.

This page combines User Type configuration and Form Builder.

---

# Section A – User Type Selection

Industry selects User Types from Master Library.

Example

Athlete

Coach

Official

Volunteer

Physio

Manager

Only selected User Types become available throughout the project.

---

# Section B – Sub User Types

Each User Type can contain Subtypes.

Example

Athlete

- Senior Athlete
- Junior Athlete
- Para Athlete

Coach

- Head Coach
- Assistant Coach

Industry may create additional custom Subtypes.

Example

Athlete

Elite Athlete

Wildcard Athlete

These remain project-specific.

---

# Section C – Registration Form Builder

Each User Type or Subtype can have a custom joining form.

Example

Senior Athlete

Registration Form

- Sport
- Discipline
- Jersey Size
- Passport Number
- Medical Certificate

Coach

Registration Form

- Certification
- Experience
- Resume

The form is shown only when an Individual joins using that User Type/Subtype.

---

## Form Builder Requirements

Support:

- Text
- Number
- Email
- Phone
- Date
- Dropdown
- Multi Select
- Radio
- Checkbox
- File Upload
- Image Upload
- Section
- Rich Text
- Declaration
- Signature (future)

---

## Standard Fields vs Custom Fields

The Form Builder should support two categories of fields.

### Standard Fields

Provided by ATOM.

Examples

Athlete

- Sport
- DOB
- Gender
- Height

Coach

- Certification

These fields are mapped to Individual Profile.

When reused in another project they should automatically pre-fill.

---

### Custom Fields

Industry-specific fields.

Examples

- Jersey Size
- Food Preference
- Arrival Date
- Accommodation

These remain project-specific.

Stored only against the Project Registration.

No profile mapping.

---

## Publish

Once published

Registration Configuration node becomes Completed.

---

## Output

Consumed by:

- Invitation Module
- Join Request
- Registration
- Individual Profile
- Athlete Management

---

# Node 3 – Quota Setup

## Purpose

Configure project participation rules and quota allocation.

Quota Setup is NOT a visual canvas.

Instead, it is a structured configuration interface.

---

# Layout

Two-panel layout.

Left

Navigation Tree

Right

Configuration Workspace

---

# Left Navigation

Displays configured Sports hierarchy.

Example

Athletics

Track

100m Men

100m Women

Relay Mixed

Swimming

Boxing

This hierarchy is automatically generated from Sports Configuration.

Selecting an Event loads its quota configuration.

---

# Right Workspace

Contains quota configuration for selected Event.

Example

Sport

Athletics

Discipline

Track

Event

100m Men

Gender

Male

(Read Only)

Gender is automatically derived from Event configuration.

No manual selection required.

---

# Allowed User Types

Displays User Types configured in Registration Configuration.

Example

Athlete

Coach

Physio

Manager

Only enabled User Types appear.

---

# Quota Cards

Each User Type has an expandable configuration card.

Example

Athlete

Minimum

Maximum

Participation Type

Individual

Waiting List

Coach

Minimum

Maximum

Support Staff

Physio

Maximum

1

---

# Participation Type

Supported values

- Individual
- Team
- Mixed

Future values can be added.

---

# Quota Constraints

The system should support configurable quota parameters.

Initial version

- Minimum
- Maximum
- Participation Type

Future extensible parameters

- Age Category
- Weight Category
- Nationality
- State
- Experience
- Qualification
- Disability Category

The quota engine should be extensible rather than hardcoded.

---

# Institution Allocation

Quota is configured in two levels.

## Level 1

Project Quota

Industry defines total quota.

Example

100m Men

Athlete

Max

16

Coach

4

---

## Level 2

Institution Allocation

Industry distributes quota to participating Institutions.

Example

NSF Delhi

Athlete

4

Coach

1

NSF Karnataka

Athlete

3

Coach

1

Remaining quota stays with Industry.

Institutions cannot exceed allocated quota.

---

# Validation Rules

Before saving quota:

Maximum ≥ Minimum

Institution quota ≤ Project quota

Allocated quota ≤ Remaining quota

Only configured User Types allowed

Only configured Sports allowed

Only configured Events allowed

---

# Publish

Once quota configuration is published

Quota Setup node becomes Completed.

---

# Flow Between Nodes

Sports

↓

Registration Configuration

↓

Quota Setup

Sports Configuration feeds Registration Configuration.

Registration Configuration feeds Quota Setup.

Quota Setup validates against both.

---

# Module Dependencies

The Flow Builder is a foundational module.

Its configurations are consumed by multiple ATOM modules.

Sports Configuration

Consumed by

- Registration
- Invitation
- Schedule
- Accreditation
- Athlete Management

Registration Configuration

Consumed by

- Registration
- Join Requests
- Invitations
- Individual Profile
- Athlete Management

Quota Setup

Consumed by

- Registration Validation
- Invitation Validation
- Join Requests
- Institution Dashboard
- Athlete Management
- Reports

---

# Design Principles

The Flow Builder should:

- Be generic and industry-agnostic.
- Support both sports and non-sports projects.
- Avoid hardcoded National Games workflows.
- Use reusable configuration artifacts.
- Allow future extension without schema redesign.
- Separate hierarchy management from validation and quota configuration.
- Make each configuration independently publishable.
- Provide a clear visual status for project readiness.

---

# Out of Scope (V2)

The following are intentionally excluded from this version:

- Nomination Configuration
- Approval Workflow Builder
- Qualification Criteria Engine
- Rule-based Eligibility Engine
- Dynamic Workflow Automation
- AI-assisted Configuration
- Bulk Quota Import/Export
- Quota Transfer Between Institutions
- Version History and Rollback

These may be introduced in future iterations of the Flow Builder.