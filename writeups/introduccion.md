# Introducción

Aquí se irán creando diferentes **writeups** de las máquinas realizadas.

Un writeup es una explicación de como se resuelve determinada máquina y que sirve como aprendizaje para otras personas.

# Concepto de CFT y bandera

En esta fase de aprendizaje se trabajará sobre máquinas vulnerables de tipo **CFC**, del inglés **Capture The Flag**.

Normalmente en estas máquinas se consigue la intrusión con un usuario con bajos privilegios, por ejemplo "pepito".

Para poder demostrar la intrusión en las diferentes plataformas hay que buscar la *flag*, que se encuentra normalmente en el directorio del usuario comprometido. 

La flag del usuario normalmente se encuentra en un fichero llamado **user.txt**.

En **linux** la flag del usuario se leería:

```bash
cat /home/pepito/Desktop/user.txt
```
O si no tiene Desktop suele estar en el home del usuario directamente:

```bash
cat /home/pepito/user.txt
```

En **windows** la flag del usuario se leería:

```cmd
type c:\USers\pepito\Desktop\user.txt
```

Para acabar una máquina de ipo CTF, después de la intrusión hay que buscar una vía de escalar privilegios que nos permita convertirnos en el usuario **root** en sistemas linux o en los usuarios **Administrator** o **NT Authority System** en sistemas linux.

La forma de demostrar la escalada de privilegios es obteniendo la segunda flag llamada **root.txt**.

En **linux** la flag del root se leería:

```bash
cat /root/root.txt
```
En **windows** la flag del usuario se leería:

```cmd
type c:\USers\Administrator\Desktop\user.txt
```

No olvidar que esto es solo en máquinas de aprendizaje. En auditorias reales solo hay que documentar las evidencias.

Como curiosidad, algunas empresas que contratan auditorias, colocan banderas para poder ver si es real el acceso al sistema.


## Enlaces interesantes sobre writeups

Algunos enlaces de writeups interesantes son:

* Writeups escritos
    * [Infayer: Maquinas resueltas y artículos curiosos](https://infayer.com/)
    * [Intrusionz3r0: Maquinas HTB y mas](https://intrusionz3r0.github.io/posts/Windows/)
    * [Maquinas resueltas TryhackMe y mas](https://guarina0x0.github.io/)
    * [Foro de Xdann1](https://xdann1.github.io/)
    * [CheatSheet de Kevin López](https://fackingamatherae.gitbook.io/cheatsheet/)
    * [Sckull Blog - WriteUps](https://sckull.github.io/)
    * [Windows Cheet-Sheet by Intrusionz3r0](https://intrusionz3r0.github.io/posts/Windows/)
* Writeups en video
    * [S4viOnLive de S4vitar](https://www.youtube.com/c/S4viOnLive/videos)
    * [Securiters de Marta Barrio](https://www.youtube.com/@Securiters/videos)
    * [Víctor García](https://www.youtube.com/@takito1812/videos)


    