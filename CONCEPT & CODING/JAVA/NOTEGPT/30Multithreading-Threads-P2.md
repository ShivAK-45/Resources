### Summary: Multi-threading in Java – Creation, Lifecycle, Synchronization, and Inter-Thread Communication

This content explores **Java multi-threading**, focusing on **thread creation methods**, **thread lifecycle**, **synchronization**, and **inter-thread communication** through practical examples and key explanations. The discussion builds foundational understanding critical for Java concurrency and includes a demonstration of **monitor locks** and **producer-consumer problems** with synchronization mechanisms.

---

### Key Topics Covered

- **Two ways to create threads in Java**
- **Runnable interface vs Thread class**
- **Thread lifecycle and states**
- **Monitor locks and synchronization**
- **wait(), notify(), notifyAll() mechanisms**
- **Producer-consumer problem with synchronized resource sharing**
- **Deprecated thread control methods**

---

### 1. Thread Creation in Java: Two Approaches

#### Methods Overview

There are **two primary ways** to create threads in Java:

| Approach                    | Description                                                                                       | Advantage                                                      | Limitation                                     |
|----------------------------|-------------------------------------------------------------------------------------------------|----------------------------------------------------------------|------------------------------------------------|
| **Implement Runnable Interface** | Create a class that implements `Runnable` and define the `run()` method. Pass the instance to a `Thread` constructor. | Can extend from other classes (supports multiple interfaces)  | Requires additional `Thread` object creation  |
| **Extend Thread Class**     | Create a class that extends `Thread` and override the `run()` method directly.                  | Directly inherits `Thread` capabilities, simpler in usage      | Cannot extend any other class (single inheritance restriction)   |

#### Explanation

- Java supports **single inheritance**, so if a class already extends another class, it cannot extend `Thread`.
- A class can implement multiple interfaces, so implementing `Runnable` provides **greater flexibility** and is preferred in production.
- The **Thread class itself implements Runnable** and provides lifecycle methods such as `start()`, `sleep()`, `interrupt()`, etc.

---

### 2. How Thread Start and Runnable Run Methods Interact

- **Create a Runnable object** implementing the `run()` method.
- **Pass the Runnable to a Thread constructor**.
- Call `thread.start()` to start the thread.
- Internally, `start()` calls the `run()` method of the passed Runnable object.
- The `run()` method contains the task executed by that thread.

#### Insights

- The `Thread` class maintains a reference to a target `Runnable` object.
- Its own `run()` method checks if this target is not null and invokes its `run()` method.
- This design allows mixing functionalities; any Runnable can be assigned to any Thread instance.

---

### 3. Thread Lifecycle and States

| Thread State          | Description                                                                                                             |
|----------------------|-------------------------------------------------------------------------------------------------------------------------|
| **New**              | Thread object created but thread not started yet (`new Thread()`).                                                      |
| **Runnable**         | Thread is ready to run and waiting for CPU time after calling `start()`. Includes both ready and running states combined. |
| **Running**          | Thread has CPU and is executing (part of Runnable state).                                                               |
| **Blocked**          | Thread waiting for I/O or synchronization lock (monitor lock held by another thread).                                     |
| **Waiting**          | Thread explicitly waits via `wait()`, releasing monitor locks until `notify()` called.                                   |
| **Timed Waiting**    | Thread waits for a specified period via `sleep()` or timed `wait()`.                                                     |
| **Terminated**       | Thread finished execution or stopped. Cannot restart again.                                                             |

#### Important Notes

- **Running** and **Runnable** are generally considered as one state — thread is either waiting for CPU or actively running.
- When a thread is **blocked** (e.g., waiting for I/O or locks), it releases monitor locks.
- During **waiting** (via `wait()`), thread goes non-runnable and releases all monitor locks.
- During **timed waiting** (e.g., `sleep()`), thread holds monitor locks but waits for specified time.
- Terminated threads cannot be restarted.

---

### 4. Synchronization and Monitor Locks in Java

#### Monitor Lock Concept

- Every Java object has an associated **monitor lock**.
- A **synchronized method or block** requires the thread to acquire the monitor lock on the object.
- Only **one thread at a time** can hold the monitor lock for a particular object.
- If the monitor lock is held by one thread, others requesting it are blocked until the lock is released.

#### Example Scenario

- Two threads access the **same object's synchronized method**:
    - Thread 1 acquires the monitor lock, executes method.
    - Thread 2 waits for the lock to be released before proceeding.
- If threads operate on **different object instances**, they acquire different locks — no blocking between them.

#### Synchronization Types

| Method                 | Description                                                                                |
|------------------------|--------------------------------------------------------------------------------------------|
| **Synchronized Method**  | Entire method locked on the object's monitor lock.                                        |
| **Synchronized Block**   | Specific code block inside method locked on specified object. Allows finer control.       |

