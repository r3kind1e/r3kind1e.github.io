---
layout: post
title: "PowerShell-Empire"
date: "2023-04-24"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# PowerShell-Empire
## PowerShell-Empire
PowerShell-Empire (Aka Empire) is a pure PowerShell exploitation/post-exploitation framework built on cryptological-secure communications and flexible architecture.

Empire implements the ability to run PowerShell agents without needing powershell.exe, rapidly deployable post-exploitation modules ranging from keyloggers to Mimikatz, and adaptable communications to evade network detection, all wrapped up in a usability-focused framework.

PowerShell Empire recently received an update and is now officially support and maintained by Kali Linux, more information regarding the update can be found here:  https://www.kali.org/blog/empire-starkiller/

PowerShell-Empire（又名 Empire）是一个纯 PowerShell 利用/后期利用框架，建立在加密安全通信和灵活的架构之上。

Empire 实现了无需 powershell.exe 即可运行 PowerShell 代理的能力、可快速部署的后期利用模块（从键盘记录器到 Mimikatz）以及可适应的通信以逃避网络检测，所有这些都包含在一个以可用性为中心的框架中。

PowerShell Empire 最近收到更新，现在由 Kali Linux 正式支持和维护，有关更新的更多信息可以在这里找到：https://www.kali.org/blog/empire-starkiller/

## Starkiller
In addition to being updated and modernized, BC Security, the company responsible for maintaining the Empire has also developed a companion to Empire called Starkiller.

Starkiller is GUI Fronted for the Powershell Empire. It is an Electron application written in VueJS and provides users with an intuitive way of interacting with Empire.

In order to get an understanding of how Empire works and the components that make up the framework, I would recommend going through the official documentation which can be found here: https://www.powershellempire.com/

PowerShell-Empire & Starkiller are both available as packages in the Kali Linux repositories.

除了更新和现代化之外，负责维护 Empire 的公司 BC Security 还开发了一个名为 Starkiller 的 Empire 伙伴。

Starkiller 是 Powershell Empire 的 GUI 前端。 它是一个用 VueJS 编写的 Electron 应用程序，为用户提供了一种与 Empire 交互的直观方式。

为了了解 Empire 的工作原理以及构成框架的组件，我建议您阅读官方文档，该文档可在此处找到：https://www.powershellempire.com/

PowerShell-Empire 和 Starkiller 都可以在 Kali Linux 存储库中作为软件包使用。

# Demo: PowerShell-Empire（演示：PowerShell 帝国）
The first step will involve installing PowerShell-Empire and Starkiller.

```
sudo apt-get update && sudo apt-get install powershell-empire starkiller -y
```

The next step after installing PowerShell-Empire and Starkiller is to start up the PowerShell Empire server.

The PowerShell-Empire server is the back end of the PowerShell-Empire exploitation and post-exploitation framework, and is responsible for managing the listener as well as callbacks from agents and also the various compilation plug-ins available. This is the back end.

```
sudo powershell-empire server
```

The C-Sharp server plug-in is used to compile Windows C-Sharp stages.

Agents are target systems that you've gained access to.

Listeners are what they are on the Metasploit Framework. These are just like a Netcat listener, or a multi handler. They are listening for connections from the actual target once the stager is executed.

A stager is just a piece of code that is executed on a target operating system. The stager contains information like the attacker's IP address. or in this case, the listener IP that we will be setting up. This utilizes the typical reverse-shell communication methodology, or infrastructure, if you will.

That is the PowerShell-Empire server.

I'll open up a new tab. It's important that you have the PowerShell-Empire server running in order to utilize the client.

```
sudo powershell-empire client
```

Within the client is where we can start typing in commands, like:

```
listeners
```

To list out the listeners available.

```
agents
```

starkiller

It connects to the RESTful API, the PowerShell-Empire server RESTful API. That's localhost port 1337.

log in

```
Username: empireadmin
Password: password123
```

Starkiller is a fronted for the PowerShell-Empire framework, more specifically, the client side of things. But it also provides you with functionality that allows you to interact with the server.

By default, you'll have three plug-ins here.

And we then have C-Sharp server, which is an Empire C-Sharp server for agents. More specifically, the C-Sharp server allows you to compile C-Sharp stages for Windows targets. Once those stages are compiled, the output will be in the form of an EXE. Once that stager is compiled, you can then transfer it over to the target Windows system and execute it. Alternatively, the stager could be a part of a larger exploit.

I would recommand starting up the C-Sharp server.

Listener

They are just listeners that are going to listen for connections from your target systems.

Stagers

These are just pieces of code or executables that will be executed on the target system. How you deliver them to the target system is entirely up to you. They can be part of a social engineering campaign. You can also inject some of the stages you generate here into documents in the form of macros. You can inject these stagers, if they are executables, into legitimate executables. There are various techniques that you can use.

Agents

This is a dynamic page, that will bring up any of the systems that you've exploited, or any of the systems on which a stager has been ran on. And agents are your target systems. And you have the ability to have more than one up to as many as you want. 