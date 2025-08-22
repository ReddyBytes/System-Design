# Database Indexing

Imagine you’re in a huge library with millions of books but there’s **no catalog**.  
If you want to find “Harry Potter and the Goblet of Fire,” you’d have to go row by row, shelf by shelf, until you stumble upon it. That would take hours!  

Now imagine the library has an **index catalog** that tells you:  
“Harry Potter and the Goblet of Fire → Row 7, Shelf 3, Position 22.”  
Suddenly, you can find the book in seconds.  

This is exactly what **Database Indexing** does.  
- A database index is like a **smart catalog** that speeds up data retrieval.  
- Instead of scanning the whole table row by row (full table scan), the database uses the index to jump directly to the location of the required data.  
- Indexes are created on columns (like `user_id`, `email`, or `order_date`) that are often used for searching, filtering, or joining.  

In real systems like Amazon or Instagram:  
- Without indexing, searching for “all orders made in the last 7 days” would require checking **every order ever placed**.  
- With indexing, the database quickly narrows down to the right subset → making user experience smooth.  



## Why do we need Database Indexing?
Without indexes, databases would perform **full scans** for every query → slow and inefficient.  

- **Problem it solves**: Searching and filtering large datasets quickly.  
- **Why engineers care**:  
  * Faster queries → better user experience.  
  * Lower CPU usage → reduced infrastructure cost.  
  * Scalability → databases can handle millions of queries.  

**Real-life example**:  
Imagine Instagram without indexing. If you search your friend “Raju” among 2 billion users:  
- Without index → Instagram scans every single profile (takes minutes!).  
- With index → It directly jumps to the place where “Raju” is stored → instant result.  

**Consequences if not used**:  
- Very slow query performance.  
- High server cost.  
- Poor scalability → app crashes with more users.  



## How does it work?
Database indexes are usually implemented using data structures like:  

1. **B-Tree Index** (most common)  
   - Organizes data in a balanced tree.  
   - Great for range queries (e.g., “find users born between 1990 and 2000”).  

2. **Hash Index**  
   - Uses a hash function to map keys → positions.  
   - Extremely fast for exact matches (e.g., `user_id = 12345`).  

3. **Bitmap Index**  
   - Uses bitmaps for columns with limited unique values (e.g., gender = M/F).  
   - Efficient for analytics queries.  

4. **Full-text Index**  
   - Special index for searching text documents.  
   - Example: Searching “machine learning” in millions of articles.  

**Step-by-step (B-Tree Example)**:  
1. Data is inserted into the table.  
2. Index maintains a tree-like structure.  
3. When a query runs, instead of scanning all rows, the database looks into the tree and finds the location instantly.  

**Diagram**:  
(Open-source image of a B-Tree or hash index can be added here for clarity.)  



## Relation to System Engineering / System Design
- Indexes make databases **scalable**.  
- In system design interviews, indexing is part of **query optimization** strategies.  
- High-scale apps (Google, Facebook, Netflix) rely heavily on indexing for search performance.  



## Before Database Indexing vs After Database Indexing

**Before Indexing**  
- Every query = full table scan.  
- Slow results, even for small lookups.  
- Not scalable for large datasets.  

**After Indexing**  
- Queries directly jump to the required rows.  
- Sub-second responses even with millions of rows.  
- Enables real-time search and analytics.  



## Main Motive / Goal
The main goal of indexing is **speed**:  
- Make data retrieval faster.  
- Avoid full table scans.  
- Reduce load on database servers.  


## Advantages
- Lightning-fast queries.  
- Efficient for large datasets.  
- Reduced CPU & memory usage.  
- Supports advanced features (full-text search, range queries).  



## Disadvantages
- Takes extra storage space.  
- Slows down **write operations** (insert/update/delete) since indexes also need updating.  
- Poorly chosen indexes can harm performance.  
- More complexity in database tuning.  


## Interview Q&A

**Q1. What is a database index and why is it used?**  
- An index is a data structure that speeds up retrieval operations by avoiding full table scans.  

**Q2. What are the types of indexes?**  
- B-Tree, Hash, Bitmap, Full-text, etc.  

**Q3. Does indexing improve inserts and updates?**  
- No, it actually makes them slower because the index must be updated along with the table.  

**Q4. Real-world scenario: If your e-commerce app slows down when searching for products, how do you fix it?**  
- Add indexes on frequently queried columns (e.g., `product_name`, `category_id`).  

**Q5. Can indexing be misused?**  
- Yes, too many indexes slow down writes and increase storage costs.  


## Key Takeaways
- Index = shortcut to data.  
- Essential for performance in large systems.  
- Not free → comes with write overhead and storage costs.  
- Choose indexes wisely (based on query patterns).  



## Topics to cover next
- Query Optimization Techniques.  
- SQL vs NoSQL indexing differences.  
- Sharding vs Indexing.  
- Caching vs Indexing.  



## Navigation
- **Previous Topic**: [Storage and Database](../Databases/Storage_and_Database.md)  
- **Next Topic**: [Query Optimization](../Databases/Query_Optimization.md)  
- **Home**: [System Design Index](../README.md)  
