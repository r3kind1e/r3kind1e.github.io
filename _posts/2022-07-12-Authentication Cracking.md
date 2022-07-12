---
layout: post
title: "Authentication Cracking"
date: "2022-07-12"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# Authentication Cracking（认证破解）
这如何支持我的渗透测试生涯？
有能力：
* 通过网络服务访问密码策略
* 访问网络服务
* 访问 Web 应用程序

在系统模块中，您了解了如何破解密码文件以获取系统的有效登录凭据。 每个需要网络身份验证的服务都可以使用类似的方法，例如：
* SSH
* 远程登录（Telnet）
* 远程桌面（Remote Desktop）
* HTTP 身份验证（HTTP authentication）
* 和更多

## Brute Force vs. Dictionary Attacks（暴力破解与字典攻击）
当渗透测试人员需要访问网络服务时，他们可以尝试通过**暴力破解**或**字典攻击**来获取有效凭据。
由于运行每个探测所需的时间，在网络上执行纯暴力攻击是非常不切实际的。

在离线暴力破解中（例如 John the Ripper），测试单个密码所需的时间由处理时间给出； 在网络身份验证攻击期间，测试密码所需的时间取决于许多其他因素。

这些因素包括：
* **网络延迟：**换句话说，将信息从渗透测试人员的机器传输到目标服务器所需的时间，反之亦然。
* **受攻击服务的延迟：**许多服务在身份验证例程中等待几秒钟，从而使身份验证攻击变得更慢。
* **受攻击服务器上的处理时间：**与离线攻击一样，目标服务器必须加密并检查凭据。

## Weak and Default Credentials（弱凭据和默认凭据）
由于这些原因，网络身份验证破解几乎完全依赖于基于字典的攻击。
该领域的大多数工具都使用**常用和默认用户名和密码的字典**。

