## Method Reference Types in Java 8

Method references are a shorthand for writing lambdas that just call an existing method.

---

### **2.1 ClassName::staticMethod**

- Refers to a **static method**.
- **Example:** `Integer::parseInt`

```java
import java.util.function.Function;

public class StaticMethodRefExample {
    public static void main(String[] args) {
        Function<String, Integer> parser = Integer::parseInt; // static method reference
        System.out.println(parser.apply("123")); // Output: 123
    }
}
```

---

### **2.2 instanceRef::instanceMethod**

- Refers to an **instance method** on a specific object.
- **Example:** `printer::println`

```java
import java.util.function.Consumer;

public class InstanceRefExample {
    public static void main(String[] args) {
        Consumer<String> printer = System.out::println; // instance method of an object
        printer.accept("Hello from instanceRef::instanceMethod!");
    }
}
```

---

### **2.3 ClassName::instanceMethod**

(receiver becomes the first parameter in the lambda)

- Refers to an **instance method** of a class, without binding it to a specific instance.
- First parameter is the receiver.
- **Example:** `String::toUpperCase`

```java
import java.util.function.Function;

public class ClassInstanceMethodExample {
    public static void main(String[] args) {
        Function<String, String> upperCaseFunc = String::toUpperCase;
        System.out.println(upperCaseFunc.apply("lambda")); // Output: LAMBDA
    }
}
```

---

### **2.4 Constructor Reference**

- Refers to a **constructor**.
- **Example:** `ArrayList::new`, `String[]::new`

```java
import java.util.function.Supplier;
import java.util.function.Function;
import java.util.List;
import java.util.ArrayList;

public class ConstructorRefExample {
    public static void main(String[] args) {
        // No-args constructor
        Supplier<List<String>> listSupplier = ArrayList::new;
        List<String> list = listSupplier.get();
        list.add("Hello");
        System.out.println(list);

        // Array constructor
        Function<Integer, String[]> arrayCreator = String[]::new;
        String[] arr = arrayCreator.apply(3);
        arr[0] = "A";
        arr[1] = "B";
        arr[2] = "C";
        System.out.println(String.join(",", arr));
    }
}
```

---

## 📌 Summary Table

| Method Reference Type             | Example               | Equivalent Lambda              | When to Use                     |
| --------------------------------- | --------------------- | ------------------------------ | ------------------------------- |
| `ClassName::staticMethod`         | `Integer::parseInt`   | `(s) -> Integer.parseInt(s)`   | Calling static methods directly |
| `instanceRef::instanceMethod`     | `System.out::println` | `(x) -> System.out.println(x)` | Bound instance method calls     |
| `ClassName::instanceMethod`       | `String::toUpperCase` | `(str) -> str.toUpperCase()`   | Unbound instance method calls   |
| `ClassName::new` (constructor)    | `ArrayList::new`      | `() -> new ArrayList<>()`      | Creating new instances          |
| `Type[]::new` (array constructor) | `String[]::new`       | `(size) -> new String[size]`   | Creating arrays dynamically     |

---

If you want, I can now prepare **one big Java file** that includes **all these method reference examples + LongFunction example** so you can run it in one go without copy-pasting multiple files.
Do you want me to do that? That way, you’ll have **one-stop runnable learning file**.
