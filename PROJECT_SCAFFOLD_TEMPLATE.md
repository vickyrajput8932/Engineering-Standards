# Universal Project Scaffolding Prompt — Template

> **How to use this template:**
> 1. Fill in **PART 1: PROJECT INPUTS** with your specific project details.
> 2. Review **PART 2: STACK SELECTION** and check the boxes that apply.
> 3. Review **PART 3: SCOPE TOGGLES** to enable/disable mobile, AI, etc.
> 4. Leave **PART 4: GOVERNING MANDATE** unchanged (these are universal rules).
> 5. Leave **PART 5: EXECUTION CONTRACT** unchanged (this tells the agent what to do).
> 6. Paste the **entire filled-in document** into Claude Code, Cursor, Copilot, or any agentic AI inside an empty project folder.

---

# PART 1 — PROJECT INPUTS (FILL THIS IN)

## 1.1 Project Identity

| Field | Value |
|---|---|
| **Project name** | `<PROJECT_NAME>` |
| **One-line tagline** | `<ONE_LINE_TAGLINE>` |
| **Primary user persona** | `<WHO_USES_THIS>` |
| **What it IS** (3 bullets) | • `<...>`<br>• `<...>`<br>• `<...>` |
| **What it is NOT** (3 bullets) | • `<...>`<br>• `<...>`<br>• `<...>` |
| **Primary value proposition** | `<WHY_USERS_CHOOSE_THIS_OVER_ALTERNATIVES>` |

## 1.2 Project Context

| Field | Value |
|---|---|
| **Bundle ID base (mobile)** | `com.<org>.<app>` (lowercase, dots only, no hyphens) |
| **Hosting region preference** | `<REGION>` (e.g., Mumbai/Singapore/Frankfurt/us-east) |
| **Expected user scale (year 1)** | `<NUMBER>` (e.g., 100 / 10k / 1M) |
| **Multi-tenant model** | `<single-tenant>` / `<org-tenant>` / `<workspace-tenant>` |
| **Compliance requirements** | `<NONE>` / `<GDPR>` / `<HIPAA>` / `<SOC2>` / `<other>` |
| **Solo or team?** | `<SOLO>` / `<TEAM_OF_N>` |

## 1.3 Domain Glossary

List 5-10 domain terms specific to this product. The agent will use these in code, docs, and tests.

| Term | Definition |
|---|---|
| `<TERM_1>` | `<DEFINITION>` |
| `<TERM_2>` | `<DEFINITION>` |
| `<TERM_3>` | `<DEFINITION>` |
| `<TERM_4>` | `<DEFINITION>` |
| `<TERM_5>` | `<DEFINITION>` |

## 1.4 Core Capabilities (Top 5)

What does the system actually DO? Not features — capabilities.

1. `<CAPABILITY_1>` — `<one-line description>`
2. `<CAPABILITY_2>` — `<one-line description>`
3. `<CAPABILITY_3>` — `<one-line description>`
4. `<CAPABILITY_4>` — `<one-line description>`
5. `<CAPABILITY_5>` — `<one-line description>`

---

# PART 2 — STACK SELECTION (CHECK ONE PER ROW)

## 2.1 Frontend Framework

- [ ] Next.js 15+ (App Router) + TypeScript — *recommended for B2B SaaS*
- [ ] Remix + TypeScript
- [ ] SvelteKit + TypeScript
- [ ] Other: `<SPECIFY>`

## 2.2 Backend Framework

- [ ] NestJS + TypeScript — *recommended; enforces Clean Architecture via DI*
- [ ] Express + TypeScript (manual layer discipline required)
- [ ] Fastify + TypeScript
- [ ] FastAPI (Python)
- [ ] Other: `<SPECIFY>`

## 2.3 Database + ORM

- [ ] PostgreSQL + Prisma — *recommended*
- [ ] PostgreSQL + Drizzle
- [ ] PostgreSQL + raw SQL
- [ ] MySQL + Prisma
- [ ] Other: `<SPECIFY>`

