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
  - 为什么 Java Web 应用（Servlet or Spring Web）在 web.xml 中配置了一个 Servlet 就可以自动初始化 bean？(How Spring initializes beans when Java web project starting?)
  - 自动初始化 bean 在源码中是怎么实现的。
  - Spring IoC 是如何初始化 bean 的？
  - 在一个非 web 项目（Java 项目）中，如何实现自动化初始化 bean？
  - 三种不同方法配置 bean 的 IoC 的实现有什么不同？



## References

[1] [Spring Framework - Wikipedia](https://en.wikipedia.org/wiki/Spring_Framework)

[2] [Spring Framework Home - Spring](https://spring.io/projects/spring-framework)

[3] [How does spring Dispatcher Servlet create default beans without any XML configuration?](https://stackoverflow.com/questions/11708383/how-does-spring-dispatcher-servlet-create-default-beans-without-any-xml-configur)

[4] [Difference between ApplicationContext and WebApplicationContext in Spring MVC]([https://www.dineshonjava.com/difference-between-applicationcontext-webapplicationcontext-in-spring-mvc/#:~:text=WebApplicationContext%20in%20Spring%20is%20web,DispatcherServlet%20associated%20with%20single%20WebApplicationContext.](https://www.dineshonjava.com/difference-between-applicationcontext-webapplicationcontext-in-spring-mvc/#:~:text=WebApplicationContext in Spring is web,DispatcherServlet associated with single WebApplicationContext.))