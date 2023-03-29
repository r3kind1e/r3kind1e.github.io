---
layout: post
title: "Banner Grabbing"
date: "2023-03-29"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Banner Grabbing
Banner grabbing is an information gathering technique used by penetration testers to enumerate information regarding the target operating system as well as the services that are running on its open ports.

The primary objective of banner grabbing is to identify the service running on a specific port as well as the service version.

Banner grabbing can be performed through various techniques:
* Performing a service version detection scan with Nmap.
* Connecting to the open port with Netcat.
* Authenticating with the service (If the service supports authentication), for example; SSH, FTP, Telnet etc.

# 横幅抓取
横幅抓取是渗透测试人员使用的一种信息收集技术，用于枚举有关目标操作系统以及在其开放端口上运行的服务的信息。

横幅抓取的主要目的是识别在特定端口上运行的服务以及服务版本。

可以通过多种技术执行横幅抓取：
* 使用 Nmap 执行服务版本检测扫描。
* 使用 Netcat 连接到开放端口。
* 例如，使用服务进行身份验证（如果服务支持身份验证）； SSH、FTP、远程登录等。

# Demo: Banner Grabbing（演示：横幅抓取）
You start off by performing an Nmap scan to identify open ports. However, your next logical step, would be to identify these services that are running on those open ports, as well as those service versions. The next logical step would be to utilize the service, as well as the service version information, to identify vulnerabilities or exploits pertinent to that specific version of whatever service is running on a particular port.

```
ifconfig
eth1: 192.8.94.2
```

Target IP Address: 192.8.94.3

The first tool we'll be taking a loot at is Nmap.

`-sV`: Perform the standard service-version detection scan, which will not only tell us what services are running on the open ports on the target, it'll also tell us the service version.

`-O`: Perform operating system scan, and operating system scan, that will give us a rough estimate as to what is running on the target, whether it's running Windows, whether it's running Linux, and in the case of Linux, it could give us the distribution release name.

```
nmap -sV -O 192.8.94.3
```

One really cool thing that you can do with Nmap is the utilization of Nmap scripts or the Nmap scripting engine. In terms of banner grabbing, there exists a very cool script that can be found under the default storage directory for Nmap scripts.

List out the contents of `/usr/share/nmap/scripts`, and we can then pipe out the output. And we can then grep for a specific script that we're looking for. In this case, the name of the script is banner.

```
ls -al /usr/share/nmap/scripts/ | grep banner
```

The banner.nsc or the banner Nmap script can be used to identify the service banner. A banner is used for logistical purposes in that it tells a user or anyone who is trying to access that particular service, what is running on that particular port, as well as the service version. Banners can be changed, but by default that information should be obtainable.

If we wanted to use this particular Nmap script.

`-sV`: Perform a service-version detection scan.

`--script=`: Use the script. We want to use the banner script. We don't need to provide the extension, which is the `.nsc`.

```
nmap -sV --script=banner 192.8.94.3
```

And for the script results, this is the banner script. It gives us pretty much the same information that we would have gotten if we had performed the scan with a typical service-version detection parameters specified.

This is just a really quick and cool Nmap script that can be used to perform banner grabbing on particular services that might not necessarily reveal what service version is running when you perform a standard service-version detection scan. You'll come across quite a few services that you're not be able to identify what is running, as well as the service version, and this script can be quite helpful.

That is how to perform banner grabbing with Nmap.

The next tool is Netcat.

```
whatis nc
man nc
```

For example, we know that the target is running SSH on port 22. We can perform banner grabbing with Netcat by saying:

```
nc 192.8.94.3 22
```

However, many penetration testers still rely on Netcat, as in certain scenarios, Nmap might not be able to give you an accurate idea of what service is running, as well as the service version.

Why is this information important? A penetration tester will typically take this information and utilize a tool like `searchsploit`.

```
searchsploit openssh 7.2
```

That is how to perform banner grabbing with Netcat.

In certain scenarios, a particular service may support authentication. And in this case, SSH does support authentication. You can also retrieve the banner by authenticating with the target system via SSH, and you don't need legitimate credentials.

```
ssh root@192.8.94.3
```

