# Scope, Capture, and `this` in Java Lambdas

## 1. **Effectively Final Variables**

### **Definition**

- Lambdas can access variables from the enclosing scope **only if they are _effectively final_**.
- **Effectively final** means:

  - Not declared `final`, but its value never changes after initialization.

### **Example: Effectively Final**

```java
public class EffectivelyFinalExample {
    public static void main(String[] args) {
        String greeting = "Hello"; // effectively final

        Runnable r = () -> System.out.println(greeting); // OK

        r.run();

        // Uncommenting below will cause compile error:
        // greeting = "Hi";
        // Runnable r2 = () -> System.out.println(greeting);
    }
}
```

**Why restriction?**

- Lambdas **capture values**, not variables.
- Prevents unexpected changes (especially in multithreaded code).

---

## 2. **Differences Between Lambdas and Anonymous Classes**

### **2.1 `this` Keyword**

```java
public class ThisKeywordExample {
    String name = "Outer";

    void test() {
        Runnable lambda = () -> System.out.println("Lambda this.name: " + this.name);

        Runnable anon = new Runnable() {
            String name = "Inner";
            @Override
            public void run() {
                System.out.println("Anonymous this.name: " + this.name);
            }
        };

        lambda.run(); // Refers to outer instance
        anon.run();   // Refers to anonymous class instance
    }

    public static void main(String[] args) {
        new ThisKeywordExample().test();
    }
}
```

**Output:**

```
Lambda this.name: Outer
Anonymous this.name: Inner
```

---

### **2.2 Variable Shadowing**

```java
public class ShadowingExample {
    public static void main(String[] args) {
        String msg = "Hello";

        Runnable lambda = () -> {
            // String msg = "Hi"; // ❌ Compile error: variable is already defined in scope
            System.out.println("Lambda msg: " + msg);
        };

        Runnable anon = new Runnable() {
            @Override
            public void run() {
                String msg = "Hi"; // ✅ Shadows the outer variable
                System.out.println("Anonymous msg: " + msg);
            }
        };

        lambda.run(); // Hello
        anon.run();   // Hi
    }
}
```

---

## 3. **Summary Table**

| Feature                | Lambda Expression                      | Anonymous Class                                                     |
| ---------------------- | -------------------------------------- | ------------------------------------------------------------------- |
| **`this` keyword**     | Refers to **enclosing class instance** | Refers to **anonymous class instance**                              |
| **Variable Shadowing** | ❌ Not allowed                         | ✅ Allowed                                                          |
| **Variable Capture**   | Only **effectively final** variables   | Can capture final variables (Java 7) or effectively final (Java 8+) |
| **Syntax**             | Compact, functional style              | Verbose, object-oriented style                                      |

---

## 4. **Key Takeaways**

- **Effectively final** variables make captured values immutable inside lambdas.
- In **lambdas**, `this` means the outer class’s `this`, unlike anonymous classes.
- Lambdas **don’t create a new variable scope** (no shadowing), improving readability.
- Prefer lambdas when you only need behavior, not a new type.

---

Do you want me to now **extend this with a diagram** showing memory capture for lambdas vs anonymous classes so it’s easier to visualize? That could make this note more memorable for certification prep.
