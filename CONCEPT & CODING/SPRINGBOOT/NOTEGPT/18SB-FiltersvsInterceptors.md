### Summary: Filters vs. Interceptors in Spring Boot

This detailed explanation by Shan from "Concept and Coding" clarifies the differences, use cases, and workflow of **Filters** and **Interceptors** within the Spring Boot framework, focusing on their roles in HTTP request and response processing. The content demystifies common doubts about when to use Filters versus Interceptors by providing definitions, architectural insights, lifecycle explanations, and practical implementation details.

---

### Core Concepts and Definitions

- **Filter**
    - Intercepts HTTP requests and responses **before** they reach the Servlet.
    - Applies to **all Servlets** within the Servlet container.
    - Used for generic cross-cutting concerns that are **agnostic of specific Servlets**.

- **Interceptor**
    - Specific to the **Spring Framework**.
    - Intercepts HTTP requests and responses **after the Servlet** (specifically Spring’s DispatcherServlet) has been chosen but **before reaching the Controller**.
    - Suitable for handling application-specific logic related to one particular Servlet (typically the Spring DispatcherServlet).

---

### Servlet Overview

- A **Servlet** is a Java class that handles HTTP requests by processing them and returning responses.
- Multiple Servlets can exist to handle distinct tasks, e.g., REST APIs, SOAP APIs, or static content.
- The **Servlet container** (like Tomcat) intercepts HTTP requests and assigns them to the appropriate Servlet based on configuration (e.g., URL patterns).
- In **Spring Boot microservices**, the use of multiple Servlets is minimal; most often, a single **DispatcherServlet** handles routing requests to controllers.

---

### Request Workflow Diagram (Conceptual)

| Step                         | Description                                                                    | Component              |
|------------------------------|--------------------------------------------------------------------------------|------------------------|
| 1. Request enters container   | Incoming HTTP request arrives at the Servlet container (e.g., Tomcat).          | Servlet Container      |
| 2. Filter chain execution     | Request passes through one or more Filters for pre-processing.                  | Filters                |
| 3. Servlet selection          | Container selects the appropriate Servlet for request handling.                 | Servlet                |
| 4. Spring DispatcherServlet   | If using Spring Boot, the request goes to the DispatcherServlet.                | DispatcherServlet      |
| 5. Interceptor chain execution| Interceptors execute before the request reaches the controller.                 | Interceptors           |
| 6. Controller processing      | Controller processes the request and generates a response.                      | Controller             |
| 7. Response travels back      | Response goes through Interceptors (reverse order), Servlet, Filters (reverse). | Interceptors, Filters  |

**Key point:**
- Filters act **before the request reaches any Servlet**
- Interceptors act **after the Servlet but before the Controller**

---

### Filters in Detail

- Filters form a **chain** where multiple Filters can be registered and executed in a specific order.
- Implemented using Java EE or Jakarta EE APIs (`javax.servlet.Filter` or `jakarta.servlet.Filter`).
- Have lifecycle methods:
    - `init()` — called once when Filter object is created, used for initialization.
    - `doFilter()` — main method where pre- and post-processing logic is implemented.
    - `destroy()` — called on Filter destruction for cleanup.

- During a request:
    - `doFilter()` runs sequentially for each Filter in the chain.
    - Each Filter calls `chain.doFilter()` to pass control to the next Filter or Servlet.
    - Response flows back through the Filters in the reverse order.

- Filters commonly handle:
    - Cross-cutting concerns like **security**, logging, compression, or request modification.
    - Logic that applies to **all incoming requests across all Servlets**.

- Filters can be registered programmatically with **URL patterns** and **ordering priority** using Spring's `FilterRegistrationBean`.

---

### Interceptors in Detail

- Interceptors are part of Spring Boot’s architecture and primarily intercept requests handled by the **DispatcherServlet**.
- Multiple Interceptors can be declared, ordered, and chained similarly to Filters.
- Interceptors provide three key callback methods:
    - `preHandle()` — runs before Controller execution, returns boolean:
        - Returning `false` stops further execution of filters, other interceptors, and controllers.
    - `postHandle()` — runs after controller processing but before view rendering.
    - `afterCompletion()` — runs after rendering the view, for cleanup tasks.

