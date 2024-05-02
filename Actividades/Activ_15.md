# ACTIVIDAD 15 : DNS


## Problema 1: Diseño de un sistema de distribución de contenidos (CDN)


### Código:
````
from flask import Flask, request
from functools import lru_cache


app = Flask(__name__)


# Diccionario para almacenar el contenido distribuido en el CDN
content_cache = {}


# Simulación de contenido en el servidor de origen
origin_content = {
    '/index.html': '<html><body><h1>Bienvenido a mi sitio web!</h1></body></html>',
    '/about.html': '<html><body><h1>Acerca de nosotros</h1><p>Somos una empresa dedicada a...</p></body></html>',
    # Agrega más contenido simulado según sea necesario
}


# Simulación de la ubicación geográfica de los usuarios
user_locations = {
    'user1': 'USA',
    'user2': 'EUROPE',
    'user3': 'ASIA',
    # Agrega más ubicaciones de usuarios según sea necesario
}


# Simulación de latencias entre los usuarios y los servidores de borde
latency_map = {
    ('USA', 'EdgeServer1'): 20,
    ('EUROPE', 'EdgeServer1'): 100,
    ('ASIA', 'EdgeServer1'): 200,
    # Agrega más asignaciones de latencia según sea necesario
}


# Función para obtener la latencia entre un usuario y un servidor de borde
def get_latency(user_location, edge_server):
    return latency_map.get((user_location, edge_server), float('inf'))


# Función para encontrar el servidor de borde más cercano a un usuario
def find_closest_edge_server(user_location):
    return min(latency_map, key=lambda x: get_latency(user_location, x[1]))[1]


# Simulación de la función fetch_from_origin para obtener contenido del servidor de origen
def fetch_from_origin(path):
    return origin_content.get(path, None)


# Ruta para manejar las solicitudes GET
@app.route('/``', methods=['GET'])
def get_content(path):
    # Verificar si el contenido está en caché
    if path in content_cache:
        return content_cache[path]
    else:
        # Obtener el contenido del servidor de origen
        origin_content = fetch_from_origin(path)
        # Almacenar el contenido en caché
        content_cache[path] = origin_content
        return origin_content
````
### Resultado
````
C:\Users\PROPIETARIO\PycharmProjects\pythonProject\venv\Scripts\python.exe C:\Users\PROPIETARIO\PycharmProjects\pythonProject\main.py 


Process finished with exit code 0
````


### Codificación Análisis

Simulación de datos:
Se agregaron diccionarios para simular el contenido en el servidor de origen, la ubicación geográfica de los usuarios y las latencias entre los usuarios y los servidores de borde.

Función para obtener la latencia:
Se agregó una función get_latency para obtener la latencia entre un usuario y un servidor de borde.

Función para encontrar el servidor de borde más cercano:
Se agregó una función find_closest_edge_server para encontrar el servidor de borde más cercano a un usuario basado en la latencia.

Implementación de la función fetch_from_origin:
Se agregó una simulación de la función fetch_from_origin para obtener contenido del servidor de origen.

Actualización de la ruta para manejar solicitudes GET:
La ruta ahora incluye el patrón ````/<path:path>```` para manejar cualquier ruta de contenido solicitada.

Se implementó la función get_content para manejar las solicitudes GET. Verifica si el contenido está en caché y, si no lo está, lo obtiene del servidor de origen y lo almacena en caché antes de devolverlo.

### Resultado análisis:

````C:\Users\PROPIETARIO\PycharmProjects\pythonProject\venv\Scripts\python.exe:```` Esta es la ruta al intérprete de Python que se utilizó para ejecutar tu script. En este caso, estás utilizando un intérprete de Python que está dentro de un entorno virtual (venv), que es una práctica común para aislar las dependencias de tu proyecto.

````C:\Users\PROPIETARIO\PycharmProjects\pythonProject\main.py:```` Esta es la ruta al script de Python que se ejecutó.
Process finished with exit code 0: Esta es una notificación del sistema que indica que tu script ha terminado de ejecutarse. Un código de salida de 0 generalmente significa que el programa se ejecutó con éxito sin errores.

##  Problema 2: Implementación de un servidor autoritario de DNS
### Código:
````
import dns.message
import dns.rdatatype
import dns.rdataclass
import dns.flags
import dns.query
import socket


# Diccionario para almacenar los registros de recursos (RR)
resource_records = {
    # Aquí deberías agregar tus registros de recursos
    # Por ejemplo: 'example.com': {'A': '192.0.2.1'}
}


def process_dns_query(query_message):
    # Lógica para procesar la consulta DNS y generar una respuesta
    # Aquí deberías buscar en tu base de datos de registros de recursos y construir la respuesta DNS apropiada
    response_message = dns.message.make_response(query_message)
    for question in query_message.question:
        records = resource_records.get(question.name.to_text(), {})
        record_type = dns.rdatatype.to_text(question.rdtype)
        record_data = records.get(record_type)
        if record_data:
            rrset = dns.rrset.from_text(question.name, 3600, dns.rdataclass.IN, question.rdtype, record_data)
            response_message.answer.append(rrset)
    return response_message


