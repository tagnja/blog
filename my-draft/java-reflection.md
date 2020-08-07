Title: Understanding Reflection

### Content

- Classes
  - Retrieving Class Objects
  - Examining Class Modifiers and Types
  - Discovering Class Members
  - Common Errors when Using Class
- Members
- Arrays and Enumerated Types

### content2

- What is reflection?
- Why do we need reflection
- Its pros and cons.
- How to use it
  - Retrieving Class Objects

## What is Reflection

Reflection is an API which is used examine or modify the behavior of methods, classes, interfaces at runtime. It is also possible to instantiate new objects, invoke methods and get/set field values using reflection.

## Classes

Every type is either a reference or a primitive. Classes, enums, and arrays (which all inherit from [`java.lang.Object`](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html)) as well as interfaces are all reference types.

For every type of object, the Java virtual machine instantiates an immutable instance of [`java.lang.Class`](https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html) which provides methods to examine the runtime properties of the object including its members and type information. [`Class`](https://docs.oracle.com/javase/8/docs/api/java/lang/Class.html) also provides the ability to create new classes and objects.

The Class API

```
# Operation
- <U> Class<? extends U>	asSubclass(Class<U> clazz)
- T	cast(Object obj)

# get object of Class

```





**Retrieving Class Objects**

## References

[1] [The Reflection API - Java Documentation](https://docs.oracle.com/javase/tutorial/reflect/index.html)