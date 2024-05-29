# Exploring Collectors 

## Common operations: filter map and reduce

Consider the example below:

```
class Person {
	// Fields
	private String name;
	private int age;

	// Constructor
	public Person(String name, int age) {
		this.name = name;
		this.age = age;
	}

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	@Override
	public String toString() {
		// TODO Auto-generated method stub
		return this.getName() +"_"+this.getAge();
	}
}
```

Method to create people:

```
public static List<Person> createPeople() {

    return Arrays.asList(
        new Person("Sara", 20), 
        new Person("Sara", 22),
        new Person("Bob", 20), 
        new Person("Paula", 32),
        new Person("Paul", 32), 
        new Person("Jack", 23),
        new Person("Jack", 72), 
        new Person("Jill", 11));
}
```

To print all person object 
```
people.forEach(System.out::println);
```

To print only the name and age
```
people.stream()
    .map(person -> person.getName())
    .forEach(System.out::println);

```
To print print person where age is greater than 30
```
people.stream()
			.filter(person -> person.getAge()>30)
			.forEach(System.out::println);
```

To get the total age of the group
```
System.out.println(
    people.stream()
        .mapToInt(person -> person.getAge())
        .sum());
```
To print the age of the group using reduce function.

```
System.out.println("Age of the group using reduce "+
    people.stream()
        .map(person -> person.getAge())
        .reduce(0, (initial, personAge) -> initial+personAge ));
```

To reduce code verbosity

```
System.out.println("Age of the group using reduce with less verbosity "+
    people.stream()
        .map(person -> person.getAge())
        .reduce(0, Integer::sum));
```

## Java has reduce in two forms one as reduce function and the other is collect. 

- reduces the stream into something more concrete.
- with lazy evaluation we are postponing the creation of object and there by avoiding creating lot of objects. 
- lazy evaluation requires purity of function (Idempotency)
- Pure function does not have side-effects.
    - Pure function do not change anything
    - Pure function do not depend on anything that may possibly change. 

## why we need to maintain the above rules:

Consider the example below: where we need to get the list of names in uppercase of those who are older than 30. 

## Do Not code as below example ##

```	
List<String> peopleAbove30  = new ArrayList<>();
	people.stream()
	.filter(person -> person.getAge() >0)
	.map(Person::getName)
	.map(String::toUpperCase)
	.forEach(name -> peopleAbove30.add(name));
```
Here this might run fine until we are not meddling with peopleAbove30 list at other part of the code. If there is any modification it can lead to error.

Also we cannot parallelize this. If we do that there might lead to some race around condition and things can go bad and it can lead to error and be very hard to debug. 

### Better way of writing 
```
List<String> peopleAbove30  = new ArrayList<>();
	peopleAbove30 =	people.stream()
		.filter(person -> person.getAge() >30)
		.map(Person::getName)
		.map(String::toUpperCase)
		.reduce( new ArrayLi(),											//seed data
				(names, name) -> {names.add(name); return names;},			//operation performed on partial result
				(names1,names2)-> {names1.addAll(names2); return names1;});	//collect the entire data from partial result
			
```

### making it concise

```
peopleAbove30 =	people.stream()
	.filter(person -> person.getAge() >30)
	.map(Person::getName)
	.map(String::toUpperCase)
	.collect(Collectors.toList());
```

To fetch the items in a map, 
```
System.out.println(people.stream()
	.filter(person -> person.getAge() >30)
	.collect(Collectors.toMap(Person::getName, Person::getAge))
	);
```
If we want to get the list of age  of persons. 

```
List<Integer> peopleAge =	people.stream()
	.map(Person::getAge)
	.collect(Collectors.toList());
```

If we need unmodifiable list

```
List<Integer> unmodifiablePeopleAge =	people.stream()
	.map(Person::getAge)
	.collect(Collectors.toUnmodifiableList());
System.out.println(unmodifiablePeopleAge);
```

comma seperated names in uppercase of people older than 30

```
System.out.println(people.stream()
	.filter(person -> person.getAge() >30)
	.map(person -> person.getName().toUpperCase())
	.collect((Collectors.joining(", ")))
	);
```
### Understanding collect method