如果用户选择弱密码或管理员未修改默认服务凭据，字典攻击将绕过密码保护。
您可以在[此处](https://wiki.skullsecurity.org/index.php/Passwords)找到全面的密码集合。

### Installing Dictionaries
你也可以通过安装 seclists 包在 Kali 上安装一些常用的密码列表。

```
apt install seclists
ls /usr/share/seclists/Passwords/
2020-200_most_used_passwords.txt  darkweb2017-top1000.txt    Keyboard-Combinations.txt               probable-v2-top207.txt         UserPassCombo-Jay.txt
500-worst-passwords.txt.bz2       darkweb2017-top100.txt     Leaked-Databases                        README.md                      WiFi-WPA
BiblePass                         darkweb2017-top10.txt      Malware                                 richelieu-french-top20000.txt  xato-net-10-million-passwords-1000000.txt
bt4-password.txt                  days.txt                   months.txt                              richelieu-french-top5000.txt   xato-net-10-million-passwords-100000.txt
cirt-default-passwords.txt        Default-Credentials        Most-Popular-Letter-Passes.txt          SCRABBLE-hackerhouse.tgz       xato-net-10-million-passwords-10000.txt
citrix.txt                        der-postillon.txt          mssql-passwords-nansh0u-guardicore.txt  seasons.txt                    xato-net-10-million-passwords-1000.txt
clarkson-university-82.txt        dutch_common_wordlist.txt  openwall.net-all.txt                    Software                       xato-net-10-million-passwords-100.txt
Common-Credentials                dutch_passwordlist.txt     Permutations                            stupid-ones-in-production.txt  xato-net-10-million-passwords-10.txt
Cracked-Hashes                    dutch_wordlist             PHP-Magic-Hashes.txt                    twitter-banned.txt             xato-net-10-million-passwords-dup.txt
darkc0de.txt                      german_misc.txt            probable-v2-top12000.txt                unkown-azul.txt                xato-net-10-million-passwords.txt
darkweb2017-top10000.txt          Honeypot-Captures          probable-v2-top1575.txt                 url-to-download-passwords.md
```

## Authentication Cracking Tools（认证破解工具）
您可以使用多种工具来利用字典，每种工具都有其优缺点。
在下面的幻灯片中，您将看到如何使用一些常用工具进行网络身份验证破解！

## Hydra
**Hydra** 是一种快速、并行化的网络身份验证破解程序，支持不同的协议。
Hydra 可以攻击近五十种不同的服务类型，包括：
* 思科认证（Cisco auth）
* FTP
* HTTP
* IMAP
* RDP
* SMB
* SSH
* Telnet

该工具可以使用用户名或密码字典，还可以执行纯暴力密码攻击。 Hydra 架构基于**模块**。 模块是让 Hydra 攻击特定**协议**的一段代码。
让我们看看如何配置它并使用它来执行攻击。

您可以通过不带参数运行 Hydra 来检查可用选项：

```
└─# hydra
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Syntax: hydra [[[-l LOGIN|-L FILE] [-p PASS|-P FILE]] | [-C FILE]] [-e nsr] [-o FILE] [-t TASKS] [-M FILE [-T TASKS]] [-w TIME] [-W TIME] [-f] [-s PORT] [-x MIN:MAX:CHARSET] [-c TIME] [-ISOuvVd46] [-m MODULE_OPT] [service://server[:PORT][/OPT]]

Options:
  -l LOGIN or -L FILE  login with LOGIN name, or load several logins from FILE
  -p PASS  or -P FILE  try password PASS, or load several passwords from FILE
  -C FILE   colon separated "login:pass" format, instead of -L/-P options
  -M FILE   list of servers to attack, one entry per line, ':' to specify port
  -t TASKS  run TASKS number of connects in parallel per target (default: 16)
  -U        service module usage details
  -m OPT    options specific for a module, see -U output for information
  -h        more command line options (COMPLETE HELP)
  server    the target: DNS, IP or 192.168.0.0/24 (this OR the -M option)
  service   the service to crack (see below for supported protocols)
  OPT       some service modules support additional input (-U for module help)

Supported services: adam6500 asterisk cisco cisco-enable cobaltstrike cvs firebird ftp[s] http[s]-{head|get|post} http[s]-{get|post}-form http-proxy http-proxy-urlenum icq imap[s] irc ldap2[s] ldap3[-{cram|digest}md5][s] memcached mongodb mssql mysql nntp oracle-listener oracle-sid pcanywhere pcnfs pop3[s] postgres radmin2 rdp redis rexec rlogin rpcap rsh rtsp s7-300 sip smb smtp[s] smtp-enum snmp socks5 ssh sshkey svn teamspeak telnet[s] vmauthd vnc xmpp

Hydra is a tool to guess/crack valid login/password pairs.
Licensed under AGPL v3.0. The newest version is always available at;
https://github.com/vanhauser-thc/thc-hydra
Please don't use in military or secret service organizations, or for illegal
purposes. (This is a wish and non-binding - most such people do not care about
laws and ethics anyway - and tell themselves they are one of the good ones.)

Example:  hydra -l user -P passlist.txt ftp://192.168.0.1

```

```
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - 请不要在军事或秘密服务组织中使用，或用于非法目的（这是不具约束力的，这些 *** 无论如何都忽略了法律和道德）。

语法：hydra [[[-l LOGIN|-L FILE] [-p PASS|-P FILE]] | [-C 文件]] [-e nsr] [-o 文件] [-t 任务] [-M 文件 [-T 任务]] [-w 时间] [-W 时间] [-f] [-s 端口] [-x MIN:MAX:CHARSET] [-c TIME] [-ISOuvVd46] [-m MODULE_OPT] [service://server[:PORT][/OPT]]

选项：
  -l LOGIN 或 -L FILE 使用 LOGIN 名称登录，或从 FILE 加载多个登录
  -p PASS 或 -P FILE 尝试密码 PASS，或从 FILE 加载多个密码
  -C FILE 冒号分隔的“login:pass”格式，而不是 -L/-P 选项
  -M FILE 要攻击的服务器列表，每行一个条目，':' 指定端口
  -t TASKS 运行 TASKS 每个目标的并行连接数（默认值：16）
  -U 服务模块使用详情
  -m 特定于模块的 OPT 选项，有关信息，请参阅 -U 输出
  -h 更多命令行选项（完整帮助）
  服务器目标：DNS、IP 或 192.168.0.0/24（此或 -M 选项）
  service 要破解的服务（有关支​​持的协议，请参见下文）
  OPT 一些服务模块支持附加输入（-U 为模块帮助）

支持的服务： adam6500 asterisk cisco cisco-enablecobaltstrike cvs firebird ftp[s] http[s]-{head|get|post} http[s]-{get|post}-form http-proxy http-proxy-urlenum icq imap [s] irc ldap2[s] ldap3[-{cram|digest}md5][s] memcached mongodb mssql mysql nntp oracle-listener oracle-sid pcanywhere pcnfs pop3[s] postgres radmin2 rdp redis rexec rlogin rpcap rsh rtsp s7-300 sip smb smtp[s] smtp-enum snmp socks5 ssh sshkey svn teamspeak telnet[s] vmauthd vnc xmpp

Hydra 是一个猜测/破解有效登录名/密码对的工具。
在 AGPL v3.0 下获得许可。最新版本始终可用；
https://github.com/vanhauser-thc/thc-hydra
请不要在军事或特工机构使用，或用于非法用途
目的。 （这是一个愿望且不具约束力 - 大多数这样的人并不关心
无论如何，法律和道德——告诉自己他们是好的法律和道德之一。）

示例： hydra -l user -P passlist.txt ftp://192.168.0.1
```

您还可以通过运行查看手册（man hydra）或扩展帮助：

```
hydra -h
```

```
└─# hydra -h
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Syntax: hydra [[[-l LOGIN|-L FILE] [-p PASS|-P FILE]] | [-C FILE]] [-e nsr] [-o FILE] [-t TASKS] [-M FILE [-T TASKS]] [-w TIME] [-W TIME] [-f] [-s PORT] [-x MIN:MAX:CHARSET] [-c TIME] [-ISOuvVd46] [-m MODULE_OPT] [service://server[:PORT][/OPT]]

Options:
  -R        restore a previous aborted/crashed session
  -I        ignore an existing restore file (don't wait 10 seconds)
  -S        perform an SSL connect
  -s PORT   if the service is on a different default port, define it here
  -l LOGIN or -L FILE  login with LOGIN name, or load several logins from FILE
  -p PASS  or -P FILE  try password PASS, or load several passwords from FILE
  -x MIN:MAX:CHARSET  password bruteforce generation, type "-x -h" to get help
  -y        disable use of symbols in bruteforce, see above
  -r        use a non-random shuffling method for option -x
  -e nsr    try "n" null password, "s" login as pass and/or "r" reversed login
  -u        loop around users, not passwords (effective! implied with -x)
  -C FILE   colon separated "login:pass" format, instead of -L/-P options
  -M FILE   list of servers to attack, one entry per line, ':' to specify port
  -o FILE   write found login/password pairs to FILE instead of stdout
  -b FORMAT specify the format for the -o FILE: text(default), json, jsonv1
  -f / -F   exit when a login/pass pair is found (-M: -f per host, -F global)
  -t TASKS  run TASKS number of connects in parallel per target (default: 16)
  -T TASKS  run TASKS connects in parallel overall (for -M, default: 64)
  -w / -W TIME  wait time for a response (32) / between connects per thread (0)
  -c TIME   wait time per login attempt over all threads (enforces -t 1)
  -4 / -6   use IPv4 (default) / IPv6 addresses (put always in [] also in -M)
  -v / -V / -d  verbose mode / show login+pass for each attempt / debug mode 
  -O        use old SSL v2 and v3
  -K        do not redo failed attempts (good for -M mass scanning)
  -q        do not print messages about connection errors
  -U        service module usage details
  -m OPT    options specific for a module, see -U output for information
  -h        more command line options (COMPLETE HELP)
  server    the target: DNS, IP or 192.168.0.0/24 (this OR the -M option)
  service   the service to crack (see below for supported protocols)
  OPT       some service modules support additional input (-U for module help)

Supported services: adam6500 asterisk cisco cisco-enable cobaltstrike cvs firebird ftp[s] http[s]-{head|get|post} http[s]-{get|post}-form http-proxy http-proxy-urlenum icq imap[s] irc ldap2[s] ldap3[-{cram|digest}md5][s] memcached mongodb mssql mysql nntp oracle-listener oracle-sid pcanywhere pcnfs pop3[s] postgres radmin2 rdp redis rexec rlogin rpcap rsh rtsp s7-300 sip smb smtp[s] smtp-enum snmp socks5 ssh sshkey svn teamspeak telnet[s] vmauthd vnc xmpp

Hydra is a tool to guess/crack valid login/password pairs.
Licensed under AGPL v3.0. The newest version is always available at;
https://github.com/vanhauser-thc/thc-hydra
Please don't use in military or secret service organizations, or for illegal
purposes. (This is a wish and non-binding - most such people do not care about
laws and ethics anyway - and tell themselves they are one of the good ones.)
These services were not compiled in: afp ncp oracle sapr3 smb2.

Use HYDRA_PROXY_HTTP or HYDRA_PROXY environment variables for a proxy setup.
E.g. % export HYDRA_PROXY=socks5://l:p@127.0.0.1:9150 (or: socks4:// connect://)
     % export HYDRA_PROXY=connect_and_socks_proxylist.txt  (up to 64 entries)
     % export HYDRA_PROXY_HTTP=http://login:pass@proxy:8080
     % export HYDRA_PROXY_HTTP=proxylist.txt  (up to 64 entries)

Examples:
  hydra -l user -P passlist.txt ftp://192.168.0.1
  hydra -L userlist.txt -p defaultpw imap://192.168.0.1/PLAIN
  hydra -C defaults.txt -6 pop3s://[2001:db8::1]:143/TLS:DIGEST-MD5
  hydra -l admin -p password ftp://[192.168.0.0/24]/
  hydra -L logins.txt -P pws.txt -M targets.txt ssh
```

```
└─# hydra -h
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - 请不要在军事或秘密服务组织中使用，或用于非法目的（这是不具约束力的，这些 *** 无论如何都忽略了法律和道德）。

语法：hydra [[[-l LOGIN|-L FILE] [-p PASS|-P FILE]] | [-C 文件]] [-e nsr] [-o 文件] [-t 任务] [-M 文件 [-T 任务]] [-w 时间] [-W 时间] [-f] [-s 端口] [-x MIN:MAX:CHARSET] [-c TIME] [-ISOuvVd46] [-m MODULE_OPT] [service://server[:PORT][/OPT]]

选项：
  -R 恢复以前中止/崩溃的会话
  -I 忽略现有的还原文件（不要等待 10 秒）
  -S 执行 SSL 连接
  -s PORT 如果服务位于不同的默认端口，请在此处定义
  -l LOGIN 或 -L FILE 使用 LOGIN 名称登录，或从 FILE 加载多个登录
  -p PASS 或 -P FILE 尝试密码 PASS，或从 FILE 加载多个密码
  -x MIN:MAX:CHARSET 密码暴力生成，输入“-x -h”获取帮助
  -y 禁止在暴力破解中使用符号，见上文
  -r 对选项 -x 使用非随机洗牌方法
  -e nsr 尝试“n”空密码，“s”作为通过登录和/或“r”反向登录
  -u 循环用户，而不是密码（有效！隐含在 -x 中）
  -C FILE 冒号分隔的“login:pass”格式，而不是 -L/-P 选项
  -M FILE 要攻击的服务器列表，每行一个条目，':' 指定端口
  -o FILE 将找到的登录名/密码对写入 FILE 而不是 stdout
  -b FORMAT 指定 -o FILE 的格式：text(default), json, jsonv1
  -f / -F 找到登录/密码对时退出（-M：-f 每个主机，-F 全局）
  -t TASKS 运行 TASKS 每个目标的并行连接数（默认值：16）
  -T TASKS 运行 TASKS 整体并行连接（对于 -M，默认值：64）
  -w / -W TIME 响应等待时间 (32) / 每个线程之间的连接 (0)
  -c TIME 在所有线程上每次登录尝试的等待时间（强制 -t 1）
  -4 / -6 使用 IPv4（默认）/ IPv6 地址（总是放在 [] 中也放在 -M 中）
  -v / -V / -d 详细模式 / 每次尝试显示登录+通过 / 调试模式
  -O 使用旧的 SSL v2 和 v3
  -K 不重做失败的尝试（有利于 -M 大规模扫描）
  -q 不打印有关连接错误的消息
  -U 服务模块使用详情
  -m 特定于模块的 OPT 选项，有关信息，请参阅 -U 输出
  -h 更多命令行选项（完整帮助）
  服务器目标：DNS、IP 或 192.168.0.0/24（此或 -M 选项）
  service 要破解的服务（有关支​​持的协议，请参见下文）
  OPT 一些服务模块支持附加输入（-U 为模块帮助）

支持的服务： adam6500 asterisk cisco cisco-enablecobaltstrike cvs firebird ftp[s] http[s]-{head|get|post} http[s]-{get|post}-form http-proxy http-proxy-urlenum icq imap [s] irc ldap2[s] ldap3[-{cram|digest}md5][s] memcached mongodb mssql mysql nntp oracle-listener oracle-sid pcanywhere pcnfs pop3[s] postgres radmin2 rdp redis rexec rlogin rpcap rsh rtsp s7-300 sip smb smtp[s] smtp-enum snmp socks5 ssh sshkey svn teamspeak telnet[s] vmauthd vnc xmpp

Hydra 是一个猜测/破解有效登录名/密码对的工具。
在 AGPL v3.0 下获得许可。最新版本始终可用；
https://github.com/vanhauser-thc/thc-hydra
请不要在军事或特工机构使用，或用于非法用途
目的。 （这是一个愿望且不具约束力 - 大多数这样的人并不关心
无论如何，法律和道德——告诉自己他们是好的法律和道德之一。）
这些服务未编译在：afp ncp oracle sapr3 smb2。

使用 HYDRA_PROXY_HTTP 或 HYDRA_PROXY 环境变量进行代理设置。
E.g. % export HYDRA_PROXY=socks5://l:p@127.0.0.1:9150 (or: socks4:// connect://)
     % export HYDRA_PROXY=connect_and_socks_proxylist.txt  (up to 64 entries)
     % export HYDRA_PROXY_HTTP=http://login:pass@proxy:8080
     % export HYDRA_PROXY_HTTP=proxylist.txt  (up to 64 entries)

例子：
  hydra -l user -P passlist.txt ftp://192.168.0.1
  hydra -L userlist.txt -p defaultpw imap://192.168.0.1/PLAIN
  hydra -C defaults.txt -6 pop3s://[2001:db8::1]:143/TLS:DIGEST-MD5
  hydra -l admin -p password ftp://[192.168.0.0/24]/
  hydra -L logins.txt -P pws.txt -M targets.txt ssh
```

要获取有关模块的详细信息，您可以使用 `-U` 命令行开关，例如：

```
hydra -U rdp
```

```
└─# hydra -U rdp
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-07-12 05:33:36

Help for module rdp:
============================================================================
Module rdp is optionally taking the windows domain name.
For example:
hydra rdp://192.168.0.1/firstdomainname -l john -p doe
```

```
模块 rdp 的帮助：
==================================================== ===========================
模块 rdp 可以选择使用 windows 域名。
例如：
hydra rdp://192.168.0.1/firstdomainname -l john -p doe
```

请记住，您可以在帮助的“支持的服务”部分中检查可用模块：

```
Supported services: adam6500 asterisk cisco cisco-enable cobaltstrike cvs firebird ftp[s] http[s]-{head|get|post} http[s]-{get|post}-form http-proxy http-proxy-urlenum icq imap[s] irc ldap2[s] ldap3[-{cram|digest}md5][s] memcached mongodb mssql mysql nntp oracle-listener oracle-sid pcanywhere pcnfs pop3[s] postgres radmin2 rdp redis rexec rlogin rpcap rsh rtsp s7-300 sip smb smtp[s] smtp-enum snmp socks5 ssh sshkey svn teamspeak telnet[s] vmauthd vnc xmpp
```

要使用用户名列表（在 users.txt 文件中）和密码列表（pass.txt 文件）对服务发起字典攻击，您必须使用以下语法：

```
hydra -L users.txt -P pass.txt <service://server> <options>
```

## Telnet Attack Example
例如，您可以通过使用以下命令行调用 Hydra 来攻击 telnet 服务：

```
hydra -L users.txt -P pass.txt telnet://target.server
```

## HTTP Basic Auth Attack Example
在这里，您可以找到针对受密码保护的 Web 资源的攻击会话：

```
hydra -L users.txt -P pass.txt http-get://localhost/
```

## References
[Common passwords](https://wiki.skullsecurity.org/index.php/Passwords)

