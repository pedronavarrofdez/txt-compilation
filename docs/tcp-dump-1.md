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

</br>

__Explicación detallada del output__   
- En los datos de ejemplo al inicio de la salida del paquete, tcpdump reporto que estaba escuchando en la interfaz `eth0` y proporcionó información sobre el tipo de enlace _(link type)_ y el tamaño de la captura en bytes:  
```bash
tcpdump: listening on eth0, link-type EN10MB (Ethernet), capture size 262144 bytes
```  

- En la segunda línea, el primer campo es la marca de tiempo _(packet's timestamp)_ del paquete, seguida del tipo de protocolo, IP:  
```
22:24:18.910372 IP
```  

- The verbose option, `-v`, ha proporcionado más detalles sobre los campos del paquete IP _(IP packet fields)_, como; _TOS_, _TTL_, _offset_, _flags_, tipo de protocolo interno (en este caso, _TCP (6)_), y longitud del paquete IP externo _(outer IP packet)_ en bytes:
```
(tos 0x0, ttl 64, id 5802, offset 0, flags [DF], proto TCP (6), length 134)
```
<sup>Son propiedades relacionadas con el paquete de red IP _(IP network packet)_.</sup>  

- En la siguiente sección, los datos muestran los sistemas que se comunican entre sí:  
```
7acb26dc1f44.5000 > nginx-us-east1-c.c.qwiklabs-terminal-vms-prod-00.internal.59788:
```  
Por defecto, `tcpdump` convertirá las direcciones IP en nombres, como se muestra en el ejemplo.  
La dirección de la flecha (>) indica la dirección del flujo de tráfico de este paquete. Cada nombre de sistema incluye un sufijo con el número de puerto (.5000 en el ejemplo), que utilizan los sistemas de origen y destino para este paquete _(source and destination)_.  

- Los datos restantes filtran los datos de cabecera *(header)* del paquete TCP interno _(inner TCP packet)_:
```
Flags [P.], cksum 0x5851 (incorrect > 0x30d3), seq 1080713945:1080714027, ack 62760789, win 501, options [nop,nop,TS val 1017464119 ecr 3001513453], length 82
```  
El campo `flags` identifica los _TCP flag_. En este caso, la P representa el flag de envío _(push flag)_ y el punto indica que se trata de un _ACK flag_. Esto significa que el paquete está enviando datos.  
El siguiente campo es el valor de la suma de comprobación TCP _(TCP checksum value)_, que se utiliza para detectar errores en los datos.  
Esta sección también incluye los números de secuencia y de acuse de recibo, el tamaño de la ventana y la longitud del paquete TCP interno _(inner TCP packet)_ en bytes.  

### 3. Captura del tráfico de red
Uso de tcpdump para guardar los datos de red capturados _(captured network data)_ en un archivo de captura de paquetes _(packet capture file)_.  
Anteriormente, en __1.__ y __2.__ uso `tcpdump` para capturar todo el tráfico de red. Ahora filtro y uso otras opciones para guardar una pequeña muestra que contenga solo datos de paquetes de red web _(puerto TCP 80)_.  

- Captura los _packet data_ en un archivo llamado `capture.pcap`:  
`sudo tcpdump -i eth0 -nn -c9 port 80 -w capture.pcap &`  
         - __`-i eth0`__: Captura data desde la interfaz `eth0`  
         - __`-nn`__: No intenta resolver ni direcciones IP ni puertos ni nombres. <sub>Es una buena practica desde el punto de vista de seguridad, ya que los datos de búsqueda podrían no ser válidos. Además, prevents malicious actors from being alerted to an investigation. </sub>  
         - __`-c9`__: Captura 9 paquetes de datos y `exit`.  
         - __`port 80`__: Filtra solo tráfico del puerto 80 _(default HTTP port)_.
         - __`-w capture.pcap`__: Guarda los datos capturados en > capture.pcap.  
         - __`&`__: Esta es una instrucción para que el shell de Bash ejecute todo en segundo plano.  
<sub> Aunque el comando se ejecute en segundo plano, parte del texto tendra salida en la terminal. El texto no afecta a los siguientes comandos que introduzcas </sub> 

</br>

- Usar _curl_ para generar un tráfico _HTTP (port 80)_:  
```
curl opensource.google.com
```
- Verify that packet data has been captured:  
```
ls -l capture.pcap
```

### 4. Filtrado de datos del paquete capturado












