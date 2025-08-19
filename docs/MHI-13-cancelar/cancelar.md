# Flujo Node-RED: MHI Cancelar 13

![MHI-13](cancelar.png)

Este es un flujo diseñado para poner el estado de un ticket como "Cancelado". A continuación, se explica la lógica principal de cada sección del flujo.

## Paso a Paso del Flujo

1. **Sacar primer elemento de la cola**: Se bloquea la cola y se saca el primer elemento. Si la cola está vacía, cada 10s se escribe que la cola está vacía.
2. Se obtiene el **token de Remedy** y se almacena.
3. Se obtiene el **token de Azure** y se almacena.
4. Se obtiene el nombre del **usuario en Azure** para obtener el Login de Remedy y consultar **si existe esa persona en Remedy**.
5. Si existe, en función del tipo de ticket:

	- Se obtiene el **número de INC/WO/CRQ**.
	- Se obtienen los **datos del ticket de Remedy**.
	- Si se encuentra el ticket, se **actualiza con estado "Cancelado"**.

Cuando algún proceso da **error**:

1. Crear el **mensaje de error** correspondiente.
2. Conectarse a la **cola de respuesta**.
3. Responder con el **mensaje de error**.
4. Comprobar que **el mensaje se ha recibido y eliminarlo de la cola**.

Por último, en el **subflujo SRID to Ticket**: Se va buscando el ticket para ver si corresponde a una incidencia, petición o cambio.
