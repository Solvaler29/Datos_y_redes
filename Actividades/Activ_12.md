# ACTIVIDAD 12: Construyendo una área de red local

## Problema 1: Diseño y Simulación de una red metro Ethernet con QoS

### Contexto: 

Una empresa necesita diseñar una red Metro Ethernet para conectar varias sucursales en una área metropolitana. Se requiere calidad de servicio (QoS) para priorizarel tráfico de voz sobre IP (VoIP) sobre el tráfico regular de datos.

#### Requisitos:

● **Explique cómo utilizaría VLAN y Trunk para segmentar y gestionar el tráfico en la red.**

Los VLANs (Virtual Local Area Networks) permiten segmentar la red en grupos independientes, como sucursales de una empresa, mejorando la seguridad y reduciendo el tráfico al limitar las transmisiones de broadcast a cada VLAN. El Trunking es una técnica que transporta el tráfico de varias VLANs a través de un solo enlace físico. El protocolo IEEE 802.1Q define cómo implementar el trunking, etiquetando los paquetes con el ID de VLAN correspondiente para dirigirlos a la VLAN correcta en el extremo receptor del enlace.

● **Diseñe una política de QoS que utilice Ethernet y VLAN tags para garantizar la prioridad del tráfico de VoIP.**

La Calidad de Servicio (QoS) es crucial para priorizar el tráfico de VoIP sobre el tráfico de datos regulares. Una técnica efectiva es la "priorización en etiquetas", que asigna alta prioridad a los paquetes de VoIP. En redes Ethernet, se puede utilizar la funcionalidad de "clase de servicio" (CoS) disponible en las etiquetas VLAN (IEEE 802.1Q). El CoS en la etiqueta VLAN tiene un valor de 3 bits, lo que permite hasta 8 niveles de prioridad. Configurar la red para etiquetar todos los paquetes de VoIP con un valor de CoS más alto garantiza su priorización durante la transmisión.

● **Simule el entorno utilizando software de simulación de redes y describa cómo los conceptos de Fast Ethernet, Gigabit Ethernet, y Full-duplex mode se aplican en este diseño.**

Los conceptos mencionados describen tecnologías y modos de operación en una red:

- **Fast Ethernet (100BASE-T):** Versión de Ethernet con velocidades de datos de hasta 100 Mbps.
- **Gigabit Ethernet (1000BASE-T):**  Versión más rápida de Ethernet con velocidades de hasta 1000 Mbps (1 Gbps), recomendada para enlaces troncales de alta capacidad.
- **Full-duplex mode:** Permite enviar y recibir datos simultáneamente, duplicando la capacidad de transmisión del enlace. Es útil para el tráfico de VoIP que requiere comunicación bidireccional constante.


### Parte 1: Diseño de red utilizando VLANs y troncales

##### Requisitos:

● Definir diferentes VLANs para VoIP y datos en cada sucursal.

● Configurar troncales para permitir el tráfico de ambas VLANs entre los switches y routers de la red Metro Ethernet.

##### Código de python

Instalamos netmiko para importar la libreria correspondiente:

```
pip install netmiko
```
```
from netmiko import ConnectHandler

def configure_switch(ip_address, username, password):
    # Definir VLANs para VoIP y datos
    commands = [
        'vlan 10',
        'name DATA',
        'exit',
        'vlan 20',
        'name VOIP',
        'exit',
    ]
    
    # Configurar troncales
    trunk_commands = [
        'interface range gig0/1-2',
        'switchport trunk encapsulation dot1q',
        'switchport mode trunk',
        'switchport trunk allowed vlan 10,20',
    ]
    
    try:
        # Conexión al switch
        switch = {
            'device_type': 'cisco_ios',
            'ip': ip_address,
            'username': username,
            'password': password,
        }
        
        # Establecer conexión y enviar comandos de VLANs
        with ConnectHandler(**switch) as conn:
            vlan_output = conn.send_config_set(commands)
            print("VLANs configuradas:")
            print(vlan_output)
            
            # Enviar comandos de troncales
            trunk_output = conn.send_config_set(trunk_commands)
            print("\nTroncales configuradas:")
            print(trunk_output)
            
            # Desconectar
            conn.disconnect()
    except Exception as e:
        print(f"Error de conexión: {e}")

# Ejemplo de uso
configure_switch('192.168.1.100', 'admin', 'password')
```
#### Resultados:

