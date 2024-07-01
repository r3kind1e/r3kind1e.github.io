---
layout: post
title: "Client Fingerprinting"
date: "2024-06-23"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Professional
    - Resource Development & Initial Access
    - Client-Side Attacks
---

# Client Fingerprinting 客户端指纹识别
## Client Fingerprinting 客户端指纹识别
● Client fingerprinting is an active client information gathering technique used to gather information about a target system’s web browser and underlying operating system in order to aid in the development of tailor made (client specific) payloads for initial access.

● Client fingerprinting plays an important role in the success of a clientside attack as it provides the attacker with accurate information of the client-side software running on the target’s/employee’s computer.

● In the context of client-side information gathering, it allows us to identify key information about the client-side software running on the target(s) system. For example, browser and browser version, OS and system architecture etc.

● 客户端指纹识别是一种主动客户端信息收集技术，用于收集有关目标系统的网络浏览器和底层操作系统的信息，以帮助开发针对性强（客户特定的）有效载荷以实现初始访问。

● 客户端指纹识别在客户端攻击的成功中扮演了重要角色，因为它为攻击者提供了有关目标/员工计算机上运行的客户端软件的准确信息。

● 在客户端信息收集的背景下，它使我们能够识别目标系统上运行的客户端软件的关键信息。例如，浏览器及其版本、操作系统和系统架构等。

● Information about the target employee’s computer is typically not publicly accessible, and as a result, we must obtain this information from the target system itself.

● This will involve utilizing social engineering techniques like Phishing to coerce the target employee into clicking a link to a web page that we control.

● This web server will typically be configured to run a script that obtains information like the browser version and OS version from the browsers of users who visit the site.

● 目标员工的计算机信息通常不是公开可获取的，因此，我们必须从目标系统本身获取这些信息。

● 这将涉及使用社会工程技术，如钓鱼，强迫目标员工点击链接到我们控制的网页。

● 这个网服务器通常会配置运行一个脚本，该脚本能从访问该站点的用户的浏览器中获取浏览器版本和操作系统版本等信息。

## Browser Fingerprinting 浏览器指纹识别
● Browser fingerprinting is an active information gathering technique that leverages client-side scripting languages like JavaScript to extract information about the target’s browser and underlying operating system.

● In order to perform this technique, you will need to purchase a domain and set up a fictitious web page that runs a specific JavaScript script/code when users visit the webpage.

● This JavaScript code can be embedded into the homepage of the website and should log/send the browser fingerprint of users who visit the web page.

● 浏览器指纹识别是一种主动信息收集技术，它利用客户端脚本语言如JavaScript来提取有关目标浏览器和底层操作系统的信息。

● 为了执行这种技术，你需要购买一个域名并设置一个虚构的网页，当用户访问该网页时运行特定的JavaScript脚本/代码。

● 这段JavaScript代码可以嵌入到网站的首页，并应记录/发送访问该网页的用户的浏览器指纹。

● In order for this client-side information gathering technique to work, the target’s/employee’s browser must be able to run the typical client-side code used in modern web pages. For example, JavaScript.

● All modern web browsers support the execution of client-side JavaScript, however, some privacy-focused browsers have the ability to block the execution of JavaScript code unless specified otherwise.

● Our primary objective is to identify the following information:
+ Web Browser
+ Web Browser Version
+ Plugins/Extensions
+ Underlying OS information (OS, OS Version, System architecture etc).

● 为了使这种客户端信息收集技术有效，目标/员工的浏览器必须能够运行现代网页中使用的典型客户端代码。例如，JavaScript。

● 所有现代网络浏览器都支持执行客户端JavaScript，但是，一些注重隐私的浏览器有能力阻止执行JavaScript代码，除非另有指定。

● 我们的主要目标是识别以下信息：
+ 网络浏览器
+ 网络浏览器版本
+ 插件/扩展
+ 底层操作系统信息（操作系统，操作系统版本，系统架构等）。

## Browser Fingerprinting Tools/Libraries 浏览器指纹识别工具/库
● We can easily generate our browser fingerprinting webpage by leveraging existing JavaScript libraries like fingerprintjs2.

● fingerprintjs2 is a modern and flexible browser fingerprinting library that enumerates a lot of useful information about a browser and the underlying operating system.

● You can learn more about fingerprintjs2 here: https://github.com/LukasDrgon/fingerprintjs2

● 我们可以通过利用现有的JavaScript库，如fingerprintjs2，轻松生成我们的浏览器指纹识别网页。

● fingerprintjs2是一个现代且灵活的浏览器指纹识别库，它可以枚举有关浏览器及其底层操作系统的许多有用信息。

● 你可以在这里了解更多关于fingerprintjs2的信息：https://github.com/LukasDrgon/fingerprintjs2

