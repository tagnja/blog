# Deeply Understanding Java Container

Based Java SE 8

Content

- Collection
- List
- Set
- Map
- Queue
- Stack

## Collection

### Collection interface

The root interface in collection hierarchy. A collection represents a group of objects, known as its elements. Some collections allow duplicate elements and others do not. some are ordered and others unordered. The JDK does not provide any direct implementations of this interface. This interface is typically used to pass collections around and manipulate them where maximum generality is desired.

**Methods of Collection**

basic operations

- `boolean add(E e)`
- `boolean addAll(Collection<? extends E> c)`
- `boolean remove(Object o)`
- `boolean removeAll(Collection<?> c)`
- `void clear()`
- `boolean contains(Object o)`
- `boolean contains(Collection<?> c)`
- `int size()`
- `boolean equals(Object o)`
- `boolean isEmpty()`
- `Iterator<E> iterator()`
- `object[] toArray()`
- `<T> T[] toArray(T[] a)`

advanced operations

- `default Stream<E> stream()`
- `default Stream<E> parallelStream()`
- `default boolean removeIf(Predicate<? super E> filter)`
- `boolean retainAll(Collection<?> c)`
- `default Spliterator<E> spliterator()`

### AbstractCollection

This class provides a skeletal implementation of the `Collection` interface, to minimize the effort required to implement `Collection` interface.

Most of methods implementation of `AbstractCollection` depend on its abstract methods `iterator()` and `size()`. These two methods are the most important and useful for collection classes.

Non-abstract methods in this class may be overridden if its subclass implemented admits a more efficient implementation.

## List

Subclass of `List`: `AbstractList`, `ArrayList`, `Vector`, `AbstractSequentialList`, `LinkedList`, `ConyOnWriteArrayList`.

### List interface

This interface control over where to insert. The user can access elements by their integer index, and search for elements in the list.

The List interface provides a special iterator, called a `ListIterator`, that allows element insertion `add(E e)` and replacement `set(E e)`, and bidirectional access `previous()` in addition to the normal operations that the Iterator interface provides.

**Additional methods of List**

- `E get(int index)`
- `int indexOf(Object o)`
- `int lastIndexOf(Object o)`
- `ListIterator listIterator()`
- `void replaceAll(UnaryOperator operator)`
- `E set(int index, E element)`
- `void sort(Comparator c)`
- `List<E> sublist(int fromIndex, int toIndex)`

### AbstractList

This class provides a skeletal implementation of the List interface to minimize the effort required to implement this interface backed by a "random access" data store (such as an array).

### AbstractSequentialList

This class provides a skeletal implementation of the `List` interface to minimize the effort required to implement this interface backed by a "sequential access" data store (such as a linked list).

### ArrayList & Vector

Resizable-array implementation of the `List` interface. Implements all optional list operations, and permits all elements, including `null`. In addition to implementing the `List` interface, this class provides methods to manipulate the size of the array that is used internally to store the list. 

`ArrayList` class is roughly equivalent to **`Vector`**, except that it is unsynchronized.

Each ArrayList instance has a **capacity**. The capacity is the size of the array used to store the elements in the list. It is always at least as large as the list size. As elements are added to an ArrayList, its capacity grows automatically. The details of the growth policy are not specified beyond the fact that adding an element has constant amortized time cost.

An application can increase the capacity of an `ArrayList` instance before adding a large number of elements using the **`ensureCapacity`** operation. This may reduce the amount of incremental reallocation.

**Note that this implementation is not synchronized.** If multiple threads access an `ArrayList` instance concurrently, and at least one of the threads modifies the list structurally, it *must* be synchronized externally. If the list used in multithread, the list should be "wrapped" using the [`Collections.synchronizedList`](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedList-java.util.List-) method. This is best done at creation time, to prevent accidental unsynchronized access to the list.

