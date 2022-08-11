---
layout: post
title: "DNS Zone Transfers"
date: "2022-08-11"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Assessment Methodologies
    - Information Gathering
    - Active Information Gathering
---

# DNS Zone Transfers
## DNS
域名系统 (DNS) 是一种用于将域名/主机名解析为 IP 地址的协议。
在互联网的早期，用户必须记住他们想要访问的站点的 IP 地址，DNS 通过将域名（更容易记住）映射到他们各自的 IP 地址来解决这个问题。
DNS 服务器（名称服务器）就像一个电话簿，其中包含域名及其对应的 IP 地址。
Cloudflare (1.1.1.1) 和 Google (8.8.8.8) 等公司已经建立了大量的公共 DNS 服务器。 这些 DNS 服务器包含 Internet 上几乎所有域的记录。

## DNS Records
* `A` - 将主机名或域解析为 IPv4 地址。
* `AAAA` - 将主机名或域解析为 IPv6 地址。
* `NS` - 对域名服务器的引用。
* `MX` - 将域解析为邮件服务器。
* `CNAME` - 用于域别名。
* `TXT` - 文本记录。
* `HINFO` - 主机信息。
* `SOA` - 域权限。
* `SRV` - 服务记录。
* `PTR` - 将 IP 地址解析为主机名。

## DNS Interrogation（DNS 询问）
DNS 询问是枚举特定域的 DNS 记录的过程。
DNS 询问的目的是探测 DNS 服务器，为我们提供特定域的 DNS 记录。
此过程可以提供重要信息，例如域的 IP 地址、子域、邮件服务器地址等。

## DNS Zone Transfer（DNS 区域传输）
在某些情况下，DNS 服务器管理员可能希望将区域文件从一台 DNS 服务器复制或传输到另一台。 这个过程被称为区域传输。
如果配置错误且不安全，攻击者可能会滥用此功能将区域文件从主 DNS 服务器复制到另一个 DNS 服务器。
DNS 区域传输可以为渗透测试人员提供组织网络布局的整体视图。
此外，在某些情况下，可能会在组织的 DNS 服务器上找到内部网络地址。

## Practical Demo（实际演示）

