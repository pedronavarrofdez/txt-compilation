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

## Custom.rules
- `cat custom.rules`  
```
alert http $HOME_NET any -> $EXTERNAL_NET any (msg:"GET on wire"; flow:established,to_server; content:"GET"; http_method; sid:12345; rev:3;)
```

Las reglas estan formadas por tres componentes: **`action`**, **`header`** y **`rule options`**.  

### action
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
  
### header
``` 
http $HOME_NET any -> $EXTERNAL_NET any
```  

La cabecera define el _signature’s network traffic_, e incluye atributos como _protocolos_, _direcciones IP_ de origen y destino, _puertos_ de origen y destino y dirección del tráfico.  
- Protocolo: `http`  
- Parametros del protocolo: `$HOME_NET any -> $EXTERNAL_NET any` donde `->` indica la direccion del tráfico de `$HOME_NET` a `$EXTERNAL_NET`.  
- ANY: indica que Suricata captura el tráfico de cualquier puerto definido en la red `$HOME_NET`.  

### rule options
```
(msg:"GET on wire"; flow:established,to_server; content:"GET"; http_method; sid:12345; rev:3;)
```  

Ayudan a filtrar el tráfico de red para encontrar exactamente lo que se busca.
Las opciones de reglas van entre paréntesis y separadas por `;`.









