### Summary: Custom Interceptors in Spring Boot

This detailed explanation discusses **custom interceptors** in Spring Boot, emphasizing their crucial role in handling cross-cutting concerns such as caching, logging, authentication, and authorization. The content is divided into two main parts: writing an interceptor that acts **before a controller method is invoked**, and writing a **custom annotation-based interceptor that acts after reaching a specific controller method**.

---

### Key Concepts and Terminology

- **Interceptor:** A mediator invoked **before or after** the execution of actual business logic (e.g., a controller method).
- **HandlerInterceptor:** A Spring MVC interface that defines three key methods to intercept requests:
    - $$ \text{preHandle} $$: Before the controller method execution.
    - $$ \text{postHandle} $$: After the controller method but before the view is rendered.
    - $$ \text{afterCompletion} $$: After the request has been completed, regardless of success or exception.
- **DispatcherServlet:** The core servlet in Spring MVC that routes incoming HTTP requests to appropriate controllers.
- **Custom Annotation:** User-defined annotation to mark or qualify methods/classes for specialized handling.
- **Meta-annotations:** Annotations applied on other annotations, primarily:
    - $$ @\text{Target} $$: Defines where the annotation can be applied (e.g., method, class).
    - $$ @\text{Retention} $$: Defines how long the annotation is retained (Source, Class, Runtime).
- **AOP (Aspect-Oriented Programming):** Used to define cross-cutting concerns with **pointcuts** and **advices**.
- **Bean Registration:** Letting Spring manage the lifecycle of interceptors and annotations via $$ @\text{Component} $$ or manual bean declaration.

---

### Part 1: Custom Interceptor Before Controller Invocation

#### Overview

- The goal is to intercept incoming HTTP requests **before** they reach a specific controller method.
- The interceptor acts between the **DispatcherServlet's request routing** and the execution of the controller method.

#### Implementation Steps

1. **Create a Controller:**
    - Example: A simple `UserController` with a single method annotated with `@GetMapping("/getUser")`.
2. **Implement a Custom Interceptor Class:**
    - Implement the `HandlerInterceptor` interface.
    - Override the three key methods:
        - $$ \text{preHandle(request, response, handler)} $$ — executed **before** the actual controller method.
        - $$ \text{postHandle(request, response, handler, modelAndView)} $$ — executed **after** the controller method, but before the view is generated.
        - $$ \text{afterCompletion(request, response, handler, exception)} $$ — executed after completion **including when exceptions occur**.
3. **Register the Interceptor:**
    - Create a configuration class implementing `WebMvcConfigurer`.
    - Override the `addInterceptors` method to register the interceptor and define:
        - **Path patterns** for which URLs to apply the interceptor.
        - **Exclude path patterns** to exclude certain URLs from interception.
    - Use $$ @\text{Component} $$ on the interceptor class to enable Spring Bean management.

#### Clarification on $$ \text{postHandle} $$ vs $$ \text{afterCompletion} $$

| Method            | When is it executed?                                             | Execution Condition                   | Purpose                                    |
|-------------------|-----------------------------------------------------------------|-------------------------------------|--------------------------------------------|
| $$ \text{postHandle} $$       | After the controller method, before rendering view                   | Only executes if no exception occurs | To add attributes to the model, or modify response |
| $$ \text{afterCompletion} $$  | After the entire request is complete, whether successful or failed | Executes **always**, including exceptions | Cleanup or resource release, similar to `finally` block |

---

### Part 2: Custom Interceptor After Controller Invocation Using Custom Annotations

#### Motivation

- Intercept method calls **after** reaching the controller, specifically **targeting methods annotated with a custom annotation**.
- Useful for advanced logging, caching, or any logic where method-level annotation-driven interception is needed.

#### Steps to Create Custom Annotation

1. **Define Annotation:**
    - Use the keyword $$ @\text{interface} $$ to define it.
    - Annotate it with meta-annotations:
        - $$ @\text{Target} $$ specifies applicable locations (e.g., $$ \text{ElementType.METHOD} $$).
        - $$ @\text{Retention} $$ controls lifetime:
            - $$ \text{RetentionPolicy.SOURCE} $$: Discarded by compiler, no bytecode presence.
            - $$ \text{RetentionPolicy.CLASS} $$: Present in bytecode but ignored at runtime.
            - $$ \text{RetentionPolicy.RUNTIME} $$: Retained in bytecode **and accessible at runtime** (required for runtime processing).
2. **Annotation "Fields":**
    - Annotations can include method-like declarations to accept values.
    - Allowed return types (as annotation elements):
        - Primitive types: $$ \text{int, boolean, double, etc.} $$
        - $$ \text{String} $$
        - Enums
        - $$ \text{Class} $$ types
        - Arrays of the above types.
    - Values can optionally have defaults.
3. **Use Case:**
    - Example: Defining `@MyCustomAnnotation(name = "user")`.
    - Apply it over methods or classes to mark them.

#### Implementing the Interceptor with AOP for Annotation-based Interception

