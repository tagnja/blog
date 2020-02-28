---
title: 深入理解 Servlet
date: 2020-02-26 10:12:34
categories: 
- Web 开发
- Java Web
tags: Servlet
---

-

Content

- Introduction to Servlet
- The Servlet Interface
- The Request
- Servlet Context
- The Response
- Filtering
- Sessions
- Dispatching Requests
- Web Applications
- Application Lifecycle Events
- Mapping Requests to Servlets
- Security

## Introduction to Servlet

### What is a Servlet

Servlet 是基于 Java 的 Web component，它被 servlet container 管理的，它用来生成动态的内容。Servlet 是平台独立的 java class，可以运行在支持 servlet container 的 Web Server 中。Servlet 通过 Servlet 容器实现的 request/response 范例与 Web 客户端进行交互。

### What is a Servlet Container

Servlet container （有时也叫做 servlet engine）是 Web server 或 application server 的一部分，它提供了发送 request 和 response 的网络服务，解析基于 MIME 的 requests，以及格式化基于 MIME 的 responses。Servlet Container 还管理 Servlets 的整个 lifecycle。

Servlet container 可以内置到 Web server 中，也可以通过 Web server 的扩展 API 作为附加组件安装到 Web server 中。Servlet container 也可以内置或安装在 application servers。

所有 servlet containers 必须支持 HTTP 作为请求和响应的协议。其它基于 request/response 的协议也可能支持如 HTTPS。

### Why do we need Servlet

基于 HTTP 协议的 Java Web 应用中，我们需要使用 servlet 技术来生成动态的响应内容。

### Why do we need Servlet Container

Servlet 只是一个 Java 类，它接收 request 对象和响应 response 对象。然而，一个应用中有很多 servlets，我们需要一个容器来管理这些 servlets 的创建和销毁，以及去解析和生成网络协议的报文。

### How they work

- Client 访问 Web server，发出一个 HTTP 请求。
- Web server 接收到请求，将请求交给 servlet container。servlet container 可以在与主机Web服务器相同的进程中运行，可以在同一主机上的不同进程中运行，也可以在与其处理请求的Web服务器不同的主机上运行。
- Servlet container 根据配置决定调用哪个 servlet，并将代表 request 和 response 的对象传递给 servlet。
- Servlet 利用 request 对象处理逻辑，生成响应的数据。
- 一旦 servlet 处理完了请求，servlet container 确保 response 正确地 flushed，并且将控制权返回给 Web server。

### Servlet History

Servlet versions history

