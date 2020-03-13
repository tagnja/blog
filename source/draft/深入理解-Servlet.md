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

SevletContext interface 定义了一组方法让 servlet 与 它的 servlet container 进行交流。例如，获取一个文件的 MIME type，dispatch request，，写日志，以及设置和存储所有 servlet 可以访问的属性等。

### Scope of ServletContext Interface

每一个 Java  虚拟机的每一个 Web 应用程序只有一个 ServletContext 对象。

### Initialization Parameters

ServletContext 接口允许 servlet 访问在 deployment descriptor 定义的 context 初始化参数的方法：

- getInitParamenter
- getInitParamenterNames

应用程序开发人员使用初始化参数来表示设置信息。如网站管理员的电子邮件地址，或者系统的关键配置数据。

### Context Attributes

Servlet 可以通过 name 把对象属性绑定到 context 中。任何绑定到 context 中的 attribute Web 应用程序中任何其他 servlet 都可以使用。ServletContext 接口操作 attributes 的方法：

- setAttribute
- getAttribute
- getAttributeNames
- removeAttribute

### Resources

ServletContext interface 提供了直接访问静态类型文档，如 HTML，GIF，和JPEG 等文件的方法：

- getResource
- getResourceAsStream

静态文件的 path 是以‘/’开始的 context 根目录的相对路径。上面的方法不能获取动态文件如 getResource("/index.jsp")

### Temporary Working Directories

Servlet contiiners 必须为 每一个 servlet context 提供一个私有的临时目录，并且可以通过 `javax.servlet.context.tempdir` context attribute 来访问这个目录。



## The Response

Response object 封装了 server 返回给 client 的所有信息。在 HTTP 协议中，这个信息通过 HTTP headers 或 message body 从 server 传输到 client。 

### Buffering

出于效率目的，允许（但不是必需）一个servlet容器来缓冲输出到客户端的输出。一般 servers buffering 是默认的，允许 servlet 去指定 buffering 的参数。

ServletResponse interface 允许 servlet 访问和设置 buffering 信息的方法：

- getBufferSize
- setBufferSize
- isCommitted
- reset
- resetBuffer
- flushBuffer

ServletResponse interface 提供这些方法去执行缓冲操作，无论 servlet 使用 ServletOutput 还是 Writer。

### Headers

Servlet 可以通过 HttpServletResponse interface 的方法去设置 HTTP repsose 的 headers：

- setHeader
- addHeader

HttpServletResponse interface 也提供了添加具体的数据类型的 headers 的方法：

- setIntHeader
- setDateHeader
- addIntHeader
- addDateHeader

### Convenience Methods

HttpServletReponse interface 中的 convenience 方法有：

- sendRedirect
- sendError

### Internationalization

Servlet 设置 reponse 的 locale 和 character encoding。使用 ServletResponse 的 setLocale 方法可以设置 response 的 locale。如果 response 已经 committed 则 setLocale 方法是无作用的。如果 servlet 在 response committed 之前没有设置 locale，container 的默认的 locale  用于确定 response 的 locale。

可以使用 locale encoding mapping 来设置使用特定 locale 时也使用对应的 character encoding。

```xml
<locale-encoding-mapping-list>
	<locale-encoding-mapping>
		<locale>zh-CN</locale>
		<encoding>UTF-8</encoding>
	</locale-encoding-mapping>
</locale-encoding-mapping-list>
```

ServletResponse 提供了设置 character encoding 的方法：

- setCharacterEncoding
- setContentType

和 setLocale 方法一样 set character encoding 在 response committed 之后是无作用的。如果 servlet 在 ServletResponse 的 getWriter 方法调用之前或 response committed 之后，没有设置 character encoding，则默认使用 ISO-8859-1 编码。

### Closure of Response Object

当 response 关闭时，container 必须立刻 flush 在 response buffer 中所有剩余的内容，返回给 client。

request 和 response object 关闭的 events：

- servlet 的 service 方法结束。
- 在 response 的 setContentLength 方法指定的内容量大于零，，并已写入 response 中。
- 调用了 sendError 方法
- 调用了 sendRedirect 方法

