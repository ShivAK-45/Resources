### Chapter: Understanding LinkedHashMap and TreeMap in Java

#### Introduction: Overview and Significance

This chapter explores two important **Map** implementations in Java: **LinkedHashMap** and **TreeMap**. As part of the larger **Map** interface family, these structures serve distinct purposes, primarily differentiated by their ordering guarantees and internal data structures. Understanding these implementations is crucial for developers aiming to optimize data retrieval, insertion order maintenance, or sorted key traversal in applications.

Key concepts introduced in this chapter include:

- **Map**: An interface describing a collection of key-value pairs.
- **HashMap** vs. **LinkedHashMap**: HashMap does not guarantee any ordering, whereas LinkedHashMap maintains order.
- **Insertion Order** and **Access Order**: Two types of ordering that LinkedHashMap can maintain.
- **Doubly Linked List**: A data structure used internally by LinkedHashMap to maintain order.
- **TreeMap**: A Map variant that keeps entries sorted according to natural ordering or a custom Comparator.
- **Red-Black Tree**: A self-balancing binary search tree data structure powering TreeMap.
- Time complexities such as **average \( O(1) \)** for HashMap and LinkedHashMap and **\( O(\log n) \)** for TreeMap.

By grasping these topics, software engineers can make informed choices about which Map implementation best fits requirements related to iteration order, performance, or sorted data needs.

---

#### 1. LinkedHashMap: Maintaining Order Beyond HashMap

##### 1.1 Definition and Differences from HashMap

- **LinkedHashMap** extends **HashMap** but differs by maintaining an order of elements.
- It supports two ordering modes:
    - **Insertion Order**: Elements are iterated in the sequence they were inserted.
    - **Access Order**: Elements are ordered based on recent access frequency; recently accessed items move to the end.

- In contrast, **HashMap** provides no ordering guarantee when iterating over elements.

##### 1.2 Internal Data Structure

- LinkedHashMap internally combines the **hash table** of HashMap with a **doubly linked list**.
- Each node in LinkedHashMap contains typical HashMap fields: **hash**, **key**, **value**, and **next** (for collision chains).
- In addition, each node holds two pointers: **before** and **after**, linking entries in order.
- This doubly linked list enables maintaining insertion/access order.

##### 1.3 Detailed Working with Example

- When inserting elements (e.g., keys *1, 21, 23, 141, 25*):
    - HashMap positions them into buckets based on hash values modulo bucket size.
    - LinkedHashMap also updates `before` and `after` pointers to chain nodes in insertion sequence.
    - For example, the node holding key 1's `after` points to key 21, key 21's `before` points back to 1, and so forth.
- On accessing elements with **accessOrder = true** (constructor parameter), the accessed node is moved to the end of the linked list, reflecting recent usage.

##### 1.4 Constructor and Usage

- By default, LinkedHashMap maintains insertion order (`accessOrder = false`).
- To enable access order, pass `true` to the constructor parameter `accessOrder`:  
  $$ \text{new LinkedHashMap<>(initialCapacity, loadFactor, true)} $$
- This feature is particularly useful for implementing caching mechanisms like Least Recently Used (LRU) caches, where least accessed items can be purged first.

##### 1.5 Performance and Thread Safety

- Time complexity remains similar to HashMap:
    - Average-case **\(O(1)\)** for insertion, lookup, and deletion.
    - Worst-case depends on hash collisions.
- LinkedHashMap is **not thread-safe**.
- To use in concurrent environments, wrap it with synchronized collections:
  $$ \text{Map<K, V> map = Collections.synchronizedMap(new LinkedHashMap<>());} $$

---

#### 2. TreeMap: Sorted Map Based on Red-Black Tree

##### 2.1 Concept and Key Features

- **TreeMap** implements the **NavigableMap** and **SortedMap** interfaces.
- It maintains keys in a **sorted order**, either:
    - The **natural ordering** of keys (e.g., ascending integer order).
    - A **custom Comparator** provided at creation.

- Unlike LinkedHashMap, it does **not** maintain insertion or access order but always returns keys sorted.

##### 2.2 Underlying Data Structure

- Internally uses a **Red-Black Tree**, a type of self-balancing binary search tree, ensuring:
    - Balanced height for efficient operations.
    - Consistent sorted ordering.
- Each node holds:
    - **key**
    - **value**
    - **parent**
    - **left child**
    - **right child**

##### 2.3 Time Complexity

- Operations such as insertion, lookup, and deletion run in **\(O(\log n)\)** time, slower than HashMap or LinkedHashMap but offering sorted views.

##### 2.4 Example: Custom Ordering with Comparator

