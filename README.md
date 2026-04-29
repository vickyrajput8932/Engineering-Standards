# Engineering Standards

> **Quality is the fixed constraint. Scope and timeline flex. Quality does not.**

This repository is the single source of truth for engineering standards, architectural patterns, and development workflows used across all projects. It defines non-negotiable rules for code quality, architecture, security, testing, and AI-assisted development.

---

## 📋 Table of Contents

- [What Is This Repo?](#what-is-this-repo)
- [Document Map](#document-map)
- [The Core System](#the-core-system)
- [Governing Principle](#governing-principle)
- [Quick Start — New Project](#quick-start--new-project)
- [Quick Start — AI Sessions](#quick-start--ai-sessions)
- [Architecture Overview](#architecture-overview)
- [Key Rules at a Glance](#key-rules-at-a-glance)
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

### Quick Reference

| File | Type | Purpose |
|------|------|---------|
| [SKILL.md](SKILL.md) | 🤖 AI Skill | The Claude skill definition. Activates the full governance and engineering OS in any AI session. Load this first. |
| [ENGINEERING_MANDATE.md](ENGINEERING_MANDATE.md) | 📜 Core Standard | Non-negotiable engineering rules. The law. Referenced in every code review and AI prompt. |
| [ARCHITECTURE_PLAYBOOK.md](ARCHITECTURE_PLAYBOOK.md) | 🏗️ Tactical Guide | How to apply the mandate in practice. Decision trees, patterns, layer guides, code review checklist. |
| [AI_COLLABORATION_PROTOCOL.md](AI_COLLABORATION_PROTOCOL.md) | 🤖 AI Workflow | How to use AI tools (Claude, Copilot, Cursor) without drifting from standards. Contains the session prefix to paste at the start of every AI session. |
| [GOVERNANCE_TEMPLATE.md](GOVERNANCE_TEMPLATE.md) | 🏛️ Governance | Per-project governance pointer. Links a project to the canonical standards and configures AI enforcement. |
| [PROJECT_VISION_TEMPLATE.md](PROJECT_VISION_TEMPLATE.md) | 🎯 Template | Per-project vision template. Captures the why and long-term direction (1 page, fill via interview or manually). |
| [PROJECT_CONTEXT_TEMPLATE.md](PROJECT_CONTEXT_TEMPLATE.md) | 📋 Template | Per-project PRD template. Captures what/who/how/when, KPIs, roadmap, and current state. |
| [CLEAN-ARCHITECTURE-GUIDE_.net.pdf](CLEAN-ARCHITECTURE-GUIDE_.net.pdf) | 📚 Reference | Clean Architecture reference guide for .NET projects. |
| [Project Setup Playbook (Idea to Production).docx](<Project Setup Playbook (Idea to Production).docx>) | 🚀 Playbook | Step-by-step guide from project idea through to production deployment. |
| [Solopreneur_Web_Dev_Playbook.docx](Solopreneur_Web_Dev_Playbook.docx) | 🧑‍💻 Playbook | Web development workflow and decision-making guide for solo developers. |
| [Personal Devs OS.docx](<Personal Devs OS.docx>) | ⚙️ Workflow | Personal developer operating system — habits, processes, and tooling setup. |
| [BA prompt.docx](<BA prompt.docx>) | 💬 Prompts | Business Analyst prompts for requirements gathering and documentation. |
| [Start the app locally.docx](<Start the app locally.docx>) | 🛠️ Guide | Instructions for setting up and running a project locally. |

---

### Detailed Descriptions

#### 🤖 AI Skill & Enforcement Layer

##### [SKILL.md](SKILL.md)
**The Claude skill definition that activates the full engineering OS. Load this first in every AI session.**

Defines the `universal-ai-product-engineering-os` skill for Claude. When loaded, it transforms Claude into a unified Principal Architect + Staff Engineer + Security Architect + Engineering Manager + Product Delivery Leader. Contains the full Governance Discovery Protocol — Claude will search for governance docs in the project repo, conversation, or knowledge base before producing any output, and refuses to proceed without them (or flags fallback mode explicitly). Defines three tiers of authority: Tier 1 (universal standards + per-project VISION and CONTEXT docs), Tier 2 (execution playbooks), and Tier 3 (business context). Includes embedded non-negotiables as a fallback minimum (Clean Architecture dependency table, strict prohibitions, SOLID/DRY, security rules). Also defines the full Execution Engine, Code Review Mode, Rejection Logic, Role Simulation (BA/PO/PM + Architect + Dev + QA + DevOps), and standardised Response Format for every substantive output.

> **Reach for this when:** starting any AI session — paste or reference this skill to ensure Claude enforces governance, validates architecture, and never produces output that violates the standards.

---

#### 📜 Core Standards (Read These First)

##### [ENGINEERING_MANDATE.md](ENGINEERING_MANDATE.md)
**The law. Read this before writing a single line of code.**

The master engineering standard covering 34 sections. It defines what is required and what is prohibited — covering Clean Architecture (4 layers, inward dependency rule), entity and DTO discipline, repository pattern, cross-cutting concerns (audit, soft delete, logging, caching, authorization), frontend architecture, component hierarchy, shared code, DRY enforcement, zero hard-coding policy, UI/UX standards, WCAG 2.2 accessibility, security (OWASP, zero-trust), scalability, performance (Core Web Vitals), testing standards, observability, error handling, feature flags, documentation standards, and version control discipline. Ends with a complete Definition of Done and a list of prohibited anti-patterns.

> **Reach for this when:** you need to know the rule on any engineering topic, or when reviewing code against the standard.

---

##### [ARCHITECTURE_PLAYBOOK.md](ARCHITECTURE_PLAYBOOK.md)
**The tactical companion to the Mandate. Answers "how do I do it correctly?"**

Provides 10 sections of hands-on guidance: a "What Goes Where" decision tree for placing any piece of code in the correct layer, deep-dive layer responsibilities with folder structures and code examples for all four Clean Architecture layers (Domain, Application, Infrastructure, Presentation), cross-cutting concern patterns (automatic audit trail, soft delete, current-user context, structured logging, authorization, caching, error handling), data access patterns (Repository and Unit of Work with real code), an illustrated catalogue of 8 common mistakes with wrong vs. right code, a consequence analysis showing what happens weeks/months/years after architecture violations, a 7-step new feature development workflow, a complete code review checklist, and a FAQ that pre-empts the most common rationalisations for taking shortcuts. Ends with technology-specific notes for Node.js/TypeScript, Python, Java/.NET, and Go.

> **Reach for this when:** you are designing a new feature, implementing a pattern, doing a code review, or wondering where a specific piece of code belongs.

---

##### [AI_COLLABORATION_PROTOCOL.md](AI_COLLABORATION_PROTOCOL.md)
**How to use AI tools without drifting from engineering standards.**

Explains why AI tools default to convenient shortcuts without enforcement, and provides the complete enforcement mechanism. Contains the ready-to-paste session prefix (a ~60-rule summary of the Mandate) for Claude, Copilot, Cursor, or any other AI tool. Covers how to configure in-editor tools via `.cursorrules` or `.github/copilot-instructions.md`, how to use with agentic/autonomous AI, a template for the per-project PROJECT_CONTEXT.md supplement, a comprehensive "Red Flags in AI Output" reference (architecture, data/logic, UI/UX, shared code, security, and operational failures), a challenge-back question bank to interrogate AI-generated code, anti-erosion discipline for long sessions, and a final gate checklist before committing any AI-generated code.

> **Reach for this when:** starting any AI-assisted coding session, setting up a new AI tool, or auditing AI-generated output before committing.

---

##### [GOVERNANCE_TEMPLATE.md](GOVERNANCE_TEMPLATE.md)
**The per-project governance pointer. Drop this into every project repo.**

A structured template that links an individual project to the canonical engineering standards in this repository. It declares which standards apply (ENGINEERING_MANDATE.md, ARCHITECTURE_PLAYBOOK.md, AI_COLLABORATION_PROTOCOL.md), their version and status, the source-of-truth URL, and the local mirror path. Includes a project context table for capturing project name, mode (MVP/Production/Hybrid), tech stack, architecture style, primary owner, and AI assistant. Contains a project-specific overrides section — default is no overrides — with the explicit rule that convenience is not a valid justification. Also defines the AI Enforcement Protocol: AI assistants that encounter this file must read it first, load the canonical docs, validate every output against the applicable standards, cite specific rules when flagging violations, and refuse to proceed if standards are inaccessible. Enables the Governance Discovery Protocol so AI tools can self-configure to the correct standards automatically.

> **Reach for this when:** setting up a new project and needing to wire it to the engineering standards, or configuring an AI assistant to enforce the correct rules for a specific codebase.

---

#### 🎯 Per-Project Templates

##### [PROJECT_VISION_TEMPLATE.md](PROJECT_VISION_TEMPLATE.md)
**The why and the long-term direction of a project. One page. Fill once, review yearly.**

A structured one-page template for capturing the long-term vision of an individual project. Contains 7 sections: a single North Star sentence, the Problem Worth Solving (who feels it, how often, why current solutions fail), a Target User / Market table (primary user, secondary users, geography, segment), Long-Term Outcome (what the world looks like in 3–5 years if the project succeeds), Core Principles / Non-Negotiables (3–5 things that will not be compromised under any pressure), What Success Looks Like (the journalist headline in 3 years), and What This Project Is NOT (deliberate scope exclusions to guard against scope creep). Can be filled via Claude interview mode (attach and say "interview me") or manually. Intentionally kept to 1 page — if it's longer, it's planning, not vision.

> **Reach for this when:** starting a new project and needing to anchor all decisions to a clear, shared purpose — or when the team has lost alignment on what the project is fundamentally trying to achieve.

---

##### [PROJECT_CONTEXT_TEMPLATE.md](PROJECT_CONTEXT_TEMPLATE.md)
**The operational PRD. Captures what, who, how, when, and where the project stands today.**

A comprehensive 15-section working PRD template that serves as the living operational document for any project. Covers: Identity (name, owner, repo, status, mode), Purpose (one-line pitch, problem solved, core value proposition), Users & Use Cases (primary persona, secondary personas, top 5 use cases), Scope (in/out/future), Tech Stack (language, frameworks, DB, ORM, auth, cache, infra, CI/CD, monitoring, AI stack), Architecture (style, folder structure, key patterns, API style, deviations from Architecture Playbook), Domain Glossary (project-specific terms for AI tooling to use), External Integrations, Constraints (SLA, security, compliance, budget, timeline, team size), KPIs & Success Metrics (North Star metric, leading indicators, health metrics), Roadmap (Now/Next/Later + milestones), Risk Register, Team & Workflow, Current State (what's built, what's broken, in-progress, blockers, tech debt), and References. Can be filled via Claude interview mode or manually. Review quarterly or on material change.

> **Reach for this when:** kicking off a new project and needing a complete project definition that AI tools (and teammates) can use to understand the full context without asking repeated questions — or when the project is drifting and needs a documented anchor.

---

#### 🏗️ Project & Development Playbooks

##### [Project Setup Playbook (Idea to Production).docx](<Project Setup Playbook (Idea to Production).docx>)
**End-to-end guide for taking a project from concept to live production.**

Covers the full project lifecycle: validating the idea, defining scope, choosing the tech stack, scaffolding the codebase with the correct Clean Architecture folder structure, setting up CI/CD pipelines, configuring environments (dev/staging/prod), handling secrets and config, establishing database migrations, wiring up observability (logging, error tracking, metrics), and deploying for the first time. Acts as a launchpad checklist to ensure no critical setup step is skipped.

> **Reach for this when:** starting a brand-new project and needing a reliable, repeatable setup process from scratch.

---

##### [Solopreneur_Web_Dev_Playbook.docx](Solopreneur_Web_Dev_Playbook.docx)
**Web development workflow and decision-making guide optimised for solo builders.**

Covers how to make fast, high-quality decisions when working alone — including how to prioritise features, when to build vs. buy, how to manage scope without a team, how to structure work sessions, and how to maintain engineering standards without the natural checks provided by a team environment. Applies the Engineering Mandate in a solo context where speed matters but quality cannot be sacrificed.

> **Reach for this when:** working as a solo developer or indie builder who needs a structured, efficient approach to shipping production-grade software without a team.

---

##### [Personal Devs OS.docx](<Personal Devs OS.docx>)
**Your personal developer operating system — habits, routines, and tools.**

Defines the day-to-day operating habits, workflow routines, tooling setup, and decision-making frameworks that keep a developer productive, consistent, and mentally clear. Covers how to structure a work day, how to manage context switching, how to handle planning and task tracking, how to maintain a learning practice, and how to avoid the common traps that erode developer effectiveness over time.

> **Reach for this when:** setting up your personal development environment and workflow, or when productivity and consistency feel off and you need to reset to a structured system.

---

##### [BA prompt.docx](<BA prompt.docx>)
**Business Analyst prompt templates for structured requirements gathering.**

Contains ready-to-use prompt templates for engaging with AI (or human stakeholders) to extract, clarify, and document business requirements. Covers how to elicit functional requirements, define acceptance criteria, identify edge cases, map user journeys, and produce unambiguous feature specifications that development can act on without revisiting every assumption. Designed to bridge the gap between a business idea and a developer-ready specification.

> **Reach for this when:** starting a new feature or project and needing to turn a vague idea into a clear, complete specification before writing any code.

---

##### [Start the app locally.docx](<Start the app locally.docx>)
**Step-by-step instructions for setting up and running any project locally.**

Covers environment prerequisites, cloning the repo, installing dependencies, configuring environment variables from the `.env.example`, running database migrations, seeding initial data, starting the development server, and verifying the app is running correctly. Also includes common troubleshooting steps for the most frequent local setup issues.

> **Reach for this when:** onboarding onto a project for the first time, setting up on a new machine, or helping a new developer get the app running locally within minutes.

---

#### 📚 Reference Material

##### [CLEAN-ARCHITECTURE-GUIDE_.net.pdf](CLEAN-ARCHITECTURE-GUIDE_.net.pdf)
**A concrete Clean Architecture implementation reference for .NET projects.**

A PDF reference guide illustrating how the Clean Architecture principles defined in the Engineering Mandate and Architecture Playbook are realised specifically in .NET (ASP.NET Core). Shows the project structure, class organisation, dependency injection wiring, EF Core configuration patterns, and how each of the four layers maps to .NET project conventions. Useful for bridging the language-agnostic playbook to .NET-specific implementation details.

> **Reach for this when:** building a .NET project and needing to translate the architecture principles into concrete .NET folder structures, project references, and framework conventions.

---

## The Core System

All documents work together as a layered system. The order matters:

```
SKILL.md                      ← The AI Activator (loads all governance into Claude)
        ↓
ENGINEERING_MANDATE.md        ← The Law (what is required, what is prohibited)
        ↓
ARCHITECTURE_PLAYBOOK.md      ← The Patterns (how to apply the law in practice)
        ↓
AI_COLLABORATION_PROTOCOL.md  ← The Enforcement (how AI tools follow both)
        ↓
GOVERNANCE_TEMPLATE.md        ← The Wiring (how each project activates the standards)
        ↓
PROJECT_VISION_TEMPLATE.md    ← The Why (long-term direction, per project)
        ↓
PROJECT_CONTEXT_TEMPLATE.md   ← The What/How/When (operational PRD, per project)
```

**When to use each:**
- Starting a new feature? → Read `ENGINEERING_MANDATE.md` Sections 3–4, then use the decision tree in `ARCHITECTURE_PLAYBOOK.md` Section 1.
- Reviewing code? → Use the checklist in `ARCHITECTURE_PLAYBOOK.md` Section 8.
- Starting an AI session? → Load `SKILL.md`, then paste the session prefix from `AI_COLLABORATION_PROTOCOL.md`.
- Unsure where code belongs? → Walk the "What Goes Where" tree in `ARCHITECTURE_PLAYBOOK.md` Section 1.
- Tempted to take a shortcut? → Read `ARCHITECTURE_PLAYBOOK.md` Section 6 (Consequences).
- Setting up a new project? → Copy `GOVERNANCE_TEMPLATE.md`, `PROJECT_VISION_TEMPLATE.md`, and `PROJECT_CONTEXT_TEMPLATE.md` into your project repo and fill them in.

---

## Governing Principle

> **Quality is the fixed constraint. Scope and timeline flex. Quality does not.**

**Zero Rework Principle:** We do not ship temporary solutions. If a feature is too large to build correctly in one pass, it is decomposed into smaller permanent slices — each shipped production-grade, each extending the prior without rewriting it. This principle governs every rule in this repository.

---

## Quick Start — New Project

1. Read `ENGINEERING_MANDATE.md` fully before writing any code.
2. Reference `ARCHITECTURE_PLAYBOOK.md` during design and implementation.
3. Copy `GOVERNANCE_TEMPLATE.md` into your project repo and fill in the project context fields.
4. Copy `PROJECT_VISION_TEMPLATE.md` into your project repo — fill it in via Claude interview mode or manually (say "interview me to fill this out" with the template attached).
5. Copy `PROJECT_CONTEXT_TEMPLATE.md` into your project repo — fill it in via Claude interview mode or manually.
6. Follow the Project Setup Playbook for project scaffolding.
7. Use the Start the app locally guide to configure local dev environments.

---

## Quick Start — AI Sessions

Load `SKILL.md` into your Claude Project or paste it at the start of the session. Then provide:

- `ENGINEERING_MANDATE.md`
- `ARCHITECTURE_PLAYBOOK.md`
- Your project's `GOVERNANCE_TEMPLATE.md` (Claude will self-configure from this)
- Your project's `PROJECT_VISION_TEMPLATE.md`
- Your project's `PROJECT_CONTEXT_TEMPLATE.md`

> Never rely on the AI to infer the standards. Always provide them explicitly. The SKILL will refuse to produce architecture or code output until it can confirm governance docs are accessible.

---

## Architecture Overview

Every project follows Clean Architecture with four strict layers and a single dependency rule:

```
┌─────────────────────────────────────────┐
│           Presentation Layer            │
│   HTTP, controllers, routes, DI wiring  │
├─────────────────────────────────────────┤
│           Application Layer             │
│  Use cases, services, DTOs, validators  │
├─────────────────────────────────────────┤
│             Domain Layer                │
│  Entities, interfaces, business rules   │
├─────────────────────────────────────────┤
│          Infrastructure Layer           │
│  DB, external APIs, repos, cross-cutting│
└─────────────────────────────────────────┘
       Dependencies flow INWARD only →
```

**The Dependency Rule (Non-Negotiable):** Inner layers never import from outer layers. Domain has zero external dependencies. Violations are caught at build time.

For the full decision tree, layer-by-layer responsibilities, and real code examples — see [ARCHITECTURE_PLAYBOOK.md](ARCHITECTURE_PLAYBOOK.md).

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
- All shared components, hooks, utilities, constants live in the `shared` layer
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

## Definition of Done

Work is **Done** when all of the following are true. Work that does not meet this bar is **In Progress**.

### Architecture & Code
- [ ] Clean Architecture layer boundaries respected; dependency direction correct
- [ ] Domain entities pure (no framework attributes, no infrastructure imports)
- [ ] DTOs at API boundaries; data access via repository abstraction
- [ ] Cross-cutting concerns automated; controllers thin; no duplicate logic
- [ ] All configuration externalized; no magic numbers; no mock data

### UI/UX
- [ ] Design system tokens and shared components used exclusively
- [ ] Loading, empty, error, and success states implemented for every data-dependent view
- [ ] WCAG 2.2 AA verified; all UI strings externalized; responsive across breakpoints

### Security
- [ ] Auth and authz enforced; input validated and sanitized
- [ ] No secrets in code; rate limiting on public endpoints

### Testing & Quality
- [ ] Business logic testable without infrastructure; automated test coverage for critical logic
- [ ] CI/CD passes cleanly; logging and error tracking instrumented

### Documentation
- [ ] API documentation updated; README updated if setup changes; ADR created for significant decisions

---

## Anti-Patterns (Strictly Prohibited)

The following are **rejected on sight** in code review:

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

*This mandate is the permanent standard. It governs every line of code, every architectural decision, every AI-assisted implementation. Quality is the constraint. Everything else flexes around it.*
