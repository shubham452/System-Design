
# CAP Theorem (Consistency, Availability, Partition Tolerance)

The CAP theorem, proposed by Eric Brewer, states that a distributed system can only guarantee two out of the three:

1.	Consistency (C) – Every node sees the same data at the same time.  
2.	Availability (A) – Every request gets a response (even if some nodes are down).  
3.	Partition Tolerance (P) – The system continues to function despite network failures.  

⚠️ No system can achieve all three simultaneously in a distributed environment.

---

## When to Use What?

| Scenario | Consistency (CP) | Availability (AP) |
|----------|------------------|-------------------|
| Financial Transactions (Banking, Payment Gateways) | ✅ Data must be consistent (double-spending prevention). | ❌ Might reject requests during failures. |
| Social Media Feeds, E-Commerce Recommendations | ❌ Slightly outdated data is okay. | ✅ Always available, even if some nodes fail. |
| Distributed Databases (NoSQL, SQL) | ✅ Use CP for strict consistency (MongoDB, HBase). | ✅ Use AP for high availability (Cassandra, DynamoDB). |

---

## CAP Theorem Example: Amazon DynamoDB

### 1️⃣ Initial Phase (Single Datacenter)

•	Amazon initially stored data in a single location for strong consistency.  
•	As traffic grew, a single database became a bottleneck and caused downtime.

### 2️⃣ Scaling with Partitioning

•	Amazon distributed its database across multiple data centers.  
•	Network failures (partitions) became common, requiring a trade-off.

### 3️⃣ Choosing Availability over Consistency

•	Amazon chose an AP system (Availability & Partition Tolerance) over Consistency:  
  - Availability: Users could always add items to their shopping carts.  
  - Eventual Consistency: If a failure occurred, it might take a few seconds to update data across all nodes.  
  - Conflict Resolution: DynamoDB uses versioning to resolve conflicts when nodes are out of sync.

### Final Scalable System

•	DynamoDB prioritizes Availability (ensuring reads/writes always succeed).  
•	Consistency is eventual, meaning recent updates might take time to propagate.  
•	Partition Tolerance is always maintained (since networks can fail).

---

## Key Takeaways

•	CP Systems (MongoDB, HBase) → Choose when strong consistency is required (e.g., banking, financial systems).  
•	AP Systems (DynamoDB, Cassandra) → Choose when high availability is needed (e.g., social media feeds, e-commerce).  
•	Eventual Consistency works well for non-critical real-time applications like chat apps, product recommendations, and content delivery.

---

## Can Consistency and Availability Be Gained at the Same Time?

**No**, Consistency (C) and Availability (A) cannot be fully achieved at the same time in a distributed system due to CAP Theorem. The moment a network partition (P) occurs, a system has to choose between:

1.	**Consistency (CP)** → Ensures all nodes have the same data, but some requests may fail.  
2.	**Availability (AP)** → Ensures every request gets a response, but data may be stale.

---

## What if There's No Network Partition (P)?

If there's no partition (P) (meaning perfect network reliability), then both Consistency and Availability can be achieved. However, in real-world distributed systems, network failures are inevitable, which is why Partition Tolerance (P) is always required.

---

## Can We Achieve "Near" Consistency & Availability Together?

Some systems balance both Consistency and Availability by using:

1.	**Tunable Consistency** → Users can choose between strong and eventual consistency (e.g., DynamoDB, Cassandra).  
2.	**Read Repair & Conflict Resolution** → Ensures eventual consistency while keeping the system highly available.  
3.	**Quorum-Based Systems** (Amazon Dynamo, Google Spanner) → Reads/Writes require a majority of nodes to agree, achieving a mix of both availability and consistency.

---

## Example: Google Spanner (CP + "High" Availability)

Google Spanner leans towards CP (Consistency & Partition Tolerance) while offering high availability by:

•	Using a globally synchronized clock (TrueTime API) to maintain strong consistency.  
•	Distributing data across multiple regions while ensuring transactional consistency.  
•	Providing high availability through automated failover.

🔹 **Trade-off** → It's not 100% available like AP systems but is highly available for most practical use cases.

---

## Key Takeaways

•	CAP theorem forces a trade-off between Consistency & Availability when a partition happens.  
•	If there’s no partition, C & A can be achieved together.  
•	Systems like Google Spanner, DynamoDB, and Cassandra try to balance both using tunable consistency.  
•	For financial transactions, CP is preferred. For social media feeds, AP is preferred.

---

## Real-World Use Case: Banking System vs. Social Media Feeds

### 1️⃣ Banking System (CP – Consistency & Partition Tolerance)

