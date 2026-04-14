# ADR-004: systemPrompt in Payload with LLM Summary Display

## Status

Accepted

## Context

Third-party Skills may want to define a system prompt to shape their behavior (e.g., "You are a dumpling shop assistant"). Including it in the manifest enables flexibility, but creates prompt injection risk.

## Decision

A compromise approach:

1. `systemPrompt` IS included in the manifest payload (preserves flexibility)
2. The agent does NOT silently inject it into the conversation
3. At install time, the user sees an **LLM-generated one-line behavior summary** (e.g., "This Skill will respond as a 'dumpling shop assistant'")
4. The raw prompt text is only shown at the "Technical Details" level (L2)
5. The `capability_analysis` pipeline step cross-validates systemPrompt content against declared permissions — flagging contradictions

## Consequences

- ✅ Preserves Skill author flexibility
- ✅ Users are informed without being overwhelmed by raw prompt text
- ✅ LLM cross-validation catches obvious prompt injection attempts
- ❌ Relies on LLM quality for summarization and detection
- ❌ Sophisticated prompt injection may evade LLM analysis