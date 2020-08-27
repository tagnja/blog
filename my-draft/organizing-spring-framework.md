# Organizing Spring Framework

Features

- The Spring Framework is a lightweight solution and a potential one-stop-shop for building your enterprise-ready applications. 
- Spring is modular, allowing you to use only those parts that you need, without having to bring in the rest. You can use the IoC container, with any web framework on top.
- The Spring Framework supports declarative transaction management, remote access to your logic through RMI or web services, and various options for persisting your data.
- It offers a full-featured MVC framework, and enables you to integrate AOP transparently into your software.
- Spring is designed to be non-intrusive, meaning that your domain logic code generally has no dependencies on the framework itself.
- The Spring Framework is a Java platform that provides comprehensive infrastructure support for developing Java applications. Spring handles the infrastructure so you can focus on your application.

Underlying Concepts

- Dependency Injection

Benefits from the Spring platform:

- Make a Java method execute in a database transaction without having to deal with transaction APIs.
- Make a local Java method a remote procedure without having to deal with remote APIs.
- Make a local Java method a management operation without having to deal with JMX APIs.
- Make a local Java method a message handler without having to deal with JMS APIs.

## Spring Framework Features

Q

> The Spring Framework is a lightweight solution and a potential one-stop-shop for building your enterprise-ready applications. 

为什么 Spring Framework 是构建企业应用的轻量级解决方案和提供一站式服务？

什么是轻量级，什么是一站式服务。

Q

> Spring is modular, allowing you to use only those parts that you need, without having to bring in the rest. You can use the IoC container, with any web framework on top.

为什么 Spring Framework 是模块化的？它是怎么实现模块化的？

Q

> Spring is designed to be non-intrusive, meaning that your domain logic code generally has no dependencies on the framework itself.

为什么 Spring Framework 是非侵入式的（non-intrusive）？它是怎么实现的？

Q

> The Spring Framework is a Java platform that provides comprehensive infrastructure support for developing Java applications. Spring handles the infrastructure so you can focus on your application.

为什么 Spring Framework 是一个平台为 Java 应用程序提供全面的基础架构支持，并让你专注与应用程序的业务实现？

## Spring Framework Internals

Q

什么是依赖注入（ Dependency Injection）？为什么依赖注入是 Spring framework 核心概念？它是怎么实现的？

References: 

Spring DOC v5.0 - &2.1

- To organize the basic building blocks into a coherent whole.
- The Spring Framework Inversion of Control (IoC) component addresses this concern by providing a formalized means of composing disparate components into a fully working application ready for use.

[Inversion of Control Containers and the Dependency Injection pattern](https://martinfowler.com/articles/injection.html)

Q

>The Spring Framework consists of features organized into about 20 modules. These modules are grouped into Core Container, Data Access/Integration, Web, AOP (Aspect Oriented Programming), Instrumentation, Messaging, and Test

Spring Framework 有哪些模块？每个模块的作用和功能是什么？每个模块的功能点以及它在源码中的实现？

Spring DOC v5.0 &2.2 Modules

Q

什么是 Spring Framework 声明式事务管理（declarative transaction management）？它是怎么实现的？

Q

> It offers a full-featured MVC framework, and enables you to integrate AOP transparently into your software.

什么是 Spring MVC？MVC 有哪些功能？它们是怎么实现的？



## Core Technologies