The iterators returned by this class's [`iterator`](https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html#iterator--) and [`listIterator`](https://docs.oracle.com/javase/8/docs/api/java/util/ArrayList.html#listIterator-int-) methods are **fail-fast**: if the list is structurally modified at any time after the iterator is created, in any way except through the iterator's own [`remove`](https://docs.oracle.com/javase/8/docs/api/java/util/ListIterator.html#remove--) or [`add`](https://docs.oracle.com/javase/8/docs/api/java/util/ListIterator.html#add-E-) methods, the iterator will throw a [`ConcurrentModificationException`](https://docs.oracle.com/javase/8/docs/api/java/util/ConcurrentModificationException.html). Thus, in the face of concurrent modification, the iterator fails quickly and cleanly, rather than risking arbitrary, non-deterministic behavior at an undetermined time in the future.

### LinkedList

Doubly-linked list implementation of the List and Deque interfaces. 

All of the operations perform as could be expected for a doubly-linked list. Operations that index into the list will **traverse** the list from the beginning or the end, whichever is closer to the specified index.

**Note that this implementation is not synchronized.** If multiple threads access a linked list concurrently, and at least one of the threads modifies the list structurally, it *must* be synchronized externally. If the list used in multithread, the list should be "wrapped" using the [`Collections.synchronizedList`](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedList-java.util.List-) method. This is best done at creation time, to prevent accidental unsynchronized access to the list.

The iterators returned by this class's `iterator` and `listIterator` methods are **fail-fast**. Same with ArrayList.

### CopyOnWriteArrayList

**What is `CopyOnWriteArrayList`**

`java.util.concurrent.CopyOnWriteArrayList` a thread-safe variant of ArrayList in which all mutative operation (add, set, and so on) are implemented by making a fresh copy of the underlying array.

> This is ordinarily too costly, but may be more efficient than alternatives when traversal operations vastly outnumber mutations, and is useful when you cannot or don't want to synchronize traversals, yet need to preclude interference among concurrent threads. The "snapshot" style iterator method uses a reference to the state of the array at the point that the iterator was created. This array never changes during the lifetime of the iterator, so interference is impossible and the iterator is guaranteed not to throw `ConcurrentModificationException`. The iterator will not reflect additions, removals, or changes to the list since the iterator was created. Element-changing operations on iterators themselves (`remove`, `set`, and `add`) are not supported. These methods throw `UnsupportedOperationException`.

**Why is the `CopyOnWriteArrayList` Thread-safe**

All write operations have a lock. The write operations are serializable.

All mutative operations operate on new copy list and then update volatile list reference. Array update is an atomic operation and hence reads will always see the array in a consistent state.

**Applicability**

Updating this collection a lot will kill performance. You should only use this read when you are doing upwards of 90+% reads. 

**Consequences**

The all read and write operations of `CopyOnWriteArrayList` are thread-safe. Some of methods have a `ReentrantLock` and copy list, some have not. 

It highly improves concurrency of read operations in thread-safe condition, but write operations is very costly.

**Questions**

Q: Why all mutative operations need to copy list? Why read and traversal operation not need locks?

A: The key to this idea is that writes are accomplished by copying the existing value, modifying it, and replacing the reference. It also follows that once set the object pointed by the reference is always **read only** (i.e. no mutation is done directly on the object referred by the reference). Therefore, readers can access it safely without synchronization.

## Queue

Hierarchy of `Queue`: 

- I-`java.util.Queue`
  - `java.util.AbstractQueue`
    - `java.util.PriorityQueue`
  - I-`java.util.Deque`
    - `java.util.ArrayDeque`
  - I-`java.util.concurent.BlockingQueue`
    - I-BlockingDeque
      - LinkedBlockingDeque
    - I-TransferQueue
      - LinkedTransferQueue
    - ArrayBlockingQueue
    - LinkedBlockingQueue
    - DelayQueue
    - PriorityBlockingQueue
    - SynchronousQueue
  - `java.util.concurrent.ConcurrentLinkedQueue`
  - `java.util.concurrent.ConcurrentLinkedDeque`

### Queue interface

### Deque interface

### BlockingQueue interface

### Concurrent Queues



## Stack



## Set

- I-`Set`
  - `AbstractSet`
    - `java.util.concurrent.CopyOnWriteArraySet`
    - `EnumSet`
    - `HashSet`
      - `LinkedHashSet`
  - I-`SortedSet`
    - I-`NavigableSet`
      - `TreeSet`
      - `java.util.concurrent.ConcurrentSkipListSet`

## Map

Hierarchy of Map

- I-`Map`
  - `Hashtable`
  - `AbstractMap`
    - `HashMap`
      - `LinkedHashMap`
    - `EnumMap`
    - `IdentityHashMap`
    - `WeakHashMap`
  - I-`SortedMap`
    - I-`NavigableMap`
      - `TreeMap`
  - I-`java.util.concurrent.ConcurrentMap`
    - `ConcurrentHashMap`
    - I-`ConcurrentNavigableMap`
      - `ConcurrentSkipListMap`

### Map interface

### AbstractMap

### HashMap

### EnumMap

### IdentityHashMap

### WeakHashMap

### SortedMap

### TreeMap

### Concurrent Maps



## References

[1] [Java SE 8 API Documentation](https://docs.oracle.com/javase/8/docs/api/)

[2] Java Source Code - `java.util.*`, `java.util.concurrent.*`

[3] [Why CopyOnWriteArrayList copys when writing?](https://stackoverflow.com/questions/18973925/why-copyonwritearraylist-copys-when-writing)

[4] [In what situations is the CopyOnWriteArrayList suitable?](https://stackoverflow.com/questions/17853112/in-what-situations-is-the-copyonwritearraylist-suitable)

[5] [How can CopyOnWriteArrayList be thread-safe?](https://stackoverflow.com/questions/2950871/how-can-copyonwritearraylist-be-thread-safe)

[6] [ReentrantLock Example in Java, Difference between synchronized vs ReentrantLock](https://javarevisited.blogspot.com/2013/03/reentrantlock-example-in-java-synchronized-difference-vs-lock.html)

--END--