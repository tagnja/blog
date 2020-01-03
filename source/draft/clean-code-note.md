Title: 如何编写整洁代码

Content

- Clean Code
- Meaning Names
- Functions
- Comments
- Formatting
- //Objects and Data Structures
- Error Handling
- //Boundaries
- Unit Test
- Classes
- //Systems
- //Emergence
- //Concurrency



---

本篇是将介绍如何更好地编程，如何编写整洁的代码，如何将 bad code 转换为 good code。

## Clean Code

### What is Clean Code

clean code 应该具有以下特点：

- 整洁性、可读性。1）简单明了，清晰地表达代码的意图。2）便于阅读，有意义的命名。3）统一的良好代码格式。
- 可维护性、可扩展性。1）单一职责，每个类和方法做好一件事。2）开闭原则。3）没有重复。4）最小化依赖。
- 健壮性。1）错误处理。2）单元测试完全覆盖，无潜在 bug。
- 高效性。性能尽可能最优。

### Why do We Need Clean Code

代码是需求的最终表达形式，实现一个软件我们需要编写正确、整洁的代码，使其高效地在机器上运行，以及便于扩展和维护。

编写 bad code 可能会导致严重的后果。bad code 会使得团队的生产力持续降低。bad code 可能导致软件无法维护，以及存在大量 bug，最终导致软件无法正常运行。最后，不得不重新设计代码，建立一个新的团队重构旧的系统，以及跟进旧系统的改变。

### 产生 bad code 的原因

产生 bad code 的原因有很多，如：1）需求的改变。2）时间规划太紧，没有足够的时间去做得更好。3）对一个程序很疲倦，想要早点结束。4）手上堆积了很多其它的事，想要赶紧做完它，然后做其它事情。5）管理者的管理不当。...

作为程序开发者，我们不能一味地抱怨外部原因，我们更应该反思我们自己，我们能否做得更好。1）当软件的时间规划和安排不合理时，我们应该及时的反馈我们的想法。大部分管理者想要看到事实，以及想要 good code。2）在开发过程中，我们应该保持专业的态度，持续保证代码的整洁，就像医生做手术前要洗手一样，多花一点时间保持代码的整洁，让保证代码不会变成 bad code。唯一保证 deadline 的方法就是在任何时候尽可能地保持代码地整洁，为了加快速度编写混乱的代码最终会拖垮你的进度。



## Meaningful Names

命名在软件开发中无处不在。我们需要为变量、方法、参数、类、包和项目文件目录等命名。我们需要大量的命名，因此我们应该把它做得更好。这一部分，我们将介绍好的命名规则，以及尽力避免的不好的命名规则。

### Rules for Creating Good Names

选择一个好的名称是需要时间的，但它节省的时间大于它的花费。持续关注代码中名称，当你发现更好的名称时立刻改变它。

**Use Intention-Revealing Names**

一个好的名称应该告诉我们它为什么存在，它能做什么，它如何使用。如果一个名称需要注释说明，然么这个名称没有展现它的意图。使用揭示意图的名称更容易理解和改变代码。示例如下：

错误写法

```java
int d; // elapsed time in days
```

正确写法

```java
int elapsedTimeInDays;
int daysSinceCretation;
```

揭示意图常用的方法：

- 使用揭示意图的名称。
- 使用静态变量代替直接量。
- 使用对象封装一组数据。

**Make Meaningful Distinction**

如果名称必须不同，那么它们一定存在某些不同。有意义的区分不同的名称，可以让读者清晰地知道它们之间的不同。

常见无意义区分：

- 使用数字序列命令 a1, a2, .. aN。如 `copyChars(char a1[], char a2[]) => copyChars(char source[], char destination[])`
- 使用同义词。如 1）info 和 data，ProductInfo 和 Productdata。2）a, an, the，zork 和 theZork。

**Use Pronounceable Names**

我们大脑更习惯处理可发音的语言。可发音的名称更容易与他人交流和讨论。命名时尽量使用完整的单词的组合。示例如下：

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

