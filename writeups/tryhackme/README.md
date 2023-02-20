# TryHackMe

Aquí se mostrarán los writeups de las máquinas resueltas de **TryHackMe**.

## Conectar VPN y arrancar máquinas de TryHackMe

Para poder trabajar con **TryHackMe** es necesario crearse una cuenta, lógicamente.

Una vez estemos dentro de la plataforma, el proceso para acceder a una máquina de pruebas es sencillo. Hay que hacer dos cosas:

* Localizar y arrancar la máquina.
* Conectarnos a una VPN para poder verla desde nuestro Kali Linux.

### Localizar y arrancar una máquina TryHackMe

Entramos desde Kali con el navegador en la plataforma TryHackMe. 

Una vez dentro podemos ir a **Learn** y buscar un path o una máquina. En nuestro caso vamos a buscar la máquina **RootMe**.

![](/.gitbook/assets/try01.png)

Entramos en la página de la máquina y buscamos la opción **Start Machine** y pulsamos.

![](/.gitbook/assets/try02.png)

Tarda un minuto en arrancar y mostrarnos la IP.

![](/.gitbook/assets/try03.png)

PAsado ese minuto nos da la IP. 

![](/.gitbook/assets/try04.png)

### Conectarnos a la VPN de TryHackMe

Tenemos la IP de la máquina victima, la **10.10.98.24**. Pero si hacemos ping no la vemos:

![](/.gitbook/assets/try05.png)

Eso es porque no estamos conectados a la VPN. Para ello primero nos descargamos el fichero de configuración de la VPN. Arriba del todo nos vamos a **Access Machine**.

![](/.gitbook/assets/try06.png)

Elegimos la opción **OpenVPN**.

![](/.gitbook/assets/try07.png)


Elegimos Linux y luego descargar.

![](/.gitbook/assets/try08.png)

A veces este proceso falla. Si es así es porque por defecto os coge un servidor VPN que no está activo. También se puede hacer de la siguiente manera:

En el perfil de usuario elegimos la opción **Access**.

![](/.gitbook/assets/try09.png)

Elegimos el servidor **EU Regular 1** y descargamos el fichero.

![](/.gitbook/assets/try10.png)

**IMPORTANTE:** La descarga del fichero solo se hace una vez. Cuando ya esté descargado nos vale para siempre.

Comprobamos en el directorio descargas si se ha descargado correctamente. Ahora solo hay que abrir la conexión VPN con el comando:

```bash
sudo openvpn fichero.ovpn
```

![](/.gitbook/assets/try11.png)

Ahora, si todo va bien, nos abre la conexión y podemos hacer ping a la máquina victima.

![](/.gitbook/assets/try12.png)

Si hacemos **ifconfig** vemos que nos ha creado una nueva interface de red llamada **tun0**. 

![](/.gitbook/assets/try13.png) 

**Esa es nuestra IP de atacante ahora**.

Kali nos muestra esa IP nueva en el panel de forma automática.

![](/.gitbook/assets/try14.png)