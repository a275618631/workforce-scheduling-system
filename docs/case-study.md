# Case Study: Constraint-Based Workforce Scheduling System

## Problem

Rotating-shift scheduling in 24-hour operational environments requires satisfying simultaneous constraints: daily coverage minimums by shift, rest periods between assignments, qualification requirements for specific roles, organizational leave policies, and the ability to handle exceptions such as locked duties and manual overrides.

The original use case was a multi-person rotating-shift public-sector environment with monthly planning cycles. Manual scheduling was the existing practice.

## Constraints

The system models the following constraints explicitly:

- **Coverage**: Each shift must meet a minimum headcount per day
- **Rest period**: Personnel must have sufficient rest between consecutive assignments
- **Qualification**: Certain duties require specific qualifications (e.g., supervision, emergency response, driving)
- **Status**: Only ACTIVE personnel are eligible for new assignments
- **Locked duty**: Some assignments are fixed and must not be moved by the scheduler
- **Organizational rules**: Configurable per-unit rules that layer on top of the base constraints

Violations and warnings are distinct: a violation is a hard constraint failure; a warning is a softer concern that a human planner should review.

## Architecture

The system is organized into four layers:

**Personnel Registry** stores staff records, qualifications, status lifecycle, and scheduling preferences.

**Scheduling Engine** generates a monthly baseline from rules, is designed to support multiple shift patterns and split-segment coverage, and enforces locked assignments before generating the remainder.

**Validation Layer** evaluates every generated assignment against all active rules, classifies each outcome as a violation, warning, or incomplete state, and surfaces per-rule explanations for any flagged assignment.

**Calendar View** provides a month overview, day detail, swap candidate visibility with eligibility reasons, and export in CSV, JSON, and Excel XLSX formats.

## What I Designed

- Constraint modeling as first-class rules with codes, descriptions, and classification tiers
- A segment model flexible enough to support 8-hour, 12-hour, and split-segment patterns without hardcoding shift structures
- A personnel status lifecycle that makes ineligibility explicit and auditable
- A swap candidate view that shows both eligible and ineligible personnel, with reasons
- Iterative delivery in phases: each phase extended the core without breaking earlier validated behavior

## Key Engineering Decisions

**Violations as data, not side effects.** The validation layer returns structured results — rule code, affected assignment, classification — rather than silently excluding invalid schedules or crashing on constraint violations. This makes the system useful as a planning aid even when coverage cannot be fully satisfied.

**Separation of generation and validation.** The scheduling engine generates a candidate schedule; the validation layer evaluates it. These are distinct steps. This allows a human planner to modify assignments and re-run validation without re-triggering full generation.

**Synthetic data only.** All personnel records used in development are synthetic (S001–S030, generated names, no real identifiers). The system accepts imported CSV/JSON/XLSX but does not ship with real operational data.

## Validation

The underlying private implementation reached 56 validated automated test cases covering personnel data transfer, scheduling rule logic, dynamic feature behavior, monthly workspace state, calendar overview, and deployment behavior.

All tests were run against synthetic data. No real personnel or operational data is included.

## Current Status

**Working system — private implementation, Companion repo published**

The private implementation is functional and validated. This companion documents the architecture, constraints, and engineering rationale.

## Lessons Learned

Modeling constraints explicitly from the start — rather than embedding them in generation logic — made each delivery phase substantially easier. Adding a new rule type did not require touching the generation engine; it only required implementing the rule interface and registering it with the validation layer.

The iterative phase structure also helped: because each phase left earlier functionality intact, new features could be validated against a known baseline rather than against a moving target.
