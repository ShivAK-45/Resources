HTTP caching is a **core performance pillar** in backend/system design. If you explain it well, you instantly sound senior.

Let’s go **from fundamentals → real-world → SDE-2 insights → interview questions** 👇

---

# 🧠 What is HTTP Caching?

**HTTP caching** stores responses so that **future requests can be served faster** without hitting the backend.

👉 Goal:

* Reduce latency
* Reduce server load
* Improve scalability

---

# 🔥 Why Caching is Needed

### ❌ Without Cache

```text
Client → Server → Database → Response (slow ❌)
```

### ✅ With Cache

```text
Client → Cache → Response (fast ✅)
```

---

# 🏗️ Types of HTTP Caching (Very Important)

---

## 🔹 1. Browser Cache (Client-side)

* Stored in user’s browser
* Fastest

---

## 🔹 2. Proxy Cache (Intermediate)

* CDN / Reverse proxy

Examples:

* Cloudflare
* Akamai Technologies

---

## 🔹 3. Server-side Cache

* Inside backend (Redis, etc.)

---

# 🔄 HTTP Caching Flow

---

# 🔑 Key HTTP Cache Headers

---

## 🔸 1. Cache-Control (MOST IMPORTANT)

Controls caching behavior

### Example:

```http
Cache-Control: max-age=3600
```

👉 Cache for 1 hour

---

### Common Directives

| Directive | Meaning            |
| --------- | ------------------ |
| max-age   | Cache duration     |
| no-cache  | Must revalidate    |
| no-store  | Don’t cache        |
| public    | Cache anywhere     |
| private   | Only browser cache |

---

---

## 🔸 2. Expires (Old method)

```http
Expires: Wed, 21 Oct 2026 07:28:00 GMT
```

👉 Absolute expiry time

---

## 🔸 3. ETag (Entity Tag)

Unique identifier for resource version

```http
ETag: "abc123"
```

---

## 🔸 4. Last-Modified

```http
Last-Modified: Wed, 21 Oct 2026
```

---

# 🔄 Caching Strategies (Very Important)

---

# 🔹 1. Strong Caching (Fresh Cache)

Browser directly uses cached response

### Example:

```http
Cache-Control: max-age=3600
```

👉 No request sent to server

---

---

# 🔹 2. Validation (Revalidation Cache)

Browser checks with server:

```http
If-None-Match: "abc123"
```

---

### Server Response:

```http
304 Not Modified
```

👉 No data sent → saves bandwidth

---

---

# 🔹 3. No Cache

```http
Cache-Control: no-cache
```

👉 Always validate before using

---

---

# 🔹 4. No Store

```http
Cache-Control: no-store
```

👉 Never cache (sensitive data)

---

# 🔥 Complete Example (End-to-End)

---

## First Request

```http
GET /profile
```

### Response:

```http
Cache-Control: max-age=60
ETag: "v1"
```

---

## Second Request (within 60 sec)

👉 Browser uses cache
👉 No network call

---

## After 60 sec

```http
GET /profile
If-None-Match: "v1"
```

---

### Server Response:

```http
304 Not Modified
```

👉 No data sent → efficient

---

# 🧠 Important Status Codes

| Code | Meaning            |
| ---- | ------------------ |
| 200  | Fresh response     |
| 304  | Use cached version |
| 404  | Not found          |

---

# 🔥 Advanced Concepts (SDE-2 Level)

---

## 🔸 1. CDN Caching

```text
User → CDN → Backend
```

👉 CDN serves cached content

---

## 🔸 2. Cache Invalidation (Hard Problem)

> “There are only two hard things:
> cache invalidation and naming things.”

---

### Strategies:

* TTL (time-based)
* Versioning (ETag)
* Manual invalidation

---

---

## 🔸 3. Stale-While-Revalidate

```http
Cache-Control: max-age=60, stale-while-revalidate=30
```

👉 Serve old data while fetching new

---

---

## 🔸 4. Cache Busting

```text
style.css?v=2
```

👉 Forces reload

---

---

## 🔸 5. Private vs Public Cache

| Type    | Example |
| ------- | ------- |
| public  | CDN     |
| private | Browser |

---

# 🔐 What Should NOT Be Cached

❌ User data
❌ Auth responses
❌ Payment APIs

Use:

```http
Cache-Control: no-store
```

---

# 🏗️ Caching in System Design

---

## Example Architecture

```text
User
 ↓
Browser Cache
 ↓
CDN
 ↓
Load Balancer
 ↓
Backend
 ↓
Database
```

---

## Key Insight

👉 Multiple layers of caching improve performance drastically

---

# ⚠️ Common Mistakes

---

## ❌ Over-caching

* Stale data

---

## ❌ Under-caching

* Poor performance

---

## ❌ Wrong headers

* Cache not working

---

# 🧠 Interview-Level Insights

---

## 🔥 Insight 1

> “Caching reduces read load and improves latency significantly”

---

## 🔥 Insight 2

> “Use strong caching for static content, validation for dynamic content”

---

## 🔥 Insight 3

> “Caching should be layered: browser + CDN + backend”

---

---

# 🎯 Interview Questions (Very Important)

---

## ❓ Q1: What is HTTP caching?

👉 Answer:

* Storing responses to reuse later

---

## ❓ Q2: Difference between ETag and Last-Modified?

| ETag    | Last-Modified |
| ------- | ------------- |
| Precise | Time-based    |
| Better  | Simpler       |

---

---

## ❓ Q3: What is 304 Not Modified?

👉 Server tells client:

* Use cached version

---

---

## ❓ Q4: Difference between no-cache and no-store?