```
Error de conexión: TCP connection to device failed.

Common causes of this problem are:
1. Incorrect hostname or IP address.
2. Wrong TCP port.
3. Intermediate firewall blocking access.

Device settings: cisco_ios 192.168.1.100:22
```
Este código proporciona una forma modular y organizada de configurar un switch utilizando Netmiko, una biblioteca de Python para automatizar tareas de redes, además, permite configurar los VLANs y troncales en el switch de manera que sea eficiente y poder manejar posibles errores de conexión de manera adecuada, Donde en el código nos dio que existe un error de conexión.


### Parte 2: Configuración de QoS para priorizar VoIP

##### Requsitos:

● Utilizar políticas de QoS para asegurar que el tráfico de VoIP tenga la máximaprioridad.

##### Código Python:
```
from netmiko import ConnectHandler

def configure_qos(ip_address, username, password):
    switch = {
        'device_type': 'cisco_ios',
        'ip': ip_address,
        'username': username,
        'password': password,
    }
    
    qos_commands = [
        'access-list 101 permit ip any any',
        'class-map match-any VOIP',
        'match access-group 101',
        'exit',
        'policy-map VOIP-Policy',
        'class VOIP',
        'set ip dscp ef',
        'exit',
        'interface gig0/1',
        'service-policy output VOIP-Policy',
    ]
    
    try:
        with ConnectHandler(**switch) as conn:
            output = conn.send_config_set(qos_commands)
            print(output)
            conn.disconnect()
    except Exception as e:
        print(f"Error de conexión: {e}")

# Ejemplo de uso
configure_qos('192.168.1.44', 'admin', 'password')

```
#### Resultados:
```
Error de conexión: TCP connection to device failed.

Common causes of this problem are:
1. Incorrect hostname or IP address.
2. Wrong TCP port.
3. Intermediate firewall blocking access.

Device settings: cisco_ios 192.168.1.44:22

```
El código utiliza la biblioteca Netmiko para establecer conexiones SSH con dispositivos Cisco IOS y configurar políticas de QoS para priorizar el tráfico de VoIP sobre otros tipos de tráfico en la red. Se define una función llamada configure_qos que toma la dirección IP del dispositivo, el nombre de usuario y la contraseña como parámetros. Esta función envía una serie de comandos de configuración de QoS al dispositivo, incluyendo la creación de listas de acceso, clases de tráfico y políticas de QoS. Luego, establece la conexión SSH con el dispositivo utilizando la función ConnectHandler de Netmiko, envía los comandos de configuración y maneja cualquier error que pueda ocurrir durante el proceso. Sin embargo, al mostrar un ejemplo de uso de la función configure_qos, se obtiene un mensaje de error.

### Parte 3: Simulación y análisis
- Utilizar herramientas de simulación o scripts adicionales en Python para simular el tráfico y medir la efectividad de las políticas de QoS. Aquí, se podría usar scapy para generar tráficode VoIP y de datos, y observar la priorización basada en los DSCP tags asignados a los paquetes.

```
pip install scapy
```
```
from scapy.all import *

def generate_traffic():
   # Definir las direcciones IP de origen y destino.
    src_ip = "192.168.1.1"
    dst_ip = "192.168.1.2"

    # Generar tráfico VoIP
    for i in range(10):
       # Cree un paquete VoIP con un valor DSCP alto (EF = 46)
        voip_packet = IP(src=src_ip, dst=dst_ip, tos=184) / UDP(dport=5060)
        # Enviar el paquete
        send(voip_packet)

    # Generar tráfico de datos
    for i in range(10):
        # Crea un paquete de datos con un valor DSCP bajo (BE = 0)
        data_packet = IP(src=src_ip, dst=dst_ip, tos=0) / TCP(dport=80)
        # Send the packet
        send(data_packet)

# Ejecutar la función para generar tráfico.
generate_traffic()

```
#### Resultados:
```
Sent 1 packets.

Sent 1 packets.
```

Este código utiliza la biblioteca Scapy en Python para generar tráfico simulado tanto de VoIP como de datos, donde crea paquetes con direcciones IP de origen y destino predefinidos y los envía a través de la red. La diferencia entre los paquetes de VoIP y datos radica en los valores DSCP (Differentiated Services Code Point) que se les asigno, lo que permite simular diferentes niveles de priorización de tráfico. Luego de generar los paquetes, los envía utilizando la función `send` de Scapy. Este código es útil para probar y analizar políticas de QoS (Quality of Service) en una red simulada.


## PROBLEMA 2: Implementación de seguridad en WLAN con tecnología MIMO-OFDN

### Contexto:

Una universidad desea actualizar su red WLAN para mejorar la cobertura, el rendimiento y la seguridad. Se decide implementar tecnología MIMO-OFDM y actualizar los protocolos de seguridad a WPA 3.

