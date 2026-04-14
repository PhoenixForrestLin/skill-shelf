# ADR-001: QR Code Encodes URL, Not Full Manifest

## Status

Accepted

## Context

QR codes have a maximum capacity of ~4KB (V40). A complete Skill Manifest with tools, permissions, and config can easily exceed this. We need to decide what the QR code should encode.

## Decision

The QR code encodes only a URL pointing to a hosted manifest, not the manifest JSON itself.

**Format:** `openclaw://install?manifest=https://example.com/manifest.json`

## Consequences

- ✅ No capacity limit — manifests can grow freely
- ✅ Manifests can be dynamically updated without reprinting QR codes
- ✅ URL scheme enables deep-linking into MCP client apps
- ❌ Requires network connectivity at scan time
- ❌ Manifest hosting becomes a dependency (but merchant already hosts an MCP server)