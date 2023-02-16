# Instalación de Kali Linux en VMWare

Se describe a continuación el proceso de instalación de **Kali Linux** en VMWare. El proceso es el mismo en VMare Workstation Pro que en VMware Player.

## Descarga de Kali Linux

Kali Linux se puede descargar en varios formatos. Hay dos que nos pueden interesar.

**Versión ISO** que permite instalar una versión limpia y en el idioma que queramos.
**Imagen de máqina virtual** ya instalada.

Elegiremos la segunda opción por facilidad.

Nos descargamos la imagen de VMWare de [https://www.kali.org/get-kali/#kali-platforms](https://www.kali.org/get-kali/#kali-platforms)

![](/.gitbook/assets/kali1.png)

![](/.gitbook/assets/kali2.png)


Esto nos descargará un fichero de nombre **kali-linux-2022.4-vmware-amd64.7z** en nuestra carpeta de descargas. Hay que descomprimir este fichero con [7z](https://www.7-zip.org/download.html).

## Importación a VMware

Una vez descargado y descomprimido el archivo con la imagen tenemos que importar la máquina en VMware.

Previamente hay que mover el directorio descomprimido a la carpeta donde se deseé almacenar las maquinas virtuales.

![](/.gitbook/assets/kali3.png)

Se abre a continuación VMware y se elige la opción de abrir máquina virtual.

![](/.gitbook/assets/kali4.png)

Elegimos el fichero **.vmx** de la carpeta de la máquina virtual.

![](/.gitbook/assets/kali5.png)

Una vez abierta tenemos que configurarla, para ello pulsamos la opción **Edit virtual machine settings**.

![](/.gitbook/assets/kali6.png)

Hay que modificar las siguientes opciones:

* **Memoria**: Un mínimo de 4GB de ram.
* **Procesadores/nucleos**: Depende de la configuración de cada PC, lo dejamos en 2/2.
* **HDD**: No se toca. Viene con un disco de 80 GB y en principio es suficiente.
* **CD/DVD**: Si el ordenador no tiene dispositivo de lectura de CD/DVD debemos eliminar este dispositivo.
* **Network Adapter**: Hay que dejar la red en modo **NAT** para trabajar en un entorno de red controlado y que no se envenene la red interna.
* **USB controler**: Se deja con compatibilidad 2.0 por si se necesita conectar dispositivos a la máquina virtual, como una tarjeta Wifi USB.
* **Tarjeta de sonido**: En este entorno eliminamos el dospositivo.
* **Display**: Desactivamos los gráficos 3D.

![](/.gitbook/assets/kali7.png)

En la pestaña **Options** podemos cambiar el nomnbre de la máquina virtual y añadir un directorio compartido con el PC.

![](/.gitbook/assets/kali9.png)

Se aceptan los cambios y se arranca la máquina virtual pulsando la opción **Power on this virtual machine**, o desde el menú **VM --> Power --> Start up guest**.

![](/.gitbook/assets/kali10.png)

El usuario/password por defecto es **kali/kali**.


## Actualización y configuración de Kali Linux


Aqui se describe algunos comandos para actualizar y *tunear* Kali Linux. Tambien es compatible con Parrot OS y otras distribuciones basadas en Debian/Ubuntu.

La parte de *tunear* es muy personal y cada persona elige que herramientas prefiere y como disponerlas en la máquina. En este documento describimos un tunning muy básico.

### Actualizar el sistema

Lo primero de todo es modificar el idioma del teclado. Navegamos por el menú de aplicaciones hasta **keyboard**.

![](/.gitbook/assets/kali12.png)


Se desbloquea **system defaults** para que nos permita modificar, se añade el teclado **Spanish** y se elimina el que hay en inglés.

![](/.gitbook/assets/kali13.png)

Tambien es conveniente cambiar la hora del sistema. Para ello con el botón derecho del raton donde se muestra la hora elegimos **properties**.

![](/.gitbook/assets/kali14.png)

![](/.gitbook/assets/kali15.png)

En timezone escribimos **Europe/Madrid** y cerramos. YA nos debe de actualizar la hora.

Ahora ya podemos actualizar. Abrimos una consola del sistema:

![](/.gitbook/assets/kali11.png)

Nos ponemos como usuario "root" con el comando:

```bash
sudo su
```
Nos pedirá la contraseña del usuario **kali**.

Ejecutamos el siguiente comando para actualizar:

```bash
apt update && apt upgrade -y
```

El **-y** es para que no pregunte confirmación.

![](/.gitbook/assets/kali17.png)

Esperamos a que acabe y reiniciamos el sistema. Desde la consola se puede reiniciar con el comando:

```bash
reboot
```

### Instalar herramientas y configurar el sistema

### Agregar repositorios de Subleme Text

**Sublime Text** es un editor que a mi me gusta bastante y es el que suelo usar. Hay que agregar el repositorio.

```
sudo apt install software-properties-common apt-transport-https curl wget ca-certificates -y
sudo wget -O- https://download.sublimetext.com/sublimehq-pub.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/sublimehq.gpg
echo 'deb [signed-by=/usr/share/keyrings/sublimehq.gpg] https://download.sublimetext.com/ apt/stable/' | sudo tee /etc/apt/sources.list.d/sublime-text.list
sudo apt-get update
```

### Instalar programas interesantes

```
sudo apt install kitty aptitude sublime-text locate -y 
```