#### Requisitos:
● **Describa el proceso de configuración de una WLAN utilizando MIMO-OFDM para maximizar la cobertura y el rendimiento.**


MIMO (Multiple Input Multiple Output) y OFDM (Orthogonal Frequency Division Multiplexing) son tecnologías utilizadas para mejorar la cobertura y el rendimiento de las redes WLAN. MIMO utiliza múltiples antenas en el transmisor y el receptor para mejorar la comunicación, mientras que OFDM divide la señal de radio en subcanales estrechos para reducir la interferencia y mejorar la velocidad de transmisión de datos. Para configurar una WLAN con MIMO-OFDM, se deben seguir estos pasos:

- Seleccionar hardware compatible, como routers y tarjetas de red, que admitan MIMO-OFDM.

- Colocar los puntos de acceso (AP) de manera que proporcionen una cobertura uniforme en todas las áreas necesarias.

- Utilizar canales no superpuestos para minimizar la interferencia y maximizar el rendimiento.

- Ajustar parámetros como la potencia de transmisión, la tasa de datos y las configuraciones de antena para maximizar el rendimiento y la cobertura.

● **Explique cómo WPA 3 mejora la seguridad en comparación con WPA y WPA2, particularmente en relación con la gestión de claves y la protección contra ataques comunes.**

WPA3 es la última versión del protocolo de seguridad de Wi-Fi Protected Access y ofrece varias mejoras sobre WPA y WPA2. En particular, WPA3 introduce una nueva forma de gestión de claves llamada Simultaneous Authentication of Equals (SAE), que es resistente a los ataques de diccionario y de fuerza bruta. Además, WPA3 también ofrece una protección mejorada contra los ataques de “hombre en el medio” mediante el uso de cifrado individualizado de datos.

● **Proponga un esquema para el uso efectivo de ESSID y BSSID en el contexto de un servicio distribuido y un servicio extendido en la red.**

ESSID (Extended Service Set Identifier) y BSSID (Basic Service Set Identifier) son identificadores en redes WLAN. ESSID es el nombre visible de la red para los usuarios, mientras que BSSID es la dirección MAC del punto de acceso. En redes grandes con varios puntos de acceso, se puede usar el mismo ESSID para proporcionar una red continua a los usuarios. Sin embargo, cada punto de acceso debe tener un BSSID único para gestionar eficientemente el tráfico.

##### Objetivos:
1. Configuración de MIMO-OFDM: Establecer configuraciones que optimicen el uso deMIMO-OFDM para mejorar la capacidad de la red y la cobertura.
2. Seguridad con WPA3: Implementar y configurar WPA 3 para mejorar la seguridad enla red WLAN.
3. Simulación con Python: Usar Python para simular la configuración de la red y testearla seguridad

### Parte 1: Configuración de MIMO-OFDM