def main():
    server_address = '127.0.0.1'
    server_port = 53
    server_socket = socket.socket(socket.AF_INET, socket.SOCK_DGRAM)
    server_socket.bind((server_address, server_port))


    while True:
        # Recibir y procesar solicitudes DNS entrantes
        query_data, client_address = server_socket.recvfrom(512)
        query_message = dns.message.from_wire(query_data)
        response_message = process_dns_query(query_message)
        # Enviar respuesta al cliente DNS
        server_socket.sendto(response_message.to_wire(), client_address)


if __name__ == "__main__":
    main()


````
### Resultados:
````
C:\Users\PROPIETARIO\PycharmProjects\pythonProject\venv\Scripts\python.exe C:\Users\PROPIETARIO\PycharmProjects\pythonProject\main.py 
Traceback (most recent call last):
  File "C:\Users\PROPIETARIO\PycharmProjects\pythonProject\main.py", line 101, in <module>
    main()
  File "C:\Users\PROPIETARIO\PycharmProjects\pythonProject\main.py", line 89, in main
    server_socket.bind((server_address, server_port))
PermissionError: [WinError 10013] Intento de acceso a un socket no permitido por sus permisos de acceso


Process finished with exit code 1


````


### Análisis de código :

Este código crea un servidor DNS básico que escucha en el puerto 53 UDP y responde a las consultas DNS entrantes. Las respuestas se generan en función de los registros de recursos almacenados en el diccionario resource_records. Por favor, ten en cuenta que este es un ejemplo muy básico y no maneja muchos aspectos de un servidor DNS real, como la autenticación, el manejo de errores, la escalabilidad, entre otros. Te recomendaría investigar más sobre estos temas si estás interesado en implementar un servidor DNS real

### Análisis de resultado:


El error PermissionError: [WinError 10013] indica que el programa no tiene permisos suficientes para enlazar el socket al puerto especificado. Para solucionarlo, puedes intentar lo siguiente:

Ejecutar el programa como administrador para obtener los privilegios necesarios.
Cambiar el puerto a uno diferente si el 5353 está en uso.
Cerrar el programa que utiliza el puerto 5353 temporalmente para liberarlo.
Verificar que el firewall no esté bloqueando el acceso al puerto especificado, desactívalo temporalmente si es necesario.

## Problema 3: Diseño de un sistema de gestión de dominios (DNS)
### Código:
````
 from flask import Flask, request, jsonify
 app = Flask(__name__)
 # Base de datos ficticia para almacenar los dominios y registros de recursos
 domain_database = {}
 @app.route('/register', methods=['POST'])
 def register_domain():
 domain_name = request.json.get('domain_name')
 ip_address = request.json.get('ip_address')
 # Verificar si el dominio ya está registrado
 if domain_name in domain_database:
 return jsonify({'message': 'Domain already registered'}), 400
 # Registrar el nuevo dominio
 domain_database[domain_name] = ip_address
 return jsonify({'message': 'Domain registered successfully'}), 200
 @app.route('/resolve/<domain_name>', methods=['GET'])
 def resolve_domain(domain_name):
 # Verificar si el dominio está registrado
 if domain_name not in domain_database:
 return jsonify({'message': 'Domain not found'}), 404
 # Obtener la dirección IP asociada con el dominio
 ip_address = domain_database[domain_name]
 return jsonify({'domain': domain_name, 'ip_address': ip_address}), 200
 if __name__ == '__main__':
 app.run(debug=True)
````
### Análisis de código:

Se importan las clases necesarias de Flask para crear la aplicación web y manejar solicitudes HTTP y JSON. Se simula una base de datos con un diccionario para almacenar nombres de dominio y sus direcciones IP. Se definen rutas para registrar y resolver dominios, verificando su existencia en la base de datos. El servidor Flask se ejecuta en modo de depuración solo si el script se ejecuta directamente.


### Resultado
````
C:\Users\PROPIETARIO\PycharmProjects\pythonProject\venv\Scripts\python.exe C:\Users\PROPIETARIO\PycharmProjects\pythonProject\main.py 
Traceback (most recent call last):
  File "C:\Users\PROPIETARIO\PycharmProjects\pythonProject\main.py", line 101, in <module>
    main()
  File "C:\Users\PROPIETARIO\PycharmProjects\pythonProject\main.py", line 89, in main
    server_socket.bind((server_address, server_port))
PermissionError: [WinError 10013] Intento de acceso a un socket no permitido por sus permisos de acceso


Process finished with exit code 1
````

### Análisis de resultado:


El error PermissionError: [WinError 10013] indica que el programa no tiene permisos suficientes para enlazar el socket a la dirección y puerto especificados. Esto puede deberse a restricciones de permisos en el sistema operativo o porque el puerto está siendo utilizado por otro proceso.

Para solucionarlo, puedes probar lo siguiente:

Ejecutar el programa como administrador para otorgar los privilegios necesarios, cambiar el puerto a uno diferente si el actual está en uso por otro proceso.
Después cerrar el programa que utiliza el puerto para liberarlo temporalmente y de hay, verificar que el firewall no esté bloqueando el acceso al puerto especificado, desactivándolo temporalmente si es necesario.


