# Caching

Imagine you are preparing for an exam and every time you need an answer, you walk to the library, find the right book, look for the page, and then read it. It takes a lot of time. To make it faster, you start writing the most frequently used answers in a notebook and keep it on your study table. Now, whenever you need those answers, you check your notebook first instead of walking to the library every single time.  

This notebook is **Cache**.  

**Caching** is the process of storing frequently accessed data in a temporary storage (cache) that is much faster to access than the original data source. Instead of going to the database or recomputing results again and again, the system stores the "hot" or "frequently used" data in cache memory so it can serve requests faster.  

For example:  
- In e-commerce apps like Amazon, product details (price, description, images) are cached so they don’t need to be fetched from the database repeatedly.  
- In YouTube or Netflix, recently watched videos are cached in nearby servers/CDNs so you don’t buffer every time you play them again.  



## Why do we need Caching?
Without caching, every request would directly go to the database, filesystem, or a remote API. This would make systems:  
- **Slow** (high latency because each query takes time).  
- **Expensive** (databases are resource-heavy compared to caches).  
- **Unreliable** (systems may crash under heavy load).  

Real-life example:  
Imagine a school canteen. Without caching, every student who wants tea must walk into the kitchen, ask the chef, wait until it’s prepared, and then get it. This creates long queues. With caching, the canteen staff keeps **a flask full of hot tea at the counter**. Now, most students get their tea instantly, while the chef only prepares more when the flask is empty.  

If we don’t have caching in real systems:  
- Websites would be painfully slow.  
- Servers would overload under traffic spikes.  
- Users would abandon apps due to poor experience.  



## How does it work?
Caching works in a simple flow:
1. A client requests data (say product details).  
2. The system checks if the data is available in the cache.  
   - If **Yes**, return it instantly. (Cache Hit)  
   - If **No**, fetch it from the original source (DB, API), store it in the cache, and then return it. (Cache Miss)  
3. Next time, the same data can be retrieved directly from the cache.  

A simple diagram can explain this flow:  
(Client → Cache → Database).  
(Open-source image can be used here like: *cache hit vs cache miss diagram*).  



## Relation to System Engineering / System Design
Caching plays a critical role in **scalability, performance, and cost optimization**.  
- It is used in **web applications** (caching HTML pages, API responses).  
- In **distributed systems**, caching reduces inter-service calls.  
- In **databases**, query results caching reduces repeated heavy queries.  
- In **content delivery networks (CDNs)**, caching brings content closer to users globally.  



## Before Caching vs After Caching
- **Before Caching:** Every user request hits the database or backend, causing slow responses and high load.  
- **After Caching:** Frequent requests are served directly from memory or local storage, leading to lightning-fast responses and reduced server/database strain.  

Example:  
- Without caching, logging into Instagram would check the database for your profile every time you refresh.  
- With caching, your profile data is stored in memory for quick access.  



## Main Motive / Goal
The main motive of caching is:  
- To reduce **latency** (faster responses).  
- To reduce **load** on backend systems.  
- To improve **scalability** under heavy traffic.  



## Advantages
- Very fast access (data stored in RAM or nearby storage).  
- Reduces database/API load.  
- Improves user experience.  
- Helps scale systems to handle millions of users.  



## Disadvantages
- Cache may serve **stale (outdated)** data if not invalidated properly.  
- Cache adds **extra complexity** in system design.  
- Cache storage is limited (RAM is expensive).  
- Need strategies to handle **cache eviction** (what to remove when cache is full).  



## How to implement Caching
### Steps:
1. Identify what data is accessed most frequently (hot data).  
2. Choose a caching layer (in-memory cache like Redis, Memcached, CDN for static files).  
3. Implement cache lookups before going to the database.  
4. Handle **cache invalidation** (remove/update cache when data changes).  
5. Use **cache eviction policies**:  
   - **LRU (Least Recently Used):** Remove the data that hasn’t been used for the longest time.  
   - **LFU (Least Frequently Used):** Remove data used the least number of times.  
   - **FIFO (First In First Out):** Remove oldest cached data first.  

### Approaches:
- **Write-through cache:** Data is written to cache and DB at the same time.  
- **Write-back cache:** Data is written to cache first and lazily pushed to DB later.  
- **Write-around cache:** Data written only to DB, cache is updated on read. 

## What is Cache Eviction?
Cache eviction is the process of removing old or less useful data from the cache when it becomes full, to make room for new data. Since cache storage (RAM, SSDs, or in-memory systems like Redis) is limited, systems need smart strategies to decide *which data should stay and which should go*.  

