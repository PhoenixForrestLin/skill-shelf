# ADR-005: Security Checks as Built-in Pipeline, Not Optional Skill

## Status

Accepted

## Context

Security checks could be implemented as a separate optional Skill that users install alongside skill-shelf, or as a built-in mandatory part of the install flow.

## Decision

Security checks are a **built-in, non-bypassable pipeline** within skill-shelf. Every manifest passes through the 7-step install pipeline (parse → validate → audit → analyze → summarize → confirm → install) before any Skill is activated.

## Consequences

- ✅ Malicious payloads cannot instruct the agent to "skip security checks"
- ✅ Users cannot accidentally disable security by uninstalling a separate Skill
- ✅ Consistent security posture for all installations
- ❌ Less modular — security logic is coupled to the platform
- ❌ Cannot be independently updated without updating skill-shelf itself