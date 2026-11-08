## How core-ts works

core-ts is built around a simple idea: **separate what applications have in common from what makes each product unique.**

A software product has its own business rules, workflows, and domain model. At the same time, it needs technical mechanisms for persistence, validation, authentication, logging, and data processing.

core-ts provides reusable building blocks for these recurring concerns. Applications combine them according to their needs instead of rebuilding everything from scratch or adopting a rigid, all-in-one framework.

### 1. A layered approach to software design

core-ts encourages a clear separation of responsibilities.

A typical application can be organized into the following layers:

```text
┌───────────────────────────────────────┐
│           Application Layer           │
│     API endpoints, controllers        │
├───────────────────────────────────────┤
│          Business Layer               │
│       Use Cases and Services          │
├───────────────────────────────────────┤
│        Abstraction Layer              │
│   Repository and service interfaces   │
├───────────────────────────────────────┤
│      Infrastructure Layer             │
│  Database adapters, external systems  │
└───────────────────────────────────────┘
```

Each layer has a distinct responsibility.

- **Application layer:** Receives requests and coordinates interactions with the application.
- **Business layer:** Implements use cases and business rules.
- **Abstraction layer:** Defines contracts that describe the capabilities required by the business layer.
- **Infrastructure layer:** Implements those contracts using databases, external APIs, and other technical systems.

The purpose of this separation is not to create more layers for their own sake. It is to make responsibilities explicit, reduce unnecessary coupling, and allow individual parts of the system to evolve independently.

core-ts provides abstractions and reusable implementations that help developers establish this separation without having to design every recurring mechanism from scratch.

### 2. Abstractions first, implementations where needed

A common source of complexity is allowing business logic to depend directly on infrastructure details.

For example, a business use case that directly constructs database queries becomes tightly coupled to a particular database implementation. Changing the persistence mechanism can then affect code that should only be concerned with business behavior.

core-ts addresses this problem through reusable interfaces and abstractions.

Consider a simplified application flow:

```text
Application
    │
    ▼
Use Case
    │
    ▼
Repository Interface
    │
    ▼
Repository Implementation
    │
    ▼
Database
```

The use case works with a repository contract rather than needing to know how the database operates internally.

This approach provides several benefits:

- Business logic can remain focused on business requirements.
- Infrastructure implementations can change without necessarily changing the use case.
- Tests can substitute infrastructure dependencies with controlled implementations.
- Shared patterns can be reused across different applications.

For example, `onecore` provides database-independent repository, service, and use-case abstractions. Database-specific libraries can implement the persistence capabilities required by an application.

The important principle is that **abstractions define responsibilities, while implementations handle technical details.**

### 3. Composable libraries instead of a monolithic framework

core-ts is an ecosystem of libraries that can be combined to solve different problems.

An application does not need every capability in the ecosystem. It can select the libraries relevant to its requirements and compose them into an application-specific solution.

For example:

| Concern | Example core-ts library |
|---|---|
| Application and repository abstractions | `onecore` |
| MongoDB repository operations | `mongodb-kit` |
| JWT authentication in Express | `express-jsonwebtoken` |
| Authorization middleware | `security-express` |
| Data import pipelines | `import-service` |
| Logging | `logger-core` |

Each library addresses a particular responsibility, while the application determines how those responsibilities fit together.

This approach avoids two extremes:

- Reimplementing common technical capabilities independently in every project.
- Adopting a large framework that dictates how every part of the application must be structured.

Instead, developers can reuse established solutions while retaining control over application architecture.

**The benefit:** Standardization where it helps, flexibility where it matters.

### 4. Reuse at different levels of abstraction

Not every recurring problem should be solved at the same architectural level.

Some problems require general-purpose interfaces. Others require reusable technical implementations. Still others involve complete workflows assembled from multiple components.

core-ts supports reuse at these different levels.

**Level 1 — Foundational abstractions**

Define common contracts and concepts that applications can use without depending on a specific infrastructure technology.

Example: repository and use-case abstractions in `onecore`.

**Level 2 — Infrastructure-specific implementations**

Provide reusable mechanisms for working with a particular technology while preserving useful application-level abstractions.

Example: `mongodb-kit` builds repository functionality on top of the official MongoDB driver.

**Level 3 — Composable workflows**

Combine multiple components into a complete process without embedding every concern into one large implementation.

Example: `import-service` composes data sources, transformers, optional validation, and writers into an import pipeline.

```text
Data Source
    │
    ▼
Transformer / Parser
    │
    ▼
Optional Validation
    │
    ▼
Writer
    │
    ▼
Result
```

A data import workflow can therefore focus on how records move through the process, while individual components handle parsing, validation, writing, and error reporting.

This layered approach to reuse makes it possible to share small building blocks without losing the ability to construct larger, application-specific solutions.

### 5. TypeScript as a foundation for reusable contracts

TypeScript provides more than syntax and static type checking. Its type system can help define contracts between components and make their intended usage explicit.

core-ts uses TypeScript abstractions and generics to support reusable components without forcing every application to use the same domain model.

For example, a generic repository can work with different entity types while retaining type information about the data being stored and retrieved.

This helps developers reuse implementations without sacrificing the benefits of static typing.

Well-defined contracts also make component boundaries easier to understand: developers can see what a component accepts, what it returns, and what responsibilities it exposes.

The objective is to make reusable code easier to compose and reason about, rather than simply making it more generic.

### 6. Adopt incrementally, evolve independently

Introducing a shared engineering foundation does not need to mean rewriting an entire application.

Developers can adopt core-ts where it provides immediate value, such as repository operations, authentication middleware, logging, or data import. Other parts of the application can continue using existing implementations until there is a reason to change them.

As the application evolves, additional core-ts libraries can be introduced where appropriate.

This incremental approach helps teams:

- Reduce the risk of large-scale architectural migrations.
- Introduce consistent patterns gradually.
- Reuse proven components in new features.
- Replace individual implementations without necessarily redesigning the entire application.

The objective is to make architecture an enabler of product development, not an obstacle to it.

### 7. The result: A reusable engineering foundation

The individual benefits of reusable libraries become more valuable when they work together.

A repository abstraction reduces duplicated persistence code. A validation library provides a common way to enforce data constraints. Logging infrastructure makes application behavior easier to observe. Authentication and authorization components address recurring security concerns. Data processing libraries simplify repeatable import workflows.

When these capabilities are available as composable building blocks, developers can spend less effort repeatedly solving technical problems and more effort delivering product-specific functionality.

The result is not simply a smaller codebase. It is a codebase with clearer responsibilities, more consistent patterns, and fewer unnecessary reasons to reinvent existing solutions.

**core-ts turns recurring software engineering problems into reusable capabilities, giving each product a stronger foundation on which to build.**