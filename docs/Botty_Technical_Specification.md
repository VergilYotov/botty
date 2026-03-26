# Botty Technical Specification

## 1. Purpose

This document turns the product definition into an implementation-ready technical baseline for the Botty MVP.

Botty consists of:

- A hosted marketplace for portable coding-agent artifacts
- A local open-source companion CLI that installs and adapts those artifacts for supported agents

This spec follows the product split defined in `Botty_Product_Definition.md`.

## 2. Locked MVP Decisions

- Monorepo with marketplace frontend, marketplace API, background worker, and local CLI
- JavaScript ecosystem with TypeScript everywhere
- Next.js for the web frontend
- Node.js services for API, worker, and CLI runtime
- CLI-first local companion
- Object storage for immutable blobs and tarballs
- Postgres for metadata, publishing, versions, installs, and review state
- External artifacts may be imported and become Botty-native records
- Bundles are distributed as tarballs with a canonical `manifest.json`
- Portable MCP definitions are stored in Botty-native schema and rendered locally by adapters
- Adapters live under `apps/cli/adapters`
- Adapters are built-in internal modules in MVP
- Anonymous install allowed for public artifacts
- Publishing requires an account
- Browser login with token handoff to the CLI
- Full file rewrite is the default config-write strategy in MVP
- Rollback is git-aware inside repos and snapshot-based elsewhere
- New Botty versions are created on content-hash change
- Telemetry is opt-in and limited to install count reporting in MVP

## 3. System Components

### 3.1 Web App

Responsibilities:

- Discovery, listing, search, publisher pages, install entry points
- Login and account settings
- Publish and import workflows
- Review and moderation views

### 3.2 API

Responsibilities:

- Auth and session endpoints
- Artifact, version, bundle, review, import, and download APIs
- CLI handoff endpoints
- Install resolution endpoints
- Publisher settings and telemetry endpoints

### 3.3 Worker

Responsibilities:

- External import ingestion
- Normalization into Botty-native objects
- Scanning and review orchestration
- Content hashing and dedupe
- Tarball assembly
- Search indexing updates

### 3.4 CLI Companion

Responsibilities:

- Marketplace authentication
- Bundle download and validation
- Install preview and apply
- Adapter selection and execution planning
- Local config rewrite
- Snapshot creation
- Rollback and inspect

## 4. Monorepo Structure

```text
apps/
  web/
  api/
  worker/
  cli/
    adapters/
packages/
  manifest/
  types/
  validation/
  install-engine/
  review/
  storage/
  auth/
docs/
```

## 5. Core Data Model

Primary entities:

- `publisher`
- `artifact`
- `artifact_version`
- `bundle`
- `bundle_version`
- `import_job`
- `review_run`
- `review_finding`
- `download_event`
- `install_event`

Key principles:

- Immutable uploaded blobs live in object storage
- Mutable discovery metadata lives in Postgres
- Human versions and upstream provenance are metadata
- Content hash is the immutable identity boundary for Botty versioning

## 6. Artifact Model

Marketplace-native artifact types:

- Instruction artifact
- Skill artifact
- MCP artifact
- Bundle artifact

Non-native outputs:

- Agent-specific config files
- Rendered MCP client configs
- Local machine install records

These are adapter outputs, not marketplace source-of-truth objects.

## 7. Packaging Model

Bundle distribution format:

- gzipped tarball
- canonical `manifest.json`
- referenced portable files
- optional metadata assets such as icons or docs

Rules:

- `manifest.json` is the only authoritative install contract
- Random files in the tarball do not imply install behavior
- All install actions must be derivable from the manifest plus adapter logic

## 8. Local Install Model

Install lifecycle:

1. Resolve bundle version
2. Download tarball
3. Verify transport and unpack
4. Validate manifest
5. Select adapter
6. Produce install plan
7. Create snapshots
8. Apply writes atomically
9. Persist install record
10. Report result

Preview uses the same flow but stops before writes.

## 9. Adapter Model

Adapter responsibilities:

- Detect local environment
- Evaluate support level
- Resolve target paths
- Render target-specific MCP config
- Produce declarative install plans
- Inspect installed state
- Provide rollback guidance

The shared install engine owns:

- File writes
- Snapshot persistence
- Rollback orchestration
- Install record persistence
- Shared plan validation

## 10. Security and Review

MVP review scope:

- Dangerous commands
- Suspicious file writes
- Unsafe MCP definitions
- Source and provenance metadata
- Installability failures

Blocking examples:

- Invalid manifest
- Missing required fields
- Broken tarball layout
- Unsupported mandatory MCP definition
- Clearly dangerous command payload

Non-blocking examples:

- Incomplete optional metadata
- Style warnings
- Soft compatibility warnings

## 11. Hosting Bias

MVP hosting target:

- Simple Node-hosted services
- Postgres
- Object storage

This keeps worker, API, and CLI handoff flows straightforward and avoids edge-first constraints early.

## 12. Delivery Order

Recommended implementation sequence:

1. Manifest package and validation
2. Postgres schema and storage model
3. Bundle ingest and tarball assembly
4. CLI install engine
5. First adapters
6. API contract implementation
7. Web discovery and publish flows
8. Review pipeline and search refinement
