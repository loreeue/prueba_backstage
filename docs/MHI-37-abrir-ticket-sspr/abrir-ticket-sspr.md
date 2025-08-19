# Flujo Node-RED: MHI Abrir Ticket SSPR

![MHI-37](abrir-ticket-sspr.png)

Este es un flujo cuyo objetivo es la apertura de tickets relacionados con el restablecimiento de contraseñas. A continuación, se explica la lógica principal de cada sección del flujo.

## Paso a Paso del Flujo

1. Se bloquea la cola y se **saca el primer elemento**. Si la cola está vacía, cada 10s se escribe que la cola está vacía.
2. Se extraen los **valores del primer elemento de la cola** y se almacenan en variables del flujo.
3. Se obtiene el **token de Remedy** y se almacena.
4. Se obtiene el **token de Azure** y se almacena.
5. Se obtiene el **nombre del usuario de Azure** y se almacena.
6. Se **consulta la persona en Remedy**.
7. Si se encuentra, se verifica si el **usuario está habilitado o deshabilitado en Remedy**.
8. Se obtienen los **datos del ticket** y, si es una INC:

	- Se almacena el Remedy Login ID.

	- Se **consulta la persona en Remedy** y se verifica si está habilitado o deshabilitado.

	- Se **consulta el CI del usuario en Remedy**.

	- Se **crea el ticket en DWP**.

	- Se **actualiza el ticket en Remedy**.

	- Se **obtiene la conversación** de la INC.

	- Se **añade el comentario al ticket de Remedy**.

Cuando algún proceso da **error**:

1. Crear el **mensaje de error**.
2. Conectarse a la **cola de respuesta**.
3. Responder con el **mensaje de error**.
4. Comprobar que **el mensaje se ha recibido y eliminarlo de la cola**.
