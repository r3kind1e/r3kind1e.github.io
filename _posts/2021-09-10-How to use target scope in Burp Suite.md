---
layout: post
title: "How to use target scope in Burp Suite"
date: "2021-09-10"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - 安全评估工具
---

# How to use target scope in Burp Suite



## 参考

[Target scope](https://portswigger.net/burp/documentation/desktop/tools/target/scope)

[How to use target scope in Burp Suite](https://www.youtube.com/watch?v=0mTg2BsYVmg&t=339s)



## target scope（目标范围）

target scope（目标范围）配置让您可以在套件范围内告诉 Burp，哪些主机和 URL 构成了您当前工作的目标。您可以将目标范围大致视为您当前感兴趣并愿意攻击的项目。

此配置会影响整个套件中工具的行为。例如：

- 您可以在[目标站点地图](https://portswigger.net/burp/documentation/desktop/tools/target/site-map#site-map-display-filter)和[代理历史记录](https://portswigger.net/burp/documentation/desktop/tools/proxy/history#proxy-history-display-filter)上设置显示过滤器以仅显示范围内的项目。
- 您可以告诉代理仅[拦截](https://portswigger.net/burp/documentation/desktop/tools/proxy/options#intercepting-http-requests-and-responses)范围内的请求和响应。
- Professional 使用[Burp Scanner](https://portswigger.net/burp/vulnerability-scanner)，您可以对范围内的项目执行[自动实时扫描](https://portswigger.net/burp/documentation/desktop/scanning/live-scans)。
- 您可以将[Intruder](https://portswigger.net/burp/documentation/desktop/tools/intruder/options#handling-redirections-during-attacks)和[Repeater](https://portswigger.net/burp/documentation/desktop/tools/repeater/options)配置为跟随重定向到任何范围内的 URL。

通过告诉 Burp 您当前的目标是什么，您可以确保 Burp 以适当的方式执行大量此类操作，仅针对您感兴趣并愿意攻击的项目。在所有情况下，您还可以在单个工具级别对目标范围和相关行为进行微调，如果需要，您可以对 Burp 所做的一切进行细粒度控制。然而，套件范围的范围定义提供了一种快速简便的方法来告诉 Burp 什么是公平的游戏，什么是禁止的，并且几乎总是值得在您认真开始工作之前进行配置。

范围定义使用两个 URL 匹配规则列表 - “包含”列表和“排除”列表。当 Burp 评估一个 URL 以确定它是否在目标范围内时，如果该 URL 匹配至少一个“包含”规则并且不匹配任何“排除”规则，则它将被视为在范围内。这使您能够将特定的主机和目录定义为通常在范围内，但从该范围内排除特定的子目录或文件（例如注销或管理功能）。

您可以使用[URL 匹配规则编辑器](https://portswigger.net/burp/documentation/desktop/functions/url-matching)在“包含”和“排除”列表中添加或编辑规则。但是，在大多数情况下，到目前为止，定义目标范围的最简单方法是通过[站点地图](https://portswigger.net/burp/documentation/desktop/tools/target/site-map)。当您通过 Burp Proxy 绘制目标应用程序时，应用程序的内容将出现在站点地图中。然后，您可以选择一个或多个主机和文件夹，并使用[上下文菜单](https://portswigger.net/burp/documentation/desktop/tools/target/site-map#site-map-testing-workflow)在范围中包括或排除这些。这个过程非常简单，在大多数情况下，您可以快速定义测试所需的所有规则。



## 如何在 Burp Suite 中使用目标范围视频教程

In this tutorial we explain how to configure target scope in Burp Suite, and describe all of the different purposes that target scope can be used for.

we use target scope to tell Burp what your currently testing, Burp can then make use of this in various places to control what happens and help to improve your efficiency.



`Target->Scope`

scope is based on URL s.

![image-20210910185258668](/img/in-post/ine/image-20210910185258668.png)

A URL will be in scope if it match something in the include list, and doesn't match anything in the exclude list.

so come out with to configure scope, you can include

a single domain

![image-20210910184333612](/img/in-post/ine/image-20210910184333612.png)

or a folder or path within that domain

![image-20210910184451509](/img/in-post/ine/image-20210910184451509.png)

you can configure multiple domains and paths together.

![image-20210910184631481](/img/in-post/ine/image-20210910184631481.png)

you can use exclude rules for various purposes, such as excluding boring content from scope,  or excluding functionality that is out of scope for the test you're doing, or exclude  fragile(脆弱的) functionality might break if you test it, or functionality is liable to break other activity such as cleaning a session, deleting your account or things like that.

![image-20210910184902429](/img/in-post/ine/image-20210910184902429.png)

by default, target scope works off simple URL prefixes, this is suitable for most purposes.

if you enable advanced scope control, you can then define scope roles based on each parts of the URL individually. This is useful for more complex cases, for example, if you want to exclude URL s in different folders, while the filename match the certain pattern.

![image-20210910190056537](/img/in-post/ine/image-20210910190056537.png)

In general, the easiest way to define scope is to browse target application with Burp's browser, and use the context menu on the Site map three or table to define the scope.

you can use context menu, select an item and say`Add to scope`.

![image-20210910191039313](/img/in-post/ine/image-20210910191039313.png)

and then you can select items beneath that use context menu and say `Remove from scope`.

![image-20210910191106786](/img/in-post/ine/image-20210910191106786.png)

if you do this, the scope configuration will be updated to reflect the items you selected.

![image-20210910191136037](/img/in-post/ine/image-20210910191136037.png)

so what can we use target scope for?

the first thing that happens is that when you do add an item to scope, that will prompt you to ask whether the proxy should stop sending out-of-scope items to the history or other Burp tools? This can help keep you data clean, reduce size of project files and reduce memory usage.

![image-20210910192206026](/img/in-post/ine/image-20210910192206026.png)

![image-20210910192232251](/img/in-post/ine/image-20210910192232251.png)

In the Site map, there is a few filter, so you can select to only show in-scope items, 

![image-20210910192341210](/img/in-post/ine/image-20210910192341210.png)

everything else get hidden from the view.

![image-20210910192526604](/img/in-post/ine/image-20210910192526604.png)

you can do the same in`Proxy->HTTP history`

![image-20210910192634497](/img/in-post/ine/image-20210910192634497.png)

![image-20210910192700808](/img/in-post/ine/image-20210910192700808.png)

 `Proxy->Options` interception rules can be based on target scope. you can define rules which only intercept items if they're in target scope.

![image-20210910193008133](/img/in-post/ine/image-20210910193008133.png)

if you configured a target scope, and you go to perform a scan,  by automatically pre-configure URL to scan based on your scope.

![image-20210910193321414](/img/in-post/ine/image-20210910193321414.png)

![image-20210910193602955](/img/in-post/ine/image-20210910193602955.png)

and in your crawl options you can configure whether out of scope resources should be fetched by the embedded browser during the scan.

![image-20210910193919280](/img/in-post/ine/image-20210910193919280.png)

if you create a live task, you configure the scope of the task based on the target Suite scope.

![image-20210910194032878](/img/in-post/ine/image-20210910194032878.png)

if you use Burp's search function, you can configure whether only In-scope items should be included in the search.

 `Burp->Search`

![image-20210910194302582](/img/in-post/ine/image-20210910194302582.png)

`Project->Save copy`

when you save the copy of your project, you can also configure whether to only include in-scope items.

![image-20210910194630964](/img/in-post/ine/image-20210910194630964.png)

`Intruder`

in Burp Intruder, you can configure whether intruder should follow redirections and the urls that should be included in the redirection so you can configure whether to base redirections on what is in target scope.

![image-20210910195049089](/img/in-post/ine/image-20210910195049089.png)

and you can do the same thing in Burp `Repeater` as well.

`Repeater->Follow redirections->In-scope only`

![image-20210910202358675](/img/in-post/ine/image-20210910202358675.png)

if you use burp session handling rules, you session handling rule can be configured based on your target scope.

`Project options->Sessions`

![image-20210910195452945](/img/in-post/ine/image-20210910195452945.png)

`Projecti options->Connections`

you can configure whether all out-of-scope requests should be drooped regardless of where they're coming from within Burp.

![image-20210910195723282](/img/in-post/ine/image-20210910195723282.png)