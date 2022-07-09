---
layout: post
title: "Password Attacks"
date: "2022-07-09"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - System Attacks
---

# Password Attacks
这如何支持我的渗透测试生涯？
有能力：
* 获得对被利用机器的持久访问权
* 选择正确的方法来攻击密码
* 利用重复使用的凭据

**密码**是系统、服务和帐户免受未经授权访问的第一道防线，而且在大多数情况下，它也是唯一一道防线。 为了保护他们的用户和应用程序，操作系统必须安全地存储密码。
密码通常存储在数据库文件中。 如果它们**以明文形式存储**，攻击者只需打开密码文件或数据库即可窃取它们。

控制帐户的凭据意味着完全控制帐户及其在系统上的权限。
因此，计算机系统密码必须以**加密形式**存储； 这可以防止恶意本地用户知道其他用户的密码。

为了让攻击者更加困难，密码是使用**单向加密算法**存储的； 从加密形式开始无法知道密码的原始形式。
**加密散列函数**用于将密码从其明文形式转换为加密且安全的存储形式。

**EXAMPLE**

当您登录计算机时，输入您的用户名和密码。 操作系统获取密码，对其进行哈希处理，然后尝试将结果与密码数据库中保存的哈希进行匹配。 如果这两个值匹配，则您登录成功。
**操作系统不需要知道明文密码！**

**密码破解**是从哈希开始恢复明文密码的过程。
它基本上是一个猜测过程； 攻击者试图猜测密码，对其进行哈希处理，然后将结果与密码文件进行比较。

可以想象，尝试手动破解密码是不切实际的。 要使此类过程自动化，有两种主要策略：
* 暴力破解攻击
* 字典攻击

在下面的幻灯片中，我们将讨论两者之间的区别、优缺点以及可用于破解密码的工具。

## Brute Force Attacks（暴力破解攻击）
Q: 您如何确定找到某人的密码？
A: 您只需尝试所有这些！

这是暴力破解攻击使用的方法； 他们生成并测试所有可能的有效密码，因为这是唯一能让您确定找到用户密码的方法。

在下面的幻灯片中，我们将讨论暴力破解攻击的工作原理、它们的优势、劣势以及可用于执行暴力破解攻击的一些工具。

暴力破解攻击是确定找到某人密码的唯一方法。 要自动执行暴力破解攻击，您必须编写一个程序来生成所有可能的密码。
让我们看看如何实现暴力破解算法！

### Brute Force Algorithm（暴力破解算法）
在下面的幻灯片中，您将看到如何使用暴力破解攻击来破解密码。
这里提出的算法适用于任何长度的密码。 它从一个字符长的密码开始，并增加密码大小，直到找到有效密码。

在这里，我们看到了暴力密码攻击的伪代码版本：

```
password_found = false
password_length = 1

while password_found == false
do
    while can_create_password_of_length(password_length)
    do
        password = create_password_of_length(password_length)
        if (hash(password) match attacked_hash)
        then
            password_found = true
    done
    password_length = password_length + 1
done
```

要找到长度未知的未知密码，算法必须循环遍历每个可能的小写字符。

```
a->b->c->d->e->...->z
```

然后它将测试大写字符。

```
a->b->c->d->e->...->z
            |
            |
            v
A->B->C->D->E->...->Z
```

循环遍历大写字符后，它将测试数字。

```
a->b->c->d->e->...->z
            |
            |
            v
A->B->C->D->E->...->Z
            |
            |
            v
0->1->2->3->4->5->...->9
```

最后，它将通过特殊符号循环。

```
a->b->c->d->e->...->z
            |
            |
            v
A->B->C->D->E->...->Z
            |
            |
            v
0->1->2->3->4->5->...->9
            |
            |
            v
!->"->#->$->%->&->...->@
```

如果未找到密码，则密码长度增加到两个字符。 然后算法为第一个字符选择一个值，执行我们刚刚在第二个字符上看到的相同操作。

```
aa->ab->...->a1->...->a!->...->a@
```

如果没有找到密码，它会更改第一个字符的值并在第二个字符上再次循环。 对第一个字符的每个可能值执行此操作。

```
aa->ab->...->ba->...->z!->@@
```

算法重复。
在小写字符...

