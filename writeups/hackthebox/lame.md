# Lame

Máquina **Lame** de dificultad **Easy**.

![](../../.gitbook/assets/htb\_lame.png)

## Emumeración

Primeramente realizamos un escaneo nmap rápido para averiguar los puertos abiertos.

```
sudo nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn 10.10.10.3 -oG allPorts
```

![](../../.gitbook/assets/nmap\_1.png)

Vemos que tenemos abiertos los puertos 21, 22, 139, 445 y 3632. Realizamos ahora un escaneo mas completo a estos puertos para averiguar versiones y demás.

```
sudo nmap -sCV -p21,22,139,445,3632 10.10.10.3 -oN targeted
```

![](../../.gitbook/assets/nmap\_2.png)

| Puerto | Servicio | Versión                  | Observaciones                     |
| ------ | -------- | ------------------------ | --------------------------------- |
| 21     | ftp      | vsftpd 2.3.4             | Permite acceso anónimo            |
| 22     | ssh      | OpenSSH 4.7p1            |                                   |
| 139    | smb      | Samba smbd 3.x-4-x       | No se determina la versión exacta |
| 445    | smb      | Samba smbd 3.0.20-Debian |                                   |
| 3632   | distccd  | distccd v1 4.2.4         |                                   |

Realizamos a continuación un reconocimiento para cada puerto.

### Reconocimiento FTP

Vemos que tenemos acceso anonimo. Vamos a ver si podemos obtener información y si tenemos permiso de escritura.

![](../../.gitbook/assets/ftp.png)

Vemos que no obtenemos ficheros ni podemos escribir con el usuario anónimo. De momento dejamos este servicio.
