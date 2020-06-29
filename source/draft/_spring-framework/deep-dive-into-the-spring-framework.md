Title: Deep Dive Into The Spring Framework

**Content**

- Versions and History

## Versions and History

Until June 2020, the latest version of spring framework is 5.2.7GA.

### History Version

| Version | Date | Notes |
| :-----: | :--: | :---: |
|   0.9   | 2002 |       |
|   1.0   | 2003 |       |
|   2.0   | 2006 |       |
|   3.0   | 2009 |       |
|   4.0   | 2013 |       |
|   5.0   | 2017 |       |

### Keyword of Commit log

- Spring 2.0
- Spring 3.0
- Spring 4.0
- Spring 5.0

**I decide to select the Spring 3.0 to read.**

## Questions

- IoC
  - 为什么 Java Web 应用（Servlet or Spring Web）在 web.xml 中配置了一个 Servlet 就可以自动初始化 bean？
  - 自动初始化 bean 在源码中是怎么实现的。
  - Spring IoC 是如何初始化 bean 的？
  - 在一个非 web 项目（Java 项目）中，如何实现自动化初始化 bean？
  - 三种不同方法配置 bean 的 IoC 的实现有什么不同？

## Explore Spring IoC

### Bean Factory

```
I-BeanFactory
|----I-ListableBeanFactory
|----I-HierarchicalBeanFactory
|--------I-ConfigurableBeanFactory
|------------A-AbstractBeanFactory
|------------I-ConfigurableListableBeanFactory
|----I-AutowireCappableBeanFactory
|---------A-AbstractAutowireCapableBeanFactory
|--------------C-DefaultListableBeanFactory
|-------------------XmlBeanFactory
|----StaticListableBeanFactory
```

```
I-ApplicationContext
	I-WebApplicationContext
	I-ConfigurableApplicationContext
		I-ConfigurableWebApplicationContext		
            A-AbstractRefreshableWebApplicationContext
                C-XmlWebApplicationContext
```



## Q1: How Spring initializes beans when Java web project starting?

web.xml

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

### `DispatherServlet` Hierarchy

```
A-javax.servlet.http.HttpServlet
    A-HttpServletBean
        A-FrameworkServlet
            C-DispatcherServlet
```

`HttpServletBean`

1.HttpServletBean.java

```java
public abstract class HttpServletBean extends HttpServlet
{
    @Override 
    public final void init(){
    	// 1. set bean properties from init parameters
    	//...
    	
        // 2. subclasses do initialization
    	initServletBean();
	}
    
    /** 
     * Subclasses may override this to perform custom initiailization.
     * This default implementation is empty
     */
    protected void initServletBean(){
        // empty method body. See subclass implementation.
    }
}
```

2.FrameworkServlet.java

```java
public abstract class FrameworkServlet extends HttpServletBean {
    
    /**
     * Overridden method of HttpServletBean, invoked after any bean properties have bean set. Creates this servlet's WebApplicationContext
     */
    @Override
    protected final void initServletBean(){
        // logging
        //... 
        
        // ***************************************
        this.webApplicationContext = initWebApplicationContext();
        initFrameworkServlet();
        
        // logging
        //...
    }
    
    /**
     * Initialize and publish the WebApplicationContext for this servlet
     * Delegates to createWebApplicationContext for actual creation of the context. Can be overridden in subclasses.
     */
    protected WebApplicationContext initWebApplicationContext() {
        // 1. construct WebApplicationContext from values of servletContext
        //...
        
        if (wac == null) {
			// No context instance is defined for this servlet -> create a local one
            // ***************************************
			wac = createWebApplicationContext(rootContext);
		}
        // 2. initial onRefresh
        if (!this.refreshEventReceived) {
			// Either the context is not a ConfigurableApplicationContext with refresh support 
             // or the context injected at construction time had already been
			// refreshed -> trigger initial onRefresh manually here.
			synchronized (this.onRefreshMonitor) {
				onRefresh(wac);
			}
		}
    }
    
    protected WebApplicationContext createWebApplicationContext(@Nullable WebApplicationContext parent) {
        // ***************************************
		return createWebApplicationContext((ApplicationContext) parent);
	}

    protected WebApplicationContext createWebApplicationContext(@Nullable ApplicationContext parent) {
        // ---------------------------------------
        // org.springframework.web.context.support.XmlWebApplicationContext
        // ---------------------------------------
		Class<?> contextClass = getContextClass();
		if (!ConfigurableWebApplicationContext.class.isAssignableFrom(contextClass)) {
			throw new ApplicationContextException(
					"Fatal initialization error in servlet with name '" + getServletName() +
					"': custom WebApplicationContext class [" + contextClass.getName() +
					"] is not of type ConfigurableWebApplicationContext");
		}
        // ---------------------------------------
		ConfigurableWebApplicationContext wac =
				(ConfigurableWebApplicationContext) BeanUtils.instantiateClass(contextClass);

		wac.setEnvironment(getEnvironment());
		wac.setParent(parent);
		String configLocation = getContextConfigLocation();
		if (configLocation != null) {
			wac.setConfigLocation(configLocation);
		}
        // ***************************************
		configureAndRefreshWebApplicationContext(wac);

		return wac;
	}

    protected void configureAndRefreshWebApplicationContext(ConfigurableWebApplicationContext wac) {
        //....
        
        
		// ***************************************
        wac.refresh();
	}
    
    protected void onRefresh(ApplicationContext context) {
        // empty method body
    }
    
    /**
     *  This method will be invoked after any bean properties have been set and the WebApplicationContext has been loadded. The default implementation is empty; subclasses may override this method to perform any initialization they require.
     */
    protected void initFrameworkServlet() {
        // empty method body
    }
}
```

