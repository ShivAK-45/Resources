### Summary of Spring Bean Scopes and Their Behaviors

This content provides a detailed explanation of **Bean Scopes** in the Spring Framework, focusing on their characteristics, lifecycle, initialization behavior, and dependency resolution. It also includes examples and common issues encountered when combining different scopes.

---

### Core Concepts and Definitions

- **Bean Scope**: Defines the lifecycle and visibility of a bean instance in the Spring container.
- **IoC (Inversion of Control) Container**: Manages bean creation, lifecycle, and injection.
- **Eager Initialization**: Bean instance is created at container startup.
- **Lazy Initialization**: Bean instance is created only when requested/needed.
- **Proxy Mode**: A mechanism to create proxy objects to resolve dependency issues in mixed scopes.

---

### Overview of Five Primary Spring Bean Scopes

| Scope      | Lifecycle Behavior                              | Initialization Type   | Typical Use Case                      | Dependency Injection Behavior           |
|------------|------------------------------------------------|----------------------|-------------------------------------|----------------------------------------|
| **Singleton** | One instance per IoC container                  | Eager                  | Default scope for shared stateless beans | Single object injected everywhere      |
| **Prototype** | New instance each request/use                    | Lazy                   | Independent stateful beans          | New object injected each time           |
| **Request**  | One instance per HTTP request                      | Lazy                   | Web applications with request scope | New object created per HTTP request     |
| **Session**  | One instance per HTTP session                       | Lazy                   | User-session-specific beans         | Same instance reused during session     |
| **Application** | One instance across multiple IoC containers*     | Lazy                   | Rarely used; for shared instances across multiple IoCs | Single object shared across multiple IoCs |

*Not commonly used; specific to multi-IoC scenarios.

---

### Detailed Explanation and Examples

#### 1. Singleton Scope (Default)
- **Definition**: Spring creates only one instance of the bean per IoC container.
- **Initialization**: Eagerly initialized when the application context starts.
- **Dependency Injection**: Multiple classes depending on a singleton bean share the same instance.
- **Example Highlights**:
    - If a `TestController` class doesn't specify a scope, it is treated as singleton by default.
    - When IoC starts, it instantiates all singleton beans and their dependencies immediately.
    - Dependencies are resolved and injected in multiple stages: constructor -> dependency injection -> PostConstruct method.
    - Hash codes printed for singleton beans confirm only one instance is created and shared.
    - All API calls use the same singleton instance; no new instances get created after context initialization.

#### 2. Prototype Scope
- **Definition**: A new bean instance is created every time it is requested.
- **Initialization**: Lazily initialized; created only when explicitly requested.
- **Dependency Injection**: Each dependent bean gets a new instance of the prototype scoped bean.
- **Example Highlights**:
    - Defining a class with `prototype` scope means every request for that bean results in a new instance.
    - If both `TestController` and `User` beans are prototype, each API call creates new instances for both.
    - Singleton-scoped dependencies injected into prototype beans remain singletons.
    - Hash codes differ for every new instance confirming new object creation.

#### 3. Request Scope
- **Definition**: Bean instance created and scoped per HTTP request.
- **Initialization**: Lazy initialization; created on-demand tied to active HTTP request.
- **Dependency Injection**: Multiple injections during the same HTTP request share the same request-scoped bean instance.
- **Example Highlights**:
    - Beans are created only when an HTTP request comes in.
    - If a `TestController` and `User` beans are both request scoped, a new bean instance is created for each incoming request but reused within that request.
    - Prototype-scoped beans inside request-scoped beans will create new instances even within the same request.
    - No new beans created on application startup for request-scoped beans.
    - Demonstrated that during a single HTTP request, a bean required multiple times is created only once.

#### 4. Session Scope
- **Definition**: One bean instance per HTTP session (shared across multiple HTTP requests within that session).
- **Initialization**: Lazy initialization; created when session starts.
- **Dependency Injection**: Beans are created once per HTTP session and reused for all requests during session lifespan.
- **Example Highlights**:
    - Beans scoped as session initiate only after HTTP session creation (usually first user interaction).
    - Within a session, repeated API calls do not create new session-scoped bean instances.
    - Upon session invalidation (logout), session-scoped beans are destroyed.
    - New API calls after session expiration cause creation of new session-scoped bean instances.
    - Session expiration time is configurable via application properties (not detailed in content).