# Demo: Browser Fingerprinting
[Fingerprintjs2](https://github.com/LukasDrgon/fingerprintjs2)

set up Apache

```
sudo apt-get install apache2
```

```
sudo systemctl start apache2
sudo systemctl status apache2
```

```
ifconfig
```

Kali Linux: 192.168.2.134

Check whether we have port 80 running.

```
netstat -antp
```

On Linux, the default directory is `/var/www/html`, this is where you store your website files especially with Apache.

```
cd /var/www/html
sudo git clone https://github.com/LukasDrgon/fingerprintjs2.git
```

```
127.0.0.1/fingerprintjs2
```

Set up a domain name that alludes to the interests of the target employee.

```
cd fingerprintjs2
sudo vim index.html
```

```html
<!doctype html>
<html>
<head>
  <title>Construction Tips</title>
  <meta name="viewport" content="width=device-width, initial-scale=1">
  <script>
    (function(i,s,o,g,r,a,m){i['GoogleAnalyticsObject']=r;i[r]=i[r]||function(){
    (i[r].q=i[r].q||[]).push(arguments)},i[r].l=1*new Date();a=s.createElement(o),
    m=s.getElementsByTagName(o)[0];a.async=1;a.src=g;m.parentNode.insertBefore(a,m)
    })(window,document,'script','//www.google-analytics.com/analytics.js','ga');

    ga('create', 'UA-42202458-2', 'auto');
    ga('send', 'pageview');
  </script>
  <script src="https://code.jquery.com/jquery-1.11.3.min.js"></script>
  <style>
    body{
      font-family: sans-serif;
      max-width: 48em;
      margin: auto;
      padding: 0 5%;
      background: #222;
      color: #fff;
    }

    h1 {
      margin: 2em 0 0;
    }

    p {
      font-size: 1.2em
    }

    button {
      border: none;
      color: #fff;
      font-size: 1.2em;
      background: #27e;
      padding: 0.5em 0.75em 0.6em;
      border-radius: 3px;
      box-shadow: 0 3px 0 #05c;
      outline: none;
    }

    button:active {
      transform: translateY(3px);
      box-shadow: none;
    }

    strong {
      display: block;
      letter-spacing: 1px;
      word-wrap: break-word;
    }

    @media (min-width: 32em) {
      h1 {
        font-size: 4em;
      }

      strong {
        font-size: 1.5em;
      }
    }
  </style>
</head>
<body>
  <div id="container"></div>


  <h1>Construction Tips</h1>

  <p>Click here to get the latest construction tips: <strong id="fp"></strong></p>
  <p><code id="time"/></p>
  <p><span id="details"/></p>
  <button type="button" id="btn">Get Construction Tips</button>

  <!-- <a href="https://github.com/Valve/fingerprintjs2"><img style="position: absolute; top: 0; right: 0; border: 0;" src="https://camo.githubusercontent.com/365986a132ccd6a44c23a9169022c0b5c890c387/68747470733a2f2f73332e616d617a6f6e6177732e636f6d2f6769746875622f726962626f6e732f666f726b6d655f72696768745f7265645f6161303030302e706e67" alt="Fork me on GitHub" data-canonical-src="https://s3.amazonaws.com/github/ribbons/forkme_right_red_aa0000.png"></a> -->

  <script src="fingerprint2.js"></script>
  <script>
    $("#btn").on("click", function () {
      var d1 = new Date();
      var fp = new Fingerprint2();
      fp.get(function(result, components) {
        var d2 = new Date();
        var timeString = "Time took to calculate the fingerprint: " + (d2 - d1) + "ms";
        var details = "Detailed information: \n";
        if(typeof window.console !== "undefined") {
          console.log(timeString);
          console.log(result);
          for (var index in components) {
            var obj = components[index];
            var value = obj.value;
            var line = obj.key + " = " + value.toString().substr(0, 100);
            console.log(line);
            details += line + "\n";
          }
        }
        // $("#details").html(details);
        $("#fp").text(result);
        $("#time").text(timeString);

        // Save the web browser fingerprint into a text file.

        var xmlhttp = new XMLHttpRequest();
        xmlhttp.open("POST", "fp.php"); // The fingerprint.php (fp.php) is what's actually going to save the fingerprint.
        xmlhttp.setRequestHeader("Content-Type", "application/txt");
        xmlhttp.send(details + result + timeString); // All the data that we're getting from the browser, we want to send it.
      });
    });
  </script>
</body>
</html>
```

We need to create the fingerprint (fp.php) script that'll save the results, or we will receive what's being sent by that JavaScript script or code, which is the browser information. We'll then save it into a text file.

```
sudo vim fp.php
```

```php
<?php
$data = "Client IP Address: " . $_SERVER['REMOTE_ADDR'] . "\n"; // Save the client IP Address. And then it's going to get the IP from the server header, that's the remote address.
$data .= file_get_contents('php://input'); // It's going to get the contents that are being sent by that JavaScript code running on the index.html
$data .= "----------------------------------\n\n";
file_put_contents('/var/www/html/fingerprintjs2/fingerprint.txt', print_r($data, true), FILE_APPEND | LOCK_EX); // Save the data into a file that's stored under the same directory. print out the data and append to it. When a new browser access the web page, it will add in the data as opposed to overwriting it.
?>
```

这段代码涉及两个主要部分：一个HTML文档和一个PHP脚本。以下是各部分的详细解释：

### HTML文档 (`index.html`)

1. **HTML结构和样式**：
   - 文档定义了一个基本的HTML页面，包括头部和身体。页面标题为“Construction Tips”。
   - 页面包含一些基本的CSS样式，用于设置文本、按钮和响应式设计。

2. **JavaScript和Google Analytics**：
   - 页面嵌入了Google Analytics脚本，用于追踪页面访问。
   - 引入了jQuery库和自定义的JavaScript代码来处理用户交互。

3. **功能性JavaScript**：
   - 使用`fingerprint2.js`库（一种流行的浏览器指纹识别库），在用户点击按钮时收集浏览器指纹信息。
   - 收集的信息包括浏览器的细节，如操作系统、浏览器版本等，然后显示在页面上，并通过AJAX请求发送到`fp.php`。

4. **用户交互**：
   - 提供一个按钮，用户点击后将触发浏览器指纹的收集和展示。
   - 使用AJAX将收集到的数据发送到服务器端的PHP脚本。

### PHP脚本 (`fp.php`)

1. **数据处理**：
   - PHP脚本接收从前端发送的浏览器指纹信息。
   - 它首先记录访问者的IP地址，然后从`php://input`流中读取通过AJAX传送的数据。

2. **文件写入**：
   - 所有收集的数据将附加到服务器上的文本文件中，不会覆盖之前的数据，因为使用了`FILE_APPEND`标志。
   - 使用`LOCK_EX`标志确保在写入文件时，文件被锁定，防止同时写入时的数据竞争。

总的来说，这个组合的代码示例展示了如何在客户端使用JavaScript进行浏览器指纹识别，并将收集的数据发送到服务器，服务器端的PHP脚本则负责将这些数据安全地存储起来。这种方法常用于网站分析、安全检测和增强个性化用户体验。

The provided code involves two main components: an HTML document and a PHP script. Here's a detailed explanation of each part:

### HTML Document (`index.html`)

1. **HTML Structure and Styling**:
   - The document defines a basic HTML page structure including the head and body sections. The page title is "Construction Tips".
   - The page includes basic CSS styles for text, buttons, and responsive design elements.

2. **JavaScript and Google Analytics**:
   - The page embeds a Google Analytics script for tracking page visits.
   - It incorporates the jQuery library and custom JavaScript code to handle user interactions.

3. **Functional JavaScript**:
   - Utilizes the `fingerprint2.js` library (a popular browser fingerprinting library) to collect browser fingerprint information when the user clicks a button.
   - The collected information includes details about the browser, such as operating system, browser version, etc., which are then displayed on the page and sent to the `fp.php` server script via an AJAX request.

4. **User Interaction**:
   - Provides a button that, when clicked, triggers the collection and display of the browser fingerprint.
   - Uses AJAX to send the collected data to a server-side PHP script.

### PHP Script (`fp.php`)

1. **Data Handling**:
   - The PHP script receives browser fingerprint information sent from the frontend.
   - It first logs the visitor's IP address, then reads the data sent via AJAX from the `php://input` stream.

2. **File Writing**:
   - All collected data are appended to a text file on the server, avoiding overwriting previous data thanks to the `FILE_APPEND` flag.
   - Uses the `LOCK_EX` flag to lock the file during writing, preventing data race conditions during concurrent writes.

Overall, this combined code example demonstrates how to use JavaScript on the client-side to perform browser fingerprinting, and how the collected data is sent to the server where a PHP script is responsible for securely storing this data. This approach is commonly used in website analytics, security assessments, and enhancing personalized user experiences.

```
ls -alps
```

命令 `ls -alps` 是在类 Unix 操作系统中与 `ls` 命令一起使用的选项组合，用于列出目录内容。以下是命令中每个选项的含义：

1. **`-a`（全部）**：此选项指示 `ls` 显示所有文件，包括以点（`.`）开头的隐藏文件。通常，`ls` 命令不会显示隐藏文件。

2. **`-l`（长列表格式）**：此选项将输出格式更改为详细列表，其中包括文件权限、链接数、所有者名、所有者组、文件大小、时间戳和文件名。这种格式提供了有关每个文件或目录的全面详细信息。

3. **`-p`**：此选项在目录名称后添加斜杠（`/`），使得在输出中更容易区分目录和文件。

4. **`-s`（大小）**：此选项显示每个文件的大小（以块为单位），与其他信息一起展示。块是类 Unix 系统中磁盘空间分配的单位。

结合这些选项，`ls -alps` 命令提供了一个详细列表，包括所有文件（包括隐藏文件），用斜杠区分目录，以长列表格式显示，并展示文件的大小（以块为单位）。这对于深入查看目录内容特别有用，尤其是在管理文件权限和磁盘空间使用时。

The command `ls -alps` is a combination of options used with the `ls` command in Unix-like operating systems, which is used to list directory contents. Here’s what each option in the command stands for:

1. **`-a` (all)**: This option tells `ls` to display all files, including those that start with a dot (`.`), which are hidden files in Unix-like systems. Normally, `ls` does not show hidden files.

2. **`-l` (long listing format)**: This option changes the output format to a detailed listing which includes file permissions, number of links, owner name, owner group, file size, timestamp, and the filename. This format provides comprehensive details about each file or directory.

3. **`-p`**: This option appends a slash (`/`) to the end of directory names, making it easier to distinguish directories from files in the output.

4. **`-s` (size)**: This option displays the size of each file in blocks alongside the other information. A block is a unit of disk space allocation in Unix-like systems.

Combining these options, the `ls -alps` command provides a detailed list of all files (including hidden ones), distinguishing directories with a slash, showing the size in blocks, and presenting this in a long listing format. This can be particularly useful for an in-depth view of directory contents, especially when managing file permissions and disk space usage.

Provide ownership to the www-data service account and group to /var/www/html/fingerprintjs2

```
sudo chown www-data:www-data /var/www/html/fingerprintjs2
```

命令 `sudo chown www-data:www-data /var/www/html/fingerprintjs2` 在 Unix-like 系统中用于更改文件或目录的所有权。下面是这条命令每部分的具体解释：

1. **`sudo`**：这是一个命令行工具，允许普通用户以超级用户（通常是 root 用户）的权限执行命令。这在需要更改系统文件或执行管理任务时非常有用。

2. **`chown`**：这是 "change owner"（更改所有者）的缩写，用于更改文件或目录的所有者和/或所属组。

3. **`www-data:www-data`**：这里指定了新的所有者和所属组。在这个例子中，`www-data` 是所有者用户名，冒号后的 `www-data` 是组名。这通常是 web 服务器（如 Apache 或 Nginx）运行的用户和组，确保了服务器对这些文件和目录有适当的访问权限。

4. **`/var/www/html/fingerprintjs2`**：这是要更改所有权的目标目录的路径。此路径通常用于存放网站内容，`fingerprintjs2` 可能是存放特定项目或库的目录。

综上所述，这条命令的作用是将 `/var/www/html/fingerprintjs2` 目录的所有权更改为用户 `www-data` 和组 `www-data`。这样做是为了确保 web 服务器能够正确地访问和管理这个目录及其内容，特别是在涉及动态内容或需要服务器写入文件的 web 应用中。这是网站部署和管理中的常见做法。

The command `sudo chown www-data:www-data /var/www/html/fingerprintjs2` is used in Unix-like systems to change the ownership of files or directories. Here's a detailed explanation of each component of this command:

1. **`sudo`**: This is a command-line utility that allows a regular user to execute commands with the privileges of the superuser (usually the root user). This is very useful when needing to modify system files or perform administrative tasks.

2. **`chown`**: This stands for "change owner" and is used to change the owner and/or group of a file or directory.

3. **`www-data:www-data`**: This specifies the new owner and the group for the file or directory. In this example, `www-data` is the username of the owner, and the `www-data` after the colon represents the group name. This is typically the user and group under which a web server (like Apache or Nginx) runs, ensuring that the server has appropriate access to these files and directories.

4. **`/var/www/html/fingerprintjs2`**: This is the path to the directory whose ownership is being changed. This path is commonly used to store web content, and `fingerprintjs2` might be a directory containing specific projects or libraries.

In summary, this command changes the ownership of the directory `/var/www/html/fingerprintjs2` to the user `www-data` and the group `www-data`. This is done to ensure that the web server can properly access and manage this directory and its contents, especially in web applications involving dynamic content or where server write permissions are necessary. This is a common practice in website deployment and management.

```
sudo systemctl restart apache2
```

刷新页面，点击按钮。

```
ls -al
cat fingerprint.txt
```

[Parse a User Agent String](https://explore.whatismybrowser.com/useragents/parse/)