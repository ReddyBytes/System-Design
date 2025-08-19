# System Design — Introduction

## 1. What is System Design?

**System Design** is the process that spans from the **initial planning** of building an application to its **execution and deployment**.  

It’s not just about coding — it’s about carefully **thinking through all aspects** of how the system will behave, scale, recover from failures, and serve users reliably.  

Think of it as the **blueprint for a skyscraper**: before construction starts, engineers decide on foundations, support structures, materials, and safety mechanisms. Similarly, system design ensures software systems can stand tall and serve millions (or even billions) of users.



## 2. Key Aspects to Consider in System Design

When designing **any application**, we must think of it from **all angles/perspectives**:

- Functional requirements (what the system must do)  
- Non-functional requirements (how well it must do it — speed, scale, reliability, security, cost, etc.)

Here are the most important **non-functional requirements**:



### a) Scalability

**Definition:**  
Scalability is the ability of a system to **handle increased load or traffic gracefully**, without breaking or slowing down significantly.  

**Example:**  
If the number of users jumps from **100 → 100,000 → 10 million**, the system should still work correctly.

**Real-world scenarios:**
- **E-commerce websites** (Flipkart, Amazon) must handle huge spikes in traffic during sales events.  
- **Payment systems** scale up during festival seasons.  
- **Social media platforms** (Twitter, Instagram) must perform well even when usage surges suddenly.

**Techniques:**
- Vertical scaling (scale up: add more CPU/RAM).  
- Horizontal scaling (scale out: add more servers).  
- Load balancing, caching (Redis, Memcached).  
- Database sharding & partitioning.  
- Asynchronous processing (Kafka, RabbitMQ, SQS).



### b) Reliability

**Definition:**  
A **reliable system** continues to operate correctly even when things go wrong. If a failure occurs, it should **recover automatically** and continue from the last known safe state — without manual intervention.

**Example:**  
If a payment service fails halfway, it must not charge the user twice or lose the transaction.

**Real-world scenarios:**
- **Banking apps** ensure transactions are consistent.  
- **Streaming services** like Netflix keep running even if one server dies.  
- **Ride-hailing apps** retry bookings automatically.

**Techniques:**
- Redundancy, replication, retries with backoff.  
- Idempotent operations (safe retries).  
- Circuit breakers to contain failures.  
- Disaster Recovery (RPO/RTO targets).  



### c) Maintainability

**Definition:**  
Maintainability means the **codebase is easy to modify, update, and extend**. A maintainable system allows teams to fix bugs or add new features without introducing chaos.

**Example from your notes:**  
> “Code should be clean for future updates/features/bug fixes.”

**Real-world scenarios:**
- **WhatsApp adding “Status” feature** on top of existing chat functionality.  
- **Netflix updating its recommendation algorithm** without rewriting the entire app.  

**Techniques:**
- Write clean, modular, well-documented code.  
- Follow design patterns and coding standards.  
- Use microservices where possible (instead of a giant monolith).  
- Have automated tests (unit, integration, end-to-end).  
- Continuous Integration/Continuous Deployment (CI/CD) pipelines.



### d) Performance

**Definition:**  
Performance is how **fast and responsive** the system is, even as the number of users increases.

**Example from your notes:**  
> “Our application should respond quickly and fast even if users increase.”

**Real-world scenarios:**
- Google search results appear within milliseconds.  
- WhatsApp messages are delivered instantly.  
- Amazon checkout process must remain snappy during peak load.

**Techniques:**
- Database indexing and query optimization.  
- Caching frequently accessed data.  
- Using Content Delivery Networks (CDNs).  
- Optimizing algorithms and reducing latency.  
- Using async processing for non-critical tasks.  



## 3. Two Layers of System Design

System design can be thought of in **two levels**:

### 1) HLD (High-Level Design)
- Focuses on the **big picture** of the system.  
- Defines **major components** and how they interact.  
- Example: frontend, backend, database, APIs, load balancers, caching layer.  

### 2) LLD (Low-Level Design)
- Focuses on the **details of each component**.  
- Example: which database table to use, what fields exist, class diagrams, detailed flow of APIs, algorithms, error handling.  



#### Example 1 - Cooking Analogy
Imagine making **Dosa**:

- **High-Level Design (HLD):**  
  Plan that you need **batter, stove, pan, oil**.  
  (Big picture: what components are needed.)

- **Low-Level Design (LLD):**  
  Know exactly **which dal to use, how long to soak, how to grind, how much oil to add**.  
  (Implementation details.)



#### Example 2 — WhatsApp

- **HLD for WhatsApp:**  
  - Frontend (chat UI, calls, contacts).  
  - Backend (messaging service, media service, notification service).  
  - Database (messages, contacts, media storage).  
  - Protocols (end-to-end encryption).  

- **LLD for WhatsApp:**  
  - How a message is stored (schema design: message_id, sender_id, receiver_id, timestamp, status).  
  - How message delivery acknowledgments are handled (✓, ✓✓, ✓✓ blue).  
  - How retries work if delivery fails.  



## 4. Quick Recap

- System Design = planning + execution blueprint of an application.  
-  Always think from all angles: functionality, scalability, reliability, security, cost.  
- Functional requirements : what the system must do
- Non-functional requirements: **Scalability, Reliability, Maintainability, Performance.**  
- System Design has **two levels**:  
  - **HLD** = big picture (what components exist).  
  - **LLD** = deep detail (how each part works).  
- Examples: cooking dosa 🍳, WhatsApp system.  


## 5. Interview Checkpoints

- **Q:** How would you design a system that grows from 100 users to 10M users?  
  **A:** Use scalable techniques (load balancing, caching, sharding, queues).  

- **Q:** What makes a system reliable?  
  **A:** Auto-recovery, redundancy, replication, and idempotent operations.  

- **Q:** What’s the difference between availability and reliability?  
  **A:** Availability = service is up; Reliability = service gives correct results over time.


