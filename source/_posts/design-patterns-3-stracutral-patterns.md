---
title: 浅谈设计模式（三）：结构型模式
date: 2019-12-20 17:36:54
categories: 
- 软件设计与架构
tags: design-patterns
---

本篇将介绍设计模式中的7种结构型模式，其中设计模式的实现代码使用 Java 语言描述。

## Structural Design Patterns

结构型设计模式关注 classes 和 objects 如何组成更大的结构。

## Adapter

### What

Adapter 将一个 interface class 转换为 Client 期望的另一个 interface。Adapter 让不兼容的 classes 一起工作。

### Why

Motivation

一个不能兼容另一个接口的接口，希望他能够兼容。

例子：画图编辑器可以让用户画图和布置图形元素，如 lines，polygons，text 等。定义了一个抽象的图形化接口为 Shape。为每种图形对象定义子类，LineShape 对应 lines，PolygonShape 对应 polygons。基本的集合图形是比较容易实现的，但是对文本的编辑很难实现，文本类 TextView 存在额外的方法，它不能兼容 Shape 接口。我们可以使用 Adapter 模式让其兼容。我们可以定义 TextShape 它使得 TextView 接口适应 Shape 接口，其中 TextShape 为 Adapter，TextView 为 Adaptee。

Adapter 模式有两种实现方式，一种是继承，另一种是组合。（1）让 TextShape 实现 Shape 接口，以及继承 TextView 的实现。（2）让 TextShape 实现 Shape 接口，以及组合一个 TextView 实例。

Applicability

- 你想要用一个已存在的 class，它的接口不匹配你的需求。
- 你想要创建一个重用的 class ，它能够于不相关的或无法预见的 classes 合作，即不一定具有兼容的接口。
- 你需要使用一些存在的子类，但是它是不能兼容每一个子类的接口。

### Solution

Structure

Adapter 使用 multiple inheritance 实现的结构：

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/adapter-structure-1.png" class="img-center" />

Adapter 使用 object composition 实现的结构：

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/adapter-structure-2.png" class="img-center" />

Participants

- Target：定义 Client 使用的特定领域的接口。
- Client：与符合 Target 接口的对象协作。
- Adaptee：定义一个需要适应的接口。
- Adapter：使 Adaptee 接口适应 Target 接口的类。

Collaborations

- Clients 请求 Adapter 实例的方法。Adapter 通过请求 Adaptee 的方法得到结果。
- Adapter 让 Adaptee 适应 Target。 

Implementations

方法一：Object Adapter（Adapter 组合 Adaptee 实例和实现 Target 接口）

<details>
  <summary>Click to expand!</summary>


```java
public interface Target{
    public void requestA();
}

public class TargetImpl{
    public void requestA(){
        System.out.println("requestA");
    }
}

public interface Adaptee{
    public void requestB();
}

public class AdapteeImpl implements Adaptee{
    public void requestB(){
        System.out.println("requestB");
    }
}

public class Adapter implements Target{
    private Adaptee adaptee;
    public Adapter(Adaptee adaptee){
        this.adaptee = adaptee;
    }
    public void requestA(){
        System.out.println("Adapter requestA");
    }
    public void requestB(){
        adaptee.requestB();
    }
}

public class Client{
    public static void main(String[] args){
        Adapter adapter = new Adapter(new AdapteeImpl());
        adapter.requestA();
        adapter.requestB();
    }
}
```

</details>

方法二：Class Adapter（Adapter 继承 AdapteeImpl 和实现 Target 接口）

<details>
  <summary>Click to expand!</summary>


```java
public interface Target{
    public void requestA();
}

public class TargetImpl{
    public void requestA(){
        System.out.println("requestA");
    }
}

public interface Adaptee{
    public void requestB();
}
public class AdapteeImpl implements Adaptee{
    public void requestB(){
        System.out.println("requestB");
    }
}
public class Adapter extends AdapteeImpl implements Target{
	public void requestA(){
        System.out.println("Adapter requestA");
    }
}

public class Client{
    public static void main(String[] args){
        Adapter adapter = new Adapter();
        adapter.requestA();
        adapter.requestB();
    }
}
```

</details>

