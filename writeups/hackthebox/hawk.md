# Hawk

## Información

La máquina a comprometer es **Hawk** de **HackTheBox**.

![](/.gitbook/assets/Hawk.png)

## Enumeración

Comprobamos que la máquina este activa:
```bash

┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ ping -c 1 10.10.10.102
PING 10.10.10.102 (10.10.10.102) 56(84) bytes of data.
64 bytes from 10.10.10.102: icmp_seq=1 ttl=63 time=111 ms

--- 10.10.10.102 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 110.576/110.576/110.576/0.000 ms

```

Vemos por la TTL que estamos ante una maquina Linux.

Realizamos un escaneo.

```bash
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ sudo nmap -sS -p- --open --min-rate 5000 -vvv -n -Pn 10.10.10.102 -oG allPorts
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-19 15:45 EST
Initiating SYN Stealth Scan at 15:45
Scanning 10.10.10.102 [65535 ports]
Discovered open port 21/tcp on 10.10.10.102
Discovered open port 80/tcp on 10.10.10.102
Discovered open port 22/tcp on 10.10.10.102
Discovered open port 5435/tcp on 10.10.10.102
Discovered open port 9092/tcp on 10.10.10.102
Discovered open port 8082/tcp on 10.10.10.102
Completed SYN Stealth Scan at 15:46, 16.48s elapsed (65535 total ports)
Nmap scan report for 10.10.10.102
Host is up, received user-set (0.12s latency).
Scanned at 2024-01-19 15:45:45 EST for 17s
Not shown: 65529 closed tcp ports (reset)
PORT     STATE SERVICE         REASON
21/tcp   open  ftp             syn-ack ttl 63
22/tcp   open  ssh             syn-ack ttl 63
80/tcp   open  http            syn-ack ttl 63
5435/tcp open  sceanics        syn-ack ttl 63
8082/tcp open  blackice-alerts syn-ack ttl 63
9092/tcp open  XmlIpcRegSvc    syn-ack ttl 63

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 16.57 seconds
           Raw packets sent: 80988 (3.563MB) | Rcvd: 79756 (3.190MB)

```

Realizamos ahora un escaneo mas minucioso:

