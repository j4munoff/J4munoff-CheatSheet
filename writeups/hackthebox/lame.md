# Lame

Máquina **Lame** de dificultad **Easy**.

![](../../.gitbook/assets/htb\_lame.png)

## Reconocimiento

Primeramente realizamos un escaneo nmap rápido para averiguar los puertos abiertos.

```
sudo nmap -p- --open -sS --min-rate 5000 -vvv -n -Pn -oG allPorts
```
