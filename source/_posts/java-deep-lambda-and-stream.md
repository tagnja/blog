---
title: Understanding Java：Lambda and Stream
date: 2020-04-16 09:24:06
categories: 
- 编程语言
- Java
tags: Java
---

This post will discuss content in Java lambda and stream. It's based Java SE 8.

## Stream

### Stream

**What is Stream**

A sequence of elements supporting sequential and parallel aggregate operations. 

Special Stream class (like `IntStream`) that they provide additional methods for its type of stream (like `sum()` of `IntStream`)

To perform a computation, stream operations are composed into a stream pipeline. A stream pipeline consist of source, zero or more intermediate operations, and a terminal operation or `forEach(Consume)`. Streams are lazy; computation on the source data is only performed when the terminal operation is initiated, and source elements are consumed only as needed.

Streams do not provide a means to directly access or manipulate their elements, and are instead concerned with declaratively describing their source and the computational operations which will be performed in aggregate on that source. However, if the provided stream operations do not offer the desired functionality, the `BaseStream.iterator()` and `BaseStream.spliterator()` operations can be used to perform a controlled traversal.

A stream should be operated on (invoking an intermediate or terminal stream operation) only once. Since some stream operations may return their receiver rather than a new stream object, it may not be possible to detect reuse in all cases.

**Methods of `Stream` interface**

Intermediate Operations

- Filter
  - `Stream distinct()`
  - `Stream filter(Predicate<? super T> predicate)`
  - `Stream limit(long maxSize)`
  - `Stream skip(long n)`
- Map (or Convert)
  - `Stream flatMap(Function mapper)`
  - `DoubleStream flatMapToDouble(Function mapper)`
  - `IntStream flatMapToInt(Function mapper)`
  - `LongStream flatMapToLong(Function mapper)`
  - `Stream map(Function mapper)`
  - `DoubleStream mapToDouble(ToDoubleFunction mapper)`
  - `IntStream mapToInt(ToIntFunction mapper)`
  - `LongStream mapTOLong(ToLongFunction mapper)`
- Operate stream elements
  - `Stream peek(Consumer action)`
  - `Stream sorted()`
  - `Stream sorted(Comparator comparator)`

Terminal Operations

- Testing

  - `boolean allMatch(Predicate predicate)`
  - `boolean anyMatch(Predicate predicate)`
  - `boolean noneMatch(Predicate predicate)`
  - `Optional findAny()`
  - `Optional findFirst()`

- Get Converted Object Value

  - `R collect(Collector<? super T,A,R> collector)`
  - `Object[] toArray()`
  - `A[] toArray(IntFunction generator)`

- Get Computing Result

  - `long count()`
  - `Optional max(Comparator comparator)`
  - `Optional min(Comparator comparator)`
  - `Optional reduce(BinaryOperator accumulator)`
  - `T reduce(T identity, BinaryOperator accumulator)`

- Traversing Object

  - `void forEach(Consumer action)`
  - `void forEachOrdered(Consumer action)`

Static Methods

- `static Stream empty()`
- `static <T> Stream.Builder<T> builder()`
- `static Stream concat(Stream<? extends T> a, Stream<? extends T> b)`
- `static Stream generate(Supplier s)`
- `static Stream iterate(T seed, UnaryOperator f)`
- `static Stream of (T... values)`
- `static Stream of (T t)`

**Methods of `BaseStream` interface**

- `void close()`
- `boolean isParallel()`
- `Iterator<T> iterator()`
- `S onClose(Runnable closeHandler)`
- `S parallel()`
- `S sequential()`
- `Spliterator<T> spliterator()`
- `S unordered()`

**Methods of `IntStream` interface**

Intermediate Operations

- `Stream<Integer> boxed()`

Terminal Operations

- `OptionalDouble average()`
- `int sum()`

**Examples**

Map (or Convert)

```java
List<String> letters = Arrays.asList("a", "b", "c");
List<String> collect = letters.stream().map(String::toUpperCase).collect(Collectors.toList());
System.out.println(collect);
```

```java
int sum = widgets.stream()
    .filter(w -> w.getColor() == RED)
    .mapToInt(w -> w.getWeight())
    .sum();
```

Testing

```java
Stream<Integer> stream = Stream.of(1, 1, 3);
stream.allMatch(i -> {return i > 1;})
```

