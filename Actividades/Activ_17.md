# Problema 19 y 20 , 11 y 21

## Problema 19: Herramienta de diagnóstico de red funcional
Conceptos: ping, traceroute/tracert, hop, Route
Desarrolla una función que simule el comando traceroute, mostrando una ruta ficticia y los tiempos de hop para cada nodo en la ruta.
Simula el funcionamiento del comando traceroute utilizando estructuras de datos y funciones puras para calcular y mostrar la ruta y los tiempos de transmisión.
// Mas tarde lo hago 

````python
import random

def create_network(nodes):
    network = {}
    for node in nodes:
        network[node] = {}
    return network

def add_connection(network, node1, node2, latency):
    network[node1][node2] = latency
    network[node2][node1] = latency

def simulate_traceroute(network, start_node, end_node, max_hops=30):
    current_node = start_node
    hop_count = 0

    print(f"Traceroute from {start_node} to {end_node}:")

    while current_node != end_node and hop_count < max_hops:
        if not network[current_node]:  # Si el nodo actual no tiene conexiones
            print(f"No more connections available at {current_node}. Traceroute interrupted.")
            return

        next_node = random.choice(list(network[current_node].keys()))
        latency = network[current_node][next_node]

        print(f"Hop {hop_count+1}: {current_node} ({latency} ms)")

        current_node = next_node
        hop_count += 1

    if current_node == end_node:
        print(f"Destination reached: {end_node}")
    else:
        print("Max hop count reached, destination not reached.")

def demo():
    nodes = ['A', 'B', 'C', 'D', 'E']

    # Crear la red y establecer conexiones
    network = create_network(nodes)
    add_connection(network, 'A', 'B', 10)
    add_connection(network, 'B', 'C', 20)
    add_connection(network, 'B', 'D', 15)
    add_connection(network, 'C', 'D', 10)
    add_connection(network, 'D', 'E', 5)

    # Simular traceroute
    simulate_traceroute(network, 'A', 'E')

if __name__ == "__main__":
    demo()
```` 

## Problema 20: Análisis de log de red
Conceptos: Log file, Filter, Event Viewer
Implementa una función que analice un archivo de log simulado de eventos de red, filtrando y mostrando eventos basados en criterios específicos como errores, alertas de seguridad, etc.
Crea una función que procese y filtre datos de manera eficiente para simular el análisis de un archivo de log de eventos de red.
// Mas tarde lo hago 
````python 
from google.colab import files
import re

def filter_network_log(log_content, criteria):
    for line in log_content.split('\n'):
        timestamp, event_type, details = line.split("|")
        if criteria(event_type, details):
            print(f"[{timestamp}] {event_type}: {details}")

def is_error(event_type, details):
    return "ERROR" in event_type

def is_security_alert(event_type, details):
    return "SECURITY ALERT" in event_type

def main():
    # Cargar el archivo de registro
    uploaded = files.upload()

    # Leer el contenido del archivo de registro
    log_content = ""
    for filename in uploaded.keys():
        log_content = uploaded[filename].decode('utf-8')

    # Filtrar y mostrar eventos de error
    print("Eventos de Error:")
    filter_network_log(log_content, is_error)

    # Filtrar y mostrar alertas de seguridad
    print("\nAlertas de Seguridad:")
    filter_network_log(log_content, is_security_alert)

if __name__ == "__main__":
    main()
````

## Problema 11: Simulación de Flooding en Ethernet
Conceptos: Flooding, MAC Table, Ethernet
Desarrolla una función que simule el proceso de "flooding" utilizado en switches Ethernet para distribuir paquetes cuando la dirección de destino no está en la tabla MAC. La función debe manejar la adición de direcciones desconocidas a la tabla MAC.
Implementar una función que simule la recepción de frames y su distribución basada en la tabla MAC.

link: https://colab.research.google.com/drive/1q9S83Ayq0pC23DQDQAQ5gejcrzH1VIcS?usp=sharing

