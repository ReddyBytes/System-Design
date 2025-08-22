# API Gateway


## What is API Gateway?

Imagine you are visiting a **shopping mall**. Instead of going to each shop’s hidden storeroom to find items, you enter through the **main entrance**. At the entrance, there’s an **information desk**. The desk guides you where to go, helps you with directions, sometimes even handles billing, and makes your shopping smooth.  

In software systems, this **information desk** is what we call an **API Gateway**.  
It acts as the single entry point for all client requests in a system built on **microservices**.  

Without an API Gateway, a mobile app, web app, or any external client would have to directly call each microservice, know their addresses, handle authentication separately, and manage failures. With an API Gateway, all of this is centralized — clients talk to one door, and the Gateway takes care of the messy backend communications.  

An **API Gateway** can:  
- `Route` requests to the right microservice.  
- Handle authorization, authentication, rate limiting, caching, and logging.  
- Aggregate responses from multiple services and send back a single response.  



## Why do we need API Gateway?

Let’s say you are building an **e-commerce application**:  
- The frontend app needs product info (Product Service), user profile (User Service), order status (Order Service), and payment confirmation (Payment Service).  
- Without an API Gateway, the app would need to separately call all these services, manage failures, authenticate each call, and handle retries. This is complex and inefficient.  

Now, **what if we don’t use an API Gateway?**  
- The client will need to remember addresses of all services.  
- If the Product Service moves to another server, every client must be updated.  
- Security must be implemented in every microservice, leading to duplication.  
- The client could be overloaded with making multiple calls.  

With an **API Gateway**, the frontend just makes **one request**. The Gateway:  
- Routes it to the right services.  
- Secures it with one central authentication.  
- Combines responses into a clean output.  
- Shields backend complexity from the client.  

👉 In real life, this is like calling a **customer support number** for a company. Instead of calling every department (billing, tech support, sales) separately, you call one number, and they route your issue internally.  


## Key Differences between API and API Gateway

| Aspect | API | API Gateway |
|--------|-----|-------------|
| **Definition** | Contract that defines communication between two software components. | A server that acts as a single entry point to manage multiple APIs. |
| **Scope** | Exposes **one service’s** functionality. | Manages and routes to **many services**. |
| **Usage** | Used directly by clients or other services. | Used by clients as a mediator to access multiple APIs. |
| **Responsibilities** | Defines endpoints, input/output, and business logic. | Handles routing, authentication, rate limiting, caching, monitoring, and aggregation. |
| **Security** | Each API must implement its own security. | Centralized security at one point. |
| **Real-life Analogy** | A single shop in a mall (you directly buy items). | Mall reception desk (you go to one place, and it coordinates everything). |
---


## How does it work?

The API Gateway acts as a **middleman** between clients and services.  

**Step-by-step working**:
1. A client (mobile app, web app, IoT device) sends a request.  
2. The API Gateway receives the request.  
3. The Gateway checks authentication/authorization.  
4. It applies policies like **rate limiting**, **caching**, or **logging**.  
5. The Gateway forwards the request to the correct microservice(s).  
6. If multiple microservices are involved, it aggregates their responses.  
7. The Gateway sends the final response back to the client.  




## Relation to System Engineering / System Design

API Gateway is a **key building block** in microservice architecture.  
- It **reduces client complexity** by hiding service discovery, load balancing, and routing.  
- It enables **centralized cross-cutting concerns** (security, logging, monitoring, caching).  
- It makes system **scalable and maintainable** since microservices can change without affecting clients.  



## Before API Gateway vs After API Gateway

| Without API Gateway (Before) | With API Gateway (After) |
|-------------------------------|--------------------------|
| Clients must talk to every service separately. | Clients only talk to the Gateway. |
| Service endpoints are exposed directly. | Backend services are hidden and protected. |
| Security & logging duplicated in every service. | Centralized at the Gateway. |
| High client complexity. | Simple, clean client integration. |



## Main Motive / Goal

The **main motive** of API Gateway is to:  
- Provide a **single entry point** for all clients.  
- Simplify communication in distributed systems.  
- Improve **security, scalability, and maintainability**.  



## Advantages

- Single entry point for all services.  
- Centralized authentication and authorization.  
- Easier client integration (especially mobile apps).  
- Enables caching, rate limiting, and monitoring.  
- Can perform request aggregation.  
- Protects backend microservices from direct exposure.  



## Disadvantages

- Can become a **single point of failure**.  
- Introduces **latency** since all traffic flows through it.  
- Complexity in configuration and management.  
- Needs to be **highly scalable**; otherwise, it becomes a bottleneck.  
- Debugging becomes harder due to multiple layers.  



## Interview Q&A

**Q1: What is an API Gateway and why is it needed?**  
A: An API Gateway is a single entry point that sits between clients and backend microservices. It simplifies communication by handling routing, authentication, logging, rate limiting, and aggregation of responses. Without it, clients must directly interact with all microservices, increasing complexity.  

**Q2: What problems does an API Gateway solve in microservices?**  
A: It solves problems like:  
- Service discovery complexity.  
- Centralizing authentication & authorization.  
- Handling cross-cutting concerns (logging, rate limiting, caching).  
- Simplifying client integration.  

**Q3: Can an API Gateway be a bottleneck?**  
A: Yes. Since all traffic passes through the Gateway, if not scaled properly, it can become a bottleneck or a single point of failure. High availability and redundancy strategies are crucial.  

**Q4: Give a real-world analogy of an API Gateway.**  
A: A **customer support desk**: instead of calling each department (billing, sales, technical), you call one number, and the operator routes your issue to the right department.  

**Q5: What’s the difference between API Gateway and Load Balancer?**  
A: A load balancer distributes traffic across servers, while an API Gateway routes traffic to the right microservice and handles extra responsibilities like authentication, rate limiting, and aggregation.  

**Q6: In an e-commerce app, how would you use API Gateway?**  
A: A mobile app requests order history. Instead of calling User Service, Order Service, and Product Service separately, it calls the API Gateway. The Gateway fetches data from all services, combines them, and returns a single response to the app.  



## Key Takeaways

- API Gateway = **single entry point** for clients in microservice architecture.  
- Simplifies client-side logic by handling routing, security, logging, and aggregation.  
- Essential for modern **distributed systems** and **system design**.  
- Needs proper scaling to avoid becoming a bottleneck.  



## Topics to cover next

- Service Discovery  
- Load Balancers vs API Gateway  
- Rate Limiting (deep dive)  
- Reverse Proxy vs API Gateway  
- gRPC Gateways  



## Navigation

- 🔙 [Previous Topic: API](./API.md)  
- ⏭️ [Next Topic: Load Balancer](./Load-Balancer.md)  
- 🏠 [Home](../README.md)  






