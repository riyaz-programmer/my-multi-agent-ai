# Update Markdown Docs

Whenever code changes are made, update all related markdown docs in the same change. Never leave docs out of sync with code.

# Docs to Keep Updated

| Doc | Update When |
|---|---|
| `AGENTS.md` | Architecture, conventions, commands, structure, or style guide changes |
| `README.md` (root) | Setup, running locally, env vars, or general usage changes |
| `docs/AGENT_REGISTRY.md` | Adding, removing, or renaming agents; port assignments change |
| `docs/ARCHITECTURE_AND_DATA_FLOW.md` | Architecture, data flow, or component relationships change |
| `docs/PROTOCOLS.md` | AG-UI events, A2A JSON-RPC, or request flow changes |
| `docs/DOMAIN_CONTEXT.md` | Business capabilities or functional areas change |
| `docs/SKILLS_GUIDE.md` | Skills pattern, loading, or bundle conventions change |
| `docs/dev-FAQ.md / docs/general-FAQ.md` | Common patterns, gotchas, or conventions change |
| `docs/decisions/` (ADRs) | A new architectural decision is made or an existing one is revised |
| `agents/*/README.md` | Domain agent structure, skills, or configuration changes |
| `agents/*/CHANGELOG.md` | Any functional change in a domain agent |
| `doc-agent-mf/README.md` | Frontend structure, components, or services change |

# Mermaid Diagrams

Update all Mermaid diagrams in the affected docs to accurately reflect the current architecture or data flow. Verify diagrams after editing — do not leave stale diagrams.

# Rules

- Update docs in the same PR/commit as the code change. Never defer doc updates.
- For agent additions/removals: update `docs/AGENT_REGISTRY.md` and `AGENTS.md` source structure.
- For new ADRs: only create one for significant architectural decisions (new patterns, cross-cutting changes, major technology choices). Routine bug fixes, minor refactors, and config tweaks do not warrant an ADR. Use `docs/decisions/NNNN-title.md` convention.
- For CHANGELOG updates: follow Keep a Changelog format with the date.
- Update all docstrings (class, module, function level) in modified Python files.