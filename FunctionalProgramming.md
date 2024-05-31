# Start from 10:00
# Introduction Java 8
### Why is Functional Programming necessary?

- **Simplifying Code for Multi-core Processing :**

    - With the increasing prevalence of multi-core processors, efficiently utilizing these cores became crucial for achieving better performance. Traditional imperative programming can make parallelism more complex and error-prone, especially when dealing with mutable shared state and concurrency. 
    - Functional programming's emphasis on immutability and stateless functions makes it easier to parallelize code without worrying about thread interference or locking issues. 
    - Features like streams and lambdas in Java allow developers to write cleaner, more concise code that can be parallelized easily.

- **Improving Code Maintainability and Conciseness :**

    - Functional programming encourages writing more declarative code that specifies what should be done, rather than how to do it. This can lead to more concise and readable code. 
    - For example, operations that would require several lines of code with loops and conditionals can often be reduced to a single expressive line using streams and lambda expressions. This reduction in boilerplate code not only makes the codebase easier to maintain but also reduces the likelihood of bugs.

- **Enhancing Collection Manipulation :**

    - Before Java 8, manipulating collections often required verbose loops and conditional statements. 
    - Functional programming introduced through the Streams API allows for more expressive and efficient manipulations of collections, such as filtering, mapping, and reducing, using high-level functions. 
    - This has made code involving collection processing easier to write and read.

- **Interoperability with Other Languages :**
    
    - As other JVM languages like Scala, Kotlin, and Clojure, which support functional programming, gained popularity, enhancing Java with functional capabilities helped maintain its competitiveness and interoperability. 
    - Developers can now use functional styles in Java itself, rather than switching to another language for functional constructs, making Java more versatile.

- **Responding to Modern Software Development Practices :**

    - The rise of big data and the need for more robust and scalable applications have also pushed Java towards adopting functional programming features. 
    - Functional programming can lead to better data processing capabilities and is well-suited for handling large data sets efficiently, especially when combined with Java's strong type-checking system.

- **Facilitating Test-Driven Development (TDD) :**
    
    - Functional programming's emphasis on pure functions has benefits for testing. 
    - Pure functions are easier to test in isolation because their output is only dependent on their input and they do not have side effects. 
    - This aligns well with test-driven development (TDD) practices, where tests are written before the code itself and can help ensure that functions behave as expected.

### Functions as First class citizen?

- We can pass functions to function. 
- We can create functions within function. 
- we can return function from function. 

- has to be pure Functions (look into appendix)

### Lambda expressions a the minimal .


    | Imperative                    | Declarative              |
    |:------------------------------|:-------------------------|
    | We focus on how to do things  | We focus on what we do   |
    | We encourage mutability       | We transform the data    |
    | We deal with Side effects     | We write pure functions  |
    | We pass objects               | We pass functions also   |
    | Hard to compose               | Functional composition   |


- lambdas are state less
- lambdas has closure (immutable state)




## Functional Programming : 

- Stage1: Stream Creation: 

    Here are some common ways to create streams:

>> | Source                  | Method Example                            | Description                                                                   |
> >|-------------------------|-------------------------------------------|-------------------------------------------------------------------------------|
> >| Collections             | `collection.stream()` or `collection.parallelStream()` | Used to create a sequential or parallel stream from any collection.           |
> >| Arrays                  | `Arrays.stream(array)`                    | Used to create a stream from an array.                                        |
> >| Static Factory Methods  | `Stream.of(...)`, `IntStream.range(...)`, etc. | Used to create streams from a series of elements or a range of integers.      |
> >| I/O Sources             | `Files.lines(Path path)`                  | Creates a stream of lines from the specified file, useful for reading files.  |


<br>

- Stage2: Intermediate Operations (Transformations):

    Intermediate operations return a new stream. They are lazy; executing an intermediate operation such as filter does not actually perform any filtering, but instead creates a new stream that, when traversed, contains the elements of the initial stream that match the given predicate. Key intermediate operations include:

<br>

> >| Operation | Description                                                                                     | Examples                                   |
> >|-----------|-------------------------------------------------------------------------------------------------|--------------------------------------------|
> >| Map       | Transforms each element to another object via a function.                                       | `map`, `mapToInt`, `mapToDouble`, `mapToLong` |
> >| Filter    | Selects elements that satisfy a predicate.                                                      |                                            |
> >| FlatMap   | Flattens nested structures.                                                                     | `flatMap`, `flatMapToInt`, `flatMapToDouble`, `flatMapToLong` |
> >| Distinct  | Returns a stream with unique elements (according to `Object.equals(Object)`).                   |                                            |
> >| Sorted    | Sorts the stream.                                                                               | `sorted()`, `sorted(Comparator<? super T> comparator)` |
> >| Peek      | Returns an equivalent stream with an additional action performed on each element as they are consumed. |                                            |
> >| Limit     | Truncates the stream to no more than the given number of elements.                               |                                            |
> >| Skip      | Discards the first n elements of the stream.                                                    |                                            |

