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



## References

