
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

