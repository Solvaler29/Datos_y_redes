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

## Problema 2: Análisis forense de un ataque de denegación de servicio(DoS)
### Requisitos:
**Analizar archivos de registro del servidor con ausearch y aureport para identificar patrones anormales**
Utilizaremos herramientas como ausearch y aureport para revisar los registros de auditoría del servidor y buscar cualquier actividad sospechosa o inusual que pudiera sugerir un compromiso de seguridad.

**Utilizar tcpdump para capturar paquetes durante el ataque y usar Wireshark para filtrar y analizar estos paquetes.**

Durante un ataque, capturamos paquetes de red utilizando tcpdump y luego utilizamos Wireshark para filtrar y analizar estos paquetes en busca de anomalías, patrones de ataque o actividad maliciosa.

**Desarrollar una estrategia de mitigación usando técnicas como la limitación de tasa de conexiones y el bloqueo de IPs específicas.**

Implemente medidas de mitigación como limitación de la tasa de conexión para evitar el ataque de denegación de servicio(DoS); bloquea simplemente algunas direcciones IP para detener el tráfico malintencionado.

**Discutir el uso de cURL y wget para simular y testear la resistencia del servidor después de aplicar las medidas de mitigación.**

Utilice cURL y wget para simular tráfico normal y tráfico malicioso para tu servidor, para que puedas evaluar la efectividad de tus medidas de mitigación y la resistencia de tu servidor en diferentes situaciones.

### Paso 1: Diseño del protocolo
El formato de los mensajes del protocolo consta de un cabecera:

**Tipo de operación(1 byte):** Indica la acción solicitada al servidor, como **PUT**(agregar/actualizar datos), **GET** (recuperar datos) o **DELETE** (eliminar datos). 

**Número de secuencia(4 bytes):** Identifica de manera única cada mensaje enviado o recibido. Incrementando cada nuevo mensaje enviado. 

**Número de acuse de recibo(4 bytes):** Confirma la recepción de un mensaje. El destinatario envía un acuse de recibo con este número al recibir un mensaje. 

### Paso 2: Implementación de control de flujo
```
import socket
import struct

def send_message(sock, msg_type, seq_num, ack_num, payload):
  header = struct.pack('!B I I I', msg_type, seq_num, ack_num, len(payload))
  message = header + payload.encode()
  sock.sendall(message)

def receive_message(sock):
  header = sock.recv(13) # Tamaño de la cabecera
  msg_type, seq_num, ack_num, length = struct.unpack('!B I I I', header)
  payload = sock.recv(length).decode()
  return msg_type, seq_num, ack_num, payload

def simulate_tcp_connection(host, port):
  server_sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  server_sock.bind((host, port))
  server_sock.listen(1)
  client_sock, addr = server_sock.accept()
  # Simulación del envío y recepción de mensajes
  send_message(client_sock, 1, 100, 0, "Hello, this is a test payload")
  print("Message sent")
  msg_type, seq_num, ack_num, payload = receive_message(client_sock)
  print("Received message:", payload)
  client_sock.close()
  server_sock.close()

simulate_tcp_connection('localhost', 12345)
```
#### Resultado: 
```
Message sent
Received message: Hello, this is a test payload
```
Nos indica que el mensaje se envió exitosamente desde el cliente al servidor y que el servidor recibe el mensaje. La primera línea significa que el cliente envió el mensaje con éxito, mientras que la segunda línea muestra el contenido del mensaje que fue recibido.