## 2.4 Database Hosting

- [ ] Supabase (Postgres, DB only — not Auth/Storage)
- [ ] Neon (serverless Postgres)
- [ ] Railway (managed Postgres)
- [ ] AWS RDS / Cloud SQL
- [ ] Local Docker only
- [ ] Other: `<SPECIFY>`

## 2.5 Auth Provider

- [ ] Clerk — *recommended for B2B with multi-tenancy needs*
- [ ] Auth.js / NextAuth
- [ ] Supabase Auth (good for single-tenant or RLS-heavy apps)
- [ ] WorkOS (B2B SSO)
- [ ] Custom JWT + bcrypt
- [ ] Other: `<SPECIFY>`

## 2.6 AI Provider Strategy

- [ ] No AI features — skip AI scaffolding entirely
- [ ] Single provider behind `IAiProvider` port
- [ ] Multi-provider with `IAiOrchestrator` (Phase 1 passthrough today, routing later) — *recommended if AI is core*
- [ ] Default provider: `<openai/anthropic/gemini/local>`

## 2.7 File Storage

- [ ] Local FS (dev) + S3-compatible (prod) behind `IFileStorage` port
- [ ] Cloudflare R2
- [ ] Supabase Storage
- [ ] No file storage needed
- [ ] Other: `<SPECIFY>`

## 2.8 Web Hosting

- [ ] Vercel — *recommended for Next.js*
- [ ] Cloudflare Pages
- [ ] Netlify
- [ ] Self-hosted
- [ ] Other: `<SPECIFY>`

## 2.9 API Hosting

- [ ] Render
- [ ] Railway
- [ ] Fly.io
- [ ] AWS App Runner / Cloud Run
- [ ] Self-hosted
- [ ] Other: `<SPECIFY>`

## 2.10 Package Manager + Monorepo

- [ ] pnpm + Turborepo — *recommended*
- [ ] pnpm + Nx
- [ ] npm workspaces
- [ ] yarn workspaces

---

# PART 3 — SCOPE TOGGLES (ENABLE/DISABLE FEATURES)

## 3.1 Surfaces To Scaffold

- [ ] **Web app** (always recommended)
- [ ] **API service** (always recommended)
- [ ] **Mobile app** (React Native via Expo + Prebuild — recommended over bare CLI)
- [ ] **Admin app** (separate Next.js app for internal users)
- [ ] **Desktop app** (Electron / Tauri) — `<SPECIFY>`

## 3.2 Mobile Specifics (only if mobile enabled)

- [ ] Expo SDK + Prebuild workflow (recommended — exit option to bare CLI in 2-3 days via ports/adapters)
- [ ] Bare React Native CLI
- [ ] Mobile auth: same provider as web, behind `IAuthClient` port
- [ ] Token storage via secure enclave (`expo-secure-store` or `react-native-keychain`) behind `ITokenStorage` port
- [ ] State management: Zustand + TanStack Query
- [ ] Navigation: React Navigation v6 (NOT `expo-router` — preserves bare-CLI exit)

## 3.3 Shared Packages (Required For Multi-Surface)

Always create these regardless of surface count (cheap insurance):

- [x] `domain-types` — branded IDs, discriminated unions, shared types
- [x] `validation` — Zod schemas as single source of truth
- [x] `eslint-config` — shared lint rules
- [x] `tsconfig` — shared TypeScript presets
- [ ] `ai-contracts` — only if AI features enabled
- [ ] `api-client` — only if mobile or external clients consume the API
- [ ] `design-tokens` — only if web + mobile share design language
- [ ] `ui-primitives` — shared logic hooks (NOT components — never share components across web/RN)

## 3.4 Optional Capabilities (Off By Default)

