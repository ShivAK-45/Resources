## 🚦 What is Routing in Backend?

**Routing** is the mechanism in a backend system that determines **how an incoming request (URL + method) is mapped to a specific handler (function/controller)** that processes it and returns a response.

Think of routing as a **traffic controller** for your backend:

* A request comes in → `/users/123`
* Routing decides → “This should go to `getUserById()`”

---

## 🧠 Core Idea

At its simplest:

```
HTTP Request (URL + Method) → Router → Handler → Response
```

Example:

```
GET /users/123 → UserController.getUser(123)
```

---

## 🔍 How Requests Find Their Way Home (Step-by-Step Flow)

### 1. Client Sends Request

* Browser / Mobile App / API client sends:

```
GET https://api.example.com/users/123
```

---

### 2. Request Reaches Server

* Hits backend server (Node.js / Spring Boot / Django)

---

### 3. Routing Layer Matches the Request

Routing uses:

* **Path** → `/users/123`
* **HTTP Method** → `GET`
* **Headers / Query Params (optional)**

Router checks defined routes:

```java
@GetMapping("/users/{id}")
public User getUser(@PathVariable int id) { ... }
```

Match found ✅

---

### 4. Path Parameters Extracted

From:

```
/users/123
```

We extract:

```
id = 123
```

---

### 5. Handler / Controller Invoked

```java
getUser(123)
```

* Business logic runs
* DB call happens
* Data processed

---

### 6. Response Sent Back

```
200 OK
{
  "id": 123,
  "name": "Shiva"
}
```

---

## 🧩 Types of Routing

### 1. Static Routing

Fixed paths:

```
GET /about
GET /contact
```

---

### 2. Dynamic Routing (Path Params)

```
GET /users/{id}
GET /orders/{orderId}
```

---

### 3. Query-Based Routing

```
GET /products?category=electronics
```

---

### 4. Nested Routing

```
GET /users/123/orders
```

---

### 5. Method-Based Routing

Same path, different methods:

```
GET    /users   → Fetch users
POST   /users   → Create user
PUT    /users   → Update user
DELETE /users   → Delete user
```

---

## ⚙️ Routing in Different Frameworks

### 🟢 Spring Boot (Java)

```java
@RestController
@RequestMapping("/users")
public class UserController {

    @GetMapping("/{id}")
    public User getUser(@PathVariable int id) {
        return userService.getUser(id);
    }
}
```

---

### 🟡 Express.js (Node.js)

```javascript
app.get('/users/:id', (req, res) => {
    const id = req.params.id;
    res.send(getUser(id));
});
```

---

### 🔵 FastAPI (Python)

```python
@app.get("/users/{id}")
def get_user(id: int):
    return get_user_service(id)
```

---

## 🧠 Internal Working of Routers (Deep Dive - SDE 2 Level)

### 1. Route Matching Algorithms

#### a. Linear Scan (Basic)

* Check routes one by one
* O(n) complexity

#### b. Trie-Based Routing (Optimized)

* URL paths stored in a **prefix tree (Trie)**
* Faster matching

Example:

```
/users
/users/:id
/users/:id/orders
```

Stored as tree:

```
root
 └── users
      └── :id
           └── orders
```

👉 Matching becomes efficient (O(length of path))

---

### 2. Middleware Chain

Routing often works with middleware:

```
Request → Auth Middleware → Logging → Router → Controller
```

Example:

```javascript
app.use(authMiddleware);
app.get('/users/:id', handler);
```

---

### 3. Route Prioritization

Problem:

```
/users/me
/users/:id
```

If user hits `/users/me`:

* Should it match `me` as ID? ❌
* Or static route? ✅

👉 Framework resolves using:

* **Static routes first**
* Then dynamic

---

### 4. Reverse Routing (Advanced Concept)

Instead of hardcoding URLs:

```java
"/users/" + id
```

Use:

```java
linkTo(UserController.class).slash(id)
```

👉 Avoids broken links when routes change

---

### 5. Load Balancer + Routing (System Design Angle)

At scale:

```
Client → Load Balancer → API Gateway → Service Router → Microservice
```

Routing happens at multiple layers:

* L7 (HTTP routing)
* API Gateway (path-based routing)
* Internal service routing

---

## ⚠️ Common Problems in Routing

### 1. Route Conflicts

```
/users/:id
/users/new
```

---

### 2. Over-fetching / Under-fetching

Bad route design:

```
GET /getUserDetailsAndOrdersAndPayments
```

---

### 3. Versioning Issues

```
/v1/users
/v2/users
```

---

### 4. Security Risks

* Exposing sensitive routes
* Missing auth middleware

---

## 💡 Best Practices (SDE 2 Level Expectations)

* Use **RESTful conventions**
* Keep routes **resource-based**, not action-based
* Avoid deep nesting (>2 levels)
* Use proper HTTP methods
* Apply middleware at correct layers
* Version your APIs properly
* Use consistent naming

---

## 🎯 SDE 2 Level Interview Questions

### 🔹 Conceptual

1. What is routing and how does it work internally?
2. How does a router differentiate between `/users/me` and `/users/:id`?
3. What are path parameters vs query parameters?
4. How does middleware interact with routing?

