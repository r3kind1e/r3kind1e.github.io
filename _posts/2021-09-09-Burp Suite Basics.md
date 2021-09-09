---
layout: post
title: "Burp Suite Basics"
date: "2021-09-09"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Penetration Testing Prerequisites
---
# Burp Suite Basics

## 任务1：运行Burp Suite并连接到目标Web应用程序

在Firefox中，将 Burp 设置为浏览器的代理。

![image-20210909212509568](/img/in-post/ine/image-20210909212509568.png)

也可以使用扩展FoxyProxy。

## 任务 2：对网站进行侦察活动

在 Firefox 上，您可以按`Ctrl + U`在新窗口中浏览页面的源代码。

![image-20210909212804018](/img/in-post/ine/image-20210909212804018.png)

### 什么是 robots.txt 文件

如果您不希望抓取工具访问您网站中的部分内容，可以创建包含相应规则的 robots.txt 文件。robots.txt 文件是一个简单的文本文件，其中包含关于哪些抓取工具可以访问网站的哪些部分的规则。

如果其他网站上有链接指向被 robots.txt 文件屏蔽的网页，则此网页仍可能会被编入索引。若要阻止你的网址出现在搜索结果中，你应该为[服务器上的文件设置设置密码保护](https://developers.google.com/search/docs/advanced/crawling/control-what-you-share)、[使用“noindex”阻止搜索引擎编入索引](https://developers.google.com/search/docs/advanced/crawling/block-indexing)。

和其他网址一样，robots.txt 文件的网址也区分大小写。

robots.txt 文件必须是采用 [UTF-8](http://en.wikipedia.org/wiki/UTF-8) 编码的纯文本文件，且各行代码必须以 `CR`、`CR/LF` 或 `LF` 分隔。可以使用Notepad、TextEdit、vi 和 emacs 可用来创建有效的 robots.txt 文件。请勿使用文字处理软件。

### 语法

`#`字符：注释，后面的所有内容都会被忽略。

`user-agent`：标识规则适用的抓取工具。

`allow`：可抓取的网址路径。

`disallow`：不可抓取的网址路径。

`allow` 和 `disallow` 字段也称为"指令"。默认情况下，指定的抓取工具没有抓取限制。

如果指定了 `[path]` 值，该路径值就是 robots.txt 文件所在网站的根目录的相对路径（使用相同的协议、端口号、主机和域名）。 路径值必须以 `/` 开头来表示根目录，该值区分大小写。

### 基于路径值的网址匹配

Google 会以 `allow` 和 `disallow` 指令中的路径值为基础，确定某项规则是否适用于网站上的特定网址。为此，系统会将相应规则与抓取工具尝试抓取的网址的路径部分进行比较。

对于路径值，Google、Bing 和其他主流搜索引擎支持有限形式的通配符。这些通配符包括：

- `*` 表示出现 0 次或多次的任何有效字符。
- `$` 表示网址结束。



| 路径匹配示例 |                                                              |
| ------------ | ------------------------------------------------------------ |
| /            | 匹配根目录以及任何下级网址。                                 |
| /*           | 等同于 `/`。结尾的通配符会被忽略。                           |
| /$           | 仅匹配根目录。任何更低级别的网址均可抓取。                   |
| /fish        | 匹配以 /fish 开头的任何路径。<br/><br/>匹配项：<br/>/fish<br/>/fish.html<br/>/fish/salmon.html<br/>/fishheads<br/>/fishheads/yummy.html<br/>/fish.php?id=anything<br/><br/>不匹配项：<br/>/Fish.asp<br/>/catfish<br/>/?id=fish<br/>/desert/fish<br/>注意：匹配时区分大小写。 |
| /fish*       | 等同于 /fish。结尾的通配符会被忽略。<br/><br/>匹配项：<br/>/fish<br/>/fish.html<br/>/fish/salmon.html<br/>/fishheads<br/>/fishheads/yummy.html<br/>/fish.php?id=anything<br/>不匹配项：<br/>/Fish.asp<br/>/catfish<br/>/?id=fish |
| /fish/       | 匹配 /fish/ 文件夹中的任何内容。<br/><br/>匹配项：<br/><br/>/fish/<br/>/animals/fish/<br/>/fish/?id=anything<br/>/fish/salmon.htm<br/>不匹配项：<br/><br/>/fish<br/>/fish.html<br/>/Fish/Salmon.asp |
| /*.php       | 匹配包含 .php 的任何路径。<br/><br/>匹配项：<br/><br/>/index.php<br/>/filename.php<br/>/folder/filename.php<br/>/folder/filename.php?parameters<br/>/folder/any.php.file.html<br/>/filename.php/<br/>不匹配项：<br/><br/>/（即使其映射到 /index.php）<br/>/windows.PHP |
| /*.php$      | 匹配以 .php 结尾的任何路径。<br/><br/>匹配项：<br/><br/>/filename.php<br/>/folder/filename.php<br/>不匹配项：<br/><br/>/filename.php?parameters<br/>/filename.php/<br/>/filename.php5<br/>/windows.PHP |
| /fish*.php   | 匹配包含 /fish 和 .php（按此顺序）的任何路径。<br/><br/>匹配项：<br/><br/>/fish.php<br/>/fishheads/catfish.php?parameters<br/>不匹配项： /Fish.PHP |

![image-20210909220346347](/img/in-post/ine/image-20210909220346347.png)

“Disallow”指令意味着网站的开发者不希望网络爬虫在搜索结果中包含应用程序的某些路径。

可以使用Burp Intruder检查*robots.txt*文件中的路径。

![image-20210909220816720](/img/in-post/ine/image-20210909220816720.png)

![image-20210909221231755](/img/in-post/ine/image-20210909221231755.png)

![image-20210909221322112](/img/in-post/ine/image-20210909221322112.png)

为了防止 Intruder 弄乱有效载荷，向下滚动到页面底部并**取消选中**“对这些字符进行 URL 编码”。

![image-20210909221421554](/img/in-post/ine/image-20210909221421554.png)

![image-20210909221531059](/img/in-post/ine/image-20210909221531059.png)

HTTP 代码“200”表示请求的资源存在。

![image-20210909223643147](/img/in-post/ine/image-20210909223643147.png)

找到了一个隐藏资源`http://172.16.160.102/connections/`

![image-20210909221635418](/img/in-post/ine/image-20210909221635418.png)

## 任务 4：使用 burp repeater 提取敏感信息

在*robots.txt*中，根据基于路径值的网址匹配

`/*?debug=*`匹配包含`?debug=`的任何路径

![image-20210909222257448](/img/in-post/ine/image-20210909222257448.png)

我的理解：`/*?debug=*`中的第一个`*`可以匹配：`connections/`，连接`?debug=`，第二个`*`匹配`TRUE`、`FALSE`等字符。

也就是说，这个规则可以匹配：`/connections/?debug=TRUE`，`/connections/?debug=FALSE`，`/connections/?debug=`。

![image-20210909222514467](/img/in-post/ine/image-20210909222514467.png)

应用程序期望debug参数的值设置为 TRUE 或 FALSE。

![image-20210909222533238](/img/in-post/ine/image-20210909222533238.png)

将debug参数的致设置为TRUE。

![image-20210909222605566](/img/in-post/ine/image-20210909222605566.png)

右键，Show response in browser

![image-20210909230149721](/img/in-post/ine/image-20210909230149721.png)

![image-20210909230239102](/img/in-post/ine/image-20210909230239102.png)

![image-20210909230343053](/img/in-post/ine/image-20210909230343053.png)



## 参考

[Google 如何解读 robots.txt 规范](https://developers.google.com/search/docs/advanced/robots/robots_txt#url-matching-based-on-path-values)

[robots.txt RFC 规范](http://www.robotstxt.org/norobots-rfc.txt)