### Lifetime of Response Object

每个 response 对象仅仅在 servlet 的 service 方法，或者在 filter 的 doFilter 方法范围内有效。Container 常常会 循环利用 response 对象来减少创建 response 对象的性能消耗。开发者必须注意在 response 对象的有效范围之外维护 response 对象的参考可能会导致不确定的行为。

## Filtering

Filter 是 Java Servlet 组件，它允许在接受和响应请求的过程中访问和修改请求的 header 和 payload。

### What is a Filter

Filter 是一段重用的代码，是一个 java class，它可以转换 HTTP request, response, 和 header 的内容。Filter 通常不会像 servlet 那样创建 response 或 响应请求，而是修改和调整对资源的请求，并修改或调整对资源的响应。

Filter 可以作用在动态或静态的内容上。动态和静态的内容一般指的是 Web resources。

开发者使用 filter 功能的类型有：

- 在请求调用之前访问资源。
- 在请求调用之前处理请求。
- 通过用自定义的 request 对象 wrapping request 来修改 request 的 headers 和 data。
- 通过用自定义的 response 对象来修改 response 的 headers 和 data。
- 调用资源后对其进行拦截。
- 对一个或一组 servlet 按顺序执行多个 filter 的操作。

常见的 Filter components：

- Authentication filters
- Logging and auditing filters
- Image conversion filters
- Data compression filters
- Encryption filters
- Tokenizing filters
- Filters that trigger resource access events
- XSL/T filters that transform XML content
- MIME-type chain filters
- Caching filters

### Main Concepts

开发者通过 implement `javax.servlet.Filter` interface 创建 filter，并且提供一个无参的 constructor。Filter 在 deployment descriptor 中使用 `<filter>` 进行声明。一个或一组 filter 可以通过在 deployment descriptor 中定义 `<filter-mapping>` 元素配置它的调用。这个配置通过 servlet 的 logic name 或者 resources URL 来实现。

Filter Lifecycle

在 Web 应用程序部署之后，container 接收到请求之前，container 必须找到应用于 Web 资源的 filter 列表。container 必须确保每个 filter 实例化，并且调用 `init(FilterConfig config)` 方法进行初始化。

每一个  filter 只有一个实例。当 container 接收请求，它传递 `ServletRequest` 和 `ServletResponse` 参数调用第一个 filter 实例的 `doFilter` 方法，并且 `FilterChain`  对象将被用于传递请求到下一个 filter。

Wrapping Requests and Responses

Filter 概念的核心是 wrapping 请求和响应，以便它可以重写行为以执行过滤任何。开发者不仅可以重写 request 和 response 对象存在的方法，还可以提供新的 API 去满足特定的过滤任务。

当一个 filter `doFilter` 方法被调用时，container 必须保证传递给下一个 filter 或者目标 web resource 的 request 和 response 对象与传递给当前 `doFilter` 方法的 request 和 response 对象是相同的。 另外，wrapper object 相同的要求也应用于从 servlet 或 filter 到 `RequestDispatcher.forward or include` 的调用。

Filter Environment

Filter 的初始化参数在 deployment descriptor 中`<filter>` 内的 `<init-params>` 元素中定义。Filter 通过 `FilterConfig` 的 `getInitParameter` 方法访问参数。另外，`FilterConfig` 为了加载资源，logging，存储状态到 `ServletContext` attribute 中等功能，它可以访问 `ServletContext` 对象。  

Configuration of Filters in a Web Application

For example:

```xml
<filter>
    <filter-name>My Filter</filter-name>
    <filter-class>com.example.MyFilter</filter-class>
</filter>
<filter-mapping>
	<filter-name>My Filter</filter-name>
    <servlet-name>MyServlet1</servlet-name>
    <url-pattern>/foo/*</url-pattern>
</filter-mapping>
```

Filters and the RequestDispatcher

Java Servlet 2.4 之后可以配置 filter 在调用 request dispatcher forward() and include() 方法时过滤。使用 `<dispatcher>` 元素在指出过滤请求的条件，它的值有：

