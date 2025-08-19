# Flujo Node-RED: MHI Mostrar Tickets 05

![MHI-05](mostrar-tickets-05.png)

Este es un flujo diseñado para mostrar los tickets de un usuario determinado. A continuación, se explica la lógica principal de cada sección del flujo.

## Paso a Paso del Flujo

1. **Se bloquea la cola y se saca el primer elemento**. Si la cola está vacía, cada 10s se escribe que la cola está vacía.
2. Se saca el **token de Remedy** y se almacena.
3. Se saca el **token de Azure** y se almacena.
4. Se saca el **nombre del usuario en Azure** y se almacena.
5. Se consulta la **persona en Remedy**.
6. Si se encuentra el usuario en Remedy, **en función del tipo de ticket**:

	- Se **almacena el número** de INC, WO o CRQ.
	- Se obtienen los **datos del ticket** correspondiente.
	- Si se encuentra el ticket y el usuario corresponde con el submitter o customerLoginID, se obtienen los **comentarios de ese ticket**.
	- Si hay comentarios, se muestran y si no hay se pone un mensaje de "No se han encontrado comentarios".

Cuando algún proceso da **error**:

1. Crear el **mensaje de error** correspondiente.
2. Conectarse a la **cola de respuesta**.
3. Responder con el **mensaje de error**.
4. Comprobar que **el mensaje se ha recibido y eliminarlo de la cola**.

Por último, en el **subflujo SRID to Ticket**, se va buscando el ticket para ver si corresponde a una incidencia, petición o cambio.