| no-cache   | no-store    |
| ---------- | ----------- |
| Revalidate | Don’t store |

---

---

## ❓ Q5: How does CDN caching work?

👉 Stores responses closer to users

---

---

## ❓ Q6: How to handle cache invalidation?

👉 TTL + versioning

---

---

## ❓ Q7: When not to cache?

👉 Sensitive or dynamic data

---

---

# 🚀 Interview Power Answer

> “HTTP caching improves performance by storing responses at multiple layers such as browser, CDN, and server. It uses headers like Cache-Control, ETag, and Last-Modified to control caching behavior. Strategies include strong caching and validation caching, and proper cache invalidation is critical to avoid stale data.”

---

# 🔑 Final Takeaways

* Caching = **performance + scalability**
* Key headers:

    * Cache-Control
    * ETag
* Types:

    * Browser
    * CDN
* Must balance:

    * Freshness vs performance

---
---

# 🔹 1. HTTP Content Negotiation

## 📌 What is Content Negotiation?

Content negotiation is the mechanism by which a client and server decide **what format of data should be returned**.

👉 Same resource, different representations:

* JSON
* XML
* HTML
* Images (WebP, PNG, JPEG)
* Language variants

---

## 🔹 Types of Content Negotiation

---

## 🔸 1. Server-Driven Negotiation (Most Common)

Client sends preferences via headers:

```http
Accept: application/json
Accept-Language: en-US
Accept-Encoding: gzip
```

Server decides best format.

---

### ✅ Example:

Request:

```http
GET /user
Accept: application/json
```

Response:

```http
Content-Type: application/json
```

---

## 🔸 2. Agent-Driven Negotiation

Server returns multiple options → client chooses.

Rarely used in practice.

---

## 🔸 3. Transparent Negotiation

Handled by intermediaries (like proxies/CDNs).

---

# 🔹 Key Headers in Content Negotiation

---

## 🔸 1. `Accept`

Specifies media types:

```http
Accept: application/json, text/html;q=0.9
```

👉 `q` = quality factor (priority)

---

## 🔸 2. `Accept-Language`

```http
Accept-Language: en-US, fr;q=0.8
```

Server may return localized content.

---

## 🔸 3. `Accept-Encoding`

```http
Accept-Encoding: gzip, br
```

Defines supported compression.

---

## 🔸 4. `User-Agent`

Used for device/browser-specific responses.

---

## 🔹 Content-Type vs Accept

| Header       | Direction       | Purpose           |
| ------------ | --------------- | ----------------- |
| Accept       | Client → Server | What client wants |
| Content-Type | Server → Client | What server sends |

---

## 🔹 Real-World Example

### Browser Request:

```http
GET /data
Accept: application/json
Accept-Encoding: gzip
Accept-Language: en-US
```

### Server Response:

```http
Content-Type: application/json
Content-Encoding: gzip
Content-Language: en-US
```

---

## 🔹 406 Not Acceptable

If server cannot satisfy request:

```http
406 Not Acceptable
```

---

# 🔹 Advanced Concepts

---

## 🔸 Quality Values (q-values)

```http
Accept: application/json;q=0.9, application/xml;q=0.8
```

👉 JSON preferred over XML

---

## 🔸 Wildcards

```http
Accept: */*
```

👉 Accept anything

---

## 🔸 Vary Header

```http
Vary: Accept-Encoding
```

👉 Tells caches that response varies based on headers

⚠️ Important for caching correctness

---

# 🔹 Interview Questions (Content Negotiation)

---

### 🔸 Q1: What is content negotiation?

Client and server agree on representation using headers like `Accept`.

---

### 🔸 Q2: Difference between `Accept` and `Content-Type`?

* Accept → request preference
* Content-Type → actual response format

---

### 🔸 Q3: What is `Vary` header?

Indicates which request headers affect caching.

---

### 🔸 Q4: What is 406 error?

Server cannot match requested format.

---

---

# 🔹 2. HTTP File Compression

## 📌 What is HTTP Compression?

Compression reduces response size → faster transfer → better performance.

---

## 🔹 How It Works

1. Client sends:

```http
Accept-Encoding: gzip, br
```

2. Server compresses response

3. Server sends:

```http
Content-Encoding: gzip
```

4. Browser decompresses automatically

---

# 🔹 Types of Compression Algorithms

---

## 🔸 1. Gzip (Most Widely Used)

* Good compression ratio
* Supported everywhere

---

## 🔸 2. Brotli (br)

* Better compression than gzip
* Used heavily by modern browsers and CDNs like Cloudflare

---

## 🔸 3. Deflate

* Older, less common

---

# 🔹 Example

---

### Request:

```http
GET /app.js
Accept-Encoding: gzip, br
```

---

### Response:

```http
200 OK
Content-Encoding: br
Content-Type: application/javascript
```

---

# 🔹 Compression vs Caching

| Feature  | Compression      | Caching           |
| -------- | ---------------- | ----------------- |
| Purpose  | Reduce size      | Avoid requests    |
| Works on | Every response   | Repeated requests |
| Headers  | Content-Encoding | Cache-Control     |

---

# 🔹 What Should Be Compressed?

✅ HTML
✅ CSS
✅ JavaScript
✅ JSON / APIs

---

## ❌ Avoid Compressing:

❌ Images (JPEG, PNG already compressed)
❌ Videos
❌ PDFs (usually compressed)

---

# 🔹 Performance Impact

* Reduce bandwidth by **60–90%**
* Faster page loads
* Lower server cost

---

# 🔹 Compression Levels

Example (gzip):

* Level 1 → fast, less compression
* Level 9 → slow, best compression

