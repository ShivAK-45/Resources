### Summary: Java 21 Pattern Matching for Switch

Java 21 introduces **pattern matching for switch statements and expressions**, an enhancement built upon previous Java versions' pattern matching capabilities such as **pattern matching for `instanceof` (introduced in Java 16)** and **switch expressions (introduced in Java 14)**. This feature significantly expands the types of values that can be used in switch constructs and improves code readability while internally relying on `instanceof` checks.

---

### Core Concepts and Enhancements

#### Traditional Switch Support Before Java 21
- Supported data types in switch:
    - Primitive types: `int`, `short`, `byte`, `char`
    - Wrapper classes: `Integer`, `Short`, `Byte`, `Character`
    - `enum` types
    - `String`

- Syntax styles:
    - Traditional switch statement with `case` labels and `break`
    - Switch expressions introduced in Java 14, allowing expressions to return values using `case X -> ...` syntax and `yield`.

---

#### Pattern Matching for Switch in Java 21

**Key Advancements:**
- Switch can now work with **objects**, including:
    - Classes
    - Interfaces
    - Abstract classes
    - Wrapper objects

- Previously, switches only supported specific primitive and reference types listed above and **did not support object pattern matching** within switch.

- Internally, pattern matching for switch **uses `instanceof` semantics**, meaning:
    - Each case checks if the object matches a certain type,
    - If matched, the object is **type-cast and assigned to a pattern variable** usable within that case block.

- Example:
  ```java
  Object obj = "Hello World";

  switch (obj) {
      case String s -> System.out.println(s.toUpperCase());
      case Integer i -> System.out.println(i + 10);
      default -> System.out.println("Unknown");
  }
  ```
  This enables **automatic type casting**, similar to the snippet above, without explicit casting.

---

### Performance Considerations

- Classic switch using primitive types are **faster** because they use jump tables or lookup tables.
- Pattern matching on objects is internally implemented as a chain of `instanceof` checks and type casts, essentially an **if-else chain**.
- Hence, **switch with object pattern matching has similar performance to if-else statements**.
- Use classic switch with primitives when performance is critical and pattern matching switches for **readability and convenience**.

---

### Variable Scope and Usage

- The pattern variable declared in a case (like `String s` in `case String s`) is **only visible within that case block**.
- It **cannot be accessed** in:
    - Other case blocks
    - The `default` block
    - Outside the switch statement

---

### Pattern Matching with Class Hierarchies and Inheritance

- You can use pattern matching switch with **abstract classes and inheritance**.
- Example hierarchy:
    - `Vehicle` (abstract class)
    - `TwoWheeler` and `FourWheeler` as subclasses
    - `Bike` and `Cycle` as subclasses of `TwoWheeler`

- Allowed cases in a switch of type `TwoWheeler`:
    - `case Bike b -> ...` (valid)
    - `case Cycle c -> ...` (valid)
    - `case TwoWheeler t -> ...` (valid)
    - `case FourWheeler f -> ...` (invalid, since `FourWheeler` is not a subtype of `TwoWheeler`)

- You can use parent class `Vehicle` to catch all child classes (i.e., bike, cycle, etc.).
- Caution with **unconditional patterns**:
    - When including both a parent class case and a child class case that covers everything, it causes a **compile-time error: "duplicate unconditional pattern"**.
    - For example, including both `case Vehicle v` and `case TwoWheeler t` is duplicate if the input is actually a `TwoWheeler`.
    - Remove redundant or overlapping case patterns to resolve this.

---

### Pattern Matching with Enums

- Enum switching becomes simpler with pattern matching.
- Instead of checking `case Color.RED`, you can write:

  ```java
  switch (obj) {
      case Color c -> System.out.println(c.name());
      default -> System.out.println("Unknown");
  }
  ```
- The switch automatically performs an instanceof check and type cast on `obj` to `Color`.

---

### Null Safety

- Pattern matching switch is **null-safe**.
- If the object is `null`, all pattern matching cases that check `instanceof` will return `false`.
- No null pointer exceptions are thrown during type casting because the null check happens before casting.
- You can handle null values in the `default` case in switch.

---

### Grouping Patterns and Limitations

- **Grouping multiple pattern labels with variables is not allowed.**
- For example, the following is invalid:

  ```java
  case Circle c, Square s -> ...
  ```

- This is because the compiler cannot guarantee that the variable refers to a guaranteed pattern due to multiple pattern variables.
- The workaround is to write separate cases for each pattern variable.

---

### Guarded Patterns (Using `when`)

- You can add extra conditions to patterns using the `when` clause, referred to as **guarded patterns**.
- Syntax example:

  ```java
  case String s when s.contains("h") || s.contains("H") -> ...
  ```

- Semantics:
    - First does the pattern match (`instanceof String`)
    - Then checks the additional `when` condition (e.g., string contains `h`)
    - If both satisfy, the case block executes; otherwise, it falls through to other cases/default.

- Important:
    - The `when` clause works as a logical AND (`&&`), not OR.
    - Using OR conditions with pattern variables is **not supported**, neither in instance of pattern matching nor switch pattern matching.
    - This limitation is similar to the `instanceof` pattern matching restrictions.

---

### Summary Table: Switch Support Before and After Java 21

| Feature / Data Type                | Supported Before Java 21 | Supported After Java 21            |
|----------------------------------|-------------------------|----------------------------------|
| Primitive types (int, byte, etc.)| Yes                     | Yes                              |
| Wrapper classes (Integer, etc.)  | Yes                     | Yes                              |
| `enum` types                     | Yes                     | Yes                              |
| `String`                        | Yes                     | Yes                              |
| Classes (custom object types)    | No                      | Yes                              |
| Interfaces                      | No                      | Yes                              |
| Abstract classes                | No                      | Yes                              |
| Pattern variables (type casting)| No                      | Yes                              |
| Grouping multiple patterns with variables | No            | Still No                         |
| Guarded patterns (`when` clause) | No                      | Yes (with AND semantics only)    |

---

### Key Insights

- **Java 21 pattern matching for switch greatly improves expressiveness by allowing switching on object types with automatic type casting inside the case.**
- **Internally, the switch constructs transform into `instanceof`-based if-else chains when working with objects, affecting performance compared to primitive type switches.**
- **The scope of pattern variables is localized to individual case blocks to preserve clarity and prevent side effects.**
- **Inheritance and class hierarchy are handled thoughtfully, but care must be taken to avoid duplicate unconditional case patterns.**
- **Enums and traditional types still work as expected, and the syntax simplifies enum switching scenarios.**
- **Null safety is built-in, removing a common source of runtime errors for switches involving object patterns.**
- **Grouping pattern variables is prohibited; cases must be handled separately for each pattern variable.**
- **Guarded pattern matching with `when` allows expressive conditions but only supports logical AND, mirroring `instanceof` limitations.**

---

### Conclusion

Java 21's **pattern matching for switch** is a natural and powerful evolution of Java’s type-checking and branching capabilities. It harmonizes traditional switch constructs with the rich type hierarchy of Java by supporting object and class matching within switch expressions and statements. This alignment improves developer productivity by reducing boilerplate casting code and increasing code clarity. However, developers need to be mindful of performance implications when switching on complex objects and the limits of pattern grouping and guarded expressions.

This feature marks a continued effort by the Java platform to support **pattern matching** as a first-class language construct, simplifying type-safe branching and paving the way for future enhancements.

---

If further discussion or clarification is required, focusing on use cases or performance trade-offs of pattern matching for switch can be addressed in follow-up explanations.