目标：[ZoneTransfer.me](https://digi.ninja/projects/zonetransferme.php)

访问：[DNSdumpster.com](https://dnsdumpster.com/)

输入：`zonetransfer.me`

或者使用`dnsrecon`获得相同的信息：

```
└─# dnsrecon -d zonetransfer.me
[*] std: Performing General Enumeration against: zonetransfer.me...
[-] DNSSEC is not configured for zonetransfer.me
[*]      SOA nsztm1.digi.ninja 81.4.108.41
[*]      NS nsztm1.digi.ninja 81.4.108.41
[-]      Recursion enabled on NS Server 81.4.108.41
[*]      NS nsztm2.digi.ninja 34.225.33.2
[-]      Recursion enabled on NS Server 34.225.33.2
[*]      MX ASPMX3.GOOGLEMAIL.COM 142.250.115.26
[*]      MX ASPMX5.GOOGLEMAIL.COM 142.250.152.27
[*]      MX ASPMX4.GOOGLEMAIL.COM 64.233.171.27
[*]      MX ASPMX.L.GOOGLE.COM 142.250.157.27
[*]      MX ALT1.ASPMX.L.GOOGLE.COM 142.250.141.26
[*]      MX ASPMX2.GOOGLEMAIL.COM 142.250.141.27
[*]      MX ALT2.ASPMX.L.GOOGLE.COM 142.250.115.27
[*]      A zonetransfer.me 5.196.105.14
[*]      TXT zonetransfer.me google-site-verification=tyP28J7JAUHA9fw2sHXMgcCC0I6XBmmoVi04VlMewxA
[*] Enumerating SRV Records
[+]      SRV _sip._tcp.zonetransfer.me www.zonetransfer.me 5.196.105.14 5060
[+] 1 Records Found
```

[dnsenum](https://www.kali.org/tools/dnsenum/)

```
root@kali:~# dnsenum -h
dnsenum 版本：1.2.6
用法：dnsenum [选项] <域>
[选项]：
注意：如果没有提供 -f 标签，则默认为 /usr/share/dnsenum/dns.txt 或
dns.txt 文件在与 dnsenum.pl 相同的目录中
常规选项：
  --dnsserver <服务器>
将此 DNS 服务器用于 A、NS 和 MX 查询。
  --enum 快捷方式选项等同于 --threads 5 -s 15 -w。
  -h, --help 打印此帮助信息。
  --noreverse 跳过反向查找操作。
  --nocolor 禁用 ANSIColor 输出。
  --private 在文件 domain_ips.txt 的末尾显示并保存私有 ip。
  --subfile <file> 将所有有效的子域写入此文件。
  -t, --timeout <value> 以秒为单位的 tcp 和 udp 超时值（默认值：10s）。
  --threads <值> 将执行不同查询的线程数。
  -v, --verbose 详细：显示所有进度和所有错误消息。
谷歌抓取选项：
  -p, --pages <value> 抓取名称时要处理的谷歌搜索页面数，
默认为 5 页，必须指定 -s 开关。
  -s, --scrap <value> 将从 Google 抓取的子域的最大数量（默认 15）。
蛮力选项：
  -f, --file <file> 从此文件中读取子域以执行暴力破解。 （优先于默认的 dns.txt）
  -u, --update <a|g|r|z>
使用有效的子域更新使用 -f 开关指定的文件。
a (all) 使用所有结果进行更新。
g 仅使用谷歌抓取结果进行更新。
r 仅使用反向查找结果进行更新。
z 仅使用 zonetransfer 结果进行更新。
  -r, --recursion 在子域上递归，暴力破解所有发现的具有 NS 记录的子域。
WHOIS 网络选项：
  -d, --delay <value> whois查询之间等待的最大值，该值随机定义，默认：3s。
  -w, --whois 对 c 类网络范围执行 whois 查询。
**警告**：这会产生非常大的网络范围，并且执行反向查找需要大量时间。
反向查找选项：
  -e, --exclude <正则表达式>
从反向查找结果中排除与正则表达式匹配的 PTR 记录，这对无效主机名很有用。
输出选项：
  -o --output <file> 以 XML 格式输出。可以在 MagicTree (www.gremwell.com) 中导入
```

`vim /etc/hosts`

```
127.0.0.1	localhost
127.0.1.1	kali

# The following lines are desirable for IPv6 capable hosts
::1     localhost ip6-localhost ip6-loopback
ff02::1 ip6-allnodes
ff02::2 ip6-allrouters
```

The DNS zone transfer functionality must to be enabled on anyone of those nameservers, primaryly the Primay DNS server or nameserver.

```
┌──(root㉿kali)-[~]
└─# dnsenum zonetransfer.me    
dnsenum VERSION:1.2.6

-----   zonetransfer.me   -----


Host's addresses:
__________________

zonetransfer.me.                         5        IN    A        5.196.105.14


Name Servers:
______________

nsztm1.digi.ninja.                       5        IN    A        81.4.108.41
nsztm2.digi.ninja.                       5        IN    A        34.225.33.2


Mail (MX) Servers:
___________________

ASPMX3.GOOGLEMAIL.COM.                   5        IN    A        142.250.115.26
ASPMX5.GOOGLEMAIL.COM.                   5        IN    A        142.250.152.26
ASPMX4.GOOGLEMAIL.COM.                   5        IN    A        64.233.171.26
ASPMX.L.GOOGLE.COM.                      5        IN    A        64.233.188.26
ALT1.ASPMX.L.GOOGLE.COM.                 5        IN    A        142.250.141.27
ASPMX2.GOOGLEMAIL.COM.                   5        IN    A        142.250.141.27
ALT2.ASPMX.L.GOOGLE.COM.                 5        IN    A        142.250.115.26


Trying Zone Transfers and getting Bind Versions:
_________________________________________________


Trying Zone Transfer for zonetransfer.me on nsztm1.digi.ninja ... 
zonetransfer.me.                         7200     IN    SOA               (
zonetransfer.me.                         300      IN    HINFO        "Casio
zonetransfer.me.                         301      IN    TXT               (
zonetransfer.me.                         7200     IN    MX                0
zonetransfer.me.                         7200     IN    MX               10
zonetransfer.me.                         7200     IN    MX               10
zonetransfer.me.                         7200     IN    MX               20
zonetransfer.me.                         7200     IN    MX               20
zonetransfer.me.                         7200     IN    MX               20
zonetransfer.me.                         7200     IN    MX               20
zonetransfer.me.                         7200     IN    A        5.196.105.14
zonetransfer.me.                         7200     IN    NS       nsztm1.digi.ninja.
zonetransfer.me.                         7200     IN    NS       nsztm2.digi.ninja.
_acme-challenge.zonetransfer.me.         301      IN    TXT               (
_sip._tcp.zonetransfer.me.               14000    IN    SRV               0
14.105.196.5.IN-ADDR.ARPA.zonetransfer.me. 7200     IN    PTR      www.zonetransfer.me.
asfdbauthdns.zonetransfer.me.            7900     IN    AFSDB             1
asfdbbox.zonetransfer.me.                7200     IN    A         127.0.0.1
asfdbvolume.zonetransfer.me.             7800     IN    AFSDB             1
canberra-office.zonetransfer.me.         7200     IN    A        202.14.81.230
cmdexec.zonetransfer.me.                 300      IN    TXT              ";
contact.zonetransfer.me.                 2592000  IN    TXT               (
dc-office.zonetransfer.me.               7200     IN    A        143.228.181.132
deadbeef.zonetransfer.me.                7201     IN    AAAA     dead:beaf::
dr.zonetransfer.me.                      300      IN    LOC              53
DZC.zonetransfer.me.                     7200     IN    TXT         AbCdEfG
email.zonetransfer.me.                   2222     IN    NAPTR             (
email.zonetransfer.me.                   7200     IN    A        74.125.206.26
Hello.zonetransfer.me.                   7200     IN    TXT             "Hi
home.zonetransfer.me.                    7200     IN    A         127.0.0.1
Info.zonetransfer.me.                    7200     IN    TXT               (
internal.zonetransfer.me.                300      IN    NS       intns1.zonetransfer.me.
internal.zonetransfer.me.                300      IN    NS       intns2.zonetransfer.me.
intns1.zonetransfer.me.                  300      IN    A        81.4.108.41
intns2.zonetransfer.me.                  300      IN    A        167.88.42.94
office.zonetransfer.me.                  7200     IN    A        4.23.39.254
ipv6actnow.org.zonetransfer.me.          7200     IN    AAAA     2001:67c:2e8:11::c100:1332
owa.zonetransfer.me.                     7200     IN    A        207.46.197.32
robinwood.zonetransfer.me.               302      IN    TXT          "Robin
rp.zonetransfer.me.                      321      IN    RP                (
sip.zonetransfer.me.                     3333     IN    NAPTR             (
sqli.zonetransfer.me.                    300      IN    TXT              "'
sshock.zonetransfer.me.                  7200     IN    TXT             "()
staging.zonetransfer.me.                 7200     IN    CNAME    www.sydneyoperahouse.com.
alltcpportsopen.firewall.test.zonetransfer.me. 301      IN    A         127.0.0.1
testing.zonetransfer.me.                 301      IN    CNAME    www.zonetransfer.me.
vpn.zonetransfer.me.                     4000     IN    A        174.36.59.154
www.zonetransfer.me.                     7200     IN    A        5.196.105.14
xss.zonetransfer.me.                     300      IN    TXT      "'><script>alert('Boo')</script>"

Trying Zone Transfer for zonetransfer.me on nsztm2.digi.ninja ... 
zonetransfer.me.                         7200     IN    SOA               (
zonetransfer.me.                         300      IN    HINFO        "Casio
zonetransfer.me.                         301      IN    TXT               (
zonetransfer.me.                         7200     IN    MX                0
zonetransfer.me.                         7200     IN    MX               10
zonetransfer.me.                         7200     IN    MX               10
zonetransfer.me.                         7200     IN    MX               20
zonetransfer.me.                         7200     IN    MX               20
zonetransfer.me.                         7200     IN    MX               20
zonetransfer.me.                         7200     IN    MX               20
zonetransfer.me.                         7200     IN    A        5.196.105.14
zonetransfer.me.                         7200     IN    NS       nsztm1.digi.ninja.
zonetransfer.me.                         7200     IN    NS       nsztm2.digi.ninja.
_acme-challenge.zonetransfer.me.         301      IN    TXT               (
_acme-challenge.zonetransfer.me.         301      IN    TXT               (
_sip._tcp.zonetransfer.me.               14000    IN    SRV               0
14.105.196.5.IN-ADDR.ARPA.zonetransfer.me. 7200     IN    PTR      www.zonetransfer.me.
asfdbauthdns.zonetransfer.me.            7900     IN    AFSDB             1
asfdbbox.zonetransfer.me.                7200     IN    A         127.0.0.1
asfdbvolume.zonetransfer.me.             7800     IN    AFSDB             1
canberra-office.zonetransfer.me.         7200     IN    A        202.14.81.230
cmdexec.zonetransfer.me.                 300      IN    TXT              ";
contact.zonetransfer.me.                 2592000  IN    TXT               (
dc-office.zonetransfer.me.               7200     IN    A        143.228.181.132
deadbeef.zonetransfer.me.                7201     IN    AAAA     dead:beaf::
dr.zonetransfer.me.                      300      IN    LOC              53
DZC.zonetransfer.me.                     7200     IN    TXT         AbCdEfG
email.zonetransfer.me.                   2222     IN    NAPTR             (
email.zonetransfer.me.                   7200     IN    A        74.125.206.26
Hello.zonetransfer.me.                   7200     IN    TXT             "Hi
home.zonetransfer.me.                    7200     IN    A         127.0.0.1
Info.zonetransfer.me.                    7200     IN    TXT               (
internal.zonetransfer.me.                300      IN    NS       intns1.zonetransfer.me.
internal.zonetransfer.me.                300      IN    NS       intns2.zonetransfer.me.
intns1.zonetransfer.me.                  300      IN    A        81.4.108.41
intns2.zonetransfer.me.                  300      IN    A        52.91.28.78
office.zonetransfer.me.                  7200     IN    A        4.23.39.254
ipv6actnow.org.zonetransfer.me.          7200     IN    AAAA     2001:67c:2e8:11::c100:1332
owa.zonetransfer.me.                     7200     IN    A        207.46.197.32
robinwood.zonetransfer.me.               302      IN    TXT          "Robin
rp.zonetransfer.me.                      321      IN    RP                (
sip.zonetransfer.me.                     3333     IN    NAPTR             (
sqli.zonetransfer.me.                    300      IN    TXT              "'
sshock.zonetransfer.me.                  7200     IN    TXT             "()
staging.zonetransfer.me.                 7200     IN    CNAME    www.sydneyoperahouse.com.
alltcpportsopen.firewall.test.zonetransfer.me. 301      IN    A         127.0.0.1
testing.zonetransfer.me.                 301      IN    CNAME    www.zonetransfer.me.
vpn.zonetransfer.me.                     4000     IN    A        174.36.59.154
www.zonetransfer.me.                     7200     IN    A        5.196.105.14
xss.zonetransfer.me.                     300      IN    TXT      "'><script>alert('Boo')</script>"

                                                                                                                                                                                             
Brute forcing with /usr/share/dnsenum/dns.txt:                                                                                                                                               
_______________________________________________                                                                                                                                              
                                                                                                                                                                                             
                                                                                                                                                                                             
                                                                                                                                                                                             
zonetransfer.me class C netranges:                                                                                                                                                           
___________________________________                                                                                                                                                          
                                                                                                                                                                                             
 4.23.39.0/24                                                                                                                                                                                
 5.196.105.0/24
 52.91.28.0/24
 74.125.206.0/24
 81.4.108.0/24
 143.228.181.0/24
 167.88.42.0/24
 174.36.59.0/24
 202.14.81.0/24
 207.46.197.0/24

                                                                                                                                                                                             
Performing reverse lookup on 2560 ip addresses:                                                                                                                                              
________________________________________________                                                                                                                                             
                                                                                                                                                                                             
                                                                                                                                                                                             
0 results out of 2560 IP addresses.

                                                                                                                                                                                             
zonetransfer.me ip blocks:                                                                                                                                                                   
___________________________                                                                                                                                                                  
                                                                                                                                                                                             
                                                                                                                                                                                             
done.

```

Perform zone transfer with `dig`:

```
└─# whatis dig                
dig (1)              - DNS lookup utility
```

We need to specify the nameserver, the actual domain which is our taget. `axfr` is the zone transfer switch. Specify the actual nameserver `nsztm1.digi.ninja`.

```
┌──(root㉿kali)-[~]
└─# dig axfr @nsztm1.digi.ninja zonetransfer.me                   

; <<>> DiG 9.18.1-1-Debian <<>> axfr @nsztm1.digi.ninja zonetransfer.me
; (1 server found)
;; global options: +cmd
zonetransfer.me.        7200    IN      SOA     nsztm1.digi.ninja. robin.digi.ninja. 2019100801 172800 900 1209600 3600
zonetransfer.me.        300     IN      HINFO   "Casio fx-700G" "Windows XP"
zonetransfer.me.        301     IN      TXT     "google-site-verification=tyP28J7JAUHA9fw2sHXMgcCC0I6XBmmoVi04VlMewxA"
zonetransfer.me.        7200    IN      MX      0 ASPMX.L.GOOGLE.COM.
zonetransfer.me.        7200    IN      MX      10 ALT1.ASPMX.L.GOOGLE.COM.
zonetransfer.me.        7200    IN      MX      10 ALT2.ASPMX.L.GOOGLE.COM.
zonetransfer.me.        7200    IN      MX      20 ASPMX2.GOOGLEMAIL.COM.
zonetransfer.me.        7200    IN      MX      20 ASPMX3.GOOGLEMAIL.COM.
zonetransfer.me.        7200    IN      MX      20 ASPMX4.GOOGLEMAIL.COM.
zonetransfer.me.        7200    IN      MX      20 ASPMX5.GOOGLEMAIL.COM.
zonetransfer.me.        7200    IN      A       5.196.105.14
zonetransfer.me.        7200    IN      NS      nsztm1.digi.ninja.
zonetransfer.me.        7200    IN      NS      nsztm2.digi.ninja.
_acme-challenge.zonetransfer.me. 301 IN TXT     "6Oa05hbUJ9xSsvYy7pApQvwCUSSGgxvrbdizjePEsZI"
_sip._tcp.zonetransfer.me. 14000 IN     SRV     0 0 5060 www.zonetransfer.me.
14.105.196.5.IN-ADDR.ARPA.zonetransfer.me. 7200 IN PTR www.zonetransfer.me.
asfdbauthdns.zonetransfer.me. 7900 IN   AFSDB   1 asfdbbox.zonetransfer.me.
asfdbbox.zonetransfer.me. 7200  IN      A       127.0.0.1
asfdbvolume.zonetransfer.me. 7800 IN    AFSDB   1 asfdbbox.zonetransfer.me.
canberra-office.zonetransfer.me. 7200 IN A      202.14.81.230
cmdexec.zonetransfer.me. 300    IN      TXT     "; ls"
contact.zonetransfer.me. 2592000 IN     TXT     "Remember to call or email Pippa on +44 123 4567890 or pippa@zonetransfer.me when making DNS changes"
dc-office.zonetransfer.me. 7200 IN      A       143.228.181.132
deadbeef.zonetransfer.me. 7201  IN      AAAA    dead:beaf::
dr.zonetransfer.me.     300     IN      LOC     53 20 56.558 N 1 38 33.526 W 0.00m 1m 10000m 10m
DZC.zonetransfer.me.    7200    IN      TXT     "AbCdEfG"
email.zonetransfer.me.  2222    IN      NAPTR   1 1 "P" "E2U+email" "" email.zonetransfer.me.zonetransfer.me.
email.zonetransfer.me.  7200    IN      A       74.125.206.26
Hello.zonetransfer.me.  7200    IN      TXT     "Hi to Josh and all his class"
home.zonetransfer.me.   7200    IN      A       127.0.0.1
Info.zonetransfer.me.   7200    IN      TXT     "ZoneTransfer.me service provided by Robin Wood - robin@digi.ninja. See http://digi.ninja/projects/zonetransferme.php for more information."
internal.zonetransfer.me. 300   IN      NS      intns1.zonetransfer.me.
internal.zonetransfer.me. 300   IN      NS      intns2.zonetransfer.me.
intns1.zonetransfer.me. 300     IN      A       81.4.108.41
intns2.zonetransfer.me. 300     IN      A       167.88.42.94
office.zonetransfer.me. 7200    IN      A       4.23.39.254
ipv6actnow.org.zonetransfer.me. 7200 IN AAAA    2001:67c:2e8:11::c100:1332
owa.zonetransfer.me.    7200    IN      A       207.46.197.32
robinwood.zonetransfer.me. 302  IN      TXT     "Robin Wood"
rp.zonetransfer.me.     321     IN      RP      robin.zonetransfer.me. robinwood.zonetransfer.me.
sip.zonetransfer.me.    3333    IN      NAPTR   2 3 "P" "E2U+sip" "!^.*$!sip:customer-service@zonetransfer.me!" .
sqli.zonetransfer.me.   300     IN      TXT     "' or 1=1 --"
sshock.zonetransfer.me. 7200    IN      TXT     "() { :]}; echo ShellShocked"
staging.zonetransfer.me. 7200   IN      CNAME   www.sydneyoperahouse.com.
alltcpportsopen.firewall.test.zonetransfer.me. 301 IN A 127.0.0.1
testing.zonetransfer.me. 301    IN      CNAME   www.zonetransfer.me.
vpn.zonetransfer.me.    4000    IN      A       174.36.59.154
www.zonetransfer.me.    7200    IN      A       5.196.105.14
xss.zonetransfer.me.    300     IN      TXT     "'><script>alert('Boo')</script>"
zonetransfer.me.        7200    IN      SOA     nsztm1.digi.ninja. robin.digi.ninja. 2019100801 172800 900 1209600 3600
;; Query time: 238 msec
;; SERVER: 81.4.108.41#53(nsztm1.digi.ninja) (TCP)
;; WHEN: Wed Aug 10 23:44:47 EDT 2022
;; XFR size: 50 records (messages 1, bytes 1994)
```

The name server is Cloudflare name server.

```
┌──(root㉿kali)-[~]
└─# dnsrecon -d hackersploit.org
[*] std: Performing General Enumeration against: hackersploit.org...
[*] DNSSEC is configured for hackersploit.org
[*] DNSKEYs:
[*]     None KSk ECDSAP256SHA256 99db2cc14cabdc33d6d77da63a2f15f7 1112584f234e8d1dc428e39e8a4a97e1 aa271a555dc90701e17e2a4c4b6f120b 7c32d44f4ac02bd894cf2d4be7778a19
[*]     None ZSK ECDSAP256SHA256 a09311112cf9138818cd2feae970ebbd 4d6a30f6088c25b325a39abbc5cd1197 aa098283e5aaf421177c2aa5d714992a 9957d1bcc18f98cd71f1f1806b65e148
[*]      SOA dee.ns.cloudflare.com 173.245.58.93
[*]      SOA dee.ns.cloudflare.com 108.162.192.93
[*]      SOA dee.ns.cloudflare.com 172.64.32.93
[*]      SOA dee.ns.cloudflare.com 2a06:98c1:50::ac40:205d
[*]      SOA dee.ns.cloudflare.com 2803:f800:50::6ca2:c05d
[*]      SOA dee.ns.cloudflare.com 2606:4700:50::adf5:3a5d
[*]      NS dee.ns.cloudflare.com 173.245.58.93
[-]      Recursion enabled on NS Server 173.245.58.93
[*]      NS dee.ns.cloudflare.com 2803:f800:50::6ca2:c05d
[*]      NS dee.ns.cloudflare.com 2606:4700:50::adf5:3a5d
[*]      NS dee.ns.cloudflare.com 2a06:98c1:50::ac40:205d
[*]      NS jim.ns.cloudflare.com 172.64.33.125
[-]      Recursion enabled on NS Server 172.64.33.125
[*]      NS jim.ns.cloudflare.com 173.245.59.125
[-]      Recursion enabled on NS Server 173.245.59.125
[*]      NS jim.ns.cloudflare.com 108.162.193.125
[-]      Recursion enabled on NS Server 108.162.193.125
[*]      NS jim.ns.cloudflare.com 2803:f800:50::6ca2:c17d
[*]      NS jim.ns.cloudflare.com 2a06:98c1:50::ac40:217d
[*]      NS jim.ns.cloudflare.com 2606:4700:58::adf5:3b7d
[*]      MX _dc-mx.2c2a3526b376.hackersploit.org 198.54.120.212
[*]      A hackersploit.org 104.21.44.180
[*]      A hackersploit.org 172.67.202.99
[*]      AAAA hackersploit.org 2606:4700:3036::ac43:ca63
[*]      AAAA hackersploit.org 2606:4700:3031::6815:2cb4
[*]      TXT hackersploit.org google-site-verification=TW0pQsFZ0xx3w4b7kysBV0UrcMq7fJFB-5Rz9h6GwkU
[*]      TXT hackersploit.org v=spf1 a:my.hackersploit.org ~all
[*] Enumerating SRV Records
[+] 0 Records Found
```

This will fail, because zone transfer have been disabled. The Cloudflare name server or dns server do not have that option configured.

```
┌──(root㉿kali)-[~]
└─# dnsenum dee.ns.cloudflare.com
dnsenum VERSION:1.2.6

-----   dee.ns.cloudflare.com   -----                                                                                                                                                        
                                                                                                                                                                                             
                                                                                                                                                                                             
Host's addresses:                                                                                                                                                                            
__________________                                                                                                                                                                           
                                                                                                                                                                                             
dee.ns.cloudflare.com.                   5        IN    A        173.245.58.93                                                                                                               

                                                                                                                                                                                             
Name Servers:                                                                                                                                                                                
______________                                                                                                                                                                               
                                                                                                                                                                                             
 dee.ns.cloudflare.com NS record query failed: NOERROR  
```

查看DNS域名枚举文件：`/usr/share/dnsenum/dns.txt`。

[fierce](https://www.kali.org/tools/fierce/)

```
root@kali:~# fierce -h
用法：fierce [-h] [--domain DOMAIN] [--connect] [--wide]
              [--traverse TRAVERSE] [--search SEARCH [SEARCH ...]]
              [--range 范围] [--delay 延迟]
              [--subdomains 子域 [子域 ...] | --subdomain-file
              SUBDOMAIN_FILE] [--dns-servers DNS_SERVERS [DNS_SERVERS ...] |
              --dns-file DNS_FILE] [--tcp]

        用于定位非连续 IP 空间的 DNS 侦察工具。
        

选项：
  -h, --help 显示此帮助信息并退出
  --domain DOMAIN 域名进行测试
  --connect 尝试与非 RFC 1918 主机的 HTTP 连接
  --wide 扫描发现记录的整个 c 类
  --traverse TRAVERSE 扫描发现记录附近的IP，这不会进入相邻的c类
  --search SEARCH [SEARCH...]
                        扩展查找时过滤这些域
  --range RANGE 扫描内部 IP 范围，使用 cidr 表示法
  --delay DELAY 查找之间等待的时间
  --subdomains SUBDOMAINS [SUBDOMAINS ...]
                        使用这些子域
  --subdomain-file SUBDOMAIN_FILE
                        使用此文件中指定的子域（每行一个）
  --dns-servers DNS_SERVERS [DNS_SERVERS ...]
                        使用这些 dns 服务器进行反向查找
  --dns-file DNS_FILE 使用此文件中指定的 dns 服务器进行反向查找（每行一个）
  --tcp 使用 TCP 而不是 UDP
```

```
┌──(root㉿kali)-[~]
└─# fierce --domain zonetransfer.me
NS: nsztm1.digi.ninja. nsztm2.digi.ninja.
SOA: nsztm1.digi.ninja. (81.4.108.41)
Zone: success
{<DNS name @>: '@ 7200 IN SOA nsztm1.digi.ninja. robin.digi.ninja. 2019100801 '
               '172800 900 1209600 3600\n'
               '@ 300 IN HINFO "Casio fx-700G" "Windows XP"\n'
               '@ 301 IN TXT '
               '"google-site-verification=tyP28J7JAUHA9fw2sHXMgcCC0I6XBmmoVi04VlMewxA"\n'
               '@ 7200 IN MX 0 ASPMX.L.GOOGLE.COM.\n'
               '@ 7200 IN MX 10 ALT1.ASPMX.L.GOOGLE.COM.\n'
               '@ 7200 IN MX 10 ALT2.ASPMX.L.GOOGLE.COM.\n'
               '@ 7200 IN MX 20 ASPMX2.GOOGLEMAIL.COM.\n'
               '@ 7200 IN MX 20 ASPMX3.GOOGLEMAIL.COM.\n'
               '@ 7200 IN MX 20 ASPMX4.GOOGLEMAIL.COM.\n'
               '@ 7200 IN MX 20 ASPMX5.GOOGLEMAIL.COM.\n'
               '@ 7200 IN A 5.196.105.14\n'
               '@ 7200 IN NS nsztm1.digi.ninja.\n'
               '@ 7200 IN NS nsztm2.digi.ninja.',
 <DNS name _acme-challenge>: '_acme-challenge 301 IN TXT '
                             '"6Oa05hbUJ9xSsvYy7pApQvwCUSSGgxvrbdizjePEsZI"',
 <DNS name _sip._tcp>: '_sip._tcp 14000 IN SRV 0 0 5060 www',
 <DNS name 14.105.196.5.IN-ADDR.ARPA>: '14.105.196.5.IN-ADDR.ARPA 7200 IN PTR '
                                       'www',
 <DNS name asfdbauthdns>: 'asfdbauthdns 7900 IN AFSDB 1 asfdbbox',
 <DNS name asfdbbox>: 'asfdbbox 7200 IN A 127.0.0.1',
 <DNS name asfdbvolume>: 'asfdbvolume 7800 IN AFSDB 1 asfdbbox',
 <DNS name canberra-office>: 'canberra-office 7200 IN A 202.14.81.230',
 <DNS name cmdexec>: 'cmdexec 300 IN TXT "; ls"',
 <DNS name contact>: 'contact 2592000 IN TXT "Remember to call or email Pippa '
                     'on +44 123 4567890 or pippa@zonetransfer.me when making '
                     'DNS changes"',
 <DNS name dc-office>: 'dc-office 7200 IN A 143.228.181.132',
 <DNS name deadbeef>: 'deadbeef 7201 IN AAAA dead:beaf::',
 <DNS name dr>: 'dr 300 IN LOC 53 20 56.558 N 1 38 33.526 W 0.00m',
 <DNS name DZC>: 'DZC 7200 IN TXT "AbCdEfG"',
 <DNS name email>: 'email 2222 IN NAPTR 1 1 "P" "E2U+email" "" '
                   'email.zonetransfer.me\n'
                   'email 7200 IN A 74.125.206.26',
 <DNS name Hello>: 'Hello 7200 IN TXT "Hi to Josh and all his class"',
 <DNS name home>: 'home 7200 IN A 127.0.0.1',
 <DNS name Info>: 'Info 7200 IN TXT "ZoneTransfer.me service provided by Robin '
                  'Wood - robin@digi.ninja. See '
                  'http://digi.ninja/projects/zonetransferme.php for more '
                  'information."',
 <DNS name internal>: 'internal 300 IN NS intns1\ninternal 300 IN NS intns2',
 <DNS name intns1>: 'intns1 300 IN A 81.4.108.41',
 <DNS name intns2>: 'intns2 300 IN A 167.88.42.94',
 <DNS name office>: 'office 7200 IN A 4.23.39.254',
 <DNS name ipv6actnow.org>: 'ipv6actnow.org 7200 IN AAAA '
                            '2001:67c:2e8:11::c100:1332',
 <DNS name owa>: 'owa 7200 IN A 207.46.197.32',
 <DNS name robinwood>: 'robinwood 302 IN TXT "Robin Wood"',
 <DNS name rp>: 'rp 321 IN RP robin robinwood',
 <DNS name sip>: 'sip 3333 IN NAPTR 2 3 "P" "E2U+sip" '
                 '"!^.*$!sip:customer-service@zonetransfer.me!" .',
 <DNS name sqli>: 'sqli 300 IN TXT "\' or 1=1 --"',
 <DNS name sshock>: 'sshock 7200 IN TXT "() { :]}; echo ShellShocked"',
 <DNS name staging>: 'staging 7200 IN CNAME www.sydneyoperahouse.com.',
 <DNS name alltcpportsopen.firewall.test>: 'alltcpportsopen.firewall.test 301 '
                                           'IN A 127.0.0.1',
 <DNS name testing>: 'testing 301 IN CNAME www',
 <DNS name vpn>: 'vpn 4000 IN A 174.36.59.154',
 <DNS name www>: 'www 7200 IN A 5.196.105.14',
 <DNS name xss>: 'xss 300 IN TXT "\'><script>alert(\'Boo\')</script>"'}
```
