---
layout: post
title: "Importing Nmap Scan Results Into MSF"
date: "2022-11-29"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - The Metasploit Framework (MSF)
---

# Importing Nmap Scan Results Into MSF
```
service postgresql start
msfconsole -q
```

Check the status of the database.

```
msf5 > db_status
```

Before we import the scan results into the MSF database, we need to create a new workspace for our scan results. This is something that I recommend doning.

```
msf5 > workspace
```

We have the default workspace. We want to import our scan results into a new workspace. So to create one:

```
msf5 > workspace -a Win2k12
msf5 > workspace
```

We can now begin importing these scan results. 

We now need to provide the path to where the Nmap scan results was saved.

```
msf5 > db_import /root/windows_server_2012
```

We can now confirm that the data has been imported successfully, by tying in:

```
msf5 > hosts
```

We can also get confirmation by trying to enumerate or trying to print out the services for the various hosts that we have imported. In this case, we've just imported one.

```
msf5 > services
```

So that is a good confirmation that we have everything imported.

As we move along, we're going to take a look at how this information can then be used in conjunction with auxiliary modules to perform various types of scans as well as exploit modules when we'll be exploring exploitation of specific services.

We can also initiate an Nmap scan from within the Metasploit Framework, which is additional functionality that you should be aware of.

MSF console allows us to initiate and perform an Nmap scan from within the MSFconsole, and consequently save the results into the database. So if you don't want to go through the process of performing an Nmap scan externally and then importing them manually, you can do it from within the MSF.

```
msf5 > workspace
```

I'm going to create a new workspace. 

```
msf5 > workspace -a Nmap_MSF
```

So in order to initiate an Nmap scan, all we need to do is type in `db_nmap` and then provide the various scan options that we want to provide.

```
msf5 > db_nmap -Pn -sV -O 10.4.22.173
```

And in this case, we don't need to output or export the scan results into a file. Because the results will automatically be save into the MSF database within our current workspace.

Execute or initiate the Nmap scan from within the MSF console.

And in this case, the results have automatically been imported into the MSF database for this workspace.

The two workspaces are different from each other and the data is considered different irregardless of whether we scanned the same target.

List out the hosts:

```
msf5 > hosts
msf5 > services
```

And additional piece of functionality that you can also perform is list out the vulnerability.

```
msf5 > vulns
```

# Importing Nmap Scan Results Into MSF
## Overview
Goal

This lab covers the process of importing Nmap scan results in to the Metasploit Framework.

## Tasks
Pre-requisites

Basic Familiarity with Metasploit.

Requirements

This lab requires the completion of the previous lab.

## 我自己的解决方案
```
Target IP Address : 10.0.17.12
```

```
root@attackdefense:~# nmap -Pn -sV -O 10.0.17.12 -oX windows_server_2012
Starting Nmap 7.70 ( https://nmap.org ) at 2022-11-29 17:53 IST
Nmap scan report for 10.0.17.12
Host is up (0.0031s latency).
Not shown: 993 filtered ports
PORT      STATE SERVICE            VERSION
80/tcp    open  http               HttpFileServer httpd 2.3
135/tcp   open  msrpc              Microsoft Windows RPC
139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp  open  ssl/ms-wbt-server?
49154/tcp open  msrpc              Microsoft Windows RPC
49155/tcp open  msrpc              Microsoft Windows RPC
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running: Microsoft Windows 2012
OS CPE: cpe:/o:microsoft:windows_server_2012
OS details: Microsoft Windows Server 2012
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 82.39 seconds

```

```
root@attackdefense:~# service postgresql start
Starting PostgreSQL 12 database server: main.
root@attackdefense:~# msfconsole -q
msf5 > db_
db_connect        db_export         db_nmap           db_remove         db_status         
db_disconnect     db_import         db_rebuild_cache  db_save           
msf5 > db_status
[*] Connected to msf. Connection type: postgresql.
```