|first char|second char|
|---|---|
|a|a->b->c->d->e->...->z|
|a|A->B->C->D->E->...->Z|
|a|0->1->2->3->4->5->...->9|
|a|!->"->#->$->%->&->...->@|

大写字符...

|first char|second char|
|---|---|
|J|a->b->c->d->e->...->z|
|J|A->B->C->D->E->...->Z|
|J|0->1->2->3->4->5->...->9|
|J|!->"->#->$->%->&->...->@|

数字...

|first char|second char|
|---|---|
|9|a->b->c->d->e->...->z|
|9|A->B->C->D->E->...->Z|
|9|0->1->2->3->4->5->...->9|
|9|!->"->#->$->%->&->...->@|

和符号.

|first char|second char|
|---|---|
|=|a->b->c->d->e->...->z|
|=|A->B->C->D->E->...->Z|
|=|0->1->2->3->4->5->...->9|
|=|!->"->#->$->%->&->...->@|

如果密码长于两个字符，则算法将移动到长度为三个字符的密码，然后是四个字符，依此类推，直到找到有效密码为止！ 最终，它将尝试字母、数字和符号的所有可能组合。
只要有**足够的时间**，暴力破解攻击**总是成功的**！

### Brute Forcing Weaknesses（暴力破解的弱点）

暴力破解攻击似乎是密码破解的权威武器，因为它们总是破解密码。 在现实世界的攻击场景中，仅当其他攻击向量失败时才使用暴力破解攻击。
* 为什么会这样？
* 暴力破解的内在弱点是什么？
* 为什么不是每个攻击者在破解密码时都只使用暴力破解方法？

暴力破解攻击在每个可能的输入组合中循环。 它们从单个字符开始，然后升级为 n 个字符的长而复杂的密码。 生成的密码可以由许多字母、数字和符号的组合组成。
生成和测试所有这些密码需要时间，即使对于计算机也是如此！ 事实上，暴力破解攻击的主要警告之一是找到密码所需的时间。

如果用户选择错误的密码（简短且仅由字母组成），那么破解它可能需要几分钟或几个小时。 如果密码很长（十五个或更多字符）并且由大小写字母、数字和符号组成，那么破解它可能需要几天甚至几年的时间。
选择正确的密码意味着由于时间限制而无法进行暴力破解攻击。

### John the Ripper（开膛手约翰）
尽管编写脚本来实现暴力攻击本身并不是一项艰巨的任务，但一些工具包括有用的功能，例如会话保存和多线程支持，以及暴力破解不同密码文件格式的能力。

