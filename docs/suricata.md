# Suricata
IDS / IPS  
Descarga: [Suricata](https://suricata.io/)  

### Enlaces
[User guide](https://docs.suricata.io/en/latest/index.html)  
[Rule management](https://docs.suricata.io/en/latest/rule-management/suricata-update.html)  
[Engine analysis and profiling](https://docs.suricata.io/en/latest/configuration/suricata-yaml.html#engine-analysis-and-profiling)  
[Json - ejemplos](https://docs.suricata.io/en/latest/output/eve/eve-json-examplesjq.html)  

---

## Principales "archivos" de Suricata 
Rules = signatures  
- **Reglas:** `custom.rules`  
- **Alertas:** `fast.log` __->__ `/var/log/suricata`  
- **Main log:** `eve.json`__->__ `/var/log/suricata`

---

## Custom.rules
- `cat custom.rules`  
```
alert http $HOME_NET any -> $EXTERNAL_NET any (msg:"GET on wire"; flow:established,to_server; content:"GET"; http_method; sid:12345; rev:3;)
```

Las reglas estan formadas por tres componentes: **`action`**, **`header`** y **`rule options`**.  

### Action
Determina la acciona a realizar si se cumplen todas las condiciones.  
Las más comunes son `alert`, `drop`, `pass` y `reject`.  

- **Alert:** indica que se genere una alerta sobre el tráfico de red seleccionado. El IDS inspeccionará los paquetes de tráfico y enviará una alerta si encuentra alguna coincidencia.  
- **Drop:** También genera una alerta, pero descarta el tráfico. El desbloqueo solo se produce cuando Suricata se ejecuta en modo IPS.
- **Pass:** Permite que el tráfico pase a través de la interfaz de red.
- **Reject:** Esta acción impide el paso del tráfico. En su lugar, se envía un paquete de reinicio _TCP reset_ y Suricata descarta el paquete correspondiente. El paquete _TCP reset_ indica a los ordenadores que dejen de enviarse mensajes entre sí.  
</br>

La regla de paso puede usarse para anular otras reglas. Se puede crear una excepción a una regla `drop` con una regla `pass`.  
Por ejemplo, la siguiente regla tiene una firma idéntica al ejemplo anterior, excepto que especifica una dirección IP concreta para permitir el paso únicamente del tráfico procedente de esa dirección:  
```
pass http 172.17.0.77 any -> $EXTERNAL_NET any (msg:"BAD USER-AGENT";flow:established,to_server;content:!”Mozilla/5.0”; http_user_agent; sid: 12365; rev:1;)
```
  
### Header
``` 
http $HOME_NET any -> $EXTERNAL_NET any
```  

La cabecera define el _signature’s network traffic_, e incluye atributos como _protocolos_, _direcciones IP_ de origen y destino, _puertos_ de origen y destino y dirección del tráfico.  
- Protocolo: `http`  
- Parametros del protocolo: `$HOME_NET any -> $EXTERNAL_NET any` donde `->` indica la direccion del tráfico de `$HOME_NET` a `$EXTERNAL_NET`.  
- ANY: indica que Suricata captura el tráfico de cualquier puerto definido en la red `$HOME_NET`.  

### Rule options
```
(msg:"GET on wire"; flow:established,to_server; content:"GET"; http_method; sid:12345; rev:3;)
```  

Ayudan a filtrar el tráfico de red para encontrar exactamente lo que se busca.
Las opciones de reglas van entre paréntesis y separadas por `;`.  
- **`msg:"GET on wire`:** _Texto de alerta_. `msg:` imprime `“GET on wire”` especificando porque salta la alerta.  
- **`flow:established,to_server`:** determina que los paquetes del cliente al servidor deben coincidir.  
(En este caso, el servidor se define como el dispositivo que responde al paquete _SYN_ inicial con un paquete _SYN-ACK_).  
- **`content:"GET"`:** indica a Suricata que busque la palabra GET en el contenido de la parte http.method del paquete.  
- **`sid:12345`:** es un valor numérico único que identifica la regla.  
- **`rev:3`:** indica la revisión de la firma, que se utiliza para identificar su versión. En este caso, la versión de revisión es la 3.  

---

## Activar una regla personalizada
- Ejecutar `suricata` usando the `custom.rules` y un archivo de red `sample.pcap`:  
`sudo suricata -r sample.pcap -S custom.rules -k none`  
  - `-r sample.pcap` especifica un archivo de entrada para simular el _tráfico de red_. En este caso, el archivo `sample.pcap`.  
  - `-S custom.rules`: indica a Suricata que utilice las reglas definidas en el archivo `custom.rules`.  
  - `-k none`: indica a Suricata que deshabilite todos los `checksum checks`_ (disable all checksum checks)_.  

Suricata añade una nueva línea de alerta al archivo `/var/log/suricata/fast.log` cuando se cumplen todas las condiciones de cualquiera de las reglas.  

- `cat /var/log/suricata/fast.log`
> output
```
11/23/2022-12:38:34.624866  [**] [1:12345:3] GET on wire [**] [Classification: (null)] [Priority: 3] {TCP} 172.21.224.2:49652 -> 142.250.1.139:80
11/23/2022-12:38:58.958203  [**] [1:12345:3] GET on wire [**] [Classification: (null)] [Priority: 3] {TCP} 172.21.224.2:58494 -> 142.250.1.139:80
```  

Cada línea del archivo `fast.log` corresponde a una alerta generada por Suricata al procesar un paquete que cumple las condiciones de generación de alertas. Cada línea de alerta incluye el mensaje que identifica la regla que la activó, así como el origen, el destino y la dirección del tráfico.

---

## Eve.json
- `cat /var/log/suricata/eve.json` mucho texto, para mejorarlo:
- `jq . /var/log/suricata/eve.json | less`
usar las letras **f** y **b** para moverse por el output, **CTRL+C** para parar el proceso, **Q** para salir de `less`

### Ejemplos
- `jq -c "[.timestamp,.flow_id,.alert.signature,.proto,.dest_ip]" /var/log/suricata/eve.json`  
Saca los datos .timestamp, flow_id, alert.signature, etc  
> output
```
["2022-11-23T12:38:34.624866+0000",14500150016149,"GET on wire","TCP","142.250.1.139"]
["2022-11-23T12:38:58.958203+0000",1647223379236084,"GET on wire","TCP","142.250.1.102"]
```  
  
- `jq "select(.flow_id==X)" /var/log/suricata/eve.json`  
Sustituyendo X por cualquiera de los valores del output anterior, saca todos los event.logs relativos a un flow_id especifico.

</br>
*👋