```
msf5 > workspace
* default
msf5 > workspace -a win2k12
[*] Added workspace: win2k12
[*] Workspace: win2k12
msf5 > workspace
  default
* win2k12
msf5 > hosts

Hosts
=====

address  mac  name  os_name  os_flavor  os_sp  purpose  info  comments
-------  ---  ----  -------  ---------  -----  -------  ----  --------

msf5 > services
Services
========

host  port  proto  name  state  info
----  ----  -----  ----  -----  ----

```

```
msf5 > db_import /root/windows_server_2012
[*] Importing 'Nmap XML' data
[*] Import: Parsing with 'Nokogiri v1.10.7'
[*] Importing host 10.0.17.12
[*] Successfully imported /root/windows_server_2012

```

```
msf5 > hosts

Hosts
=====

address     mac  name  os_name       os_flavor  os_sp  purpose  info  comments
-------     ---  ----  -------       ---------  -----  -------  ----  --------
10.0.17.12             Windows 2012                    server         

msf5 > services
Services
========

host        port   proto  name               state  info
----        ----   -----  ----               -----  ----
10.0.17.12  80     tcp    http               open   HttpFileServer httpd 2.3
10.0.17.12  135    tcp    msrpc              open   Microsoft Windows RPC
10.0.17.12  139    tcp    netbios-ssn        open   Microsoft Windows netbios-ssn
10.0.17.12  445    tcp    microsoft-ds       open   Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
10.0.17.12  3389   tcp    ssl/ms-wbt-server  open   
10.0.17.12  49154  tcp    msrpc              open   Microsoft Windows RPC
10.0.17.12  49155  tcp    msrpc              open   Microsoft Windows RPC

```

```
msf5 > workspace
  default
* win2k12
msf5 > workspace -a msf_nmap
[*] Added workspace: msf_nmap
[*] Workspace: msf_nmap
msf5 > workspace
  default
  win2k12
* msf_nmap
msf5 > db_nmap -Pn -sV -O 10.0.17.12
[*] Nmap: Starting Nmap 7.70 ( https://nmap.org ) at 2022-11-29 18:06 IST
[*] Nmap: Nmap scan report for 10.0.17.12
[*] Nmap: Host is up (0.0032s latency).
[*] Nmap: Not shown: 993 filtered ports
[*] Nmap: PORT      STATE SERVICE            VERSION
[*] Nmap: 80/tcp    open  http               HttpFileServer httpd 2.3
[*] Nmap: 135/tcp   open  msrpc              Microsoft Windows RPC
[*] Nmap: 139/tcp   open  netbios-ssn        Microsoft Windows netbios-ssn
[*] Nmap: 445/tcp   open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
[*] Nmap: 3389/tcp  open  ssl/ms-wbt-server?
[*] Nmap: 49154/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: 49155/tcp open  msrpc              Microsoft Windows RPC
[*] Nmap: Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
[*] Nmap: Device type: general purpose
[*] Nmap: Running: Microsoft Windows 2012
[*] Nmap: OS CPE: cpe:/o:microsoft:windows_server_2012
[*] Nmap: OS details: Microsoft Windows Server 2012
[*] Nmap: Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows
[*] Nmap: OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
[*] Nmap: Nmap done: 1 IP address (1 host up) scanned in 81.95 seconds
msf5 > hosts

Hosts
=====

address     mac  name  os_name       os_flavor  os_sp  purpose  info  comments
-------     ---  ----  -------       ---------  -----  -------  ----  --------
10.0.17.12             Windows 2012                    server         

msf5 > services
Services
========

host        port   proto  name               state  info
----        ----   -----  ----               -----  ----
10.0.17.12  80     tcp    http               open   HttpFileServer httpd 2.3
10.0.17.12  135    tcp    msrpc              open   Microsoft Windows RPC
10.0.17.12  139    tcp    netbios-ssn        open   Microsoft Windows netbios-ssn
10.0.17.12  445    tcp    microsoft-ds       open   Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
10.0.17.12  3389   tcp    ssl/ms-wbt-server  open   
10.0.17.12  49154  tcp    msrpc              open   Microsoft Windows RPC
10.0.17.12  49155  tcp    msrpc              open   Microsoft Windows RPC

```

```
msf5 > vulns

Vulnerabilities
===============

Timestamp  Host  Name  References
---------  ----  ----  ----------
```