# Problema 19 y 20 , 11 y 21

## Problema 19: Herramienta de diagnóstico de red funcional
Conceptos: ping, traceroute/tracert, hop, Route
Desarrolla una función que simule el comando traceroute, mostrando una ruta ficticia y los tiempos de hop para cada nodo en la ruta.
Simula el funcionamiento del comando traceroute utilizando estructuras de datos y funciones puras para calcular y mostrar la ruta y los tiempos de transmisión.
// Mas tarde lo hago 

## Problema 20: Análisis de log de red
Conceptos: Log file, Filter, Event Viewer
Implementa una función que analice un archivo de log simulado de eventos de red, filtrando y mostrando eventos basados en criterios específicos como errores, alertas de seguridad, etc.
Crea una función que procese y filtre datos de manera eficiente para simular el análisis de un archivo de log de eventos de red.
// Mas tarde lo hago 


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

´´´´

´´´´

