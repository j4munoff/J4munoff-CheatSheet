# Laboratorios

Se describen aquí algunos laboratorios interesantes. Todos los laboratorios serán accesibles mediante Docker.

## DVWA
Lar archiconocida Damn Vulnerable Web Application es una aplicación basada en PHP y MySQL, el cual permite aplicar distintos niveles de seguridad y dificultad para nuestra formación y/o entrenamiento.

```bash
$ docker search web-dvwa
NAME                   DESCRIPTION                                     STARS               OFFICIAL            AUTOMATED
vulnerables/web-dvwa   Damn Vulnerable Web App (DVWA) is a PHP/MySQ…   154   
```
O desde este enlace de DockerHub: [DVWA](https://hub.docker.com/r/vulnerables/web-dvwa)

Lo descargamos con el siguiente comando:
```bash
docker pull vulnerables/web-dvwa
```

Y para finalizar lo arrancamos en modo interactivo y para que se borre al finalizar este:

```bash
docker run -it --rm -p 80:80 vulnerables/web-dvwa
```

Ahora podemos abrir el navegador con la url http://localhost.

![](/.gitbook/assets/laboratorios_2024-11-02-19-02-23.png)