👉 Trade-off: CPU vs size

---

# 🔹 Important Headers

---

## 🔸 `Accept-Encoding`

Client-supported compression

---

## 🔸 `Content-Encoding`

Compression used by server

---

## 🔸 `Content-Length`

Size AFTER compression

---

## 🔸 `Transfer-Encoding: chunked`

Used when size unknown

---

# 🔹 Advanced Concepts

---

## 🔸 1. Pre-compressed Files

Serve:

```http
app.js.gz
app.js.br
```

---

## 🔸 2. Dynamic Compression

Server compresses on the fly (CPU intensive)

---

## 🔸 3. Compression + CDN

CDNs like Akamai auto-compress responses.

---

## 🔸 4. Double Compression Issue

⚠️ Avoid compressing already compressed data

---

# 🔹 Interview Questions (Compression)

---

### 🔸 Q1: How does HTTP compression work?

Client sends `Accept-Encoding`, server responds with compressed data.

---

### 🔸 Q2: Difference between gzip and Brotli?

* Brotli → better compression
* gzip → faster, more compatible

---

### 🔸 Q3: Why not compress images?

Already compressed → no benefit, wasted CPU

---

### 🔸 Q4: What is `Content-Encoding`?

Indicates compression used

---

### 🔸 Q5: How does compression improve performance?

Reduces payload size → faster transfer

---

# 🔚 Final Summary

---

## 🔹 Content Negotiation

* Decides **what format** to send
* Uses headers like `Accept`, `Accept-Language`
* Works with caching via `Vary`

---

## 🔹 Compression

* Reduces **size of response**
* Uses `Accept-Encoding` & `Content-Encoding`
* Improves speed and bandwidth usage

---
Let’s combine **HTTP Caching + Compression + Content Negotiation** into a **real-world system design**, exactly the kind of explanation expected in backend/system design interviews.

---

# 🚀 1. Problem Statement

Design a **high-performance API / web system** that:

* Serves millions of users globally
* Minimizes latency
* Reduces bandwidth usage
* Supports multiple formats (JSON/XML) and languages
* Scales efficiently

---

# 🏗️ 2. High-Level Architecture

```
Client (Browser / Mobile)
        ↓
     CDN (Cache + Compression)
        ↓
   Load Balancer
        ↓
   App Servers
        ↓
     Database
```

---

# 🔹 3. Step-by-Step Flow (End-to-End)

---

## 🟢 Step 1: Client Request (Negotiation Starts)

```http
GET /products
Accept: application/json
Accept-Encoding: br, gzip
Accept-Language: en-US
```

👉 Client specifies:

* Format → JSON
* Compression → Brotli preferred
* Language → English

---

## 🟢 Step 2: CDN Layer (Caching + Compression)

Using a CDN like Cloudflare:

### ✅ Check Cache Key:

Cache key depends on:

* URL (`/products`)
* Headers (via `Vary`)

Example:

```
/products + Accept-Encoding + Accept-Language
```

---

### 🔸 Case A: Cache HIT

👉 CDN already has response:

```http
200 OK
Content-Type: application/json
Content-Encoding: br
Cache-Control: public, max-age=3600
```

✅ Response returned immediately (fastest path)

---

### 🔸 Case B: Cache MISS

👉 Request forwarded to backend

---

## 🟢 Step 3: Application Server (Negotiation Logic)

Server processes headers:

### 📌 Content Negotiation:

* `Accept: application/json` → return JSON
* `Accept-Language: en-US` → return English

---

### Example Response:

```http
Content-Type: application/json
Content-Language: en-US
```

---

## 🟢 Step 4: Compression Decision

Server checks:

```http
Accept-Encoding: br, gzip
```

👉 Chooses **Brotli** (better compression)

```http
Content-Encoding: br
```

---

## 🟢 Step 5: Caching Headers

Server sets:

```http
Cache-Control: public, max-age=300, s-maxage=600
ETag: "v1-products"
Vary: Accept-Encoding, Accept-Language
```

---

### 🔍 Meaning:

* `max-age=300` → browser cache (5 min)
* `s-maxage=600` → CDN cache (10 min)
* `ETag` → validation
* `Vary` → different cache per encoding/language

---

## 🟢 Step 6: CDN Stores Response

CDN stores compressed version:

* `/products + br + en-US`
* `/products + gzip + fr`

---

## 🟢 Step 7: Future Requests

---

### ✅ Within Cache Time:

* Served directly from CDN (no backend hit)

---

### 🔁 After Expiry:

Client sends:

```http
If-None-Match: "v1-products"
```

Server responds:

```http
304 Not Modified
```

✅ Saves bandwidth

---

# 🔹 4. How All Three Work Together

---

## 🔸 1. Content Negotiation

Determines:

* Format (`JSON/XML`)
* Language
* Compression type

---

## 🔸 2. Compression

Reduces payload size:

* Brotli / gzip

---

## 🔸 3. Caching

Avoids repeated work:

* Browser cache
* CDN cache
* Validation via ETag

---

# 🔹 5. Cache Key Design (Critical in Interviews)

Bad cache key ❌:

```
/products
```

Good cache key ✅:

```
/products + Accept-Encoding + Accept-Language
```

👉 Ensures correct variants are served

---

# 🔹 6. Real Example (Full Flow)

---

### First Request:

```http
GET /products
Accept: application/json
Accept-Encoding: gzip
Accept-Language: en-US
```

---

### Response:

```http
200 OK
Content-Type: application/json
Content-Encoding: gzip
Cache-Control: public, max-age=300
ETag: "v1"
Vary: Accept-Encoding, Accept-Language
```

