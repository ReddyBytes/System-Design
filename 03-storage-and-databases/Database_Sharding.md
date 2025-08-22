# Database Sharding


## What is Database Sharding?
Imagine you are running an online e-commerce store like Amazon. Millions of customers are searching, adding products to carts, and making purchases at the same time. All this data (users, products, transactions, reviews) is stored in a database.  
Now, if you put *all of this data into a single giant database*, it quickly becomes overloaded: queries become slow, storage becomes huge, and scaling is nearly impossible.  

This is where **Database Sharding** comes in. Sharding is the process of splitting a single large database into smaller, faster, and more manageable parts called **shards**. Each shard is like a piece of the puzzle—it stores a portion of the data. For example:
- Shard 1 → Customers whose IDs start with A–F  
- Shard 2 → Customers whose IDs start with G–L  
- Shard 3 → Customers whose IDs start with M–R  
- Shard 4 → Customers whose IDs start with S–Z  

Together, all shards form the full dataset, but individually, each shard is smaller and faster to query.  
It’s like dividing a classroom of 100 students into 4 smaller groups of 25 each, with each group having its own teacher. Now teaching and managing is far easier.



## Why do we need Database Sharding?
Without sharding, a single large database faces these issues:
- Slow queries due to massive data volume.  
- Difficulties in scaling horizontally (adding more machines).  
- High risk of downtime since everything depends on one giant system.  

With sharding, systems can:
- Handle **billions of records efficiently**.  
- Scale by simply adding new shards (new servers).  
- Improve user experience with **faster queries**.  

**Real-life consequence example**:  
Imagine Facebook storing all user posts in one database. If 3 billion users are hitting it at the same time, the database would crawl, fail, or even crash. With sharding, Facebook splits data (e.g., by region: US shard, Europe shard, Asia shard), so no single database is overloaded.



## How does it work?
1. **Define a Sharding Key**  
   - The most important part of sharding is choosing the **shard key**. It decides how data is split across shards.  
   Example: `user_id`, `region`, or `order_id`.

2. **Distribute Data Based on Shard Key**  
   - A rule (like hashing or ranges) decides which shard stores the data.  
   Example: Users with ID 1–1000 → Shard 1, IDs 1001–2000 → Shard 2.

3. **Query Routing**  
   - An application or middleware routes queries to the correct shard.  
   Example: If a user with ID = 1520 logs in, the system knows to check **Shard 2** directly.

4. **Shards Work Independently**  
   - Each shard behaves like a mini-database and handles queries locally, reducing load on the global system.

**Diagram (Open-source illustration can be added):**  
Clients → Query Router → Multiple Shards → Combined Results



## Relation to System Engineering / System Design
Database Sharding is a critical part of **scalable system design**. When building distributed systems like social media, banking, or e-commerce platforms, handling large amounts of data efficiently is crucial.  
Sharding allows engineers to:
- Scale horizontally (add machines instead of upgrading a single server).  
- Improve fault isolation (failure in one shard doesn’t crash the whole system).  
- Support millions of concurrent users smoothly.  



## Before Database Sharding vs After Database Sharding
### Before Sharding:
- One giant database.  
- Slow response times.  
- Single point of failure.  
- Hard to scale when traffic grows.  

### After Sharding:
- Data distributed across shards.  
- Faster queries and better performance.  
- Easy to scale by adding new shards.  
- Failure in one shard doesn’t affect others.  



## Main Motive / Goal
The main goal of database sharding is **scalability and performance**. It enables applications to handle massive amounts of data and high traffic loads without slowing down or crashing.



## Advantages
- Efficient horizontal scalability.  
- Faster query response times.  
- Reduces load on individual databases.  
- Fault isolation: issues are contained to a shard.  



## Disadvantages
- Choosing a shard key is tricky—bad keys cause uneven distribution.  
- Queries that need data from multiple shards become complex (cross-shard queries).  
- Increased operational complexity (monitoring, backups, migrations).  
- Resharding (redistributing data when shards grow uneven) is difficult.  



## Interview Q&A

**Q1. What is database sharding?**  
A method of splitting a large database into smaller parts (shards) that each handle a portion of the total data.  

**Q2. How do you decide on a shard key?**  
Choose a field that ensures **even distribution of data and queries**. Example: `user_id` for a social network.  

**Q3. What happens if the shard key is poorly chosen?**  
Data may cluster in one shard (hotspot problem), overloading it while others stay underutilized.  

**Q4. How does sharding differ from replication?**  
- **Replication:** Copies the entire database across multiple servers. Improves availability and read performance.  
- **Sharding:** Splits the database into parts. Improves scalability and write performance.  

**Q5. Real-world scenario**: If an e-commerce platform with 200M users sees query latency rising, how would sharding help?  
By splitting the user data into multiple shards (e.g., 20 shards with 10M users each), queries run on smaller datasets, reducing response time drastically.



## Key Takeaways
- Database sharding = splitting one huge database into smaller parts for scalability.  
- Helps large systems like Facebook, Amazon, and Twitter manage billions of records.  
- Needs careful shard key selection to avoid uneven loads.  
- Improves performance but increases complexity.  



## Topics to cover next
- **Replication vs Sharding**  
- **Partitioning Strategies (Range, Hash, Directory-based)**  
- **Shard Rebalancing**  
- **Consistent Hashing**  
- **CAP Theorem & Sharding**  



## Navigation
- **Previous Topic:** Database Redundancy  
- **Next Topic:** Replication vs Sharding  
- **Related Topics:** Data Partitioning, Consistent Hashing, CAP Theorem  
- **🏠 Home:** [System Design Knowledge Base](#)  
