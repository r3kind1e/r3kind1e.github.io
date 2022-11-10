---
layout: post
title: "WiFi Traffic Analysis"
date: "2022-11-10"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Network-Based Attacks
---

# WiFi Traffic Analysis
`Statistics->Protocol Hierarchy`

`(wlan.fc.type_subtype == 0x0008)`: We're going to look for Beacon. It's a wireless LAN subtype of 0008.

`!(wlan.wfa.ie.wpa.version == 1)`: We don't want WPA.

`!(wlan.tag.number==48)`: We don't want RSN, which is the Robust Security Network interface.

That would be identified with WPA2.

```
(wlan.fc.type_subtype == 0x0008) && !(wlan.wfa.ie.wpa.version == 1) && !(wlan.tag.number==48)
```

```
wlan contains Home_Network
```

`IEEE 802.11 wireless LAN->Tagged parameters->Tag: DS Parameter set: Current Channel: 6->Current Channel: 6`

```
wlan contains LazyArtists
```

`IEEE 802.11 wireless LAN->Tag: RSN Information->Auth Key Management (AKM) List 00:0f:ac (Ieee 802.11) PSK`

For RSN, this is WPA2 PSK.

```
(wlan.ssid contains Amazon) && (wlan.fc.type_subtype == 0x0008)
```

`IEEE 802.11 wireless LAN->Tag: Vendor Specific: Microsoft Corp.: WPS->Type: WPS (0x04)`

```
(wlan.ta == e8:de:27:16:87:18) || (wlan.ra == e8:de:27:16:87:18)
```

```
wlan contains SecurityTube
```

`IEEE 802.11 Beacon frame, Flags: ........C`

```
((wlan.bssid == e8:de:27:16:87:18) && (wlan.fc.type_subtype == 0x0020))
```

`IEEE 802.11 Data, Flags: ......F.C->Frame Control Field: 0x0802->Destination address`

We're looking for a traffic from `5c` to `3a`,and looking for a subtype of 0x0001, which is an association response.

```
(((wlan.bssid == e8:de:27:16:87:18)) && (wlan.addr == 5c:51:88:31:a0:3b)) && (wlan.fc.type_subtype == 0x0001)
```

`IEEE 802.11 Association Response, Flags: ........C->Type/Subtype: Association Response (0x0001)`

# WiFi Security: Traffic Analysis I（WiFi 安全：流量分析 I）
## 概述
实验室提供了 WiFi 流量捕获。使用 Wireshark 分析流量并回答以下问题：
1. 数据包转储中存在的开放（无安全）SSID 的名称是什么？
2. SSID“Home_Network”在哪个频道上运行？
3. 为 SSID 'LazyArtists' 配置了哪种安全机制？您的选项是：OPEN、WPA-PSK、WPA2-PSK。
4. SSID“Amazon Wood”是否启用了 WiFi 保护设置 (WPS)？说明是或否。
5. MAC e8:de:27:16:87:18 的设备发送或接收的数据包总数是多少？
6. 与 SSID 'SecurityTube_Open' 交换数据包的站点的 MAC 地址是什么？
7. 从上一个问题中，我们知道一个站点连接到 SSID 'SecurityTube_Open'。提供从接入点发送到该站点的关联响应的 TSF 时间戳。


## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-1141.pdf

[Pentester Academy WiFi course](https://www.pentesteracademy.com/course?id=9)

## 我自己的思路
A WiFi traffic capture is provided in the lab. Analyze the traffic using Wireshark and answer the following questions:

* What is the name of the Open (No Security) SSID present in the packet dump?

```
SecurityTube_Open
```

![WiFi-Security-Traffic-Analysis-1-1.png](/img/in-post/ine/WiFi-Security-Traffic-Analysis-1-1.png)

* The SSID 'Home_Network' is operating on which channel?

```
Current Channel: 6
```

![WiFi-Security-Traffic-Analysis-1-2.png](/img/in-post/ine/WiFi-Security-Traffic-Analysis-1-2.png)

* Which security mechanism is configured for SSID 'LazyArtists'? Your options are: OPEN, WPA-PSK, WPA2-PSK.

```
WPA2-PSK
```

![WiFi-Security-Traffic-Analysis-1-3.png](/img/in-post/ine/WiFi-Security-Traffic-Analysis-1-3.png)

* Is WiFi Protected Setup (WPS) enabled on SSID 'Amazon Wood'?  State Yes or No.

```
Yes
```

![WiFi-Security-Traffic-Analysis-1-4.png](/img/in-post/ine/WiFi-Security-Traffic-Analysis-1-4.png)

* What is the total count of packets which were either transmitted or received by the device with MAC e8:de:27:16:87:18?

```
5701
```

![WiFi-Security-Traffic-Analysis-1-5.png](/img/in-post/ine/WiFi-Security-Traffic-Analysis-1-5.png)

* What is the MAC address of the station which exchanged data packets with SSID 'SecurityTube_Open'?

```
5c:51:88:31:a0:3b
```

![WiFi-Security-Traffic-Analysis-1-7.png](/img/in-post/ine/WiFi-Security-Traffic-Analysis-1-7.png)

* From the last question, we know that a station was connected to SSID 'SecurityTube_Open'. Provide TSF timestamp of the association response sent from the access point to this station.

![WiFi-Security-Traffic-Analysis-1-8.png](/img/in-post/ine/WiFi-Security-Traffic-Analysis-1-8.png)

```
115152625
```

