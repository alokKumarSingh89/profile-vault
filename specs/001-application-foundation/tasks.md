# Tasks: Application Foundation for ProfileVault

**Input**: Design documents from `/specs/001-application-foundation/`

**Prerequisites**: plan.md (required), spec.md (required for user stories), research.md, data-model.md, contracts/

**Organization**: Tasks are grouped by user story to enable independent implementation and testing of each story.

## Format: `[ID] [P?] [Story] Description`

- **[P]**: Can run in parallel (different files, no dependencies)
- **[Story]**: Which user story this task belongs to (e.g., US1, US2, US3)
- Include exact file paths in descriptions

## Phase 1: Setup (Shared Infrastructure)

**Purpose**: Establish the workspace, toolchain, and package structure required by the plan.

- [ ] T001 Create the root monorepo structure and config files in `package.json`, `pnpm-workspace.yaml`, `tsconfig.base.json`, `eslint.config.js`, `prettier.config.js`, `vitest.config.ts`, `.env.example`, `.npmrc`, and `README.md`; configure Vitest discovery for the documented unit, integration, and API E2E test conventions without creating empty test suites.
- [ ] T002 [P] Create the `apps/web` workspace in `apps/web/package.json`, `apps/web/tsconfig.json`, `apps/web/next.config.ts`, and `apps/web/app/layout.tsx`.
- [ ] T003 [P] Create the `apps/api` workspace and minimal NestJS bootstrap in `apps/api/package.json`, `apps/api/tsconfig.json`, `apps/api/src/main.ts`, and `apps/api/src/app.module.ts`. Do not add health endpoints here.
- [ ] T004 [P] Create the `packages/database` package scaffold and empty, non-domain Prisma schema in `packages/database/package.json`, `packages/database/tsconfig.json`, and `packages/database/prisma/schema.prisma`; reserve `packages/database/src/index.ts` and `packages/database/src/client.ts` implementation for T011.
- [ ] T005 [P] Create only the `packages/config` package-level scaffold in `packages/config/package.json` and `packages/config/tsconfig.json`; do not create source placeholders.
- [ ] T006 [P] Create the `packages/contracts` placeholder package in `packages/contracts/package.json`, `packages/contracts/tsconfig.json`, and `packages/contracts/src/index.ts`.
- [ ] T007 Create the Docker PostgreSQL setup in `docker/postgres/docker-compose.yml` and `docker/postgres/.env.example`.
- [ ] T008 Define the Node.js 20 LTS and pnpm toolchain in `.nvmrc` or `.node-version`, `package.json` `packageManager` and `engines`, and `README.md` for consistent local and CI use.

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Establish shared infrastructure prerequisites before user-story behavior is implemented.

**⚠️ CRITICAL**: User-story work depends on completion of this phase.

- [ ] T009 Implement root workspace scripts in `package.json` for `dev`, `dev:web`, `dev:api`, `lint`, `format`, `format:check`, `typecheck`, `test` (unit), `test:integration`, `test:e2e`, and `build`; integration discovery must pass when no integration suite exists yet.
- [ ] T010 [P] Implement environment validation and fail-fast configuration loading in `packages/config/src/env.ts` and `packages/config/src/index.ts`, ensuring invalid or missing required values fail without exposing secrets.
- [ ] T011 Implement the Prisma 7 client in `packages/database/src/client.ts` and `packages/database/src/index.ts` using the PostgreSQL driver-adapter architecture; keep all Prisma infrastructure in `packages/database`.
- [ ] T012 Add a lightweight PostgreSQL connectivity helper in `packages/database/src/health.ts`, using the client from T011 and a query such as `SELECT 1`; do not create a database table for health tracking.
- [ ] T013 [P] Add config validation tests in `packages/config/src/env.test.ts` or the project's designated unit-test location to verify fail-fast behavior and that error output does not expose secret values.
- [ ] T014 Document installation, environment setup, Docker PostgreSQL startup, local app startup, test locations, test commands, and the no-empty-suite convention in `README.md` and `specs/001-application-foundation/quickstart.md`.

**Checkpoint**: Shared workspace, configuration, database connectivity, and test infrastructure are ready for user stories.

---

## Phase 3: User Story 1 - Bootstrap the monorepo foundation (Priority: P1) 🎯 MVP

**Goal**: Establish a runnable monorepo foundation that a developer can install, configure, and start locally without business features.

**Independent Test**: Install with pnpm, configure documented environment values, start PostgreSQL with Docker, and verify the web and API applications start successfully.

### Tests for User Story 1

- [ ] T015 [P] [US1] Add a minimal web app startup smoke test in `apps/web/tests/app-shell.spec.tsx` that verifies the basic app shell renders without final portfolio UI.
- [ ] T016 [P] [US1] Add an API bootstrap smoke test in `apps/api/test/bootstrap.spec.ts` that verifies the NestJS application module boots; do not require health endpoints in this test.

### Implementation for User Story 1

- [ ] T017 [US1] Implement the minimal Next.js app shell in `apps/web/app/page.tsx` and `apps/web/app/globals.css` without adding final portfolio design or a large component library.
- [ ] T018 [US1] Wire the web app to typed configuration values in `apps/web/lib/config.ts` or the equivalent minimal runtime config layer.
- [ ] T019 [US1] Verify the foundation UI constraints in `apps/web/app/layout.tsx`, `apps/web/app/page.tsx`, `apps/web/app/globals.css`, `apps/web/next.config.ts`, and `apps/web/package.json`: semantic HTML, accessible document title/metadata, viewport metadata, responsive foundation, no unnecessary heavy UI library, and no choices that prevent future light/dark themes, design tokens, reduced-motion support, or Next.js metadata/SEO. Do not add final visual design, a complete theme system, portfolio components, animations, or a full design system.

