# Architecture: Sovereign Patterns Bible

## Overview

**Package ID:** `PKG-016`  
**Domain:** Architecture Linter & Design Standards  
**Microservice Port:** `8794`  
**n8n Webhook Path:** `patterns-bible-trigger`  
**GitHub:** [BlackFoxgamingstudio/patterns-bible](https://github.com/BlackFoxgamingstudio/patterns-bible)

Living architecture standards engine that lints codebases against design patterns, enforces SBB conventions, generates pattern documentation, and scores technical debt.

---

## System Architecture

```
                     ┌──────────────────────────────────┐
                     │       Sovereign Patterns Bible      │
                     │       Port: 8794            │
                     ├──────────────┬───────────────────┤
   n8n Webhook ────▶ │  REST API    │   Core Engine     │
   HTTP POST         │  /api/v1/*   │   Dispatcher      │
                     └──────┬───────┴────────┬──────────┘
                            │                │
              ┌─────────────▼────────────────▼─────────┐
              │          Component Layer                 │
              │  PatternLinter   | ConventionEnfor | PatternDocGe  │
              └────────────────────────┬────────────────┘
                                       │
              ┌────────────────────────▼────────────────┐
              │      n8n Central Event Bus (:5678)       │
              └─────────────────────────────────────────┘
```

## Core Components

### `PatternLinter`
Handles all patternlinter operations. Exposes async methods callable from the core dispatcher.

### `ConventionEnforcer`
Handles all conventionenforcer operations. Exposes async methods callable from the core dispatcher.

### `PatternDocGenerator`
Handles all patterndocgenerator operations. Exposes async methods callable from the core dispatcher.

### `TechDebtScorer`
Handles all techdebtscorer operations. Exposes async methods callable from the core dispatcher.

### `PatternCatalogManager`
Handles all patterncatalog operations. Exposes async methods callable from the core dispatcher.

---

## API Contract

All interactions follow the SBB standard envelope:

```http
POST /api/v1/execute
Content-Type: application/json
X-SBB-API-Key: <api-key>

{
  "action": "<operation>",
  "payload": {},
  "trace_id": "optional-uuid"
}
```

**Success Response (HTTP 200):**
```json
{
  "status": "success",
  "data": {},
  "trace_id": "...",
  "timestamp": "2025-01-01T00:00:00Z"
}
```

**Health Check:**
```http
GET /health
→ {"status": "healthy", "service": "sovereign-patterns-bible", "port": 8794}
```

## Integration Matrix

| System | Protocol | Direction | Purpose |
|--------|----------|-----------|---------|
| n8n Event Bus (:5678) | HTTP POST | Outbound | Event forwarding |
| n8n Webhook | HTTP POST | Inbound | Trigger execution |
| SBB Codebase Vault (:8766) | HTTP | Outbound | Code analysis |
| SBB Patterns Bible (:8794) | HTTP | Outbound | Standards validation |
| External APIs | HTTPS | Outbound | Domain-specific data |

## Deployment Architecture

```yaml
# docker-compose excerpt
sovereign-patterns-bible:
  image: sovereign-patterns-bible:latest
  ports: ["8794:8794"]
  healthcheck:
    test: curl -f http://localhost:8794/health
    interval: 30s
```

## Security Model

| Control | Implementation |
|---------|---------------|
| Authentication | `X-SBB-API-Key` header (env: `SBB_API_KEY`) |
| Rate Limiting | 100 req/min per client IP |
| Input Validation | Pydantic models (strict mode) |
| Container Security | Non-root user (`appuser:1001`) |
| Secrets | Environment variables only (never hardcoded) |
| TLS | Terminate at reverse proxy (nginx/caddy) |

## Tags
`patterns`, `linting`, `architecture`, `standards`
