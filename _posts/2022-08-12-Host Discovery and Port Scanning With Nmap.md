---
layout: post
title: "Host Discovery and Port Scanning With Nmap"
date: "2022-08-12"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Information Gathering
    - Active Information Gathering
---

# Host Discovery With Nmap（使用 Nmap 发现主机）
How to discover systems or hosts on a network that you are connected to.

Performing a pentest in the internal network and the first order of business is to discover all the devices on that network. This can be done through the use of nmap. More specifically we will look at how to perform a ping sweep, or a ping scan with nmap, in order to discover the active or the host that currently on the target network that you are currently connect to.

```
ip a s
```

```
┌──(root㉿kali)-[~]
└─# ip a s  
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 00:0c:29:af:2b:2a brd ff:ff:ff:ff:ff:ff
    inet 192.168.248.148/24 brd 192.168.248.255 scope global dynamic noprefixroute eth0
       valid_lft 1153sec preferred_lft 1153sec
    inet6 fe80::20c:29ff:feaf:2b2a/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
```

```
man nmap

HOST DISCOVERY:
    -sn: Ping Scan - disable port scan
    -sn：Ping 扫描 - 禁用端口扫描
```

`-sP` (Ping扫描)
该选项告诉Nmap仅仅进行ping扫描 (主机发现)，然后打印出对扫描做出响应的那些主机。 没有进一步的测试 (如端口扫描或者操作系统探测)。 这比列表扫描更积极，常常用于和列表扫描相同的目的。它可以得到些许目标网络的信息而不被特别注意到。对于攻击者来说，了解多少主机正在运行比列表扫描提供的一列IP和主机名往往更有价值。

系统管理员往往也很喜欢这个选项。它可以很方便地得出网络上有多少机器正在运行或者监视服务器是否正常运行。常常有人称它为地毯式ping，它比ping广播地址更可靠，因为许多主机对广播请求不响应。

-sP选项在默认情况下，发送一个ICMP回声请求和一个TCP报文到80端口。如果非特权用户执行，就发送一个SYN报文 (用connect()系统调用)到目标机的80端口。 当特权用户扫描局域网上的目标机时，会发送ARP请求(-PR)， ，除非使用了--send-ip选项。 -sP选项可以和除-P0)之外的任何发现探测类型-P* 选项结合使用以达到更大的灵活性。 一旦使用了任何探测类型和端口选项，默认的探测(ACK和回应请求)就被覆盖了。 当防守严密的防火墙位于运行Nmap的源主机和目标网络之间时， 推荐使用那些高级选项。否则，当防火墙捕获并丢弃探测包或者响应包时，一些主机就不能被探测到。    

在以前的 Nmap 版本中，`-sn` 被称为 `-sP`。

```
sudo nmap -sn 192.168.248.0/24
```

```
┌──(root㉿kali)-[~]
└─# sudo nmap -sn 192.168.248.0/24  
Starting Nmap 7.92 ( https://nmap.org ) at 2022-08-11 23:20 EDT
Nmap scan report for 192.168.248.1
Host is up (0.00031s latency).
MAC Address: 00:50:56:C0:00:08 (VMware)
Nmap scan report for 192.168.248.2
Host is up (0.00027s latency).
MAC Address: 00:50:56:F7:2A:63 (VMware)
Nmap scan report for 192.168.248.254
Host is up (0.00030s latency).
MAC Address: 00:50:56:F1:C5:33 (VMware)
Nmap scan report for 192.168.248.148
Host is up.
Nmap done: 256 IP addresses (4 hosts up) scanned in 2.12 seconds
```

```
sudo apt-get install netdiscover -y
```

```
Currently scanning: Finished!   |   Screen View: Unique Hosts                                                                                                                              
                                                                                                                                                                                            
 4 Captured ARP Req/Rep packets, from 3 hosts.   Total size: 240                                                                                                                            
 _____________________________________________________________________________
   IP            At MAC Address     Count     Len  MAC Vendor / Hostname      
 -----------------------------------------------------------------------------
 192.168.248.1   00:50:56:c0:00:08      1      60  VMware, Inc.                                                                                                                             
 192.168.248.2   00:50:56:f7:2a:63      2     120  VMware, Inc.                                                                                                                             
 192.168.248.254 00:50:56:f1:c5:33      1      60  VMware, Inc.
```

```
sudo netdiscover -i eth0 -r 192.168.248.0/24
```

