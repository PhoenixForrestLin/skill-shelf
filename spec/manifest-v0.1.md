# Skill Manifest Specification v0.1

**Status:** Draft — Feedback Welcome

**Version:** 0.1 (V0 MVP)

**Last Updated:** 2026-04-14

---

## Abstract

A **Skill Manifest** is a JSON document that fully describes how an AI agent can discover, install, and interact with a third-party Skill (MCP server or prompt-based capability). It is the core data format of the skill-shelf platform.

This specification defines the minimal field set required for the V0 MVP: scan a QR code, parse the manifest, register the MCP connection, and manage its lifecycle.

---

## Notation

The key words "MUST", "MUST NOT", "REQUIRED", "SHALL", "SHOULD", "SHOULD NOT", "RECOMMENDED", "MAY", and "OPTIONAL" in this document are to be interpreted as described in [RFC 2119](https://www.rfc-editor.org/rfc/rfc2119).

---

## Design Principles

### Three-Layer Information Model

All Skill installation methods (git clone, npm/pip install, HTTP endpoint, platform API, pure prompt) reduce to three layers of information:

1. **Acquisition** — How does the agent obtain a runnable Skill instance?
2. **Capability Declaration** — What can this Skill do?
3. **Permissions & Trust** — Why should the user trust it?

V0.1 covers Layers 1 and 2. Layer 3 is reserved for v1+.

---

## Top-Level Structure

A valid v0.1 manifest MUST be a JSON object with the following top-level fields:

```json
{
  "$schema": "https://skill-shelf.dev/manifest/v0.1.json",
  "version": "0.1",
  "source": { ... },
  "metadata": { ... },
  "tools": [ ... ]
}
```

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `$schema` | string | RECOMMENDED | URL pointing to the JSON Schema for validation. SHOULD be `https://skill-shelf.dev/manifest/v0.1.json`. |
| `version` | string | REQUIRED | Manifest spec version. MUST be `"0.1"` for this version. |
| `source` | object | REQUIRED | Describes how to obtain the Skill. See **Source Object**. |
| `metadata` | object | REQUIRED | Human-readable Skill information. See **Metadata Object**. |
| `tools` | array | RECOMMENDED | Static declaration of MCP tools this Skill exposes. See **Tools Array**. |

---

## Source Object (Layer 1: Acquisition)

The `source` object answers: **How does the agent get a runnable Skill instance?**

It uses a `type` discriminator to support five atomic acquisition modes. V0.1 implementations MUST support `remote_endpoint`. Other types are defined for forward compatibility.

### `remote_endpoint` — Already deployed, connect directly

```json
"source": {
  "type": "remote_endpoint",
  "url": "https://mcp.shop.local/sse",
  "transport": "sse"
}
```

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | string | REQUIRED | MUST be `"remote_endpoint"` |
| `url` | string (URL) | REQUIRED | Endpoint URL of the running MCP server |
| `transport` | string | REQUIRED | One of `"sse"`, `"http"`, `"stdio"` |

### `package` — Install from package registry

```json
"source": {
  "type": "package",
  "registry": "npm",
  "name": "mcp-server-wifi",
  "version": "^1.0.0",
  "runCommand": "npx mcp-server-wifi"
}
```

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | string | REQUIRED | MUST be `"package"` |
| `registry` | string | REQUIRED | Package registry: `"npm"`, `"pypi"`, `"cargo"`, etc. |
| `name` | string | REQUIRED | Package name |
| `version` | string | RECOMMENDED | Semver range |
| `runCommand` | string | REQUIRED | Command to start the MCP server after installation |

### `repository` — Clone, build, and run

```json
"source": {
  "type": "repository",
  "url": "https://github.com/user/repo",
  "ref": "main",
  "buildSteps": ["npm install", "npm run build"],
  "runCommand": "node dist/index.js"
}
```

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | string | REQUIRED | MUST be `"repository"` |
| `url` | string (URL) | REQUIRED | Git repository URL |
| `ref` | string | RECOMMENDED | Branch, tag, or commit hash. Defaults to default branch. |
| `buildSteps` | array of strings | MAY | Ordered shell commands to build the project |
| `runCommand` | string | REQUIRED | Command to start the MCP server after build |

### `bundle` — Download pre-built artifact

```json
"source": {
  "type": "bundle",
  "url": "https://cdn.example.com/skill.tar.gz",
  "format": "tar.gz",
  "runCommand": "./skill-binary"
}
```

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | string | REQUIRED | MUST be `"bundle"` |
| `url` | string (URL) | REQUIRED | Download URL for the artifact |
| `format` | string | REQUIRED | One of `"tar.gz"`, `"zip"`, `"binary"` |
| `runCommand` | string | REQUIRED | Command to start the MCP server after extraction |

### `inline` — Embedded definition

```json
"source": {
  "type": "inline",
  "runtime": "prompt",
  "code": "You are a dumpling shop assistant..."
}
```

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `type` | string | REQUIRED | MUST be `"inline"` |
| `runtime` | string | REQUIRED | One of `"prompt"`, `"javascript"`, `"python"` |
| `code` | string | REQUIRED | The inline Skill definition. For `"prompt"` runtime, this is a system prompt. |

---

## Metadata Object (Layer 2: Capability Declaration)

The `metadata` object provides human-readable information about the Skill.

```json
"metadata": {
  "name": "Dumpling Shop Assistant",
  "description": "Query WiFi password, today's menu, queue status",
  "author": "shop-owner",
  "version": "1.0.0",
  "homepage": "https://shop.example.com",
  "icon": "🥟",
  "tags": ["local", "food"],
  "ttl": "session"
}
```

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `name` | string | REQUIRED | Display name of the Skill |
| `description` | string | REQUIRED | One-line description of what the Skill does |
| `author` | string | RECOMMENDED | Author or organization name |
| `version` | string | RECOMMENDED | Skill version (semver recommended) |
| `homepage` | string (URL) | MAY | URL to the Skill's homepage or documentation |
| `icon` | string | MAY | Emoji or URL to an icon image |
| `tags` | array of strings | MAY | Categorization tags |
| `ttl` | string | REQUIRED | Default lifetime. One of `"session"`, `"daily"`, `"24h"`, `"persistent"`. See **TTL Semantics**. |

### TTL Semantics

| Value | Behavior | Typical Use Case |
| --- | --- | --- |
| `session` | Removed when the conversation/session ends | One-time lookup (WiFi password) |
| `daily` | Available for the current day, auto-removed at end of day | Regular customer, daily menu check |
| `24h` | Removed 24 hours after installation | Event-specific services |
| `persistent` | Remains until manually removed by the user | Long-term services (clinic booking) |

The manifest's `ttl` value is the **default**. Implementations SHOULD allow users to override this at install time.

---

## Tools Array (Layer 2: Capability Declaration)

The `tools` array provides a static preview of the MCP tools this Skill exposes.

```json
"tools": [
  {
    "name": "get_wifi_password",
    "description": "Get shop WiFi password",
    "inputSchema": {}
  }
]
```

| Field | Type | Required | Description |
| --- | --- | --- | --- |
| `name` | string | REQUIRED | Tool name (must match the MCP `tools/list` response) |
| `description` | string | REQUIRED | Human-readable description of what this tool does |
| `inputSchema` | object | MAY | JSON Schema for the tool's input parameters |

**Why static declaration?** For `remote_endpoint` sources, tools can theoretically be fetched via MCP `tools/list` at runtime. However, at scan time the server may be offline. Static declaration ensures the user can preview capabilities **before** connecting.

---

## QR Code Encoding

A QR code MUST encode a URL pointing to the hosted manifest, not the manifest JSON itself (see [ADR-001](../docs/adr/001-qr-encodes-url.md)).

**Format:**

```
openclaw://install?manifest=https://example.com/manifest.json
```

**Rationale:**

- QR Code V40 max capacity is ~4KB; manifests can exceed this
- URL-based encoding allows manifests to be dynamically updated
- The URL scheme enables deep-linking into MCP client apps

---

## Reserved Fields (v1+)

The following fields are reserved for future specification versions. Implementations MUST NOT reject manifests containing these fields, but MAY ignore them in v0.1.

- `permissions` — Declarative permission requests (network, filesystem, data access)
- `trust` — Signing, publisher verification, and audit information
- `config` — Environment variables, system prompts, and runtime configuration
- `metadata.interactionMode` — `"sandboxed"` vs `"agent-assisted"` execution mode
- `metadata.triggerHints` — Context and disambiguation hints for multi-Skill routing

---

## Complete Example

See [`examples/jiaozi-shop.json`](examples/jiaozi-shop.json) for a complete v0.1 manifest.

---

## Security Considerations

V0.1 does not define a security layer. Implementations SHOULD:

1. Display manifest contents to the user before installation
2. Run all third-party Skills in an isolated context
3. Not auto-install manifests from unverified sources

The full security architecture (7-step install pipeline, LLM-native capability analysis, trust tiers) is defined in [`docs/security.md`](../docs/security.md) and will be formalized in v1.0.

---

## Changelog

- **v0.1** (2026-04-14): Initial draft. Covers source, metadata, and tools.