- Example of a descending order comparator provided during TreeMap creation:

  $$
  \text{Comparator<Integer> cmp = (k1, k2) -> k2 - k1;}
  $$

- Keys inserted into TreeMap ({21, 13, 11, 5}) will be stored and returned as:  
  $$ 21, 13, 11, 5 $$  
  rather than ascending \(5, 11, 13, 21\).

##### 2.5 NavigableMap Interface Methods

TreeMap, as a NavigableMap, offers helpful methods to query entries relative to given keys:

- **lowerEntry(k)**: Returns the entry with key strictly less than $k$.
- **lowerKey(k)**: As above but only returns the key.
- **floorEntry(k)**: Entry with key less than or equal to $k$.
- **floorKey(k)**: Key less than or equal to $k$.
- **ceilingEntry(k)**: Entry with key greater than or equal to $k$.
- **ceilingKey(k)**: Key greater than or equal to $k$.
- **higherEntry(k)**: Entry with key strictly greater than $k$.
- **higherKey(k)**: Key strictly greater than $k$.

Additional methods include:

- **firstEntry()** / **lastEntry()**: Get minimum and maximum key entries.
- **pollFirstEntry()** / **pollLastEntry()**: Return and remove minimum/maximum entries.
- **descendingMap()**: Returns a reverse-ordered view of the map.
- **keySet()** / **descendingKeySet()**: Access keys in ascending or descending order.
- **headMap(toKey, inclusive)** and **tailMap(fromKey, inclusive)**: Return a view of the portion of the map whose keys are less than / greater than a specified key, with inclusivity options.

##### 2.6 Use Cases and Practical Implications

- TreeMap is ideal whenever a sorted key iteration or querying by range is required.
- The \(O(\log n)\) performance has to be balanced against the need for sorting.
- Lack of insertion/access order makes it less suitable when those orders are critical.

---

#### 3. Summary and Implications

This chapter distinguished between **LinkedHashMap** and **TreeMap** as implementations of the **Map** interface that extend the functionality of a traditional **HashMap** with ordering capabilities.

- **LinkedHashMap** preserves the order of elements either by insertion or by access. Internally, it uses a doubly linked list on top of the hash buckets, adding negligible overhead, and keeps runtime complexity close to **\(O(1)\)** on average.
    - This makes it suitable when iteration order must be predictable or when implementing LRU-type caches.
    - However, LinkedHashMap is not thread-safe and requires external synchronization for concurrent use.

- **TreeMap**, in contrast, relies on a red-black tree data structure to maintain keys sorted by natural order or a comparator. This leads to **\(O(\log n)\)** performance.
    - It offers rich navigational methods to interact with sorted data, including range views and closest-match queries.
    - It does not maintain insertion or access order, focusing strictly on key order.

Choosing between these implementations depends on requirements related to ordering guarantees and performance characteristics. LinkedHashMap excels where stable iteration order or access-order-based eviction is required, while TreeMap is best for sorted traversals, range queries, and ordering-dependent algorithms.

---

#### Key Insights and Takeaways

- **LinkedHashMap versus HashMap:**
    - Maintains order, uses doubly linked list pointers (**before**, **after**) in nodes.
    - Supports **insertion order** by default; **access order** optionally.
- **Access order** feature enables simple LRU cache implementations by moving accessed nodes to the tail.
- **TreeMap** is a sorted map implemented via a red-black tree, guaranteeing key order.
- TreeMap’s key sorting is configurable through a **Comparator** and supports powerful navigation/query methods.
- Performance parallels:
    - HashMap and LinkedHashMap: average **\(O(1)\)**.
    - TreeMap: average **\(O(\log n)\)**.
- Neither LinkedHashMap nor TreeMap is inherently thread-safe; use **Collections.synchronizedMap()** for concurrency.
- Understanding these implementations informs data structure selection for precise ordering, efficiency, and functionality in Java applications.

---

#### Example Snippets Highlighting Differences

- **Creating a LinkedHashMap maintaining insertion order:**

$$ \text{Map<Integer, String> map = new LinkedHashMap<>();} $$

- **Creating a LinkedHashMap with access order enabled (for cache-like behavior):**

$$ \text{Map<Integer, String> map = new LinkedHashMap<>(16, 0.75f, true);} $$

- **Creating a TreeMap with descending order comparator:**

$$ \text{Map<Integer, String> treeMap = new TreeMap<>((k1, k2) -> k2 - k1);} $$

---

This completes our comprehensive exploration of **LinkedHashMap** and **TreeMap** including their data structures, ordering mechanisms, time complexities, concurrency considerations, and practical applications. Mastery of these topics equips developers to incorporate optimal map-based solutions tailored to their specific software needs.