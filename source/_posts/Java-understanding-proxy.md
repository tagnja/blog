---
title: Understanding Java：Proxy
date: 2020-06-15 16:49:20
categories: 
- Java
- Java 基础
tags: Java
---



## What is Proxy

The Proxy is a design pattern in software design. It provides a surrogate or placeholder for another object to control access to it. It lets you call a method of a proxy object the same as calling a method of a POJO. But the  proxy's methods can do additional things than POJO's method.

## Why Do We Need Proxy

- It lets you control to access an object. For example, to delay an object's construction and initialization.
- It lets you track the object access. For example, tracking methods access that can analyze user behavior or analyze system performance.

## How to Use Proxy in Java

There are commonly three ways to implement the proxy pattern in Java: static proxy, JDK dynamic proxy, and CGLib dynamic proxy.

### Static Proxy

The static proxy implemented by reference the common class in proxy class. The following example is a basic static proxy implementation:

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
    private RealSubject realSubject = new RealSubject();
    public void request(){
        System.out.println("before...");
        realSubject.request();
        System.out.println("after...");
    }
}

public class Client{
    public static void main(String[] args){
        Subject subject = new ConcreteProxy();
        subject.request();
    }
}
```

### JDK Dynamic Proxy

The JDK dynamic proxy implemented in package `java.lang.reflect` of Java API. Its underlying implementation is the Java reflection.

The manly class for implemented dynamic proxy are: the interface `java.lang.reflect.InvocationHandler`, and the class `java.lang.reflect.Proxy`.

You can call the `Proxy`'s a static method `Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)` to get a proxy object. 

- The `ClassLoader` instance commonly use `ClassLoader.getSystemClassLoader()`.  
- The array of Class `Class<?>[]` is the interfaces you want to implement. 
- The `InvocationHandler` instance is an invocation handler that handling proxy method calling with the method `invoke(Object proxy, Method method, Object[] args)`. We need to create an invocation handler by implementing the interface `InvocationHandler`. 

The following code example is a basic JDK Dynamic proxy implementation:

```java
public interface Subject{
    void request();
}

public class RealSubject implements Subject{
    public void request(){
        System.out.println("request to RealSubject...");
    }
}

public class MyInvocationHandler implements InvocationHandler {
    private RealSubject realSubject = new RealSubject();

    @Override
    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
        System.out.println("before...");
        Object result = method.invoke(realSubject, args);
        System.out.println("after...");
        return result;
    }
}

public class Client {
    public static void main(String[] args) {
        Subject subject = (Subject) Proxy.newProxyInstance(
                ClassLoader.getSystemClassLoader(),
                new Class[]{Subject.class}, new MyInvocationHandler());
        subject.request();
    }
}
```

**Properties of Proxy Classes**

- All proxy classes extend the class `java.lang.reflect.Proxy`. 
- A proxy class has only one instance field--the invocation handler, which is defined in the `Proxy` superclass.
- Any additional data required to carry out the proxy objects' tasks must be stored in the invocation handler. The invocation handler wrapped the actual objects.
- The name of proxy classes are not defined. The `Proxy` class in Java virtual machine generates class names that begin with string $Proxy.
- There is only one proxy class for a particular class loader and ordered set of interfaces. If you call the `newProxyInstance` method twice with the same class loader and interface array, you get two objects of the same class. You can get class by `Proxy.getProxyClass()`. You can test whether a particular `Class` object represents a proxy class by calling the `isProxyClass` method of the `Proxy` class.



### CGLib Dynamic Proxy

CGLib (**C**ode **G**eneration **Lib**rary) is an open source library that capable creating and loading class files in memory during Java runtime. To do that it uses Java bytecode generation library ‘asm’, which is a very low-level bytecode creation tool. CGLib can proxy to objects without Interface.

```xml
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.3.0</version>
</dependency>
```

**How to Use CGLib**

To create a proxy object using CGLib is almost as simple as using the JDK reflection proxy API. The following code example is a basic CGLib Dynamic proxy implementation:

```java
public interface Subject{
    void request();
}

public class RealSubject implements Subject{
    public void request(){
        System.out.println("request to RealSubject...");
    }
}

public class MyMethodInterceptor implements MethodInterceptor {
    private final Subject realSubject;

    public MyMethodInterceptor(Subject subject){
        this.realSubject = subject;
    }

    @Override
    public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
        System.out.println("before...");
        Object result = method.invoke(realSubject, args);
        System.out.println("after...");
        return result;
    }
}

public class Client {
    public static void main(String[] args) {
        Subject subject = new RealSubject();
        MethodInterceptor methodInterceptor = new MyMethodInterceptor(subject);
        Subject proxy = (Subject) Enhancer.create(Subject.class, methodInterceptor);
        proxy.request();
    }
}
```

The difference between JDK dynamic proxy and CGLib is that name of the classes are a bit different and CGLib do not have an interface.

It is also important that the proxy class extends the original class and thus when the proxy object is created it invokes the constructor of the original class.



## Conclusion

Differences between JDK proxy and CGLib:

- JDK Dynamic proxy can only proxy by interface (so your target class needs to implement an interface, which is then also implemented by the proxy class). CGLib (and javassist) can create a proxy by subclassing. In this scenario the proxy becomes a subclass of the target class. No need for interfaces.
- JDK Dynamic proxy generates dynamically at runtime using JDK Reflection API. CGLib is built on top of [ASM](https://en.wikipedia.org/wiki/ObjectWeb_ASM), this is mainly used the generate proxy extending bean and adds bean behavior in the proxy methods.



## References

[1] Core Java Volume I Fundamentals by S, Horstmann

[2] Design Patterns: Elements of Reusable Object-Oriented Software by Erich Gamma, Richard Helm,  Ralph Johnson, John Vlissides

[3] [Understanding “proxy” arguments of the invoke method of java.lang.reflect.InvocationHandler - Stack Overflow](https://stackoverflow.com/questions/22930195/understanding-proxy-arguments-of-the-invoke-method-of-java-lang-reflect-invoca)

[4] [cglib - GitHub](https://github.com/cglib/cglib)

[5] [Introduction to cglib](https://www.baeldung.com/cglib)

[6] [Creating a Proxy Object Using cglib](https://dzone.com/articles/creating-a-proxy-object-using-cglib)

[7] [What is the difference between JDK dynamic proxy and CGLib? - Stack Overflow](https://stackoverflow.com/questions/10664182/what-is-the-difference-between-jdk-dynamic-proxy-and-cglib)