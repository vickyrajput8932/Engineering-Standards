# Architecture Playbook

> **The tactical companion to `ENGINEERING_MANDATE.md`.**
> The Mandate answers *"what is required?"* This Playbook answers *"how do I do it correctly?"*
> Technology-agnostic. Applies to Node.js, Python, Java, .NET, Go, or any modern stack.

---

## How To Use This Document

- **During design:** Use the decision trees in Section 1 and the layer guides in Section 2 to place code correctly from the start.
- **During implementation:** Use Section 3 for cross-cutting concern patterns and Section 4 for data access patterns.
- **During code review:** Use Section 8 as the review checklist.
- **During AI sessions:** Attach this document alongside the Mandate so AI tools can apply patterns correctly.
- **When tempted to take a shortcut:** Read Section 6 (Consequences) and Section 9 (FAQ). The temptation passes.

---

## Table Of Contents

1. [The "What Goes Where" Decision Tree](#1-the-what-goes-where-decision-tree)
2. [Layer Responsibilities — Deep Dive](#2-layer-responsibilities--deep-dive)
3. [Cross-Cutting Concerns Patterns](#3-cross-cutting-concerns-patterns)
4. [Data Access Patterns](#4-data-access-patterns)
5. [Common Mistakes & How To Avoid Them](#5-common-mistakes--how-to-avoid-them)
6. [Consequences Of Violating The Architecture](#6-consequences-of-violating-the-architecture)
7. [New Feature Development Workflow](#7-new-feature-development-workflow)
8. [Code Review Checklist](#8-code-review-checklist)
9. [FAQ — Anticipating Rationalizations](#9-faq--anticipating-rationalizations)
10. [Reference: Technology-Specific Notes](#10-reference-technology-specific-notes)

---

## 1. The "What Goes Where" Decision Tree

When you have a piece of code and don't know where it belongs, walk through this tree top to bottom. Stop at the first YES.

```
START
│
├─ Is it a business concept? (Customer, Order, Lobby, Transaction, Worker)
│     ├─ YES → Domain/entities/
│
├─ Is it a contract for data access or external services? (IUserRepository, IPaymentGateway)
│     ├─ YES → Domain/interfaces/
│
├─ Is it a business rule that doesn't depend on infrastructure? (OrderStatus enum, DomainException)
│     ├─ YES → Domain/
│
├─ Is it data coming FROM or going TO the API? (CreateOrderRequest, OrderDto)
│     ├─ YES → Application/dtos/
│
├─ Does it orchestrate multiple entities or coordinate a business workflow?
│     ├─ YES → Application/services/
│
├─ Is it input validation for an API? (CreateOrderValidator)
│     ├─ YES → Application/validators/
│
├─ Is it a mapping between DTOs and entities?
│     ├─ YES → Application/mappings/
│
├─ Does it implement a Domain interface? (UserRepository implements IUserRepository)
│     ├─ YES → Infrastructure/
│
├─ Does it talk to a database, external API, file system, or queue?
│     ├─ YES → Infrastructure/
│
├─ Does it handle HTTP requests, routes, or status codes?
│     ├─ YES → Presentation/controllers/ or Presentation/routes/
│
├─ Is it application bootstrap, DI configuration, or global middleware?
│     ├─ YES → Presentation/ (entry point)
│
└─ None of the above?
      └─ Ask: is this a shared cross-cutting utility?
            ├─ YES → shared/ (cross-layer utility)
            └─ NO  → You probably haven't identified what it really is. Stop and reconsider.
```

### Example Applications

**"Where does the password hashing function go?"**
- It's not a business concept → not Domain/entities
- It's not a contract → not Domain/interfaces
- It does I/O? No, it's a pure function
- It talks to a database? No
- It's a shared utility? Yes → `shared/utilities/` with an interface in Domain if Application services need to inject it

**"Where does the code that sends a welcome email go?"**
- It talks to an external service (email provider) → Infrastructure
- Business service calls it via an `IEmailService` interface defined in Domain

**"Where does the calculation of order total + tax go?"**
- It's a business rule → Domain (as a method on the Order entity or a Domain service)

**"Where does the code that queries all pending orders go?"**
- It talks to the database → Infrastructure (inside `OrderRepository`)
- Service calls `orderRepository.getPending()`, never writes the query itself

---

## 2. Layer Responsibilities — Deep Dive

### 2.1 Domain Layer

**Purpose:** The heart of the application. Contains business concepts and business rules. Independent of everything.

**What belongs here:**
- Business entities (classes/records representing business concepts)
- Value objects (immutable objects with business meaning — Money, Address, DateRange)
- Domain interfaces (`IUserRepository`, `IUnitOfWork`, `IEmailService`)
- Domain exceptions (`OrderNotFoundException`, `InvalidOrderStateException`)
- Enums and constants expressing business concepts
- Pure business logic methods on entities

**What does NOT belong here:**
- Database access code (ORM imports, DbContext, query builders)
- API code (controllers, routes, HTTP concerns)
- External service integrations
- Framework dependencies (anything beyond standard language libraries)
- DTOs (those belong in Application)
- Validation attributes tied to specific frameworks
- Framework-specific serialization annotations

**Allowed dependencies:**
- Standard language libraries only
- Possibly: domain-specific libraries (e.g., a money library) if they're framework-independent
- **NO reference to any other project/module in this codebase**

**Example folder structure:**
```
domain/
├── entities/
│   ├── user.ts
│   ├── order.ts
│   └── order-item.ts
├── value-objects/
│   ├── money.ts
│   └── email-address.ts
├── interfaces/
│   ├── i-auditable.ts
│   ├── i-soft-deletable.ts
│   ├── i-user-repository.ts
│   └── i-unit-of-work.ts
├── exceptions/
│   ├── user-not-found-exception.ts
│   └── invalid-order-state-exception.ts
└── enums/
    └── order-status.ts
```

**Why this layer exists:**
- Contains your competitive advantage — unique business logic
- Can be moved to a different framework/technology without changes
- Can be tested without any infrastructure
- Is the stable core; everything else revolves around it

**What happens if you violate this layer:**
- Importing an ORM library → business logic tied to that ORM; swap requires full rewrite
- Adding table/column attributes to entities → entity knows about database; breaks testability
- Adding a logger dependency → entity depends on a framework
- Adding HTTP-framework validation attributes → entity knows about the web layer

### 2.2 Application Layer

**Purpose:** Orchestrates domain entities to fulfill business use cases. Exposes the operations the application supports.

**What belongs here:**
- Service interfaces (`IUserService`, `IOrderService`) — the contracts your application exposes
- Service implementations (`UserService`, `OrderService`) — orchestrate repositories and domain entities
- DTOs — inbound request shapes and outbound response shapes
- Input validators — validate DTOs against business rules
- Mapping profiles — convert between DTOs and entities
- Abstractions for cross-cutting services consumed by business logic (`ICurrentUserService`, `ILogger`)

**What does NOT belong here:**
- Database code (no ORM imports, no query builders, no SQL)
- HTTP/API concerns (no route decorators, no HTTP status codes, no request/response objects)
- Framework-specific data access
- Direct instantiation of infrastructure implementations

**Allowed dependencies:**
- Domain (always)
- Validation library (Zod, Yup, Joi, FluentValidation, Pydantic, etc.)
- Mapping library (AutoMapper, class-transformer, etc.)
- Logging abstraction (not a specific logger implementation)
- **NO direct ORM imports. NO HTTP framework imports.**

**Example folder structure:**
```
application/
├── dtos/
│   ├── create-user-request.ts
│   ├── update-user-request.ts
│   ├── user-dto.ts
│   └── user-list-item-dto.ts
├── interfaces/
│   ├── i-user-service.ts
│   ├── i-current-user-service.ts
│   └── i-email-service.ts
├── services/
│   └── user-service.ts
├── validators/
│   ├── create-user-validator.ts
│   └── update-user-validator.ts
└── mappings/
    └── user-mapping-profile.ts
```

**Why this layer exists:**
- Orchestrates domain entities for business use cases
- Provides stable interfaces to the API layer
- Contains workflow logic that spans multiple entities
- Maps between the external world (DTOs) and internal world (entities)

**What happens if you violate this layer:**
- Writing database queries here → business logic tied to data access technology
- Using an ORM context directly → cannot test without a database
- Returning entities from services → internal structure exposed to API; breaks encapsulation
- Manual audit/logging code → duplicated everywhere; inconsistent

### 2.3 Infrastructure Layer

**Purpose:** Implements technical concerns — how data is persisted, how external APIs are called, how cross-cutting concerns are applied.

**What belongs here:**
- Database context / ORM configuration
- Repository implementations (implement interfaces defined in Domain)
- Unit of Work implementation
- Database migrations
- External API clients (payment gateways, email providers, SMS providers)
- File system access
- Caching implementations
- Queue and message bus implementations
- Current-user-context implementation (extracts user from HTTP context, JWT, etc.)
- Cross-cutting interceptors (automatic audit, soft delete, logging)

**What does NOT belong here:**
- Business logic (that's Application)
- Domain entities (that's Domain)
- Controllers (that's Presentation)

**Allowed dependencies:**
- Domain (to implement its interfaces)
- Application (only for abstractions like `ICurrentUserService`)
- Any infrastructure libraries (ORM, HTTP clients, SDK packages)

**Example folder structure:**
```
infrastructure/
├── data/
│   ├── database-context.ts
│   ├── migrations/
│   └── configurations/
│       ├── user-configuration.ts
│       └── order-configuration.ts
├── repositories/
│   ├── user-repository.ts
│   ├── order-repository.ts
│   └── unit-of-work.ts
├── external-services/
│   ├── email-service.ts
│   ├── sms-service.ts
│   └── payment-gateway.ts
├── caching/
│   └── redis-cache-service.ts
└── context/
    └── current-user-service.ts
```

**Why this layer exists:**
- Isolates technical implementation details
- Implements contracts defined by Domain
- Can be swapped (different database, different email provider) without affecting business logic
- Handles cross-cutting concerns at the right boundary

**What happens if you violate this layer:**
- Putting business logic in repositories → logic duplicated across data access code
- Using the database context directly in Application → cannot swap databases
- Infrastructure dictating domain structure → business logic tied to database schema

### 2.4 Presentation Layer

**Purpose:** The entry point. Handles HTTP requests (or GraphQL, gRPC, CLI — whatever the external interface is), routing, authentication, and response formatting.

**What belongs here:**
- Controllers / route handlers / endpoint definitions
- Application bootstrap / startup / DI configuration
- Middleware
- API filters and attributes
- OpenAPI/Swagger/GraphQL schema configuration
- Configuration files (`appsettings`, `config.yaml`, environment loading)

**What does NOT belong here:**
- Business logic (delegate to Application services)
- Data access code (no repositories, no ORM)
- Domain entities (accept and return DTOs only)

**Allowed dependencies:**
- Application (to consume services)
- Infrastructure (only for DI registration at bootstrap — NOT for business logic consumption)
- HTTP framework (Express, ASP.NET Core, FastAPI, Spring, etc.)
- Authentication middleware libraries

**Example folder structure:**
```
presentation/
├── controllers/
│   ├── users-controller.ts
│   ├── orders-controller.ts
│   └── auth-controller.ts
├── middleware/
│   ├── auth-middleware.ts
│   ├── error-middleware.ts
│   └── correlation-id-middleware.ts
├── filters/
│   └── validation-filter.ts
├── config/
│   ├── appsettings.json
│   ├── appsettings.development.json
│   └── appsettings.production.json
└── bootstrap.ts (or program.ts / main.ts / server.ts)
```

**Why this layer exists:**
- Entry point for external requests
- Handles protocol-specific concerns
- Configures dependency injection
- Thin layer that delegates to Application

**What happens if you violate this layer:**
- Business logic in controllers → duplicated across endpoints; untestable
- Direct database/ORM usage → cannot swap implementations
- Returning entities → internal structure exposed externally

---

## 3. Cross-Cutting Concerns Patterns

Cross-cutting concerns are behaviors that apply across many features. The rule: **implement once at the right boundary; never repeat per-feature.**

### 3.1 Automatic Audit Trail Pattern

**Problem:** Every entity needs `createdAt`, `createdBy`, `modifiedAt`, `modifiedBy`. Manually setting these in every service method is error-prone, repetitive, and inconsistent.

**Pattern:**

1. Define a marker interface in Domain:
   ```
   interface IAuditable {
       createdAt: DateTime
       createdBy: UserId
       modifiedAt: DateTime
       modifiedBy: UserId
   }
   ```

2. Domain entities that need auditing implement this interface.

3. Infrastructure data-access layer intercepts all save operations:
   ```
   on saveChanges():
       for each entity being saved:
           if entity implements IAuditable:
               if state == ADDED:
                   entity.createdAt = now()
                   entity.createdBy = currentUserService.getUserId()
                   entity.modifiedAt = now()
                   entity.modifiedBy = currentUserService.getUserId()
               elif state == MODIFIED:
                   entity.modifiedAt = now()
                   entity.modifiedBy = currentUserService.getUserId()
                   // CRITICAL: prevent overwriting created fields
                   markFieldAsUnmodified('createdAt')
                   markFieldAsUnmodified('createdBy')
       super.saveChanges()
   ```

4. Business services contain **zero audit code**:
   ```
   service UserService:
       createUser(request):
           user = mapper.map(request, User)
           userRepository.add(user)
           unitOfWork.saveChanges()  // audit happens automatically
   ```

**Why this works:**
- One place to change audit logic
- Impossible to forget
- Consistent across all auditable entities
- Business services stay focused on business logic

### 3.2 Automatic Soft Delete Pattern

**Problem:** Hard-deleting records loses history. Every entity needs "soft delete" — mark as deleted instead of removing.

**Pattern:**

1. Marker interface in Domain:
   ```
   interface ISoftDeletable {
       isDeleted: boolean
       deletedAt: DateTime?
   }
   ```

2. Infrastructure intercepts deletes:
   ```
   on saveChanges():
       for each entity being deleted:
           if entity implements ISoftDeletable:
               convert delete operation to update
               entity.isDeleted = true
               entity.deletedAt = now()
   ```

3. Global query filter excludes soft-deleted records:
   ```
   on query setup:
       for entities implementing ISoftDeletable:
           add filter: where isDeleted == false
   ```

4. Services calling `delete()` get soft delete automatically.

### 3.3 Current-User Context Pattern

**Problem:** Services need to know "who is the current user" for audit, authorization, tenant isolation. But services must not depend on HTTP primitives.

**Pattern:**

1. Interface in Application:
   ```
   interface ICurrentUserService:
       getUserId(): UserId
       getRoles(): Role[]
       isAuthenticated(): boolean
   ```

2. Implementation in Infrastructure extracts from HTTP context:
   ```
   class HttpCurrentUserService implements ICurrentUserService:
       constructor(httpContextAccessor)
       getUserId():
           claim = httpContextAccessor.current.user.findClaim('sub')
           return claim?.value ?? throw UnauthorizedException
   ```

3. Services inject the interface:
   ```
   class OrderService:
       constructor(currentUserService: ICurrentUserService, ...)
   ```

4. Tests inject a mock without any HTTP dependency.

### 3.4 Structured Logging Pattern

**Problem:** Logs scattered across features with inconsistent formats, missing correlation IDs, unstructured text.

**Pattern:**

1. Single logger abstraction (framework-provided or custom `ILogger`) injected via DI.
2. At request boundary, middleware generates a correlation ID and attaches it to the logging context for the request's lifetime.
3. All log entries automatically include: timestamp, severity, correlation ID, service name, environment.
4. Business services log business events only (`"order.created"`, `"payment.failed"`) — not framework noise.
5. Errors include full exception context; user-facing messages are separate from internal logs.

### 3.5 Authorization Pattern

**Problem:** Scattered `if (user.role !== 'admin')` checks across controllers are error-prone and hard to audit.

**Pattern:**

1. **Role/permission gates at Presentation layer** — simple "only admins can call this endpoint" checks via framework authorization (attributes, middleware, decorators).

2. **Business-rule authorization at Application layer** — "a user can only edit orders they own" checks inside services, using `currentUserService` to identify the user and checking domain-level ownership.

3. **Never in data access** — repositories don't know about authorization; services enforce it before calling repositories.

4. **Policy-based preferred** — instead of `if (user.role === 'admin')`, use `authorizationService.can(user, 'orders.edit', order)`. Policies defined once, used everywhere.

### 3.6 Caching Pattern

**Problem:** Adding caching directly in services pollutes business logic with cache concerns.

**Pattern (Decorator):**

1. Services depend on repository interfaces (`IUserRepository`).
2. Two implementations exist: `UserRepository` (talks to database) and `CachedUserRepository` (wraps `UserRepository` and checks cache first).
3. DI registration decides which implementation is active.
4. Business services are unaware caching exists.
5. Cache invalidation happens inside the cached repository implementation or via events.

### 3.7 Error Handling Pattern

**Problem:** Errors thrown at different layers need different treatment. Domain errors (invalid state) differ from infrastructure errors (database timeout) which differ from presentation errors (bad request).

**Pattern:**

1. **Domain exceptions** — thrown by domain rules (`InvalidOrderStateException`). Defined in Domain. Express business failures.

2. **Infrastructure exceptions** — thrown by infrastructure (timeout, connection failure, third-party API error). Caught at Application layer and wrapped or handled.

3. **API errors** — what the API returns. Mapped from the above in a global error middleware at Presentation.

4. **Global error middleware** at Presentation:
   - Catches all unhandled exceptions
   - Maps domain exceptions to appropriate HTTP status codes (e.g., `InvalidStateException` → 400, `NotFoundException` → 404)
   - Logs the full error with correlation ID
   - Returns a user-safe error response without leaking internals

5. **Services use try/catch only when they can meaningfully recover or add context.** Otherwise, they let exceptions propagate to the global handler.

---

## 4. Data Access Patterns

### 4.1 The Repository Pattern

**Purpose:** Abstract data access behind a domain-meaningful interface.

**Rules:**
- Repository interface lives in Domain.
- Repository implementation lives in Infrastructure.
- Repository methods speak the business language (`getActiveUsers()`, not `selectWhere(...)`).
- Repositories return entities, not database rows or ORM tracking objects.
- Repositories never contain business logic — only data access.

**Example interface (Domain):**
```
interface IUserRepository:
    getById(id: UserId): User?
    getByEmail(email: string): User?
    getActiveUsers(): User[]
    add(user: User): void
    update(user: User): void
    delete(user: User): void
```

**Example implementation (Infrastructure):**
```
class UserRepository implements IUserRepository:
    constructor(dbContext)
    getById(id):
        return dbContext.users.where(u => u.id === id).first()
    getActiveUsers():
        return dbContext.users.where(u => u.isActive && !u.isDeleted).toArray()
    // ... etc
```

**Services consume the interface:**
```
class UserService:
    constructor(userRepository: IUserRepository, unitOfWork: IUnitOfWork)
    async updateEmail(userId, newEmail):
        user = await userRepository.getById(userId)
        if (!user) throw UserNotFoundException()
        user.changeEmail(newEmail)  // domain method enforces business rules
        await unitOfWork.saveChanges()
```

### 4.2 The Unit of Work Pattern

**Purpose:** Coordinate transactions across multiple repositories.

**Rules:**
- UoW interface lives in Domain.
- UoW implementation lives in Infrastructure.
- Services call `unitOfWork.saveChanges()` once per logical operation.
- UoW exposes repositories as properties (or services inject repositories separately and UoW only for saving — both patterns valid).
- All changes in one UoW commit together or roll back together.

**Example interface (Domain):**
```
interface IUnitOfWork:
    users: IUserRepository
    orders: IOrderRepository
    saveChanges(): Task<int>
```

**Service usage:**
```
class CheckoutService:
    constructor(unitOfWork: IUnitOfWork)
    async checkout(cartId):
        cart = await unitOfWork.carts.getById(cartId)
        order = createOrderFromCart(cart)
        await unitOfWork.orders.add(order)
        cart.markAsConverted()
        await unitOfWork.saveChanges()  // order created and cart updated in one transaction
```

### 4.3 DTO vs Entity Usage

**Inbound (from API):**
```
Controller:
    accepts CreateOrderRequest (DTO)
    passes to OrderService.createOrder(request)

Application service:
    receives CreateOrderRequest (DTO)
    maps to Order (entity) using mapping profile
    validates business rules on entity
    persists via repository
```

**Outbound (to API):**
```
Application service:
    loads Order (entity) via repository
    maps to OrderDto using mapping profile
    returns OrderDto

Controller:
    receives OrderDto
    returns OrderDto as response body
```

**Never:**
- Controller accepts an entity as input.
- Controller returns an entity as output.
- Service returns an entity to a controller.
- Entity is serialized directly to JSON response.

---

## 5. Common Mistakes & How To Avoid Them

### Mistake 1: Business Logic In Controllers

**Wrong:**
```
POST /orders endpoint:
    read request body
    validate customer exists (query database)
    calculate total = sum of items * prices
    apply tax = total * 0.1
    set status = 'pending'
    set createdAt = now
    insert into orders table
    send confirmation email via SMTP
    return order as response
```

**Right:**
```
POST /orders endpoint:
    read and validate CreateOrderRequest
    call orderService.create(request)
    return OrderDto as response

// All logic lives in orderService.create() which orchestrates
// domain entities, repositories, and injected services.
```

### Mistake 2: Framework Attributes On Domain Entities

**Wrong:**
```
@Table("orders")
@Entity
class Order:
    @PrimaryKey
    @Column("order_id")
    id: UUID

    @Column("total")
    @Required
    @MaxLength(50)
    total: decimal
```

**Right:**
```
// Domain
class Order:
    id: OrderId
    total: Money
    items: OrderItem[]
    // pure business properties; no attributes

// Infrastructure
class OrderConfiguration:
    configure(modelBuilder):
        modelBuilder.entity<Order>():
            toTable("orders")
            hasKey(o => o.id)
            property(o => o.total).hasColumnName("total")
```

### Mistake 3: Using The Data Access Library Directly In Application

**Wrong:**
```
// Application layer
import { PrismaClient } from '@prisma/client'

class UserService:
    constructor(prisma: PrismaClient)  // ❌ Application depends on Prisma

    async getById(id):
        return prisma.user.findUnique({ where: { id } })  // ❌ direct ORM usage
```

**Right:**
```
// Domain
interface IUserRepository:
    getById(id): User?

// Application
class UserService:
    constructor(userRepository: IUserRepository)  // ✅ depends on abstraction

    async getById(id):
        return userRepository.getById(id)  // ✅ uses repository

// Infrastructure
class PrismaUserRepository implements IUserRepository:
    constructor(prisma: PrismaClient)
    // ... Prisma-specific implementation
```

### Mistake 4: Returning Entities From API

**Wrong:**
```
GET /users/{id}:
    user = userService.getById(id)
    return user  // ❌ entity directly as response
```

**Problems:**
- Internal structure exposed to API consumers
- Cannot change entity without breaking API
- May expose sensitive fields (password hash, internal flags)
- Circular reference issues with navigation properties

**Right:**
```
GET /users/{id}:
    userDto = userService.getById(id)  // ✅ service returns DTO
    return userDto
```

### Mistake 5: Manual Audit Trail In Services

**Wrong:**
```
class OrderService:
    createOrder(request):
        order = new Order(...)
        order.id = uuid()
        order.createdAt = now()  // ❌ manual
        order.createdBy = currentUserId  // ❌ manual
        order.modifiedAt = now()  // ❌ manual
        order.modifiedBy = currentUserId  // ❌ manual
        orderRepository.add(order)
        unitOfWork.saveChanges()

// Problem: repeated in every service method; easy to forget; inconsistent
```

**Right:**
```
class OrderService:
    createOrder(request):
        order = mapper.map(request, Order)
        order.id = uuid()
        orderRepository.add(order)
        unitOfWork.saveChanges()  // ✅ audit applied automatically in SaveChanges interceptor

// See Section 3.1 for the interceptor pattern
```

### Mistake 6: Skipping A Layer "For Simple CRUD"

**Wrong rationalization:** *"This is just a simple lookup. It doesn't need a repository interface."*

**Why it fails:**
- The "simple" code grows over time.
- Six months later, it has caching, logging, and special-case logic — all inside the controller.
- Now you can't test it without a database.
- Every other "simple" endpoint follows the same pattern, and you have 50 of them.

**Rule:** **Consistency is more important than convenience.** Follow the pattern always, even for simple CRUD. The 10 extra minutes of correct structure saves weeks of refactoring later.

### Mistake 7: Business Logic In Repositories

**Wrong:**
```
class OrderRepository:
    async createOrderWithInventoryUpdate(orderData):
        // ❌ Business logic in repository
        if (orderData.total > 1000) orderData.requiresApproval = true
        reduceInventory(orderData.items)
        await db.orders.insert(orderData)
        await sendConfirmationEmail(orderData.email)
```

**Right:**
```
class OrderRepository:
    async add(order): ...
    async getById(id): ...
    // Only data access. No business logic.

class OrderService:
    async create(request):
        order = buildOrderFromRequest(request)  // business logic here
        if (order.total > threshold) order.markForApproval()
        await inventoryService.reserveItems(order.items)
        await orderRepository.add(order)
        await unitOfWork.saveChanges()
        await emailService.sendConfirmation(order)
```

### Mistake 8: Domain Depending On Application Or Infrastructure

**Wrong:**
```
// Domain
import { OrderDto } from '@application/dtos'  // ❌ Domain imports from Application
import { DbContext } from '@infrastructure/data'  // ❌ Domain imports from Infrastructure

class Order:
    toDto(): OrderDto { ... }  // ❌ Domain knows about DTOs
```

**Right:**
```
// Domain
class Order:
    // pure business properties and methods
    // no knowledge of DTOs or data access

// Application
class OrderMapper:
    toDto(order: Order): OrderDto { ... }  // ✅ mapping lives in Application
```

---

## 6. Consequences Of Violating The Architecture

### Short-Term (Weeks To Months)

- 200+ line controllers containing validation, business logic, data access, and email sending
- Cannot test business logic without a database, network, and full stack running
- Changes to one endpoint ripple through many files
- Code reviews take hours because concerns are tangled

### Medium-Term (Months To A Year)

- Cannot swap a technology (database, email provider) without a multi-month rewrite
- Tests are slow and flaky because they require real infrastructure
- New developers take months to onboard
- Estimates become inaccurate because small changes have unpredictable blast radius

### Long-Term (1–3 Years)

- "Big ball of mud" codebase where nobody knows where anything is
- Code reviews take days
- New features regularly break old features
- Small changes require massive testing
- Technical debt compounds; team velocity collapses
- Developers leave; institutional knowledge walks out the door
- Product stops being able to evolve

### The Business Impact

| Metric | Without Architecture | With Architecture |
|---|---|---|
| Time to add feature | Weeks | Days |
| Time to fix bug | Days | Hours |
| Test coverage | 20-30% | 80%+ |
| Onboarding new dev | Months | 1-2 weeks |
| Technical debt trajectory | Growing | Controlled |
| Production incidents | Frequent | Rare |
| Deployment confidence | Low | High |

---

## 7. New Feature Development Workflow

Follow this sequence for every new feature. Skipping steps creates debt.

### Step 1 — Model The Domain

Ask: *"What is this concept in the business?"*

Create:
- Domain entity with business properties
- Any value objects the entity uses
- Domain interfaces the entity needs (repositories, domain services)
- Domain exceptions for invariant violations

No code outside Domain yet.

### Step 2 — Define The Application Contract

Ask: *"What operations do users perform on this concept?"*

Create:
- Service interface in Application describing the operations
- DTOs for each operation's input and output
- Validators for input DTOs
- Mapping profiles between DTOs and entities

Still no database or HTTP code.

### Step 3 — Implement The Application Logic

Create the service implementation:
- Inject repository interfaces from Domain
- Inject any cross-cutting service interfaces (logger, current-user, email)
- Orchestrate domain entities
- Contain business workflow logic
- Return DTOs

Write unit tests now. The service should be testable with pure mocks.

### Step 4 — Implement Infrastructure

Create:
- Repository implementations that implement the Domain interfaces
- Database configuration / ORM mapping
- Any external service implementations needed
- Migration for new tables/columns

Write integration tests for repositories.

### Step 5 — Add Presentation

Create:
- Controller / route handler
- Route definitions
- DTO validation wiring
- Error handling

Wire up DI at bootstrap.

Write E2E tests for critical flows.

### Step 6 — Documentation

- Update API documentation (OpenAPI/GraphQL schema)
- Add ADR if architectural decisions were made
- Update README if setup changes
- Add runbook entries for operational concerns

### Step 7 — Verify

- Run all tests
- Run linter, type checker
- Review against the Definition of Done in the Mandate
- Review against the Code Review Checklist below
- Deploy behind a feature flag if risky

---

## 8. Code Review Checklist

### Architecture
- [ ] Dependency direction correct (inward only)?
- [ ] Domain has zero external dependencies? (check manifest files)
- [ ] Application doesn't import from data access library?
- [ ] Controllers don't import from Infrastructure (except bootstrap)?
- [ ] Entities have no framework attributes?
- [ ] DTOs used at API boundaries (not entities)?
- [ ] Data access via repository abstraction (not direct ORM)?
- [ ] Cross-cutting concerns automated (no manual audit/logging in services)?

### Code Quality
- [ ] No duplicate logic introduced?
- [ ] No duplicate constants or types?
- [ ] All shared code lives in shared layer?
- [ ] No magic numbers or strings?
- [ ] All configurable values externalized?
- [ ] Functions focused (< ~50 lines)?
- [ ] Files focused (< ~300 lines)?
- [ ] No `console.log` or equivalent in production paths?
- [ ] No unexplained `TODO`/`FIXME`?

### Frontend (if applicable)
- [ ] No business logic in pages?
- [ ] No data fetching duplicated across pages?
- [ ] Uses design system tokens exclusively?
- [ ] Uses shared components (no one-off UI)?
- [ ] Loading, empty, error, success states implemented?
- [ ] Keyboard accessible?
- [ ] WCAG 2.2 AA compliant?
- [ ] All UI strings externalized?

### Data & API
- [ ] Input validation at API boundary?
- [ ] Consistent response envelope?
- [ ] Pagination on list endpoints?
- [ ] API contract versioned?
- [ ] No mock or hardcoded data?

### Security
- [ ] Auth and authz enforced?
- [ ] Input sanitized?
- [ ] No secrets in code?
- [ ] Rate limiting on public endpoints?
- [ ] PII handled correctly?

### Testing
- [ ] Business logic testable without infrastructure?
- [ ] Unit tests use mocked dependencies?
- [ ] Integration tests exist for API contracts?
- [ ] Tests are deterministic (no real clock, no real network)?

### Operations
- [ ] Logging and error tracking instrumented?
- [ ] Metrics captured for key operations?
- [ ] Errors have correlation IDs?
- [ ] Documentation updated?

---

## 9. FAQ — Anticipating Rationalizations

These are the most common excuses for shortcuts. Memorize the answers.

**Q: Can I skip a layer for simple CRUD?**
A: **No.** Consistency > convenience. Every "just this once" exception becomes a pattern within months. The 10 extra minutes now saves weeks later.

**Q: Can Application reference Infrastructure?**
A: **No.** This violates the dependency rule. Application depends only on Domain. Infrastructure is injected via DI. If you need something Infrastructure provides, define an interface in Application (or Domain) and let Infrastructure implement it.

**Q: Where do validators go — Domain or Application?**
A: **Application.** Validators validate *input DTOs* from the API, not business entities. They enforce API-contract rules (required fields, string lengths, format checks). Business rules about entities live on the entities themselves in Domain.

**Q: Can I put ORM attributes on Domain entities?**
A: **No.** Use fluent configuration or mapper classes in Infrastructure instead. Keep entities pure. The minute an entity has an ORM attribute, your business logic is coupled to that ORM.

**Q: Where does caching go?**
A: **Infrastructure.** Create a cache abstraction interface in Application (or Domain if domain services need it), implement in Infrastructure using Redis/Memcached/whatever, inject via DI. Use the decorator pattern (Section 3.6) to add caching to existing implementations without changing services.

**Q: I have shared code that's used by multiple projects. Where does it go?**
A: Create a separate `shared` package/project at the same level as Domain. Other projects reference it. It follows its own internal architecture.

**Q: What if my DTO looks exactly like my entity — do I still need both?**
A: **Yes.** Today they look the same. Tomorrow the entity adds an internal-only field, or the API needs to add a computed field, or the API needs to hide a field. Having separate DTOs decouples API evolution from entity evolution. The cost is low; the benefit is large.

**Q: Do I need to write an interface for a repository that only has one implementation?**
A: **Yes.** Two reasons: (1) testability — services can be tested with mocks; (2) flexibility — swapping implementations (adding caching, switching databases) doesn't require touching callers.

**Q: Can I put business logic inside an ORM query/specification?**
A: **No.** The query returns data; business logic is applied to data by services or domain methods. Mixing them makes business logic dependent on the ORM and hard to test.

**Q: The controller is going to be thin — can I just put the service call inline without a separate service file?**
A: **No.** Even "thin" controllers have HTTP concerns; services have business concerns. They evolve differently. Keeping them separate costs nothing and preserves the architecture.

**Q: The service method only has 3 lines of code. Does it really need to exist?**
A: **Yes.** The service provides a stable contract the controller depends on. When you add logging, add authorization, add error handling, add another operation — all of those changes happen in the service without touching the controller.

**Q: I need to do something the repository doesn't support. Can I just query the database directly this one time?**
A: **No.** Add the method to the repository. That's what it's there for. "Just this once" becomes three times becomes ten times becomes a repository bypass pattern that destroys the architecture.

---

## 10. Reference: Technology-Specific Notes

This playbook is technology-agnostic. How you implement the patterns depends on your stack. Here are brief pointers for common stacks:

### Node.js / TypeScript (Next.js, NestJS, Express, Fastify)

- **Domain:** Pure TypeScript classes/interfaces. No decorators except domain ones.
- **Application:** Services as classes with constructor injection (NestJS) or factory functions (Next.js).
- **Infrastructure:** Prisma / TypeORM / Drizzle / Mongoose for data; typed clients for external APIs.
- **Cross-cutting concerns:** Prisma middleware or ORM hooks for audit trail; NestJS interceptors or Next.js middleware for logging.
- **Unit of Work:** Prisma transactions, TypeORM's `DataSource.transaction()`, or explicit transaction boundaries.
- **Validation:** Zod schemas in Application, shared between API validation and frontend forms.

### Python (FastAPI, Django, Flask)

- **Domain:** Pure dataclasses or Pydantic models without DB dependencies. Use `attrs` or dataclasses.
- **Application:** Service classes with dependency injection (FastAPI's `Depends`, dependency-injector library).
- **Infrastructure:** SQLAlchemy (Imperative mapping keeps entities clean), Django ORM (requires care to keep entities pure), Motor for MongoDB.
- **Cross-cutting concerns:** SQLAlchemy event listeners for audit; FastAPI middleware for logging.
- **Validation:** Pydantic models for DTOs.

### Java / Kotlin (Spring Boot, Micronaut, Quarkus)

- **Domain:** POJOs, no JPA annotations. Use separate `@Entity` classes or mapping via XML/converters.
- **Application:** `@Service` classes (though be careful — Spring `@Service` is a Spring concept; keep service interfaces pure).
- **Infrastructure:** Spring Data repositories (define interfaces in Domain; `@Repository` implementations in Infrastructure).
- **Cross-cutting concerns:** JPA `@EntityListeners` for audit; Spring AOP for logging.
- **Validation:** Bean Validation on DTOs.

### .NET (ASP.NET Core)

- **Domain:** Plain C# classes. No EF Core annotations.
- **Application:** Services registered via DI.
- **Infrastructure:** EF Core with fluent configuration in `OnModelCreating`. Repository classes implement Domain interfaces.
- **Cross-cutting concerns:** Override `SaveChangesAsync` for audit; middleware for logging and correlation IDs.
- **Validation:** FluentValidation on DTOs.

### Go

- Go has different idioms (no classes, interface-based polymorphism is first-class).
- **Domain:** Structs with methods, interfaces for contracts.
- **Application:** Services as structs with constructor functions.
- **Infrastructure:** GORM, sqlx, or ent; implement Domain interfaces.
- **Cross-cutting concerns:** GORM hooks for audit; middleware for logging.

Whatever the stack, the *principles* are identical:
1. Pure Domain at the center
2. Application orchestrates
3. Infrastructure implements
4. Presentation delegates
5. Dependencies point inward
6. DTOs at boundaries
7. Cross-cutting concerns automated at the right layer

---

*This Playbook is the tactical companion to `ENGINEERING_MANDATE.md`. Use them together. The Mandate is the law; the Playbook is how the law is applied. Both are required reading for every AI session and every code review.*
