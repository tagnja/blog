Title: Clean Code Note

Content

- Clean Code
- Meaning Names
- Functions
- Comments
- Formatting
- Objects and Data Structures
- Error Handling
- Boundaries
- Unit Test
- Classes
- Systems
- Emergence
- Concurrency



---

本篇是将介绍如何更好地编程，如何编写 good code，如何将 bad code 转换为 good code。

## Clean Code

### What is Clean Code

clean code 应该具有以下特点：

- 整洁性、可读性。1）简单明了，清晰地表达代码的意图。2）便于阅读，有意义的命名。3）统一的良好代码格式。
- 可维护性、可扩展性。1）单一职责，每个类和方法做好一件事。2）开闭原则。3）没有重复。4）最小化依赖。
- 健壮性。1）错误处理。2）单元测试完全覆盖，无潜在 bug。
- 高效性。性能尽可能最优。

### Why do We Need Clean Code

代码是需求的最终表达形式，实现一个软件需求我们需要编写正确、整洁的代码，使得软件高效地在机器上运行，以及便于扩展和维护。

编写 bad code 可能会导致严重的后果。bad code 会使得团队的生产力持续降低。bad code 可能导致软件无法维护，以及存在大量 bug，最终导致软件无法正常运行。最后，不得不重新设计代码，建立一个新的团队重构旧的系统，以及跟进旧系统的改变。

### 产生 bad code 的原因

产生 bad code 的原因有很多，如：1）需求的改变。2）时间规划太紧，没有足够的时间去做得更好。3）对这个程序很疲倦，想要早点结束。4）手上堆积了很多其它的事，想要赶紧做完它，然后做其它事情。5）管理者的管理不当。...

作为程序开发者，我们不能一味地抱怨外部原因，我们更应该反思我们自己，我们能否做得更好。1）当软件的时间规划和安排不合理时，我们应该及时的反馈我们的想法。大部分管理者想要看到事实，以及想要 good code。2）在开发过程中，我们应该保持专业的态度，持续保证代码的整洁，就像医生做手术前要洗手一样，多花一点时间保持代码的整洁，让保证代码不会变成 bad code。唯一保证 deadline 的方法就是在任何时候尽可能地保持代码地整洁，为了加快速度编写混乱的代码最终会拖垮你的进度。

## Meaningful Names

命名在软件开发中无处不在。我们需要为变量、方法、参数、类、包和项目文件目录等命名。我们需要大量的命名，因此我们应该把它做得更好。这一部分，我们将介绍好的命名规则，以及尽力避免的不好的命名规则。

### Rules for Creating Good Names

选择一个好的名称是需要时间的，但它节省的时间大于它的花费。关注代码中命名，当你发现更好的名称时改变它。

**Use Intention-Revealing Names**

一个好的名称应该告诉我们它为什么存在，它能做什么，它如何使用。如果一个名称需要注释说明，然么这个名称没有展现它的意图。使用揭示意图的名称更容易理解和改变代码。

- 使用揭示意图的名称。
- 使用静态变量代替直接量。
- 使用对象封装一组数据。

例子：

错误写法

```java
int d; // elapsed time in days
```

正确写法

```java
int elapsedTimeInDays;
int daysSinceCretation;
```

**Make Meaningful Distinction**

如果名称必须不同，那么它们一定存在某些不同。有意义的区分不同的名称，可以让读者清晰地知道它们之间的不同。

常见无意义区分：

- 使用数字序列命令 a1, a2, .. aN。如 `copyChars(char a1[], char a2[]) => copyChars(char source[], char destination[])`
- 使用同义词。如 1）info 和 data，ProductInfo 和 Productdata。2）a, an, the，zork 和 theZork。

**Use Pronounceable Names**

我们大脑更习惯处理可发音的语言。可发音的名称更容易与他人交流和讨论。命名时尽量使用完整的单词的组合。

例子：

错误的写法

