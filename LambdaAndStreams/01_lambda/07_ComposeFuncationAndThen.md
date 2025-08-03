The `compose` function in Java’s `Function` (and also in `UnaryOperator`) is used to **combine two functions** —
but it applies them **in reverse order** compared to `andThen`.

---

## **Definition**

For `Function<T, R>`:

```java
default <V> Function<V, R> compose(Function<? super V, ? extends T> before)
```

- Takes another function (`before`) that runs **first**.
- Then applies the current function **after** that.
- Think of it as:
  **`thisFunction.compose(before)` = `thisFunction(before(input))`**

---

## **Visual difference**

| Method      | Order of execution |
| ----------- | ------------------ |
| **compose** | `before → current` |
| **andThen** | `current → after`  |

---

## **Example**

```java
import java.util.function.Function;

public class ComposeDemo {
    public static void main(String[] args) {
        Function<Integer, Integer> square = x -> x * x;
        Function<Integer, Integer> doubleIt = x -> x * 2;

        // compose: double first, then square
        Function<Integer, Integer> squareAfterDouble = square.compose(doubleIt);
        System.out.println(squareAfterDouble.apply(3)); // double(3)=6 → square(6)=36

        // andThen: square first, then double
        Function<Integer, Integer> doubleAfterSquare = square.andThen(doubleIt);
        System.out.println(doubleAfterSquare.apply(3)); // square(3)=9 → double(9)=18
    }
}
```

---

✅ **Rule of thumb to remember**:

- **compose** = “**before** me” → runs the _argument function first_.
- **andThen** = “**after** me” → runs the _argument function afterwards_.

---

If you want, I can make a **diagram** for `compose` vs `andThen` so you can visualize the flow and never confuse them again.
Do you want me to prepare that?

---

## Checked exceptions with lambdas: options

Most JDK functional interfaces **don’t declare checked exceptions**, so this fails:

```java
// Fails: Function<T,R> has no 'throws IOException'
java.util.function.Function<String, String> f = path -> java.nio.file.Files.readString(java.nio.file.Path.of(path));
```

**Options:**

1. **Handle inside the lambda** (try/catch and wrap to unchecked):

   ```java
   Function<String, String> safeRead = path -> {
       try {
           return java.nio.file.Files.readString(java.nio.file.Path.of(path));
       } catch (java.io.IOException e) {
           throw new RuntimeException(e); // wrap
       }
   };
   ```

2. **Define a custom functional interface** that declares `throws E`.

   ```java
   @FunctionalInterface
   interface ThrowingFunction<T, R, E extends Exception> {
       R apply(T t) throws E;
   }
   ```

3. **Write adapter helpers** that convert throwing lambdas into standard ones by wrapping exceptions.

> As a beginner, prefer option **1** for simplicity; graduate to adapters/custom interfaces as your codebase grows.

---

## Best practices & common pitfalls

**Best practices**

- Prefer **stateless** lambdas (no external mutable state).
- Use **method references** when they make code shorter **and** clearer.
- Use **primitive specializations** (`IntPredicate`, etc.) in performance‑sensitive areas.
- For transformations, favor **`map`/`filter`/`reduce`** (pure functions) over side‑effecting `forEach`.

**Pitfalls**

- **Effective finality:** locals captured in lambdas must be final/effectively final (no reassignment).
- **Ambiguous overloads:** if two overloads take different functional interfaces with compatible shapes, the compiler may be confused—add an **explicit type** or **cast**.
- **Checked exceptions:** JDK interfaces don’t declare them—handle or wrap.
- **Parallel streams + side effects:** avoid mutating shared state inside `forEach` on parallel streams—use collectors/reducers instead.

---

## Mini practice

1. **Filter with `Predicate`:**
   Create a `Predicate<String>` for “non‑empty and length ≤ 3”, then filter `["", "a", "go", "java"]` → result `["a", "go"]`.

2. **Map with `Function`:**
   Use `Function<String,Integer>` (length) and `andThen` to format as `"len=N"`.

3. **Reduce with `BinaryOperator`:**
   Sum a list of integers using `reduce(0, Integer::sum)`.

4. **Create and use a `TriFunction`:**
   Define `TriFunction<Double, Double, Double, Double>` that computes the average of three numbers.

---

### A tiny, end‑to‑end example using all six

```java
import java.util.*;
import java.util.function.*;

public class Demo {
    public static void main(String[] args) {
        // Supplier: start with some data
        Supplier<List<String>> namesSupplier = () -> new ArrayList<>(List.of("  alice", "", "Bob ", " cara  "));

        // Predicate: keep non-empty after trimming
        Predicate<String> nonEmptyAfterTrim = s -> !s.trim().isEmpty();

        // UnaryOperator: normalize (trim + capitalize)
        UnaryOperator<String> normalize = s -> {
            String t = s.trim();
            return t.isEmpty() ? t : Character.toUpperCase(t.charAt(0)) + t.substring(1).toLowerCase();
        };

        // Function: transform string -> "Name: <value>"
        Function<String, String> toLabel = v -> "Name: " + v;

        // Consumer: print each
        Consumer<String> print = System.out::println;

        // BinaryOperator: pick shorter name (demo only)
        BinaryOperator<String> shorter = (a, b) -> a.length() <= b.length() ? a : b;

        List<String> names = namesSupplier.get();
        List<String> cleaned = names.stream()
                .filter(nonEmptyAfterTrim)
                .map(normalize.andThen(toLabel)) // compose operations
                .toList();

        cleaned.forEach(print);

        // Reduce to the shortest labeled name
        cleaned.stream().reduce(shorter).ifPresent(s -> System.out.println("Shortest: " + s));
    }
}
```

---

If you’d like, share a small snippet you’re writing—I can point out **which** functional interface fits best and how to make it more idiomatic.
