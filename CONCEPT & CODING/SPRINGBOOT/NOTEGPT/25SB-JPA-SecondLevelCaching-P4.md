### Summary: Understanding Second Level Caching in Spring Boot with JPA and Hibernate

This content offers an in-depth explanation of **second level caching (L2 caching)** as applied in modern **Spring Boot applications using JPA and Hibernate**. It builds on foundational caching concepts, especially contrasting it with **first level caching (L1 caching)**, and dives into practical implementation aspects including dependencies, configuration, working principles, cache concurrency strategies, and troubleshooting insights.

---

### Core Concepts

#### First Level Caching (L1 Cache) - Recap

- **Scope:** Limited to a single **EntityManager object** and its associated **persistence context**.
- **Behavior:**
    - Every HTTP request creates a **new EntityManager instance**, hence a new persistence context.
    - Because each persistence context is isolated per request, **L1 cache is not shared across requests**.
    - The result: even if multiple identical get calls occur sequentially, each will hit the database (**no cache hit across requests**).
- **Within the same EntityManager:**
    - The first query loads the entity from the DB, subsequent queries for the same entity are served from the **L1 cache (cache hit)**.
- L1 caching ensures a cache hit only within the **same transaction or request context**.

---

#### Second Level Caching (L2 Cache)

- **Scope:** Shared across multiple **EntityManager instances** and their persistence contexts.
- **Purpose:** Improves cache hit ratio across different requests, solving the limitation of L1 caching which is confined to a single persistence context.
- **Placement:** Positioned between the **persistence context (L1 cache)** and the **database**.
- **Operation flow:**
    1. EntityManager’s persistence context checks L1 cache.
    2. If miss, checks L2 cache (shared cache).
    3. If miss again, hits the database.
    4. Once loaded from the DB, results are populated back to L2 cache and L1 cache for future requests.
- L2 cache is **in-memory and shared** among multiple users and requests, reducing repeated DB hits across sessions.

---

### Implementation: Enabling Second Level Cache in Spring Boot

#### Dependencies Required (Pom.xml)

| Dependency                 | Role Description                                                                                                     |
|----------------------------|---------------------------------------------------------------------------------------------------------------------|
| **Ehcache**                | Core implementation provider for caching. Alternative providers like Caffeine or Hazelcast are also available.       |
| **JCache API (javax.cache API)** | API interface that abstracts different caching providers, allows switching providers without code changes.          |
| **Hibernate JCache**       | Bridge between Hibernate and JCache API, enables Hibernate to communicate and orchestrate cache operations.          |

- These dependencies enable integration of caching mechanisms with Hibernate ORM in a modular and provider-agnostic way.

#### Application Configuration (application.properties)

- To enable L2 caching, the following types of configurations are mandatory:

| Configuration Property                               | Purpose                                                                                      |
|-----------------------------------------------------|----------------------------------------------------------------------------------------------|
| `spring.jpa.properties.hibernate.cache.use_second_level_cache` | Enables L2 cache globally. Must be set to `true`.                                           |
| `spring.jpa.properties.hibernate.cache.region.factory_class`  | Defines the **cache region factory** class, which manages cache regions logically and delegates caching operations. Often set to Hibernate's JCache region factory. |
| `spring.jpa.properties.hibernate.cache.provider_class`        | Specifies the actual **cache provider** like Ehcache, allowing Hibernate to use the chosen caching backend.                    |

- Additional debug properties may be enabled to print SQL statements for observing cache hits and misses but are *not mandatory* for caching operations.

---

### Cache Regions and Region Factory Concept

- **Region:** Logical grouping unit for cache entries, often aligned with an entity or related set of entities.
    - Allows different cache policies (eviction, time-to-live, concurrency) per region.
    - Example regions might be `user_detail_cache`, `order_detail_cache`.
    - Each region can have distinct TTL, cache size limits, and eviction strategies (e.g., LRU, FIFO).

- **Region Factory Class:**
    - Acts as a **manager**/handler for all cache regions.
    - Often the Hibernate JCache Region Factory is preferred due to its abstraction, facilitating easy switching across caching providers in the future without code changes.
    - Direct use of cache provider factories like Ehcache Factory is also possible but reduces portability.

---

### Enabling Cache at Entity Level

- Example:

$$\text{@Cache(usage = CacheConcurrencyStrategy.READ\_ONLY, region = "user\_detail\_cache")}$$

- This Hibernate annotation enables L2 cache on a specific entity and assigns it to a logical cache region.
- Supports multiple concurrency strategies (explained below).
- Simplifies cache activation on entities without impacting how repositories or services interact with the data.

---

### Cache Concurrency Strategies

Cache concurrency strategies govern the **consistency and synchronization** between cache content and the underlying database during concurrent operations (reads, writes, updates). Choosing the right strategy depends heavily on the business use case, particularly whether stale data is tolerable.

