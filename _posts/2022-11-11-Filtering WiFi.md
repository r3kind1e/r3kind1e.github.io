---
layout: post
title: "Filtering WiFi"
date: "2022-11-11"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Network-Based Attacks
---

# Filtering WiFi（过滤 WiFi）
```
tshark -v
ls
```

```
tshark -r WiFi_traffic.pcap -Y 'wlan'
```

```
tshark -r WiFi_traffic.pcap -Y 'wlan.fc.type_subtype==0x000c'
```

```
tshark -r WiFi_traffic.pcap -Y 'eapol'
```

```
tshark -r WiFi_traffic.pcap -Y 'wlan.fc.type_subtype==8' -Tfields -e wlan.ssid -e wlan.bssid
```

```
tshark -r WiFi_traffic.pcap -Y 'wlan.ssid==LazyArtists' -Tfields -e wlan.bssid
```

```
tshark -r WiFi_traffic.pcap -Y 'wlan.ssid==Home_Network' -Tfields -e wlan_radio.channel
```

```
tshark -r WiFi_traffic.pcap -Y 'wlan.fc.type_subtype==0x000c' -Tfields -e wlan.ra
```

```
tshark -r WiFi_traffic.pcap -Y 'wlan.ta==5c:51:88:31:a0:3b && http' -Tfields -e http.user_agent
```

# Filtering Advanced: WiFi（过滤高级：WiFi）
## 概述
WiFi 无处不在：家庭、工作场所、机场、医院、购物中心甚至飞机上 :) 在这些练习中，您将探索如何使用 tshark 分析 Wi-Fi 流量。
启动实验室，找到 PCAP 文件并使用 tshark 回答以下问题：

问题：

A组：
1. 哪个命令可用于仅显示 WiFi 流量？
2. 哪个命令只能查看解除认证包？
3. 哪个命令可以只显示 WPA 握手包？
4. 哪个命令可用于仅打印所有信标帧的 SSID 和 BSSID 值？

B组：
1. SSID“LazyArtists”的 BSSID 是什么？
2. SSID“Home_Network”在哪个频道上运行？
3. 哪两个设备收到了 deauth 消息？说明两者的 MAC 地址。
4. MAC 5c:51:88:31:a0:3b 属于哪个设备？提及设备的制造商和型号。

## 解决方案
此实验室的解决方案可在以下手册中找到：https://assets.ine.com/labs/ad-manuals/walkthrough-4.pdf

## 我自己的思路
WiFi is everywhere: home, work, airport, hospitals, shopping complexes and even on airplanes :) In these exercises, you will explore how to analyze Wi-Fi traffic using tshark.

Start the lab, locate the PCAP file and use tshark to answer the below questions:

```
student@attackdefense:~$ ls
WiFi_traffic.pcap
```

Questions:

Set A:

* Which command can be used to show only WiFi traffic?

```
student@attackdefense:~$ tshark -r WiFi_traffic.pcap -Y 'wlan' | wc -l
66690
```

