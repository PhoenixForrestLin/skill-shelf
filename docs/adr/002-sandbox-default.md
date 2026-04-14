# ADR-002: Sandbox Isolation as Default Interaction Mode

## Status

Accepted

## Context

Scanned Skills from untrusted sources (e.g., a random shop's QR code) could potentially access the user's existing tools and data if given the same context as the host agent. We need to decide the default trust posture.

## Decision

Route A (sandbox isolation) is the default interaction mode. All mini-skills run in a fully isolated sandbox by default (`interactionMode: "sandboxed"`). They cannot see the user's other conversations, tools, or data.

Route B (agent-assisted, with access to host context) is available as an opt-in upgrade requiring:

- Explicit `permissions.dataAccess` declaration in the manifest
- Higher trust tier (verified or audited publisher)
- Explicit user authorization at install time

## Consequences

- ✅ Most offline/local scenarios work perfectly with zero trust requirements
- ✅ Users don't need to understand permission models for basic use
- ✅ Security by default — isolation eliminates most attack vectors
- ❌ Advanced use cases (e.g., a Skill that books a meeting on your calendar) require extra trust steps