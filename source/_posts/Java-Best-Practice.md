---
title: Java Best Practice
date: 2021-01-08 10:55:28
tags:
categories: 
- Java
- Java 基础
---



## Basics

### Variables and Types

**Principles**

- Member Variables should using wrapper types for fields. E.g. Integer, Long.

#### Initialize variables

- Primitive types

```
byte byteVal = 1;
short shortVal = 1;
int intVal = 1;
long longVal = 1L;
float floatVal = 1.0F;
double doubleVal = 1.0;
boolean booleanVal = true;
char charVal = 'a';
```

- Strings and arrays

```
String str = "hello";
int[] array = {1, 2, 3};
int[] array2 = new int[]{1, 2, 3};
int[] array3 = new int[3];
array3[0] = 1;
array3[1] = 2;
array3[2] = 3;
```

```
// objects
Object object = new Object();
```

- List

```
List<Integer> list = new ArrayList(Arrays.asList(1, 2, 3));
```

- Set

```
// Using Another Collection Instance
Set<Integer> set = new HashSet<>(Arrays.asList(1, 2, 3));

// Using Anonymous Class
Set<Integer> set = new HashSet() {{
        add(1);
        add(2);
}};

// Using Stream Since Java 8
Set<String> set3 = Stream.of("a", "b", "c")
    .collect(Collectors.toCollection(HashSet::new));
```

- Maps

```
// Using Anonymous Class
Map<Integer, String> map = new HashMap<Integer, String>() {{
    put(1, "one");
    put(2, "two");
    put(3, "three");
}};

// Using Stream Since Java 8
Map<String, Integer> map2 = Stream.of(new Object[][] {
    { "key1", 1 },
    { "key2", 2 },
}).collect(Collectors.toMap(data -> (String) data[0], data -> (Integer) data[1]));

// Using a Stream of Map.Entry
Map<String, Integer> map3 = Stream.of(
    new AbstractMap.SimpleEntry<>("key1", 1),
    new AbstractMap.SimpleEntry<>("key2", 2))
    .collect(Collectors.toMap(Map.Entry::getKey, Map.Entry::getValue));
```

#### Initialize constants

- Primitive types

```
public static final byte byteVal = 1;
public static final short shortVal = 1;
public static final int intVal = 1;
public static final long longVal = 1L;
public static final float floatVal = 1.0F;
public static final double doubleVal = 1.0;
public static final boolean booleanVal = true;
public static final char charVal = 'a';
```

- Strings and Arrays

```
public static final int[] MY_ARRAY = {1, 2, 3};
```

- Immutable List

```
// JDK 8 (Don't expose internal_list reference of unmodifiableList(List internale_list)). Arrays.asList() can't increase size, but it can modify its elements.
public static final List UNMODIFY_LIST = Collections.unmodifiableList(Arrays.asList(1, 2, 3));

// JDK 9 (Recommend, less space cost)
public static final List stringList = List.of("a", "b", "c");
```

- Immutable Set

```
// JDK 8
public static final Set<String> stringSet = Collections.unmodifiableSet(new HashSet<>(Arrays.asList("a", "b", "c")));

// JDK 9 (Recommend)
public static final Set<String> stringSet2 = Set.of("a", "b", "c");
```

- Immutable Map

```
// Immutable Map, JDK 8
public static final Map<Integer, String> UNMODIFY_MAP = Collections.unmodifiableMap(
    new HashMap<Integer, String>()
    {
        {
            put(1, "one");
            put(2, "two");
            put(3, "three");
        };
    }); 

// java 9, return ImmutableCollections (Recommend)
public static final Map<Integer, String> my_map2 = Map.of(1, "one", 2, "two");

// java 10, return ImmutableCollections (Recommend)
public static final Map<Integer, String> my_map3 = Map.ofEntries(
    entry(1, "One"), 
    entry(2, "two"), 
    entry(3, "three"));
```

#### Enumeration Type vs Constants

- If number of a set of constant is fixed, you should use `enum` type. 
- If number of a set of constant is increasing and variable, you should use constant variables. 

```
public enum WeekDay {
    MONDAY,
    TUESDAY,
    WEDNESDAY,
    THURSDAY,
    FRIDAY,
    SATURDAY,
    SUNDAY
}
```

```
public static final int MONDAY = 0;
public static final int TUESDAY = 1;
public static final int WEDNESDAY = 2;
public static final int THURSDAY = 3;
public static final int FRIDAY = 4;
public static final int SATURDAY = 5;
public static final int SUNDAY = 6;
```



## Core



## Advanced

### Container

#### Basics

1. forEach() vs stream()

If you just want to consume list, you best to choose forEach(), else stream(). 

#### Remove elements from collection