John the Ripper 是一款非常流行的密码破解工具，专为基于 Unix 的操作系统编写。 如今，您可以在 [Openwall 网站](https://www.openwall.com/john/)上找到适用于 Linux、Mac OSX 和 Windows 的源代码和二进制文件。
John the Ripper 可以对密码数据库进行暴力破解和基于字典的攻击。

您可以在近百种加密格式上使用它。 您可以使用 `--list=formats` 命令行选项检查它们：

```
└─# john --list=formats
Created directory: /root/.john
descrypt, bsdicrypt, md5crypt, md5crypt-long, bcrypt, scrypt, LM, AFS, 
tripcode, AndroidBackup, adxcrypt, agilekeychain, aix-ssha1, aix-ssha256, 
aix-ssha512, andOTP, ansible, argon2, as400-des, as400-ssha1, asa-md5, 
AxCrypt, AzureAD, BestCrypt, BestCryptVE4, bfegg, Bitcoin, BitLocker, 
bitshares, Bitwarden, BKS, Blackberry-ES10, WoWSRP, Blockchain, chap, 
Clipperz, cloudkeychain, dynamic_n, cq, CRC32, cryptoSafe, sha1crypt, 
sha256crypt, sha512crypt, Citrix_NS10, dahua, dashlane, diskcryptor, Django, 
django-scrypt, dmd5, dmg, dominosec, dominosec8, DPAPImk, dragonfly3-32, 
dragonfly3-64, dragonfly4-32, dragonfly4-64, Drupal7, eCryptfs, eigrp, 
electrum, EncFS, enpass, EPI, EPiServer, ethereum, fde, Fortigate256, 
Fortigate, FormSpring, FVDE, geli, gost, gpg, HAVAL-128-4, HAVAL-256-3, hdaa, 
hMailServer, hsrp, IKE, ipb2, itunes-backup, iwork, KeePass, keychain, 
keyring, keystore, known_hosts, krb4, krb5, krb5asrep, krb5pa-sha1, krb5tgs, 
krb5-17, krb5-18, krb5-3, kwallet, lp, lpcli, leet, lotus5, lotus85, LUKS, 
MD2, mdc2, MediaWiki, monero, money, MongoDB, scram, Mozilla, mscash, 
mscash2, MSCHAPv2, mschapv2-naive, krb5pa-md5, mssql, mssql05, mssql12, 
multibit, mysqlna, mysql-sha1, mysql, net-ah, nethalflm, netlm, netlmv2, 
net-md5, netntlmv2, netntlm, netntlm-naive, net-sha1, nk, notes, md5ns, 
nsec3, NT, o10glogon, o3logon, o5logon, ODF, Office, oldoffice, 
OpenBSD-SoftRAID, openssl-enc, oracle, oracle11, Oracle12C, osc, ospf, 
Padlock, Palshop, Panama, PBKDF2-HMAC-MD4, PBKDF2-HMAC-MD5, PBKDF2-HMAC-SHA1, 
PBKDF2-HMAC-SHA256, PBKDF2-HMAC-SHA512, PDF, PEM, pfx, pgpdisk, pgpsda, 
pgpwde, phpass, PHPS, PHPS2, pix-md5, PKZIP, po, postgres, PST, PuTTY, 
pwsafe, qnx, RACF, RACF-KDFAES, radius, RAdmin, RAKP, rar, RAR5, Raw-SHA512, 
Raw-Blake2, Raw-Keccak, Raw-Keccak-256, Raw-MD4, Raw-MD5, Raw-MD5u, Raw-SHA1, 
Raw-SHA1-AxCrypt, Raw-SHA1-Linkedin, Raw-SHA224, Raw-SHA256, Raw-SHA3, 
Raw-SHA384, restic, ripemd-128, ripemd-160, rsvp, RVARY, Siemens-S7, 
Salted-SHA1, SSHA512, sapb, sapg, saph, sappse, securezip, 7z, Signal, SIP, 
skein-256, skein-512, skey, SL3, Snefru-128, Snefru-256, LastPass, SNMP, 
solarwinds, SSH, sspr, Stribog-256, Stribog-512, STRIP, SunMD5, SybaseASE, 
Sybase-PROP, tacacs-plus, tcp-md5, telegram, tezos, Tiger, tc_aes_xts, 
tc_ripemd160, tc_ripemd160boot, tc_sha512, tc_whirlpool, vdi, OpenVMS, vmx, 
VNC, vtp, wbb3, whirlpool, whirlpool0, whirlpool1, wpapsk, wpapsk-pmk, 
xmpp-scram, xsha, xsha512, zed, ZIP, ZipMonster, plaintext, has-160, 
HMAC-MD5, HMAC-SHA1, HMAC-SHA224, HMAC-SHA256, HMAC-SHA384, HMAC-SHA512, 
dummy, crypt
416 formats (149 dynamic formats shown as just "dynamic_n" here)
```

由于大量使用并行化，该工具非常快。 它还可以在暴力攻击期间使用多种不同的破解策略，并且您可以指定不同的密码字符集，例如仅字母或仅数字。
在下面的幻灯片中，我们将看到如何在破解会话中使用 John the Ripper。


在这种情况下，在未经授权访问 Linux 机器后，我们制作了密码文件的副本：
* `/etc/passwd` 包含有关用户帐户的信息
* `/etc/shadow` 包含实际密码哈希

我们想用John the Ripper来破解一些密码。

#### Unshadow
John 需要用户名和密码在同一个文件中，所以我们需要使用 unshadow 实用程序。 Unshadow 与开膛手约翰一起出现。

```
unshadow passwd shadow > crackme
```

现在可以在crackme文件上开始实际的破解过程。

#### Brute force with John the Ripper
通常，一个密码文件包含多个用户的密码。如果您对破解其中一些感兴趣，可以使用 `-users` 选项。
要执行纯暴力攻击，您必须使用以下语法：

```
john -incremental -users:<users list> <file to crack>
```

要暴力破解受害者用户的密码，您必须输入（2022.2版kali没有测试成功）：

```
john -incremental -users:victim crackme
```

在完成一个简单的破解会话后，您可以在这里看到 Joth the Ripper 的输出：

```
john -incremental -users:victim crackme
Created directory: /root/.john
Loaded 1 password hash (sha512crypt [64/64])
test    (victim)
guesses: 1 time: 0:00:00:28 DONE (Sun Feb 15 15:36:43 2015) c/s: 856 trying: tomr - tina
Use the "--show" option to display all of the cracked passwords reliably
```

要显示 John 恢复的密码，您可以使用 `--show` 选项：

```
john --show crackme
victim:test:1001:1003:,,,:/home/victim:/bin/bash
```

大多数破解会话需要数小时或数天才能完成。 在长时间的破解会话中，您可以按任意键显示 John 的状态：

```
john -incremental -users:victim crackme
Loaded 1 password hash (sha512crypt [64/64])
guesses: 0 time: 0:00:00:02 0.00% c/s: 718 trying: 0101975 - 010091
guesses: 0 time: 0:00:00:08 0.00% c/s: 822 trying: andy21 - arc130
guesses: 0 time: 0:00:00:17 0.00% c/s: 848 trying: chattest - cheres96
```

即使在非常强大的机器上，通过蛮力破解一个非常长且复杂的密码也可能需要数年时间。 破解密码所需的时间是保护密码免受暴力攻击的唯一方法。
在选择密码或设置暴力攻击时请记住这一点！

## Dictionary Attacks（字典攻击）
现在我们已经了解了暴力破解攻击的工作原理，我们可以研究一种通常更快的攻击：**字典攻击**。
字典攻击不会尝试生成所有可能的密码，而是使用**常见密码字典**，测试其中包含的每一个条目。

字典攻击比纯暴力攻击更快，因为即使是大字典，在数量级上，也小于帐户可能的有效密码数量。

常用的八字长密码有**3,051,366**个，有效的八字长小写密码有**208,827,064,576**个。
**攻击期间要测试 2080 亿个密码与 300 万个密码！！！**

### Performing a Dictionary Attack（执行字典攻击）
要执行字典攻击，您需要：
* 包含要破解的散列密码的密码文件。
* 密码字典或单词表。
* 根据密码文件测试单词表中每个密码的工具。

在字典攻击期间，使用的密码恢复工具只是测试单词列表中的每个条目。
词表通常包含常用的密码，例如“admin”、“password1234”或“trustNO1”。

### Weaknesses of Dictionary Attacks（字典攻击的弱点）
**选择不当或默认密码**更容易被字典破解。
不常见的密码可能是安全的，但只有真正随机的长密码才能被认为是安全的。

“8Z3G^N>+#97#+6C:zahZ”或“deA0E6jU3W0653wMwTLd”是安全密码，但不易记住，因此您需要一个工具，例如 [Keepass](https://keepass.info/)，来安全地存储它们。 大多数用户都很懒惰，不会那样做！

根据 `https://www.security.org/how-secure-is-my-password/`，以前的密码也可以防止纯暴力攻击。

针对这些密码中的每一个的蛮力攻击将分别采取：
* 第一个 3 六亿年（3 * 10^21 年）
* 第二个5000亿年（五千亿年）

但是，这并不意味着只有强密码才能抵抗字典攻击。 它根本无法被收录在字典中！
如果密码不在单词表中，执行字典攻击的工具将无法破解密码。

### Mangling words（乱七八糟的词）
仅仅因为大小写差异或单词末尾的一些数字而无法破解密码将是一种耻辱。 因此，破解工具提供了一些选项来破坏字典中的单词。
“cat”的一些变体可能是： cat12, caT, CAT, Cat, Cat, c@t 等等……

### Dictionary Attacks with John the Ripper（开膛手约翰的字典攻击）
您可以通过将 `-wordlist` 参数传递给 John 来运行字典攻击。 您还可以指定自定义词表。
```
john -wordlist<=custom wordlist file><file to crack>
```

您还可以使用 `-rules` 参数应用一些修饰：

```
john -wordlist<=custom wordlist file> -rules <file to crack>
```

在这个例子中，我们要使用 John 默认的 wordlist 来破解 crackme 文件：

```
john -wordlist -users=victim, victim2 crackme
```

如果默认单词表不起作用，您可以使用自定义单词表。 我们首先检查自定义词表的内容，然后与 John 一起使用：

```
cat mywordlist
mysteryguy
MEGAPASSWORD

john -wordlist=mywordlist -user=victim,victim2 crackme
```

作为最后的手段，您可以启用字典修改：

```
cat mywordlist
mysteryguy
MEGAPASSWORD

john -wordlist=mywordlist -rules -users=victim, victim2 crackme
```

![dictionary-mangling.png](/img/in-post/ine/dictionary-mangling.png)

您可以在 [Github](https://github.com/danielmiessler/SecLists) 上的 OWASP Seclists Project 中找到一些有用的密码字典。 如果你使用 Kali Linux，你可以安装 seclists 包。

```
apt-get install seclists
```

安装后，您将在 `/usr/share/seclists/Passwords` 中找到字典

## Rainbow Tables（彩虹表）
还有另一种非常聪明的破解密码的方法：**彩虹表**。
彩虹表提供了计算密码哈希所需的处理时间和发起攻击所需的存储空间之间的权衡。

详细讨论彩虹表背后的数学原理超出了本课程的范围。
要了解攻击的工作原理，您只需要知道彩虹表是一个包含一个哈希函数运行结果与另一个哈希函数结果之间的链接的表。

存储彩虹表所需的空间取决于密码中允许的字符数（大小写字符、数字、符号）及其长度。 此外，用于存储密码的特定散列函数也起作用。
彩虹表的大小从数百兆字节到数百千兆字节不等。

通过在彩虹表中执行查找，破解工具将节省散列每个候选密码所需的计算时间。
这种方法将破解会话时间从几天缩短到几秒钟！

### Rainbow Tables Limitations（彩虹表限制）
彩虹表的限制是保证成功破解会话所需的存储空间。
一个彩虹表，在破解长度为 1 到 9 个字符并使用 MD5 权重 864GB 的密码时保证 96.8% 的成功率！
此外，它们可以成为破解简单和复杂短密码（5 到 8 个字符的长密码）的绝佳资源。

执行彩虹破解的一个很好的工具是 **ophcrack**。 它是一个针对Windows密码恢复的工具，所以你只能用它来破解Windows认证密码。
在其网站上，您还可以找到一些**免费的彩虹表**，其大小范围可以从 300MB 到 2TB。

Ophcrack 可以在 Windows、Linux、Unix 和 OSX 上运行。
在这里，我们看到主窗口。

![ophcrack-main-window.png](/img/in-post/ine/ophcrack-main-window.png)

要开始破解密码，只需单击主窗口中的表格，然后选择安装。

![ophcrack-install-tables.png](/img/in-post/ine/ophcrack-install-tables.png)

您可以安装免费或购买的表格，无需全部安装。

![purchased-tables.png](/img/in-post/ine/purchased-tables.png)

然后，您可以使用主窗口中的“加载”按钮加载密码文件。
您将在本模块末尾的 Meterpreter 视频中看到如何从被利用的机器获取密码文件。

![ophcrack-main-window.png](/img/in-post/ine/ophcrack-main-window.png)

最后，单击“破解”按钮开始该过程。
根据您安装的表和加密格式，您将能够恢复部分或全部密码！

![ophcrack-recover-passwords.png](/img/in-post/ine/ophcrack-recover-passwords.png)

## Conclusion
密码破解是维持对受感染机器的访问的方法之一。 在访问机器后，渗透测试人员可以转储密码数据库，然后破解它。 此活动允许他们使用有效凭据访问机器，从而能够使他们对机器的控制持久化。

您还可以将本章中看到的技术应用于其他密码资源，例如您通过 SQL 注入转储的某些凭据或您以某种方式从系统中窃取的密码文件备份。

## References
[John the Ripper](https://www.openwall.com/john/)

[Keepass](https://keepass.info/)

[KeepassX](https://www.keepassx.org/)

[How Secure is my Password](https://www.security.org/how-secure-is-my-password/)

[Ophcrack](https://ophcrack.sourceforge.io/)

[daniemiessler](https://github.com/danielmiessler)/[SecLists](https://github.com/danielmiessler/SecLists)