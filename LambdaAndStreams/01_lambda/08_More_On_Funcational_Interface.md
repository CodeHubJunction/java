Got it ✅
Since you’ve already covered **Function**, **Predicate**, **Consumer**, **Supplier**, **UnaryOperator**, and **BinaryOperator**,
I’ll now give you **the remaining functional interfaces** — with **two full runnable examples for each** — in **Markdown format** so you can store it as study notes.

---

# 📘 Java 8 — Additional Functional Interfaces with Examples

## 1️⃣ **BiPredicate\<T, U>**

- **Purpose:** Takes two arguments, returns `boolean`.

```java
import java.util.function.BiPredicate;

public class BiPredicateExample {
    public static void main(String[] args) {
        // Example 1: Compare two strings ignoring case
        BiPredicate<String, String> equalsIgnoreCase = (a, b) -> a.equalsIgnoreCase(b);
        System.out.println(equalsIgnoreCase.test("java", "JAVA")); // true

        // Example 2: Check if sum of two integers is even
        BiPredicate<Integer, Integer> sumIsEven = (a, b) -> (a + b) % 2 == 0;
        System.out.println(sumIsEven.test(3, 5)); // true
    }
}
```

---

## 2️⃣ **BiConsumer\<T, U>**

- **Purpose:** Takes two arguments, returns nothing (`void`).

```java
import java.util.function.BiConsumer;
import java.util.HashMap;

public class BiConsumerExample {
    public static void main(String[] args) {
        // Example 1: Print key-value pairs
        BiConsumer<String, Integer> printMapEntry = (k, v) -> System.out.println(k + ": " + v);
        printMapEntry.accept("Age", 30);

        // Example 2: Add two numbers and print result
        BiConsumer<Integer, Integer> addAndPrint = (a, b) -> System.out.println("Sum = " + (a + b));
        addAndPrint.accept(5, 7);
    }
}
```

---

## 3️⃣ **BiFunction\<T, U, R>**

- **Purpose:** Takes two arguments, returns a result.

```java
import java.util.function.BiFunction;

public class BiFunctionExample {
    public static void main(String[] args) {
        // Example 1: Concatenate two strings
        BiFunction<String, String, String> concat = (a, b) -> a + b;
        System.out.println(concat.apply("Hello ", "World"));

        // Example 2: Multiply two integers and return as String
        BiFunction<Integer, Integer, String> multiplyToString = (a, b) -> "Result: " + (a * b);
        System.out.println(multiplyToString.apply(4, 5));
    }
}
```

---

## 4️⃣ **IntFunction<R>, LongFunction<R>, DoubleFunction<R>**

- **Purpose:** Takes a primitive (`int`, `long`, `double`) and returns a result.

```java
import java.util.function.IntFunction;
import java.util.function.LongFunction;
import java.util.function.DoubleFunction;

public class PrimitiveFunctionExample {
    public static void main(String[] args) {
        // Example 1: IntFunction - square the number and return String
        IntFunction<String> intSquare = n -> "Square: " + (n * n);
        System.out.println(intSquare.apply(5));

        // Example 2: LongFunction - convert to string with prefix
        LongFunction<String> longToStr = n -> "Long value: " + n;
        System.out.println(longToStr.apply(1000L));

        // Example 3: DoubleFunction - format double value
        DoubleFunction<String> doubleFormat = val -> String.format("%.2f", val);
        System.out.println(doubleFormat.apply(3.14159));
    }
}
```

---

## 5️⃣ **ToIntFunction<T>, ToLongFunction<T>, ToDoubleFunction<T>**

- **Purpose:** Takes an object, returns primitive int/long/double.

```java
import java.util.function.ToIntFunction;
import java.util.function.ToLongFunction;
import java.util.function.ToDoubleFunction;
import java.util.Date;

public class ToPrimitiveFunctionExample {
    public static void main(String[] args) {
        // Example 1: ToIntFunction - string length
        ToIntFunction<String> length = s -> s.length();
        System.out.println(length.applyAsInt("Lambda"));

        // Example 2: ToLongFunction - get milliseconds from Date
        ToLongFunction<Date> dateToMillis = date -> date.getTime();
        System.out.println(dateToMillis.applyAsLong(new Date(0)));

        // Example 3: ToDoubleFunction - convert Integer to sqrt
        ToDoubleFunction<Integer> sqrt = n -> Math.sqrt(n);
        System.out.println(sqrt.applyAsDouble(16));
    }
}
```

---

## 6️⃣ **ObjIntConsumer<T>, ObjLongConsumer<T>, ObjDoubleConsumer<T>**

- **Purpose:** Takes an object + a primitive, returns nothing.