为一个抽象概念选择一个词，并且坚持使用它。例如，fetch，retrieve，get 等相似含义的名称用在不同的类中功能相似的方法中，会使人感到困惑。类似还有 controller，manager 和 driver。同一个概念应该坚持使用同一个单词。

**Use Solution Domain Names and Problem Domain Names**

阅读代码的人一定是程序员，我们应该坚持使用计算机术语。计算机术语大部分程序员都比较熟悉，遇到不清楚的术语，可以在网上快速查询得到结果，不必与作者沟通。

**Add Meaningful Context**

增加有意义的 Context 让读者能清晰直观地知道这个变量是某个类的一部分。

如 firstName，lastName，street，houseNumber，city，state，zipcode 等变量是 Address 类中的成员。其中 state 不能直观的看出它是 adderss 的一部分。我们可以添加前缀来增加 context。state => addressState。

### Avoid  Creating Bad Names

**Avoid Disinformation**

我们应该避免留下误解代码含义的错误线索。

- 避免单词缩写。如， hypotenuse 缩写成 hp。
- 集合对象不确定数据结构时，应避免添加固定的类型后缀。如，accountList => accountGroup, bunchOfAccounts, or accounts.

**Avoid Encodings**

编码的名称增加解码的负担。编码名称一般很少是可发音的，以及它容易误解类型。尽量避免使用编码的名称。

常见的编码命名方式：

- 匈牙利表示法（Hungarian Notation）变量名以一个或多个小写字母开头，代表变量的类型。后面附以变量的名字。这种方案允许每个变量都附有表示变量类型的信息。如 `int[] aNames`，`boolean bDeleted`
- 成员前缀 m_。
- 接口和实现。使用 Ixxx 表示接口,如 IShapeFactory。

**Avoid Mental Mapping**

避免思维映射，即避免使读者将名称翻译为它们已经知道的概念。如设计模式名称、算法名称等等。在命名时我们使用问题领域术语和解决方案领域术语。

**Don't Be Cute**

如果名称太机灵，它们将仅能被作者分享过它的含义的人记住。如 HolyHandGrenade 实际上表示 DeleteItems，whack() 实际表示 kill()，eatMyShorts() 表示 abort()。 尽量避免这种命名方式。

**Don't Pun**

避免使用相同的词表示两个目的。使用相同的术语表示两个不同的想法是一语双关的，让人不易理解，产生误解。如，一个 add 方法用于将两个值相加或连接。然而，另一个 add 方法则表示将元素添加到集合对象中。我们不应该一词双关，我们可以使用 insert 或 append 代替 add。

**Don't Add Gratuitous Context**

不要增加不必要的 context。如，为某个模块的所有 classes 添加前缀标识。

### 有意义的命名总结

单个命名应该：揭示意图，可发音，可搜索，一致性，使用专业术语，增加有意义的 context。

多个命名之间应该：可区分。

有意义的命名的实现目标：1）整洁性、可维护性。名称揭示意图，代码解释一切，无注释。2）可读性。代码命名简单、明确，让人更轻松、更快地阅读和理解代码。



## Functions

**Small**

function 的第一规则：它们应该非常小。

1. Blocks and Indenting。if..else，while 等代码块应该占一行，作为方法调用。代码块作为方法调用可以有一个很好的描述名称。

2. indent level 不应该超过一层或两层。这使得 functions 更容易阅读和理解。

**Do One Thing**

Functions 应该做一件事，它们应该做好，它们应该只做一件事。

如何知道是否是只做一件事：

- function 只有一层抽象，即不包含多个不同的抽象层级。
- function 不能被划分多个 sections。

**Switch Statements**

让 switch 语句只做一件事是困难的，根据它的性质，switch 语句总是做 N 件事。我们可以将 switch 的每一个分支封装成一个方法。

**Use Descriptive Names**

functions 的名称应该描述 function 做了什么。不要害怕使得名称很长，一个长的 descriptive name 比短的神秘的名称更好。一个长的 descriptive name 比一个长的 comment 更好。