---

### Second Request (within 5 min):

✅ Served from browser/CDN cache
🚫 No server call

---

### After Expiry:

```http
If-None-Match: "v1"
```

Response:

```http
304 Not Modified
```

---

# 🔹 7. Optimization Strategies

---

## ✅ Static Assets (JS/CSS)

```http
Cache-Control: public, max-age=31536000, immutable
```

* Use file hashing:

```
app.abc123.js
```

---

## ✅ Dynamic APIs

```http
Cache-Control: public, max-age=60
ETag: "dynamic-v1"
```

---

## ✅ Compression Strategy

* Use Brotli for modern browsers
* Fallback to gzip

---

## ✅ CDN Strategy

* Use `s-maxage`
* Enable edge caching

---

# 🔹 8. Common Pitfalls

---

❌ Missing `Vary` header → wrong cached content
❌ Caching personalized data
❌ Compressing already compressed files
❌ Not using ETag → wasted bandwidth
❌ Overusing `no-store`

---

# 🔹 9. Interview-Level Questions

---

### 🔸 Q1: How do caching and content negotiation interact?

👉 Via `Vary` header to store multiple representations.

---

### 🔸 Q2: Why is `Vary` important?

👉 Prevents serving wrong format/language from cache.

---

### 🔸 Q3: Where should compression happen?

👉 Ideally at CDN (edge) for efficiency.

---

### 🔸 Q4: How do you design cache keys?

👉 Include all varying headers:

* Encoding
* Language
* Format

---

### 🔸 Q5: How to handle stale data?

👉 Use:

```http
stale-while-revalidate
```

---

# 🔹 10. Final Mental Model

Think of it like this:

* **Negotiation → WHAT to send**
* **Compression → HOW SMALL to send**
* **Caching → WHETHER to send at all**

---

# 🔚 Final Summary

A production-grade system:

* Uses CDN (like Cloudflare) for caching + compression
* Uses headers (`Accept`, `Cache-Control`, `ETag`)
* Uses `Vary` for correctness
* Minimizes latency and bandwidth

---
Below is a **practical implementation** of a system that combines
✅ Content Negotiation
✅ HTTP Caching
✅ Compression

in both **Spring Boot (Java)** and **Node.js (Express)**—written in a way you can explain in interviews and also run in real projects.

---

# 🚀 1. Spring Boot Implementation

---

## 🔹 1.1 Add Dependencies (Maven)

```xml
<dependencies>
    <!-- Web -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>

    <!-- Caching -->
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-cache</artifactId>
    </dependency>
</dependencies>
```

---

## 🔹 1.2 Enable Caching

```java
@SpringBootApplication
@EnableCaching
public class App {
    public static void main(String[] args) {
        SpringApplication.run(App.class, args);
    }
}
```

---

## 🔹 1.3 Controller (Negotiation + Caching)

```java
@RestController
@RequestMapping("/products")
public class ProductController {

    @GetMapping(produces = { MediaType.APPLICATION_JSON_VALUE, MediaType.APPLICATION_XML_VALUE })
    public ResponseEntity<List<Product>> getProducts(
            @RequestHeader(value = "Accept-Language", defaultValue = "en") String lang,
            @RequestHeader(value = "If-None-Match", required = false) String ifNoneMatch) {

        String etag = "\"v1-products-" + lang + "\"";

        // ETag validation
        if (etag.equals(ifNoneMatch)) {
            return ResponseEntity.status(HttpStatus.NOT_MODIFIED).build();
        }

        List<Product> products = fetchProducts(lang);

        return ResponseEntity.ok()
                .eTag(etag)
                .cacheControl(CacheControl.maxAge(60, TimeUnit.SECONDS).cachePublic())
                .header(HttpHeaders.VARY, "Accept-Encoding, Accept-Language")
                .body(products);
    }

    private List<Product> fetchProducts(String lang) {
        return List.of(new Product(1, lang.equals("fr") ? "Produit" : "Product"));
    }
}
```

---

## 🔹 1.4 Enable Compression (application.yml)

```yaml
server:
  compression:
    enabled: true
    mime-types: application/json,application/xml,text/html,text/plain
    min-response-size: 1024
```

👉 Spring Boot automatically:

* Reads `Accept-Encoding`
* Applies gzip compression

---

## 🔹 1.5 Content Negotiation Config (Optional Advanced)

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void configureContentNegotiation(ContentNegotiationConfigurer configurer) {
        configurer
            .favorParameter(false)
            .ignoreAcceptHeader(false)
            .defaultContentType(MediaType.APPLICATION_JSON);
    }
}
```

---

# 🚀 2. Node.js (Express) Implementation

---

## 🔹 2.1 Install Dependencies

```bash
npm init -y
npm install express compression etag
```

---

## 🔹 2.2 Basic Server

```js
const express = require("express");
const compression = require("compression");
const app = express();

