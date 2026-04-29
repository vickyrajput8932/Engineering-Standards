# AI Collaboration Protocol

> **The enforcement mechanism for `ENGINEERING_MANDATE.md` and `ARCHITECTURE_PLAYBOOK.md` in all AI-assisted development sessions.**
> Paste this at the start of every session with any AI assistant (Claude, Copilot, Antigravity, Cursor, or any future tool).

---

## Purpose

AI coding tools are powerful at implementation but indifferent to architectural discipline. Without explicit enforcement, they default to convenient shortcuts — not out of malice, but because each individual suggestion looks reasonable in isolation while accumulating into technical debt. This protocol ensures every AI session operates under the same non-negotiable standards defined in `ENGINEERING_MANDATE.md` and applies the patterns from `ARCHITECTURE_PLAYBOOK.md`.

---

## The Three-Document System

- **`ENGINEERING_MANDATE.md`** — The law. What is required, what is prohibited.
- **`ARCHITECTURE_PLAYBOOK.md`** — The patterns. How to apply the law in practice.
- **`AI_COLLABORATION_PROTOCOL.md`** (this file) — How to make AI tools follow both.

Attach all three to AI sessions when possible. If attachment isn't possible, paste the Session Prefix below.

---

## 📌 Session Prefix (Copy-Paste This At The Start Of Every AI Session)

```
You are operating under the Engineering Mandate defined in ENGINEERING_MANDATE.md
and the Architecture Playbook defined in ARCHITECTURE_PLAYBOOK.md (attached or
referenced in this repository).

GOVERNING PRINCIPLE:
Quality is the fixed constraint. Scope and timeline flex. Quality does not.
Zero rework — if something cannot be built permanently in one pass, decompose
it into smaller permanent slices. Never ship temporary solutions.

CORE LAWS:
- If you write the same logic twice, the second instance is a bug.
- If a value can change, it is configuration, not code.
- If a component can be reused, it lives in the shared layer.
- Your business logic must not care about technical details.
- Consistency is more important than convenience.
- Industry best practices apply by default wherever the mandate is silent.

ARCHITECTURE — CLEAN ARCHITECTURE NON-NEGOTIABLE:
1. Four layers: Presentation → Application → Domain ← Infrastructure.
2. Dependencies point INWARD only. Inner layers never import from outer layers.
3. Domain has zero external dependencies except standard language libraries.
4. Application imports only Domain. No direct ORM or HTTP framework imports.
5. Infrastructure implements interfaces defined in Domain.
6. Presentation delegates to Application; imports Infrastructure only for DI wiring.

ENTITY, DTO & DATA ACCESS DISCIPLINE:
7. Domain entities are pure — no ORM attributes, no database annotations,
   no serialization attributes, no framework imports.
8. DTOs live in Application. Entities never leave the Domain layer.
9. All data access flows through a repository abstraction (interface in Domain,
   implementation in Infrastructure).
10. Transactions use Unit of Work. One logical operation = one UoW save.
11. Controllers are thin (< 50 lines). No business logic. No data access.

CROSS-CUTTING CONCERNS — AUTOMATED, NEVER MANUAL:
12. Audit trail (createdAt/createdBy/modifiedAt/modifiedBy) populated automatically
    at the data access boundary via marker interface. Services contain zero audit code.
13. Soft delete applied automatically via marker interface and save interceptor.
14. Structured logging with correlation IDs, injected via DI. Initialized once.
15. Current-user context accessed through Application-layer interface, never
    directly from HTTP primitives.
16. Authorization checks at Presentation (role gates) and Application
    (business-rule authorization). Never in data access code.

FRONTEND DISCIPLINE:
17. Pages are composition only — no business logic, no data-fetching logic,
    no validation logic in page files.
18. All shared components, hooks, utilities, constants, and types live in
    the shared layer.
19. One shared API client, one shared validation schema per data shape,
    one design system.
20. No duplicate code, ever.

CONFIGURATION:
21. Zero hard-coding of business values. All thresholds, timeouts, limits,
    enums, and UI text are externalized.
22. No magic numbers or magic strings in business logic.
23. Single source of truth for every configurable value.

DATA & API:
24. No mock data in frontend. No hardcoded data anywhere. All data via real APIs.
25. Input validation at every API boundary. Consistent response envelopes.
    Pagination mandatory.
26. Versioned APIs from the first public endpoint.

UI/UX:
27. Design system is the single source of truth for visual values.
    No raw colors, sizes, or spacing.
28. Loading, empty, error, and success states for every data-dependent view.
29. WCAG 2.2 AA accessibility compliance. Mobile-first responsive design.
30. All UI strings externalized for i18n from day 1.

SECURITY (ZERO TOLERANCE):
31. OWASP compliance, zero-trust mindset, input validation on every boundary.
32. No secrets in code. Rate limiting on all public endpoints, especially auth.
33. Proper authentication and authorization on every protected endpoint.

TESTABILITY AS ARCHITECTURE:
34. Business logic must be testable WITHOUT infrastructure (no real DB,
    no real network, no real external APIs).
35. If a module is hard to test, it is architecturally wrong.
    Fix: restructure. Do not: skip the test.

QUALITY & OPERATIONS:
36. Structured logging, error tracking, observability from day one.
37. Automated tests for critical logic. Untested code does not ship.
38. Environment separation (dev/staging/prod). Reversible migrations.
    Feature flags for risky changes.
39. Shared backend across all client platforms. No platform-specific
    business logic.

PROHIBITED (REJECT ON SIGHT):
- Business logic in controllers, UI components, or database triggers
- Framework attributes or decorators on Domain entities
- Entities returned from API endpoints or accepted as inputs
- Direct ORM/SDK imports in Application layer
- Manual audit field setting in business services
- Skipping layers "for simple CRUD"
- Temporary fixes, "we'll fix later", mock data, hardcoded values
- Magic numbers, magic strings, copy-pasted code
- One-off styling outside the design system
- Skipping validation, authentication, authorization, or tests
- Silent error swallowing
- Secrets in code, logs, or URLs
- Unbounded queries without pagination
- console.log in production code paths
- TODO/FIXME without tracked tickets

BEHAVIOR REQUIREMENTS:
- If my request would violate the mandate, REJECT it and propose the
  permanent alternative.
- If trade-offs exist, name them explicitly with costs on both sides.
- Do not hallucinate context. If information is missing, ask.
- Do not assume the mandate allows convenience. It does not.
- Every suggestion must pass the Definition of Done in ENGINEERING_MANDATE.md.

FOR IMPLEMENTATION REQUESTS:
- Produce production-grade code only.
- State which layer each file belongs to (Domain / Application / Infrastructure / Presentation).
- Include input validation, error handling, and logging in every implementation.
- Reference the relevant pattern from ARCHITECTURE_PLAYBOOK.md for cross-cutting concerns.
- Identify which shared modules are used or created.
- Identify which configuration values are externalized.
- Flag any code you generate that would need future rework — rework is prohibited.

Acknowledge these rules before proceeding.
```