**Function Arguments**

理想的 function 参数的个数是0个，其次是1个和2个，3个参数是尽量避免的。

多个参数的问题：

- 参数需要占用额外的概念理解的精力，每次都需要解释它。
- 参数越多越难测试。

其它参数问题

1. Flag 参数。传递一个 boolean 参数给方法是很糟糕的，你明显的表现出这个 function 不是做一件事。
2. 参数对象。超过两个或三个参数应该封装成一个 class。
3. 动词和关键词。方法名应该是动词+名词对。如使用 `writeField()` 代替 `write()`。
4. 输出参数。输出参数比输入参数是更难理解的。我们既要关注输入是什么，也要关注输出是什么，它使我们花费双倍的精力。如果你的 function 必须改变某些状态，让它改变其拥有对象的状态。如 `appendFooter(String report) => report.appendFooter()`。

**Have No Side Effects**

你的 function 保证了做一件事，但可能存在其它隐藏的事情。示例如下：

```java
public boolean checkPassword(String userName, String password){
    User user = UserGateway.findByName(userName);
    if (user != User.NULL){
        if (user.getPassword.equals(password)){
            Session.initialize();
            return ture;
        }
    }
    return false;
}
```

上面的 `checkPassword()` 方法中的 `Session.initialize()` 是隐藏的事情。因为初始化会话这件事不属于检查密码。可以将方法改为 `checkPasswordAndInitializeSession()`，其实现如下：

```java
public boolean checkPasswordAndInitializeSession(String userName, String password){
    boolean passwordRight = checkPassword(userName, password);
    if (passwordRight){
        Session.initialize();
        return true;
    }
    return false;
}
```

**Command Query Separation**

Functions 应该是做某些事或者回答某些事，而不是两者都做。你的 function 应该改变一个对象的状态或者返回一个对象的一些信息。一个错误的例子如下：

```java
public boolean set(String attribute, String value);
```

上面这个方法即设置了对象的属性，又返回操作是否成功和属性是否存在。这会导致出现奇怪的语句，如 `if (set("username", "unclebob"))`。

上面的方法应该划分为两个方法，一个执行操作，一个查询状态。如下：

```java
public void set(String attribute, String value);
public boolean attributeExists(String attribute);
```

**Prefer Exception to Returning Error Codes**

执行命令的方法返回 error codes 轻微地违反了 command query separation 原则。当你返回一个 error code，调用者必须立刻处理 error，才能继续执行其它语句，这将导致代码很多层嵌套判断。示例代码如下：

```java
if (deletePage(page) == E_OK){
    if (registry.deleteReference(page.name) == E_OK){
        if (configKeys.deleteKey(page.name.makeKey()) == E_OK){
            ...
        } else {
            
        }
    }
}
```

你可以使用 exception 代替返回 error codes，这样的话，不需要立即处理错误情况，代码没有太多的嵌套，是代码更整洁。代码如下：

```java
try {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey());
}
catch (Exception e) {
	logger.log(e.getMessage());
}
```

Extract Try/Catch Blocks

Try/catch 代码块是丑陋的，它混合了正常代码和错误处理代码。更好的办法是将 try/catch 代码块封装到一个独立的方法。上面的代码可以改为：

```java
public void delete(Page page){
    try {
        deletePageAndAllReference(page);
    } catch (Exception e){
        logError(e);
    }
}
public void deletePageAndAllReferences(Page page) throws Exception {
    deletePage(page);
    registry.deleteReference(page.name);
    configKeys.deleteKey(page.name.makeKey());
}

private void logError(Exception e){
    logger.log(e.getMessage());
}
```

Error Handling Is One Thing

Functions 应该做一件事，Error handling 它是一件事，所以，一个处理 errors 的 function 不应该做其它的事。即在 catch/finally 代码块后面不应有其它的语句，或一个处理 error 的 function 仅包含 try/catch/finally 代码块。 

**Don't Repeat Yourself**

