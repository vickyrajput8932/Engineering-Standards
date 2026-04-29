# Engineering Mandate & Architecture Standards

> **Single source of truth for engineering quality across all projects.**
> This document defines non-negotiable rules. It is referenced in every AI prompt, every code review, and every architectural decision.
> For *how* to apply these rules in practice, see `ARCHITECTURE_PLAYBOOK.md`.
> For *how AI sessions enforce* these rules, see `AI_COLLABORATION_PROTOCOL.md`.
> Non-compliance is not acceptable.

---

## 0. Governing Principle

> **Quality is the fixed constraint. Scope and timeline flex. Quality does not.**

Every feature — existing or new — is measured against this mandate. If an implementation does not meet the standard, it is rebuilt, not patched. Sunk cost has zero weight in this decision.

**Zero Rework Principle:** We do not ship temporary solutions. If a feature is too large to build correctly in one pass, it is decomposed into smaller **permanent** slices — each shipped production-grade, each extending the prior without rewriting it.

This principle governs every rule that follows.

---

## 1. Core Philosophy

- We build for scale, not for today's usage.
- No shortcuts. No temporary fixes. No "we'll fix later."
- Every implementation is production-ready from the first commit.
- Quality of foundations > speed of feature output.
- Refactoring (adapting correct code to new requirements) is healthy and expected.
- Rework (fixing shortcut code that should not have shipped) is prohibited.
- **If you write the same logic twice, the second instance is a bug.**
- **If a value can change, it is configuration, not code.**
- **If a component can be reused, it lives in the shared layer.**
- **Your business logic should not care about technical details.**
- **Consistency is more important than convenience.** The pattern is followed always, not only when easy.

---

## 2. Industry Best Practices (Incorporated By Reference)

Where this mandate is silent on a topic, industry best practices apply by default and carry equal weight:

- **OWASP Top 10** and OWASP ASVS for security
- **Twelve-Factor App** principles for service design
- **Clean Architecture** (Robert C. Martin) for layered separation
- **Domain-Driven Design** principles for modeling business logic
- **REST API design conventions** (resource-oriented, proper HTTP semantics, idempotency)
- **GraphQL best practices** where GraphQL is used
- **Semantic Versioning (SemVer)** for all public interfaces
- **Conventional Commits** for commit message discipline
- **WCAG 2.2 Level AA** for web accessibility
- **Core Web Vitals** thresholds for frontend performance
- **PCI DSS** where payment data is handled
- **GDPR / DPDP Act / CCPA** for applicable jurisdictions regarding PII
- **Framework-official best practices** for the chosen stack

When industry consensus contradicts convenience, industry consensus wins.

---

## 3. Architecture Principles

### 3.1 Clean Architecture — The Four Layers

Every project maintains strict separation across four layers, with dependencies flowing inward only:

**Presentation Layer** — UI, controllers, API handlers. Knows HTTP/UI concerns. Knows nothing about data storage. Delegates to Application; returns DTOs.

**Application Layer** — use cases, orchestration, business workflows, services. Coordinates domain operations. Knows Domain interfaces; does not know specific data access implementations.

**Domain Layer** — entities, value objects, business rules, domain interfaces. Pure, dependency-free, framework-free. Defines contracts that outer layers implement.

**Infrastructure Layer** — databases, external APIs, file systems, message queues, third-party SDKs. Implements interfaces defined by Domain. Handles cross-cutting technical concerns.

### 3.2 The Dependency Rule (Non-Negotiable)

**Dependencies point INWARD.** This is the most important architectural rule.

- Inner layers NEVER import from outer layers.
- Outer layers depend on inner layers via interfaces defined by the inner layer.
- Domain imports from nothing except standard language libraries.
- Application imports only from Domain.
- Infrastructure imports from Domain (and optionally Application for abstractions).
- Presentation imports from Application (and Infrastructure only for DI wiring).

