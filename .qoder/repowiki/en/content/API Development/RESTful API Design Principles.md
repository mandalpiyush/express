# RESTful API Design Principles

<cite>
**Referenced Files in This Document**
- [index.js](file://examples/resource/index.js)
- [index.js](file://examples/web-service/index.js)
- [index.js](file://examples/multi-router/controllers/api_v1.js)
- [index.js](file://examples/multi-router/controllers/api_v2.js)
- [index.js](file://examples/route-separation/index.js)
- [index.js](file://examples/route-separation/user.js)
- [index.js](file://examples/route-separation/post.js)
- [index.js](file://examples/content-negotiation/index.js)
- [index.js](file://examples/error-pages/index.js)
- [index.js](file://examples/error/index.js)
- [index.js](file://examples/params/index.js)
- [index.js](file://examples/mvc/index.js)
- [index.js](file://examples/mvc/db.js)
- [index.js](file://examples/mvc/controllers/user/index.js)
- [index.js](file://examples/mvc/controllers/pet/index.js)
- [index.js](file://examples/mvc/controllers/main/index.js)
- [express.js](file://lib/express.js)
- [application.js](file://lib/application.js)
</cite>

## Table of Contents
1. [Introduction](#introduction)
2. [Project Structure](#project-structure)
3. [Core Components](#core-components)
4. [Architecture Overview](#architecture-overview)
5. [Detailed Component Analysis](#detailed-component-analysis)
6. [Dependency Analysis](#dependency-analysis)
7. [Performance Considerations](#performance-considerations)
8. [Troubleshooting Guide](#troubleshooting-guide)
9. [Conclusion](#conclusion)

## Introduction
This document presents RESTful API design principles implemented in Express.js using concrete examples from the repository. It covers resource-based URL design, HTTP method semantics (GET, POST, PUT, DELETE), proper resource naming, REST architectural constraints (statelessness, cacheability, layered system, uniform interface), resource representation patterns, URI design best practices, hierarchical resource organization, HTTP status code usage, error handling, idempotency, and discoverability via hypermedia controls.

## Project Structure
The repository organizes examples by concept: resource routing, web service scaffolding, multi-version APIs, route separation, content negotiation, error handling, parameter parsing, and MVC-style controllers. These demonstrate how to structure routes and controllers to align with REST constraints and best practices.

```mermaid
graph TB
subgraph "Examples"
R["Resource Routing<br/>examples/resource/index.js"]
WS["Web Service<br/>examples/web-service/index.js"]
MR["Multi-Router<br/>examples/multi-router/controllers/*.js"]
RS["Route Separation<br/>examples/route-separation/index.js"]
CN["Content Negotiation<br/>examples/content-negotiation/index.js"]
EP["Error Pages<br/>examples/error-pages/index.js"]
ERR["Error Handling<br/>examples/error/index.js"]
PM["Params & Validation<br/>examples/params/index.js"]
MVC["MVC Controllers<br/>examples/mvc/index.js"]
end
subgraph "Core Library"
EXP["express.js"]
APP["application.js"]
end
R --> EXP
WS --> EXP
MR --> EXP
RS --> EXP
CN --> EXP
EP --> EXP
ERR --> EXP
PM --> EXP
MVC --> EXP
EXP --> APP
```

**Diagram sources**
- [express.js:1-82](file://lib/express.js#L1-L82)
- [application.js:1-632](file://lib/application.js#L1-L632)
- [index.js](file://examples/resource/index.js)
- [index.js](file://examples/web-service/index.js)
- [index.js](file://examples/multi-router/controllers/api_v1.js)
- [index.js](file://examples/multi-router/controllers/api_v2.js)
- [index.js](file://examples/route-separation/index.js)
- [index.js](file://examples/content-negotiation/index.js)
- [index.js](file://examples/error-pages/index.js)
- [index.js](file://examples/error/index.js)
- [index.js](file://examples/params/index.js)
- [index.js](file://examples/mvc/index.js)

**Section sources**
- [express.js:1-82](file://lib/express.js#L1-L82)
- [application.js:1-632](file://lib/application.js#L1-L632)

## Core Components
- Resource-based routing: Demonstrated by a custom resource method and built-in HTTP verbs to model collections and individual resources.
- Content negotiation: Using Accept headers to deliver appropriate representations.
- Error handling: Centralized error middleware and explicit status codes for client and server errors.
- Parameter parsing and validation: Using app.param to convert and validate path parameters.
- Multi-version APIs: Organizing routes under versioned namespaces.
- Route separation: Splitting concerns across modules for maintainability.
- MVC-style controllers: Structuring request handling with before hooks and CRUD actions.

**Section sources**
- [index.js](file://examples/resource/index.js)
- [index.js](file://examples/web-service/index.js)
- [index.js](file://examples/content-negotiation/index.js)
- [index.js](file://examples/error-pages/index.js)
- [index.js](file://examples/error/index.js)
- [index.js](file://examples/params/index.js)
- [index.js](file://examples/multi-router/controllers/api_v1.js)
- [index.js](file://examples/multi-router/controllers/api_v2.js)
- [index.js](file://examples/route-separation/index.js)
- [index.js](file://examples/mvc/index.js)

## Architecture Overview
The examples illustrate a layered approach:
- Application bootstrap and middleware setup
- Versioned API routing
- Resource routing with HTTP verbs
- Content negotiation and error handling
- Parameter parsing and validation
- MVC-style controller pattern

```mermaid
graph TB
Client["Client"]
App["Express App<br/>lib/express.js + lib/application.js"]
MW1["Middleware<br/>Body Parser, Method Override"]
APIv1["API v1 Router<br/>controllers/api_v1.js"]
APIv2["API v2 Router<br/>controllers/api_v2.js"]
Res["Resource Routes<br/>examples/resource/index.js"]
Sep["Route Separation<br/>examples/route-separation/index.js"]
MVC["MVC Controllers<br/>examples/mvc/index.js"]
Client --> App
App --> MW1
App --> APIv1
App --> APIv2
App --> Res
App --> Sep
App --> MVC
```

**Diagram sources**
- [express.js:1-82](file://lib/express.js#L1-L82)
- [application.js:1-632](file://lib/application.js#L1-L632)
- [index.js](file://examples/multi-router/controllers/api_v1.js)
- [index.js](file://examples/multi-router/controllers/api_v2.js)
- [index.js](file://examples/resource/index.js)
- [index.js](file://examples/route-separation/index.js)
- [index.js](file://examples/mvc/index.js)

## Detailed Component Analysis

### Resource-Based URL Design and HTTP Methods
- Collections and individual resources:
  - GET /users lists all users.
  - GET /users/:id retrieves a specific user.
  - DELETE /users/:id removes a user.
  - Range queries and format negotiation are supported via custom routes.
- Idempotency:
  - GET, PUT, DELETE are designed to be idempotent; repeated identical requests should have the same effect.
  - POST is intentionally non-idempotent for resource creation.

```mermaid
sequenceDiagram
participant C as "Client"
participant A as "App Router"
participant U as "User Controller"
C->>A : "GET /users"
A->>U : "index(req,res)"
U-->>A : "200 OK + users"
A-->>C : "Response"
C->>A : "GET /users/1"
A->>U : "show(req,res)"
U-->>A : "200 OK + user or error"
A-->>C : "Response"
C->>A : "DELETE /users/1"
A->>U : "destroy(req,res,id)"
U-->>A : "200 OK + result"
A-->>C : "Response"
```

**Diagram sources**
- [index.js](file://examples/resource/index.js)

**Section sources**
- [index.js](file://examples/resource/index.js)

### URI Design Best Practices and Hierarchical Organization
- Use plural nouns for resource collections (/users).
- Use hierarchical paths for relationships (/api/user/:name/repos).
- Keep URIs stable and versioned (/api/v1, /api/v2).
- Use query parameters for filtering/sorting when appropriate.

```mermaid
flowchart TD
Start(["Incoming Request"]) --> CheckVersion["Check API Version Path"]
CheckVersion --> |"/api/v1"| V1Routes["Route to API v1"]
CheckVersion --> |"/api/v2"| V2Routes["Route to API v2"]
CheckVersion --> |Other| Collection["Collection or Individual Resource"]
V1Routes --> End(["Dispatch Handler"])
V2Routes --> End
Collection --> End
```

**Diagram sources**
- [index.js](file://examples/multi-router/controllers/api_v1.js)
- [index.js](file://examples/multi-router/controllers/api_v2.js)
- [index.js](file://examples/web-service/index.js)

**Section sources**
- [index.js](file://examples/multi-router/controllers/api_v1.js)
- [index.js](file://examples/multi-router/controllers/api_v2.js)
- [index.js](file://examples/web-service/index.js)

### Resource Representation Patterns and Content Negotiation
- Support multiple representations (JSON, HTML) using content negotiation.
- Use Accept headers to choose the appropriate formatter.

```mermaid
sequenceDiagram
participant C as "Client"
participant A as "App"
participant H as "Handler"
C->>A : "GET /?format=json"
A->>H : "index(req,res)"
H-->>A : "res.json(...) or res.format(...)"
A-->>C : "200 OK + JSON"
```

**Diagram sources**
- [index.js](file://examples/content-negotiation/index.js)

**Section sources**
- [index.js](file://examples/content-negotiation/index.js)

### HTTP Status Codes and Proper Error Responses
- Use explicit status codes for client errors (400, 401, 404) and server errors (500).
- Centralize error handling with error-handling middleware.
- Return structured error payloads.

```mermaid
sequenceDiagram
participant C as "Client"
participant A as "App"
participant M as "Middleware"
participant E as "ErrorHandler"
C->>A : "Request"
A->>M : "Validate API key"
M-->>A : "next(error with status)"
A->>E : "Pass error to error handler"
E-->>C : "401/400/404 or 500 + error payload"
```

**Diagram sources**
- [index.js](file://examples/web-service/index.js)
- [index.js](file://examples/error-pages/index.js)
- [index.js](file://examples/error/index.js)

**Section sources**
- [index.js](file://examples/web-service/index.js)
- [index.js](file://examples/error-pages/index.js)
- [index.js](file://examples/error/index.js)

### Idempotent vs Non-Idempotent Operations
- Idempotent:
  - GET: Retrieve resource(s)
  - PUT/PATCH: Replace/update resource
  - DELETE: Remove resource
- Non-idempotent:
  - POST: Create new resource

```mermaid
flowchart TD
Op["Operation Type"] --> GET["GET<br/>Idempotent"]
Op --> PUT["PUT/PATCH<br/>Idempotent"]
Op --> DEL["DELETE<br/>Idempotent"]
Op --> POST["POST<br/>Non-idempotent"]
```

**Diagram sources**
- [index.js](file://examples/resource/index.js)
- [index.js](file://examples/route-separation/index.js)

**Section sources**
- [index.js](file://examples/resource/index.js)
- [index.js](file://examples/route-separation/index.js)

### Resource Linking and Hypermedia Controls
- Build discoverable APIs by returning links to related resources.
- Use hierarchical routes to imply relationships (/api/user/:name/repos).
- Provide navigable entry points and consistent link formats.

```mermaid
graph LR
Users["/api/users"] --> Repo["/api/repos"]
Users --> UR["/api/user/:name/repos"]
Repo --> UR
```

**Diagram sources**
- [index.js](file://examples/web-service/index.js)

**Section sources**
- [index.js](file://examples/web-service/index.js)

### Parameter Parsing, Validation, and Typed Conversion
- Use app.param to convert and validate parameters (e.g., integers, user lookup).
- Return appropriate errors (400/404) for invalid inputs.

```mermaid
flowchart TD
P["Path Parameter"] --> Conv["Convert to Integer"]
Conv --> Valid{"Valid Number?"}
Valid --> |No| Err400["400 Bad Request"]
Valid --> |Yes| Lookup["Lookup User"]
Lookup --> Found{"Found?"}
Found --> |No| Err404["404 Not Found"]
Found --> |Yes| Next["Call Next Handler"]
```

**Diagram sources**
- [index.js](file://examples/params/index.js)

**Section sources**
- [index.js](file://examples/params/index.js)

### Route Separation and MVC Controllers
- Separate concerns by organizing routes and controllers.
- Use before hooks to load resources and enforce presence checks.
- Apply CRUD actions consistently across controllers.

```mermaid
sequenceDiagram
participant C as "Client"
participant R as "Router"
participant L as "Loader (before)"
participant Ctrl as "Controller"
C->>R : "GET /user/ : id/edit"
R->>L : "load(req,res,next)"
L-->>R : "next(err or continue)"
R->>Ctrl : "edit(req,res)"
Ctrl-->>C : "200 OK + rendered view"
```

**Diagram sources**
- [index.js](file://examples/route-separation/index.js)
- [index.js](file://examples/route-separation/user.js)

**Section sources**
- [index.js](file://examples/route-separation/index.js)
- [index.js](file://examples/route-separation/user.js)
- [index.js](file://examples/mvc/controllers/user/index.js)
- [index.js](file://examples/mvc/controllers/pet/index.js)
- [index.js](file://examples/mvc/controllers/main/index.js)
- [index.js](file://examples/mvc/db.js)
- [index.js](file://examples/mvc/index.js)

## Dependency Analysis
Express exposes application, request, and response prototypes and integrates middleware and routers. The examples rely on these abstractions to implement RESTful patterns.

```mermaid
graph TB
EXP["express.js"]
APP["application.js"]
Rsrc["examples/resource/index.js"]
WSvc["examples/web-service/index.js"]
MVCI["examples/mvc/index.js"]
EXP --> APP
Rsrc --> EXP
WSvc --> EXP
MVCI --> EXP
```

**Diagram sources**
- [express.js:1-82](file://lib/express.js#L1-L82)
- [application.js:1-632](file://lib/application.js#L1-L632)
- [index.js](file://examples/resource/index.js)
- [index.js](file://examples/web-service/index.js)
- [index.js](file://examples/mvc/index.js)

**Section sources**
- [express.js:1-82](file://lib/express.js#L1-L82)
- [application.js:1-632](file://lib/application.js#L1-L632)

## Performance Considerations
- Prefer lightweight representations (JSON) for machine-to-machine communication.
- Use caching headers and ETags where applicable.
- Minimize synchronous operations in middleware and route handlers.
- Keep route handlers small and delegate to services or controllers.

## Troubleshooting Guide
- 404 Not Found: Ensure routes match the intended path and method; verify middleware ordering.
- 400 Bad Request: Validate inputs using app.param and return structured errors.
- 401 Unauthorized: Confirm API key middleware is mounted and keys are provided.
- 500 Internal Server Error: Wrap errors and ensure error-handling middleware is registered last.

**Section sources**
- [index.js](file://examples/error-pages/index.js)
- [index.js](file://examples/error/index.js)
- [index.js](file://examples/web-service/index.js)
- [index.js](file://examples/params/index.js)

## Conclusion
The repository demonstrates RESTful API design in Express.js through practical examples: resource-based routing, HTTP method semantics, content negotiation, error handling, parameter validation, and MVC-style controllers. By following these patterns—URI design, idempotency, layered systems, and uniform interfaces—you can build scalable, maintainable, and discoverable APIs.