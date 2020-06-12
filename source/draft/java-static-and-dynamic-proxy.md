Title: Java Static and Dynamic Proxy

**Content**

- What is The Proxy in Java
- Why Do We Need Proxy
- How to Use Proxy
  - Static Proxy
  - JDK Dynamic Proxy
  - Properties of Proxy Classes
  - cglib Dynamic Proxy
- Conclusion
- References

## What is Proxies in Java

The Proxy is a design pattern in software design. It provides a surrogate or placeholder for another object to control access to it. It lets you call a method of a proxy object the same as calling a method of a POJO. The a proxy's methods can do additional thing than POJO's method.

## Why Do We Need Proxy

- It lets you control to access an object. For example, to delay an object's construction and initialization.
- It lets you track the object access. For example, tracking methods access that can analyze user behavior or analyze system performance.

## How to User Proxy

There are commonly three ways to implement the proxy pattern in Java: static proxy, JDK dynamic proxy, and CGLIB dynamic proxy.

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

### JDK Dynamic Proxy

The JDK dynamic proxy implemented in package `java.lang.reflect` of Java API. Its underlying implementation is the Java reflection.

The manly class for implemented dynamic proxy are: the interface `java.lang.reflect.InvocationHandler`, and the class `java.lang.reflect.Proxy`.

You can call the `Proxy`'s a static method `Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces, InvocationHandler h)` to get a proxy object. 

- The `ClassLoader` instance commonly use `ClassLoader.getSystemClassLoader()`.  
- The array of Class `Class<?>[]` is the interfaces you want to implement. 
- The `InvocationHandler` instance is an invocation handler that handling proxy method calling with the method `invoke(Object proxy, Method method, Object[] args)`. We need to create an invocation handler by implementing the interface `InvocationHandler`. 

The following code example is a basic JDK Dynamic proxy implementation:



**Properties of Proxy Classes**

...

### cglib Dynamic Proxy

```xml
<dependency>
    <groupId>cglib</groupId>
    <artifactId>cglib</artifactId>
    <version>3.2.4</version>
</dependency>
```



## Conclusion

JDK dynamic proxy implemented by using Java reflection to call methods.

CGLIB dynamic proxy implemented by using the index of the array to call methods.

## References

[1] Core Java

[2] Design Patterns

[3] [Understanding “proxy” arguments of the invoke method of java.lang.reflect.InvocationHandler - Stack Overflow](https://stackoverflow.com/questions/22930195/understanding-proxy-arguments-of-the-invoke-method-of-java-lang-reflect-invoca)

[4] [cglib](https://github.com/cglib/cglib)

[5] [Introduction to cglib](https://www.baeldung.com/cglib)

[6] [Creating a Proxy Object Using cglib](https://dzone.com/articles/creating-a-proxy-object-using-cglib)