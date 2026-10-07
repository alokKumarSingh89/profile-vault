# Data Model: Application Foundation for ProfileVault

## Overview

This feature does not define business domain models. It defines the minimal infrastructure data and operational contracts required to prove the app foundation works and remains ready for future profile and document features.

## Core entities

### Environment Configuration

**Purpose**: Defines required runtime values for local development and CI.

**Fields**:
- APP_ENV: runtime mode, expected values such as development, test, or production
- WEB_PORT: port used by the Next.js app
- API_PORT: port used by the NestJS app
- DATABASE_URL: PostgreSQL connection string used by Prisma
- POSTGRES_DB: database name for local development
- POSTGRES_USER: database user for local development
- POSTGRES_PASSWORD: database password for local development
- NEXT_PUBLIC_API_URL: public API base URL used by the web shell

**Rules**:
- Required values must be validated at startup.
- Secret values must never be printed in logs or error output.
- Missing values must fail fast with actionable errors.

### Database Runtime

**Purpose**: Represents the local PostgreSQL service used by Prisma and readiness checks.

**Fields**:
- host: database host
- port: database port
- database: logical database name
- user: database user
- password: secret value handled via environment configuration

**Rules**:
- This service is local-only development infrastructure, not a business model.
- Readiness checks may probe the database connectivity status.
- Liveness checks must not depend on database availability.

### Health Status

**Purpose**: Represents API health and readiness status for operations monitoring.

**Fields**:
- status: ok or error
- timestamp: ISO timestamp
- checks: list of named checks and statuses

**Rules**:
- /health/live returns process health only.
- /health/ready returns readiness only when the application and required dependencies are available.
- Failure modes must be explicit and safe for future orchestration use.

## Relationships

- The web app depends on environment configuration to determine API URLs and runtime behavior.
- The API depends on environment configuration and the database runtime for readiness checks.
- The database runtime is shared infrastructure for Prisma and the API service lifecycle.
- Health status is derived from runtime dependencies and does not represent domain state.

## Validation approach

- Runtime configuration is validated through a shared config package.
- Prisma connectivity is validated through a database health check helper in the database package.
- Health endpoints are tested for both healthy and degraded conditions without introducing business logic.
