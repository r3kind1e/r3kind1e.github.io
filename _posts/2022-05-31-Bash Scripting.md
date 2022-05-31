---
layout: post
title: "Bash Scripting"
date: "2022-05-31"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Bash Shell
---

## Bash Scripting Part 1
`cat *.nmap`

```
# Nmap 7.92 scan initiated Tue May 31 03:10:15 2022 as: nmap -oN dvwa.nmap dvwa.co.uk
Nmap scan report for dvwa.co.uk (185.199.108.153)
Host is up (0.00055s latency).
rDNS record for 185.199.108.153: cdn-185-199-108-153.github.com
Not shown: 997 filtered tcp ports (no-response)
PORT    STATE SERVICE
21/tcp  open  ftp
25/tcp  open  smtp
110/tcp open  pop3

# Nmap done at Tue May 31 03:11:26 2022 -- 1 IP address (1 host up) scanned in 71.10 seconds
```

`cat *.nmap | grep "open"`

```
21/tcp  open  ftp
25/tcp  open  smtp
110/tcp open  pop3
```

`cat *.nmap | grep "open" | grep -v "filtered"`

```
21/tcp  open  ftp
25/tcp  open  smtp
110/tcp open  pop3
```

`man cut`

```
-d, --delimiter=DELIM
              use DELIM instead of TAB for field delimiter
```

`cat *.nmap | grep "open" | grep -v "filtered" | cut -d "/" -f 1`

```
21
25
110
```

`cat *.nmap | grep "open" | grep -v "filtered" | cut -d "/" -f 2`

```
tcp  open  ftp
tcp  open  smtp
tcp open  pop3
```

`man sort`

```
      -u, --unique
              with -c, check for strict ordering; without -c, output only the first of an equal run

```

`cat *.nmap | grep "open" | grep -v "filtered" | cut -d "/" -f 1 | sort -u`

```
110
21
25
```

`man xargs`

```
xargs - build and execute command lines from standard input
```

`cat *.nmap | grep "open" | grep -v "filtered" | cut -d "/" -f 1 | sort -u | xargs`

```
110 21 25
```

`man tr`

```
tr - translate or delete characters
```

`cat *.nmap | grep "open" | grep -v "filtered" | cut -d "/" -f 1 | sort -u | xargs | tr ' ' ','`

```
110,21,25
```

`cat *.nmap | grep "open" | grep -v "filtered" | cut -d "/" -f 1 | sort -u | xargs | tr ' ' ',' > ports.txt`
`cat ports.txt`

```
110,21,25
```

## Bash Scripting Part 2
`cat domains.txt`

```
127.0.0.1:1337
www.cnblogs.com
```

`curl http://www.cnblogs.com`

```
<html>
<head><title>302 Found</title></head>
<body bgcolor="white">
<center><h1>302 Found</h1></center>
<hr><center>nginx</center>
</body>
</html>
```

`curl http://127.0.0.1:1337`

```
curl: (7) Failed to connect to 127.0.0.1 port 1337 after 0 ms: Connection refused
```

`man curl`

```
-w, --write-out <格式>

完成传输后，让 curl 在标准输出上显示信息。格式是一个字符串，可以包含与任意数量的变量混合的纯文本。格式可以指定为文字“字符串”，或者您可以让 curl 从带有“@filename”的文件中读取格式，并告诉 curl 从您写入“@-”的标准输入中读取格式。

输出格式中的变量将被 curl 认为合适的值或文本替换，如下所述。所有变量都指定为 %{variable_name} 并输出一个正常的 % 你只需将它们写为 %%。您可以使用 \n 输出换行符，用 \r 输出回车，用 \t 输出制表符空格。

输出将被写入标准输出，但可以使用 %{stderr} 将其切换为标准错误。

使用%header{name} where name从最近的请求中输出 HTTP 标头是标头的不区分大小写的名称（不带尾随冒号）。标头内容与通过网络发送的内容完全相同，前导和尾随空格被修剪。在 curl 7.84.0 中添加。

注意： % 符号是 win32 环境中的特殊符号，在使用此选项时，所有出现的 % 都必须加倍。

可用的变量有：

http_code在上次检索到的 HTTP(S) 或 FTP(s) 传输中找到的数字响应代码。
response_code在上次传输中找到的数字响应代码（以前称为“http_code”）。
如果多次使用此选项，将使用最后一个。

例子：

 curl -w '%{http_code}\n' https://example.com
```

`curl --write-out "%{http_code}" http://www.cnblogs.com`

```
<html>
<head><title>302 Found</title></head>
<body bgcolor="white">
<center><h1>302 Found</h1></center>
<hr><center>nginx</center>
</body>
</html>
302 
```

`curl -h | grep output`

```
-o, --output <file>        Write to file instead of stdout
```

`curl -h | grep silent`

```
-s, --silent               Silent mode
```

`curl --write-out "%{http_code}" --output /dev/null http://www.cnblogs.com`

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   154  100   154    0     0    913      0 --:--:-- --:--:-- --:--:--   916
302
```

`curl --write-out "%{http_code}\n" --output /dev/null --silent http://www.cnblogs.com`

```
302
```

`curl --write-out "%{http_code}\n" --output /dev/null https://expired.badssl.com`

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
  0     0    0     0    0     0      0      0 --:--:-- --:--:-- --:--:--     0
