---
trigger: always_on
globs: ["**/*"]
---

# Hard Constraints (Always Active)

These apply to every agent action in this project, regardless of task scope or gate.

- Never hardcode user emails, IDs, or identifiers in any code, config, or Terraform. Resolve from directory or runtime context.
- Never write user message content to any log, monitoring stream, or storage layer. Log metadata only: timestamp, operation, latency, token count.
- Never execute a destructive change to a user's key, namespace, or state. Pause and present options first.
- Never use a third-party service that sends data outside the GCP org boundary.
- Never use license-tax middleware (dbt, Matia/Materia, or similar). Use native GCP primitives.
- Never use environment variables or plaintext config for credentials. Use Secret Manager.
- Never make a manual Cloud Console change that is not mirrored in Terraform or declarative config. Infrastructure is code.
- Never build toward a specific human interface layer (e.g. Google Chat, Slack) unless it was explicitly sealed in Gate 1. Interface decisions belong to the user, not the template.
- Never assume a commit message convention. Commit convention is a Gate 1 decision. If unsealed, use plain-language descriptive messages — complete sentences, no prefixes, no shorthand.
- Never rely on training data for GCP service names, API shapes, SDK methods, model names, or product capabilities. Treat training-data knowledge of GCP specifics as a hypothesis. Verify against the Developer Knowledge MCP before using in architecture, schemas, or code. If the MCP contradicts training data, the MCP wins. State your source when making a GCP-specific claim.
- Always grep before creating. Run `grep -rnI "symbol_name" src/` before introducing any new function, class, or module. Do not assume a symbol exists or does not exist.
- Never provide casual read access to any individual user's data layer — not even to `roles/owner`. Cryptographic isolation is the enforcement mechanism, not application-layer access control.
