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

- [ ] T001 Create the root monorepo structure and config files in `package.json`, `pnpm-workspace.yaml`, `tsconfig.base.json`, `eslint.config.js`, `prettier.config.js`, `vitest.config.ts`, `.env.example`, `.npmrc`, and `README.md`.
- [ ] T002 [P] Create the `apps/web` workspace with the Next.js App Router shell in `apps/web/package.json`, `apps/web/tsconfig.json`, `apps/web/next.config.ts`, and `apps/web/app/layout.tsx`.
- [ ] T003 [P] Create the `apps/api` workspace with the NestJS bootstrap in `apps/api/package.json`, `apps/api/tsconfig.json`, `apps/api/src/main.ts`, and `apps/api/src/app.module.ts`.
- [ ] T004 [P] Create the `packages/database` Prisma infrastructure in `packages/database/package.json`, `packages/database/tsconfig.json`, `packages/database/prisma/schema.prisma`, `packages/database/src/index.ts`, and `packages/database/src/client.ts`.
- [ ] T005 [P] Create the `packages/config` runtime validation package in `packages/config/package.json`, `packages/config/tsconfig.json`, `packages/config/src/index.ts`, and `packages/config/src/env.ts`.
- [ ] T006 [P] Create the `packages/contracts` placeholder package in `packages/contracts/package.json`, `packages/contracts/tsconfig.json`, and `packages/contracts/src/index.ts`.
- [ ] T007 Create the Docker PostgreSQL setup in `docker/postgres/docker-compose.yml` and `docker/postgres/.env.example` and document the local database startup in `README.md`.
- [ ] T008 Define the Node toolchain decision in `.nvmrc` or `.node-version`, `package.json` `packageManager` and `engines`, and document the pinned version in `README.md` for local development and CI consistency.

---

## Phase 2: Foundational (Blocking Prerequisites)

**Purpose**: Put the operating foundation in place before any user-story implementation begins.

**⚠️ CRITICAL**: No user-story work can begin until this phase is complete.

- [ ] T009 Implement the root workspace scripts in `package.json` for `dev`, `dev:web`, `dev:api`, `lint`, `format`, `format:check`, `typecheck`, `test`, and `build`.
- [ ] T010 [P] Implement environment validation and fail-fast configuration loading in `packages/config/src/env.ts` and `packages/config/src/index.ts`, ensuring invalid or missing required values fail fast without exposing secret values in errors.
- [ ] T011 Implement the Prisma 7 database client and connectivity abstraction in `packages/database/src/client.ts` and `packages/database/src/index.ts`, using the PostgreSQL driver-adapter architecture and keeping Prisma infrastructure limited to `packages/database`.
- [ ] T012 [P] Add a lightweight PostgreSQL readiness check abstraction in `packages/database/src/health.ts` using a connectivity query such as `SELECT 1` without creating a domain table for health tracking.
- [ ] T013 Create the API health module in `apps/api/src/health/health.module.ts` and `apps/api/src/health/health.controller.ts` with the `GET /health/live` endpoint defined without a database dependency.
- [ ] T014 Create the API readiness module in `apps/api/src/health/health.service.ts` and wire it into `apps/api/src/app.module.ts` so `GET /health/ready` checks required dependencies including PostgreSQL connectivity.
- [ ] T015 [P] Add health behavior tests in `apps/api/test/health.e2e-spec.ts` that prove liveness stays healthy when PostgreSQL is unavailable while readiness fails or reports degraded status when the database is unavailable.
- [ ] T016 [P] Add config validation tests in `packages/config/src/env.test.ts` or the project's designated unit-test location to prove invalid configuration fails fast and secrets are not leaked in error output.
- [ ] T017 Add root-level verification documentation in `README.md` and `specs/001-application-foundation/quickstart.md` covering the clean-start workflow: install, config, Docker PostgreSQL startup, app startup, and validation commands.

**Checkpoint**: Foundation ready - user-story implementation can now begin in parallel.

---

## Phase 3: User Story 1 - Bootstrap the monorepo foundation (Priority: P1) 🎯 MVP

**Goal**: Establish a runnable monorepo foundation that a developer can install, configure, and start locally without business features.

