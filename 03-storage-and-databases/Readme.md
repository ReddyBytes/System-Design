# Storage and Database

📂 **Folder Classification**:  
This topic belongs to **Databases** → `Databases/Storage_and_Database.md`

---

## What is Storage and Database?

Imagine you are writing a diary. Every day, you store your thoughts in a notebook. That notebook is **storage**. Now imagine you not only write your thoughts but also organize them with dates, tags, and an index so you can quickly find “all entries about school” or “everything I wrote in July.” That organized system is a **database**.  

- **Storage** is about keeping raw data safe — like files, logs, or images.  
- **Database** is about managing, structuring, and querying that data so you can get meaningful insights.  

In real life:  
- Your phone gallery → **Storage** (just keeps the pictures).  
- Google Photos → **Database** (lets you search “dog photos from last December”).  

So, while storage is like a warehouse holding boxes, a database is like a smart librarian who organizes everything and helps you find what you need instantly.  



## Why do we need Storage and Database?

Without proper storage and databases, our digital world would be chaos.  

- **Storage Need**: We need a place to keep all our raw data safely (documents, logs, images, backups).  
- **Database Need**: We need to search, filter, and analyze data in real-time, not just dump it.  

**Real-Life Example**:  
Imagine an e-commerce company like Amazon. If they only used storage, they could save product details, customer orders, and reviews in files. But when a customer searches “red shoes under $50,” it would be painfully slow to check each file.  
With a **database**, queries are lightning-fast, customers get instant results, and business runs smoothly.  

**Consequences without them**:  
- Without storage: Data gets lost, no reliable history.  
- Without databases: Slow systems, poor customer experience, and impossible scaling.  



## How does it work?

### Storage
1. Data is saved in binary form on disks (HDD, SSD, or cloud storage).  
2. Files are organized in directories.  
3. Retrieval is manual or sequential.  

### Database
1. Data is structured into tables, documents, graphs, or key-value pairs.  
2. Database Management System (DBMS) ensures indexing, transactions, and queries.  
3. Applications interact with DBMS using SQL/NoSQL queries.  


So, storage is the basement where data lives, and the database is the smart system that knows exactly where to look.  

### SQL vs NoSQL (data models)

> SQL (relational): structured tables with fixed schema, strong joins and transactions (ACID). Example: bank ledger where consistency matters.

> NoSQL (document, key-value, wide-column, graph): flexible schemas or specialized models. Example: product catalog (documents) or user session store (key-value) where scale and flexibility matter.

### ACID vs BASE

> ACID: Atomicity, Consistency, Isolation, Durability → strict correctness for transactions. Example: transferring money between accounts.

> BASE: Basically Available, Soft state, Eventual consistency → system remains available and accepts temporary divergence, then reconciles. Example: social media likes: slightly stale is OK.

### Indexing

> Indexes (B-tree, LSM-tree) let the DB find rows fast. Analogy: library’s index cards. Over-indexing speeds reads but slows writes & uses space.

### Replication & Consensus

> Primary-replica (leader-follower): one leader accepts writes, followers replicate. Used for strong ordering.  

> Multi-master: many nodes accept writes; conflict resolution needed (CRDTs, last-write-wins).  

> Consensus protocols (Raft/Paxos): used to elect leaders and agree on committed entries for strong consistency (metadata, config stores).

### Partitioning / Sharding

>Split data by a key (userID, SKU) across nodes so each node handles a subset. This enables horizontal scaling but adds complexity for cross-shard transactions.

### Caching

> Cache frequently read data in memory (Redis, memcached) to reduce DB reads and latency. Example: product pages cached at CDN edge.

### Backup & Recovery

> Periodic snapshots and WAL archiving let you restore after failures. Define RPO (allowed data loss) and RTO (recovery time).

### Storage types in practice

> `Block:` VM disks, databases often store their files on block.

> `File:` Shared file systems used by applications requiring POSIX semantics.

> `Object:` Immutable objects with metadata; great for photos, backups, big blobs.

## Relation to System Engineering / System Design

- In **system design**, storage ensures persistence (data isn’t lost).  
- Databases make applications usable at scale (searching, filtering, transactions).  
- For distributed systems, databases + storage are the backbone of **high availability** and **scalability**.  

Without them, no modern system (social media, fintech, IoT, etc.) can exist.  



## Before Storage & Database vs After Storage & Database

### Before:
- Developers stored files in flat files or memory.  
- Searching data meant reading file by file → very slow.  
- Risk of data loss and inconsistency.  

