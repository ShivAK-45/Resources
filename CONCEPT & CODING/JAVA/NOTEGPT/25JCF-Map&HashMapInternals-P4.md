### Chapter: Understanding Java Map and HashMap Internals

#### Introduction to Map and Its Significance in Collections

The Java Collections Framework encompasses various interfaces and classes to store and manipulate groups of objects. Among these, the concept of a **Map** is fundamental yet distinct from other collections like **List**, **Set**, or **Queue**. Unlike these structures that deal with single values, a **Map** specializes in storing **key-value pairs**, enabling efficient data association. This chapter delves deeply into the Map interface, focusing particularly on **HashMap**, its properties, mechanisms, and performance characteristics.

Understanding Map is essential because many other collection types internally rely on it; for example, **HashSet** uses a **HashMap** internally, **LinkedHashSet** uses a **LinkedHashMap**, and **TreeSet** uses a **TreeMap**. Hence, grasping Map’s functioning is prerequisite before exploring these related collections.

**Key Vocabulary:**

- **Map Interface:** A data structure storing key-value pairs where keys are unique.
- **HashMap:** A widely used, unsynchronized **Map** implementation providing average constant time performance.
- **Hash Code:** Integer representation computed from an object’s key, used for indexing.
- **Load Factor:** A measure determining when to resize the internal storage.
- **Rehashing:** The process of resizing and reorganizing the HashMap.
- **Collision:** Occurs when two keys generate the same index.
- **Chaining:** Collision resolution strategy using linked nodes.
- **Treeify:** Conversion from linked nodes to balanced binary trees for improved performance.
- **Thread Safety:** Whether a data structure supports concurrent access by multiple threads.

---

#### Distinctiveness of Map in the Collections Framework

- Traditional collections in Java, such as **List** and **Set**, deal with collections of values and implement the **Collection** interface.
- However, **Map** does **not** extend Collection because its operations revolve around key-value pairs rather than single values.

    - Methods in Collection operate on single elements (e.g., add, remove a value).
    - Map requires operations specific to pairings like `put(key, value)`, `get(key)`, and handling **unique keys**.

- This fundamental difference justifies why Map has its own interface and distinct methods tailored for efficient key-based lookup and modification.

---

#### Overview of Map and HashMap Characteristics

- **Map** is an *interface* that maps objects from a key type $K$ to a value type $V$.
- Core implementations include **HashMap**, **HashTable**, **LinkedHashMap**, and **TreeMap**.

    - **HashMap** is the most popular; stores key-value pairs using hashing.
    - The keys must be **unique**; however, values can be duplicate.

Example:

| Key (Roll Number) | Value (Student Name) |
|-------------------|----------------------|
| 1                 | SJ                   |
| 2                 | KJ                   |
| 3                 | SJ                   | (duplicate value allowed) |

- Attempting to insert a duplicate key will **overwrite** the previous value associated with that key.

---

#### Core HashMap Methods and Their Utilities

- `size()` — Returns the total number of key-value mappings present.
- `isEmpty()` — Returns `true` if there are no key-value mappings; else `false`.
- `containsKey(Object key)` — Checks if a particular key exists.
- `get(Object key)` — Fetches the value associated with a given key.
- `put(K key, V value)` — Adds or updates the key-value mapping.

These methods form the **basic interface** for interacting with a map. For example, calling `m.put(2, "J")` inserts the key $2$ with value `"J"`, or overrides it if key $2$ already exists.

---

#### Internal Data Structure of HashMap

- Underneath, a **HashMap** maintains an **array** (called `table`) of **Node<K, V>** objects.
- Each **Node** implements the **Map.Entry<K, V>** interface and contains:

    1. **hash** — Computed integer associated with the key.
    2. **key** — The actual key object.
    3. **value** — The value object.
    4. **next** — Reference to the next node in case of collision (forming a linked list).

- The array has a **default initial capacity of 16** if not specified.
- When inserting a key-value pair:

    1. Compute the hash of the key.
    2. Calculate the index using:

       $$
       \text{index} = \text{hash} \mod \text{table.length}
       $$
    3. Insert the node at that index in the array.

Example: If the hash of key $1$ is $1234567$, and the table size is $3$, the index is $1234567 \mod 3 = 1$, so the node is stored in slot 1.

---

#### Collision Handling via Chaining

- A collision happens if the computed index already contains a node.
- HashMap resolves collisions using **chaining**:

    - A linked list of nodes is maintained at that index.
    - On `put()`, traverse the list:
        - If the key exists, overwrite the value.
        - Else, append a new node to the end.

Example:

- Index 1 contains node `(key=1, value="SJ")`.
- Inserting `(key=10, value="KJ")` with the same index:
    - Since key differs, node for 10 is linked as next.

Thus, the structure at index 1 becomes:

Node(key=1, value="SJ") → Node(key=10, value="KJ") → null

