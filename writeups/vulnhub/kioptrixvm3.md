# KioptrixVM3

Este primer **writeup** será un poco mas extenso de lo habitual por ser el primero.

## Información

La máquina a comprometer es **KIOPTRIX: LEVEL 1.2 (#3)** de **VulnHub**.

![](/.gitbook/assets/kio01.png)

La URL para descargar la máquina es <https://www.vulnhub.com/entry/kioptrix-level-12-3,24/>. 

Una vez descargada hay que importarla en nuestro gestor de máquinas virtuales.

![](/.gitbook/assets/kio0.png)

Destacar que esta máquina no tiene banderas. Utilizaremos como banderas:

* La contraseña del usuario **loneferret** como bandera de usuario.
* El contenido del fichero **/root/Congrats.txt** como bandera de root.

Existen varios writeups de esta máquina:

* <https://steflan-security.com/vulnhub-kioptrix-level-1-2-walkthrough/>
* <https://ratiros01.medium.com/vulnhub-kioptrix-level-1-2-3-9be13ab09011>


## Host discovery

Tenemos dos máquinas, la máquina atacante Kali Linux y la máquina victima, Kioptrix. El problema es que no sabemos la IP de la máquina victima. Utilizaremos técnicas de [Discovering hosts](/pentesting/enumeracion/discovering-hosts.md) para averiguarla. Por ejemplo **arp-scan**.

```bash
sudo arp-scan -I eth0 --localnet
```

![](/.gitbook/assets/kio03.png)

Tenemos ya la IP, 192.168.11.161. La ponemos en el panel para tenerla siempre a mano. 

```bash
cleanTarget
setTarget "Vuln - Kioptrix - 192.168.11.161"
```

![](/.gitbook/assets/kio04.png)

## Enumeración

Nos preparamos para enumerar la máquina victima. Nos preparamos nuestro block de notas. En nuestro caso abrimos Cherrytree y creamos un nodo para la máquina. Anotamos las IPs, victima y atacante, para hacer copy cuando haga falta.

![](/.gitbook/assets/kio05.png)

Abrimos consola y empezamos a enumerar.

### Averiguar tipo de sistema operativo

Con un ping vamos a ver a que nos enfrentamos, si una máquina Linux o Windows.

```bash
ping -c 1 192.168.11.161
```


![](/.gitbook/assets/kio08.png)


Normalmente las máquinas Linux devuelven un **ttl** de 64 y las máquinas windows de 128. En este caso podemos ya saber que la máquina es Linux. Lo apuntamos.

### nmap

Usamos nmap para descubrir puertos abiertos.

```bash 
sudo nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 192.168.11.161 -oG allPorts
```

![](/.gitbook/assets/kio06.png)

Vemos que hay 2 puertos abiertos en la máquina. Haceos un escaneo mas completo a los dos puertos abiertos, 22 y 80.

```bash 
nmap -sCV -p22,80 192.168.11.161 -oN targeted
```

![](/.gitbook/assets/kio07.png)

Apuntamos los resultados, **IMPORTANTE** siempre apuntar.

![](/.gitbook/assets/kio09.png)

### Averiguar versión del sistema operativo

Tenemos las versiones que corren en los dos puertos abiertos:

* 22: OpenSSH 4.7p1 Debian 8ubuntu1.2 
* 80: Apache httpd 2.2.8 (Ubuntu)

Ya se intuye que es un **Ubuntu**. Vamos a ver si damos con la versión. Para sistemas Debian/Linux se puede buscar en Google con el patrón "<version> launchpad".

Probamos con la versión del SSH:


![](/.gitbook/assets/kio10.png)

![](/.gitbook/assets/kio11.png)

Y ahora con la del http server:

![](/.gitbook/assets/kio12.png)

![](/.gitbook/assets/kio13.png)

Al coincidir ambas podemos predecir que estamos ante un **Ubuntu Hardy**. Buscando por Google nos devuelve que es un Ubuntu versión 8. Bastante antigua. 

Comprobaremos si hemos acertado cuando comprometamos la máquina.

Si en las búsquedas nos encontráramos con resultados distintos hay que pensar que lo mismo  nos enfrentamos a contenedores, tipo Docker.

Apuntamos el sistema operativo.

### Enumeración Web

Al no tener credenciales válidas no podemos hacer mucho de momento por el puerto 22 por lo que nos centraremos en el puerto 80.

Primero vamos a ver a que nos enfrentamos en cuanto a tecnologías Web. Podemos ver información con la herramienta **whatweb**

![](/.gitbook/assets/kio14.png)

Esto y nos dice que estamos ante un servidor que corre PHP y que hay un posible gestor de contenido, LotusCMS.

Veamos la web en el navegador.

![](/.gitbook/assets/kio15.png)

Examinamos la web. Revisamos el código fuente.

Nos a vamos a la página de Login y vemos que efectivamente hay un CMS llamado Lotus CMS.

![](/.gitbook/assets/kio17.png)

Vamos a ver si encontramos exploits para este cmd. Para ello se usará la herramienta **searchsploit**:

![](/.gitbook/assets/kio18.png)

Vemos que existe una vulnerabilidad que nos´permite ejecución remota de comandos para la versión 3.0.

Se trata de un script en ruby para metasploit.

Podríamos usar metasploit pero como no lo hemos tocado vamos a investigar si existe algún exploit en algo mas conocido, como bash o python.

Buscamos en Google:

![](/.gitbook/assets/kio20.png)

Parece que hay cosas, tanto en sh como en python.


## Explotación

Elijo el enlace indicado que me lleva a un fichero python.

![](/.gitbook/assets/kio21.png)

Nos copiamos el código en un fichero y ejecutamos.

![](/.gitbook/assets/kio22.png)

Nos dice como usarlo.

Nos ponemos a la escucha en una consola diferente con el comando:

```bash
nc -nlvp 4444
```

Nos hemos puesto a la escucha por el puerto 4444. Ahora ejecutamos el script con los datos correctos.

Y ...

![](/.gitbook/assets/kio23.png)

Hemos entrado en la máquina victima.

Ahora hacemos un tratamiento de la consola para poder hacer CTLR+L y CTLR+C sin que se nos vaya la consola.

```bash
script /dev/null -c bash
# CTLR + Z para poner en segundo plano la terminal
stty raw -echo; fg
reset xterm
export TERM=xterm
export shell=bash
stty rows 48 columns 193

```

Las filas y columnas de la terminal salen de ejecutar en una terminal limpia el comando 
```bash
stty size
```

## Escalada de privilegios

Somos el usuario **www-data** y no tenemos privilegios. Necesitaríamos convertirnos en algún usuario del sistema. Veamos que usuarios hay.


![](/.gitbook/assets/kio26.png)

Vemos dos usuarios, dreg y loneferret, que puedes tener mas privilegios al ser nominativos.

Hay muchas técnicas para buscar formas de escalar privilegios.

En este caso, cuando se entra con el usuario Web a un directorio que es una aplicación PHP podemos buscar ficheros con credenciales de base de datos. Siempre debemos buscar en esta ruta por patrones tipo "config" o "backup".

```bash
find . -name *config*
```

Buscamos ficheros config y visualizamos:

![](/.gitbook/assets/kio27.png)

Vaya, nos dice que en localhost hay un Mysql y nos muestra los datos de root. Vamos a mysql, que se supone instalado.

![](/.gitbook/assets/kio28.png)

Haciendo un poco de exploración en base de datos descubrimos 2 credenciales asociadas a los dos usuarios que hemos visto.

LA password está encriptada. Nos vamos primero a [CrackStation](https://crackstation.net/) e intentamos romperlas:

![](/.gitbook/assets/kio29.png)

Pues ya tenemos dos credenciales. Vamos a probar. Antes guardamos las evidencias.

![](/.gitbook/assets/kio30.png)

Ahora nos cambiamos al usuario **loneferret** con las credenciales obtenidas.

![](/.gitbook/assets/kio31.png)

Ahora lo ideal es convertirnos en root. Para ello revisamos cosas.

Siempre es conveniente mirar el fichero .bash_history por si hay algún comando o credencial suelta.

![](/.gitbook/assets/kio32.png)

Vemos que ejecuta con permisos de root el comando ht.

Revisamos que permisos sudo tiene el usuario:

![](/.gitbook/assets/kio33.png)

Efectivamente tiene permisos de root para este programa, **ht**, pero de momento no sabemos que es.

En el directorio del usuario hay un fichero que por su nombre nos llama la atención, **CompanyPolicy.README**. Lo revisamos:

![](/.gitbook/assets/kio34.png)

Vale, pues parece que es un programa de edición de ficheros, como nano.

Si lo abrimos con sudo, lo estaremos haciendo como root. 

Cuando antes lanzamos **sudo -l** realmente estamos visualizando el fichero **/etc/sudoers** que es donde se guardan los permisos sudo. Este fichero solo lo puede ver y modificar el root. Como **loneferret** no podemos.

![](/.gitbook/assets/kio35.png)

Pero si abrimos el editor ht con sudo podremos abrir y modifica el fichero y podemos añadir permisos para **/bin/bash**. Investigamos un poco por internet y vemos que ese programa utiliza las teclas F1-F10.

No interesa:

* F3 - Abre archivo
* F2: - Salva archivo 
* F10: - Sale del programa

Ejecutamos **sudo ht* y:

![](/.gitbook/assets/kio36.png)

Pulsamos F3 y escribimos la ruta /etc/sudoers y Enter

![](/.gitbook/assets/kio37.png)

![](/.gitbook/assets/kio38.png)

Añadimos **,/bin/bash** a la linea de permisos de **loneferret**:

![](/.gitbook/assets/kio39.png)

Pulsamos F2 para salvar y F10 para salir. Comprobamos ahora los permisos sudo.

![](/.gitbook/assets/kio40.png)

Pues se han guardado. Ahora si hacemos **sudo /bin/bash** deberíamos ser root.

![](/.gitbook/assets/kio41.png)

Pues funciona. Somos root y podemos entrar en /root y visualizar un fichero llamado **Congrats.txt** que en este caso es la bandera.

