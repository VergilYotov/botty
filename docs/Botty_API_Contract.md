# Botty API Contract

## 1. Goal

Define the MVP HTTP API used by the web app, CLI, and worker.

Base path:

- `/v1`

## 2. API Principles

- JSON request and response bodies
- Cursor pagination for list endpoints
- Stable ids for artifacts and versions
- Public read access for public artifacts
- Auth required for publishing and private access

## 3. Auth Modes

- Anonymous user for public discovery and install resolution of public artifacts
- Browser session auth for web flows
- CLI token auth obtained through browser handoff
- Internal worker auth for background operations

## 4. Core Resources

- `artifact`
- `artifact_version`
- `bundle`
- `bundle_version`
- `publisher`
- `import_job`
- `review_run`

## 5. Endpoint Groups

### 5.1 Auth

- `POST /auth/cli/handoff/start`
- `POST /auth/cli/handoff/complete`
- `POST /auth/logout`

### 5.2 Discovery

- `GET /artifacts`
- `GET /artifacts/{artifactId}`
- `GET /artifacts/{artifactId}/versions`
- `GET /bundles`
- `GET /bundles/{bundleId}`
- `GET /bundles/{bundleId}/versions/{versionId}`

### 5.3 Publishing

- `POST /bundles`
- `POST /bundles/{bundleId}/versions`
- `POST /uploads`
- `POST /imports`

### 5.4 Review

- `GET /reviews/{reviewId}`
- `GET /bundles/{bundleId}/versions/{versionId}/reviews`

### 5.5 Download and Install

- `GET /bundles/{bundleId}/versions/{versionId}/download`
- `POST /install/resolve`

### 5.6 Settings and Telemetry

- `GET /me`
- `PATCH /me/settings`
- `POST /telemetry/install`

## 6. Install Resolve Contract

Request example:

```json
{
  "bundle_id": "publisher.slug/example",
  "version": "latest",
  "target_agent": "codex"
}
```

Response example:

```json
{
  "bundle_id": "publisher.slug/example",
  "resolved_version": "1.0.0",
  "content_hash": "sha256:...",
  "download_url": "https://...",
  "visibility": "public"
}
```

## 7. Review Result Shape

```json
{
  "status": "warning",
  "findings": [
    {
      "severity": "medium",
      "code": "mcp_remote_unverified",
      "message": "Remote MCP endpoint has no provenance metadata"
    }
  ]
}
```

## 8. Error Model

Common shape:

```json
{
  "error": {
    "code": "validation_error",
    "message": "Manifest is invalid",
    "details": []
  }
}
```

## 9. Authorization Rules

- Public bundles are readable anonymously
- Private bundles require owner access
- Publishing requires authenticated account
- Review details for unpublished artifacts require owner access

## 10. Worker-Owned Internal Flows

Internal flows may use separate auth but follow the same resource model for:

- import creation
- review status updates
- bundle materialization
- search indexing sync

## 11. CLI Handoff Flow

1. Browser signs user in
2. Web app starts CLI handoff
3. User launches CLI through custom protocol
4. CLI redeems short-lived handoff token
5. CLI receives install-scoped or session-scoped credentials
