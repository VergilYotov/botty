# Botty Technical Decisions

> Extracted from ChatGPT conversation on 2026-03-26

## Architecture Decisions

### System Boundaries
- **Components**: Frontend + API + Worker + CLI in one monorepo
- **Local companion**: CLI-first for MVP
- **Public API**: Initially just for Botty's own web app and companion

### Tech Stack
| Layer | Choice | Rationale |
|-------|--------|-----------|
| Frontend | **Next.js** | Self-hosts cleanly on Node, built-in TS support, standard for handoff to agents |
| Backend | **TypeScript** | Node.js runtime |
| CLI | **TypeScript** | Compiled to Node |
| Validation | **Zod or Valibot** | Runtime validation at boundaries |

### Data Model
- **Storage**: Raw files in object storage + metadata in DB
- **Versioning**: Botty-owned versioning with upstream semver/commit as provenance
- **Content-hash**: New Botty version created only on content-hash change
- **Search**: Consider ParadeDB (expecting ~15k artifacts, 1k+ at launch)
- **External artifacts**: Imported to become Botty-native artifacts

### Package Format
- **Bundle**: Tarball with manifest that CLI knows how to handle
- **Manifest**: `manifest.json` (JSON only for MVP)
- **AGENTS.md/Skills**: Already defined by community, stored with extra metadata/versioning
- **MCP**: Need our own portable definition

### CLI Architecture
- **Adapters**: Plugin system from day one, under `/apps/cli/adapters`
- **Install**: Copy files + write correct config (full file rewrite for MVP)
- **MCP config**: Only thing needing adapting in MVP
- **Launch**: Custom protocol `botty://install/...` for one-click from webpage

### Security & Auth
- **Publishing**: Tied to user accounts (orgs post-MVP)
- **Checksums**: No checksums in MVP, just download over HTTPS
- **Risk scanning**: All items (malicious commands, suspicious writes, dangerous MCP, license/source provenance)
- **Tools to consider**: [Snyk agent-scan](https://github.com/snyk/agent-scan), [Gen Digital skill-scanner](https://ai.gendigital.com/skill-scanner)
- **Anonymous install**: Allowed for public artifacts
- **Free tier**: 1 private bundle per user
- **CLI auth**: Browser login + token handoff
- **Telemetry**: Install count only if enabled, otherwise private

### Deployment
- **Frontend**: Cloudflare (static)
- **Backend**: Simple Node hosting + Postgres + object storage (Cloudflare or Hetzner VPC)
- **Event-driven jobs**: Day one for review/scanning, indexing, duplicate detection

### Rollback
- **Git-aware rollback** when inside repos, snapshots elsewhere
- Git-aware means: narrowly scoped to files Botty recorded changing, not broad git resets
- Snapshots always created even inside repos

### Review Gate Policy
**Block on:**
- Invalid manifest/schema
- Clearly dangerous commands/payloads
- Broken installability
- Unresolved required env definitions

**Warn on everything else.**

---

## Repo Structure (Implied)

```
botty/
├── apps/
│   ├── web/           # Next.js frontend
│   ├── api/           # API backend
│   ├── worker/        # Background jobs
│   └── cli/           # CLI companion
│       └── adapters/  # Agent adapters (internal plugins)
├── packages/
│   ├── schemas/       # Shared TS types/Zod schemas
│   └── ...            # Other shared packages
└── ...
```

---

## Documents To Create (from ChatGPT canvas)

These need to be exported from ChatGPT separately:

1. **Botty Technical Specification** - Main spec doc
2. **Botty Manifest Schema RFC** - manifest.json contract, member schemas, validation
3. **Botty CLI Adapter Interface Spec** - Adapter contract, detection, planning, MCP rendering
4. **Botty Install and Rollback Spec** - Install lifecycle, rollback modes, snapshots
5. **Botty API Contract Doc** - REST endpoints, auth, pagination, error model
6. **Botty Import and Review Pipeline Spec** - Ingestion, normalization, scanning

---

## Next Steps

1. Export the 6 canvas documents from ChatGPT
2. Add them to `docs/` folder
3. Bootstrap the monorepo structure
4. Create initial schemas package
