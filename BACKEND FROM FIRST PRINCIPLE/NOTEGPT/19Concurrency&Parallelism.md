### Summary of Video Content on Backend Concurrency, Parallelism, and Threading Models

- [00:00:00 ~ 00:02:49]  
  Every backend system must handle multiple tasks concurrently because serving one request at a time leads to unacceptable delays or errors. In real-world production environments with thousands of users, servers cannot afford to be synchronous or single-threaded. Understanding how servers and operating systems handle multiple tasks simultaneously is crucial for forming an effective mental model to design scalable backend applications. This video focuses on building that foundational understanding rather than immediate implementation tips. It explains how backend requests are processed and why concurrency is essential for handling multiple requests efficiently.

- [00:03:27 ~ 00:09:46]  
  A typical backend request involves receiving a client request, processing it through layers (routing, service, handler, repository), and making database queries. The waiting time for database responses varies based on deployment—locally it might be 1-2 milliseconds, but in production across regions, it can reach 100 milliseconds or more. During this waiting period, if the backend operates synchronously, the CPU remains idle, wasting billions of instructions that could otherwise be executed. For example, a 100-millisecond database wait on a modern CPU capable of 3 billion instructions per second wastes roughly 300 million instructions.

A typical API call involves multiple IO-bound operations (database queries, external API calls like email or cache), resulting in significant amounts of time spent waiting rather than computing. In one example, 95% of the time is spent waiting for IO responses, while only 5% is CPU usage. Concurrency addresses this inefficiency by allowing the CPU to process other tasks while waiting for IO operations to complete, thus improving overall resource utilization.

- [00:09:46 ~ 00:13:38]  
  The video distinguishes between **concurrency** and **parallelism**, concepts often confused:

    - **Parallelism** means executing multiple instructions simultaneously, which requires multiple CPU cores. Each core executes one instruction at a time, so parallelism depends on hardware capabilities.

    - **Concurrency** means structuring a program to deal with multiple tasks at once by starting, pausing, and resuming various operations. This can be achieved even on a single CPU core by interleaving execution. From the user’s perspective, multiple tasks appear to be handled simultaneously, but the CPU processes one instruction at a time, switching rapidly between tasks.

A timeline example illustrates concurrency with two requests (A and B) sharing a single CPU core: while request A waits for a database response, the CPU switches to process request B, preventing idle CPU cycles. When both requests have IO waits, the CPU continues switching among other tasks, maintaining efficiency without needing multiple cores.

- [00:13:38 ~ 00:22:28]  
  When multiple CPU cores are available, true parallelism can be achieved by running multiple threads simultaneously, each on a separate core. Parallelism accelerates CPU-bound workloads like image processing or encryption, which involve intensive computations and matrix operations. Concurrency is generally more beneficial for IO-bound workloads where the system spends most of its time waiting for external responses.

Backend applications are predominantly IO-bound, making concurrency mechanisms crucial to maximize CPU utilization and handle many simultaneous requests. However, CPU-bound tasks benefit from parallelism, using multiple cores to speed up processing.

- [00:22:28 ~ 00:26:56]  
  There are two fundamental mechanisms to implement concurrency:

    1. **Threads:** Operating system–managed independent units of execution. Each thread has its own stack, instruction pointer, and state. The OS scheduler assigns CPU time slices to threads and preemptively switches between them, enabling multitasking. Threads can be blocked by IO operations, causing the scheduler to switch to runnable threads. Threads within the same process share memory, allowing fast inter-thread communication via shared variables, but this shared memory model can introduce concurrency bugs.

    2. **Event Loops:** Typically single-threaded, event loops avoid blocking by using callbacks to suspend and resume tasks. When an IO operation starts, the task registers a callback and yields control to the event loop, which continues processing other tasks. The event loop checks for IO completion and resumes tasks accordingly. This model avoids thread overhead and context switching latency but requires careful programming to avoid blocking the single thread.

- [00:26:56 ~ 00:37:52]  
  Threading overheads include:

    - **Memory overhead:** Each thread requires its own stack (often several megabytes).
    - **Creation overhead:** Creating threads involves system calls and setup.
    - **Context switching overhead:** Switching between threads requires saving/restoring CPU registers and state, costing microseconds to milliseconds per switch.