---

## How To Use This Protocol

### For Conversational AI Assistants (Claude, ChatGPT, etc.)

1. Paste the session prefix above at the start of any new conversation.
2. Attach or reference `ENGINEERING_MANDATE.md` and `ARCHITECTURE_PLAYBOOK.md` if the tool supports file context.
3. Wait for explicit acknowledgment before proceeding with any task.
4. If the assistant ever suggests a shortcut, respond with: *"This violates the mandate. Propose the permanent alternative."*

### For In-Editor AI Tools (Copilot, Antigravity, Cursor, etc.)

1. Place `ENGINEERING_MANDATE.md` and `ARCHITECTURE_PLAYBOOK.md` at the repo root so the tool indexes them.
2. Add a `.cursorrules`, `.github/copilot-instructions.md`, or equivalent file containing the session prefix above.
3. For project-specific rules, create `PROJECT_CONTEXT.md` (see template below).
4. Review AI-generated code against the mandate before committing. AI tools cannot enforce the mandate on themselves; the human is the final reviewer.

### For Agentic / Autonomous AI

When delegating work to an autonomous agent:

1. Include the session prefix in the agent's system instructions.
2. Attach both the Mandate and the Playbook.
3. Define acceptance criteria as literal items from the `Definition of Done` in the Mandate.
4. Require the agent to produce a self-audit against the Mandate and Playbook before marking work complete.
5. Reject any output that does not include this self-audit.

---

## Project-Specific Supplement

Each project should maintain a `PROJECT_CONTEXT.md` that is referenced alongside the Mandate and Playbook. This file captures project-specific decisions without diluting universal standards.

Recommended structure for `PROJECT_CONTEXT.md`:

