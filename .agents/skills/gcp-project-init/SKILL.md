---
name: gcp-project-init
description: >
  Run before /grill-me at Gate 1. Classifies the project type, then primes
  the agent with the right questions for that type before the interview begins.
  Covers shared decisions every GCP project must seal, plus type-specific
  extensions for AI agents, data pipelines, container APIs, and event-driven
  systems. Does not answer the questions — defines the shape of what /grill-me
  must extract from the user.
tags: [gcp, architecture, gate-1, initialization, requirements]
trigger: on_demand
---

# GCP Project Init

## Purpose

Classify the project, then define the questions that must be answered before Gate 1 can close. `/grill-me` does the interviewing — this skill tells it what a complete sealed brief looks like for this project type.

---

## Step 1 — Classify

Before asking any questions, classify the project on two axes. State the classification in one line before proceeding.

### Project Type
- **A** — AI Agent: a system with a model, memory, and users at the center; acts autonomously or ambiently
- **D** — Data: pipelines, warehouses, analytics, transformation, reporting
- **C** — Container API: a backend service, REST/gRPC API, or microservice
- **E** — Event-driven: a system primarily organized around events, triggers, and async processing
- **H** — Hybrid: more than one of the above in meaningful proportion

### Complexity
- **1** — Focused: single workload, small team, clear scope
- **2** — Moderate: multiple components, some integration, a few stakeholders
- **3** — Complex: many components, external integrations, enterprise constraints

> Example output: `"A2 — AI Agent, moderate complexity"`

Depth and question count scale with complexity. A 1-project gets the minimum viable question set. A 3-project gets the full set plus probing follow-ups.

---

## Step 2 — Core Questions (All Project Types)

Every project must seal these before Gate 1 closes.

### 1. Users
- Who are the users and what are their roles?
- How are they identified at runtime? (directory lookup, SSO, manual config, anonymous?)
- Is there a per-user personalization or isolation requirement, or is the system uniform?

### 2. Interface
- How do users or systems interact with this? (API, UI, messaging platform, ambient/background, CLI, none?)
- Who initiates — the user, the system, or both?
- Any latency expectations from the user's perspective?

### 3. Security and Privacy
- How sensitive is the data this system handles?
- What is the isolation requirement? (shared with row-level security, per-user encryption, full cryptographic isolation, none?)
- Who should never be able to access a user's data even in an admin context?

### 4. Compute Posture
- Request-driven (scale-to-zero acceptable) or always-on (persistent process required)?
- Long-running background tasks separate from request handling?
- Any throughput or cold-start constraints?

### 5. Lifecycle
- What happens to data when a user or project is decommissioned?
- Any regulatory or retention requirements?

### 6. Commit Convention
- Existing convention? (Conventional Commits, plain language, other?)
- Will CI/CD automation depend on commit message format?
- If none: default is plain-language descriptive messages — complete sentences, no prefixes.

---

## Step 3 — Type-Specific Extensions

Surface only the extension block that matches the classified project type. Skip the rest.

---

### Extension A — AI Agent

Surface for types: A, and any H that includes an agent component.

#### A1. Agent Behavior
- Does the agent act on user request, proactively, or both?
- What does "acting accordingly" mean for this agent — what signals should change its behavior per user?
- What does a successful interaction look like vs. a failed one?

#### A2. Memory and State
- What does the agent need to remember across sessions? (conversation history, user preferences, behavioral patterns, domain knowledge?)
- How long must it persist? (session only, months, indefinitely?)
- Any existing data the agent must incorporate?

#### A3. Interoperability
- Does this agent need to communicate with other agents or external systems?
- If yes: what protocol? (A2A, webhook, direct API, none?)
- What capabilities need to be exposed externally?

#### A4. Model and SDK
- Which Gemini model(s)? (Flash for throughput, Pro for depth, or both?)
- Any embedding or multimodal requirements?
- Any reason not to use the google-genai SDK with ADC?

#### A5. Break-Glass
- Is there a scenario where user-isolated agent state must be accessed by a third party?
- Who authorizes it, under what conditions, and how is it audited?

---

### Extension D — Data Pipeline

Surface for types: D, and any H that includes a significant data component.

#### D1. Data Sources
- Where does data originate? (internal systems, external APIs, user uploads, streaming events?)
- What are the ingestion patterns? (batch, streaming, CDC, API pull?)
- Any existing schemas or contracts that must be honored?

#### D2. Transformation and Quality
- What transformations are required? (aggregation, enrichment, normalization, ML inference?)
- What are the data quality requirements and how are they enforced?
- Any lineage or auditability requirements?

#### D3. Consumers
- Who or what reads the output? (dashboards, APIs, ML models, other pipelines?)
- What are the freshness requirements? (real-time, near-real-time, daily batch?)

#### D4. Scale and Cost
- Approximate data volume today and at 10x growth?
- Any cost constraints that would rule out certain processing patterns?

---

### Extension C — Container API

Surface for types: C, and any H that includes a service/API component.

#### C1. API Contract
- REST, gRPC, GraphQL, or other?
- Who are the API consumers — internal services, external clients, both?
- Any existing API contracts or versioning requirements?

#### C2. State and Persistence
- Is this service stateless or does it own data?
- If stateful: what is the data model and access pattern?

#### C3. Traffic and Scaling
- Expected request volume and traffic shape (steady, spiky, scheduled)?
- Any SLA or availability requirements?
- Synchronous only or does it need async job processing?

#### C4. Integration
- What downstream services does this call?
- Any external APIs, third-party services, or legacy systems in the dependency chain?

---

### Extension E — Event-Driven

Surface for types: E, and any H with a significant eventing component.

#### E1. Event Sources and Schema
- What produces the events? (user actions, system state changes, external webhooks, schedules?)
- Is there an existing event schema or does one need to be designed?
- Approximate event volume and expected growth?

#### E2. Processing and Ordering
- Is event ordering significant?
- Are there exactly-once processing requirements?
- What is the acceptable processing latency?

#### E3. Failure Handling
- What happens to a failed event? (retry, dead-letter, discard?)
- What is the acceptable data loss tolerance?
- Any replay requirements?

---

## Step 4 — Instructions for the Agent

When this skill is invoked:

1. Classify the project and state it in one line.
2. Confirm to the user which question blocks will be covered.
3. When running `/grill-me`, use the Core Questions plus the appropriate Extension block. Do not surface extension blocks that do not match the project type.
4. Group and sequence questions naturally — do not recite the numbered list. Cover all questions before sealing.
5. When the interview is complete, produce `sealed-technical-brief.md` with:
   - Project classification
   - A section for each question with the user's answer
   - Any architectural implications drawn from those answers
   - A clear statement: "Gate 1 is sealed. Proceed to Gate 2."
6. Write the sealed decisions into the `## Sealed Decisions` section of `AGENTS.md`.

Do not fill in answers. Every answer must come from the user.
