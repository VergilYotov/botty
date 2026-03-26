# Botty Manifest Schema RFC

## 1. Goal

Define the canonical `manifest.json` format used inside Botty bundle tarballs.

The manifest is the only installation authority in the package.

## 2. Design Principles

- JSON only in MVP
- Stable enough for hashing and versioning
- Portable across supported coding agents
- Explicit install intent
- Adapter hints are advisory, not imperative commands

## 3. Top-Level Shape

```json
{
  "schema_version": "1",
  "bundle": {
    "id": "publisher.slug/name",
    "version": "1.0.0",
    "name": "Example Bundle",
    "description": "Portable coding-agent setup"
  },
  "instructions": [],
  "skills": [],
  "mcps": [],
  "files": [],
  "environment_requirements": [],
  "install_intents": [],
  "adapter_hints": {}
}
```

## 4. Required Fields

- `schema_version`
- `bundle.id`
- `bundle.version`
- `bundle.name`

At least one of these must be non-empty:

- `instructions`
- `skills`
- `mcps`
- `files`

## 5. Member Types

### 5.1 Instructions

```json
{
  "id": "core-instructions",
  "path": "instructions/AGENTS.md",
  "format": "agents_md",
  "role": "primary"
}
```

### 5.2 Skills

```json
{
  "id": "review-skill",
  "path": "skills/review/",
  "entrypoint": "SKILL.md",
  "name": "Review Skill"
}
```

### 5.3 MCPs

```json
{
  "id": "github",
  "transport": "command",
  "command": "npx",
  "args": ["-y", "@modelcontextprotocol/server-github"],
  "env": [
    {
      "name": "GITHUB_TOKEN",
      "required": true
    }
  ],
  "metadata": {
    "display_name": "GitHub MCP"
  }
}
```

Allowed MVP transports:

- `command`
- `remote`

### 5.4 Files

```json
{
  "id": "icon",
  "path": "assets/icon.png",
  "kind": "asset"
}
```

## 6. Environment Requirements

Example:

```json
{
  "name": "GITHUB_TOKEN",
  "required": true,
  "description": "Token used by the GitHub MCP server"
}
```

Rules:

- Secrets are declared as environment-variable requirements
- Values are not stored in the bundle
- Validation checks presence and shape only

## 7. Install Intents

Install intents describe portable desired behavior, such as:

- install a primary instruction file
- install a skill package
- enable a named MCP

Example:

```json
{
  "type": "install_instruction",
  "ref": "core-instructions",
  "required": true
}
```

## 8. Adapter Hints

Adapter hints are optional.

They may include:

- preferred install location hints
- target client compatibility notes
- naming hints
- merge strategy hints

They must not contain:

- raw shell scripts to execute
- arbitrary filesystem writes
- adapter-specific imperative steps

## 9. Validation Policy

Hard-fail:

- invalid JSON
- unknown required top-level shape
- duplicate ids in a single collection
- missing referenced files
- invalid MCP transport definition
- install intent referencing missing members

Warnings:

- unused files
- missing optional metadata
- compatibility notes without enforced support labels

## 10. Canonicalization for Content Hashing

Before hashing:

- object keys are sorted
- arrays that are identity-based are sorted by stable key
- insignificant whitespace is removed
- line endings are normalized to LF
- paths are normalized to relative POSIX style

The canonical manifest plus referenced portable content determines the Botty content hash.

## 11. Version Semantics

- `bundle.version` is publisher-facing version metadata
- Botty creates a new native version only when canonical content hash changes
- Upstream semver or commit provenance may be stored alongside the Botty version

## 12. Implementation Notes

- Shared schema and normalization logic must live in one package
- Worker and CLI must use the same validator
- Adapters consume already validated portable objects