```java
class DtaRcrd102{
    private Date genymdhms;
    private Date modymdhms;
    private final String pszqint = "102";
}
```

正确的写法

```java
class Customer {
    private Date generationTimestamp;
    private Date modificationTimestamp;
    private final String recordId = "102";
}
```

**Use Searchable Names**

单字母的名称和数字常量等名称很难在文本中定位。尽量不要使用单字母命名和直接使用常量。

**Class Names**

Classes 和 objects 的名称应该为名词或名词短语。如 Customer，WikiPage，Account，AddressParser 等。

**Method Names**

方法名称应该为动词或者动词短语。如 postPayment，deletePage，save 等。

**Pick One Word per Concept**

为一个抽象概念选择一个词，并且坚持使用它。例如，fetch，retrieve，get 等作为相同含义的方法在不同过的类中会使人感到困惑。类似还有 controller，manager 和 driver。同一个概念应该坚持使用同一个单词。

**Use Solution Domain Names and Problem Domain Names**

阅读代码的人一定使程序员，我们应该坚持使用计算机术语。计算机术语大部分程序员都比较熟悉，遇到不清楚的术语，可以在网上快速查询得到结果。

**Add Meaningful Context**

增加有意义的 Context 让读者能清晰直观地知道这个变量是某个类的一部分。

如 firstName，lastName，street，houseNumber，city，state，zipcode 是 Address 中的成员。其中 state 不能直观的看出它是 adderss 的一部分。我们可以添加前缀来增加 context。state => addressState。

### Avoid  Creating Bad Names

**Avoid Disinformation**

我们应该避免留下误解代码含义的错误线索。

- 避免单词缩写。如， hypotenuse 缩写成 hp。
- 一组对象不确定数据结构时，避免添加固定类型后缀。如，accountList => accountGroup, bunchOfAccounts, or accounts.

**Avoid Encodings**

编码的名称增加解码的负担。编码名称一般很少是可发音的，以及它容易误解类型。尽量避免使用编码的名称。

常见的编码命名方式：

- 匈牙利表示法（Hungarian Notation）变量名以一个或多个小写字母开头，代表变量的类型。后面附以变量的名字。这种方案允许每个变量都附有表示变量类型的信息。如 `int[] aNames`，`boolean bDeleted`
- 成员前缀 m_。
- 接口和实现。使用 Ixxx 表示接口,如 IShapeFactory。

**Avoid Mental Mapping**

避免思维映射，即避免使读者将名称翻译为它们已经知道的。如设计模式名称、算法名称等等。在命名时我们使用问题领域术语和解决方案领域术语。

**Don't Be Cute**

如果名称太机灵，它们将仅被作者分享过它的含义的人记住。如 HolyHandGrenade 实际上表示 DeleteItems，whack() 实际表示 kill()，eatMyShorts() 表示 abort()。 

**Don't Pun**

避免使用相同的词表示两个目的。使用相同的术语表示两个不同的想法使双关的，这让人不易理解，产生误解。如，一个 add 方法用于将两个值相加或连接。另一个 add 方法表示将元素添加到集合对象中。我们不应该一词双关，我们可以使用 insert 或 append 代替 add。

**Don't Add Gratuitous Context**

不要增加不必要的 context。如，为某个模块的所有 classes 添加前缀标识。

### 有意义的命名总结

单个命名应该：揭示意图，可发音，可搜索，一致性，使用专业术语，增加有意义的 context。

多个命名之间应该：可区分。

有意义的命名的实现目标：1）整洁性、可维护性。名称揭示意图，代码解释一切，无注释。2）可读性。代码命名简单、明确，让人更轻松、更快地阅读和理解代码。

## Functions

Small

Do One Thing

One Level of Abstraction per Function

Switch Statements

Use Descriptive Names

Function Arguments

Have No Side Effects

Command Query Separation

Prefer Exception to Returning Error Codes

Don't Repeat Yourself

Structured Programming



## Comments



## Formatting





## References

[1] Clean Code by Robert C. Martin