**Project reference rules are enforced structurally** — manifest files (`package.json`, `.csproj`, `pyproject.toml`) must reflect these rules. Violations are caught at build time.

### 3.3 SOLID Principles

- **Single Responsibility** — one reason to change per module
- **Open/Closed** — extendable without modification
- **Liskov Substitution** — implementations respect their contracts
- **Interface Segregation** — many small interfaces over one large one
- **Dependency Inversion** — depend on abstractions, not concretions

### 3.4 Modularity

- Modular, extensible, reusable design throughout.
- No tight coupling. Dependencies flow inward toward the domain.
- No monolithic shortcuts.
- **Feature-based organization** preferred over technical-type organization.

### 3.5 Scalability In Design

- Horizontal scalability considered in data model and API design from day 1.
- Stateless services — no in-memory session state.
- Advanced patterns (BFF, microservices, message queues, CQRS, event sourcing) adopted **only when justified by concrete requirements**, never preemptively.

### 3.6 The Consistency Rule

Architecture is applied consistently. There are no "simple CRUD exceptions." The layered pattern is followed whether the feature has 1 endpoint or 100.

---

## 4. Entity, DTO & Data Access Discipline

### 4.1 Domain Entity Purity

Domain entities are pure business objects. They must not contain:

- Framework attributes or decorators tied to the data access library
- Database column mappings or table name attributes
- Serialization attributes tied to presentation frameworks
- Validation attributes tied to the presentation layer
- Framework-specific imports of any kind

How entities are stored is an Infrastructure concern, handled outside the entity itself.

### 4.2 DTOs Are Not Entities

- **DTOs live in the Application layer.** They represent data crossing the API boundary.
- **Entities never leave the Domain.** They are never returned from APIs and never accepted as API inputs.
- **The Presentation layer only sees DTOs.**
- **Mapping between DTOs and entities happens in the Application layer.**
- **DTOs are versioned with the API.** Changes to entities do not automatically change DTOs.

### 4.3 Repository Pattern For Data Access

- **All data access flows through a repository abstraction** defined as an interface in Domain.
- **Repository interfaces live in Domain; implementations live in Infrastructure.**
- **Application services never import the data access library directly.** They consume repository interfaces.
- **Complex queries are encapsulated in repositories** — services orchestrate repositories.

### 4.4 Unit of Work For Transactions

- **Transactions spanning multiple repositories use a Unit of Work abstraction** defined in Domain.
- **Services call `saveChanges()` on the Unit of Work**, not on individual repositories.
- **One logical operation = one Unit of Work transaction.** Partial writes are prohibited.

### 4.5 Controllers Are Thin

- No business logic in controllers.
- No data access in controllers.
- No entity construction in controllers — they accept DTOs, pass DTOs to services.
- Controllers handle HTTP concerns only.
- **A controller method should be under ~50 lines.**

---

## 5. Cross-Cutting Concerns Discipline

Cross-cutting concerns (audit trails, soft deletes, logging, authorization, caching, tracing) are implemented **once** at the appropriate architectural boundary — never manually repeated in business services.

### 5.1 The Rule

**If a concern applies to more than one feature, it is not implemented per-feature.** It is implemented once at an infrastructure or application boundary and applied automatically.

### 5.2 Audit Trail

- Entities that need audit fields implement a marker interface (e.g., `IAuditable`).
- The data access layer intercepts save operations and automatically populates `createdAt`, `createdBy`, `modifiedAt`, `modifiedBy`.
- **Business services contain zero audit code.**
- Created-at and created-by are immutable after creation.

### 5.3 Soft Delete

- Entities supporting soft delete implement a marker interface (e.g., `ISoftDeletable`).
- The data access layer converts hard deletes to soft deletes automatically.
- Query filters automatically exclude soft-deleted records unless explicitly requested.

### 5.4 Logging

- Structured logging initialized once at bootstrap, injected where needed.
- Correlation IDs attached at the request boundary, flowing through every log.
- Business services log business events, not technical noise.

