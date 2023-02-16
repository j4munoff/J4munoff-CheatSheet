# KALI

Aqui describimos algunos comandos para **tunear** Kali Linux. Tambien es compatible con Parrot OS y otras distribuciones basadas en Debian/Ubuntu.

### Actualizar el sistema

```
sudo apt update && sudo apt upgrade -y
```

El **-y** es para que no pregunte confirmación.

### Agregar repositorios de Subleme Text

**Sublime Text** es un editor que a mi me gusta bastante y es el que suelo usar. Hay que agregar el repositorio.

```
sudo apt install software-properties-common apt-transport-https curl wget ca-certificates -y
sudo wget -O- https://download.sublimetext.com/sublimehq-pub.gpg | gpg --dearmor | sudo tee /usr/share/keyrings/sublimehq.gpg
echo 'deb [signed-by=/usr/share/keyrings/sublimehq.gpg] https://download.sublimetext.com/ apt/stable/' | sudo tee /etc/apt/sources.list.d/sublime-text.list
sudo apt-get update
```

### Instalar programas interesantes

```
sudo apt install kitty aptitude sublime-text locate -y 
```
