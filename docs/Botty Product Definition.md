# Botty Product Definition

## 1. Product

Botty is a **coding-agent-agnostic marketplace** plus a **minimal open-source local companion**.

- The **marketplace** distributes portable agent artifacts.
- The **local companion** handles agent-specific installation and compatibility.

---

## 2. Core Product Split

### 2.1 Marketplace

The marketplace stores and indexes:

- `AGENTS.md`-style agent instructions
- Skills
- MCPs
- Full configuration bundles

The marketplace does **not** handle:

- MCP installation semantics
- Claude / Codex / Gemini / OpenCode-specific config translation
- Machine-local file placement rules
- Writing files into user environments

The marketplace acts as:

- Discovery layer
- Version registry
- Review layer
- Publishing layer
- Monetization layer

### 2.2 Local Companion

The local companion is:

- Open source
- Written in JavaScript
- Minimal, clean, and easy to inspect

The local companion handles:

- One-click local install
- Translation of generic bundle metadata into agent-specific local config
- Agent-specific MCP setup
- Local capability dashboard
- Behavior Inspector
- Rollback and version pinning

---

## 3. Vision

Create the default way to discover, install, inspect, and version portable coding-agent configurations across coding agents, while keeping agent-specific compatibility logic local, open source, and easy to inspect.

---

## 4. Product Thesis

The market should not be organized around each agent vendor’s config format.

It should be organized around the **portable layer** of coding-agent behavior:

- Instructions
- Skills
- MCP definitions
- Reusable bundles

The agent-specific layer should live in the local companion, not in the marketplace:

- MCP wiring
- Config-file translation
- File placement
- Compatibility quirks

---

## 5. Product Architecture

### 5.1 Marketplace Responsibilities

The marketplace is responsible for:

- Artifact discovery
- Artifact storage and registry
- Artifact versioning
- Search and meta-search
- Automated review
- Submission workflows
- Listing pages
- Featured placement monetization

The marketplace is not responsible for:

- Local installation
- Machine-specific environment checks
- MCP wiring
- Codex / Claude / Gemini / OpenCode config translation
- Writing files into user environments

### 5.2 Local Companion Responsibilities

The local companion is responsible for:

- Authenticating with the marketplace
- Downloading artifacts and bundles
- Resolving install plans
- Translating generic artifacts into specific agent installs
- Managing agent-specific config formats
- Managing MCP setup locally
- Showing installed capabilities
- Showing active behavior and config sources
- Version pinning and rollback

---

## 6. Artifact Model

### 6.1 Marketplace-Native Artifact Types

These are the portable, first-class artifacts in the marketplace.

#### Agent Instruction Artifact
- Canonical form: `AGENTS.md`
- Treated as the portable instruction layer

#### Skill Artifact
- Reusable skill package
- Versioned
- Installable into bundles

#### MCP Artifact
A portable MCP definition that is straightforward to configure.

Examples of portable MCP fields:
- Name
- Source
- URL
- `npx` command
- Command + args
- Required environment variables
- Optional metadata
- Adapter hints for local install

**Important:** the marketplace stores the **portable MCP definition**, not the final client-specific rendered config.

#### Bundle Artifact
A package containing any combination of:

- One or more `AGENTS.md` artifacts
- One or more skills
- One or more MCPs
- Metadata
- Install intent
- Adapter hints for local resolution

### 6.2 Non-Native Artifacts

These are **not** marketplace-native objects:

- MCP config rendered for a specific agent
- Codex-specific local config
- Claude Code-specific local config
- Gemini-specific local config
- OpenCode-specific local config

Examples:
- `~/.config/claude/...`
- `~/.codex/config.toml`
- `.gemini/settings.json`
- `opencode.jsonc`

These remain **adapter outputs** of the local companion.

---

## 7. Positioning

Botty is:

- A marketplace for portable coding-agent setups
- Plus a local runtime that adapts them to each coding agent

Botty is not:

- The Codex marketplace
- The MCP marketplace
- A config marketplace for one vendor

---

## 8. Design Principles

- Marketplace is portable
- Local companion is agent-specific
- Open-source companion is tiny and inspectable
- JavaScript implementation should favor:
  - Readability
  - Minimal dependencies
  - Deterministic behavior
  - Plugin adapters
  - Transparent file operations
  - Explicit dry-run / install preview

---

