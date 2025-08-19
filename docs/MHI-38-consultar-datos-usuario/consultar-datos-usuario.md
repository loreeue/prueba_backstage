# Flujo Node-RED: MHI Consultar Datos Usuario
![MHI-38](consultar-datos-usuario.png)

Este es un flujo cuyo objetivo es consultar los datos de un usuario y ver si tiene el MFA habilitado o no. A continuación, se explica la lógica principal de cada sección del flujo.

## Paso a Paso del Flujo

1. Se bloquea la cola y se **saca el primer elemento**. Si la cola está vacía, cada 10s se escribe que la cola está vacía.
2. Se extraen los **valores del primer elemento de la cola** y se almacenan en variables del flujo.
3. Se obtiene el **token de Remedy** y se almacena.
4. Se obtiene el **token de Azure** y se almacena.
5. Se obtiene el **nombre del usuario del peticionario de Azure** y se almacena.
6. Se **consulta la persona en Remedy**.
7. Si se encuentra, se verifica **si el ID del usuario tiene un @**.
8. En ese caso, se busca por el **userPrincipalName**. En caso contrario, se busca por el **onPremisesSamAccountName**.
9. Se obtienen los **datos del usuario**.
10. Se consulta el **MFA del usuario**.
11. Se verifica si el usuario tiene MFA habilitado en Azure AD y qué métodos usa (SMS o App Authenticator).

Cuando algún proceso da **error**:

1. Crear el **mensaje de error**.
2. Conectarse a la **cola de respuesta**.
3. Responder con el **mensaje de error**.
4. Comprobar que **el mensaje se ha recibido y eliminarlo de la cola**.
