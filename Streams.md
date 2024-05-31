# Introduction Java 8

### Simplifying Code with Lambda Expressions

Lambda expressions, introduced in Java 8, greatly simplify how we write code, making it more readable and concise. This section explores the effective use of lambda expressions in streamlining thread creation.

### Traditional Thread Creation

The conventional method for creating a thread in Java requires defining a new Thread instance and implementing the Runnable interface. Here's an example:


```
Thread traditionalThread  = new Thread(new Runnable() {

			@Override
			public void run() {
				// TODO Auto-generated method stub
				System.out.println("Another thread");

			}
		});
		traditionalThread .start();
		
		System.out.println("Main method!! ");
```

This approach, while clear, can be verbose, especially for implementations that simply execute a single method.

### Streamlining Code with Lambda Expressions

Java 8 allows for a more succinct expression of single-method interfaces (functional interfaces) through lambda expressions. These can particularly simplify thread creation:

```
Thread lambdaThread = new Thread(() -> System.out.println("Another lambda Thread"));
		lambdaThread.start();
```

This code snippet demonstrates how a lambda expression reduces boilerplate by directly implementing the run() method of the Runnable interface.

The code has undergone some weight loss program. 

### Compiler Efficiency with Lambda Expressions.

Lambda expressions do not only simplify syntax but also enhance compiler efficiency compared to traditional anonymous inner classes. Using anonymous inner classes, Java generates a separate class file for each, which can increase the memory footprint and degrade performance. In contrast, lambda expressions avoid this by not generating separate class files.


### From Imperative to Declarative: Enhancing Code Readability

Imperative programming, such as using an external iterator, can be verbose and error-prone:

```
List<Integer> numbers = Arrays.asList(1,2,3,4,5,6,7,8,9,10);		
for(int i=0;i<numbers.size();i++) {
	System.out.println(numbers.get(i));
}		
```

In this we have an external iterator. The code looks simple but its not. We have to initialize the code, look for the boundary condition and ensure that the increment is done perfectly else it can lead to error. 

#### Improved
This can be improved with an enhanced for-loop:

```
for(int number:numbers) {
	System.out.println(number);
}
```
But this is also for external where we need to do say go to another element and another element etc. 


#### Further Simplification Using Lambda Expressions

```
numbers.forEach(new Consumer<Integer>() {

    @Override
    public void accept(Integer value) {
        // TODO Auto-generated method stub
        System.out.println(value);
    }
    
});
```

The benefit is polymorphism. Where we can tell what to do and vary the implementation detail based on the actual type of the object. 

But this can be simplified further 

```
numbers.forEach((Integer value)-> System.out.println(value));
```

In this case the values are Integer, Its  obvious so we can remove Integer and its called type inference, only for lambda expression. 


```
numbers.forEach((value)-> System.out.println(value));
```
We can reduce it further by removing the parentheses. Note this removal can be done only if there are one parameter. 

```
numbers.forEach( value-> System.out.println(value));
```

We are passing the value and doing nothing. Hence we can further reduce it my method referencing.  
```
numbers.forEach(System.out::println);
```

Now the code is very expressive.  We know that we are just printing the value.

lambda should be short: http://blog.agiledeveloper.com/2015/06/lambdas-are-glue-code.html

When the lambda expression is large:
	
	- The code is hard to read.
	- It's noisy
	- Leads to duplication.
	- hard to test. 

Lambda expression must not contain any logic. If you need to implement any logic it should be implemented in a function. That function should be througly tested and the function should be called from Lambda. 

### Method referencing. 

#### Method referencing for instance method.
This can be of use when you do not want to alter the value but want to pass it to the next journey of code. 

Eg:

```
numbers.forEach(System.out::println);
```
Here we are passing to the instance method println. Where System -> Class out -> static method. and print ln is the instance method. 


#### Method referencing for static method.

```
numbers.stream()
		.map(e -> String.valueOf(e))
		.forEach(System.out::println);
```

