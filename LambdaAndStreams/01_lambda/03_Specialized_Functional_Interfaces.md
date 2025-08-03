# Java 8 Lambdas — Functional Interfaces

## Primitive specializations (avoid boxing overhead)

To reduce autoboxing of `int`, `long`, `double`, Java provides many specialized functional interfaces:

| Concept                | `int`               | `long`               | `double`               |
| ---------------------- | ------------------- | -------------------- | ---------------------- |
| Predicate              | `IntPredicate`      | `LongPredicate`      | `DoublePredicate`      |
| Consumer               | `IntConsumer`       | `LongConsumer`       | `DoubleConsumer`       |
| Supplier               | `IntSupplier`       | `LongSupplier`       | `DoubleSupplier`       |
| Function `T→primitive` | `ToIntFunction<T>`  | `ToLongFunction<T>`  | `ToDoubleFunction<T>`  |
| Function `primitive→R` | `IntFunction<R>`    | `LongFunction<R>`    | `DoubleFunction<R>`    |
| UnaryOperator          | `IntUnaryOperator`  | `LongUnaryOperator`  | `DoubleUnaryOperator`  |
| BinaryOperator         | `IntBinaryOperator` | `LongBinaryOperator` | `DoubleBinaryOperator` |
| ObjXConsumer           | `ObjIntConsumer<T>` | `ObjLongConsumer<T>` | `ObjDoubleConsumer<T>` |

> Tip: Prefer these in **hot loops** or performance‑sensitive code to avoid boxing (e.g., `IntStream`, `IntUnaryOperator`).

---

## 1️⃣ LongFunction<R> vs Function\<Long, R>

**Definition:**

- `Function<Long, R>`: A general-purpose function that takes a **boxed Long** and returns a value of type `R`. This causes **autoboxing/unboxing** if you use primitives.
- `LongFunction<R>`: A specialized version for `long` primitive input, **avoids boxing overhead**.

**Example:**

```java
import java.util.function.Function;
import java.util.function.LongFunction;

public class LongFunctionVsFunction {
    public static void main(String[] args) {
        // Function<Long, String> - uses boxed Long
        Function<Long, String> function = (Long val) -> "Function Value: " + (val * 2);
        System.out.println(function.apply(10L)); // Autoboxing occurs

        // LongFunction<String> - primitive long
        LongFunction<String> longFunction = (long val) -> "LongFunction Value: " + (val * 2);
        System.out.println(longFunction.apply(10)); // No boxing
    }
}
```

**Key Difference:**

- `Function<Long, R>`: Works with **wrapper class Long**
- `LongFunction<R>`: Works directly with **primitive long**
- Performance gain when processing large streams of primitives.

---