**Scenario**:  
Imagine you transfer $1,000 from your Account A to Account B.  

•	The system must ensure the money is deducted from Account A and added to Account B, no matter what.  
•	Even if there’s a network failure (P), the system should not allow stale or conflicting data.

**Trade-off**:  
•	Prioritizing Consistency (C) → The system ensures that all nodes always have the latest transaction data.  
•	Limited Availability (A) → If a database shard holding your account is temporarily unavailable, your transaction might be delayed rather than processed with incorrect data.  
•	Partition Tolerance (P) is necessary → because network failures can happen.

**Technology Example**:  
•	Google Spanner (CP) → Provides global consistency for transactions across distributed databases.  
•	Relational Databases (SQL, PostgreSQL, MySQL) → Ensure ACID compliance.

✅ **Why CP?** → In banking, losing consistency (stale balances) can lead to financial losses, so availability is sacrificed in case of a network failure.

---

### 2️⃣ Social Media Feeds (AP – Availability & Partition Tolerance)

**Scenario**:  
You're scrolling through Instagram or Twitter, and you see posts from your friends.

•	Even if the backend system is experiencing network failures, you should still be able to load some posts (even if they’re slightly outdated).  
•	The system prioritizes Availability (A) over Consistency (C) because users expect content without delays.  
•	If a network partition occurs, the system does not block reads or writes—instead, it may return slightly stale data.

**Trade-off**:  
•	Prioritizing Availability (A) → Users can always see posts even if they are slightly outdated.  
•	Relaxed Consistency (C) → A newly posted tweet might take a few seconds to appear globally.  
•	Partition Tolerance (P) is required because users are distributed globally.

**Technology Example**:  
•	Apache Cassandra, Amazon DynamoDB (AP systems) → Prioritize high availability with eventual consistency.  
•	CDNs (Content Delivery Networks) → Cache and serve content globally, ensuring availability even when some data centers are unreachable.

✅ **Why AP?** → Users prefer fast-loading content even if it's slightly outdated, rather than waiting for perfect consistency.

---

## Final Takeaways

| Use Case | Prioritization | Example Technologies |
|----------|----------------|----------------------|
| Banking Transactions | CP (Consistency + Partition Tolerance) | Google Spanner, PostgreSQL, MySQL |
| Social Media Feeds | AP (Availability + Partition Tolerance) | DynamoDB, Cassandra, CDNs |
| E-commerce Inventory | Tunable (Mix of CP & AP) | Amazon DynamoDB with quorum-based consistency |

---

## Balancing Consistency and Availability: E-Commerce Inventory System (Tunable Consistency)

**Scenario**:  
Imagine you’re shopping on Amazon during a big sale. There’s only 1 unit of a PlayStation 5 left in stock.

✅ **Goal**: Ensure that two users don’t buy the last unit at the same time while keeping the website responsive.

💡 **Challenge**:  
•	If strict consistency (CP) is enforced, every purchase request must verify stock before confirming the order. This can slow down purchases.  
•	If high availability (AP) is enforced, users might purchase the same unit before the inventory updates, leading to overselling.

---

## How Amazon Handles This?

### Step 1: Mix of CP & AP (Tunable Consistency)

•	**Strong consistency for checkout** → When a user adds an item to the cart and checks out, the system ensures the stock is still available.  
•	**Eventual consistency for browsing** → The product page may show slightly outdated stock info to keep pages loading fast.

### Step 2: Optimizations

•	**Quorum-based Writes** (DynamoDB & Cassandra) → Instead of waiting for all servers to update stock, only a subset (e.g., majority of nodes) must confirm.  
•	**Distributed Locks** → When a user begins checkout, a temporary lock is placed on the item to prevent others from purchasing it at the same time.

---

## Trade-offs in an E-Commerce System

| Approach | Consistency (C) | Availability (A) | Use Case |
|----------|------------------|------------------|----------|
| Strong Consistency (CP) | ✅ High | ❌ Low | Payment Processing, Order Finalization |
| Eventual Consistency (AP) | ❌ Lower | ✅ High | Product Listings, Recommendations |
| Tunable (Balance CP & AP) | ⚖️ Adjustable | ⚖️ Adjustable | Inventory Management |

---

📌 **Example Technologies Used in E-commerce**:

•	DynamoDB with "strongly consistent reads" for order placement  
•	Cassandra with quorum reads/writes for inventory stock  
•	Redis caching for product availability (AP mode)  

✅ **Final Answer**:  
E-commerce systems balance consistency and availability by using strong consistency where necessary (checkout) and eventual consistency where possible (product listings, recommendations).