```
student@attackdefense:~$ tshark -r WiFi_traffic.pcap -Y 'wlan' | more
    1   0.000000 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1939, FN=0, Flags=........C, BI=100, SSID=Home_Network
    2   0.092045 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1940, FN=0, Flags=........C, BI=100, SSID=Home_Network
    3   0.194397 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1941, FN=0, Flags=........C, BI=100, SSID=Home_Network
    4   0.296816 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1942, FN=0, Flags=........C, BI=100, SSID=Home_Network
    5   0.399190 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1943, FN=0, Flags=........C, BI=100, SSID=Home_Network
    6   0.501658 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1944, FN=0, Flags=........C, BI=100, SSID=Home_Network
    7   0.604028 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1945, FN=0, Flags=........C, BI=100, SSID=Home_Network
    8   0.704155 D-LinkIn_5f:81:74 → SamsungE_1d:97:78 802.11 303 Probe Response, SN=1947, FN=0, Flags=........C, BI=100, SSID=Home_Network
    9   0.706592 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1946, FN=0, Flags=........C, BI=100, SSID=Home_Network
   10   0.725570 D-LinkIn_5f:81:74 → SamsungE_1d:97:78 802.11 303 Probe Response, SN=1948, FN=0, Flags=........C, BI=100, SSID=Home_Network
   11   0.748555 D-LinkIn_5f:81:74 → SamsungE_1d:97:78 802.11 303 Probe Response, SN=1949, FN=0, Flags=........C, BI=100, SSID=Home_Network
   12   0.808809 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1950, FN=0, Flags=........C, BI=100, SSID=Home_Network
   13   0.911226 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1951, FN=0, Flags=........C, BI=100, SSID=Home_Network
   14   0.980795 D-LinkIn_5f:81:74 → LgElectr_f6:69:dd 802.11 303 Probe Response, SN=1952, FN=0, Flags=........C, BI=100, SSID=Home_Network
   15   1.013620 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1953, FN=0, Flags=........C, BI=100, SSID=Home_Network
   16   1.048064 D-LinkIn_5f:81:74 → LgElectr_f6:69:dd 802.11 303 Probe Response, SN=1954, FN=0, Flags=........C, BI=100, SSID=Home_Network
   17   1.060033 D-LinkIn_5f:81:74 → LgElectr_f6:69:dd 802.11 303 Probe Response, SN=1955, FN=0, Flags=........C, BI=100, SSID=Home_Network
   18   1.065658 D-LinkIn_5f:81:74 → LgElectr_f6:69:dd 802.11 303 Probe Response, SN=1956, FN=0, Flags=........C, BI=100, SSID=Home_Network
   19   1.116042 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1957, FN=0, Flags=........C, BI=100, SSID=Home_Network
   20   1.189746 D-LinkIn_5f:81:74 → LgElectr_f6:69:dd 802.11 303 Probe Response, SN=1958, FN=0, Flags=........C, BI=100, SSID=Home_Network
   21   1.195922 D-LinkIn_5f:81:74 → LgElectr_f6:69:dd 802.11 303 Probe Response, SN=1959, FN=0, Flags=........C, BI=100, SSID=Home_Network
   22   1.218430 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1960, FN=0, Flags=........C, BI=100, SSID=Home_Network
   23   1.320810 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1961, FN=0, Flags=........C, BI=100, SSID=Home_Network
   24   1.423218 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1962, FN=0, Flags=........C, BI=100, SSID=Home_Network
   25   1.527045 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1963, FN=0, Flags=........C, BI=100, SSID=Home_Network
   26   1.628030 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1964, FN=0, Flags=........C, BI=100, SSID=Home_Network
   27   1.730441 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1965, FN=0, Flags=........C, BI=100, SSID=Home_Network
   28   1.832834 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1966, FN=0, Flags=........C, BI=100, SSID=Home_Network
   29   1.935239 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1967, FN=0, Flags=........C, BI=100, SSID=Home_Network
   30   2.037619 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1968, FN=0, Flags=........C, BI=100, SSID=Home_Network
   31   2.140053 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1969, FN=0, Flags=........C, BI=100, SSID=Home_Network
   32   2.242411 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1970, FN=0, Flags=........C, BI=100, SSID=Home_Network
   33   2.344835 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1971, FN=0, Flags=........C, BI=100, SSID=Home_Network
   34   2.448354 D-LinkIn_5f:81:74 → Broadcast    802.11 309 Beacon frame, SN=1972, FN=0, Flags=........C, BI=100, SSID=Home_Network
   35   2.466250              → OneplusT_30:1f:6d (c0:ee:fb:30:1f:6d) (RA) 802.11 50 Acknowledgement, Flags=........C
   36   2.466261 D-LinkIn_5f:81:6b → OneplusT_30:1f:6d 802.11 121 QoS Data, SN=954, FN=0, Flags=.p....F.C
   37   2.466263 OneplusT_30:1f:6d → D-LinkIn_5f:81:6b 802.11 118 QoS Data, SN=493, FN=0, Flags=.p.....TC
   38   2.466265              → OneplusT_30:1f:6d (c0:ee:fb:30:1f:6d) (RA) 802.11 50 Acknowledgement, Flags=........C
```