```java
import java.util.function.ObjIntConsumer;
import java.util.function.ObjLongConsumer;
import java.util.function.ObjDoubleConsumer;

public class ObjPrimitiveConsumerExample {
    public static void main(String[] args) {
        // Example 1: ObjIntConsumer - repeat string
        ObjIntConsumer<String> repeat = (s, times) -> System.out.println(s.repeat(times));
        repeat.accept("Hi", 3);

        // Example 2: ObjLongConsumer - append long to string
        ObjLongConsumer<String> appendLong = (s, num) -> System.out.println(s + num);
        appendLong.accept("Value: ", 123456L);

        // Example 3: ObjDoubleConsumer - formatted output
        ObjDoubleConsumer<String> formatDouble = (fmt, val) -> System.out.printf(fmt, val);
        formatDouble.accept("Formatted value: %.2f", 3.14159);
    }
}
```

---

## 7️⃣ **IntPredicate, LongPredicate, DoublePredicate**

- **Purpose:** Takes primitive, returns boolean.

```java
import java.util.function.IntPredicate;
import java.util.function.LongPredicate;
import java.util.function.DoublePredicate;

public class PrimitivePredicateExample {
    public static void main(String[] args) {
        // Example 1: IntPredicate - is even
        IntPredicate isEven = n -> n % 2 == 0;
        System.out.println(isEven.test(10));

        // Example 2: LongPredicate - is positive
        LongPredicate isPositive = n -> n > 0;
        System.out.println(isPositive.test(-5L));

        // Example 3: DoublePredicate - is integer value
        DoublePredicate isWhole = val -> val % 1 == 0;
        System.out.println(isWhole.test(5.0));
    }
}
```

---

## 8️⃣ **IntSupplier, LongSupplier, DoubleSupplier**

- **Purpose:** Takes no input, returns primitive.

```java
import java.util.function.IntSupplier;
import java.util.function.LongSupplier;
import java.util.function.DoubleSupplier;

public class PrimitiveSupplierExample {
    public static void main(String[] args) {
        // Example 1: IntSupplier - return constant
        IntSupplier getTen = () -> 10;
        System.out.println(getTen.getAsInt());

        // Example 2: LongSupplier - return current time
        LongSupplier currentTime = () -> System.currentTimeMillis();
        System.out.println(currentTime.getAsLong());

        // Example 3: DoubleSupplier - return PI
        DoubleSupplier piValue = () -> Math.PI;
        System.out.println(piValue.getAsDouble());
    }
}
```

---

## 🔟 **IntUnaryOperator, LongUnaryOperator, DoubleUnaryOperator**

- **Purpose:** Takes primitive, returns primitive of same type.

```java
import java.util.function.IntUnaryOperator;
import java.util.function.LongUnaryOperator;
import java.util.function.DoubleUnaryOperator;

public class PrimitiveUnaryOperatorExample {
    public static void main(String[] args) {
        // Example 1: IntUnaryOperator - increment
        IntUnaryOperator increment = n -> n + 1;
        System.out.println(increment.applyAsInt(5));

        // Example 2: DoubleUnaryOperator - halve the value
        DoubleUnaryOperator halve = val -> val / 2;
        System.out.println(halve.applyAsDouble(8.0));

        // Example 3: LongUnaryOperator - square
        LongUnaryOperator square = n -> n * n;
        System.out.println(square.applyAsLong(12L));
    }
}
```

---

## 1️⃣1️⃣ **IntBinaryOperator, LongBinaryOperator, DoubleBinaryOperator**

- **Purpose:** Takes two primitives, returns same type.

```java
import java.util.function.IntBinaryOperator;
import java.util.function.LongBinaryOperator;
import java.util.function.DoubleBinaryOperator;

public class PrimitiveBinaryOperatorExample {
    public static void main(String[] args) {
        // Example 1: IntBinaryOperator - sum
        IntBinaryOperator sum = (a, b) -> a + b;
        System.out.println(sum.applyAsInt(5, 7));

        // Example 2: DoubleBinaryOperator - multiply
        DoubleBinaryOperator multiply = (a, b) -> a * b;
        System.out.println(multiply.applyAsDouble(3.5, 2.0));

        // Example 3: LongBinaryOperator - max
        LongBinaryOperator max = (a, b) -> a > b ? a : b;
        System.out.println(max.applyAsLong(100L, 200L));
    }
}
```

---

✅ **This markdown now covers:**

- BiPredicate
- BiConsumer
- BiFunction
- All primitive specializations: `IntFunction`, `ToIntFunction`, `ObjIntConsumer`, `IntPredicate`, `IntSupplier`, `IntUnaryOperator`, `IntBinaryOperator` and their `long` & `double` counterparts.

---

If you want, I can also prepare **one combined Java file** with **all these examples in sequence** so you can run them in one go without copy-pasting.
Do you want me to prepare that combined runnable file?