- REQUEST：表示请求直接来自 client 时过滤。
- FORWARD：表示请求在使用 RequestDispatcher forward() 时过滤。  
- INCLUDE：请求在使用 RequestDispatcher include() 时过滤。
- ERROR：请求转到 error resource 时过滤。
- 以上多个值的组合

`<dispatcher>` 元素的使用示例，如下：

```xml
<filter-mapping>
	<filter-name>Logging Filter</filter-name>
	<url-pattern>/products/*</url-pattern>
	<dispatcher>FORWARD</dispatcher>
	<dispatcher>REQUEST</dispatcher>
</filter-mapping>
```



## Sessions

HTTP 是一种 stateless 协议。为了构建有效的 Web 应用程序，将来自特定 client 的请求彼此关联是必要的。Servlet specification 定义了简单的 HttpSession interface，它允许 servlet container  使用多种方法中的一种来 track 用户的 session，而无需让开发者介入方法之间的细微差别。

### Session Tracking Mechanisms

Cookies

Session 通过 HTTP cookies 来跟踪会话是 session tracking mechanism 最常用使用的。Container 发送 cookie 给 client，client 将在接下来的请求 server 中发送这个 cookie，明确地将请求与会话关联。session tracking cookie 的名称必须是 JSESSIONID。

URL Rewriting

URL Rewriting 是 session tracking 的另一种方法。当 client 不接受 cookie，URL rewriting 可能用于 server 的 session tracking。URL rewriting 涉及到添加 session ID 到 URL path 中，这个 URL path 被container 解析，并将这个 request 与一个 session 进行关联。URL rewriting 的 URL 例子如下：

```
http://www.myserver.com/index.html;jsessionid=1234
```

只有当 client 不接受 cookie 时，URL rewriting 才会有效果。当 client 可以接受 cookie 时，URL rewiring 的 URL 不会被 container 解析和将 request 与 session 关联。

### Creating Session

如果一个 session 只是一个预期的 session 而尚未建立，则认为该 session 是新的。因为 HTTP 是基于 request-response 的协议，一个 HTTP session 直到有一个 client joint it 才认为是新的。当 session 跟踪信息已经返回到 server 以表明已建立会话时，则称 client joins a session。 

### Session Scope

HttpSession 对象必须是 application (or servlet context) level。不同 context 可以有相同的建立 session 的 cookie，但是 container 不能在不同的 context 之间共享这些 session 对象。

### Binding Attributes into a Session

Servlet 可以通过一个 name 将一个 object attribute 绑定到 HttpSession 中。绑定在 session 中的 object 对于任何其他属于相同的 ServletContext 的 servlet 并且属于同一个会话的请求都是可以使用的 。

### Session Timeouts

在 HTTP protocol 中，当 client 不在活跃时没有明确的结束信号。这意味着只有 timeout period mechanism 可以表明 client 不在活跃。

Session 的默认 timeout period 在 servlet container 中定义，它可以通过 HttpSession 接口的 getMaxInactiveInterval 方法获取。开发者可以通过 HttpSession 的 setMaxInactiveInterval 方法改变这个 timeout。这些方法的 timeout period 是以秒为单位来定义的。如果 session 的 timeout period 设置为-1，这个 session 将永远不会过期。

### Important Session Semantics

Threading Issues

多个 servlet 执行请求线程可以同时访问同一个 session object。访问 session object 应该是 synchronized，开发者负责适当地 synchronizing 访问 session resources。

Client Semantics

由于 cookie 或 SSL certificates 一般是被 Web browser 控制的，它们没有与特定的浏览器窗口关联的，来自所有从 client 浏览器窗口到 servlet container 的请求可能是同一个 session。为了获得最大的可移植性，开发者应该始终假定 client 的所有窗口都参与同一个 session。

## Dispatching Requests

当构建一个 Web 应用程序，将请求的处理 forward 到另一个 servlet 或 include 另一个 servlet 在 response 中的 输出通常很有用。RequestDispatcher interface 提供了一种机制去实现它。

### Obtaining a RequestDispatcher

获取 RequestDispatcher 接口的对象可以通过 ServletContext 接口的以下方法：

