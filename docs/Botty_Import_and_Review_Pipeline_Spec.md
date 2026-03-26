# Botty Import and Review Pipeline Spec

## 1. Goal

Define how external artifacts are ingested, normalized, reviewed, versioned, and published as Botty-native records.

## 2. Pipeline Stages

1. Intake
2. Fetch
3. Raw snapshot
4. Classification
5. Normalization
6. Canonical hashing
7. Dedupe
8. Review and scanning
9. Bundle materialization
10. Publish state transition
11. Search index update

## 3. Intake

Sources:

- direct publisher upload
- external URL import
- seeded bulk import jobs

Each intake creates an `import_job`.

## 4. Fetch and Snapshot

The worker:

- downloads the source
- stores immutable raw snapshot in object storage
- records provenance such as source URL, source version, and fetch timestamp

## 5. Classification

The worker identifies whether the input contains:

- instruction artifacts
- skills
- MCP definitions
- mixed bundles

Unknown content does not become installable until normalized.

## 6. Normalization

Normalization converts source material into Botty-native portable objects.

Rules:

- imported externals become Botty-native records
- upstream structure may be preserved as provenance metadata
- portable MCPs are rewritten into Botty schema
- generated `manifest.json` becomes the canonical bundle contract

## 7. Canonical Hashing and Dedupe

Hash input:

- canonical manifest
- referenced portable files

Dedupe rules:

- identical content hash does not create a new Botty version
- upstream version changes without content change update provenance only

## 8. Review and Scanning

MVP review scope includes:

- dangerous commands
- suspicious file write intent
- MCP transport validation
- missing environment declarations
- provenance and source metadata gaps
- installability validation

Potential future integrations discussed:

- agent-focused static scanners
- source reputation signals

## 9. Blocking vs Warning

Block publish on:

- invalid normalized manifest
- broken tarball assembly
- failed installability validation
- clearly unsafe payloads

Warn on:

- incomplete metadata
- soft compatibility issues
- missing optional publisher context

## 10. Bundle Materialization

After successful normalization and acceptable review:

- portable files are assembled
- canonical `manifest.json` is generated
- bundle tarball is created
- immutable bundle blob is stored

## 11. Publish State Model

Suggested states:

- `pending`
- `normalized`
- `review_failed`
- `ready`
- `published`
- `rejected`

## 12. Search and Latest Version Updates

After publish:

- artifact search documents are updated
- latest-version pointers are refreshed
- install resolution surfaces the newest published Botty version

## 13. Bulk Seeding Guidance

Marketplace seeding should support:

- batched imports
- resumable jobs
- dedupe across repeated source discovery
- review queues that can be parallelized by workers