* Which command can be used only view the deauthentication packets?

```
student@attackdefense:~$ tshark -r WiFi_traffic.pcap -Y 'wlan.fc.type_subtype==0x000c'
15694 127.895235 Motorola_31:a0:3b ? D-LinkIn_5f:81:74 802.11 66 Deauthentication, SN=1626, FN=0, Flags=........C
33477 152455.676623 Motorola_31:a0:3b ? AsustekC_c3:5e:01 802.11 66 Deauthentication, SN=876, FN=0, Flags=........C
```

* Which command can be used to only display WPA handshake packets?

```
student@attackdefense:~$ tshark -r WiFi_traffic.pcap -Y 'eapol'
  493  29.999631 D-LinkIn_5f:81:74 ? Motorola_31:a0:3b EAPOL 195 Key (Message 1 of 4)
  497  30.047179 Motorola_31:a0:3b ? D-LinkIn_5f:81:74 EAPOL 195 Key (Message 2 of 4)
  499  30.050069 D-LinkIn_5f:81:74 ? Motorola_31:a0:3b EAPOL 253 Key (Message 3 of 4)
  501  30.053057 Motorola_31:a0:3b ? D-LinkIn_5f:81:74 EAPOL 173 Key (Message 4 of 4)
  502  30.054456 Motorola_31:a0:3b ? D-LinkIn_5f:81:74 EAPOL 173 Key (Message 4 of 4)
  503  30.055784 Motorola_31:a0:3b ? D-LinkIn_5f:81:74 EAPOL 173 Key (Message 4 of 4)
```

* Which command can be used to only print the SSID and BSSID values for all beacon frames?

```
student@attackdefense:~$ tshark -r WiFi_traffic.pcap -Y 'wlan.fc.type_subtype==8' -Tfields -e wlan.ssid -e wlan.bssid | more
Home_Network    6c:19:8f:5f:81:74
Home_Network    6c:19:8f:5f:81:74
Home_Network    6c:19:8f:5f:81:74
Home_Network    6c:19:8f:5f:81:74q
```

Set B:

* What is BSSID of SSID “LazyArtists”?

```
student@attackdefense:~$ tshark -r WiFi_traffic.pcap -Y 'wlan.ssid==LazyArtists' -Tfields -e wlan.bssid
fc:b0:c4:91:71:e0
fc:b0:c4:91:71:e0
fc:b0:c4:91:71:e0
fc:b0:c4:91:71:e0
fc:b0:c4:91:71:e0
```

* SSID “Home_Network” is operating on which channel?

```
student@attackdefense:~$ tshark -r WiFi_traffic.pcap -Y 'wlan.ssid==Home_Network' -Tfields -e wlan_radio.channel | more
6
6
6
```

* Which two devices received the deauth messages? State the MAC addresses of both.

```
student@attackdefense:~$ tshark -r WiFi_traffic.pcap -Y 'wlan.fc.type_subtype==0x000c' -Tfields -e wlan.ra
6c:19:8f:5f:81:74
bc:ae:c5:c3:5e:01
```

* Which device does MAC 5c:51:88:31:a0:3b belongs to? Mention manufacturer and model number of the device.

```
student@attackdefense:~$ tshark -r WiFi_traffic.pcap -Y 'wlan.ta==5c:51:88:31:a0:3b && http' -Tfields -e http.user_agent
Dalvik/2.1.0 (Linux; U; Android 6.0; MotoG3 Build/MPI24.65-25)
Dalvik/2.1.0 (Linux; U; Android 6.0; MotoG3 Build/MPI24.65-25)
Mozilla/5.0 (Linux; Android 6.0; MotoG3 Build/MPI24.65-25) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.83 Mobile Safari/537.36
Mozilla/5.0 (Linux; Android 6.0; MotoG3 Build/MPI24.65-25) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/47.0.2526.83 Mobile Safari/537.36
```