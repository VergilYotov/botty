# Botty CLI Adapter Interface Spec

## 1. Goal

Define the contract between the shared CLI install engine and target-agent adapters.

Adapters are planners and inspectors. They are not the primary file-writing authority.

## 2. Location and Scope

Adapters live under:

- `apps/cli/adapters`

MVP policy:

- built-in internal modules only
- one adapter per supported target family

Initial targets:

- Codex
- Claude Code
- Gemini CLI
- OpenCode

## 3. Responsibilities

Adapters own:

- environment detection
- support evaluation
- target path resolution
- target config rendering
- MCP rendering
- install planning
- install inspection
- rollback guidance

The shared engine owns:

- bundle download and unpack
- manifest validation
- snapshot creation
- plan validation
- atomic writes
- install record persistence
- rollback coordination

## 4. Core Interface

```ts
export interface Adapter {
  id: string;
  detect(ctx: DetectContext): Promise<DetectResult>;
  evaluateSupport(ctx: SupportContext): Promise<SupportResult>;
  planInstall(ctx: PlanContext): Promise<InstallPlan>;
  inspect(ctx: InspectContext): Promise<InspectResult>;
  planRollback(ctx: RollbackContext): Promise<RollbackPlan>;
}
```

## 5. Detect Contract

Detect returns facts, not decisions.

Examples:

- candidate config paths
- detected CLI binary
- current platform
- repo boundary if present
- existing managed config markers if any

## 6. Support Contract

Support levels:

- `supported`
- `partial`
- `unsupported`

Support results include:

- unsupported portable features
- required environment gaps
- target-specific warnings

## 7. Install Plan Contract

Install plans are declarative.

Allowed operation types:

- `write_file`
- `mkdir`
- `backup_marker`
- `set_env_reference`
- `record_metadata`

Each operation includes:

- target path
- ownership metadata
- whether it is required or optional
- rollback relevance

No operation may embed arbitrary shell execution.

## 8. Full File Rewrite Policy

MVP config strategy is full file rewrite for Botty-managed targets.

Consequences:

- adapters produce complete rendered file content
- engine writes to temp file and atomically replaces target
- engine snapshots prior content before replacement

This avoids partial patch complexity in MVP.

## 9. MCP Rendering Contract

Portable MCP inputs are converted into target-specific config payloads by adapters.

Rules:

- adapters may reject unsupported portable fields
- adapters must preserve env references, not inline secrets
- adapters must emit deterministic output for equivalent input

## 10. Inspect Contract

Inspect must report:

- resolved target files
- installed Botty version if known
- drift signals
- compatibility state
- rendered MCP presence and shape

## 11. Rollback Planning

Adapters do not directly revert files.

They provide:

- target paths that matter
- target-specific restore ordering if relevant
- validation hints after restore

The engine performs the actual restore from git-aware or snapshot-based sources.

## 12. Error Model

Error categories:

- `detect_error`
- `unsupported_error`
- `validation_error`
- `plan_error`
- `inspect_error`
- `rollback_error`

Errors should be structured enough for CLI and API surfaces to present clear guidance.

## 13. Testing

Required test layers:

- unit tests for detection and rendering
- golden tests for output config
- plan validation tests
- fixture-based inspect tests
