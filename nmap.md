# NMAP__ (ง'̀-'́)ง
### Intro
Nmap (Network Mapper) es una herramienta de código abierto utilizada para la exploración de redes y la auditoría de seguridad.
Permite descubrir dispositivos que funcionan en una red y detectar puertos abiertos junto con varios atributos de la red.  
- Descarga: [/nmap.org/download](https://nmap.org/download.html)

--- 

### Comandos muy básicos
- __Escaneo normal:__ escaneo de los 1.000 puertos más comunes del objetivo. Respuesta: puertos y sus estados, *open* (abierto), *closed* (cerrado), o *filtered* (filtrado).  
`nmap [dirección IP o nombre de dominio]`

- __Especificar puertos:__  
`nmap [puerto, rango de puertos] [dirección IP]` <sub>por ejemplo -> </sub> `nmap -p 22,80,443 192.168.1.1`

- __Escaneo tipo ping:__ descubre qué hosts están activos en la red sin escanear sus puertos.  
`nmap -sn [dirección IP o rango]`

- __Detectar versión de servicios:__ Identifica qué servicios y qué versiones de esos servicios están corriendo en los puertos abiertos.  
`nmap -sV [dirección IP]`

- __Escaneo sistemas operativos:__ OS  
`nmap -O [dirección IP]`

---

### Comandos básicos.v2
- __Escaneo agresivo:__ combina la detección de versión de servicios, detección de sistema operativo, script de escaneo y traceroute.  
`nmap -A [dirección IP]`

- __Escaneo usando TCP SYN (half-open scan):__ Envía un paquete SYN (ver nota al pie de página) al objetivo. Es menos detectable que un escaneo completo de conexión.  
`nmap -sS [dirección IP]`

- __Escaneo UPD:__ Escanea los puertos UDP del objetivo. Es más lento que el escaneo TCP debido a la naturaleza del protocolo UDP  
`nmap -sU [dirección IP]`

- __Escaneo sin resolver nombres de dominio:__ Evita las resoluciones DNS, acelera el escaneo si no necesitas nombres de host.  
`nmap -n [dirección IP]`

- __Escaneo evasivo:__ Fragmenta los paquetes para dificultar la detección del escaneo por parte de sistemas de detección de intrusiones (IDS).  
`nmap -f [dirección IP]`

- __Definir fuente de puerto específico:__ Utiliza un puerto específico como puerto fuente para el escaneo, que puede ser útil para evadir ciertas defensas de red.
`nmap – source-port [puerto] [dirección IP]` <sub>por ejemplo -> </sub> `nmap --source-port 53 192.168.1.1`

- __Uso de scripts del nmap Scirpting Engine (NSE):__ El NSE permite a los usuarios escribir y compartir scripts para ampliar las capacidades de Nmap. `nmap --script=vuln 192.168.1.1`  
`nmap -- script=[nombre del script] [dirección IP]` <sub>por ejemplo -> </sub> `nmap --max-retries 2 --host-timeout 30s 192.168.1.1`

---

---

> [!NOTE]
> Un paquete SYN _(synchronize)_ es un tipo de paquete utilizado en el protocolo TCP para iniciar una conexión entre dos sistemas. Es el primer paso
en el llamado "handshake" de tres vías de TCP, para establecer una conexión entre cliente y servidor.
> 
El proceso "handshake" funciona así:  
1. __SYN:__ El cliente envía un paquete TCP con la bandera SYN activada al servidor para solicitar una conexión. Además, proporciona un número de secuencia inicial.  

2. __SYN-ACK:__ Si el servidor está dispuesto a aceptar la conexión, responde con un paquete que tiene tanto la bandera SYN como la bandera ACK activadas. El servidor también
proporciona su propio número de secuencia inicial.  

3. __ACK:__ El cliente responde nuevamente con un paquete con la bandera ACK activada. Este paquete reconoce la recepción del paquete SYN-ACK del servidor, y a partir de este
punto, la conexión TCP está establecida y los datos pueden comenzar a transmitirse entre el cliente y el servidor.  

El uso de paquetes SYN en escaneos, particularmente en el _"escaneo SYN"_ o _"half-open scan"_, aprovecha esta característica del protocolo TCP. En un escaneo SYN, el atacante o
el auditor envía un paquete __SYN__ a un puerto objetivo.  
[x] Si el puerto está abierto, el objetivo responderá con un paquete __SYN-ACK__.  
[ ] Si el puerto está cerrado, el objetivo responderá con un paquete __RST (reset)__.  
Sin embargo, en lugar de completar el _"handshake"_ enviando un paquete __ACK__, el atacante simplemente no responde o envía un paquete __RST (reset)__, terminando el proceso de
conexión. Por eso se llama _"half-open scan"_, ya que la conexión nunca se completa.  
***Este tipo de escaneo es más discreto y rápido que un escaneo completo de conexión.***  

<br />
*👋

