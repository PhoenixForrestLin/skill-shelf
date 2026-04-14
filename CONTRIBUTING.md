# Contributing to skill-shelf

Thank you for your interest in skill-shelf! This project is in its early **spec + design** phase — there is no implementation code yet. This means your input on the design is especially valuable.

---

## Current Project Status

- ✅ Skill Manifest Specification v0.1 (draft)
- ✅ Architecture design documents
- ✅ Security architecture design
- ✅ 6 Architecture Decision Records
- ❌ No implementation code yet
- ❌ No running prototype yet

---

## How to Contribute

### 1. Review the Spec

The most impactful thing you can do right now is **review the Manifest Spec v0.1** and provide feedback:

- Read [`spec/manifest-v0.1.md`](spec/manifest-v0.1.md)
- Open an issue with the `[Spec]` prefix for feedback, questions, or suggestions
- Comment on existing spec-related issues

### 2. Join Design Discussions

Several open design questions need community input:

- Runtime sandbox architecture (call proxy vs. process isolation)
- LLM security analysis prompt engineering
- Multi-platform compatibility (beyond OpenClaw)
- Offline/degraded mode strategies

Look for issues tagged `design` or `discussion`.

### 3. Claim an Implementation Task

Once the spec stabilizes, implementation tasks will be available. Look for issues tagged `good first issue` or `implementation`. Current planned tasks include:

- V0 MCP Server skeleton (TypeScript)
- Manifest parser + JSON Schema validator
- TTL lifecycle manager
- Example mini-skill implementations

### 4. Improve Documentation

- Fix typos, improve clarity, add examples
- Create diagrams or visualizations
- Translate documents (Chinese ↔ English)
- Create a demo video or animated GIF

---

## Development Guidelines

### Language

- **Spec, README, and docs**: English (for international MCP community reach)
- **Issues and discussions**: English or Chinese — both welcome
- **Code comments**: English

### Commit Messages

Use [Conventional Commits](https://www.conventionalcommits.org/):

```
feat: add TTL manager for session lifecycle
fix: correct manifest schema validation for inline type
docs: improve security architecture explanation
spec: add inputSchema field to tools array definition
```

### Pull Requests

1. Fork the repository
2. Create a feature branch (`git checkout -b feat/ttl-manager`)
3. Make your changes
4. Submit a PR with a clear description of what and why

---

## Communication

- **GitHub Issues**: For bugs, feature requests, and spec feedback
- **GitHub Discussions**: For open-ended design questions
- *(More channels TBD as the community grows)*

---

## Code of Conduct

Be kind, be constructive, be respectful. We're building something new together.

---

## Recognition

All contributors will be acknowledged in the README. Early contributors who help shape the spec and architecture will be recognized as founding contributors.