// Enable compression (gzip)
app.use(compression());
```

---

## 🔹 2.3 Middleware: Content Negotiation + Caching

```js
app.get("/products", (req, res) => {
    const accept = req.headers["accept"] || "application/json";
    const language = req.headers["accept-language"] || "en";
    const encoding = req.headers["accept-encoding"] || "";

    const etag = `v1-products-${language}`;

    // ETag validation
    if (req.headers["if-none-match"] === etag) {
        return res.status(304).end();
    }

    let data = [
        { id: 1, name: language.includes("fr") ? "Produit" : "Product" }
    ];

    // Content negotiation
    if (accept.includes("application/xml")) {
        res.set("Content-Type", "application/xml");
        data = `<products><product><id>1</id><name>${data[0].name}</name></product></products>`;
    } else {
        res.set("Content-Type", "application/json");
    }

    // Headers
    res.set({
        "Cache-Control": "public, max-age=60",
        "ETag": etag,
        "Vary": "Accept-Encoding, Accept-Language"
    });

    res.send(data);
});
```

---

## 🔹 2.4 Start Server

```js
app.listen(3000, () => {
    console.log("Server running on port 3000");
});
```

---

# 🔹 3. Example Request Flow

---

## Request:

```http
GET /products
Accept: application/json
Accept-Encoding: gzip
Accept-Language: en
```

---

## Response:

```http
200 OK
Content-Type: application/json
Content-Encoding: gzip
Cache-Control: public, max-age=60
ETag: v1-products-en
Vary: Accept-Encoding, Accept-Language
```

---

## Revalidation Request:

```http
If-None-Match: v1-products-en
```

Response:

```http
304 Not Modified
```

---

# 🔹 4. What This Implementation Covers

---

## ✅ Content Negotiation

* JSON vs XML
* Language-based response

---

## ✅ Compression

* Spring Boot → automatic gzip
* Express → `compression` middleware

---

## ✅ Caching

* `Cache-Control`
* `ETag`
* `304 Not Modified`

---

## ✅ Correct Cache Behavior

* `Vary: Accept-Encoding, Accept-Language`

---

# 🔹 5. Interview Talking Points

---

When explaining this in an interview, say:

👉 “I implement content negotiation using `Accept` headers,
apply compression using gzip/Brotli middleware,
and enable caching with `Cache-Control` and `ETag`.

To ensure correctness, I use the `Vary` header so CDNs cache different variants properly.”

---

# 🔚 Final Takeaway

This setup ensures:

* ⚡ Fast responses (CDN + caching)
* 📦 Smaller payloads (compression)
* 🌍 Flexible formats (negotiation)

---
Let’s take your system to a **production-grade, Netflix-style architecture** by integrating:

✅ **CDN (edge caching + compression)**
✅ **Redis (distributed caching layer)**
✅ **Application-level HTTP caching + negotiation**

This is exactly the level expected in **senior backend / system design interviews**.

---

# 🚀 1. Final Architecture (Netflix-Style)

```id="rq7uv3"
Client (Browser / Mobile)
        ↓
 CDN (Cloudflare / Akamai)
        ↓
   API Gateway / Load Balancer
        ↓
   App Servers (Stateless)
        ↓
     Redis Cache
        ↓
     Database
```

---

# 🔹 2. Role of Each Layer

---

## 🌐 CDN Layer (Edge Optimization)

Using CDNs like:

* Cloudflare
* Akamai

### Responsibilities:

* Cache static + API responses
* Serve from nearest location
* Handle **compression (Brotli/gzip)**
* Respect `Cache-Control`, `Vary`

---

## ⚖️ API Gateway / Load Balancer

* Authentication
* Rate limiting
* Routing to microservices
* Can normalize headers (important for cache keys)

---

## 🧠 App Servers (Stateless)

* Business logic
* Content negotiation
* ETag generation
* Cache orchestration

---

## ⚡ Redis (Distributed Cache)

Using Redis:

### Responsibilities:

* Cache database results
* Reduce DB load
* Store precomputed responses

---

## 🗄️ Database

* Source of truth
* Only hit when cache miss occurs

---

# 🔹 3. End-to-End Request Flow

---

## 🟢 Step 1: Client Request

```http
GET /products
Accept: application/json
Accept-Encoding: br
Accept-Language: en-US
```

---

## 🟢 Step 2: CDN (Edge Cache)

### Cache Key:

```id="9bmmdn"
/products + Accept-Encoding + Accept-Language
```

---

### ✅ Case A: CDN HIT

* Instant response (no backend call)

---

### ❌ Case B: CDN MISS

* Forward to backend

---

## 🟢 Step 3: App Server

### 1. Generate Cache Key (Redis)

```id="5up9q3"
products:en-US:json
```

---

### 2. Check Redis

---

### ✅ Redis HIT

* Fetch cached data
* Generate HTTP response
* Add headers:

  ```http
  Cache-Control: public, max-age=60, s-maxage=300
  ETag: "v1-products-en"
  Vary: Accept-Encoding, Accept-Language
  ```

---

### ❌ Redis MISS

---

## 🟢 Step 4: Database Query

* Fetch data
* Transform based on language

---

## 🟢 Step 5: Store in Redis

```id="30kdc1"
SET products:en-US:json (data) EX 60
```

---

## 🟢 Step 6: Response to Client (via CDN)

CDN:

* Stores response
* Applies Brotli compression
* Serves future requests

---

# 🔹 4. Node.js + Redis Implementation

---

## 📦 Install

```bash
npm install express compression redis
```

---

## 🧠 Redis + API Integration

```js
const express = require("express");
const compression = require("compression");
const redis = require("redis");

const app = express();
app.use(compression());

const client = redis.createClient();
client.connect();

app.get("/products", async (req, res) => {
    const lang = req.headers["accept-language"] || "en";
    const accept = req.headers["accept"] || "application/json";

    const cacheKey = `products:${lang}:${accept}`;

    // 1. Check Redis
    const cached = await client.get(cacheKey);
    if (cached) {
        res.set({
            "X-Cache": "HIT",
            "Cache-Control": "public, max-age=60, s-maxage=300",
            "Vary": "Accept-Encoding, Accept-Language"
        });
        return res.send(JSON.parse(cached));
    }

    // 2. Simulate DB call
    const data = [
        { id: 1, name: lang.includes("fr") ? "Produit" : "Product" }
    ];

    // 3. Store in Redis
    await client.setEx(cacheKey, 60, JSON.stringify(data));

    // 4. Send response
    res.set({
        "X-Cache": "MISS",
        "Cache-Control": "public, max-age=60, s-maxage=300",
        "ETag": `v1-products-${lang}`,
        "Vary": "Accept-Encoding, Accept-Language"
    });

    res.json(data);
});