It's actually a terminal operation that transforms the elements of a stream into a different form, usually a collection like a List or a Set. This method is particularly versatile because it can be used to collect elements into virtually any type of result container you want.

Here’s a more accurate breakdown of the collect method:

- Supplier: This is the first argument and it provides a new result container. For example, it could be an empty list or set where the results will be accumulated.

- Accumulator: The second argument is a function that takes two parameters: the result container (from the supplier) and the current stream element. This function is used to add an element to the result container.

- Combiner: The third argument is necessary in parallel processing. It defines how two result containers are to be merged when the stream is processed in parallel.


Group the people based on the age. 

```
List<Person> evenAged = people.stream()
	.filter(person -> person.getAge()%2==0)
	.collect(Collectors.toList());

List<Person> oddAged = people.stream()
	.filter(person -> person.getAge()%2!=0)
	.collect(Collectors.toList());
```


The above is not good way to code as we are iterating the people list two times. 

```
Map<Boolean, List<Person>> peoplePartitioned = 
	people.stream()
	.collect(Collectors.partitioningBy(person -> person.getAge()%2==0));
```

**Note**:  Partition is used  if you have a simple boolean condition and want to divide your data into "yes" and "no" buckets.


Grouping : For more complex grouping operations where groups are formed based on the properties of the items and there can be multiple distinct groups.

```
Map<String, List<Person>> groupByName =
				people.stream()
				.collect(
					Collectors.groupingBy(person -> person.getName()));
```

output:

```
{
	Bob=[Bob_20], 
	Sara=[Sara_20, Sara_22], 
	Jill=[Jill_11], 
	Jack=[Jack_3], 
	JACK=[JACK_72], 
	PAUL=[PAUL_32], 
	PAULA=[PAULA_32]
}
```

Now there is person object instead we just want age of the person nothing not the entire person object. 

```
Map<String, List<Integer>> groupByAgeByName = 
	people.stream()
	.collect(
			Collectors.groupingBy(
					person -> person.getName(), 
					Collectors.mapping(Person::getAge, Collectors.toList())));
```

Instead of add the names, I want you to count the number of persons with the same name.

```
Map<String, Long> countByName = 
				people.stream()
				.collect(
						Collectors.groupingBy(
								person -> person.getName(), 
								Collectors.mapping(Person::getAge, Collectors.counting())));
```
To count people with name.

what if we want int instead of long..

//groupingBy and mapping (Function, Collector)
//collectingAndThen (Collector, Function)

```
Map<String, Integer> countByNameInt = 
				people.stream()
				.collect(
						Collectors.groupingBy(
								Person::getName, 
								Collectors.collectingAndThen( Collectors.counting(),Long::intValue)));
```
max


```
System.out.println(
				people.stream()
				.map(Person::getAge)
				.reduce(0, (total, age)-> total+age));
```

can be shorten further

```
System.out.println(
				people.stream()
				.mapToInt(Person::getAge)
				.sum());
```

To get maximum age: Its returning it as Optional cause there are chances that the List can be empty.

```
OptionalInt maxAge = people.stream().mapToInt(Person::getAge).max();
if (maxAge.isPresent()) {
    System.out.println("Maximum age is: " + maxAge.getAsInt());
} else {
    System.out.println("No maximum age available.");
}
```
Instead of the age I need the person with maximum age. 

```
Optional<Person> oldestPerson = people.stream().max(Comparator.comparing(Person::getAge));
		

		if (oldestPerson.isPresent()) {
			Person person = oldestPerson.get();
			System.out.println("Oldest person is: " + person.getName() + " with age " + person.getAge());
		} else {
			System.out.println("No persons in the list.");
		}
```

Or by using generic collect

```
	oldestPerson = people.stream().collect(Collectors.maxBy(Comparator.comparing(Person::getAge)));
		if (oldestPerson.isPresent()) {
			Person person = oldestPerson.get();
			System.out.println("Oldest person is: " + person.getName() + " with age " + person.getAge());
		} else {
			System.out.println("No persons in the list.");
		}
```