# Port Scanning With Nmap（使用 Nmap 进行端口扫描）

The process of identifying open ports on the target system, as well as respective services on the running on this open ports use nmap.

Targeting or performing a port scan on a host, in order to identify the open ports that running on this target system as well as the services running on the open ports try to perform some service version detection, scanning operating system detection. The objective is to obtain as much information as possible or enumerate as mush information as possible from a target system with nmap. Focus on port scanning on both TCP ports or UDP ports.

Perform a default Nmap scan, you perform a SYN scan on a thousand of the most frequently used ports.

```
nmap 10.4.19.218
```

Whenever you are dealing with Windows target system. Windows system will typically block ICMP pings or ping probes if you will. And as a result, nmap will obtain "Host seems down." And that is because it blocks the ping probes.

`-Pn` avoiding the process of performing a ping or try to identify if the host is active.

We are still performing a port scan on the a thounsand of mostly used ports, the only difference now is telling nmap we not check this host is online, just perform the port scan.

```
nmap -Pn 10.4.19.218
```

Perform nmap scan on the entire range of TCP ports.

```
nmap -Pn -p- 10.4.19.218
nmap -Pn -p1-65535 10.4.19.218
```

```
nmap -Pn -p 80 10.4.19.218
nmap -Pn -p 80,445,3389 10.4.19.218
nmap -Pn -p 8080 10.4.19.218
nmap -Pn -p1-1000 10.4.19.218
```

`-F` (快速 (有限的端口) 扫描)
在nmap的nmap-services 文件中(对于-sO，是协议文件)指定您想要扫描的端口。 这比扫描所有65535个端口快得多。 因为该列表包含如此多的TCP端口(1200多)，这和默认的TCP扫描 scan (大约1600个端口)速度差别不是很大。如果您用--datadir选项指定您自己的 小小的nmap-services文件 ，差别会很惊人。

`-F` fast scann option, which would only scann a hundred of the most commonly used ports on the target system.

```
nmap -Pn -F 192.168.248.148
```

```
┌──(root㉿kali)-[~]
└─# nmap -Pn -F 192.168.248.148      
Starting Nmap 7.92 ( https://nmap.org ) at 2022-08-12 06:07 EDT
Nmap scan report for 192.168.248.148
Host is up (0.0000030s latency).
All 100 scanned ports on 192.168.248.148 are in ignored states.
Not shown: 100 closed tcp ports (reset)

Nmap done: 1 IP address (1 host up) scanned in 0.10 seconds
```

`-sU` Perform a UDP port scan. By default, Nmap will perform TCP port scan.

```
nmap -Pn -sU 192.168.248.148
```

`-v` Increase the verbosity of the output.

```
nmap -Pn -F 192.168.248.148 -v
```

Identify the specific service and service version running on the open ports.

`-sV` Perform a service verison detection scann.

```
nmap -Pn -F -sV 192.168.248.148
```

`-O` Perform operating system detection scann. That will try to identify what operating system is running on the target system.

```
nmap -Pn -F -sV -O 192.168.248.148
nmap -Pn -F -sV -O 192.168.248.148 -v
```

We can obatin more information from these open ports by 

`-sC` Performming the default Nmap script scan. This will run a list of nmap script on the ports that are currently open in order to identify or enumerate more information from these open ports.

```
SCRIPT SCAN:
             -sC: equivalent to --script=default
```

Run the default Nmap script scan.

```
nmap -Pn -F -sV -O -sC 192.168.248.148 -v
```

The agressive scan. It combines `-sV` service version detection, `-O` operating system detection and `-sC` the default Nmap script scan into one. It can be specified by `-A`.

```
nmap -Pn -F -A 192.168.248.148 -v
```

If you want to speed up the scan, you can utilize nmap 

`/-T4`

```
TIMING AND PERFORMANCE:
    Options which take <time> are in seconds, or append 'ms' (milliseconds),
    's' (seconds), 'm' (minutes), or 'h' (hours) to the value (e.g. 30m).
    -T<0-5>: Set timing template (higher is faster)

```