### 5.5 Authorization

- Authorization checks live in the Application layer (business-rule authorization) and Presentation layer (role/permission gates).
- Never duplicated inside data access code.
- Policy-based authorization preferred over scattered role checks.

### 5.6 Current-User Context

- Access to the current user goes through an abstraction defined in Application, implemented in Infrastructure.
- Business services depend on the abstraction, not on HTTP primitives.

### 5.7 Caching

- Caching is an Infrastructure concern.
- Added when measured latency or load requires it — never preemptively.
- Application services call cache-aware implementations through the same interfaces used for non-cached versions.

---

## 6. Frontend Architecture & Page Discipline

### 6.1 Pages Are Composition, Not Logic

- **No business logic in page files.**
- **No data-fetching logic duplicated across pages.** Lives in shared hooks or service modules.
- **No validation rules in page components.** Lives in shared schema layer.
- **No inline styles or one-off styling in pages.**
- **Pages route and compose. They do not compute, validate, or own state beyond navigation.**

### 6.2 Component Hierarchy

- **Primitives / Atoms** — pure, no business logic
- **Composites / Molecules** — composed from primitives
- **Patterns / Organisms** — reusable application sections
- **Features** — feature-specific compositions glued to business logic
- **Pages / Screens** — route-bound top-level compositions

Rules:
- Any component used in more than one place lives in the shared layer.
- Any component with business logic lives in the feature layer.
- Primitives and composites are presentational only.

### 6.3 Feature Isolation

- Each feature exposes a clean public API (single index file).
- Features do not reach into each other's internals.
- Cross-feature communication flows through the application layer.

### 6.4 Routing

- Routes declarative and centralized.
- Route-level code-splitting for non-trivial features.
- Protected routes guarded at a shared layer.

---

## 7. Shared Code & DRY Discipline

### 7.1 The Shared Layer

Every project has an explicit shared layer containing:

- Shared Components, Hooks/Composables, Utilities, Constants & Enums, Types/Interfaces
- Shared API Client (single configured instance)
- Shared Validation Schemas (one schema per data shape, reused across UI and API)
- Shared Error Handlers
- Shared Theme & Design Tokens

### 7.2 DRY Enforcement

- If you write the same logic twice, the second instance is a bug.
- If you write the same value twice, it becomes a constant.
- If you write the same type twice, it becomes a shared type.
- Copy-paste is not a valid implementation strategy.

### 7.3 Shared API Client Rules

- One configured client instance per API surface.
- Auth, retry, error handling, logging, base URL configured once.
- No raw `fetch`/`axios` calls scattered across features.

### 7.4 Shared Validation

Schemas (Zod, Yup, Joi) defined once and reused across frontend forms, backend API validation, and type generation.

---

## 8. Configuration & Zero Hard-Coding Policy

### 8.1 The Rule

**If a value can change — ever, in any environment, for any reason — it is configuration, not code.**

### 8.2 What Counts As Configurable

Never hardcoded in business logic:
- Environment values (URLs, keys, secrets, connection strings)
- Business thresholds (limits, quotas, timeouts, retry counts)
- Feature parameters (radius ranges, OTP expiry, rate limits, pagination sizes)
- Enums and categorical values (roles, statuses, categories)
- UI text and copy (lives in i18n/locale files)
- Validation rules (lives in shared schema layer)
- Feature flags
- Integration endpoints

### 8.3 Configuration Hierarchy

Resolved in order (later overrides earlier):
1. Defaults in code
2. Config files (non-secret, committed)
3. Environment variables (per-environment, includes secrets)
4. Remote config / feature flags (runtime-toggleable)

### 8.4 Prohibited Hard-Coding

Prohibited in business logic:
- Magic numbers
- Magic strings
- Hardcoded URLs, paths, endpoints, timeouts
- Hardcoded UI text or error messages
- Validation rules embedded in form components

