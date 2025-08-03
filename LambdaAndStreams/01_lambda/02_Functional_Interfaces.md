# Java 8 Lambdas — Functional Interfaces

> **Goal:** Understand what functional interfaces are, how to use the JDK’s built‑ins (`Predicate`, `Consumer`, `Supplier`, `Function`, `UnaryOperator`, `BinaryOperator`), and how to define your own with `@FunctionalInterface`. This guide is beginner‑friendly with small, annotated examples.

---

## What is a Functional Interface (SAM type)?

A **functional interface** is an interface that declares **exactly one abstract method** (a _Single Abstract Method_, often called a **SAM**).

- It **may** contain default methods, static methods, and methods from `Object` (`toString`, `equals`, `hashCode`)—**these don’t count** toward the single abstract method.
- Lambdas and method references can be assigned to variables of a functional interface type.

```java
@FunctionalInterface
interface Greeting {
    void say(String name);  // exactly one abstract method
    // default/static methods are allowed
    default void info() { System.out.println("A greeting interface"); }
    static Greeting hi() { return n -> System.out.println("Hi " + n); }
}
```

> `@FunctionalInterface` is **optional** but recommended: the compiler will enforce the “one abstract method” rule and produce a helpful error if you accidentally add another.

---

## Why do lambdas need functional interfaces?

A lambda has no type by itself; it gets a type from the **target context** (assignment, parameter, return).
That target must be a **functional interface** (one abstract method) so the compiler knows which method your lambda implements.

```java
// Target typing: the compiler knows `() -> ...` implements Runnable#run()
Runnable r = () -> System.out.println("Hello");
```

---

## Built‑in functional interfaces: quick overview

All below are in `java.util.function` unless noted.

| Interface           | Abstract method       | Typical use                                               |
| ------------------- | --------------------- | --------------------------------------------------------- |
| `Predicate<T>`      | `boolean test(T t)`   | Filtering and conditions (`filter`, `removeIf`)           |
| `Consumer<T>`       | `void accept(T t)`    | Do something with a value (logging, printing, collecting) |
| `Supplier<T>`       | `T get()`             | Provide a value on demand (factories, lazy init)          |
| `Function<T,R>`     | `R apply(T t)`        | Transform a value (map one type to another)               |
| `UnaryOperator<T>`  | `T apply(T t)`        | Transform **T → T** (e.g., sanitize string)               |
| `BinaryOperator<T>` | `T apply(T t1, T t2)` | Combine two **T** values into one (reduce/fold)           |

> You’ll also meet **Bi** variants (`BiPredicate`, `BiConsumer`, `BiFunction`) and many **primitive** specializations (covered later).

---

## `Predicate<T>`

**Definition:** a boolean‑returning test.

- **Method:** `boolean test(T t)`
- **Combinators:** `and`, `or`, `negate`
- **Where used:** `Stream.filter`, `Collection.removeIf`, `Optional.filter`

```java
import java.util.*;
import java.util.function.Predicate;

List<String> words = Arrays.asList("alpha", "", "beta", "gamma", "delta");

Predicate<String> nonEmpty = s -> !s.isEmpty();     // simple condition
Predicate<String> longWord = s -> s.length() >= 5;  // another condition

// Combine: non-empty AND long
List<String> filtered = words.stream()
    .filter(nonEmpty.and(longWord))  // same as: s -> !s.isEmpty() && s.length() >= 5
    .toList();

System.out.println(filtered); // [alpha, gamma, delta]

// Negation
List<String> empties = words.stream().filter(nonEmpty.negate()).toList();
System.out.println(empties); // [""]
```

**BiPredicate example** (two inputs, boolean output):

```java
import java.util.function.BiPredicate;

BiPredicate<String, Integer> longerThan = (s, n) -> s.length() > n;
System.out.println(longerThan.test("hello", 3)); // true
```

---

## `Consumer<T>`

**Definition:** performs an action, returns **void**.

- **Method:** `void accept(T t)`
- **Combinator:** `andThen` (chain side effects)
- **Where used:** `Stream.forEach`, `Iterable.forEach`, logging

```java
import java.util.*;
import java.util.function.Consumer;

List<String> names = Arrays.asList("Ava", "Ben", "Cara");
Consumer<String> print = System.out::println; // method reference
Consumer<String> exclaim = s -> System.out.println(s + "!");
names.forEach(print.andThen(exclaim));
```

**BiConsumer example** (e.g., iterating a `Map`):

```java
import java.util.function.BiConsumer;

Map<String, Integer> ages = Map.of("Ava", 18, "Ben", 20);
BiConsumer<String, Integer> printer = (k, v) -> System.out.println(k + " -> " + v);
ages.forEach(printer); // Map#forEach takes a BiConsumer
```

---

## `Supplier<T>`

**Definition:** provides a value when asked.

- **Method:** `T get()`
- **Where used:** lazy evaluation, factories, `Optional.orElseGet`

```java
import java.util.function.Supplier;

Supplier<Double> random = Math::random; // produces a new random double each call
System.out.println(random.get());
System.out.println(random.get());
```

**With Optional:**

```java
String value = java.util.Optional.<String>empty()
        .orElseGet(() -> "default value"); // Supplier supplies fallback lazily
```

---

## `Function<T, R>`

**Definition:** transform input `T` to output `R`.

- **Method:** `R apply(T t)`
- **Combinators:** `andThen`, `compose`, `identity()`

  - `f.andThen(g)` means **g(f(x))**
  - `f.compose(g)` means **f(g(x))**

```java
import java.util.function.Function;

Function<String, Integer> length = String::length;     // "abc" -> 3
Function<Integer, String> describe = n -> "len=" + n;

Function<String, String> pipeline = length.andThen(describe);
System.out.println(pipeline.apply("hello")); // "len=5"

Function<String, String> pipeline2 = describe.compose(length); // same result
System.out.println(Function.identity().apply("same")); // "same"
```

**BiFunction example:**

```java
import java.util.function.BiFunction;

BiFunction<Integer, Integer, String> sumToStr = (a, b) -> "sum=" + (a + b);
System.out.println(sumToStr.apply(2, 3)); // "sum=5"
```

---

## `UnaryOperator<T>` and `BinaryOperator<T>`

These are special cases of `Function`:

- `UnaryOperator<T>` is a `Function<T,T>`
- `BinaryOperator<T>` is a `BiFunction<T,T,T>`

**Use them when the input and output types are the same** — it reads better.

```java
import java.util.function.UnaryOperator;

UnaryOperator<String> trimAndUpper =
        s -> s.trim().toUpperCase(); // T -> T
System.out.println(trimAndUpper.apply("  hello  ")); // "HELLO"
```

```java
import java.util.Comparator;
import java.util.function.BinaryOperator;

BinaryOperator<Integer> add = (a, b) -> a + b; // T,T -> T
System.out.println(add.apply(3, 4)); // 7

// minBy / maxBy with a Comparator
BinaryOperator<String> shortest =
        BinaryOperator.minBy(Comparator.comparingInt(String::length));
System.out.println(shortest.apply("alpha", "beta")); // "beta"
```

**In reductions:**

```java
int total = java.util.stream.Stream.of(1, 2, 3, 4)
        .reduce(0, Integer::sum); // BinaryOperator<Integer>
System.out.println(total); // 10
```

---
