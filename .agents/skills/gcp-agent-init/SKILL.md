---
name: gcp-agent-init
description: >
  Run this skill before /grill-me at Gate 1. Primes the agent with the eight
  architectural questions that must be answered before a GCP agent project can
  be designed. Does not answer the questions — defines the shape of what
  /grill-me needs to extract from the user.
tags: [gcp, architecture, gate-1, initialization, requirements]
trigger: on_demand
---

# GCP Agent Init

## Purpose

Prime the agent before the `/grill-me` interview. Define the eight load-bearing architectural questions for any GCP agent project so that `/grill-me` extracts them all — in the user's own words — before Gate 1 closes.

This skill does not answer anything. It tells the agent what a complete Gate 1 output looks like.

---

## The Eight Required Decisions

A sealed technical brief from Gate 1 must contain a clear answer to each of these. If any remain ambiguous after the `/grill-me` interview, the agent must ask again before sealing.

### 1. Users
- Who are the users?
- How many? What are their roles?
- How are they identified at runtime? (email, directory lookup, SSO, other?)
- Is there a per-user personalization requirement, or is this uniform?

### 2. Interface Layer
- How do users interact with the agent? (messaging platform, API, ambient/background, voice, other?)
- What specific tool or platform? (Microsoft Teams, Slack, custom app, none?)
- Do users initiate interactions, or does the agent act proactively?

### 3. Agent Interoperability
- Does this agent need to communicate with other agents or external systems?
- If yes: what protocol? (A2A, webhook, direct API, none?)
- What capabilities need to be exposed to external systems?

### 4. Security and Privacy Posture
- How sensitive is the per-user data? (operational, personal, confidential, regulated?)
- What is the isolation requirement? (shared DB with row-level security, per-user encryption, full cryptographic isolation?)
- Who should never be able to access a user's data even in an admin context?
- Is there a break-glass / emergency access requirement? If so, who approves it?

### 5. Compute Posture
- Is this request-driven (scale-to-zero acceptable) or always-on (persistent process required)?
- Are there long-running background tasks separate from request handling?
- Any latency or throughput constraints that would rule out cold-start compute?

### 6. Data and Memory Strategy
- What does the agent need to remember? (conversation history, user preferences, behavioral patterns, domain knowledge?)
- How long does it need to persist? (session only, months, indefinitely?)
- Any existing data systems it must integrate with?

### 7. Model and SDK
- Which Gemini model(s)? (Flash for throughput, Pro for reasoning depth, or both?)
- Any embedding or multimodal requirements?
- Any reason not to use the google-genai SDK with ADC?

### 8. Break-Glass and Lifecycle
- What happens when a user leaves the organization? (data retained, archived, destroyed?)
- Is there a recovery scenario where user-isolated data must be accessed by a third party?
- Who has authority to authorize that access and under what conditions?

### 9. Commit Convention
- Does the team have an existing commit message convention? (Conventional Commits, plain language, other?)
- Will CI/CD automation depend on commit message format (changelogs, semver bumps)?
- If no convention exists: default is plain-language descriptive messages — complete sentences, no prefixes.

---

## Instructions for the Agent

When this skill is invoked:

1. Confirm to the user that you have loaded the eight required decisions.
2. Tell the user you are about to run `/grill-me` and that you will use these eight decisions as the framework for the interview.
3. When running `/grill-me`, structure your questions to extract a clear answer for each of the eight decisions above. You do not have to ask them as a numbered list — group and sequence them naturally — but do not seal the brief until all eight have answers.
4. When the interview completes, produce `sealed-technical-brief.md` with:
   - A section for each of the eight decisions with the user's answer
   - Any architectural implications you drew from those answers
   - A clear statement: "Gate 1 is sealed. Proceed to Gate 2."
5. Paste the sealed decisions into the `## Sealed Decisions` section of `AGENTS.md`.

Do not fill in answers yourself. Every answer must come from the user.
