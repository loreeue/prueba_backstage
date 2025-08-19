# Flujo Node-RED: MHI Reclamación-Callback 07

![MHI-02](reclamacion-callback.png)

Este es un flujo diseñado para gestionar tickets en Remedy. Primero consulta el ticket (ya sea de tipo INC, WO o CRQ) utilizando varios nodos para obtener la información correspondiente. Luego, añade un comentario en el ticket que indica que el usuario reclama la petición desde el chatbot. Además, se ejecuta un callback (a través de Talkdesk) para que el usuario sea contactado por teléfono. Es decir, si el usuario tiene un problema que, aunque inicialmente menor, puede convertirse en algo crítico, el sistema automáticamente añade el comentario al ticket y solicita que le llamen para dar seguimiento a la incidencia. A continuación, se explica la lógica principal de cada sección del flujo.

## Paso a Paso del Flujo

1. **Sacar primer elemento de la cola**: Se bloquea la cola y se saca el primer elemento. Si la cola está vacía, cada 10s se escribe que la cola está vacía.
2. Se hace **login en el Talk Desk**.
3. Se **almacena el token** de acceso.
4. Se hace el **callback**.
5. Se obtiene el **token de Remedy** y se guarda.
6. En función del tipo de ticket:

	- **Incidencia**:

		1. Se obtienen los datos de la incidencia.
		2. Si se encuentra, se añade el comentario a la incidencia.
	- **Petición**:

		1. Se obtienen los datos de la petición.
		2. Si se encuentra, se añade el comentario a la petición.
	- **Cambio**:

		1. Se obtienen los datos del cambio.
		2. Si se encuentra, se añade el comentario al cambio.

Cuando algún proceso da **error**:

1. Crear el **mensaje de error** correspondiente.
2. Conectarse a la **cola de respuesta**.
3. Responder con el **mensaje de error**.
4. Comprobar que **el mensaje se ha recibido y eliminarlo de la cola**.

Por último, en el **subflujo SRID to Ticket**: Se va buscando el ticket para ver si corresponde a una incidencia, petición o cambio.
