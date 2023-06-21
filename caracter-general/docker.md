# Docker

En esta página se describen los principales fundamentos de **Docker**, que son muy útiles en la práctica de Pentesing.

## Instalación en Linux

En Linux derivadas de Debian/Ubuntu la instalación es:

```bash
apt install docker.io
```

Una vez instalado hay que iniciar el demonio.

```bash
service docker start
```

## Crear una imagen básica

Creamos un archivo **Dockerfile** de la siguiente forma:

```console
FROM ubuntu:latest

MAINTAINER Maripili maripili@maripili.com

```

Otro ejemplo mas completo:

```console

FROM ubuntu:latest

ENV DEBIAN_FRONTEND noninteractive

RUN apt update && apt install -y nano apache2 php libapache2-mod-php mariadb-server php-mysql && net-tools

# A lo mejor hay que setear la variable short_open_tag del fichero /etc/php/8.1/apache2/php.ini de False a True

EXPOSE 80

ENTRYPOINT service apache2 start && service mariadb start && /bin/bash


```

Ahora, con el comando **docker build**  creamos una imagen.

```console
docker build -t mi_imagen:v1 .
```

Donde el parámetro **-t** define el nombre de la imagen y el punto final la ruta al **Dockerfile**.

## Descargar imagen desde un registro de imágenes

Podemos descargar imágenes con el comando **docker pull**.

```console
docker pull ubuntu:latest
```

## Ver imágenes disponibles en el sistema

Con el comando **docker images**.

## Desplegando un contenedor

Con el comando **docker run**.

```console
docker run -dit mi_imagen
```

Donde:

* **-d o --detach** para arrancar el contenedor en segundo plano.
* **-i o --interactive** para permitir la entrada interactiva al contenedor.
* **-t o --tty** para asignar un pseudorterminal al contenedor.

## Listando contenedores desplegados

Con el comando **docker ps**:

```console
# Sin opciones vemos solo los contenedores en ejecuciónj en el sistema

docker ps

# Con -a o --all vemos todos los contenedores
docker ps -a

# Con -q o --quiet vemos solos los identificadores
docker ps -q
```

## Ejecución de comandos en contenedores

Con el comando do**docker exec**.

```console
docker exec -it 123456789 bash
```

Se le pasa el id de contenedor y el comando, en este caso una bash. Los parámetros -i y -t son para que permita la entrada interactiva y para que asigne tty respectivamente.

## Comandos mas comunes

* **docker rm $(docker ps -a -q) –force**: este comando se utiliza para eliminar todos los contenedores en el sistema, incluyendo los contenedores detenidos. La opción “-q” se utiliza para mostrar sólo los identificadores numéricos de los contenedores, y la opción “–force” se utiliza para forzar la eliminación de los contenedores que están en ejecución.
* **docker rm id_contenedor**: este comando se utiliza para eliminar un contenedor específico a partir de su identificador.
* **docker rmi $(docker images -q)**: este comando se utiliza para eliminar todas las imágenes de Docker en el sistema. La opción “-q” se utiliza para mostrar sólo los identificadores numéricos de las imágenes.
* **docker rmi id_imagen**: este comando se utiliza para eliminar una imagen específica a partir de su identificador.

## Port Forwarding

Se denomina así al reenvio de puertos desde el host al contenedor. Se utiliza en el arranque del contendor con el parámetr **-p o --publish**.

```console
docker run -p 8080:80 mi_imagen
```

Esto redirige el puerto 8080 del host al 80 del contenedor.

## Monturas

Las monturas permiten compartir un directorio o archivo entre el sistema host y el contenedor. Para ello se usa el parámeto **-v o --volume** para indicar una ruta del host y un punto dmontaje en el contenedor:

```console
docker run -v /home/usuario/datos:/datos mi_imagen
```

## Docker Compose

Docker Compose es una herramienta de orquestación de contenedores que permite definir y ejecutar aplicaciones multi-contenedor de manera fácil y eficiente. Podemos describir los diferentes servicios que componen nuestra aplicación en un archivo YAML.

Si estamnos en un directorio donde tenemos el fichero **docker-compose.yml** podemos ejecutar el comando **docker-compose** y ya nos montará la imagen y el entorno.

```console
docker-compose up -d
```

## Enlaces interesantes

* [Repositorio Docker Vulnhub](https://github.com/vulhub/vulhub)
* [Simple SQL Injection Training App](https://github.com/appsecco/sqlinjection-training-app)
* [Cross-Site Scripting](https://github.com/globocom/secDevLabs)