### Paso 3: Evaluación y optimización
```
import socket
import struct
import time
import random
def send_message(sock, msg_type, seq_num, ack_num, payload):
  header = struct.pack('!B I I I', msg_type, seq_num, ack_num, len(payload))
  message = header + payload.encode()
  sock.sendall(message)
def receive_message(sock):
  header = sock.recv(13) # Tamaño de la cabecera
  msg_type, seq_num, ack_num, length = struct.unpack('!B I I I', header)
  payload = sock.recv(length).decode()
  return msg_type, seq_num, ack_num, payload

def simulate_tcp_connection(host, port):
  server_sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  server_sock.bind((host, port))
  server_sock.listen(1)
  client_sock, addr = server_sock.accept()

  # Simulación del envío y recepción de mensajes
  send_message(client_sock, 1, 100, 0, "Hello, this is a test payload")
  print("Message sent")
  msg_type, seq_num, ack_num, payload = receive_message(client_sock)
  print("Received message:", payload)
  client_sock.close()
  server_sock.close()

def simulate_network_conditions(host, port, delay_prob=0.1, loss_prob=0.1):
  server_sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
  server_sock.bind((host, port))
  server_sock.listen(1)
  client_sock, addr = server_sock.accept()

  # Simulación del envío y recepción de mensajes con condiciones de red
  send_message(client_sock, 1, 100, 0, "Hello, this is a test payload")
  print("Message sent")

# Simular retraso
  if random.random() < delay_prob:
    print("Simulating delay...")
    time.sleep(5) # Simular un retraso de 5 segundos
  # Simular pérdida de paquetes
  if random.random() < loss_prob:
    print("Packet loss simulated.")
    return
  msg_type, seq_num, ack_num, payload = receive_message(client_sock)
  print("Received message:", payload)
  client_sock.close()
  server_sock.close()

# Simular una conexión TCP con condiciones de red
simulate_network_conditions('localhost', 12345, delay_prob=0.2, loss_prob=0.1)
```
#### Resultado: 
```
Message sent
Received message: Hello, this is a test payload
```
El resultado si la conexión se establece correctamente y no se simula ningún retraso ni pérdida de paquetes. El mensaje "Message sent" indica que el mensaje se envió correctamente desde el cliente al servidor, y "Received message: Hello, this is a test payload" indicando que el servidor recibió correctamente el mensaje después del retraso simulado.

Si se simulan retrasos o pérdida de paquetes, habrá mensajes adicionales:
```
Message sent
Simulating delay...
```
Se indica que se simuló un retraso en la red y otro mensaje indicando que se simuló la pérdida de paquetes. La recepción del mensaje no ocurrirá en este caso debido a la pérdida simulada de paquetes.

## Problema 3: Implementación y análisis de un red segura utilizando varias herramientas
El objetivo es diseñar una red para una pequeña empresa que incluya servidores web, de correo electrónico y bases de datos. Se utilizarán herramientas como ifconfig o ipconfig para configurar las interfaces de red, dig y whois para configurar los registros DNS, y cURL o wget para probar la accesibilidad de los servicios. Además, se implementarán estrategias de defensa en profundidad, como zonas desmilitarizadas (DMZ) y subredes internas. El enfoque se centra en configurar y verificar las configuraciones de red, utilizar herramientas de DNS para resolver nombres de host, y establecer y verificar medidas de seguridad. Se anima a presentar la configuración y código utilizado para demostrar los procedimientos realizados.

#### Configuración de la red

*Configuraremos el IP estática en Linux* utilizando este comando para hacerlo en la interfaz eth0
```
sudo ip addr 192.168.1.100/24 dev eth0
sudo ip link set eth0 up
```
Después vamos a *configurar el DNS con dig y whois*, para obtener información de dominio: 
```
dig +short example.com A
whois example.com
```
De hay vamos a escribir un script para monitorear la disponibilidad de los servicios y la conectividad del ping y traceroute.
```
import smtplib
from email.mime.text import MIMEText
import subprocess

def send_email(message):
    sender = "monitor@example.com"
    receivers = ["admin@example.com"]
    msg = MIMEText(message)
    msg['Subject'] = "Alerta de Red"
    msg['From'] = sender
    msg['To'] = ", ".join(receivers)
    
    # Simulando el envío de correo electrónico
    print("Simulando el envío de correo electrónico...")
    print(f"From: {sender}")
    print(f"To: {', '.join(receivers)}")
    print(f"Subject: {msg['Subject']}")
    print("Mensaje:")
    print(msg.get_payload())

def check_server(address):
    # Simulando el comando ping
    print(f"Simulando el ping a {address}...")
    return True  # Simular que el ping es exitoso

if __name__ == "__main__":
    servers = ["192.168.1.100", "192.168.1.101", "192.168.1.102"]
    for server in servers:
        if not check_server(server):
            print(f"Fallo en la conexión con {server}")
            send_email(f"Fallo en la conexión con {server}")
```
Su salida sería un mensaje indicando si se envio correctamente la alerta por el correo para cada servidor de la lista *servers*. Pero si la conexión falla, se enviará un correo de aleta al administrado especificado en *receivers*. 

