# Flujo Node-RED: MHI Escalar-Callback 09

![MHI-09](escalar-callback.png)

Este es un flujo diseñado para escalar tickets consultando grupos de soporte, asignando detalles como organización, grupo y compañía y añadiendo comentarios internos. Es decir, se añade un comentario en el ticket que indica que el problema no se ha solventado via chatbot y al mismo tiempo, se ejecuta un callback (a través de Talkdesk) para que el usuario sea contactado por teléfono. A continuación, se explica la lógica principal de cada sección del flujo.

## Paso a Paso del Flujo

1. **Sacar primer elemento de la cola**: Se bloquea la cola y se saca el primer elemento. Si la cola está vacía, cada 10s se escribe que la cola está vacía.
2. Se obtiene el **token de Remedy** y se almacena.
3. En función del tipo de ticket:

	- Se obtienen los **datos del ticket** correspondiente.

	- Se comprueba si se encuentra. En ese caso,

		- Se añade el ID de la tool y del procedimiento.
		- Se obtienen los datos del ticket.
		- Si el grupo seleccionado es "grupo_tecnico" o no se selecciona grupo, se asigna el grupo de escalado. En caso contrario, se asigna el grupo seleccionado.
		- En función del tipo de ticket:
			- Se crea el mensaje del grupo de soporte.
			- Se obtiene el grupo de soporte. Si se localiza,
				- Se actualiza el ticket.
				- Se añade el comentario.
				- Se obtiene la conversación.
				- Se añade el comentario con la conversación.


Cuando algún proceso da **error**:

1. Crear el **mensaje de error** correspondiente.
2. Conectarse a la **cola de respuesta**.
3. Responder con el **mensaje de error**.
4. Comprobar que **el mensaje se ha recibido y eliminarlo de la cola**.

Por último, en el **subflujo SRID to Ticket**: Se va buscando el ticket para ver si corresponde a una incidencia, petición o cambio.