````python 
class Switch:
    def __init__(self):
        self.mac_table = {}  # Tabla MAC vacía

    def receive_frame(self, source_mac, dest_mac, frame):
        if dest_mac in self.mac_table:
            port = self.mac_table[dest_mac]
            print(f"Enviando frame a puerto {port}: {frame}")
        else:
            print("Dirección de destino desconocida, realizando flooding.")
            for port in range(1, 5):
                if port != source_mac:
                    print(f"Enviando frame desde el puerto {source_mac} a {port}")
            self.mac_table[dest_mac] = source_mac

# Ejemplo de uso
switch = Switch()
switch.receive_frame(1, 2, "Datos del frame")
switch.receive_frame(2, 1, "Datos del frame")
````

````
puertos = [1,2,3,4]
frames = [
        {'origen': 'AA:BB:CC:DD:EE:FF', 'destino': 'FF:EE:DD:CC:BB:AA'},
        {'origen': 'BB:CC:DD:EE:FF:AA', 'destino': 'AA:BB:CC:DD:EE:FF'},
    ]
tabla_mac={} #tiene que ser numeros enteros o sectores, como [] seria str.

def agregar_a_tabla_mac(direccion_mac, puerto): #las variables direccion y puerto se igualara, la direccion se guarda a la tabla 
    ##"""Agrega o actualiza la entrada de la dirección MAC en la tabla con su puerto correspondiente."""
    tabla_mac[direccion_mac] = puerto
    print(f"Dirección MAC {direccion_mac} asociada al puerto {puerto}.")

def flooding(frame, puerto_origen, puertos):
    ##"""Envía el frame a todos los puertos excepto al puerto de origen."""
    for puerto in puertos:
        if puerto != puerto_origen:
            print(f"Enviando frame desde el puerto {puerto_origen} a {puerto}")

def recibir_frame(frame, puerto_origen, puertos):
    #"""Procesa frames recibidos en un puerto específico."""
    direccion_destino = frame['destino']
    if direccion_destino in tabla_mac:
        puerto_destino = tabla_mac[direccion_destino]
        print(f"Enviando frame a {puerto_destino} desde el puerto {puerto_origen}.")
    else:
        print("Dirección de destino desconocida, realizando flooding.")
        flooding(frame, puerto_origen, puertos)
    # Simulamos que la dirección de origen se aprende automáticamente
    direccion_origen = frame['origen']
    agregar_a_tabla_mac(direccion_origen, puerto_origen)
````

## Problema 22: Gestión de caché en un servidor DNS
Conceptos involucrados: Caching DNS name server, TTL, Expiration time, Refresh rate.
Desarrolla una función que simule un servidor DNS que utiliza caché para almacenar respuestas DNS. La caché debe expirar las entradas basadas en su TTL.
Crea una función query_dns que consulte una caché antes de simular una consulta externa.
Implementa una función update_cache para añadir o actualizar entradas en la caché, incluyendo manejo del TTL.
Añade una función clean_expired_entries que limpie las entradas expiradas de la caché periódicamente.
````python 
import time

class DNSCache:
    def __init__(self):
        self.cache = {}

    def query_dns(self, domain):
        if domain in self.cache:
            record = self.cache[domain]
            if record['expiration_time'] > time.time():
                print(f"Cache hit: {domain} [A] -> {record['ip_address']}")
                return record['ip_address']
            else:
                del self.cache[domain]  # Elimina la entrada expirada de la caché
                print(f"Cache expired for: {domain} [A]")
        return None

    def update_cache(self, domain, ip_address, ttl):
        expiration_time = time.time() + ttl
        self.cache[domain] = {'ip_address': ip_address, 'expiration_time': expiration_time}
        print(f"Cache updated: {domain} [A] -> {ip_address} (TTL: {ttl}s)")

    def clean_expired_entries(self):
        current_time = time.time()
        expired_domains = [domain for domain, record in self.cache.items() if record['expiration_time'] <= current_time]
        for domain in expired_domains:
            del self.cache[domain]
        print("Expired entries cleaned from cache.")