- [ ] Streaming AI responses (SSE end-to-end)
- [ ] WebSocket support
- [ ] Background job queue (BullMQ + Redis)
- [ ] Email sending (Resend / Postmark / SES) behind `IEmailProvider` port
- [ ] Push notifications behind `IPushNotificationService` port
- [ ] Analytics behind `IAnalytics` port (PostHog / Amplitude / no-op default)
- [ ] Crash reporting behind `ICrashReporter` port (Sentry / no-op default)
- [ ] Feature flags behind `IFeatureFlags` port

---

# PART 4 — GOVERNING MANDATE (DO NOT EDIT)

## 4.1 Governing Principle

> **Quality is the fixed constraint. Scope and timeline flex. Quality does not.**
>
> Zero rework — if something cannot be built permanently in one pass, decompose it into smaller permanent slices. Never ship temporary solutions.

## 4.2 Core Laws

1. If you write the same logic twice, the second instance is a bug.
2. If a value can change, it is configuration, not code.
3. If a component can be reused, it lives in the shared layer.
4. Industry best practices apply by default wherever the mandate is silent.
5. Build the architectural seam today; full machinery when justified by a feature.

## 4.3 Non-Negotiable Rules

1. **Clean Architecture:** Domain → Application → Infrastructure → Presentation. Strict dependency rule. CI-enforced.
2. **SOLID, DRY, feature-based folder structure.**
3. **No business logic** in controllers, UI components, or DB triggers.
4. **No framework decorators** on Domain entities. Domain is pure TypeScript.
5. **No ORM/SDK imports** in Application layer — only interfaces (ports).
6. **No entities returned from API** — DTOs only at the boundary.
7. **No hardcoded values, magic numbers, or magic strings** anywhere.
8. **No mock data. No TODOs. No placeholders. No "we'll fix later."**
9. **Input validation at every API boundary.** Versioned APIs from day 1.
10. **Structured logging, error tracking, environment separation** from day 1.
11. **Secrets via env vars only** — never in code, never in repo, never in chat.
12. **Testability is architectural** — business logic must run without DB / network.
13. **Ports and adapters discipline** — every third-party SDK hidden behind a port. Adapters are the only place SDK imports are allowed.
14. **Configuration validation at boot** — app fails to start if env is invalid (with clear error message).

## 4.4 Verification & Quality Gates (Non-Negotiable)

### Rule V1 — "All Green" Requires The Production Build

No claim of "all green" or "verified complete" is valid unless it includes:
1. Architecture check (`pnpm arch:check` or equivalent)
2. Typecheck across all non-mobile packages (`pnpm -r typecheck`)
3. **Production build of every shippable app** (`pnpm --filter <app> build`)
4. (When applicable) Smoke test against the deployment target

**Why:** TypeScript's `tsc --noEmit` and the production bundler check different things. `noEmit: true` skips declaration-portability checks that webpack/Metro catches. Typecheck alone is necessary but insufficient.

### Rule V2 — Smoke-Test Against Production Pipelines

Every new package, app, or major dependency change must be smoke-tested against its actual production pipeline before being declared done:
- Web changes → run the web production build (mirrors deploy pipeline)
- API changes → run the API production build
- Mobile changes → run `npx expo prebuild --clean --platform <android|ios>`
- DB schema changes → run `prisma generate` (catches type drift)

**Why:** Local dev tooling is forgiving. Production tooling is strict. The gap between them is where bugs hide.

### Rule V3 — Agent-Pinned Versions Are Untrusted

AI agents must NOT pin exact dependency versions from memory. Use:
- Caret ranges (`^x.y.z`) for application dependencies
- `latest` or upper-bounded ranges for tooling
- For framework-aligned packages (Expo SDK, NestJS modules), run the framework's official version-aligner post-scaffold (e.g., `npx expo install --check`)

If an agent pins exact versions, treat as a bug regardless of how recent the version looks.

### Rule V4 — Locked Decisions Require A 6-Month Defense Test

Before locking a decision, the decider must answer: *"Will I defend this in 6 months without a strong new constraint?"*
- "Yes, confidently" → lock with date and rationale in `DECISION_LOG.md`
- "I think so" → leave as Open Decision until confidence increases
- "I'm pivoting because of X" → record old → new → reason in `DECISION_LOG.md`

