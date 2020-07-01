Title: Spring 源码分析：IoC

filename: spring-dive-into-source-code

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

`WebApplicationContext` interface provides configuration for a web application. This is read-only while the application is running, but may be reloaded if the implementation supports this.

`WebApplcationContext` extends `ApplicationContext`. This interface adds a `getServletContext()` method to the generic ApplicationContext interface, and defines a well-known application attribute name that the root context must be bound to in the bootstrap process.

`WebApplicationContext` in Spring is web aware `ApplicationContext` i.e it has Servlet Context information. In single web application there can be multiple `WebApplicationContext`. That means each `DispatcherServlet` associated with single `WebApplicationContext`. 

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
|------------A-AbstractApplicationContext
|------------I-ConfigurableWebApplicationContext		
|----------------A-AbstractRefreshableWebApplicationContext
|--------------------C-XmlWebApplicationContext
```

**AbstractApplicationContext Hierarchy**

```
A-AbstractApplicationContext
|----A-AbstractRefreshableApplicationContext
|--------A-AbstractRefreshableConfigApplicationContext
|------------A-AbstractRefreshableWebApplicationContext
|----------------C-AnnotationConfigWebApplicationContext
|----------------C-XmlWebApplicationContext
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

### 1.1 instantiate and init servlets from web.xml

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

### 2 The DispatcherServlet initialization and Calling init() in HttpServletBean

### 2.1 set bean properties from init parameters

Set initial parameters specified in the `web.xml` file for the servlet instance `DispatcherServlet`.

### 3 initServletBean() in FrameworkServlet

### 3.1 initWebApplicationContext()

Initialize and publish the WebApplicationContext for this servlet.

