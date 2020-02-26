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

Servlet container 是 Web server 或 application server 的一部分，它提供了发送 request 和 response 的网络服务，解析基于 MIME 的 requests，以及格式化基于 MIME 的 responses。Servlet Container 还管理 Servlets 的整个 lifecycle。

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



### Servlet Life Cycle



### End of Service



## The Request

Servlet Context

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





