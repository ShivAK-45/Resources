### Summary: Introduction of Sequence Interfaces in Java 21 Collections Framework

Java 21 introduces **three new interfaces:**
- **SequenceCollection**
- **SequenceSet**
- **SequenceMap**

These interfaces are incorporated into the existing Java Collections Framework hierarchy to **standardize operations on collections that maintain a defined element order**, fill gaps in the API, and improve code consistency and maintainability.

---

### Existing and New Collection Hierarchy in Java 21

- **SequenceCollection** extends the existing **Collection** interface.
- **List** and **Deque (DQ)** now extend **SequenceCollection** (previously they directly extended Collection).
- **SequenceSet** extends **SequenceCollection**.
    - **LinkedHashSet** and **SortedSet** now extend **SequenceSet** (previously they extended Set).
- **SequenceMap** extends **Map**.
    - **LinkedHashMap** and **SortedMap** extend **SequenceMap** (previously they extended Map).

**Collections left out** from these new interfaces are:
- **Queue (Q)**
- **PriorityQueue**
- **HashSet**
- **HashMap**
- **Hashtable**

---

### Motivation Behind Introducing Sequence Interfaces

Three critical questions guide understanding the need for these interfaces:

1. **What is the criteria to determine which collections belong to these new Sequence interfaces?**
2. **Why are these new interfaces necessary?**
3. **Why is there a separate SequenceSet interface instead of just using SequenceCollection?**

The **primary answer to all three questions lies in the concept of "sequenced collections"**—collections that maintain a **predictable iteration order** and allow **first and last element access/manipulation** along with **reversible views**.

---

### Key Criteria for a "Sequenced" Collection

A collection can be classified as "sequenced" if it satisfies **all** the following:

| Condition                  | Description                                                                                         | Examples                     |
|----------------------------|-------------------------------------------------------------------------------------------------|------------------------------|
| **Predictable Iteration**  | Elements are returned in a **consistent, well-defined order** every iteration. This can be: insertion order or sorted order. | List (insertion order), SortedSet/SortedMap (sorted order) |
| **Access/Manipulation of First & Last Elements** | Collection supports operations to **get, add, or remove** elements at the first and last positions.        | List: `get(0)`, `add(0, e)`; Deque: `addFirst`, `removeLast` |
| **Reversible View**        | Ability to obtain a **reversed view of the collection** without copying or modifying the original data. | Collections with methods like `reverse()`, `descendingIterator()` |

If all these conditions are met, a collection can extend **SequenceCollection** (for lists and similar), **SequenceSet** (for sets with uniqueness), or **SequenceMap** (for map key-value pairs).

---

### Application of Criteria to Common Collection Types

| Collection Type    | Predictable Iteration | Supports First/Last Access & Manipulation | Supports Reversible View | Sequence Interface Inclusion |
|--------------------|----------------------|-------------------------------------------|-------------------------|------------------------------|
| List               | Yes (insertion order) | Yes (`get`, `add`, `remove`)              | Yes (`Collections.reverse`) | **SequenceCollection**       |
| Deque (DQ)         | Yes (insertion order) | Yes (`getFirst`, `addLast`, `removeFirst`) | Yes (`descendingIterator`)  | **SequenceCollection**       |
| Queue (Q)          | Yes (insertion order) | No (cannot add/remove last element)       | No                       | *Not included*                |
| PriorityQueue      | No (no sorted/insertion order) | No                                      | No                       | *Not included*                |
| HashSet            | No                   | No                                        | No                       | *Not included*                |
| LinkedHashSet      | Yes (insertion order) | Yes (requires manual iteration)           | Yes (via iteration)       | **SequenceSet**               |
| SortedSet (TreeSet)| Yes (sorted order)   | Yes (limited add first/last; throws UnsupportedOperationException) | Yes (`descendingIterator`) | **SequenceSet**               |
| HashMap            | No                   | No                                        | No                       | *Not included*                |
| LinkedHashMap      | Yes (insertion order) | Yes (manual iteration for access)         | Yes (can implement reverse view) | **SequenceMap**             |
| SortedMap (TreeMap)| Yes (sorted order)   | Yes (limited add first/last; throws exception) | Yes (`descendingMap()`)   | **SequenceMap**               |

---

### Why Some Collections Are Excluded from Sequence Interfaces

- **Queue** and **PriorityQueue** are excluded because:
    - Queue only allows insertion at the end and removal from the front; cannot access or manipulate the last element arbitrarily.
    - PriorityQueue's iteration order is undefined and unstable (uses heap internally). Only the head element has ordering guarantees (min/max), so the iteration is unpredictable.

- **HashSet**, **HashMap**, and **Hashtable** do not maintain insertion or sorted order, thus failing the first condition for predictable iteration.

---

