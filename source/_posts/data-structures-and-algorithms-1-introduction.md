---
title: 介绍数据结构和算法
date: 2019-09-16 16:41:37
categories: 
- 计算机基础
- 算法
tags: Algorithms
---



大多数计算机程序的主要目的不是计算，而是存储和取出信息。面对大量的信息，我们要如何组织信息，使它能支持更高效的处理？这就是数据结构需要解决的问题。在特定问题下，我们可以根据不同的数据结构它们对应的花费和收益，选择一个更高效更快的数据结构。

解决一个问题常常由很多方法。我们要如何选择？首先，我们要知道程序设计的核心是什么？它主要是实现以下两个目标：

1. 设计一个易于理解、实现和调试的算法。
2. 设计一个更高效的使用计算机资源的算法。

第一点指的是程序的优雅，第二点是指程序的效率。在算法领域，我们主要是探究算法的效率。一个问题最优的解决方案就是找到一个效率最高的算法。

如何知道一个算法比另一个算法更好？这就要测量一个算法的效率。一个常见的用来估计算法的效率的方法称为**渐进分析**（Asymptotic Analysis）。

### 抽象数据类型和数据结构

**类型**（Type）是指一组值。如布尔类型由 `true` 和 `false` 组成。整数也是一个类型。类型可以分为：简单类型（Simple Type）和复合类型（Composite Type）。一个数据项只有一个成员的类型称为简单类型。如整数类型。一个数据单元由多个成员组成的类型称为复合类型。如银行帐号的个人信息。

**数据类型**（Data Type）它指的是一个类型和一组操作。

**抽象数据类型**（Abstract Data Type，ADT）它定义了一个类型和一组的操作。它是数据类型的逻辑实现。

**数据结构**（Data Structure）是一个 ADT 的在编程语言上的具体实现。它是数据类型的物理实现。

数据类型只是一个称呼，它没有具体的内容。抽象数据类型是数据类型的抽象的（或逻辑的）表示，它表示了有哪些具体的操作，定义了类型的名称和操作的名称，它是描述文字。数据结构是用具体的编程语言代码对 ADT 的定义，它是可执行的代码。

### 问题，算法和程序

问题是一个要执行的任务。它表示为任意的输入得到匹配的输出。问题必须是明确定义的和能清晰理解的。问题的定义应该包含计算机资源的限制，如内存，硬盘和时间。

算法是解决一个问题的方法（或过程）。一个算法必须具备以下属性：

- 正确性。每一个输入能够转换为正确的输出。
- 可行性。算法中任何计算步骤可以被分解为基本的可执行的操作。
- 确定性。没有歧义的。
- 有穷性。能在执行有限个步骤之后终止。
- 输入项。0个或多个输入。
- 输出项。一个或多个输出。

程序是一个由编程语言实现的算法的实例（或具体的表现）。程序不像算法，程序是可以一直运行不终止的，如操作系统。

### 数据结构的选择

给定一个足够的空间去存储一组数据项，无论使用什么数据结构去表示这些数据，每种数据结构总是可能执行所有必要的操作，如搜索，排序，插入，删除等。然而使用不同的数据结构的区别在于，执行一个程序，一个只需要几秒钟，另一个则需要好几天。一个选择数据结构的方法如下：

- 分析问题需要哪些基本的操作。如插入，删除，查找。
- 量化每个操作的资源约束（时间和空间）。
- 选择满足需求的最好的数据结构。

每个数据结构有它相应的花费和收益。没有一个数据结构在任何情况下都比另一个数据结构好。你需要根据问题需求选择合适的数据结构。

### 算法的选择

一个好的数据结构的选择，可以做到事半功倍的效果。一个算法实现策略的选择也十分关键。简单地使用直接法（枚举法）可以解决问题，但效率很低，它与贪心策略或动态规划相比，算法执行的时间往往是数量级的差别。

算法的核心优化思路是去除不必要的操作和去除重复的操作。几乎所有的算法策略都是这个思路，如动态规划、分治法。

### References

[1] Data Structures and Algorithm Analysis in C++ (3rd) by Clifford A. Shaffer