##  Problema 4: Optimización de la resolución de nombres de dominio (DNS)


### Código 
````
import dns.resolver


def dns_lookup(domain_name):
    try:
        # Realizar una consulta DNS para el nombre de dominio dado
        result = dns.resolver.resolve(domain_name, 'A')
        for ip_address in result:
            print('IP Address:', ip_address.to_text())
    except dns.resolver.NoAnswer:
        print('No se encontraron registros de recursos para el dominio:', domain_name)
    except dns.resolver.NXDOMAIN:
        print('El dominio no existe:', domain_name)
    except dns.exception.Timeout:
        print('La consulta DNS ha excedido el tiempo de espera:', domain_name)


if __name__ == "__main__":
    domain_name = 'example.com'
    dns_lookup(domain_name)


````
### Resultados 
```
C:\Users\PROPIETARIO\PycharmProjects\pythonProject\venv\Scripts\python.exe C:\Users\PROPIETARIO\PycharmProjects\pythonProject\main.py 
  File "C:\Users\PROPIETARIO\PycharmProjects\pythonProject\main.py", line 143
    result = dns.resolver.resolve(domain_name, 'A')
    ^
IndentationError: expected an indented block after 'try' statement on line 141


Process finished with exit code 1
```
### Análisis de código:

El código importa el módulo dns.resolver para realizar consultas DNS.

La función dns_lookup(domain_name) toma un parámetro domain_name y realiza una consulta DNS para ese nombre de dominio.

Dentro de un bloque try, intenta resolver la consulta DNS. Si tiene éxito, itera sobre los resultados e imprime las respuestas.

Maneja excepciones como NoAnswer, NXDOMAIN y Timeout.

El bloque if __name__ == "__main__" asegura que el código dentro de él solo se ejecute cuando el script se ejecute directamente. Define el nombre de dominio como 'example.com' y llama a la función dns_lookup() con este nombre de dominio.

Para corregir el error de indentación, asegúrate de que todas las líneas dentro del bloque try estén correctamente indentadas.


### Análisis de resultados


El error de "IndentationError" que se muestra en la salida se produce debido a un problema de indentación en el código. La línea de código que inicia el bloque try debe tener una indentación adicional para indicar que todo el código dentro de ese bloque está dentro del try.




##  Problema 5: Implementación de un sistema de registro de dominios


````
import dns.resolver


def dns_lookup(domain_name):
    try:
        # Realizar una consulta DNS para el nombre de dominio dado
        result = dns.resolver.resolve(domain_name, 'A')
        for ip_address in result:
            print('IP Address:', ip_address.to_text())
    except dns.resolver.NoAnswer:
        print('No se encontraron registros de recursos para el dominio:', domain_name)
    except dns.resolver.NXDOMAIN:
        print('El dominio no existe:', domain_name)
    except dns.exception.Timeout:
        print('La consulta DNS ha excedido el tiempo de espera:', domain_name)


if __name__ == "__main__":
    domain_name = 'example.com'
    dns_lookup(domain_name)


````
### Análisis de código:
Este código utiliza dnspython para realizar una consulta DNS.

1. Importa dns.resolver para realizar las consultas.

2. Define dns_lookup(domain_name) para consultar el nombre de dominio especificado.

3. Intenta resolver la consulta DNS usando dns.resolver.resolve().

4. Itera sobre los resultados y los imprime.

5. Maneja casos donde no hay respuesta, el dominio no existe o hay un tiempo de espera excedido.

6. Al final, si se ejecuta como script principal, se hace una consulta para 'example.com'.


En resumen, este código realiza una consulta DNS para el nombre de dominio `'example.com'` y muestra la dirección IP asociada con este nombre de dominio si existe. Si no se encuentran registros de recursos para el dominio o si el dominio no existe, se imprime un mensaje correspondiente.


### Resultados:




```
 C:\Users\PROPIETARIO\PycharmProjects\pythonProject\venv\Scripts\python.exe C:\Users\PROPIETARIO\PycharmProjects\pythonProject\main.py 
  File "C:\Users\PROPIETARIO\PycharmProjects\pythonProject\main.py", line 143
    result = dns.resolver.resolve(domain_name, 'A')
    ^
IndentationError: expected an indented block after 'try' statement on line 141


Process finished with exit code 1
```


### Análisis de resultados

Análisis de requisitos: Identificar las necesidades del sistema, como registrar y verificar la disponibilidad de dominios, y gestionar la información del propietario.

Diseño de la base de datos: Crear la estructura para almacenar detalles como nombre de dominio, información del propietario y fecha de registro.

Desarrollo de la interfaz de usuario: Crear una interfaz intuitiva con formularios de registro y búsqueda de disponibilidad de nombres de dominio.

Implementación de la lógica de negocio: Escribir el código para funciones como el registro de dominios y la gestión de la información del propietario.

Pruebas y depuración: Realizar pruebas exhaustivas para garantizar el correcto funcionamiento del sistema y corregir errores identificados mediante herramientas de depuración.