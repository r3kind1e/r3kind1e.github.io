---
layout: post
title: "Scanning and OS Fingerprinting"
date: "2022-06-19"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Footprinting & Scanning
---
# 概述
在本实验中，您将使用 Nmap 扫描仪执行扫描、侦察和操作系统指纹识别活动。

# 任务
## 实验室环境
在这个实验室环境中，用户将获得对 Kali GUI 实例的访问权限。同一网络上有几台机器运行各种服务。

**目的：** 使用 Nmap 工具进行扫描和操作系统指纹识别，并回答以下问题：

有多少台机器？
* pc1.ine.local机器上打开了哪些端口？

```
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
```

* 机器pc1.ine.local机器上正在运行什么操作系统？

```
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
```

* pc2.ine.local机器上正在运行哪些服务？

```
PORT      STATE SERVICE
27017/tcp open  mongod
```

* 在其中一台机器上运行的 FTP 服务器的版本是什么？

vsftpd 3.0.3

* 缓存服务器也在其中一台机器上运行。那台机器的域名是什么？

pc3.ine.local

* NoSQL 数据库和 SQL 数据库服务在不同的机器上运行。我们可以使用 Nmap 脚本从中提取一些信息吗？
感觉没有提取到特别有用的信息。

![pcinelocal](/img/in-post/ine/pcinelocal.png)

## 工具
本实验的最佳工具是： - Nmap - Linux 终端

