## The core-ts ecosystem

core-ts consists of reusable TypeScript libraries that address recurring problems in software product development.

Each library has a focused responsibility. Together, they provide building blocks for application architecture, data access, validation, security, observability, and data processing.

Rather than requiring every application to adopt the entire ecosystem, core-ts allows developers to select and compose the capabilities their products need.

### 1. Core abstractions

**`onecore` — A common foundation for application development**

Repository: [core-ts/core](https://github.com/core-ts/core)

`onecore` provides database-independent abstractions for common application operations, including repositories, services, use cases, filtering, search results, and metadata.

It establishes reusable contracts that application code and infrastructure implementations can share.

With these abstractions, developers can build common CRUD and search operations without tying business functionality directly to a particular database driver.

The benefit is a consistent foundation for application development that can be reused across different products and persistence technologies.

**`sql-core` — Common abstractions for SQL data access**

`sql-core` provides abstractions for working with SQL databases, allowing application-level data access patterns to be separated from database-specific implementations.

This helps applications share repository and database interaction patterns while using the appropriate database adapter for their chosen technology.

### 2. Database access and persistence

Software products frequently need to persist, retrieve, search, and update data. Implementing these operations repeatedly across applications can lead to duplicated code and inconsistent behavior.

core-ts provides database-related libraries that help standardize these recurring operations.

| Library | Responsibility |
|---|---|
| `mongodb-kit` | Repository and data-access operations for MongoDB |
| `mysql2-core` | MySQL integration using the `mysql2` driver |
| `postgres-kit` | PostgreSQL integration |
| `mssql-core` | Microsoft SQL Server integration |
| `oracle-core` | Oracle Database integration |

These libraries address different database technologies while fitting into the broader goal of reusable data-access patterns.

For example, `mongodb-kit` provides a generic repository framework over the official MongoDB driver. It includes capabilities such as CRUD operations, searching, pagination, sorting, and batch operations.

Repository: [core-ts/mongo](https://github.com/core-ts/mongo)

The intention is not to hide every database-specific capability. Instead, core-ts provides reusable abstractions for common operations while allowing applications to retain access to the capabilities they actually need.

**Product benefit:** Less repetitive persistence code, more consistent data-access patterns, and a clearer separation between business behavior and database implementation.

### 3. Validation and data integrity

Data validation is a recurring requirement across APIs, services, imports, and other application workflows.

Without shared validation mechanisms, different parts of a product may implement similar rules differently, making behavior harder to maintain.

core-ts includes validation-related libraries such as:

- `validation-core` — reusable validation functionality.
- `types-validation` — TypeScript-oriented validation utilities.

These libraries help developers establish consistent validation mechanisms and reuse them across application boundaries.

Validation remains a product-specific responsibility: the application defines which data is valid according to its requirements, while reusable libraries provide mechanisms for expressing and enforcing those rules.

**Product benefit:** Less duplicated validation logic and more consistent handling of invalid data.

### 4. Web applications and middleware

Backend products often share recurring HTTP concerns, including request handling, response construction, logging, and middleware integration.

core-ts includes libraries designed to simplify these tasks in web applications.

| Library | Responsibility |
|---|---|
| `express-web-kit` | Common Express utilities and REST-related helpers |
| `middleware-logging` | HTTP request and response logging middleware |
| `express-jsonwebtoken` | JWT verification and authentication middleware for Express |

For example, `middleware-logging` helps standardize HTTP logging across applications instead of requiring each project to implement request and response logging independently.

Repository: [core-ts/express-middleware](https://github.com/core-ts/express-middleware)

Similarly, `express-jsonwebtoken` handles JWT verification and can renew access tokens and expose authenticated user information through Express response locals.

Repository: [core-ts/express-jsonwebtoken](https://github.com/core-ts/express-jsonwebtoken)

These libraries address recurring HTTP infrastructure concerns while allowing applications to keep their own routes, controllers, and business workflows.

**Product benefit:** Less duplicated middleware code and more consistent request-processing behavior across services.

### 5. Authentication and authorization

Security is not a single operation. Authentication establishes who is making a request, while authorization determines whether that identity has permission to perform the requested action.

core-ts includes libraries for different parts of this responsibility.

| Library | Responsibility |
|---|---|
| `authentication-express` | Authentication endpoints and token-related workflows |
| `express-jsonwebtoken` | JWT verification and request authentication |
| `security-express` | Express authorization middleware |
| `authen-service` | Reusable authentication-related service logic |
| `password-service` | Password-related workflows |
| `signup-service` | User registration workflows |

These components can be combined into an application-specific security architecture.

For example, a protected API request may follow this flow:

```text
Incoming Request
       │
       ▼
JWT Authentication
       │
       ▼
Identify the User
       │
       ▼
Authorization Check
       │
       ▼
Business Use Case
       │
       ▼
Response
```

Authentication and authorization remain distinct responsibilities, and business logic can remain separate from the HTTP mechanisms used to enforce them.

**Product benefit:** Reusable security mechanisms and clearer boundaries between identity verification, access control, and business operations.

### 6. Configuration, logging, and operational support

A software product needs more than business functionality. It must also be configured, observed, diagnosed, and operated.

core-ts includes libraries for recurring operational concerns.

| Library | Responsibility |
|---|---|
| `config-plus` | Application configuration |
| `logger-core` | Shared logging functionality |
| `health-service` | Health-check functionality |
| `pg-exporter` | PostgreSQL-related exporting or metrics integration |

These capabilities help applications establish consistent operational patterns without independently rebuilding every supporting mechanism.

For example, shared logging infrastructure can make log handling more consistent across services, while configuration utilities help separate environment-specific settings from application logic.

**Product benefit:** More consistent operational behavior and less repetitive infrastructure work across development, testing, and production environments.

### 7. Data processing and integration

Not all software functionality is exposed through HTTP APIs. Products also need to import files, export data, transform records, and integrate with external systems.

core-ts includes libraries that address these scenarios.

| Library | Responsibility |
|---|---|
| `import-service` | Reusable data-import workflows |
| `export-kit` | Data-export capabilities |
| `web-clients` | Reusable web-client functionality |
| `locale-service` | Localization-related functionality |

For example, an import workflow can be composed from a data source, transformation logic, optional validation, and a writer.

```text
Source
  │
  ▼
Transform
  │
  ▼
Validate
  │
  ▼
Write
  │
  ▼
Report Results
```

The application defines what the data means and how it should be transformed. Reusable components handle the recurring mechanics of moving and processing the data.

**Product benefit:** Less duplicated integration code and more reusable data-processing workflows.

### 8. Messaging and asynchronous processing

As software products grow, they often need to process work asynchronously or communicate between services through messaging systems.

The core-ts ecosystem includes messaging and processing-related packages such as:

- `rabbitmq-transport`
- `kafka-plus`
- `message-processing`

These libraries support messaging and message-processing scenarios without requiring every application to independently implement the same integration mechanisms.

This is particularly useful when products contain background jobs, event-driven workflows, or integrations between independently deployed services.

**Product benefit:** Reusable integration mechanisms and more consistent approaches to asynchronous processing.

### 9. How the libraries work together

The real value of core-ts becomes clearer when multiple libraries are combined in a product.

Consider a typical REST API backed by PostgreSQL.

The application might use:

- `express-web-kit` for Express utilities.
- `middleware-logging` for HTTP request and response logging.
- `validation-core` for validation.
- `onecore` for common repository and use-case abstractions.
- `sql-core` for SQL data-access abstractions.
- `postgres-kit` for PostgreSQL integration.
- `logger-core` for logging.
- `config-plus` for configuration.

These libraries address separate responsibilities, but they can be composed into one application.

```text
                 HTTP Request
                      │
                      ▼
              Express Application
                      │
             Logging and Validation
                      │
                      ▼
                Controller
                      │
                      ▼
              Service / Use Case
                      │
                      ▼
                 Repository
                      │
                      ▼
                  sql-core
                      │
                      ▼
                postgres-kit
                      │
                      ▼
                  PostgreSQL
```

Configuration and logging support the application across these layers.

The business layer remains responsible for product-specific behavior, while reusable libraries handle the technical mechanisms that support it.

This architecture is demonstrated by the [PostgreSQL modular sample](https://github.com/typescript-tutorial/postgres-modular-sample), which brings several core-ts libraries together in a lightweight application.

The objective is not to force every product into exactly this structure. It is to provide reusable building blocks that make such structures easier to implement and maintain.

### 10. One ecosystem, many software products

Different products have different requirements. A small API may need only configuration, logging, validation, and database access. A larger system may additionally require authentication, authorization, messaging, and data-import workflows.

core-ts supports this variety through focused libraries that can be adopted independently and combined where appropriate.

As a result, teams can share technical foundations without having to share identical business logic or adopt an identical application architecture.

**The libraries solve recurring engineering problems. The products built on top of them deliver the business value.**

That is how core-ts turns reusable TypeScript components into a practical foundation for software product development.