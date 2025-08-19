# Distributed Systems


## 1) Context — why this matters 

When you move from a single machine to many machines (regions, data centers, or cloud zones), your system becomes a **distributed system**. This brings huge benefits (scale, fault isolation, low latency for global users) but also new problems: **how to keep data correct, keep the service responsive, and survive network failures**.

This file explains:
- What distributed systems are,
- The **CAP theorem** and what it means in practice,
- How to pick trade-offs for real-world use-cases,
- Practical design recipes, examples, and interview-ready explanations.


## 2) What is a distributed system? 

A **distributed system** is a collection of independent computers (called *nodes* or *servers*) that work together and appear to users as a single, unified service. Each node may run on a different machine, in a different rack, or in a different city or country. The goal is to serve users faster, scale by adding machines, and tolerate failures of individual machines.  
Typical goals:
- **Lower latency:** serve users from a nearby server (faster response).  
- **Scale:** handle more traffic by adding servers horizontally.  
- **Fault tolerance:** if one server or data center dies, others keep serving.  
- **Geographic requirements & regulations:** keep data in-region for laws or latency.  
- **Cost control:** spread load and use cheaper servers in different places.


**Day-to-day analogy:**  
A global bank branch network: local branches serve customers nearby (low latency), branches must sync customer ledgers occasionally, and the HQ must ensure no double withdrawals even if islands lose connectivity.


### Core components & concepts (what you’ll see in every distributed system)
- **Nodes / replicas:** independent machines running the software.  
- **Clients:** users or apps making requests.  
- **Replication:** copying data across nodes so multiple places can serve reads.  
- **Partitioning (sharding):** splitting data by key (userID, SKU) so each node handles only a slice.  
- **Consensus & leader election:** protocols (Raft/Paxos) used when nodes must agree on a value (who is leader).  
- **Messaging / networking:** the network carrying requests and replication messages; it can be unreliable.  
- **Coordination layer:** metadata service (which node owns which shard, who is leader).  
- **Monitoring & observability:** metrics, logs, traces to detect failures and slowness.


### Everyday real-world scenarios 
- **Food delivery app:** user in Hyderabad queries menu and places order; Hyderabad servers handle UI and order placement; orders replicate to central analytics for reporting. If the link to central analytics breaks, Hyderabad still accepts and confirms orders locally.
- **E-commerce (catalog vs checkout):** product pages (catalog) served from caches and replicas (low latency, eventual correctness ok). Checkout (reduce inventory) often must be strict — you don’t want oversell.
- **Chat app (WhatsApp-like):** messages are stored locally and replicated; temporary network splits may delay message sync, but users expect to keep chatting locally.
- **Banking ledger:** writes (debits/credits) must be correct; double-spend or lost updates are unacceptable.


## CAP Theorem 

### What CAP theorem is, and why it appears in distributed systems
**Short plain idea:** CAP is a rule about what is possible when the network between servers can fail. If nodes cannot communicate reliably (a partition), you cannot at that same time give both perfect correctness (consistency) and guaranteed responses for every request (availability). Because real networks fail, this forces designers to choose which guarantee to prioritize while tolerating partitions.

**Why CAP matters in practice:**  
- Distributed systems rely on networked replication; networks can and will fail.  
- CAP helps engineers make explicit, business-aware choices: is correctness now more important than always answering users? This affects UX, revenue, and legal correctness.



### C — Consistency 
**Meaning:** After a successful write, every subsequent read should reflect that write. There should be one global order of operations that everyone agrees on.

**Plain example:** If two people see “2 items in stock” and both try to buy them, consistency guarantees that only two purchases succeed and any later attempt shows “out of stock.”

**Techniques to achieve C:**
- **Synchronous replication:** write to multiple replicas before returning success.  
- **Leader-based design:** one leader orders writes and ensures followers apply in order.  
- **Consensus algorithms:** Raft/Paxos ensure replicas agree on the commit order.  
- **Quorum writes & reads:** choose `W` and `R` so `W + R > N` ensuring overlap.

**When to prefer C (business reasons):**
- Money transfers, account balances, checkout inventory, legal records.

**Implications for users:** During network partitions or heavy failures, a consistency-first system may block or reject requests (reduced availability) so data stays correct.


### A — Availability 
**Meaning:** The system tries to answer every request; clients get a response (success or graceful failure) quickly — the system does not hang or refuse to respond due to remote replicas being unreachable.

**Plain example:** If a social app’s central server is temporarily unreachable, the app still shows cached posts and accepts likes; the user sees instant responsiveness, even if some changes won’t be visible everywhere immediately.

**Techniques to achieve A:**
- **Asynchronous replication:** accept writes locally and replicate later.  
- **Multi-master / multi-leader writes:** accept writes at multiple nodes.  
- **Local caches & local read-serving:** reduce dependency on remote nodes.  
- **Graceful degradation:** return cached data or limited functionality rather than fail completely.

**When to prefer A:**
- Social feeds, presence/typing indicators, read-mostly UIs, CDN/caches.

**Implications for users:** Users rarely see errors and the app feels snappy, but different users may see slightly different states until reconciliation.


### P — Partition Tolerance 
**Meaning:** The system continues to function despite network messages being delayed, lost, or blocked between nodes. Partition tolerance is about surviving the reality of network failures.

**Plain example:** If the network between Hyderabad and London data centers is cut, each region continues to serve its local customers, queuing cross-region replicates for when the link returns.

**Why P is mandatory in real systems:** Real networks are not perfect. For any geo-distributed system, you must design for partitions — otherwise a single network glitch would break your service.

**Techniques implementing P:**
- **Local-write acceptance with durable logging:** make local progress and store writes for later replication.  
- **Queueing & back-pressure:** buffer operations when remote systems are slow.  
- **Conflict detection & reconciliation:** plan how to merge divergent states after partition heals (vector clocks, CRDTs, application rules).



### How C, A and P relate at the moment of partition
- When a partition occurs, you **must** sacrifice either **C** or **A** (because **P** is required to handle the partition).  
  - **Choose C:** reject or delay some requests until replicas can agree → consistent but possibly unavailable.  
  - **Choose A:** accept requests locally and reconcile later → always available but temporarily inconsistent.

**Important business question:** Which is worse for your business: temporarily refusing customers (reduced revenue, unhappy UX) or temporarily having inconsistent state (risk of oversell or double-charge)?



### Concrete, accurate examples tied to CAP choices (inside the CAP section)

- **Banking transfer (Choose C):** If two branches are partitioned, the system refuses the transfer at one branch rather than risk inconsistent balances. After partition heals, normal operation resumes and all ledgers match.
- **E-commerce catalog vs checkout (Mixed use):** Catalog pages use AP (fast browsing); checkout uses CP (strong correctness) — design the system so different flows have different guarantees.
- **Social timeline (Choose A):** Keep the feed available even if a region is isolated; accept posts locally and sync later.
- **DNS (Choose A):** High availability is paramount; updates propagate slowly (eventually consistent).




