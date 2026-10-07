# Health API Contract

## Overview

The API exposes operational health endpoints that support local development and future orchestrator checks without implementing business functionality.

## Endpoint: GET /health/live

### Purpose
Returns whether the API process is alive.

### Semantics
- Must succeed when the NestJS process is running.
- Must not depend on PostgreSQL availability.
- Must report a healthy status for the process itself.

### Example response

```json
{
  "status": "ok",
  "timestamp": "2026-10-07T12:00:00.000Z"
}
```

## Endpoint: GET /health/ready

### Purpose
Returns whether the API is ready to serve traffic.

### Semantics
- Must verify required dependencies, including PostgreSQL connectivity when configured.
- Must fail when readiness dependencies are unavailable.
- Must be safe to use by orchestrators and local health checks.

### Example response

```json
{
  "status": "ok",
  "timestamp": "2026-10-07T12:00:00.000Z",
  "checks": {
    "database": "ok"
  }
}
```

### Failure example

```json
{
  "status": "error",
  "timestamp": "2026-10-07T12:00:00.000Z",
  "checks": {
    "database": "unavailable"
  }
}
```

## Security requirements

- These endpoints are operational only and must not expose secrets, credentials, stack traces, or internal infrastructure details.
- Error responses must remain concise and safe for client consumption.
