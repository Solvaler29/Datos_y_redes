# ACTIVIDAD 5: IDENTIDICACIÓN DE DIRECCIONES MAC Y DIRECCIONES IP

### Pregunta:

- **¿Qué dispositivo tiene el MAC de destino que se muestra?**

El dispositivo con la dirección MAC de destino 00D0:BA8E:741A es el router

### Preguntas:

- **1. ¿Se utilizaron diferentes tipos de cables / medios para conectar dispositivos?**

Sí, tenemos 3 tipos, un medio inalámbrico, otro directo de cobre, y otro de fibra óptica.

- **2. ¿Los cables cambiaron el manejo de la PDU de alguna manera?**

No, ya que solo los cables trabajan a nivel capa 1.

- **3. ¿El Hub perdió parte de la información que recibió?**
 
 No, perdió la información.

- **4. ¿Qué hace el hub con las direcciones MAC y las direcciones IP?**

El hub no hace nada, solo reenvia a todos sus puertos la trama o el paquete que se envía.

- **5. ¿El punto de acceso inalámbrico hizo algo con la información que se le entregó?**
 
 Sí, este punto de acceso vuelve a empatquetar la trama en una forma inalámcbrica para que viaje por el aire.

- **6. ¿Se perdió alguna dirección MAC o IP durante la transferencia inalámbrica?**

No, perdió minguna dirreción IP o MAC.

- **7. ¿Cuál fue la capa OSI más alta que utilizaron el hub y el punto de acceso?** 

El access point solo trabajan a nivel capa 1.

- **8. ¿El hub o el punto de acceso reprodujeron en algún momento una PDU rechazada con una “X” de color rojo?**

Sí, ya que al reenviar a todos los puertos, solo uno es el destino, mientras que los demás rechaza, porque no son el destino.


- **9. Al examinar la ficha PDU Details (Detalles de PDU), ¿qué dirección MAC aparecía primero, la de origen o la de destino?**

En este caso aparece la de destino.



- **10. ¿Por qué las direcciones MAC aparecen en este orden?**

Porque si ya se se conoce primero el destino, este se enviará rapidamente.

- **11. ¿Había un patrón para el direccionamiento MAC en la simulación?**

 En este caso No hubo un patrón de direccionamiento MAC.

- **12. ¿Los switches reprodujeron en algún momento una PDU rechazada con una “X” de color rojo?**

No, ya que el swtich solo reenvia al destino que se requiere y no a todas las PCS.

- **13. Cada vez que se enviaba la PDU entre las redes 10 y 172, había un punto donde las direcciones MAC 
 cambiaban repentinamente. ¿Dónde ocurrió eso?**

- **14. Sí, cambió en el router¿Qué dispositivo usa direcciones MAC que comienzan con 00D0: BA?** 

Era la del router.



- **15. ¿A qué dispositivos pertenecían las otras direcciones MAC?**

Petenecnía a direcciones emisores, receptores, que podía ser Access point, switchers.

- **16. ¿Las direcciones IPv4 de envío y recepción cambiaron los campos en alguna de las PDU?**

No cambiaron en ningún campo de las PDU.

- **17. Cuando sigue la respuesta a un ping, a veces llamado pong, ¿ve el cambio de envío y recepción de direcciones IPv4?**

Sí se logra visualizar el envio y repecepción de las direccions IPv4.

- **18. ¿Cuál es el patrón para el direccionamiento IPv4 utilizado en esta simulación?**

Cada pueto debe manejar una direccion IP diferente, y cada Dispositivo dentro de la red, debe no solaparse.

- **19. ¿Por qué es necesario asignar diferentes redes IP a los diferentes puertos de un router?** 

 Es necesario para interconectar dos redes que se realizan en la simulación.

- **20. Si esta simulación se configura con IPv6 en lugar de IPv4, ¿cuál sería la diferencia?**

Todo sería igual, solo el formato donde se manejan las direcciones IP, ya que la IPv6 manejan una versión sexagesimal.
