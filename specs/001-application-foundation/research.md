# Phase 0 Research: Application Foundation for ProfileVault

## Decision: monorepo structure and package boundaries

**Decision**: Use a pnpm workspace monorepo with the existing structure: apps/web, apps/api, packages/database, packages/contracts, packages/config, and docker.

**Rationale**: The spec and constitution both require a production-quality foundation that supports future feature work without premature abstraction. A small monorepo keeps responsibilities clear while staying lightweight enough for a single-team project. The selected package boundaries align with the constitutional expectation for a clean domain boundary between runtime applications and reusable infrastructure.

**Alternatives considered**: One single app, a Turbo monorepo with many packages, or separate repos. A single app would create architecture drift too early; a large multi-package structure would add complexity without a concrete need. This feature only justifies the required packages.

## Decision: web foundation

**Decision**: Use Next.js App Router with React and TypeScript, but keep the initial shell intentionally minimal and non-product-specific.

**Rationale**: The constitution mandates a premium public experience later, but this feature is intentionally limited to proving the foundation is valid. Early design-system work is deferred, but the foundation must not block it. The App Router and strict TypeScript enable future SEO, accessibility, theming, and responsive design work without locking in a final UI.

**Alternatives considered**: Full portfolio scaffolding, a large component library, or a custom CSS system. These would exceed scope for Feature 001 and would prematurely overfit the app to final UI requirements.

## Decision: API and health semantics

**Decision**: Build the API as a NestJS application with strict TypeScript and expose GET /health/live and GET /health/ready.

**Rationale**: The health endpoints are required for local development and future orchestrator checks, and the constitution requires clear operational readiness semantics. Liveness must remain independent from PostgreSQL so app/process availability can be assessed even when the database is down. Readiness can include PostgreSQL connectivity and fail when the database is not ready.

**Alternatives considered**: A single combined health endpoint or a readiness-only check. A unified endpoint would blur operational semantics and weaken health monitoring. Separate endpoints better support container orchestration and developer debugging.

## Decision: database and Prisma architecture

**Decision**: Use PostgreSQL in Docker for local development and Prisma 7 with the current Prisma 7 driver adapter architecture in packages/database.

**Rationale**: The spec requires Prisma 7 and a current adapter setup. Keeping Prisma logic in the database package isolates infrastructure concerns and avoids premature business models. A separate database runtime and reusable client layer allow future domain features to consume a consistent infrastructure boundary without redesigning the stack.

**Alternatives considered**: Prisma in the API app, direct pg usage everywhere, or a different ORM. Prisma-bound infrastructure in its own package is the cleanest fit for a monorepo and future readiness checks.

## Decision: environment and validation strategy

**Decision**: Use environment files and fail-fast validation for required runtime values, with no secret values exposed in logs or startup output.

**Rationale**: The constitution and spec both require clear runtime configuration and CI-safe validation without developer-specific machine assumptions. Strict validation helps developers discover incorrect settings immediately and prevents hidden runtime failures when dependencies are missing.

**Alternatives considered**: Implicit defaults everywhere or silent fallback to undefined values. Both create difficult debugging and weak operational hygiene. Fail-fast validation is safer and more maintainable.

## Decision: testing and quality gates

**Decision**: Use a single consistent unit-test strategy across the monorepo, with health behavior tests and future expansion points for integration and E2E testing.

**Rationale**: The project requires linting, formatting, type checking, unit tests, and build verification in CI. A single toolchain reduces setup overhead and keeps the foundation maintainable. The plan reserves future integration and E2E coverage without implementing unrelated product tests in this feature.

**Alternatives considered**: Fragmented test tooling for each app or waiting to define test infra later. Fragmentation would increase onboarding friction and reduce CI consistency.

## Decision: scope boundaries

**Decision**: Keep Feature 001 strictly at the platform foundation layer; do not add auth, profile domains, document sharing, resume generation, or portfolio UI work.

**Rationale**: The specification explicitly names these items as non-goals. This reduces risk, preserves a small vertical slice, and avoids speculative abstractions before the domain is fully specified.

**Alternatives considered**: Including a mock admin shell or partial profile domain. Those would contravene the constitution and the feature scope and would create unnecessary rework.

## Outstanding decisions resolved for this feature

- Node.js and package manager versions are set by repository conventions and toolchain compatibility for Next.js and NestJS.
- Docker is the local PostgreSQL runtime; developer machines run the app processes directly.
- Health endpoints are limited to liveness and readiness semantics without domain or auth logic.
- Shared contracts are minimal and reserved for the health API and later explicit shared types.
