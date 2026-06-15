# GCP Agent Harness

A reusable project harness for building production-ready, GCP-native AI agents with Antigravity 2.0 and Gemini. Enforces a four-gate pipeline from raw concept to production codebase.

---

## What this is

A template, not a starter app. It gives you:
- A gated build pipeline that prevents the agent from writing infrastructure before the architecture is locked
- Hard constraints that survive deep into Gate 4 when the agent is generating code
- A structured design review (Gate 3) that stress-tests the architecture before a line of Terraform is written
- Working rules tuned for GCP specifics — including a rule that prevents the agent from treating stale training data as fact

What it does not give you: any decisions about your project. Those come from you, via `/grill-me`.

---

## Prerequisites

- [Antigravity 2.0](https://antigravity.google/download) installed
- GCP Developer Knowledge MCP server connected (see below)
- A GCP project with billing enabled
- `gcloud` CLI authenticated: `gcloud auth login && gcloud config set project YOUR_PROJECT_ID`

### Connect the GCP Developer Knowledge MCP

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

---

## How to use this template

### 1. Clone and initialize

```bash
git clone https://github.com/schartung/gcp-agent-harness my-project
cd my-project
git remote set-url origin https://github.com/YOUR_USERNAME/YOUR_REPO
git init
```

### 2. Open in Antigravity 2.0

Open the project folder as a new Project in Antigravity 2.0. Do not open a plain conversation — the Project binding is what loads AGENTS.md and the `.agents/` directory on every run.

### 3. Gate 1 — Seal the technical brief

Run the init skill, then `/grill-me`. In that order.

```
@gcp-agent-init
```

Then:

```
/grill-me
```

The init skill primes the agent with the questions that need answers before Gate 2. `/grill-me` inverts the dynamic — the agent interviews you. Answer in your own words. When the interview is complete, the agent produces `sealed-technical-brief.md`. Review it. Correct anything that doesn't match your intent. That file is the source of truth for Gates 2–4.

### 4. Gate 2 — Architecture and infrastructure schemas

With the brief sealed and the GCP Developer Knowledge MCP active:

```
Proceed to Gate 2. Draft the event-driven GCP architecture and infrastructure schemas based on the sealed technical brief.
```

The agent will verify all GCP-specific claims against the Developer Knowledge MCP, not training data.

### 5. Gate 3 — Locked architectural declaration

```
@multi-agent-brainstorming-gcp
```

Four reviewer roles run in sequence against the Gate 2 output. The Constraint Guardian verifies every hard constraint is enforced architecturally, not just assumed. The process produces a Decision Log and a disposition: `APPROVED`, `REVISE`, or `REJECT`. Do not proceed to Gate 4 on anything other than `APPROVED`.

### 6. Gate 4 — Production codebase

```
/goal Build the production codebase per the locked architectural declaration and Decision Log.
```

`/goal` runs to completion without prompting. The agent works against the locked declaration. It will pause and surface options at the conditions defined in AGENTS.md — do not skip those pauses.

---

## What to fill in

After cloning, before running `/grill-me`, open `AGENTS.md` and replace the two placeholder lines in the Project section with your project name and a one-sentence description. Everything else gets filled by the pipeline.

### 6. Gate 5 — Loop retro

After the first real production use, run the retro. This is what closes the loop.

```
@loop-retro
```

The skill walks through five questions — architecture delta, sealed decision regret, constraint gaps, init blind spots, pipeline signal. Produces two files: `retro-[project-name].md` (stays in the project repo) and `template-diff.md` (proposed changes back to this template). Review the diff. Merge what's useful back into `antigravity-runway`. The loop closes when at least one change makes it back into the template.

---

## Repository structure

```
├── README.md
├── AGENTS.md                                    ← primary SSoT, loaded every run
├── GEMINI.md                                    ← thin entry point, delegates to AGENTS.md
└── .agents/
    ├── rules/
    │   └── hard-constraints.md                  ← always-on, fires every agent invocation
    └── skills/
        ├── gcp-agent-init/
        │   └── SKILL.md                         ← run before /grill-me at Gate 1
        ├── multi-agent-brainstorming-gcp/
        │   └── SKILL.md                         ← run at Gate 3
        └── loop-retro/
            └── SKILL.md                         ← run at Gate 5, after production use
```

---

## After Gate 1 completes

The sealed brief populates `AGENTS.md`'s `## Sealed Decisions` section. From that point forward, AGENTS.md is the contract. The agent reasons from it. Do not modify sealed decisions directly — if something needs to change, surface it as a trade-off at the appropriate gate.
