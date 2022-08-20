# Devel

Máquina **Devel** de dificultad **Easy**.

![](../../.gitbook/assets/Selection\_002.png)

## Enumeración

Primeramente realizamos un escaneo nmap rápido para averiguar los puertos abiertos.

```
sudo nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 10.10.10.5 -oG allPorts
```

![](../../.gitbook/assets/Selection\_003.png)

Vemos que tenemos abiertos los puertos 21 y 80. Realizamos ahora un escaneo mas completo a estos puertos para averiguar versiones y demás.&#x20;

```
sudo nmap -sCV -p21,80 10.10.10.5 -oN targeted
```

![](../../.gitbook/assets/Selection\_004.png)

Realizamos ahora un reconocimiento para cada puerto.

### Reconocimiento FTP

Vemos que tenemos acceso anonimo. Vamos a ver si podemos obtener información y si tenemos permiso de escritura.

![](../../.gitbook/assets/Selection\_005.png)

## Explotación

Buscamos en google posibles exploit de esta vulnerabilidad.

![](../../.gitbook/assets/google\_blue1.png)

Vemos que la primera opción, **AutoBlue**, es en python3.&#x20;

![](../../.gitbook/assets/google\_blue2.png)

Nos descargamos el exploit y lanzamos el checker para verificar si es vulnerable.

![](../../.gitbook/assets/checker.png)

Vemos que es vulnerable. Nos abrimos el fichero zzz\_exploit.py y lo modificamos para crearnos una reverse shell.

![](../../.gitbook/assets/blue1.png)

Le decimos que ejecute nc.exe, que se encuentra en nuestra maquina en un recurso compartido llamado smbFolder y nos ejecute la shell inversa.

Ahora hay que hacer 3 cosas:

Primero localizar el fichero nc.exe y crear un recurso compartido con impacket-smbserver:

```
impacket-smbserver smbFolder $(pwd) -smb2support
```

Luego ponernos a la escucha por el puerto 443.

```
sudo rlwrap nc -nlvp 443
```

Y por último lanzar el exploit

![](../../.gitbook/assets/exploit3.png)

Vemos abajo a la izquierda que hemos comprometido la máquina. La maquina es muy limitada y no nos da comandos como **whoami** o **id**. Pero hemos entrado com permisos de administrador ya que nos permite leer ambas flags por lo que no es necesario escalar privilegios.

![](../../.gitbook/assets/flags.png)

Y damos la máquina por terminada.
