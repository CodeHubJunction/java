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

## Key Concepts

- _Lambda expression_: A concise way to represent a function or a block of code.
- _Functional interface_: An interface that has a single abstract method (SAM).
- _SAM_: Single abstract method.

## Use Cases

- _Event handling_: Lambda expressions can be used to handle events, such as button clicks or keyboard input.
- _Data processing_: Lambda expressions can be used to process data, such as filtering or mapping data.
- _Functional programming_: Lambda expressions can be used to implement functional programming concepts, such as map, filter, and reduce.

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

# Method References in Java

## Introduction

Method references are a shorthand way of expressing lambda expressions that call a specific method. They are denoted by the double colon (::) operator.

## Types of Method References

There are three types of method references:

1.  `_Static Method References_`: These are used to reference static methods.
2.  `_Instance Method References_`: These are used to reference instance methods.
3.  `_Constructor References_`: These are used to reference constructors.

## Examples

Here are some examples of method references:

### Static Method References

```java
// Use a lambda expression to parse a string to an integer
Function<String, Integer> parser = s -> Integer.parseInt(s);

// Use a static method reference to parse a string to an integer
Function<String, Integer> parserRef = Integer::parseInt;

// Test the parser and parserRef
System.out.println(parser.apply("123")); // prints 123
System.out.println(parserRef.apply("123")); // prints 123
```

- In this example, `System.out::println` is an instance method reference that references the println method of the PrintStream instance represented by System.out.

### Instance Method References

```java
// Using a lambda expression
List<String> list = Arrays.asList("a", "b", "c");
list.forEach(s -> System.out.println(s));

// Using a method reference
list.forEach(System.out::println);
```

- In this example, System.out::println is an instance method reference that references the println method of the PrintStream instance represented by System.out.

### Instance Method References

```java
// Using a lambda expression
List<String> list = Arrays.asList("a", "b", "c");
list.forEach(s -> s.toUpperCase());

// Using a method reference
list.forEach(String::toUpperCase);
```

- However, the above example will not work as expected because forEach expects a Consumer that consumes a String and returns nothing, but String::toUpperCase returns a String. To fix this, we can use a lambda expression or a method reference with a Consumer that consumes a String and prints its uppercase version.

```java
list.stream().map(String::toUpperCase).forEach(System.out::println);
```

### Constructor References

```java
// Using a lambda expression
Supplier<List<String>> supplier = () -> new ArrayList<>();

// Using a constructor reference
Supplier<List<String>> supplier = ArrayList::new;
```

- In this example, ArrayList::new is a constructor reference that references the no-arg constructor of the ArrayList class.

## Lambda Expressions vs Method References

Lambda expressions and method references are both used to represent a function or a block of code that can be executed. However, they differ in their syntax and usage.

- `_Lambda Expressions_`: Lambda expressions are a more general way of representing a function or a block of code. They can be used to represent any kind of function, including those that are not associated with a specific method.
- `_Method References_`: Method references are a shorthand way of expressing lambda expressions that call a specific method. They are more concise and readable than lambda expressions, but they are limited to referencing existing methods.

## When to Use Method References

Method references are a good choice when:

- You need to reference an existing method.
- You want to make your code more concise and readable.

## When to Use Lambda Expressions

Lambda expressions are a good choice when:

- You need to represent a function or a block of code that is not associated with a specific method.
- You need more flexibility in your code.

## Summary

- Method references are a shorthand way of expressing lambda expressions that call a specific method.
- There are three types of method references: static method references, instance method references, and constructor references.
- Method references are more concise and readable than lambda expressions, but they are limited to referencing existing methods.

## Key Concepts

- _Method references_: A shorthand way of expressing lambda expressions that call a specific method.
- _Lambda expressions_: A more general way of representing a function or a block of code.

## Use Cases

- _Event handling_: Method references can be used to handle events, such as button clicks or keyboard input.
- _Data processing_: Method references can be used to process data, such as filtering or mapping data.

## Best Practices

- _Use method references when possible_: Method references are more concise and readable than lambda expressions, so use them when possible.
- _Use lambda expressions when necessary_: Lambda expressions are more flexible than method references, so use them when necessary.

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
