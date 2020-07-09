Title: 分布式系统（一）：应用服务器集群

**Content**

- 应用服务器集群的实现
- 应用服务器集群的问题和挑战
- References

## 应用服务器集群的实现

### 使用 DNS 实现负载均衡

我们可以使用自建的域名服务器（nameservers）集群的方式去实现负载均衡，其中，有一个 master nameserver 和多个 slave nameservers。

另外，使用 Anycast （不同节点的服务器用相同的 IP 地址）可以实现 DNS 的高可用。

### 使用代理服务器实现负载均衡

Nginx 服务器是常用的代理服务器，以下是 Nginx 实现负载均衡的简单的配置：

```
upstream myapp1 {
    server srv1.example.com max_fails=1 fail_timeout=10s;
    server srv2.example.com;
    server srv3.example.com;
}

server {
    listen 80;
    location / {
    	proxy_pass http://myapp1;
    }
}
```



## 应用服务器集群的问题和挑战

### 分布式服务器集群的 Session 一致性问题

HTTP 协议是无状态的，对于网站服务，我们需要维护一个状态，来标识一个请求是那个用户发出的。对于某一个客户端的用户在一段时间内的连续多次请求，我们需要维护一个会话状态。

具体的实现是：服务器为每个新的请求创建一个新的 session，并把 sessionId 作为 cookie 保存到客户端中。

然而，当我们的服务器从一台变为多台时，我们就会遇到 Session 一致性问题。某一个客户端的用户在一段时间的连续多次请求，可能不是请求在同一个服务器。不同的服务器独立的维护自己的 session，导致某个客户端的连续多次请求有不同的 session，因此，不一致的 session 导致无法通过 session 去标识一个用户。

解决 session 一致性问题常见的方法有：session sticky，session replication，session storing，cookie based 等。下面具体详细的说明每种方法的实现。

**Session Sticky**

Session sticky 的实现是让负载均衡器，让同一个用户的请求发送到同一个服务器端处理。

Nginx 作为负载均衡器时，只需要选择 ip_hash 的 load-balancing method，就可以实现 Session Sticky 的功能。Nginx 的配置如下：

```
upstream backend {
    ip_hash;
    server backend1.example.com;
    server backend2.example.com;
}
```

Session sticky 方法的缺点：

- 如果有一台 Web 服务器宕机或者重启，那么这台机器上的会话数据会丢失。
- 负载均衡需要额外的开销，来保证同一会话的请求发送到同一服务器。
- 负载均衡变成了一个有状态的节点，需要保存会话到具体 Web 服务器的映射。需要消耗更多的内存，容灾方面会更麻烦。

**Session Replication**

Session Replication 是通过将会话同步复制到每一个应用服务器中，从而保证每一个服务器的 session 都是一致的。

一般的应用服务器都支持 session replication 功能。

与 session sticky 方法相比，session replication 对负载均衡器没有那么多的要求。这个方案也有一些不足的之处。

Session Replication 的缺点：

- 同步 session 数据造成了网络宽带的开销。
- 每台 web 服务器都要保存所有的 session 数据，如果整个集群的 session 数量很多（很多人在同时访问网站）的话，每台机器用于保存 session 数据的内容占用会很严重。

Session Replication 的使用场景：

这个方案不适合集群机器数多的场景。如果只有几台机器，用这个方案是可以的。





---

Session Storing

Cookie Based

## References

