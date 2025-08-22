# Load Balancing

Imagine you run a very popular ice-cream shop. Every summer, hundreds of kids come at the same time, and if you alone handle the counter, the line becomes too long, kids get frustrated, and some even leave. To solve this, you hire multiple workers at different counters. Each kid who comes in is directed to the counter with the shortest line, making sure everyone is served quickly and fairly.  

This is exactly what **Load Balancing** is in computer systems. Instead of ice-cream counters, we have servers. Instead of kids, we have incoming requests (users, API calls, database queries, etc.). **Load Balancing ensures that requests are evenly distributed across multiple servers so that no single server is overwhelmed while others are idle.**

Load balancers act as the **traffic police** in a busy city intersection—directing cars (requests) to available roads (servers) to prevent traffic jams.



## Why do we need Load Balancing?
- **The Problem:** If all traffic goes to one server, it gets overloaded, slows down, or even crashes. Meanwhile, other servers remain idle.  
- **The Solution:** A load balancer evenly distributes the requests, ensuring high availability, reliability, and better performance.

**Real-life example:**  
Imagine an online shopping festival like Black Friday. Millions of users rush to buy items. Without load balancing, one server might collapse under traffic, leading to a poor shopping experience and loss of revenue. With load balancing, requests are distributed across multiple servers, and users enjoy smooth transactions.  

**If we don’t use load balancing, consequences include:**  
- System crashes due to overload.  
- Poor user experience (slow responses, timeouts).  
- Loss of revenue/customers.  
- Wasted server resources (some idle, some overloaded).  



## How does it work?
1. A client (user/browser/app) sends a request.  
2. Instead of going directly to the server, the request first reaches the **load balancer**.  
3. The load balancer decides which server should handle this request based on certain algorithms.  
4. The chosen server processes the request and sends the response back to the load balancer.  
5. The load balancer forwards the response to the client.  

👉 A diagram (open-source image can be added):  

**Client → Load Balancer → Server 1 / Server 2 / Server 3 …**



## Relation to System Engineering / System Design
Load balancing is a **core building block** in distributed systems. It ensures:  
- **Scalability:** Easily add more servers to handle growing demand.  
- **High Availability:** If one server fails, traffic is routed to others.  
- **Reliability:** Consistent and predictable performance.  

In short, it makes large-scale systems resilient and capable of handling millions of users simultaneously.



## Before Load Balancing vs After Load Balancing
**Before Load Balancing:**  
- Requests go to a single server.  
- Server overload leads to crashes.  
- No fault tolerance.  
- Scaling limited by a single machine’s capacity.  

**After Load Balancing:**  
- Requests distributed evenly.  
- Servers work efficiently together.  
- Failover support if one server goes down.  
- Easy to add/remove servers dynamically.  



## Main Motive / Goal
The main goal of load balancing is to **ensure that no server is overwhelmed, improve fault tolerance, maximize resource usage, and provide a smooth, fast user experience.**



## Types of Load Balancing
### 1. **Hardware Load Balancer**
- Physical devices installed in data centers.  
- Extremely powerful but costly.  
- Example: F5 Networks appliances.  

### 2. **Software Load Balancer**
- Installed as software on commodity hardware.  
- Flexible and cheaper.  
- Example: NGINX, HAProxy.  

### 3. **DNS Load Balancing**
- Domain Name System distributes traffic to different IPs.  
- Used for geo-distribution.  

### 4. **Global Load Balancing**
- Balances traffic across multiple geographic regions or data centers.  
- Ensures low latency worldwide.  



## Load Balancing Algorithms
A load balancing algorithm decides **how incoming requests are distributed among multiple servers**. Choosing the right algorithm ensures fairness, efficiency, and reliability in handling user traffic. Different algorithms fit different scenarios depending on workload, server capacity, and traffic patterns.  

Below are the most commonly used load balancing algorithms explained in detail with examples.



## 1. Round Robin
- **Definition:** Requests are distributed sequentially across servers in rotation. Once the last server is reached, the next request goes back to the first server.  
- **Real-life Analogy:** Imagine students entering classrooms in order. First student goes to Class A, second to Class B, third to Class C, and the fourth again to Class A.  
- **Example:**  
   - Servers: `S1, S2, S3`  
   - Requests: `R1 → S1`, `R2 → S2`, `R3 → S3`, `R4 → S1`, `R5 → S2`, and so on.  
- **Use Case:** Best when all servers have **similar capacity and workload**.  



## 2. Least Connections
- **Definition:** Directs traffic to the server with the fewest active connections at the moment.  
- **Real-life Analogy:** Think of a bank with multiple counters. A new customer always goes to the counter with the **shortest queue**.  
- **Example:**  
   - `S1` has 10 active connections, `S2` has 4, and `S3` has 2.  
   - The next request will go to `S3` (least busy).  
- **Use Case:** Works best for systems with **long-lived connections** like video streaming, chat servers, or database connections.  



