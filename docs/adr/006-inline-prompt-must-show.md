# ADR-006: Inline Prompt Skills Must Be Fully Displayed to Users

## Status

Accepted

## Context

`inline` source type with `runtime: "prompt"` embeds a system prompt directly in the manifest. Unlike MCP server connections, there is no separate running service — the prompt IS the entire Skill. Silent injection would give third parties direct, unreviewed influence over the agent's behavior.

## Decision

When `source.type` is `"inline"` and `source.runtime` is `"prompt"`, the full `source.code` content MUST be displayed to the user before activation. Silent injection is not allowed.

## Consequences

- ✅ Users have full transparency over what influences their agent
- ✅ Eliminates the most direct prompt injection vector
- ❌ Long prompts may create a poor user experience (mitigated by L1 summary + expandable L2 details)
- ❌ Skill authors cannot hide proprietary prompt engineering (acceptable trade-off for user safety)