在软件中重复是所有罪恶的根源。我们应该尽可能的消除重复的代码。

### Clean functions 的总结

一个 function 应该尽量小，只做一件事，尽量少的参数，和使用描述性的名称。

如何只做一件事：只有一层抽象，不能划分为多个 sections，没有隐藏功能，命令和查询分离。

其它问题：错误处理，消除重复。

一个专业的程序员把系统当作故事来讲诉，而不是编写程序。



## Comments

> Don't comment bad code--rewrite it. 

注释的正确用法是为了弥补我们在代码中失败地表达自己。当你需要写注释时，你需要思考它有没有方式在代码中表达。每次你写了一个注释，你应该感到你表达能力的失败。

为什么尽量不写注释，因为程序员很少去维护注释的正确性。

- 注释不能弥补差的代码。与其花时间去写注释解释混乱的代码，不如花时间使代码整洁。

- 在代码中解释你自己。在很多时候可以创建一个方法来代替你注释要说明的。如下：

  ```java
  // Check to see if the employee is eligible for full benefits
  if ((employee.flog & HOURLY_FLAG) && (employee.age > 65))
  ```

  代替为

  ```java
  if (employee.isEligibleForFullBenefits())
  ```

### Good Comments

有些注释时有必要的或有益的。始终记住真正好的注释是找到一种方式不写注释。

常见的有必要的注释

- Legal Comments。版权和作者声明是有必要的和合理的。如 `// Copyright (C) 2003,2004,2005 by Object Mentor, Inc. All rights reserved.`
- Explanation of Intent。解释为什么这么做。如 `// we are greater because we are the right type.`
- Warning of Consequence。警告注释。如 `// Don't run unless you, have some time to kill`
- TODO Comments。TODO 一般用于此刻没时间做或者暂时做不了的事。它解释代码的问题和未来应该做什么。如 `//TODO-MdM these are not needed // We expect this to go away when we do the checkout model`
- Amplification。详述注释。用于详述某些重要的事情，其他人可能认为不重要的事。`// the trim is real important. It removes the starting spaces that could cause the item to be recognized as another list.`



## Formatting

Code formatting 是重要的，编码风格和可读性会持续影响软件的维护性和扩展性。

### Vertical Formatting

1. 行数。一个源码文件不应该超过 500 行，一般最好 200 行左右。小文件通常比大文件更容易理解。
2. 自顶向下阅读。最上面的方法提供高层次的概念和算法。细节应该是从上到下递增的。
3. 垂直的间隔。不同概念的一组代码应该用空行分隔。
4. 垂直的密度。垂直密度暗示了紧密的关系，相关性高得代码应该紧密排布在一起。
5. 垂直的距离。紧密关联的概念应该保持垂直方向接近的。1）变量声明语句应该尽可能接近它们使用的地方。2）实例变量应该声明在 class 的顶端。3）依赖的方法。一个方法调用另一个方法，它们应该在垂直方向相互接近的，调用者应该在被调者的上面。4）概念上类似。相似的内容应该在垂直方向相互接近的。如方法的重载，相同的方法名，不同的参数的方法，它们的功能是类似的。
6. 垂直的顺序。方法的调用依赖时指向下面方向的。

### Horizontal Formatting

1. 水平的字符数应该不超过 120 个字符。
2. 水平的间隔和密度。操作符与操作数之间有一个空格，将它们分成左边和右边两个部分，看起来更清晰。
3. 缩进。使用缩进来表示代码语句范围的层级结构。

### Team Rules

每一个程序员都有自己最喜欢的格式规则，但是如果在一个团队中工作，我们需要按照团队的规则去编写代码。一个团队的所有开发者应该同意一个编码风格，然后每个团队成员应该使用这个风格。我们需要一个统一的编码风格。



## Error Handling

错误处理时必须要做的一件事。输入可能异常，设备可能失败，总的来说，事情可能发生错误，程序员有责任确保当发生异常时系统可以正常工作。