```
# [Project Name] — Project Context

## Product Overview
What this project is, who it serves, what problem it solves.

## Tech Stack (Locked Decisions)
- Frontend: [framework + version]
- Backend: [framework + version]
- Database: [engine + version]
- ORM / Data access: [library]
- Auth: [mechanism]
- Hosting: [provider]
- Key integrations: [list]

## Clean Architecture Realization
How the four layers are realized in this codebase.
- Domain folder: [path]
- Application folder: [path]
- Infrastructure folder: [path]
- Presentation folder: [path]
- Shared folder: [path]

## Shared Layer Inventory
- Component library location and structure
- Shared hooks and utilities location
- Design tokens location
- Validation schemas location
- API client location
- Constants and enums location

## Cross-Cutting Concern Implementation
- Audit trail mechanism: [e.g., ORM save hook, middleware]
- Soft delete mechanism: [e.g., query filter + save interceptor]
- Logging: [library and configuration location]
- Current user context: [interface name and implementation location]
- Authorization: [pattern used]

## Design System Reference
- Design tokens (or link to design file)
- Component library documentation
- Typography and spacing scales

## Configuration Inventory
Every configurable value, its default, and where it's defined.

## Domain Glossary
Key terms and their precise meanings in this project.

## Known Constraints
Regulatory, business, or technical constraints that shape decisions.

## Current Phase
Where the project is in its lifecycle. What is in scope. What is out of scope.

## Open Architectural Decisions
Decisions not yet made. Tracked so they are not made accidentally.
```

---

## Red Flags In AI Output — Reject Immediately

### Architecture Red Flags
- **Business logic inside a controller or route handler** — must move to Application service.
- **ORM/data access library imported in Application layer** — must go through repository abstraction.
- **Entity returned directly from an API endpoint** — must map to DTO first.
- **Entity accepted as request body** — must accept DTO.
- **Framework attributes on a Domain entity** (ORM decorators, validation decorators) — entities must be pure.
- **Manual audit/logging code in a business service** — must be automated at infrastructure boundary.
- **Direct database query inside a service** — must go through a repository method.
- **Skipping a layer because "this is simple"** — consistency is non-negotiable.
- **New code that depends on a parent of its own layer** (e.g., Domain importing from Infrastructure) — dependency direction violation.

### Data & Logic Red Flags
- **"// TODO: add validation later"** — validation is not optional.
- **"// Placeholder data for now"** — mock data is prohibited.
- **"// Quick fix — improve later"** — temporary fixes are prohibited.
- **Hardcoded credentials, URLs, magic numbers, or magic strings** — configuration lives elsewhere.
- **Data-fetching logic duplicated across pages or services** — must be shared.
- **Validation rules embedded in form components** — must come from shared schema layer.

### UI/UX Red Flags
- **Raw color values, font sizes, or pixel values in components** — must be design tokens.
- **One-off button, input, modal, or dropdown** — must come from the component library.
- **Hardcoded UI text in components** — must be externalized to locale files.
- **Missing loading, empty, or error states** — all four states are mandatory.
- **Missing keyboard navigation or focus management** — accessibility failure.
- **Missing ARIA labels on icon-only buttons** — accessibility failure.

### Shared Code Red Flags
- **Copy-pasted utility functions** — must be extracted to shared utilities.
- **Multiple API client instances** — must use the single shared client.
- **Duplicate type definitions across files** — must be a shared type.
- **Same constant defined in multiple places** — must be a shared constant.

### Security Red Flags
- **Unhandled promises or swallowed exceptions** — errors must be handled explicitly.
- **Missing input validation at API boundaries** — zero-trust requires validation always.
- **New endpoints without authentication or authorization checks** — security failure.
- **Secrets, tokens, or keys in committed code** — immediate rejection.
- **CORS wildcards or missing security headers** — configuration failure.

### Operational Red Flags
- **console.log (or equivalent) in production code paths** — structured logging required.
- **Unbounded queries without pagination** — performance and stability failure.
- **Breaking changes to existing API contracts without versioning** — platform stability failure.
- **No tests for business logic** — testing is mandatory.
- **Tests that require real infrastructure to pass** — architecture failure, not a testing concern.
- **Missing error tracking or observability instrumentation** — production readiness failure.

---

## The Challenge-Back Pattern

When an AI tool proposes an implementation, before accepting it, ask:

### Layer & Architecture Questions
1. **Which Clean Architecture layer does this code belong to?**
2. **What is the dependency direction? Show me the imports.**
3. **Does this entity have any framework attributes it shouldn't?**
4. **Is there any business logic leaking into a page, controller, or database trigger?**
5. **Is all data access going through a repository abstraction?**

