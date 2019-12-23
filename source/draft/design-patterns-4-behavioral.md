Title：设计模式（三）：行为型模式

## Chain of Responsibility
### What

通过给多个对象处理请求的机会，避免耦合请求发送者与接收者。Chain 接收请求，并沿着 chain 传递请求直到有一个对象能够处理该请求为止。

### Why

Motivation

避免将请求者与具体的接收者绑定。使用一个类统一接收所有请求，接收者连接成一条链来处理请求。

Applicability

- 超过一个对象去处理请求。
- 你想发送一个请求给一些对象中的一个，并且不明确指定接收者。
- 动态指定一组对象能处理一个请求。

### Solution

Structure

类结构

<img src="../img/design-patterns-structure-and-example/chainofresponsibility-structure.png" class="img-center" />

对象结构

<img src="../img/design-patterns-structure-and-example/chainofresponsibility-structure-2.png" class="img-center" />

Participants

- Handler：定义一个接口去处理请求。
- ConcreteHandler：处理请求。接收继任者（successor）。如果它能处理一个请求，则处理它；如果不能，则转发这个请求给它的继任者。
- Client：初始化一个请求给 chain 上的一个 ConcreteHandler 对象。

Collaborations

- 当 Client 发送一个请求，这个请求在 Chain 上传播，直到有一个 ConcreteHandler 对象能够处理它。

Implementations

<details>
  <summary>Click to expand!</summary>

```java
public abstract class Handler{
    private Handler nextHandler;
    
    public abstract void handleRequest();
    
    public void setNext(Handler handler){
        this.nextHandler = handler;
    }
}

public class ConcreteHandler1 extends Handler{
    public void handleRequest(int request){
        if (request == 1){
            System.out.println("handle by ConcreteHandler1");
        }else{
            if (nextHandler != null)
                nextHandler.process(request);
        }
    }
}

public class ConcreteHandler2 extends Handler{
    public void handleRequest(int request){
        if (request == 2){
            System.out.println("handle by ConcreteHandler2");
        }else{
            if (nextHandler != null)
                nextHandler.process(request);
        }
    }
}

public class Client{
    public static void main(String[] args){
        Handler handler = new ConcreteHandler1();
        Handler nextHandler = new ConcreteHandler2();
        handler.setNext(nextHandler);
        handler.handleRequest(1);
        handler.handleRequest(2);
    }
}
```
</details>

### Consequences

Benefits

- 减少了耦合。
- 增加了分配职责给对象的灵活性。

Drawbacks

- 接收不能保证。因为一个请求没有明确指定接收者，所有不能保证它能被处理。

## Command

### What

封装一个请求作为一个对象，因此让你参数化客户端的不同请求。

### Why

Motivation

有时需要发送一个请求给对象，但不知道任何关于请求的操作或者请求的接收者。

Applicability

- 参数化执行动作作为对象。
- 在不同的时间指定，排队和执行请求。Command 对象的生命周期可以独立于原始请求。
- 支持 undo。Command 执行操作可以存储状态用来反转影响 command 本身。
- 支持 logging changes。所以可以在系统崩溃后可以重新正确运行。
- 围绕基于 primitive 操作的 high-level 操作来构建系统。

### Solution

Structure

<img src="../img/design-patterns-structure-and-example/command-structure.png" class="img-center" />

Participants

- Command：为执行一个操作声明一个接口。
- ConcreteCommand：定义一个在 Receiver object 和 action 的绑定。通过调用接收者相应的操作来实现 Execute。 
- Cient：创建一个 ConcreteCommnd object，以及设置它的 receiver。
- Invoker：请求 command 得到请求结果。
- Receiver：知道如何执行与请求相关的操作。

Collaborations

- Client 创建一个 ConcreteCommand 以及指定它的 receiver。
- Invoker 对象存储 ConcreteCommand 对象。
- Invoker 通过调用 command 对象的 execute() 方法就，发出请求。
- ConcreteCommand 对象调用接收者的操作得到请求结果。

Implementations

<details>
  <summary>Click to expand!</summary>