<br>

- Stage3: Terminal Operations (Reductions and Closures):

   Terminal operations produce a result. Once a terminal operation is performed, the stream can no longer be used. Key terminal operations include:

<br>


> >| Operation | Description                                                                                          | Examples                                          |
> >|-----------|------------------------------------------------------------------------------------------------------|---------------------------------------------------|
> >| Reduce    | Combines elements of the stream using a binary function to reduce the stream to a single summary element. |                                                   |
> >| Collect   | Accommodates mutable reduction into Collections like List, Set, Map, etc., using Collectors.          |                                                   |
> >| ForEach   | Performs an action for each element of the stream.                                                   |                                                   |
> >| Match     | Returns a boolean value based on conditions.                                                         | `anyMatch`, `allMatch`, `noneMatch`               |
> >| Count     | Returns the count of elements in the stream.                                                         |                                                   |
> >| Find      | Returns an optional describing some element of the stream.                                           | `findFirst`, `findAny`                            |



<br>

- Stage4: Short-circuiting Operations

    Some operations are short-circuiting, meaning they do not need to process the entire stream to produce a result:

<br>

> >| Operation         | Description                                                                                             |
> >|-------------------|---------------------------------------------------------------------------------------------------------|
> >| AnyMatch, AllMatch, NoneMatch | These can terminate early without examining all elements.                                               |
> >| FindFirst, FindAny | These can also terminate as soon as a suitable element is found.                                          |
> >| Limit             | While limit is an intermediate operation, it can also enable short-circuiting in conjunction with other operations. |

<br/>

- **More on short circuit Operations**

    short-circuiting operations in the context of Java streams can be both intermediate and terminal operations. The main point about them is that they do not need to process the entire stream to produce a result. This characteristic allows these operations to potentially terminate early, improving performance especially for large streams.

    Here's how they fit into the stream operation categories:
    - **Intermediate Short-circuiting Operations:**
    
        - **limit(n):** This is an intermediate operation that limits the size of the stream to n elements. It is short-circuiting in the sense that it does not need to process more than n elements of the stream if chained with other operations.

    - **Terminal Short-circuiting Operations:**

        - **anyMatch(predicate):** This operation checks whether any elements of the stream match the given predicate. It terminates the operation as soon as a match is found, thus potentially avoiding examination of all elements.
        - **allMatch(predicate)**: It checks if all elements of the stream match the given predicate. The operation terminates as soon as a non-matching element is found.
        - **noneMatch(predicate)**: Similar to allMatch, this checks whether no elements of the stream match the given predicate, terminating as soon as any matching element is found.
        - **findFirst()**: This returns the first element of the stream, which is especially useful in parallel streams because it provides a guarantee about which element is returned.
        - **findAny()**: This can return any element from the stream that satisfies some condition or just any encountered element, useful for performance optimization in parallel streams as it can return the first element found without considering order.


### Function programming example

```
List<Integer> numbers = Arrays.asList(1,2,3,4,5,6,7,8,9,10);	
		int function =2;
		Stream<Integer> stream = numbers.stream().map(e -> e*function);
		function =4;
		System.out.println("The end!");
```

Throws error it says it should be effectively final. 


#### Functional Interface:

A functional interface, as defined by the @FunctionalInterface annotation, should have exactly one abstract method. This annotation is not mandatory for the compiler to treat an interface as functional, but it helps in clarity and ensures that the interface maintains its functional status in the case of future modifications.


#### Common Functional Interfaces in Java

Java provides many built-in functional interfaces in the java.util.function package. Some of the most commonly used are:

- **Predicate&lt;T&gt;:** It takes one argument of type T and returns a boolean. It's often used for filtering or matching.
    - Abstract Method: boolean test(T t)
    - Example: 

        ```
        Predicate<String> nonEmptyStringPredicate = s -> !s.isEmpty();
        List<String> nonEmpty = list.stream()
                .filter(nonEmptyStringPredicate)
                .collect(Collectors.toList());
        ```

