# Lambda Expressions in Java

## Introduction

Lambda expressions are a concise way to represent a function or a block of code that can be passed as an argument to a method or returned from a method. They are a key feature of Java 8 and later versions.

## Syntax

The syntax of a lambda expression is as follows:

```java
(parameters) -> { body }
```

- `_parameters_`: The input parameters of the lambda expression. The type of the parameters can be explicitly specified or inferred by the compiler.
- `_->_`: The lambda operator, which separates the parameters from the body.
- `_body_`: The code that is executed when the lambda expression is invoked. The body can be a single expression or a block of statements.

## Examples

Here are some examples of lambda expressions:

```java
// A lambda expression with no parameters
() -> System.out.println("Hello");

// A lambda expression with one parameter
x -> x \* x;

// A lambda expression with multiple parameters
(x, y) -> x + y;

// A lambda expression with a block of statements
(x, y) -> {
int sum = x + y;
return sum;
};
```

# Lambda Expressions in Java: Advanced Topics

## When to Use {} (Block Statement)

The {} is required when the lambda expression body contains more than one statement.

```java
// Example: Using {} with multiple statements
Runnable r = () -> {
System.out.println("Hello");
System.out.println("World");
};
```

- In this example, the lambda expression body contains two statements, so {} is required.

## When to Use Return Statement

The return statement is required when the lambda expression body is a block statement and it needs to return a value.

```java
// Example: Using return statement with a block statement
Comparator<String> c = (s1, s2) -> {
int result = s1.compareTo(s2);
return result;
};
```

- In this example, the lambda expression body is a block statement and it returns a value, so return is required.

## When to Omit {} and Return Statement

The {} and return statement can be omitted when the lambda expression body is a single expression.

```java
// Example: Omitting {} and return statement with a single expression
Comparator<String> c = (s1, s2) -> s1.compareTo(s2);
```

- In this example, the lambda expression body is a single expression, so {} and return can be omitted.

## When to Use () for Arguments

The () is required when the lambda expression has no arguments or when the type of the arguments is explicitly specified.

```java
// Example: Using () with no arguments
Runnable r = () -> System.out.println("Hello");

// Example: Using () with explicitly specified argument types
Comparator<String> c = (String s1, String s2) -> s1.compareTo(s2);
```

- In the first example, the lambda expression has no arguments, so () is required.
- In the second example, the type of the arguments is explicitly specified, so () is required.

## When to Omit () for Arguments

The () can be omitted when the lambda expression has a single argument and the type of the argument is inferred by the compiler.

```java
// Example: Omitting () with a single argument
Consumer<String> c = s -> System.out.println(s);
```

- In this example, the lambda expression has a single argument and the type of the argument is inferred by the compiler, so () can be omitted.

## Best Practices

Here are some best practices for using lambda expressions:

- _Keep it simple_: Lambda expressions should be concise and easy to understand.
- _Use meaningful variable names_: Use meaningful variable names to make the lambda expression easier to understand.
- _Avoid complex logic_: Avoid using complex logic in lambda expressions. If the logic is complex, consider using a separate method.

## Common Pitfalls

Here are some common pitfalls to avoid when using lambda expressions:

- _Not using the correct functional interface_: Make sure to use the correct functional interface when using a lambda expression.
- _Not understanding the context_: Make sure to understand the context in which the lambda expression is being used.

## Summary

- Lambda expressions are a powerful feature in Java that allows you to represent a function or a block of code as an object.
- The syntax of a lambda expression includes parameters, the lambda operator, and a body.
- The {} is required when the lambda expression body contains more than one statement.
- The return statement is required when the lambda expression body is a block statement and it needs to return a value.
- The () is required when the lambda expression has no arguments or when the type of the arguments is explicitly specified.

# Type Inference in Lambda Expressions: Step-by-Step Explanation

## Introduction

Type inference is a feature in Java that allows the compiler to infer the type of a variable or an expression based on the context. In lambda expressions, type inference is used to infer the type of the parameters. In this section, we will explain the step-by-step process of how type inference works in lambda expressions.

## Step 1: Target Typing

The first step in type inference is target typing. The compiler looks at the target type of the lambda expression, which is the type of the variable or expression that the lambda expression is being assigned to.

```java
Comparator<String> c = (s1, s2) -> s1.compareTo(s2);
```

- In this example, the target type is Comparator<String>.

## Step 2: Functional Interface

The next step is to check if the target type is a functional interface. A functional interface is an interface that has a single abstract method (SAM).

```java
@FunctionalInterface
public interface Comparator<T> {
int compare(T o1, T o2);
// Other methods...
}
```

- In this example, Comparator is a functional interface with a SAM compare.