curl: (60) SSL certificate problem: certificate has expired
More details here: https://curl.se/docs/sslcerts.html

curl failed to verify the legitimacy of the server and therefore could not
establish a secure connection to it. To learn more about this situation and
how to fix it, please visit the web page mentioned above.
000
```

Let's try to bypass the warning content with `--insecure` option.


```
-k，--insecure

(TLS SFTP SCP) 默认情况下，curl 建立的每个安全连接在传输发生之前都经过验证是安全的。此选项使 curl 跳过验证步骤并继续进行而不检查。

当此选项不用于使用 TLS 的协议时，curl 在继续之前验证服务器的 TLS 证书：证书包含与 URL 中使用的主机名匹配的正确名称，并且证书已由 CA 证书签名证书存储。有关详细信息，请参阅此在线资源：

 https://curl.se/docs/sslcerts.html
对于 SFTP 和 SCP，此选项使 curl 跳过known_hosts验证。known_hosts是一个文件，通常存储在用户主目录的 .ssh 子目录中，其中包含主机名及其公钥。

警告：使用此选项会使传输不安全。

例子：

curl --insecure https://example.com
```

`curl --write-out "%{http_code}\n" --output /dev/null --insecure https://expired.badssl.com`

```
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   494  100   494    0     0    326      0  0:00:01  0:00:01 --:--:--   327
200
```

`curl --write-out "%{http_code}\n" --output /dev/null --silent --insecure http://www.cnblogs.com`

redirections happen

```
302
```

```
-L，--location
跟随重定向。

(HTTP) 如果服务器报告请求的页面已移动到不同的位置（用 Location: 标头和 3XX 响应代码指示），此选项将使 curl 在新位置重做请求。如果与 --include 或-I, --head一起使用，将显示所有请求页面的标题。使用身份验证时，curl 仅将其凭据发送到初始主机。如果重定向需要 curl 到不同的主机，它将无法拦截用户+密码。另请参阅 --location-trusted 以了解如何更改此设置。您可以使用 --max-redirs 选项限制要遵循的重定向数量。

当 curl 跟随重定向并且请求是 POST 时，如果 HTTP 响应是 301、302 或 303，它将使用 GET 发送以下请求。如果响应代码是任何其他 3xx 代码，curl 将重新发送以下请求使用相同的未修改方法。

您可以使用专用选项告诉 curl 在 30 倍响应后不要将 POST 请求更改为 GET：--post301、 --post302 和--post303。

使用 --request 设置的方法会覆盖 curl 将选择使用的方法。

例子：

 curl -L https://example.com
```

`curl -L --write-out "%{http_code}\n" --output /dev/null --silent --insecure http://www.cnblogs.com`

```
200
```

`timeout --help`

```
Start COMMAND, and kill it if still running after DURATION.
```

`timeout 5 curl -L --write-out "%{http_code}\n" --output /dev/null --silent --insecure http://www.cnblogs.com`

```
200
```

`vim alivecheck.sh`

`chmod +x alivecheck.sh`

```bash
#!/bin/bash
while read line;
do
        echo $line
done < domains.txt
```

`./alivecheck.sh`

```
127.0.0.1:1337
www.cnblogs.com
```

```bash
#!/bin/bash
for protocol in 'http://' 'https://'; do
        while read line;
        do
                echo $protocol$line
        done < domains.txt
done
```

```
http://127.0.0.1:1337
http://www.cnblogs.com
https://127.0.0.1:1337
https://www.cnblogs.com
```

```bash
#!/bin/bash
for protocol in 'http://' 'https://'; do
        while read line;
        do
                curl -L --write-out "%{http_code}\n" --output /dev/null --silent --insecure http://www.cnblogs.com
        done < domains.txt
done
```

```
200
200
200
200
```

```bash
#!/bin/bash
for protocol in 'http://' 'https://'; do
        while read line;
        do
                echo $protocol$line: $(curl -L --write-out "%{http_code}\n" --output /dev/null --silent --insecure http://www.cnblogs.com)
        done < domains.txt
done
```

```
http://127.0.0.1:1337: 200
http://www.cnblogs.com: 200
https://127.0.0.1:1337: 200
https://www.cnblogs.com: 200
```

```bash
#!/bin/bash
for protocol in 'http://' 'https://'; do
        while read line;
        do
                echo $protocol$line: $(curl -L --write-out "%{http_code}\n" --output /dev/null --silent --insecure $protocol$line)
        done < domains.txt
done
```

```
http://127.0.0.1:1337: 000
http://www.cnblogs.com: 200
https://127.0.0.1:1337: 000
https://www.cnblogs.com: 200
```

```bash
#!/bin/bash
for protocol in 'http://' 'https://'; do
        while read line;
        do
                code=$(curl -L --write-out "%{http_code}\n" --output /dev/null --silent --insecure $protocol$line)
        if [ $code = "000" ]; then
                echo "$protocol$line: not responding."
        else
                echo "$protocol$line: HTTP $code"
                echo "$protocol$line: $code" >> alive.txt
        fi
        done < domains.txt
done
```

`./alivecheck.sh`

```
http://127.0.0.1:1337: not responding.
http://www.cnblogs.com: HTTP 200
https://127.0.0.1:1337: not responding.
https://www.cnblogs.com: HTTP 200
```

`cat alive.txt`

```
http://www.cnblogs.com: 200
https://www.cnblogs.com: 200
```