- **Consumer&lt;T&gt;:** It accepts a single input argument and returns no result, typically used for operations that act on a given argument without returning a result.
    - Abstract Method: void accept(T t)
    - Example: 
        ```
        Function<Integer, Integer> squareFunction = x -> x * x;
        List<Integer> squared = numbers.stream()
                .map(squareFunction)
                .collect(Collectors.toList());
        ```
- **Function&lt;T,R&gt;:** It takes one argument of type T and returns a result of type R. This interface is suitable for transforming values.
    - Abstract Method: R apply(T t)
    - Example:
        ```
        Function<Integer, Integer> squareFunction = x -> x * x;
        List<Integer> squared = numbers.stream()
                .map(squareFunction)
                .collect(Collectors.toList());
        ```
- **Supplier&lt;T&gt;:** It takes no arguments and returns a result of type T, often used for lazy generation of values.
    - Abstract Method: T get()
    - Example:
        ```
        Supplier<Date> dateSupplier = () -> new Date(); // Supplier that returns the current date and time
        Date date = dateSupplier.get();  // Fetches the current date and time
        System.out.println(date);  // Outputs the current date and time
        ```

- **UnaryOperator&lt;T&gt;:** A specialization of Function where the input and output types are the same.
    - Abstract Method: T apply(T t)
    - Example:
        ```
        UnaryOperator<Integer> square = x -> x * x;  // Squares an Integer
        Integer squaredValue = square.apply(5);  // squaredValue = 25
        ```

- **BinaryOperator&lt;T&gt;:** A specialization of BiFunction where both operands and the result are all of the same type.
    - Abstract Method: T apply(T t1, T t2)
    - Example:
        ```
        BinaryOperator<Integer> adder = (a, b) -> a + b; // BinaryOperator that adds two integers
        Integer result = adder.apply(5, 3);  // result is 8
        System.out.println(result);  // Outputs 8

      ```





































<br><br><br><br><br><br><br><br><br><br>
<br><br><br><br><br><br><br><br><br><br>
<br><br><br><br><br><br><br><br><br><br>












































## Appendix :
### pure function : 

- Pure function is a fundamental concept in functional programming that refers to a specific type of function which adheres to two main rules:

    - **Deterministic Output :** The output of a pure function is solely determined by its input values. Given the same set of inputs, a pure function will always produce the same output, regardless of any other conditions or states that may exist in the program.

    - **No Side Effects :** A pure function does not cause side effects. A side effect is any change in the state of the system that is observable outside the function itself. This includes modifying any external variables, input objects, I/O operations (like printing to a console or writing to a file), or any other action that affects the state of the application or external environment.

- **Benefits of Pure Functions:**

    - **Predictability :** Since the output is determined only by the input values, pure functions are highly predictable, making them easier to debug and test.

    - **Reusability :** The lack of dependency on external state allows pure functions to be reused in different contexts without unexpected behavior.

    - **Concurrency :** Pure functions are inherently thread-safe because they do not share or alter any state that could lead to race conditions or other synchronization issues.

    - **Cacheability :** The deterministic nature of pure functions allows for memoization, a technique where the results of function calls are cached based on their input parameters. This can significantly improve performance for expensive function calls. 

### Mutable state :  

- In computing, mutable state refers to data or objects that can be modified after they are created. When a state is mutable, any function or method with access to that state can change its contents, potentially affecting the behavior of other parts of the program that also access the same state.

- Shared state, on the other hand, refers to state (data or objects) that is accessible from multiple threads, processes, or components simultaneously. When state is shared, it becomes a critical area in concurrent programming because unsynchronized access by multiple threads can lead to unpredictable results and hard-to-track bugs.

    **Challenges with Mutable Shared State in Concurrent Programming:**

