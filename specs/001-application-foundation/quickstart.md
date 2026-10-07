# Quickstart: Application Foundation Validation

## Prerequisites

- pnpm
- Docker Desktop or comparable Docker runtime
- Node.js 20 LTS

## Setup

1. From the repository root, install dependencies:
   - pnpm install
2. Copy the example environment file and fill in the required values:
   - cp .env.example .env
3. Start the local PostgreSQL instance:
   - docker compose -f docker/postgres/docker-compose.yml up -d
4. Ensure the runtime configuration matches the database and app ports defined in the project configuration.

## Validation commands

Run the following from the repository root:

- pnpm lint
- pnpm format:check
- pnpm typecheck
- pnpm test
- pnpm test:integration
- pnpm test:e2e
- pnpm build

## Testing conventions

- Unit tests use `*.spec.ts` or `*.spec.tsx`, are colocated with source, and run only with `pnpm test`; discovery excludes `*.integration-spec.ts` and `*.e2e-spec.ts` so the categories do not overlap.
- Integration tests use `*.integration-spec.ts` under the owning app or package's `tests/integration/` directory and run only with `pnpm test:integration`. Feature 001 includes a real database connectivity integration test and requires PostgreSQL for this command.
- API E2E tests use `apps/api/test/*.e2e-spec.ts` and run only with `pnpm test:e2e`.
- Feature 001 adds infrastructure integration and application E2E coverage, but no product-level integration suites or empty test suites solely for future use.

## Local app startup

- pnpm dev:web
- pnpm dev:api

Expected results:

- Next.js app starts successfully and serves the minimal shell.
- NestJS API starts successfully and responds on the configured API port.
- PostgreSQL is reachable through Docker and Prisma.

## Health validation

- `GET /health/live` returns HTTP 200 when the API process is healthy, including when PostgreSQL is unavailable.
- `GET /health/ready` returns HTTP 200 when required dependencies are available and HTTP 503 Service Unavailable when a required dependency, including PostgreSQL, is unavailable.
- Public health responses do not expose connection strings, credentials, host details, stack traces, or internal exception messages.

## CI-equivalent validation

CI uses reproducible dependency installation and provides PostgreSQL with deterministic test-only configuration before `pnpm test:integration`. Connection configuration is limited to the integration-test process; application logs must not print credentials or connection strings. Static checks and unit tests require neither PostgreSQL nor runtime secrets.
