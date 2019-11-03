---
title: 基本数据结构的概念与理解
date: 2019-09-20 10:33:18
categories: 
- 计算机基础
- 算法
tags: Algorithms
---



## 基本数据结构

### List 

<br>

#### List 的抽象数据类型

列表（List）包含一组按顺序排列的相同类型的元素，它的操作如下：

- get() - 返回给定位置的一个元素。
- insert() - 插入一个元素在指定位置。
- remove() - 删除第一个元素。
- removeAt() - 删除一个特定位置的元素。
- size() - 返回列表元素的数量。
- isEmpty() - 返回列表是否为空。
- isFull() - 返回列表是否已满。
- clear() - 清空列表的内容。



#### List 的实现

**Array List**

**数组**是列表的一种实现，它在内存中是连续的空间。

优点

- 它可以快速的随机访问。

缺点

- 插入和删除性能不太好，需要大量的元素位置移动。

应用场景

- 简单的存储一组数据。



**Linked List**

**链表**是一个线性结构，它是列表的另一种实现，它在内存中是非连续的空间。每个元素称为节点，相邻的两个节点由指针进行连接。

优点

- 它具有很好的扩展性，快速的插入和删除。

缺点

- 随机访问（按位置访问）性能不好。节点的访问必须从前往后依次遍历。
- 比数组消耗更多的内存。在使用的指针上。
- 需要更多的读取时间。在内存中是不连续的，CPU 一次只能获取一个元素，

应用场景

- 迭代器。



**Array List vs Linked List**

|            | 数组        | 链表        |
| ---------- | ----------- | ----------- |
| 随机存取   | T(n) = O(1) | T(n) = O(n) |
| 插入和删除 | T(n) = O(n) | T(n) = O(1) |



### Stack 

<br>

#### Stack 的抽象数据类型

栈（Stack）是一个线性的数据结构。它遵循一个特殊的操作顺序。后进先出（Last In First Out, LIFO），它的操作如下：

- push() 
- pop()
- peek()
- size()
- claer()

#### Stack 的实现

**栈**可以基于数据实现，也可以基于链表实现。

优点

- 方便的按照某种循序访问数据。

缺点

- 只用于特定场合。

应用场景

- 逆序输出。
  - 进制转换。
- 递归嵌套。
  - 括号匹配
  - 栈混洗（Stack Permutation）。通过一个中间栈，重新排序原始栈的数据顺序。。
- 延迟缓冲。
  - 中缀表达式。操作符以中缀形式处于操作数中间的算术表达式。
  - 逆波兰表达式。不需要括号的后缀表达式。
- 栈式计算。



### Queue 

<br>

#### Queue 的抽象数据类型

队列（Queue）是一个线性结构。它和栈类似有特殊的操作顺序。先进先出（First In First Out, FIFO）。它的操作如下：

- enquque()
- dequeue()
- peek()
- size()
- claer()



#### Queue 的实现

**队列**可以基于数组实现，也可以基于链表实现。

优点

- 方便的按照某种循序访问数据。

缺点

- 只使用在特定的场合。

应用场景

- 系统任务调度。



### Binary Tree

<br>

#### Binary Tree 的抽象数据类型

二叉树（Binary Tree）是一个树结构，每个节点最多有两个子节点，分别表示为左子树和右子树。它的操作：

- getRoot()
- isEmpty()
- size()
- contains()
- find()
- toString()
- iteratorInOrder()
- iteratorPreOrder()
- iteratorPostOrder()
- iteratorLevelOrder() 

#### Binary Tree 的实现

**二叉树**通过指针关联节点实现。

优点

- 可以表示树结构的数据关系。

缺点

- 大量的指针消耗。

应用场景

- 用于搜索。通过 binary search tree 可以支持快速的搜索、插入和删除，复杂度都是 O(log n)。
- 用于压缩。使用在 Huffman Coding。
- 用于数据库的索引的实现。



### Graph

<br>

#### Graph 的抽象数据类型

图（Graph）由一组有限个顶点（Vertex）和有限的边（Edge）组成。它的操作如下：

