---
layout: post
title: "HTTP Cookies"
date: "2021-09-05"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Penetration Testing Prerequisites
---

# HTTP Cookies

HTTP 是一种无状态协议； 这意味着网站无法跨不同的 HTTP 请求保持访问状态。

换句话说，每个 HTTP 请求都与其前后的请求完全无关。

为了克服这个限制，1994 年发明了会话和 cookie。

当时的领先公司 Netscape 发明了 cookie 来使 HTTP 有状态。

**这如何支持我的渗透测试生涯？** 

* Cookie 是许多应用程序授权的基础

* 漏洞利用通常依赖于窃取 cookie 

Cookies只是网站安装到网络浏览器的“cookie jar”中的文本信息。

cookie jar 是 Web 浏览器存储 cookie 的存储空间。



## Cookies Format（Cookies格式）

服务器可以通过响应消息中的 Set-Cookie HTTP 标头字段设置 cookie。

cookie 包含以下属性：

* 实际内容

* 失效日期

*  路径

* 域

* 可选标志
  *  Http only flag
  * Secure flag

```http
HTTP/1.1 200 OK
Date: Wed, 19 Nov 2014 10:06:45 GMT
Cache-Control: private, max-age=0
Content-Type: text/html; charset=UTF-8
Content-Encoding: gzip
Server: Apache/2.2.15 (CentOS)
Set-Cookie: ID=Value; expires=Thu, 21-May-2015 15:25:20 GMT; path=/; domain=.example.site; HttpOnly
Content-Length: 99043
```

Cookie内容: `ID=Value;`

失效日期: `expires=Thu, 21-May-2015 15:25:20 GMT;`

路径: `path=/example/path;`

域: `domain=.example.site;`

标志设置属性 : `HttpOnly; Secure`



## Cookies Handling（Cookie 处理）

浏览器使用 domain、path、expires 和 flags 属性来选择是否在请求中发送 cookie。

Cookie 仅在未过期时并根据其标志发送到有效`domain/path`。



## Cookie Domain（Cookie 域）

domain 字段和 path 字段设置了 cookie 的范围。 只有当请求是针对正确的域时，浏览器才会发送 cookie。

当 Web 服务器安装 cookie 时，它会设置域字段，例如 `elearnsecurity.com`。 然后，浏览器将对发送到该域及**其所有子域**的每个请求使用 cookie。

例子：

当 cookie 的域属性设置为：

- **domain**=`elearnsecurity.com`

or

- **domain**=`.elarnsecurity.com`

浏览器会将 cookie 发送到：

- `www.elearnsecurity.com`
- `whatever.subdomain.elearnsecurity.com`
- `elearnsecurity.com`



如果服务器没有指定域属性，浏览器会自动将域设置为服务器域并设置cookie host-only标志； 这意味着 cookie 将只发送到那个精确的主机名。

在以下示例中，您将看到浏览器如何选择发送或不发送 cookie。



example.com 将 cookie 的内容设置为“a=mycookie”。

该 cookie 对 subdomain.example.com 等子域也有效。 

![image-20210905190510804](/img/in-post/ine/image-20210905190510804.png)

sub2.example.com 不是 cookie 域 sub.example.com 的有效子域

![image-20210905190557540](/img/in-post/ine/image-20210905190557540.png)



如果 cookie 域字段为空，则客户端设置仅主机标志并将域设置为主机名。 cookie 将仅发送到该特定主机。

![image-20210905190628624](/img/in-post/ine/image-20210905190628624.png)

## Cookie Path（Cookie 路径）

正如我们之前看到的，**路径**和**域**属性设置了 cookie 的范围。

浏览器会将 cookie 发送到**正确的域和路径字段值的任何子路径**。

例子

当 cookie 的 path 属性设置为：

* **path**=`/the/path`

浏览器会将 cookie 发送到正确的域和以下资源：

* `/the/path`

* `/the/path/sub`

* `/the/path/sub/sub/sub/path`

但它不会将它发送到 `/otherpath` 



## Cookie Expires Attribute（Cookie 过期属性）

**expires** 属性设置 cookie 的**有效时间窗口**。

浏览器不会向服务器发送过期的 cookie。 会话 cookie 随着 HTTP 会话过期； 您将在本模块的后面看到更多相关信息。 



## Cookie Http-Only Attribute（Cookie Http-Only 属性）

当服务器将具有`http-only`属性的cookie 安装到客户端时，客户端将为该cookie设置`http-only`标志。 这种机制可以防止 JavaScript、Flash、Java 和任何其他非 HTML 技术读取 cookie，从而防止通过XSS窃取 cookie。





## Cookie Secure Attribute（Cookie 安全属性）

Secure标志创建仅通过 HTTPS 连接发送的安全 cookie（它们不会通过 HTTP 发送）。



## Cookie Content（Cookie内容）

cookie 可以携带多个值。 通过指定多个 KEY=Value 对，服务器可以使用单个 Set-Cookie 标头设置多个值。

**Set-Cookie:** Username="john"; auth=1

设置两个值：一个用于用户名，一个用于身份验证。



## Cookie Protocol（Cookie协议）

[RFC6265](https://datatracker.ietf.org/doc/html/rfc6265)规定了 cookie 格式、服务器如何安装 cookie 以及客户端如何使用它们。

让我们用一个简单的例子来看看 cookie 的作用。



Cookie 通常在登录期间安装。

在这个例子中，浏览器发送一个带有用户名和密码的 POST 请求。

![image-20210905190657675](/img/in-post/ine/image-20210905190657675.png)

服务器发送带有 Set-Cookie 标头字段的响应，从而告诉浏览器安装 cookie。 

![image-20210905190720308](/img/in-post/ine/image-20210905190720308.png)

对于每个后续请求，浏览器会考虑：

* 域

* 路径

* 到期

- 标志

![image-20210905190740927](/img/in-post/ine/image-20210905190740927.png)

如果所有检查都通过，浏览器将在请求中插入一个 **cookie:** 头部。

![image-20210905190802090](/img/in-post/ine/image-20210905190802090.png)



## 参考

[RFC6265](https://datatracker.ietf.org/doc/html/rfc6265)

[RFC 6265 要点翻译](https://github.com/renaesop/blog/issues/4)