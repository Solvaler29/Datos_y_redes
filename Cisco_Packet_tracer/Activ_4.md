# **Actividad 4: Configuración inicial de un Switch**

# 1.Verifica la configuración predeterminada del switch
## 1.1. Ingresa al modo EXEC privilegiado.
Puedes acceder a todos los comandos del switch en el modo EXEC privilegiado. Sin embargo,
debido a que muchos de los comandos privilegiados configuran parámetros operativos, el acceso
privilegiado se debe proteger con una contraseña para evitar el uso no autorizado.

El conjunto de comandos EXEC privilegiado incluye los comandos disponibles en el modo EXEC del
usuario, muchos comandos adicionales y el comando configure a través del cual se obtiene acceso
a los modos de configuración.

 a. Haz clic en S1 y luego en la pestaña CLI. Presione Enter.
 
 b. Ingresa al modo EXEC privilegiado introduciendo el comando enable: (Abra la ventana de configuración para S1)
    
    Switch> enable
    Switch#

Observa que la solicitud cambió para reflejar el modo EXEC privilegiado.


## 1.2. Examina la configuración actual del switch.
Ingresa el comando show running-config.

    Switch# show running-config

### Responde las siguientes preguntas:
  
  **a)**¿Cuántas interfaces Fast Ethernet tiene el switch?

En total tenemos 24 Inteface Fast Ethernet( son puertos en un switch que admiten velocidades de hasta 100 Mbps),los 24 puertos nos permite admitir conexiones de hasta 100 Mbps(megabit por segundo) cada uno. Estos puertos son esenciales para las capacidades de transferencia de datos en una red local.

**b)** ¿Cuántas interfaces Gigabit Ethernet tiene el switch?

Presenta dos gigabits ethernet(son puertos en un switch que admiten velocidades de hasta 1 Gbps o más).

**c)** ¿Cuál es el rango de valores que se muestra para las líneas vty?

El rango de lineas vty son de 0, 4 a 5 al 15.(son las líneas de terminal virtual del enrutador, utilizadas exclusivamente para controlar las conexiones Telnet entrantes. )Estas líneas permiten administrar el dispositivo de forma remota.

**d)** ¿Qué comando muestra el contenido actual de la memoria de acceso aleatorio no volátil (NVRAM)? show star

El comando seria: show startup-config. Proporciona la configuración almacenada en la NVRAM, que incluye la configuración inicial del dispositivo.

**e)** ¿Por qué el switch responde con "startup-config no está presente"?

Aparece así porque no hemos guardado nada en la memoria, todo sigue estando en la memoria ram, y esto es prueba cuando queremos guardar algo, está todavía en la Memoria RAM.

# 2. Crea una configuración básica del switch
## Asigna un nombre a un switch.
Para configurar los parámetros de un switch, quizá deba pasar por diversos modos de configuración.
Observa cómo cambia la petición de entrada mientras navega por el switch.

    Switch# configure terminal
    Switch(config)# hostname S1
    S1(config)# exit
    S1#

## Proporciona acceso seguro a la línea de consola.
Para proporcionar un acceso seguro a la línea de la consola, acceda al modo config-line y establezca
la contraseña de consola en cesar.

    S1# configure terminal
    Enter configuration commands, one per line. End with CNTL/Z.
    S1(config)# line console 0
    S1(config-line)# password cesar
    S1(config-line)# login
    S1(config-line)# exit
    S1(config)# exit
    %SYS-5-CONFIG_I: Configured from console by console
    S1#

### Pregunta:
 **¿Por qué se requiere el comando login?**

El comando login se utiliza para requerir autenticación al acceder a la línea de consola. Esto significa que cuando alguien intenta acceder a través de la línea de consola, se le pedirá que ingrese una contraseña (en este caso, "cesar") para verificar su identidad.


## Verifica que el acceso a la consola sea seguro.
Salimos del modo privilegiado para verificar que la contraseña del puerto de consola esté vigente.
    
    S1# exit
      Switch con0 is now available
      Press RETURN to get started.
      User Access Verification
      Password:
      S1>

## Proporciona un acceso seguro al modo privilegiado.
Establece la contraseña de **enable** en **jeka**. Esta contraseña protege el acceso al modo privilegiado.

    S1> enable
    S1# configure terminal
    S1(config)# enable password jeka
    S1(config)# exit
    %SYS-5-CONFIG_I: Configured from console by console
    S1#

## Verifica que el acceso al modo privilegiado sea seguro.
   
   **a.** Introduce el comando exit nuevamente para cerrar la sesión del switch.

   **b.** Presiona <Enter>; a continuación, se le pedirá que introduzca una contraseña:
    User Access Verification
    Password:

   **c.** La primera contraseña es la contraseña de consola que configuró para line con 0. Introduzca esta contraseña para volver al modo EXEC del usuario.

   **d.** Introduce el comando para acceder al modo privilegiado.
   
   **e.** Introduce la segunda contraseña que configuró para proteger el modo EXEC privilegiado.
   
   **f.** Verifica su configuración examinando el contenido del archivo de configuración en ejecución:

    S1# show running-config