`WebApplicationContext` in Spring is web aware `ApplicationContext` i.e it has Servlet Context information. In single web application there can be multiple `WebApplicationContext`. That means each `DispatcherServlet` associated with single `WebApplicationContext`.

`WebApplcationContext` extends `ApplicationContext`.

AbstractApplicationContext.java

```java
public abstract class AbstractApplicationContext extends DefaultResourceLoader implements ConfigurableApplicationContext {

	public void refresh() throws BeansException, IllegalStateException {
        synchronized(this.startupShutdownMonitor) {
            this.prepareRefresh();
            // ---------------------------------------
            // DefaultListableBeanFactory@xxxxxxxx
            // ---------------------------------------
            ConfigurableListableBeanFactory beanFactory = this.obtainFreshBeanFactory();
            this.prepareBeanFactory(beanFactory);

            try {
                this.postProcessBeanFactory(beanFactory);
                this.invokeBeanFactoryPostProcessors(beanFactory);
                this.registerBeanPostProcessors(beanFactory);
                this.initMessageSource();
                this.initApplicationEventMulticaster();
                this.onRefresh();
                this.registerListeners();
                // ***************************************
                this.finishBeanFactoryInitialization(beanFactory);
                this.finishRefresh();
            } catch (BeansException var9) {
                if (this.logger.isWarnEnabled()) {
                    this.logger.warn("Exception encountered during context initialization - cancelling refresh attempt: " + var9);
                }

                this.destroyBeans();
                this.cancelRefresh(var9);
                throw var9;
            } finally {
                this.resetCommonCaches();
            }

        }
    }

    protected ConfigurableListableBeanFactory obtainFreshBeanFactory() {
        this.refreshBeanFactory();
        return this.getBeanFactory();
    }
    
    protected void finishBeanFactoryInitialization(ConfigurableListableBeanFactory beanFactory) {
        //...
        
        // ***************************************
        beanFactory.preInstantiateSingletons();
    }
}
```

DefaultListableBeanFactory.java

```java
public class DefaultListableBeanFactory extends AbstractAutowireCapableBeanFactory implements ConfigurableListableBeanFactory, BeanDefinitionRegistry, Serializable {
    
	public void preInstantiateSingletons() throws BeansException {
		//...
        List<String> beanNames = new ArrayList(this.beanDefinitionNames);
        Iterator var2 = beanNames.iterator();
		//...
        while(...){
            //...
            
            // ***************************************
            if (this.isFactoryBean(beanName)) {
                bean = this.getBean("&" + beanName);
                break;
            }
            this.getBean(beanName);
		}
	}
}
```

AbstractBeanFactory.java

```java
public abstract class AbstractBeanFactory extends FactoryBeanRegistrySupport implements ConfigurableBeanFactory {

    public Object getBean(String name) throws BeansException {
        return this.doGetBean(name, (Class)null, (Object[])null, false);
    }
    
    protected <T> T doGetBean(String name, @Nullable Class<T> requiredType, @Nullable Object[] args, boolean typeCheckOnly) throws BeansException {
    	//...
        return bean;
    }
}
```



3.DispatcherServlet.java

```java
public class DispatcherServlet {
    
    @Override
    protected void onRefresh(ApplicationContext context){
        initStrategies(context);
    }
    
    /**
	 * Initialize the strategy objects that this servlet uses.
	 * May be overridden in subclasses in order to initialize further strategy objects.
	 */
	protected void initStrategies(ApplicationContext context) {
		initMultipartResolver(context);
		initLocaleResolver(context);
		initThemeResolver(context);
		initHandlerMappings(context);
		initHandlerAdapters(context);
		initHandlerExceptionResolvers(context);
		initRequestToViewNameTranslator(context);
		initViewResolvers(context);
		initFlashMapManager(context);
	}

    private void initHandlerMappings(ApplicationContext context) {
		
	}

}
```

You'll note that `DispatcherServlet` tries to find existing beans with some fixed name and either uses default or nothing if non found.

1. Handler mapping object. No resolver is used if no other resolver is configured.
2. Multipart Resolver. 
3. Theme Resolver. `FixedThemeResolver` is used if no other resolver is configured.

Conceptions

- Resolver
  - MultipartResolver
  - LocaleResolver
  - ThemeResolver
- HandlerMapping
- HandlerAdapter
- HandlerExceptionResolver
- RequestToViewNameTranslator
- ViewResolver
- FlashMapManager







## Q2: xxx



## References

[1] [Spring Framework - Wikipedia](https://en.wikipedia.org/wiki/Spring_Framework)

[2] [Spring Framework Home - Spring](https://spring.io/projects/spring-framework)

[3] [How does spring Dispatcher Servlet create default beans without any XML configuration?](https://stackoverflow.com/questions/11708383/how-does-spring-dispatcher-servlet-create-default-beans-without-any-xml-configur)

[4] [Difference between ApplicationContext and WebApplicationContext in Spring MVC]([https://www.dineshonjava.com/difference-between-applicationcontext-webapplicationcontext-in-spring-mvc/#:~:text=WebApplicationContext%20in%20Spring%20is%20web,DispatcherServlet%20associated%20with%20single%20WebApplicationContext.](https://www.dineshonjava.com/difference-between-applicationcontext-webapplicationcontext-in-spring-mvc/#:~:text=WebApplicationContext in Spring is web,DispatcherServlet associated with single WebApplicationContext.))