### 8.5 Single Source Of Truth Per Value

- Each configurable value has exactly one authoritative source.
- No duplication across config files.
- Same value in multiple places = imported from source, never copied.

---

## 9. Code Quality Rules

- No dead code, duplicate logic, unused variables/imports/functions, commented-out code blocks.
- No `console.log` (or equivalent) in production — use structured logger.
- No `TODO`/`FIXME`/`HACK` without a tracked ticket and deadline.
- No silent exception swallowing.
- Every function has single clear responsibility.
- Readable before clever. Explicit before implicit.
- Functions < ~50 lines. Files < ~300 lines.

---

## 10. Naming, Structure & File Conventions

### 10.1 Naming

- Consistent conventions chosen once per project, followed without exception.
- Files: kebab-case or PascalCase (per project)
- Directories: kebab-case
- Variables/functions: per language convention
- Classes/Types: PascalCase
- Constants: SCREAMING_SNAKE_CASE
- Env vars: SCREAMING_SNAKE_CASE with project prefix
- Booleans: prefixed `is`/`has`/`can`/`should`
- Event handlers: prefixed `handle`/`on`
- Names describe intent, not type.

### 10.2 Folder Structure

- Feature-based organization preferred over type-based.
- Shared code in explicit `shared/` or `common/` root.
- Tests co-located or mirrored.
- Configuration in dedicated `config/` folder.

### 10.3 Import Discipline

- Absolute imports for cross-feature.
- Relative imports only for same-feature/folder.
- Import order enforced (external → internal → relative → styles).
- No circular dependencies. Ever.

---

## 11. Data & API Policy

- **No mock data in the frontend. Ever.**
- **No hardcoded data anywhere.**
- **All data flows through real APIs**, including test and seed data.
- Test/dummy data seeded via migrations, accessed via the same API layer.
- **API contracts versioned** (`/v1/`, `/v2/`) from the first public endpoint.
- Consistent response envelope.
- Input validation at API boundary, always.
- Output serialization explicit — never return entities to clients.
- Pagination mandatory on unbounded endpoints.
- Idempotency on non-GET endpoints where repeatability matters.
- Correlation IDs in responses.

---

## 12. State Management Discipline

### 12.1 State Categories

- **Server state** — from APIs, managed by dedicated library (React Query, SWR, etc.)
- **Client state** — UI state (modals, forms, theme)
- **URL state** — filters, pagination, search (shareable/bookmarkable)
- **Form state** — managed by dedicated form library

### 12.2 Rules

- Server state never manually synced into client state.
- Global client state only when distant components genuinely need the same state.
- Derived state computed, not stored.
- State colocated; promoted only when necessary.
- No prop drilling beyond 2 levels.

---

## 13. UI/UX Standards

### 13.1 Design System As Source Of Truth

- Documented design system mandatory.
- Design tokens (colors, typography, spacing, radii, shadows, motion) are single source for all visual values.
- No raw colors, font sizes, or pixel values in components. Only tokens.
- Component library used for all standard UI. No reinventing per page.

### 13.2 Interaction Consistency

- Loading, empty, error, success states implemented for every data-dependent view.
- Form patterns standardized.
- Navigation patterns consistent.
- Modal/dialog behavior consistent.
- Keyboard interactions follow platform conventions.

### 13.3 Responsive Design

- Mobile-first approach.
- Defined breakpoints used consistently.
- Touch targets meet platform minimums.
- No horizontal scrolling on mobile unless intentional.

### 13.4 Typography & Spacing

Scale-based. No arbitrary values.

### 13.5 Iconography & Imagery

One icon library per project. Automatic image optimization.

### 13.6 Motion

- Follows design system tokens.
- Respects `prefers-reduced-motion`.
- Serves comprehension or feedback, not decoration.

---

## 14. Accessibility Standards

### 14.1 Baseline: WCAG 2.2 Level AA