#### 5. Application Scope
- **Definition**: One bean instance shared across multiple IoC containers.
- **Use Case**: *Not widely used*; applicable in scenarios with multiple Spring IoCs.
- **Initialization**: Lazy initialization.
- **Notes**:
    - The content states it is hardly encountered in real-world applications.
    - Since mostly a single IoC is used per application, either singleton or other scopes suffice.
    - Provided mainly for completeness and conceptual understanding.

---

### Important Concepts and Challenges

#### Dependency Injection Across Different Scopes

- Injecting a **request-scoped bean into a singleton bean** produces errors at startup:
    - Singleton beans are eagerly initialized during application start, but request-scoped beans need an active HTTP request.
    - Since no HTTP request exists at startup, Spring fails to create the required request-scoped bean, causing a Bean creation failure.

- **Solution: Proxy Mode**
    - Use `@Scope(value="request", proxyMode = ScopedProxyMode.TARGET_CLASS)` on the request-scoped bean.
    - Spring creates a proxy/dummy object during singleton initialization instead of the real object.
    - The real bean is resolved and created upon actual HTTP request invocation.
    - This avoids Bean creation failure and supports lazy resolution of dependencies tied to HTTP request lifecycle.

---

### Bean Lifecycle and Initialization Flow (Simplified)

1. **IoC startup**:
    - Singleton beans and their dependencies eagerly initialized.
    - Request, Session, Prototype beans skipped if lazily initialized and not immediately needed.

2. **Dependency resolution**:
    - If a bean depends on another bean, that dependency is resolved per scope and initialization rules.
    - If a scope mismatch occurs (e.g., singleton depends on request), proxy mode is recommended.

3. **At runtime API call**:
    - For prototype/request/session scoped beans, create instances as per the scope rules.
    - Inject dependencies accordingly.
    - For singleton beans, use pre-created instances.

---

### Summary Table of Bean Initialization Examples

| Bean Type         | Scope       | Initialization                 | New Instance on API Call | Dependency Injection Behavior                                      |
|-------------------|-------------|-------------------------------|-------------------------|-------------------------------------------------------------------|
| TestController 1  | Singleton   | Eager at application start    | No                      | Shares same instances of dependencies (like User)                |
| User              | Singleton   | Eager at application start    | No                      | Single instance injected everywhere                               |
| TestController 1  | Prototype   | Lazy, on-demand at API call   | Yes                     | New instance each API call; dependencies injected per their scope|
| User              | Prototype   | Lazy, on-demand               | Yes                     | New object every time requested                                   |
| TestController 1  | Request     | Lazy, per HTTP request        | Yes (per request)        | User and Student beans instantiated or reused per request         |
| User              | Request     | Lazy, per HTTP request        | Yes (per request)        | One instance per request                                           |
| Student           | Prototype   | Lazy                          | Yes                     | New instance per injection/request                                |
| TestController 1  | Session     | Lazy, per HTTP session        | No (per session)         | Same User singleton instance reused                               |
| User              | Singleton   | Eager                         | No                      | Single instance in entire app                                     |

---

### Key Takeaways

- **Singleton scope** is the default, eagerly initialized, and results in one shared bean instance per application.
- **Prototype scope** produces a new instance on each injection and is lazily initialized.
- **Request scope** and **Session scope** apply only in web applications:
    - Request scope: One bean instance per HTTP request.
    - Session scope: One bean instance per HTTP session.
- Beans with `request` or `session` scopes cannot be directly injected into eagerly initialized `singleton` beans without proxying.
- Proxy mode enables relationship between `singleton` and request/session beans by deferring bean instantiation to the actual request/session.
- **Application scope** is for shared beans across multiple IoC containers, but rarely used.

---

### Conclusion

Understanding Spring Bean scopes is critical in designing a robust application with appropriate lifecycle management and memory utilization. This walkthrough clarifies the differences between scopes, their initialization timing, and common pitfalls when mixing scopes in dependency injection. It emphasizes the importance of proxy mode to handle lifecycle discrepancies and ensure smooth operation, especially in web applications with request and session-aware beans.

This fundamental knowledge allows developers to optimize Spring applications and avoid runtime exceptions related to bean lifecycle conflicts.