# Flujo Node-RED: MHI Resolución 04

![MHI-02](resolucion.png)

Este es un flujo cuyo objetivo es la **resolución de tickets**. A continuación, se explica la lógica principal de cada sección del flujo.

## Paso a Paso del Flujo

1. **Sacar primer elemento de la cola**: Se bloquea la cola y se saca el primer elemento. Si la cola está vacía, cada 10s se escribe que la cola está vacía.
2. Se obtiene el **token de Remedy** y se almacena.
3. Se obtiene el **token de Azure** con Graph y se almacena.
4. Se obtiene el **nombre del usuario** con Graph.
5. **Login en Remedy** y hacer una consulta de la persona.
6. Si se encuentra la persona y no es ni una petición, cambio o incidencia, buscar el ticket.
7. Si se encuentra el ticket, en función del **tipo de ticket**:

	- <u> **INCIDENCIA**:</u>

		1. Se obtienen los datos de la incidencia.
		2. Se actualiza el ticket.
		3. El siguiente paso puede ser uno de estos dos:
			- Obtener la conversación, guardarla y añadir el comentario de la incidencia a Remedy.
			- Crear un mensaje nuevo y añadir el comentario de la incidencia a Remedy.

	- <u> **PETICIÓN**:</u>

		1. Se obtienen los datos de la petición.
		2. Se actualiza el ticket.
		3. El siguiente paso puede ser uno de estos dos:
			- Obtener la conversación, guardarla y añadir el comentario de la petición a Remedy.
			- Crear un mensaje nuevo y añadir el comentario de la petición a Remedy.

	- <u> **CAMBIO**:</u>

		1. Se obtienen los datos del cambio.
		2. Se actualiza el ticket.
		3. El siguiente paso puede ser uno de estos dos:
			- Obtener la conversación, guardarla y añadir el comentario del cambio a Remedy.
			- Crear un mensaje nuevo y añadir el comentario del cambio a Remedy.

Cuando algún proceso da **error**:

1. Crear el **mensaje de error** correspondiente.
2. Conectarse a la **cola de respuesta**.
3. Responder con el **mensaje de error**.
4. Comprobar que **el mensaje se ha recibido y eliminarlo de la cola**.

Por último, en el **subflujo SRID to Ticket**: Se va buscando el ticket para ver si corresponde a una incidencia, petición o cambio.
