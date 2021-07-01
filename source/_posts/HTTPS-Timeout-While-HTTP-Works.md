---
title: '[Error] HTTPS Timeout While HTTP Works'
date: 2021-07-01 10:24:37
tags: error
categories:
- 其它
- 问题解决
---

## Background

Using HTTP domain visit my website  is ok, but HTTPS not.

## Error Info

ERR_TIMED_OUT

```
This site can’t be reached
xxx.xxx.com took too long to respond.
Try:
- Checking the connection
- Checking the proxy and the firewall
- Running Windows Network Diagnostics
ERR_TIMED_OUT
```

## Solutions

ERR_TIMED_OUT represents this is a connection error, not SSL certificate errors.

Common Solution

- Check whether the server IP or HTTP domain can be visited. If ok, the connection is OK.
- Check whether the server reversed proxy is right, and server firewall is open 80 and 443.
- Check whether the client browser and client network is right.

Current HTTP is work represented the connection is ok. And client browser can visit other HTTPS websites, represented client is ok.

You need to check your server reversed proxy is right, and ports 80 and 443 of your server firewall are open .

## Reasons

My server's port 443 is not open.

## References

[1] [https timeout while http works](https://serverfault.com/questions/89399/https-timeout-while-http-works)