**Independent Test**: Clone the repo, install dependencies with pnpm, configure the documented env vars, start PostgreSQL with Docker, and run the web and API applications successfully.

### Tests for User Story 1

- [ ] T018 [P] [US1] Add a minimal startup smoke test for the web app in `apps/web/tests/app-shell.spec.tsx` to validate the app can render the basic shell without the final portfolio UI.
- [ ] T019 [P] [US1] Add a minimal startup smoke test for the API in `apps/api/test/bootstrap.spec.ts` to validate the app module boots and the health module is registered.

### Implementation for User Story 1

- [ ] T020 [US1] Implement the minimal Next.js app shell in `apps/web/app/page.tsx` and `apps/web/app/globals.css` without introducing the final public portfolio design or a large component library.
- [ ] T021 [US1] Wire the web app to typed config values in `apps/web/lib/config.ts` or the equivalent minimal runtime config layer so browser/runtime settings are explicit and future-safe.
- [ ] T022 [US1] Add the API bootstrap and health module wiring in `apps/api/src/main.ts`, `apps/api/src/app.module.ts`, and the health files to ensure the service starts cleanly without business APIs.
- [ ] T023 [US1] Ensure the web app and API remain runnable directly on the developer machine while PostgreSQL runs through Docker, as documented in `README.md` and `specs/001-application-foundation/quickstart.md`.
- [ ] T024 [US1] Verify the repository structure preserves the established layout under `apps/`, `packages/`, and `docker/` without additional shared package proliferation.

**Checkpoint**: At this point, User Story 1 should be fully functional and independently testable.

---

## Phase 4: User Story 2 - Validate health semantics and dependency readiness (Priority: P1)

**Goal**: Guarantee the API distinguishes between process liveness and dependency readiness and behaves correctly when PostgreSQL is unavailable.

**Independent Test**: Hit `GET /health/live` and `GET /health/ready` in both healthy and degraded database conditions and confirm that the responses reflect the intended semantics.

### Tests for User Story 2

- [ ] T025 [P] [US2] Add a test in `apps/api/test/health.e2e-spec.ts` for the healthy-case readiness response when PostgreSQL is available.
- [ ] T026 [P] [US2] Add a test in `apps/api/test/health.e2e-spec.ts` for the degraded-case readiness response when PostgreSQL is unavailable.
- [ ] T027 [P] [US2] Add a test in `apps/api/test/health.e2e-spec.ts` confirming that `/health/live` remains successful even when PostgreSQL is unavailable.

### Implementation for User Story 2

- [ ] T028 [US2] Implement the `GET /health/live` endpoint in `apps/api/src/health/health.controller.ts` so it reports the process state only and never depends on PostgreSQL availability.
- [ ] T029 [US2] Implement the `GET /health/ready` endpoint in `apps/api/src/health/health.controller.ts` and `apps/api/src/health/health.service.ts` so it checks dependency readiness including the database connection.
- [ ] T030 [US2] Ensure error responses are safe and do not leak secrets, stack traces, or internal infrastructure details as documented in `specs/001-application-foundation/contracts/health-api.md`.
- [ ] T031 [US2] Validate that the health contract and behavior align with the planning decisions in `specs/001-application-foundation/research.md` and `specs/001-application-foundation/contracts/health-api.md`.

**Checkpoint**: At this point, both health checks should behave independently and correctly under failure conditions.

---

## Phase 5: User Story 3 - Prepare CI and quality validation for the foundation (Priority: P2)

**Goal**: Ensure the repository supports reliable validation in local development and CI without developer-machine assumptions.

**Independent Test**: Run the documented quality commands and the clean-start quickstart in a fresh environment and confirm they execute successfully.

### Tests for User Story 3

- [ ] T032 [P] [US3] Add a root-level validation checklist task in `README.md` or project docs that enumerates the commands for `lint`, `format:check`, `typecheck`, `test`, and `build`.
- [ ] T033 [P] [US3] Add a CI validation plan in `.github/workflows/ci.yml` or the project’s equivalent workflow file that runs the reproducible install and validation commands from a clean environment.

### Implementation for User Story 3

