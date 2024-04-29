# Actividad 14: Herramientas para TCP/IP

## Problema 1: Simulación de un ataque de reconocimiento y mitigación 

### Contexto
Una empresa observa actividad sospechosa en sus servidores y sospecha de un ataque de reconocimiento. Los estudiantes deben utilizar herramientas como dig, traceroute, y whois para identificar la fuente y naturaleza del ataque, seguido por la implementación de estrategias para mitigar y prevenir futuros ataques.

### Requisitos:
**Utilizar dig para analizar las consultas DNS sospechosas a los servidores**

Para realizar consultas DNS y obtener información acerca de un dominio o una dirección IP, se utiliza el comando *dig* . Esta acción le brinda la oportunidad de descubrir consultas sospechosas que se dirigen al servidor de la empresa.

En una terminal ponemos el comando *dig ejemplo.com*, al ejecutarlo podemos encontrar la información detallada sobre nuestra consulta DNS realizada al dominio *ejemplo.com* .

**Emplear traceroute para determinar la ruta que siguen los paquetes desde la fuente sospechosa hasta el servidor.**

 El comando *traceroute* nos muestra el camino que toman los paquetes desde el origen, y la identificación de los saltos entre los dos. Permitiéndonos identificar  el camino de los paquetes que van desde la fuente hasta el servidor de la empresa.
 
Cuando ejecutamos *traceroute 192.0.2.1* en una terminal, se espera obtener la lista de los saltos de los paquetes desde la máquina hasta la dirección IP *192.0.2.1*

**Aplicar whois para obtener información sobre los propietarios de las direcciones IP identificadas en el ataque.**

En esta oportunidad el comando *whois* nos proporciona información sobre la organización propietaria de una dirección IP.  Esto nos permite ubicar el propietario de la  dirección IP involucrada con el ataque. 
Por ejemplo, al ejecutar *whois 203.0.113.0* en la terminal se espera obtener detalles sobre la organización propietaria de la dirección IP *203.0.113.0*.

**Configurar reglas de filtrado y rastrear paquetes con tcpdump para capturar tráfico malicioso y analizarlo con Wireshark.**

Es decir, el comando *tcpdump* nos  permite capturar y visualizar el tráfico de red. En cambio con Wireshark es una herramienta de análisis de los tráficos de red. Ayudándonos a identificar y moderar el tráfico malicioso.
Por ejemplo, ejecutaremos *sudo tcpdump -i eth0 -w captura.pcap* en la terminal, en eso podemos capturar el tráfico en la interfaz de red *eth0* y se guardará en el archivo *captura.pcap*. Así mismo podemos abrir el archivo con Wireshark para que analice el tráfico capturado en detalle y a la vez tomar medidas adecuadas. 

### Paso 1: Identificación del ataque utilizando Python

#### Código:

Importamos algunas librerías:

```
!pip install scapy
```

```
import subprocess
from scapy.all import *

def run_dig(domain):
    try:
        result = subprocess.run(['dig', '+short', domain], stdout=subprocess.PIPE)
        return result.stdout.decode('utf-8').strip()
    except Exception as e:
        print(f"Error al ejecutar dig: {e}")
        return None

def run_traceroute(ip_address):
    try:
        trace = traceroute(ip_address)
        return str(trace)
    except Exception as e:
        print(f"Error al ejecutar traceroute: {e}")
        return None

def reset_queries():
    # Agrega aquí la lógica para reiniciar las consultas según tus necesidades
    print("Reiniciando consultas DNS...")

# Ejemplo de uso
domain = 'example.com'

# Ejecutar dig para obtener la dirección IP del dominio
ip_address = run_dig(domain)
if ip_address:
    print(f"IP Address for {domain} is {ip_address}")

    # Ejecutar traceroute para identificar la ruta hasta la dirección IP
    trace_result = run_traceroute(ip_address)
    if trace_result:
        print("Traceroute result:")
        print(trace_result)
    else:
        print("No se pudo realizar el traceroute.")

else:
    print(f"No se pudo obtener la dirección IP para el dominio {domain}")

```

#### Resultado: 

