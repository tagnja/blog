---
title: 浅谈设计模式（二）：创建型设计模式
date: 2019-12-18 12:02:57
categories: 
- 软件设计与架构
tags: design-patterns
---



本篇将介绍设计模式中的5种创建型设计模式，其中设计模式的实现代码使用 Java 语言描述。

## Creational Design Patterns

创建型设计模式是抽象实例化过程。它们帮助使系统独立于对象是如何创建、如何组合和如何表示的。

创建型模式封装了系统使用的具体的 classes 的细节，隐藏了 classes 的 objects 是如何创建的和如何一起协作的，系统最多知道对象的接口或抽象类是什么。创建型模式给你很大的灵活性在“创建什么”，“谁创建的”，“怎样创建的”和“什么时候创建的”等方面。

## Abstract Factory

### What

Abstract Factory 模式提供了一个接口来创建相关的对象家族，而不用指定它们具体的 classes。

### Why

Motivation

我们想要整体的改变多个 objects，从一种类型的对象家族改变为另一种类型。

例子：一个应用可以切换不同的外观，主要是切换 widgets 的样式，widgets 包括：scroll bars，windows，和 buttons。不同的风格需要创建不同的 objects，我们希望风格切换时，可以轻易地创建不同风格的 widgets 对象。

Applicability

- 一个系统它的产品是如何 created，composed，和 represented 应该是独立的。
- 一个系统应该是配置多个产品家族中的一个。
- 相关的家族产品对象是设计成一起使用。
- 你想要提供一个产品的类库，只暴露它们的接口，不暴露它们的实现。

### Solution

Structure

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/abstract-factory-structure.png" class="img-center" />

Participants

- AbstactFactory：提供创建抽象产品对象的接口。
- ConcreteFactory：实现创建具体产品对象的操作。
- AbstractProduct：定义一类产品对象的接口。
- ConcreteProduct：具体的产品对象。
- Client：仅仅使用 AbstractFactory 和 AbstractProduct classes 去创建家族产品对象。

Collaborations

在运行时，正常只有单个 ConcreteFactory class 实例是创建的。这个 concrete factory 创建产品对象有特定的实现。创建不同的产品对象，client 应该使用不同的 concrete factory。

AbstractFactory 将产品对象的创建推迟到它的 subclass ConcreteFactory.

Implementations

<details>
  <summary>Click to expand!</summary>


```java
interface AbstractFactory{
    abstract ProductA createProductA();
    abstract ProductB createProductB();
}                                                                              
	
class ConcreteFactory1 implements AbstractFactory{
    public ProductA createProductA(){
        return new ProductA1();
    }
    public ProductB createProductB(){
        return new ProductB1();
    }
}

class ConocreteFactory2 implements AbstractFactory{
    public ProductA createProductA(){
        return new ProductA2();
    }
    public ProductB createProductB(){
        return new ProductB2();
    }
}
	
interface ProductA{}
class ProductA1 implements AbstractProductA{}
class ProductA2 implements AbstractProductA{}
	
interface ProductB{}
class ProductB1 implements AbstractProductB{}
class ProductB2 implements AbstractProductB{}

public class FactoryProvider{
	public static AbstractFactory getFactory(String choice){
        return "1".equals(choice) ? new ConcreteFactory1() : new ConcreteFactory2();
    }
}

public class Client{
    public static void main(String[] args){
        AbstrsctFactory factory = FactoryProvider.getFactory("1");
        ProductA productA = factory.createProductA();
        ProductB productB = factory.createProductB();
    }
}
```

</details>

### Consequences

Benefits

- 它隔离具体的 classes。
- 它使得改变产品家族对象变得容易。
- 它提升了产品对象的一致性。

Drawbacks

- 支持新种类的产品是困难的。他需要修改 AbstractFactory 接口和它的子类。

## Builder

### What

将复杂对象的构造与其表现分开，因此相同的构造过程可以创建不同的表现。

### Why

Motivation

不改变过程，想要增加新的实现方式。

例子：阅读器软件可以将 RTF（Rich Text Format）文档转换为很多其他的文本格式。每一种格式转换处理器对应一个 Converter 对象，它们都是 TextConverter 的子类。我们想要不改变阅读器转换的处理逻辑，轻易的增加新的格式转换处理器。

Applicability