##### Código python:
```
import numpy as np

def simulate_ofdm_signal(num_subcarriers):
    data = np.random.randint(0, 2, num_subcarriers)
    ofdm_signal = np.fft.ifft(data)
    return ofdm_signal

def mimo_transmission(signal, num_antennas):
    transmitted_signal = np.tile(signal, (num_antennas, 1))
    for i in range(num_antennas):
        transmitted_signal[i, :] *= np.exp(1j * np.random.rand())
    return transmitted_signal

# Ejemplo de uso
num_subcarriers = 40
num_antennas = 2

ofdm_signal = simulate_ofdm_signal(num_subcarriers)
mimo_signal = mimo_transmission(ofdm_signal, num_antennas)

# Imprimir resultados
print("Señal OFDM generada:")
print(ofdm_signal)
print("\nSeñal MIMO transmitida:")
print(mimo_signal)

```
#### Resultado:
```
Señal OFDM generada:
[ 5.75000000e-01+0.00000000e+00j -4.78836617e-02-2.48018398e-02j
 -1.93871503e-02+2.56234488e-02j  1.47479229e-02-1.22757266e-01j
  2.97745751e-02+8.04110203e-02j -6.93889390e-18-1.03553391e-02j
 -8.72474571e-02+4.00655372e-02j -5.72348414e-02+6.43163902e-03j
  2.31762746e-02+3.50021123e-02j -1.06525597e-01-5.77566179e-02j
 -2.50000000e-02+5.55111512e-18j  4.54640101e-02-4.66950308e-02j
  5.77254249e-02-8.25549381e-02j -2.48671347e-02-2.56703371e-02j
  3.72474571e-02-1.87129880e-02j -2.77555756e-18-6.03553391e-02j
 -6.06762746e-02-1.09800283e-01j -1.74424737e-03-5.97535900e-02j
 -3.06128497e-02+1.20729100e-01j -7.19564506e-02+4.50382725e-02j
  7.50000000e-02+0.00000000e+00j -7.19564506e-02-4.50382725e-02j
 -3.06128497e-02-1.20729100e-01j -1.74424737e-03+5.97535900e-02j
 -6.06762746e-02+1.09800283e-01j  8.32667268e-18+6.03553391e-02j
  3.72474571e-02+1.87129880e-02j -2.48671347e-02+2.56703371e-02j
  5.77254249e-02+8.25549381e-02j  4.54640101e-02+4.66950308e-02j
 -2.50000000e-02+0.00000000e+00j -1.06525597e-01+5.77566179e-02j
  2.31762746e-02-3.50021123e-02j -5.72348414e-02-6.43163902e-03j
 -8.72474571e-02-4.00655372e-02j  2.77555756e-18+1.03553391e-02j
  2.97745751e-02-8.04110203e-02j  1.47479229e-02+1.22757266e-01j
 -1.93871503e-02-2.56234488e-02j -4.78836617e-02+2.48018398e-02j]

Señal MIMO transmitida:
[[ 0.52073287+0.24384889j -0.03284643-0.04276785j -0.02842396+0.01498337j
   0.06541557-0.10491735j -0.00713659+0.08544897j  0.00439154-0.00937803j
  -0.09600444-0.00071609j -0.05456071-0.0184478j   0.00614508+0.04152741j
  -0.07197825-0.0974816j  -0.02264056-0.01060213j  0.06097589-0.02300746j
   0.08728775-0.05028311j -0.01163383-0.03379341j  0.04166803-0.00115081j
   0.0255958 -0.05465915j -0.00838514-0.12516949j  0.02376097-0.0548539j
  -0.07892309+0.09635252j -0.08426543+0.01027201j  0.06792168+0.03180638j
  -0.04606531-0.07130332j  0.02347572-0.12231743j -0.02692023+0.05337448j
  -0.10151445+0.07370569j -0.0255958 +0.05465915j  0.02579623+0.03274299j
  -0.03340664+0.01270185j  0.01726712+0.09924409j  0.02137056+0.06156867j
  -0.02264056-0.01060213j -0.12096568+0.00712977j  0.03583282-0.02186999j
  -0.04910559-0.03009708j -0.06202206-0.07328459j -0.00439154+0.00937803j
   0.06106563-0.06019507j -0.03870347+0.1174261j  -0.00669092-0.03142697j
  -0.0538826 +0.00215436j]
 [ 0.57213767+0.05730174j -0.04517367-0.02945023j -0.02184415+0.02356387j
   0.02690791-0.12067648j  0.02161298+0.08297793j  0.00103196-0.01030379j
  -0.09080588+0.03117143j -0.05759087+0.00069587j  0.01957276+0.03713751j
  -0.10023957-0.06808494j -0.02487555-0.00249138j  0.04989109-0.04193186j
   0.0656651 -0.07639134j -0.02218516-0.02802069j  0.03892689-0.01490793j
   0.00601472-0.06005489j -0.04943206-0.11530041j  0.00421919-0.05962996j
  -0.04249174+0.11707739j -0.07608655+0.03764324j  0.07462665+0.00747414j
  -0.06710996-0.05198491j -0.01842918-0.12317884j -0.00769032+0.05928232j
  -0.0713164 +0.10320699j -0.00601472+0.06005489j  0.03519719+0.02233174j
  -0.02730153+0.02306441j  0.04921104+0.08789662j  0.04058429+0.05099331j
  -0.02487555-0.00249138j -0.11175106+0.04685328j  0.02654905-0.03251824j
  -0.05630898-0.01210337j -0.0828204 -0.04856075j -0.00103196+0.01030379j
   0.03763973-0.07704355j  0.00244111+0.12361589j -0.01673713-0.02742793j
  -0.05011693+0.01990652j]]

```
El código simula la transmisión MIMO-OFDM, una tecnología clave en redes WLAN modernas. Primero, se genera una señal OFDM utilizando la transformada de Fourier inversa (IFFT) para convertir datos aleatorios en una señal en el dominio del tiempo. Luego, se simula la transmisión MIMO de esta señal, aplicando una fase aleatoria a cada señal transmitida por cada antena. Depúes se agregaron las declaraciones de impresión para visualizar las señales generadas y transmitidas. En resumen, este código ofrece una base para simular y comprender la transmisión MIMO-OFDM utilizando Python y NumPy.