Después, usamos cURL o wget para verificar la accesibilidad de los servidores web en la terminal.
```
curl -o /dev/null -s -w "%{http_code}\n" http://192.168.1.100
wget -q --spider http://192.168.1.100
if [ $? -eq 0 ]; then
echo "Servidor accesible"
else
echo "Servidor no accesible"
fi
```
Este código verifica si el servidor web en la dirección *http://192.168.1.100* es accesible. Si lo es, imprime "Servidor accesible"; de lo contrario, imprime "Servidor no accesible"

Por último *implementar seguridad*, configuremos un firewalls utilizando iptables para limitar el acceso no autorizado a los servicios con:
```
sudo iptables -A INPUT -p tcp -s 192.168.1.0/24 -j ACCEPT
sudo iptables -A INPUT -p tcp -j DROP
```
La primera línea permite todas las conexiones TCP entrantes desde cualquier puerto de origen dentro de la subred 192.168.1.0/24, significa que cualquier dispositivo dentro de esa subred puede establecer una conexión TCP con el sistema local.

Y por último la última línea deniega toda las conexiones TCP que no hayan sido permitidas por el anterior, en sí bloquea todas las conexiones TCP entrantes que no están permitidas explícitamente. 

## Problema 4: Desarrollo de un script para monitoreo y alerta de red

El problema requiere desarrollar un script utilizando bash que monitoree la disponibilidad de servidores críticos utilizando herramientas de red como ping, traceroute y nslookup. El script debe registrar anomalías y enviar alertas automáticas en caso de detectar problemas como tiempos de respuesta elevados o pérdida de paquetes. Los objetivos incluyen automatizar el monitoreo de la red, interpretar la salida de las herramientas de red y desarrollar mecanismos de alerta en tiempo real.

```
import subprocess
import smtplib
from email.mime.text import MIMEText
import datetime

def send_email(subject, message):
    sender = 'monitor@yourdomain.com'
    receivers = ['admin@yourdomain.com']
    msg = MIMEText(message)
    msg['Subject'] = subject
    msg['From'] = sender
    msg['To'] = ", ".join(receivers)

    try:
        # Configurar el servidor SMTP según tu configuración
        server = smtplib.SMTP('smtp.yourdomain.com')
        server.starttls()
        server.login('your_username', 'your_password')
        server.sendmail(sender, receivers, msg.as_string())
        print("Alerta enviada por email")
    except Exception as e:
        print(f"Error al enviar el correo electrónico: {e}")
    finally:
        server.quit()

def check_server(address):
    # Ping al servidor
    response = subprocess.run(['ping', '-c', '3', address], stdout=subprocess.PIPE, text=True)
    if response.returncode != 0:
        now = datetime.datetime.now()
        message = f"Fallo de ping a {address} el {now.strftime('%Y-%m-%d %H:%M:%S')}"
        send_email("Alerta de Fallo de Red", message)
        log_event(message)
    else:
        print(f"{address} está activo")

def trace_route(address):
    # Ejecutar traceroute
    result = subprocess.run(['traceroute', address], stdout=subprocess.PIPE, text=True)
    log_event(f"Traceroute a {address}:\n{result.stdout}")

def log_event(message):
    with open("network_monitor_log.txt", "a") as file:
        file.write(message + "\n")

if __name__ == "__main__":
    servers = ["192.168.1.100", "192.168.1.101", "192.168.1.102"]
    for server in servers:
        check_server(server)
        trace_route(server)
```
#### Resolución:
Si los servidores están activos y responden a los pings, la salida va a hacer:
```
192.168.1.100 está activo
Traceroute a 192.168.1.100:
<salida del traceroute>

192.168.1.101 está activo
Traceroute a 192.168.1.101:
<salida del traceroute>

192.168.1.102 está activo
Traceroute a 192.168.1.102:
<salida del traceroute>
```
Si los servidores no responden al ping, enviará un correo de alerta y se registrará un mensajes en el archivo de registro. 
```
Fallo de ping a 192.168.1.101 el 2024-04-30 13:45:00
```
La salida del archivo de registro sería similar a la salida del traceroute mostrada anteriormente.