# 我自己的解决思路
首先查看本机的IP地址，发现eth1在192.97.27.0/24网络中。
```
root@INE:~# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.1.0.7  netmask 255.255.0.0  broadcast 10.1.255.255
        ether 02:42:0a:01:00:07  txqueuelen 0  (Ethernet)
        RX packets 808  bytes 94391 (92.1 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 672  bytes 1953877 (1.8 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.97.27.2  netmask 255.255.255.0  broadcast 192.97.27.255
        ether 02:42:c0:61:1b:02  txqueuelen 0  (Ethernet)
        RX packets 16  bytes 1416 (1.3 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 2911  bytes 23898783 (22.7 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 2911  bytes 23898783 (22.7 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

对该/24网络进行ping扫描，发现192.97.27.1-6主机存活。
```
root@INE:~# nmap -sn 192.97.27.0/24
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 08:03 IST
Nmap scan report for linux (192.97.27.1)
Host is up (0.000050s latency).
MAC Address: 02:42:86:24:B0:B8 (Unknown)
Nmap scan report for pc1.ine.local (192.97.27.3)
Host is up (0.000014s latency).
MAC Address: 02:42:C0:61:1B:03 (Unknown)
Nmap scan report for pc2.ine.local (192.97.27.4)
Host is up (0.000012s latency).
MAC Address: 02:42:C0:61:1B:04 (Unknown)
Nmap scan report for pc3.ine.local (192.97.27.5)
Host is up (0.000012s latency).
MAC Address: 02:42:C0:61:1B:05 (Unknown)
Nmap scan report for pc4.ine.local (192.97.27.6)
Host is up (0.000020s latency).
MAC Address: 02:42:C0:61:1B:06 (Unknown)
Nmap scan report for INE (192.97.27.2)
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 2.07 seconds
```
对这6个主机进行操作系统指纹识别。其中，pc2和pc3没有识别出操作系统详细信息，因为扫描的1000个常用tcp端口全部是closed。
```
root@INE:~# nmap -Pn -O 192.97.27.1-6
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 08:06 IST
Nmap scan report for linux (192.97.27.1)
Host is up (0.000019s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE    SERVICE
22/tcp  open     ssh
80/tcp  filtered http
443/tcp filtered https
MAC Address: 02:42:86:24:B0:B8 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop

Nmap scan report for pc1.ine.local (192.97.27.3)
Host is up (0.000014s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
MAC Address: 02:42:C0:61:1B:03 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop

Nmap scan report for pc2.ine.local (192.97.27.4)
Host is up (0.000033s latency).
All 1000 scanned ports on pc2.ine.local (192.97.27.4) are in ignored states.
Not shown: 1000 closed tcp ports (reset)
MAC Address: 02:42:C0:61:1B:04 (Unknown)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop

Nmap scan report for pc3.ine.local (192.97.27.5)
Host is up (0.000015s latency).
All 1000 scanned ports on pc3.ine.local (192.97.27.5) are in ignored states.
Not shown: 1000 closed tcp ports (reset)
MAC Address: 02:42:C0:61:1B:05 (Unknown)
Too many fingerprints match this host to give specific OS details
Network Distance: 1 hop

Nmap scan report for pc4.ine.local (192.97.27.6)
Host is up (0.000025s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
MAC Address: 02:42:C0:61:1B:06 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop

Nmap scan report for INE (192.97.27.2)
Host is up (0.000025s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE
3389/tcp open  ms-wbt-server
5910/tcp open  cm
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6.32
OS details: Linux 2.6.32
Network Distance: 0 hops

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 6 IP addresses (6 hosts up) scanned in 5.57 seconds

```

从[OS Detection](https://nmap.org/book/man-os-detection.html)中，我们可以知道，Nmap需要至少找到一个开放端口和一个关闭端口，才能猜测目标的操作系统。所以，我们推测pc2和pc3可能在非常用端口上运行着服务。

对6个目标进行全端口扫描，以进行操作系统检测。
```
root@INE:~# nmap -p1-65535 -Pn -O 192.97.27.1-6
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 08:14 IST
Nmap scan report for linux (192.97.27.1)
Host is up (0.000034s latency).
Not shown: 65530 closed tcp ports (reset)
PORT      STATE    SERVICE
22/tcp    open     ssh
80/tcp    filtered http
443/tcp   filtered https
25555/tcp open     unknown
29999/tcp open     bingbang
MAC Address: 02:42:86:24:B0:B8 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop

Nmap scan report for pc1.ine.local (192.97.27.3)
Host is up (0.000017s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
MAC Address: 02:42:C0:61:1B:03 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop

Nmap scan report for pc2.ine.local (192.97.27.4)
Host is up (0.000014s latency).
Not shown: 65534 closed tcp ports (reset)
PORT      STATE SERVICE
27017/tcp open  mongod
MAC Address: 02:42:C0:61:1B:04 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop

Nmap scan report for pc3.ine.local (192.97.27.5)
Host is up (0.000013s latency).
Not shown: 65534 closed tcp ports (reset)
PORT      STATE SERVICE
11211/tcp open  memcache
MAC Address: 02:42:C0:61:1B:05 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop

Nmap scan report for pc4.ine.local (192.97.27.6)
Host is up (0.000043s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
MAC Address: 02:42:C0:61:1B:06 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop

Nmap scan report for INE (192.97.27.2)
Host is up (0.000029s latency).
Not shown: 65532 closed tcp ports (reset)
PORT      STATE SERVICE
3389/tcp  open  ms-wbt-server
5910/tcp  open  cm
45654/tcp open  unknown
Device type: general purpose
Running: Linux 2.6.X
OS CPE: cpe:/o:linux:linux_kernel:2.6.32
OS details: Linux 2.6.32
Network Distance: 0 hops

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 6 IP addresses (6 hosts up) scanned in 10.17 seconds
```

在pc4上运行的ftp服务的版本为vsftpd 3.0.3。

```
root@INE:~# nmap -sV -p21 pc4.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 08:20 IST
Nmap scan report for pc4.ine.local (192.97.27.6)
Host is up (0.000060s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
MAC Address: 02:42:C0:61:1B:06 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.49 seconds
```

使用[mysql-enum](https://nmap.org/nsedoc/scripts/mysql-enum.html)枚举账号。
```
root@INE:~# nmap --script=mysql-enum pc1.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 08:51 IST
Nmap scan report for pc1.ine.local (192.254.94.3)
Host is up (0.0000090s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
| mysql-enum: 
|   Valid usernames: 
|     root:<empty> - Valid credentials
|     netadmin:<empty> - Valid credentials
|     user:<empty> - Valid credentials
|     test:<empty> - Valid credentials
|     guest:<empty> - Valid credentials
|     sysadmin:<empty> - Valid credentials
|     administrator:<empty> - Valid credentials
|     webadmin:<empty> - Valid credentials
|     admin:<empty> - Valid credentials
|     web:<empty> - Valid credentials
|_  Statistics: Performed 10 guesses in 1 seconds, average tps: 10.0
MAC Address: 02:42:C0:FE:5E:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.41 seconds
```

将账号保存在文件usernames.txt中。
```
root
netadmin
user
test
guest
sysadmin
administrator
webadmin
admin
web
```
观察到桌面上有一个wordlists文件夹，里面有一个100-common-passwords.txt文件，文件的内容如下：

```
242424
0987654321
marisol
nikita
daisy
jeremiah
pineapple
mhine
isaiah
christmas
cesar
lolipop
butterfly1
chloe
lawrence
xbox360
sheena
murphy
madalina
anamaria
gateway
debbie
yourmom
blonde
jasmine1
please
bubbles1
jimmy
beatriz
poopoo
diamonds
whitney
friendship
sweetness
pauline
desiree
trouble
741852
united
marley
brian
barbara
hannah1
bananas
julius
leanne
sandy
marie1
anita
lover1
chicago
twinkle
pantera
february
birthday
shadow1
qwert
bebita
87654321
twilight
imissyou
pollito
ashlee
tucker
cookie1
shelly
catalina
147852369
beckham
simone
nursing
iloveyou!
eugene
torres
damian
123123123
joshua1
bobby
babyface
andre
donald
daniel1
panther
dinamo
mommy
juliana
cassandra
trustno1
freedom1
14344
autumn
mendoza
sq!us3r
adminpasswd
raspberry
74k&^*nh#$
arcsight
promise
karaf
vagrant
```
我们可以尝试用获取的用户名和这些密码进行mysql暴力破解，虽然最终一无所获。

```
root@INE:~# cd /root/Desktop/wordlists/
root@INE:~/Desktop/wordlists# nmap --script=mysql-brute --script-args userdb=usernames.txt,passdb=100-common-passwords.txt pc1.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 09:16 IST
Nmap scan report for pc1.ine.local (192.129.50.3)
Host is up (0.0000090s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
| mysql-brute: 
|   Accounts: No valid accounts found
|_  Statistics: Performed 1010 guesses in 1 seconds, average tps: 1010.0
MAC Address: 02:42:C0:81:32:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.57 seconds
```

```
nmap --script=mysql-brute --script-args userdb=usernames.txt pc1.ine.local
nmap --script=mysql-brute pc1.ine.local
```

使用[mongodb-databases](https://nmap.org/nsedoc/scripts/mongodb-databases.html)，尝试获取pc2的mongod的信息：
```
root@INE:~# nmap -p27017 --script mongodb-databases pc2.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 09:28 IST
Nmap scan report for pc2.ine.local (192.129.50.4)
Host is up (0.000046s latency).

PORT      STATE SERVICE
27017/tcp open  mongodb
| mongodb-databases: 
|   databases
|     0
|       name = admin
|       empty = false
|       sizeOnDisk = 32768.0
|     1
|       name = city
|       empty = false
|       sizeOnDisk = 1781760.0
|     2
|       name = config
|       empty = false
|       sizeOnDisk = 12288.0
|     3
|       name = local
|       empty = false
|       sizeOnDisk = 32768.0
|   totalSize = 1859584.0
|_  ok = 1.0
MAC Address: 02:42:C0:81:32:04 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.42 seconds
```

# Solution（解决方案）
第 1 步：打开实验室链接以访问 Kali GUI 实例。

步骤 2：检查提供的主机网络上的在线机器数。

检查提供的主机的 IP 地址：

命令：

```
ip addr
```

```
root@INE:~# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: ip_vti0@NONE: <NOARP> mtu 1480 qdisc noop state DOWN group default qlen 1000
    link/ipip 0.0.0.0 brd 0.0.0.0
15195: eth0@if15196: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:0a:01:00:0b brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 10.1.0.11/16 brd 10.1.255.255 scope global eth0
       valid_lft forever preferred_lft forever
15198: eth1@if15199: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default 
    link/ether 02:42:c0:40:8c:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 192.64.140.2/24 brd 192.64.140.255 scope global eth1
       valid_lft forever preferred_lft forever
```
请注意，有 2 个接口（不包括环回接口）：eth0和eth1。

在本实验中，我们将关注分配给eth1接口的 IP 地址，即192.204.142.2。

注意：您的 IP 地址会有所不同，因此请确保使用正确的 IP 地址，否则命令可能不会给出预期的结果！

为了使用 Nmap 扫描此主机网络上存在的所有机器，请使用以下命令：

命令：

```
nmap 192.64.140.0/24
```

```
root@INE:~# nmap 192.64.140.0/24
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:05 IST
Nmap scan report for linux (192.64.140.1)
Host is up (0.0000090s latency).
Not shown: 997 closed tcp ports (reset)
PORT    STATE    SERVICE
22/tcp  open     ssh
80/tcp  filtered http
443/tcp filtered https
MAC Address: 02:42:3B:C9:86:3F (Unknown)

Nmap scan report for pc1.ine.local (192.64.140.3)
Host is up (0.0000090s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
MAC Address: 02:42:C0:40:8C:03 (Unknown)

Nmap scan report for pc2.ine.local (192.64.140.4)
Host is up (0.0000090s latency).
All 1000 scanned ports on pc2.ine.local (192.64.140.4) are in ignored states.
Not shown: 1000 closed tcp ports (reset)
MAC Address: 02:42:C0:40:8C:04 (Unknown)

Nmap scan report for pc3.ine.local (192.64.140.5)
Host is up (0.0000090s latency).
All 1000 scanned ports on pc3.ine.local (192.64.140.5) are in ignored states.
Not shown: 1000 closed tcp ports (reset)
MAC Address: 02:42:C0:40:8C:05 (Unknown)

Nmap scan report for pc4.ine.local (192.64.140.6)
Host is up (0.0000090s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
MAC Address: 02:42:C0:40:8C:06 (Unknown)

Nmap scan report for INE (192.64.140.2)
Host is up (0.0000040s latency).
Not shown: 998 closed tcp ports (reset)
PORT     STATE SERVICE
3389/tcp open  ms-wbt-server
5910/tcp open  cm

Nmap done: 256 IP addresses (6 hosts up) scanned in 3.43 seconds
```

这将导致 Nmap 扫描整个 /24 子网，该子网将包含 2 8 (=256) 个潜在主机。

但正如您所见，只有6 台主机在线，如上图所示。

如果您注意到，上述命令导致 Nmap 扫描主机上的服务。但这对于我们感兴趣的信息来说太过分了。因此，我们可以使用以下命令跳过端口扫描，只报告响应主机发现扫描的可用主机：

命令：
```
nmap -sP 192.64.140.0/24
```

```
root@INE:~# nmap -sP 192.64.140.0/24
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:08 IST
Nmap scan report for linux (192.64.140.1)
Host is up (0.000036s latency).
MAC Address: 02:42:3B:C9:86:3F (Unknown)
Nmap scan report for pc1.ine.local (192.64.140.3)
Host is up (0.000011s latency).
MAC Address: 02:42:C0:40:8C:03 (Unknown)
Nmap scan report for pc2.ine.local (192.64.140.4)
Host is up (0.000011s latency).
MAC Address: 02:42:C0:40:8C:04 (Unknown)
Nmap scan report for pc3.ine.local (192.64.140.5)
Host is up (0.0000090s latency).
MAC Address: 02:42:C0:40:8C:05 (Unknown)
Nmap scan report for pc4.ine.local (192.64.140.6)
Host is up (0.000022s latency).
MAC Address: 02:42:C0:40:8C:06 (Unknown)
Nmap scan report for INE (192.64.140.2)
Host is up.
Nmap done: 256 IP addresses (6 hosts up) scanned in 2.06 seconds
```

我们再次得到相同的结果：6 个主机启动。但是这次输出只包含我们需要的东西，Nmap 不需要做额外的举重！

第 3 步：确定pc1.ine.local上的开放端口。

为此，我们将使用以下命令：

命令：
```
nmap -p- pc1.ine.local
```

```
root@INE:~# nmap -p- pc1.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:09 IST
Nmap scan report for pc1.ine.local (192.64.140.3)
Host is up (0.0000090s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
MAC Address: 02:42:C0:40:8C:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.18 seconds
```

**默认情况下，Nmap 只扫描前 1000 个端口，而不是扫描所有端口。** 这使得基本扫描非常快，但正如您可以想象的那样，它也会错过在 1000 个端口以外的端口上运行的服务。

因此，要获取所有开放端口的列表，最好使用上述命令扫描所有端口。

您可以发出以下命令，明确指定端口，以获得相同的结果：

命令：
```
nmap -p1-65535 pc1.ine.local
```

```
root@INE:~# nmap -p1-65535 pc1.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:11 IST
Nmap scan report for pc1.ine.local (192.64.140.3)
Host is up (0.000012s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
MAC Address: 02:42:C0:40:8C:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.19 seconds
```

它将显示与上述命令相同的输出。随意尝试一下。

现在，让我们回到分析结果。此主机上打开了 3 个端口：80 (HTTP)、443 (HTTPS)、3306 (MySQL)。

由于这些是非常常见的端口，当你在没有`-p`标志的情况下运行 Nmap 时，你仍然会得到相同的结果：

```
root@INE:~# nmap pc1.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:13 IST
Nmap scan report for pc1.ine.local (192.64.140.3)
Host is up (0.0000090s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
MAC Address: 02:42:C0:40:8C:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.21 seconds
```

这台机器是某种网络服务器。虽然暴露 MySQL 并不常见，但由于它是内部网络，它可能是可以接受的。

第 4 步：确定在pc1.ine.local机器上运行的操作系统。

我们可以使用 Nmap 使用以下`-O`标志执行操作系统检测：

命令：

```
nmap -O pc1.ine.local
```

```
root@INE:~# nmap -O pc1.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:14 IST
Nmap scan report for pc1.ine.local (192.64.140.3)
Host is up (0.000035s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
MAC Address: 02:42:C0:40:8C:03 (Unknown)
Device type: general purpose
Running: Linux 4.X|5.X
OS CPE: cpe:/o:linux:linux_kernel:4 cpe:/o:linux:linux_kernel:5
OS details: Linux 4.15 - 5.6
Network Distance: 1 hop

OS detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 1.71 seconds
```

响应表明 Linux 内核可以从版本 4.15 到 5.6。但是我们确定它是Linux！这是因为目标机器如何响应 Nmap 在操作系统检测扫描期间发送的探测。

信息：万一您想知道CPE是什么意思，它代表Common Platform Enumeration。这是一种命名软件应用程序、操作系统和硬件平台的标准化方式。有关它的更多信息，请查看[此页面](https://nmap.org/book/output-formats-cpe.html)。

第 5 步：确定在pc2.ine.local机器上运行的服务。

使用以下命令扫描pc2.ine.local：

命令：
```
nmap pc2.ine.local
```

```
root@INE:~# nmap pc2.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:18 IST
Nmap scan report for pc2.ine.local (192.64.140.4)
Host is up (0.000010s latency).
All 1000 scanned ports on pc2.ine.local (192.64.140.4) are in ignored states.
Not shown: 1000 closed tcp ports (reset)
MAC Address: 02:42:C0:40:8C:04 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.23 seconds
```

请注意，没有打开的端口。至少这就是输出所说的。但也可能是机器上有开放端口但仍未报告。这是因为 Nmap 扫描前 1000 个端口而不是所有端口（从 1 到 65535）。因此，为了获得更准确的结果，最好使用`-p-`(或者`-p1-65535`，如果您希望使命令更具可读性）。
```
nmap -p- pc2.ine.local
```

```
root@INE:~# nmap -p- pc2.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:19 IST
Nmap scan report for pc2.ine.local (192.64.140.4)
Host is up (0.0000090s latency).
Not shown: 65534 closed tcp ports (reset)
PORT      STATE SERVICE
27017/tcp open  mongod
MAC Address: 02:42:C0:40:8C:04 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 1.12 seconds
```

请注意，这次输出确实表明mongodb服务正在目标机器上运行，并且暴露在默认端口（即 27017）上。

所以到现在为止，您一定已经明白，如果您更喜欢速度而不是准确性，`-p-`则可以省略选项。但是，如果您重视准确性，那么包含`-p`标志可能是一个好主意。与默认（快速）扫描相比，这会使您的扫描速度变慢，但您会更好地了解目标机器上可用的开放端口！

所以底线是：**根据场景选择扫描类型和你指定给 Nmap 的选项。没有一种万能的选择。**

第 6 步：确定其中一台机器上运行的 FTP 服务器的版本。

使用以下命令来确定打开 21 端口的主机，并确定在该端口上运行的服务的版本（很可能是某个 FTP 服务器）：

命令：
```
nmap -p21 -sV 192.64.140.*
```

```
root@INE:~# nmap -p21 -sV 192.64.140.*
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:24 IST
Nmap scan report for linux (192.64.140.1)
Host is up (0.000030s latency).

PORT   STATE  SERVICE VERSION
21/tcp closed ftp
MAC Address: 02:42:3B:C9:86:3F (Unknown)

Nmap scan report for pc1.ine.local (192.64.140.3)
Host is up (0.000026s latency).

PORT   STATE  SERVICE VERSION
21/tcp closed ftp
MAC Address: 02:42:C0:40:8C:03 (Unknown)

Nmap scan report for pc2.ine.local (192.64.140.4)
Host is up (0.000010s latency).

PORT   STATE  SERVICE VERSION
21/tcp closed ftp
MAC Address: 02:42:C0:40:8C:04 (Unknown)

Nmap scan report for pc3.ine.local (192.64.140.5)
Host is up (0.000010s latency).

PORT   STATE  SERVICE VERSION
21/tcp closed ftp
MAC Address: 02:42:C0:40:8C:05 (Unknown)

Nmap scan report for pc4.ine.local (192.64.140.6)
Host is up (0.000028s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
MAC Address: 02:42:C0:40:8C:06 (Unknown)
Service Info: OS: Unix

Nmap scan report for INE (192.64.140.2)
Host is up (0.000051s latency).

PORT   STATE  SERVICE VERSION
21/tcp closed ftp

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 256 IP addresses (6 hosts up) scanned in 2.51 seconds
```

请注意，target-4已打开端口 21，并且它正在运行版本为3.0.3的vsftpd 服务器。

但是输出仍然太大，如果有很多主机，我们可能需要`grep`了解相关细节。但也有一个简单的方法：

命令：
```
nmap -p21 -sV 192.64.140.* --open
```

```
root@INE:~# nmap -p21 -sV 192.64.140.* --open
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:28 IST
Nmap scan report for pc4.ine.local (192.64.140.6)
Host is up (0.000019s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
MAC Address: 02:42:C0:40:8C:06 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 256 IP addresses (6 hosts up) scanned in 2.50 seconds
```

这次我们将只获取端口 21 处于打开（或可能打开）状态的主机，而不是获取所有主机，即使端口处于 `closed, filtered,`和 `closed|filtered` 状态也是如此。

信息： -
```
--open
```
将仅显示`open、open|filtered`和`unfiltered`端口。- 当 Nmap 由于包过滤阻止其探测到达端口而无法确定端口是否打开时，输出将显示`filtered`状态。-`unfiltered`状态表示端口可访问，但 Nmap 无法确定该端口是打开还是关闭。

第 7 步：确定运行缓存服务的机器的域名。

使用以下命令扫描 6 个可用主机的所有端口（1-65535）：

命令：
```
nmap -p- 192.64.140.1,2,3,4,5,6
```

```
root@INE:~# nmap -p- 192.64.140.1,2,3,4,5,6
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:31 IST
Nmap scan report for linux (192.64.140.1)
Host is up (0.0000070s latency).
Not shown: 65530 closed tcp ports (reset)
PORT      STATE    SERVICE
22/tcp    open     ssh
80/tcp    filtered http
443/tcp   filtered https
25555/tcp open     unknown
29999/tcp open     bingbang
MAC Address: 02:42:3B:C9:86:3F (Unknown)

Nmap scan report for pc1.ine.local (192.64.140.3)
Host is up (0.0000090s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
MAC Address: 02:42:C0:40:8C:03 (Unknown)

Nmap scan report for pc2.ine.local (192.64.140.4)
Host is up (0.0000090s latency).
Not shown: 65534 closed tcp ports (reset)
PORT      STATE SERVICE
27017/tcp open  mongod
MAC Address: 02:42:C0:40:8C:04 (Unknown)

Nmap scan report for pc3.ine.local (192.64.140.5)
Host is up (0.000023s latency).
Not shown: 65534 closed tcp ports (reset)
PORT      STATE SERVICE
11211/tcp open  memcache
MAC Address: 02:42:C0:40:8C:05 (Unknown)

Nmap scan report for pc4.ine.local (192.64.140.6)
Host is up (0.0000090s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
MAC Address: 02:42:C0:40:8C:06 (Unknown)

Nmap scan report for INE (192.64.140.2)
Host is up (0.0000040s latency).
Not shown: 65532 closed tcp ports (reset)
PORT      STATE SERVICE
3389/tcp  open  ms-wbt-server
5910/tcp  open  cm
45654/tcp open  unknown

Nmap done: 6 IP addresses (6 hosts up) scanned in 5.88 seconds
```

请注意，target-3正在运行memcache服务（在它的默认端口 11211 上）。

为了确定域名，我们可以检查提供的主机上的`/etc/hosts`文件：

命令：

```
cat /etc/hosts
```

```
root@INE:~# cat /etc/hosts
127.0.0.1       localhost
::1     localhost ip6-localhost ip6-loopback
fe00::0 ip6-localnet
ff00::0 ip6-mcastprefix
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
10.1.0.11       INE
127.0.0.1 AttackDefense-Kali
192.64.140.2    INE
192.64.140.2 INE
192.64.140.3 pc1.ine.local
192.64.140.4 pc2.ine.local
192.64.140.5 pc3.ine.local
192.64.140.6 pc4.ine.local
```

第 8 步：确定运行 SQL 和 NoSQL 数据库服务的主机。

使用以下命令扫描 6 个可用主机的所有端口（1-65535）：

命令：

```
nmap -p- 192.64.140.1,2,3,4,5,6
```

```
root@INE:~# nmap -p- 192.64.140.1,2,3,4,5,6
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:39 IST
Nmap scan report for linux (192.64.140.1)
Host is up (0.0000070s latency).
Not shown: 65530 closed tcp ports (reset)
PORT      STATE    SERVICE
22/tcp    open     ssh
80/tcp    filtered http
443/tcp   filtered https
25555/tcp open     unknown
29999/tcp open     bingbang
MAC Address: 02:42:3B:C9:86:3F (Unknown)

Nmap scan report for pc1.ine.local (192.64.140.3)
Host is up (0.0000090s latency).
Not shown: 65532 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
MAC Address: 02:42:C0:40:8C:03 (Unknown)

Nmap scan report for pc2.ine.local (192.64.140.4)
Host is up (0.0000090s latency).
Not shown: 65534 closed tcp ports (reset)
PORT      STATE SERVICE
27017/tcp open  mongod
MAC Address: 02:42:C0:40:8C:04 (Unknown)

Nmap scan report for pc3.ine.local (192.64.140.5)
Host is up (0.0000090s latency).
Not shown: 65534 closed tcp ports (reset)
PORT      STATE SERVICE
11211/tcp open  memcache
MAC Address: 02:42:C0:40:8C:05 (Unknown)

Nmap scan report for pc4.ine.local (192.64.140.6)
Host is up (0.0000090s latency).
Not shown: 65534 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
MAC Address: 02:42:C0:40:8C:06 (Unknown)

Nmap scan report for INE (192.64.140.2)
Host is up (0.0000040s latency).
Not shown: 65532 closed tcp ports (reset)
PORT      STATE SERVICE
3389/tcp  open  ms-wbt-server
5910/tcp  open  cm
45654/tcp open  unknown

Nmap done: 6 IP addresses (6 hosts up) scanned in 5.72 seconds
```

请注意MySQL在target-1上运行，而mongod在target-2上运行。

第 9 步：使用 Nmap 脚本确定有关 NoSQL 数据库 ( MongoDB ) 的信息。

命令：

```
nmap -p27017 --script=mongodb-info target-2 | less
```

```
Nmap scan report for pc2.ine.local (192.64.140.4)
Host is up (0.000046s latency).

PORT      STATE SERVICE
27017/tcp open  mongodb
| mongodb-info: 
|   MongoDB Build info
|     versionArray
|       1 = 6
|       0 = 3
|       3 = 0
|       2 = 3
|     allocator = tcmalloc
|     modules
|     version = 3.6.3
|     maxBsonObjectSize = 16777216
|     storageEngines
|       1 = ephemeralForTest
|       0 = devnull
|       3 = wiredTiger
|       2 = mmapv1
|     ok = 1.0
|     javascriptEngine = mozjs
|     debug = false
|     gitVersion = 9586e557d54ef70f9ca4b43c26892cd55257e1a5
|     buildEnvironment
```
请注意，Nmap 确实提供了有关 MongoDB 的非常详细的信息。这些详细信息还包括 MongoDB 的版本，这对渗透测试者非常有益，以确定已安装的 MongoDB 版本是否容易受到任何现有 CVE 的攻击！

运行另一个 Nmap 脚本以从 MongoDB 数据库中获取所有数据库的列表：

命令：
```
nmap -p27017 --script=mongodb-databases pc2.ine.local
```

```
root@INE:~# nmap -p27017 --script=mongodb-databases pc2.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:48 IST
Nmap scan report for pc2.ine.local (192.64.140.4)
Host is up (0.000070s latency).

PORT      STATE SERVICE
27017/tcp open  mongodb
| mongodb-databases: 
|   totalSize = 1867776.0
|   databases
|     3
|       sizeOnDisk = 32768.0
|       name = local
|       empty = false
|     0
|       sizeOnDisk = 32768.0
|       name = admin
|       empty = false
|     1
|       sizeOnDisk = 1789952.0
|       name = city
|       empty = false
|     2
|       sizeOnDisk = 12288.0
|       name = config
|       empty = false
|_  ok = 1.0
MAC Address: 02:42:C0:40:8C:04 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.38 seconds
```

输出列出了 4 个数据库： - admin - city - config - local

运行另一个 Nmap 脚本来对 MongoDB 数据库执行暴力密码审计：

命令：
```
nmap -p27017 --script=mongodb-brute pc2.ine.local
```

```
root@INE:~# nmap -p27017 --script=mongodb-brute pc2.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:50 IST
Nmap scan report for pc2.ine.local (192.64.140.4)
Host is up (0.000053s latency).

PORT      STATE SERVICE
27017/tcp open  mongod
|_mongodb-brute: No authentication needed
MAC Address: 02:42:C0:40:8C:04 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 0.35 seconds
```

输出表明不需要身份验证。这就是为什么我们使用mongodb-databases获得数据库列表而不需要暴力破解任何凭据的原因！

第 10 步：使用 Nmap 脚本确定有关 SQL 数据库 ( MySQL ) 的信息。

正如我们在上一步中看到的，我们可以使用适当的 Nmap 脚本来获取有关不同服务的详细信息，甚至执行攻击。

有时，可能需要为特定类别运行所有可用的 NSE 脚本，而不是一次运行一个。所以让我们运行所有与 MySQL 相关的 Nmap 脚本：

命令：

```
nmap --script=mysql-* pc1.ine.local
```

```
root@INE:~# nmap --script=mysql-* pc1.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:53 IST
Nmap scan report for pc1.ine.local (192.64.140.3)
Host is up (0.0000090s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
| mysql-enum: 
|   Valid usernames: 
|     root:<empty> - Valid credentials
|     netadmin:<empty> - Valid credentials
|     guest:<empty> - Valid credentials
|     user:<empty> - Valid credentials
|     web:<empty> - Valid credentials
|     sysadmin:<empty> - Valid credentials
|     administrator:<empty> - Valid credentials
|     webadmin:<empty> - Valid credentials
|     admin:<empty> - Valid credentials
|     test:<empty> - Valid credentials
|_  Statistics: Performed 10 guesses in 1 seconds, average tps: 10.0
| mysql-info: 
|   Protocol: 10
|   Version: 5.5.47-0ubuntu0.14.04.1
|   Thread ID: 3
|   Capabilities flags: 63487
|   Some Capabilities: LongColumnFlag, Support41Auth, ConnectWithDatabase, InteractiveClient, Speaks41ProtocolOld, SupportsTransactions, IgnoreSpaceBeforeParenthesis, SupportsCompression, IgnoreSigpipes, ODBCClient, SupportsLoadDataLocal, Speaks41ProtocolNew, LongPassword, DontAllowDatabaseTableColumn, FoundRows, SupportsMultipleResults, SupportsAuthPlugins, SupportsMultipleStatments
|   Status: Autocommit
|   Salt: l?5+v4<rJX^MIeBXtahg
|_  Auth Plugin Name: mysql_native_password
| mysql-brute: 
|   Accounts: No valid accounts found
|_  Statistics: Performed 50009 guesses in 11 seconds, average tps: 4546.3
MAC Address: 02:42:C0:40:8C:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 11.44 seconds
```

请注意，输出包含多个 MySQL 相关 Nmap 脚本提供的详细信息。这是因为我们指定了通配符 (`*`) 来告诉 Nmap 运行所有与 MySQL 相关的脚本。

在这种情况下运行了以下脚本： - mysql-brute - mysql-enum - mysql-info

这些收集了很多有趣的信息！这对渗透测试者来说非常有益，因为了解你的目标是第一步。

但是有一个警告——使用通配符可能并不总是一个好主意。再次尝试上面的命令，看看会发生什么：

命令：

```
nmap --script=mysql-* pc1.ine.local
```

```
root@INE:~# nmap --script=mysql-* pc1.ine.local
Starting Nmap 7.92 ( https://nmap.org ) at 2022-06-19 23:59 IST
Nmap scan report for pc1.ine.local (192.64.140.3)
Host is up (0.0000090s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE
80/tcp   open  http
443/tcp  open  https
3306/tcp open  mysql
|_mysql-empty-password: Host 'INE' is blocked because of many connection errors; unblock with 'mysqladmin flush-hosts'
|_mysql-vuln-cve2012-2122: ERROR: Script execution failed (use -d to debug)
| mysql-enum: 
|   Accounts: No valid accounts found
|   Statistics: Performed 5 guesses in 1 seconds, average tps: 5.0
|_  ERROR: Host 'INE' is blocked because of many connection errors; unblock with 'mysqladmin flush-hosts'
| mysql-brute: 
|   Accounts: No valid accounts found
|_  Statistics: Performed 50009 guesses in 6 seconds, average tps: 8334.8
MAC Address: 02:42:C0:40:8C:03 (Unknown)

Nmap done: 1 IP address (1 host up) scanned in 6.36 seconds
```

这一次我们没有得到之前扫描提供的所有信息。发生这种情况只是因为 Nmap 向 MySQL 发送了相当多的请求，其中许多请求导致连接错误。并且安装在该主机上的 MySQL 被配置为阻止发送大量请求的主机，这可能表示暴力攻击。

所以不建议使用通配符扫描，因为这会产生很多噪音，甚至阻止您的主机发出进一步的请求。

这就是我们如何使用 Nmap 对主机进行扫描和指纹识别等等！