---
name: loop-retro
description: >
  Run after the first real production use of a GCP agent built with this
  harness. Structured retrospective that produces a concrete diff against the
  template — proposed updates to AGENTS.md, hard-constraints.md, or
  gcp-project-init — so the harness improves from what the project learned.
  This is what closes the loop.
tags: [retro, loop-engineering, gate-5, template-improvement]
trigger: on_demand
---

# Loop Retro — Gate 5

## Purpose

The pipeline is not complete at Gate 4. What the agent learned in production — about the architecture, the users, the sealed decisions, the constraints — has value for the next project only if it flows back into the template.

This skill closes the loop. It produces a concrete diff against the harness, not a narrative summary.

---

## Pre-Conditions

Before invoking this skill:
- The agent has been running in production with real users
- At least one full interaction cycle has completed
- The Decision Log from Gate 3 is available

---

## The Five Retro Questions

Each question must produce a concrete answer — not "things went well." If the answer is "nothing," say so explicitly.

### 1. Architecture delta
What did the architecture get wrong that the Decision Log did not catch?
- Race conditions, quota issues, cold-start problems that emerged in production
- Services that behaved differently than the MCP documentation described
- Integrations that required more complexity than the schemas anticipated
- **Output:** specific changes to the Gate 2 schema template or Gate 3 reviewer focus areas

### 2. Sealed decision regret
Which sealed decision from Gate 1 caused the most rework, confusion, or limitation?
- The one you would re-open if you were starting over
- Why it seemed right at Gate 1 and what reality showed instead
- **Output:** a new question for `gcp-project-init` that would have surfaced this earlier, or a sharper framing of an existing one

### 3. Constraint gaps
What should be in `hard-constraints.md` that wasn't?
- Things the agent did that surprised you or required correction
- Patterns that emerged in the code that violated the spirit of the constraints without violating the letter
- Constraints that were too broad and caused unnecessary friction
- **Output:** additions, removals, or rewrites to `hard-constraints.md`

### 4. Init blind spots
What question should `gcp-project-init` be asking at Gate 1 that it didn't?
- Decisions that had to be made mid-build that should have been sealed upfront
- Assumptions the agent made that a better interview question would have caught
- **Output:** proposed additions to the core questions in `gcp-project-init`

### 5. Loop signal
What would make the next project noticeably faster or safer?
- Not "better prompts" — specific structural changes to the pipeline
- A new gate, a new reviewer role, a new always-on rule
- Something the Constraint Guardian missed at Gate 3 that a new reviewer role would catch
- **Output:** a specific proposed change to the pipeline structure in AGENTS.md

---

## Output Format

Produce two artifacts:

### `retro-[project-name].md`
The full retrospective answers. Saved in the project repo, not the template. This is the project's record.

### `template-diff.md`
The proposed changes to the template — concrete and actionable. Format:

```
## hard-constraints.md
ADD: [exact text of new constraint]
REMOVE: [exact text of removed constraint]
REWRITE: [old text] → [new text]

## gcp-project-init / Core Questions
ADD QUESTION: [exact text]
REWRITE QUESTION [N]: [old text] → [new text]

## AGENTS.md
[proposed structural changes]

## Pipeline
[proposed gate additions or modifications]
```

---

## Instructions for the Agent

1. Run through all five questions with the user. Do not skip any. If a question yields nothing, confirm that explicitly before moving on.
2. Produce `retro-[project-name].md` with the full answers.
3. Produce `template-diff.md` with only concrete proposed changes — no narrative, no hedging.
4. Tell the user: *"Gate 5 complete. Review template-diff.md. Apply any changes you accept back to the template repo and open a PR or commit directly to `antigravity-launchpad`."*

The loop closes when `template-diff.md` is reviewed and at least one change makes it back into the template. Not every suggestion will be accepted — that is expected. The retro's job is to generate the diff. The human's job is to decide what to merge.