### After:
- Databases brought indexing, transactions (ACID), replication, and queries.  
- Storage solutions evolved to be redundant, fault-tolerant, and cloud-native.  
- Systems became reliable, fast, and scalable.  



## Main Motive / Goal
Store data reliably, let systems query and update it efficiently, and ensure correctness and availability according to business needs.
- **Storage**: Persist data safely.  
- **Database**: Organize and retrieve data efficiently.  



## Advantages

- Durability: data survives crashes and restarts.

- Consistency: transactional guarantees prevent corruption.

- Scalability: sharding and replication let systems handle growth.

- Performance: indexing and caching provide low-latency access.

- Flexibility: different data models fit different problems.

## Disadvantages

- Complexity: distributed databases, sharding, and replication add engineering overhead.

- Cost: storage tiers, replication, and backups increase expense.

- Trade-offs: no single system perfectly optimizes latency, consistency, and availability (CAP).

- Operational burden: monitoring, backups, schema migrations, and disaster recovery.


## Interview Q&A

**Q1. What is the difference between storage and database?**  
- Storage = raw data persistence (files, objects).  
- Database = structured organization + querying capabilities.  

**Q2. Can we build a system with only storage?**  
- Yes, but it would be inefficient. You’d face performance and scalability issues.  

**Q3. Why do companies use both storage and databases?**  
- Storage handles raw backups, large files.  
- Databases handle structured, query-heavy operations.  

**Q4. Real-world scenario: If Instagram only had storage but no database, what happens?**  
- You could store images, but searching “all reels from last week with #food” would be impossible.  

**Q5. When would you choose SQL over NoSQL?**
- Choose SQL when strict schemas, complex joins, and transactional guarantees (ACID) are required — e.g., financial systems, billing, order transaction processing.

**Q6. What is sharding and how do you pick a shard key?**
- Sharding splits data across nodes by key. Pick a shard key that evenly distributes load and aligns with query patterns (e.g., userID for per-user traffic). Avoid hot keys that concentrate traffic.

**Q7. Explain replication lag and its impact.**
- Replication lag is the delay between a write on primary and visibility on replicas. It causes stale reads; systems must handle read-after-write consistency or route reads to primary when freshness is required.

**Q8. What are typical backup strategies?**
- Periodic snapshots + continuous WAL/transaction log archiving; test restores; offsite storage for disaster recovery.

**Q9. How does an LSM-tree differ from a B-tree and when is it used?**
- LSM-tree batches writes in memory and flushes to disk as sorted runs (good for write-heavy workloads, e.g., Cassandra); B-tree keeps tree balanced on disk (good for balanced read/write patterns and random access).

**Q10. How do you design for strong consistency across regions?**
- Use synchronous/quorum replication or distributed consensus (Spanner-like with TrueTime or Paxos/Raft) — tradeoff: higher latency and potential reduced availability during partitions.

**Q11. What’s eventual consistency and where is it acceptable?**
- Eventual consistency means replicas converge over time; temporary divergence is allowed. Acceptable for social feeds, cacheable reads, analytics — not for critical financial writes.

**Q12. How do you handle schema changes in production?**
- Use backward-compatible changes (add new nullable columns), deploy consumers to handle both old/new schemas, migrate data in background, and coordinate deployments (blue/green or rolling)

## Key Takeaways

- Storage is the underlying medium; databases provide structured access and guarantees.

- Choose data model (relational, document, key-value, graph) based on access patterns and consistency needs.

- Use replication, sharding, and caching to scale and make systems resilient.

- Plan backups, monitoring, and recovery procedures; test them regularly.

- Be explicit about trade-offs (CAP, latency vs consistency) and design per business requirements.


## Topics to cover next

- File Systems vs Databases.  
- Types of Databases (SQL vs NoSQL).  
- Data Warehouses vs Databases.  
- Distributed Storage (S3, HDFS, Ceph).
- ACID vs BASE (detailed)

- Replication strategies (async vs sync)

- Sharding patterns and re-sharding techniques

- Indexing internals: B-tree vs LSM-tree deep dive

- Distributed transactions and two-phase commit / sagas

- Storage tiers and cost optimization (hot/cold storage)

- Backup, restore, and disaster recovery playbooks




## Navigation

- **Previous Topic**: [API Gateway](../APIs/API_Gateway.md)  
- **Next Topic**: [SQL vs NoSQL](../Databases/SQL_vs_NoSQL.md)  
- **Home**: [System Design Index](../README.md)  


