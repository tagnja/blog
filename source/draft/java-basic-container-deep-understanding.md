# Deeply Understanding Java Container

Based Java SE 8

Content

- Collection
- List
- Queue
- Stack
- Set
- Map
- Utility Classes

## Collection

### Collection interface

The root interface in collection hierarchy. A collection represents a group of objects, known as its elements. Some collections allow duplicate elements and others do not. some are ordered and others unordered. The JDK does not provide any direct implementations of this interface. This interface is typically used to pass collections around and manipulate them where maximum generality is desired.

**Methods of Collection**

Basic Operations

- `boolean add(E e)`, `boolean addAll(Collection<? extends E> c)`
- `boolean remove(Object o)`, `boolean removeAll(Collection<?> c)`
- `void clear()`
- `boolean contains(Object o)`, `boolean contains(Collection<?> c)`
- `int size()`
- `boolean equals(Object o)`
- `boolean isEmpty()`
- `Iterator<E> iterator()`
- `object[] toArray()`, `<T> T[] toArray(T[] a)`

Advanced Operations

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

Hierarchy of `List`:

```
(I)List
|----(A)AbstractList
|--------ArrayList
|--------Vector
|--------(A)AbstractSequentialList
|------------LinkedList
|----java.util.concurrent.CopyOnWriteArrayList
```

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

```
(I)java.util.Queue
|----(A)java.util.AbstractQueue
|--------java.util.PriorityQueue
|----(I)java.util.Deque
|--------java.util.ArrayDeque
|----(I)java.util.concurent.BlockingQueue
|--------(I)BlockingDeque
|------------LinkedBlockingDeque
|--------(I)TransferQueue
|------------LinkedTransferQueue
|--------ArrayBlockingQueue
|--------LinkedBlockingQueue
|--------PriorityBlockingQueue
|--------DelayQueue
|--------SynchronousQueue
|----java.util.concurrent.ConcurrentLinkedQueue
|----java.util.concurrent.ConcurrentLinkedDeque
```

### Queue interface

A group of elements in First in first out manner.

The `Queue` interface does not define the blocking queue methods, which are common in concurrent programming.

Queue implementations generally do not allow insertion of null elements, although some implementations do not prohibit. Null should not be inserted into a queue, as null is also used as a special return value by the poll method to indicate that the queue contains no elements.

**Methods of `Queue`**

- insert
  - `boolean add(E e)` - Inserts element into this queue.
  - `boolean offer(E e)` - It is similar with `add()`.
- remove
  - `E poll()` - Retrieves and remove the head of this queue.
  - `E reomve()` - It is similar with `poll()`
- examine
  - `E element()` - It is similar with `peek()`.
  - `E peek()` - Retrieves, but does not remove, the head of this queue.

### Deque interface

A group of elements supports element insertion and removal at both head and tail. Deque can also be used as LIFO (last-in-First-out) stacks.

The name deque is short for "double ended queue" and is usually pronounced "deck".

**Methods of `Deque`**

- insert
  - `boolean add(E e)`, `void addFirst(E e)`, `void addLast(E e)`
  - `boolean offer(E e)`, `boolean offerFirst(E e)`, `boolean offerLast(E e)`
  - `void push(E e)`
- remove
  - `E remove()`, `E removeFirst()`, `E removeLast()`
  - `boolean removeFirstOccurrence(Object o)`, `boolean removeLastOccurrence(Object o)`
  - `E poll()`, `E pollFirst()`, `E pollLast()`
  - `E pop()`
- examine
  - `E getFirst()`, `E getLast()`
  - `E peek()`, `E peekFirst()`, `E peekLast()`
  - `E element()`
- `boolean contains(Object o)`
- `Iterator descendingIterator()`
- `Iterator iterator()`
- `int size()`

### BlockingQueue interface

`java.util.concurrent.BlockingQueue` is a queue additionally supports operations that wait for the queue to become non-empty when retrieve an element, and wait for space to become available in the queue when storing an element.

`BlockingQueue` implementations are designed to be used primarily for producer-consumer queues. A `BlockingQueue` can safely be used with multiple producers and multiple consumers.

`BlockingQueue` implementations are thread-safe. All `queuing methods` achieve their effects atomically using internal locks or other forms of concurrency control. However, the bulk Collection operations addAll, containsAll, retainAll and removeAll are not necessarily performed atomically. So it is possible, for example, for addAll(c) to fail after adding only some of the elements in c.

**Methods of `BlockingQueue`**

- insert
  - `boolean add(E e)`
  - `boolean offer(E e)`
  - `void put(E e)` - blocks
  - `boolean offer(E e, long time, TimeUnit unit) `- blocks and times out
- remove
  - `boolean remove(Object o)`
  - `E poll()`
  - `E take()` - blocks
  - `E poll(long time, TimeUnit unit)` - blocks and times out
- examine
  - `E element()`
  - `E peek()`
- `boolean contains(Object o)`
- `drainTO(Collection c)`
- `int remainingCapacity()`

Usage example of producer-consumer

```java
class Producer implements Runnable {
   private final BlockingQueue queue;
   Producer(BlockingQueue q) { queue = q; }
   public void run() {
     try {
       while (true) { queue.put(produce()); }
     } catch (InterruptedException ex) { ... handle ...}
   }
   Object produce() { ... }
 }

 class Consumer implements Runnable {
   private final BlockingQueue queue;
   Consumer(BlockingQueue q) { queue = q; }
   public void run() {
     try {
       while (true) { consume(queue.take()); }
     } catch (InterruptedException ex) { ... handle ...}
   }
   void consume(Object x) { ... }
 }

 class Setup {
   void main() {
     BlockingQueue q = new SomeQueueImplementation();
     Producer p = new Producer(q);
     Consumer c1 = new Consumer(q);
     Consumer c2 = new Consumer(q);
     new Thread(p).start();
     new Thread(c1).start();
     new Thread(c2).start();
   }
 }
```

