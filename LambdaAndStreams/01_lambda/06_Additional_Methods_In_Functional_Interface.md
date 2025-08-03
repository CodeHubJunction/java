## At-a-glance table of additional methods

> **Legend:** > **D** = default method, **S** = static method

| Functional interface                                | Additional methods (beyond the SAM)                                                                              |
| --------------------------------------------------- | ---------------------------------------------------------------------------------------------------------------- |
| **Predicate<T>**                                    | **D** `and(Predicate)`, **D** `or(Predicate)`, **D** `negate()`; **S** `isEqual(Object)`, **S** `not(Predicate)` |
| **Function\<T,R>**                                  | **D** `andThen(Function)`, **D** `compose(Function)`; **S** `identity()`                                         |
| **Supplier<T>**                                     | _(none)_ — only `T get()`                                                                                        |
| **Consumer<T>**                                     | **D** `andThen(Consumer)`                                                                                        |
| **UnaryOperator<T>** (extends `Function<T,T>`)      | **D** `andThen(Function)`, **D** `compose(Function)`; **S** `identity()`                                         |
| **BinaryOperator<T>** (extends `BiFunction<T,T,T>`) | **D** `andThen(Function)`; **S** `minBy(Comparator)`, **S** `maxBy(Comparator)`                                  |

> Notes
> • `Predicate.not(...)` was added in newer Java (Java 11+).
> • `UnaryOperator.identity()` is a typed identity convenient over `Function.identity()`.

---

## 2) Complete examples

### A) `Predicate<T>` — `and`, `or`, `negate`, `isEqual`, `not`

```java
import java.util.function.Predicate;

public class PredicateDemo {
    public static void main(String[] args) {
        Predicate<String> nonNull = s -> s != null;
        Predicate<String> nonEmpty = s -> !s.isEmpty();
        Predicate<String> longEnough = s -> s.length() >= 5;

        // and / or / negate
        Predicate<String> good = nonNull.and(nonEmpty).and(longEnough);
        Predicate<String> shortOrEmpty = nonEmpty.negate().or(longEnough.negate());

        System.out.println(good.test("hello"));    // true
        System.out.println(good.test("hi"));       // false
        System.out.println(shortOrEmpty.test("")); // true

        // isEqual
        Predicate<String> isHello = Predicate.isEqual("hello");
        System.out.println(isHello.test("hello")); // true
        System.out.println(isHello.test("hey"));   // false

        // not (Java 11+): invert another predicate (often with method refs)
        Predicate<String> notBlank = Predicate.not(String::isBlank);
        System.out.println(notBlank.test("  "));   // false
        System.out.println(notBlank.test(" ok ")); // true
    }
}
```

---

### B) `Function<T,R>` — `compose`, `andThen`, `identity`

```java
import java.util.function.Function;

public class FunctionDemo {
    public static void main(String[] args) {
        Function<String, Integer> length = String::length;
        Function<Integer, Integer> square = x -> x * x;
        Function<String, Integer> lengthThenSquare = length.andThen(square); // (after)
        Function<String, Integer> squareOfParsed = square.compose(Integer::parseInt); // (before)

        System.out.println(lengthThenSquare.apply("hello")); // 25
        System.out.println(squareOfParsed.apply("7"));       // 49

        // identity
        Function<String, String> id = Function.identity();
        System.out.println(id.apply("unchanged")); // unchanged
    }
}
```

---

### C) `Supplier<T>` — _(no extra chaining methods)_

> `Supplier` only has `T get()`. If you want a transformed supplier, wrap with a `Function` yourself.

```java
import java.util.UUID;
import java.util.function.Function;
import java.util.function.Supplier;

public class SupplierDemo {
    public static void main(String[] args) {
        Supplier<String> uuid = () -> UUID.randomUUID().toString();

        // Manually compose: create a supplier that formats the original supplier’s value
        Function<String, String> shorten = s -> s.substring(0, 8);
        Supplier<String> shortUuid = () -> shorten.apply(uuid.get());

        System.out.println(uuid.get());     // full UUID
        System.out.println(shortUuid.get()); // first 8 chars
    }
}
```

---

### D) `Consumer<T>` — `andThen`

```java
import java.util.function.Consumer;

public class ConsumerDemo {
    public static void main(String[] args) {
        Consumer<String> print = System.out::println;
        Consumer<String> shout = s -> System.out.println(s.toUpperCase());

        Consumer<String> printThenShout = print.andThen(shout);

        printThenShout.accept("hello"); // prints "hello" then "HELLO"
    }
}
```

---

### E) `UnaryOperator<T>` — `compose`, `andThen`, `identity`

```java
import java.util.function.UnaryOperator;

public class UnaryOperatorDemo {
    public static void main(String[] args) {
        UnaryOperator<Integer> inc = x -> x + 1;
        UnaryOperator<Integer> dbl = x -> x * 2;

        UnaryOperator<Integer> incThenDbl = inc.andThen(dbl);      // (x + 1) * 2
        UnaryOperator<Integer> dblThenInc = inc.compose(dbl);      // (x * 2) + 1

        System.out.println(incThenDbl.apply(3)); // 8
        System.out.println(dblThenInc.apply(3)); // 7

        // identity
        UnaryOperator<String> keep = UnaryOperator.identity();
        System.out.println(keep.apply("same"));  // same
    }
}
```

---

### F) `BinaryOperator<T>` — `andThen`, `minBy`, `maxBy`

```java
import java.util.Comparator;
import java.util.function.BinaryOperator;
import java.util.function.Function;

public class BinaryOperatorDemo {
    public static void main(String[] args) {
        BinaryOperator<Integer> sum = Integer::sum;

        // andThen: post-process the result of the binary op
        Function<Integer, String> toLabel = n -> "Sum=" + n;
        var sumThenLabel = sum.andThen(toLabel);

        System.out.println(sumThenLabel.apply(4, 5)); // "Sum=9"

        // minBy / maxBy
        Comparator<String> byLength = Comparator.comparingInt(String::length);

        BinaryOperator<String> shorter = BinaryOperator.minBy(byLength);
        BinaryOperator<String> longer  = BinaryOperator.maxBy(byLength);

        System.out.println(shorter.apply("cat", "tiger")); // "cat"
        System.out.println(longer.apply("cat", "tiger"));  // "tiger"
    }
}
```

---

## 3) Quick memory cues

- **Predicate**: logic combinators — `and`, `or`, `negate`, plus `isEqual`, `not`.
- **Function**: flow control — `compose` (before), `andThen` (after), `identity`.
- **Supplier**: source only — _(no chainers)_.
- **Consumer**: sink with chaining — `andThen`.
- **UnaryOperator**: `Function<T,T>` — inherits `compose/andThen`, has `identity`.
- **BinaryOperator**: `BiFunction<T,T,T>` — `andThen` + `minBy`/`maxBy`.
