### Summary of Lombok Features in Java

This content provides an in-depth overview of the **Lombok library** in Java, designed primarily to **reduce boilerplate code** using annotations that automatically generate common coding elements during compilation. It emphasizes the practicality of Lombok in real-world industry projects and explains how to properly integrate it within an IDE (such as IntelliJ) and a Java project (using Maven `pom.xml`). The description highlights the **top 10 frequently used Lombok features**, explaining their usage, purpose, and behavior with detailed examples.

---

### Introduction to Lombok

- **Lombok** is a popular Java library aimed at **eliminating boilerplate code** that is unrelated to business logic.
- It operates by processing annotations **at compile-time** to inject additional code into Java classes.
- Compatible from **Java 6 onwards**, and supports more recent versions.
- Common issue: IDEs often show compilation errors because they don’t recognize the injected code until annotation processing is enabled and the Lombok plugin is installed.

**Setup Requirements:**

| Step                             | Description                                             |
|---------------------------------|---------------------------------------------------------|
| Add Lombok dependency           | Include in Maven `pom.xml` with the latest version      |
| Install Lombok plugin in IDE    | Required for IntelliJ (or other IDEs) to recognize annotations |
| Enable annotation processing    | Find in IDE settings under Build / Annotation Processing |

---

### Key Lombok Features Explained

#### 1. **`val` and `var` - Local Variable Type Inference**

- Used for declaring local variables with inferred types from their initializer expression.
- **`val`**: Makes the local variable **immutable** (final).
- **`var`**: Allows mutable local variables.
- Applies **only to local variables inside methods or blocks**, not to fields or method parameters.

*Example:*

```java
val a = 10;  // 'a' is inferred as final int
var b = "Hello";  // 'b' is inferred as String and mutable
```

---

#### 2. **`@NonNull` - Null Safety**

- Generates an automatic null check for method or constructor parameters annotated with `@NonNull`.
- If a `null` value is passed, a **`NullPointerException`** is thrown with an appropriate message.
- Used to prevent null values early to improve robustness.

---

#### 3. **`@Getter` and `@Setter` - Automatic Getter and Setter Generation**

- Generates public getter and setter methods for fields.
- Can be applied:
    - At **field level** for specific fields.
    - At **class level** to cover all non-static (getters) or all non-static and non-final fields (setters).
- Supports controlling **access modifiers** (`public`, `private`, `protected`) and **exclusion** for granular control.

| Scope             | Getter Generated For                      | Setter Generated For                        |
|-------------------|------------------------------------------|--------------------------------------------|
| Field level       | Specific field                           | Specific field                             |
| Class level       | All non-static fields                    | All non-static & non-final fields          |
| Static fields      | *No getter or setter generated*          | *No setter generated*                      |
| Final fields       | Getter generated                         | *No setter generated*                      |

---

#### 4. **`@ToString` - Automatic `toString()` Method**

- Generates a default `toString()` method that returns the class name and all field names along with their values.
- Supports customization by:
    - **Excluding specific fields** from output.
    - **Choosing to include or exclude field names**.
    - **Explicit field inclusion** for selective printing.

*Purpose:* Useful mainly for **logging and debugging**.

---

#### 5. **Constructors: `@NoArgsConstructor`, `@AllArgsConstructor`, `@RequiredArgsConstructor`**

| Annotation          | Description                                      |
|---------------------|------------------------------------------------|
| `@NoArgsConstructor` | Generates a **no-argument constructor**        |
| `@AllArgsConstructor`| Generates a constructor with parameters for **all fields** |
| `@RequiredArgsConstructor` | Generates a constructor including only **final** and **`@NonNull`** fields with null checks |

- `@NonNull` on fields contributes to `@RequiredArgsConstructor`.
- Null checks are inserted in constructors for `@NonNull` fields.

---

#### 6. **`@EqualsAndHashCode` - Generates `equals()` and `hashCode()` Methods**

- Creates `equals()` and `hashCode()` implementations consistent with their contract.
- By default considers **all non-static and non-transient fields**.
- Supports **inclusion and exclusion** annotations for selective field participation.
- Important for collection behavior, hashing, and comparison of objects.

