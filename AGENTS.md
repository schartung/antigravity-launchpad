---
template: gcp-agent-harness
version: 1.0.0
scope: global
---

# AGENTS.md

## Project

**Name:** [FILL: project name]
**Mission:** [FILL: one sentence — what this agent does and for whom]

---

## Pipeline

Four gates plus a retro. Each gate must close before the next opens. The retro is what closes the loop — it feeds improvements back into the template so the next project starts smarter.

```
GATE 1  @gcp-project-init → /grill-me
        Output: sealed-technical-brief.md

GATE 2  Developer Knowledge MCP
        Output: event-driven GCP architecture + infrastructure schemas

GATE 3  @multi-agent-brainstorming-gcp
        Output: locked architectural declaration + Decision Log

GATE 4  /goal
        Output: production codebase

GATE 5  @loop-retro  ← after first real production use
        Output: retro-[project].md + template-diff.md
        Loop closes when at least one change from template-diff.md
        is merged back into antigravity-runway
```

---

## Sealed Decisions

*Empty until Gate 1 closes. Populated from sealed-technical-brief.md. Do not fill manually — this section is the agent's output, not the user's input.*

[GATE 1 OUTPUT GOES HERE]

---

## Hard Constraints

Non-negotiable. Do not propose alternatives without surfacing the trade-off explicitly.

- **No license-tax middleware.** No dbt, no Matia/Materia, no transformation layers with per-seat or per-usage fees. Use native GCP primitives.
- **No data egress.** No third-party services that require data to leave the GCP org boundary.
- **No hardcoded user identifiers.** Emails, IDs, and names belong in runtime configuration or directory lookups, not in code or infra.
- **No admin backdoor to user state.** Per-user data must be cryptographically isolable at the identity level. Application-layer access control is not sufficient.
- **No destructive operations without a pause.** Any action that deletes, rotates, or deprovisions user-scoped resources must surface options and wait for explicit instruction.
- **Secrets via Secret Manager.** No environment variables for credentials. No plaintext values in code, config, or Terraform.
- **Infrastructure as code.** No manual Cloud Console changes that are not mirrored in Terraform or declarative config.

---

## Working Rules

- **Verify GCP specifics against the Developer Knowledge MCP, not training data.** For any GCP service name, API shape, SDK method, model name, or product capability: treat training-data knowledge as a hypothesis. Confirm against the Developer Knowledge MCP before using it in architecture, schemas, or code. When making a GCP-specific claim, state your source. If the MCP contradicts training data, the MCP wins.
- **Grep before creating.** Antigravity does not pre-index. Before introducing any new function, class, or module: `grep -rnI "symbol_name" src/`. Do not assume a symbol exists or does not exist.
- **Commit convention is a Gate 1 decision.** Do not assume Conventional Commits. Ask during `/grill-me` and honor whatever the user seals. If unsealed, default to plain-language descriptive messages — complete sentences a non-technical reader can follow.
- **Log metadata, never message content.** Timestamp, operation, latency, token count are acceptable. User message bodies are never logged anywhere.

---

## When to Pause

Stop and present options — do not auto-decide — when:

1. A trade-off exists between execution speed and cryptographic isolation of user data
2. A new GCP service not in the current scope would become load-bearing infrastructure
3. Any operation touches a user's key, namespace, or state in a destructive way
4. The architecture requires data to cross the GCP org boundary for any reason
5. A sealed decision from Gate 1 needs to be revisited

Format: state the trade-off, present two options, give your recommended pick with reasoning. Wait for instruction before proceeding.

---

## Commands

```bash
# Verify auth context
gcloud auth list && gcloud config get-value project

# Validate infra before apply
cd infra && terraform init && terraform plan -out=plan.tfplan

# Apply infra
terraform apply plan.tfplan

# Type checks
pyright src/

# Tests
pytest tests/ -v --tb=short
```
