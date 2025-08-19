
### Rate Limiting → **networking**
Rate limiting is a traffic-control mechanism at the network/API boundary. It belongs under **Networking** because it sits with load balancing, DNS, L4/L7, and API gateway concerns.

**Short contents to include in the file**
- Rate-limiting goals (protect upstream, fairness, quota enforcement).
- Algorithms: token bucket, leaky bucket, fixed window, sliding window.
- Placement: client-side, API-gateway, service-side, downstream protection.
- UX patterns: 429 responses, retry-after headers, backoff guidance.
- Real example: panipuri/20-plates and ChatGPT tokens case.
- Instrumentation: rate-limited request count, throttled-user ratio, rate-limit errors p99.


# Rate Limiting & Throttling 

**Rate limiting** is the technique of controlling how many requests (or how much work) a user/client/application can do in a defined time window. **Throttling** is the act of slowing or rejecting requests when limits are exceeded.

**Why:** to prevent overload, ensure fairness, protect downstream services, and control costs.

---

## 1) Business goals & UX trade-offs
- **Protect services** from traffic spikes (avoid cascading failures).  
- **Fairness:** prevent a single user from consuming all resources.  
- **Monetization & quotas:** enforce paid tiers vs free tiers.  
- **Abuse prevention:** mitigate DoS or automated scraping.

**UX trade-offs:** strict limits improve reliability but can frustrate users; provide helpful responses (`429`, `Retry-After`) and graceful degradation.

---

## 3) Common algorithms (how they work)

### 3.1 Fixed Window
- Count requests per fixed time bucket (e.g., 1000 requests per minute).
- Simple but suffers from burstiness at window boundaries.

### 3.2 Sliding Window (counter or log)
- More precise than fixed window; maintains sliding window counters or timestamps.

### 3.3 Token Bucket (popular)
- Tokens are added at a steady rate; each request consumes a token.
- Allows short bursts up to bucket capacity, then enforces sustained rate.

### 3.4 Leaky Bucket
- Requests enter a queue and are processed at a fixed rate; excessive arrivals overflow/dropped.
- Enforces steady output rate.

### 3.5 Rate-limiting by quota & tiering
- Per-user, per-IP, per-API-key, per-tenant, or global limits.
- Tiered quotas for free vs paid users.

---

## 4) Where to enforce rate limits (placement)
- **Client-side:** polite throttling to reduce retries.  
- **Edge / CDN / API Gateway:** first line of defense (scales well).  
- **Service-side:** protect business-critical endpoints (authoritative).  
- **Downstream protection:** prevent DB or payment gateway overload.

Best practice: enforce limits at multiple layers (edge + service) with consistent semantics.

---

## 5) Implementation details & patterns

### Key design elements
- **Keying:** decide the key (user ID, API key, IP, tenant).  
- **Window size & burst allowance:** window duration, token refill rate, bucket size.  
- **Distributed counters:** use Redis (INCR + TTL), token buckets in Redis, or specialized rate-limit services.  
- **Sliding window with approximate storage:** use time buckets to approximate sliding windows for scale.

### Example: Token bucket pseudo-code (single-node)
```text
state: tokens, last_refill_time
on request:
  now = current_time()
  tokens += (now - last_refill_time) * rate
  tokens = min(tokens, capacity)
  if tokens >= 1:
    tokens -= 1
    allow request
  else:
    reject (429) with Retry-After
  last_refill_time = now
```

### API & Communication Types (Sync / Async) → **networking**
APIs and communication modes are core networking topics: HTTP/gRPC/websockets and the decision between synchronous and asynchronous flows. Put this under **Networking** so readers learn protocols, request/response semantics, and when to use async messaging.

**Short contents to include in the file**
- API definition and role (contract between services / external third parties).
- Protocols overview (HTTP, gRPC, REST, GraphQL, WebSocket) — short pros/cons.
- Synchronous vs asynchronous explained with user examples (phone call vs WhatsApp):  
  - Synchronous examples: request→response (login, checkout validation).  
  - Asynchronous examples: background jobs, email sending, enrichment pipelines.
- Integration patterns: request-reply, fire-and-forget, callback/webhook, message queues, event streaming.
- When to use which and how this relates to reliability & rate limiting.



# API & Communication Types (Synchronous vs Asynchronous) — Deep Dive

**Repo placement:** `networking/01-api-and-communication-types.md` — File #1 in Networking

---

