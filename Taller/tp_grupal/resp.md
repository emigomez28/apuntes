# Redis Serialization Protocol (RESP)

- Puede serializar distintos tipos de datos (ints, strings, arrays)

- Un cliente manda una request al server de Redis como un array de strings donde el contenido es el comando y los argumentos con el que el servidor debe ejecutar. La respuesta del servidor depende del comando.

- RESP es **binary-safe** y usa tamaño fijo para transferir la data (tipo TCP).

- RESP es el protocolo que hay que implementar en el Cliente Redis

> [!NOTE]
> EL protocolo es solo para cliente-servidor. El protocolo de **Redis Cluster** es diferente y es binario, pero este sirve para mandar mensajes entre nodos.


# Capa de Red

- Un cliente se conecta a un servidor Redis creando una conexión TCP a su puerto (el dafault es 6379)

> [!NOTE]
> Ojo con el tema puertos porque cada puerto tiene un rol especifico, por ejemplo el 8080 esta reservado.

# Modelo Request-Response

- Las request pueden ser **pipelined**. La idea es que los clientes pueden mandar varias consultas a la vez y esperar las respuestas para después.

- Cuando se subscribe a un canal Pub/Sub el protocolo pasa a ser **push**. El cliente ahora ya no necesita mandar comandos por que ahora el servidor va a mandar automaticamente nuevos mensajes al cliente.

> [!NOTE]
> La idea general es que el cliente y el servidor se comunican en "modo ping-pong" donde el cliente hace una petición (request) y el servidor da la respuesta (response), el tema es que si te subscribis pasa a un tipo **push**, es decir, ahora el servidor le manda de una al cliente (porque justamente esta subscripto).