Non-negotiable. All production code meets or exceeds WCAG 2.2 AA.

### 14.2 Specific Requirements

- Semantic HTML
- Keyboard navigation for every interactive element
- Focus management (visible indicators, logical order, restoration after modal close)
- Screen reader support (ARIA where needed, live regions for dynamic updates)
- Color contrast ≥ 4.5:1 body, ≥ 3:1 large text/UI
- Form labels associated (never placeholder-as-label)
- Error messages programmatically associated
- Meaningful alt text; empty alt for decorative
- No content conveyed by color alone
- Touch/target sizes per platform guidelines

### 14.3 Testing

- Automated accessibility tests in CI (axe, Lighthouse)
- Manual keyboard-only testing for critical flows
- Screen reader testing for critical flows

---

## 15. Internationalization & Localization

- All UI strings externalized from day 1.
- No hardcoded UI text.
- Locale-aware formatting for dates, times, numbers, currencies.
- Pluralization via i18n library.
- RTL support considered where market requires.
- Locale user-selectable where multiple locales are supported.

---

## 16. Security Standards (Zero Tolerance)

- Zero-trust mindset on every request, user, service.
- OWASP Top 10 compliance is minimum, not target.
- Auth and authz separate, explicit, enforced at every protected endpoint.
- Input validation and sanitization on every user-supplied value.
- Output encoding to prevent XSS (content-aware).
- No secrets in code. Vault/env vars only.
- No sensitive data in logs, URLs, client-side storage beyond necessity.
- Rate limiting on all public endpoints, especially auth.
- PII handling complies with GDPR/DPDP/CCPA as applicable.
- Encryption at rest and in transit.
- Principle of least privilege.
- CSRF protection on state-changing operations.
- CORS explicitly configured (never wildcard in production).
- Security headers (CSP, HSTS, X-Frame-Options, etc.) globally applied.
- Dependency vulnerability scanning on defined cadence.
- Secure session management.
- Password hashing with bcrypt/argon2.
- Security review required before auth, payment, or PII features ship.

---

## 17. Scalability & Performance Standards

- Stateless services.
- Efficient database queries with explicit indexing on hot paths.
- N+1 queries are bugs.
- Pagination mandatory on unbounded endpoints.
- Caching added when measured need exists.
- Async via queues for operations that don't need to block responses.
- Connection pooling configured.
- Migrations never block writes on large tables without planning.
- Background jobs have retry, timeout, dead-letter handling.

---

## 18. Frontend Performance Standards

### 18.1 Core Web Vitals

- LCP ≤ 2.5s
- INP ≤ 200ms
- CLS ≤ 0.1

### 18.2 Performance Budget

JS bundle, image weight, third-party scripts — defined budgets enforced in CI.

### 18.3 Rules

- Code splitting per route minimum.
- Lazy loading below the fold.
- Modern image formats (WebP, AVIF).
- Font loading optimized.
- No unnecessary API calls.
- Debounce/throttle user-triggered network calls.
- Memoization where measurably beneficial, not by default.

---

## 19. Platform Consistency

- One unified backend serves all client platforms.
- Shared database, shared authentication, shared business logic.
- No platform-specific business logic unless justified and documented.
- API contracts platform-agnostic.
- Design system applied consistently across platforms.

---

## 20. Production Readiness

- Environment separation: Dev/Staging/Prod with isolated data, secrets, infrastructure.
- Structured logging with consistent format/severity.
- Error tracking integrated from first deployment.
- Application metrics captured from first deployment.
- Health check endpoints on every service.
- CI/CD pipeline with automated builds, tests, deployments.
- Versioned reversible migrations applied through pipeline.
- Secrets via proper vault.
- Graceful degradation when dependencies fail.
- Backup and disaster recovery defined and tested.
- Zero-downtime deployments as default.
- Rollback procedures documented and rehearsed.

---

## 21. Testability As An Architectural Requirement

Testability is not a testing concern. It is an architectural outcome.

