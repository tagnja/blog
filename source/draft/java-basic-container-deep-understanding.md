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

```
(I)java.util.Queue
|----java.util.AbstractQueue
|--------java.util.PriorityQueue
|----(I)java.util.Deque
|--------java.util.ArrayDeque
|----(I)java.util.concurent.BlockingQueue
|--------(I)BlockingDeque
|------------LinkedBlockingDeque
|--------(I)TransferQueue
|------------LinkedBlockingDeque
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

> The `Queue` interface does not define the blocking queue methods, which are common in concurrent programming.

> Queue implementations generally do not allow insertion of null elements, although some implementations do not prohibit. Null should not be inserted into a queue, as null is also used as a special return value by the poll method to indicate that the queue contains no elements.

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

> The name deque is short for "double ended queue" and is usually pronounced "deck".

**Methods of `Deque`**

- insert
  - `boolean add(E e)`
  - `void addFirst(E e)`
  - `void addLast(E e)`
  - `boolean offer(E e)`
  - `boolean offerFirst(E e)`
  - `boolean offerLast(E e)`
  - `void push(E e)`
- remove
  - `E remove()`
  - `E removeFirst()`
  - `E removeLast()`
  - `boolean removeFirstOccurrence(Object o)`
  - `boolean removeLastOccurrence(Object o)`
  - `E poll()`
  - `E pollFirst()`
  - `E pollLast()`
  - `E pop()`
- examine
  - `E getFirst()`
  - `E peekFirst()`
  - `E getLast()`
  - `E peekLast()`
  - `E element()`
  - `E peek()`
  - `E peekFirst()`
  - `E peekLast()`
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

### SynchronousQueue

### ConcurrentLinkedQueue

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

[7] [Difference between BlockingQueue and TransferQueue](https://stackoverflow.com/questions/7317579/difference-between-blockingqueue-and-transferqueue)

--END--