AS we have already seen we can further reduce it by removing the common 'e'

```
numbers.stream()
		.map(String::valueOf)
		.forEach(System.out::println);

```


#### Method referencing as a target. 

```
numbers.stream()
		.map(e -> e.toString()) //Here we are converting the Integer to String.
		.forEach(System.out:: println);
```


We can make this better by using method referencing. Here the object e is of type Integer and hence we can write it as 

```
numbers.stream()
		.map(Integer::toString) //Here we are converting the Integer to String but 
		                        //this cannot be used as there are two toString method. It will //create ambiguity.
		.forEach(System.out:: println);
```


We can rewrite it as

```
numbers
		.stream()
		.map(String::valueOf)
		.forEach(System.out:: println);
```

#### Method referencing when two parameters as arguments.

Note: Order of the parameter is very important. In the below example the sum may not be very strigent on that but other methods might be. 

```

System.out.println(
		numbers
		.stream()
		.reduce(0, (e, total) -> Integer.sum(total, e)));
		
```

for better analysis:

```
BinaryOperator<Integer> binaryOperatorAccumulator = (total, e) -> {
			int result =total+e;
			System.out.println("Accumulator: Total = " + total + ", Element = " + e+" , Result = "+result);
			return result;
		};

int sum = numbers.stream().reduce(0, binaryOperatorAccumulator);
```
the output is 

```
Accumulator: Total = 0, Element = 1 , Result = 1
Accumulator: Total = 1, Element = 2 , Result = 3
Accumulator: Total = 3, Element = 3 , Result = 6
Accumulator: Total = 6, Element = 4 , Result = 10
Accumulator: Total = 10, Element = 5 , Result = 15
Accumulator: Total = 15, Element = 6 , Result = 21
Accumulator: Total = 21, Element = 7 , Result = 28
Accumulator: Total = 28, Element = 8 , Result = 36
Accumulator: Total = 36, Element = 9 , Result = 45
Accumulator: Total = 45, Element = 10 , Result = 55
Sum = 55

```

While using parallel Stream. 

```
	BinaryOperator<Integer> binaryOperatorAccumulator = (total, e) -> {
			int result =total+e;
			System.out.println("Accumulator: Total = " + total + ", Element = " + e+" , Result = "+result);
			return result;
		};

		 BinaryOperator<Integer> combiner = (a, b) -> {
			 int result = a+b;
	            System.out.println("Combiner: A = " + a + ", B = " + b+" , Result = "+result);
	            return a + b;
	        };

		int sum = numbers.stream().parallel().reduce(0, binaryOperatorAccumulator, combiner);
```
outPut:

```
Accumulator: Total = 0, Element = 7 , Result = 7
Accumulator: Total = 0, Element = 2 , Result = 2
Accumulator: Total = 0, Element = 9 , Result = 9
Accumulator: Total = 0, Element = 1 , Result = 1
Accumulator: Total = 0, Element = 3 , Result = 3
Combiner: A = 1, B = 2 , Result = 3
Accumulator: Total = 0, Element = 5 , Result = 5
Accumulator: Total = 0, Element = 8 , Result = 8
Accumulator: Total = 0, Element = 10 , Result = 10
Combiner: A = 9, B = 10 , Result = 19
Combiner: A = 8, B = 19 , Result = 27
Accumulator: Total = 0, Element = 4 , Result = 4
Combiner: A = 4, B = 5 , Result = 9
Combiner: A = 3, B = 9 , Result = 12
Combiner: A = 3, B = 12 , Result = 15
Accumulator: Total = 0, Element = 6 , Result = 6
Combiner: A = 6, B = 7 , Result = 13
Combiner: A = 13, B = 27 , Result = 40
Combiner: A = 15, B = 40 , Result = 55
Sum = 55

```

This can be further reduced by method referencing. As mentioned earlier method referencing the order of the parameter is very important but in case of sum it does not  matter. 

```
System.out.println(
				numbers
				.stream()
				.reduce(0, Integer::sum));
```