## Step 3: SAM Signature

The compiler then looks at the signature of the SAM, which includes the parameter types and return type.

```java
int compare(T o1, T o2);
```

- In this example, the compare method has two parameters of type T and T, and returns an int.

## Step 4: Type Map

The compiler creates a type map that maps the type parameters of the functional interface to the actual type arguments.

```java
Comparator<String> c = (s1, s2) -> s1.compareTo(s2);
```

- In this example, the type parameter T is mapped to String, so the type map is {T -> String}.

## Step 5: Parameter Type Inference

Finally, the compiler uses the type map to infer the type of the parameters.

```java
(s1, s2) -> s1.compareTo(s2);
```

- In this example, the type of s1 and s2 is inferred to be String, based on the type map {T -> String}.

## Example Walkthrough

Let's walk through the type inference process for the following example:

```java
Comparator<String> c = (s1, s2) -> s1.compareTo(s2);
```

1.  _Target Typing_: The target type is Comparator<String>.
2.  _Functional Interface_: Comparator is a functional interface with a SAM compare.
3.  _SAM Signature_: The compare method has two parameters of type T and T, and returns an int.
4.  _Type Map_: The type parameter T is mapped to String, so the type map is {T -> String}.
5.  _Parameter Type Inference_: The type of s1 and s2 is inferred to be String, based on the type map {T -> String}.

## Characteristics

Lambda expressions have several characteristics:

- _Anonymous_: Lambda expressions are anonymous, meaning they are not declared with a name.
- _Function_: Lambda expressions represent a function or a block of code that can be executed.
- _Passed as an argument_: Lambda expressions can be passed as an argument to a method.
- _Returned from a method_: Lambda expressions can be returned from a method.

# Accessing Variables in Lambda Expressions

## Introduction

Lambda expressions can access variables from the surrounding scope.

## Accessing Instance Variables

Instance variables can be accessed within lambda expressions.

```java
public class MyClass {
private int instanceVar = 10;

    public void myMethod() {
        Runnable r = () -> System.out.println(instanceVar);
        r.run(); // prints 10
    }

    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.myMethod();
    }

}
```

- In this example, the lambda expression accesses the instance variable instanceVar.

## Accessing Static Variables

Static variables can be accessed within lambda expressions.

```java
public class MyClass {
private static int staticVar = 20;

    public void myMethod() {
        Runnable r = () -> System.out.println(staticVar);
        r.run(); // prints 20
    }

    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.myMethod();
    }

}
```

- In this example, the lambda expression accesses the static variable staticVar.

## Modifying Instance and Static Variables

Instance and static variables can be modified within lambda expressions.

```java
public class MyClass {
private int instanceVar = 10;
private static int staticVar = 20;

    public void myMethod() {
        Runnable r = () -> {
            instanceVar = 30; // Modifying instance variable
            staticVar = 40; // Modifying static variable
        };
        r.run();
        System.out.println(instanceVar); // prints 30
        System.out.println(staticVar); // prints 40
    }

    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.myMethod();
    }

}
```

- In this example, the lambda expression modifies the instance variable instanceVar and the static variable staticVar.

## Variable Shadowing Rules

Variable shadowing occurs when a variable in a lambda expression has the same name as a variable in the surrounding scope.

```java
public class MyClass {
private int x = 10;

    public void myMethod() {
        int x = 20; // Shadows the instance variable x
        Runnable r = () -> System.out.println(x); // Prints 20
        r.run();
    }

    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.myMethod();
    }

}
```

- In this example, the lambda expression shadows the local variable x with the same name.

## Limitations of Variable Usage in Lambda Context

There are certain limitations to using variables in lambda expressions.

1.  _Effectively final variables_: Variables used in lambda expressions must be effectively final, meaning they cannot be used to be reassigned after they are initialized.

```java
public class MyClass {
public void myMethod() {
   int x = 10;
   Runnable r = () -> {
   // x = 20; // Uncommenting this line will cause a compilation error
   System.out.println(x);
};
r.run();
}

    public static void main(String[] args) {
        MyClass obj = new MyClass();
        obj.myMethod();
    }

}
```

Alright — I’ll explain **SAM types & overload resolution** in Java 8,
covering **rules**, **ambiguity**, and **disambiguation**, and give you everything in **Markdown** so you can keep it as notes.

---

# 📘 Java 8 — SAM Types & Overload Resolution

## 1️⃣ What is a SAM Type?

- **SAM** = **Single Abstract Method** type.
- A **functional interface** is a SAM type — it has **exactly one** abstract method.
- **Lambda expressions** and **method references** can be used wherever a SAM type is expected.

### Example:

