# Flujo Node-RED: MHI Relacionar Masiva 24

![MHI-24](relacionar-masivas.png)

Este es un flujo diseñado para automatizar el proceso de creación y relación de tickets entre Remedy y DWP, partiendo de una cola de entrada con peticiones masivas.
El flujo automatiza el tratamiento masivo de solicitudes, extrayendo usuarios de una cola, validando su existencia en los sistemas de identidad de Azure y Remedy, y gestionando la creación y relación de tickets en DWP y Remedy. A continuación, se explica la lógica principal de cada sección del flujo.

## Paso a Paso del Flujo

1. Se bloquea la cola y se **saca el primer elemento**. Si la cola está vacía, cada 10s se escribe que la cola está vacía.
2. Se guardan los valores de las variables del mensaje en las variables de flujo.
3. Se obtiene el **token de Remedy** y se almacena.
4. Se obtiene el **token de Azure** y se almacena.
5. Se obtiene el **nombre del usuario en Azure** y se almacena.
6. Si el ID del usuario de Azure es uno concreto, se pone en el **login del usuario de Azure el de Chema**.
7. En caso contrario, se **consulta la persona en Remedy**.
8. Si el usuario existe en Azure, se almacena el **login del usuario en Remedy**.
9. Si no, se verifica si el **usuario final existe en Azure**.
10. Se hace la consulta en Remedy para ver si el **usuario existe o no existe y si está habilitado o deshabilitado**.
11. Si el usuario existe y está habilitado, se **obtiene el tipo de ticket**.
12. Se filtra el login del usuario final en Remedy y se hace una consulta a Remedy con ese usuario.
13. Si el usuario se encuentra, se **crea el ticket en DWP** y se **relaciona el ticket en Remedy**.

Cuando algún proceso da **error**:

1. Crear el **mensaje de error** correspondiente.
2. Conectarse a la **cola de respuesta**.
3. Responder con el **mensaje de error**.
4. Comprobar que **el mensaje se ha recibido y eliminarlo de la cola**.

Por último, en el **subflujo SRID to Ticket**, se va buscando el ticket para ver si corresponde a una incidencia, petición o cambio.
