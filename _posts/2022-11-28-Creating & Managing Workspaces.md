---
layout: post
title: "Creating & Managing Workspaces"
date: "2022-11-28"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Creating & Managing Workspaces
## MSF Workspaces
Workspaces allow you to keep track of all your hosts, scans and activities and are extremely useful when conducting penetration tests as they allow you to sort and organize your data based on the target or organization.

MSFconsole provides you with the ability to create, manage and switch between multiple workspaces depending on your requirements.

We will be using workspaces to organize our assessments as we progress through the course.

# 创建和管理工作区
## MSF 工作区
工作区允许您跟踪所有主机、扫描和活动，并且在进行渗透测试时非常有用，因为它们允许您根据目标或组织对数据进行排序和组织。

MSFconsole 使您能够根据需要创建、管理和切换多个工作区。

随着课程的进展，我们将使用工作区来组织我们的评估。

# Demo: Creating & Managing Workspaces（演示：创建和管理工作区）
Check the status of the database.

```
msf6 > db_status
```

Interact with workspaces. We can use `Tab+Alt` completion.

```
msf6 > workspace -h
```

This is the data that's being stored in current workspace:

```
msf6 > workspace
msf6 > hosts
```

Create a new workspace:

```
msf6 > workspace -a Test
msf6 > workspace
msf6 > hosts
```

Switch back to the dafault workspace?

```
msf6 > workspace default
msf6 > hosts
```

Create a new workspace and then provide the company name.

```
msf6 > workspace -a INE
msf6 > workspace
```

Switch between all the other workspaces.

```
msf6 > workspace Test
msf6 > workspace default
```

Delete a workspace.

```
msf6 > workspace -d Test
msf6 > workspace
```

```
msf6 > workspace -h
msf6 > workspace -r INE PTA
msf6 > workspace
```

# Home Lab
```
msf6 > db_status
[*] Connected to msf. Connection type: postgresql.
```

```
msf6 > workspace -h
Usage:
    workspace          List workspaces
    workspace [name]   Switch workspace

OPTIONS:

    -a, --add <name>          Add a workspace.
    -d, --delete <name>       Delete a workspace.
    -D, --delete-all          Delete all workspaces.
    -h, --help                Help banner.
    -l, --list                List workspaces.
    -r, --rename <old> <new>  Rename a workspace.
    -S, --search <name>       Search for a workspace.
    -v, --list-verbose        List workspaces verbosely.

```

```
msf6 > workspace -h
用法：
    workspace 列出工作空间
    workspace [名称] 切换工作空间

选项：

    -a, --add <name> 添加工作区。
    -d, --delete <name> 删除工作区。
    -D, --delete-all 删除所有工作区。
    -h, --help 帮助横幅。
    -l, --list 列出工作区。
    -r, --rename <old> <new> 重命名工作区。
    -S, --search <name> 搜索工作空间。
    -v, --list-verbose 详细列出工作区。
```

```
msf6 > workspace
* default
msf6 > hosts

Hosts
=====

address          mac  name             os_name       os_flavor  os_sp  purpose  info  comments
-------          ---  ----             -------       ---------  -----  -------  ----  --------
192.168.248.133       WIN-4QCPUB6FIVL  Windows 2008  Standard          server

```

```
msf6 > workspace -a Test
[*] Added workspace: Test
[*] Workspace: Test
msf6 > workspace
  default
* Test
msf6 > hosts

Hosts
=====

address  mac  name  os_name  os_flavor  os_sp  purpose  info  comments
-------  ---  ----  -------  ---------  -----  -------  ----  --------

```

```
msf6 > workspace default
[*] Workspace: default
msf6 > hosts

Hosts
=====

address          mac  name             os_name       os_flavor  os_sp  purpose  info  comments
-------          ---  ----             -------       ---------  -----  -------  ----  --------
192.168.248.133       WIN-4QCPUB6FIVL  Windows 2008  Standard          server

```

```
msf6 > workspace -a INE
[*] Added workspace: INE
[*] Workspace: INE
msf6 > workspace
  Test
  default
* INE
msf6 > workspace Test
[*] Workspace: Test
msf6 > workspace default
[*] Workspace: default
msf6 > workspace -d Test
[*] Deleted workspace: Test
msf6 > workspace
  INE
* default

```

```
msf6 > workspace
  INE
* default
msf6 > workspace -r INE PTA
[*] Renamed workspace 'INE' to 'PTA'
msf6 > workspace
  PTA
* default
```