- getReqeustDispatcher
- getNameDispatcher

getRequestDispathcer 方法使用一个在 ServletContext 范围的描述路径的 String 参数。这个路径是相对 ServletContext 根目录的和以 “/” 开头的相对路径。这个方法使用这个 path 去查询一个 servlet。

### Using a Request Dispatcher

使用 request dispatcher，servlet 调用 ReqeustDispatcher 接口的 include 或 forward 方法。这些方法的参数可以是传递给 javax.servlet 接口 service 方法的 request 和 response 参数或者是 request 和 response wrapper classes 的子类的实例。container 应该确保将 request 分发到目标 servlet 发生在和原始请求相同的 JVM 虚拟的同一线程。

### The Include Method

RequestDispatcher 接口的 include 方法可能在任何时候被调用。include 方法的目标 servlet 可以访问 request 对象的所有方面，但是它使用 response 对象是有很多限制的。

目标 servlet 仅仅可以把信息写入 response 对象的 ServletOutputStream 或者 Writer。它不能设置 header 或调用任何影响 header 的方法，HttpServletRequest.getSession() 方法会抛出 IllegalStateException 异常。

Included Request Paramenters

Servlet 使用 requestDispathcer 的 include 方法，以下 request attributes 是必须设置的：

- javax.servlet.include.request_uri
- javax.servlet.include.context_path
- javax.servlet.include.servlet_path
- javax.servlet.include.path_info
- javax.servlet.include.query_string

### The Forward Method

调用 RequestDispatcher 的 forward 方法必须仅仅在 server 没有内容提交给给 client。如果在 response buffer 中有没有提交的 输出数据，在目标的 servlet 的 service 方法调用之前，这些内容必须是清空的。如果 response 已经提交了，必须抛出 IllegalStateException。

Servlet 使用 requestDispathcer 的 forward 方法，以下 request attributes 是必须设置的：

- javax.servlet.forward.request_uri
- javax.servlet.forward.context_path
- javax.servlet.forward.servlet_path
- javax.servlet.forward.path_info
- javax.servlet.forward.query_string

### Error Handling

如果 request dispatcher 的目标 servlet 抛出 runtime exception 或者 Servlet Exception or IOException checked exception，它应该传播到 calling servlet。所有其他的一场应该包装成 ServletException，并且  root cause of exception 设置为 original exception，因为不应该传播该异常。

## Web Applications

Web application 是由 servlets，HTML pages，classes 和 其他资源的集合。Web 应用程序可以在不同供应商的 container 中运行。

### Web Applications Within Web Servers

Web 应用程序根植于 Web server 的特定路径。例如，catalog 应用程序应该通过 http://www.mycrop.com/catalog 定位到。所有以这个前缀开头的请求都将被路由到表示 catalog 应用程序的 ServletContext。

### Relationship to ServletContext

Servlet container 必须强制一个 Web 应用程序与一个 ServletContext 一一对应。ServletContext 对象为 servlet 提供了其应用程序视角。

### Elements of a Web Application

一个 Web application 可能有一下 item 组成：

- Servlets
- JSP Pages
- Utility Classes
- Static documents（HTML，images，sounds，etc）
- Client side Java applets，beans，and classes
- Descriptive meta information that ties all of the above elements together

### Directory Structure

Web 应用程序作为目录的结构化层次结构存在。在应用程序的层次结构中有一个特殊的目录为 `WEB-INF`，这个目录包含与应用程序相关的不在应用程序的文档 root 目录中的文件。`WEB-INF` 节点不是 应用程序的公开文档树中的一部分。容器不能将 `WEB-INF` 目录中包含的文件直接提供给 client。`WEB-INF` 目录的内容是对 ServletContext getResource 和 getResourceAsStream 方法是可见的，以及使用 RequestDispatcher 调用。如果应用开发者需要访问如应用的配置信息文件，但不希望把它直接暴露给 Client，可以把它们放到 `WEB-INF` 目录下。任何访问 `WEB-INF` 目录资源的请求将返回 404。`WEB-INF` 目录的内容有：