*Note*:

To see the internal workings in the above code we can modify it as as below. 

```
System.out.println("Checking ");
		System.out.println(numbers.stream().reduce(0, (total, e) -> {
			System.out.println("E " + e + " total " + total);
			return Integer.sum(total, e);
		}));
		
```

the output of it is 

```
Checking 
E 1 total 0
E 2 total 1
E 3 total 3
E 4 total 6
E 5 total 10
E 6 total 15
E 7 total 21
E 8 total 28
E 9 total 36
E 10 total 45
55
```
We can observe that the identity value 0 is taken by the first parameter of the binary operator and the successive values are taken by e. 

Now when we interchange the parameters. 

```
	System.out.println("Checking ");
		System.out.println(numbers.stream().reduce(0, (e, total) -> {
			System.out.println("E " + e + " total " + total);
			return Integer.sum(total, e);
		}));
```


We can observe that the first parameter still takes the identity value in this case it happens to be e and the successive values are taken by total. 

```
Checking 
E 0 total 1
E 1 total 2
E 3 total 3
E 6 total 4
E 10 total 5
E 15 total 6
E 21 total 7
E 28 total 8
E 36 total 9
E 45 total 10
55
```

on similar lines can try to concatenate values

```
System.out.println(
				numbers
				.stream()
				.map(String::valueOf)
				.reduce("", (carry, str) -> carry.concat(str)));
```

output:

```
12345678910
```

now if we change the order 

```
System.out.println(
				numbers
				.stream()
				.map(String::valueOf)
				.reduce("", (str, carry) -> carry.concat(str)));
```

output:

```
10987654321
```

By using method referencing. 

```
System.out.println(
				numbers
				.stream()
				.map(String::valueOf)
				.reduce("", String::concat)
		);
```

output:

```
12345678910
```
This give this output as the first argument is has the empty value initialized and the default implementation is considered.

**Note**:

Method referencing cannot be used:

	- When we want to modify the data before processing.
	- When there is conflict between instance method and static method.
	
# Functional composition:

### Imperative stype of coding: Double the number if they are even and calculate the sum.

```
int result =0;
for(int e: numbers) {
	if(e%2==0) {
		result += e*2;
	}
}		
```

This is so difficult to read, it spegati code and it becomes hard to read. It can be simplified as below 

```
System.out.println(
	numbers.stream()
	.filter(n -> n%2==0)
	.map(n-> n*2)
	.reduce(0,Integer::sum)
	);
```
This is so easy to read. Can be made better by

```
System.out.println(
	numbers.stream()
		.filter(n -> n%2==0)
		.mapToInt(n-> n*2)
		.sum()
	);
```

Consider this piece of code:

```
int result =0;		
for(int i=0;i<numbers.size();i++) {
	if(numbers.get(i)>5 && isEven(numbers.get(i))&& numbers.get(i)<9&& numbers.get(i)*2>15) {
		result= numbers.get(i);
		break;
	}
}
System.out.println("Result "+result);
```

Its so difficult to read and understand.

using streams  simplifies it to.
```
int resultLambda = 
	numbers.stream()
		.filter(n -> n>5)
		.filter(n -> isEven(n))
		.filter(n -> n<9)
		.filter(n -> n*2>15)
		.findFirst()
		cch.get();
		
System.out.println("resultLambda "+ resultLambda);
```

### Consider this piece of code where in we measure time taken to run the code.

A helper class to measure the time taken

```
class Timeit {

	public static void code(Runnable block) {
		long start = System.nanoTime();

		try {
			block.run();
		} catch (Exception e) {

		} finally {
			long end = System.nanoTime();
			System.out.println("Time taken " + (end - start) / (1.0e9));

		}
	}
}
```
A helper method that performs computation.

```
public static int compute(int number) { // asssume that its very time intensive
		try {
			Thread.sleep(1000);
		} catch (Exception e) {
			// TODO: handle exception
		}
		return number * 2;
	}
```


