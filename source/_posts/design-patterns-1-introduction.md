---
title: 浅谈设计模式（一）：介绍设计模式
date: 2019-12-17 12:46:36
categories: 
- 软件设计与架构
tags: design-patterns
---



欢迎来到设计模式系列文章，这是设计模式系列文章的第一篇，本篇将介绍设计模式的基本概念，主要介绍“什么是设计模式”，“为什么要使用设计模式”，以及“如何使用设计模式”等内容。

设计面向对象软件是困难的，设计可重用的面向对象软件则更加困难。你必须找到相关的 objects，以适当的 granularity 将他们分解为 classes，定义 class interfaces 和 inheritance hierarchies，以及在他们之间建立关系。你的设计应当解决当前的问题，也要足以解决未来的问题和需求。你还要避免重复设计，或者最小化重复设计。做好这些事情不是一件简单的事情，设计模式可以快速的帮助我们有效的解决复杂问题。

## What is Design Patterns?

设计模式是描述特定环境下重复出现的问题和问题解决方案的核心内容，且这个方案可以重复的使用。具体来说，它描述了 objects 和 classes 之间的交流，它是定制化地去解决一个在特定场景下的设计问题。

一般来说，一个设计模式包含以下四个基本元素：

- Pattern Name。模式名称可以用于描述问题和它的解决方案。它是对设计的高度的抽象。模式的专业词汇，能够方便我们去讨论交流和写文档等。
- Problem。问题描述了我们什么时候要使用这个模式。它解释了问题和模式的应用场景。它可能描述一个不灵活的 class 和 object 结构设计的特征，通过这些特征可以知道应该通过哪些设计模式去解决这些问题。
- Solution。抽象地描述问题和一般的解决方案。它描述了设计方案，以及 classes 或 objects 之间的 relationships，responsibilities，和 collaborations 等。
- Consequences。应用一个模式的结果和 trade-offs。它描述了应用一个模式的 costs 和 benefits。

## Why design patterns are needed?

设计模式是帮助我们快速解决特定场景的设计问题，帮助我们设计一个可重用、可扩展性的应用。它主要有以下几个优点：

- 设计模式使得重用成功的设计和架构变得更加容易。
- 设计模式通过提供 class 和 object 相互作用和它们的潜在意图的明确规范，来提升对存在系统的文档编写和维护。
- 设计模式可以帮助设计者更快的得到一个正确的设计。

## How Design Patterns Solve Design Problems

面向对象程序是由 objects 组成的。一个 object 包含 data 和 procedures。当 object 接收到来自 client 的请求时，执行相关的操作。请求只能让 object 执行一个操作。操作是唯一改变 object 内部数据的方式。object 内部状态是 encapsulated，对外部是不可见的。

面向对象最难的部分就是把一个系统 decomposing 为 objects。这个任务是很复杂的，因为它涉及到很多因素。如：encapsulated，granularity，dependency，flexibility，performance，evolution，reusability 等等。这些影响因素往往是相互冲突的，需要有所权衡，然而，兼顾这么多因素不是一件很容易的事。设计模式通过考虑这些因素，针对特定的应用场景的问题，提供了一个有效的解决方案。

## How to Select a Design Pattern

常见的设计模式有20多种，我们应该怎么去选择呢？下面列出了一些找到合适的设计模式的方法：

- 考虑设计模式是如何解决问题的。
- 查看每个设计模式的意图和目的。
- 思考设计模式是如何相互关联的。设计模式之间的关系。
- 检查导致重复设计的原因。看你的设计是否有类似的问题，看哪些模式可以帮助避免重复设计。
- 考虑你的设计中什么是变化的。

## How to Use a Design Pattern

当你选择好了设计模式之后，你可以通过以下步骤将设计模式应用在你的程序中。

1. 查看该模式的 Applicability 和 Consequence 部分的描述内容，确定你选择的模式能够正确地解决你的问题。
2. 学习该模式的 Structure，Participants 和 Collaborations 部分内容，确保你理解了 classes 和 objects 在模式中是如何关联的。
3. 查看具体的代码实现例子。学习如何实现该模式。
4. 选择对于你的应用环境有意义的 Participants 的名称。如使用 Strategy 模式设计文本组合算法，你可以命名为 SimpleLayoutStrategy，TeXLayoutStrategy。
5. 定义 Classes。具体为：声明 interfaces，建立继承关系，定义对象的变量。
6. 定义该模式在具体应用中的classes 的操作名称。即定义有意义的方法名称。如在 factory method 模式中，可能使用 create- 为前缀的方法名称。
7. 实现定义好的方法，实现模式中的 responsibilities 和 collaborations。

## References

[1] Design Patterns: Elements of Reusable Object-Oriented Software by Erich Gamma, Richard Helm, Ralph Johnson and John Vlissides