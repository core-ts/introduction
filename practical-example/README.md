## A practical example: Building a REST API with core-ts

The benefits of reusable libraries become clearer when we see how they work in a real application.

Consider a common requirement: building a REST API that manages users.

The API must support:

- Creating a user
- Retrieving users
- Searching with filters, pagination, and sorting
- Updating and deleting users
- Validating input
- Logging HTTP requests
- Connecting to a database
- Handling errors consistently

These requirements are not unique to one product. Many business applications need similar capabilities, even though their business entities and rules differ.

Let's see how core-ts can help implement this functionality.

### 1. Start with the product requirement

Suppose we are building a user-management service with Express, TypeScript, and PostgreSQL.

A user has the following properties:

```typescript
interface User {
  id: string;
  username: string;
  email: string;
  phone: string;
  dateOfBirth: Date;
}
```

The business requirements are straightforward. The application must allow clients to manage users through a REST API.

Without reusable abstractions, developers must implement or assemble the mechanics of CRUD operations, repository methods, filtering, pagination, validation, and HTTP handling for each project.

The challenge is not that these operations are difficult to write once. It is that similar implementations tend to recur across products, creating more code to test and maintain.

### 2. Organize the application into clear responsibilities

With core-ts, we can organize the feature into separate components.

```text
src/
├── app.ts
├── config.ts
├── context.ts
├── route.ts
└── user/
    ├── user.ts
    ├── repository.ts
    ├── service.ts
    ├── controller.ts
    └── index.ts
```

Each component has a clear purpose:

| Component | Responsibility |
|---|---|
| `user.ts` | Defines the user model and its validation rules |
| `repository.ts` | Connects user data operations to persistence |
| `service.ts` | Exposes the use cases required by the application |
| `controller.ts` | Handles HTTP requests and responses |
| `context.ts` | Creates and connects application dependencies |
| `route.ts` | Registers API routes |

This structure makes the responsibilities visible without requiring a large framework or a dependency injection container.

The application controls how its components are connected, while core-ts provides reusable abstractions for the recurring implementation work.

### 3. Reuse generic repository functionality

A repository typically needs to support operations such as:

- Finding a record by ID
- Retrieving records
- Searching with filters
- Creating records
- Updating records
- Deleting records

A conventional implementation may repeat similar methods for users, customers, products, orders, and other entities.

core-ts addresses this repetition through generic repository abstractions.

In the core-ts PostgreSQL modular sample, the user repository follows this pattern:

```typescript
export class SqlUserRepository
  extends Repository<User, string, UserFilter> {
  // Application-specific repository configuration
}
```

This illustrates the key idea: the repository builds on reusable SQL repository functionality instead of independently implementing every common data-access operation.

The generic parameters describe the entity type, identifier type, and filter type. The exact implementation and constructor configuration depend on the underlying core-ts package APIs.

When the product requires custom queries or specialized persistence behavior, the repository can be extended accordingly.

**What this means for the product**

Suppose we add a `Customer` entity. Much of its standard persistence behavior may already be covered by the same reusable abstractions.

The team can focus on the customer-specific model, filters, and queries rather than recreating the entire repository mechanism.

The same principle applies when adding products, invoices, or other business entities.

### 4. Reuse CRUD use-case functionality

Repositories address persistence. Applications also need a service or use-case layer that exposes operations to the rest of the application.

For many entities, the basic CRUD workflows are similar even though the data models differ.

The core-ts sample demonstrates generic use-case reuse through this declaration:

```typescript
export class UserUseCase
  extends UseCase<User, string, UserFilter>
  implements UserService {
  // Application-specific business behavior
}
```

The `UseCase` abstraction provides a reusable foundation for common operations. The application-specific class connects that functionality to the user feature and can introduce additional business rules where required.

This distinction is important.

Generic CRUD behavior can be reused, but business requirements should remain explicit. For example, an application may need to prevent deletion of a user who owns active orders. That rule belongs in the appropriate business workflow, not in a generic repository merely because the repository supports deletion.

**What this means for the product**

The team can implement standard data-management functionality quickly while preserving the ability to express business rules that distinguish one product from another.

### 5. Keep HTTP handling separate from business behavior

The controller receives HTTP requests, validates input, invokes the relevant service, and produces responses.

A simplified request flow looks like this:

```text
POST /users
     │
     ▼
Express Middleware
     │
     ├── Request Logging
     │
     ▼
Controller
     │
     ├── Validate Input
     │
     ▼
User Use Case
     │
     ▼
User Repository
     │
     ▼
SQL Abstraction
     │
     ▼
PostgreSQL
```

Each component has a defined responsibility.

The controller should not need to contain SQL queries. The repository should not need to interpret HTTP headers. The business layer should not need to know how Express dispatches requests.

This separation makes it easier to modify one part of the application without unnecessarily affecting the others.

For example, changing an API response format should primarily affect the HTTP-facing layer. Changing the persistence implementation should primarily affect the repository and database integration layers.

### 6. Centralize validation rules

Input validation is another area where duplication can accumulate.

Consider a request to create a user:

