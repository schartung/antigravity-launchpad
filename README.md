# Antigravity Launchpad

A five-gate project scaffold for building GCP-native AI agents with Google Antigravity 2.0.

---

## What this is

A scaffold, not a starter app. It gives you:
- A gated build pipeline that prevents the agent from writing infrastructure before the architecture is locked
- Hard constraints that survive deep into Gate 4 when the agent is generating code
- A structured design review (Gate 3) that stress-tests the architecture before a line of Terraform is written
- Working rules tuned for GCP specifics — including a rule that prevents the agent from treating stale training data as fact

This scaffold is purpose-built for **AI agent projects** on GCP. The Gate 1 init skill classifies your project type and routes to the right questions, but the pipeline assumes you are building something with a model, memory, and users at the center. For general GCP infrastructure work, this will over-ask.

What it does not give you: any decisions about your project. Those come from you, via `/grill-me`.

---

## Prerequisites

- [Antigravity 2.0](https://antigravity.google/download) installed
- A GCP project with billing enabled
- `gcloud` CLI authenticated: `gcloud auth login && gcloud config set project YOUR_PROJECT_ID`
- GCP Developer Knowledge MCP server connected (see below)

### Connect the GCP Developer Knowledge MCP

The Developer Knowledge MCP gives the agent live access to Google's official developer documentation. Without it, Gate 2 runs on training data — which is stale for GCP specifics.

1. Enable the API in your GCP project: [console.cloud.google.com/apis/library/developerknowledge.googleapis.com](https://console.cloud.google.com/apis/library/developerknowledge.googleapis.com)
2. Create an API key restricted to the Developer Knowledge API
3. Add to `~/.gemini/antigravity/mcp_config.json`:

```json
{
  "mcpServers": {
    "google-developer-knowledge": {
      "httpUrl": "https://developerknowledge.googleapis.com/mcp",
      "headers": { "X-Goog-Api-Key": "YOUR_API_KEY" }
    }
  }
}
```

The MCP requires a GCP project and API key — it cannot be bundled into this scaffold. Set it up once and it is available across all projects.

---

## How to use this scaffold

### 1. Create your project from this template

Click **Use this template** on [github.com/schartung/antigravity-launchpad](https://github.com/schartung/antigravity-launchpad) and create a new repository under your account. Then clone it locally:

```bash
git clone https://github.com/YOUR_USERNAME/YOUR_REPO
cd YOUR_REPO
```

### 2. Open in Antigravity 2.0

Open the project folder as a new **Project** in Antigravity 2.0. Do not open a plain conversation — the Project binding is what loads AGENTS.md and the `.agents/` directory on every run.

### 3. Fill in the two placeholders in AGENTS.md

Open `AGENTS.md` and replace:
- `[FILL: project name]`
- `[FILL: one sentence — what this agent does and for whom]`

Everything else gets filled by the pipeline.

### 4. Gate 1 — Seal the technical brief

```
@gcp-project-init
```

Then:

```
/grill-me
```

`gcp-project-init` classifies your project type and primes the agent with the right questions. `/grill-me` inverts the dynamic — the agent interviews you. Answer in your own words. When the interview is complete, the agent produces `sealed-technical-brief.md` and writes the decisions back into the `## Sealed Decisions` section of `AGENTS.md`. Review it. Correct anything that does not match your intent. That file is the source of truth for Gates 2–4.

### 5. Gate 2 — Architecture and infrastructure schemas

With the brief sealed and the GCP Developer Knowledge MCP active:

```
Proceed to Gate 2. Draft the event-driven GCP architecture and infrastructure schemas based on the sealed technical brief.
```

The agent verifies all GCP-specific claims against the Developer Knowledge MCP, not training data.

### 6. Gate 3 — Locked architectural declaration

```
@multi-agent-brainstorming-gcp
```

Four reviewer roles run in sequence against the Gate 2 output. The Constraint Guardian verifies every hard constraint is enforced architecturally, not just assumed. The process produces a Decision Log and a disposition: `APPROVED`, `REVISE`, or `REJECT`. Do not proceed to Gate 4 on anything other than `APPROVED`.

### 7. Gate 4 — Production codebase

```
/goal Build the production codebase per the locked architectural declaration and Decision Log.
```

`/goal` runs to completion without prompting. The agent will pause and surface options at the conditions defined in AGENTS.md — do not skip those pauses.

### 8. Gate 5 — Loop retro

After the first real production use, run the retro. This is what closes the loop.

```
@loop-retro
```

Five questions — architecture delta, sealed decision regret, constraint gaps, init blind spots, pipeline signal. Produces two files: `retro-[project-name].md` (stays in your project repo) and `template-diff.md` (proposed changes back to this scaffold). Review the diff. Merge what is useful back into `antigravity-launchpad`. The loop closes when at least one change makes it back into the scaffold.

---

## Repository structure

```
├── README.md
├── AGENTS.md                                        ← primary SSoT, loaded every run
├── GEMINI.md                                        ← thin entry point, delegates to AGENTS.md
└── .agents/
    ├── rules/
    │   └── hard-constraints.md                      ← always-on, fires every agent invocation
    └── skills/
        ├── gcp-project-init/
        │   └── SKILL.md                             ← Gate 1: classifies project, primes /grill-me
        ├── multi-agent-brainstorming-gcp/
        │   └── SKILL.md                             ← Gate 3: four-reviewer design review
        └── loop-retro/
            └── SKILL.md                             ← Gate 5: retro that feeds back into the scaffold
```

---

## After Gate 1 completes

The sealed brief populates `AGENTS.md`'s `## Sealed Decisions` section. From that point forward, AGENTS.md is the contract. Do not modify sealed decisions directly — if something needs to change, surface it as a trade-off at the appropriate gate.
