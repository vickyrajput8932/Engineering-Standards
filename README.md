# Engineering Standards

> **Quality is the fixed constraint. Scope and timeline flex. Quality does not.**

This repository is the single source of truth for engineering standards, architectural patterns, and development workflows used across all projects. It defines non-negotiable rules for code quality, architecture, security, testing, and AI-assisted development.

---

## 📋 Table of Contents

- [What Is This Repo?](#what-is-this-repo)
- [Document Map](#document-map)
- [The Three-Document Core System](#the-three-document-core-system)
- [Governing Principle](#governing-principle)
- [Quick Start — New Project](#quick-start--new-project)
- [Quick Start — AI Sessions](#quick-start--ai-sessions)
- [Architecture Overview](#architecture-overview)
- [Key Rules at a Glance](#key-rules-at-a-glance)
- [Playbooks & Guides](#playbooks--guides)
- [Definition of Done](#definition-of-done)
- [Anti-Patterns (Strictly Prohibited)](#anti-patterns-strictly-prohibited)

---

## What Is This Repo?

This repository contains the complete engineering operating system for solo and team-based software development — from first commit to production. It covers:

- **Architecture** — Clean Architecture rules, dependency flow, layer responsibilities
- **Code Quality** — DRY, SOLID, naming, structure, configuration discipline
- **Security** — OWASP compliance, zero-trust, secrets management
- **Frontend** — Component hierarchy, design system, accessibility, performance
- **Testing** — Test pyramid, testability as an architectural requirement
- **AI Collaboration** — How to use AI tools without accumulating technical debt
- **Operations** — Observability, CI/CD, production readiness

Every document is technology-agnostic and applies to Node.js, Python, Java, .NET, Go, or any modern stack.

---

## Document Map

| File | Type | Purpose |
|------|------|---------|
| [ENGINEERING_MANDATE.md](./ENGINEERING_MANDATE.md) | 📜 Core Standard | Non-negotiable rules. The law. Referenced in every code review and AI prompt. |
| [ARCHITECTURE_PLAYBOOK.md](./ARCHITECTURE_PLAYBOOK.md) | 🏗️ Tactical Guide | How to apply the mandate in practice. Decision trees, patterns, layer guides, code review checklist. |
| [AI_COLLABORATION_PROTOCOL.md](./AI_COLLABORATION_PROTOCOL.md) | 🤖 AI Workflow | How to use AI tools (Claude, Copilot, Cursor) without drifting from standards. Contains the session prefix to paste at the start of every AI session. |
| [CLEAN-ARCHITECTURE-GUIDE_.net.pdf](./CLEAN-ARCHITECTURE-GUIDE_.net.pdf) | 📚 Reference | Clean Architecture reference guide for .NET projects. |
| [Project Setup Playbook (Idea to Production).docx](./Project%20Setup%20Playbook%20%28Idea%20to%20Production%29.docx) | 🚀 Playbook | Step-by-step guide from project idea through to production deployment. |
| [Solopreneur_Web_Dev_Playbook.docx](./Solopreneur_Web_Dev_Playbook.docx) | 🧑‍💻 Playbook | Web development workflow and decision-making guide for solo developers. |
| [Personal Devs OS.docx](./Personal%20Devs%20OS.docx) | ⚙️ Workflow | Personal developer operating system — habits, processes, and tooling setup. |
| [BA prompt.docx](./BA%20prompt.docx) | 💬 Prompts | Business Analyst prompts for requirements gathering and documentation. |
| [Start the app locally.docx](./Start%20the%20app%20locally.docx) | 🛠️ Guide | Instructions for setting up and running a project locally. |

---

## The Three-Document Core System

The engineering system is built around three interlocking documents that must be used together:

```
ENGINEERING_MANDATE.md        ← The Law       (what is required, what is prohibited)
        ↓
ARCHITECTURE_PLAYBOOK.md      ← The Patterns  (how to apply the law in practice)
        ↓
AI_COLLABORATION_PROTOCOL.md  ← The Enforcement (how AI tools follow both)
```

**When to use each:**

- **Starting a new feature?** → Read ENGINEERING_MANDATE.md Sections 3–4, then use the decision tree in ARCHITECTURE_PLAYBOOK.md Section 1.
- **Reviewing code?** → Use the checklist in ARCHITECTURE_PLAYBOOK.md Section 8.
- **Starting an AI session?** → Paste the session prefix from AI_COLLABORATION_PROTOCOL.md.
- **Unsure where code belongs?** → Walk the "What Goes Where" tree in ARCHITECTURE_PLAYBOOK.md Section 1.
- **Tempted to take a shortcut?** → Read ARCHITECTURE_PLAYBOOK.md Section 6 (Consequences).

---

## Governing Principle

**Quality is the fixed constraint. Scope and timeline flex. Quality does not.**

**Zero Rework Principle:** We do not ship temporary solutions. If a feature is too large to build correctly in one pass, it is decomposed into smaller permanent slices — each shipped production-grade, each extending the prior without rewriting it.

This principle governs every rule in this repository.

---

## Quick Start — New Project

1. **Read** [ENGINEERING_MANDATE.md](./ENGINEERING_MANDATE.md) fully before writing any code.
2. **Reference** [ARCHITECTURE_PLAYBOOK.md](./ARCHITECTURE_PLAYBOOK.md) during design and implementation.
3. **Create** a `PROJECT_CONTEXT.md` in your project repo using the template in [AI_COLLABORATION_PROTOCOL.md](./AI_COLLABORATION_PROTOCOL.md).
4. **Follow** the [Project Setup Playbook](./Project%20Setup%20Playbook%20%28Idea%20to%20Production%29.docx) for project scaffolding.
5. **Use** the [Start the app locally](./Start%20the%20app%20locally.docx) guide to configure local dev environments.

---

## Quick Start — AI Sessions

Paste the session prefix from [AI_COLLABORATION_PROTOCOL.md](./AI_COLLABORATION_PROTOCOL.md) at the start of **every** AI session (Claude, Copilot, Cursor, or any other tool). Then attach or reference:

- `ENGINEERING_MANDATE.md`
- `ARCHITECTURE_PLAYBOOK.md`
- Your project's `PROJECT_CONTEXT.md`

> **Never rely on the AI to infer the standards.** Always provide them explicitly.

---

## Architecture Overview

Every project follows **Clean Architecture** with four strict layers and a single dependency rule:

```
┌─────────────────────────────────────────┐
│           Presentation Layer            │  HTTP, controllers, routes, DI wiring
├─────────────────────────────────────────┤
│           Application Layer             │  Use cases, services, DTOs, validators
├─────────────────────────────────────────┤
│             Domain Layer                │  Entities, interfaces, business rules
├─────────────────────────────────────────┤
│          Infrastructure Layer           │  DB, external APIs, repos, cross-cutting
└─────────────────────────────────────────┘
       Dependencies flow INWARD only →
```

**The Dependency Rule (Non-Negotiable):** Inner layers never import from outer layers. Domain has zero external dependencies. Violations are caught at build time.

For the full decision tree, layer-by-layer responsibilities, and real code examples — see [ARCHITECTURE_PLAYBOOK.md](./ARCHITECTURE_PLAYBOOK.md).

---

## Key Rules at a Glance

### Architecture
- Four layers with inward-only dependencies — always, no exceptions
- Domain entities are pure: no ORM attributes, no framework imports, no serialization attributes
- DTOs live in Application; entities never cross the API boundary
- All data access flows through repository interfaces defined in Domain
- Transactions use Unit of Work; controllers are thin (< 50 lines, no business logic)

### Code Quality
- Write the same logic twice = the second instance is a bug
- Every configurable value (URLs, timeouts, thresholds, UI text) is externalized — no magic numbers or strings
- No dead code, no commented-out blocks, no TODOs without tracked tickets
- No `console.log` in production — use structured logging

### Cross-Cutting Concerns (Automated, Never Manual)
- Audit trail (`createdAt`, `createdBy`, etc.) applied via marker interface at the data access boundary
- Soft delete applied via marker interface and save interceptor
- Logging and correlation IDs initialized once at bootstrap, injected everywhere
- Authorization at Presentation (role gates) and Application (business-rule checks) — never in data access

### Frontend
- Pages compose — they do not contain business logic, data fetching, or validation
- All shared components, hooks, utilities, constants live in the shared layer
- Design tokens are the single source of truth for all visual values (no raw colors or px values)
- WCAG 2.2 Level AA is non-negotiable
- All UI text externalized for i18n from day 1

### Security (Zero Tolerance)
- OWASP Top 10 compliance is the minimum
- No secrets in code — vault/env vars only
- Input validation and sanitization on every user-supplied value
- Rate limiting on all public endpoints, especially auth
- Security review required before auth, payment, or PII features ship

### Testing
- Untested code does not ship
- Business logic must be testable without any infrastructure (no real DB, no real network)
- If a module is hard to test, it is architecturally wrong — fix the architecture, don't skip the test

---

## Playbooks & Guides

| Document | When to Use |
|----------|-------------|
| [Project Setup Playbook](./Project%20Setup%20Playbook%20%28Idea%20to%20Production%29.docx) | Starting a new project from scratch |
| [Solopreneur Web Dev Playbook](./Solopreneur_Web_Dev_Playbook.docx) | Solo web development workflow and prioritisation |
| [Personal Devs OS](./Personal%20Devs%20OS.docx) | Setting up your personal development habits and tooling |
| [BA Prompt](./BA%20prompt.docx) | Structuring requirements and business analysis conversations |
| [Start the App Locally](./Start%20the%20app%20locally.docx) | Running any project in a local environment |
| [Clean Architecture Guide (.NET)](./CLEAN-ARCHITECTURE-GUIDE_.net.pdf) | .NET-specific Clean Architecture reference |

---

## Definition of Done

Work is **Done** when all of the following are true. Work that does not meet this bar is **In Progress**.

**Architecture & Code**
- Clean Architecture layer boundaries respected; dependency direction correct
- Domain entities pure (no framework attributes, no infrastructure imports)
- DTOs at API boundaries; data access via repository abstraction
- Cross-cutting concerns automated; controllers thin; no duplicate logic
- All configuration externalized; no magic numbers; no mock data

**UI/UX**
- Design system tokens and shared components used exclusively
- Loading, empty, error, and success states implemented for every data-dependent view
- WCAG 2.2 AA verified; all UI strings externalized; responsive across breakpoints

**Security**
- Auth and authz enforced; input validated and sanitized
- No secrets in code; rate limiting on public endpoints

**Testing & Quality**
- Business logic testable without infrastructure; automated test coverage for critical logic
- CI/CD passes cleanly; logging and error tracking instrumented

**Documentation**
- API documentation updated; README updated if setup changes; ADR created for significant decisions

---

## Anti-Patterns (Strictly Prohibited)

The following are rejected on sight in code review:

- Business logic in controllers, route handlers, or UI components
- ORM/framework attributes on Domain entities
- Entities returned from or accepted by API endpoints
- Direct ORM/SDK imports in Application layer
- Manual audit field setting in business services
- Skipping layers "for simple CRUD"
- Temporary fixes framed as "we'll fix later"
- Mock data in UI or production code paths
- Hardcoded values, magic numbers, magic strings in business logic
- One-off styling outside the design system
- Secrets committed to version control
- Unbounded queries without pagination
- `console.log` in production code
- TODOs/FIXMEs without tracked tickets
- Skipped tests without documented justification

---

## Review Cadence

This repository is reviewed when a new category of risk emerges, when a recurring violation indicates a rule needs sharpening, or at minimum every six months. Updates require documented justification and explicit approval.

---

> *This mandate is the permanent standard. It governs every line of code, every architectural decision, every AI-assisted implementation. Quality is the constraint. Everything else flexes around it.*
