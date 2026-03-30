## ⚖️ What is a Load Balancer?

A **load balancer** is a system that **distributes incoming traffic across multiple servers** to ensure:

* High availability
* Scalability
* Low latency
* Fault tolerance

👉 Instead of one server handling all requests (and crashing), traffic is spread intelligently.

---

# 🧠 Why Do We Need Load Balancers?

### ❌ Without Load Balancer

```text
Client → Server (overloaded ❌)
```

Problems:

* Server crashes
* Slow response
* Single point of failure

---

### ✅ With Load Balancer

```text
Client → Load Balancer → [Server1, Server2, Server3]
```

Benefits:

* No overload
* Better performance
* Fault tolerance

---

# 🔄 How Load Balancer Works (Step-by-Step)

1. Client sends request
2. Request hits load balancer
3. Load balancer:

    * Picks a backend server
    * Forwards request
4. Server responds
5. Response goes back via load balancer

---

## 🔄 Visual Flow

---

# 🏗️ Types of Load Balancers

---

## 🔹 1. Layer 4 Load Balancer (Transport Layer)

Works at:

* TCP / UDP level

### How it decides:

* IP address
* Port

👉 Fast but less intelligent

---

## 🔹 2. Layer 7 Load Balancer (Application Layer)

Works at:

* HTTP / HTTPS level

### How it decides:

* URL path (`/api/users`)
* Headers
* Cookies

👉 Smarter routing

---

## 🔥 Key Interview Line

> “L4 is faster, L7 is smarter.”

---

# ⚙️ Load Balancing Algorithms

---

## 🔸 1. Round Robin

```text
Request1 → Server1  
Request2 → Server2  
Request3 → Server3  
```

👉 Simple and commonly used

---

## 🔸 2. Least Connections

* Sends request to server with **fewest active connections**

👉 Better for uneven load

---

## 🔸 3. Weighted Round Robin

* More traffic to stronger servers

```text
Server1 (weight 3) → more requests  
Server2 (weight 1)
```

---

## 🔸 4. IP Hash

* Same user → same server

👉 Useful for session stickiness

---

# 🔐 Key Features of Load Balancer

---

## 1. Health Checks

* Continuously checks if servers are alive

👉 Removes unhealthy servers automatically

---

## 2. Failover

* If server fails → traffic redirected

---

## 3. SSL Termination

```text
HTTPS → Load Balancer → HTTP (internal)
```

👉 Offloads encryption work

---

## 4. Session Persistence (Sticky Sessions)

* Same user → same server

👉 Needed for session-based apps

---

# 🌍 Types Based on Deployment

---

## 🔹 1. Hardware Load Balancer

* Physical device
* Expensive
* Used in enterprises

---

## 🔹 2. Software Load Balancer

* Runs on servers

Examples:

* NGINX
* HAProxy

---

## 🔹 3. Cloud Load Balancer

Examples:

* AWS Elastic Load Balancer
* Google Cloud Load Balancing

---

# 🧩 Load Balancer in System Design

---

## 🔹 Typical Architecture

```text
User
 ↓
DNS
 ↓
Load Balancer
 ↓
Application Servers
 ↓
Database
```

---

## 🔹 Multi-Level Load Balancing

```text
DNS → Global LB → Regional LB → Services
```

👉 Used in large-scale systems (Netflix, Amazon)

---

# 🔥 Advanced Concepts (SDE-2 Level)

---

## 🔸 1. Anycast Routing

* Same IP across multiple locations
* Routes to nearest server

---

## 🔸 2. Auto Scaling Integration

* Adds/removes servers dynamically

---

## 🔸 3. Zero Downtime Deployment

* Gradually shift traffic

---

## 🔸 4. Blue-Green Deployment

```text
Old Version → Blue  
New Version → Green  
```

👉 Switch traffic safely

---

# ⚠️ Limitations

---

## ❌ Single Point of Failure

👉 Solution:

* Multiple load balancers

---

## ❌ Latency Overhead

👉 Extra network hop

---

## ❌ Sticky Sessions Problem

👉 Hard to scale

---

# 🧠 Load Balancer vs Reverse Proxy

👉 Smart answer:

> “A load balancer is a specialized reverse proxy focused on distributing traffic, while reverse proxy also handles caching, security, and routing.”

---

# 🚀 Real-World Example

### Scenario: E-commerce Website

1. User hits website
2. Load balancer:

    * Sends request to least loaded server
3. Server processes order
4. Response returned

---

# 🔑 Final Takeaways

* Load balancer = **traffic manager**
* Ensures:

    * High availability
    * Scalability
    * Reliability
* Works at:

    * L4 (fast)
    * L7 (smart)
* Uses algorithms like:

    * Round robin
    * Least connections

---

# 💡 Interview Power Statement

> “A load balancer distributes incoming traffic across multiple servers to ensure high availability and scalability. It can operate at L4 or L7, use different algorithms like round robin or least connections, and plays a critical role in fault tolerance and system performance.”

---

