## Custom functional interfaces with `@FunctionalInterface`

Use this when the JDK doesn’t have what you need (e.g., **three arguments**).

```java
@FunctionalInterface
interface TriFunction<A, B, C, R> {
    R apply(A a, B b, C c); // one abstract method
    // default or static helpers are allowed
    default <V> TriFunction<A, B, C, V> andThen(java.util.function.Function<? super R, ? extends V> f) {
        return (a, b, c) -> f.apply(apply(a, b, c));
    }
}

TriFunction<Integer, Integer, Integer, Integer> sum3 = (x, y, z) -> x + y + z;
System.out.println(sum3.apply(1, 2, 3)); // 6
```

**Rules to remember**

- Exactly **one** abstract method.
- `default` and `static` methods are okay.
- Methods from `Object` **don’t count** toward the abstract method limit.
- The annotation is **not required**, but adds **compile‑time safety**.

---
