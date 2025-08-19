# Flujo Node-RED: MHI Comentar Ticket 23

![MHI-23](comentar-ticket.png)

Este es un flujo diseñado para comentar un ticket. A continuación, se explica la lógica principal de cada sección del flujo.

## Paso a Paso del Flujo

1. **Se bloquea la cola y se saca el primer elemento**. Si la cola está vacía, cada 10s se escribe que la cola está vacía.
2. Se extraen los valores de las variables del mensaje y se **almacena en las variables** del flujo.
3. Se obtiene el **token de Remedy** y se almacena.
4. Se obtiene el **token de Azure** y se almacena.
5. Se obtiene el **nombre del usuario en Azure** y se almacena.
6. Se consulta **si el usuario existe en Remedy**.
7. Si existe, se comprueba el tipo de ticket (**INC, WO o CRQ**) y:

	- Se extrae el ID del ticket.

	- Se obtienen los datos del ticket.

	- Si existe, se verifica que el usuario está solicitando tickets de él mismo y no de otro usuario.

	- Se prepara el comentario dependiendo de si es público o privado.

	- Si el ticket está cerrado o cancelado se pone el mensaje correspondiente y se termina el flujo.

8. Se vuelve a dividir el flujo dependiendo del tipo de ticket (**INC, WO o CRQ**) y:

	- Si está "pending" y se requiere la acción del usuario, se añaden los campos actualizados al ticket, se actualiza el ticket, se crea el mensaje de respuesta y se termina el flujo.

	- En caso contrario, se crean los mensajes de respuesta y se termina el flujo.

Cuando algún proceso da **error**:

1. Crear el **mensaje de error** correspondiente.
2. Conectarse a la **cola de respuesta** y **encriptar el mensaje** que se va a enviar.
3. Responder con el **mensaje de error**.
4. Comprobar que **el mensaje se ha recibido y eliminarlo de la cola**.

Por último, en el **subflujo SRID to Ticket**: Se va buscando el ticket para ver si corresponde a una incidencia, petición o cambio.