### Consequences

Class Adapter (Inheritance)

- 它只能让 Adaptee 的具体子类适应 Target。但它不能使一个 class 和它的子类都适应 Target。
- 可以让 Adapter 重写 Adaptee 的一些行为。
- 仅仅引入一个对象，并且不需要其他指针间接访问 adaptee。

Object Adapter (Composition)

- Adapter 让 Adaptee 和它的所有子类都适应 Target。Adapter 可以同时为所有 Adaptee 增加功能。
- 很难 override Adaptee 的行为。他需要参考 Adaptee 的子类，而不是自己作为它的子类。

## Bridge

### What

Bridge 模式解耦抽象与它的实现，因此它们可以独立地改变。抽象类和实现类没有通过 implement 关键字进行绑定，而是通过构造方法注入的方式，将实现类注入到抽象类中。

### Why

Motivation

一个抽象有很多种可能的实现，通常使用 inheritance 来实现。定义一个 interface 去抽象，和具体的 subclasses 不同的方式去实现。但是这种方式不总是足够的灵活。inheritance 将 implementation 永久地绑定了 abstraction，这使得很难独立地去修改、扩展，以及重用 abstraction 和 implementation。

例子：Window 需要在两个平台（X Window System and IBM's Presentation Manager）实现，可以定义一个抽象类 Window 和定义两个子类 XWindow 和 PMWindow。这个方法有两个缺点：1. 它不方便去扩展 Window 抽象在不同种类的 windows 或新的平台。支持新的 IconWindow 需要实现两个新的类 XIconWindow 和 PMIconWindow。支持新的平台需要每种 window 都需要新的 Window 子类。2. 它使得 client 代码是平台依赖的。

Client 应该能够创建 window 对象时不需要指定具体的实现。仅仅只有 window implementation 应该是依赖平台的，Windows 的 abstraction 不需要指定平台。

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/bridge-example.png" class="img-center" />

Applicability

- 你想避免永久地绑定抽象和它的实现。
- 抽象和它的实现都应该通过子类扩展。
- 改变一个抽象的实现应该不会对 Client 有影响。
- 你想要完全地隐藏一个抽象的实现。
- 你的 classes 种类非常多。class 层级结构表明 classes 需要分离到两个部分。
- 你想多个 objects 共享一个实现，并且这个事实应该对 client 隐藏。

### Solution

Structure

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/bridge-structure.png" class="img-center" />

Participants

- Abstraction：定义 abstraction 的接口。维护一个对 Implementor 的对象的参考。
- RefinedAbstraction：扩展 Abstraction 接口。
- Implementor：定义 implementation 的接口。
- ConcreteImplementor：实现 implementor 接口。

Collaborations

- Abstraction 将 client 的请求转发到它的 implementor 对象。

Implementations

<details>
  <summary>Click to expand!</summary>


```java
abstract interface Abstraction{
    protected Implementor implementor;
    protected Abstraction(Implementor implementor){
        this.implementor = implementor;
    }
    abstract public void operation();
}

public class AbstractionImpl{
    public AbstractionImpl(Implementor implementor){
        super(implementor);
    }
    public void operation(){
        implementor.operationImpl();
    }
}

public interface Implementor{
    void operationImpl();
}

public class ImplementorImpl1{
    public void operationImpl(){
        System.out.println("operation implements by ImplementorImpl1...");
    }
}
public class ImplementorImpl2{
    public void operationImpl(){
        System.out.println("operation implements by ImplementorImpl2...");
    }
}

public class Client{
    public static void main(String[] args){
        Abstraction abstraction = new AbstractionImpl(new ImplementorImpl2());
        abstraction.operation();
    }
}
```

</details>

### Consequences

Benefits

- 解耦 interface 和 implementation。
- 提升扩展性。你可以独立地扩展 Abstraction 和 Implementor 层级结构。
- 可以对 client 隐藏 implementation 的实现。

## Composite

### What

将对象组成树形结构以表示部分-整体层次结构。Composite 让 client 统一地对待单一对象和组合对象。

### Why

Motivation

可以将多个 components 组成更大的 components，每个节点可以是单个组件，也可以是多个组件的组合，每个节点用统一的接口对象表示。

