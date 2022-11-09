---
layout: post
title: "Filtering Basics"
date: "2022-11-09"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Network-Based Attacks
---

# Filtering Basics
apply a filter with `-Y`:

```
tshark -r HTTP_traffic.pcap -Y 'http'
tshark -r HTTP_traffic.pcap -Y 'http' | more
```

```
tshark -r HTTP_traffic.pcap -Y 'ip.src==192.168.252.128 && ip.dst==52.32.74.91'
```

```
tshark -r HTTP_traffic.pcap -Y 'http.request.method==GET'
tshark -r HTTP_traffic.pcap -Y 'http.request.method==GET' | more
```

```
tshark -r HTTP_traffic.pcap -Y 'http.request.method==GET' -Tfields -e frame.time -e ip.src -e http.request.full_uri
tshark -r HTTP_traffic.pcap -Y 'http.request.method==GET' -Tfields -e frame.time -e ip.src -e http.request.full_url | more
```

`contains` does a regular expression.

```
tshark -r HTTP_traffic.pcap -Y 'http contains password'
```

```
tshark -r HTTP_traffic.pcap -Y 'http.request.method==GET && http.host==www.nytimes.com' -Tfields -e ip.dst
```

```
tshark -r HTTP_traffic.pcap -Y 'ip contains amazon.in && ip.src==192.168.252.128' -Tfields -e ip.src -e http.cookie
```

```
tshark -r HTTP_traffic.pcap -Y 'ip.src==192.168.252.128 && http' -Tfields -e http.user_agent
```

# Filtering Basics: HTTP
## 概述
HTTP可能是 Internet 上最常见和最广泛使用的协议。不幸的是，HTTP 是纯文本，可以访问网络的攻击者可以毫不费力地嗅探和读取数据包中的数据。在本实验中，您将使用 tshark 从 HTTP 流量中分析和提取有趣的信息。

启动lab，找到当前目录下的PCAP文件，使用tshark回答以下问题：

问题：

设置A：

1. 命令仅显示来自 PCAP 文件的 HTTP 流量？
2. 仅显示从 IP 地址 192.168.252.128 发送到 IP 地址 52.32.74.91 的 IP 数据包的命令？
3. 仅打印包含 GET 请求的数据包的命令？
4. 命令仅打印所有 GET 请求数据包的数据包源 IP 和 URL？

设置 B:

1. 有多少 HTTP 数据包包含“密码”字符串？
2. 为纽约时报 (www.nytimes.com) 发送的 GET 请求的目标 IP 地址是什么？
3. 192.168.252.128 用于亚马逊印度商店 (amazon.in) 的会话 ID 是什么？
4. IP 地址为 192.168.252.128 的机器使用什么类型的操作系统（即 Windows/Linux/MacOS/Solaris/Unix/BSD）？奖励：你也能猜出分布/味道吗？

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-2.pdf

