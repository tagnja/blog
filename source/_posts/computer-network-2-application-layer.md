---
title: 深入理解计算机网络（二）：应用层
date: 2019-08-21 18:25:51
categories: 
- 计算机基础
- 计算机网络
tags: Network
---

本文主要探究计算机网络的应用层，我们先介绍应用层的基本概念，然后逐一的介绍常见的应用层协议。

### 应用层的基本概念

应用层是网络协议栈中最上的一层，也是网络通信的第一层，它提供了让两个终端设备进行信息交换的服务。它定义了信息交换的格式，然后把定义好的信息交给它下面一层的传输层去传输。

应用层是由应用层的硬件和软件组成。硬件主要是终端设备，如手机、个人电脑、服务器等。软件主要是应用层网络协议，如 HTTP、DNS 等。应用层协议是给应用程序提供网络通信的基础，一个终端设备中应用程序通过应用层协议发送网络请求，另一个终端设备接收到请求，并通过应用层协议响应请求。其中，发送请求的一端称为客户端，响应请求的一端称为服务端。

网络通信实际上是两个终端设备上的应用程序进行通信，操作系统中一个运行的应用程序称为进程，网络通信也称为**进程通信**。

开发一个网络应用程序，首先，我们需要根据需求选择一个**网络应用架构**，常见的架构有 CS 架构（Client-Server Architecture）和 P2P 架构 （Peer-to-Peer Architecture）。然后选择一个合适的**应用层协议**，如 HTTP，FTP等。

- CS 架构。它有一个一直开启、一直提供服务的主机，称为服务器，其它请求它的主机称为客户机。它的主要特点：客户端之间不直接进行通信，服务器有一个固定的 IP 地址，所有的通信都是在客户端与服务器之间进行。常见的 CS 架构应用有：Web 应用，文件传输，远程登录，和 电子邮件等。

- P2P 架构。任何两个主机直接可以直接进行通信，这一对主机称为 Peers。提供服务的称为服务器，请求服务的称为客户机。任何一台主机既可以作为服务端，又可以作为客户端。常见的 P2P 架构应用有：文件分享应用，对等下载加速应用迅雷，和网络电话视频应用如 Skype。

- 混合架构，应用程序既使用 CS 架构，也使用 P2P 架构。如即时消息软件中，服务器用于记录用户的IP地址和相关信息，用户与用户之间直接进行通信。

不同的架构采用了不同的应用层协议，不同的应用层协议使用不同的传输层协议提供的传输服务。下面列举了一些常见的应用采用的网络架构和网络协议。

| 应用         | 应用架构 | 应用层协议               | 底层传输层协议 |
| ------------ | -------- | ------------------------ | -------------- |
| 电子邮件     | CS       | SMTP                     | TCP            |
| 远程终端登录 | CS       | Telnet                   | TCP            |
| Web          | CS       | HTTP                     | TCP            |
| 文件传输     | CS       | FTP                      | TCP            |
| 流媒体       | CS       | HTTP                     | TCP            |
| 网络电话视频 | P2P      | SIP, RTP, or Proprietary | UDP or TCP     |
| 文件分享     | P2P      | BitTorrent               | TCP            |

通过这个小节，我们了解了应用层的基本概念，对应用层有了一个大致的了解。接下来，我们将介绍一些常见的应用层协议。

### Web 应用和 HTTP 协议

<br>

#### 介绍 HTTP

介绍 HTTP 之前，我们先了解一下 World Wide Web 也称 Web。它是世界上使用最广泛的网络应用之一。它是一个信息系统，它提供文件和其它 web 资源的访问服务，所有的资源文件通过 URL（Uniform Resource Locator）来定位。在浏览器输入一个网站的 URL 就可以访问这个网站的资源文件。Web 应用采用的是 CS 架构和 HTTP 协议。

HTTP 协议（HyperText Transfer Protocol），它定义了网络通信中消息的结构，以及客户端和服务端如何交换消息，消息可以称为 HTTP 报文（HTTP Message）。HTTP 网络通信由客户端应用程序和服务端应用程序构成，客户端应用程序为 Web 浏览器，服务端应用程序为 Web 服务器。HTTP 通信的过程是：浏览器通过 URL 指定要访问的资源，然后发送 HTTP 请求给服务器，服务器收到请求后，响应 HTTP 请求。浏览器收到的服务器的响应信息后，渲染该资源文件。

#### HTTP 报文格式

**HTTP 请求报文格式**

```
(Request Line) method URL Version
(Header Lines) header_field_name: value
               header_field_name: value
			   ...
(Entity Body)  ...
```

例子

```
GET /somedir/page.html HTTP/1.1
HOST: www.someschool.edu
Connection: close
User-agent: Mozilla/5.0
Accept-language: en
(data data ...)
```

字段说明：

请求行（Request Line）中定义了 HTTP 请求的关键参数。其中 Method 字段表示请求的方式，常用的请求方式为GET，POST。URL 字段用来定位资源，Version 字段用来说明当前使用的协议版本。

