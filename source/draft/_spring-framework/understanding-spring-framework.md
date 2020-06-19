Title: Understanding The Spring Framework Core

**Content**

- What is the Spring Framework



## What is the Spring Framework

> The Spring Framework provides a comprehensive programming and configuration model for modern Java-based enterprise applications - on any kind of deployment platform.

> A key element of Spring is infrastructural support at the application level: Spring focuses on the "plumbing" of enterprise applications so that teams can focus on application-level business logic, without unnecessary ties to specific deployment environments.

> The Spring Framework is a Java platform that provides comprehensive infrastructure support for developing Java applications. Spring handles the infrastructure so you can focus on your application.

In my opinion, the Spring framework is a framework that does a lot of infrastructure things to help you focus on your application business logic development. In addition, the Spring framework is non-intrusive, meaning that your domain logic code generally has no dependencies on the framework itself.

Features

- Core technologies: dependency injection, events, resources, i18n, validation, data binding, type converion, SpEL, AOP.
- Testing: mock objects, TestContext framework, Spring MVC Test, WebTestClient.
- Data Access: transactions, DAO support, JDBC, ORM, Marshalling XML.
- Spring MVC and Spring WebFlux web frameworks.
- Integration: remoting, JMS, JCA, JMX, email, tasks, scheduling, cache.
- Languages: Kotlin, Groovy, dynamic languages.

## Modules

Core Container

- spring-core. It provides basic classes for exception handling and version detection, and other core helpers that are not specific to any part of the framework. **Core utilities, used by many other Spring modules.**
- spring-beans. It provides the fundamental parts of the framework. The IoC. `BeanFactory`.
  - [org.springframework.beans](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/package-summary.html). This package contains interfaces and classes for manipulating Java beans.
  - [org.springframework.beans.annotation](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/annotation/package-summary.html). Support package for beans-style handling of Java 5 annotations.
  - [org.springframework.beans.factory](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/package-summary.html). The core package implementing Spring's lightweight Inversion of Control (IoC) container.
  - [org.springframework.beans.factory.annotation](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/annotation/package-summary.html). Support package for annotation-driven bean configuration.
  - [org.springframework.beans.factory.parsing](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/parsing/package-summary.html). Support infrastructure for bean definition parsing.
  - [org.springframework.beans.factory.xml](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/xml/package-summary.html). Contains an abstract XML-based `BeanFactory` implementation, including a standard "spring-beans" XSD.
- spring-context. To access objects in a framework-style manner. Provides support for internationalization (using, for example, resource bundles), event propagation, resource loading,  and the transparent creation of contexts by, for example, a Servlet container. The Context module  also supports Java EE features such as EJB, JMX, and basic remoting.`ApplicationContext`.
  - [org.springframework.context](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/package-summary.html). This package builds on the beans package to add support for message sources and for the Observer design pattern, and the ability for application objects to obtain resources using a consistent API.
- spring-context-support. It provides support for integrating common third-party libraries into a Spring application context. For example, `EhCache`, `Quartz`
- spring-expression. provides a powerful Expression Language for querying and manipulating an object graph at runtime.

AOP

- spring-aop. It provides an AOP Alliance-compliant aspect-oriented programming implementation. Proxy-based AOP support.
  - [org.springframework.aop](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/aop/package-summary.html). Core Spring AOP interfaces, built on AOP Alliance AOP interoperability interfaces.
  - [org.springframework.aop.aspectj](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/aop/aspectj/package-summary.html). AspectJ integration package.
  - [org.springframework.aop.config](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/aop/config/package-summary.html). Support package for declarative AOP configuration, with XML schema being the primary configuration format.