### Parte 2: Configuración y simulación de WPA3
##### Implementación de WPA3

Configurar WPA3 requiere soporte de hardware y software, pero podemos simular elproceso de configuración utilizando scripts que podrían ser ejecutados en puntos de accesocompatibles con WPA3.

###### Código python
```
def configure_wpa3(device_config):
    # Simular la configuración de WPA3 en un dispositivo
    print("Configurando WPA3 en el dispositivo")
    device_config['security'] = 'WPA3'
    device_config['ssid'] = 'Universidad-RedSegura'
    device_config['password'] = 'contraseña_segura_345'
    return device_config

# Ejemplo de uso
device_config = {}
updated_config = configure_wpa3(device_config)
print(updated_config)
```
#### Resultados:
```
Configurando WPA3 en el dispositivo
{'security': 'WPA3', 'ssid': 'Universidad-RedSegura', 'password': 'contraseña_segura_123'}
```
El código simula la configuración de WPA3 en un dispositivo WLAN compatible, mediante la función `configure_wpa3`, que actualiza la configuración del dispositivo proporcionado como un argumento, estableciendo el protocolo de seguridad en WPA3, el SSID en "Universidad-RedSegura" y la contraseña en "contraseña_segura_345". El ejemplo de uso define un diccionario vacío para la configuración inicial del dispositivo, que llama a la función `configure_wpa3` con esta configuración y luego imprime la configuración actualizada del dispositivo.

### Parte 3: Evaluación de la seguridad

Para evaluar la seguridad de la implementación de WPA3, se pueden utilizar herramientas como Scapy para simular ataques y verificar la robustez del cifrado y autenticación en la red. Scapy es una biblioteca de Python que permite la creación, manipulación y envío de paquetes en la red, lo que la hace ideal para pruebas de seguridad y simulación de ataques. Se puede utilizar para simular intentos de ataque y evaluar la seguridad de implementación de WPA3.

```
from scapy.all import *

def simulate_attack(target_ip):
    # Definir la dirección IP de origen
    src_ip = "192.168.1.1"

    # Generar un paquete malicioso
    packet = IP(src=src_ip, dst=target_ip) / ICMP()

   # Enviar el paquete
    send(packet)

# Ejecutar la función para simular un ataque.
simulate_attack('192.168.1.100')
```
#### Resultados:

```
Sent 1 packets.
```
Este script genera un paquete ICMP (Internet Control Message Protocol) malicioso y lo envía a la dirección IP objetivo. Es un ejemplo muy básico donde se puede ajustar para que se adapte a las necesidades específicas que se quieran emplear o ejecutar.

## PROBLEMA 3: Estrategias de mitigaión para interferencia en redes Ad Hoc Inalambricas
### Contexto:

En un entorno de red ad hoc inalámbrico, los dispositivos sufren de interferenciaco-canal y de problemas de acceso al medio debido a la naturaleza descentralizada de la red.

#### Requisitos:

● **Explique cómo los protocolos CSMA/CA y CSMA/CD difieren y por qué uno es más adecuado que el otro para redes ad hoc inalámbricas.**

CSMA/CD (Carrier Sense Multiple Access / Collision Detection) y CSMA/CA (Carrier Sense Multiple Access / Collision Avoidance) se utiliza en redes cableadas, detecta colisiones después de que ocurren y espera un tiempo aleatorio antes de retransmitir. CSMA/CA, para redes inalámbricas, anuncia la intención de transmitir para evitar colisiones y espera un intervalo antes de transmitir si el medio está ocupado.


● **Discuta el impacto de la interferencia co-canal y proponga métodos de modulación como FHSS o DSSS para mitigar este problema.**

La interferencia co-canal (CCI) se produce por las transmisiones de dispositivos en la misma área y frecuencia, lo que puede llevar a una reducción del rendimiento en el sistema y a un mayor retraso y caída de paquetes. Los métodos de modulación como FHSS (Frequency Hopping Spread Spectrum) y DSSS (Direct Sequence Spread Spectrum) pueden ayudar a mitigar este problema:

- FHSS utiliza una portadora de banda estrecha que salta de una frecuencia a otra, lo que proporciona una mayor resistencia a los efectos de la interferencia y el ruido.
  
- DSSS dispersa la señal transmitida por todo el ancho de banda disponible, lo que proporciona una mayor robustez frente a posibles interferencias.
  
● **Desarrolle un algoritmo o protocolo para mejorar el período libre de contención eneste tipo de redes, incluyendo el uso de vectores de inicialización para la seguridadde las transmisiones.**

