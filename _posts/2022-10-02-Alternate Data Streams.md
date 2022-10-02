---
layout: post
title: "Alternate Data Streams"
date: "2022-10-02"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - System/Host Based Attacks
---

# Alternate Data Streams（备用数据流）
## 备用数据流 (ADS)
备用数据流 (ADS) 是 NTFS（新技术文件系统）文件属性，旨在提供与 MacOS HFS（分层文件系统）的兼容性。

在 NTFS 格式化驱动器上创建的任何文件都将具有两个不同的分叉/流：

数据流 - 包含文件数据的默认流。

资源流 - 通常包含文件的元数据。

攻击者可以使用 ADS 将恶意代码或可执行文件隐藏在合法文件中，以逃避检测。

这可以通过将恶意代码或可执行文件存储在合法文件的文件属性资源流（元数据）中来实现。

该技术通常用于规避基于基本签名的 Avs 和静态扫描工具。

# Demo: Alternate Data Streams
If I had an executables that was malicious in nature, I can use ADS to hide the payload within metadata or the resource stream of the legitimate file. Whenever I run the txt file with the resouce stream specified. The resource stream will contain the executable then the executable will be executed.

```
C:\Temp
```

```
C:\User\Win7> cd Desktop
C:\User\Win7\Desktop> notepad test.txt
```

```
Hello World!
```

`Right click on the file->Properties->Details`, this is the metadata of the file. This is the resource stream. We can hide malicious payload within the resource stream. Whenver we open this file, we can specify we want to execute the malicious payload.

```
C:\User\Win7\Desktop> del test.txt
```

It will open up secret.txt with notepad. It's not opening up test.txt.

```
C:\User\Win7\Desktop> notepad test.txt:secret.txt
```

I'm currentlt working with hidden secrect.txt file:

```
This is hidden
```

Open up test.txt, you can see test.txt doesn't have anything. The current size is 0 byte.

```
C:\User\Win7\Desktop> dir

12/29/2021 03:32 AM     0 test.txt
```

Add data to test.txt file.

```
C:\User\Win7\Desktop> notepad test.txt

This is visible.
```

Whenver I open up test.txt, we can get data that's visible.

If I ever want to access the hidden file. Access the actual resouce stream using the colon as I did when I was creating a hidden file. Open up the following hidden file that's in the resource stream of test.txt. And in this case, we are going to open up secret.

```
C:\User\Win7\Desktop> notepad test.txt:secret.txt
```

That is the hidden file, that is the hidden data that's been installed in the resource stream of the legitimate file of the test.txt file.

```
C:\User\Win7\Desktop> del test.txt
```

I have a `winPEASx64` binary here. `winPEAS` is a utility used by penetration tester to perform local enumeration on the Windows system, in order to identify the vulnerabilities that can be exploited or vulnerabilities that can be exploited to elevate privileges. It is not really a malicous file, but its something you want to keep hidden as much as possible.

[PEASS-ng](https://github.com/carlospolop/PEASS-ng/tree/master/winPEAS)

Rename this to payload. Cut it and put it within the `C:\Temp` directory. Because this is where you typically want to hide or stored your exploit file payloads.

```
C:\User\Win7\Desktop> cd \
C:\> cd Temp
C:\Temp>dir
05/24/2021 01:49 AM     1,678,336 payload.exe

```

Use `type` command to redirect the output of payload.exe into a new legitimate txt file. Pass in the executable into the legitimate txt file resource stream.

Create a new legitimate file called windowslog.txt and hide the executable that I provided name of the executable that we will hide in resource stream. I just called it winpeas.exe.

```
C:\Temp>type payload.exe > windowslog.txt:winpeas.exe
```

windowslog is 0 in terms of this size. Because we haven't added any data. 

```
C:\Temp>notepad windowslog.txt

This is visible
C:\Temp>dir
```

Delete payload. Because we already hidden payload output, the content of payload into the `winpeas.exe` file that is currently stored within the resource stream of the legitimate windowslog.txt file.

I can directly execute this, this will probably work on Windows 7. But I can `start` because now executing an executable.

I want to execute the hidden winpeas.exe executable that are hide in within the resource stream of windowslog.txt file.

```
C:\Temp>start windowslog.txt:winpeas.exe
Access is denied.
```

```
There is no program associated to perform the requested action. Please install a program or, if one is already installed, create an association in the Default Programs control panel.
```

This is been done or facilitated through the start command, we can create a symbolic link.

Create a symbolic link within `C:\Windows\System32`.

`mklink`, we call it Windows update(wupdate), whenever I type in `wupdate` within cmd, it will automatically execute the hidden `winpeas` executable that is within the resource stream of the Windowslog file that we just created.

```
C:\Temp>cd \
C:\>cd Windows\System32
C:\Windows\System32>mklink wupdate.exe C:\Temp\windowslog.txt:winpeas.exe
You do not have sufficient privilege to perform this operation.
```

`Command Prompt->Run as Administrators`: We just do it with elevated session.

The hidden file within the resource stream is called `winpeas.exe`.

```
C:\Windows\System32>mklink wupdate.exe C:\Temp\windowslog.txt:winpeas.exe
symbolic link created for wupdate.exe <<===>> C:\Temp\windowslog.txt:winpeas.exe
C:\Windows\System32>wupdate
```

This is a cool vulnerability within the Windows file system, it is extremely helpful in evading very basic detection by humanbeings adminisrators or by very basic signature based anti-virus system. I could done the same thing for the meterpreter payload that I generated with msfvenom. You are not just limited to hidding payloads or executables you can also hide txt file documents. Every any type of file that you want to hide it within the resouce stream of the legitimate file.

That is how alternate data stream work and how they can be abused or exploied by attacker in order to hide their malicious payloads or executables.