`-T <Paranoid|Sneaky|Polite|Normal|Aggressive|Insane>` (设置时间模板)
上述优化时间控制选项的功能很强大也很有效，但有些用户会被迷惑。此外， 往往选择合适参数的时间超过了所需优化的扫描时间。因此，Nmap提供了一些简单的 方法，使用6个时间模板，使用时采用-T选项及数字(0 - 5) 或名称。模板名称有paranoid (0)、sneaky (1)、polite (2)、normal(3)、 aggressive (4)和insane (5)。前两种模式用于IDS躲避，Polite模式降低了扫描 速度以使用更少的带宽和目标主机资源。默认模式为Normal，因此-T3 实际上是未做任何优化。Aggressive模式假设用户具有合适及可靠的网络从而加速 扫描。Insane模式假设用户具有特别快的网络或者愿意为获得速度而牺牲准确性。

用户可以根据自己的需要选择不同的模板，由Nmap负责选择实际的时间值。 模板也会针对其它的优化控制选项进行速度微调。例如，-T4 针对TCP端口禁止动态扫描延迟超过10ms，-T5对应的值为5ms。 模板可以和优化调整控制选项组合使用，但模板必须首先指定，否则模板的标准值 会覆盖用户指定的值。建议在扫描可靠的网络时使用 -T4，即使 在自己要增加优化控制选项时也使用(在命令行的开始)，从而从这些额外的较小的优化 中获益。

如果用于有足够的带宽或以太网连接，仍然建议使用-T4选项。 有些用户喜欢-T5选项，但这个过于强烈。有时用户考虑到避免使主机 崩溃或者希望更礼貌一些会采用-T2选项。他们并没意识到-T Polite选项是如何的慢，这种模式的扫描比默认方式实际上要多花10倍的时间。默认时间 选项(-T3)很少有主机崩溃和带宽问题，比较适合于谨慎的用户。不进行 版本检测比进行时间调整能更有效地解决这些问题。

虽然-T0和-T1选项可能有助于避免IDS告警，但 在进行上千个主机或端口扫描时，会显著增加时间。对于这种长时间的扫描，宁可设定确切的时间 值，而不要去依赖封装的-T0和-T1选项。

T0选项的主要影响是对于连续扫描，在一个时间只能扫描一个端口， 每个探测报文的发送间隔为5分钟。T1和T2选项比较类似， 探测报文间隔分别为15秒和0.4秒。T3是Nmap的默认选项，包含了并行扫描。 T4选项与 --max-rtt-timeout 1250 --initial-rtt-timeout 500 等价，最大TCP扫描延迟为10ms。T5等价于 --max-rtt-timeout 300 --min-rtt-timeout 50 --initial-rtt-timeout 250 --host-timeout 900000，最大TCP扫描延迟为5ms。

By default Nmap will automatically detect what timing template to use based on the network infrastructure.

`-T5` is not recommand in the prodution network because this will increase the amount of packages been sent that could potentially lead to a denial of service on the networking equipment like router or switches.

```
nmap -Pn -F -T5 -sV -O -sC 192.168.248.148 -v
```

`-oN <filespec>` (标准输出)
要求将标准输出直接写入指定 的文件。如上所述，这个格式与交互式输出 略有不同。

```
-oX <filespec> (XML输出)
要求XML输出直接写入指定 的文件。Nmap包含了一个文档类型定义(DTD)，使XML解析器有效地 进行XML输出。这主要是为了程序应用，同时也可以协助人工解释 Nmap的XML输出。DTD定义了合法的格式元素，列举可使用的属性和 值。最新的版本可在https://nmap.org/data/nmap.dtd获取。

XML提供了可供软件解析的稳定格式输出，主要的计算机 语言都提供了免费的XML解析器，如C/C++，Perl，Python和Java。 针对这些语言有一些捆绑代码用于处理Nmap的输出和特定的执行程序。 例如perl CPAN中的Nmap::Scanner 和Nmap::Parser。 对几乎所有与Nmap有接口的主要应用来说，XML是首选的格式。

XML输出引用了一个XSL样式表，用于格式化输出结果，类似于 HTML。最方便的方法是将XML输出加载到一个Web浏览器，如Firefox 或IE。由于nmap.xsl文件的绝对 路径，因此通常只能在运行了Nmap的机器上工作(或类似配置的机器)。 类似于任何支持Web机器的HTML文件，--stylesheet 选项可用于建立可移植的XML文件。
```

```
nmap -Pn -F 192.168.248.148 -oN test.txt
```

`-oX` XML format. This output can be import into the framwork like Metasploit.

```
nmap -Pn -F 192.168.248.148 -oX test.txt
```