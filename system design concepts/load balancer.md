# Load Balancer in System Design

A **Load Balancer** distributes incoming network traffic across multiple servers to ensure:
- ⚡ **Reliability** (failover handling)
- 📈 **Scalability** (handle traffic spikes)
- ⚖️ **Efficiency** (optimal resource utilization)

---

## 🌐 Types of Load Balancing

### 1️⃣ Layer 4 (Transport Layer)
| Characteristic | Details |
|---------------|---------|
| **Protocols** | TCP/UDP |
| **Routing** | IP + Port based |
| **Use Cases** | Gaming, VoIP, raw data streams |
| **Examples** | AWS Network Load Balancer |

### 2️⃣ Layer 7 (Application Layer)
| Characteristic | Details |
|---------------|---------|
| **Protocols** | HTTP/HTTPS |
| **Routing** | URL, Headers, Cookies |
| **Use Cases** | Web apps, APIs, microservices |
| **Examples** | Nginx, HAProxy, AWS ALB |

**Key Difference:**  
Layer 7 understands application content while Layer 4 only sees network packets.

---

## 🏗️ AWS Evolution Case Study

### Phase 1: Monolithic Architecture
```mermaid
graph LR
    Users -->|All Traffic| SingleServer[Single Web Server]
    SingleServer --> Database