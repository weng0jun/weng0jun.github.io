---
layout: post
title: "Rest API 设计要领"
---

REST (Representational State Transfer) 是一种面向分布式超媒体系统（Distributed Hypermedia Systems）的架构思想/风格，最早由 Roy Fielding 于2000年在他的博士论文中提出。这种架构方法的核心思想是把所有信息都抽象成 Resource ，然后用一组 Resource Methods 实现 Resource 状态的转移。采用REST方法进行架构设计的6个指导原则是：

* Client–server
* Stateless
* Cacheable
* Uniform interface
* Layered system
* Code on demand (optional)

> In simplest words, in the REST architectural style, data and functionality are considered resources and are accessed using Uniform Resource Identifiers (URIs). The resources are acted upon by using a set of simple, well-defined operations. The clients and servers exchange representations of resources by using a standardized interface and protocol – typically HTTP.

## Resource

Resource 是 REST 的核心概念，所以在设计 REST API 的时候首先要想清楚后端模型中有哪几种 Resource 以及这些 Resource 之间的关系。我自己的一般做法是一边放着 ER 图，另一边放产品原型图，然后思考如何将 ER 图中的各个对象转换成 Resource 供前端操作，从而方便的实现业务逻辑。 

> The key abstraction of information in REST is a resource.

> REST uses a resource identifier to identify the particular resource involved in an interaction between components.

> The state of resource at any particular timestamp is known as resource representation.

罗列清楚有哪些 Resource 以后还要对这些 Resource 进行归类。我们可以将 Resource 分成四类，分别是 Document, Collection, Store 和 Controller。这里要重点说明的是 Collection 和 Store 之间的区别, Collection 是由服务端管理（server-managed）的 Resource 集合，即 Collection 里面的所有 Resource 的生死存亡是有服务端管控的。相反，Store是由客户端管理（client-mmanaged）的 Resource 的集合。下面分别举几个例子方便理解：

* Collection举例:

```
http://api.example.com/device-management/managed-devices
http://api.example.com/user-management/users
http://api.example.com/user-management/users/{id}/accounts
```

* Store举例：

```
http://api.example.com/cart-management/users/{id}/carts
http://api.example.com/song-management/users/{id}/playlists
```

Controller 是唯一一类使用动词描述的 Resource，Controller 是对过程性的概念进行建模。以下几个例子说明 Controller 的使用场景：

```
http://api.example.com/cart-management/users/{id}/cart/checkout
http://api.example.com/song-management/users/{id}/playlist/play

```

## Resource Methods

Resource Methods 是一组改变 Resource 状态的方法，最常使用的是 HTTP 的 PUT/POST/GET/DELETE 四种方法。关于 Resource Methods 要强调等幂（idempotent）的概念，如果多次重复同一个请求的效果等于只执行一次请求，那么这个操作就是等幂的。根据这个定义 HTTP 的四种方法中只有 POST 是非等幂的。结合之前 Collection 和 Store 的区别，对于 Collection 类资源，我们应该使用 POST 创建新成员，而对于 Store 类资源我们应该使用 PUT。 

> In the context of REST APIs, when making multiple identical requests has the same effect as making a single request – then that REST API is called idempotent.

## 方法论

在给 WEB 应用设计 REST API 的时候可以遵循以下步骤：

* Identify Object Model.
* Create Model URIs.
* Determine Representations.
* Assign HTTP Methods.

## 结语

设计一组好的 REST API 是作为一个架构师/后端程序员的基本功。提升自己的 API 设计能力一方面要多参考好的 API 设计，比如 [GitHub API](https://developer.github.com/v3/)就是非常优秀的 REST API。另一方面，可以阅读别人在这方面的经验总结，**"Designing Web APIs Building APIs That Developers Love"** 就是一本很不错的参考书。当然，要真正掌握这门技能还是要多思考、多设计、多实践。

## 引用
[1] [What is REST](https://restfulapi.net/)

[2] [REST Resource Naming Guide](https://restfulapi.net/resource-naming/)

[3] [How to design a REST API](https://restfulapi.net/rest-api-design-tutorial-with-example/)

[4] [Idempotent REST APIs](https://restfulapi.net/idempotent-rest-apis/)

[5] [Rest Api设计的一些基本原则](https://lingenliu.com/2016/12/20/base-principle-about-rest-api/)