Excessive threads can cause performance degradation due to these overheads, especially in high-concurrency environments like web servers handling thousands of requests.

- [00:37:52 ~ 00:44:34]  
  Event loops, used by languages like JavaScript (Node.js), are more efficient for IO-bound workloads because they avoid context switches and large memory overhead by running on a single thread with callbacks or async/await syntax. The key requirement is **never to block the event loop** with long-running CPU-bound operations, as this halts all processing. Async/await is syntactic sugar over callbacks, making asynchronous code easier to write and read.

- [00:44:34 ~ 00:51:08]  
  The video compares how threading and event loop concurrency models handle backend requests step-by-step:

    - In **threading models**, each request is handled by a separate thread that blocks during IO, and the OS scheduler switches to other threads during blocking.

    - In **event loop models**, a single thread handles multiple requests by registering callbacks for IO operations. When an IO operation is pending, the event loop switches to other tasks without blocking.

Code examples show JavaScript callbacks vs. async/await syntax illustrating how concurrency is implemented under the hood.

- [00:51:08 ~ 01:11:20]  
  The video introduces **Go's concurrency model**, which uses **goroutines**—lightweight, user-space "virtual threads" managed by the Go runtime scheduler instead of the OS. Goroutines are more memory-efficient than OS threads, allowing thousands or millions to run concurrently. The Go runtime multiplexes many goroutines onto a smaller number of OS threads, switching between goroutines efficiently with minimal overhead (pointer switches).

Go creates a new goroutine per incoming HTTP request, enabling concurrent request processing with minimal resource usage. Goroutines pause when performing IO and allow the scheduler to run other goroutines, similar to threads but more lightweight. This hybrid approach balances concurrency and parallelism by leveraging OS threads for parallel execution and goroutines for concurrency.

- [01:11:20 ~ 01:16:39]  
  The video explains how async/await works internally as a **state machine**: the async function maintains states that represent execution checkpoints. Each await represents a suspension point where control is yielded back to the event loop, and execution resumes when the awaited operation completes. This explains why async functions must be marked with `async` and why blocking the event loop halts all async code execution.

- [01:16:39 ~ 01:24:20]  
  Concurrency introduces **race conditions** when multiple threads or goroutines access and modify shared memory without synchronization. The video uses a counter increment example to illustrate the **lost update problem**, where concurrent increments cause incorrect final values due to interleaved reads and writes.

Even single-threaded async/await models face race conditions when shared variables are modified concurrently in asynchronous functions. The example of a bank balance withdrawal function shows how concurrent calls can result in inconsistent state (negative balance) due to interleaved checks and updates.

- [01:24:20 ~ 01:26:56]  
  To avoid race conditions, synchronization mechanisms like **locks (mutexes)** are used, which enforce mutual exclusion so only one thread can access a critical section at a time. The video briefly mentions Go's **channels**, which provide a message-passing concurrency model to avoid shared state problems by communicating through channels instead of shared memory.

- [01:26:56 ~ 01:28:39]  
  **Final summary and recommendations:**

    - IO-bound workloads (typical backend tasks involving databases, external API calls, file operations) require concurrency to efficiently utilize CPU and handle many simultaneous requests. Event loops, async/await, goroutines, and virtual threads are effective concurrency primitives.

    - CPU-bound workloads (heavy computation like image processing, encryption) benefit from parallelism—using multiple CPU cores and threads to perform multiple operations simultaneously.

    - Most backend applications lean heavily on concurrency because IO waits dominate request processing time, but some niche applications require parallelism.

    - Understanding the distinction between concurrency and parallelism enables better architectural decisions and helps choose appropriate tools and languages for backend development.

---

### Core Concepts and Keywords:

- **Concurrency:** Structuring programs to handle multiple tasks by interleaving execution, possibly on a single CPU core.
- **Parallelism:** Executing multiple tasks literally at the same time using multiple CPU cores.
- **IO-bound:** Tasks waiting on external resources like databases, network, or file system.
- **CPU-bound:** Tasks requiring intensive computation.
- **Thread:** OS-managed unit of execution with its own stack and state.
- **Event loop:** Single-threaded mechanism using callbacks or async/await to handle IO without blocking.
- **Goroutine:** Lightweight virtual thread managed by Go runtime.
- **Scheduler:** Component that allocates CPU time slices to threads or goroutines.
- **Context switch:** Overhead of switching CPU execution from one thread to another.
- **Race condition:** Concurrency bug caused by unsynchronized access to shared variables.
- **Mutex/Lock:** Synchronization primitive to avoid race conditions.
- **Channels:** Go’s message-passing mechanism to avoid shared state concurrency bugs.
- **Async/await:** Language syntax for asynchronous programming, implemented as state machines.

---

### FAQ:

**Q: Why can’t a server process one request at a time?**  
A: Because the CPU would be idle during IO waits, causing massive inefficiency and inability to handle multiple simultaneous users.

**Q: What is the difference between concurrency and parallelism?**  
A: Concurrency is about managing multiple tasks logically at the same time, even if only one is executed at an instant; parallelism is about physically executing multiple tasks simultaneously using multiple CPU cores.

**Q: How does event loop concurrency avoid blocking?**  
A: By registering callbacks and yielding control when waiting for IO, allowing the single thread to process other tasks.

**Q: What are goroutines and how do they differ from OS threads?**  
A: Goroutines are lightweight, user-space managed virtual threads that multiplex many onto fewer OS threads, resulting in lower overhead and higher scalability.

**Q: How do race conditions occur?**  
A: When multiple threads or goroutines access shared memory without synchronization, leading to lost updates or inconsistent state.

**Q: How can race conditions be prevented?**  
A: By using locks, mutexes, or message-passing mechanisms like Go channels to enforce exclusive access or coordinate state changes safely.

---

This comprehensive understanding of backend concurrency, parallelism, threading, and event loops forms a critical foundation for designing scalable, efficient backend systems that maximize resource utilization and provide responsive services to users.


### Chapter: Understanding Concurrency and Parallelism in Backend Systems

#### Introduction: The Significance of Handling Multiple Requests Simultaneously
- [00:00:00 ~ 00:01:44]  
  Every backend system, particularly **HTTP-based web servers**, must handle **multiple requests concurrently**. Handling only one request at a time causes delays or errors for other users, making the system impractical for real-world, multi-user environments. Understanding how servers and operating systems manage **concurrent processing** is crucial for backend developers.
- Key vocabulary:
  - **Concurrency**: The ability to deal with multiple tasks by starting, pausing, and resuming them, often on a single CPU core.
  - **Parallelism**: The ability to execute multiple instructions simultaneously, requiring multiple CPU cores.
  - **IO-bound operations**: Tasks mostly waiting on external resources like databases or network calls.
  - **CPU-bound operations**: Tasks primarily involving computation by the CPU.

- This chapter explores how concurrency and parallelism operate mechanically, the trade-offs involved, and how different programming languages and runtimes express these concepts (e.g., Node.js, Go). The goal is to build a **mental model** that helps debug, architect, and optimize backend applications.

---

#### Section 1: The Nature of Backend Requests and IO Wait Times
- [00:03:27 ~ 00:06:49]  
  Backend requests often involve waiting for responses from databases or other external services, which introduces **IO wait times**. For example:
  - A simple local database query might take 1-2 milliseconds.
  - Queries to a database in a different availability zone might take 20-30 milliseconds.
  - Queries to databases in distant regions might take 90-100 milliseconds.

- If the server processes requests **synchronously** (one at a time), the CPU remains **idle** during these waits. Given modern CPUs can execute around **3 million instructions per millisecond**, a 100 ms wait means **300 million instructions are wasted** per request.

- Real-world API calls often involve multiple such IO operations (e.g., 3-5 database queries plus external API calls), totaling ~250 ms of waiting, while CPU usage during processing might be as low as 10 ms — meaning **95% of server resources remain idle**.

- Understanding and solving this inefficiency is the primary goal of **concurrency** in backend systems.

---

#### Section 2: Distinguishing Concurrency from Parallelism
- [00:09:42 ~ 00:13:38]  
  **Parallelism** involves executing multiple instructions at the same time on multiple CPU cores. For example, two CPU cores can process two threads simultaneously. This requires **hardware-level support**.

