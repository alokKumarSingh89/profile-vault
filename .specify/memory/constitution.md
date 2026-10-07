<!--
Sync Impact Report:
- Version change: 1.0.0 → 1.1.0
- Modified principles: I. Public Excellence and Trust, II. Secure Private Administration, III. Source-of-Truth Data Architecture, V. Quality, Security, and Delivery Discipline
- Added sections: Security & Privacy Requirements (expanded), Product & Experience Standards (expanded), Performance Requirements, Resume Output Requirements, Engineering Continuity Requirements
- Removed sections: none
- Deferred items: none
-->

# ProfileVault Constitution

## Core Principles

### I. Public Excellence and Trust

The public professional experience MUST demonstrate premium product quality, not a generic resume template or generic SaaS dashboard. It MUST function as a showcase of frontend, UI, and UX engineering ability and must be responsive, accessible, performant, polished across light and dark themes, and optimized for recruiter, hiring-manager, and engineering-manager discovery. Public content MUST prioritize readability, clear hierarchy, and fast comprehension over decorative novelty. The site SHOULD communicate technical depth and professional credibility without sacrificing usability.

### II. Secure Private Administration

Private profile data, employment records, documents, and share links MUST remain protected behind application-controlled authorization. Google Drive is a storage provider, not the authorization system. Access decisions MUST be enforced by the application on every protected document request, support revocation and expiration, and follow least-privilege and auditability requirements. Raw secure-sharing tokens MUST NEVER be persisted. Only cryptographic hashes of secure-sharing tokens MAY be persisted. A share token MUST authorize only the explicitly selected documents and permissions associated with that share. Private Google Drive files MUST NOT be made public as part of the sharing flow. Authentication and session credentials MUST use secure handling appropriate for web applications. Secret material, credentials, infrastructure details, and stack traces MUST never be exposed to public clients.

### III. Source-of-Truth Data Architecture

The database MUST be the source of truth for professional profile information, including resume content, employment history, skills, education, projects, certifications, and documents. Public resume output and downloadable resume artifacts MUST derive from the same structured data model rather than separate static files. DOCX resume generation MUST be supported, and generated DOCX content MUST derive from the same structured profile data used by the public resume. The architecture SHOULD allow PDF and role-specific resume variants later without redesigning the core profile model.

### IV. Maintainable Monorepo Architecture

ProfileVault MUST use a monorepo with a Next.js + React + TypeScript web application, a NestJS + TypeScript API, PostgreSQL, and Prisma 7 with the current Prisma configuration and driver adapter architecture. Storage integrations MUST be abstracted behind a clear boundary so providers such as Google Drive or S3 can be swapped with minimal business-level disruption. Domain logic SHOULD remain independent from framework and storage details where practical, and API contracts MUST be explicit and typed.

### V. Quality, Security, and Delivery Discipline

TypeScript strict mode, ESLint, formatting, unit tests, integration tests, and end-to-end tests MUST be enforced in CI. Business logic and security-sensitive behavior MUST be covered by tests, and new features MUST include appropriate validation. Security-sensitive operations MUST be auditable. Changes MUST be developed through Spec Kit workflows, clarified before implementation when ambiguity materially affects security, architecture, or UX, and reviewed for production readiness. Accessibility MUST be considered during implementation and testing, not only during final visual review. Do not reduce test quality merely to satisfy CI.

## Product & Experience Standards

ProfileVault MUST provide a premium public-facing experience with strong typography, spacing, visual hierarchy, responsive behavior across mobile, tablet, laptop, and desktop layouts, subtle motion, keyboard support, and robust accessibility. Public pages MUST target WCAG 2.2 AA, support keyboard navigation, and respect prefers-reduced-motion. The public application MUST use a coherent design system with reusable tokens for typography, spacing, color, radius, shadow, and motion. It MUST avoid excessive gradients, glassmorphism, animations, and visual noise, and visual novelty MUST NOT reduce readability or usability. Responsive behavior MUST be intentionally designed rather than merely technically supported. Every important screen MUST define loading, empty, error, and success states; forms MUST provide clear validation and actionable error messages; destructive actions MUST require confirmation.

## Security & Privacy Requirements

Employment and personal documents are private by default. Temporary sharing MUST support expiration, revocation, scoped access, and one-time or limited-use policies. Raw secure-sharing tokens MUST NEVER be persisted; only cryptographic hashes of secure-sharing tokens MAY be persisted. A share token MUST authorize only the explicitly selected documents and permissions associated with that share. Document authorization MUST be checked on every protected document request. Private Google Drive files MUST NOT be made public as part of the sharing flow. Authentication and session credentials MUST use secure handling appropriate for web applications. Security-sensitive operations MUST be auditable. Access to documents MUST be authorized by the application and restricted to the minimum required scope. Google Drive file URLs MUST never serve as the application’s authorization mechanism.

## Performance Requirements

Public pages MUST treat performance and Core Web Vitals as product requirements. Images, fonts, JavaScript, and animation SHOULD be budgeted carefully. Major performance regressions MUST block production release until they are understood and explicitly accepted. Performance MUST be designed into the application from the start, not patched at the end of a feature cycle.

## Resume Output Requirements

DOCX resume generation MUST be supported. Generated DOCX content MUST derive from the same structured profile data used by the public resume. The architecture SHOULD allow PDF and role-specific resume variants later without redesigning the core profile model.

## Engineering Continuity Requirements

Do not silently rename, move, or replace established files, directories, modules, database concepts, or architectural boundaries. Architectural changes MUST be explicitly documented in the relevant spec or plan, and the team MUST preserve continuity of the project’s existing structure unless the change is intentionally planned and approved.

## Development Workflow

Features MUST be developed through Spec Kit specifications. Requirements MUST be clarified before implementation when ambiguity materially affects architecture, security, or UX. Plans MUST explain important decisions, tasks SHOULD be small, testable, and traceable to requirements, and incremental vertical slices SHOULD be preferred over large unverified implementations. Established file names, modules, and architectural concepts MUST NOT be silently renamed during later features unless the work is intentionally scoped and documented.

## Governance

This constitution governs the product, architecture, security, and quality decisions for ProfileVault. Amendments MUST document the rationale, include a version bump, and review any impacted principles, security controls, or workflow requirements before adoption. Compliance is measured by whether work remains aligned with privacy, product quality, maintainability, accessibility, performance, and production-readiness standards. The constitution supersedes informal practices when they conflict with the requirements above.

**Version**: 1.1.0 | **Ratified**: 2026-10-07 | **Last Amended**: 2026-10-07
