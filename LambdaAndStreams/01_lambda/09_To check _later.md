Short answer: **No—there are more.** Here’s the broader set you should know, grouped by type, with quick examples.

---

## Functions & Operators

- **`Function<T,R>`** → `compose`, `andThen`, `identity`

  ```java
  Function<String,Integer> len = String::length;
  Function<Integer,String> toHex = i -> Integer.toHexString(i);
  Function<String,String> lenToHex = len.andThen(toHex);
  ```

- **`BiFunction<T,U,R>`** → `andThen`

  ```java
  BiFunction<Integer,Integer,Integer> sum = Integer::sum;
  Function<Integer,String> toStr = Object::toString;
  BiFunction<Integer,Integer,String> sumToStr = sum.andThen(toStr);
  ```

- **`UnaryOperator<T>`** (a `Function<T,T>`) → inherits `compose/andThen`; `UnaryOperator.identity()`.
- **Primitive unary operators** (`IntUnaryOperator`, `LongUnaryOperator`, `DoubleUnaryOperator`) → `compose`, `andThen`.

---

## Predicates

- **`Predicate<T>`** → `and`, `or`, `negate`, `isEqual`, **`Predicate.not`** _(Java 11)_

  ```java
  Predicate<String> nonEmpty = s -> !s.isEmpty();
  Predicate<String> notBlank = Predicate.not(String::isBlank); // J11
  ```

- **`BiPredicate<T,U>`** → `and`, `or`, `negate`.
- **Primitive predicates** (`IntPredicate`, `LongPredicate`, `DoublePredicate`) → `and`, `or`, `negate`.

---

## Consumers

- **`Consumer<T>`** → `andThen`

  ```java
  Consumer<String> log = System.out::println;
  Consumer<String> save = s -> repo.save(s);
  Consumer<String> logThenSave = log.andThen(save);
  ```

- **`BiConsumer<T,U>`** → `andThen`
- **Primitive consumers** (`IntConsumer`, `LongConsumer`, `DoubleConsumer`) → `andThen`

  > Note: `ObjIntConsumer<T>` **does not** have `andThen`.

---

## Comparators

- **`Comparator<T>`** → `comparing`, `thenComparing`, `reversed`, `nullsFirst/nullsLast`, `naturalOrder/reverseOrder`

  ```java
  Comparator<Person> byCityThenName =
      Comparator.comparing(Person::city, Comparator.nullsLast(Comparator.naturalOrder()))
                .thenComparing(Person::name)
                .reversed();
  ```

- Also: `comparingInt/Long/Double` to avoid boxing.

---

## Collectors (powerful composition)

- **`collectingAndThen(downstream, finisher)`**
- **`mapping(mapper, downstream)`**
- **`flatMapping(mapper, downstream)`** _(Java 9)_
- **`filtering(predicate, downstream)`** _(Java 9)_
- **`teeing(c1, c2, merger)`** _(Java 12)_

  ```java
  record Stats(long count, double avg){}
  Collector<Person,?,Stats> stats = Collectors.teeing(
      Collectors.counting(),
      Collectors.averagingInt(Person::age),
      Stats::new
  );
  ```

---

## Streams & Optional (composition in pipelines)

- **Streams:** chain `map → filter → flatMap → sorted → distinct → ...`
- **Optional:** `map`, `flatMap`, `filter`, `or`, `ifPresentOrElse` (J9+)

  ```java
  Optional<String> hexLen = Optional.of("abc").map(String::length).map(Integer::toHexString);
  ```

---

## Async composition (very testable in exams)

- **`CompletableFuture`** → `thenApply`, `thenCompose` (flatten), `thenCombine`, `allOf/anyOf`, `handle`

  ```java
  cfA.thenCompose(a -> fetchB(a))      // compose async
     .thenCombine(fetchC(), (b, c) -> merge(b, c));
  ```

---

### Bottom line

Your list is a good start, but **also learn**: `BiFunction.andThen`, `BiPredicate.*`, primitive specializations’ compose/andThen, richer `Comparator` chains, **collector composition** (`mapping`, `flatMapping`, `filtering`, `collectingAndThen`, `teeing`), `Predicate.not`, and async composition with `CompletableFuture`.

If you want, I can turn this into a one-page cheat sheet with tiny runnable examples for each.