**Concurrency**, however, is about structuring programs to handle multiple tasks by **interleaving execution** on a single CPU core. This is done by **starting, pausing, and resuming** execution in a way that *appears* simultaneous, even though only one instruction runs at any given millisecond.

- Visualization of concurrency on a timeline shows two requests (A and B) processed by a single core:
  - Request A uses CPU for initial processing, then waits on IO.
  - While A waits, Request B gets CPU time.
  - When IO for A returns, A resumes CPU usage, and B waits during its IO phase.

- The **CPU is never idle**; it switches between tasks efficiently. This interleaving is the essence of concurrency.

- In contrast, with parallelism (multiple cores), both requests are truly processed simultaneously.

---

#### Section 3: IO Bound vs CPU Bound Workloads
- [00:18:57 ~ 00:22:28]  
  Backend workloads are mostly **IO-bound**, spending the majority of their time waiting for external responses. CPU is used briefly for parsing, validation, or simple computation.

- **IO-bound tasks** require concurrency to avoid CPU idling. The system must handle many IO operations simultaneously to be efficient.

- **CPU-bound tasks** involve heavy computations, such as image processing, encryption, or video rendering. These benefit from **parallelism**, using multiple cores to speed up processing.

- Most backend services lean heavily on concurrency because they deal mostly with IO-bound workloads. However, for CPU-intensive tasks, parallelism is critical.

---

#### Section 4: Mechanisms for Concurrency: Threads and Event Loops
- [00:22:28 ~ 00:38:18]  
  Two major mechanisms enable concurrency:

1. **Threads**:
  - Threads are independent execution units managed by the operating system.
  - Each thread maintains its own **stack** (for function calls and local variables) and an **instruction pointer** (tracking execution state).
  - The OS scheduler assigns CPU time slices (e.g., 2 ms per thread), preemptively switching between threads.
  - When a thread encounters a **blocking IO operation**, it signals the OS scheduler to mark it as blocked and switches to another runnable thread.
  - Threads within the same process share memory (heap, global variables), enabling fast but potentially dangerous **shared-state communication**.
  - Threads are **memory-heavy** (each stack can be ~8 MB virtual memory) and expensive to create and switch due to overhead and context switching latency (~1-10 microseconds).
  - This overhead limits scalability when thousands of threads are created simultaneously.

2. **Event Loop Model**:
  - Uses a **single thread** that manages multiple IO operations via **callbacks** or **async/await** patterns.
  - The event loop continuously checks (polls) for IO completion events and resumes the corresponding task.
  - The key is **never blocking the event loop**; all IO operations must be non-blocking, allowing the loop to handle thousands of connections efficiently.
  - This model reduces memory overhead and context switching costs dramatically.
  - However, **CPU-bound tasks block the event loop**, deteriorating performance, so heavy computations are discouraged inside event-loop-driven code.

---

#### Section 5: Practical Examples of Threading vs Event Loop in Backend Systems
- [00:44:06 ~ 00:56:54]  
  **Threading Model Example:**
- Each request is handled by a separate OS thread.
- Request processing includes parsing input, making a blocking database query, and waiting for results.
- When a thread blocks, the OS scheduler switches to another thread.
- Threads handle their own stacks and maintain execution state via OS data structures.
- This model is easy to understand but expensive in resources.

**Event Loop Model Example (JavaScript):**
- Requests are processed in a single thread using callback functions or **async/await**.
- When an IO call (e.g., database query) occurs, control is handed back to the event loop, which schedules other tasks.
- Once IO completes, the registered callback resumes execution.
- Async/await syntax simplifies callback nesting by internally managing state machines.
- This approach is highly efficient for IO-bound workloads but requires care to avoid blocking the event loop with CPU-heavy code.

---

#### Section 6: Go’s Virtual Threads (Goroutines) – A Hybrid Approach
- [01:00:59 ~ 01:11:20]  
  Go introduces **goroutines**, which are **virtual threads** managed by the Go runtime rather than the OS.

