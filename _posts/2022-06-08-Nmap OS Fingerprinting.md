---
layout: post
title: "Nmap OS Fingerprinting"
date: "2022-06-08"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Footprinting & Scanning
---

# Nmap OS Fingerprinting

```
nmap

OS DETECTION
    -O: Enable OS detection
    --osscan-limit: Limit OS detection to promising targets
    --osscan-guess: Guess OS more aggressively
```
We can also configure more accurate scan by scanning one of the previous scan techniques.

`-sT`: allows to perform a TCP connect scan.

```
SCAN TECHNIQUES:
    -sS/sT/sA/sW/sM: TCP SYN/Connect()/ACK/Window/Maimon scans
    -sU: UDP Scan
```

```
nmap -sT -O 192.168.102.0/24
```

Sometimes the scan may also fail. Here indeed, we can see the nmap is not able to correctly find the OS of the machine. This may happen for firewall blocks or rejects some packets, blocking normol behavior and fingerprint the reponses.
```
Nmap scan report for 192.168.102.200
...
Too many fingerprints match this host to give specific OS details.
```

This warnning indicates the scann result may not be 100% correct, it was not able to find at least 1 open and 1 closed port on the target machine.
```
Nmap scan report for 192.168.102.220
...
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed ports
...
Running (JUST GUESSING): Comau embedded (92%), FreeBSD 8.X (85%), OpenBSD 4.X (85%)
```

Since our machine is in the same network of the target machine, nmap also scan our IP address and found we are running a Linux 3.7. so Let's verify the nmap results correct.

We can start check the OS of our machine.

```bash
uname -a
```

Let's check the machine at the address 192.168.102.135.

```bat
ipconfig
```

`Start->My computer->Properties`，可以看到在System Properties中，System是Microsoft Windows XP Service Pack 3.

Let's go on find the OS of machine 192.168.102.138.

```bat
ipconfig
```

`Start->My computer->Properties`，可以看到在System Properties中，System是Microsoft Windows Server 2003 Service Pack 1.

The last machine we want to check is the FreeBSD machine at 192.168.102.220

```bash
ifconfig em1
uname -r # check the OS
uname -a
```



