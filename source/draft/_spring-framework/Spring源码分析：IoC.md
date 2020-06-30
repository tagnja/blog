Title: Spring 源码分析：IoC

**Content**

- Basics
  - Basic Concepts
  - Class Hierarchy
- The Process of IoC Implementation
- Conclusion
- References

This post is diving into the Spring framework in deep. We are going to analyze IoC implementation in the source code of the Spring framework. 



## Basics

### Basic Concepts

**Bean and BeanDefinition**

In Spring, the objects that form the backbone of your application and that are managed by the Spring IoC container are called beans. A bean is an object that is instantiated, assembled, and otherwise managed by a Spring IoC container.

A Spring IoC container manages one or more beans. These beans are created with the configuration metadata that you supply to the container, for example, in the form of XML `<bean/>` definitions.

Within the container itself, these bean definitions are represented as `BeanDefinition` objects.

**IoC Containers**

IoC container is the implementation of IoC functionality in the Spring framework. The interface `org.springframework.context.ApplicationContext`represents the Spring IoC container and is responsible for instantiating, configuring, and assembling the aforementioned beans. The container gets its instructions on what objects to instantiate, configure, and assemble by reading configuration metadata. The configuration metadata is represented in XML, Java annotations, or Java code. It allows you to express the objects that compose your application and the rich interdependencies between such objects.

Several implementations of the ApplicationContext interface are supplied out-of-the-box with Spring. In standalone applications it is common to create an instance of ClassPathXmlApplicationContext or FileSystemXmlApplicationContext. 

**BeanFactory vs ApplicationContext**

Spring provides two kinds of IoC container, one is `XMLBeanFactory` and other is `ApplicationContext`. The `ApplicationContext` is a more powerful IoC container than `BeanFactory`.

Bean Factory

- Bean instantiation/wiring

Application Context

- Bean instantiation/wiring
- Automatic BeanPostProcessor registration
- Automatic BeanFactoryPostProcessor registration
- Convenient MessageSource access (for i18n)
- ApplicationEvent publication

**WebApplicationContext vs ApplicationContext**

`WebApplicationContext` in Spring is web aware `ApplicationContext` i.e it has Servlet Context information. In single web application there can be multiple `WebApplicationContext`. That means each `DispatcherServlet` associated with single `WebApplicationContext`. `WebApplcationContext` extends `ApplicationContext`.

### Class Hierarchy

**Bean Factories Hierarchy**

```
I-BeanFactory
|----I-ListableBeanFactory
|--------C-StaticListableBeanFactory
|----I-HierarchicalBeanFactory
|--------I-ConfigurableBeanFactory
|------------A-AbstractBeanFactory
|------------I-ConfigurableListableBeanFactory
|----I-AutowireCappableBeanFactory
|---------A-AbstractAutowireCapableBeanFactory
|--------------C-DefaultListableBeanFactory
|-------------------C-XmlBeanFactory
```

**ApplicationContext Hierarchy** 

```
I-ListableBeanFactory
I-HierarchicalBeanFactory
|----I-ApplicationContext
|--------I-WebApplicationContext
|--------I-ConfigurableApplicationContext
|------------I-ConfigurableWebApplicationContext		
|----------------A-AbstractRefreshableWebApplicationContext
|--------------------C-XmlWebApplicationContext
```

**BeanRegister & BeanFactory Hierarchy**

```
I-SingletonBeanRegistry
|----C-DefaultSingletonBeanRegistry
|--------A-FactoryBeanRegistrySupport
|------------A-AbstractBeanFactory
```

**DispatherServlet Hierarchy**

```java
A-javax.servlet.http.HttpServlet
|----A-HttpServletBean
|--------A-FrameworkServlet
|------------C-DispatcherServlet
```



## The Process of IoC Implementation

The following figure shows the process of IoC implementation.



### <img> //TODO..............................#$%^&*()*&^%$



### 1 Java web project starting

### 1.1 instantiate and init servlets in web.xml

When a Java web project running in an application server like Apache Tomcat, the servlet container of the server will load, instantiate, and init Java servlets that be `<load-on-startup>` from `web.xml` file. 

```xml
<servlet>
    <servlet-name>DispatcherServlet</servlet-name>
    <servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
    <init-param>
        <param-name>contextConfigLocation</param-name>
        <param-value>classpath:applicationContext.xml</param-value>
    </init-param>
    <load-on-startup>1</load-on-startup>
</servlet>
<servlet-mapping>
    <servlet-name>DispatcherServlet</servlet-name>
    <url-pattern>/</url-pattern>
</servlet-mapping>
```

### 2 The DispatcherServlet initialization Calling init()

### 2.1 set bean properties from init parameters

Set initial parameters specified in the `web.xml` file for the servlet instance `DispatcherServlet`.

### 3 initServletBean()

### 3.1 initWebApplicationContext()

...





3.1.1 createWebApplicationContext()

3.1.1.1 configureAndRefreshWebApplicationContext()s

3.2 initFrameworkServlet()

4 refresh()

As this is a startup method, it should destroy already created singletons if it fails, to avoid dangling resources. In other words, after invocation of that method, either all or no singletons at all should be instantiated.

4.1 obtainFreshBeanFactory()

4.2 finishBeanFactoryInitialization()

5 preInstantiateSingletons()

5.1 to create all bean instances

6 getBean(name)

6.1 doGetBean()

7 getSingleton(beanName, singletonFactory)

8 createBean(beanName, beanDefinition, args)



## Conclusion

...

## References

[1] [Spring Framework - Wikipedia](https://en.wikipedia.org/wiki/Spring_Framework)

[2] [Spring Framework Home - Spring](https://spring.io/projects/spring-framework)

[3] [How does spring Dispatcher Servlet create default beans without any XML configuration?](https://stackoverflow.com/questions/11708383/how-does-spring-dispatcher-servlet-create-default-beans-without-any-xml-configur)

[4] [Difference between ApplicationContext and WebApplicationContext in Spring MVC](https://www.dineshonjava.com/difference-between-applicationcontext-webapplicationcontext-in-spring-mvc/#:~:text=WebApplicationContext%20in%20Spring%20is%20web,DispatcherServlet%20associated%20with%20single%20WebApplicationContext.)

[1] [Spring life-cycle when we refresh the application context](https://stackoverflow.com/questions/20489272/spring-life-cycle-when-we-refresh-the-application-context)

[2] [BeanFactory vs ApplicationContext](https://stackoverflow.com/questions/243385/beanfactory-vs-applicationcontext)