```java
import java.util.function.*;
import java.util.*;

public class FunctionalInterfaceExamples {

    public static void main(String[] args) {
        // 1. Function
        functionExample();

        // 2. Predicate
        predicateExample();

        // 3. Consumer
        consumerExample();

        // 4. Supplier
        supplierExample();

        // 5. BiFunction
        biFunctionExample();

        // 6. BiPredicate
        biPredicateExample();

        // 7. BiConsumer
        biConsumerExample();

        // 8. Primitive Specializations
        primitivePredicateExample();
        primitiveConsumerExample();
        primitiveSupplierExample();
        toPrimitiveFunctionExample();
        primitiveToObjectFunctionExample();
        primitiveUnaryOperatorExample();
        primitiveBinaryOperatorExample();
        objPrimitiveConsumerExample();
    }

    // ------------------------
    // Function<T,R>
    // ------------------------
    static void functionExample() {
        Function<String, Integer> lengthFunc = (str) -> str.length();
        System.out.println("Function Example: " + lengthFunc.apply("Lambda")); // Output: 6
    }

    // ------------------------
    // Predicate<T>
    // ------------------------
    static void predicateExample() {
        Predicate<Integer> isPositive = num -> num > 0;
        System.out.println("Predicate Example: " + isPositive.test(5));  // true
        System.out.println("Predicate Example: " + isPositive.test(-1)); // false
    }

    // ------------------------
    // Consumer<T>
    // ------------------------
    static void consumerExample() {
        Consumer<String> printConsumer = str -> System.out.println("Consumer Example: " + str);
        printConsumer.accept("Hello Java!");
    }

    // ------------------------
    // Supplier<T>
    // ------------------------
    static void supplierExample() {
        Supplier<Long> timestampSupplier = () -> System.currentTimeMillis();
        System.out.println("Supplier Example: " + timestampSupplier.get());
    }

    // ------------------------
    // BiFunction<T,U,R>
    // ------------------------
    static void biFunctionExample() {
        BiFunction<String, String, String> concatFunction = (s1, s2) -> s1 + s2;
        System.out.println("BiFunction Example: " + concatFunction.apply("Hello, ", "World!"));
    }

    // ------------------------
    // BiPredicate<T,U>
    // ------------------------
    static void biPredicateExample() {
        BiPredicate<String, String> areEqual = (a, b) -> a.equalsIgnoreCase(b);
        System.out.println("BiPredicate Example: " + areEqual.test("Java", "java"));
    }

    // ------------------------
    // BiConsumer<T,U>
    // ------------------------
    static void biConsumerExample() {
        BiConsumer<String, Integer> printKeyValue = (key, value) ->
                System.out.println("BiConsumer Example: " + key + " = " + value);
        printKeyValue.accept("Age", 25);
    }

    // ------------------------
    // IntPredicate, LongPredicate, DoublePredicate
    // ------------------------
    static void primitivePredicateExample() {
        IntPredicate isEven = num -> num % 2 == 0;
        LongPredicate isPositiveLong = val -> val > 0;
        DoublePredicate isWhole = val -> val % 1 == 0;

        System.out.println("IntPredicate: " + isEven.test(10));       // true
        System.out.println("LongPredicate: " + isPositiveLong.test(5L)); // true
        System.out.println("DoublePredicate: " + isWhole.test(5.0));  // true
    }

    // ------------------------
    // IntConsumer, LongConsumer, DoubleConsumer
    // ------------------------
    static void primitiveConsumerExample() {
        IntConsumer doubleIt = val -> System.out.println("IntConsumer: " + (val * 2));
        LongConsumer halfIt = val -> System.out.println("LongConsumer: " + (val / 2));
        DoubleConsumer squareIt = val -> System.out.println("DoubleConsumer: " + (val * val));

        doubleIt.accept(5);   // 10
        halfIt.accept(100L);  // 50
        squareIt.accept(4.0); // 16.0
    }

    // ------------------------
    // IntSupplier, LongSupplier, DoubleSupplier
    // ------------------------
    static void primitiveSupplierExample() {
        IntSupplier intSup = () -> 42;
        LongSupplier longSup = () -> 1_000_000L;
        DoubleSupplier doubleSup = () -> Math.PI;

        System.out.println("IntSupplier: " + intSup.getAsInt());
        System.out.println("LongSupplier: " + longSup.getAsLong());
        System.out.println("DoubleSupplier: " + doubleSup.getAsDouble());
    }

    // ------------------------
    // ToIntFunction<T>, ToLongFunction<T>, ToDoubleFunction<T>
    // ------------------------
    static void toPrimitiveFunctionExample() {
        ToIntFunction<String> stringLength = s -> s.length();
        ToLongFunction<Date> dateToMillis = date -> date.getTime();
        ToDoubleFunction<Integer> intToSqrt = num -> Math.sqrt(num);

        System.out.println("ToIntFunction: " + stringLength.applyAsInt("Hello"));
        System.out.println("ToLongFunction: " + dateToMillis.applyAsLong(new Date(0)));
        System.out.println("ToDoubleFunction: " + intToSqrt.applyAsDouble(16));
    }

    // ------------------------
    // IntFunction<R>, LongFunction<R>, DoubleFunction<R>
    // ------------------------
    static void primitiveToObjectFunctionExample() {
        IntFunction<String> intToString = num -> "Number: " + num;
        LongFunction<String> longToString = num -> "Long value: " + num;
        DoubleFunction<String> doubleToString = num -> "Double value: " + num;

        System.out.println(intToString.apply(7));
        System.out.println(longToString.apply(123456789L));
        System.out.println(doubleToString.apply(3.14));
    }

    // ------------------------
    // IntUnaryOperator, LongUnaryOperator, DoubleUnaryOperator
    // ------------------------
    static void primitiveUnaryOperatorExample() {
        IntUnaryOperator square = n -> n * n;
        LongUnaryOperator increment = n -> n + 1;
        DoubleUnaryOperator half = n -> n / 2;

        System.out.println("IntUnaryOperator: " + square.applyAsInt(4));
        System.out.println("LongUnaryOperator: " + increment.applyAsLong(10L));
        System.out.println("DoubleUnaryOperator: " + half.applyAsDouble(8.0));
    }

    // ------------------------
    // IntBinaryOperator, LongBinaryOperator, DoubleBinaryOperator
    // ------------------------
    static void primitiveBinaryOperatorExample() {
        IntBinaryOperator addInts = (a, b) -> a + b;
        LongBinaryOperator multiplyLongs = (a, b) -> a * b;
        DoubleBinaryOperator divideDoubles = (a, b) -> a / b;

        System.out.println("IntBinaryOperator: " + addInts.applyAsInt(5, 10));
        System.out.println("LongBinaryOperator: " + multiplyLongs.applyAsLong(2L, 3L));
        System.out.println("DoubleBinaryOperator: " + divideDoubles.applyAsDouble(10.0, 2.0));
    }

    // ------------------------
    // ObjIntConsumer<T>, ObjLongConsumer<T>, ObjDoubleConsumer<T>
    // ------------------------
    static void objPrimitiveConsumerExample() {
        ObjIntConsumer<String> repeatString = (str, times) ->
                System.out.println("ObjIntConsumer: " + str.repeat(times));

        ObjLongConsumer<String> appendLong = (str, val) ->
                System.out.println("ObjLongConsumer: " + str + val);

        ObjDoubleConsumer<String> formatDouble = (str, val) ->
                System.out.println("ObjDoubleConsumer: " + String.format(str, val));

        repeatString.accept("Hi", 3); // HiHiHi
        appendLong.accept("Value: ", 12345L);
        formatDouble.accept("Formatted value: %.2f", 3.14159);
    }
}

```