### BlockingDeque interface

It is the combination of `BlockingQueue` and `Deque` interface. You can use this interface for both blocking queues and blocking stacks.

### TransferQueue interface

The `TransferQueue` interface is a refinement of the `BlockingQueue` interface in which producers can wait for consumers to receive elements. The `BlockingQueue` can only put element into queue (and block if queue is full). However, with `TransferQueue`, you can also block producer (whether the queue full or not) until other consumer threads receive your element.

**Methods of `TransferQueue`**

- `int getWaitingConsumerCount()` - Returns an estimate of the number of consumers waiting to receive elements via take() or timed poll.
- `boolean hasWaitingConsumer()` - Returns true if there is at least one consumer waiting to receive an element via take() or timed poll.
- `void transfer(E e)` - Transfers the element to a consumer, waiting if necessary to do so.
- `boolean tryTransfer(E e)` - Transfers the element to a waiting consumer immediately, if possible.
- `boolean tryTransfer(E e, long timeout, TimeUnit unit)` - Transfers the element to a consumer if it is possible to do so before the timeout elapses.

Usage Example of `TransferQueue`

```java
public class TransferQueueExample {

    TransferQueue<String> queue = new LinkedTransferQueue<String>();

    class Producer implements Runnable{
        @Override
        public void run() {
            for(int i = 0; i < 2; i++){
                try{
                    System.out.println("Producer waiting to transfer: " + i);
                    queue.transfer("" + i);
                    System.out.println("Producer transfered: " + i);
                }catch(Exception e){
                    e.printStackTrace();
                }
            }
        }

    }

    class Consumer implements Runnable{
        @Override
        public void run() {
            for(int i = 0; i < 2; i++){
                try{
                    Thread.sleep(2000);
                    System.out.println("Consumer waiting to comsume: " + i);
                    queue.take();
                    System.out.println("Consumer consumed: " + i);
                }catch(Exception e){
                    e.printStackTrace();
                }
            }
        }
    }

    public static void main(String args[]){
        TransferQueueExample example = new TransferQueueExample();
        new Thread(example.new Producer()).start();
        new Thread(example.new Consumer()).start();
    }
}
```

The output is:

```java
Producer waiting to transfer: 0
Consumer waiting to comsume: 0
Consumer consumed: 0
Producer transfered: 0
Producer waiting to transfer: 1
Consumer waiting to comsume: 1
Consumer consumed: 1
Producer transfered: 1
```

Producer thread is blocked before consumer thread take product away.

### AbstractQueue

This class provides skeletal implementations of some `Queue` operations. Its implemented methods actually implementing by calling abstract methods `offer()`, `poll()`, `peek()`.

### PriorityQueue

An unbounded priority queue based on a priority heap. The elements of the priority queue are ordered according to their natural ordering (ascending order. sort by e1.compareTo(e2)), or by a [`Comparator`](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html) provided at queue construction time, depending on which constructor is used. A priority queue does not permit `null` elements. A priority queue relying on natural ordering also does not permit insertion of non-comparable objects.

Its implementation provides O(log(n)) time for the enqueuing and dequeuing methods (`offer`, `poll`, `remove()` and `add`); linear time for the `remove(Object)` and `contains(Object)` methods; and constant time for the retrieval methods (`peek`, `element`, and `size`).

### Concurrent Queues

### DelayQueue

An unbounded blocking queue of Delayed elements, in which an element can only be taken when its delay has expired. If no delay has expired there is no head and poll will return null.

`DelayQueue<E extends Delay>` is a `PriorityQueue` order by `Delayed`.

Methods of Delayed interface

- `long getDelay(TimeUnit unit)` - Returns the remaining delay associated with this object, in the given time unit.
- `int compareTo(Delayed obj)`

Methods of `DelayQueue` similar with `BlockingQueue`.

Usage Example

```java
class MyDelayed implements Delayed {
    private String name;
    private long time;

    public MyDelayed(String name, long delayMillisecond) {
        this.name = name;
        this.time = System.currentTimeMillis() + delayMillisecond;
    }

    @Override
    public long getDelay(TimeUnit unit) {
        return unit.convert(time - System.currentTimeMillis(), TimeUnit.MILLISECONDS);
    }

    @Override
    public int compareTo(Delayed o) {
        return (int) (this.getDelay(TimeUnit.MILLISECONDS) - o.getDelay(TimeUnit.MILLISECONDS));
    }
    
    @Override
    public String toString() {
        return "MyDelayed{" + "name='" + name + '\'' + ", time=" + time + '}';
    }
}

public class Test{
    public static void main(String[] args) throws InterruptedException {
        DelayQueue<MyDelayed> delayQueue = new DelayQueue();
        // offer
        delayQueue.offer(new MyDelayed("B", 3000L));
        delayQueue.offer(new MyDelayed("A", 2L));
        // poll
        // waiting for the delay is expired to poll
        Thread.sleep(2L);
        System.out.println("before poll");
        // Output: MyDelayed{name='A', time=1587625828209}
        System.out.println(delayQueue.poll()); 
        // Output is null, because the delay is not expired
        System.out.println(delayQueue.poll());
    }
}
```

### SynchronousQueue

A blocking queue in which each insert operation must wait for a corresponding remove operation by another thread, and vice verse. A synchronous queue does not have any internal capacity.

The insert and remove operations of `SynchronousQueue` may block its thread. The insert and remove operations must occur at the same time, and the fast thread will block to wait for another operation thread.

```java
public static void main(String[] args) throws InterruptedException {
    SynchronousQueue<Integer> synchronousQueue = new SynchronousQueue<>();
    new Thread(()->{
        try {
            Thread.sleep(1000L);
            System.out.println("the put to wait for remove...");
            synchronousQueue.put(1);
            System.out.println("end to put.");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }).start();
    new Thread(()->{
        try {
            System.out.println("the remove to wait for put...");
            System.out.println(synchronousQueue.take());
            System.out.println("end to remove.");
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }).start();
}
```