- spring-aspects. It provides integration with AspectJ. AspectJ based aspects.
  - [org.springframework.beans.factory.aspectj](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/beans/factory/aspectj/package-summary.html)
  - [org.springframework.cache.aspectj](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/cache/aspectj/package-summary.html)
  - [org.springframework.context.annotation.aspectj](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/context/annotation/aspectj/package-summary.html)
  - [org.springframework.scheduling.aspectj](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/scheduling/aspectj/package-summary.html)
  - [org.springframework.transaction.aspectj](https://docs.spring.io/spring-framework/docs/current/javadoc-api/org/springframework/transaction/aspectj/package-summary.html)
- spring-instrument. It provides class instrumentation support and classloader implementations to be used in certain application servers. 

Messaging

- spring-messaging

Data Access/Integration

- spring-jdbc
- spring-tx
- spring-orm
- spring-oxm
- spring-jms

Web

- spring-web
- spring-webmvc
- spring-websocket

Test

- spring-test

## IoC container

### Ioc Container

**What is IoC?**

IoC is also known as dependency injection (DI). It is a process whereby objects define their dependencies, that is, the other objects they work with, only through constructor arguments, arguments to a factory method, or properties that are set on the object instance after it is constructed or returned from a factory method. The container then injects those dependencies when it creates the bean. This process is fundamentally the inverse, hence the name Inversion of Control (IoC), of the bean itself controlling the instantiation or location of its dependencies by using direct construction of classes, or a mechanism such as the Service Locator pattern.

The org.springframework.beans and org.springframework.context packages are the basis for Spring Framework’s IoC container. The BeanFactory interface provides an advanced configuration mechanism capable of managing any type of object. ApplicationContext is a subinterface of BeanFactory. It adds easier integration with other Spring features.

In short, the BeanFactory provides the configuration framework and basic functionality, and the ApplicationContext adds more enterprise-specific functionality.

**What is Bean?**

In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container. Otherwise, a bean is simply one of many objects in your application. Beans, and the dependencies among them, are reflected in the configuration metadata used by a container.

**What is IoC Container?**

IoC container is the implementation of IoC functionality in the Spring framework.

**How IoC container works?**

The interface `org.springframework.context.ApplicationContext`represents the Spring IoC container and is responsible for instantiating, configuring, and assembling the aforementioned beans. The container gets its instructions on what objects to instantiate, configure, and assemble by reading configuration metadata. The configuration metadata is represented in XML, Java annotations, or Java code. It allows you to express the objects that compose your application and the rich interdependencies between such objects.

Several implementations of the ApplicationContext interface are supplied out-of-the-box with Spring. In standalone applications it is common to create an instance of ClassPathXmlApplicationContext or FileSystemXmlApplicationContext. 

The following diagram is a high-level view of how Spring works. Your application classes are combined with configuration metadata so that after the ApplicationContext is created and initialized, you have a fully configured and executable system or application.

![image-20200618171205075](spring-ioc-container.png)

As the preceding diagram shows, the Spring IoC container consumes a form of configuration metadata; this configuration metadata represents how you as an application developer tell the Spring container to
instantiate, configure, and assemble the objects in your application.

Process of Using the IoC Continer

- Configuration metadata based on XML, annotation, or Java code.
- Instantiating a container.
- Using the container to get beans.

### Bean Overview

A Spring IoC container manages one or more beans. These beans are created with the configuration metadata that you supply to the container, for example, in the form of XML `<bean/>` definitions.

Within the container itself, these bean definitions are represented as `BeanDefinition` objects, which contain the following metadata:

- A package-qualified class name.
- Bean behavioral configuration: scope, lifecycle callbacks, and so on.
- Other configuration settings to set in the newly created object, for example, the number of connections to use in a bean that manages a connection pool.

Properties of the `BeanDefinition` class

- class. Instantiating beans.
- name. Naming beans.
- scope. Bean scopes.
- constructor arguments. Dependency injection.
- properties. Dependency injection.
- autowiring mode. Autowiring collaborators.
- lazy-initialization mode. Lazy-initialized beans.
- initialization method. Initialization callbacks.
- destruction method. Destruction callbacks.

The `ApplicationContext` implementations also permit the registration of existing objects that are created outside the container, by users. For example,  the `registrationSingleton()` method of the `DefaultListableBeanFactory` class.

However typical applications work solely with beans defined through metadata bean definitions.

## AOP

## Aspect Oriented Programming with Spring

Aspect-Oriented Programming (AOP) complements Object-Oriented Programming (OOP) by providing another way of thinking about program structure. The key unit of modularity in OOP is the class, whereas in AOP the unit of modularity is the aspect.

...

## Validation, Data Binding, and Type Conversion

...

## References

[1] [Spring Framework - spring.io](https://spring.io/projects/spring-framework)

[2] [Spring Framework API - Current Version](https://docs.spring.io/spring-framework/docs/current/javadoc-api/)