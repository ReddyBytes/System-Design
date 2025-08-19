### Single Point of Failure (SPOF) → **reliability-and-operations**
SPOF and fault tolerance are operational concerns: they explain how to keep the system alive when a component fails. This topic belongs with circuit-breakers, retries, backups, DR, canaries, and SLOs — all found under **Reliability & Operations**.

**Short contents to include in the file**
- Definition of SPOF, examples (DB, single leader process, single load balancer).
- Fault tolerance techniques: redundancy, failover, replication, active-active vs active-passive, health checks, automation.
- Day-to-day ops signals: failover times, leader-election metrics, backup restore tests.
- Small cookable example from your notes (rice cooker → pressure cooker analogy).


# Single Point of Failure (SPOF) 

A **Single Point of Failure (SPOF)** is any single part of a system which, if it fails, causes the whole system (or a critical user-visible path) to stop working. SPOFs are dangerous because one small fault can take down an entire product.

**Simple example:** If your website depends on one database server and it crashes, the whole site stops. That database is a SPOF.


### Why SPOFs matter (business impact)
- **Availability risk:** one failure can cause downtime and lost revenue.  
- **Operational risk:** manual recovery is slow, error-prone, and creates on-call fatigue.  
- **User trust:** repeated outages reduce user trust and churn.  
- **Compliance & SLAs:** downtime can violate SLAs and regulatory commitments.


### Common SPOFs in modern systems
- Single database instance (no replicas).  
- Single load balancer without failover.  
- Single message broker or queue node.  
- Single leader process controlling critical state (without automatic failover).  
- A single external dependency (third-party payment gateway) without a fallback.  
- Single network link (e.g., sole WAN link to a data center).


## Fault tolerance strategies (how to remove SPOFs)

### 1. Redundancy & Replication
- **Active-passive:** standby replica that takes over if primary fails (requires failover automation).  
- **Active-active:** multiple instances serve traffic concurrently; traffic is load-balanced.  
- **Replication for data:** ensure at least N replicas (e.g., 3) across failure domains (AZ/region).

### 2. Failover & Automation
- **Automated health checks** and **automatic failover** minimize MTTR.  
- Use orchestration (Kubernetes, cloud auto-recovery, managed DB failover) to automate leader election.

### 3. Partitioning & Isolation
- **Shard** critical services to reduce blast radius (one shard fails ≠ entire system down).  
- **Bulkheads:** isolate resources (thread pools, connection pools) so a bad dependency doesn’t exhaust everything.

### 4. Circuit Breakers & Graceful Degradation
- Circuit breakers stop calling failing dependencies and return fallbacks.  
- Graceful degradation serves limited functionality instead of total failure (e.g., read-only mode).

### 5. Multi-region & Multi-AZ design
- Place replicas across availability zones and regions to survive datacenter-level failures.
- Use geo-aware routing + replication strategies.

### 6. Backups & Disaster Recovery (DR)
- Regular automated backups, tested restores (DR drills).  
- Define RPO (how much data you can lose) and RTO (how fast you must recover).


## Operational practices & runbooks
- **Health checks & readiness probes** for every component.  
- **Chaos engineering**: introduce controlled failures to validate redundancy.  
- **SLOs / error budgets**: decide acceptable failure windows and enforce improvements.  
- **Playbooks:** step-by-step automated/manual runbooks for failover, rollback, and restore.  
- **DR drills:** scheduled restore tests from backups and failover rehearsals.


## Interview Q&A (SPOF)
1. **What is a SPOF and why is it bad?**  
   A single component whose failure causes system-wide outage — bad because it reduces reliability and increases downtime.

2. **How would you remove SPOFs at the database layer?**  
   Add replicas across AZs/regions, enable automated failover, use quorum writes for correctness, and test failovers.

3. **What is a bulkhead and why use it?**  
   Isolation pattern preventing one failing component from exhausting shared resources — limits blast radius.

4. **How do you test that SPOFs are eliminated?**  
   Run chaos engineering tests and DR drills, measure failover times and verify no data loss.

---

## Navigation
- ⬅️ Previous: `distributed-systems.md`  
- 🏠 Home: `reliability-and-operations/README.md`  
- ➡️ Next: `reliability-and-operations/02-retries-timeouts-circuit-breakers.md`
