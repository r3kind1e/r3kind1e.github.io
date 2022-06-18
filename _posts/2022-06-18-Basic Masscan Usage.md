---
layout: post
title: "Basic Masscan Usage"
date: "2022-06-18"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Footprinting & Scanning
---

[masscan](https://github.com/robertdavidgraham/masscan)

```
sudo apt-get --assume-yes install git make gcc
git clone https://github.com/robertdavidgraham/masscan
cd masscan
make -j
cd bin
./masscan --regress
```

```
./masscan -p22,80,443,53,3389,8080,445 -Pn --rate=800 --banners 10.142.111.0/24 -e tap0 --router-ip 10.142.111.1
./masscan -p22,80,443,53,3389,8080,445 -Pn --rate=800 --banners 10.142.111.0/24 -e tap0 --router-ip 10.142.111.1 --echo > masscan.conf
```

```
gedit masscan.conf

output-filename = scan.list
format = list
```

```
masscan -c masscan.conf
cat scan.list
```
