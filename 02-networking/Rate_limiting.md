
## What is Rate Limiting?
Rate limiting is a way of **controlling how many requests a user, system, or application can make in a given time window**.  

Think of it like a **traffic signal at a busy intersection**. If everyone tries to cross at the same time, chaos happens — cars collide, traffic jams occur, and nobody moves. But when the signal controls the flow, cars move in order, fairly and safely.  

In computing, the “cars” are requests, and the “signal” is the rate limiter.  

### Everyday Example:
Imagine you’re at an ice cream shop that allows each customer to buy only **2 scoops per visit**. If a kid tries to buy 10 scoops, the cashier stops them: “Sorry, only 2 at a time.” This ensures there’s enough ice cream for everyone in line. That’s exactly how rate limiting works in systems — it prevents one greedy user (or bot) from hogging all the resources.  

### Real-world Example:
- **E-commerce flash sales**: When Amazon does a “Big Billion Sale,” thousands of users refresh the page every second. If there was no rate limiting, some users could overload the system with thousands of requests and slow down the website for everyone else. Rate limiting ensures every customer gets a fair chance.  
- **Login attempts in Banking apps**: Imagine someone trying to break into your account by guessing your password. Without rate limiting, they could try thousands of passwords per second. With rate limiting, after 5 failed attempts, the system locks further requests for a while.  
- **APIs like Twitter or Google Maps**: Developers can’t just spam them with unlimited queries. Twitter might say: *You can make 300 requests every 15 minutes*. That way, the service stays reliable.  

So, **rate limiting = rules that keep digital traffic smooth, fair, and safe**.  

---

## Why do we need Rate Limiting?
Rate limiting solves multiple real-world problems:  
- **Prevents Abuse**: Stops bots or malicious users from overwhelming a system.  
- **Protects Infrastructure**: Prevents backend crashes due to sudden traffic spikes.  
- **Ensures Fairness**: Every user gets equal access.  
- **Controls Costs**: External APIs often charge per request; limits keep costs predictable.  

Without it, even one bad actor could make the whole system unusable for everyone else.  


## How does it work?
Rate limiting works by defining **rules** like:  
- "A user can send 100 requests per minute"  
- "An IP can attempt only 5 logins in 10 minutes"  

These rules are enforced using **algorithms**. Let’s explore them in detail with examples.



### 1. Fixed Window Counter
This is the simplest method.  
- Divide time into fixed windows (e.g., 1 minute).  
- Each window allows a set number of requests.  

**Example**:  
- Limit: 100 requests per minute.  
- If you send 90 requests in the first 30 seconds and 10 more in the next 20 seconds, you’re fine.  
- But if you send the 101st request before the minute ends → rejected.  

**Problem**: Bursts at the boundary.  
Imagine it’s 12:00:59 and you send 100 requests. At 12:01:00 (new window), you send another 100 requests immediately. Effectively, you sent **200 requests in 2 seconds**. This can still overwhelm servers.  



### 2. Sliding Window
To fix the burst problem, sliding window looks at a **rolling time frame**.  
Instead of resetting at every minute boundary, it always checks the last 60 seconds (or any time span).  

**Example**:  
- Limit: 100 requests per minute.  
- If you send 90 requests at 12:00:30, and another 20 requests at 12:01:10 → the system counts requests from 12:00:10–12:01:10. That’s 110 requests → so the extra 10 are blocked.  

**Benefit**: Fairer and smoother, avoids sharp resets.  

**Analogy**: Imagine you’re filling a glass of water continuously. Instead of resetting the glass every minute, you always look at how much water is inside right now.  



### 3. Token Bucket
This is one of the most popular methods.  
- Each user gets a **bucket** of tokens.  
- Every request costs 1 token.  
- Tokens refill at a steady rate (e.g., 5 tokens per second).  

**Example**:  
Suppose the bucket holds 100 tokens. If you make 100 requests quickly, the bucket empties. You must wait until new tokens drip in. If refill is 5/sec, you’ll get 5 more requests after 1 second.  

**Analogy**: Imagine arcade tokens at a gaming center. You buy 100 tokens and play 100 games. To play more, you must wait until the machine dispenses more tokens.  

**Benefit**:  
- Allows bursts (if you’ve saved tokens).  
- But still controls overall rate.  



### 4. Leaky Bucket
This works like a bucket with a small hole.  
- Requests fill the bucket (like water).  
- But they leave at a **fixed rate**.  
- If requests arrive too fast and overflow the bucket → they’re dropped.  

**Example**:  
If your bucket capacity is 50 requests and leak rate is 10 requests/second, you can handle bursts temporarily. But if you send 100 requests at once, 50 are dropped.  

**Analogy**: Think of pouring soda into a glass with a hole at the bottom. You can pour quickly, but the liquid only leaves at a fixed speed. If you pour too much, it overflows.  

**Use case**: Best for ensuring a **steady flow of traffic** like video streaming or message queues.  



## Relation to System Engineering / System Design
Rate limiting is critical for:  
- **APIs**: Protects services like Google Maps, Twitter, AWS.  
- **Authentication systems**: Prevents brute force.  
- **Distributed systems**: Works with load balancers, caches, and gateways.  
- **Cloud cost control**: Limits API consumption.  

It is often implemented at **API Gateways (like Kong, NGINX, Apigee)** or **edge servers (like Cloudflare, AWS API Gateway)**.  



## Before Rate Limiting vs After Rate Limiting
- **Before**:  
  - A malicious user floods requests.  
  - Server crashes.  
  - Genuine users suffer.  

- **After**:  
  - Requests are controlled.  
  - Fair access for all.  
  - Stable performance, no overload.  



## Main Motive / Goal
To **maintain fairness, protect systems from abuse, and ensure reliability**.  



## Advantages
- Prevents denial-of-service attacks.  
- Controls API costs.  
- Ensures fairness.  
- Improves system stability.  



## Disadvantages
- If limits are too strict, genuine users are blocked.  
- Implementation in distributed systems is tricky (requires synchronization).  
- Adds complexity to architecture.  



## Interview Q&A

**Q1. What is rate limiting and why is it needed?**  
A: A technique to control request flow. Needed to prevent abuse, control load, ensure fairness, and protect infrastructure.  

**Q2. Explain the difference between Token Bucket and Leaky Bucket.**  
A:  
- Token Bucket allows bursts (you can save tokens).  
- Leaky Bucket enforces a fixed flow (no bursts).  

**Q3. How do you implement distributed rate limiting?**  
A: Use a centralized store like **Redis** to track counters/tokens across servers, so limits apply consistently.  

**Q4. What problems can occur if rate limiting is too strict?**  
A: Users may face errors or throttling even during genuine use, leading to poor user experience.  

**Q5. Give a real-world scenario where rate limiting saved a system.**  
A: In login systems, it prevents brute-force password guessing by blocking after a few failed attempts.  

---

## Key Takeaways
- Rate limiting = **traffic signal** for digital requests.  
- Algorithms: Fixed Window, Sliding Window, Token Bucket, Leaky Bucket.  
- Used in APIs, banking logins, e-commerce, and more.  
- Prevents abuse, ensures fairness, and keeps systems reliable.  
- Commonly implemented with **Redis + API Gateway** in distributed systems.  

---
