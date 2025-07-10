# 📌 What is an API Gateway?

An API Gateway is a server that acts as an entry point for all client requests in a microservices architecture.  
It handles request routing, authentication, rate limiting, and other cross-cutting concerns.

---

## 🛠️ Example: E-commerce App

When a user places an order:  
1️⃣ The request goes to the **API Gateway**  
2️⃣ It routes the request to the **Order Service**  
3️⃣ Also calls **Payment** and **Inventory Services**  
4️⃣ Combines the responses and returns it to the user  

---

## 🔵 Why Use an API Gateway?

- ✅ **Single point of entry** for all client apps (web, mobile, etc.)  
- ✅ **Hides the complexity** of microservices  
- ✅ **Centralized authentication**, logging, rate limiting, and caching  
- ✅ Helps enforce **security** and **standardized responses**  

---

## 📌 Key Features of API Gateway

| Feature               | Description |
|-----------------------|-------------|
| **Routing**           | Routes requests to the appropriate service based on the URL path |
| **Authentication**    | Validates tokens (e.g., JWT) before forwarding requests |
| **Rate Limiting**     | Restricts the number of requests to prevent abuse |
| **Caching**           | Caches common responses to reduce backend load |
| **Request Aggregation** | Combines results from multiple services into one response |
| **Load Balancing**    | Distributes traffic across service instances |
| **Monitoring/Logging** | Logs incoming requests and performance metrics |

---

## 📌 Real-World Example: Netflix API Gateway

- Netflix uses an **Edge API Gateway** to handle millions of requests from different devices.  
- It performs **device detection, authorization, throttling**, and routes to the right service (movies, accounts, etc.).  

---

## 📌 Popular API Gateway Tools

| Tool                  | Use Case / Highlights |
|-----------------------|-----------------------|
| **NGINX**            | High-performance gateway with load balancing, caching |
| **Kong**             | Open-source, plugin-based gateway for microservices |
| **Amazon API Gateway** | Fully managed gateway for AWS Lambda & services |
| **Apigee**           | Google’s enterprise-grade API management platform |
| **Zuul (Netflix)**   | Java-based gateway with dynamic routing & filtering |