app.listen(3000);
```

---

# 🔹 5. Spring Boot + Redis Implementation

---

## 📦 Dependencies

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```

---

## ⚙️ Config

```yaml
spring:
  redis:
    host: localhost
    port: 6379
```

---

## 🧠 Service Layer with Cache

```java
@Service
public class ProductService {

    @Autowired
    private RedisTemplate<String, Object> redisTemplate;

    public List<Product> getProducts(String lang) {
        String key = "products:" + lang;

        List<Product> cached = (List<Product>) redisTemplate.opsForValue().get(key);

        if (cached != null) {
            return cached;
        }

        List<Product> data = fetchFromDB(lang);

        redisTemplate.opsForValue().set(key, data, 60, TimeUnit.SECONDS);

        return data;
    }

    private List<Product> fetchFromDB(String lang) {
        return List.of(new Product(1, lang.equals("fr") ? "Produit" : "Product"));
    }
}
```

---

## 🧾 Controller

```java
@GetMapping("/products")
public ResponseEntity<List<Product>> getProducts(
        @RequestHeader(value = "Accept-Language", defaultValue = "en") String lang) {

    List<Product> products = productService.getProducts(lang);

    return ResponseEntity.ok()
            .cacheControl(CacheControl.maxAge(60, TimeUnit.SECONDS).cachePublic())
            .header(HttpHeaders.VARY, "Accept-Encoding, Accept-Language")
            .eTag("v1-products-" + lang)
            .body(products);
}
```

---

# 🔹 6. Netflix-Style Enhancements

---

## 🎯 1. Multi-Level Caching

| Layer      | Purpose                |
| ---------- | ---------------------- |
| CDN        | Edge caching           |
| Redis      | Shared cache           |
| App memory | ultra-fast local cache |

---

## 🎯 2. Stale-While-Revalidate

```http
Cache-Control: max-age=60, stale-while-revalidate=30
```

👉 Serve stale data while refreshing in background

---

## 🎯 3. Cache Invalidation Strategy

* Event-driven invalidation
* Versioned keys:

```id="cphaj3"
products:v2:en
```

---

## 🎯 4. Horizontal Scaling

* Stateless services
* Redis shared across instances

---

## 🎯 5. Observability

Track:

* Cache hit ratio
* Latency
* Redis performance

---

# 🔹 7. Interview-Level Explanation (Golden Answer)

If asked:

👉 “How would you design a scalable API?”

You say:

> “I use a multi-layer caching strategy:
>
> * CDN for edge caching and compression
> * Redis for shared caching
> * HTTP caching with ETag and Cache-Control
>
> I use content negotiation via Accept headers and ensure correctness using the Vary header.
>
> This reduces latency, database load, and bandwidth while keeping responses flexible.”

---

# 🔚 Final Mental Model

```
Negotiation → decides format
Compression → reduces size
CDN → avoids backend calls
Redis → avoids DB calls
ETag → avoids data transfer
```

---
Here’s a **production-grade, Netflix-style architecture** that combines
**CDN + multi-level caching + compression + content negotiation + microservices**—the level expected in senior system design interviews.

---

# 🚀 1. Netflix-Style High-Level Architecture

```id="pglpjp"
        ┌───────────────┐
        │   Clients     │
        │ (Web/Mobile)  │
        └──────┬────────┘
               ↓
        ┌───────────────┐
        │ CDN (Edge)    │
        │ Cache + Brotli│
        └──────┬────────┘
               ↓
     ┌─────────────────────┐
     │ API Gateway         │
     │ Auth + Routing      │
     └──────┬──────────────┘
            ↓
   ┌───────────────────────────┐
   │ Microservices Layer       │
   │ (Stateless Services)      │
   └──────┬──────────┬─────────┘
          ↓          ↓
   ┌──────────┐  ┌──────────┐
   │ Redis    │  │ Kafka    │
   │ Cache    │  │ Events   │
   └────┬─────┘  └────┬─────┘
        ↓              ↓
   ┌──────────┐   ┌──────────┐
   │ Database │   │ Workers  │
   └──────────┘   └──────────┘
```

---

# 🔹 2. Real Technologies (Netflix-Inspired Stack)

Netflix popularized many patterns using tools like:

* Netflix architecture style
* Zuul / API Gateway
* Eureka
* Hystrix
* Apache Kafka
* Redis

(Modern replacements: Spring Cloud Gateway, Resilience4j, etc.)

---

# 🔹 3. Layer-by-Layer Deep Dive

---

## 🌐 3.1 CDN Layer (Edge Optimization)

Using:

* Cloudflare
* Akamai

### Responsibilities:

* Edge caching (HTML, APIs, images)
* Brotli/gzip compression
* TLS termination
* Geo-based routing

---

### 🔑 Headers Used:

```http
Cache-Control: public, max-age=60, s-maxage=300
Vary: Accept-Encoding, Accept-Language
```

---

## ⚖️ 3.2 API Gateway Layer

Acts as the **single entry point**

### Responsibilities:

* Authentication (JWT/OAuth)
* Rate limiting
* Request routing
* Header normalization (important for caching!)

---

### Example Flow:

```id="0tqbtg"
Client → /api/products → Gateway → Product Service
```