- [ ] T034 [US3] Configure root CI validation in `.github/workflows/ci.yml` to run `pnpm install --frozen-lockfile`, `pnpm lint`, `pnpm format:check`, `pnpm typecheck`, `pnpm test`, and `pnpm build` without depending on local developer state.
- [ ] T035 [US3] Verify the Node version pin and toolchain compatibility in `.nvmrc`, `.node-version`, or `package.json` `engines` and ensure local development and CI use the same version contract.
- [ ] T036 [US3] Ensure linting, formatting, and type-checking do not require runtime secrets or a running PostgreSQL instance while still validating the workspace correctly.
- [ ] T037 [US3] Run the documented clean-start validation workflow from `specs/001-application-foundation/quickstart.md` and record the expected results for final verification in the implementation review.

**Checkpoint**: At this point, the project foundation should be ready for future domain features without compromising CI or local developer workflow.

---

## Phase 6: Polish & Cross-Cutting Concerns

**Purpose**: Final verification that the foundation remains aligned with the spec, plan, and constitution.

- [ ] T038 [P] Review the final repo structure against the plan and constitution to confirm no business features, auth flows, document-sharing code, or resume-generation work were introduced in `apps/`, `packages/`, or `docker/`.
- [ ] T039 [P] Check that the Prisma setup exists only in `packages/database` and that no second Prisma client or database setup was created under `apps/api`.
- [ ] T040 [P] Confirm the Node version pin and package manager decision are documented consistently and remain aligned with local and CI environments.
- [ ] T041 [P] Verify the health and configuration contracts remain minimal, explicit, and free from secret leakage, and that the contract docs reflect the current operational behavior.
- [ ] T042 Run the final repository validation sequence from the project quickstart and confirm all required checks pass before the foundation is considered implementation-ready.

---

## Dependencies & Execution Order

### Phase Dependencies

- **Setup (Phase 1)**: No dependencies - starts immediately.
- **Foundational (Phase 2)**: Depends on Setup completion; blocks all user-story work.
- **User Stories (Phase 3+)**: All depend on Phase 2 completion.
- **Polish (Phase 6)**: Depends on all desired user stories being complete.

### User Story Dependencies

- **User Story 1 (P1)**: Can start after Phase 2; no dependency on other stories.
- **User Story 2 (P1)**: Can start after Phase 2; should be independently testable with the health contract.
- **User Story 3 (P2)**: Can start after Phase 2; verifies quality, CI, and environment discipline.

### Parallel Opportunities

- Setup tasks marked [P] can proceed in parallel.
- Foundational tasks marked [P] can proceed in parallel.
- The user-story test tasks can be written in parallel within each story.
- The polish tasks can run in parallel once implementation is complete.

---

## Parallel Example: User Story 2

```bash
# Run health tests for readiness and liveness in parallel once the health module exists:
Task: "Add a healthy-case readiness response test in apps/api/test/health.e2e-spec.ts"
Task: "Add a degraded-case readiness response test in apps/api/test/health.e2e-spec.ts"
Task: "Add a liveness test in apps/api/test/health.e2e-spec.ts"
```

---

## Implementation Strategy

### MVP First (User Story 1 Only)

1. Complete Phase 1: Setup.
2. Complete Phase 2: Foundational.
3. Complete Phase 3: User Story 1.
4. Stop and validate the app shell, API bootstrap, and local setup.
5. Proceed only after the minimum viable foundation is proven to run.

### Incremental Delivery

1. Complete Setup + Foundational -> foundation ready.
2. Deliver User Story 1 -> app shell and bootability.
3. Deliver User Story 2 -> health semantics and readiness contracts.
4. Deliver User Story 3 -> CI, quality gates, and runtime discipline.
5. Finish with polish, cross-cutting validation, and final constitution alignment checks.

### Parallel Team Strategy

With multiple developers:

1. Team completes Setup + Foundational together.
2. Once foundational work is done:
   - Developer A: User Story 1
   - Developer B: User Story 2
   - Developer C: User Story 3
3. Final polish runs after all user-story tasks are complete.

---

## Notes

- [P] tasks = different files or independent validation areas; they can run in parallel.
- [Story] labels map tasks to specific user stories for traceability and independent delivery.
- Each user story is intentionally small and independently testable.
- Validation tasks for lint, format:check, typecheck, test, and build are explicit and required.
- No feature-implementation work beyond the application foundation is included in this task set.