| Concurrency Strategy        | Description                                                                                         | Use Case / Characteristics                                                       |
|-----------------------------|-------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| **READ\_ONLY**              | Suitable for **immutable/static data**. Any update attempts throw exceptions.                     | Static reference data rarely needing updates (e.g. country codes).              |
| **READ\_WRITE**             | Supports concurrent reads and writes with **cache locking** to prevent stale data.               | Critical consistency needed, e.g. financial/payment data.                       |
| **NONSTRICT\_READ\_WRITE** | More lenient; updates mark cache as invalid but do **not update cache directly**. Reads can get stale data in small windows. No locks on read. | Suitable for high read, low update environments where small stale data window is acceptable (e.g., product catalogues). |
| **TRANSACTIONAL**           | Strictest, requiring cache locking for both read and write. Reads bypass cache if locked; get data directly from DB.| Strict transactions where strict cache consistency is mandatory. *Not fully supported by some providers.* |

---

### Detailed Explanation of Concurrency Strategies

#### READ\_ONLY

- Entities marked as read-only cannot be updated.
- Any attempts to update will cause an internal Hibernate error.
- Suitable only for data that never changes.

#### READ\_WRITE

- Guarantees strong cache consistency.
- Operations:
    - Before update, cache entry is **locked and marked as invalid**.
    - Database is updated.
    - Cache updated with new data after DB commit, **invalid flag removed**.
    - Lock released.
- Prevents stale reads by locking cache entries during updates.
- Typically used in sensitive industries (e.g., payment processing).
- Failure scenarios (like transaction rollback) leave cache invalidated, forcing future reads from DB to refresh cache.

#### NONSTRICT\_READ\_WRITE

- No lock acquired during reads; updates mark cache as invalid after DB commit.
- During update:
    - Cache invalidated but not blocked or locked.
    - Reads can see stale data briefly; acceptable if stale reads are tolerable.
- Good for heavy read applications with occasional writes.
- Rolls back releases locks without invalidating cache if transaction fails.

#### TRANSACTIONAL

- Acquires locks both on read and write.
- Reads block if cache is locked, bypassing cache and fetching directly from DB.
- Writes update the cache only after successful DB commit.
- Models strict transactional isolation.
- Less commonly used due to complexity and limited provider support.

---

### Practical Example Flow: Using READ\_WRITE Strategy

1. **Insert operation (POST):** Directly inserts data into DB; cache not involved.
2. **Get operation (GET):**
    - First GET causes a cache miss.
    - Hits DB, loads data into L2 cache and L1 cache.
3. **Put operation (UPDATE):**
    - Cache entry locked and marked invalid.
    - Database updated.
    - Cache updated with new data.
    - Lock released.
4. **Subsequent get calls** return data from cache (cache hit), no DB query needed.

---

### Underlying Cache Management

- **Cache Locking:** Utilized in READ\_WRITE and TRANSACTIONAL strategies to avoid stale reads and maintain data integrity.
- **Invalidation Flags:** Used during updates to mark cache entries stale before DB update, preventing inconsistency.
- **Best Effort Cache Updates:** Cache updates should not fail the main DB transaction since transactions are costly, and cache is an optimization layer.
- **Rollback Scenario:** Cache lock is released but invalidation flag retained, forcing future cache misses and DB reads for refresh.

---

### Summary Table: Cache Operation Behavior by Strategy

| Operation             | READ\_ONLY                 | READ\_WRITE                            | NONSTRICT\_READ\_WRITE               | TRANSACTIONAL                   |
|-----------------------|----------------------------|--------------------------------------|------------------------------------|-------------------------------|
| Read operation        | Cache hits only, no updates allowed | Cache locked for reading, shared locks for concurrent reads | No locks, possibly stale reads    | Cache locked, reads bypass if locked |
| Write operation       | Not allowed (exceptions)    | Locks cache, invalidates, updates DB, updates cache post-commit | Updates DB, invalidates cache post-commit | Locks cache, updates DB, updates cache post-commit |
| Stale data possibility| None                       | None                                 | Possible brief window              | None                          |
| Cache update on write | No                        | Yes, after commit                    | No, cache invalidated             | Yes, after commit             |

---

### Key Insights

- **Second level caching adds a critical layer optimizing database querying across multiple transactions or requests**, preventing redundant DB hits.
- **Choosing the appropriate cache concurrency strategy is crucial based on business data consistency needs.**
- **Implementation involves enabling L2 caching, adding proper dependencies, configuring cache regions, and annotating entities correctly.**
- **Abstracting with JCache API provides flexibility to switch caching providers without changing code.**
- Cache locking and invalidation mechanisms ensure consistent data while enabling concurrent access.
- Cache updates are designed as best-effort to avoid compromising expensive database transactions.
- Debugging SQL output during development helps understand cache hits and cache misses.

---

### Keywords

- Second Level Caching (L2 Cache)
- First Level Caching (L1 Cache)
- EntityManager
- Persistence Context
- Hibernate
- JPA
- Ehcache
- JCache API
- Cache Region
- Cache Region Factory
- Cache Concurrency Strategy
- READ\_ONLY
- READ\_WRITE
- NONSTRICT\_READ\_WRITE
- TRANSACTIONAL
- Cache Invalidations
- Cache Locking
- Spring Boot
- Cache Provider
- Cache Eviction Policy
- Time to Live (TTL)

---

This comprehensive explanation underscores the importance and complexity of integrating **second level caching in large-scale Java applications with Hibernate and Spring Boot**, illustrating best practices for configuration, entity-level setup, provider selection, and cache concurrency management.