Invoking the main method


```
public static void main(String[] args) {

		List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
		Timeit.code(() -> {
			System.out.println(
					numbers.stream()
					.filter(e -> e % 2 == 0)
					.mapToInt(Sample::compute)
					.sum());
		});
	}
```
Output:

```
60
Time taken 5.0550743
```

On parallization

```
public static void main(String[] args) {

		List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
		Timeit.code(() -> {
			System.out.println(
					numbers.parallelStream()
					.filter(e -> e % 2 == 0)
					.mapToInt(Sample::compute)
					.sum());
		});
	}
```

Output:

```
60
Time taken 2.0483415
```

Parllization is like a bull dozer. Having one does not mean we need to carry it around and use it for all our purpose. 

When to use parallelStream :

- It makes sense to use, The problem at hand is actually parallizable.
- We we are willing to spend lot more resource to get the result faster. 
- When the data size is big enough to get the performance benefits. 
- When the task is big enough to get the performance benefits.


### Streams and Abstractions:

Streams is a bunch of functions that you would be evaluating. Its not data like list of set. 

#### Stream Operations :
- filter
	- filter: Zero or  <= number of elements in the output. <= number of input
	- input : Stream <T> filter takes Predicate<T>
- map
	- transforms values
	- number of output == number of inputs
	- No guarantee on the type of the output with respect to the type of the input
	- parameter: Stream<T> map takes Function<T,R> to return Stream<R>

- **Note**: Both filter and map stays within their swimlanes. It takes one input at a time
- reduce 
	- cuts across the swimlanes and produces the result for the operation. It takes the inputs together and then produces the result for the operation. 
	- reduce on Stream<T> takes two parameters: 
		- first parameter is of type T
		- second parameter is of type BiFunction<R,T,R> to produce a result of R
		- R first  - Input Type
		- T        - Element Type
		- R Second - Output Type

			Reduce example:

			```
			List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9, 10);
					
			System.out.println(
					numbers.stream()
					.mapToInt(e -> e*2)
					.reduce(0,(element1, element2) -> element1+element2));
			```
			Specialized reduce :

			```
			System.out.println(
								numbers.stream()
								.mapToInt(e -> e*2)
								.sum());
			```