Think of your refrigerator at home. You can’t keep piling groceries in forever. When it gets full, you need to remove older items (like expired milk) to store fresh ones. Cache eviction works the same way.  

## Why do we need Cache Eviction?
- A cache cannot grow indefinitely; it has finite space.  
- Without eviction policies, the cache would stop storing new data once it’s full.  
- Smart eviction ensures frequently used and valuable data remains in cache, while less useful data is discarded.  

**Example:** Imagine Netflix’s video streaming cache. If their cache is full and does not evict old shows, then when a new blockbuster movie is released, users won’t get it served quickly from cache. Instead, they’d wait longer as it’s fetched from slower storage.  

## Types of Cache Eviction Policies

### 1. **Least Recently Used (LRU)**
- Removes the data that has not been accessed for the longest time.  
- Based on the idea that recently used items are more likely to be reused soon.  

**Example:**  
Suppose your mobile browser cache is full. You recently opened Google, YouTube, and Twitter. But you last opened a news site 3 weeks ago. LRU will evict that news site from cache first.  

**Analogy:** A crowded closet: You throw out the clothes you haven’t worn in years to make space for the ones you wear often.  



### 2. **Least Frequently Used (LFU)**
- Removes data that is accessed the least number of times.  
- Good when you want to prioritize popularity over recency.  

**Example:**  
In Spotify’s cache, if a rare old song has been cached but hardly anyone plays it, while a new trending song is played thousands of times daily, LFU will evict the old rare song.  

**Analogy:** A restaurant menu: Dishes that nobody orders are eventually removed to make space for popular dishes.  



### 3. **First In, First Out (FIFO)**
- Removes the oldest inserted item in the cache, regardless of usage.  

**Example:**  
Think of a ticket queue. The first person in line is served and leaves. Similarly, the first cached item is evicted when space runs out.  

**Analogy:** A stack of milk cartons in a grocery store where the oldest stock is sold first.  

**Downside:** Popular items could get removed just because they were added earlier.  



### 4. **Last In, First Out (LIFO)**
- Opposite of FIFO. The most recently added item is removed first.  

**Example:**  
Imagine you stack books on a table. When you need space, you remove the last book you placed on top.  

**Downside:** Recently added but important data may be evicted prematurely.  



### 5. **Random Replacement**
- Evicts an item at random when space is needed.  

**Example:**  
Redis sometimes uses random eviction when configured in "random" mode.  

**Analogy:** Like tossing out a random item from your fridge without thinking whether it’s fresh or expired.  

**Downside:** Might remove highly useful data by chance.  



### 6. **Time-To-Live (TTL) / Expiry Based**
- Each cached item has a pre-set time limit after which it is automatically evicted.  

**Example:**  
Instagram stories cache expires after 24 hours because stories themselves expire.  

**Analogy:** Food in your fridge has an expiry date – after that, you throw it out.  



### 7. **Segmentation Policies (ARC - Adaptive Replacement Cache)**
- Uses a mix of **LRU** and **LFU**, keeping track of both recency and frequency.  
- Adjusts dynamically depending on workload.  

**Example:**  
Databases like IBM DB2 and some advanced storage systems use ARC.  

**Analogy:** A teacher tracking both *who raised their hand most frequently* and *who raised their hand most recently* to decide who should answer the next question.  

## What is Cache Invalidation?
Cache invalidation is the process of **removing or updating stale (outdated) data from cache** to ensure that applications always serve correct and fresh information to users.  

Think of a refrigerator again. If you keep fresh milk and after a few days it expires, you need to throw it away. If you don’t, you’ll end up drinking spoiled milk. Similarly, if a cache keeps old values and doesn’t update them, users will see outdated or wrong data.  

**Real-Life Example:**  
Imagine an **online shopping site**. A user adds a product to their cart. If the cache still shows old stock values (e.g., 10 items available), but the actual database shows only 2 items left, then multiple customers might try to buy it and face errors at checkout. To avoid this, the cache must be **invalidated** (updated with the new stock value).  

## Why do we need Cache Invalidation?
- **Accuracy:** Without invalidation, caches might serve stale data to users.  
- **Consistency:** Keeps cache in sync with the source of truth (databases).  
- **User Trust:** Users get up-to-date prices, availability, and content.  

**Example Consequence Without Invalidation:**  
Twitter posts are cached for fast display. If cache isn’t invalidated when someone deletes a tweet, people might still see the deleted tweet for hours — leading to confusion and mistrust.  

