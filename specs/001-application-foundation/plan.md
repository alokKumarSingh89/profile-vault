# Implementation Plan: Application Foundation for ProfileVault

**Branch**: `001-application-foundation` | **Date**: 2026-10-07 | **Spec**: [spec.md](spec.md)

**Input**: Feature specification from `/specs/001-application-foundation/spec.md`

## Summary

Feature 001 establishes the production-quality technical foundation for ProfileVault without implementing business features. The plan introduces a pnpm workspace monorepo with a minimal Next.js App Router shell, a strict TypeScript NestJS API, Docker-managed PostgreSQL, a Prisma 7 database package, and minimal shared packages for configuration and contracts. The result is a stable base for future profile, document, sharing, and resume work while keeping the initial implementation narrow, testable, and aligned to the constitutional requirements for security, accessibility, performance, and maintainability.

## Technical Context

**Language/Version**: Node.js 20 LTS; TypeScript 5.x; Next.js App Router with React; NestJS with TypeScript; Prisma 7

**Primary Dependencies**: pnpm workspaces, Next.js, React, NestJS, Prisma, PostgreSQL, ESLint, Prettier, Vitest, Docker Compose

**Storage**: PostgreSQL through Docker for local development; Prisma schema and client in packages/database

**Testing**: Vitest for unit tests; health endpoint tests for process and readiness behavior; future integration/E2E structure reserved in the repo layout

**Target Platform**: Local developer machines for development; CI containerized validation for install, lint, formatting, type check, unit tests, and build

**Project Type**: Web application + API service + shared infrastructure monorepo

**Performance Goals**: Keep the initial web shell light; avoid unnecessary JS, heavy dependencies, or unnecessary interactive complexity; support future performance budgets as part of the constitution

**Constraints**: No auth, no profile domains, no document sharing, no Google Drive integration, no portfolio final UI; app must fit within a small monorepo with clear app/package boundaries; no developer-specific setup assumptions in CI

**Scale/Scope**: Small team, foundation stage, not yet shipping business features; structure designed for future expansion without premature abstraction

## Constitution Check

*GATE: Must pass before Phase 0 research. Re-check after Phase 1 design.*

This feature passes the constitution check because it preserves the five core principles while staying within the intended scope of the foundation feature:

- Public Excellence and Trust: the web shell is intentionally minimal, but the foundation is built to support future premium design, accessibility, responsive behavior, and SEO without locking in a final UI.
- Secure Private Administration: no auth or document-sharing features are introduced; the initial project avoids exposing any private or sensitive runtime decisions before the correct domain workflows are specified.
- Source-of-Truth Data Architecture: Prisma infrastructure is centralized in packages/database and the app/services are not yet domain-specific.
- Maintainable Monorepo Architecture: the repo follows the required monorepo structure, keeps package boundaries minimal, and avoids speculative shared packages.
- Quality, Security, and Delivery Discipline: strict TypeScript, test coverage for health behavior, fail-fast configuration, and CI-ready validation are included from the start.

No constitution violations require a complexity exception in this feature.

## Project Structure

### Documentation (this feature)

```text
specs/001-application-foundation/
├── plan.md
├── research.md
├── data-model.md
├── quickstart.md
├── contracts/
│   └── health-api.md
├── spec.md
├── checklists/
│   └── requirements.md
└── tasks.md   # created later by /speckit-tasks
```

### Source Code (repository root)