Get Converted Object Value

```java
Stream<Integer> stream = Stream.of(1, 1, 3);
stream.filter(i -> {return i > 1}).collect(Collectors.toList());
```

Get Computing Result

```java
Stream.of(1, 1, 3).count();
```

```java
Stream<Integer> stream = Stream.of(1, 2, 3, 2, 1);
int sum = stream.reduce(0, (i, j) -> {
    return i + j;
}));
int product = stream.reduce(1, (i, j) -> {
    return i * j;
});
```

Traversing Object

```java
Stream<Integer> stream = Stream.of(1, 2, 3);
stream.forEach(i -> System.out.println(i));
```

```java
Stream<Integer> stream = Stream.of(1, 2, 3, 2, 1);
stream.sorted((o1, o2) -> {
    return o1 - o2;
}).forEachOrdered(System.out::print);
```



### Collector

A [mutable reduction operation](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html#Reduction) that accumulates input elements into a mutable result container, optionally transforming the accumulated result into a final representation after all input elements have been processed. Reduction operations can be performed either sequentially or in parallel.

### Collectors

Implementations of [`Collector`](https://docs.oracle.com/javase/8/docs/api/java/util/stream/Collector.html) that implement various useful reduction operations, such as accumulating elements into collections, summarizing elements according to various criteria.

## Function

### Function

`Function` is a functional Interface

- `R apply(T t)`

Example

```java
Function<Integer, Double> half = a -> a/2.0;
System.out.println(half.apply(10));
```

### Predicate

`Predicate` is a functional Interface

- `boolean test(T t)`

Example

```java
Predicate<Integer> predicate = i -> {return i > 1;};
System.out.println(predicate.test(2));
```

```java
public boolean testPredicate(int val, Predicate<Integer> predicate){
    return predicate.test(val);
}
public void test(){
    assertTrue(testPredicate(2, i -> i > 1));
    assertFalse(testPredicate(1, i -> i > 1));
}
```

### Consumer

`Consumer` is a functional Interface

- `void accept(T t)`

Example

```java
Consumer consumer = System.out::println;
consumer.accept("hello Consumer");
```

### Supplier

`Supplier` is a functional Interface

- `T get()`

Example

```java
Supplier supplier = () -> {return "hello Supplier";};
System.out.println(supplier.get());
```

```java
public static void testSupplier(Supplier supplier){
    System.out.println(supplier.get());
}
public static void main(String[] args) {
    testSupplier(()->"hello");
}
```

### BinaryOperator

`BinaryOperator` is a functional interface, and it extends `BiFunction<T, U, R>`

- `R apply(T t, U u)`

### Lambda Expressions and Method References

**what is lambda expression?**

Implements anonymous inner class of interface that has a single method and have annotation `@FuncationalInterface` can using lambda expression.

Example 1

```java
Thread thread = new Thread(new Runnable(){
    void run(){
        System.out.println("annoymous inner class");
    }
});
```

```java
Thread thread = new Thread(()->{
    System.out.println("lambda expression");
});
```

Example 2

```java
File dir = new File("{dir_path}");
File[] files = dir.listFiles(new FileFilter(){
    @Override
    public boolean accept(File file){
        return file.getName().endsWith(".log");
    }
});
```

```java
File dir = new File("{dir_path}");
File[] files = dir.listFiles((file)->{
    return file.getName().endsWith(".log");
});
```

**What is Method Reference?**

You use [lambda expressions](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html) to create anonymous methods. Sometimes, however, a lambda expression does nothing but call an existing method. In those cases, it's often clearer to refer to the existing method by name. Method references enable you to do this; they are compact, easy-to-read lambda expressions for methods that already have a name.

**Kinds of Method References**

There are four kinds of method references:

| Kind                                                         | Example                                |
| ------------------------------------------------------------ | -------------------------------------- |
| Reference to a static method                                 | `ContainingClass::staticMethodName`    |
| Reference to an instance method of a particular object       | `containingObject::instanceMethodName` |
| Reference to an instance method of an arbitrary object of a particular type | `ContainingType::methodName`           |
| Reference to a constructor                                   | `ClassName::new`                       |

Examples of Usage

```java
Arrays.sort(rosterAsArray, Person::compareByAge);
Arrays.sort(rosterAsArray, myComparisonProvider::compareByName);
Arrays.sort(stringArray, String::compareToIgnoreCase);
Set<Person> rosterSet = transferElements(roster, HashSet::new);
```

## Java Util

### Optional

**What is Optional?**

Convert a object to Optional object, if the value is not null, `isPresent()` will return true and get() return the value; if the value is null, `isPresent()` will return false and get() return empty Optional object. 

In short, you can either get value or empty optional from an optional object, and you never get null.

**Optional Methods**

Static Methods

- `static Optional empty()` // get empty Optional
- `static Optional of(T value)` // if value is null, it will throw `NullPointerException`
- `static Optional ofNullable(T value)` // if value is null, return empty Optional

Intermediate Operations

- `Optional filter(Predicate predicate)`
- `Optional flatMap(Function mapper)`
- `Optional map(Function mapper)`

Terminal Operations

- Test
  - `void ifPresent(Consumer consumer)`
  - `boolean ifPresent()`
- Get
  - `T get()`
  - `T orElse(T other)`
  - `T orElseGet(Supplier other)`
  - `T orElseThrow(Supplier exceptionSupplier)`

Example

```java
int getNotNullValueOrDefault(Integer obj, int defaultValue){
    Optional<Integer> optional = Optional.ofNullable(obj);
    return optional.orElse(defaultValue);
}
```

### Comparator

Comparator is a functional interface

- `int compare(T o1, T o2)`

### Spliterator

Spliterator is for traversing and partitioning elements of a source. The source of elements covered by a Spliterator could be, for example, an array, a Collection, an IO channel, or a generator function.

When used in a parallel programming solution, bear in mind that the individual *Spliterator* is not thread safe; instead the parallel processing implementation must insure that each individual *Spliterator* is handled by one thread at a time. A thread calling the *trySplit()* of a *Spliterator*, may hand over the returned new *Spliterator* to a separate thread for processing. This follows the idea of decomposition of a larger task into smaller sub-tasks that can be processed in parallel individually of each other.

Note Spliterator likes Stream every operate only once. Can't traverse the same spliterator more than once.

**Methods of Spliterator**

- Characteristics
  - `int characteristics()`
  - `default boolean hasCharacteristics(int characteristics)`
- Traversal
  - `default void forEachRemaining(Consumer action)`
  - `boolean tryAdvance(Consumer action)`
- Others
  - `long estimateSize()`
  - `default long getExactSizeIfKnow()`
  - `default Comparator getComparator()`
  - `Spliterator trySplit()`

**Example**

characteristics

```java
List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3));
Spliterator<Integer> spliterator = list.spliterator();
System.out.println(spliterator.hasCharacteristics(Spliterator.SIZED)); // true
int expected = Spliterator.ORDERED | Spliterator.SIZED | Spliterator.SUBSIZED;
System.out.println(spliterator.characteristics() == expected); // true
```

traversal

```java
spliterator.forEachRemaining(System.out::println);
```

```java
while (spliterator.tryAdvance(System.out::println));
```

split

```java
List<Integer> list = new ArrayList<>(Arrays.asList(1, 2, 3));
Spliterator<Integer> spliterator = list.spliterator();
Spliterator<Integer> newPartition = spliterator.trySplit();
spliterator.forEachRemaining(System.out::println);
System.out.println("======");
newPartition.forEachRemaining(System.out::println);
```



## Summary

**Stream** is similar to "for loop", but it's more concise than "for loop" and it provides many convenient operations implementation. Additionally, it can sequential or parallel operating container elements.

**Functional Interface** represents the class that can be use lambda expression.

**Lambda Expression** is similar to implementation of anonymous inner class, but it's more concise than anonymous inner class.

**Method Reference** is similar to lambda expression, but it's more concise than Lambda Expression. It is a replacement of method implementation of an anonymous inner class when they have the same input and output of the method. It's more convenient and concise than Lambda Expression.

## References

[1] [Java API docs](https://docs.oracle.com/javase/8/docs/api/)

[2] [Lambda Expressions - The Java Tutorials](https://docs.oracle.com/javase/tutorial/java/javaOO/lambdaexpressions.html)

[3] [Method References - The Java Tutorials](https://docs.oracle.com/javase/tutorial/java/javaOO/methodreferences.html)