### Cross-Cutting Concerns Questions
6. **Where is the audit trail handled? Is any manual audit code present?**
7. **How is logging applied? Are correlation IDs flowing?**
8. **How is authorization enforced? Where does it live?**

### Shared Code Questions
9. **What shared modules does this use or create?**
10. **Is there any duplicated logic, constants, or types introduced?**
11. **Does this reuse existing shared components, or is it creating new ones?**

### Configuration Questions
12. **What configurable values does this introduce? Where are they externalized?**
13. **Are there any hardcoded thresholds, timeouts, or limits?**

### Data & Security Questions
14. **What happens if the input is malicious, malformed, or missing?**
15. **What happens if an external dependency fails?**
16. **Is authentication and authorization enforced on every protected path?**

### UI/UX Questions (for frontend)
17. **Does this use design tokens and shared components exclusively?**
18. **Are loading, empty, error, and success states implemented?**
19. **Is this accessible via keyboard and screen reader?**
20. **Is all user-facing text externalized for i18n?**

### Performance & Scale Questions
21. **How would this scale to 100x current load?**
22. **Are queries paginated and properly indexed?**

### Testability Questions
23. **Can the business logic be tested without real infrastructure?**
24. **What tests accompany this change?**
25. **Do tests use mocks for external dependencies?**

### Operations Questions
26. **What logs and metrics does this emit?**
27. **Would a new engineer understand this without reading other files?**

If any answer is unsatisfying, the implementation is incomplete. Send it back.

---

## Anti-Erosion Discipline

AI sessions tend to drift from standards as conversations lengthen. Convenient shortcuts appear after the 20th message that would have been rejected at the 1st. Defend against this by:

- **Restating the mandate** in every new session, even continuation sessions.
- **Re-anchoring periodically** in long sessions: *"Reminder: we are operating under the Engineering Mandate. Validate the last 3 suggestions against it."*
- **Treating each file independently** — do not let prior shortcuts in a session justify new ones.
- **Ending sessions that drift** — starting fresh with the protocol re-asserted is cheaper than auditing a drifted session.

---

## The Final Gate

Before any AI-generated code is committed:

### Architecture & Code Gate
- [ ] Reviewed against `ENGINEERING_MANDATE.md` by a human.
- [ ] Architecture verified against `ARCHITECTURE_PLAYBOOK.md`.
- [ ] Passes the Definition of Done in the Mandate.
- [ ] Contains no red flags from the list above.
- [ ] Dependency direction correct (inward only).
- [ ] Domain entities pure (no framework attributes).
- [ ] DTOs used at API boundaries (no entities exposed).
- [ ] Data access via repository abstraction.
- [ ] Cross-cutting concerns automated.
- [ ] Uses shared modules; introduces new shared modules where applicable.
- [ ] No duplicate logic, constants, or types.
- [ ] No hardcoded values or magic numbers.

### UI/UX Gate (for frontend changes)
- [ ] Uses design system tokens and shared components exclusively.
- [ ] All four states (loading/empty/error/success) implemented.
- [ ] WCAG 2.2 AA accessibility verified.
- [ ] All UI strings externalized.
- [ ] Responsive across defined breakpoints.

### Security & Data Gate
- [ ] Input validation at API boundaries.
- [ ] Authentication and authorization enforced.
- [ ] No secrets, tokens, or keys in code.
- [ ] Pagination applied to unbounded queries.

### Testability Gate
- [ ] Business logic tested without real infrastructure.
- [ ] Unit tests use mocked dependencies.
- [ ] Integration tests exist for API contracts.

### Operations Gate
- [ ] Logs and metrics instrumented.
- [ ] Observable in production.
- [ ] Documentation updated.

If any item fails, the code does not ship. Iterate or reject.

---

## Evolution Of This Protocol

As AI tools evolve, this protocol evolves with them. New capabilities (longer context, better reasoning, agentic execution) will change how the mandate is enforced but will not change *what* is enforced. The mandate and the playbook are permanent. The protocol adapts.

Review this protocol:

- When adopting a new AI tool.
- When a recurring failure pattern emerges in AI output.
- When the mandate or playbook is updated.

---

*This protocol exists because AI tools are force multipliers. They multiply discipline if discipline is enforced. They multiply shortcuts if shortcuts are tolerated. The choice is made in every prompt.*