例子：如画图编辑器，可以画线、多边形和文字，也可以是图文的方式。图文这种元素是多种组件的结合，我们想把组合元素和单一元素一样对待，减少代码的复杂，可以使用 Composite 模式来实现。

Applicability

- 你想表示对象的部分整体层次结构。
- 你想让 Client 忽略组合对象和单一对象的区别。Client 统一地对待所有 Composite 接口的 Object。

### Solution

Structure

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/composite-structure.png" class="img-center" />

Participants

- Component：为 Composite 对象定义一个接口。为访问和管理子节点声明接口。
- Composite：实现 Component 的类。它为有 Children 的组件定义行为。存储子组件。实现 child-related 操作。
- Client：通过 Component 接口库操作 Composite 中的对象。

Collaborations

- Client 使用 Component class interface 去与 Composite 结构中的 Objects 进行交互。

Implementations

<details>
  <summary>Click to expand!</summary>


```java
public interface Component{
    void operation();
    void add(Component component);
    void remove(Component component);
    Component getChildren(int index);
}

public class Composite implements Component{
    private String name;
    public Composite(String name){
        this.name = name;
    }
    private List<Composite> compositeList;
    public void operation(){
        System.out.println("I am " + name);
        if (compositeList != null){
            for (Composite composite : compositeList){
                composite.operation();
            }
        }
    }
    public void add(Composite composite){
        if (compositeList == null){
            compositeList = new ArrayList<Composite>();
        }
        compositeList.add(Composite)
    }
    public void remove(Composite composite){...}
    public Composite getChild(int index){
        if (index >= 0 && && composite != null && index < compositeList.size()){
            return compositeList.get(index);
        }
        return null;
    }
}

public class Client{
    public static void main(String[] args){
        Component parent = new Composite("parent");
        Component child = new Composite("child");
        parent.add(child);
        parent.operation();
        child.operation();
    }
}
```

</details>

### Consequences

Benefits

- 定义由 primitive objects 和 composite objects 组成的 class 层级结构。Primitive objects 可以组合为更复杂的 objects。
- 它使得 client 简单。 Client 可以统一地对待 composite structures 和 individual objects。
- 更容易添加新类型的 components。

Drawbacks

- 它使你的设计过于笼统。很难去限制 composite component。你需要自己在运行时检查对 composite component 的约束，如限制一个组合组件的子组件的个数。

## Decorator

### What

动态地给一个 object 附加额外的职责。Decorator 为子类提供了灵活的替代方案，以扩展功能。

### Why

Motivation

有时我们想为 objects 添加职责，而不是整个 class。如，为文本阅读器添加 border 或者 scroll 等。

一种实现方式是通过 inheritance 来添加职责，这种方式是不灵活的，它是静态的，它为每一个实例都添加了这个职责，而且每次添加额外的职责都需要修改 class。另一种更灵活的方法是使用 Decorator 设计模式。它让你循环嵌套 decotrators，允许无限的添加职责。

Applicability

- 动态地、透明地为单个 object 添加职责，没有影响其他的 objects。
- 职责是可以被撤回的。
- 当不能使用子类去扩展。

### Solution

Structure

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/decorator-structure.png" class="img-center" />

Participants

- Component：定义组件接口。
- ConcreteComponent：实现组件接口的 class。
- Decorator：为 Component 对象维护一个引用。定义一个符合 Component 接口的接口。
- ConcreteDecorator：为 component 对象添加职责的 class。

Collaborations

- Decorator 将请求转发给它的 Component 对象。它可能选择性地在请求之前和之后执行额外的操作。

Implementations

<details>
  <summary>Click to expand!</summary>


```java
public interface Component{
    pubilc String operation();
}

public class ConcreteComponet{
    public String operation(){
        return "concreteComponent operation..."
    }
}

public interface Decorator extends Component{}

public class ConcreteDecorator{
    private Component component;
    public ConcreteDecorator(Component component){
        this.component = component;
    }
    public String operation(){
        return this.component.operation() + "ConcreteDecorator1 operation..."
    }
}

public class Client{
    public static void main(String[] args){
        Component component = new ConcreteComponent();
        component = new ConcreteDecorator(component);
        System.out.println(component.operation());
    }
}
```

