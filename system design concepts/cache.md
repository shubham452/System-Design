# Cache in System Design

## 🛠️ What is Caching?
Caching is a technique used to store frequently accessed data in a fast storage layer to reduce latency and improve system performance. Instead of fetching data from a slow database or making an expensive computation every time, the system retrieves it from a cache, which is much faster.

---

## 🛠️ Example: YouTube Video Streaming
1️⃣ A user watches a video → YouTube fetches the video from its storage.  
2️⃣ Instead of retrieving the video every time from the main database, YouTube caches popular videos in a Content Delivery Network (CDN) near the user's location.  
3️⃣ The next time another user requests the same video, it loads instantly from the cache instead of streaming from YouTube's main servers.  

✅ **Benefits:**  
- Faster video load times  
- Reduced load on backend servers  

---

## 🔴 Why is Caching Needed?
- 🚫 **Slow Database Queries** → Fetching data from a relational database takes time.  
- 🚫 **Expensive Computation** → Calculating trending hashtags on Twitter for every request is inefficient.  
- 🚫 **High Traffic** → If millions of users request the same data, caching prevents the backend from overloading.  

---

## 🟢 Where is Caching Used?

| Cache Type | Description | Example |
|------------|-------------|---------|
| **Application-Level Cache** | Stores frequently used data in application memory | Django & Express.js user sessions |
| **Database Query Cache** | Stores results of frequent database queries | Amazon product details |
| **Content Delivery Network (CDN)** | Stores static files close to users | Netflix, YouTube video streaming |
| **Web Browser Cache** | Stores assets in user's browser | Facebook profile pictures |
| **Distributed Caching** | Caches data across multiple servers | Twitter tweet caching |

---

## 🛠️ Cache Strategies

| Strategy | Description | Use Case |
|----------|-------------|----------|
| **Write-Through** | Writes to cache and DB simultaneously | Banking transactions |
| **Write-Back** | Writes to cache first, DB later | Product recommendations |
| **LRU Eviction** | Removes least recently used data | Web browsers |
| **TTL Expiry** | Data expires after set time | Stock prices |

---

## 📌 Cache vs. Database

| Feature | Cache (Redis/Memcached) | Database (MySQL/PostgreSQL) |
|---------|-------------------------|----------------------------|
| Speed | Fast (microseconds) | Slow (milliseconds) |
| Storage | Small (RAM-based) | Large (Disk-based) |
| Persistence | Temporary | Permanent |
| Use Case | High-speed retrieval | Long-term storage |

---

## 🛠️ When to Use Caching?
✅ When data is frequently accessed  
✅ When database queries are slow  
✅ When handling millions of concurrent users  

🚫 **When NOT to Use Caching?**  
❌ When data needs 100% consistency  
❌ When data changes frequently  

---

# Cache Invalidation Techniques

## 🛠️ Why is Cache Invalidation Important?
🚨 **Problem:** Cached data can become stale if not updated  
✅ **Solution:** Proper invalidation ensures consistency while keeping speed  

---

## 🔴 Types of Cache Invalidation Strategies

| Strategy | How It Works | Best For | Trade-offs |
|----------|-------------|----------|------------|
| **TTL Expiry** | Data expires after set time | Stock prices | May lead to stale data |
| **LRU Eviction** | Removes least used data | Web browsers | Might remove important data |
| **Write-Through** | Writes to both cache & DB | Banking systems | Slower writes |
| **Write-Back** | Writes to cache first, DB later | Game leaderboards | Risk of data loss |
| **Cache-Aside** | Loads from cache if available | Netflix | Cache miss causes delay |

---

# Redis vs. Memcached vs. CDN Caching

## 🔹 Comparison Table

| Feature | Redis | Memcached | CDN |
|---------|-------|-----------|-----|
| Type | In-memory key-value store | In-memory key-value store | Static content delivery |
| Persistence | ✅ Yes | ❌ No | ❌ No |
| Data Types | Supports complex structures | Key-value only | Static files only |
| Best For | Real-time apps | Simple caching | Global content delivery |

---

# Redis Advanced Features

## 🔹 Redis Replication vs. Clustering vs. Pub/Sub

| Feature | Replication | Clustering | Pub/Sub |
|---------|-------------|------------|---------|
| Use Case | Read scalability | Horizontal scaling | Real-time messaging |
| Scalability | Read scaling only | Read + write scaling | Not for storage |
| Best For | High availability | Large-scale apps | Chat applications |

---

## 📌 Final Thoughts
✅ **Caching** is essential for performance but requires proper invalidation  
✅ **Redis** is best for complex, persistent caching  
✅ **Memcached** is ideal for simple, high-speed caching  
✅ **CDN** is perfect for global static content delivery  
✅ Advanced Redis features enable distributed systems at scale  