头部行（Header Lines）中定义了可选的参数，用来辅助 HTTP 请求。

数据域（Entity Body） 用来传输数据。



**HTTP 响应报文格式**

```
(Status Line)  version status_code phrase
(Header Lines) header_field_name: value
               header_field_name: value
               ...
(Entity Body)  ...
```

例子

```
HTTP/1.1 200 OK
Connection: close
Date: Wed, 12 Aug 2019 12:00:01 GMT
Server: Apache/2.2.3
Last-Modified: Wed, 12 Aug 2019 9:11:03 GMT
Content-Length: 6821
Content-Type: text/html
(data data ...)
```

字段说明：

状态行（Status Line）定义了响应报文的关键参数。其中 Version 用来说明当前使用的协议版本，Status Code 表示请求结果的状态码，phrase 为状态码的说明。

头部行（Header Lines）中定义了可选的参数，用来辅助 HTTP 响应。

数据域（Entity Body） 用来传输数据。响应的数据一般是 HTML 文本，或者 JSON 文本。

常见的状态码

- 200 OK：请求成功。
- 301 Moved Permanently：资源位置永久移动，请求会自动重定向到资源新的 URL。
- 400 Bad Request：错误的请求。
- 404 Not Found：请求的资源不存在服务器中。
- 505 HTTP Version Not Supported：HTTP  协议版本不支持。



#### Cookie

HTTP 服务器是无状态的，这个设计简化了服务器，允许开发者开发一个高性能的 Web 服务器，服务器可以同时处理上千的 TCP 连接。但是一个网站往往想要区分用户，这样可以限制用户的接入权限，以及记录用户的行为等。为了达到这个目的，HTTP 可以使用 cookie 来追踪用户。

Cookie 是 HTTP 报文中头部中的一个属性，使用 Cookie 由四个部分组成：

- HTTP 响应报文的头部 （header lines）字段 `Set-Cookie` 设置 cookie  。
- HTTP 请求报文的头部字段 `Cookie` 携带用户的 cookie。
- 一个 Cookie 文件保存在用户终端，由浏览器管理。
- 后端数据库保存用户 Cookie。

浏览器每次请求都会携带 Cookie 发送给服务器，服务器可以根据 Cookie 来追踪用户的行为，分析数据，从而优化网站的功能。



#### Web Cache

Web 缓存服务器 （Web Cache）也称代理服务器，Web Cache 可以缓存静态资源在本地，而不用请求 Web 服务器。

一个机构或公司使用局域网 Web 缓存服务器，可以减少客户端 HTTP 请求的响应时间，也可以减少机构接入网络的带宽。



### DNS 协议

<br>

#### 介绍 DNS

网络中服务器是通过 IP 地址来识别的， IP 地址是由4个十进制的数字组成如121.7.106.83，这样的地址不方便记忆。DNS （Domain Name System）可以为 Web 服务器提供一个有字母和数字组成的域名 ，如 google.com。

DNS 是一个实现在多个 DNS 服务器的分布式数据库。一个应用层协议允许去查询分布式数据库。它提供了将域名转换为 IP 地址的服务。

浏览器通过服务器的域名获取 IP 地址的过程

- 查询本地缓存。浏览器先检查浏览器本地缓存中是否有该域名，如果没有，检查电脑的 DNS 缓存中是否有该域名。
- 查询 DNS 服务器。如果本地缓存没有找到，浏览器通过发送 DNS 查询请求给 DNS 服务器，DNS 接收到请求，返回该域名的 IP 地址。浏览器获得该网站的 IP 地址。（为了便于理解，这里简化了对 DNS 服务器的访问，实际会请求多个 DNS 服务器，下面会详细说明）

>  为什么浏览器知道了域名，还需要 Web 服务器的 IP 地址？
>
>  浏览器是客户端进程，Web 服务器是服务端的一个进程，在操作系统中进程之间的通信是通过 Socket 完成，每个进程都有唯一对应的 Socket，Socket 连接应用层和传输层桥梁。因为 Socket 通信需要的是 IP 地址建立 TCP 连接，所以浏览器需要知道 Web 服务器的 IP 地址。不过查询 IP 地址的过程，是自动地快速地进行的，用户是无感的。

DNS 提供地服务

- Web 服务器别名。
- 邮件服务器别名。
- 负载分配。一个域名关联一组 IP 地址，多个服务器依次处理 HTTP 请求，从而减少了单个服务器的压力。



#### DNS 层级结构

DNS 服务器是分布在世界各地的，是有层级关系的。三层结构如下：

- 第一层：根 DNS 服务器 （Root DNS Servers）管理下一层顶级域名 DNS 服务器的地址。

- 第二层：顶级域名 DNS 服务器（Top-Level Domain (TLD) DNS Servers）管理下一层权威域名 DNS 服务器的地址。一般的顶级域名如 .com、 .edu、 .gov、 .net等。

