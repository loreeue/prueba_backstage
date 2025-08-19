# Flujo Node-RED: MHI Adjuntar Imagen 25

![MHI-25](adjuntar-imagen.png)

Este es un flujo diseñado para automatizar el proceso de adjuntar imágenes en los comentarios de un ticket en Remedy. A continuación, se explica la lógica principal de cada sección del flujo.

## Paso a Paso del Flujo

1. Se bloquea la cola y se **saca el primer elemento**. Si la cola está vacía, cada 10s se escribe que la cola está vacía.
2. Se guardan los valores de las variables del mensaje en las variables de flujo.
3. Se obtiene el **token de Remedy** y se almacena.
4. Se obtiene el **token de Azure** y se almacena.
5. Se obtiene el **nombre del usuario en Azure** y se almacena.
6. Se **consulta la persona en Remedy**.
8. Si el usuario se encuentra en Remedy, se ve el **número de imágenes adjuntadas**. Máximo se almacenan 3 imágenes.
9. Se hace una llamada a la **API para pasarlo a base 64**.
10. Se **almacenan las imágenes** en base 64 y con la extensión correspondiente.
11. En función del tipo de ticket,

	- Se filtra el número de INC, WO o CRQ.

	- Se obtienen los **datos del ticket**.

	- Se verifica que el usuario del ticket es el mismo que el usuario que está haciendo esta solicitud.

	- Se prepara y se añade a Remedy el **comentario** público con los adjuntos.

	- Se crea el **mensaje final** y se termina el flujo.

	- Si hay algún adjunto que ocupa mucho tamaño o que tiene una extensión no permitida, se devuelve el mensaje de error correspondiente.

Cuando algún proceso da **error**:

1. Crear el **mensaje de error** correspondiente.
2. Conectarse a la **cola de respuesta**.
3. Responder con el **mensaje de error**.
4. Comprobar que **el mensaje se ha recibido y eliminarlo de la cola**.

Por último, en el **subflujo SRID to Ticket**, se va buscando el ticket para ver si corresponde a una incidencia, petición o cambio.
