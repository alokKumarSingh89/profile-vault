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
- pnpm build

## Local app startup

- pnpm dev:web
- pnpm dev:api

Expected results:
- Next.js app starts successfully and serves the minimal shell.
- NestJS API starts successfully and responds on the configured API port.
- PostgreSQL is reachable through Docker and Prisma.

## Health validation

- GET /health/live should respond with a healthy status even when PostgreSQL is unavailable.
- GET /health/ready should respond with a readiness status that reflects PostgreSQL availability.

## CI-equivalent validation

The repository must support the same validation commands in a clean CI environment using reproducible dependency installation rather than developer-machine state.