```java
public interface Command{
    void execute();
}
public class Receiver{
    public void action1(){
        System.out.println("action 1 executing...");
    }
    public void action2(){
        System.out.println("action 2 executing...");
    }
}
public class ConcreteCommand1 implements Command{
    public Receiver receiver;
    public ConcreteCommand1(Receiver receiver){
        this.receiver = receiver;
    }
    public void execute(){
        receiver.action1();
    }
}
public class ConcreteCommand2 implements Command{
    public Receiver receiver;
    public ConcreteCommand1(Receiver receiver){
        this.receiver = receiver;
    }
    public void execute(){
        receiver.action2();
    }
}
public class Invoker{
    Command slot;
    public void setCommand(Command command){
        this.slot = command;
    }
    public void requestExecute(){
        this.slot.execute();
    }
}
public class Clinet{
    Invoker invoker = new Invoker();
    Receiver receiver = new Receiver();
    invoker.setCommnad(new ConcreteComand1(receiver));
    invoker.requestExecute();
    invoker.setCommnad(new ConcreteComand2(receiver));
    invoker.requestExecute();
}
```
</details>

### Consequences

Benefits

- 请求命令是很灵活的。
- 支持 undo 和 redo。Command 可以提供一个方式去反转它的执行。
- 避免在 undo 过程中错误积累。

Drawbacks


## Interpreter

### What

### Why

Motivation

Applicability

### Solution

Structure

<img src="../img/design-patterns-structure-and-example/interpreter-structure.png" class="img-center" />

Participants

Collaborations

Implementations

<details>
  <summary>Click to expand!</summary>

```java
todo
```
</details>

### Consequences

Benefits

Drawbacks


## Iterator

### What

### Why

Motivation

Applicability

### Solution

Structure

<img src="../img/design-patterns-structure-and-example/xxx-structure.png" class="img-center" />

Participants

Collaborations

Implementations

<details>
  <summary>Click to expand!</summary>

```java
todo
```
</details>

### Consequences

Benefits

Drawbacks


## Mediator

### What

### Why

Motivation

Applicability

### Solution

Structure

<img src="../img/design-patterns-structure-and-example/xxx-structure.png" class="img-center" />

Participants

Collaborations

Implementations

<details>
  <summary>Click to expand!</summary>

```java
todo
```
</details>

### Consequences

Benefits

Drawbacks

## Memento

### What

### Why

Motivation

Applicability

### Solution

Structure

<img src="../img/design-patterns-structure-and-example/xxx-structure.png" class="img-center" />

Participants

Collaborations

Implementations

<details>
  <summary>Click to expand!</summary>

```java
todo
```
</details>

### Consequences

Benefits

Drawbacks


## Observer
### What

### Why

Motivation

Applicability

### Solution

Structure

<img src="../img/design-patterns-structure-and-example/xxx-structure.png" class="img-center" />

Participants

Collaborations

Implementations

<details>
  <summary>Click to expand!</summary>

```java
todo
```
</details>

### Consequences

Benefits

Drawbacks


## State
### What

### Why

Motivation

Applicability

### Solution

Structure

<img src="../img/design-patterns-structure-and-example/xxx-structure.png" class="img-center" />

Participants

Collaborations

Implementations

<details>
  <summary>Click to expand!</summary>

```java
todo
```
</details>

### Consequences

Benefits

Drawbacks


## Strategy
### What

### Why

Motivation

Applicability

### Solution

Structure

<img src="../img/design-patterns-structure-and-example/xxx-structure.png" class="img-center" />

Participants

Collaborations

Implementations

<details>
  <summary>Click to expand!</summary>

```java
todo
```
</details>

### Consequences

Benefits

Drawbacks


## Template Method
### What

### Why

Motivation

Applicability

### Solution

Structure

<img src="../img/design-patterns-structure-and-example/xxx-structure.png" class="img-center" />

Participants

Collaborations

Implementations

<details>
  <summary>Click to expand!</summary>

```java
todo
```
</details>

### Consequences

Benefits

Drawbacks


## Visitor

### What

### Why

Motivation

Applicability

### Solution

Structure

<img src="../img/design-patterns-structure-and-example/xxx-structure.png" class="img-center" />

Participants

Collaborations

Implementations

<details>
  <summary>Click to expand!</summary>

```java
todo
```
</details>

### Consequences

Benefits

Drawbacks