---

## 🧩 3.3 Microservices Layer

Each service:

* Stateless
* Independently deployable
* Owns its data

---

### Example Services:

* Product Service
* User Service
* Recommendation Service
* Playback Service (in Netflix-like system)

---

### Key Features:

* Content negotiation (`Accept`, `Accept-Language`)
* ETag generation
* Cache orchestration

---

## ⚡ 3.4 Redis (Distributed Cache)

Using Redis

---

### Multi-Level Caching Strategy:

| Level | Cache Type               |
| ----- | ------------------------ |
| L1    | In-memory (per instance) |
| L2    | Redis (shared cache)     |
| L3    | CDN (edge cache)         |

---

### Cache Key Example:

```id="3jkmvw"
products:en-US:json:v1
```

---

## 🗄️ 3.5 Database Layer

* Sharded / replicated DB
* Only accessed on cache miss

---

## 🔄 3.6 Event-Driven Layer (Kafka)

Using Apache Kafka

---

### Purpose:

* Cache invalidation
* Async processing
* Data synchronization

---

### Example Event:

```id="0s8tyo"
PRODUCT_UPDATED
```

Consumers:

* Clear Redis cache
* Purge CDN

---

# 🔹 4. End-to-End Request Flow (Critical for Interviews)

---

## 🟢 Step 1: Client Request

```http id="54swy5"
GET /products
Accept: application/json
Accept-Encoding: br
Accept-Language: en-US
```

---

## 🟢 Step 2: CDN

### ✅ Cache HIT:

* Return instantly (~10ms)

### ❌ Cache MISS:

* Forward to API Gateway

---

## 🟢 Step 3: API Gateway

* Auth check
* Route to Product Service

---

## 🟢 Step 4: Product Service

### 1. Check Local Cache (L1)

---

### 2. Check Redis (L2)

---

### ✅ Redis HIT:

* Return data
* Add headers

---

### ❌ Redis MISS:

* Query DB

---

## 🟢 Step 5: Database

* Fetch data
* Return to service

---

## 🟢 Step 6: Cache Population

* Store in Redis
* Store in local cache

---

## 🟢 Step 7: Response Headers

```http id="thm0pb"
Cache-Control: public, max-age=60, s-maxage=300
ETag: "v1-products-en"
Vary: Accept-Encoding, Accept-Language
Content-Encoding: br
```

---

## 🟢 Step 8: CDN Stores Response

Future requests → no backend hit

---

# 🔹 5. Advanced Netflix-Style Optimizations

---

## 🔸 1. Stale-While-Revalidate

```http id="ojbe9d"
Cache-Control: max-age=60, stale-while-revalidate=30
```

👉 Serve stale content while refreshing

---

## 🔸 2. Circuit Breakers

Using Hystrix:

* Prevent cascading failures
* Fallback responses

---

## 🔸 3. Service Discovery

Using Eureka:

* Dynamic scaling
* Auto-registration

---

## 🔸 4. Auto Scaling

* Scale microservices horizontally
* Stateless design enables this

---

## 🔸 5. Canary Deployments

* Gradual rollout
* Reduce risk

---

# 🔹 6. Failure Handling (Very Important)

---

## ❌ Redis Down

👉 Fallback:

* Hit DB
* Increase latency but system works

---

## ❌ DB Slow

👉 Use:

* Cached data
* Stale responses

---

## ❌ Service Down

👉 Circuit breaker returns:

```json
{
  "message": "Service temporarily unavailable"
}
```

---

# 🔹 7. Key Design Principles

---

## ✅ 1. Reduce Latency

* CDN + caching

## ✅ 2. Reduce Load

* Redis + cache

## ✅ 3. Reduce Bandwidth

* Compression

## ✅ 4. Ensure Correctness

* `Vary` header

## ✅ 5. Scale Horizontally

* Stateless services

---

# 🔹 8. Golden Interview Answer (Concise)

If asked:

👉 *“Design a scalable system like Netflix”*

You say:

> “I use a multi-layer architecture:
>
> * CDN for edge caching and compression
> * API Gateway for routing and auth
> * Stateless microservices
> * Redis for distributed caching
> * Kafka for event-driven cache invalidation
>
> I combine HTTP caching (`Cache-Control`, `ETag`), compression (Brotli), and content negotiation (`Accept` headers) to optimize performance and scalability.”

---

# 🔚 Final Mental Model

```id="hhsboe"
Client → CDN → Gateway → Service → Redis → DB
           ↑         ↓
      Cache HIT   Cache MISS
```

---
Let’s extend the architecture with **Kafka + event-driven cache invalidation**, which is a key pattern in **Netflix-style systems** for keeping caches fresh without sacrificing performance.

---

# 🚀 1. Why Event-Driven Cache Invalidation?

Caching (CDN + Redis) improves performance, but introduces a problem:

👉 **Stale data**

Instead of waiting for TTL expiry, we:

* **Push updates via events**
* Immediately invalidate/update caches

---

# 🔹 2. Updated Architecture (with Kafka)

```id="b9qeqt"
Client
  ↓
CDN (Edge Cache)
  ↓
API Gateway
  ↓
Microservices
  ↓
Redis Cache
  ↓
Database
  ↓
Kafka (Event Bus)
  ↓
Consumers (Cache Invalidation Workers)
```

---

# 🔹 3. Core Idea

When data changes:

```id="87vxjw"
DB Update → Publish Event → Kafka → Consumers → Invalidate Cache
```

---

# 🔹 4. Example Scenario

---

## 🎯 Use Case: Product Update

---

### 🟢 Step 1: Update API Called

