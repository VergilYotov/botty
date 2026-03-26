# Botty Install and Rollback Spec

## 1. Goal

Define the shared local execution path for preview, install, inspect, and rollback.

## 2. Install Lifecycle

1. Resolve requested bundle version
2. Download tarball over HTTPS
3. Unpack into temporary workspace
4. Validate `manifest.json`
5. Resolve adapter target
6. Detect local environment
7. Evaluate support and env requirements
8. Produce install plan
9. Validate plan
10. Snapshot target state
11. Apply writes atomically
12. Persist install record
13. Emit result summary

## 3. Preview Lifecycle

Preview runs the same path through plan validation but does not write files.

Preview output must show:

- selected adapter
- target paths
- files to be written
- required environment variables
- warnings and unsupported fields

## 4. Plan Validation Rules

Reject plans that:

- write outside approved target scopes
- contain duplicate conflicting writes
- reference missing rendered content
- rely on imperative shell execution
- omit rollback-relevant target metadata

## 5. Atomic Write Semantics

For each write:

1. create temp output in same directory where possible
2. fsync if supported
3. replace target atomically
4. record final checksum

If replacement fails, the engine restores from snapshot before reporting failure.

## 6. Environment Requirements

Rules:

- required env references must be checked before apply
- secrets are not persisted in bundle metadata
- CLI may prompt later, but MVP primarily validates presence of required env names

## 7. Install Record

Each install stores:

- bundle id and version
- Botty content hash
- adapter id
- target paths
- checksums before and after
- snapshot references
- install timestamp
- source of install request

## 8. Rollback Modes

### 8.1 Git-Aware Rollback

Used when target files are inside a git repository and the engine can safely identify file ownership.

Meaning in MVP:

- Botty records changed files
- Botty may use git state for context and validation
- Botty does not perform broad resets
- rollback remains scoped to files Botty recorded touching

### 8.2 Snapshot Rollback

Used elsewhere.

Rules:

- restore only files Botty changed
- restore prior content or remove newly created files if recorded as created
- validate resulting state after restore

## 9. Drift Handling

Drift detection inputs:

- current file checksum
- managed install record checksum
- adapter inspect result

If drift is detected:

- preview warns
- rollback may require force or explicit confirmation in a later UX
- engine never silently discard unknown user edits

## 10. Failure Policy

Install failure:

- restore any already-written files from snapshot
- persist partial failure diagnostics

Rollback failure:

- stop on first unsafe restore condition
- keep remaining snapshot material
- report exact unresolved paths

## 11. Responsibility Split

Engine:

- shared lifecycle
- writes
- snapshots
- install records
- rollback execution

Adapter:

- planning
- render logic
- inspect logic
- target-specific validation hints

## 12. Test Plan

- clean install
- preview only
- install with missing env requirement
- install over existing managed config
- rollback in git repo
- rollback outside git repo
- drifted file rollback refusal path
