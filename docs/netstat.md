# NETSTAT_ (ಠ_ಠ´)
## Intro
Netstat _(Network Statistics)_ es una herramienta de comandos que proporciona estadísticas de redes, y tiene gran utilidad para diagnosticar problemas en conexiones de red.
Es útil para entender las conexiones establecidas y detectar posibles actividades sospechosas.  

<br/>

__Windows:__ ejecutando `cmd`  o `powershell` <sub>usando su equivalente -></sub> `Get-NetTCPConnection` | `Get-NetUDPEndpoint`  
__Linux:__ en algunas versiones requiere de la instalación del paquete `net-tools` <sub>instalación -> </sub> `sudo apt-get install net-tools`

---

### Comandos básicos
- Todas las conexiones activas  
`netstat`
- Todas las conexiones y nombres de protocolos (tanto activas como esperando)  
`netstat -a`
- Interfaces de red (lista interfaces y estado)  
`netstat -i`
- Todas las conexiones + escuchando puertos con sus nombres. <sub>(Los nombres no se resuelven, acelera el resultado si hay muchas conexiones) </sub>  
`netstat -an`
- Ver el programa que está utilizando el puerto <sub>(útil para detectar programas que pueden estar escuchando en puertos
inesperados)</sub>  
`netstat -aon`
- Puerto concreto  
`netstat -aon | find “PORT”`  
    - __Proto:__ Tipo de protocolo _(TCP, UDP)_
    - __Local Address:__ Dirección y puerto local que está siendo utilizado.
    - __Foreign Address:__ Dirección y puerto del equipo remoto con el que se ha establecido la conexión.
    - __State:__ Estado de la conexión _(ESTABLISHED, LISTEN, CLOSED, etc.)_  
</br>

### Refinado
- Solo protocolo _TCP_  
`netstat -t`
- Solo protocolo _UDP_  
`netstat -u`

- Mostrar estadísticas por protocolo: <sub>útil para determinar el tipo de tráfico que domina. Aumento inusual en cierto tráfico puede indicar actividad maliciosa.</sub>  
`netstat -a -p UDP`

- Refrescar la salida periódicamente <sub>en este caso cada dos segundos, conexion en tiempo real</sub>  
`netstat -an 2`  
</br>

### Rutas IP
- Tabla de enrutamiento:  
`netstat -r`  <sub>Revisar las rutas por si entradas inesperadas, especialmente si hay sospecha de ataque man-in-the-middle o de reenvío </sub>  
- Salida
    - __Route Table _(Tabla de Enrutamiento)_:__ encabezado para identificar la tabla de enrutamiento.
    - __Network Destination _(Destino de Red)_:__ _Red_ o _Host_ al que se refiere la entrada.  
Por ejemplo, podría ser una dirección IP de red, una dirección IP específica, o el valor `0.0.0.0`, que en el contexto de una tabla de enrutamiento en Windows, indica la ruta por defecto _(análogo a 'default' en sistemas Unix-like)_
    - __Netmask _(Máscara de Red)_:__ Define qué parte de la _"Network Destination"_ es la
dirección de red y qué parte puede ser usada para _hosts_ en esa red.
    - __Gateway _(Puerta de enlace)_:__ Es el siguiente salto o el equipo al que se debe enviar el
paquete si va hacia el _"Network Destination"_ de esa entrada. Si estás en una red local, verás la dirección IP de tu router aquí para las conexiones que van fuera de tu red local.
    - __Interface _(Interfaz)_:__ Dirección IP de la interfaz de red utilizada para esta ruta específica. Por lo general, es la dirección IP de tu computadora en esa red.
    - __Metric _(Métrica)_:__ Es un número que representa el coste asociado con usar esa ruta. Windows utiliza este número para determinar la mejor ruta si hay múltiples rutas disponibles para un mismo destino. Una métrica más baja indica una ruta preferida.  

Esta tabla es utilizada por el sistema para determinar cómo enviar paquetes basándose en su destino.
Cuando intenta enviar un paquete, revisa esta tabla de arriba a abajo y utiliza la primera coincidencia que encuentra.
Por eso, las entradas más específicas se colocan generalmente en la parte superior, mientras que la ruta por defecto (0.0.0.0) está en la parte inferior.  
</br>

### Filtrar conexiones específicas
- Filtrar por una IP específica:  
`netstat -an | find “IP específica a filtrar”`

- Para filtrar por un puesto específico:  
`netstat -an | find “80”`  

- Para filtrar por estado de conexión _(por ejemplo, conexiones ESTABLISHED)_  
`netstat -an | find “ESTABLISHED”`
</br>

# Otros comandos

## Ping

## Traceroute






<br/>
*👋