```json
{
  "id": "wolverine",
  "username": "james.howlett",
  "email": "james.howlett",
  "phone": "0987654321a",
  "dateOfBirth": "1974-11-16T16:59:59.999Z"
}
```

The email and phone values are invalid according to the application's rules.

With the core-ts validation approach demonstrated in the sample, validation rules can be associated with the model and reused instead of being scattered across individual controllers.

For example, the application can define rules for required fields, email formats, string lengths, and phone-number patterns.

The validation library handles the reusable validation mechanism, while the product defines which rules its data must satisfy.

The application can also return structured validation errors that identify the affected fields and error codes.

**What this means for the product**

When validation rules need to change, developers have a consistent place to maintain them. The same rules can be applied across relevant application workflows rather than reimplemented in every endpoint.

### 7. Reuse logging and configuration

A working API also needs operational infrastructure.

The application can use `middleware-logging` to log HTTP requests and responses, `logger-core` for shared logging functionality, and `config-plus` to manage configuration.

For example, the database connection and server settings should not be hard-coded separately into individual modules.

Instead, configuration can be managed centrally and supplied according to the deployment environment.

```text
Default Configuration
          │
          ▼
Environment Configuration
          │
          ▼
Environment Variables
          │
          ▼
Application Configuration
```

Similarly, request logging can be configured through shared middleware instead of implementing a different logging mechanism for every route.

These concerns are essential to operating a product, but they rarely differentiate its business functionality.

**What this means for the product**

The team can establish consistent operational practices without repeatedly rebuilding the same supporting mechanisms for every new service.

### 8. Support searching, pagination, and sorting

Real applications rarely stop at basic CRUD operations.

A user-management screen may need to search by email, filter by date of birth, paginate through results, and sort by multiple fields.

For example, a client might send:

```json
{
  "page": 1,
  "limit": 20,
  "sort": "phone,-id",
  "email": "tony"
}
```

The API can interpret this as a request for the first page of matching users, sorted by phone in ascending order and ID in descending order.

The core-ts SQL sample demonstrates reusable search and pagination capabilities, including multiple filters and sorting criteria.

This is a valuable example of the difference between generic infrastructure and product-specific behavior.

The search mechanism can be reused, while each product defines the fields and filtering rules relevant to its entities.

### 9. What changes when we build another product?

Now imagine the same team needs to build an inventory-management product.

The product introduces a new entity:

```typescript
interface Product {
  id: string;
  name: string;
  price: number;
  quantity: number;
}
```

The business requirements differ from user management, but many technical concerns remain the same:

- Repository operations
- CRUD use cases
- Validation
- Logging
- Configuration
- Database integration
- HTTP request handling

Without a reusable foundation, developers may reproduce much of the original infrastructure.

With core-ts, they can build on the same abstractions and shared libraries while implementing the inventory-specific behavior.

The architecture can remain familiar even though the business model is different.

This is where the ecosystem creates value beyond a single project: **a solution developed for one product can help accelerate the development of subsequent products.**

### 10. The difference is more than fewer lines of code

The purpose of core-ts is not to make every implementation as short as possible.

A highly compressed implementation can be difficult to understand, and excessive abstraction can make simple functionality unnecessarily complicated.

The goal is to reuse recurring mechanisms while keeping business behavior explicit.

| Without shared building blocks | With core-ts |
|---|---|
| Common repository behavior is repeatedly implemented | Generic repository functionality can be reused |
| Similar CRUD workflows are independently developed | Generic use cases provide a reusable foundation |
| Validation mechanisms are repeatedly assembled | Validation capabilities can be shared |
| HTTP logging is implemented separately in projects | Shared middleware provides common logging behavior |
| Each application establishes its own technical conventions | Projects can build on consistent patterns |
| Improvements are often repeated across codebases | Shared improvements can benefit multiple consumers |

These benefits depend on how the libraries are adopted and maintained. Reuse does not automatically guarantee better architecture; the abstractions must remain appropriate to the problem.

When applied well, however, core-ts can reduce repetitive implementation work without sacrificing control over the product.

### 11. See the complete example

The [core-ts PostgreSQL modular sample](https://github.com/typescript-tutorial/postgres-modular-sample) demonstrates these ideas in a working TypeScript and Express application.

It combines:

- `onecore` for generic CRUD use cases and common abstractions
- `sql-core` for SQL repository abstractions
- `postgres-kit` for PostgreSQL integration
- `validation-core` and `types-validation` for validation
- `middleware-logging` and `logger-core` for logging
- `config-plus` for configuration

The example also demonstrates explicit dependency composition, request processing, health checks, and a modular feature structure.

Rather than introducing a large framework, it shows how focused libraries can work together to form a complete application.

Explore the sample to see how the individual abstractions are used in actual source code.

## The takeaway

A software product needs both business-specific functionality and the technical foundation that supports it.

core-ts focuses on making recurring technical capabilities reusable, so developers can spend more time implementing product requirements and less time rebuilding common infrastructure.

When the same approach is applied across multiple products, the benefits can compound: familiar architecture, less duplicated code, shared improvements, and a more sustainable development process.

**Build the product, not the same infrastructure over and over again.**