- 第三层：权威域名服务器 （authoritative DNS servers）记录了组织机构、公司的所有域名对应的 IP 地址。如 *.baidu.com（www.baidu.com, baike.baidu.com, news.baidu.com）

通过循环查询 DNS 服务器，得到一个域名对应的 IP 地址的过程， 如 cs.umass.edu

- 客户端请求 <本地 DNS 服务器>，本地 DNS 服务器检查缓存中是否有 cs.umass.edu 对应的 IP 地址。如果有就返回，没有就进行到下一步。
- 本地 DNS 服务器请求<根 DNS 服务器>，查询 edu 顶级域名服务器 IP 地址。
- 本地 DNS 服务器请求<顶级域名 DNS 服务器>，查询 umass.edu 的权威域名服务器的 IP 地址。
- 本地 DNS 服务器请求<权威域名服务器>，查询 cs.umass.edu 对应的 IP 地址。
- 本地 DNS 服务器最终得到了cs.umass.edu 的 IP 地址，并返回给客户端。



#### DNS 记录

DNS 分布式数据存储的是 Resource Records （RRs）

一个记录有四个字段（Name, Value, Type, TTL）

TTL 表示域名在 本地 DNS 服务器缓存的时间。

不同的类型表示不同的功能，常见的类型有：

Type=A，表示域名映射到 IP 地址。如（bar.foo.com, 145.37.93.126, A）

Type=CNAME, 表示权威域名的别名。如（foo.com, www.foo.com, CNAME）

Type=MX，表示权威域名的邮箱服务别名。如（foo.com, mail.foo.com, MX）

Type=NS，表示域名进一步查询地址。如（foo.com, dns.foo.com, NS）

#### DNS 报文

DNS 的请求报文和响应报文格式是一致的，由于用的比较少，具体细节，这里不详细展开，有兴趣的朋友可以去查阅相应资料。



### CDN 内容分发网络

网络视频需要消耗大量的网络带宽，很多网络视频公司会选择 CDN （Content Distribution Network）服务，来优化用户的体验。

一个 CDN 网络管理很多分布在世界各地的服务器，在每个服务器上复制了视频和其它资源文件的副本，用户请求 CDN，然后 CDN 提供一个离用户客户端距离较近的服务器同响应客户端请求，从而给用户更快更好的网络体验。CDN 的实现是利用了 DNS 拦截和重定向请求功能。

不使用 CDN 可能会出现的问题

- 每个客户端都请求同一个地区的服务器（数据中心），服务器的带宽是一定的，请求太多造成客户端得到的资源吞吐量下降，会导致视频卡顿。
- 一个流行的视频会在同一个通信链路上多次传输，浪费网络带宽。
- 一个数据中心容易出现单点故障，导致整个服务不可用。

CDN 使用的过程

- 用户访问一个视频的链接，如 http://video.netcinema.com/6Y7B23V 。
- 浏览器查询客户端本地缓存是否存在该域名的 IP 地址。如果没有，继续下一步查询。
- 浏览器请求本地 DNS 服务器，查询 video.netcinema.com 域名的 IP 地址。本地 DNS 服务器检查本地缓存是否存在该域名，不存在，则请求权威 DNS 服务器，查询 video.netcinema.com 的 IP 地址。权威 DNS 服务器发现是 video 开头的域名，则返回 CDN 提供商的权威域名如 a105.kingcdn.com。
- 本地 DNS 服务器请求权威域名服务器，查询 a105.kingcdn.com 的 IP 地址，CDN 提供商根据客户端的地理位置，最终返回 CDN 系统中的一个 web 服务器 IP 地址。
- 本地 DNS 服务器把 IP 地址返回给浏览器。
- 浏览器通过 IP 地址与服务器建立 TCP 连接，发送视频访问请求。web 服务器返回视频内容给浏览器。

CDN 是如何让客户端访问离自己最近的一个服务器

方法一

使用 DNS 服务器响应一个离用户最近的服务器的 IP 地址。DNS 服务器通过查询 IP 位置数据库，获取客户端所在的城市。根据客户端所在的城市选一个最近的一个服务器的 IP 地址返回给客户端。

方法二

使用任播（Anycast）路由选择一个最近的服务器。CDN 分布世界各地的所有 Web 服务器使用同一个 IP 地址。路由器根据最少花费（Lowest Cost）的路由规则，选择一个离客户端最近的服务器。



### References

[1] Computer Networking: A Top-Down Approach, by Jim Kurose

[2] [World Wide Web - Wikipedia](https://en.wikipedia.org/wiki/World_Wide_Web)

[3] [DNS Explained - How Your Browser Finds Websites](https://scotch.io/tutorials/dns-explained-how-your-browser-finds-websites)

[4] [How does Akamai CDN use DNS to allocate closest edge server to client? - Quora](https://www.quora.com/How-does-Akamai-CDN-use-DNS-to-allocate-closest-edge-server-to-client)