However, for other services that support authentication, like FTP or Telnet, you can also try and utilize the standard authentication client to identify a banner or information that can be useful later on during exploitation.

That is how to perform banner grabbing with Nmap, Netcat, as well as the relevant authentication clients pertinent to the service that's running on a target system. 

# SSH Recon: Basic
## 复现视频内容
```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.146.171.2  netmask 255.255.255.0  broadcast 192.146.171.255
        ether 02:42:c0:92:ab:02  txqueuelen 0  (Ethernet)
```

Target IP Address: 192.146.171.3

```
root@attackdefense:~# nmap -sV -O 192.146.171.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-29 14:05 UTC
Nmap scan report for target-1 (192.146.171.3)
Host is up (0.000031s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
MAC Address: 02:42:C0:92:AB:03 (Unknown)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.70%E=4%D=3/29%OT=22%CT=1%CU=30130%PV=N%DS=1%DC=D%G=Y%M=0242C0%T
OS:M=642445B3%P=x86_64-pc-linux-gnu)SEQ(SP=102%GCD=1%ISR=10E%TI=Z%CI=Z%II=I
OS:%TS=A)OPS(O1=M5B4ST11NW7%O2=M5B4ST11NW7%O3=M5B4NNT11NW7%O4=M5B4ST11NW7%O
OS:5=M5B4ST11NW7%O6=M5B4ST11)WIN(W1=FE88%W2=FE88%W3=FE88%W4=FE88%W5=FE88%W6
OS:=FE88)ECN(R=Y%DF=Y%T=40%W=FAF0%O=M5B4NNSNW7%CC=Y%Q=)T1(R=Y%DF=Y%T=40%S=O
OS:%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=
OS:0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)T6(R=Y%DF=Y%T=40%W=0%
OS:S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD=0%Q=)U1(
OS:R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK=G%RUCK=G%RUD=G)IE(R=Y%DFI=
OS:N%T=40%CD=S)

Network Distance: 1 hop
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.14 seconds
```

```
root@attackdefense:~# ls -al /usr/share/nmap/scripts/ | grep banner
-rw-r--r-- 1 root root  6129 Jan  9  2019 banner.nse
```

```
root@attackdefense:~# nmap -sV --script=banner 192.146.171.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-03-29 14:11 UTC
Nmap scan report for target-1 (192.146.171.3)
Host is up (0.000010s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.6 (Ubuntu Linux; protocol 2.0)
|_banner: SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6
MAC Address: 02:42:C0:92:AB:03 (Unknown)
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.55 seconds
```

```
root@attackdefense:~# whatis nc
nc (1)               - TCP/IP swiss army knife
root@attackdefense:~# man nc
```

```
root@attackdefense:~# nc 192.146.171.3 22
SSH-2.0-OpenSSH_7.2p2 Ubuntu-4ubuntu2.6

Protocol mismatch.
```

```
root@attackdefense:~# searchsploit openssh 7.2
-------------------------------------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                                              |  Path
                                                                                            | (/usr/share/exploitdb/)
-------------------------------------------------------------------------------------------- ----------------------------------------
OpenSSH 7.2 - Denial of Service                                                             | exploits/linux/dos/40888.py
OpenSSH 7.2p1 - (Authenticated) xauth Command Injection                                     | exploits/multiple/remote/39569.py
OpenSSH 7.2p2 - Username Enumeration                                                        | exploits/linux/remote/40136.py
OpenSSHd 7.2p2 - Username Enumeration                                                       | exploits/linux/remote/40113.txt
-------------------------------------------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
```

```
root@attackdefense:~# ssh root@192.146.171.3
The authenticity of host '192.146.171.3 (192.146.171.3)' can't be established.
ECDSA key fingerprint is SHA256:dxlBXgBb0Iv5/LmemZ2Eikb5+GLl9CSLf/B854fUeV8.
Are you sure you want to continue connecting (yes/no)? yes
Warning: Permanently added '192.146.171.3' (ECDSA) to the list of known hosts.
Welcome to attack defense ssh recon lab!!
root@192.146.171.3's password: 
Permission denied, please try again.
root@192.146.171.3's password: 
Permission denied, please try again.
root@192.146.171.3's password: 
root@192.146.171.3: Permission denied (publickey,password).
```