---

#### Hash Code and Equals Contract

Two critical methods must obey the contract for keys in HashMap:

- **hashCode()** generates the hash value used for indexing.
- **equals()** determines logical equivalence of two keys.

Contracts:

1. If two objects are **equal** (i.e., `obj1.equals(obj2) == true`), their hash codes **must** be identical.

    - This ensures keys that are equal map to the same bucket.

2. If two objects share the same hash code, they are **not necessarily equal**.

    - Hash collisions are possible; hence, `equals()` is used to differentiate keys within a bucket.

This contract ensures consistent placement and lookup of keys despite collisions.

---

#### Load Factor and Rehashing for Performance

- **Load Factor ($\alpha$)** is a measure controlling when the HashMap should increase capacity to maintain performance.
- Default **load factor = 0.75**.

    - If capacity = $n$, threshold before resizing is:

      $$
      \text{threshold} = n \times \alpha
      $$

    - For initial capacity $16$, threshold = $12$.

- When number of key-value mappings exceeds threshold, **rehashing** occurs:

    - HashMap doubles capacity (e.g., from $16$ to $32$).
    - Existing entries are recalculated and redistributed into new buckets.

- Rehashing reduces collisions and maintains the **average time complexity of $O(1)$** for insertion, deletion, and lookup.

---

#### Treeification for Handling High Collisions

- When collisions cause a bucket's linked list to grow beyond a certain size (threshold $8$), the list is converted into a **balanced binary search tree** (likely a Red-Black tree).

- This process is called **treeification** and improves worst-case operations’ time complexity from **$O(n)$ to $O(\log n)$**, where $n$ is the number of nodes in that bucket.

- This step prevents performance degradation in the worst scenarios where many keys hash to the same bucket.

---

#### Additional Functionalities: Null Keys, Thread Safety, and Variants

- **HashMap allows one null key and multiple null values**, distinguishing it from other Map implementations.
- **Thread safety:**

    - HashMap is *not* thread-safe.
    - **Hashtable** is a synchronized, thread-safe variant, disallowing null keys or values.
    - **ConcurrentHashMap** offers a thread-safe alternative with better concurrency characteristics.

Hence, choosing the right implementation depends on concurrency requirements.

---

#### Iterating Through a HashMap

- Use the `entrySet()` method to obtain a set view of **Map.Entry<K, V>**.
- Iterate over each entry to access key and value.

Example:

```java
for (Map.Entry<K, V> entry : hashmap.entrySet()) {
    K key = entry.getKey();
    V value = entry.getValue();
    // process key and value
}
```

- This process provides a clear and efficient way to traverse map's contents.

---

#### Summary of Methods Covered

- `size()`: number of key-value pairs.
- `isEmpty()`: checks if map is empty.
- `containsKey()`: checks if key exists.
- `get()`: retrieves value by key.
- `put()`: inserts or updates key-value pairs.
- `remove()`: deletes a mapping by key.
- `putIfAbsent()`: inserts a key-value only if key is not present or mapped to null.
- `keySet()`: returns a set of all keys.
- `values()`: returns a collection of all values.

---

#### Conclusion: Importance and Implications

Understanding **HashMap** internals is critical for writing efficient Java applications and performing well in technical interviews. Mastery over its design:

- Explains why a Map isn’t a Collection due to its **key-value** semantics.
- Reveals how **hashing** and **collision resolution** maintain efficient access.
- Highlights the role of **load factor** in balancing memory and speed.
- Demonstrates methods to mitigate worst-case performance via **treeification**.
- Clarifies differences in thread safety among Map implementations.

In essence, grasping these concepts empowers developers to select, use, and optimize Maps effectively, ensuring scalable and performant code. Further exploration of variants like **LinkedHashMap** and **TreeMap** can be pursued with this foundation.

---

### Advanced Notes Summary

- **Map** focuses on key-value pairs, differs from Collection.
- Map’s keys are unique; values can repeat.
- HashMap uses an **array of Node<K,V>** storing hash, key, value, and a pointer.
- Hashing + modulo operation determines the array index.
- Collisions handled via linked list chaining; converted to balanced trees after threshold.
- `hashCode()` and `equals()` must obey contracts for correct operation.
- Load factor $0.75$ triggers resizing once threshold is exceeded.
- Treeification occurs when bucket size exceeds 8 nodes, improving worst-case lookup.
- HashMap supports one null key and multiple null values; not thread-safe.
- Hashtable is thread-safe, disallows nulls.
- ConcurrentHashMap provides thread-safe operations with better performance.
- Key methods: `put()`, `get()`, `remove()`, `putIfAbsent()`, `containsKey()`, etc.
- Iteration via `entrySet()` provides access to key-value pairs.
- Average time complexity: $O(1)$; worst case $O(\log n)$ after treeification.

Master these internals to fully leverage the power and efficiency of Java Maps.