- Unlike OS threads, goroutines are lightweight and have small stacks that grow dynamically.
- The Go runtime maintains a scheduler that multiplexes thousands of goroutines onto a small number of OS threads (usually equal to CPU cores).
- When a goroutine blocks on IO, the scheduler pauses it and runs other goroutines on the same OS thread.
- This approach combines concurrency (many goroutines) with efficient use of parallelism (multiple OS threads/cores).
- The **Go HTTP server** creates a new goroutine per request, enabling massive concurrency with low memory overhead.

---

#### Section 7: Understanding Async/Await as State Machines
- [01:10:50 ~ 01:16:39]  
  Async/await functions can be conceptualized as **state machines**:
- Each `await` statement represents a suspension point, transitioning the function to a new state.
- Execution pauses at await, yielding control back to the event loop.
- When IO completes, the runtime resumes execution from the saved state.
- This explains why `await` can only be used inside `async` functions — the async keyword instructs the compiler/runtime to convert the function into a state machine.

---

#### Section 8: The Concurrency Problem: Race Conditions and Shared State
- [01:16:09 ~ 01:26:56]  
  Concurrency introduces challenges, notably **race conditions** caused by **shared mutable state**.

- Example: Two threads incrementing a shared counter concurrently may cause lost updates, where increments overwrite each other, resulting in incorrect final values.
- Even in single-threaded async/await environments, race conditions can occur if asynchronous operations overlap and modify shared state without synchronization.
- Solutions include:
  - **Locks/mutexes** to ensure mutual exclusion, allowing only one thread to modify shared data at a time.
  - **Channels (Go)** or message-passing models to avoid shared memory by passing data safely between goroutines.

- The video emphasizes that understanding concurrency problems is critical, but the primary takeaway is knowing the distinction between IO-bound and CPU-bound tasks and choosing the appropriate concurrency model accordingly.

---

#### Conclusion: Key Takeaways and Implications for Backend Development
- [01:26:25 ~ 01:28:39]
- Backend systems are predominantly **IO-bound**, requiring concurrency to avoid wasting CPU cycles during IO waits.
- **Concurrency** enables efficient multitasking on limited CPU cores by interleaving task execution, maximizing resource utilization.
- **Parallelism** leverages multiple CPU cores to speed up CPU-intensive tasks and complements concurrency in backend systems.
- Threads provide native OS-level concurrency but come with significant overhead in terms of memory, context switching, and latency.
- Event loops provide lightweight concurrency suitable for IO-bound workloads but cannot handle CPU-heavy tasks well without blocking.
- Languages like Go use **virtual threads (goroutines)** to combine concurrency and parallelism efficiently with minimal overhead.
- Understanding the difference between concurrency and parallelism empowers developers to select the right tools and design choices for backend architecture.
- Mastery of these concepts improves debugging, performance tuning, and system design for scalable backend applications.

---

### Summary of Important Points (Advanced Bullet Notes)

- Backend servers must handle **multiple simultaneous requests** to serve thousands of users efficiently.
- **IO-bound operations** (database, network, file systems) dominate backend workloads and cause CPU idling if not handled concurrently.
- Modern CPUs can execute billions of instructions per second; synchronous blocking wastes this capacity.
- **Concurrency** means managing multiple tasks by interleaving execution on one or few CPU cores without actual simultaneous execution.
- **Parallelism** means executing multiple instructions simultaneously on multiple CPU cores.
- Threads: OS-managed, expensive in memory (stack ~8MB), creation, and context switching overhead.
- Event loop: Single-threaded, uses callbacks or async/await for non-blocking IO, avoids thread overhead but blocks on CPU-heavy tasks.
- Go’s **goroutines** are lightweight virtual threads managed by runtime, enabling thousands of concurrent tasks with efficient scheduling on OS threads.
- Async/await functions are implemented as **state machines** to pause and resume execution cleanly.
- Race conditions arise from **shared mutable state**; solved by locks, mutexes, or channel-based communication.
- IO-bound tasks benefit from **concurrency**; CPU-bound tasks from **parallelism**; backend systems usually require both.
- Choosing the right concurrency model depends on workload characteristics and hardware capabilities.

---

This chapter provides a foundational understanding of how backend systems manage concurrency and parallelism, equipping developers with the conceptual tools to build efficient, scalable applications.