```java
@FunctionalInterface
interface MyPrinter {
    void print(String msg);
}

public class SamExample {
    public static void main(String[] args) {
        MyPrinter p = (m) -> System.out.println(m); // Lambda → SAM type
        p.print("Hello SAM!");
    }
}
```

---

## 2️⃣ SAM Rules in Java

1. **One Abstract Method Only**

   - Can have any number of `default` or `static` methods.
   - Annotation `@FunctionalInterface` is optional but recommended.

2. **Target Typing**

   - A lambda has **no type by itself**; the type is inferred from the **target functional interface**.

3. **Overriding `Object` Methods**

   - Methods like `equals()`, `hashCode()` in a functional interface **do not count** towards the single abstract method requirement.

---

## 3️⃣ Overload Resolution Problem with SAM Types

If you have **two overloaded methods** that take **different functional interfaces** with the **same lambda parameter shape**, the compiler **can’t decide** which to call → **ambiguity error**.

### Example: Ambiguity

```java
import java.util.function.Consumer;
import java.util.function.Function;

public class OverloadAmbiguity {
    static void process(Consumer<String> c) {
        System.out.println("Consumer called");
        c.accept("Test");
    }

    static void process(Function<String, String> f) {
        System.out.println("Function called");
        f.apply("Test");
    }

    public static void main(String[] args) {
        // ❌ Ambiguity — both take a String parameter
        // process(s -> s.toUpperCase());
    }
}
```

**Compiler Error:**

```
reference to process is ambiguous
```

---

## 4️⃣ Disambiguation Techniques

### **A) Casting the Lambda**

Explicitly tell the compiler the intended SAM type.

```java
process((Function<String, String>) s -> s.toUpperCase()); // ✅ Function
process((Consumer<String>) s -> System.out.println(s));   // ✅ Consumer
```

---

### **B) Using Method References**

Sometimes method references make the target type clear.

```java
process(System.out::println);          // Matches Consumer<String>
process(String::toUpperCase);          // Matches Function<String, String>
```

---

### **C) Generic Type Witness**

If the method is generic, you can **provide type parameters** to guide resolution.

```java
class GenericExample {
    static <T> void execute(Function<T, T> f) {
        System.out.println("Function<T, T>");
    }
    static <T> void execute(Consumer<T> c) {
        System.out.println("Consumer<T>");
    }

    public static void main(String[] args) {
        // Type witness helps choose Function
        GenericExample.<String>execute(s -> s.toUpperCase());
    }
}
```

---

### **D) Changing Lambda Form**

Changing lambda to match **return type** or **void** can disambiguate.

```java
process(s -> { System.out.println(s); }); // Matches Consumer
process(s -> { return s.toUpperCase(); }); // Matches Function
```

---

## 📌 Summary Table

| Problem Area            | Cause                                                   | Fix                        |
| ----------------------- | ------------------------------------------------------- | -------------------------- |
| Ambiguous overload      | Multiple functional interfaces with same parameter list | Cast to intended interface |
| Unclear lambda type     | Lambda form fits both                                   | Add return/void explicitly |
| Generic methods         | Type inference picks multiple                           | Use generic type witness   |
| Both fit via method ref | Method reference matches multiple                       | Use explicit cast          |

---

## ✅ Key Takeaways

- **SAM** = Single Abstract Method → basis of functional interfaces.
- Overload resolution can fail if two methods accept different functional interfaces but share the **same parameter signature**.
- Resolve ambiguity by:

  1. **Casting** the lambda
  2. Using **method references**
  3. Supplying **generic type witness**
  4. Adjusting **lambda body** to match only one functional interface

---

If you want, I can also give you **one runnable Java file** containing **all 4 disambiguation examples** so you can compile & see which one gets called in each case.
Do you want me to prepare that? That would make this even more practical.

## Summary

- Lambda expressions can access instance and static variables.
- Instance and static variables can be modified within lambda expressions.
- Variable shadowing occurs when a variable in a lambda expression has the same name as a variable in the surrounding scope.
- Variables used in lambda expressions must be effectively final.
- Lambda expressions capture variables by value, not by reference.

## Key Concepts

- _Lambda expressions_: A concise way to represent a function or a block of code.
- _Effectively final variables_: Variables that are not reassigned after they are initialized.
- _Variable shadowing_: When a variable in a lambda expression has the same name as a variable in the surrounding scope.

## Use Cases

- _Data processing_: Lambda expressions can be used to process data in a collection.
- _Event handling_: Lambda expressions can be used to handle events, such as button clicks or keyboard input.

## Best Practices

- _Use effectively final variables_: Use effectively final variables to avoid compilation errors.
- _Avoid variable shadowing_: Avoid variable shadowing to make the code more readable and maintainable.