El algoritmo de retroceso exponencial binario (BEB) mejora el período libre de contención en redes ad hoc inalámbricas al evitar colisiones de paquetes mediante la aleatorización de los momentos de acceso a los canales. Los vectores de inicialización (IVs) son bloques de bits utilizados en cifrados por bloques y cifrados en flujo para permitir un cifrado independiente de otros cifrados producidos por la misma clave, mejorando así la confidencialidad de las transmisiones.

##### Objetivos:
1. Implementar CSMA/CA para manejo de acceso al medio.
2. Mitigar la interferencia co-canal utilizando técnicas de modulación como FHSS oDSSS.
3. Mejorar el período libre de contención utilizando un algoritmo personalizado.
4. Simulación con Python de las estrategias propuestas.

### Parte 1: Implementación de CSMA/CA en Python

##### Simulación de CSMA/CA:
```
import random
import time

def simulate_csma_ca(node_id, attempt_limit=5):
    attempt = 0
    while attempt < attempt_limit:
        # Sensar el medio (simulado por una función que retorna True si el medio está ocupado)
        if is_channel_busy():
            print(f"Node {node_id}: Canal ocupado, aplicando backoff")
            time_to_wait = exponential_backoff(attempt)
            time.sleep(time_to_wait)
            attempt += 1
        else:
            print(f"Node {node_id}: Canal libre, transmitiendo datos")
            send_data(node_id)
            break

def is_channel_busy():
    # Aquí iría la lógica para determinar si el canal está realmente ocupado 
    return random.choice([True, False])

def exponential_backoff(attempt):
    return random.randint(0, 2**attempt - 1)

def send_data(node_id):
    print(f"Node {node_id}: Datos enviados exitosamente")

# Ejemplo de uso
simulate_csma_ca(node_id=4)
```
#### Resultados:
```
Node 4: Canal libre, transmitiendo datos
Node 4: Datos enviados exitosamente
```
El código es una simulación del protocolo CSMA/CA (Carrier Sense Multiple Access with Collision Avoidance) en una red inalámbrica, donde la función `simulate_csma_ca` simula el comportamiento de un nodo CSMA/CA, donde el nodo intenta transmitir datos al medio, dado que si el canal está ocupado, se aplica un mecanismo de backoff exponencial antes de intentar nuevamente, pero si el canal está libre, este se transmite exitosamente. Las funciones auxiliares `is_channel_busy` y `exponential_backoff` simulan la detección del medio y el cálculo del tiempo de espera, respectivamente.

### Parte 2: Mitigación de interferenca Co-canal
##### Simulación básica de DSSS:
```
def dsss_encode(data, chip_code):
    encoded = []
    for bit in data:
        encoded_bit = [chip * int(bit) for chip in chip_code]
        encoded.extend(encoded_bit)
    return encoded

def dsss_decode(encoded_data, chip_code):
    decoded = []
    index = 0
    while index < len(encoded_data):
        segment = encoded_data[index:index+len(chip_code)]
        decoded_bit = 1 if sum(segment) > len(chip_code)/2 else 0
        decoded.append(decoded_bit)
        index += len(chip_code)
    return decoded

# Ejemplo de uso
data2 = [0, 1, 0, 1]
chip_code2 = [1, 1, -1, -1, 1, 1]

# Codificar y decodificar los nuevos datos utilizando el código de chip 
encoded_data2 = dsss_encode(data2, chip_code2)
decoded_data2 = dsss_decode(encoded_data2, chip_code2)

# Imprimir los resultados del nuevo ejemplo de uso
print("Encoded Data 2:", encoded_data2)
print("Decoded Data 2:", decoded_data2)
```
#### Resultados:
```
Encoded Data 2: [0, 0, 0, 0, 0, 0, 1, 1, -1, -1, 1, 1, 0, 0, 0, 0, 0, 0, 1, 1, -1, -1, 1, 1]
Decoded Data 2: [0, 0, 0, 0]
```

El código implementa la técnica DSSS (Direct Sequence Spread Spectrum) para mitigar la interferencia co-canal en redes inalámbricas. La función dsss_encode codifica los datos multiplicando cada bit de entrada por un código de chip correspondiente, dispersando así la señal en un espectro más amplio. La función dsss_decode decodifica los datos sumando los segmentos de datos codificados y determinando el bit decodificado basado en la mayoría de chips. Esto permite detectar y corregir errores causados por la interferencia co-canal. El código es modular y puede adaptarse a diferentes conjuntos de datos y códigos de chip, lo que brinda flexibilidad para simular diversos escenarios de mitigación de interferencia.

