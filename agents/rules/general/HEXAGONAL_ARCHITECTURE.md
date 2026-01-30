# Hexagonal Architecture Rules & Best Practices

## Core Principles

### 1. Dependency Rule
**Dependencies must point inward only.** Outer layers can depend on inner layers, but inner layers cannot depend on outer layers.

```
Infrastructure/Adapters → Application → Domain
(Outer)                                  (Inner)
```

### 2. Layer Structure

#### Domain Layer (Core/Center)
- Contains business logic and entities
- No dependencies on external layers
- Pure business rules
- Framework-agnostic
- Most stable layer

**Rules:**
- No framework imports (Spring, Jakarta, etc.)
- No database annotations (@Entity, @Table)
- No REST annotations (@RestController, @RequestMapping)
- Use plain Java/Kotlin objects (POJOs/POKOs)
- Define interfaces for ports

#### Application Layer (Use Cases)
- Orchestrates domain logic
- Implements application-specific business rules
- Defines port interfaces (if not in domain)
- Transaction boundaries

**Rules:**
- Can depend on domain layer only
- Defines input/output ports
- No implementation details of adapters
- No database or HTTP logic

#### Infrastructure Layer (Adapters)
- Implements port interfaces
- Database adapters
- REST/GraphQL controllers
- Message queue consumers
- External service clients

**Rules:**
- Depends on application and domain layers
- Implements port interfaces
- Contains all framework code
- Handles technical concerns

## Port Types

### Primary Ports (Driving/Input)
Interfaces that allow external actors to interact with the application.

**Examples:**
- REST API controllers
- CLI commands
- Message consumers
- Scheduled jobs

**Rule:** Application exposes interfaces; adapters implement the calling mechanism.

### Secondary Ports (Driven/Output)
Interfaces the application uses to interact with external systems.

**Examples:**
- Database repositories
- Email services
- External API clients
- File systems

**Rule:** Application defines interfaces; adapters implement them.

## File Organization

### Option 1: By Layer
```
src/
├── domain/
│   ├── model/
│   ├── service/
│   └── port/
├── application/
│   ├── usecase/
│   └── port/
└── infrastructure/
    ├── adapter/
    │   ├── in/
    │   │   └── rest/
    │   └── out/
    │       ├── persistence/
    │       └── external/
    └── config/
```

### Option 2: By Feature
```
src/
├── order/
│   ├── domain/
│   ├── application/
│   └── adapter/
└── payment/
    ├── domain/
    ├── application/
    └── adapter/
```

## Key Rules & Constraints

### Domain Layer Rules
1. **No Infrastructure Leakage:** Domain must not import anything from infrastructure
2. **Define Abstractions:** Create interfaces for external dependencies (repositories, services)
3. **Pure Business Logic:** All validation and business rules live here
4. **No Annotations:** Avoid framework-specific annotations
5. **Value Objects:** Use immutable value objects where appropriate

### Application Layer Rules
1. **Orchestration Only:** Coordinate between domain and infrastructure
2. **Port Definitions:** Define all port