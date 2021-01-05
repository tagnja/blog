---
title: How to use Nginx reverse proxy virtual domain on Windows 10
date: 2021-01-05 10:55:41
tags: Nginx
categories: 
- Java
- Java Web
---



## Downloading Nginx for Windows

Downloading Nginx for Windows from [Nginx](http://nginx.org/en/docs/windows.html).

## Configuring hosts file of Windows

Configuring virtual domains in `C:\Windows\System32\drivers\etc\hosts`

```
127.0.0.1 example1.com
127.0.0.1 example2.com
```

## Configuring Nginx Reverse Proxy

Configuring Nginx reverse proxy in `conf\nginx.conf`

```
http {
	...
    server {
        listen       80;
        server_name  example1.com;
        location / {
            proxy_pass http://127.0.0.1:8081;
        }
    }
    
    server {
        listen       80;
        server_name  example2.com;
        location / {
            proxy_pass http://127.0.0.1:8082;
        }
    }

}
```

## Starting Nginx

### Starting Nginx

```
cd D:\nginx-1.18.0
start nginx.exe
```

### Stop Nginx

CMD of Windows

```
cd D:\nginx-1.18.0

# fast shutdown
nginx -s stop

# graceful shutdown
nginx -s quit
```

Git bash

```
cd D:\nginx-1.18.0

# fast shutdown
./nginx.exe -s stop

# graceful shutdown
./nginx.exe -s quit
```

### Reload configuration of Nginx

CMD of Windows

```
cd D:\nginx-1.18.0
nginx -s reload
```

Git bash

```
cd D:\nginx-1.18.0
./nginx.exe -s reload
```

**Note**

1. Ports 80, 433... of Nginx listened can't be occupied.
2. You must use Nginx commands to start and stop Nginx, else you can't to stop the Nginx. Unless to kill Nginx processes on Task Manager - Details of Windows, or to restart the Windows system.
3. Before run `start nginx`, you need to check is there an Nginx server is running. If there is an Nginx server is running, you must run `nginx -s stop` firstly. Repeatedly running `start nginx`, it will start multiple Nginx server and you can't stop all Nginx servers. Unless to kill Nginx processes on Task Manager - Details of Windows, or to restart the Windows system.