**Checkpoint**: The minimal web shell and API bootstrap are independently testable and runnable, with PostgreSQL run through Docker.

---

## Phase 4: User Story 2 - Validate health semantics and dependency readiness (Priority: P1)

**Goal**: Guarantee the API distinguishes process liveness from dependency readiness and behaves correctly when PostgreSQL is unavailable.

**Independent Test**: Exercise `GET /health/live` and `GET /health/ready` with PostgreSQL available and unavailable; verify response behavior matches the health API contract.

### Tests for User Story 2

- [ ] T020 [US2] Add API end-to-end behavior tests in `apps/api/test/health.e2e-spec.ts` verifying HTTP 200 for healthy liveness with PostgreSQL readiness simulated as available and unavailable, HTTP 200 for readiness when required dependencies are available, and HTTP 503 Service Unavailable when PostgreSQL readiness fails. Use a controlled readiness-probe test double so the HTTP tests do not require a live PostgreSQL instance. Verify public response bodies do not expose connection strings, credentials, host details, stack traces, or internal exception messages.

### Implementation for User Story 2

- [ ] T021 [US2] Implement the health module, `GET /health/live`, and `GET /health/ready` in `apps/api/src/health/health.module.ts`, `apps/api/src/health/health.controller.ts`, `apps/api/src/health/health.service.ts`, and `apps/api/src/app.module.ts`. Liveness must return HTTP 200 for a healthy process and never depend on PostgreSQL; readiness must return HTTP 200 when required dependencies are available and HTTP 503 Service Unavailable when one is unavailable, using the database connectivity helper from `packages/database/src/health.ts`. Keep public response bodies minimal and aligned with `specs/001-application-foundation/contracts/health-api.md`.

**Checkpoint**: Health endpoints have one implementation owner and their required healthy/degraded behavior is covered by one focused test task.

---

## Phase 5: User Story 3 - Prepare CI and quality validation for the foundation (Priority: P2)

**Goal**: Ensure the repository supports reliable validation in local development and CI without developer-machine assumptions.

**Independent Test**: Verify CI installs reproducibly, uses the declared Node/pnpm toolchain, and runs quality checks without runtime secrets or a running PostgreSQL instance.

### Implementation for User Story 3

- [ ] T022 [US3] Create `.github/workflows/ci.yml` to use the repository's Node.js and pnpm version contract, install with `pnpm install --frozen-lockfile`, and run `pnpm lint`, `pnpm format:check`, `pnpm typecheck`, `pnpm test`, `pnpm test:integration`, `pnpm test:e2e`, and `pnpm build` in a clean environment.
- [ ] T023 [US3] Configure linting, formatting, type-checking, and tests so they do not require runtime secrets or a running PostgreSQL instance.

**Checkpoint**: CI and local quality checks use the same toolchain and do not assume developer-specific runtime state.

---

## Phase 6: Polish & Cross-Cutting Verification

**Purpose**: Confirm the completed foundation remains within scope and meets all required validation criteria.

- [ ] T024 Review the repository structure against the plan and constitution: confirm `packages/database` is the only Prisma infrastructure owner, Prisma 7 uses the PostgreSQL driver adapter, no artificial health table exists, and no business/domain features were introduced.
- [ ] T025 Run final verification from `specs/001-application-foundation/quickstart.md`: `pnpm lint`, `pnpm format:check`, `pnpm typecheck`, `pnpm test`, `pnpm test:integration`, `pnpm test:e2e`, and `pnpm build`, plus the documented clean-start workflow including dependency installation, environment setup, Docker PostgreSQL startup, and app startup.

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies; establishes workspace and toolchain files.
- **Foundational (Phase 2)**: Depends on Setup; provides shared config, database connectivity, scripts, and test infrastructure.
- **User Stories (Phases 3-5)**: Depend on Foundational. User Stories 1, 2, and 3 can proceed independently after that phase; within User Story 2, write the behavior tests before implementing the health endpoints.
- **Polish (Phase 6)**: Depends on all user stories being complete.

### Parallel Opportunities

- Setup tasks T002-T007 can proceed in parallel where their files do not overlap; T008 may proceed independently after the root manifest exists.
- Config validation tests (T013) can proceed independently of the database tasks once the test infrastructure and config package structure exist.
- User Story 1 smoke tests (T015-T016) can be authored independently.
- User Story 3 CI setup can proceed independently of the app behavior tasks after the toolchain and root scripts are established.

## Implementation Strategy

1. Complete Setup and Foundational phases.
2. Deliver User Story 1 and validate the minimal app shell and API bootstrap.
3. Deliver User Story 2, writing its behavior tests before implementing endpoints.
4. Deliver User Story 3 and verify CI and local quality-check independence.
5. Complete the scope audit and run every final verification command plus the clean-start quickstart.

## Notes

- [P] marks tasks that can proceed in parallel without conflicting file ownership.
- Each implementation responsibility has one owning task; tests and final verification are not repeated across phases.
- No authentication, profile or document domain, sharing, or resume-generation features are in scope.
