Java Lambda and Stream

Content

- java.util.stream

  - Stream
  - Collector

- java.util.function

  - Function
  - Consumer
  - Predicate

  - Supplier

- java.util

  - Optional
  - Comparator
  - Spliterator

## Stream

### Stream

Methods of Stream interface

intermediate operations

- static Stream concat(Stream<? extends T> a, Stream<? extends T> b)
- Stream distinct()
- Stream filter(Predicate<? super T> predicate)
- Stream flatMap(Function mapper)
- DoubleStream flatMapToDouble(Function mapper)
- IntStream flatMapToInt(Function mapper)
- LongStream flatMapToLong(Function mapper)
- static Stream generate(Supplier s)
- static Stream iterate(T seed, UnaryOperator f)
- Stream limit(long maxSize)
- Stream map(Function mapper)
- DoubleStream mapToDouble(ToDoubleFunction mapper)
- IntStream mapToInt(ToIntFunction mapper)
- LongStream mapTOLong(ToLongFunction mapper)
- static Stream of (T... values)
- static Stream of (T t)
- Stream peek(Consumer action)
- Stream skip(long n)
- Stream sorted()
- Stream sorted(Comparator comparator)

terminal operations

- boolean allMatch(Predicate predicate)

- ```java
  Stream<Integer> stream = Stream.of(1, 1, 3);
  stream.allMatch(i -> {return i > 1;})
  ```

- boolean anyMatch(Predicate predicate)

- R collect(Collector<? super T,A,R> collector)

- long count()

- Optional findAny()

- Optional findFirst()

- void forEach(Consumer action)

- void forEachOrdered(Consumer action)

- Optional max(Comparator comparator)

- Optional min(Comparator comparator)

- boolean noneMatch(Predicate predicate)

- Optional reduce(BinaryOperator accumulator)

- T reduce(T identity, BinaryOperator accumulator)

- Object[] toArray()

- A[] toArray(IntFunction generator)

Others

- static Stream empty()
- `static <T> Stream.Builder<T> builder()`

### Collector

## Function

### Function

Functional Interface

- R apply(T t)

### Predicate

Functional Interface

- boolean test(T t)

### Consumer

Functional Interface

- void accept(T t)

Example

```java

```

### Supplier

Functional Interface

- T get()

Example

```java
public static void testSupplier(Supplier supplier){
    System.out.println(supplier.get());
}
public static void main(String[] args) {
    testSupplier(()->"hello");
}
```



## Questions

### what is lambda expression?

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

### What is Method Reference?