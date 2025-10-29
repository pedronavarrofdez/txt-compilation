# TCP DUMP - EJERCICIO nº1
## Objetivo
Uso de `tcpdump` para identificar, fltrar y capturar el tráfico de red.  
Data in a packet capture (p-cap) file and then examine the contents of the captured packet data to focus on specific types of traffic.  

Datos practicos especificos de este ejemplo:  
> nombre de usuario en la terminal de linux: `analyst`

## PRACTICA
### 1. Identificar interfaces de red
- Utilizar ___ifconfig___ para identificar las interfaces disponibles:  
`sudo ifconfig`  
> output: 
```
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1460
        inet 172.17.0.2  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:ac:11:00:02  txqueuelen 0  (Ethernet)
        RX packets 784  bytes9379957 (8.9 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 683  bytes 56880 (55.5 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0 collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 400  bytes 42122 (041.1 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 400  bytes 42122 (041.1 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0 collisions 0
```
- Utilizar tcpdump para identificar las opciones de interfaz disponibles para la captura de paquetes:  
`sudo tcpdump -D`  
<sub>Este comando también permite identificar qué interfaces de red están disponibles. Útil en sistemas que no incluyen el comando ifconfig.</sub>
### 2. Inspeccion del tráfico de red de una interfaz de red.
- Usar ___tcpdump___ para filtrar datos de paquetes de red en tiempo real _(live network packet data)_ desde la interfaz `eth0`:  
`sudo tcpdump -i eth0 -v -c5`  
                - `-i eth0`: Capturar datos específicamente desde la interfaz `eth0`.  
                - `-v`: Muestra datos detallados del paquete.  
                - `-c5`: Captura 5 paquetes de datos.
> output
```
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
10:57:33.427749 IP (tos 0x0, ttl 64, id 35057, offset 0, flags [DF], protot TCP (6), length 134)
  7acb26dc1f44.5000 > nginx-us-east1-c.c.qwiklabs-terminal-vms-prod-00.internal.59788: Flags [P.], cksum 0x5851 (incorrect > 0x30d3), seq 1080713945:1080714027, ack 62760789, win 501, options [nop,nop,TS val 1017464119 ecr 3001513453], length 82
10:57:33.427954 IP (tos 0x0, ttl 64, id 21812, offset 0, flags [DF], protot TCP (6), length 52)
  nginx-us-east1-c.c.qwiklabs-terminal-vms-prod-00.internal.59788 > 7acb26dc1f44.5000: Flags [.], cksum 0x9122 (correct), ack 82, win 510, options [nop,nop,TS val 3001513453 ecr 1017464119], length 0
2 packets captured
4 packets received by filter
0 packets dropped by kernel
```
- Explicación detallada del output
```
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
```
1. En los datos de ejemplo al inicio de la salida del paquete, tcpdump reporto que estaba escuchando en la interfaz `eth0` y proporcionó información sobre el tipo de enlace _(link type)_ y el tamaño de la captura en bytes:  

- 
- 