- addVertex(vert)
- addEdge(fromVert, toVert)
- addEdge(fromVert, toVert, weight)
- getVertex(vertKey)

#### Graph 的实现

**图**可以通过邻接表（adjacency List），也可以邻接矩阵（adjacency Matrix）实现。

|                   | 邻接矩阵 | 邻接表                     |
| ----------------- | -------- | -------------------------- |
| 内存              | O(n^2)   | less than adjacency Matrix |
| 查询一条边        | O(1)     | O(k)                       |
| 遍历所有边        | slow     | fast                       |
| 添加/删除一个节点 | O(n^2)   | O(n)                       |
| 添加一条边        | O(1)     | O(1)                       |

应用场景：

- 用于计算机网络的路由，找到最小花费路径。

- 社交媒体应用，用户之间的关联。
- 地图应用。如，找到最优的行驶路线。
- 电商网站。推荐商品。



## 其它数据结构



### Dictionary

<br>

#### Dictionary 的抽象数据类型

字典（Dictionary）高效地组织数据，支持快速地存储和查找。每个元素由一个 key 和一个复合类型（对象）组成<key, E>。通过 key 来唯一标识一个对象，可以通过 key 来查找对象。它的操作如下：

- insert(key, object)
- remove(key)
- find(key)
- size()
- clear()



### Hash Table

<br>

#### Hash Table 的抽象数据类型

哈希表（Hash Table）是一个由链表组成的数组结构。它使用哈希函数计算一个元素值在数组中的索引。它的操作：

- put(key, value)
- get(key)
- containsKey(key)
- contains(value)
- remove(key)

#### Hash Table 的实现

哈希表由链表数组实现。

优点

- 快速的查找。
- 快速的插入和删除。

缺点

- 不保证元素的顺序。不能随机访问。



## 数据结构的选择

### 插入删除/扩展性

数据的容量需要很好的扩展性，或者频繁的插入和删除，选择动态的数据结构（指针关联）比较合适。如链表，二叉树等等。不足之处是指针结构对随机存取效率没有静态结构（连续空间）好。

### 搜索

简单的搜索即从第一个元素一直比较直到最后一个元素。如数组，它的复杂度是 O(n)。

更高效率搜索的数据结构，哈希表的复杂度为 O(1)，有序数组复杂度为 O(log n)，搜索二叉树的复杂度为 O(log n)

### 综合考虑

兼顾**搜索和插入**：可以考虑哈希表和搜索二叉树。有序数组虽然搜索复杂度和搜索二叉树一样，但是有序数组的插入性能不好。

兼顾**搜索和随机存取**：可以考虑有序数组。



## 数据结构的总结

组成形式：连续空间，不连续（指针）空间。

结构关系类型：线性，树状，图

本质：存储一组元素，表示元素之间的关系，提供操作元素的方法。

数据结构的总结：

- 数据结构是用来表示数据和操作数据的。
- 有些数据结构只适用于特殊的场景。

- 一个好的数据结构可以大幅度提高算法的效率。

- 不存在一种数据结构在所有方面都优于另一个数据结构，每个数据结构都有自己的优缺点。数据结构的选择是一个权衡利弊和妥协的过程。



## References

[1] Data Structures and Algorithm Analysis in C++ (3rd) by Clifford A. Shaffer

[2] [Abstract Data Types - GeeksforGeeks](https://www.geeksforgeeks.org/abstract-data-types/)

[3] [The BinaryTree ADT](http://www.csd.uwo.ca/Courses/CS1027b/notes/CS1027-014-Trees-W12.pdf)

[4] [The Graph Abstract Data Type](https://runestone.academy/runestone/books/published/pythonds/Graphs/TheGraphAbstractDataType.html)

[5] [Linked List - Wikipedia](https://en.wikipedia.org/wiki/Linked_list)

[6] [What are the applications of binary trees? - Stack Overflow](https://stackoverflow.com/questions/2130416/what-are-the-applications-of-binary-trees)

[7] [What are real life applications of graphs? - Quora](https://www.quora.com/What-are-real-life-applications-of-graphs)