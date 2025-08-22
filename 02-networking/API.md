# API

### What is API?
An API `(Application Programming Interface)` is a **contract and messenger** between two software systems. One side asks for something, the other side does the work and replies. You only need to know how to ask and what to expect back — you do not need to know the inner implementation.

Examples:  
Ex-1: 
Imagine a restaurant:  
- The **menu** lists dishes you can order (the API contract).  
- You tell the **waiter** what you want (the request).  
- The **kitchen** prepares the food (the service).  
- The waiter brings the dish back (the response).  

Ex-2 : 
- In the same way, when a mobile weather app shows the forecast, it calls a Weather API like `GET /forecast?city=Hyderabad`. The API returns structured data; the app displays it without calculating weather itself.  

Ex-3,4,5 : 
- When a merchant charges a card, they call Stripe’s `POST /charge` API — Stripe handles the sensitive card handling and returns success or failure.

- A travel app calls Maps API (`GET /route?from=A&to=B`) to get ETA and route. The app doesn’t compute routes itself; it uses the API result.  
- A ride-hailing app uses a Pricing API to compute fare; the app shows the fare and books the ride.  
- A backend batch job calls a Payments API to reconcile transactions and avoid duplicate charges.

APIs let systems integrate, reuse capabilities, and remain decoupled: callers rely only on the API surface, not the implementation details.

## Why do we need API?
- **Separation of concerns:** hide implementation behind a stable contract so teams can work independently.  
- **Reusability:** multiple clients (web, mobile, batch) reuse the same service.  
- **Interoperability:** different languages, platforms, or companies can integrate through standard contracts.  
- **Security & governance:** authentication, authorization, rate-limiting, and validation are enforced at the API boundary.  
- **Scalability & ops:** APIs enable gateways, CDNs, caches, and independent scaling.  
- **Faster delivery:** frontends and partners can move forward against a stable API while the backend evolves.

Without clear APIs, systems become monolithic, fragile, and hard to change.

## How does it work?
### End-to-end (simple synchronous request/response)
1. Client builds a request (method, path, headers, body).  
2. Client authenticates (API key, OAuth token).  
3. Request hits the edge (load balancer → API gateway).  
4. Gateway enforces cross-cutting rules (auth, rate limits, routing, telemetry).  
5. Gateway routes to the correct service instance (service discovery / DNS).  
6. Service validates input and runs business logic.  
7. Service reads/writes storage and builds a response.  
8. Service returns response through gateway to client.  
9. Observability records traces, metrics, and logs.

### API styles and deep sub-topic explanations (with examples)

#### REST (HTTP + JSON)
REST exposes resources at URLs and uses HTTP verbs (GET, POST, PUT, DELETE).
- Example: `GET /users/123` returns user data.  
- Real-world scenario: a news website provides `GET /articles` so mobile apps load headlines.  
- When to use: public web APIs, CRUD operations, cacheable endpoints.  
- Trade-offs: human-friendly and cacheable; may be chatty and needs schema (OpenAPI) for strong contracts.

#### gRPC (Protocol Buffers + HTTP/2)
gRPC defines service methods with protobuf and runs over HTTP/2.
- Example: `rpc GetUser(GetUserRequest) returns (GetUserResponse)`.  
- Real-world scenario: microservices in a backend (auth, user, billing) use gRPC for low-latency calls.  
- When to use: internal RPC, streaming, high throughput.  
- Trade-offs: fast and typed, requires client libraries, less browser-friendly.

#### GraphQL
GraphQL lets clients request exactly the fields they need in one endpoint.
- Example: `{ user(id:"123"){name, posts{title}} }`.  
- Real-world scenario: a mobile app fetches user profile + recent posts in a single request, avoiding multiple round-trips.  
- When to use: flexible UIs that would otherwise over/under-fetch.  
- Trade-offs: reduces round trips; caching and complexity can be harder to manage.

#### WebSockets / Streaming
Long-lived bidirectional connection for real-time updates.
- Real-world scenario: chat apps, live dashboards, multiplayer games use WebSockets to push updates instantly.  
- Trade-offs: good for real-time; connection management and scale are complex.

