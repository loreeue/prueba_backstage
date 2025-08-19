# Flujo Node-RED: MHI Crear Ticket con Plantilla 27

![MHI-27](crear-ticket-plantilla.png)

Este flujo tiene como objetivo la **creación y gestión de tickets mediante el uso de plantillas**, es decir, cargando una configuración común que permite generar un ticket específico y, posteriormente, modificar los campos que se consideren necesarios. A continuación, se detalla la lógica principal de cada sección del flujo.

## Paso a Paso del Flujo

1. Se bloquea la cola y se **saca el primer elemento**. Si la cola está vacía, cada 10s se escribe que la cola está vacía.
2. Se guardan los valores de las variables del mensaje en las variables de flujo.
3. Se obtiene el **token de Remedy** y se almacena.
4. Se obtiene el **token de Azure** y se almacena.
5. Se obtiene el **nombre del usuario en Azure** y se almacena.
6. Se **consulta la persona en Remedy** y se verifica si el **usuario se encuentra** en Remedy.
7. Se obtienen los **datos del ticket** haciendo una llamada a la API.
9. Si es una incidencia,

	- Se **consulta la persona** en Remedy.

	- Se **consulta la configuración del usuario** en Remedy y, si se encuentra, se pone el SRID como identificador.

	- Se pone el **ID del procedimiento** al que está ligado este flujo.

	- Se obtiene la **conversación**, se guarda y se añade el comentario de la incidencia a Remedy.

	- Por último, se crea el **mensaje de respuesta**.

Cuando algún proceso da **error**:

1. Crear el **mensaje de error** correspondiente.
2. Conectarse a la **cola de respuesta**.
3. Responder con el **mensaje de error**.
4. Comprobar que **el mensaje se ha recibido y eliminarlo de la cola**.
