Title：设计模式总结

## 设计模式的总结

| Design Pattern          | What                                                         | Why                                                          | Examples                                                     | How                                                          | Functions        |
| ----------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | ---------------- |
| (Creational)            |                                                              |                                                              |                                                              |                                                              |                  |
| Abstract Factory        | 提供了一个接口来创建相关的对象家族，而不用指定它们具体的 classes。 | 整体的改变多个 objects。                                     | 应用切换不同的外观（scroll bar，window，button）             | Client 通过 FactoryProvider 获取一个 AbstractFactory 的子类实例，使用这个 Factory 去生产产品对象。 | 一致性           |
| Builder                 | 将复杂对象的构造与其表现分开，因此相同的构造过程可以创建不同的表现。 | 不改变过程，构造不同的对象。                                 | 阅读器需要一个格式转换处理器对象，不同处理器可以转换不同的格式。不改变转换逻辑，增加新的转换处理器。 | Client 通过将不同的 Builder 子类实例传给 Director，然后通过调用 director 的construct()方法得到产品对象。 | 扩展性           |
| Factory Method          | 让一个类的实例化延迟到子类。                                 | 使用抽象对象维持关系，一个抽象类型的具体子类决定另一个抽象类型的具体子类。 | 文本编辑器不同的子应用创建不同的文档。只有当具体的子应用创建后，才知道要创建哪个文档类型实例。 | 不同的 Creator 子类生产不同的 Product 子类。Client 通过创建不同的 Creator 子类来创建不同的 Product 子类。 | 灵活性           |
| Prototype               | 通过拷贝这个 prototype 去创建新的对象。                      | 想要重复创建类似的对象，且这个对象的创建过程比较复杂。       | 编辑音乐乐谱的应用，需要重复添加多个音符对象。               | Client 创建 Prototype 子类实例，然后调用它的 clone() 方法，得到这个类的对象拷贝。 | 复用性           |
| Singleton               | 确保一个 class 仅有一个实例，并且提供一个全局的访问它的方法。 | 有些 class 必须只有一个实例。                                | 一个系统应该仅有一个文件系统和一个窗口管理器。               | 方法一：（饥饿式）直接创建静态的实例的成员变量。方法二：（懒惰式）定义一个同步的静态方法获取实例。方法三：（懒惰式）在内部类中定义一个静态的实例的成员变量。 | 唯一性           |
| (Structural)            |                                                              |                                                              |                                                              |                                                              |                  |
| Adapter                 | 让不兼容的 classes 一起工作。Adapter 让 Adaptee 适应 Target。 | 一个不能兼容另一个接口的接口，希望他能够兼容。               | 画图编辑器有 lines, polygons, text 等元素。定义抽象的图形化接口 Shape，text 与 lines, polygons 有额外的操作，不同同时兼容一个接口。 | 方法一：（Object Adapter）Adapter 组合 Adaptee 。方法二：（Class Adapter）Adapter 实现 AdapteeImpl。 | 复用性           |
| Bridge                  | 解耦抽象与它的实现，使它们可以独立地改变。                   | 想要独立地修改、扩展，以及重用 abstraction 和 implementation。 | Window 有对应不同平台的子类，有不同特性的子类。都使用子类实现的话，需要多维度组合表示，难以添加新的子类。 | Client 通过将 implementation 子类实例作为参数，创建 abstraction 的子类实例。调用 abstract 对象的方法实际上是调用 implementation 的方法。 | 扩展性           |
| Composite               | 将对象组成树形结构以表示部分-整体层次结构。Composite 让 client 统一地对待单一对象和组合对象。 | 将多个 components 组成更大的 components，每个节点可以是单个组件，也可以是多个组件的组合，每个节点用统一的接口对象表示。 | 画图编辑器可以插入线、多边形和文字，可以插入图文。图文是图形和文字的组合。想把组合元素和单一元素统一对待，减少代码的复杂性。 | Composite 是 Component 的子类，它有一个 Component list 成员。Client 通过 new Composite() 来创建 Component 对象。 | 易用性，扩展性   |
| Decorator               | 动态地给一个 object 附加额外的职责。                         | 有时我们想为 objects 添加职责，而不是整个 class。            | 为文本阅读器添加 border 或者 scroll 等。                     | Client 创建一个 ConcreteComponent 对象，然后将对象作为参数，创建 Decorator 子类，调用 ConreteDecorator 对象是操作间接的调用 ConreteComponent 对象的操作。 | 扩展性           |
| Facade                  | 为一个 subsystem 中的一组接口提供一个统一的接口，使得 subsystem 更容易使用。 | 将系统构建为子系统降低它的复杂性，并且，想要最小化系统之间的交流和依赖。 |                                                              | Client 创建 Facade 实例，调用它的方法，它去调用子系统中的指定对象的方法。 | 易用性，松耦合   |
| Flyweight               | 通过共享可以有效地支持大量细粒度的对象。                     | 一个应用存在大量的相同的元素，如果每个元素都使用一个对象去表示，会造成空间的浪费。 | 文档编辑器中的相同的字符可以共享一个字符对象。               | Client 创建一个 FlyweightFactory，通过它来存储和去除共享的 Flyweight 对象。 | 复用性           |
| Proxy                   | Proxy 为另一个对象提供了一个代理，去控制对这个对象的访问。   | 推迟对象的创建和初始化的全部花费，直到我们真正需要使用它。   | 文档中的大图片需要很长时间去加载，我们可以控制文档的加载，先加载所有文字，再加载图片。 | Client 通过 new ConcreteProxy() 创建 Subject 实例。Proxy 是 Subject 的子接口。ConcreteProxy 对象存在一个对 Subject 对象的引用。 | 灵活性           |
| (Behavioral)            |                                                              |                                                              |                                                              |                                                              |                  |
| Chain of Responsibility | 通过给多个对象处理请求的机会，避免耦合请求发送者与接收者。   | 避免将请求者与具体的接收者绑定。                             |                                                              | Client 创建多个handler 将它们连接起来，把所有请求发送给第一个 handler。 | 松耦合、可扩展性 |
| Command                 | 将一个请求封装为一个对象，因此让你参数化客户端的不同请求。   | 有时需要发送一个请求给对象，但不知道任何关于请求的操作或者请求的接收者。 |                                                              | Client 创建 Invoker，Receiver 对象和 Command 对象。receiver 对象作为参数构建 command 对象。执行命令先调用 invoker 的 setCommand() ，再调用requestExecute() 方法。 | 松耦合、可扩展性 |
| Interpreter             | 给定一种语言，定义其语法表示形式，以及使用该表示形式来解释语言中的句子。 | 如果一个种特殊的问题经常发生，它可能值得用简单的语言将问题的实例表达为句子。我们可以构建 interpreter 通过解释这些句子来解决问题。 |                                                              | Context 对象表示句子，TerminalExpression 对象可以解释 context。NonterminalExpression 对象可以连接 TerminalExpression 对象进行解释。 | 可扩展性         |
| Iterator                | 提供以一种方法去顺序地访问聚合对象的元素，而不暴露它的底层表示。 | 访问它的元素而不暴露它的内部结构，提供统一的接口去遍历不同类型的聚合数据结构。 |                                                              | Aggreate 子类可以创建对应 Iterator 子类，Iterator 实现顺序遍历。 | 灵活性           |
| Mediator                | 定义一个对象去封装一组对象是如何交互。                       | 大量的相互连接让系统的行为很难去改变。Mediator 可以用来控制和协调一组对象之间的交互。 |                                                              | Mediator 作为参数创建不同的 ConcreteColleague 对象，colleague 之间的调用时通过调用 mediator 对象的方法实现。 | 松耦合、可扩展性 |
| Memento                 | 在不违反封装和外部化一个对象的内部状态等情况下，使得该对象可以在以后恢复之前的状态。 | 有时需要记录一个对象的内部状态。实现检查点和 undo 功能，让用户在发生错误时恢复状态记录的对象状态。 |                                                              | caretaker 对象可以存储和删除 memento 对象，memento 对象保存了 originator 对象的状态，caretaker 可以设置不同的 memento 来恢复 originator 对象的状态。 | 可恢复           |
| Observer                | 在对象中定义一对多的依赖，因此当一个对象改变状态时，所有它的依赖者是自动通知和更新的。 | 将系统划分为一组合作的 classes 常见的辅作用是需要维护相关对象之间的一致性。你不想通过使 classes 紧耦合来实现一致性，因为它降低了代码的可复用性。 |                                                              | Subject 对象可以 attach Observer 对象，可以 detach Observer 对象。Subject 中存储了所有 attach 的 Observer对象的list，当 Subject 状态发送改变时，自动通知所有 attach 的 Observer 对象。 | 松耦合、可扩展性 |
| State                   | 当一个对象的内部状态改变时允许改变它的行为。                 | 一个对象需要在不同的状态表现不同的行为。                     | TCPConnection class 他表示一个网络连接。一个 TCPConnection object 可可能是多个不同状态中的一个，如：Established，Listening，Closed。当一个 TCPConnection 对象接收到请求时，它可以根据当前的状态进行响应。 | Context 对象设置不同的 State 对象，执行 request() 方法得到不同的结果。 | 可扩展性         |
| Strategy                | 定义一组算法，封装每一个，以及让它们是可互换的。             | 一个行为可能切换不同的实现方式。                             |                                                              | Context 配置不同的 Strategy 对象，执行不同的算法。           | 扩展性           |
| Template Method         | 在操作中定义算法的骨架，将某些步骤推迟到子类。               | 通过使用抽象操作定义算法的某些步骤，模板方法可以固定其顺序，但可以让子类更改这些步骤以适合其需求。 |                                                              | Client 通过 new ConcreteClass() 来创建 AbstractClass，ConcreteClass 实现了父类抽象步骤的方法。 | 复用性           |
| Visitor                 | Visitor 可以让你定义新的的操作，而无需更改其所操作的元素的类。 | 抽象父类定义了一组操作，不同的子类不一定需要实现所有的操作。所有操作放在所有子类中，会让人感到疑惑，以及难以维护。 |                                                              | Client 定义一组 Element 对象，然后循环调用每个 Element 对象的 accept(Visitor visitor) 方法，可以对所有 Element 对象执行一个指定操作。 | 松耦合、可扩展性 |

## 设计模式的用途

设计模式常见的用途如下：

- 可复用性：减少冗余代码。
- 可扩展性：隔离职责，松耦合。很容易添加新类型的 class，容易添加新的功能。
- 灵活性：方便修改功能，少改动，独立修改。方便控制管理对象。
- 易用性：使得 Client 操作简单。

| 可复用性                                                   | 可扩展性                                                     | 灵活性                                  | 易用性                | 其他                                         |
| ---------------------------------------------------------- | ------------------------------------------------------------ | --------------------------------------- | --------------------- | -------------------------------------------- |
| Prototype<br />Adapter<br />Flyweight<br />Template Method | Builder<br />Bridge<br />Decorator<br />Chain of Responsibility<br />Command<br />Interpreter<br />Mediator<br />Observer<br />State<br />Strategy<br />Visitor | Factory Method<br />Proxy<br />Iterator | Composite<br />Facade | Abstract Factory<br />Singleton<br />Memento |