- When combining mutability with shared access in a multi-threaded environment, several issues can arise:

    - Race Conditions: When two or more threads access shared mutable state concurrently and at least one thread modifies the state, the final state depends on the timing of the threads' execution. This unpredictable behavior is known as a race condition and can lead to incorrect or unexpected results.

    - Deadlocks: When multiple threads hold locks on resources while waiting for other locks to be released by other threads, it can lead to a situation where none of the threads can proceed, known as a deadlock.

    - Livelocks: Similar to deadlocks, livelocks occur when threads keep changing states in response to each other without making progress, typically because they are designed to react to changes made by other threads.

    - Resource Starvation: This occurs when one or more threads are perpetually denied necessary resources to proceed because other threads are monopolizing those resources.

    **How Functional Programming Addresses These Issues:**

    - Functional programming promotes immutability and stateless functions, which significantly reduces the complexity of dealing with concurrent programming issues:

        - Immutability: In a functional approach, objects or data structures are immutable, meaning that once they are created, their state cannot be changed. If you need to modify an object, you create a new object with the desired changes. This eliminates the risk of one thread altering the state of an object while another thread is using it.

        - Stateless Functions: Functions in functional programming typically don't store or modify any external state. Each function's output is determined solely by its input values. This property makes functions inherently safe for concurrent execution since there's no shared state that could lead to race conditions or other synchronization issues.

    **More Discussion on Immutability:**

    Immutability involves creating new objects instead of modifying existing ones. This approach can indeed lead to concerns about performance, especially in terms of memory usage and the cost of creating new objects frequently. However, there are several strategies and optimizations that Java and functional programming languages use to mitigate these potential performance issues:

    - **Garbage Collection Efficiency:** Java's garbage collector is highly optimized for handling a large number of short-lived objects. Immutable objects are often short-lived, as they are created, used, and then go out of scope quickly, making them eligible for garbage collection. This can actually lead to more predictable performance in some scenarios, as modern garbage collectors are very efficient in managing short-lived objects.

    - **Sharing Common Structures:** Immutable objects can share their internal structures with other objects safely because they cannot be changed after their creation. For example, when creating a new version of an immutable list with an additional element, the underlying implementation can reuse most of the existing data structure, appending only the new element. This technique, often seen in functional languages, reduces the need for complete data duplication and lowers 
    memory overhead.

        - The idea is to reuse parts of a data structure instead of copying the entire structure when creating a modified version of it. This is particularly useful with large collections like lists, trees, or maps.

        - **Example:** Consider an immutable list. When you want to add an element to it, instead of copying the entire list to create a new one, you create a new list node that points to the existing list. This way, the new list shares as much of its structure as possible with the original. This approach is used in many functional languages and is known as persistent data structures.

    - **String Interning:** Java uses a concept called string interning to manage memory for immutable strings efficiently. Interning saves memory by ensuring that identical strings share the same memory. This is a form of structural sharing at the language runtime level.

        - String interning is a method of storing only one copy of each distinct string value, which must be immutable. By ensuring that all strings that are identical share the same storage, Java can significantly reduce memory consumption and improve performance when comparing strings.

        - In Java: The Java Virtual Machine (JVM) maintains a special memory area called the "String Pool." When a string is created, the JVM checks the pool to see if an identical string already exists. If it does, the reference to the existing string is returned instead of creating a new object. This is why:

            ```
            String s1 = "hello";
            String s2 = "hello";
            boolean same = s1 == s2;  // This will be true because both refer to the same object in the string pool.
            ```

            However, strings created using the new keyword do not automatically use the pool:

            ```
            String s3 = new String("hello");
            boolean sameAsS1 = s1 == s3;  // This will be false.
            ```


    - **Lazy Evaluation:** Some functional concepts, like lazy evaluation, can be employed to improve performance. For example, Java's streams API allows for building a pipeline of operations that are only executed when necessary. This means that intermediate immutable structures need not be created unless absolutely required.

        - Lazy evaluation is a computation strategy that delays the evaluation of an expression until its value is actually needed. This can lead to performance improvements by avoiding needless calculations, and it can also allow for the creation of data structures that can theoretically be infinite in size.

        - In Java: The Streams API introduced in Java 8 is a good example of lazy evaluation in action. Stream operations that transform or filter data do not actually perform any computation when they're called; instead, they create a new stream that knows how to perform the operation when it's eventually needed. The actual computation happens when the stream is consumed by a terminal operation like collect(), forEach(), or reduce().

            ```
            List<String> words = Arrays.asList("hello", "world", "java", "stream");
            List<String> result = words.stream()        // Creates a stream
                .filter(s -> s.length() > 4)            // Filter operation is lazy
                .map(String::toUpperCase)               // Map operation is also lazy
                .collect(Collectors.toList());          // Actual computation happens here
            ```

            In this example, filtering and mapping are set up lazily; they only get executed when the final collect() operation is invoked. This means if you don’t need all the elements (say, if you added a .limit(2) before collect()), not all the elements go through all the stages—only as many as needed, which is an efficient use of resources.

    - **Optimized Collections:** Java 8 introduced several immutable collections (like List.of(), Set.of(), and Map.of()) that are more memory-efficient than their mutable counterparts. These immutable collections are highly optimized for performance and memory usage.

    - **Escape Analysis:** Modern Java Virtual Machines (JVMs) use a technique called escape analysis to determine if an object is used outside of the scope where it was created. If it isn’t, the JVM can optimize away the allocation of such objects on the heap, sometimes allocating them on the stack instead, which is much faster.