Ten en cuenta que la consola y las contraseñas de activación están en texto plano. Esto
podría suponer un riesgo para la seguridad si alguien está mirando por encima de su hombro
u obtiene acceso a los archivos de configuración almacenados en una ubicación de copia de
seguridad.


## Configura una contraseña encriptada para proporcionar un acceso seguro al modo privilegiado.
La **contraseña de enable** se debe reemplazar por una nueva contraseña secreta encriptada
mediante el comando **enable secret**. Configura la contraseña de enable secret como **itsasecret**.

    S1# config t
    S1(config)# enable secret itsasecret
    S1(config)# exit
    S1#
**Nota:** La contraseña de **enable secret** sobrescribe la contraseña de **enable** password. Si ambos
están configurados en el switch, debes ingresar la contraseña **enable secret** para ingresar al modo
EXEC privilegiado.

## Verifica si la contraseña de enable secret se agregó al archivo de configuración.
Introduce el comando show running-config nuevamente para verificar si la nueva contraseña de
enable secret está configurada.

**Nota:** Puedes abreviar show running-config como

    S1# show run
### Preguntas:
**¿Qué se muestra como contraseña de enable secret?**

La contraseña de enable secret se muestra como una cadena encriptada, lo que la hace no legible en el archivo de configuración.

**¿Por qué la contraseña de enable secret se ve diferente de lo que se configuró?**

La contraseña de enable secret se ve diferente porque, por defecto, el IOS de Cisco encripta las contraseñas de forma automática para mejorar la seguridad.

## Encripta las contraseñas de consola y de enable.
Como notó en el paso anterior, la contraseña **enable secret** estaba encriptada, pero las contraseñas
**enable y console** todavía estaban en texto plano. Ahora encriptamos estas contraseñas de texto no
cifrado con el comando **service password-encryption**.

    S1# config t
    S1(config)# service password-encryption
    S1(config)# exit
### Pregunta:
**Si configuras más contraseñas en el switch, ¿se mostrarán como texto no cifrado o en forma cifrada
en el archivo de configuración? Explica.**

Cuando se utiliza el comando service password-encryption, todas las contraseñas configuradas después de este comando, incluidas las de consola y enable, se mostrarán en forma cifrada en el archivo de configuración.

# 3. Configure un aviso de MOTD
## Configura un aviso de mensaje del día (MOTD).
El conjunto de comandos de Cisco IOS incluye una característica que permite configurar los
mensajes que cualquier persona puede ver cuando inicia sesión en el switch. Estos mensajes se
denominan “mensajes del día” o “avisos de MOTD”. Coloca el texto del mensaje en citas o utilizando
un delimitador diferente a cualquier carácter que aparece en la cadena de MOTD.

    S1# config t
    S1(config)# banner motd "This is a secure system. Authorized Access Only!
    S1(config)# exit
    %SYS-5-CONFIG_I: Configured from console by console
    S1#
### Preguntas:
**¿Cuándo se muestra este aviso?**
Se muestra después de que un usuario inicia sesión en el switch. Es la primera información que ven al acceder a la interfaz de línea de comandos (CLI).


**¿Por qué todos los switches deben tener un aviso de MOTD?**

Todos los switch deben tener un banner MOTD para evitar que personas no autorizadas entren, es más que todo para resguardar la seguridad.



# 4 . Guarda y verifica archivos de configuración en NVRAM
### Verifica que la configuración sea precisa mediante el comando show run.
Guarda el archivo de configuración. Tu has completado la configuración básica del switch. Ahora
haga una copia de seguridad del archivo de configuración en ejecución a NVRAM para garantizar
que los cambios que se han realizado no se pierdan si el sistema se reinicia o se apaga.

    S1# copy running-config startup-config
    Destination filename [startup-config]?[Enter]
    Building configuration...
    [OK]

Cierre la ventana de configuración para S1

### Preguntas:
**¿Cuál es la versión abreviada más corta del comando copy running-config startup-config?**

La versión abreviada más corta del comando es copy run start.

**Examine el archivo de configuración de inicio.¿Qué comando muestra el contenido de la NVRAM?
Escriba sus respuestas aquí.**

El comando que muestra el contenido de la NVRAM es show startup-config.

**¿Todos los cambios realizados están grabados en el archivo?
Escriba sus respuestas aquí.**

Sí, todos los cambios realizados están grabados en el archivo de configuración de inicio (startup-config) en la NVRAM. Esto asegura que los cambios persistan incluso si el sistema se reinicia o se apaga.


# 5. Configura S2
Hemos completado la configuración en S1. Ahora configura el S2. Si no recuerda los comandos,
consulte las partes 1 a 4 para obtener ayuda.
## Configura el S2 con los siguientes parámetros:
Abra la ventana de configuración para S2

a. Device name: S2

b. Protege el acceso a la consola con la contraseña chalo.

c. Configura enable password como claudi y una contraseña enable secret como itsasecret.

d. Configura un mensaje apropiado para aquellos que inician sesión en el switch.

e. Encripta todas las contraseñas de texto no cifrado.

f. Asegúrate de que la configuración sea correcta.

g. Guarda el archivo de configuración para evitar perderlo si el switch se apaga.

g. Cierra la ventana de configuración para S2