## How does Cache Invalidation Work?
1. Data is cached when first accessed.  
2. When data is updated/deleted in the database, invalidation policies decide whether to remove/update that data in cache.  
3. New data is fetched and stored back in the cache upon the next access.  

(Open-source diagrams can be used here showing Cache <-> Database sync).  

## Types of Cache Invalidation Policies

### 1. **Write-Through**
- Data is written **to both the cache and the database simultaneously**.  
- Cache always has the latest data.  

**Example:**  
In an e-commerce platform, when a new order is placed, the system updates both the cache and database with the new stock value immediately.  

**Analogy:** Writing homework on both your notebook and a whiteboard at the same time so both are always in sync.  

**Pros:**  
- Data consistency between cache and DB.  
- Low risk of stale data.  

**Cons:**  
- Slower writes (since it writes to two places).  
- Cache may store data that isn’t accessed often.  



### 2. **Write-Around**
- Data is written **only to the database**, not the cache.  
- Cache gets updated only when that data is read again.  

**Example:**  
Banking system: When you transfer money, it updates the main database directly. The cache is updated only if someone checks their balance afterward.  

**Analogy:** Writing homework in your notebook only. If a teacher asks later, you copy it onto the whiteboard.  

**Pros:**  
- Avoids filling cache with data that may not be read soon.  
- Faster writes since only the database is updated.  

**Cons:**  
- Risk of cache misses (first read after write will be slow).  



### 3. **Write-Back (or Write-Behind)**
- Data is written **to the cache first** and then asynchronously written to the database later.  

**Example:**  
In Facebook “likes” system, likes are written to cache instantly and flushed to DB in bulk later for efficiency.  

**Analogy:** Writing rough notes on a sticky pad (cache) and later copying them neatly into a notebook (database).  

**Pros:**  
- Very fast writes.  
- Reduces DB load.  

**Cons:**  
- Risk of data loss if cache crashes before flushing.  
- More complex implementation.  



### 4. **Time-to-Live (TTL) / Expiry-Based Invalidation**
- Cached items are automatically invalidated after a set time.  

**Example:**  
Instagram stories cached for 24 hours — after that, cache automatically invalidates them since stories themselves expire.  

**Analogy:** Milk cartons with expiry dates.  

**Pros:**  
- Simple and automatic.  
- Prevents stale data build-up.  

**Cons:**  
- Data may expire even if still valid, causing cache misses.  



### 5. **Event-Based / Explicit Invalidation**
- Cache is invalidated when a **specific event or trigger occurs** (manual or system-driven).  

**Example:**  
- When a user updates their profile picture on LinkedIn, the cache for that profile is explicitly invalidated so the new picture shows instantly.  
- Content Delivery Networks (CDNs) like Cloudflare allow manual cache purge when new website versions are deployed.  

**Analogy:** Erasing the whiteboard when a new lesson starts.  

**Pros:**  
- Provides real-time accuracy.  
- Only invalidates what’s necessary.  

**Cons:**  
- Requires additional logic and event tracking.  

## Interview Q&A

**Q1. What is caching and why is it important?**  
A: Caching is storing frequently accessed data in a faster, temporary storage layer (like Redis, Memcached, CDN). It reduces latency, improves user experience, and reduces load on backend systems.  

**Q2. What is cache hit vs cache miss?**  
A: Cache Hit = data found in cache; Cache Miss = data not found in cache, must fetch from DB.  

**Q3. How do you handle cache invalidation?**  
A: Strategies include time-to-live (TTL), manual eviction when data changes, or write-through/write-around caching.  

**Q4. What are cache eviction policies?**  
A: LRU, LFU, FIFO, Random Replacement. These decide what data to remove when cache is full.  

**Q5. Give a real-world example of caching.**  
A: YouTube uses caching in CDNs to store videos close to users so they don’t buffer every time.  

**Q6. What’s the downside of caching?**  
A: Stale data, limited storage, additional complexity.  

**Q7. Where would you not use caching?**  
A: For highly sensitive or frequently changing data like bank account balances.  



## Key Takeaways
- Caching = notebook for quick answers instead of library trips.  
- Reduces latency, cost, and backend load.  
- Cache invalidation and eviction are key challenges.  
- Implemented using in-memory caches (Redis, Memcached) or CDNs.  

---

## Topics to cover next
- Content Delivery Networks (CDNs).  
- Cache Invalidation Strategies.  
- Distributed Caching.  
- Database Query Optimization.  

---

## Navigation
- **Previous Topic:** Load Balancing  
- **Next Topic:** Content Delivery Networks (CDNs)  
- **Home:** [System Design Topics](../README.md)  