#### Webhooks (Server-to-Server Callbacks)
Server calls a client-provided URL when events happen.
- Real-world scenario: payment gateways POST to your `/webhook/order-paid` when payment clears.  
- Trade-offs: near-real-time push, but consumers must secure endpoints and handle retries.

#### Async / Batch APIs (Queue-Based)
Client enqueues work and receives a job id; worker processes later.
- Real-world scenario: image/video processing — upload returns `202 Accepted` + jobId; worker processes and posts result or calls webhook.  
- Trade-offs: decouples long tasks, increases durability; requires status tracking and eventual consistency.

### Operational patterns and best practices
- **Contracts & schemas:** OpenAPI, Protobuf — define request/response shapes.  
- **Versioning:** `/v1/` or header-based to evolve without breaking clients.  
- **Idempotency:** use request ids for operations like `POST /charge` so retries don’t duplicate effects.  
- **Timeouts & retries:** clients set reasonable timeouts and exponential backoff with jitter to avoid thundering herd.  
- **Rate limiting & quotas:** protect backends and enforce fair usage.  
- **Security:** TLS, OAuth/OIDC, API keys, scopes.  
- **Observability:** distributed tracing, endpoint metrics, structured logs.

### Relation to System Engineering / System Design

APIs are the boundary and contract of system design. They determine:

- Ownership & boundaries: which team owns which service and data.

- Scaling model: stateless APIs scale differently from stateful backends.

- SLOs & SLIs: endpoint-level latency and error budgets.

- Security model: where auth/authorization is enforced.

- Change management: how versioning and deprecation are handled.
Good API design directly impacts maintainability, security, and scalability.

### Before API vs After API

`Before:` tightly coupled systems, brittle integrations, fragile deployments, no clear contract.

`After:` clear contracts, independent deployments, standardized security/rate-limits, easier testing and automation.

### Main Motive / Goal

Provide a stable, discoverable, and secure interface so different systems can interact predictably, enabling modularity, reuse, and independent evolution.

### Advantages

- Decouples services and clients.

- Enables polyglot systems (different languages/tech stacks).

- Centralizes auth, rate-limiting, logging, and telemetry.

- Facilitates testing, automation, and CI/CD.

- Scales via gateways, caches, and edge routing.

### Disadvantages

- Bad API design creates long-term technical debt.

- Versioning/backward compatibility adds complexity.

- Operational cost for gateways, monitoring, and security.

- Potential attack surface if not hardened.

- Can be chatty or inefficient if poorly designed.

### Interview Q&A

**Q1 — REST vs gRPC: when to choose which?**  
A1: REST is human-readable and cache-friendly — great for public web APIs. gRPC is binary, strongly-typed, and low-latency — ideal for internal service-to-service RPC and streaming.

**Q2 — How do you make APIs idempotent and why?**  
A2: Use idempotency keys (client-generated request IDs) stored with results so retries return the same outcome. Critical for payments and order creation to avoid duplicates.

**Q3 — How to version an API without breaking clients?**  
A3: Use URL versioning (/v1/), header-based versioning, or additive non-breaking changes; provide deprecation timelines and migration docs.

**Q4 — How to secure public APIs?**  
A4: Use TLS, OAuth/OIDC or API keys, scopes/roles, rate limiting, input validation, logging, and WAF. Enforce auth at the gateway and follow least-privilege.

**Q5 — How to handle long-running tasks?**  
A5: Return 202 Accepted with job id; process via durable queues; notify with webhook or let client poll for completion.

**Q6 — How do API gateways help at scale?**   
A6: Gateways centralize cross-cutting concerns (auth, rate-limiting, TLS termination, routing, observability, canary routing), simplifying backends and protecting them.

**Q7 — How to monitor APIs end-to-end?**  
A7: Use distributed tracing (propagate trace IDs), collect latency/error metrics per endpoint, set SLOs and alerts, and capture sufficient request/response metadata for debugging.

### Key Takeaways

- APIs are the contract layer that enables systems to interact without exposing internals.

Pick the API style by use-case: REST (public), gRPC (internal/low latency), GraphQL (flexible client queries), WebSocket (real-time).

- Design for robustness: authentication, rate-limiting, timeouts, idempotency, versioning, and observability.

- Good API design enables independent teams, safer evolution, and scalable operations.

### Navigation