#### Practical Example

- Multiple threads invoke synchronized and unsynchronized methods on the same object.
- Synchronized methods block concurrent access.
- Unsynchronized methods run immediately without acquiring any lock.

---

### 5. wait(), notify(), and notifyAll() — Inter-Thread Communication

- **wait()**: Thread releases monitor lock and waits indefinitely until another thread calls `notify()` or `notifyAll()` on the same object.
- **notify() / notifyAll()**: Wakes up one or all waiting threads respectively, moving them to runnable state.
- **Important:** `wait()` must be called inside synchronized blocks to hold the monitor lock.

#### Use Cases

- Threads coordinate work on shared resources.
- A thread waits if it cannot proceed (e.g., buffer empty).
- Another thread notifies waiting threads after making shared state changes.

---

### 6. Producer-Consumer Problem Implementation Example

#### Problem Statement

- A **fixed-size shared buffer (queue)** is accessed by two threads:
    - **Producer:** Generates data, inserts into buffer.
    - **Consumer:** Takes data from buffer for processing.
- Constraints:
    - Producer must wait if buffer is full.
    - Consumer must wait if buffer is empty.

#### Shared Resource Class Design

| Method         | Purpose                         | Synchronization | Behavior                                            |
|----------------|--------------------------------|-----------------|----------------------------------------------------|
| `addItem()`    | Producer adds item              | Synchronized     | Sets `itemAvailable=true`, calls `notifyAll()`     |
| `consumeItem()`| Consumer consumes item          | Synchronized     | Waits if `itemAvailable=false`, then consumes item |

#### Producer and Consumer Runnable Classes

- **Producer Runnable**:
    - Sleeps for predefined time.
    - Adds item to buffer.

- **Consumer Runnable**:
    - Attempts to consume item.
    - Waits if no item is available.

#### Workflow and Thread Coordination

- Consumer thread waits if item not present.
- Producer thread adds item, calls `notifyAll()`.
- Consumer thread is awakened, resumes consumption.
- This loop prevents buffer overflow and underflow, maintaining thread-safe coordination.

---

### 7. Additional Notes on Thread Control

- Methods like `stop()`, `suspend()`, and `resume()` are **deprecated** due to unsafe behavior.
- Best practice to control thread termination involves:
    - Using flags or interruption mechanisms.
    - Designing threads to safely finish execution.

---

### Summary Table: Thread States and Lock Behavior

| Thread State   | Monitor Lock Held?    | Description                                  | Example Scenario                          |
|----------------|----------------------|----------------------------------------------|------------------------------------------|
| New            | No                   | Thread object instantiated, not started      | `Thread t = new Thread();`                |
| Runnable       | May or may not        | Ready/running; waiting for or executing on CPU | After `start()`, active or waiting for CPU |
| Blocked        | No                   | Waiting for I/O or lock held by another thread | Waiting to acquire synchronized resource  |
| Waiting        | No                   | Thread explicitly waiting, waiting for notification | Called `wait()` inside synchronized block |
| Timed Waiting  | Yes                  | Waiting a specified time, e.g., sleep         | Using `sleep(5000)`                       |
| Terminated     | No                   | Finished execution, cannot restart            | Run completes or thread stopped          |

---

### Key Insights

- **Two thread creation ways serve different design needs:** interface method for classes needing other inheritance; inheritance for straightforward thread subclassing.
- **Synchronization uses monitor locks to guard critical sections**, ensuring one thread works on resource at a time.
- **wait() and notify() enable threads to communicate about resource availability**, crucial in producer-consumer scenarios.
- Use of **while loop for wait conditions** is vital to avoid spurious wakeups per Java specification.
- **Thread lifecycle states define transitions from creation to termination and intermediate waiting/blocking stages**.
- Deprecation of older thread control methods encourages safer, more maintainable concurrency practices.

---

### Summary Conclusion

This session provides a detailed exploration of **Java multithreading fundamentals**, focusing on:

- **Thread creation techniques** and their implications for object-oriented design.
- The **lifecycle states of threads**, including how threads move between runnable, waiting, and blocked states.
- The mechanism of **synchronization with monitor locks** to enforce exclusive access to critical sections.
- Practical understanding of **thread coordination using wait/notify** for use cases like the **producer-consumer problem**.
- Awareness of **deprecated methods**, highlighting modern best practices to manage thread lifecycle safely.

Understanding these concepts and their correct implementations are **essential for writing robust, concurrent Java applications** and is foundational knowledge for interviews and professional software development.

---

**Assignment:** Develop a synchronized **producer-consumer program** using a shared fixed-size queue with proper use of `wait()`, `notify()`, and synchronized blocks. This exercise consolidates concepts of thread communication and synchronization in concurrent programming.   