| Servlet API version | Released                                                     | Specification                                                | Platform             | Important Changes                                            |
| ------------------- | ------------------------------------------------------------ | ------------------------------------------------------------ | -------------------- | ------------------------------------------------------------ |
| Servlet 4.0         | Sep 2017                                                     | [JSR 369](https://jcp.org/en/jsr/detail?id=369)              | Java EE 8            | [HTTP/2](https://en.wikipedia.org/wiki/HTTP/2)               |
| Servlet 3.1         | May 2013                                                     | [JSR 340](https://jcp.org/en/jsr/detail?id=340)              | Java EE 7            | Non-blocking I/O, HTTP protocol upgrade mechanism ([WebSocket](https://en.wikipedia.org/wiki/WebSocket))[[14\]](https://en.wikipedia.org/wiki/Java_servlet#cite_note-14) |
| Servlet 3.0         | [December 2009](http://www.javaworld.com/javaworld/jw-02-2009/jw-02-servlet3.html) | [JSR 315](https://jcp.org/en/jsr/detail?id=315)              | Java EE 6, Java SE 6 | Pluggability, Ease of development, Async Servlet, Security, File Uploading |
| Servlet 2.5         | [September 2005](http://www.javaworld.com/javaworld/jw-01-2006/jw-0102-servlet.html) | JSR 154                                                      | Java EE 5, Java SE 5 | Requires Java SE 5, supports annotation                      |
| Servlet 2.4         | [November 2003](http://www.javaworld.com/jw-03-2003/jw-0328-servlet.html) | [JSR 154](https://jcp.org/en/jsr/detail?id=154)              | J2EE 1.4, J2SE 1.3   | web.xml uses XML Schema                                      |
| Servlet 2.3         | [August 2001](http://www.javaworld.com/jw-01-2001/jw-0126-servletapi.html) | [JSR 53](https://jcp.org/en/jsr/detail?id=53)                | J2EE 1.3, J2SE 1.2   | Addition of `Filter`                                         |
| Servlet 2.2         | [August 1999](http://www.javaworld.com/jw-10-1999/jw-10-servletapi.html) | [JSR 902](https://jcp.org/en/jsr/detail?id=902), [JSR 903](https://jcp.org/en/jsr/detail?id=903) | J2EE 1.2, J2SE 1.2   | Becomes part of J2EE, introduced independent web applications in .war files |
| Servlet 2.1         | [November 1998](http://www.javaworld.com/jw-12-1998/jw-12-servletapi.html) | [2.1a](https://web.archive.org/web/20090611171402/http://java.sun.com/products/servlet/2.1/servlet-2.1.pdf) | Unspecified          | First official specification, added `RequestDispatcher`, `ServletContext` |
| Servlet 2.0         | December 1997                                                | N/A                                                          | JDK 1.1              | Part of April 1998 Java Servlet Development Kit 2.0[[15\]](https://en.wikipedia.org/wiki/Java_servlet#cite_note-15) |
| Servlet 1.0         | December 1996                                                | N/A                                                          |                      | Part of June 1997 Java Servlet Development Kit (JSDK) 1.0[[9\]](https://en.wikipedia.org/wiki/Java_servlet#cite_note-Hunter200003-9) |

Servlet versions and apache tomcat versions

| **Servlet Spec** | **JSP Spec** | **EL Spec** | **WebSocket Spec** | **JASPIC Spec** | **Apache Tomcat Version** | **Latest Released Version** | **Supported Java Versions**             |
| :--------------- | :----------- | :---------- | :----------------- | :-------------- | :------------------------ | :-------------------------- | :-------------------------------------- |
| 4.0              | 2.3          | 3.0         | 1.1                | 1.1             | 9.0.x                     | 9.0.31                      | 8 and later                             |
| 3.1              | 2.3          | 3.0         | 1.1                | 1.1             | 8.5.x                     | 8.5.51                      | 7 and later                             |
| 3.1              | 2.3          | 3.0         | 1.1                | N/A             | 8.0.x (superseded)        | 8.0.53 (superseded)         | 7 and later                             |
| 3.0              | 2.2          | 2.2         | 1.1                | N/A             | 7.0.x                     | 7.0.100                     | 6 and later (7 and later for WebSocket) |
| 2.5              | 2.1          | 2.1         | N/A                | N/A             | 6.0.x (archived)          | 6.0.53 (archived)           | 5 and later                             |
| 2.4              | 2.0          | N/A         | N/A                | N/A             | 5.5.x (archived)          | 5.5.36 (archived)           | 1.4 and later                           |
| 2.3              | 1.2          | N/A         | N/A                | N/A             | 4.1.x (archived)          | 4.1.40 (archived)           | 1.3 and later                           |
| 2.2              | 1.1          | N/A         | N/A                | N/A             | 3.3.x (archived)          | 3.3.2 (archived)            | 1.1 and later                           |

### Servlet Example: HelloWorld

1.Generating maven project

```shell
$ mvn archetype:generate -DgroupId=com.taogen.example -DartifactId=servlet-helloworld -DarchetypeArtifactId=maven-archetype-webapp -DinteractiveMode=false
```

2.Add servlet-api dependencies in `pom.xml`

```xml
<dependency>
    <groupId>javax.servlet</groupId>
    <artifactId>servlet-api</artifactId>
    <version>2.5</version>
    <scope>provided</scope>
</dependency>
```

3.Add `HelloWroldServlet.java`

```java
package com.taogen.example;

import java.io.*;
import java.util.Date;
import javax.servlet.*;
import javax.servlet.http.*;

// Extend HttpServlet class
public class HelloWorldServlet extends HttpServlet {
 
   private String message;

   public void init() throws ServletException {
      // Do required initialization
      message = "Hello World! ";
   }

   public void doGet(HttpServletRequest request, HttpServletResponse response)
      throws ServletException, IOException {
      
      // Set response content type
      response.setContentType("text/html");

      // Actual logic goes here.
      PrintWriter out = response.getWriter();
      out.println("<h3>" + this.message + "</h3>");
   }

   public void destroy() {
      // do nothing.
   }
}
```

4.Configuring servlet in `web.xml`

```xml
<servlet>
    <servlet-name>HelloWorld</servlet-name>
    <servlet-class>com.taogen.example.HelloWorldServlet</servlet-class>
</servlet>
<servlet-mapping>
    <servlet-name>HelloWorld</servlet-name>
    <url-pattern>/HelloWorld</url-pattern>
</servlet-mapping>
```

5.Running project

Package project `mvn package`, move war file to Apache tomcat `/webapps` directory, starting Apache Tomcat server.

Accessing the URL `http://localhost:8080/{your-servlet-context}/HelloWrold`

## The Servlet Interface

Servlet interface 是 Java Servlet API 的核心抽象。所有的 servlets 直接或间接的 implements 这个接口。Java Servlet API 中有两个实现了 Servlet interface 的类 `GenericServlet` 和 `HttpServlet`。一般开发人员通过 extends `HttpServlet`  来实现它们 Servlets。

### Request Handing Methods

基础的 Servlet interface 定义了 `service` 方法来处理客户端请求。对于 servlet container 路由到 servlet 实例的每个请求，都会调用此方法。

处理 Web 应用程序的并发请求通常需要 Web Developer 设计可以处理多个线程执行 `service` 方法的servlets。

通常 Web container 通过在不同的线程上并发执行 `service` 方法来处理对同一 servlet 的并发请求。

#### HTTP Specific Request Handling Methods

`Servlet` 接口 的抽象子类 `HttpServlet` 添加了额外的方法来帮助处理基于 HTTP 的请求。这些方法是：

- doGet
- doPost
- doPut
- doDelete
- doHead
- doOptions
- doTrace

### Number of Instances

在非分布式环境，servlet container 中每个 servlet 仅能有一个实例。servlet 实现 `SingleThreadModel` 接口，servlet container 可能会实例化多个 servlet 实例。

`SingleThreadModel` 保证仅有一个线程执行 servlet 实例的 service 方法，它避免并发访问一个 servlet 实例的 service 方法，然而，我们可以通过其它方法达到这个目的，`SingleTHreadModel` 是不推荐使用的。

### Servlet Life Cycle

Servlet 是通过定义明确的声明周期进行管理的，该生命周期定义了如何加载和实例化，如何初始化，如何处理来时客户端的请求，和如何退出服务。API 中的生命周期由 `javax.servlet.Servlet` 接口的 `init`，`service` 和 `destroy` 方法表示，所有的 servlet 必须直接或者通过 GenericServlet 或 HttpServlet 抽象类间接地实现这个接口。

#### Loading and Instantiation

Servlet container 负责加载和实例化 servlet。加载和实例化可以在 container 启动的时候，或者延迟到 container 需要 servlet 来处理请求。

#### Initialization

在 servlet 对象实例化后，在它能处理客户端请求之前，container 必须初始化 servlet。初始化方便 servlet 可以读取持久性配置数据，初始化昂贵的资源以及执行其它一次性的活动。container 通过实现 ServletConfig 接口唯一（每个 Servlet 声明）对象调用 Servlet 接口的 init 方法开初始化 servlet。配置对象允许 servlet 从 Web 应用配置信息中访问 name-value 初始化参数。

#### Request Handling

在一个 servlet 正确初始化后，servlet container 可能使用它来处理客户端的请求。请求通过 ServletRequest 对象来表示，servlet 通过调用 ServletResponse 对象提供的方法来响应请求。这两个对象作为参数传递给Servlet 接口的 service 方法。

对于 HTTP 请求，container 提供的对象类型是 HttpServletRquest 和 HttpServletResponse。

Multithreading Issues

Servlet container 可能通过 servlet 的 service 方法发送并发请求。为了处理这些请求，Servlet 开发者必须为 service 方法中的多线程并发处理做好充分的准备。

Servlet 的 service 方法不建议使用 synchronized 关键字，因为那将使得 container 不能使用 instance pool，而是顺序执行请求，这会严重影响 servlet 的性能。

Exception During Request Handling

Servlet 在请求 service 时，可能 throw ServletException 或 UnavailableException，ServletException 表示在处理请求过程中由错误发生，并且 container 应该采取适当措施来清理请求。UnavailableException 表示这个 servlet 临时或永久地不能处理请求，container 必须从服务中移除这个 servlet，调用它的 destroy 方法，并且释放 servlet 实例。

Thread Safety

request 和 response 对象的实现没有保证线程安全，这意味着它们应该在请求处理线程范围内使用。request 和 response 对象的引用不应该执行在其它线程。

#### End of Service 

servlet container 不需要在任何特定时间内都保持 servlet 的加载。Servlet 实例在 servlet 容器中的生命周期可能是几天，几个月或者几年。

当 servlet container 决定把一个 servlet 从 service 中移除，它调用 Servlet 接口的 destroy 方法去允许这个 servlet 去释放所有它使用的和保存的任何持久状态的资源。

在 servlet container 调用destroy 方法，它必须允许任何正在执行这个 servlet 的 service 方法的线程执行完毕，或者执行超时。

一旦一个 servlet 实例的 destroy 方法被调用了，这个实例将不再接收请求。如果 container 需要这个servlet，它必须重新创建一个新的实例。在 destroy 方法完成后，servlet container 必须释放 servlet 实例，让它能够进行垃圾收集。

## The Request

Request object 封装了来自客户端请求的所有信息。对于 HTTP 协议，这个信息是从客户端传递到服务器的 HTTP  请求的 headers 和 message body。

### HTTP Protocol Parameters

Parameters 是由一组 name-value pair 存储的。ServletRequest 接口中获取参数的方法有：

- getParameter
- getParameterNames
- getParameterValues
- getParameterMap

来自 query string 和 post body 的数据是聚合在 request parameter set 中的。query string 数据表示在post body 数据之前。如：一个请求的 query string 是 a=hello，它的 post body 是 a=goodbye&a=world，参数集的结果将是 a=(hello, goodbye, world)。

Post 表单数据转换为 parameter set 的条件：

1. 它是一个 HTTP or HTTPS 请求。
2. HTTP method 是 POST。
3. content type 是 application/x-www-form-urlencoded
4. Servlet 已对请求对象上的任何 getParameter 方法族进行了初始调用。

如果上面的条件没有全部满足，post 请求的 form data 不会包含在 parameter set中，但 post data 依然可以通过 request object 的 input stream  中获取。如果所有条件都满足，post form data 将不再能从 request object 的 input stream 中读取。

### Attributes

Attributes 是关联一个请求的对象。Container 可以设置 attributes 以表示无法通过 API 表示的信息，或者可以由 servlet  设置 attributes 以将信息传递给另一个 servlet （通过 RequestDispatcher）。ServletRequest 接口操作 attributes 的方法有： 

- getAttribute
- getAttributeNames
- setAttribute

一个 attribute value 只能与一个 attribute name 关联。

以 “java.” 和 “javax.” 为前缀的 attributes 是 servlet specification 定义的。

### Headers

HttpServletRequest 接口获取 header 的方法有：

- getHeader
- getHeaders
- getHeaderNames

可能存在多个 headers 是相同的名称，如果有多个 header 是相同的名称，getHeader 方法返回第一个 header，getHeaders 方法返回所有 headers 的 Enumeration 对象。

Headers 可能 string 表示的 int 和 Date 数据，HttpServletRequest 接口提供了直接获取 int 和 Date 类型的数据的方法：

- getIntHeader
- getDateHeader

getIntHeader 方法可能会 throw NumberFormatException，getDateHeader 方法可能 throw IllegalArgumentException。

### Request Path Elements

request URI = Context Path + Servlet Path + Path Info

### Path Translation Methods

Servlet API 中允许开发者获取 Java Web 应用的文件在文件系统中的绝对文件路径。这些方法是：

- ServletContext.getRealPath
- HttpServletRequest.getPathTranslated

### Cookies

HttpServletRquest 接口提供了 getCookies 方法去获取请求中的一组 cookies。这些 cookies 是从客户端每次发送到服务端的请求中的数据。

服务端可以添加和删除 Cookie， 以及设置 cookie 的属性，如有效期等。

### Internationalization

客户端可以选择向 Web server 指示它们希望以哪种语言给出响应。客户端可以使用 Accept-Language header 来传达这个信息。ServletRequest 接口提供了决定发送者的偏好语言的方法：

- getLocale
- getLocales

getLocale 方法返回客户端最希望的语言的 locale 对象。getLocales 方法返回 locale 对象的 Enumeration，以降序的方式表示客户端所有偏好的语言。

如果客户端没有指明偏好的语言，那么 getLocale 将返回 servlet container 默认的 locale，getLocales 将返回只包含 默认 locale 的 enumeration。

### Request data encoding

如果客户端没有通过 Content-Type header 指明 HTTP request 的字符编码，HttpServletRequest.getCharacterEncoding 方法将返回 null。

Container 读取 request 的数据的默认编码为 ISO-8859-1。开发者可以通过 setCharacterEncoding 方法来设置 request 的字符编码。设置 request 字符编码一定要在读取 request 数据之前，一旦数据被读取了，字符编码的设置将不会生效。

### Lifetime of the Request Object

每一个 request 对象仅仅在 servlet 的 service 方法或者 filter 的 doFilter 方法范围内有效。Container 为了减少创建 request 对象的性能花费，通常会循环利用 request 对象。开发者必须注意，在非有效范围之外维持 request 对象的引用是不推荐的，它可能导致不确定的结果。

## Servlet Context

The Response

Filtering

Sessions

Dispatching Requests

Web Applications

Application Lifecycle Events

Mapping Requests to Servlets

Security

## References

[1] Java Servlet 2.5 Specification

[2] [Java servlet - Wikipedia](https://en.wikipedia.org/wiki/Java_servlet)



--END--