</details>

### Consequences

Benefits

- Decorator 比静态 inheritance 更灵活。
- 避免功能丰富的 classes 增加层级结构。

Drawbacks

- decorator 和它的 component 不是同一个 object。当你使用 decorator 时，你不应该依赖一个对象。
- 系统存在大量很小的 objects。

## Facade

### What

为一个 subsystem 中的一组接口提供一个统一的接口。Facade 定义了一个更高层级的接口，它使得 subsystem 更容易使用。

### Why

Motivation

将系统构建为子系统有助于降低复杂性。一个普遍的设计目标时最小化系统之间的交流和依赖。一种实现这个目标的方式是使用 Facade 模式。

Applicability

- 你想为复杂的子系统提供一个简单的接口。
- 在 Client 和抽象类的实现之间有很多依赖。使用 Facade 去解耦子系统与 client 和其他子系统，从而提升子系统的独立性和可移植性。
- 你想将你的子系统分层。使用 Facade 为每个子系统定义一个接入点。

### Solution

Structure

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/facade-structure.png" class="img-center" />

Participants

- Facade：知道 subsystem 中的哪个 class 能处理哪个 request。将 client 的请求委托给合适的 subsystem 的 objects。
- subsystem classes：subsystem 的 classes。

Collaborations

Implementations

<details>
  <summary>Click to expand!</summary>


```java
public interface SubSystemInterface{
    public String handleRequest();
}
public class SubSystemClass1 implements SubSystemInterface{
    public String handleRequest(){
        return "SubSystemClass1 return result...";
    }
}
public class SubSystemClass2 implements SubSystemInterface{
    public String handleRequest(){
        return "SubSystemClass2 return result...";
    }
}

public class Facade{
    public String handleRequest1(){
        return new SubSystemClass1().handleRequest();
    }
    public String handleRequest2(){
        return new SubSystemClass2().handleRequest();
    }
}

public class Client{
    public static void main(String[] args){
        Facade facade = new Facade();
        String result1 = facade.handleRequest1();
        String result2 = facade.handleRequest2();
    }
}
```

</details>

### Consequences

Benefits

- 它对 client 隐蔽子系统的 components，减少 client 需要处理的 objects 的数量，以及使得 subsystem 更容易使用。
- 它减少了 subsystem 和 clients 之间的耦合。
- 它没有阻止应用程序在需要时使用子系统的 classes。因此你可以在易用性和通用性之间进行选择。

## Flyweight

### What

通过共享可以有效地支持大量细粒度的对象。

### Why

Motivation

一个应用存在大量的相同的元素，如果每个元素都使用一个对象去表示，那么会出现大量内容相似的对象，这样是没有必要的，造成空间的浪费。我们可以通过共享对象来处理这个问题。

例子：文档编辑器由文本编辑和格式化的功能。一个文档中如果每一个 character 创建一个对象，那么会出现大量的对象。我们可以每一种字符创建一个对象，然后所有相同类型的字符都共享一个相同的对象。

Applicability

- 应用使用了大量的对象。
- 对象的存储花费很高。
- 大部分对象是没有本质区别的，是可以共享相同对象的。
- 应用不依赖对象的本身。

### Solution

Structure

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/flyweight-structure.png" class="img-center" />

Participants

- Flyweight：定义一个接口，Flyweight 可以通过该接口来接收外部状态，并对其存储的内部状态进行操作。
- ConcreteFlyweight：实现 Flyweight 接口的类。它存储 intrinsic state。它的对象是可被分享的。
- UnsharedConcreteFlyweight：不需要分享的 Flyweight 的 subclasses。不是所有的 Flyweight subclasses 需要被分享。
- FlyweightFactory：创建和管理 Flyweight 对象。确保 Flyweight 是正确地分享的。
- Client：维护对 Flyweight 的引用。计算和存储 Flyweight 的 extrinsic state。

Collaborations

