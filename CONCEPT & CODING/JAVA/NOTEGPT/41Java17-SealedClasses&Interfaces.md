### Summary of Sealed Classes and Interfaces in Java 17

The content provides a comprehensive explanation of **sealed classes and interfaces** introduced in **Java 17**. It focuses on addressing the problem of uncontrolled inheritance hierarchies in Java, explaining how sealed types bring stronger control over which classes can extend or implement a particular type.

---

### Key Problem Addressed: Lack of Control Over Inheritance

- **Uncontrolled class inheritance**: Prior to sealed classes, any class could extend a superclass or implement an interface without restrictions.
- For example, an interface like `Shape` could be implemented by **any class**, such as `Circle`, `Rectangle`, `Triangle`, or even unplanned classes.
- This led to **unbounded hierarchies**, where:
    - Subclasses could be unlimited and unpredictable.
    - Code handling these classes must always include a **default condition** (especially in `switch` statements or conditional checks) to account for unknown or newly added subclasses.
- Unintentional additions by other developers could cause silent bugs if new subclasses aren't appropriately handled.

**Summary of the issue:**

| Problem Aspect                 | Description                                                                            |
|-------------------------------|----------------------------------------------------------------------------------------|
| Unrestricted subclassing       | Any class can implement or extend a type, even unplanned subclasses                   |
| Maintenance complexity         | Always need default clauses to handle unknown subclasses                              |
| Risk of silent errors          | Code breaks if new subclasses aren't handled properly                                |
| Lack of hierarchy control      | No way to limit or specify allowed subclasses                                        |

---

### Introduction to Sealed Classes and Interfaces

- **Sealed types** restrict which classes or interfaces can extend or implement a particular class or interface.
- The **`sealed` keyword** is used along with the **`permits` clause**, which explicitly lists permitted subclasses.

For example:

$$
\text{sealed interface Shape permits Circle, Rectangle}
$$

- Only the classes/interfaces listed in the `permits` clause (`Circle` and `Rectangle`) can implement or extend `Shape`.
- Attempting to declare any other subclass, such as `Triangle`, not included in `permits` will cause a compile-time error.

---

### Rules and Requirements for Sealed Types

1. **Permits list** defines all the allowed immediate subclasses.
    - All classes or interfaces in the permits list must be **direct subclasses/implementations** of the sealed type.
    - No indirect subclasses are allowed in the permits declaration.
2. Each subclass in the permits list **must declare its own sealing modifier**, one of:
    - `final`: no further subclassing allowed (terminates the hierarchy).
    - `non-sealed`: opens the subclass for unrestricted further inheritance.
    - `sealed` (with its own permits list): continues controlled inheritance by restricting subclasses further.
3. All subclasses listed must have an **actual implementation** present at the time of sealing.
    - You cannot allow future subclasses that are not currently implemented.
4. Enforcement is **compile-time** ensuring control over class hierarchies and preventing accidental extension.

---

### Example Hierarchy Using Sealed Types

The content provides a concrete example using `Shape`, which is a sealed interface permitting only `Circle`, `Polygon`, and `AbstractShape`.

| Type               | Modifier        | Explanation                                                        |
|--------------------|-----------------|------------------------------------------------------------------|
| `Shape`            | `sealed`        | Permits: `Circle`, `Polygon`, `AbstractShape`                    |
| `Circle`           | `final`         | No further subclasses allowed (branch ends here)                 |
| `Polygon`          | `non-sealed`    | Open branch, allowing unlimited subclasses like `Hexagon`        |
| `AbstractShape`    | `sealed`        | Permits: `Rectangle` and `Triangle` only                         |
| `Rectangle`        | `final`         | No further subclasses allowed                                    |
| `Triangle`         | `non-sealed`    | Open for further subclassing                                       |

This structure shows how developers can selectively restrict or open branches of a type hierarchy:

- The `Circle` branch is closed.
- The `Polygon` branch is open.
- The `AbstractShape` branch restricts permission to specific subclasses but allows further control at deeper levels.

---

### Benefits and Use Cases

- **Strong control on type hierarchies**:
    - Prevents unwanted or unexpected subclassing.
    - Ensures that only explicitly allowed subtypes can extend or implement certain interfaces/classes.
- **Eliminates the need for default cases in exhaustive conditionals**:
    - When the permit list and subclass modifiers are properly used, all subtypes can be exhaustively handled in `switch` or `if` statements.
- **Improves maintainability and readability**:
    - Clear documentation of allowed subclasses directly in the code.
    - Helps during refactoring or API design, ensuring client code handles all known cases.

---

### Important Notes on Modifiers for Child Classes of a Sealed Type

| Modifier          | Effect                                                                                   |
|-------------------|------------------------------------------------------------------------------------------|
| `final`           | No further subclasses allowed. Terminates the branch.                                   |
| `non-sealed`      | Opens the class/interface for unrestricted subclassing.                                |
| `sealed` + `permits` | Restricts subclasses again to the specified types. Continues controlled hierarchy.  |

---

### Summary Table: Design Choices For Sealed Subclasses

| Subclass         | Purpose                            | Allows Further Subclassing?  | Use Case Example               |
|------------------|----------------------------------|-----------------------------|-------------------------------|
| `final`          | End of inheritance branch        | No                          | `Circle` class – branch closure|
| `non-sealed`     | Open for all further subclasses  | Yes                         | `Polygon` interface – open branch|
| `sealed` + permits | Controlled further subclassing   | Yes, but limited            | `AbstractShape` - controlled branching |

---

### Core Concepts Recap

- **Sealed types help control inheritance hierarchies by explicitly specifying permitted subclasses.**
- Every subclass or subinterface permitted must declare its subclassing policy (`final`, `non-sealed`, or `sealed`).
- Sealing enforces that all subclasses are known at compile-time.
- This mechanism improves safety, predictability, and maintainability of Java programs by eliminating uncontrolled inheritance.

---

### Keywords and Definitions

| Term                | Definition                                                                                                  |
|---------------------|-------------------------------------------------------------------------------------------------------------|
| `sealed`            | Java keyword that restricts which classes/interfaces can extend/implement a class/interface.                |
| `permits`           | Clause specifying the exact set of permitted subclasses for a sealed class or interface.                    |
| `final`             | Modifier that prevents further subclassing of a class or interface.                                         |
| `non-sealed`        | Modifier that removes restrictions on subclassing, allowing any subclass to extend/implement it.            |
| `sealed` (modifier) | Modifier for subclasses that continue to restrict subclassing by specifying their own permits list.         |
| Direct subclass     | A class/interface that immediately extends/implements a sealed type without any classes in-between.         |
| Default handling    | The catch-all case in switch statements or conditions that handle any unknown subtypes (avoided by sealing).|

---

### Conclusion

**Sealed classes and interfaces in Java 17 provide powerful tools for controlling class hierarchies by defining explicit permitted subclasses, enforcing strict subclassing policies, and improving code safety and maintainability.** This feature is particularly useful for API design and modeling well-defined class hierarchies, where control over extension is necessary.

By using modifiers such as `final`, `non-sealed`, and `sealed`, developers choose whether to close, open, or partially restrict each branch of the hierarchy. This flexibility combined with compile-time enforcement marks a significant improvement in Java's type system.

---

### *Not specified / Uncertain Details*

- Specific syntax or examples for switch statements handling sealed types.
- Detailed behavioral nuances or runtime impacts.
- Version compatibility issues beyond Java 17.

---

**If you have any questions or require further clarification on sealed classes and interfaces, discussion and comments are encouraged.**