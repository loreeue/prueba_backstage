# Flujo Node-RED: MHI Mostrar Ticket 15

![MHI-15](mostrar-ticket.png)

Este es un flujo diseñado para mostrar los tickets de un usuario determinado. A continuación, se explica la lógica principal de cada sección del flujo.

## Paso a Paso del Flujo

1. **Se bloquea la cola y se saca el primer elemento**. Si la cola está vacía, cada 10s se escribe que la cola está vacía.
2. Se saca el **token de Remedy** y se almacena.
3. Se saca el **token de Azure** y se almacena.
4. Se saca el **nombre del usuario en Azure** y se almacena.
5. Se consulta la **persona en Remedy**.
6. Si existe, se verifica si el **usuario que se quiere consultar es el mismo que el usuario que está interactuando**. Si no es así, se da error.
7. Se divide el flujo en función del tipo de ticket:

	- **INC**

		- Se obtienen los tickets que se hayan resuelto hace menos de 7 días o que tengan estado menor que 4 y que el usuario sea el Customer Login ID o el Submitter.
		- Si se encuentra alguno, se obtienen los comentarios de cada incidencia según un filtro concreto.

	- **WO**

		- Se obtienen los tickets que se hayan completado hace menos de 7 días o que tengan estado menor que 5 y el usuario sea el Requestor ID o el Submitter.
		- Si se encuentra alguno, se obtienen los comentarios de cada incidencia según un filtro concreto.

	- **CRQ**

		- Se obtienen los tickets que se hayan completado hace menos de 7 días o que tengan estado menor que 10 y el usuario sea el Customer Login ID o el Submitter.
		- Si se encuentra alguno, se obtienen los comentarios de cada incidencia según un filtro concreto.
8. Por último, se muestran de una forma determinada los tickets obtenidos.

En este flujo, se está verificando constantemente si la variable "enProceso" está a true o false para controlar que no se ejecuten múltiples instancias simultáneas de este flujo en Node-RED.

Cuando algún proceso da **error**:

1. Crear el **mensaje de error** correspondiente.
2. Poner la variable **"enProceso"** a false.
2. Conectarse a la **cola de respuesta**.
3. Responder con el **mensaje de error**.
4. Comprobar que **el mensaje se ha recibido y eliminarlo de la cola**.