[tshark](https://www.wireshark.org/docs/man-pages/tshark.html)

[wireshark](https://www.wireshark.org/)

## 我自己的思路
HTTP is probably the most common and widely used protocol on the Internet. Unfortunately, HTTP is plain text and an attacker having access to the network can sniff and read the data within the packets effortlessly. In this lab, you will analyze and extract interesting information from the HTTP traffic using tshark.

Start the lab, locate the PCAP file in the current directory and use tshark to answer the following questions:

Questions:

Set A:
* Command to show only the HTTP traffic from a PCAP file?

```
-Y|--display-filter <displaY filter>
Cause the specified filter (which uses the syntax of read/display filters, rather than that of capture filters) to be applied before printing a decoded form of packets or writing packets to a file. Packets matching the filter are printed or written to file; packets that the matching packets depend upon (e.g., fragments), are not printed but are written to file; packets not matching the filter nor depended upon are discarded rather than being printed or written.

Use this instead of -R for filtering using single-pass analysis. If doing two-pass analysis (see -2) then only packets matching the read filter (if there is one) will be checked against this filter.
```

```
-Y|--display-filter <显示过滤器>
导致在打印数据包的解码形式或将数据包写入文件之前应用指定的过滤器（使用读取/显示过滤器的语法，而不是捕获过滤器的语法）。匹配过滤器的数据包被打印或写入文件；匹配数据包所依赖的数据包（例如，片段）不打印，而是写入文件；不匹配过滤器也不依赖的数据包将被丢弃，而不是被打印或写入。

使用它而不是 -R 来使用单遍分析进行过滤。如果进行两遍分析（参见-2），那么只有匹配读取过滤器（如果有）的数据包将根据该过滤器进行检查。
```

```
student@attackdefense:~$ tshark -r HTTP_traffic.pcap -Y 'http' | more
   30   4.166998 192.168.252.128 → 54.239.32.8  HTTP 904 GET / HTTP/1.1
   38   4.168852 192.168.252.128 → 54.239.39.114 HTTP 1150 POST /1/batch/1/OE/ HTTP/1.1  (text/plain)
   46   4.472330 54.239.39.114 → 192.168.252.128 HTTP 431 HTTP/1.1 204 No Content  146   5.060440 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/412maSA0fuL._AC_SY200_.jpg HTTP/1.1  150   5.062201 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/41wuVnAeedL._AC_SY200_.jpg HTTP/1.1  158   5.068393 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/412OwlOMxPL._AC_SY200_.jpg HTTP/1.1
  159   5.068453 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/316zh6kkqML._AC_SY200_.jpg HTTP/1.1
  162   5.068562 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/41O1oRMP6pL._AC_SY200_.jpg HTTP/1.1
  166   5.069236 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/41w3V6ilQPL._AC_SY200_.jpg HTTP/1.1
  189   5.244323 52.84.108.225 → 192.168.252.128 HTTP 6840 HTTP/1.1 200 OK  (JPEG JFIF image)
```

* Command to show only the IP packets sent from IP address 192.168.252.128 to IP address 52.32.74.91?

```
student@attackdefense:~$ tshark -r HTTP_traffic.pcap -Y 'ip.src==192.168.252.128 && ip.dst==52.32.74.91' | more
24168 144.928157 192.168.252.128 → 52.32.74.91  TCP 74 48544 → 80 [SYN] Seq=0 Win=29200 Len=0 MSS=1460 SACK_PERM=1 TSval=837027 TSecr=0 WS=102424214 145.179880 192.168.252.128 → 52.32.74.91  TCP 74 48546 → 80 [SYN] Seq=0 Win=29200 Len=0 MSS=1460 SACK_PERM=1 TSval=837089 TSecr=0 WS=102424233 145.236767 192.168.252.128 → 52.32.74.91  TCP 54 48544 → 80 [ACK] Seq=1 Ack=1 Win=29200 Len=024241 145.475893 192.168.252.128 → 52.32.74.91  TCP 54 48546 → 80 [ACK] Seq=1 Ack=1 Win=29200 Len=0
24276 145.878151 192.168.252.128 → 52.32.74.91  HTTP 493 GET /420046.gif?partner_uid=o9JecfYE-vSFC1OCbXDJnra2LQPPRlM1ETKuEF9Onaz1rdbviK35w2TZ9SGQ7pfg HTTP/1.1
24312 146.203489 192.168.252.128 → 52.32.74.91  TCP 54 48544 → 80 [ACK] Seq=440 Ack=500 Win=30016 Len=0
24645 147.877429 192.168.252.128 → 52.32.74.91  HTTP 521 GET /420046.gif?partner_uid=o9JecfYE-vSFC1OCbXDJnra2LQPPRlM1ETKuEF9Onaz1rdbviK35w2TZ9SGQ7pfg&redirect=1 HTTP/1.1
24772 148.196810 192.168.252.128 → 52.32.74.91  TCP 54 48546 → 80 [ACK] Seq=468 Ack=1119 Win=31304 Len=026345 156.200222 192.168.252.128 → 52.32.74.91  TCP 54 [TCP Keep-Alive] 48544 → 80 [ACK] Seq=439 Ack=500 Win=30016 Len=0
26835 158.196558 192.168.252.128 → 52.32.74.91  TCP 54 [TCP Keep-Alive] 48546 → 80 [ACK] Seq=467 Ack=1119 Win=31304 Len=029102 166.228525 192.168.252.128 → 52.32.74.91  TCP 54 [TCP Keep-Alive] 48544 → 80 [ACK] Seq=439 Ack=500 Win=30016 Len=0
29225 168.211953 192.168.252.128 → 52.32.74.91  TCP 54 [TCP Keep-Alive] 48546 → 80 [ACK] Seq=467 Ack=1119 Win=31304 Len=0
29853 173.206316 192.168.252.128 → 52.32.74.91  TCP 54 48544 → 80 [FIN, ACK] Seq=440 Ack=500 Win=30016 Len=029954 173.228854 192.168.252.128 → 52.32.74.91  TCP 54 48546 → 80 [FIN, ACK] Seq=468 Ack=1119 Win=31304 Len=030364 173.610539 192.168.252.128 → 52.32.74.91  TCP 54 48546 → 80 [ACK] Seq=469 Ack=1120 Win=31304 Len=030412 174.324657 192.168.252.128 → 52.32.74.91  TCP 54 48544 → 80 [ACK] Seq=441 Ack=501 Win=30016 Len=0
```

* Command to print only packets containing GET requests?

```
student@attackdefense:~$ tshark -r HTTP_traffic.pcap -Y 'http.request.method == "GET"' | more
   30   4.166998 192.168.252.128 → 54.239.32.8  HTTP 904 GET / HTTP/1.1  146   5.060440 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/412maSA0fuL._AC_SY200_.jpg HTTP/1.1  150   5.062201 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/41wuVnAeedL._AC_SY200_.jpg HTTP/1.1  158   5.068393 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/412OwlOMxPL._AC_SY200_.jpg HTTP/1.1
  159   5.068453 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/316zh6kkqML._AC_SY200_.jpg HTTP/1.1
  162   5.068562 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/41O1oRMP6pL._AC_SY200_.jpg HTTP/1.1
  166   5.069236 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/41w3V6ilQPL._AC_SY200_.jpg HTTP/1.1
  191   5.244536 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/41wEkEluHYL._AC_SY200_.jpg HTTP/1.1  199   5.260230 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/21RJXZUGwJL._AC_SY200_.jpg HTTP/1.1
  209   5.268489 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/51FKbs7rlkL._AC_SY200_.jpg HTTP/1.1  215   5.297530 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/51WCISldn8L._AC_SY200_.jpg HTTP/1.1
  227   5.311214 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/41VsfYu1RuL._AC_SY200_.jpg HTTP/1.1
  228   5.311295 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/31PoS1o0AbL._AC_SY200_.jpg HTTP/1.1  236   5.359014 192.168.252.128 → 52.84.108.225 HTTP 399 GET /images/I/414KNwJQbEL._AC_SY200_.jpg HTTP/1.1  245   5.400521 192.168.252.128 → 52.84.108.225 HTTP 401 GET /images/I/41eCI%2Bcj1nL._AC_SY200_.jpg HTTP/1.1
```

* Command to print only packets only source IP and URL for all GET request packets?

```
-e <field>
Add a field to the list of fields to display if -T ek|fields|json|pdml is selected. This option can be used multiple times on the command line. At least one field must be provided if the -T fields option is selected. Column names may be used prefixed with "_ws.col."

Example: tshark -e frame.number -e ip.addr -e udp -e _ws.col.Info

Fields are separated by tab characters by default. -E controls the format of the printed fields. Giving a protocol rather than a single field will print the protocol summary (subtree label) from the packet details as a single field. If the protocol summary contains only the protocol name (e.g. "Hypertext Transfer Protocol") then the protocol filter name ("http") will be printed.
```

```
-e <字段>
如果选择了-T ek|fields|json|pdml，则将一个字段添加到要显示的字段列表中 。该选项可以在命令行中多次使用。如果选择了-T fields选项，则必须至少提供一个字段。列名可以使用前缀“_ws.col”。

示例：tshark -e frame.number -e ip.addr -e udp -e _ws.col.Info

默认情况下，字段由制表符分隔。 -E控制打印字段的格式。提供协议而不是单个字段将从数据包详细信息中打印协议摘要（子树标签）作为单个字段。如果协议摘要仅包含协议名称（例如“超文本传输​​协议”），则将打印协议过滤器名称（“http”）。
```

```
-T ek|fields|json|jsonraw|pdml|ps|psml|tabs|text
Set the format of the output when viewing decoded packet data. The options are one of:
fields The values of fields specified with the -e option, in a form specified by the -E option. For example,

tshark -T fields -E separator=, -E quote=d
would generate comma-separated values (CSV) output suitable for importing into your favorite spreadsheet program.
```

```
-T ek|fields|json|jsonraw|pdml|ps|psml|tabs|text
查看解码的数据包数据时设置输出的格式。选项是以下之一：
fields使用-e选项指定的字段值，采用-E选项指定的格式。例如，

tshark -T 字段 -E 分隔符=, -E 引用=d
将生成适合导入您喜欢的电子表格程序的逗号分隔值 (CSV) 输出。
```

```
student@attackdefense:~$ tshark -r HTTP_traffic.pcap -Y 'http.request.method == "GET"' -T fields -e frame.time -e ip.src -e http.request.full_uri | moreJun 20, 2016 07:38:28.678418000 UTC     192.168.252.128 http://www.amazon.in/Jun 20, 2016 07:38:29.571860000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/412maSA0fuL._AC_SY200_.jpg
Jun 20, 2016 07:38:29.573621000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/41wuVnAeedL._AC_SY200_.jpg
Jun 20, 2016 07:38:29.579813000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/412OwlOMxPL._AC_SY200_.jpg
Jun 20, 2016 07:38:29.579873000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/316zh6kkqML._AC_SY200_.jpg
Jun 20, 2016 07:38:29.579982000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/41O1oRMP6pL._AC_SY200_.jpgJun 20, 2016 07:38:29.580656000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/41w3V6ilQPL._AC_SY200_.jpg
Jun 20, 2016 07:38:29.755956000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/41wEkEluHYL._AC_SY200_.jpgJun 20, 2016 07:38:29.771650000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/21RJXZUGwJL._AC_SY200_.jpg
Jun 20, 2016 07:38:29.779909000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/51FKbs7rlkL._AC_SY200_.jpg
Jun 20, 2016 07:38:29.808950000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/51WCISldn8L._AC_SY200_.jpgJun 20, 2016 07:38:29.822634000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/41VsfYu1RuL._AC_SY200_.jpgJun 20, 2016 07:38:29.822715000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/31PoS1o0AbL._AC_SY200_.jpgJun 20, 2016 07:38:29.870434000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/414KNwJQbEL._AC_SY200_.jpgJun 20, 2016 07:38:29.911941000 UTC     192.168.252.128 http://ecx.images-amazon.com/images/I/41eCI%2Bcj1nL._AC_SY200_.jpg
```

Set B:
* How many HTTP packets contain the "password" string?

```
student@attackdefense:~$ tshark -r HTTP_traffic.pcap -Y 'http contains password'
12866  99.041118 52.84.108.101 ? 192.168.252.128 HTTP 1573 HTTP/1.1 200 OK  (text/javascript)
13089  99.686253 52.84.108.101 ? 192.168.252.128 HTTP 7905 HTTP/1.1 200 OK  (text/css)
13173 100.072333 52.84.108.101 ? 192.168.252.128 HTTP 12836 HTTP/1.1 200 OK  (text/javascript)
13185 100.077752 52.84.108.101 ? 192.168.252.128 HTTP 5527 HTTP/1.1 200 OK  (text/javascript)
```

* What is the destination IP address for GET requests sent for New York Times (www.nytimes.com)?

```
student@attackdefense:~$ tshark -r HTTP_traffic.pcap -Y 'http.request.method == "GET" && http.host == "www.nytimes.com"' -T fields -e ip.dst
170.149.159.130
170.149.159.130
170.149.159.130170.149.159.130
170.149.159.130
```

* What is the session ID being used by 192.168.252.128 for Amazon India store (amazon.in)?

`session-id=278-7381968-4337153`

```
student@attackdefense:~$ tshark -r HTTP_traffic.pcap -Y 'ip.src == 192.168.252.128 && http contains "amazon.in"' -T fields -e http.cookiex-wl-uid=1YUrrvyo2aOwaC2tX1u3CL5JwhNCwEZhfsOUf8932b9zxC9BkYOYTKpVuh02IxmGM3Gs2/XgdUCA=; session-id-time=2082758401l; session-id=278-7381968-4337153; csm-hit=0JAE5VRXPMH77731K1TX+s-0JAE5VR
XPMH77731K1TX|1466408308416; visitCount=2; ubid-acbin=280-4213374-9863463; lc-acbin=en_IN; session-token=4pTTa7bIe2i6bm7hJhGt4Jp7Mr2r5jgqscUc9YZTkXxjaaP+H+ezTpZLgyH8KjFSbiwETGfn0kOVzX5WUyryAQphMTcttvLjvBRVEBmw0UkKdZhVIoiDIT1EdQPUzTnfJDAQCKzdVpEGdKxlOlU+rQw+L2ZCE5eMBIZ2ip7xXq3PMsOCq+k2RSZ+4wh50U4EawgJPj7CaidkmVdFLbn0WrJKQw1f9hnd82LtRSDccz8FXsH8ksdKEQ==x-wl-uid=1YUrrvyo2aOwaC2tX1u3CL5JwhNCwEZhfsOUf8932b9zxC9BkYOYTKpVuh02IxmGM3Gs2/XgdUCA=; session-id-time=2082758401l; session-id=278-7381968-4337153; visitCount=2; ubid-acbin=280-4213374-9863463; lc-acbin=en_IN; session-token=4pTTa7bIe2i6bm7hJhGt4Jp7Mr2r5jgqscUc9YZTkXxjaaP+H+ezTpZLgyH8KjFSbiwETGfn0kOVzX5WUyryAQphMTcttvLjvBRVEBmw0UkKdZhVIoiDIT1EdQPUzTnfJDAQCKzdVpEGdKxlOlU+rQw+L2ZCE5eMBIZ2ip7xXq3PMsOCq+k2RSZ+4wh50U4EawgJPj7CaidkmVdFLbn0WrJKQw1f9hnd82LtRSDccz8FXsH8ksdKEQ==

ad-id=A4j_ufxFTkvnjjO3TcT5SL8; ad-privacy=0

x-wl-uid=1YUrrvyo2aOwaC2tX1u3CL5JwhNCwEZhfsOUf8932b9zxC9BkYOYTKpVuh02IxmGM3Gs2/XgdUCA=; session-id-time=2082758401l; session-id=278-7381968-4337153; visitCount=2; ubid-acbin=280-4213374-9863463; lc-acbin=en_IN; session-token=4pTTa7bIe2i6bm7hJhGt4Jp7Mr2r5jgqscUc9YZTkXxjaaP+H+ezTpZLgyH8KjFSbiwETGfn0kOVzX5WUyryAQphMTcttvLjvBRVEBmw0UkKdZhVIoiDIT1EdQPUzTnfJDAQCKzdVpEGdKxlOlU+rQw+L2ZCE5eMBIZ2ip7xXq3PMsOCq+k2RSZ+4wh50U4EawgJPj7CaidkmVdFLbn0WrJKQw1f9hnd82LtRSDccz8FXsH8ksdKEQ==
```

* What type of OS the machine on IP address 192.168.252.128 is using (i.e. Windows/Linux/MacOS/Solaris/Unix/BSD)? Bonus: Can you also guess the distribution/flavor?

```
student@attackdefense:~$ tshark -r HTTP_traffic.pcap -Y 'ip.src == 192.168.252.128' -T fields -e http.user_agent
Mozilla/5.0 (X11; Linux x86_64; rv:31.0) Gecko/20100101 Firefox/31.0 Iceweasel/31.8.0
Mozilla/5.0 (X11; Linux x86_64; rv:31.0) Gecko/20100101 Firefox/31.0 Iceweasel/31.8.0
```