### Why Introduce SequenceSet Separately?

- The **SequenceSet** interface addresses collections that enforce **no duplicate elements**, unlike SequenceCollection which allows duplicates (e.g., List, Deque).
- **LinkedHashSet** and **SortedSet** are examples of sequenced collections that **maintain order (insertion or sorted)** but **disallow duplicates**, so they extend SequenceSet (which itself extends SequenceCollection).

| Feature                | SequenceCollection | SequenceSet     |
|------------------------|--------------------|-----------------|
| Allows duplicates       | Yes                | No (unique elements only) |
| Provides first/last element access | Yes         | Yes             |
| Supports reversible view| Yes                 | Yes             |

---

### Improvements Provided by Sequence Interfaces

- **Uniformity of API:** Previously, each sequenced collection had its own set of methods to access/manipulate first and last elements and to create reversed views, which was difficult to memorize and maintain in code.
- **Standardized Method Names and Behavior:** The new interfaces provide common methods such as:
    - `$addFirst()$, $addLast()$`
    - `$getFirst()$, $getLast()$`
    - `$removeFirst()$, $removeLast()$`
    - `$reverse()$` (view reversal without data copying)

Example for List in Java 21+:

- `$getFirst()$` and `$getLast()$` return the first and last elements.
- `$addFirst(element)$` adds an element at the start (equivalent to `add(0, element)` before).
- `$addLast(element)$` appends an element.
- `$removeFirst()$` and `$removeLast()$` remove the first and last elements respectively.
- `$reverse()$` provides a reversed view of the list.

All other sequenced collections like Deque, LinkedHashSet, SortedSet, LinkedHashMap, and SortedMap now follow this standardized API.

---

### Handling of Unsupported Operations

- For **SortedSet** and **SortedMap**, the methods `$addFirst()$` and `$addLast()$` **throw `UnsupportedOperationException`**, since elements are ordered based on sorting rules, not insertion position.
- These collections rely on natural or comparator ordering: adding an element places it at the correct sorted position automatically.

---

### Practical Examples

#### List Example (Java 21+)

```java
SequenceCollection<String> list = List.of("B", "C", "D");
list.addFirst("A");   // Adds "A" at the start
list.addLast("E");    // Adds "E" at the end
String first = list.getFirst();  // "A"
String last = list.getLast();    // "E"
list.removeFirst();    // Removes "A"
list.removeLast();     // Removes "E"
list.reverse();        // Provides a reversed view
```

#### LinkedHashSet Example

- Maintains insertion order and uniqueness. If an existing element is added with `$addFirst()$`, it moves the element to the first position.
- Manual iteration was needed before; now standardized methods simplify code.

#### SortedSet Example

- Does not support addFirst/addLast explicitly; adding elements respects sorted order.
- Reverse iteration is supported by `$descendingIterator()$`.

#### LinkedHashMap and SortedMap Example

- LinkedHashMap preserves insertion order; new interface provides uniform API to access first/last entry and reverse iteration.
- SortedMap sorts keys naturally or via comparator; addFirst/Last throw exceptions but supports first/last entry retrieval and descending views.

---

### Summary of Key Improvements

- **Fills a major API gap:** Lack of a common interface for sequenced collections covering predictable iteration, first/last access, and reversible view.
- **Simplifies coding:** Engineers no longer need to memorize different method names and implementations for similar operations across different collection types.
- **Enables consistent code and easier maintenance** when dealing with ordered collections in Java.
- **Preserves existing behavior** but standardizes across types.

---

### Final Notes

- Collections that do not preserve order or allow access/manipulation at both ends (Queue, PriorityQueue, HashSet, HashMap) are excluded because they do not qualify as sequenced collections under the defined criteria.
- These new interfaces are purely additive and **do not break backwards compatibility**; they provide a clearer contract for collections where order and positioning matter.
- Developers are encouraged to use and practice these interfaces in Java 21+ to write cleaner and more maintainable code.
- Complete familiarity with the hierarchy and criteria negates the need to memorize interface extensions; understanding the sequencing concept suffices.

---

### Keywords

- **SequenceCollection**
- **SequenceSet**
- **SequenceMap**
- **Predictable iteration**
- **Insertion order**
- **Sorted order**
- **First/last element access**
- **Reversible view**
- **LinkedHashSet**
- **LinkedHashMap**
- **SortedSet**
- **SortedMap**
- **API standardization**

---

### Conclusion

Java 21’s introduction of Sequence interfaces standardizes operations on ordered collections by defining a common API for access, modification, and traversal operations involving the first and last elements and reversible views. This innovation fills a longstanding gap in the Java Collections Framework, enhancing code clarity and reducing cognitive load on developers by unifying different collection types under common behavior contracts while preserving their distinct characteristics such as uniqueness or sorting constraints.