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

- Unit tests live next to their source as `*.test.ts` or `*.test.tsx` and run with `pnpm test`.
- Future integration tests live under the owning app or package's `tests/integration/` directory and run with `pnpm test:integration`.
- API end-to-end tests live in `apps/api/test/` with `*.e2e-spec.ts` filenames and run with `pnpm test:e2e`. The health endpoint end-to-end test is the initial proof of this setup.
- Feature 001 does not add product-level integration suites or empty test suites solely for future use. The integration command may report that no matching tests exist until a feature adds a suite.

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

The repository must support the same validation commands in a clean CI environment using reproducible dependency installation rather than developer-machine state.