1. 使用异常处理而不是返回值。使用异常处理比返回错误码更整洁。返回错误码需要立刻处理，会导致多层嵌套的复杂结构。
2. 尝试编写强制异常的测试。
3. 使用 Unchecked Exceptions。Checked exceptions 违反了 Open/Closed 原则。如果你抛出一个 checked exception，你需要在你 catch 和 throw 之间的所有方法上声明异常。
4. 根据调用者的需求定义异常类。我们定义异常类最重要是它们是怎么被捕获的。通常单个异常是较好的。我们可以简化 API 将多个异常包装成一个异常。
5. 不要用异常处理 try/catch 来做业务流程处理。即 catch 代码块中不应该出现业务代码。
6. 不要返回 Null。返回 null，给调用者造成额外的负担，null 会导致很多 null 检查代码。如果尝试返回 null 时，可以考虑抛出异常或者返回特殊对象代替。如 `Collections.emptyList()`。
7. 不要传递 Null 作为参数。



## Unit Test

单元测试保证了代码的每一个功能都是我们所期待的结果。

### Keeping Tests Clean

混乱的测试代码比没有测试更糟糕。测试代码必须随着生产代码的发展而改变，混乱的测试代码很难去改变，测试代码越来越多，越来越混乱，最终整个测试代码会被弃用。

没有测试代码，不能保证代码的改变是正常工作的，不能保证改变系统的一部分没有破环系统的其他部分。所以系统的缺陷率开始上升。他们停止清洁和整理生产代码，因为他们害怕改变会影响系统正常工作。他们的生产代码开始腐烂变坏。

测试代码和生产代码是一样重要的。它需要思考，设计和关注。它必须保持和生产代码一样整洁。

单元测试让我们的代码是灵活的、可维护的和可重用的。因为如果你有测试，你就不害怕改变代码，你可以不断地优化你的代码。

### Clean Tests

使测试代码整洁的方法：可读性。让测试方法和正常方法一样整洁，使它尽量的小，只做一件事，只有一层抽象，和使用描述性的名称等。

### Single Concept per Test

每个测试方法应该只测试单个概念。

### F.I.R.S.T

编写整洁测试代码的 5 个规则：

- Fast。测试应该是快的。如果测试运行很慢，你不想频繁地运行它们。如果你没有频繁地运行测试，你不会更早地发现问题，以及更轻松地修复问题。
- Indenpent。测试方法应该不依靠其它代码。你应该能够独立地测试，以及以任何顺序运行测试。
- Repeatable。测试应该在任何环境重复执行。如生产环境、QA 环境，和本地环境等。
- Self-Validating。测试应该有一个布尔输出。你可以不通过日志文件知道测试是否通过。
- Timely 。测试代码应该在业务代码之前写。



## Classes

### Class Organization

标准的 Java convention，一个 class 由一组变量开始，首先是 pubilc static constants，然后是 private static 变量，最后是 instance 变量。变量后面是方法，方法按照抽象层次由高往下。

### Class Should Be Samll

class 的第一规则是 class should be small。class 的名称应该说明其应履行的职责。我们应该能够不使用 “if”, "and", "or", "but" 等单词情况下，用 25 words 来写出 class 的简单的描述。

Single Responsibility Principle 表示一个 class 或 module 应该只有一个发生改变的原因。这个原则帮助我们进行职责的定义和对 class size 的指导。尝试识别职责（reasons to change）常常能帮助我们在代码中找到和创建更好的抽象。

Cohension。classes 应该有少量的实例变量。一般更多的变量被一个方法操作，这个方法对 class 来说越内聚。当一个 classes 不内聚，应该分离它们。

### Organizing for Change

大部分系统是不断改变的。每一个改变都可能使得系统的其它部分不能正常工作的风险。我们可以阻止我们的 classes 减少改变的风险。下面这些原则可以帮助我们更好地组织 classes 以减少代码改变带来的风险：SRP（Single Responsibility Principle），Open-Closed Principle（OCP），Dependency Inversion Principle（DIP）



## References

[1] Clean Code by Robert C. Martin