Output

```java
the remove to wait for put...
the put to wait for remove...
end put.
1
end remove.
```

### ConcurrentLinkedQueue

An unbound thread-safe queue based on linked nodes. This queue orders elements FIFO (first-in-first-out). A `ConcurrentLinkedQueue` is an appropriate choice when many threads will share access to a common collection.

## Stack

Hierarchy of `Stack`:

```
java.util.Stack
java.util.Deque
java.util.LinkedList
java.util.ArrayList
```

`java.util.Stack` class represents a last-in-first-out sequence. It extends class Vector. It's thread-safe implemented by synchronized methods. 

If you don't operate in a multithread environment, you should use `Deque` as `Stack` that is fast than `Stack`.

Methods of  `Stack`

- `E peek()`
- `E pop()`
- `E push(E item)`
- `boolean empty()`
- `int search(Object o)`

## Set

Hierarchy of `Set`:

```
(I)Set
|----(A)AbstractSet
|--------HashSet
|------------LinkedHashSet
|--------(A)EnumSet
|--------java.util.concurrent.CopyOnWriteArraySet
|----(I)SortedSet
|--------(I)NavigableSet
|------------TreeSet
|------------java.util.concurrent.ConcurrentSkipListSet
```

### Set Interface

A collection that contains no duplicate elements, and at most one null element.

Methods of Set interface

- `boolean add(E e)`, `addAll(Collection c)`
- `void clear()`
- `boolean contains(Object o)`, `containsALl(Collection c)`
- `boolean equals(Object o)`
- `int hashCode()`
- `boolean isEmpty()`
- `Iterator iterator()`
- `boolean remove(Object o)`, `removeAll(Collection c)`
- `boolean retainAll(Collection c)`
- `int size()`
- `default Spliterator spliterator()`
- `Object[] toArray()`, `T[] toArray(T[] a)`

The Set interface does not have any `get` or `find` methods to find an element from the Set container. The only way to find the specified element is by using its iterator to traverse the Set.

### AbstractSet  Class

This class provides a skeletal implementation of the Set interface to minimize the effort required to implement this interface.

Implemented methods of Set interface: 

- `equals(Object o)`
- `hashCode()`
- `removeAll(Collection c)`

### HashSet Class

This class implements the Set interface, backed by a hash table (actually a HashMap instance). It makes no guarantees as to the iteration order of the set.

This class offers constant time performance for the basic operation (add, remove, contains and size), assuming the hash function disperses the elements properly among the buckets.

This implementation is not synchronized.

This class's iterator is fail-fast.

### LinkedHashSet

Hash table and linked list implementation of Set interface, with predictable iteration order. This implementation differs from `HashSet` in that it maintains a doubly-linked list running through all of its entries. This linked list defines the iteration ordering, which is insertion-order.

### EnumSet Abstract Class

A specialized Set implementation for use with enum types. All of the elements in an enum set must come from a single enum type. Enum sets are represented internally as bit vectors. This representation is extremely compact and efficient.

### SortedSet Interface

SortedSet is a Set that provides a total ordering on its elements. The elements are ordered using their natural ordering, or by a Comparator typically provided at sorted set creation time. The set's iterator will traverse the set in ascending element order.

### NavigableSet interface

NavigableSet is a SortedSet textended with navigation methods reporting closest matches for given search targets. Methods lower, floor, ceiling, and higher return elements respectively less than, less than or equal, greater than or equal, and greater than a given element, returning null if there is no such element. A NavigableSet may be accessed and traversed in either ascending or descending order. 

Methods of NavigableSet interface

- Navigation
  - `E ceiling(E e)`
  - `E floor(E e)`
  - `E higher(E e)`
  - `E lower(E e)`
  - `E pollFirst()`
  - `E pollLast()`
- Iterator
  - `Iterator descendingIterator()`'
  - `Iterator iterator()`
- Subset
  - `SortedSet headSet(E toElement)`, `NavigableSet headSet(E toElement, boolean inclusive)`
  - `SortedSet tailSet(E fromElement)`, `NavigableSet tailSet(E fromElement, boolean inclusive)`
  - `SortedSet subSet(E fromElement, E toElement)`, `NavigableSet subSet(E fromElement, boolean fromInclusive, E toElement, boolean toInclusive)`
- Reverse
  - `NavigableSet descendingSet()`

### TreeSet Class

`TreeSet` is a `NavigableSet` implementation based on `TreeMap`. The elements are ordered using their natural ordering, or by a Comparator provided at set creation time, depending on which constructor is used.

This implementation provides guaranteed log(n) time cost for the basic operations (add, remove, and contains).

This implementation is not synchronized. If you want to use TreeSet in multithread environment, you can using the `Collections.synchronizedSortedSet(new TreeSet())` to wrap it.

### Concurrent Set

**CopyOnWriteArraySet**

`CopyOnWriteArraySet` is a Set that uses an internal `CopyOnWriteArrayList` for all of its operations.

It shares basic properties:

- It is best suited for applications in which set sizes generally stay small, read-only operations vastly outnumber mutative operations, and you need to prevent interference among threads during traversal.
- It is thread-safe.
- Mutative operations (`add`, `set`, `remove`, etc.) are expensive since they usually entail copying the entire underlying array.
- Iterators do not support the mutative `remove` operation.
- Traversal via iterators is fast and cannot encounter interference from other threads. Iterators rely on unchanging snapshots of the array at the time the iterators were constructed.

**ConcurrentSkipListSet**

`ConcurrentSkipListSet` is a scalable concurrent `NavigableSet` implementation based on a `ConcurrentSkipListMap`. The elements of the set are kept sorted according to their natural ordering, or by a Comparator provided at set creation time, depending on which constructor is used.

This implementation provides expected average *log(n)* time cost for the `contains`, `add`, and `remove` operations and their variants. Insertion, removal, and access operations safely execute concurrently by multiple threads.