- 创建一个复杂的对象的算法应该是独立于组成对象的部分及其组装方式。
- 对象构造过程必须允许不同的表现。

### Solution

Structure

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/builder-structure.png" class="img-center" />

Participants

- Bulider：指定一个创建产品对象的抽象接口。
- ConcreteBuilder：实现 Builder 接口，构造和装配产品对象。
- Director：使用 Builder 接口构造对象。
- Product：要被创建的产品对象。

Collaborations

- Client 创建 Director 对象和配置一个想要的 Builder 对象。
- Director 通知 builder 产品对象什么时候应该构建。
- Builder 处理来自 director 的请求。
- Client 从 builder 中取出产品对象。

Implementations

<details>
  <summary>Click to expand!</summary>


```java
interface Builder{
    void buildComponet1();
    void buildComponet2();
}
public class ConcreteBuilder1 implements Builder{
    Product product;
    public ConcreteBuilder1(){
        this.product = new Product();
    }
    void buildComponet1(){
        this.product.setComponet1(xxx);
    }
    void buildComponet2(){
        this.product.setComponet2(xxx);
    }
    public Product getProduct(){
        return this.product.
    }
}
public class ConcreteBuilder2 implements Builder{
    Product product;
    public ConcreteBuilder2(){
        this.product = new Product();
    }
    void buildComponet1(){
        this.product.setComponet1(xxx);
    }
    void buildComponet2(){
        this.product.setComponet2(xxx);
    }
    public Product getProduct(){
        return this.product.
    }
}

public class Director{
    private Builder builder;
    public Director(Builder builder){
        this.builder = builder;
    }
    public void construct(){
        this.builder.buildComponet1();
        this.builder.buildComponet2();
    }
    pubilc Product getProduct(){
        return this.builder.getProduct();
    }
}

public class Client{
    public static void main(String[] args){
        Builder builder = new ConcreteBuilder1();
        Director director = new Director(builder);
        director.construct();
        Product product = director.getProduct();
    }
}
```

</details>

### Consequences

Benefits

- 它让你轻易改变一个产品内部的表现。
- 它隔离了构建和表现得代码。构建过程不变，可以有不同的表现。
- 它让你更好的控制构建过程。

Drawbacks

- 改变产品的内部表现，需要定义不同的 Builder 子类。

## Factory Method

### What

定义一个创建对象的接口，但是让子类决定那个类应该被实例化。Factory Method 让一个类的实例化延迟到子类。

### Why

Motivation

使用抽象对象维持关系，一个抽象类型的具体子类决定另一个抽象类型的具体子类。把对象的实例化延迟到子类。

例子：文本编辑器可以创建多种格式的文档，不同的子应用决定了不同的文档类型，如 WPS 软件可以创建 word，excel，ppt 等文档。系统只知道创建一个应用就要创建一个对应的文档，但是不能预测文档 Document 的哪个子类被实例化。可以通过 Factory Method 解决这个问题，封装 Document 具体子类的创建过程，将它从客户端处理过程分离。

Applicability

- 一个必须被实例化的 class 不能被实例化。
- 一个 class 想要它的子类去指定哪个对象要被创建。

### Solution

Structure

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/factory-method-structure.png" class="img-center" />

Participants

- Product：定义要被创建的对象的接口。
- ConcreteProduct：实现 Product 的具体的类。
- Creator：声明 factory method，返回 Product 类型的对象。
- ConcreteCretor：override factory method 返回一个 ConcreteProduct 的实例。

Collaborations

- Creator 依靠它的子类去返回一个合适的 ConcreteProduct 实例。

Implementations

<details>
  <summary>Click to expand!</summary>


```java
public interface Product{}

public class ProductA implements Product{}

public class ProductB implements Product{}

public interface Creator{
    Product createProduct();
}

public class CreatorA implements Creator{
    Product createProduct(){
        return new ProductA();
    }
}

public class CreatorB implements Creator{
    Product createProduct(){
        return new ProductB();
    }
}

public class Client{
    Creator creator = new CreatorA();
    Product productA = creator.createProudct();
}
```

</details>

### Consequences

Benefits

- 消除绑定具体的子类的代码。代码仅仅处理 Product 接口。
- 更灵活的创建对象。
- 连接平行的类的层次结构。

Drawbacks