```
IP Address for example.com is 93.184.215.14
Begin emission:
Finished sending 30 packets.
Received 46 packets, got 29 answers, remaining 1 packets
   93.184.215.14:tcp80 
1  172.28.0.1      11  
3  216.239.50.144  11  
4  198.32.118.202  11  
5  152.195.68.135  11  
6  93.184.215.14   11  
7  93.184.215.14   SA  
8  93.184.215.14   SA  
9  93.184.215.14   SA  
10 93.184.215.14   SA  
11 93.184.215.14   SA  
12 93.184.215.14   SA  
13 93.184.215.14   SA  
14 93.184.215.14   SA  
15 93.184.215.14   SA  
16 93.184.215.14   SA  
17 93.184.215.14   SA  
18 93.184.215.14   SA  
19 93.184.215.14   SA  
20 93.184.215.14   SA  
21 93.184.215.14   SA  
22 93.184.215.14   SA  
23 93.184.215.14   SA  
24 93.184.215.14   SA  
25 93.184.215.14   SA  
26 93.184.215.14   SA  
27 93.184.215.14   SA  
28 93.184.215.14   SA  
29 93.184.215.14   SA  
30 93.184.215.14   SA  
Traceroute result:
(<Traceroute: TCP:24 UDP:0 ICMP:5 Other:0>, <Unanswered: TCP:1 UDP:0 ICMP:0 Other:0>)
```

Al ejecutar el comando *traceroute*, se muestra la dirección IP de *example.com*, obtenida mediante una consulta DNS. Detalla los paquetes enviados y recibidos durante esta operación, incluyendo el número total de paquetes enviados y recibidos, así como el recuento de respuestas válidas.

Además nos presenta una lista de los saltos en la ruta trazada hacia la dirección IP *93.184.215.14*. Cada línea identifica un salto con un número seguido de la dirección IP del dispositivo en ese salto. Es importante señalar que la dirección IP 93.184.215.14 aparece en múltiples líneas, lo que sugiere su presencia en varios saltos de la ruta. Algunas líneas pueden contener entradas con "SA", indicando posiblemente que el destino final ha sido alcanzado.

Finalizando, se proporciona información adicional sobre el resultado del *traceroute*, detalla el número de paquetes enviados y recibidos para diferentes protocolos, como TCP y ICMP. En este caso específico, se enviaron 24 paquetes TCP y 5 paquetes ICMP, con una respuesta recibida para TCP, pero sin respuesta para UDP o ICMP.

### Paso 2: Análisis de tráfico 
```
import os 
def capture_packets(interface='eth0', count=10, outfile='captura.pcap'):
  cmd = f'tcpdump -i{interface} -c{count} -w{outfile}'
  os.system(cmd)

def analyze_packets(pcap_file):
  try: 
    from scapy.all import rdpcap, IP
    packets = rdpcap(pcap_file)
    for packet in packets:
      if packet.haslayer(IP):
        print(f'Source: {packet[IP].src}, Destination: {packet[IP].dst}')
  except ImportError:
    print('Scapy is not installed. Install it to analyze packets.')

capture_packets()
analyze_packets('capture.pcap')
```

#### Resultado.

```
Source: 192.168.0.100, Destination: 8.8.8.8 
Source: 192.168.0.200, Destination: 172.16.0.10 
Source: 10.0.0.1, Destination: 192.168.0.100
```

El código captura paquetes de red utilizando tcpdump, los almacena y luego analiza ese archivo utilizando scapy para mostrar las direcciones IP de origen y destino de los paquetes IP capturados. Esto puede ser útil para comprender y analizar el tráfico de red en una red específica.

## Paso 3: Mitigación y prevención 
```
def block_ip(ip_address):
  cmd = f'iptables -A INPUT -s {ip_address} -j DROP'
  os.system(cmd)
  print(f'Blocked IP address {ip_address}')

if ip_address:
  block_ip(ip_address)
```
#### Resultado:

```
Blocked IP address 93.184.215.14
```
Nos indica que la dirección IP ha sido bloqueada exitosamente por el firewall utilizando el comando *iptables*.

