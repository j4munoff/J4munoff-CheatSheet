# Objetivo

 Se crea este repositorio de información para poder documentar todos los avances en la preparación del equipo de AFI en ciberseguridad.

La idea es tener aqui un repositorio con todos los conocimientos adquiridos de manera que sirva como libro de consulta y que pueda ayudar al resto de compañeros.

# Consejos para comenzar

Es muy complejo definir que forma de aprender ciberseguridad es la mejor. 

Despues de investigar bastante y ver diferentes técnica el que escribe opina que lo mejor es empezar a practicar cuanto antes.

En el caso del **pentesting** lo mejor es prepararse unos mínimos de entorno y de conocimiento y luego empezar a estudiar practicando con máquinas.

Si fijamos como primer objetivo la realización de **auditorias web** la secuancia recomendada debería ser la siguiente:


1 **Instalación de entorno de trabajo**: Lo primero debería ser prepararse un entorno de trabajo. En el apartado siguiente se verán formas de instalarse un entorno.
2 **Conocimientos básicos en Linux**: Al ser el entorno de trabajo un sistema operativo linux, aquellas personas que no tengan unos conocimientos mínimos de trabajo con Linux deberían realizar algún tipo de formación. En un apartado posterior se indicarán algunas vías para adquirir estos conocimientos. 
3 **Comenzar practicando**: Todas las metodologías para realizar pentesting, asi como todas las herramientas, se pueden aprender con la practica. La idea es empezar haciendo máquinas donde se puedan ver metodologías, herramientas, vulnerabilidades e irlas explotando. En un apartatado posterior se describirá la metodología de aprendizaje en base a la practica.

# Preparación de entorno de trabajo
Lo primero de todo es instalarnos una máquina virtual. Normalmente se usa [Kali Linux](https://www.kali.org/) pero tambien está muy extendido el uso de [Parrot Security](https://www.parrotsec.org/).

Previamente hay que instalarse un software de virtualización. Existen muchos pero se recomienda:

* [**VMware Workstation Pro**](https://www.vmware.com/es/products/workstation-pro.html), Es el mas recomendado pero es de pago, aunque es posible conseguir licencia muy barata.
* [**Oracle VirtualBox**](https://www.virtualbox.org/), Es gratuito y se puede hacer lo mismo que con el anterior, pero da menor rendimiento.
* [**VMware Player**](https://www.vmware.com/es/products/workstation-player.html), Es gratuito y da buen rendimiento, pero no permite personalizar redes NAT, (necesario en prácticas de pivoting), y tampoco permite tomar instantaneas de maquinas virtuales.

Una vez instalado el software de virtualización debemos proceder a instalar la maquina virtual que queramos.

* [Instalación y configuración de Kali Linux en VMWare](broken-reference)
* [Instalación y configuración de Kali Linux en VirtualBox](caracter-general/entornos-trabajo/kali-linux/kali-virtualbox.md)

# Conocimientos básicos en Linux

En caso de no tener experiencia en Linux es necesario adquirir un mínimo de conocimientos. 

No es objetivo de este curso el aprender a usar Linux, por lo que intentaremos ofrecer recursos disponibles para poder avanzar en este punto.

A continucación listamos diferentes formas de introducirnos en el mundo Linux.

## Curso de Introducción Hacking Etico by S4vitar

El creador de contenido **Marcelo Vazquez**, conocido en la red como **S4vitar** creó un curso de introducción al hacking etico en la web [Mastermind](https://www.mastermind.ac/courses/introduccion-al-hacking-etico).

Todos los videos de este curso está disponible en la siguiente dirección: [Introducción al hacking ético](https://ns2.elhacker.net/descargas/Cursos/Introducci%C3%B3n%20Hacking%20%C3%89tico%20-%20S4vitar/).

Si bien es recomendable ver todo el curso, recomendamos ver hasta el video número 14 para tener una base inicial.



## Linux desde 0. Guia para novatos by Nate Gentile

Curso de 45 minutos muy bueno para empezar. [Linux desde CERO: Guía personal para NOVATOS!](https://www.youtube.com/watch?v=knrc4q1S_q0).