- Typical use cases for Interceptors:
    - Application-specific request logic tied to controllers.
    - Authorization, performance monitoring, or modifying the request/response specific to an application module.

---

### Comparison Table: Filters vs. Interceptors

| Feature                   | Filter                                   | Interceptor                               |
|---------------------------|------------------------------------------|------------------------------------------|
| Framework Scope           | Servlet API / Jakarta EE, generic across frameworks | Spring Framework specific                 |
| Execution Point           | Before Servlet allocation                 | After Servlet allocation, before Controller |
| Use Case                  | Generic, applies across multiple Servlets | Application-specific, tied to Spring dispatching |
| Lifecycle Methods         | `init()`, `doFilter()`, `destroy()`      | `preHandle()`, `postHandle()`, `afterCompletion()` |
| Control over Execution    | Can stop chain by not calling `chain.doFilter()` | Can stop processing by returning `false` from `preHandle()` |
| Ordering                  | Configurable via `FilterRegistrationBean`  | Configurable via registry with order      |
| Access to Request/Response| Yes                                     | Yes                                     |
| Typical Usage             | Logging, security filters, compression, encoding | Authentication, metrics, custom logic per controller |
| Applicability in Microservices | Often used widely for cross-cutting concerns | Used for logic specific to Spring MVC controllers |

---

### Sequence of Execution with Multiple Filters and Interceptors

| Request Flow                      | Response Flow                   |
|---------------------------------|-------------------------------|
| Filter 1 → Filter 2 → Servlet → Interceptor 1 → Interceptor 2 → Controller | Controller → Interceptor 2 → Interceptor 1 → Filter 2 → Filter 1 |

- Filters process in registration order on request; reversed on response.
- Interceptors execute in registration order for `preHandle` on request; reversed order on `postHandle` and `afterCompletion` on response.

---

### Practical Implementation Tips

- **Filters**:
    - Register using Spring `FilterRegistrationBean` with explicit order and URL patterns.
    - Implement `doFilter()` to call the next filter before processing response.
    - Use filters for concerns like Spring Security, which intercepts all HTTP requests globally.

- **Interceptors**:
    - Register via Spring MVC configuration using `registry.addInterceptor()`.
    - Implement all three lifecycle methods, controlling flow by returning boolean from `preHandle()`.
    - Useful for logic tightly coupled to Spring controllers.

- Both Filters and Interceptors can modify request headers, request bodies, or response contents. The choice depends on whether the logic needs to apply broadly (Filters) or is specific to Spring MVC (Interceptors).

---

### Summary of When to Use What

- **Use Filters** when:
    - You need to apply logic to **all HTTP requests**, across multiple Servlets or outside Spring framework context.
    - You require framework-agnostic request/response manipulation (e.g., encoding, security headers, logging).
    - You want to handle pre-processing before any Servlet selection happens.

- **Use Interceptors** when:
    - Your logic is **specific to Spring MVC** controllers or DispatcherServlet.
    - You want to add pre-/post-processing only to controller handlers.
    - You require fine-grained control for microservice application-specific logic.

---

### Additional Notes

- The Servlet container and DispatcherServlet architecture has evolved; with microservices architecture, the need for multiple Servlets has reduced.
- Spring DispatcherServlet usually handles all API requests routed to the application.
- Both Filters and Interceptors support ordering and multiple instances.
- Returning `false` in Interceptor’s `preHandle()` prevents further request processing.
- Filters must call the filter chain to allow the next filter or Servlet to execute.

---

### Conclusion: Key Insights

- **Filters and Interceptors, while similar in purpose, operate at different layers in the web request lifecycle**.
- Filters act on a more fundamental level, intercepting requests globally before Servlet selection.
- Interceptors are tied into Spring MVC’s DispatcherServlet mechanism, working before controller invocation.
- The **choice between Filter vs. Interceptor depends on scope, framework specificity, and application architecture**.
- Developers must understand these distinctions clearly to add cross-cutting concerns such as security, logging, or request modification efficiently.
- Both constructs allow multiple instances, ordered execution, and ability to halt the request flow conditionally.

---

This explanation provides concrete understanding and practical guidance on handling HTTP request interception in Spring Boot applications. It emphasizes the **architectural distinction**, suitable use cases, and implementation patterns critical for efficient Spring Boot development.