- **Business logic must be testable without infrastructure.** If testing a service requires real DB/HTTP/external API, architecture has failed.
- Dependencies injected via interfaces.
- Pure functions preferred for non-stateful logic.
- Side effects isolated behind interfaces at Infrastructure boundary.
- **If a module is hard to test, it is architecturally wrong.** Fix: restructure. Not: skip the test.

---

## 22. Testing Standards

- Automated tests mandatory. Untested code does not ship.
- Test pyramid: unit for business logic, integration for API contracts, E2E for critical flows.
- Security-critical flows have dedicated coverage.
- No test-skipping without tracked ticket + deadline.
- Test data follows same rules as application data.
- Component tests for shared UI components.
- Visual regression for design system components.
- Contract tests between frontend and backend.
- Coverage thresholds enforced in CI as floor, not goal.

---

## 23. Observability

- Structured logs with correlation IDs.
- Error tracking for every unhandled exception with full context.
- Metrics for request volume, error rates, latency, business counters.
- Alerts for critical failure modes.
- Distributed tracing for multi-service systems.
- Frontend RUM for Core Web Vitals + JS error tracking.
- User-facing errors logged with reproduction context.

---

## 24. Error Handling & Stability

- No silent failures.
- Consistent error response format across APIs.
- User-facing messages clear, actionable, never leak internals.
- Retry with exponential backoff for transient failures.
- Circuit breakers on external service calls.
- Graceful UI fallback states for loading/empty/error on every data-dependent view.
- Error boundaries isolate component failures.
- Offline handling considered.
- Domain exceptions, infrastructure exceptions, API errors are distinct categories with defined handling at each layer.

---

## 25. Feature Flags & Deployment Safety

- Feature flags decouple deployment from release.
- Risky changes gated by flags.
- Rollback strategy for every deployment.
- Blue-green/canary when traffic justifies.
- Flags cleaned up after rollout.
- Kill switches for expensive external services.

---

## 26. Documentation Standards

- README.md at project root (purpose, setup, architecture).
- API docs auto-generated and current.
- ADRs for non-trivial architectural decisions.
- Inline comments explain *why*, not *what*.
- Onboarding docs enable local setup in 30 minutes.
- Design system components documented with examples.
- Runbooks for production operations.

---

## 27. Dependency Hygiene

- Every new dependency vetted (maintenance, security, license, bundle impact, transitive count).
- No abandonware.
- Minimal footprint — prefer ~20 lines of in-house code over trivial dependencies.
- Lockfiles committed.
- Regular updates on defined cadence.
- Security advisories acted on within severity-based SLAs.
- License compliance reviewed.

---

## 28. Version Control Discipline

- Conventional Commits for all commit messages.
- Feature branches — no direct commits to `main`.
- PRs required for `main`, with review.
- Small, focused commits.
- No unjustified binary blobs.
- `.gitignore` discipline.
- Branch protection on `main`.
- SemVer for releases.

---

## 29. Anti-Patterns (Strictly Prohibited)

**Architecture & Layering**
- Business logic in controllers, route handlers, or UI components
- Data access code in Application layer (direct ORM/SDK bypassing repositories)
- Framework attributes or decorators on Domain entities
- Entities returned from API endpoints or accepted as API inputs
- Inner layers depending on outer layers
- Circular dependencies between modules
- Skipping layers for "simple" CRUD
- Direct database access from controllers
- Manual audit field setting in business services
- Page-specific logic that should be shared

**Data & Logic**
- Mock data in UI or production code paths
- Hardcoded business logic or configuration
- Magic numbers or magic strings in business logic
- Skipped input validation
- Copy-pasted code instead of shared utilities

**Security**
- Security shortcuts of any kind
- Secrets committed to version control
- Unauthenticated/unauthorized endpoints on protected resources
- Unsanitized user input reaching DB/filesystem/external systems
- Sensitive data in logs or URLs