def demo():
    cache = DNSCache()

    # Añadir entradas a la caché
    cache.update_cache("example.com", "93.184.216.34", 10)  # TTL de 10 segundos
    cache.update_cache("example.com", "93.184.216.34", 30)  # Actualiza TTL a 30 segundos

    # Consulta DNS
    print("Consulta DNS para example.com:", cache.query_dns("example.com"))  # Debería devolver la IP
    print("Consulta DNS para example.com:", cache.query_dns("example.com"))  # Debería devolver la IP

    # Esperar hasta que las entradas expiren
    time.sleep(15)

    # Consultar DNS después de la expiración del TTL
    print("Consulta DNS para example.com después de TTL expirado:", cache.query_dns("example.com"))  # Debería devolver None

    # Limpiar entradas expiradas
    cache.clean_expired_entries()

if __name__ == "__main__":
    demo()
````
###  Ejercicios adicionales
Modifica las funciones update_cache y query_dns para soportar diferentes tipos de registros DNS. Asegúrate de que tu caché pueda almacenar múltiples tipos de registros para un mismo dominio y que la función query_dns busque correctamente según el tipo de registro solicitado.

Implementa una función external_dns_query que simule de manera más realista una consulta DNS externa. Por ejemplo, podría seleccionar de un conjunto predefinido de respuestas basadas en el dominio y el tipo de registro.

Implementa una manera de que esta función se ejecute automáticamente en intervalos regulares. Considera el uso de threading o programación asíncrona para manejar esta tarea en el fondo sin bloquear las consultas principales.

````python
import time
import threading
import random

class DNSCache:
    def __init__(self):
        self.cache = {}

    def query_dns(self, domain, record_type='A'):
        if domain in self.cache and record_type in self.cache[domain]:
            record = self.cache[domain][record_type]
            if record['expiration_time'] > time.time():
                print(f"Cache hit: {domain} [{record_type}] -> {record['value']}")
                return record['value']
            else:
                del self.cache[domain][record_type]  # Elimina la entrada expirada de la caché
                print(f"Cache expired for: {domain} [{record_type}]")
                if not self.cache[domain]:  # Si no hay más registros para este dominio, lo eliminamos completamente
                    del self.cache[domain]
        return None

    def update_cache(self, domain, value, ttl, record_type='A'):
        expiration_time = time.time() + ttl
        if domain not in self.cache:
            self.cache[domain] = {}
        self.cache[domain][record_type] = {'value': value, 'expiration_time': expiration_time}
        print(f"Cache updated: {domain} [{record_type}] -> {value} (TTL: {ttl}s)")

    def clean_expired_entries(self):
        current_time = time.time()
        expired_domains = [domain for domain, records in self.cache.items() for record_type, record in records.items() if record['expiration_time'] <= current_time]
        for domain, records in self.cache.items():
            self.cache[domain] = {record_type: record for record_type, record in records.items() if record['expiration_time'] > current_time}
            if not self.cache[domain]:
                del self.cache[domain]
        print("Expired entries cleaned from cache.")

def external_dns_query(domain, record_type='A'):
    # Simulación de una consulta DNS externa más realista
    # Aquí podrías implementar lógica para seleccionar respuestas basadas en el dominio y el tipo de registro
    # En este ejemplo, simplemente generamos una IP aleatoria
    return '.'.join(str(random.randint(0, 255)) for _ in range(4))

def periodic_external_dns_query(cache, interval=60):
    while True:
        for domain, records in cache.cache.items():
            for record_type in records:
                external_result = external_dns_query(domain, record_type)
                cache.update_cache(domain, external_result, 60, record_type)
        time.sleep(interval)

def demo():
    cache = DNSCache()

    # Ejemplo de uso
    cache.update_cache("example.com", "93.184.216.34", 60)
    cache.update_cache("example.com", "2606:2800:220:1:248:1893:25c8:1946", 60, 'AAAA')

    # Consulta DNS
    print("Consulta DNS para example.com (A):", cache.query_dns("example.com", 'A'))
    print("Consulta DNS para example.com (AAAA):", cache.query_dns("example.com", 'AAAA'))

    # Iniciar el hilo para las consultas externas periódicas
    threading.Thread(target=periodic_external_dns_query, args=(cache,), daemon=True).start()

    # Espera infinita para mantener el programa en ejecución
    while True:
        time.sleep(1)

if __name__ == "__main__":
    demo()

````