### Rule V5 — `tsc --noEmit` Has Known Blind Spots

Typecheck does NOT validate:
- Declaration-file portability (caught by `noEmit: false` builds)
- Module resolution for production bundlers (caught by webpack/Metro/Turbopack)
- Eager-evaluation env validation (caught by Next.js's "Collecting page data" pass)
- Some `exactOptionalPropertyTypes` violations across module boundaries

Therefore: typecheck is necessary but insufficient. Production build is the second gate. Both required for any "ready to push" claim.

## 4.5 Secret Handling Rule (Non-Negotiable)

Secrets (API keys, tokens, signing secrets, passwords) flow from **origin → final destination only**. They MUST NOT pass through chat interfaces, screenshots, notes apps, or any intermediate storage. Use the password manager as the sole intermediate cache.

**Reject on sight:** any output that contains TODOs, mock data, hardcoded business values, business logic in wrong layer, secrets in code, or skipped setup steps.

---

# PART 5 — EXECUTION CONTRACT (DO NOT EDIT)

## 5.1 Mandatory Monorepo Structure

Generate exactly this tree (omit sections for surfaces not enabled in PART 3):

```
<project-root>/
├── apps/
│   ├── web/                          # Frontend app (per PART 2.1)
│   ├── api/                          # Backend service (per PART 2.2)
│   └── mobile/                       # Mobile app (only if PART 3.1 enables it)
│
├── packages/
│   ├── domain-types/                 # Branded IDs, discriminated unions
│   ├── validation/                   # Zod schemas — single source of truth
│   ├── eslint-config/                # Shared ESLint rules
│   ├── tsconfig/                     # Shared TypeScript presets
│   ├── ai-contracts/                 # Only if AI enabled (PART 2.6)
│   ├── api-client/                   # Only if multiple clients (PART 3.3)
│   └── design-tokens/                # Only if multiple visual surfaces (PART 3.3)
│
├── docs/
│   ├── governance/
│   │   ├── ENGINEERING_MANDATE.md    # Generated from PART 4
│   │   ├── ARCHITECTURE_PLAYBOOK.md  # Generated — Clean Architecture details
│   │   ├── AI_COLLABORATION_PROTOCOL.md  # Generated — agent behavior rules
│   │   ├── PROJECT_CONTEXT.md        # Generated from PART 1 + chosen stack
│   │   └── DECISION_LOG.md           # Generated — empty initial entry
│   └── adr/
│       ├── 0001-clean-architecture.md
│       ├── 0002-mobile-platform.md   # Only if mobile enabled
│       ├── 0003-shared-packages.md   # Only if shared packages > 4
│       ├── 0004-ports-adapters.md
│       └── 0005-ai-orchestration.md  # Only if AI enabled
│
├── .github/
│   └── workflows/
│       ├── ci.yml                    # Lint + typecheck + test + arch:check + production builds
│       └── deploy.yml                # Per chosen hosting providers
│
├── scripts/
│   └── check-architecture.sh         # CI-enforced layer + ports/adapters rules
│
├── .gitignore
├── .gitattributes                    # LF eol; binary rules for images
├── .nvmrc
├── .prettierrc
├── .editorconfig
├── pnpm-workspace.yaml               # Or chosen package manager equivalent
├── turbo.json                        # If Turborepo
├── package.json                      # Root — workspaces + verify script
├── README.md
├── CLAUDE.md                         # AI session memory — references PROJECT_CONTEXT
└── SCAFFOLDING_RUNBOOK.md            # Generated commands + verification steps
```

## 5.2 Clean Architecture — Strict Dependency Rule

| Layer | May import from | MUST NOT import |
|---|---|---|
| `domain/` | Nothing external. Pure TypeScript only. | Any framework, any ORM, any third-party SDK |
| `application/` | `domain/` only | ORM clients, SDK clients, infrastructure files |
| `infrastructure/` | `domain/`, `application/` (interfaces only) | `presentation/` |
| `presentation/` | `application/`, `domain/` (types only) | `infrastructure/*` directly (use DI) |

CI-enforced via `scripts/check-architecture.sh` which uses `grep` and exits non-zero on any forbidden import.

## 5.3 Ports & Adapters Pattern (Universal)

Every third-party SDK MUST be hidden behind a port (interface) defined in the domain layer. Adapters live in dedicated adapter files in the infrastructure layer. Feature/application code imports the port, never the SDK.

**Required ports (always):**
- `IIdentityProvider` — auth provider abstraction
- `ILogger` — structured logging abstraction
- `IClock` — time abstraction (for testability)
- `IIdGenerator` — ID generation abstraction (for testability)

**Conditional ports (per scope):**
- `IAiProvider` + `IAiOrchestrator` — if AI enabled
- `IFileStorage` — if file uploads enabled
- `IEmailProvider` — if email sending enabled
- `IPushNotificationService` — if mobile push enabled
- `IAnalytics`, `ICrashReporter` — if observability enabled
- `ITokenStorage`, `IAuthClient`, `IEnvProvider` — if mobile enabled (mobile-side ports)

**Migration cost guarantee:** swapping any SDK = swap one adapter file + update DI binding. Zero changes in feature code.

## 5.4 AI Layer Requirements (only if AI enabled in PART 2.6)

1. Define `IAiProvider` in `packages/ai-contracts` with: `complete()`, `stream()`, `embed()`.
2. Define `IAiOrchestrator` in domain layer with: `complete(req, ctx)`, `stream(req, ctx)`, `embed(req, ctx)`.
3. Implement default provider adapter (per PART 2.6 default).
4. Implement `PassthroughAiOrchestrator` that forwards to single configured provider.
5. Application use cases consume `IAiOrchestrator` ONLY — never `IAiProvider` directly.
6. Define RAG interfaces (`IVectorStore`, `IEmbeddingService`, `IRetriever`) in `packages/ai-contracts` — interfaces only, no implementations.
7. `AI_ENABLED` feature flag → controller returns 503 with clear message when disabled. Provider client lazy-initializes (no boot-time crash on missing API key).
8. Streaming end-to-end via SSE.

## 5.5 Env / Config Strategy

- `.env.example` per app (the contract — committed)
- `.env` per app (real values — gitignored)
- Boot-time Zod validation; app fails fast on missing/invalid config with named-variable error message
- Conditional optionality via Zod `superRefine` (e.g., `OPENAI_API_KEY` required only when `AI_ENABLED=true`)
- For monorepo Postgres + Prisma: schema declares both `url` and `directUrl` (handles connection pooling vs migrations)

## 5.6 Dev Experience Requirements

- `pnpm dev` (or chosen equivalent) at root → starts web + api in parallel with prefixed colored logs
- Hot reload on all surfaces
- Pino pretty-print in dev, JSON in prod
- Global exception filter returns consistent envelope: `{ error: { code, message, requestId } }`
- Request ID middleware → propagated through logs
- DB migrations: `pnpm db:migrate`, `pnpm db:studio`, `pnpm db:seed`
- `pnpm verify` script: arch:check + typecheck + production builds (the "ready to push" gate)

## 5.7 Mobile-Specific Requirements (only if mobile enabled in PART 3.1)

**Critical: Metro for monorepo (the #1 mobile failure mode).** Configure `metro.config.js`:
- `watchFolders` includes `<repo-root>/packages` and workspace `node_modules`
- `resolver.nodeModulesPaths` resolves hoisted packages
- `resolver.disableHierarchicalLookup = true` (prevents duplicate React instances)

**Strict ports/adapters rule for mobile (CI-enforced):**
- `apps/mobile/src/features/**` and `apps/mobile/src/app/**` MUST NOT import any third-party SDK directly
- Only `apps/mobile/src/shared/adapters/**` may import SDKs
- Feature code imports ports only (`IAuthClient`, `ITokenStorage`, `IEnvProvider`, etc.)
- DI container in `shared/config/container.ts` is the single binding point

**Other mobile requirements:**
- Tokens stored in secure enclave (NOT AsyncStorage)
- Env vars validated by Zod at boot
- React Navigation v6 (NOT `expo-router` if using Expo — preserves bare-CLI exit)
- Do NOT introduce React Native Web (known scaling trap)
- Do NOT share UI components between web and mobile (different render primitives)

## 5.8 Self-Audit Before Final Output

Before printing anything, the agent must verify:

- [ ] No file contains `TODO`, `FIXME`, or placeholder values in code paths
- [ ] No business logic anywhere imports a framework
- [ ] Application layer imports zero infrastructure libraries
- [ ] Every env var is in `.env.example` AND validated by Zod
- [ ] CI workflow runs lint + typecheck + test + arch:check + production builds
- [ ] `pnpm verify` (or equivalent) passes end-to-end
- [ ] README answers: what, why, how to run, how to extend
- [ ] All chosen ports have adapters wired via DI
- [ ] No SDK imports outside adapter files
- [ ] (If mobile) Metro config has all 3 monorepo settings
- [ ] (If AI) AI controller gates on AI_ENABLED before any provider call
- [ ] No half-finished features

If any item fails, fix before responding. Do not ship partial work.

## 5.9 Deliverables (Exact Order)

1. **One-line confirmation** of locked stack from PART 2 + PART 3.

2. **`SCAFFOLDING_RUNBOOK.md`** — every terminal command in order, copy-paste ready, with verification step after each phase. Phases:
   - Phase 0: Prereqs check
   - Phase 1: Init monorepo
   - Phase 2: Scaffold backend app
   - Phase 3: Scaffold frontend app
   - Phase 4: Create shared packages
   - Phase 5: DB schema + initial migration
   - Phase 6: Wire auth provider behind port
   - Phase 7: Wire AI provider behind port (if enabled)
   - Phase 8: Scaffold mobile app + Metro config (if enabled)
   - Phase 9: CI workflows + arch-boundary script
   - Phase 10: Documentation (README, CLAUDE.md, PROJECT_CONTEXT, ADRs, DECISION_LOG)
   - Phase 11: Verification — `pnpm verify` green; smoke test each surface

3. **Generate every file in full.** No placeholders. No TODOs. If a value cannot be decided, use the documented safe default and record in PROJECT_CONTEXT "Open Decisions".

4. **README.md** — project overview (from PART 1), tech stack table (from PART 2), prerequisites, setup steps (link to runbook), run commands, folder structure, link to governance docs.

5. **PROJECT_CONTEXT.md** — full PART 1 content, locked tech stack table, Clean Architecture realization, shared layer inventory, config inventory, domain glossary, current phase = "Foundation Scaffold", open decisions section.

6. **CLAUDE.md** — Mandate reference, project tech stack, "always read PROJECT_CONTEXT.md first" rule, common commands, architecture boundary rules, Verification & Honest Reporting rules.

7. **ADRs** — `0001-clean-architecture.md` mandatory; others per scope.

8. **DECISION_LOG.md** — initial entry with foundation scaffold decisions.

9. **Self-audit checklist** with every item ticked.

10. **Next step recommendation** — what to build first after scaffold passes verification.

## 5.10 Acknowledgment Protocol

Before doing anything, acknowledge:
1. The Mandate's Core Laws and Non-Negotiable Rules (PART 4)
2. The locked stack from PART 2 + PART 3
3. The verification gates V1-V5
4. That you will pause for explicit user input before creating any `.env` file with real values

Then proceed.

## 5.11 Output Format

1. Stack confirmation (one line)
2. Full SCAFFOLDING_RUNBOOK
3. Every file's full contents, grouped by folder
4. Self-audit checklist with every item ticked
5. Dependency graph (which apps consume which packages)
6. Migration playbook excerpt (for the AI orchestrator and mobile ports)
7. Next step

Begin.
