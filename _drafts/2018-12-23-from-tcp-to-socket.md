---
layout: post
title: "From TCP To Socket"
---
## TCP 和 UDP

## 关于Socket

### TCP Server如何同时和多个Client建立连接？
server方通过accept()接受或等待到来的（或已经在队列中的）第一个连接请求。用户程序界面为：
    int accept(int s, struct sockaddr *addr, socklen_t *addrlen);
参数s为一个已经挂了号的server方插口（打开文件号），参数addr与addrlen则用来返回连接请求的来源，也就是client方插口的地址。对accept()调用要到有连接请求到来而建立起连接，或者发生了出错才返回。特别要说明的是，这个函数返回一个新的打开文件号。这个新的打开文件号就代表着已经建立起连接的server方插口。而原来的插口s，则保持原样不变，又可以用来接受新的（其他的）连接请求。

### 长连接与短连接

### 连接池

## 引用
* [一篇搞懂TCP、HTTP、Socket、Socket连接池](https://segmentfault.com/a/1190000014044351)