```http id="yuzs1z"
PUT /products/1
```

---

### 🟢 Step 2: Service Updates DB

```id="1smu9o"
UPDATE products SET name = "New Product" WHERE id = 1;
```

---

### 🟢 Step 3: Publish Event to Kafka

Using Apache Kafka:

```json id="i2lgti"
{
  "eventType": "PRODUCT_UPDATED",
  "productId": 1,
  "version": "v2"
}
```

---

### 🟢 Step 4: Kafka Consumers Trigger

Consumers listen to topic:

```id="qddnto"
product-events
```

---

### 🟢 Step 5: Cache Invalidation

Consumers:

* Delete Redis keys
* Purge CDN cache

```id="5g1qik"
DEL products:en-US:json
DEL products:fr:json
```

---

### 🟢 Step 6: Next Request

* Cache MISS
* Fetch fresh data
* Repopulate cache

---

# 🔹 5. Types of Cache Invalidation

---

## 🔸 1. Hard Invalidation

```id="giboyq"
DEL key
```

👉 Remove cache completely

---

## 🔸 2. Soft Invalidation (Versioning)

```id="9r11r5"
products:v2:en-US
```

👉 Old cache ignored automatically

---

## 🔸 3. Partial Invalidation

Only invalidate affected keys:

```id="k3ydzt"
products:1
```

---

# 🔹 6. Node.js + Kafka + Redis Implementation

---

## 📦 Install

```bash id="xmpxr1"
npm install kafkajs redis express
```

---

## 🔹 Producer (On Update)

```js id="9xg3ee"
const { Kafka } = require("kafkajs");

const kafka = new Kafka({ clientId: "app", brokers: ["localhost:9092"] });
const producer = kafka.producer();

async function publishProductUpdate(productId) {
    await producer.connect();

    await producer.send({
        topic: "product-events",
        messages: [
            {
                value: JSON.stringify({
                    eventType: "PRODUCT_UPDATED",
                    productId
                })
            }
        ]
    });

    await producer.disconnect();
}
```

---

## 🔹 Consumer (Cache Invalidation)

```js id="0qth5z"
const redis = require("redis");
const { Kafka } = require("kafkajs");

const client = redis.createClient();
client.connect();

const kafka = new Kafka({ clientId: "cache-service", brokers: ["localhost:9092"] });
const consumer = kafka.consumer({ groupId: "cache-group" });

async function run() {
    await consumer.connect();
    await consumer.subscribe({ topic: "product-events" });

    await consumer.run({
        eachMessage: async ({ message }) => {
            const event = JSON.parse(message.value.toString());

            if (event.eventType === "PRODUCT_UPDATED") {
                // Invalidate Redis
                await client.del(`products:en`);
                await client.del(`products:fr`);

                console.log("Cache invalidated for product:", event.productId);
            }
        }
    });
}

run();
```

---

# 🔹 7. Spring Boot + Kafka Implementation

---

## 📦 Dependencies

```xml id="2av4qd"
<dependency>
  <groupId>org.springframework.kafka</groupId>
  <artifactId>spring-kafka</artifactId>
</dependency>
```

---

## 🔹 Producer

```java id="nq2f7b"
@Autowired
private KafkaTemplate<String, String> kafkaTemplate;

public void publishEvent(String productId) {
    String event = "{\"eventType\":\"PRODUCT_UPDATED\",\"productId\":\"" + productId + "\"}";
    kafkaTemplate.send("product-events", event);
}
```

---

## 🔹 Consumer

```java id="l69rdo"
@KafkaListener(topics = "product-events", groupId = "cache-group")
public void consume(String message) throws Exception {
    ObjectMapper mapper = new ObjectMapper();
    JsonNode event = mapper.readTree(message);

    if (event.get("eventType").asText().equals("PRODUCT_UPDATED")) {
        redisTemplate.delete("products:en");
        redisTemplate.delete("products:fr");
    }
}
```

---

# 🔹 8. CDN Cache Invalidation

CDNs like:

* Cloudflare

Support:

* **Purge by URL**
* **Purge by tag**

---

### Example:

```id="rf5ry1"
POST /purge_cache
{
  "files": ["/products"]
}
```

---

# 🔹 9. Advanced Patterns (Netflix-Level)

---

## 🔸 1. Event Sourcing

Store all events:

```id="5c1ec6"
PRODUCT_CREATED
PRODUCT_UPDATED
PRODUCT_DELETED
```

---

## 🔸 2. CQRS (Command Query Responsibility Segregation)

* Writes → DB
* Reads → Redis / cache

---

## 🔸 3. Idempotent Consumers

Avoid duplicate processing:

```id="lq16af"
if (alreadyProcessed(eventId)) return;
```

---

## 🔸 4. Retry + Dead Letter Queue

If processing fails:

* Retry
* Send to DLQ

---

# 🔹 10. Failure Handling

---

## ❌ Kafka Down

👉 Fallback:

* Use TTL-based cache expiry

---

## ❌ Consumer Failure

👉 Use:

* Retry mechanism
* DLQ

---

## ❌ Partial Invalidation

👉 Use versioning:

```id="6nvm27"
products:v3
```

---

# 🔹 11. Interview-Level Explanation (Perfect Answer)

> “I use Kafka for event-driven cache invalidation.
> When data changes, services publish events to Kafka.
> Consumers listen and invalidate Redis and CDN caches.
>
> This ensures strong consistency while maintaining high performance with caching.”

---

# 🔚 Final Mental Model

```id="mx6ez9"
Write → DB → Kafka Event → Consumers → Cache Cleared
Read → CDN → Redis → DB
```

---