## 1) Plain-English definition: What is an API?
An **API (Application Programming Interface)** is a contract that allows two pieces of software to communicate. It defines how requests are formed, what responses look like, and what behaviors clients can expect.

APIs are the building blocks of service-to-service communication and of exposing product functionality to third parties.

---

## 2) Why API design & communication modes matter
- They determine **latency**, **reliability**, and **complexity** of integrations.  
- Choosing sync vs async affects user experience, operational behavior, and failure modes.  
- Good API design improves developer experience and reduces bugs.

---

## 3) Synchronous communication (what + when to use)

### Definition
Client sends a request and **waits** for a response in the same thread/context.

### Common protocols
- HTTP/HTTPS (REST), gRPC (HTTP/2), WebSocket (bidirectional, often for real-time streaming).

### Use-cases
- Login/authentication, real-time validation, checkout confirmation, user requests that require immediate result.

### Pros
- Simpler mental model, immediate response, easy to reason about causal flow.

### Cons
- Tightly-coupled latency; downstream slowness directly impacts client.  
- More fragile under network instability unless mitigated with timeouts/retries.

### Best practices
- Set timeouts and retries with exponential backoff + jitter.  
- Make operations **idempotent** where possible.  
- Use circuit breakers and bulkheads.  
- Keep responses small and fast for good SLAs.

---

## 4) Asynchronous communication (what + when to use)

### Definition
Client sends a request (or an event) and does **not wait** synchronously for processing to complete. Processing happens in the background; client may receive notification later (webhook, event, polling).

### Common patterns
- Message queues (RabbitMQ, SQS), event streams (Kafka), background workers, webhooks, SSE (Server-Sent Events) for notifications.

### Use-cases
- Email sending, image/video processing, analytics ingestion, long-running tasks, decoupled microservice workflows.

### Pros
- Decouples sender & receiver, improves resilience and throughput, better for bursty workloads.

### Cons
- More complex: needs durable queues, retry logic, and reconciliation.  
- UX must manage async states (showing “Processing” vs “Done”).

### Best practices
- Use durable message queues with persistence.  
- Design idempotent consumers.  
- Use dead-letter queues and monitoring for poison messages.  
- Provide client-visible status and webhooks to notify completion.

---

## 5) Patterns & integration styles

### Request–Reply (synchronous)
- Client expects immediate answer. Use REST/gRPC.

### Fire-and-forget (async)
- Client sends event and does not wait. Use queues or event bus.

### Callback/Webhook
- Client provides a URL, system calls back on completion (semi-sync architecture).

### Polling / Long-polling
- Client periodically asks for status; long-poll keeps connection open for short time.

### Streaming (bidirectional)
- WebSockets or gRPC streams for real-time two-way communication (chat, telemetry).

---

## 6) Designing APIs for reliability & scale

### Idempotency
- For retries, include idempotency keys (order id, request id) so retries don’t duplicate effects.

### Timeouts & retries
- Configure sensible timeouts; avoid infinite retries. Use exponential backoff + jitter.

### Versioning
- Version APIs (v1, v2) to evolve without breaking clients.

### Contracts & schemas
- Schema validation (OpenAPI/Proto) and backward-compatibility guarantees.

### Security
- AuthN (OAuth, API keys), TLS, rate-limiting, input validation, and scopes for least privilege.

---

## 7) Choosing sync vs async — decision checklist
- **Does user need immediate response?** Yes → sync.  
- **Is operation long-running or CPU-intensive?** Yes → async.  
- **Is the upstream unstable or slow?** Async can decouple and improve resilience.  
- **Is ordering or locality critical?** Choose a pattern that preserves ordering (partitions, message ordering).

---

## 8) Failure handling & idempotency (practical)
- **Producer side:** persist request ID and retry policy; don’t drop unacked messages.  
- **Consumer side:** design idempotent handlers, dead-letter queue for poison messages, and monitoring.  
- **End-to-end visibility:** trace requests across queues and services (distributed tracing).

---

## 9) Example flows (mermaid)

### 9.1 Synchronous checkout validation
```mermaid
sequenceDiagram
  User->>Frontend: Submit order
  Frontend->>CheckoutAPI: Validate & reserve (sync)
  CheckoutAPI->>InventoryService: Reserve (sync)
  InventoryService-->>CheckoutAPI: Ack/Reject
  CheckoutAPI-->>Frontend: Order accepted / rejected