- Use Spring AOP's concepts of **Pointcuts** and **Advices**.
- Define a pointcut that targets methods annotated with the custom annotation using:

  $$ @\text{annotation}(com.example.MyCustomAnnotation) $$

- Define an advice (e.g., around advice) to execute code **before and after** the annotated method invocation.
- Inside the advice, use Java Reflection to:
    - Obtain method metadata.
    - Access annotation properties such as `name`.
- Example workflow inside advice:
    - Print **"do something before actual method"**.
    - Read annotation details (like `name`) and possibly log or act on it.
    - Proceed with the original method call.
    - Perform any after-method logic.

---

### Technical Details of the DispatcherServlet & Interceptor Execution Flow

| Step                      | Description                                                                                 |
|---------------------------|---------------------------------------------------------------------------------------------|
| 1                         | Request enters **DispatcherServlet** (central routing component in Spring MVC)              |
| 2                         | $$ \text{preHandle} $$ of registered interceptors are invoked in sequence                 |
| 3                         | Controller method is invoked (business logic execution)                                    |
| 4                         | $$ \text{postHandle} $$ methods executed if no exception                                  |
| 5                         | View rendering (if applicable)                                                            |
| 6                         | $$ \text{afterCompletion} $$ methods executed regardless of exceptions (like finally)     |

---

### Summary Table: Creating a Custom Interceptor vs Annotation-Based Interceptor

| Aspect                                 | Custom Interceptor (HandlerInterceptor)                      | Custom Annotation + AOP Interceptor                        |
|--------------------------------------|-------------------------------------------------------------|----------------------------------------------------------|
| Intercepts at                         | Before and after controller method (globally or path-based) | Specific methods annotated with a custom annotation      |
| Used Interface/Class                  | `HandlerInterceptor`                                        | Aspect class with `@Aspect` and advice methods            |
| Key Methods                          | `preHandle()`, `postHandle()`, `afterCompletion()`             | Advice methods: `@Before`, `@After`, `@Around`             |
| Method Targeting                     | URL/path patterns                                            | Methods or classes annotated with custom annotation       |
| Annotation Requirement               | Not mandatory                                               | Mandatory (custom created annotation drives pointcut)     |
| Bean Registration                   | Via $$ @\text{Component} $$ or manual bean registration         | Aspect beans managed by Spring                             |
| Runtime Access to Annotation Values | Not applicable                                             | Yes, via reflection during advice execution                |
| Use Case Examples                   | Authentication, request validation, CORS filtering           | Dynamic logging, caching, role-based method-level control  |

---

### Important Technical Notes on Annotations

- **Meta-annotation Explanation:**

| Meta-Annotation | Purpose                                                    | Example                                              |
|-----------------|------------------------------------------------------------|------------------------------------------------------|
| $$ @\text{Target} $$        | Specifies where an annotation can be applied (method, class, param) | `@Target(ElementType.METHOD)` means applicable on methods only |
| $$ @\text{Retention} $$     | How long the annotation is retained: source, bytecode, runtime        | `@Retention(RetentionPolicy.RUNTIME)` – runtime retention|

- Annotation elements (pseudo-fields) cannot have parameters and must return:
    - Primitive types
    - String
    - Enum
    - Class literals
    - Arrays of above types

---

### Practical Use Cases Highlighted

- **Before Controller Interceptors:**
    - Authentication and authorization checks before executing controller logic.
    - Logging request metadata.
    - Request validation or throttling.

- **Annotation-driven Post-controller Interceptors:**
    - Custom logging based on method context or annotation variables.
    - Caching support with specific annotation hints.
    - Auditing actions triggered around specific business methods.

---

### Final Remarks

- **Key takeaway:** Understanding the **interceptor lifecycle** and the ability to create **custom annotations** paired with **AOP advices** empowers developers to build flexible, reusable, and maintainable Spring Boot applications that address concerns like security, monitoring, and performance optimizations elegantly.
- The **distinction between interceptors at web-level (HandlerInterceptor) and method-level (AOP with custom annotations)** is crucial.
- Proper use of **meta-annotations $$ @\text{Target} $$ and $$ @\text{Retention} $$** is essential for annotation validity and runtime accessibility.
- Practice and familiarity with this foundational knowledge are essential, as they form the basis for advanced Spring Boot features including caching, security, and custom logging mechanisms.

---

### Keywords

- **Spring Boot**
- **Custom Interceptor**
- **HandlerInterceptor**
- **DispatcherServlet**
- **PreHandle, PostHandle, AfterCompletion**
- **Custom Annotation**
- **Meta-annotation ($@Target$, $@Retention$)**
- **AOP (Pointcut, Advice)**
- **Annotation Retention Policies**
- **Bean Registration**
- **Java Reflection**

---

This summary thoroughly condenses the original tutorial content, preserving all critical technical details, terms, explanations, and coding best practices about custom interceptors and annotations in Spring Boot, suitable for professional learning and reference.