---

### 🔹 Deep Dive

5. How would you design a high-performance routing system?

    * Expectation: Trie, caching, hash maps

6. How does routing work in Spring Boot internally?

    * DispatcherServlet
    * HandlerMapping
    * HandlerAdapter

7. How would you handle route conflicts?

---

### 🔹 Coding / Design

8. Implement a basic router in Java / Python

    * Input: method + path
    * Output: handler function

9. Design routing for a multi-tenant SaaS:

```
/tenant/{tenantId}/users
```

---

### 🔹 System Design

10. How does routing work in microservices architecture?
11. Difference between API Gateway routing vs service-level routing
12. How would you implement rate limiting per route?

---

### 🔹 Scenario-Based

13. You have 1M routes (e.g., dynamic APIs). How will you optimize matching?
14. How do you handle backward compatibility when routes change?
15. How do you debug a routing issue in production?

---

## 🧩 Real-World Analogy

Routing is like:

* **Google Maps navigation**
* You give a destination (URL)
* It finds the best route (handler)
* Avoids conflicts (traffic rules)
* Reaches destination (response)

---

## 🚀 Final Insight (Important for You)

Since you're targeting **SDE 2 backend roles**, you should think beyond “just mapping URLs”:

👉 Focus on:

* Performance (Trie vs HashMap)
* Scalability (API Gateway)
* Maintainability (REST design)
* Debugging (logs, tracing)
* Security (auth before routing)

---

Great — let’s run this like a **real SDE 2 backend interview**.
I’ll behave like an interviewer: asking questions, pushing deeper, and adapting based on your answers.

⚠️ **Important**: Don’t just read — **answer each question**. I’ll then challenge you further like in a real interview.

---

# 🎯 Interview Setup (Routing Focus)

* Role: **SDE 2 Backend (Java-heavy)**
* Focus: Routing internals, scalability, design thinking
* Format:

    1. Conceptual
    2. Deep dive
    3. Coding / LLD
    4. System design twist

---

# 🧩 Round 1: Fundamentals

### ❓ Q1: What is routing in backend systems?

👉 Follow-up (after your answer):

* How is routing different from **load balancing**?
* Where does routing sit in request lifecycle?

---

### ❓ Q2: Suppose we have these routes:

```
GET /users/me
GET /users/:id
```

👉 Question:
How should the router decide which one to match for:

```
/users/me
```

👉 Follow-ups:

* What if framework matches `/users/:id` first?
* How would YOU design this to avoid ambiguity?

---

# 🧠 Round 2: Internal Working (Depth Check)

### ❓ Q3: How do frameworks like Spring Boot handle routing internally?

👉 Expected direction (don’t jump directly):

* What is the role of DispatcherServlet?
* What is HandlerMapping?

👉 Follow-ups:

* How does it achieve fast lookup?
* Is it O(n) or optimized?

---

### ❓ Q4: Design a routing system for this:

```
GET /users/:id/orders/:orderId
POST /users
GET /products/:category
```

👉 Question:
What data structure will you use and why?

👉 Follow-ups:

* Can HashMap work?
* Why is Trie better?
* What is time complexity?

---

# 💻 Round 3: Coding (LLD)

### ❓ Q5: Implement a simple router

👉 Requirements:

* Input: method + path
* Output: handler name

Example:

```
addRoute("GET", "/users/:id", "getUserHandler")

resolve("GET", "/users/123") → "getUserHandler"
```

👉 Follow-ups:

* How will you extract path params?
* How will you handle conflicts?
* Can you extend this to support wildcards (`*`)?

---

# ⚙️ Round 4: Real-World Challenges

### ❓ Q6: You have 1 million routes (dynamic APIs)

👉 Question:
How will you optimize routing?

👉 Follow-ups:

* Will Trie still work?
* Can you cache?
* What about memory tradeoffs?

---

### ❓ Q7: In production, a route suddenly stops working

👉 Question:
How do you debug?

👉 Expected thinking:

* Logs
* Route registration issues
* Middleware interference

👉 Follow-ups:

* How do you trace request path end-to-end?

---

# 🌐 Round 5: System Design Twist

### ❓ Q8: Routing in Microservices

```
Client → API Gateway → Services
```

👉 Question:
Where does routing happen?

👉 Follow-ups:

* Difference between:

    * API Gateway routing
    * Service-level routing
* How does Netflix Zuul / Spring Cloud Gateway work?

---

### ❓ Q9: Design API versioning strategy

```
/v1/users
/v2/users
```

👉 Follow-ups:

* URL versioning vs header versioning
* How routing supports versioning?

---

# 🔥 Round 6: Edge Cases (SDE 2 Differentiator)

### ❓ Q10: What are common routing pitfalls?

👉 Expect:

* Route conflicts
* Deep nesting
* Security issues
* Performance bottlenecks

👉 Follow-ups:

* How do you prevent breaking changes?
* How do you ensure backward compatibility?

---

# 🧠 Bonus Challenge (Bar-Raiser Level)

### ❓ Q11: Can routing become a bottleneck?

👉 Follow-ups:

* Under what conditions?
* How would you horizontally scale routing?

---