- `/WEB-INF/web.xml`: deployment descriptor
- `/WEB-INF/classes/`: servlet 和 uitlity classes.
- `/WEB-INF/lib/*.jar`: Java Archive files.These files contains servlets, beans, and other utility classes. 

### Web Application Archive File

可以使用标准的 Java archive tools  将 Web 应用程序打包并签名为 Web ARchive format (WAR) file。WAR 文件中的 META-INF 目录包含了对 Java archive tools 有用的信息。这个目录不能直接被 client 访问。

### Web Application Deployment Descriptor

Web 应用程序 deployment descriptor 包含一下配置和部署信息的类型：

- ServletContext Init Paramenters
- Session Configuration
- Servlet/JSP Definitions
- Servlet/JSP Mappings
- Welcome File list
- Error Pages
- Security

Dependencies On Extesions

当大量的应用程序使用相同的 code 或 resources，它们通常将作为库文件安装在 servlet container 中。这些文件一般是常用的或标准的 API，使用它们不会牺牲可移植性。Servlet container 必须为这些 libraries 提供一个目录。位于这个目录的文件必须可用被所有 Web 应用程序使用。该目录位置是特定于 container 的。

应用程序开发者依赖的扩展必须在 WAR 文件中提供 META-INF/MANIFEST.MF entry，其中列出了 WAR 需要的所有扩展。Manifest entry 的格式应该遵循标准的 JAR manifest 格式。

Web container 必须也能够识别在 WAR 中 WEB-INF/lib 条目下的任何 library JARs 的 manifest entry 中表达的声明的依赖。

### Error Handling

当异常发生在 servlet 或 JSP page，下面的属性必须设置的：

- javax.servlet.error.status_code (java.lang.Integer)
- javax.servlet.error.exception_type (java.lang.Class)
- javax.servlet.error.message (java.lang.String)
- javax.servlet.error.exception (java.lang.Throwable)
- javax.servlet.error.request_uri (java.lang.String)
- javax.servlet.error.servlet_name (java.lang.String)

这些属性允许 servlet 去生成特定的内容。

### Error Pages

为了允许当 servlet 出现了 error 时，开发者可以去自定义返回给 Web client 的内容，deployment descriptor 定义了一组 error page 描述。这个语法允许当 servlet 或 filter 调用 HttpResponse.sendError 返回特定的 status code，或者 servlet 产生的 exception 或 error 传播到了 container 时，container 将返回资源配置。

如果在 response 上调用了 sendError 方法，container 查询使用 status-code 语法声明的 Web 应用程序的 error page 列表，并尝试进行匹配。如果匹配成功，container 返回通过 location entry 指定的资源。error page 声明的例子：

```xml
<error-page>
    <error-code>404</error-code>
    <!-- /src/main/webapp/error-404.html-->
    <location>/error-404.html</location>
</error-page>
<error-page>
    <exception-type>java.lang.Exception</exception-type>
    <location>/errorHandler</location>
</error-page>
```

### Web Application Deployment

当一个 Web 应用程序部署到 container 中，在 Web 应用程序开始处理 client 请求之前，接下来的步骤是必须执行的：

- 实例化每一个在 deployment descriptor 中 `<listener>`  元素定义的 listener 的实例。
- 为了实例化实现了 ServletContextListener 的 Listener 实例，调用它的 contextInitialized() 方法。
- 实例化每个在 deployment descript 中的 `<filter>` 元素定义的 filter 的实例，并且调用它的 init() 方法进行初始化。
- 实例化每个包含 `<load-on-startup>` 的 `<servlet>` 实例，并且调用它的 init() 方法进行初始化。

### Inclusion of  a web.xml Deployment Descriptor

如果 Web 应用程序不包含任何 servlet，filter，或 listener 组件，这个应用程序不需要包含 web.xml。换句话说，一个仅仅包含静态文件或 JSP page 的Web 应用程序不需要出现 web.xml。



## Application Lifecycle Events

## Mapping Requests to Servlets

## Security

## References

[1] Java Servlet 2.5 Specification

[2] [Java servlet - Wikipedia](https://en.wikipedia.org/wiki/Java_servlet)



--END--