Iterators and spliterators are [*weakly consistent*](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/package-summary.html#Weakly).

Beware that, unlike in most collections, the `size` method is *not* a constant-time operation. Because of the asynchronous nature of these sets, determining the current number of elements requires a traversal of the elements, and so may report inaccurate results if this collection is modified during traversal. Additionally, the bulk operations `addAll`, `removeAll`, `retainAll`, `containsAll`, `equals`, and `toArray` are *not* guaranteed to be performed atomically. For example, an iterator operating concurrently with an `addAll` operation might view only some of the added elements.

## Map

Hierarchy of `Map`:

```
(I)Map
|----Hashtable
|----(A)AbstractMap
|--------HashMap
|------------LinkedHashMap
|--------EnumMap
|--------IdentityHashMap
|--------WeakHashMap
|----(I)SortedMap
|--------(I)NavigableMap
|------------TreeMap
|----(I)java.util.concurrent.ConcurrentMap
|--------ConcurrentHashMap
|--------(I)ConcurrentNavigableMap
|------------ConcurrentSkipListMap
```

### Map interface

An object that maps keys to values. A map connot contain duplicate keys, and each key can map to at most one value.

The Map interface provides three collection views, a set of keys, collection of values, or set of key-value mappings. 

Methods of Map interface

- Basic

  - `V get(Object key)`
  - `V put(K key, V value)`
  - `V remove(Object key)`
  - `int size()`
  - `boolean isEmpty()`
  - `void clear()`
  - `boolean containsKey(Object key)`
  - `boolean containsValue(Object value)`
  - `void putAll(Map m)`
  - `default V putIfAbsent(K key, V value)`
  - `default V getOrDefault(Object key, V defaultValue)`
  - `default boolean remove(Object key, Object value)`
  - `default V replace(K key, V value)`
  - `default boolean replace(K key, V oldValue, V newValue)`

- View

  - `Set entrySet()`
  - `Set keySet()`
  - `Collection values()`

- Function

  - `default V compute(K key, BiFunction remappingFunction)` - Attempts to compute a mapping for the specified key and its current mapped value. `computeIfAbsent(...)`, `computeIfPresent(...)`
  - `default void forEach(BiConsumer action)`
- `default V merge(K key, V value, BiFunction remappingFunction)`
  - `default void replaceAll(BiFunction function)`

Note that map means mapping keys to values, it does not specify how to mapping. The hash function is one of the ways to map keys to values.

### HashTable

This class implements a hash table, which maps keys to values.

`Hashtable` is synchronized. If a thread-safe implementation is not needed, it is recommended to use [`HashMap`](https://docs.oracle.com/javase/8/docs/api/java/util/HashMap.html) in place of `Hashtable`. If a thread-safe highly-concurrent implementation is desired, then it is recommended to use [`ConcurrentHashMap`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentHashMap.html) in place of `Hashtable`.

### AbstractMap

This class provides a skeletal implementation of the Map interface, to minimize the effort required to implement this interface.

It simply implemented some methods of Map just using iterator, like `get(), containsKey()`. 

### HashMap

Hash table based implementation of the Map interface. It permits null values and null key. This class makes no guarantees order of the map. The `HashMap` class is roughly equivalent to `HashTable`, except that it is unsynchronized and permit nulls.

This implementation is not synchronized. If want to use a `HashMap` object in multithread environment, you can using `Collections.synchronizeMap()` to wrap it.

### EnumMap

A specialized Map implementation for use with enum type keys. All of the keys in an enum map must come from a single enum type that is specified, explicitly or implicitly, when the map is created.

Enum maps are maintained in the *natural order* of their keys (the order in which the enum constants are declared). This is reflected in the iterators returned by the collections views ([`keySet()`](https://docs.oracle.com/javase/8/docs/api/java/util/EnumMap.html#keySet--), [`entrySet()`](https://docs.oracle.com/javase/8/docs/api/java/util/EnumMap.html#entrySet--), and [`values()`](https://docs.oracle.com/javase/8/docs/api/java/util/EnumMap.html#values--)).

Iterators returned by the collection views are *weakly consistent*: they will never throw [`ConcurrentModificationException`](https://docs.oracle.com/javase/8/docs/api/java/util/ConcurrentModificationException.html) and they may or may not show the effects of any modifications to the map that occur while the iteration is in progress.

Null keys are not permitted. Attempts to insert a null key will throw [`NullPointerException`](https://docs.oracle.com/javase/8/docs/api/java/lang/NullPointerException.html).

Like most collection implementations `EnumMap` is not synchronized. If want to use a `EnumMap` object in multithread environment, you can using `Collections.synchronizeMap()` to wrap it.

### IdentityHashMap

This class implements the Map interface with a hash table, using reference-equality in place of object-equality when comparing keys (and values). In other words, in an `IdentityHashMap`, two keys `k1` and `k2` are considered equal if and only if `(k1==k2)`. For example, the key new String("a") and key "a" will different key in `IdentityHashMap`, but it's same key in `HashMap`.

This class is *not* a general-purpose `Map` implementation! While this class implements the `Map` interface, it intentionally violates `Map's` general contract, which mandates the use of the `equals` method when comparing objects. This class is designed for use only in the rare cases wherein reference-equality semantics are required.

This implementation is not synchronized. If want to use a `IdentityHashMap` object in multithread environment, you can using `Collections.synchronizeMap()` to wrap it.

### WeakHashMap

Hash table based implementation of the `Map` interface, with *weak keys*. An entry in a `WeakHashMap` will automatically be removed when its key is no longer in ordinary use. More precisely, the presence of a mapping for a given key will not prevent the key from being discarded by the garbage collector, that is, made finalizable, finalized, and then reclaimed. When a key has been discarded its entry is effectively removed from the map, so this class behaves somewhat differently from other `Map` implementations.

Each key object in a `WeakHashMap` is stored indirectly as the referent of a weak reference. Therefore a key will automatically be removed only after the weak references to it, both inside and outside of the map, have been cleared by the garbage collector.

Like most collection classes, this class is not synchronized. A synchronized `WeakHashMap` may be constructed using the [`Collections.synchronizedMap`](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedMap-java.util.Map-) method.

**Applicability**

When you want to automatically remove useless objects from map, you can use `WeakHashMap`. You can manually remove elements of any types of map, but if don't or you forget it. It may cause a memory leak.

**How does the `WeakHashMap` Implement?**

`java.lang.ref.WeakReference`

### SortedMap interface

A [`Map`](https://docs.oracle.com/javase/8/docs/api/java/util/Map.html) that further provides a *total ordering* on its keys. The map is ordered according to the [natural ordering](https://docs.oracle.com/javase/8/docs/api/java/lang/Comparable.html) of its keys, or by a [`Comparator`](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html) typically provided at sorted map creation time. This order is reflected when iterating over the sorted map's collection views (returned by the `entrySet`, `keySet` and `values` methods). Several additional operations are provided to take advantage of the ordering.

### NavigableMap interface

A [`SortedMap`](https://docs.oracle.com/javase/8/docs/api/java/util/SortedMap.html) extended with navigation methods returning the closest matches for given search targets. Methods `lowerEntry`, `floorEntry`, `ceilingEntry`, and `higherEntry` return `Map.Entry` objects associated with keys respectively less than, less than or equal, greater than or equal, and greater than a given key, returning `null` if there is no such key. Similarly, methods `lowerKey`, `floorKey`, `ceilingKey`, and `higherKey` return only the associated keys. All of these methods are designed for locating, not traversing entries.

### TreeMap

A Red-Black tree based [`NavigableMap`](https://docs.oracle.com/javase/8/docs/api/java/util/NavigableMap.html) implementation. The map is sorted according to the [natural ordering](https://docs.oracle.com/javase/8/docs/api/java/lang/Comparable.html) of its keys, or by a [`Comparator`](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html) provided at map creation time, depending on which constructor is used.

This implementation provides guaranteed log(n) time cost for the `containsKey`, `get`, `put` and `remove` operations.

This class is not synchronized. A synchronized `TreeMap` may be constructed using the [`Collections.synchronizedMap`](https://docs.oracle.com/javase/8/docs/api/java/util/Collections.html#synchronizedMap-java.util.Map-) method.

### Concurrent Maps

**ConcurrentHashMap**

What is it

A concurrent map implements by hash table, and supports full concurrency of retrievals and high concurrency for updates. It likes `Hashtable`, but it has more concurrency.

A hash table supporting full concurrency of retrievals and high expected concurrency for updates. This class obeys the same functional specification as [`Hashtable`](https://docs.oracle.com/javase/8/docs/api/java/util/Hashtable.html), and includes versions of methods corresponding to each method of `Hashtable`. However, even though all operations are thread-safe, retrieval operations do *not* entail locking, and there is *not* any support for locking the entire table in a way that prevents all access. This class is fully interoperable with `Hashtable` in programs that rely on its thread safety but not on its synchronization details.

**ConcurrentSkipListMap**

A scalable concurrent [`ConcurrentNavigableMap`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentNavigableMap.html) implementation. The map is sorted according to the [natural ordering](https://docs.oracle.com/javase/8/docs/api/java/lang/Comparable.html) of its keys, or by a [`Comparator`](https://docs.oracle.com/javase/8/docs/api/java/util/Comparator.html) provided at map creation time, depending on which constructor is used.

## Utility Classes

### Collections

**What Is It**

This class consists of static methods that operate on or return collections.

**Methods of `Collections`**

- Operations
  - `static boolean addAll(Collection c, T... elements)`
  - `static int binarySearch(List list, T key)`, `binarySearch(List list, T key, Comparator c)`
  - `static void copy(List dest, List src)`
  - `static boolean disjoint(Collection c1, Collection c2)`
  - `static void fill(List list, T obj)`
  - `static int frequency(Collection c, Object o)`
  - `static int indexOfSubList(List source, List target)`, `int lastIndexOfSubList(List source, List target)`
  - `static T max(Collection c)`, `max(Collection coll, Comparator comp)`
  - `static T min(Collection c)`, `min(Collection coll, Comparator comp)`
  - `static boolean replaceAll(List list, T oldVal, T newVal)`
  - `static void reverse(List list)`
  - `static Comparator reverseOrder()`, `reverseOrder(Comparator comp)`
  - `static void rotate(List list, int distance)`
  - `static void shuffle(List list)`, `shuffle(List list, Random rnd)`
  - `static void sort(List list)`, `sort(List list, Comparator comp)`
  - `static void swap(List list, int i, int j)`
- Transforms
  - `static Queue asLifoQueue(Deque deque)`
  - `static Collection checkedCollection(Collection c, Class type)`, `checkedList(List list, Class type)`, `checkedMap`, `checkedNavigableMap`, ...
  - `static Enumeration enumeration(Collecdtion c)`
  - `static ArrayList List(Enumeration e)`
  - `static newSetFromMap(Map map)`
  -  `static Collection synchronizedCollection(Collection c)`, `synchronizedList(List list)`, `synchronizedMap(Map map)`, `synchronizedSet(Set set)`, ...
  - `static Collection unmodifiableCollection(Collection)`, `unmodifiableList(List list)`, `unmodifiableMap(Map map)`, ...
- Creations
  - `static List emptyList()`, `emptyMap()`, `emptySet()`, ...
  - `static List nCopies(int n, T o)`
  - `static Set singleton(T o)`, `singletonList(T o)`, `singletonMap(K key, V value)`

### Arrays 

**What Is It**

This class contains various static methods for manipulating arrays (such as sorting and searching). This class also contains a static factory that allows arrays to be viewed as lists.

**Methods of `Arrays`**

- Manipulations
  - `static int binarySearch(byte[] a, byte key)`, `binarySearch(byte[] a, int fromIndex, int toIndex, byte key)`, `binarySearch(char[] a, char key)`, `binarySearch(int[] a, int key)`, ...
  - `static boolean deepEquals(Object[] a1, object[] a2)`
  - `static int deepHashcode(Object[] a)`
  - `static String deepToString(Object[] a)`
  - `static boolean equals(int[] a, int[] a2)`, `equals(byte[] a, byte[] a2)`, ...
  - `static void fill(int[] a, int val)`, `fill(byte[] a, byte val)`, ...
  - `static int hashcode(int[] a)`, `hashcode(byte[] a)`, ...
  - `static void parallelPrefix(int[] array, IntBinaryOperator op)`, ...
  - `static void setAll(int[] a, IntUnaryOperator generator)`, ...
  - `static void parallelSetAll(int[] a, IntUnaryOperator generator)`, ...
  - `static void sort(int[] a)`, `sort(int[] a, int fromIndex, int toIndex)`, ...
  - `static void parallelSort(int[] a)`, `parallelSort(int[] a, int fromIndex, int toIndex)`, `parallelSort(byte[] a)`
  - `static Spliterator spliterator(int[] a)`, ...
  - `static Stream stream(int[] a)`, ...
  - `static toString(int[] a)`, ...
- Transforms
  - `static int[] copyOf(int[] original, int newLength)`, `copyOf(byte[] original, int newLength)`, ...
  - `static int[] copyOfRange(int[] original, int from, int to)`, `copyOfRange(byte[] original, int from, int to)`, ...
- Creations
  - `static List asList(T... a)`



## Container Features

### Traversal

**Fail Fast**

when one thread using iterator for traversal, updating the container object by add, remove operations, the iterator thread will throw a `ConcurrentModificationException`.

### Ordering

**Natural Ordering**

Elements order by method `compareTo()` of the class.

### Consistency

**Happens-before**

This relationship is simply a guarantee that memory writes by one specific statement are visible to another specific statement in multiple threads.

For example, an variable `int counter=0` shared in thread A and thread B. If thread A increment counter by `counter++`, thread B `print counter` by `System.out.println(counter)`, the printed value would be "1".

## Concurrent Collections

Besides Queues, this package supplies Collection implementations designed for use in multithreaded contexts: [`ConcurrentHashMap`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentHashMap.html), [`ConcurrentSkipListMap`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentSkipListMap.html), [`ConcurrentSkipListSet`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentSkipListSet.html), [`CopyOnWriteArrayList`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CopyOnWriteArrayList.html), and [`CopyOnWriteArraySet`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/CopyOnWriteArraySet.html). When many threads are expected to access a given collection, a `ConcurrentHashMap` is normally preferable to a synchronized `HashMap`, and a `ConcurrentSkipListMap` is normally preferable to a synchronized `TreeMap`. A `CopyOnWriteArrayList` is preferable to a synchronized `ArrayList` when the expected number of reads and traversals greatly outnumber the number of updates to a list.

The "Concurrent" prefix used with some classes in this package is a shorthand indicating several differences from similar "synchronized" classes. For example `java.util.Hashtable` and `Collections.synchronizedMap(new HashMap())` are synchronized. But [`ConcurrentHashMap`](https://docs.oracle.com/javase/8/docs/api/java/util/concurrent/ConcurrentHashMap.html) is "concurrent". A concurrent collection is thread-safe, but not governed by a single exclusion lock. In the particular case of ConcurrentHashMap, it safely permits any number of concurrent reads as well as a tunable number of concurrent writes. "Synchronized" classes can be useful when you need to prevent all access to a collection via a single lock, at the expense of poorer scalability. In other cases in which multiple threads are expected to access a common collection, "concurrent" versions are normally preferable. And unsynchronized collections are preferable when either collections are unshared, or are accessible only when holding other locks.

Most concurrent Collection implementations (including most Queues) also differ from the usual `java.util` conventions in that their [Iterators](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html) and [Spliterators](https://docs.oracle.com/javase/8/docs/api/java/util/Spliterator.html) provide *weakly consistent* rather than fast-fail traversal:

- they may proceed concurrently with other operations
- they will never throw [`ConcurrentModificationException`](https://docs.oracle.com/javase/8/docs/api/java/util/ConcurrentModificationException.html)
- they are guaranteed to traverse elements as they existed upon construction exactly once, and may (but are not guaranteed to) reflect any modifications subsequent to construction.

## Summary

### Container Class Contrast Table

List

| Class Type             | Size      | Order           | Allows Null | Iterator     | Time Cost                             | Space Cost                            | Thread Safe      | Impl         |
| ---------------------- | --------- | --------------- | ----------- | ------------ | ------------------------------------- | ------------------------------------- | ---------------- | ------------ |
| `ArrayList`            | Resizable | Insert Ordering | Allows Null | Fail Fast    | Insert: O(n), remove: O(n), get: O(1) | O(n)                                  | Not Thread Safe  | Array        |
| `Vector`               | Resizable | Insert Ordering | Allows Null | Fail Fast    | Insert: O(n), remove: O(n), get: O(1) | O(n)                                  | **Synchronized** | Array        |
| `LinkedList`           | Resizable | Insert Ordering | Allows Null | Fail Fast    | Insert: O(1), remove: O(1), get: O(n) | O(n)                                  | Not Thread Safe  | Linked Nodes |
| `CopyOnWriteArrayList` | Resizable | Insert Ordering | Allows Null | **Snapshot** | Insert: O(n), remove: O(n), get: O(1) | O(n), Traverse and write need to copy | **Concurrent**   | Array        |

Queue & Stack

| Class Type              | Size               | Order                               | Allows Null | Iterator              | Time Cost                  | Space Cost | Thread Safe      | Impl                      |
| ----------------------- | ------------------ | ----------------------------------- | ----------- | --------------------- | -------------------------- | ---------- | ---------------- | ------------------------- |
| `Stack`                 | Resizable          | Insert Ordering                     | Allows Null | Fail Fast             | Pop: O(1), Push: O(1)      | O(n)       | **Synchronized** | extends vector            |
| `ArrayDeque`            | Resizable          | Insert Ordering                     | Not Null    | Fail Fast             | **Enqueue Dequeue: O(1)**  | O(n)       | Not Thread Safe  | Arrays                    |
| `PriorityQueue`         | Resizable          | **Natural Ordering, or Comparator** | Not Null    | Fail Fast             | Enqueue Dequeue: O(log(n)) | O(n)       | Not Thread Safe  | Priority Heap by Arrays   |
| `LinkedBlockingDeque`   | Optionally Bounded | Insert Ordering                     | Not Null    | **Weakly Consistent** | Enqueue Dequeue: O(1)      | O(n)       | **Concurrent**   | Linked Nodes              |
| `LinkedTransferQueue`   | Resizable          | Insert Ordering                     | Not Null    | Weakly Consistent     | Enqueue Dequeue: O(1)      | O(n)       | **Concurrent**   | Linked Nodes              |
| `ArrayBlockingQueue`    | Bounded            | Insert Ordering                     | Not Null    | Weakly Consistent     | Enqueue Dequeue: O(1)      | O(n)       | **Concurrent**   | Array                     |
| `LinkedBlockingQueue`   | Optionally Bounded | Insert Ordering                     | Not Null    | Weakly Consistent     | Enqueue Dequeue: O(1)      | O(n)       | **Concurrent**   | Linked Nodes              |
| `PriorityBlockingQueue` | Resizable          | Natural Ordering, or Comparator     | Not Null    | Weakly Consistent     | Enqueue Dequeue: O(log(n)) | O(n)       | **Concurrent**   | Priority Heap by Arrays   |
| `DelayQueue`            | Resizable          | Insert Ordering                     | Not Null    | Weakly Consistent     | Enqueue Dequeue: O(log(n)) | O(n)       | **Concurrent**   | Wrapped Priority Queue    |
| `SynchronousQueue`      | No Capacity        | No Exist                            | Not Null    | Can't Iterator        | Enqueue Dequeue: O(1)      | O(1)       | **Concurrent**   | Dual Stack and Dual Queue |
| `ConcurrentLinkedQueue` | Resizable          | Insert Ordering                     | Not Null    | Weakly Consistent     | Enqueue Dequeue: O(1)      | O(n)       | **Concurrent**   | Linked Nodes              |
| `ConcurrentLinkedDeque` | Resizable          | Insert Ordering                     | Not Null    | Weakly Consistent     | Enqueue Dequeue: O(1)      | O(n)       | **Concurrent**   | Linked Nodes              |

Set

| Class Type              | Size      | Order                               | Allows Null        | Iterator          | Time Cost                                                 | Space Cost          | Thread Safe     | Impl                          |
| ----------------------- | --------- | ----------------------------------- | ------------------ | ----------------- | --------------------------------------------------------- | ------------------- | --------------- | ----------------------------- |
| `HashSet`               | Resizable | No Ordering                         | Allows Single Null | Fail Fast         | Insert: O(1), Remove: O(1), Contains: O(1)                | O(n)                | Not Thread Safe | Hash Table or `HashMap`       |
| `LinkedHashSet`         | Resizable | **Insert Ordering**                 | Allows Single Null | Fail Fast         | Insert: O(1), Remove: O(1), Contains: O(1)                | O(n)                | Not Thread Safe | Hash Table or `LinkedHashMap` |
| `CopyOnWriteArraySet`   | Resizable | Insert Ordering                     | Allows Single Null | **Snapshot**      | Insert: O(n), Remove: O(n), Contains: O(n)                | O(n), Cost to Write | **Concurrent**  | `CopyOnWriteArrayList`        |
| `TreeSet`               | Resizable | **Natural Ordering, or Comparator** | Not Null           | Fail Fast         | Insert: O(log(n)), Remove: O(log(n)), Contains: O(log(n)) | O(n)                | Not Thread Safe | `TreeMap`                     |
| `ConcurrentSkipListSet` | Resizable | **Natural Ordering, or Comparator** | Not Null           | Weakly Consistent | Insert: O(log(n)), Remove: O(log(n)), Contains: O(log(n)) | O(n)                | **Concurrent**  | `ConcurrentSkipListMap`       |

Map

| Class Type              | Size      | Order                               | Allows Null        | Iterator              | Time Cost                                            | Space Cost | Thread Safe      | Impl                                          |
| ----------------------- | --------- | ----------------------------------- | ------------------ | --------------------- | ---------------------------------------------------- | ---------- | ---------------- | --------------------------------------------- |
| `HashTable`             | Resizable | No Ordering                         | **Not Null**       | Fail Fast             | Insert: O(1), Remove: O1), Get: O(1)                 | O(n)       | **Synchronized** | Hash Table (Linked Array)                     |
| `HashMap`               | Resizable | No Ordering                         | Allows Single Null | Fail Fast             | Insert: O(1), Remove: O1), Get: O(1)                 | O(n)       | Not Thread Safe  | Hash Table (Linked Array)                     |
| `LinkedHashMap`         | Resizable | **Insert Ordering**                 | Allows Single Null | Fail Fast             | Insert: O(1), Remove: O1), Get: O(1)                 | O(n)       | Not Thread Safe  | Hash Table and Linked List                    |
| `EnumMap`               | Bounded   | Natural Order of keys               | Not Null           | Not Fail Fast         | Insert: O(1), Remove: O1), Get: O(1)                 | O(n)       | Not Thread Safe  | Object Array and enum types as index of array |
| `IdentityHashMap`       | Resizable | No Ordering                         | Allows Single Null | Fail Fast             | Insert: O(1), Remove: O1), Get: O(1)                 | O(n)       | Not Thread Safe  | Hash Table                                    |
| `WeakHashMap`           | Resizable | No Ordering                         | Allows Single Null | Fail Fast             | Insert: O(1), Remove: O1), Get: O(1)                 | O(n)       | Not Thread Safe  | Hash Table                                    |
| `TreeMap`               | Resizable | **Natural Ordering, or Comparator** | Not Null           | Fail Fast             | Insert: O(log(n)), Remove: O(log(n)), Get: O(log(n)) | O(n)       | Not Thread Safe  | Red Black Tree                                |
| `ConcurrentHashMap`     | Resizable | No Ordering                         | **Not Null**       | **Weakly Consistent** | Insert: O(1), Remove: O1), Get: O(1)                 | O(n)       | **Concurrent**   | Hash Table                                    |
| `ConcurrentSkipListMap` | Resizable | **Natural Ordering, or Comparator** | Not Null           | **Weakly Consistent** | Insert: O(log(n)), Remove: O(log(n)), Get: O(log(n)) | O(n)       | **Concurrent**   | Tree-Like Two-Dimensionally Linked `SkipList` |

**Container Classes core features**

- bounded, or unbounded
- ordered, or disordered (insert ordering, natural ordering, comparator ordering)
- null value
- traverse operation is fail-fast or weakly consistent
- performance
  - read, write and traversal operations time and space cost.
- thread safe: concurrency, consistency
  - synchronized
  - concurrent
  - consistent

**How to select a effective container class to use**

List

- **`ArrayList`** and **`LinkedList`** is common lists. `ArrayList` implemented by array, it is fast to random access, but slow to insert and remove. `LinkedList` implemented by nodes, it is fast to insert and remove, but slow to random access. 
- **`Vector`** is thread-safe list by synchronized. If you want to use list in multithread environment, you can choose it.
- **`CopyOnWriteArrayList`** is thread-safe list, and It is more concurrent than synchronized, but its write operations is very costly. If you want to use list in multithread environment with high concurrent, and operations are almost read operations(90%+), you can choose it. You will have high concurrent in read and write operations.

Stack

- If you want to use stack in single thread environment, you can use **`ArrayDeque`** and **`LinkedList`**, or construct by wrapper `ArrayList` or array. `ArrayDeque` > `LinkedList`
- If you want to use stack with thread-safe. You can use **`Stack`** or **`ConcurrentLikedDeque`**. The `Stack` has strictly consistent and poor concurrency. the `ConcurrentLinkedDeque` has high concurrency and weakly consistent.

Queue

- In single thread environments, the **`ArrayDeque`** is the most common queue. If you want a queue has priority, you can use **`PriorityQueue`**.
- In multithread environments, There have two type queues: blocking queue and concurrent queue. Blocking queues is commonly using for the producer-consumer scenario. About blocking queue selection, blocking queues for general using you can select **`LinkedBlockingDeque`**, **`ArrayBlockingQueue`**, and **`LinkedBlockingQueue`**. There are many blocking queues that have special features. They are **`LinkedTransferQueue`**, **`PriorityBlockingQueue`**, **`DelayQueue`**, **`SynchronousQueue`**. You can use it according to their features.
- Another thread-safe queue type is concurrent queues. They are **`ConcurrentLinkedQueue`** and **`ConcurrentLinkedDeque`**. They are very similar, just `ConcurrentLinkedDeque` can use as both queue and stack.

Set

- In single thread environments, the **`HashSet`** is the most common set. Other sets are **`LinkedHashSet`** and **`TreeSet`**. The `LinkedHashSet` is similar to `HashSet`, but it has additional functionality that keeps elements insert order by linking them. The `TreeSet` has lower performance than `HashSet`, but it can keep elements order with natural order or comparator.
- In multithread environments, We can use thread-safe sets **`CopyOnWriteArraySet`** and **`ConcurrentSkipListSet`**. The `CopyOnWriteArraySet` only use when most of operations (90%+) are reads. The `ConcurrentSkipListSet` use when you want to keep elements ordering.

Map

- In single thread environments, the **`HashMap`** is the most common map. It's for general using. Other maps are `LinkedHashMap`, `EnumMap`, `IdentityHashMap`, `WeakHashMap`, `TreeMap`. The **`LinkedHashMap`** is similar to `HashMap`, but it keeps elements insert order. The **`EnumMap`** use in when all keys of map are from an `Enum` types. The **`IdentityHashMap`** and **`WeakHashMap`** are rarely using, just using in special scenario according to their features. The `TreeMap` is lower performance than `HashMap`, but it keep elements of map order (natural order or comparators).

String

- `String` is the most common class for representing a string. It's final or immutable. If you want to concatenate multiple strings to one, you better use `StringBuilder`, it doesn't generate middle strings that saving memory cost.
- The `StringBuffer` is similar to `StringBuilder`, only difference between `StringBuffer` and `StringBuilder` is `StringBuffer` is thread-safe by synchronized. 

## References

[1] [Java SE 8 API Documentation](https://docs.oracle.com/javase/8/docs/api/)

[2] Java Source Code - `java.util.*`, `java.util.concurrent.*`

[3] [Why CopyOnWriteArrayList copys when writing?](https://stackoverflow.com/questions/18973925/why-copyonwritearraylist-copys-when-writing)

[4] [In what situations is the CopyOnWriteArrayList suitable?](https://stackoverflow.com/questions/17853112/in-what-situations-is-the-copyonwritearraylist-suitable)

[5] [How can CopyOnWriteArrayList be thread-safe?](https://stackoverflow.com/questions/2950871/how-can-copyonwritearraylist-be-thread-safe)

[6] [ReentrantLock Example in Java, Difference between synchronized vs ReentrantLock](https://javarevisited.blogspot.com/2013/03/reentrantlock-example-in-java-synchronized-difference-vs-lock.html)

[7] [Difference between BlockingQueue and TransferQueue](https://stackoverflow.com/questions/7317579/difference-between-blockingqueue-and-transferqueue)

[8] [Memory Consistency Errors](https://docs.oracle.com/javase/tutorial/essential/concurrency/memconsist.html)

--END--