similarly to identify miniumum

```
Optional<Person> youngestPerson = people.stream().min(Comparator.comparing(Person::getAge));


if (youngestPerson.isPresent()) {
	Person person = youngestPerson.get();
	System.out.println("youngestPerson person is: " + person.getName() + " with age " + person.getAge());
} else {
	System.out.println("No persons in the list.");
}



youngestPerson = people.stream().collect(Collectors.minBy(Comparator.comparing(Person::getAge)));
if (youngestPerson.isPresent()) {
	Person person = youngestPerson.get();
	System.out.println("youngestPerson person is: " + person.getName() + " with age " + person.getAge());
} else {
	System.out.println("No persons in the list.");
}
```

Now we are getting the person. Instead we dont want the person bu we need the name not person object.

1) It creates a stream from the people list.

2) It applies a collect operation using Collectors.maxBy, which finds the person with the maximum age based on a comparator.
3) The result, which is an Optional<Person>, is then transformed by Collectors.collectingAndThen.
4) This transformation extracts the person's name if the optional is not empty; otherwise, it returns an empty string.
5) Finally, the name (or empty string if no person is found) is stored in the variable eldestPerson.

```
String eldestPerson = people.stream()
    .collect(Collectors.collectingAndThen(
        Collectors.maxBy(Comparator.comparing(Person::getAge)),
        person -> person.map(Person::getName).orElse("")
    ));

```


Difference between

map and mapping: 

map Method in Streams
The map method is used with Java Streams to apply a function to each element of a stream and collect the results into a new stream. This is a form of transformation where each input element is mapped to a corresponding output element,

mapping Collector in Streams
The mapping collector is part of the Collectors class in Java 8 and is used in conjunction with other collectors, like groupingBy, to apply a transformation function during the collection process.

```
Map<Integer, List<Person>> personGroupByAge = 
				people.stream()
				.collect(Collectors.groupingBy(Person::getAge));
```

Instead of person if we want only the name of the person.

```
Map<Integer, List<String>> personGroupByAge =		
				people.stream()
				.collect(
						Collectors.groupingBy((Person::getAge),Collectors.mapping(Person::getName, Collectors.toList())));
```
output:

```
{
	32=[PAULA, PAUL], 
	3=[Jack], 
	20=[Sara, Bob], 
	22=[Sara], 
	72=[JACK], 
	11=[Jill]
}
```

if we want to filter only those names greater than 4.

```
	Map<Integer, List<String>> personGroupByAge =		
				people.stream()
				.collect(
						Collectors.groupingBy((Person::getAge),Collectors.mapping(Person::getName, Collectors.filtering(name -> name.length()>4,Collectors.toList()))));
```
```
{
	32=[PAULA], 
	3=[], 
	20=[], 
	22=[], 
	72=[], 
	11=[]
}
```

Teeing:

Teeing collector is a feature introduced later in Java 12 as part of the Stream API enhancements. 

This collector is particularly useful for performing two different operations on the same stream and then merging their results.

The teeing collector combines two other collectors, applies them to the same stream, and then merges the results using a supplied "merger" function. This allows you to perform parallel aggregations of a stream with different criteria and then combine the outcomes.

```
List<Integer> numbers = List.of(1, 2, 3, 4, 5);

var result = numbers.stream()
	.collect(Collectors.teeing(
		Collectors.summingInt(Integer::intValue),
		Collectors.counting(),
		(sum, count) -> "Sum: " + sum + ", Count: " + count
	));

System.out.println(result);
```
In this example:

- One collector computes the sum of the numbers.
- Another collector counts the elements.
- The results are merged into a single string that describes both outcomes.

Note: 

- grouping, mapping (Function, Collector)
- collectingAndThen(Collector, Function)
- teeing(Collector, Collector, Function)


UnderStanding of FlatMap. 

One to One Function example:

Its a one to one Function, Where we take an input and we get an output. 

```
List<Integer> numbers = List.of(1,2,3);

//One to one Function
System.out.println(
	numbers.stream()
	.map(number -> number *2)
	.collect(Collectors.toList()));
```
output:

```
[2,4,6]
```