## 3. IP Hash
- **Definition:** A mathematical hash function uses the client’s IP address to consistently assign the same client to the same server.  
- **Real-life Analogy:** Imagine a cinema hall where ticket numbers decide the row. Every person with ticket numbers ending in 1-10 always goes to Row A, 11-20 to Row B, etc.  
- **Example:**  
   - User with IP `192.168.1.10` is always mapped to `S1`.  
   - User with IP `192.168.1.11` is always mapped to `S2`.  
- **Use Case:** Ensures **session persistence** (a user always goes back to the same server) which is important for shopping carts, gaming servers, or login sessions.  



## 4. Weighted Round Robin
- **Definition:** Each server is assigned a weight (capacity). Requests are distributed proportionally to weights.  
- **Real-life Analogy:** Think of three restaurants: a large one can serve 50 people, a medium one 30, and a small one 20. If 100 people arrive, the large gets 50, medium 30, and small 20.  
- **Example:**  
   - Weights: `S1 = 5`, `S2 = 3`, `S3 = 2`.  
   - For every 10 requests: `S1 gets 5`, `S2 gets 3`, `S3 gets 2`.  
- **Use Case:** Useful when servers have **different CPU, memory, or network capacities**.  



## 5. Weighted Least Connections
- **Definition:** Combines **least connections** with weights. A server with fewer active connections but higher capacity gets prioritized.  
- **Real-life Analogy:** A supermarket with different-sized counters: the bigger counters can serve multiple people at once. Even if a smaller counter has fewer customers, the bigger counter is chosen because it can handle more.  
- **Example:**  
   - `S1` weight = 5, `S2` weight = 1.  
   - Even if `S1` has 6 connections and `S2` has 2, the algorithm still sends traffic to `S1` because its capacity is higher.  
- **Use Case:** Ideal when servers differ in capacity **and traffic load is uneven**.  



## 6. Random Choice
- **Definition:** Each request is assigned to a server **randomly**.  
- **Real-life Analogy:** Imagine rolling a dice to pick which restaurant to eat at. Every roll is independent and random.  
- **Example:**  
   - `R1 → S2`, `R2 → S1`, `R3 → S3`, `R4 → S2`, purely based on random selection.  
- **Use Case:** Useful in **large-scale distributed systems** where fairness is less important and randomness itself distributes load fairly.  


### Key Takeaways
- **Round Robin:** Simple, fair, best for equal servers.  
- **Least Connections:** Best for systems with varying connection durations.  
- **IP Hash:** Ensures session persistence.  
- **Weighted RR & Weighted Least Connections:** Useful when servers differ in power.  
- **Random Choice:** Simple, scalable, and works well for large clusters.



## Advantages
- High availability and reliability.  
- Efficient use of servers.  
- Fault tolerance (fallback to healthy servers).  
- Scalability made easy.  
- Better performance and user experience.  



## Disadvantages
- Adds complexity to system architecture.  
- Single point of failure if load balancer itself crashes (needs redundancy).  
- Hardware load balancers can be expensive.  
- Requires constant monitoring and health checks.  



## How to implement Load Balancing
1. **Identify Need:** Decide why your system needs load balancing (traffic spikes, redundancy, etc.).  
2. **Choose Type:** Pick between hardware, software, or DNS-based load balancing.  
3. **Select Algorithm:** Round Robin, Least Connections, etc.  
4. **Setup Infrastructure:** Deploy multiple backend servers.  
5. **Configure Load Balancer:** Use tools like HAProxy, NGINX, AWS ELB, or GCP Load Balancer.  
6. **Health Checks:** Ensure automatic failover for unhealthy servers.  
7. **Monitoring & Scaling:** Continuously monitor and add/remove servers based on traffic.  



## Interview Q&A
**Q1: What is load balancing in simple terms?**  
A: Distributing incoming traffic across multiple servers so no single server is overwhelmed.  

**Q2: What are common algorithms used in load balancing?**  
A: Round Robin, Least Connections, Weighted algorithms, IP Hash.  

**Q3: Why is load balancing critical for high-traffic applications?**  
A: It prevents crashes, ensures availability, and provides scalability.  

**Q4: What’s the difference between horizontal scaling and load balancing?**  
A: Horizontal scaling adds more servers; load balancing ensures traffic is evenly distributed across them.  

**Q5: Scenario:** If one server goes down, how does load balancer handle it?  
A: It uses health checks to detect failure and automatically reroutes traffic to healthy servers.  



## Key Takeaways
- Load balancing is like a **traffic cop** for system requests.  
- Prevents overload, downtime, and improves performance.  
- Different algorithms help optimize request distribution.  
- It’s a must-have for scalable and reliable system design.  



## Topics to cover next
- **Auto Scaling** (works closely with load balancing).  
- **Failover Mechanisms**.  
- **Content Delivery Networks (CDNs)**.  
- **Service Discovery**.  



## Navigation
- ⬅️ Previous: **Scaling**  
- ➡️ Next: **Auto Scaling**  
- 🏠 Home: **System Design Index**
