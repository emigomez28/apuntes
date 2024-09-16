# Modelo Cliente-Servidor.

## Sistemas Distribuidos

> Los componentes de software o hardware que lo conforman se encuentran conectados mediante una red.
> Se comunican mediante mensajes.
> Arquitectura es como se relacionan los componentes entre sí.

## Paradigmas de comunicación

> Entre procesos: A través de Apis de bajo nivel (channels, sockets, etc).
> Invocaciones remotas: Comunicaciones entre procesos que se encuentran en distintas máquinas.
> En forma indirecta: A través de una tercera entidad. Los componentes no necesitan conocerse entre sí.

## Cliente-Servidor

Es la arquitectura más común en sistemas distribuidos.

> Servidor: Es un proceso que corre en una computadora que implementa un servicio especifico. Este servicio es accedido y utilizado por los clientes.
> Cliente: Es un proceso que solicita un servicio al servidor. Para esto, debe establecer una conexión con el servidor, enviarle un mensaje indicando que servicio quiere ejecutar y esperar una respuesta.

### Diseño de servidores

Cuando se diseña un servidor hay que tener en cuenta:

- Si será iterativo o concurrente.
- Cuales son los endpoints del servidor.
- Stateful o Stateless, es decir, si almacena información del cliente o no.

Algunas claves de la arquitectura son:

- El servidor tiene un rol pasivo, mientras que el cliente tiene un rol activo.
- Los clientes deben conocer la ubicación del servidor para poder localizarlo y enviarle pedidos.
- Los clientes no entablan ninguna comunicación entre ellos.
- Permite centralizar la toma de decisiones en una aplicación distribuida.
- Se suele asumir que los servidores tienen más capacidad de **hardware** que el cliente.

### Peer to Peer

- Otro tipo de arquitectura distribuida es la Peer to Peer.
- Es la utilizada por protocolos como BitTorrent.
- Es descentralizada.
- Cada nodo (peer) puede actuar como cliente y como servidor.

## Comunicación

### Patrón de comunicación

El patrón de comunicación de esta arquitectura se conoce como **request-reply**.

- Es sincrónico. El proceso cliente se bloquea esperando la respuesta del servidor.
- Se puede modelar a partir de la existencia de tres primitivas:
  1. doOperation: El cliente invoca una operación en el servidor.
  2. getRequest: El servidor recibe una solicitud del cliente.
  3. sendReply: El servidor envía una respuesta al cliente.

### Protocolo TCP

El protocolo TCP resuelve:

1. En la red que comunica al cliente con el servidor que deriven en la pérdida de paquetes correspondientes a solicitudes o respuestas, generando una falla en la comunicación.
2. Mensajes que no son entregados entre nodos en el orden que deberían.

### Marshalling y Unmarshalling (Serialización y Deserialización)

Cuando queremos intercambiar datos entre un cliente y un servidor, debemos tener en cuenta el siguiente problema que puede surgir del intercambio

- Los programas almacenan la información en variables y estructuras de datos diversos tipos.
- Cuando queremos transmitir información, esta debe ser convertida a una secuencia de **bytes** y reconstruida en el destino.
- Las computadora alamacenan la información en distintas formas little/big endian, floating point, ASCII o unicode, etc.

Se define un estándar (protocolo) para realizar esto de una misma forma.

> Marshalling: Es el proceso en el cual los datos que se quieren enviar en un _request_ o un _reply_ se transforman a una forma estándar que permita transmitirlos en mensaje.
> Unmarshalling: Es el proceso en el cual, ante el arribo de un _request_ o _reply_, se desempaquetan los datos reproducionedo valores similares antes del momento que fueran transformados.

Algunos ejemplos de serialización son:

- JSON
- XML

## HTTP: Hyper-text Transfer Protocol

HTTP es uno de los protocolos de comunicación más difundidos y utilizados.

- Es un protocolo de capa de aplicación. Esto significa que se monta sobre la capa de transporte, especificamente sobre el protocolo **TCP**.
- Es el protocolo que se utiliza por _default_ los navegadores web para soliciar recursos a _web servers_.
- Se utilizan URLs para localizar los recursos en los servidores.
- Es un protocolo de tipo **request-reply**.

### Funcionamiento

- El cliente solicita establecer una conexión que debe ser aceptada por el servidor, utilizando URL y un puerto.
- El cliente envia la solicitud al servidor.
- El servidor resuelve la solicitud y envia la respuesta al cliente.
- Se cierra la conexión.

### Representación de los datos.

- Las solicitudes y respuestas son representadas en textos ASCII. Algunos recursos como imágenes pueden ser representados como una secuencia de bytes.
- Los recursos son representados con estrcutras **MIME** (Multipurpose Internet Mail Extensions).
- Cada dato tiene un prefijo que indican el tipo **MIME**, para que el receptor del mismo sepa como procesarlo.

Algunos ejemplos de **MIME** son:

- text/plain
- text/html
- image/jpeg
- image/gif

### Solicitudes

La estructura de una solicitud HTTP es la siguiente:

- Method: Especifica el método a realizar.
- URL: Especifica la dirección del recurso.
- Version: Especifica la versión del protocolo.
- Headers: Especifica los headers de la solicitud. Opcional
- Body: Especifica el cuerpo de la solicitud. Opcional

### Métodos

- GET: Solicita un recurso.
- POST: Envía datos al servidor.
- PUT: Actualiza o crea un recurso.
- DELETE: Elimina un recurso.

Estos métodos son secundarios y se utilizan con menos frecuencia

- PATCH: Actualiza parcialmente un recurso.
- TRACE: Realiza un seguimiento de la solicitud.
- OPTIONS: Devuelve los métodos HTTP soportados por el servidor.

### Respuestas

La estructura de una respuesta HTTP es la siguiente:

- Version: Especifica la versión del protocolo.
- Status Code: Especifica el estado de la respuesta.
- Reason: La razón que explica el estado.
- Headers: Especifica los headers de la respuesta. Similar a la solicitud.
- Body: Especifica el cuerpo de la respuesta.

### Status Codes

Los códigos que indican el resultado de una petición HTTP se agrupan en familias:

- 200-299: Respuestas satisfactorias.
- 300-399: Redirecciones a otro recurso.
- 400-499: Errores del cliente.
- 500-599: Errores del servidor.

# Github flow.

- Crear una branch en base a un feature que queremos implementar.
- Se commitea a esa branch.
- Se hace un pull request a la branch master.
- Se revisa el código y se aprueba.