```text
profile-vault/
├── apps/
│   ├── web/
│   │   ├── app/
│   │   ├── components/
│   │   ├── lib/
│   │   ├── public/
│   │   ├── tests/
│   │   ├── package.json
│   │   ├── tsconfig.json
│   │   └── next.config.ts
│   └── api/
│       ├── src/
│       │   ├── app.module.ts
│       │   ├── health/
│       │   └── main.ts
│       ├── test/
│       ├── package.json
│       └── tsconfig.json
├── packages/
│   ├── database/
│   │   ├── prisma/
│   │   ├── src/
│   │   ├── package.json
│   │   └── tsconfig.json
│   ├── contracts/
│   │   ├── src/
│   │   └── package.json
│   ├── config/
│   │   ├── src/
│   │   └── package.json
│   └── README.md
├── docker/
│   └── postgres/
│       ├── docker-compose.yml
│       └── .env.example
├── package.json
├── pnpm-workspace.yaml
├── .env.example
├── .npmrc
├── eslint.config.js
├── prettier.config.js
├── vitest.config.ts
├── tsconfig.base.json
└── README.md
```

**Structure Decision**: A lean two-application monorepo with three minimal shared packages is the correct balance for this feature. The database package owns the Prisma and connectivity layer; the contracts package is reserved for explicit shared TypeScript contracts; the config package owns reusable runtime validation rather than a catch-all helper layer. This preserves a clear boundary without over-building the package graph.

## Complexity Tracking

No constitution violations were identified. No complexity exception is required for this feature.

## Phase 0 Research Summary

The planning work resolved the primary architectural unknowns:

- The monorepo will use pnpm workspaces with the required package structure and minimal package counts.
- The app and API will run directly on developer machines, while PostgreSQL lives in Docker for local development.
- Health endpoints will be split into liveness and readiness semantics to align with operational best practices and the constitution.
- The database package will be the single Prisma and connection-layer owner so the API and web app remain thin and future-proof.
- Environment and startup validation will fail fast, but only for required runtime values and with safe error output.

## Phase 1 Design Notes

### Application design

The web app is intentionally minimal: a small app shell, root layout, and a simple landing page that demonstrates the app boots successfully. No premium portfolio UI or large component library is introduced in this feature. Instead, it uses a minimal structure that can support future design tokens, accessibility, light/dark themes, responsive layouts, and SEO work without rework.

### API design

The API begins with a thin bootstrap module and health module. Health endpoints will be implemented directly at the root of the API and will remain framework-native. No domain controllers or business APIs are introduced because they are explicitly out of scope for Feature 001.

### Database design

The database package will include the Prisma schema and client wiring needed to validate connectivity, establish the database runtime contract, and support readiness checks. It will not include ProfileVault business models. The schema can be intentionally empty or include only a minimal metadata table needed for readiness validation, depending on the chosen Prisma setup, but must not represent domain entities.

### Configuration design

A shared config package will validate environment variables and expose typed configuration objects for the web and API. The package will reject invalid or missing values before apps boot so the runtime fails fast with actionable messages. Secret values will be treated as sensitive and will never be echoed into logs or errors.

### Testing design

Unit tests will cover the health endpoints and config validation logic. The repository will reserve folders for future integration and E2E testing, but Feature 001 will not add unrelated product-level tests. Health tests must include both healthy and failing database conditions so readiness is properly exerciseable.

### CI design

CI will run reproducible install and validation commands from the repo root:

- pnpm install --frozen-lockfile
- pnpm lint
- pnpm format:check
- pnpm typecheck
- pnpm test
- pnpm build

This ensures local and CI behavior remain aligned and does not depend on any developer-specific environment state.

## Risk and tradeoff notes

- The project intentionally avoids premature abstraction in the interface layer because the constitution requires maintainability without overengineering. The shared packages are intentionally minimal.
- The health endpoints are operational, not business-level contracts, so their shape remains intentionally small and stable.
- The minimal web shell may look intentionally basic for now; this is a deliberate feature-scope choice and is consistent with the constitution’s requirement that the final premium public experience be designed later, not forced into this foundation step.
- The design resists adding auth, document storage, or resume domains until their requirements are specified and planned in later features.

## Final Gate Check

The plan is aligned with the current spec and constitution. It preserves the product intent, does not stray into domain work, and provides a strong technical foundation for future ProfileVault feature development.