- Check is there any WebApplicationContext instance or root application context, if not create a new webApplicationContext instance (See #3.1.1).

### 3.1.1 createWebApplicationContext() 

Using reflection to create a WebApplicationContext instance, and configure it (see #3.1.1.1).

`FrameworkServlet.java`

```java
protected WebApplicationContext createWebApplicationContext(@Nullable ApplicationContext parent) {
	//...
	ConfigurableWebApplicationContext wac =
    	(ConfigurableWebApplicationContext) BeanUtils.instantiateClass(contextClass);
    //...go to #3.1.1.1
    configureAndRefreshWebApplicationContext(wac);
}
```

### 3.1.1.1 configureAndRefreshWebApplicationContext()

Set WebApplication instance properties and go to refresh (See #4).

`FrameworkServlet.java`

```java
protected void configureAndRefreshWebApplicationContext(ConfigurableWebApplicationContext wac) {
    //...
    wac.setServletContext(getServletContext());
    wac.setServletConfig(getServletConfig());
    wac.setNamespace(getNamespace());
    postProcessWebApplicationContext(wac);
    applyInitializers(wac);
    //...go to #4
    wac.refresh();
}
```

### 3.2 initFrameworkServlet()

This step after the step `#4.3`.

### 4 refresh() in AbstractApplicationContext

As this is a startup method, it should destroy already created singletons if it fails, to avoid dangling resources. In other words, after invocation of that method, either all or no singletons at all should be instantiated.

`AbstractApplicationContext.java`

```java
public void refresh() throws BeansException, IllegalStateException {
    //...
    // 1. Create a BeanFactory instance. Refer to #4.1
    ConfigurableListableBeanFactory beanFactory = this.obtainFreshBeanFactory();
    // 2. Configure properties of AbstractApplicationContext, prepare for bean initialization and context refresh
    this.postProcessBeanFactory(beanFactory);
    this.invokeBeanFactoryPostProcessors(beanFactory);
    this.registerBeanPostProcessors(beanFactory);
    this.initMessageSource();
    this.initApplicationEventMulticaster();
    this.onRefresh();
    this.registerListeners();
    // 3. bean initialization. Refer to #4.2
    this.finishBeanFactoryInitialization(beanFactory);
    // 4. context refresh.
    this.finishRefresh();
    // ...
}
```

### 4.1 obtainFreshBeanFactory()

Create a BeanFactory instance in `AbstractRefreshableApplicationContext`.

`AbstractRefreshableApplicationContext.java`

```java
protected final void refreshBeanFactory() throws BeansException {
    //...
    // 1. Creating a BeanFactory instance.
    DefaultListableBeanFactory beanFactory = this.createBeanFactory();
    // 2. Set BeanFactory instance's properties
    beanFactory.setSerializationId(this.getId());
    this.customizeBeanFactory(beanFactory);
    // 3. load bean definitions
    this.loadBeanDefinitions(beanFactory);
    synchronized(this.beanFactoryMonitor) {
        // 4. assign this new BeanFactory instance to the AbstractRefreshableApplicationContext's filed beanFactory
        this.beanFactory = beanFactory;
    }
    //...
}

protected DefaultListableBeanFactory createBeanFactory() {
    return new DefaultListableBeanFactory(this.getInternalParentBeanFactory());
}
```

### 4.2 finishBeanFactoryInitialization()

Initializate beanFactory, and go to instantiate bean instances (see #5).

`AbstractApplicationContext.java`

```java
protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {
	// 1. initialize bean factory
    //...
    // 2. instantiate bean instances, go to #5
    beanFactory.preInstantiateSingletons();
}
```

### 5 preInstantiateSingletons() in DefaultListableBeanFactory

Using Iterator of beanNames to get bean instances.

### 5.1 to create all bean instances

`AbstractApplicationContext.java`

```java
public void preInstantiateSingletons() throws BeansException {
    // 1. get beanNames' iterator
    List<String> beanNames = new ArrayList(this.beanDefinitionNames);
    Iterator var2 = beanNames.iterator();
	
    // 2. for loop to create bean instances, go to #6
    while (true){
        //...
        beanName = (String)var2.next();
        if (this.isFactoryBean(beanName)) {
            bean = this.getBean("&" + beanName);
            break;
        }
        this.getBean(beanName);
        //...
    }
}
```

### 6 getBean(name) in AbstractBeanFactory

To get bean instances.

`AbstractBeanFactory.java`

```java
public Object getBean(String name) throws BeansException {
    return this.doGetBean(name, (Class)null, (Object[])null, false);
}

```

### 6.1 doGetBean()

To get bean instance by bean name.

`AbstractBeanFactory.java`

```java
protected <T> T doGetBean(String name, @Nullable Class<T> requiredType, @Nullable Object[] args, boolean typeCheckOnly) throws BeansException {
    String beanName = this.transformedBeanName(name);
    Object sharedInstance = this.getSingleton(beanName);
    Object bean;

    // 1. check is the bean instance in creation
    if (sharedInstance != null && args == null) {
        //...
    } else {
        // 2. get and check beanDefinition by beanName
        RootBeanDefinition mbd = this.getMergedLocalBeanDefinition(beanName);
        this.checkMergedBeanDefinition(mbd, beanName, args);
        // 3. to get different type bean instance: singleton, prototype, and so on.
        if (mbd.isSingleton()) {
            // to create singleton bean instance go to #7
            sharedInstance = this.getSingleton(beanName, () -> {
                try {
                    return this.createBean(beanName, mbd, args);
                } catch (BeansException var5) {
                    this.destroySingleton(beanName);
                    throw var5;
                }
            });
            bean = this.getObjectForBeanInstance(sharedInstance, name, beanName, mbd);
        } else if (mbd.isPrototype()) {
            //...
        } else{
            //...
        }
    }
}
```



### 7 getSingleton(beanName, singletonFactory) in DefaultSingletonBeanRegistry

To get singleton bean instance.

`DefaultSingletonBeanRegistry.java`

```java
public Object getSingleton(String beanName, ObjectFactory<?> singletonFactory) {
    // 1. check conditions and logging
    //...
    
    // 2. to get a singleton bean instance, go to #8
    singletonObject = singletonFactory.getObject();
    newSingleton = true;

    // 3. add created singleton bean instance to the bean list
    if (newSingleton) {
        this.addSingleton(beanName, singletonObject);
    }
}

protected void addSingleton(String beanName, Object singletonObject) {
    synchronized(this.singletonObjects) {
        // add bean instance to the bean list "singletonObjects"
        this.singletonObjects.put(beanName, singletonObject);
        this.singletonFactories.remove(beanName);
        this.earlySingletonObjects.remove(beanName);
        this.registeredSingletons.add(beanName);
    }
}
```

### 8 createBean(beanName, beanDefinition, args) in AbstractAutowireCapableBeanFactory

To create a bean instance actually.

`AbstractAutowireCapableBeanFactory.java`

```java
protected Object createBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args) throws BeanCreationException {
    // ...
    // 1. construct a beanDefinition for use.
    // 2. try to create bean instance
    Object beanInstance = this.resolveBeforeInstantiation(beanName, mbdToUse);
    if (beanInstance != null) {
        return beanInstance;
    }
    // 3. try to create bean instance again if fail to create in first time
    beanInstance = this.doCreateBean(beanName, mbdToUse, args);
    return beanInstance;
}
```

//TODO

```java
protected Object doCreateBean(String beanName, RootBeanDefinition mbd, @Nullable Object[] args) throws BeanCreationException {
    
}
```



## Conclusion

...

## References

[1] [Spring Framework - Wikipedia](https://en.wikipedia.org/wiki/Spring_Framework)

[2] [Spring Framework Home - Spring](https://spring.io/projects/spring-framework)

[3] [How does spring Dispatcher Servlet create default beans without any XML configuration?](https://stackoverflow.com/questions/11708383/how-does-spring-dispatcher-servlet-create-default-beans-without-any-xml-configur)

[4] [Difference between ApplicationContext and WebApplicationContext in Spring MVC](https://www.dineshonjava.com/difference-between-applicationcontext-webapplicationcontext-in-spring-mvc/#:~:text=WebApplicationContext%20in%20Spring%20is%20web,DispatcherServlet%20associated%20with%20single%20WebApplicationContext.)

[1] [Spring life-cycle when we refresh the application context](https://stackoverflow.com/questions/20489272/spring-life-cycle-when-we-refresh-the-application-context)

[2] [BeanFactory vs ApplicationContext](https://stackoverflow.com/questions/243385/beanfactory-vs-applicationcontext)