```
List<Book> books = new ArrayList<Book>();
books.add(new Book(new ISBN("0-201-63361-2")));
books.add(new Book(new ISBN("0-201-63361-3")));
books.add(new Book(new ISBN("0-201-63361-4")));
```

1. Collect objects set and `removeAll()`

- Swap position of elements and create new collection copy from updated old collection. Don't reorganize the collection.
- T(n) = O(n), S(n) = O(n)

```
ISBN isbn = new ISBN("0-201-63361-2");
List<Book> found = new ArrayList<Book>();
for(Book book : books){
    if(book.getIsbn().equals(isbn)){
        found.add(book);
    }
}
books.removeAll(found);
```

1.2 Collect indexes and remove one by one

- T(n) = O(n), S(n) = O(m * n)

1.3 Collect objects set and remove one by one

- T(n) = O(n), S(n) = O(m * n)

2. Using iterator

- Iterator using the `cursor` variable to traverse collection and remove by index of collection. If you remove a element, the cursor will update correctly. Iterator like forEach, but it index is not from 0 to size-1 of collection. The every remove operations will creating a new collection that copy from updated old collection.
- T(n) = O(n), S(n) = O(m * n)

```
ListIterator<Book> iter = books.listIterator();
while(iter.hasNext()){
    if(iter.next().getIsbn().equals(isbn)){
        iter.remove();
    }
}
```

3. `removeIf()` method (JDK 8)

- Swap position of elements, set new size for collection, and set null for between new size to old size elements.
- T(n) = O(n), S(n) = O(1)

```
ISBN other = new ISBN("0-201-63361-2");
books.removeIf(b -> b.getIsbn().equals(other));
```

4. Stream API (JDK 8)

- Creating new collection. Traversing has no order.
- T(n) = O(n), S(n) = O(n) guess by "A stream does not store data and, in that sense, is not a data structure. It also never modifies the underlying data source."

```
ISBN other = new ISBN("0-201-63361-2");
List<Book> filtered = books.stream()
                           .filter(b -> b.getIsbn().equals(other))
                           .collect(Collectors.toList());
```

Recommend: `removeIf()` > `stream()` or `parallelStream()`> Collect objects set and `removeAll()` > Using iterator, or Collect indexes and remove one by one, or Collect objects set and remove one by one.

#### Ordered Collections

1. Sorted Collection Classes

- TreeSet
- TreeMap

2. Inserted order Collection Classes

- LinkedList
- LinkedHashSet
- LinkedHashMap

3. Using `Colletions` `sort()` to sort Comparable elements

It uses merge sort. T(n) = O(log n)

- `sort(List<T> list)`
- `sort(List<T> list, Comparator c)`

Comparators

- `Comparator.naturalOrder()`
- `Collections.reverseOrder()`
- `Collections.reverseOrder(Comparator c)`

Summary: if you don't need to keep collections always be ordered, you just use Collections sort() to get sorted collections.

#### Type Conversation

**To ArrayList**

Convert Set to ArrayList

```java
Set<String> set = new HashSet();
ArrayList<String> list = new ArrayList(set);
```

```java
list.addAll(set);
```

```java
// Java 8
List<String> list = set.stream().collect(Collectors.toList());
```

```java
// Java 10
var list = List.copyOf(set);
```

Convert Array to ArrayList

```java
String[] array = new String[10];
ArrayList<String> list = new ArrayList(Arrays.asList(array));
```

**To Set**

Convert ArrayList to Set

```java
ArrayList<String> list = new ArrayList();
Set<String> set = new HashSet(list);
```

```java
set.addAll(aList);
```

```java
// Java 8
Set<String> set = list.stream().collect(Collectors.toSet());
```

```java
// Java 10
var set = Set.copyOf(list);
```

Convert Array to Set

```java
String[] array = new String[10];
Set<String> set = new HashSet(Arrays.asList(array));
```



## Utility Libraries



## References

Variables and Types

- [Initialize a HashMap in Java](https://www.baeldung.com/java-initialize-hashmap)

Container

- [Remove elements from collection while iterating - Stack Overflow](https://stackoverflow.com/questions/10431981/remove-elements-from-collection-while-iterating)
- [The Collection Interface - The Java Tutorials](https://docs.oracle.com/javase/tutorial/collections/interfaces/collection.html)
- [The Difference Between Collection.stream().forEach() and Collection.forEach()](https://www.baeldung.com/java-collection-stream-foreach)
- Stream API
  - [Java 8 API - Package java.util.stream Description](https://docs.oracle.com/javase/8/docs/api/)
  - [A Guide to Java Streams in Java 8: In-Depth Tutorial With Examples](https://stackify.com/streams-guide-java-8/)