- Specialized reduce operation Collect
	- To List :
		
		```
		//double the even values and put that to a list.
			
			List<Integer> dup_numbers= numbers.stream()
				.filter(n -> n%2==0)
				.map(e -> e*2)
				.collect(Collectors.toList());
		```

		output:
		```
		[4,8,4,8]
		```
		Collect is the reduce operation.

		never do this. It can lead to concurrency issues etc.

		```
		List<Integer> dup_numbers= new ArrayList<>();
				numbers.stream()
				.filter(n -> n%2==0)
				.map(e -> e*2)
				.map(e -> dup_numbers.add(e) );
		```
	- To Set:

		```
		Set<Integer> dup_numbers= numbers.stream()
				.filter(n -> n%2==0)
				.map(e -> e*2)
				.collect(Collectors.toSet());
		```

		output:
		```
		[4,8]
		```

	- To Map :

		- Consider the person class

		```
		class Person {
			// Fields
			private String name;
			private Gender gender;
			private int age;

			// Constructor
			public Person(String name, Gender gender, int age) {
				this.name = name;
				this.gender = gender;
				this.age = age;
			}

			//setter and getter
		}

		```
		- The enum

		```
		enum Gender {
			MALE, FEMALE
		}
		```
		- Creating lis tof people

		```
		public static List<Person> createPeople() {

		return Arrays.asList(
				new Person("Sara", Gender.FEMALE, 20), new Person("Sara", Gender.FEMALE, 22),
				new Person("Bob", Gender.MALE, 20), new Person("Paula", Gender.FEMALE, 32),
				new Person("Paul", Gender.MALE, 32), new Person("Jack", Gender.MALE, 2),
				new Person("Jack", Gender.MALE, 72), new Person("Jill", Gender.FEMALE, 12));
		}
		```

		creating a map using streams:

		```
		Map<String, Person> map = people.stream()
                .collect(Collectors.toMap(
                		p -> p.getName() + "_" + p.getAge(),  //  setting key 
                		p -> p)); 							  // setting value
				System.out.println(map);
		```

	- Group by  :

		- To create a map and group by name
			```
			Map<String, List<Person>> groupedByName = 
						people.stream()
						.collect(
								Collectors.groupingBy(Person::getName) 
								);
			``` 

		- output:
			```
			{
				Bob=[Person{name='Bob', gender=MALE, age=20}], 
				Sara=[Person{name='Sara', gender=FEMALE, age=20}]
			}
			```
	- Group by name and get only age not the entire person object. 

		- 
			```
			Map<String, List<Integer>> result = 
						people.stream()
						.collect(
								Collectors.groupingBy(
										Person::getName,Collectors.mapping(
											Person::getAge, Collectors.toList()
											)
										)
								);
			```
		- output 
			```
			{
				Bob=[20], 
				Sara=[20, 22], 
				Jill=[12], 
				Jack=[2, 72], 
				Paula=[32], 
				Paul=[32]
			}
			```

	- Group by name and get hash of age and name combined:

		- 
			```
			Function<Person, String> nameAndageString = (e -> e.getName()+e.getAge());
				
				Map<String, List<String>> resultChecking = 
						people.stream()
						.collect(
								Collectors.groupingBy(
										Person::getName,Collectors.mapping(nameAndageString, Collectors.toList())));
			```
		-  output
			```
			{
				Bob=[Bob20], 
				Sara=[Sara20, Sara22], 
				Jill=[Jill12], 
				Jack=[Jack2, Jack72], 
				Paula=[Paula32], 
				Paul=[Paul32]
			}
			```


#### Performance :


- Given an ordered list find the double of the first even number greater than 3. 

old way of coding
```
int result = 0;
for(int num : numbers) {
	if(num>3 && num%2==0) {
		result = num *2;
		break;
	}
}
		
```



The 
Functional coding:

```
Optional result2 = numbers.stream()
			.filter(n-> n>3)
			.filter(n -> n%2==0)
			.map(e-> e*2)
			.findFirst();
		
System.out.println("Optional "+result2.get());
```

Understanding the inner workings:
We will modify the code, These are called Intermediate Operations and will not execute until terminal operation.

```
Optional result2 = numbers.stream()
			.filter(n-> n>3)	 //Intermediate Operations
			.filter(n -> n%2==0) //Intermediate Operations
			.map(e-> e*2)		 //Intermediate Operations
			.findFirst();		 //Terminal Operations
		
System.out.println("Optional "+result2.get());
```


The above code is easy to read easy to understand.  Performance 

```
public static boolean greaterThan3(Integer number) {
	System.out.println("greaterThan3 "+number );
	return number>3;
}

public static boolean isEven(Integer number) {
	System.out.println("isEven "+number);
	return number%2==0;
}

public static Integer doubleIt(Integer number) {
	System.out.println("doubleIt "+number);
	return number*2;
}

```
and our lambda changes to 

```
Optional result2 = numbers.stream()
	.filter(Sample:: greaterThan3)
	.filter(Sample:: isEven)
	.map(Sample:: doubleIt)
	.findFirst();

System.out.println("Optional "+result2.get());
```

output is :

```
greaterThan3 1
greaterThan3 2
greaterThan3 3
greaterThan3 4
isEven 4
doubleIt 4
Optional 8

```
As one can observe it does not run through the entire stream. Here we take only one element and apply the operations.  

More of analysis. 


```
Optional<Integer> result2 = numbers.stream()
		.filter(Sample:: greaterThan3)
		.filter(Sample:: isEven)
		.filter(Sample:: isDivisibleBy5)
		.map(Sample:: doubleIt)
		.findFirst();
```
newly added method:

```
public static boolean isDivisibleBy5(Integer number) {
		System.out.println("isDivisibleBy5 "+number);
		return number%5==0;
	}
```

Here it takes one element and checks if it matches the filter criteria and if it matches then it proceed to the next filter else no.. 

We can observe the same in the output. 

```
greaterThan3 1
greaterThan3 2
greaterThan3 3
greaterThan3 4
isEven 4
isDivisibleBy5 4
greaterThan3 5
isEven 5
greaterThan3 6
isEven 6
isDivisibleBy5 6
greaterThan3 7
isEven 7
greaterThan3 8
isEven 8
isDivisibleBy5 8
greaterThan3 9
isEven 9
greaterThan3 10
isEven 10
isDivisibleBy5 10
doubleIt 10
Optional 20

```

The code or the pipeline does not get executed until it meets the terminal operations. 
```
Stream<Integer> result2 = numbers.stream()
			.filter(Sample:: greaterThan3)
			.filter(Sample:: isEven)
			.map(Sample:: doubleIt);
```
Here we can observe that since there is no terminal operations the intermediate operations does not get executed at all.


#### Characteristics of a Stream : sized, ordered, distinct, sorted:

sized: A stream may be sized or unsized. i.e a stream may not have any bounds. 

ordered: a stream may be ordered or unordered.

distinct: a stream may enforce distinctness or may not enforce distinctness. 

sorted: a sorted or unsorted stream.

To know the properties of a stream

```
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 1, 2, 3, 4, 5);
	numbers.stream()
		.filter(n -> n%2==0)
		.forEach(System.out:: println);
```

output:

```
2
4
2
4
```

The above stream is:

	- sized: as it coming from a list.
	- ordered as it coming from a list and list is ordered. First element second element etc. 
	- non- distinct as list can have duplicates. 
	- non sorted as the values are non sorted when we go through the values. 

We can change the properties of the stream as shown:

```
List<Integer> numbers = Arrays.asList(1, 2, 3, 4, 5, 1, 2, 3, 4, 5);
	numbers.stream()
		.filter(n -> n%2==0)
		.sorted()
		.forEach(System.out:: println);
```

output:

```
2
2
4
4
```

We can change the properties by tinker with the operations as shown above. 

#### Infinite stream:

Consider the example:

```
public static void main(String[] args) {
	System.out.println("Start");
	System.out.println(Stream.iterate(100, n -> n+1));
	System.out.println("End");
}
```

output:
```
Start
java.util.stream.ReferencePipeline$Head@68f7aae2
End
```


Example:

Given a number k and a count of n, find the total of double of n even numbers. starting with k where sqrt of each number is >20

```
public static int compute(int k, int n) {
	int result = 0;

	int index = k;
	for (int count = 0; count<n; index++) {
		if (index % 2 == 0 && Math.sqrt(index) > 20) {
			result = result + index*2;
			System.out.println(index);
			count++;
		}
	}
	return result;
}
```

caller method:

```
System.out.println("Start");
//System.out.println(Stream.iterate(100, n -> n + 1));
System.out.println(compute(1, 10));
System.out.println("End");
```
output:

```
Start
402
404
406
408
410
412
414
416
418
420
8220
End

```

Look at the complexity of coding it + the increment of count is done in the if condition and the index increment is done at the end of the loop. making it harder to read and understand.

```
public static int compute(int k, int n) {
	return 
	Stream.iterate(k, element -> element+1)    // unbounded, lazy stream 
	.filter(element -> element%2==0)           // unbounded, lazy stream   
	.filter(element -> Math.sqrt(element)>20)  // unbounded, lazy stream
	.mapToInt(element -> element *2)           // unbounded, lazy stream
	.limit(n)                                  // bounded,   lazy sliced stream
	.sum();                                    // returns int
}
```
Output:

```
Start
8220
End
```

The above code is concise, easy to understand, the code is choesive (It does one thing and one thing only).

# Start from 2:24:00 Charectistics of streams