```bash
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ nmap -sCV -p21,22,80,5435,8082,9092 10.10.10.102 -oN targeted
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-19 15:46 EST
Nmap scan report for 10.10.10.102
Host is up (0.11s latency).

PORT     STATE SERVICE       VERSION
21/tcp   open  ftp           vsftpd 3.0.3
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.10.14.35
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 2
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_drwxr-xr-x    2 ftp      ftp          4096 Jun 16  2018 messages
22/tcp   open  ssh           OpenSSH 7.6p1 Ubuntu 4 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 e4:0c:cb:c5:a5:91:78:ea:54:96:af:4d:03:e4:fc:88 (RSA)
|   256 95:cb:f8:c7:35:5e:af:a9:44:8b:17:59:4d:db:5a:df (ECDSA)
|_  256 4a:0b:2e:f7:1d:99:bc:c7:d3:0b:91:53:b9:3b:e2:79 (ED25519)
80/tcp   open  http          Apache httpd 2.4.29 ((Ubuntu))
|_http-title: Welcome to 192.168.56.103 | 192.168.56.103
|_http-server-header: Apache/2.4.29 (Ubuntu)
| http-robots.txt: 36 disallowed entries (15 shown)
| /includes/ /misc/ /modules/ /profiles/ /scripts/ 
| /themes/ /CHANGELOG.txt /cron.php /INSTALL.mysql.txt 
| /INSTALL.pgsql.txt /INSTALL.sqlite.txt /install.php /INSTALL.txt 
|_/LICENSE.txt /MAINTAINERS.txt
|_http-generator: Drupal 7 (http://drupal.org)
5435/tcp open  tcpwrapped
8082/tcp open  http          H2 database http console
|_http-title: H2 Console
9092/tcp open  XmlIpcRegSvc?
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port9092-TCP:V=7.94SVN%I=7%D=1/19%Time=65AADFA3%P=x86_64-pc-linux-gnu%r
SF:(NULL,45E,"\0\0\0\0\0\0\0\x05\x009\x000\x001\x001\x007\0\0\0F\0R\0e\0m\
SF:0o\0t\0e\0\x20\0c\0o\0n\0n\0e\0c\0t\0i\0o\0n\0s\0\x20\0t\0o\0\x20\0t\0h
SF:\0i\0s\0\x20\0s\0e\0r\0v\0e\0r\0\x20\0a\0r\0e\0\x20\0n\0o\0t\0\x20\0a\0
SF:l\0l\0o\0w\0e\0d\0,\0\x20\0s\0e\0e\0\x20\0-\0t\0c\0p\0A\0l\0l\0o\0w\0O\
SF:0t\0h\0e\0r\0s\xff\xff\xff\xff\0\x01`\x05\0\0\x01\xd8\0o\0r\0g\0\.\0h\x
SF:002\0\.\0j\0d\0b\0c\0\.\0J\0d\0b\0c\0S\0Q\0L\0E\0x\0c\0e\0p\0t\0i\0o\0n
SF:\0:\0\x20\0R\0e\0m\0o\0t\0e\0\x20\0c\0o\0n\0n\0e\0c\0t\0i\0o\0n\0s\0\x2
SF:0\0t\0o\0\x20\0t\0h\0i\0s\0\x20\0s\0e\0r\0v\0e\0r\0\x20\0a\0r\0e\0\x20\
SF:0n\0o\0t\0\x20\0a\0l\0l\0o\0w\0e\0d\0,\0\x20\0s\0e\0e\0\x20\0-\0t\0c\0p
SF:\0A\0l\0l\0o\0w\0O\0t\0h\0e\0r\0s\0\x20\0\[\x009\x000\x001\x001\x007\0-
SF:\x001\x009\x006\0\]\0\n\0\t\0a\0t\0\x20\0o\0r\0g\0\.\0h\x002\0\.\0m\0e\
SF:0s\0s\0a\0g\0e\0\.\0D\0b\0E\0x\0c\0e\0p\0t\0i\0o\0n\0\.\0g\0e\0t\0J\0d\
SF:0b\0c\0S\0Q\0L\0E\0x\0c\0e\0p\0t\0i\0o\0n\0\(\0D\0b\0E\0x\0c\0e\0p\0t\0
SF:i\0o\0n\0\.\0j\0a\0v\0a\0:\x003\x004\x005\0\)\0\n\0\t\0a\0t\0\x20\0o\0r
SF:\0g\0\.\0h\x002\0\.\0m\0e\0s\0s\0a\0g\0e\0\.\0D\0b\0E\0x\0c\0e\0p\0t\0i
SF:\0o\0n\0\.\0g\0e\0t\0\(\0D\0b\0E\0x\0c\0e\0p\0t\0i\0o\0n\0\.\0j\0a\0v\0
SF:a\0:\x001\x007\x009\0\)\0\n\0\t\0a\0t\0\x20\0o\0r\0g\0\.\0h\x002\0\.\0m
SF:\0e\0s\0s\0a\0g\0e\0\.\0D\0b\0E\0x\0c\0e\0p\0t\0i\0o\0n\0\.\0g\0e\0t\0\
SF:(\0D\0b\0E\0x\0c\0e\0p\0t\0i\0o\0n\0\.\0j\0a\0v\0a\0:\x001\x005\x005\0\
SF:)\0\n\0\t\0a\0t\0\x20\0o\0r\0g\0\.\0h\x002\0\.\0m\0e\0s\0s\0a\0g\0e\0\.
SF:\0D\0b\0E\0x\0c\0e\0p\0t\0i\0o\0n\0\.\0g\0e\0t\0\(\0D\0b\0E\0x\0c\0e\0p
SF:\0t\0i\0o\0n\0\.\0j\0a\0v\0a\0:\x001\x004\x004\0\)\0\n\0\t\0a\0t\0\x20\
SF:0o\0r");
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 16.45 seconds

```

Vemos que tenemos un FTP con acceso anónimo. Nos conectamos.

## Explotación

## Escalada de privilegios

