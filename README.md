# Objetivo

 Se crea este repositorio de información para poder documentar todos los avances en la preparación del equipo de AFI en ciberseguridad.

La idea es tener aquí un repositorio con todos los conocimientos adquiridos de manera que sirva como libro de consulta y que pueda ayudar al resto de compañeros.

# Consejos para comenzar

Es muy complejo definir que forma de aprender ciberseguridad es la mejor. 

Después de investigar bastante y ver diferentes técnica el que escribe opina que lo mejor es empezar a practicar cuanto antes.

En el caso del **pentesting** lo mejor es prepararse unos mínimos de entorno y de conocimiento y luego empezar a estudiar practicando con máquinas.

Si fijamos como primer objetivo la realización de **auditorias web** la secuencia recomendada debería ser la siguiente:


* **Instalación de entorno de trabajo**: Lo primero debería ser prepararse un entorno de trabajo. En el apartado siguiente se verán formas de instalarse un entorno.
* **Conocimientos básicos en Linux**: Al ser el entorno de trabajo un sistema operativo linux, aquellas personas que no tengan unos conocimientos mínimos de trabajo con Linux deberían realizar algún tipo de formación. En un apartado posterior se indicarán algunas vías para adquirir estos conocimientos. 
* **Comenzar practicando**: Todas las metodologías para realizar pentesting, asi como todas las herramientas, se pueden aprender con la practica. La idea es empezar haciendo máquinas donde se puedan ver metodologías, herramientas, vulnerabilidades y después explotarlas. En un apartado posterior se describirá la metodología de aprendizaje en base a la practica.

# Preparación de entorno de trabajo
Lo primero de todo es instalarnos una máquina virtual. Normalmente se usa [Kali Linux](https://www.kali.org/) pero también está muy extendido el uso de [Parrot Security](https://www.parrotsec.org/).

Previamente hay que instalarse un software de virtualización. Existen muchos pero se recomienda:

* [**VMware Workstation Pro**](https://www.vmware.com/es/products/workstation-pro.html), Es el mas recomendado pero es de pago, aunque es posible conseguir licencia muy barata.
* [**Oracle VirtualBox**](https://www.virtualbox.org/), Es gratuito y se puede hacer lo mismo que con el anterior, pero da menor rendimiento.
* [**VMware Player**](https://www.vmware.com/es/products/workstation-player.html), Es gratuito y da buen rendimiento, pero no permite personalizar redes NAT, (necesario en prácticas de pivoting), y tampoco permite tomar instantáneas de maquinas virtuales.

Una vez instalado el software de virtualización debemos proceder a instalar la maquina virtual que queramos.

* [Instalación y configuración de Kali Linux en VMWare](broken-reference)
* [Instalación y configuración de Kali Linux en VirtualBox](caracter-general/entornos-trabajo/kali-linux/kali-virtualbox.md)

# Conocimientos básicos en Linux

En caso de no tener experiencia en Linux es necesario adquirir un mínimo de conocimientos. 

No es objetivo de este curso el aprender a usar Linux, por lo que intentaremos ofrecer recursos disponibles para poder avanzar en este punto.

Existen en la red muchísimos recursos para iniciarse en Linux. A medida que recopilemos recursos interesantes los iremos añadiendo aquí. 

A continuación listamos algunas de las diferentes formas de introducirnos en el mundo Linux.

## Path **Pre Security** de TryHackMe

Dentro de este path hay un módulo llamado **Linux Fundamentals** muy interesante y con prácticas.

Conviene hacer el path entero y obtener el certificado. Anima bastante.

![](/.gitbook/assets/consejo1.png)

La ruta es <https://tryhackme.com/paths>.


## Curso de Introducción Hacking Ético by S4vitar

El creador de contenido **Marcelo Vazquez**, conocido en la red como **S4vitar** creó un curso de introducción al hacking ético en la web [Mastermind](https://www.mastermind.ac/courses/introduccion-al-hacking-etico).

Todos los videos de este curso está disponible en la siguiente dirección: [Introducción al hacking ético](https://ns2.elhacker.net/descargas/Cursos/Introducci%C3%B3n%20Hacking%20%C3%89tico%20-%20S4vitar/).

Si bien es recomendable ver todo el curso, recomendamos ver hasta el video número 14 para tener una base inicial.



## Linux desde 0. Guía para novatos by Nate Gentile

Curso de 45 minutos muy bueno para empezar. [Linux desde CERO: Guía personal para NOVATOS!](https://www.youtube.com/watch?v=knrc4q1S_q0).

# Comenzar practicando

Creemos que lo mejor es empezar practicando. Para ello existen diferentes plataformas que permiten *atacar* a máquinas vulnerables de un modo controlado y con fines didácticos. Las mas importantes son:
* **[VulnHub](https://www.vulnhub.com/)**: Esta plataforma permite la descarga de máquinas virtuales vulnerables que posteriormente se importarán a VMware y/o VirtualBox. Esta plataforma presenta unas máquinas de dificultad sencilla por lo que es ideal para comenzar. 
* **[TryHackMe](https://tryhackme.com/)**: Plataforma de pentesting muy famosa. Es muy interesante por los llamados **Learning Paths**. Son rutas de aprendizaje donde se mezcla parte teórica con parte práctica. El problema es que la versión gratuita no deja avanzar por los paths hasta el final, se limita mucho tanto los Learning Paths como las máquinas disponibles, (aquí se llaman **rooms**). La versión de premium son 10 euros/mes y permite el acceso a todo. 
* **[HackTheBox](https://www.hackthebox.com/)**: Probablemente la mejor y mas famosa plataforma de CTF (capture the flag) para practicar. Básicamente tiene dos tipos de retos, **machines** y **challenges**. Las machines son máquinas sueltas para practicar y los challenges son retos que engloban varias máquinas asi como otras actividades. En ambos casos se dividen en **activas** y **retiradas**. Con la versión gratuita solo se puede acceder a las activas, pero la política de HackTheBox impide que se publiquen soluciones de máquinas o retos activos por lo que solo se encontrarán en la red artículos o writeups de las retiradas. Para poder acceder a las retiradas hay que hacerse VIP con un coste de 12 euros/mes.
* **[Proving Grounds](https://www.offensive-security.com/labs/individual/)**: De Offensive Security, creadores de Kali Linux y que certifican el famoso OSCP. Tiene dos modalidades, **Play** y **Practice**. La parte Play es gratuita y son unas 50 máquinas Linux muy interesantes para practicar hacking web.
* **[PortSwigger - Web Security Academy](https://portswigger.net/web-security)**: PortSwigger son los creadores de BurpSuite. Presenta una serie de prácticas por Vulnerabilidad, muy interesante al principio. 

# Metodología de aprendizaje

Lo ideal es empezar viendo videos de resolución de máquinas que luego podamos intentar hacer nosotros. Los videos que recomendaremos son muy didácticos y ayudan a entender la metodología de pentesting. Deberíamos comenzar con videos de máquinas sencillas y de una metodología concreta.

Posteriormente, podríamos empezar a probar máquinas por nosotros mismos. Cuando nos quedemos atascado podemos revisar o bien un video o bien leyendo un writeup de la máquina. 

A medida que vayamos practicando iremos automatizando las cosas y menos veces tendremos que mirar.

