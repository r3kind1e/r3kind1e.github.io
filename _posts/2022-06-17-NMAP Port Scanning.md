---
layout: post
title: "NMAP Port Scanning"
date: "2022-06-17"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Footprinting & Scanning
---

```
man nmap

HOST DISCOVERY:
    -sn: Ping Scan - disable port scan
SCAN TECHNIQUES:
    -sS/sT: TCP SYN/Connect()
PORT SPECIFICATION AND SCAN ORDER:
    -p <port ranges>: Only scan specified ports
    Ex: -p22; -p1-65535; -p U:53,111,137,T:21-25,80,139,8080,S:9
SERVICE/VERSION DETECTION:
    -sV: Probe open ports to determine service/version info
```

Use nmap to discover one host available on the target network.

```
nmap -sn 192.168.102.0-255
nmap -sn 192.168.102.0/24
nmap -sn 192.168.102.0/24 > hosts-up.txt
cat hosts-up.txt
nmap 192.168.102.138
```

The default scan is the SYN scan, we can accomplish the SYN scan by using the `-sS` to identify the type of scan nmap use.

```
nmap -sS 192.168.102.138
```

Similarly, we can also use the TCP connect scan by specify the `-sT`. 

```
nmap -sT 192.168.102.138
```

`-p`: we only want to scan the single port.

```
nmap -sT -p 80 192.168.102.0/24
nmap -sT -p0-100 192.168.102.0/24
```

`-sV`: To know the service running on the specific ports.

```
nmap -sS -sV -p 21 192.168.102.135
nmap -sS -sV 192.168.102.0/24
```