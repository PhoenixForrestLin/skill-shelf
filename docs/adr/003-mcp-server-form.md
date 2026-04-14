# ADR-003: skill-shelf Form Factor — MCP Server

## Status

Accepted

## Context

skill-shelf needs to run on users' AI agents. Three form factors were considered: MCP Server, Agent Skill ([SKILL.md](http://SKILL.md)), or Native Plugin.

## Decision

skill-shelf is implemented as an MCP Server, exposing `install`, `list`, `remove`, and `invoke` as MCP tools.

Sandbox isolation is achieved through a **call proxy** mechanism — skill-shelf never exposes mini-skill MCP connections directly to the host agent. All tool invocations are routed through skill-shelf's `invoke` tool, which performs permission checks before forwarding.

## Consequences

- ✅ Most universal — works with any MCP-compatible client (OpenClaw, Claude, ChatGPT, etc.)
- ✅ No platform lock-in
- ✅ Call proxy provides a natural interception point for security enforcement
- ❌ MCP has no native nested sandbox support — isolation must be self-built
- ❌ Slightly more overhead than a native plugin integration