- Client 必须通过实例化 Creator 子类去创建一个特定的 ConcreteProduct 对象。当 ConcreteProduct 类增加时，ConcreteCreator 类也需要增加 。

## Prototype

### What

指定使用 prototype 实例创建的对象的类型，并且通过拷贝这个 prototype 去创建新的对象。

### Why

Motivation

想要重复创建类似的对象，且这个对象的创建过程比较复杂。

例子：一个可以编辑音乐谱的编辑器。他需要重复的添加多个音符。

Applicability

- 一个系统它的产品对象的创建，组合，和表现应该是独立的。
- 一个 class 的实例化在运行时指定的。
- 很方便的去克隆大量的对象，而不是手动实例化。

### Solution

Structure

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/prototype-structure.png" class="img-center" />

Participants

- Prototype：定义一个克隆自己的接口。
- ConcretePrototype：实现克隆自己的操作。
- Client：通过请求 prototype 克隆方法来创建对象。

Collaborations

- Client 请求 prototype 去克隆自己。

Implementations

<details>
  <summary>Click to expand!</summary>


```java
public interface Prototype extends Cloneable{
    Prototype clone();
}
public class ConcretePrototype1 implements Prototype{
    private String name;

    public ConcretePrototype1(){
        this.name= "ConcretePrototype1-aha";
        // simulating complex construction process
        try {
            Thread.sleep(1);
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }

    public ConcretePrototype1 clone() {
        Object prototype = null;
        try {
            prototype = super.clone();
        } catch (CloneNotSupportedException e) {
            e.printStackTrace();
        }
        return (ConcretePrototype1) prototype;
    }
}
public class ConcretePrototype2 implements Prototype{
	//... same with ConcretePrototype1
}
public class Client{
    public static void main(String[] args){
        ConcretePrototype1 concretePrototype1 = new ConcretePrototype1;
        ConcretePrototype1 copy1 = concretePrototype1.clone();
        System.out.println(copy1.equals(concretePrototype1));
    }
}
```

</details>

### Consequences

Benefits

- Prototype 有一些和 Abstract Factory 和 Builder 模式类似的优点，即对 client 隐藏具体的产品类，这些模式让 client 使用不同的具体的子类，却不用修改 Client 实现逻辑。
- 在运行时动态地增加或去除产品对象。

Drawbacks

- 每一个 Prototype 必须实现 clone 操作，这个 clone 操作的实现有可能是复杂的。如，不支持拷贝或存在循环参考。

## Singleton

### What

确保一个 class 仅有一个实例，并且提供一个全局的访问它的方法。

### Why

Motivation

有些 class 必须只有一个实例。如，一个系统应该仅有一个文件系统和窗口管理器。

Applicability

- 一个 class 必须有且仅有一个实例，并且有一个全局访问的接入点。

### Solution

Structure

<img src="https://taogenjia.com/img/design-patterns-structure-and-example/singleton-structure.png" class="img-center" />

Participants

- Singleton：它负责去创建一个唯一的实例。它提供一个获取实例的操作，让 client 得到唯一的实例。

Collaborations

- Client 通过 Sigleton 的获取实例操作，获取到唯一的 Singleton 实例。

Implementations

- Private constructor method.
- Public getInstance() method.

<details>
  <summary>Click to expand!</summary>


```java
// Hungry
public class Singleton{
    private final static Singleton instance =  new Singleton();
    private Singleton(){}
    public static Singleton getInstance(){
        return instance;
    }
}

// Lazy 1 by lazily create instance.
public class Singleton
{
	private static Singleton instance;
	private Singleton() {}
	public synchronized static Singleton getInstance() 
	{
		if (instance == null)
		{
			synchronized (Singleton.class)
			{
				if (instance == null)
				{
					instance = new Singleton();
				}
			}
		}
		return instance;
	}
}

// Lazy 2 by Inner Class
public class Singleton 
{
	private Singleton() {}
	
	private static class Inner
	{
		private static final Singleton INSTANCE = new Singleton();
	}
	
	public static Singleton getInstance() 
	{
		return Inner.INSTANCE;
	}
}
```

</details>

### Consequences

Benefits

- 控制访问唯一的实例。

## References

[1] Design Patterns: Elements of Reusable Object-Oriented Software by Erich Gamma, Richard Helm, Ralph Johnson and John Vlissides