One to Many Function example:

```
List<Integer> numbers = List.of(1,2,3);

//One to many Function
System.out.println(
	numbers.stream()
	.map(number -> List.of(number-1, number+1))
	.collect(Collectors.toList()));
```
output:

```
[[0, 2], [1, 3], [2, 4]]
```

Now from the result we can observe that the output is a list of list instead we are interested to have a list. 

consider the code below:

```
List<Integer> numbers = List.of(1,2,3);

//One to many Function
System.out.println(
		numbers.stream()
		.flatMap(number -> List.of(number-1, number+1).stream())
		.collect(Collectors.toList())
		);
```
**Note**: 

The use of .stream() inside flatMap is crucial. Without it, flatMap would not function because it does not automatically flatten collections into a stream; it only flattens streams.


//If you have a one to one function, use map to go from Stream<T> to Stream<R>

//If you have a one to many function, use map to go from Stream<T> to Stream<Collection>

//If you have a one to many function, use flatMap to go from Stream<T> to Stream<R>


Another example to get all the character present in the name

```
List<String> peopleCharacter = 
	people.stream()
	.map( Person::getName)
	.flatMap( name -> Stream.of(name.split("")))
	.collect(Collectors.toList());
System.out.println(peopleCharacter);
```
output:

```
[S, a, r, a, S, a, r, a, B, o, b, P, A, U, L, A, P, A, U, L, J, a, c, k, J, A, C, K, J, i, l, l]
```

We want to get the characters of the name of the people when grouped by age.

Stage 1:

```
System.out.println(people.stream()
		.collect(Collectors.groupingBy(Person::getAge)));
```

output:

```
{
	Bob=[Bob_20], 
	Sara=[Sara_20, Sara_22], 
	Jill=[Jill_11], 
	Jack=[Jack_3], 
	JACK=[JACK_72], 
	PAUL=[PAUL_32], 
	PAULA=[PAULA_32]
}
```
Stage 2:

```
System.out.println(people.stream()
		.collect(
				Collectors.groupingBy((Person::getAge),Collectors.mapping(Person::getName, Collectors.toList()))));
```
output:

```
{
	32=[PAULA, PAUL], 
	3=[Jack], 
	20=[Sara, Bob], 
	22=[Sara], 
	72=[JACK], 
	11=[Jill]
}
```
To fetch only the character

```
Map<Integer, List<String>> groupedByAge = people.stream()
            .collect(
                Collectors.groupingBy(
                    Person::getAge, // Group people by age
                    Collectors.flatMapping(
                        person -> Stream.of(person.getName().split("")), // Flatten each name into individual characters
                        Collectors.toList() // Collect characters into a list
                    )
                )
            );
```
output:

```
{
	32=[P, A, U, L, A, P, A, U, L], 
	3=[J, a, c, k], 
	20=[S, a, r, a, B, o, b], 
	22=[S, a, r, a], 
	72=[J, A, C, K], 
	11=[J, i, l, l]
}
```

Now if we want do not want any duplicates then instead of list we use set. 

For getting everything in caps:

```
System.out.println(
				people.stream()
				.collect(Collectors.groupingBy(
						Person::getAge,
						Collectors.mapping(
								person -> person.getName().toUpperCase(), 
								Collectors.flatMapping(name -> Stream.of(name.split("")), Collectors.toList()))
						))
				);
```

output:

```
{
	32=[P, A, U, L, A, P, A, U, L], 
	3=[J, A, C, K], 
	20=[S, A, R, A, B, O, B], 
	22=[S, A, R, A], 
	72=[J, A, C, K], 
	11=[J, I, L, L]
}
```
//reduce - sum, min, max, reduce, collect

//collect (Collector)
//Collectors
//toList toSet toMap
//toUnmodifiableList toUnmodifiableSet toUnmodifiableMap

//partitioningBy
//groupingBy(Function, Collector)
//mapping(Function, Collector)

//CollectingAndThen(Collector, Function)
//teeing(Collector, Collector, Operator)

//exception handling should be done in the downstream. You should refer to how they have coded reactive library.

# Start with 01:55