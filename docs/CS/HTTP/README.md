# HTTP 协议

HTTP 协议是个无状态协议，不会保存状态。

## HTTP 有哪些方法？

- HTTP1.0 定义了三种请求方法： GET, POST 和 HEAD 方法
- HTTP1.1 新增了五种请求方法：OPTIONS, PUT, DELETE, TRACE 和 CONNECT

## 这些方法的具体作用是什么？

- GET: 通常用于请求服务器发送某些资源
- HEAD: 请求资源的头部信息，并且这些头部与 HTTP GET 方法请求时返回的一致。该请求方法的一个使用场景是在下载一个大文件前先获取其大小再决定是否要下载，以此可以节约带宽资源
- OPTIONS: 用于获取目的资源所支持的通信选项
- POST: 发送数据给服务器
- PUT: 用于新增资源或者使用请求中的有效负载替换目标资源的表现形式
- DELETE: 用于删除指定的资源
- PATCH: 用于对资源进行部分修改
- CONNECT: HTTP/1.1 协议中预留给能够将连接改为管道方式的代理服务器
- TRACE: 回显服务器收到的请求，主要用于测试或诊断

## GET 和 POST 有什么区别？🌟

- **数据传输方式不同：** GET 请求通过 URL 传输数据，而 POST 的数据通过请求体传输。
> URL 有长度限制，会影响 Get 请求，但是这个长度限制是浏览器规定的，不是 RFC 规定的。Post 可以通过 request body 来传输比 Get 更多的数据。
- **安全性不同：** POST 的数据因为在请求主体内，所以有一定的安全性保证，而 GET 的数据在 URL 中，通过历史记录，缓存很容易查到数据信息。
> Post 相对 Get 安全一点点 虽然 Get 请求能被缓存，Post 不能。但是在抓包的情况下都是一样的。
- **数据类型不同：** GET 只允许 ASCII 字符，而 POST 对数据类型不做限制，所以支持更多的编码类型。
- **GET 无害：** 刷新、后退等浏览器操作 GET 请求是无害的，POST 可能重复提交表单
- **特性不同：** GET 是安全（这里的安全是指只读特性，就是使用这个方法不会引起服务器状态变化）且幂等（幂等的概念是指同一个请求方法执行多次和仅执行一次的效果完全相同），而 POST 是非安全非幂等

### 副作用和幂等的概念：

- 副作用指对服务器上的资源做改变，搜索是无副作用的，注册是有副作用的。

- 幂等指发送 M 和 N 次请求（两者不相同且都大于 1），服务器上资源的状态一致，比如注册 10 个和 11 个帐号是不幂等的，对文章进行更改 10 次和 11 次是幂等的。

> 在规范的应用场景上说，Get 多用于无副作用，幂等的场景，例如搜索关键字。
> 
> Post 多用于副作用，不幂等的场景，例如注册。

**PUT 和 POST 都是给服务器发送新增资源，有什么区别？**

PUT 和 POST 方法的区别是，PUT 方法是幂等的：连续调用一次或者多次的效果相同（无副作用），而 POST 方法是非幂等的。

除此之外还有一个区别，通常情况下，PUT 的 URI 指向是具体单一资源，而 POST 可以指向资源集合。

举个🌰，我们在开发一个博客系统，当我们要创建一篇文章的时候往往用 POST 请求`https://www.juejin.com/articles`，这个请求的语义是，在 articles 的资源集合下创建一篇新的文章，如果我们多次提交这个请求会创建多个文章，这是非幂等的。

而 PUT 请求`https://www.juejin.com/articles/123`的语义是更新对应文章的内容（比如修改作者名称等），这个 URI 指向的就是单一资源，而且是幂等的，比如你把『XXX』修改成『蔡徐坤』，提交多少次都是修改成『蔡徐坤』

> ps: 『POST 表示创建资源，PUT 表示更新资源』这种说法是错误的，这两个方法都能创建资源，根本区别在于幂等性

**PUT 和 PATCH 都是给服务器发送修改资源，有什么区别？**

PUT 和 PATCH 都是更新资源，而 PATCH 用来对已知资源进行局部更新。

## HTTP 的部首有哪些？

> 用 typroa 编辑更新

## http 的请求报文是什么样的？

请求报文有 4 部分组成：

请求行
请求头部
空行
请求体

## 常见状态码

**2XX 成功**

- 200 OK，表示从客户端发来的请求在服务器端被正确处理 ✨
- 201 Created 请求已经被实现，而且有一个新的资源已经依据请求的需要而建立
- 202 Accepted 请求已接受，但是还没执行，不保证完成请求
- 204 No content，表示请求成功，但响应报文不含实体的主体部分
- 205 Reset Content，表示请求成功，但响应报文不含实体的主体部分，但是与 204 响应不同在于要求请求方重置内容
- 206 Partial Content，进行范围请求 ✨

**3XX 重定向**

- 301 moved permanently，永久性重定向，表示资源已被分配了新的 URL
- 302 found，临时性重定向，表示资源临时被分配了新的 URL ✨
- 303 see other，表示资源存在着另一个 URL，应使用 GET 方法获取资源
- 304 not modified，表示服务器允许访问资源，但因发生请求未满足条件的情况
- 307 temporary redirect，临时重定向，和 302 含义类似，但是期望客户端保持请求方法不变向新的地址发出请求

> **同样是重定向 302，303，307 的区别？**
> 
> 302 是 http1.0 的协议状态码，在 http1.1 版本的时候为了细化 302 状态码又出来了两个 303 和 307。
>
> 303 明确表示客户端应当采用 get 方法获取资源，他会把 POST 请求变为 GET 请求进行重定向。 307 会遵照浏览器标准，不会从 post 变为 get。

**4XX 客户端错误**

- 400 bad request，请求报文存在语法错误 ✨
- 401 unauthorized，表示发送的请求需要有通过 HTTP 认证的认证信息 ✨
- 403 forbidden，表示对请求资源的访问被服务器拒绝 ✨
- 404 not found，表示在服务器上没有找到请求的资源 ✨
- 408 Request timeout, 客户端请求超时
- 409 Confict, 请求的资源可能引起冲突

**5XX 服务器错误**

- 500 internal sever error，表示服务器端在执行请求时发生了错误 ✨
- 501 Not Implemented 请求超出服务器能力范围，例如服务器不支持当前请求所需要的某个功能，或者请求是服务器不支持的某个方法
- 503 service unavailable，表明服务器暂时处于超负载或正在停机维护，无法处理请求
- 505 http version not supported 服务器不支持，或者拒绝支持在请求中使用的 HTTP 版本

> [常见 HTTP 状态码含义](https://sunburst.wang/posts/2018/02/12/common-http-status-code.html)

## keep-alive 是干什么的？

在早期的 HTTP/1.0 中，每次 http 请求都要创建一个连接，而创建连接的过程需要消耗资源和时间，为了减少资源消耗，缩短响应时间，就需要重用连接。在后来的 HTTP/1.0 中以及 HTTP/1.1 中，引入了重用连接的机制，如果想要保持长连接，就需要在 http 请求头中加入`Connection: keep-alive`来告诉对方这个请求响应完成后不要关闭，下一次咱们还用这个请求继续交流。

**keep-alive 的优点：**

- 较少的 CPU 和内存的使用（由于同时打开的连接的减少了）
- 允许请求和应答的 HTTP 管线化
- 降低拥塞控制 （TCP 连接减少了）
- 减少了后续请求的延迟（无需再进行握手）
- 报告错误无需关闭 TCP 连接

## HTTP 的缓存的过程是怎样的？🌟

> todo 