### Parte 3: Mejora del Período Libre de Contención
##### Algoritmo de ajuste dinámico:
```
import random

def adjust_contention_window(node_id, success_rate):
    if success_rate < 0.5:
        increase_backoff(node_id)
    else:
        decrease_backoff(node_id)

def increase_backoff(node_id):
    print(f"Node {node_id}: Incrementando el tiempo de backoff debido a baja tasa de éxito")

def decrease_backoff(node_id):
    print(f"Node {node_id}: Disminuyendo el tiempo de backoff debido a alta tasa de éxito")

def contention_window_adjustment(node_id):
    success_rate = calculate_success_rate(node_id)
    adjust_contention_window(node_id, success_rate)

def calculate_success_rate(node_id):
   
    return random.random()

# Ejemplo de uso del ajuste de la ventana de contención
node_id = 4
contention_window_adjustment(node_id)

```
#### Resultados:
```
Node 4: Disminuyendo el tiempo de backoff debido a alta tasa de éxito

```

Este código implementa el algoritmo de ajuste dinámico para el período libre de contención en redes ad hoc inalámbricas, donde la función `adjust_contention_window` ajusta dinámicamente los tiempos de espera basándose en la tasa de éxito de las transmisiones recientes que nos indica que si la tasa de éxito es baja, se incrementa el tiempo de backoff para reducir las colisiones pero si la tasa de éxito es alta, se disminuye el tiempo de backoff para aprovechar mejor el medio de transmisión. Mientras que la función `contention_window_adjustment` simula el proceso de ajuste basándose en una tasa de éxito calculada.

## PROBLEMA 4: Análisis y Resolución de Problemas de conectvidad en una rede compleja

### Contexto:

Una red empresarial compuesta por diferentes segmentos de red, incluyendoEthernet, WLAN y VPN, experimenta problemas de conectividad y rendimientointermitentes.

#### Requisitos:

● **Utilice técnicas de diagnóstico para identificar y resolver problemas de crosstalk y jam signal en la red Ethernet.**

La diafonía (crosstalk) y la señal de interferencia (jam signal) son problemas comunes en redes Ethernet que afectan la calidad de las comunicaciones. Para identificar y resolver estos problemas, se pueden utilizar varias técnicas:

- Herramientas de diagnóstico de red: Ayudan a identificar y resolver problemas con precisión.

- Asegurar que los cables estén correctamente apantallados: El blindaje reduce la diafonía al evitar la superposición de señales.

- Utilizar cables de alta calidad: Diseñados para minimizar la interferencia y la diafonía.

- Tecnología de cancelación de eco: Ayuda a reducir la diafonía en comunicaciones de voz.

● **Explique cómo el roaming y la utilización de SIM card pueden afectar la experiencia de los usuarios en la WLAN y proponga soluciones para optimizar la transición entrepuntos de acceso.**

El roaming permite a los usuarios de telefonía móvil utilizar sus dispositivos en redes de otros operadores, ya sea dentro del mismo país o en el extranjero. Sin embargo, puede generar preocupaciones sobre costos y conectividad, además de posibles períodos de inactividad de la tarjeta SIM y problemas de experiencia para los usuarios. Para mejorar la transmisión entre puntos de acceso en WLAN, se pueden seguir estas estrategias:

- Mapeo cuidadoso de la red y ajuste de la potencia de transmisión y ubicación de los puntos de acceso para hacer los traspasos de Wifi más fluidos.

- Establecimiento de un segundo router como otro punto de acceso, que actuará como repetidor de señal.

- Actualización de Windows y drivers para resolver problemas de conectividad

● **Evalúe cómo diferentes configuraciones de VPN podrían influir en la latencia y el ancho de banda de la red, y proponga ajustes para mejorar la conectividad y seguridad.**

Las VPN pueden aumentar la latencia debido a las etapas adicionales en la conexión, ya que cifran el tráfico y lo redirigen a través de servidores seguros, lo que incrementa el tiempo de respuesta, especialmente si los servidores están lejos. Para mejorar la conectividad y seguridad en las VPN, se pueden seguir estas recomendaciones:

- Elegir el protocolo VPN más seguro posible: Como por ejemplo OpenVPN, IKEv2 o WireGuard11.
- No guardar registro de las conexiones: Esto mejora la privacidad del usuario.
- Asegurarse de configurar el firewall para que no bloquee la VPN: Esto mejora la conectividad.
- Usar una DNS segura: Esto puede darle un plus de privacidad y seguridad a la conexión VPN