## 9. Supported Ecosystems

### MVP

The marketplace remains coding-agent agnostic.

The local companion supports:

1. Codex
2. Claude Code
3. Gemini CLI
4. OpenCode

### Principle

The marketplace does not need to know how each target agent works internally.

It only needs portable artifacts plus enough metadata for the local adapter to do the translation.

---

## 10. Open-Source Local Companion

### 10.1 Goals

- Minimal
- Clean code
- Readable
- Auditable
- Cross-platform
- Easy for community contribution

### 10.2 Technical Requirements

- Written in JavaScript
- Small dependency surface
- Modular adapter architecture
- Local-first behavior
- Transparent file operations
- Explicit dry-run / install preview
- Deterministic install plans where possible

### 10.3 Suggested Implementation Style

- Node.js
- Plain JavaScript modules
- Minimal framework use
- CLI-first core
- Optional tiny local UI shell later

---

## 11. MVP Definition

### 11.1 Marketplace MVP

- Browse and search listings for:
  - `AGENTS.md`
  - Skills
  - MCPs
  - Bundles
- Meta-search / indexing of major public sources
- Artifact pages
- Bundle pages
- User submissions
- Version history
- Automated review reports
- Paid featured placements for:
  - Skills
  - MCPs
  - Bundles

### 11.2 Local Companion MVP

- Install bundle locally
- Install single skill locally
- Install single `AGENTS.md` locally
- Configure MCPs locally
- Adapter resolution for supported coding agents
- Local capability dashboard
- Behavior Inspector
- Rollback / version pinning

### 11.3 Automated Review MVP

- Metadata validation
- Structural validation
- Duplicate detection
- Risk scanning
- Installability checks for portable artifacts
- Review summary on listing page

---

## 12. Marketplace UX

### 12.1 Search Modes

- Native listings
- Indexed external artifacts
- Bundles
- Skills
- MCPs
- Instructions

### 12.2 Listing Page Requirements

Every listing page should show:

- Artifact type
- Version
- Source
- Review summary
- Included files
- Bundle contents
- Install CTA
- Change history
- Featured / sponsored badge where relevant

---

## 13. Local Companion UX

### 13.1 Capability Dashboard

Shows:

- Installed bundles
- Installed skills
- Installed instruction sets
- Installed MCPs
- Target coding agents detected locally
- What capabilities are active
- Update availability
- Conflicts and warnings

### 13.2 Behavior Inspector

Shows:

- Active instruction layers
- Source bundle or artifact
- Installed skills affecting behavior
- Installed MCPs affecting capability
- Which local adapter resolved the config
- What was written locally
- Current version pins

---

## 14. Post-MVP Features

### 14.1 Product Features

- Debugger
- Workflow composer
- Visual `AGENTS.md` editor
- Richer adapter controls
- Broader agent bundle support beyond coding agents

### 14.2 Commercial Features

- Private registry
- Audit logs
- Compliance
- Org policies
- Approval workflows

---

## 15. Business Model

### 15.1 Free

- Browse
- Install
- Inspect
- Submit
- Use the open-source local companion

### 15.2 Monetized

- Featured listings for:
  - Skills
  - MCPs
  - Bundles

### 15.3 Later

- Pro
- Team
- Enterprise governance

---

## 16. Strategic Positioning

Botty should position itself as:

> The registry for portable coding-agent setups, with an open-source local runtime that adapts them to your agent of choice.

Not as:

- The Codex marketplace
- The MCP marketplace
- The config marketplace for one vendor

---

## 17. Recommended MVP Statement

Build an agent-agnostic marketplace for portable coding-agent artifacts — `AGENTS.md`, skills, MCPs, and bundles — paired with a minimal open-source JavaScript local companion that performs one-click installation, agent-specific translation, local capability visibility, and behavior inspection.

---

## 18. Recommended Post-MVP Statement

Extend the platform with debugging, composition, visual editing, and team governance while keeping the marketplace portable and the agent-specific complexity in the open-source local companion.

---

## 19. Summary

Botty separates the system into two clean layers:

### Marketplace
- Portable
- Searchable
- Versioned
- Reviewable
- Monetizable

### Local Companion
- Agent-specific
- Open source
- Minimal
- Inspectable
- Responsible for all local translation and install behavior

This split keeps the marketplace generic and scalable while keeping compatibility logic transparent and local.