- Intrinsic state 在 ConcreteFlyweight 对象中存储，Extrinsic state 在 Client 对象中存储和计算。当调用对象的操作时，Client 传递这个 state 给 flyweight。
- Client 不应该直接实例化 ConcreteFlyweight。Client 必须通过 FlyweightFactory 对象来获取 ConcreteFlyweight 对象，以确保它们是正确地分享。

Implementations

<details>
  <summary>Click to expand!</summary>


```java
public interface Flyweight{
    void operation(String extrinsicState);
}

public class ConcreteFlyweight implements Flyweight{
    public String intrinsicState;
    public ConcreteFlyweight(String intrinsicState){
        this.intrinsicState = intrinsicState;
    }
    public void operation(String extrinsicState){
        return intrinsicState;
    }
}

public class FlyweightFactory{
    Map<key, Flyweight> flyweightList = new HashMap<>();
    
    public Flyweight getFlyweight(String key){
        if (flyweightList.keySet().contains(key)){
            return flyweightList.get(key);
        }else{
            Flyweight newFlyweight = new ConcreteFlyweight(key);
            flyweightList.add(newFlyweight);
            return newFlyweight;
        }
    }
}

public class Client{
    FlyweightFactory flyweightFactory = new FlyweightFactory();
    Flyweight flyweight = flyweightFactory.get("a");
    flyweight.operation("print")
}
```

</details>

### Consequences

Benefits

- Flyweight 是节省空间的，它减少了对象实例的数量，对象被共享的越多节省越多的空间。

Drawbacks

- Flyweight 可能带来与 transferring, finding, computing extrinsic state 相关的 run-time costs。

## Proxy

### What

Proxy 为另一个对象提供了一个代孕（Surrogate）或占位符（Placeholder）去控制对这个对象的访问。

### Why

Motivation

控制访问一个对象的原因是推迟它的创建和初始化的全部花费，直到我们真正需要使用它。如，文档编辑器可以在文档中嵌入图形对象。创建一些非常大的图像是十分昂贵的。一般来说，打开一个文档应该越快越好，所以我们应该避免在文档打开的时候立刻创建昂贵的对象。解决这个问题可以使用另一个 image proxy 对象，而不是 image 对象，proxy 对象作为真实 image 的替身。

Applicability

- Remote proxy。为在不同地址空间的对象提供一个本地的代表。
- Virtual proxy。创建昂贵的对象 on demand。
- Protection proxy。通过控制访问来保护原始对象。
- Smart reference。在访问对象时执行额外的操作。

### Solution

Structure

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/proxy-structure-1.png" class="img-center" />

运行时 proxy 结构

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/proxy-structure-2.png" class="img-center" />

Participants

- Proxy：1）维护一个引用，让 proxy 访问 real subject。2）提供一个与 Subject  相同的接口，使得 proxy 可以代替 real subject。3）控制对 real subject 的访问，以及可能有创建和删除它的职责。
- Subject：为 RealSubject 定义一个公共接口。
- RealSubject：定义 proxy 表示的 real object。

Collaborations

- Proxy 在适当的时候将请求转发给 RealSubject，具体取决于 proxy 的类型。

Implementations

<details>
  <summary>Click to expand!</summary>


```java
public interface Subject{
    void request();
}

public class RealSubject implements Subject{
    public void request(){
        System.out.println("request to RealSubject...");
    }
}

public interface Proxy extends Subject{}

public class ConcreteProxy implements Proxy{
    RealSubject realSubject = new RealSubject();
    public void request(){
        System.out.println("before ...");
        realSubject.request();
        System.out.println("after ...");
    }
}

public class Client{
    public static void main(String[] args){
        Subject subject = new ConcreteProxy();
        subject.request();
    }
}
```

</details>

### Consequences

Benefits

- remote proxy 可以隐藏位于不同地址空间的对象。
- virtual proxy 可以执行优化。如，按需创建对象。
- protection proxy 和 smart reference 在对象被访问的时候允许额外的看管。

## References

[1] Design Patterns: Elements of Reusable Object-Oriented Software by Erich Gamma, Richard Helm, Ralph Johnson and John Vlissides

[2] [Difference between object adapter pattern and class adapter pattern](https://stackoverflow.com/questions/9978477/difference-between-object-adapter-pattern-and-class-adapter-pattern)

