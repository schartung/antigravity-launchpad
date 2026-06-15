---
name: multi-agent-brainstorming-gcp
description: >
  Run at Gate 3 after architecture schemas are drafted. Runs four constrained
  reviewer roles in sequence — Skeptic, Constraint Guardian, User Advocate,
  Integrator — against the proposed architecture. Produces a Decision Log and
  a final disposition: APPROVED, REVISE, or REJECT. Do not proceed to Gate 4
  on anything other than APPROVED.
tags: [architecture, review, gcp, security, design-validation, gate-3]
trigger: on_demand
---

# Multi-Agent Architecture Review (GCP)

## Purpose

Transform a single-agent GCP architecture proposal into a review-validated design. This is sequential, gated design review with enforced roles — not brainstorming.

---

## Pre-Conditions

Before invoking this skill:
- Gate 2 output exists: architecture schemas and infrastructure designs are drafted
- `AGENTS.md` and `.agents/rules/hard-constraints.md` are loaded and active
- `sealed-technical-brief.md` exists and is complete

---

## The Four Reviewer Roles

### 1. Skeptic / Challenger
Assume this architecture fails in production. Find the failure modes.
- Focus: hidden dependencies, race conditions, quota limits, cold-start risks, partial failure states, the thing that breaks at 2x load
- May NOT: propose redesigns, add features, or offer alternative architectures
- Prompt: *"Assume this design fails. What breaks first, and why?"*

### 2. Constraint Guardian
Enforce the hard constraints in `AGENTS.md` and `hard-constraints.md` line by line.
- For each constraint: is it enforced architecturally, or merely assumed at the application layer?
- Focus on: per-user cryptographic isolation, no-egress posture, no-admin-backdoor, no license-tax middleware, no hardcoded identifiers, secrets management
- Also verify: every GCP service referenced in the architecture was confirmed against the Developer Knowledge MCP, not training data
- May NOT: debate product goals, suggest features, or optimize cost at the expense of the security posture

### 3. User Advocate
Represent the end users as described in the sealed technical brief.
- Focus: what the user actually experiences vs. what the architecture assumes about them; silent failure modes the user would never know about; friction at entry points; what happens on first use vs. steady state
- May NOT: redesign the architecture, add features, or override stated goals

### 4. Integrator / Arbiter
Resolve all open objections. Produce the Decision Log and final disposition.
- Review: full architecture + all reviewer objections + Primary Designer responses
- For each objection: explicitly ACCEPTED or REJECTED with rationale
- Declare final disposition: `APPROVED`, `REVISE`, or `REJECT`
- If REVISE or REJECT: state exactly what must change before re-entry
- May NOT: invent new requirements or reopen locked decisions without cause

---

## Process

### Phase 1 — Architecture Presented
Primary agent presents the Gate 2 output. No reviewers yet. Decision Log initialized.

### Phase 2 — Sequential Review
Invoke reviewers one at a time, in order. After each:
- Feedback must be explicit and scoped to a specific design decision or assumption
- No new features or scope may be introduced
- Primary agent must respond to every objection and update the Decision Log before the next reviewer is invoked

Order: Skeptic → Constraint Guardian → User Advocate

### Phase 3 — Integration
Arbiter reviews final design + Decision Log + all unresolved objections. Issues disposition.

---

## Decision Log Format

For every non-trivial decision:

```
Decision: [what was decided]
Alternatives considered: [what else was on the table]
Objections raised: [who raised what]
Resolution: [accepted / rejected with rationale]
```

No design exits this skill without a completed Decision Log.

---

## Exit Criteria

All must be true before exiting:

- [ ] All four reviewer roles have been invoked
- [ ] Every objection is resolved or explicitly rejected with rationale
- [ ] Decision Log is complete
- [ ] Arbiter has declared a disposition: `APPROVED` / `REVISE` / `REJECT`

If `REVISE` or `REJECT`: do not proceed to Gate 4. State what must change before re-entry.