**UI/UX**
- One-off styling outside the design system
- Hardcoded colors, font sizes, spacing values
- Reinventing shared components per page
- Inaccessible interactive elements
- Hardcoded UI text

**Operations**
- Temporary fixes framed as "we'll fix later"
- Manual changes to production data or schema
- Disabled/skipped tests without documented justification
- Unbounded queries without pagination
- Sync processing of slow ops that block responses
- `console.log` left in production
- `TODO`/`FIXME` without tracked tickets

---

## 30. Definition of Done

**Architecture & Code**
- [ ] Clean Architecture layer boundaries respected
- [ ] Dependency direction correct (inward only)
- [ ] Domain entities pure (no framework attributes, no infrastructure imports)
- [ ] DTOs at API boundary (no entities exposed)
- [ ] Data access via repository abstraction
- [ ] Cross-cutting concerns automated (no manual audit/logging/authz in services)
- [ ] Controllers thin (< 50 lines, no business logic)
- [ ] SOLID principles applied
- [ ] No hardcoded values, magic numbers, mock data
- [ ] All shared logic in shared layer
- [ ] No business logic in pages or UI primitives
- [ ] No duplicate code or constants
- [ ] All configuration externalized

**UI/UX**
- [ ] Design system tokens and shared components exclusively
- [ ] Loading/empty/error/success states implemented
- [ ] Responsive across defined breakpoints
- [ ] WCAG 2.2 AA verified
- [ ] All user-facing text externalized for i18n

**Data & API**
- [ ] All data via real APIs — no mocks
- [ ] Input validation on every API boundary
- [ ] Consistent response envelope
- [ ] Pagination where applicable
- [ ] API documentation updated

**Security**
- [ ] Auth and authz enforced
- [ ] Input validated and sanitized
- [ ] No secrets in code
- [ ] Rate limiting on public endpoints
- [ ] Security review passed (for sensitive features)

**Testability & Testing**
- [ ] Business logic testable without infrastructure
- [ ] Automated test coverage for critical logic
- [ ] Unit tests use mocked dependencies
- [ ] Integration tests for API contracts
- [ ] E2E tests for critical user flows

**Quality & Operations**
- [ ] Logging and error tracking instrumented
- [ ] Performance budget respected
- [ ] Observable in production
- [ ] Feature flag in place if risk warrants
- [ ] Documentation updated
- [ ] CI/CD passes cleanly
- [ ] Reviewed against this mandate

Work that does not meet this bar is not "done." It is in progress.

---

## 31. Enforcement

- Any code or design violating this mandate is rejected.
- Every feature reviewed against this document before merge.
- This document is the single source of truth for engineering standards.
- Updates require documented justification and explicit approval. Drift is not allowed.
- All AI-assisted development references this document in every prompt. See `AI_COLLABORATION_PROTOCOL.md`.
- Tactical application of these rules is in `ARCHITECTURE_PLAYBOOK.md`.

---

## 32. Trade-off Transparency

When a genuine trade-off exists:
- It is named explicitly, not hidden.
- The cost of each option is documented.
- The chosen path is recorded with reasoning (usually as an ADR).
- No trade-off silently compromises this mandate.

---

## 33. Scope vs. Quality

Two independent dials. Only scope is negotiable.

- Scope can shrink or be phased.
- Quality cannot be reduced to meet timelines. Ever.

When timeline pressure exists, cut scope. Do not cut quality.

---

## 34. Review Cadence

Reviewed when:
- New category of risk emerges
- Recurring violation indicates a rule needs sharpening
- At minimum, every six months

Updates are deliberate, not reactive.

---

*This mandate is the permanent standard. It governs every line of code, every architectural decision, every AI-assisted implementation. Quality is the constraint. Everything else flexes around it.*

*For tactical application — decision trees, layer patterns, feature workflows, code review checklists — see `ARCHITECTURE_PLAYBOOK.md`.*
