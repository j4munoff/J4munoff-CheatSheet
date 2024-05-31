---
Título: Ready
Plataforma: "**Vulnyx**"
Fecha: 25-05-2024
Dificultad:
  - Easy
Técnicas:
  - "**Brute force**"
Relaciones:
  - "**Writeups**"
  - "**Vulnyx**"
  - "**6379 - Redis**"
  - "**Brute force**"
tags:
  - "#Writeups"
  - Vulnyx
  - Easy
  - Redis
  - "#Brute"
Observaciones: Escalada abusando de grupo disk
---
# Información
![](/.gitbook/assets/Pasted%20image%2020240528173258.png)

# Enumeración

Escaneamos la red.
![](/.gitbook/assets/Pasted%20image%2020240528174600.png)

Comprobamos la IP encontrada y parece que es una máquina Linux.
![](/.gitbook/assets/Pasted%20image%2020240528174709.png)

Ejecutamos un escaneo rápido de puertos abiertos.
![](/.gitbook/assets/Pasted%20image%2020240528174843.png)
Ejecutamos un escaneo mas completo a los puertos abiertos.
![](/.gitbook/assets/Pasted%20image%2020240528174945.png)
Resumimos los puertos y los servicios.

| Puerto | Servicio  |
| ------ | --------- |
| 22     | ssh       |
| 80     | http      |
| 6379   | **6379 - Redis** |
| 8080   | http      |
Parece que la página inicial de los dos servicios http son la inicial de Apache.
Revisamos las páginas sin sacar nada en claro.

Realizamos **Fuzzing** a ambas direcciones y tampoco nos muestra nada.

Nos centramos en **6379 - Redis**. Comprobamos si podemos acceder sin autorización.
![](/.gitbook/assets/Pasted%20image%2020240528181057.png)
En principio nos ha dejado. Vamos a intentar explotar por esta vía.
# Explotación

Realizamos una prueba, intentar depositar un fichero que permita ejecutar comandos en php en una ruta accesible vía web. Lo mas común es que exista la ruta /var/www/html.
![](/.gitbook/assets/Pasted%20image%2020240528181753.png)
Probamos ahora a acceder a la URL por el puerto 80 y si no funciona por el puerto 808. Funciona en este último.
![](/.gitbook/assets/Pasted%20image%2020240528181949.png)
Ejecutamos ahora una reverse shell.
![](/.gitbook/assets/Pasted%20image%2020240528182159.png)
![](/.gitbook/assets/Pasted%20image%2020240528182253.png)
Estamos en la maquina. Configuramos la TTY y enumeramos la máquina.
![](/.gitbook/assets/Pasted%20image%2020240528182750.png)

Vemos varias cosas.
* Que somos el usuario **ben**.
* Que podemos ejecutar bash como el usuario **peter**.
* Que dentro de los grupos del usuario **ben** está el grupo **disk**. Posible vector de escalada.
* Que la flag de usuario está en el home de **ben**.
# Escalada de privilegios

Después de las enumeraciones comunes optamos por explotar el grupo **disk** del usuario **ben**.
Este grupo permite al usuario montar una unidad de disco como root.
![](/.gitbook/assets/Pasted%20image%2020240528183636.png)
Se ha intentando entrar en /bin a ver si podíamos darle permisos suid a la bash, pero no ha sido posible.
Es posible entrar en /root por lo que podríamos leer la flag, pero vamos a intentar acceder. Podemos entrar en .ssh y leer la clave id_rsa. Nos la copiamos. Como vemos que está encriptada vamos a intentar romper la clave.
![](/.gitbook/assets/Pasted%20image%2020240528184116.png)
Ahora desde otra terminal nos conectamos como root con la clave.

![](/.gitbook/assets/Pasted%20image%2020240528184249.png)
Nos hemos conectado y vemos que la flag está en zip. Nos traspasamos el zip.
![](/.gitbook/assets/Pasted%20image%2020240528184519.png)
Nos lo hemos transferido. Vemos que nos solicita password.
![](/.gitbook/assets/Pasted%20image%2020240528184557.png)
Intentamos romperla.
![](/.gitbook/assets/Pasted%20image%2020240528184706.png)
Ahora lo descomprimimos y podemos leer la flag.
![](/.gitbook/assets/Pasted%20image%2020240528191507.png)