---

#### 7. **`@Data` - Shortcut for Common Annotations**

- Combines multiple Lombok features:
    - `@ToString`
    - `@EqualsAndHashCode`
    - `@Getter` on all fields
    - `@Setter` on all **non-final** fields
    - `@RequiredArgsConstructor`
- Enables rapid data class creation with minimal code.

---

#### 8. **`@Value` - Immutable Data Class**

- Immutable variant of `@Data`.
- Applies the following:
    - Makes the class **final** (cannot be subclassed).
    - All fields become **private** and **final**.
    - No setters are generated.
    - Includes `toString()`, `equals()`, and `hashCode()`.
    - Generates an **all-arguments constructor** (similar to `@AllArgsConstructor`).
- Suitable for creating **immutable objects** with thread safety and predictability.

---

#### 9. **`@Builder` - Builder Pattern Implementation**

- Automates the **builder pattern** for a class.
- Creates a nested `Builder` class with setter-like methods for each field returning the builder instance, enabling **chainable method calls**.
- Builds an immutable object when `.build()` is called.
- Helps **create objects step-by-step** and supports **immutability** by omitting setters on the main class.
- Ideal for classes with many fields or optional parameters.

---

#### 10. **`@Cleanup` - Automatic Resource Management**

- Ensures that resources (e.g., streams, files) are automatically **closed** when exiting the current scope.
- Internally generates a `try-finally` block to invoke the resource's `close()` method.
- Simplifies management of resources like file streams, reducing errors and boilerplate.

---

### Summary Table of Features and Their Purposes

| Feature               | Purpose                                               | Usage Scope                | Key Behavior                           |
|-----------------------|-------------------------------------------------------|----------------------------|---------------------------------------|
| `val` / `var`         | Local variable type inference                         | Local variables             | `val` = final, `var` = mutable        |
| `@NonNull`            | Null-safety checks for parameters                     | Method/Constructor parameters| Throws NPE if null                     |
| `@Getter` / `@Setter` | Auto-generation of getters/setters                    | Fields or Class level       | Supports access control and exclusions |
| `@ToString`           | Generates `toString()` for debugging/logging          | Class or fields             | Custom inclusion/exclusion of fields  |
| `@NoArgsConstructor`  | Generate no-argument constructor                       | Class                      | Simple default constructor            |
| `@AllArgsConstructor` | Generate full fields constructor                       | Class                      | Constructor with all fields            |
| `@RequiredArgsConstructor`| Constructor with final and `@NonNull` fields       | Class                      | Null checks for required fields       |
| `@EqualsAndHashCode`  | Generates equals and hashCode methods                   | Class                      | Uses non-static fields by default      |
| `@Data`               | Shortcut for common annotations                         | Class                      | Combines getter, setter, toString, equals, hashCode, required constructor |
| `@Value`              | Immutable data class                                   | Class                      | Final fields, no setters, final class  |
| `@Builder`            | Builder pattern for object creation                     | Class                      | Nested builder class for chaining      |
| `@Cleanup`            | Ensures automatic resource cleanup                      | Local resource variables   | Generates try-finally for cleanup      |

---

### Key Insights

- **Lombok immensely reduces boilerplate in Java**, making code cleaner, shorter, and less error-prone.
- Enables **immutability** and better **null-safety** through annotations.
- Strongly supports **industry-standard design patterns** like builder and immutable data classes.
- Requires **proper IDE setup** (Lombok plugin and annotation processing) to avoid false compilation errors.
- While Lombok automates repetitive code, developers must understand generated code concepts to use it effectively and control generated members.

---

### Conclusion

Lombok is a powerful tool that enhances Java development productivity by automatically generating repetitive code segments such as getters, setters, constructors, `toString()`, and more. Its industry use is widespread, especially in complex projects requiring reliable coding patterns with minimal boilerplate. Correct setup within the development environment is critical for smooth usage. Familiarity with Lombok's top features discussed here can significantly improve code quality and maintainability.