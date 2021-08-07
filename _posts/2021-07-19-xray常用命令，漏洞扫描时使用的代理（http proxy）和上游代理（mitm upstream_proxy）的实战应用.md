---
layout: post
title: "xray常用命令，漏洞扫描时使用的代理（http proxy）和上游代理（mitm upstream_proxy）的实战应用"
date: "2021-07-19"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags:
    - 安全评估工具
---

# xray常用命令，漏洞扫描时使用的代理（http proxy）和上游代理（mitm upstream_proxy）的实战应用

## 最常用的两个命令

使用代理模式，加载pocs目录下的所有poc，启用所有的插件。适用于日常渗透项目。

[社区贡献的poc](https://github.com/chaitin/xray)

```cmd
xray.exe webscan --listen 127.0.0.1:7777 --html-output xray-testphp.html --poc pocs/* --plugin phantasm,baseline,brute-force,cmd-injection,crlf-injection,dirscan,fastjson,jsonp,path-traversal,redirect,shiro,sqldet,ssrf,struts,thinkphp,upload,xss,xxe
```

仅启用部分插件：

```cmd
xray --config /home/usual/hack/xray/config.yaml webscan --url-file /home/usual/hack/url4.txt --plugin shiro,fastjson,struts,thinkphp,phantasm --html-output /home/usual/hack/output/HW.html
```



## 配置文件(config.yaml)

### HTTP配置

#### 漏洞扫描用的代理

配置该项后**漏洞扫描发送请求**时将使用代理发送，支持 `http`, `https` 和 `socks5` 三种格式，如:

```yaml
http:
  proxy: "http://username:password@146.137.9.45:65233"
```

```yaml
http:
  proxy: "https://username:password@146.137.9.45:65233"
```

```yaml
http:
  proxy: "socks5://username:password@146.137.9.45:65233"
```



### 被动代理配置

#### 上游代理（upstream_proxy）

> 该项配置仅对http代理本身生效，不对漏洞扫描发出的请求生效。如果想配置漏洞扫描时的代理，请参照配置文件`http`部分的配置。

`upstream_proxy`是为mitm本身配置独立的代理。也就是说，攻击者与网站正常交互的流量，走`upstream_proxy`。该配置不会影响插件在漏洞探测时的发包行为。

官方文档的说明很抽象，下面我举3个实际例子来解释：

假设有一个代理池：`http://username:password@146.137.9.45:65233`，出口IP每30秒会更换一次。有一个代理：`http://username:password@1.2.3.4:65233`，出口IP固定。

方案一：只在HTTP配置中填写漏洞扫描时使用的代理，不使用被动代理中的上游代理。xray漏洞扫描的流量和用户与Web页面正常交互的流量都会经过代理池`146.137.9.45`。

```yaml
http:
  proxy: "http://username:password@146.137.9.45:65233"                             # 漏洞扫描时使用的代理池
mitm:
  upstream_proxy: ""                    # 为 mitm 本身配置独立的代理
```

实际生产环境中存在安全设备。如果按照方案一配置，一旦漏洞扫描的出口IP被封禁，攻击者也无法用浏览器与网站正常交互了。

方案二：只使用上游代理，不使用HTTP配置中漏洞扫描的代理。漏洞扫描的流量直接到达目标主机。而访问Web页面的IP为代理`1.2.3.4`的出口IP。所以，上游代理只将浏览器的流量发送到代理`1.2.3.4`。

```yaml
http:
  proxy: ""                             # 漏洞扫描时使用的代理

mitm:
  upstream_proxy: "http://username:password@1.2.3.4:65233"                    # 为 mitm 本身配置独立的代理

```

综上，我们可以将漏洞扫描发出的请求的流量，与用户和Web页面正常交互的流量分开。

结论三：漏洞扫描发出的请求的流量走代理池，浏览器（用户和Web页面正常交互）流量走代理。

```yaml
http:
  proxy: "http://username:password@146.137.9.45:65233"                             # 漏洞扫描时使用的代理池
mitm:
  upstream_proxy: "http://username:password@1.2.3.4:65233"                    # 为 mitm 本身配置独立的代理

```

攻击者漏洞扫描的流量会从代理池的某个出口IP到达目标站点。即使该出口IP被封禁，它也会很快地更换为新的出口IP。而攻击者通过浏览器与Web应用正常交互不会受到影响。

## 总结
1. 只配置`http proxy`，xray漏洞扫描流量和浏览器流量都会走`http proxy`。
2. 只配置`mitm upstream_proxy`，漏洞扫描的流量直接到达目标站点，浏览器的流量走`mitm upstream_proxy`。
3. 同时配置`http proxy`与`mitm upstream_proxy`，漏洞扫描流量走`http proxy`，浏览器流量走`upstream_proxy`。