#### Objetivos:
1. Diagnóstico de Problemas de Ethernet: Identificar y resolver problemas de crosstalky señales de jam en la red Ethernet.
2. Optimización de WLAN: Examinar el impacto del roaming y la utilización de tarjetasSIM en la experiencia del usuario y la conectividad.
3. Configuración y Optimización de VPN: Evaluar y ajustar la configuración de VPNpara mejorar la latencia y el ancho de banda.
4. Simulación con Python de las estrategias propuestas.

### Parte 1: Diagnóstico de problemas de Ethernet
##### Código de Python para simular y diagnósticar problemas de Ethernet:
```
import random

def simulate_ethernet_traffic():
    # Simular el tráfico de Ethernet y detectar problemas potenciales
    traffic_patterns = ['normal', 'crosstalk', 'jam']
    for _ in range(10):  # Simular 10 ciclos de tráfico
        traffic_type = random.choice(traffic_patterns)
        if traffic_type == 'crosstalk':
            print("Crosstalk detected! Adjusting cable configurations.")
        elif traffic_type == 'jam':
            print("Jam signal detected! Resetting Ethernet interfaces.")
        else:
            print("Normal traffic.")

# Ejemplo de uso
simulate_ethernet_traffic()
```
#### Resultados:
```
Normal traffic.
Jam signal detected! Resetting Ethernet interfaces.
Crosstalk detected! Adjusting cable configurations.
Jam signal detected! Resetting Ethernet interfaces.
Normal traffic.
Jam signal detected! Resetting Ethernet interfaces.
Crosstalk detected! Adjusting cable configurations.
Jam signal detected! Resetting Ethernet interfaces.
Jam signal detected! Resetting Ethernet interfaces.
Jam signal detected! Resetting Ethernet interfaces.
```
El código simula el tráfico en una red Ethernet y emplea un enfoque de diagnóstico para lograr identificar posibles problemas, donde tiliza un bucle para simular varios ciclos de tráfico y selecciona aleatoriamente entre tres patrones de tráfico: normal, crosstalk y jam. Para cada ciclo, se imprime un mensaje que indica si se detectó crosstalk, jam signal o si el tráfico es normal. Esto nos permite una representación simplificada pero efectiva de cómo se podrían identificar y abordar los problemas de crosstalk y jamming en una red Ethernet.

### Parte 2: Optimización de WLAN

##### Código de python para simular roaming en WLAN:
```
import random

def simulate_roaming(user, access_points):
    current_ap = None
    print(f"{user} starts connection attempt...")
    for ap in access_points:
        if random.random() > 0.5:  # Simulates the probability of connecting to an access point
            current_ap = ap
            print(f"{user} connected to {ap}")
            break
    if not current_ap:
        print(f"{user} could not connect to any access point.")

simulate_roaming('User1', ['AP1', 'AP2', 'AP3'])
```
#### Resultados:
```
User1 starts connection attempt...
User1 connected to AP3
```

El código simula el proceso de roaming en una red WLAN, donde un usuario intenta conectarse a varios puntos de acceso (AP). Utiliza una función llamada simulate_roaming, que recibe el nombre del usuario y una lista de puntos de acceso como parámetros. Luego, itera sobre la lista de puntos de acceso y simula la probabilidad de que el usuario se conecte a uno de ellos. Si el usuario se conecta a un AP, imprime un mensaje indicando que se ha conectado. Si el usuario no puede conectarse a ningún AP, imprime un mensaje indicando que la conexión ha fallado. Esto permite simular cómo el roaming puede afectar la conectividad del usuario en una WLAN.

### Parte 3: Configuración y Optimización de VPN
##### Código de python paraa simular y optimizar VPN:
```
def configure_vpn_settings(vpn_connection):
    print("Configuring VPN...")
    vpn_connection['latency_reduction'] = True
    vpn_connection['bandwidth_optimization'] = True
    return vpn_connection

# Example usage
vpn_settings = configure_vpn_settings({})
print("VPN Settings Adjusted:", vpn_settings)
```
#### Resultados:
```
Configuring VPN...
VPN Settings Adjusted: {'latency_reduction': True, 'bandwidth_optimization': True}
```
El código simula la configuración y optimización de una conexión VPN en Python. La función configure_vpn_settings recibe un diccionario que representa la configuración de la conexión VPN y ajusta dos parámetros: latency_reduction (reducción de latencia) y bandwidth_optimization (optimización de ancho de banda), estableciéndolos en True. Luego, devuelve el diccionario modificado. El ejemplo de uso muestra cómo se llama a la función con un diccionario vacío y luego imprime la configuración de la VPN ajustada. Esto permite simular cómo ajustar la configuración de una VPN puede influir en la latencia y el ancho de banda de una red.

