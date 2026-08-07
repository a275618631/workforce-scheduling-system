# Constraint-Based Workforce Scheduling System

A rule-driven scheduling and operations system for complex rotating-shift environments.

---

## Problem

Rotating-shift workforce scheduling in operational contexts — such as 24-hour public-service environments — involves constraints that cannot be reduced to a simple timetable generator. Personnel have varying qualifications, preferred start times, and leave requirements. Duty assignments must satisfy coverage minimums, rest-period rules, and organizational policies. Manual scheduling is error-prone at scale, and violations may not surface until a shift is already underway.

The system addresses this by modeling constraints explicitly, generating baseline schedules from rules, and surfacing violations and warnings as first-class states rather than silent failures.

## What this demonstrates

| Capability | Evidence |
| --- | --- |
| Requirements analysis | Constraints modeled from operational policy documentation |
| Rule engine design | Coverage, rest, qualification, locked-duty, and organizational rules |
| Personnel abstraction | Status lifecycle, qualifications, preferences |
| Calendar workflow | Monthly generation, day/shift/segment granularity |
| Violation handling | Red/warning/incomplete states with per-rule explanations |
| Iterative evolution | Multiple delivery phases building on a stable core |
| Validation | The underlying private implementation reached 56 validated automated test cases |

## Architecture

```text
Personnel Registry
  → Qualification / Status model
  → Preference model

Scheduling Engine
  → Monthly workspace
  → Rule-based baseline generation
  → Segment and shift model (8h / 12h / split)
  → Manual override and locked-duty support

Validation Layer
  → Coverage check (per shift, per day)
  → Rest-period check
  → Duplicate assignment check
  → Qualification constraint check
  → Organizational rule check
  → Violation / Warning / Incomplete classification

Calendar View
  → Month overview
  → Day detail
  → Swap candidate visibility
  → Export (CSV / JSON / Excel XLSX)
```

## Key Engineering Decisions

**Explicit constraint modeling over implicit rules**
Each scheduling constraint is represented as a distinct rule with a code, a description, and a classification (violation vs. warning). This makes the system auditable — any flagged assignment can be traced to a specific rule — and allows rules to be added or modified without changing the core generation logic.

**Segment model for shift flexibility**
Rather than hardcoding shift patterns, the system uses a segment model that supports 8-hour three-shift patterns, 12-hour day/night patterns, and split segments (e.g., two people sharing a 12-hour coverage window). This reflects real operational flexibility without overfitting to a single pattern.

**Status lifecycle for personnel**
Personnel carry a status (ACTIVE / INACTIVE / ON_LEAVE) that gates their eligibility for assignment. The system does not silently skip ineligible personnel; it surfaces them explicitly in the swap candidate view with the reason for exclusion.

**Iterative delivery phases**
The system was built in phases: personnel management → baseline generation → rule validation → manual assignment and swap → calendar overview → export. Each phase extended the core without breaking earlier functionality.

## Validation

The underlying private implementation reached **56 validated automated test cases**, exercising personnel data transfer, scheduling logic, dynamic features, monthly workspace state, calendar overview, and deployment behavior.

All sample data used during development is **synthetic and anonymized**. No real personnel records, duty rosters, or operational data are included in this repository or referenced in validation.

## Current Status

**Working system — private implementation**

The scheduling engine, rule validation, calendar view, and export functions are implemented and validated in the private repository. This companion documents the architecture and engineering approach.

## Privacy & Publication Boundary

This companion repository contains:
- Architecture documentation
- Engineering decision rationale
- Anonymized synthetic examples (where included)

It does not contain:
- The private source code
- Real personnel records
- Real duty rosters or shift assignments
- Non-public operational policies
- Any account, credential, or access material

The system was originally developed around a rotating-shift public-sector scheduling use case. All identifying operational details remain private.

→ [Publication Boundary](docs/publication-boundary.md) | [Case Study](docs/case-study.md)
